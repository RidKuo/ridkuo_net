---
title: "用C#角度學GO For迴圈"
date: 2023-05-22T02:41:03+08:00
draft: false
tags:
- Golang
- C#
- 用C#角度學GO
categories:
- 學習筆記
---

C#寫久了，最近想嘗試不同的語言

這系列的筆記是用熟悉的C#，去看待GO的語法特性，方便自己做使用對應

# 迴圈

## For Loop

- 基本上兩者沒有什麼差別

C#

```C#
    var sum = 0;
    for (int i = 0; i < 10; i++)
    {
        sum += i;
    }
```

Golang

```go
    sum := 0
    for int i = 0 ; i < 10 ; i++ {
        sum += i
    }
```

## While

- GO可以簡單直接用for下while條件就可以了，這點真的簡潔

C#

```C#
    var sum = 0;
    var i = 0;
    while(i < 10)
    {
        sum += i;
        i++;
    }
```

Golang

```go
    sum := 0
    i := 0
    for i < 10 {
        sum += i
        i++;
    }
```

## ForEach

- GO也是只用For語句搞定，配合range取出陣列的數值
- 第一個參數是key，第二個參數是value，可適用於不同集合
  - 例如我們使用dict的型別，第一參數就是dict's key 第二個參數為 dict's Value

C#

```C#
    var numbers = new int[] {1 ,2, 3, 4, 5};
    var sum = 0;

    foreach(var i in numbers)
    {
        sum += i;
    }
```

Golang

```go
    sum := 0
    numbers := [5]int{1, 2, 3, 4, 5}
    for key, value:= range numbers {
       sum += value;
    }  

```

- Golang可以精簡成只取得Key

```go
    sum := 0
    numbers := [5]int{1, 2, 3, 4, 5}
    for key:= range numbers {
       sum += numbers[key];
    }  

```

- Golang特性是有未使用的參數會噴錯，所以也可只取得value，key參數改帶 _ 即可

```go
    sum := 0
    numbers := [5]int{1, 2, 3, 4, 5}
    for _, value:= range numbers {
       sum += value;
    }  

```