---
title: '#90DaysOfDevOps - The OSI Model - The 7 Layers - Day 22'
published: false
description: 90DaysOfDevOps - The OSI Model - The 7 Layers
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049037
---

下面的內容主要來自 Practical Networking 的 [Networking Fundamentals series](https://www.youtube.com/playlist?list=PLIFyRwBY_4bRLmKfP1KnZA6rZbRHtxmXi)。如果你更喜歡視頻形式的內容，請查看這兩個視頻：

* [The OSI Model: A Practical Perspective - Layers 1 / 2 / 3](https://www.youtube.com/watch?v=LkolbURrtTs&list=PLIFyRwBY_4bRLmKfP1KnZA6rZbRHtxmXi&index=3)
* [The OSI Model: A Practical Perspective - Layers 4 / 5+](https://www.youtube.com/watch?v=0aGqGKrRE0g&list=PLIFyRwBY_4bRLmKfP1KnZA6rZbRHtxmXi&index=4)

## OSI 模型 - 7 層

網絡作為一個行業的總體目的是允許兩個主機共享數據。在網絡之前，如果我想從這個主機獲取數據到這個主機，我必須將某些東西插入這個主機，然後走到另一個主機並將其插入另一個主機。

網絡允許我們通過允許主機自動跨線路共享數據來自動化這一點，為了讓這些主機做到這一點，它們必須遵循一組規則。

這與任何語言沒有什麼不同。英語有一組規則，兩個說英語的人必須遵循。西班牙語有自己的規則集。法語有自己的規則集，而網絡也有自己的規則集

網絡的規則分為七個不同的層，這些層被稱為 OSI 模型。

### OSI 模型介紹

OSI 模型（開放系統互連模型）是用於描述網絡系統功能的框架。OSI 模型將計算功能表徵為通用規則和需求集，以支持不同產品和軟體之間的互操作性。在 OSI 參考模型中，計算系統之間的通信分為七個不同的抽象層：**物理、數據鏈路、網絡、傳輸、會話、表示和應用**。

![](Images/Day22_Networking1.png)

### 物理層

OSI 模型中的第 1 層，這被稱為物理層，能夠通過某種方式（無論是物理電纜還是我們也可以考慮此層中的 Wi-Fi）從一個主機獲取數據到另一個主機的前提。我們可能還會看到一些更傳統的硬件，如集線器和中繼器，用於將數據從一個主機傳輸到另一個主機。

![](Images/Day22_Networking2.png)

### 數據鏈路層

第 2 層，數據鏈路層實現節點到節點的傳輸，其中數據被打包成幀。還有一個錯誤糾正級別，可能發生在物理層。這也是我們引入或首次看到 MAC 地址的地方。

這是我們看到交換機首次提及的地方，我們在網絡的第一天 [Day 21](day21.md) 中介紹過

![](Images/Day22_Networking3.png)

### 網絡層

你可能聽說過第 3 層交換機或第 2 層交換機。在我們的 OSI 模型第 3 層中，網絡層的目標是端到端交付，這是我們看到 IP 地址也在第一天概述中提及的地方。

路由器和主機存在於第 3 層，記住路由器是在多個網絡之間路由的能力。任何帶有 IP 的東西都可以被視為第 3 層。

![](Images/Day22_Networking4.png)

那麼為什麼我們在第 2 層和第 3 層都需要尋址方案？（MAC 地址 vs IP 地址）

如果我們考慮從一個主機獲取數據到另一個主機，每個主機都有一個 IP 地址，但中間有幾個交換機和路由器。每個設備都有該第 2 層 MAC 地址。

第 2 層 MAC 地址將僅從主機到交換機/路由器，它專注於跳躍，而第 3 層 IP 地址將與該數據包一起保留，直到它到達其終端主機。（端到端）

IP 地址 - 第 3 層 = 端到端交付

MAC 地址 - 第 2 層 = 跳躍到跳躍交付

現在有一個網絡協議，我們將深入探討，但不是今天，稱為 ARP（地址解析協議），它將我們的第 3 層和第 2 層地址鏈接起來。

### 傳輸層

服務到服務交付，第 4 層用於區分數據流。就像第 3 層和第 2 層都有它們的尋址方案一樣，在第 4 層中我們有端口。

![](Images/Day22_Networking5.png)

### 會話、表示、應用層

第 5、6、7 層之間的區別是或已經變得有些模糊。

值得查看 [TCP IP Model](https://www.geeksforgeeks.org/tcp-ip-model/) 以獲得更新的理解。

現在讓我們嘗試解釋當主機使用此網絡堆棧相互通信時發生的事情。此主機有一個應用程序，將生成要發送到另一個主機的數據。

源主機將經歷的是所謂的封裝過程。該數據將首先發送到第 4 層。

第 4 層將向該數據添加一個標頭，這可以促進第 4 層的目標，即服務到服務交付。這將是使用 TCP 或 UDP 的端口。它還將包括源端口和目標端口。

這也可能被稱為段（數據和端口）

此段將沿 OSI 堆棧向下傳遞到第 3 層，網絡層，網絡層將向此數據添加另一個標頭。
此標頭將促進第 3 層的目標，即端到端交付，這意味著在此標頭中你將有源 IP 地址和目標 IP，標頭加上數據也可能被稱為數據包。

第 3 層然後將該數據包移交給第 2 層，第 2 層將再次向該數據添加另一個標頭，以實現第 2 層的跳躍到跳躍交付目標，這意味著此標頭將包括源和目標 mac 地址。
當你有第 2 層標頭和數據時，這被稱為幀。

然後該幀被轉換為 1 和 0，並通過第 1 層物理電纜或 wifi 發送。

![](Images/Day22_Networking6.png)

我確實在上面提到了每個層的標頭加上數據的命名，但也決定將其繪製出來。

![](Images/Day22_Networking7.png)

發送數據的應用程序正在發送到某個地方，因此接收在某種程度上是反向的，以將其恢復到堆棧並進入接收主機。

![](Images/Day22_Networking8.png)

## 資源

* [Networking Fundamentals](https://www.youtube.com/playlist?list=PLIFyRwBY_4bRLmKfP1KnZA6rZbRHtxmXi)
- [Computer Networking full course](https://www.youtube.com/watch?v=IPvYjXCsTg8)

我們[Day23](day23.md)見
