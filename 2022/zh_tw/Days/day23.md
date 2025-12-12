---
title: '#90DaysOfDevOps - Network Protocols - Day 23'
published: false
description: 90DaysOfDevOps - Network Protocols
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048704
---

下面的內容主要來自 Practical Networking 的 [Networking Fundamentals series](https://www.youtube.com/playlist?list=PLIFyRwBY_4bRLmKfP1KnZA6rZbRHtxmXi)。如果你更喜歡視頻形式的內容，請查看此視頻：

* [Network Protocols - ARP, FTP, SMTP, HTTP, SSL, TLS, HTTPS, DNS, DHCP](https://www.youtube.com/watch?v=E5bSumTAHZE&list=PLIFyRwBY_4bRLmKfP1KnZA6rZbRHtxmXi&index=12)


## 網絡協議

形成標準的規則和消息集。互聯網標準。

- ARP - 地址解析協議

如果你真的想深入了解 ARP，你可以在這裡閱讀互聯網標準。[RFC 826](https://datatracker.ietf.org/doc/html/rfc826)

將 IP 地址連接到固定物理機器地址，也稱為跨第 2 層網絡的 MAC 地址。

![](Images/Day23_Networking1.png)

- FTP - 文件傳輸協議

允許從源到目標傳輸文件。通常，此過程是經過身份驗證的，但如果配置為使用匿名訪問，則具有此能力。你現在會更頻繁地看到 FTPS，它為客戶端提供到 FTP 服務器的 SSL/TLS 連接以獲得更好的安全性。此協議將在 OSI 模型的應用層中找到。

![](Images/Day23_Networking2.png)

- SMTP - 簡單郵件傳輸協議

用於電子郵件傳輸，郵件服務器使用 SMTP 發送和接收郵件消息。你會發現即使使用 Microsoft 365，SMTP 協議也用於相同的目的。

![](Images/Day23_Networking3.png)

- HTTP - 超文本傳輸協議

HTTP 是互聯網和瀏覽內容的基礎。使我們能夠輕鬆訪問我們最喜歡的網站。HTTP 仍然被大量使用，但 HTTPS 在大多數你最喜歡的網站上被使用或應該被使用。

![](Images/Day23_Networking4.png)

- SSL - 安全套接字層 | TLS - 傳輸層安全

TLS 已經取代了 SSL，TLS 是一個**加密協議**，提供網絡上的安全通信。它可以並且將在郵件、即時消息和其他應用程序中找到，但最常見的是用於保護 HTTPS。

![](Images/Day23_Networking5.png)

- HTTPS - 使用 SSL/TLS 保護的 HTTP

HTTP 的擴展，用於網絡上的安全通信，HTTPS 使用上面提到的 TLS 加密。這裡的重點是在主機之間交換數據時帶來身份驗證、隱私和完整性。

![](Images/Day23_Networking6.png)

- DNS - 域名系統

DNS 用於映射人類友好的域名，例如我們都知道 [google.com](https://google.com)，但如果你要打開瀏覽器並輸入 [8.8.8.8](https://8.8.8.8)，你會得到我們幾乎都知道的 Google。但是，祝你好運，試圖記住所有網站的所有 IP 地址，其中一些我們甚至使用 google 來查找信息。

這就是 DNS 的用武之地，它確保主機、服務和其他資源可以訪問。

在所有主機上，如果它們需要互聯網連接，那麼它們必須有 DNS 才能解析這些域名。DNS 是一個你可以花數天和數年學習的領域。我還想說，根據經驗，DNS 是網絡錯誤的最常見原因。不確定網絡工程師是否會同意這一點。

![](Images/Day23_Networking7.png)

- DHCP - 動態主機配置協議

我們已經討論了很多關於使我們的主機工作所需的協議，無論是訪問互聯網還是在彼此之間傳輸文件。

我們需要在每個主機上實現這兩個任務的 4 件事。

- IP 地址
- 子網掩碼
- 默認網關
- DNS

我們已經介紹了 IP 地址是主機在其所在網絡上的唯一地址，我們可以將其視為我們的門牌號。

我們將很快介紹子網掩碼，但你可以將其視為郵政編碼或郵遞區號。

默認網關通常是我們網絡上路由器的 IP，為我們提供第 3 層連接。你可以將其視為允許我們離開街道的單一道路。

然後我們有 DNS，正如我們剛剛介紹的那樣，幫助我們將複雜的公共 IP 地址轉換為更合適和可記住的域名。也許我們可以將其視為巨大的分揀辦公室，以確保我們收到正確的郵件。

正如我所說，每個主機都需要這 4 件事，如果你有 1000 或 10,000 個主機，那麼這將需要很長時間來單獨確定每一個。這就是 DHCP 的用武之地，它允許你為網絡確定一個範圍，然後此協議將分發到網絡中所有可用的主機。

另一個例子是你走進咖啡店，拿一杯咖啡，然後用筆記本電腦或手機坐下來，讓我們稱之為你的主機。你將主機連接到咖啡店 WiFi，你獲得互聯網訪問，消息和郵件開始通過，你可以瀏覽網頁和社交媒體。當你連接到咖啡店 WiFi 時，你的機器將從專用 DHCP 服務器或最可能從也處理 DHCP 的路由器獲取 DHCP 地址。

![](Images/Day23_Networking8.png)

### 子網劃分

子網是 IP 網絡的邏輯細分。

子網將大型網絡分解為更小、更易管理的網絡，這些網絡運行更高效。

每個子網都是更大網絡的邏輯細分。具有足夠子網的連接設備共享公共 IP 地址標識符，使它們能夠相互通信。

路由器管理子網之間的通信。

子網的大小取決於連接需求和使用的網絡技術。

組織負責在可用地址空間的限制內確定子網的數量和大小，詳細信息仍然對該組織本地。子網也可以進一步細分為更小的子網，用於點對點鏈路或支持少數設備的子網絡等。

在其他優勢中，將大型網絡分段為子網可以實現 IP 地址重新分配並緩解網絡擁塞，簡化網絡通信和效率。

子網還可以改善網絡安全。
如果網絡的一部分受到損害，可以對其進行隔離，使壞人難以在更大的網絡中移動。

![](Images/Day23_Networking9.png)

## 資源

- [Networking Fundamentals](https://www.youtube.com/playlist?list=PLIFyRwBY_4bRLmKfP1KnZA6rZbRHtxmXi)
- [Subnetting Mastery](https://www.youtube.com/playlist?list=PLIFyRwBY_4bQUE4IB5c4VPRyDoLgOdExE)
- [Computer Networking full course](https://www.youtube.com/watch?v=IPvYjXCsTg8)

我們[Day 24](day24.md)見
