---
title: "Windows開發Go串接Sqlite，遇到Binary was compiled with 'CGO_ENABLED=0', go-sqlite3 requires cgo to work."
tags:
- golang
categories:
- 錯誤處理
date: 2023-05-22T22:55:27+08:00
draft: false
---

# 遇到的問題

在練習用GO建立網站的時候，在連線SqliteDB，發生了下面的錯誤

`
    Binary was compiled with 'CGO_ENABLED=0', go-sqlite3 requires cgo to work. This is a stub
`

查詢了一下
原因是連線Sqlite的套件會使用到C++的編譯套件，需要另外安裝gcc套件

# 解決方式

使用scoop輸入下列指令安裝gcc就解決了

`
    scoop bucket add versions  
`

`
    scoop install versions/tdm-gcc
`
