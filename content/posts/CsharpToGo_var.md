---
title: "用C#角度學GO 變數"
date: 2023-05-29T00:00:03+08:00
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

# 變數

## 指定型別

- Go使用指定型別較為麻煩點，實務上都是用下一節的不指定型別的方式居多

C#

```C#
    int number = 0;
    string str = "It's String";

```

Golang

```go
    var number int = 0
    var str string = "It's String"

```

## 不指定型別

- Go直接使用:=宣告變數的值，但要注意的是不能var宣告後，再用:=給值，會錯誤

C#

```C#
    var number = 0;
```

Golang

```go
    number := 0
```
```go
    var number int
    number := 0 /// 這樣是不允許的
    number = 0 /// 需這樣寫才能給值
```

### 多變數

- C#無法用var為多變數給值，需指定型別
- Go可用var或是:=給值

C#

```C#
     int number1 = 1, number2 = 2, number3 = 0;
```

Golang

```go
    number1, number2, number3 := 0, 0, 0
    var number1, number2, number3 = 0, 0, 0 ///兩種都可以同時為多變數給值
```