---
title: "EF使用Postgresql觸發併發處理衝突(DbConcurrencyException)"
date: 2023-05-31T00:41:03+08:00
draft: false
tags:
- C#
- EntityFramework
- Postgresql
- DbConcurrencyException
categories:
- 心得筆記
---

# 遇到的問題

實務上會發生同一時間，多個請求對相同一筆資料進行異動的問題
這部份如果不處理好，容易造成資料互相覆蓋的狀況

1. A 請求 取得商品A，其庫存為5
2. B 請求 取得商品A，其庫存為5
3. A 請求 要對商品A增加2個庫存，其庫存計算後為5+2=7
4. B 請求 要對商品A扣除3個庫存，其庫存計算後為5-3=2
5. A 請求 更新商品A庫存7
6. B 請求 更新商品A庫存2

最後商品A的資料庫存會變成2...
但是不對啊!
我們處理兩筆請求，一筆+2 一筆-3，最後商品A的庫存應該是 5 + 2 -3 = 4 才對
一來一往就會造成庫存的資料數量越差越大...
這就是所謂的併發處理衝突

# 解法

大部分DB都有提供一個版本號的機制，像Postgresql在每一筆資料都有一個xmin的隱藏欄位
這個欄位可以當作版本號，每次更新xmin就會異動
利用這個機制處理併發衝突的話就要做以下的流程
1. A 請求 取得商品A，其庫存為5 版本號為1
2. B 請求 取得商品A，其庫存為5 版本號為1
3. A 請求 要對商品A增加2個庫存，其庫存計算後為5+2=7
4. B 請求 要對商品A扣除3個庫存，其庫存計算後為5-3=2
5. A 請求 更新商品A庫存7，DB查詢商品A且版本號為1的資料進行更新
6. A 請求 商品A已更新為庫存7，且版本號變更為2
7. B 請求 更新商品A庫存2，DB查詢商品A且版本號為1的資料進行更新
8. B 請求 由於已經找不到版本號為1的資料，發現資料更新筆數為0，拋出**併行處理衝突**的例外
9. B 請求 重新取得商品A，其庫存為7 版本號為2
10. B 請求 計算新的庫存為7-3=4，更新商品A庫存4，DB查詢商品A且版本號為2的資料進行更新

根據上面流程，就可以解決資料互相覆蓋的問題
但若直接使用EF+Postgresql的情況下
是不會觸發這個版本號機制的

需要做以下設定

在Table的Class增加uint的型別，Property名稱隨意，並掛上TimeStamp的Attribute
如果不是CodeFirst，可以用partial class的方式來增加這個Property
```C#
    public Table {

        public long Id { get; set; }

        [Timestamp]
        public uint Version { get; set; }
    }
```

在DbContext的OnModelCreating覆寫方法內
將剛剛加的Property，設定為IsRowVersion()
``` C#
class MyContext : DbContext
{
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Table>()
            .Property(b => b.Version)
            .IsRowVersion();
    }
}
```

照上述設定後，遇到有併行衝突，EF就會拋出DbConcurrencyException
我們再針對這個例外進行資料的重新讀取與計算就可以了

``` C#
    int retryTimes = 0

    try 
    {
        //// 資料處理
    }
    catch (DbConcurrencyException)
    {
        if (retryTimes < 5) //// 限制重試次數
        {
            //// Reload 資料
            retryTimes++;
            int radomMilSec = new Random().Next(100, 1000);//// 設定隨機時間延遲
            Thread.Sleep(radomMilSec);//// 睡個隨機時間，降低與其他併行處理再次撞到的機率
        }
    }
```

參考: https://www.npgsql.org/efcore/modeling/concurrency.html?tabs=fluent-api