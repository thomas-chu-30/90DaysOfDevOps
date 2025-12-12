---
title: '#90DaysOfDevOps - Getting user input with Pointers and a finished program - Day 12'
published: false
description: 90DaysOfDevOps - Getting user input with Pointers and a finished program
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048864
---

## 使用指針獲取用戶輸入和完成的程序

昨天（[Day 11](day11.md)），我們創建了第一個自包含的 Go 程序，我們想要獲取用戶輸入的部分在代碼中創建為變量並賦值，我們現在想詢問用戶的輸入以給變量賦值用於最終消息。

## 獲取用戶輸入

在我們這樣做之前，讓我們再次查看我們的應用程序，並在獲取用戶輸入之前測試我們想要的變量。

昨天我們完成的代碼看起來像這樣 [day11_example4.go](Go/day11_example4.go)，我們在代碼中手動定義了 `challenge, daystotal, dayscomplete` 變量和常量。

現在讓我們添加一個名為 `TwitterName` 的新變量，你可以在 [day12_example1.go](Go/day12_example1.go) 找到這個新代碼，如果我們運行這個代碼，這是我們的輸出。

![](Images/Day12_Go1.png)

我們在第 12 天，如果這是硬編碼的，我們每天都需要更改 `dayscomplete` 並每天編譯我們的代碼，這聽起來不太好。

獲取用戶輸入，我們想獲取一個名稱和已完成天數的值。為了做到這一點，我們可以使用 `fmt` 包中的另一個函數。

回顧 `fmt` 包，用於格式化輸入和輸出（I/O）的不同函數

- 打印消息
- 收集用戶輸入
- 寫入文件

這是代替為變量賦值，我們想詢問用戶的輸入。

```
fmt.Scan(&TwitterName)
```

請注意，我們還在變量前使用 `&`。這被稱為指針，我們將在下一節中介紹。

在我們的代碼 [day12_example2.go](Go/day12_example2.go) 中，你可以看到我們要求用戶輸入兩個變量，`TwitterName` 和 `DaysCompleted`

現在讓我們運行我們的程序，你會看到我們有上述兩個的輸入。

![](Images/Day12_Go2.png)

好的，太好了，我們得到了一些用戶輸入並打印了一條消息，但是讓我們的程序告訴我們在挑戰中還剩多少天呢？

為了做到這一點，我們創建了一個名為 `remainingDays` 的變量，我們在代碼中將其硬編碼為 `90`，然後當我們獲得用戶輸入的 `DaysCompleted` 時，我們需要更改此值以打印剩餘天數，我們可以通過這個簡單的變量更改來做到這一點。

```
remainingDays = remainingDays - DaysCompleted
```

你可以在這裡看到我們完成的程序 [day12_example2.go](Go/day12_example3.go)。

如果我們現在運行這個程序，你可以看到根據用戶輸入和 `remainingDays` 的值進行了簡單的計算

![](Images/Day12_Go3.png)

## 什麼是指針？（特殊變量）

指針是一個（特殊）變量，它指向另一個變量的內存地址。

在 [geeksforgeeks](https://www.geeksforgeeks.org/pointers-in-golang/) 可以找到對此的很好解釋

現在讓我們簡化我們的代碼，並在我們的一個打印命令前顯示有和沒有 `&` 的情況，這為我們提供了指針的內存地址。我在這裡添加了這個代碼示例。[day12_example4.go](Go/day12_example4.go)

下面是運行此代碼。

![](Images/Day12_Go4.png)

## 資源

- [StackOverflow 2021 Developer Survey](https://insights.stackoverflow.com/survey/2021)
- [Why we are choosing Golang to learn](https://www.youtube.com/watch?v=7pLqIIAqZD4&t=9s)
- [Jake Wright - Learn Go in 12 minutes](https://www.youtube.com/watch?v=C8LgvuEBraI&t=312s)
- [Techworld with Nana - Golang full course - 3 hours 24 mins](https://www.youtube.com/watch?v=yyUHQIec83I)
- [**NOT FREE** Nigel Poulton Pluralsight - Go Fundamentals - 3 hours 26 mins](https://www.pluralsight.com/courses/go-fundamentals)
- [FreeCodeCamp - Learn Go Programming - Golang Tutorial for Beginners](https://www.youtube.com/watch?v=YS4e4q9oBaU&t=1025s)
- [Hitesh Choudhary - Complete playlist](https://www.youtube.com/playlist?list=PLRAV69dS1uWSR89FRQGZ6q9BR2b44Tr9N)

我們[Day 13](day13.md)見.
