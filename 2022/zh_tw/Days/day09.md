---
title: '#90DaysOfDevOps - Let''s explain the Hello World code - Day 9'
published: false
description: 90DaysOfDevOps - Let's explain the Hello World code
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1099682
---

## 讓我們解釋 Hello World 代碼

### Go 如何工作

在 [Day 8](day08.md) 中，我們完成了在工作站上安裝 Go，然後創建了我們的第一個 Go 應用程序。

在本節中，我們將更深入地研究代碼，並了解有關 Go 語言的更多信息。

### 什麼是編譯？

在我們深入了解 [Hello World 代碼的 6 行](Go/hello.go) 之前，我們需要對編譯有一點了解。

我們常用的程式語言，如 Python、Java、Go 和 C++，都是高級語言。這意味著它們是人類可讀的，但當機器試圖執行程序時，它需要是機器可以理解的形式。我們必須將我們的人類可讀代碼翻譯成機器碼，這稱為編譯。

![](Images/Day9_Go1.png)

從上面你可以看到我們在 [Day 8](day08.md) 上做了什麼，我們創建了一個簡單的 Hello World main.go，然後我們使用命令 `go build main.go` 來編譯我們的可執行文件。

### 什麼是包？

包是同一目錄中一起編譯的源文件集合。我們可以進一步簡化，包是同一目錄中的一堆 .go 文件。還記得我們在 Day 8 的 Hello 文件夾嗎？如果你進入更複雜的 Go 程序，你可能會發現你有 folder1 folder2 和 folder3 包含不同的 .go 文件，這些文件構成你的程序，具有多個包。

我們使用包，這樣我們可以重用其他人的代碼，我們不必從頭開始編寫所有內容。也許我們想要一個計算器作為我們程序的一部分，你可能會找到一個現有的 Go 包，其中包含你可以導入到代碼中的數學函數，從長遠來看，這可以節省你大量的時間和精力。

Go 鼓勵你將代碼組織在包中，以便易於重用和維護源代碼。

### Hello #90DaysOfDevOps 逐行

現在讓我們看一下我們的 Hello #90DaysOfDevOps main.go 文件並逐行進行。

![](Images/Day9_Go2.png)

在第一行，你有 `package main`，這意味著此文件屬於名為 main 的包。所有 .go 文件都需要屬於一個包，它們也應該在開頭行有 `package something`。

包可以命名為你想要的任何名稱。我們必須將其稱為 `main`，因為這是將在此包中的程序的起點，這是一個規則。（我需要更多地了解這個規則？）

![](Images/Day9_Go3.png)

每當我們想要編譯和執行我們的代碼時，我們必須告訴機器執行需要從哪裡開始。我們通過編寫一個名為 main 的函數來做到這一點。機器將尋找一個名為 main 的函數來找到程序的入口點。

函數是一段代碼，可以執行某些特定任務，並可以在整個程序中使用。

你可以使用 `func` 聲明任何名稱的函數，但在這種情況下，我們需要將其命名為 `main`，因為這是代碼開始的地方。

![](Images/Day9_Go4.png)

接下來，我們將查看代碼的第 3 行，導入，這意味著你想將另一個包引入你的主程序。fmt 是這裡使用的標準包，由 Go 提供，此包包含 `Println()` 函數，因為我們已導入此，我們可以在第 6 行使用它。你可以在程序中包含幾個標準包，並在你的代碼中利用或重用它們，從長遠來看，這可以節省你從頭開始編寫的麻煩。[Go 標準庫](https://pkg.go.dev/std)

![](Images/Day9_Go5.png)

我們這裡的 `Println()` 是一種將標準輸出寫入終端的方式，無論可執行文件在哪裡成功執行。請隨時更改 () 之間的消息。

![](Images/Day9_Go6.png)

### TLDR

- **第 1 行** = 此文件將在名為 `main` 的包中，這需要稱為 `main`，因為包括程序的入口點。
- **第 3 行** = 為了使用 `Println()`，我們必須導入 fmt 包以在第 6 行使用它。
- **第 5 行** = 實際起點，它是 `main` 函數。
- **第 6 行** = 這將讓我們在系統上打印"Hello #90DaysOfDevOps"。

## 資源

- [StackOverflow 2021 Developer Survey](https://insights.stackoverflow.com/survey/2021)
- [Why we are choosing Golang to learn](https://www.youtube.com/watch?v=7pLqIIAqZD4&t=9s)
- [Jake Wright - Learn Go in 12 minutes](https://www.youtube.com/watch?v=C8LgvuEBraI&t=312s)
- [Techworld with Nana - Golang full course - 3 hours 24 mins](https://www.youtube.com/watch?v=yyUHQIec83I)
- [**NOT FREE** Nigel Poulton Pluralsight - Go Fundamentals - 3 hours 26 mins](https://www.pluralsight.com/courses/go-fundamentals)
- [FreeCodeCamp - Learn Go Programming - Golang Tutorial for Beginners](https://www.youtube.com/watch?v=YS4e4q9oBaU&t=1025s)
- [Hitesh Choudhary - Complete playlist](https://www.youtube.com/playlist?list=PLRAV69dS1uWSR89FRQGZ6q9BR2b44Tr9N)

我們[Day 10](day10.md)見.
