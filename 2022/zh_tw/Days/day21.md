---
title: '#90DaysOfDevOps - The Big Picture: DevOps and Networking - Day 21'
published: false
description: 90DaysOfDevOps - The Big Picture DevOps and Networking
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048761
---

## 大局：DevOps 和網絡

與所有部分一樣，我使用開放和免費的培訓材料，很多內容可以歸因於其他人。在網絡部分的情況下，顯示的大部分內容來自 [Practical Networking](https://www.practicalnetworking.net/) 的免費 [Networking Fundamentals series](https://www.youtube.com/playlist?list=PLIFyRwBY_4bRLmKfP1KnZA6rZbRHtxmXi)。它在資源中以及鏈接中都有提及，但從社群的角度來看，強調這一點是合適的，我利用這門課程來幫助自己更好地理解技術的特定領域。這個存儲庫是我記筆記的存儲庫，並使社群能夠從中受益以及列出的資源。

歡迎來到第 21 天！我們將在接下來的 7 天中進入網絡，網絡和 DevOps 是總體主題，但我們也需要了解一些網絡基礎知識。

最終，正如我們之前所說，DevOps 是關於組織內的文化和流程變革，正如我們所討論的，這可以是虛擬機、容器或 Kubernetes，但也可以是網絡，如果我們對基礎設施使用這些 DevOps 原則，那必須包括網絡，更從 DevOps 的角度來看，你還需要了解網絡，如不同的拓撲和我們可用的網絡工具和堆棧。

我認為我們應該使用基礎設施即代碼來配置我們的網絡設備，並像虛擬機一樣自動化一切，但要做到這一點，我們必須對我們正在自動化的內容有很好的理解。

### 什麼是 NetDevOps | 網絡 DevOps？

你可能還會聽到術語 Network DevOps 或 NetDevOps。也許你已經是網絡工程師，並且對基礎設施中的網絡組件有很好的掌握，你了解圍繞網絡使用的元素，如 DHCP、DNS、NAT 等。你還將對硬件或軟體定義的網絡選項、交換機、路由器等有很好的理解。

但如果你不是網絡工程師，那麼我們可能需要在這些領域中的一些領域獲得基礎知識，以便我們能夠理解網絡 DevOps 的最終目標。

但關於這些術語，我們可以將 NetDevOps 或 Network DevOps 視為將 DevOps 原則和實踐應用於網絡，將版本控制和自動化工具應用於網絡創建、測試、監控和部署。

如果我們認為網絡 DevOps 必須需要自動化，我們之前提到過 DevOps 打破團隊之間的孤島。如果網絡團隊不改變為類似的模型和流程，那麼它們就會成為瓶頸，甚至整體失敗。

使用圍繞供應、配置、測試、版本控制和部署的自動化原則是一個很好的開始。自動化總體上將實現部署速度、網絡基礎設施的穩定性以及持續改進，以及一旦經過測試，流程可以在多個環境中共享。例如，在一個環境中經過充分測試的網絡策略可以快速在另一個位置使用，因為這是在代碼中的性質，而不是之前可能是手動編寫的流程。
可以在這裡找到這種思維的一個非常好的觀點和概述。[Network DevOps](https://www.thousandeyes.com/learning/techtorials/network-devops)

## 網絡基礎知識

讓我們暫時忘記 DevOps 方面的事情，現在我們需要非常簡要地了解一些網絡基礎知識。

### 網絡設備

如果你更喜歡視頻形式的內容，請查看 Practical Networking 的這些視頻：

* [Network Devices - Hosts, IP Addresses, Networks - Networking Fundamentals - Lesson 1a](https://www.youtube.com/watch?v=bj-Yfakjllc&list=PLIFyRwBY_4bRLmKfP1KnZA6rZbRHtxmXi&index=1)
* [Network Devices - Hub, Bridge, Switch, Router - Networking Fundamentals - Lesson 1b](https://www.youtube.com/watch?v=H7-NR3Q3BeI&list=PLIFyRwBY_4bRLmKfP1KnZA6rZbRHtxmXi&index=2)

**主機**是發送或接收流量的任何設備。

![](Images/Day21_Networking1.png)

**IP 地址**是每個主機的身份。

![](Images/Day21_Networking2.png)

**網絡**是在主機之間傳輸流量的東西。如果我們沒有網絡，將會有大量手動移動數據！

需要類似連接性的主機的邏輯組。

![](Images/Day21_Networking3.png)

**交換機**促進網絡**內**的通信。交換機在主機之間轉發數據包。交換機直接將數據包發送到主機。

- 網絡：需要類似連接性的主機分組。
- 網絡上的主機共享相同的 IP 地址空間。

![](Images/Day21_Networking4.png)

**路由器**促進網絡之間的通信。正如我們之前所說，交換機負責網絡內的通信，路由器允許我們將這些網絡連接在一起，或者至少在允許的情況下讓它們相互訪問。

路由器可以提供流量控制點（安全、過濾、重定向）現在越來越多的交換機也提供其中一些功能。

路由器學習它們連接到的網絡。這些被稱為路由，路由表是路由器知道的所有網絡。

路由器在它們連接到的網絡中有一個 IP 地址。這個 IP 也將是每個主機離開其本地網絡的方式，也稱為網關。

路由器還創建了我之前提到的網絡層次結構。

![](Images/Day21_Networking5.png)

## 交換機 vs 路由器

**路由**是在網絡之間移動數據的過程。

- 路由器是主要目的是路由的設備。

**交換**是在網絡內移動數據的過程。

- 交換機是主要目的是交換的設備。

這是設備的基礎概述，因為我們知道有許多不同的網絡設備，例如：

- 接入點
- 防火牆
- 負載平衡器
- 第 3 層交換機
- IDS / IPS
- 代理
- 虛擬交換機
- 虛擬路由器

儘管所有這些設備都將執行路由和/或交換。

在接下來的幾天裡，我們將更多地了解這個列表。

- OSI 模型
- 網絡協議
- DNS（域名系統）
- NAT
- DHCP
- 子網

## 資源

* [Networking Fundamentals](https://www.youtube.com/playlist?list=PLIFyRwBY_4bRLmKfP1KnZA6rZbRHtxmXi)
* [Computer Networking full course](https://www.youtube.com/watch?v=IPvYjXCsTg8)

我們[Day22](day22.md)見
