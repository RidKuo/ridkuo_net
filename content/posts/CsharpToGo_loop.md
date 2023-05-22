---
title: "用C#角度學GO For迴圈"
date: 2023-05-22T02:41:03+08:00
draft: false
tags:
- Golang
- C#
categories:
- 學習筆記
---

C#寫久了，最近想轉換不同的語言

覺得切換語言的方式，就是用熟悉的語言規則去看待新的語言

所以就有了這系列的筆記

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

## 實務性質

- 待續
