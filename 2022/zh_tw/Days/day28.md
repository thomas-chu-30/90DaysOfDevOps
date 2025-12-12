---
title: '#90DaysOfDevOps - The Big Picture: DevOps & The Cloud - Day 28'
published: false
description: 90DaysOfDevOps - The Big Picture DevOps & The Cloud
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048737
---

## 大局：DevOps 和雲

當談到雲計算和提供的內容時，它與 DevOps 精神和流程非常吻合。我們可以將雲計算視為帶來技術和服務，而 DevOps，正如我們之前多次提到的，是關於流程和流程改進。

但開始雲學習之旅是一個陡峭的過程，確保你知道並理解所有元素或為正確的價格點選擇最佳服務是令人困惑的。

![](Images/Day28_Cloud1.png)

公共雲是否需要 DevOps 心態？我在這裡的答案是否定的，但要真正利用雲計算並可能避免許多人遇到的那些大型雲帳單，重要的是將雲計算和 DevOps 結合起來思考。

如果我們從 40,000 英尺的角度來看公共雲的含義，它是關於將一些責任轉移到託管服務，使你和你的團隊能夠專注於更重要的方面，這應該是應用程序和最終用戶。畢竟公共雲只是別人的計算機。

![](Images/Day28_Cloud2.png)

在第一部分中，我想進入並更多地描述公共雲是什麼以及一些被稱為公共雲整體的構建塊。

### SaaS

第一個要涵蓋的領域是軟體即服務，此服務正在消除你可能曾經在本地運行的服務的幾乎所有管理開銷。讓我們考慮一下用於電子郵件的 Microsoft Exchange，這曾經是位於數據中心或樓梯下櫥櫃中的物理盒子。你需要餵養和澆水該服務器。我的意思是你需要保持更新，你將負責購買服務器硬件，很可能安裝操作系統，安裝所需的應用程序，然後保持補丁，如果出現任何問題，你必須進行故障排除並讓事情恢復運行。

哦，你還必須確保你正在備份數據，儘管這在大多數情況下也不會隨著 SaaS 改變。

SaaS 所做的，特別是 Microsoft 365，因為我提到了 Exchange，是消除管理開銷，他們提供一個服務，通過郵件提供你的 exchange 功能，還有許多其他生產力（Office 365）和存儲選項（OneDrive），總體上為最終用戶提供了很好的體驗。

其他 SaaS 應用程序被廣泛採用，如 Salesforce、SAP、Oracle、Google 和 Apple。所有這些都消除了必須管理更多堆棧的負擔。

我確信有一個關於 DevOps 和基於 SaaS 的應用程序的故事，但我很難找出它們可能是什麼。我知道 Azure DevOps 與 Microsoft 365 有一些很好的集成，我可能會查看並報告回來。

![](Images/Day28_Cloud3.png)

### 公共雲

接下來我們有公共雲，大多數人會以幾種不同的方式思考這個問題，有些人會將其視為僅超大型提供商，如 Microsoft Azure、Google Cloud Platform 和 AWS。

![](Images/Day28_Cloud4.png)

有些人還會將公共雲視為更廣泛的產品，包括那些超大型提供商，還包括世界各地的數千個 MSP。對於這篇文章，我們將考慮包括超大型提供商和 MSP 的公共雲，儘管稍後，我們將專門深入探討一個或多個超大型提供商以獲得基礎知識。

![](Images/Day28_Cloud5.png)
_數千家更多的公司可能會出現在這裡，我只是從我合作過並了解到的本地、區域、電信和全球品牌中挑選。_

我們在 SaaS 部分提到，雲消除了管理系統部分責任或負擔。如果 SaaS 我們看到很多抽象層被移除，即物理系統、網絡、存儲、操作系統，甚至在某種程度上是應用程序。當談到雲時，我們可以根據你的需求移除或保留各種級別的抽象。

我們已經提到了 SaaS，但關於公共雲，至少還有兩個要提及。

基礎設施即服務 - 你可以將此層視為虛擬機，但在本地你將不得不照顧物理層，在雲中這不是這種情況，物理是雲提供商的責任，你將管理和管理操作系統、數據和你希望運行的應用程序。

平台即服務 - 這繼續消除層的責任，這真的是關於你控制數據和應用程序，但不必擔心底層硬件或操作系統。

那裡有許多其他 aaS 產品，但這兩個是基礎。你可能會看到圍繞 StaaS（存儲即服務）的產品，為你提供存儲層，但不必擔心下面的硬件。或者你可能聽說過 CaaS 用於容器即服務，我們稍後會進入，另一個 aaS 我們將在接下來的 7 天中查看是 FaaS（功能即服務），在那裡也許你不需要一直運行的系統，你只希望一個函數在需要時執行。

公共雲可以提供你希望傳遞並為其付費的控制抽象層的方式有很多。

![](Images/Day28_Cloud6.png)

### 私有雲

擁有自己的數據中心並不是過去的事情，我認為這已經成為許多發現 OPEX 模型難以管理以及僅使用公共雲的技能集的公司的復興。

這裡要注意的重要事項是公共雲現在可能是你的責任，它將在你的場所。

我們在這個領域有一些有趣的事情發生，不僅是 VMware 主導了虛擬化時代和本地基礎設施環境。我們還有超大型提供商提供其公共雲的本地版本。

![](Images/Day28_Cloud7.png)

### 混合雲

繼公共和私有雲之後，我們還可以跨越這兩個環境，以在兩者之間提供靈活性，也許利用公共雲中可用的服務，但也利用本地或可能是規定你必須在本地存儲數據的規定的功能和特性。

![](Images/Day28_Cloud8.png)

將所有這些結合起來，我們有很多選擇來存儲和運行工作負載。

![](Images/Day28_Cloud9.png)

在我們進入特定的超大規模之前，我已經詢問了 Twitter 的力量，我們應該去哪裡？

![](Images/Day28_Cloud10.png)

[Link to Twitter Poll](https://twitter.com/MichaelCade1/status/1486814904510259208?s=20&t=x2n6QhyOXSUs7Pq0itdIIQ)

無論哪一個獲得最高百分比，我們將更深入地了解產品，我認為重要的是要提到，儘管所有這些服務都非常相似，這就是為什麼我說從一個開始，因為我發現在了解一個的基礎以及如何創建虛擬機、設置網絡等時，我能夠去其他並在這些領域快速提升。

無論哪種方式，我將分享一些涵蓋所有三個超大型提供商的優秀**免費**資源。

我還將構建一個場景，就像我在其他部分所做的那樣，我們可以在幾天內構建一些東西。

## 資源

- [Hybrid Cloud and MultiCloud](https://www.youtube.com/watch?v=qkj5W98Xdvw)
- [Microsoft Azure Fundamentals](https://www.youtube.com/watch?v=NKEFWyqJ5XA&list=WL&index=130&t=12s)
- [Google Cloud Digital Leader Certification Course](https://www.youtube.com/watch?v=UGRDM86MBIQ&list=WL&index=131&t=10s)
- [AWS Basics for Beginners - Full Course](https://youtu.be/k1RI5locZE4?si=0wPB2cdTY5hHgjl5)

我們[Day 29](day29.md)見
