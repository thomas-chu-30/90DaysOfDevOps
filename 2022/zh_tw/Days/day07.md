---
title: '#90DaysOfDevOps - The Big Picture: Learning a Programming Language - Day 7'
published: false
description: 90DaysOfDevOps - The Big Picture DevOps & Learning a Programming Language
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048856
---

## 大局：DevOps 與學習程式語言

我認為可以公平地說，要長期成功地成為 DevOps 工程師，你必須在基礎水平上至少了解一種程式語言。我想利用本節的第一個會話來探討為什麼這是一個如此關鍵的技能，希望在本週或本節結束時，你將更好地理解為什麼、如何以及做什麼來推進你的學習之旅。

我認為如果我在社交媒體上問你是否需要具備 DevOps 相關角色的編程技能，答案很可能是一個強硬的"是"？如果你認為不是這樣，請告訴我？好的，但更大的問題是，這是你不會得到如此明確答案的地方，哪種程式語言？我在這裡看到的最常見答案是 Python 或越來越常見的 Golang 或 Go 應該是你學習的語言。

要在 DevOps 中取得成功，你必須具備良好的編程技能知識，這至少是我的收穫。但我們必須理解為什麼需要它來選擇正確的道路。

## 理解為什麼你需要學習程式語言

Python 和 Go 經常被推薦給 DevOps 工程師的原因是很多 DevOps 工具都是用 Python 或 Go 編寫的，如果你要構建 DevOps 工具，這是有道理的。現在這很重要，因為這將真正決定你應該學習什麼，這可能是最有益的。如果你要構建 DevOps 工具，或者你加入一個這樣做的團隊，那麼學習相同的語言是有意義的，如果你將大量參與 Kubernetes 或容器，那麼你很可能會想要選擇 Go 作為你的程式語言。對我來說，我工作的公司（Kasten by Veeam）在雲原生生態系統中，專注於 Kubernetes 的數據管理，一切都用 Go 編寫。

但你可能沒有如此明確的理由來選擇，你可能是一名學生或正在轉換職業，沒有為你做出真正的決定。我認為在這種情況下，你應該選擇似乎與你希望使用的應用程序產生共鳴並適合的語言。

記住，我在這裡不是要成為軟體開發人員，我只是想更多地了解程式語言，這樣我就可以閱讀和理解這些工具在做什麼，然後這可能導致我們如何幫助改進事情。

了解如何與這些 DevOps 工具交互也很重要，這些工具可能是 Kasten K10 或 Terraform 和 HCL。這些是我們將稱為配置文件，這就是你與這些 DevOps 工具交互以使事情發生的方式，通常這些將是 YAML。（我們可能會在本節的最後一天深入探討 YAML）

## 我剛才是否說服自己不學習程式語言？

大多數時候或取決於角色，你將幫助工程團隊將 DevOps 實施到他們的工作流程中，圍繞應用程序進行大量測試，並確保構建的工作流程與我們在前幾天提到的 DevOps 原則保持一致。但在現實中，這將是很多時候對應用程序性能問題或類似問題進行故障排除。這回到了我最初的觀點和推理，我需要知道的程式語言是代碼用哪種語言編寫的？如果他們的應用程序是用 NodeJS 編寫的，如果你有 Go 或 Python 徽章，這不會有太大幫助。

## 為什麼選擇 Go

為什麼 Golang 是 DevOps 的下一個程式語言，Go 在近年來已成為一種非常流行的程式語言。根據 2021 年的 StackOverflow 調查，Go 在最想要的程式、腳本和標記語言中排名第四，Python 位居榜首，但請聽我說。[StackOverflow 2021 開發者調查 – 最想要的鏈接](https://insights.stackoverflow.com/survey/2021#section-most-loved-dreaded-and-wanted-programming-scripting-and-markup-languages)

正如我也提到的，一些最知名的 DevOps 工具和平台都是用 Go 編寫的，如 Kubernetes、Docker、Grafana 和 Prometheus。

Go 的一些特性使其非常適合 DevOps？

## Go 程序的構建和部署

在 DevOps 角色中使用像 Python 這樣被解釋的語言的優勢是，你不需要在運行之前編譯 Python 程序。特別是對於較小的自動化任務，你不想被需要編譯的構建過程拖慢，儘管 Go 是一種編譯的程式語言，**Go 直接編譯成機器碼**。Go 也以快速編譯時間而聞名。

## DevOps 的 Go vs Python

Go 程序是靜態鏈接的，這意味著當你編譯 Go 程序時，所有內容都包含在單個二進制可執行文件中，不需要需要在遠程機器上安裝的外部依賴項，這使得 Go 程序的部署變得容易，與使用外部庫的 Python 程序相比，你必須確保所有這些庫都安裝在你希望運行的遠程機器上。

Go 是一種與平台無關的語言，這意味著你可以為所有操作系統、Linux、Windows、macOS 等生成二進制可執行文件，並且非常容易做到。使用 Python，為特定操作系統創建這些二進制可執行文件並不容易。

Go 是一種性能非常高的語言，它具有快速編譯和快速運行時，資源使用率較低，如 CPU 和內存，特別是與 Python 相比，Go 語言中實現了許多優化，使其如此高性能。（下面的資源）

與 Python 不同，Python 通常需要使用第三方庫來實現特定的 Python 程序，Go 包含一個標準庫，其中包含你需要的 DevOps 的大部分功能，直接內置於其中。這包括功能文件處理、HTTP Web 服務、JSON 處理、對並發和並行的本機支持以及內置測試。

這絕不是把 Python 扔到公共汽車下，我只是給出我選擇 Go 的理由，但它們不是上面的 Go vs Python，通常是因為它有意義，因為我工作的公司用 Go 開發軟體，所以這就是原因。

我會說，一旦你有了，或者至少我被告知，因為我現在還沒有進入這一章的許多頁面，一旦你學會了你的第一種程式語言，學習其他語言就會變得更容易。你可能永遠不會在任何公司的任何地方有單一工作，在那裡你不必處理管理、架構、編排、調試 JavaScript 和 Node JS 應用程序。

## 資源

- [StackOverflow 2021 Developer Survey](https://insights.stackoverflow.com/survey/2021)
- [Why we are choosing Golang to learn](https://www.youtube.com/watch?v=7pLqIIAqZD4&t=9s)
- [Jake Wright - Learn Go in 12 minutes](https://www.youtube.com/watch?v=C8LgvuEBraI&t=312s)
- [Techworld with Nana - Golang full course - 3 hours 24 mins](https://www.youtube.com/watch?v=yyUHQIec83I)
- [**NOT FREE** Nigel Poulton Pluralsight - Go Fundamentals - 3 hours 26 mins](https://www.pluralsight.com/courses/go-fundamentals)
- [FreeCodeCamp - Learn Go Programming - Golang Tutorial for Beginners](https://www.youtube.com/watch?v=YS4e4q9oBaU&t=1025s)
- [Hitesh Choudhary - Complete playlist](https://www.youtube.com/playlist?list=PLRAV69dS1uWSR89FRQGZ6q9BR2b44Tr9N)

現在對於這個主題的接下來 6 天，我打算完成上面列出的一些資源並記錄我每天的筆記。你會注意到它們通常作為完整課程大約 3 小時，我想分享我的完整列表，這樣如果你有時間，你應該繼續前進，如果時間允許，完成每一個，我將堅持每天學習一小時。

我們[Day 8](day08.md)見.
