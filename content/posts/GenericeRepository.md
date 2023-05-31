---
title: "使用統一Repository操作DbContext"
date: 2023-05-31T00:41:03+08:00
draft: false
tags:
- C#
- EntityFramework
categories:
- 心得筆記
---

# 遇到的問題
目前工作上遇到DA層操作CUD的寫法都是如下
針對不同資料表，建立各自的Repository，除了有Query的商業邏輯外，也對各自的Table做EF的CUD操作

```C#
public class TableRepository: ITableRepository {
    public async void InsertAsync(Table item){
        await dbContext.Table.AddAsync(item);
        await dbContext.SaveChangesAsync();
    }

    public async void UpdateAsync(Table item){
        await dbContext.Table.Attach(item);
        await dbContext.SaveChangesAsync();
    }
}
```

在Service上的操作如下

```C#
    await repository.InsertAsync(item);
    await repository.UpdateAsync(item);
```

這樣的寫法，主要問題是在EF執行SaveChangesAsync時，就會檢測EF內的實體變更
根據實體的狀態EntityState，決定要進行的CUD操作，並且開啟連線對DB操作
那像上面service內對Repository的操作，等於是跟DB建立了兩次連線操作
一來一往就會有與DB的連線時間差，如果一個API執行的CUD業務量越多，效能就會越低落

# 嘗試的解法

為了維持各自Table有自己的Repository，維持有自己的Query商業邏輯
拉出一個統控的Repository，對DbContext做統一的CUD操作
並且將SaveChangesAsync獨立出來一個方法

```C#
public class GenericRepository : IGenericRepository
{
    public async void InsertAsync(Table item){
        await dbContext.AddAsync(item);
    }

    public async void UpdateAsync(Table item){
        await dbContext.Table.Attach(item);
    }

    public async void SaveChangesAsync(Table item){
        await dbContext.SaveChangesAsync();
    }
}
```

在Service上的操作如下

```C#
    await repository.InsertAsync(item);
    await repository.UpdateAsync(item);
    await repository.SaveChangesAsync(item);
```

這樣做可以降低EF與資料庫來回溝通的頻率，在整個業務邏輯跑完之後，才將資料變更一併送上DB
但使用上會變麻煩一點，需要明確下SaveChangesAsync()才會變更，算是要習慣的一點

還會遇到一個問題就是如果需要資料建立後的ID的話，就仍需要先SaveChangesAsync後，才能取得ID
關於這點，EF其實有另外的解法叫做HiLo，下次再來分享