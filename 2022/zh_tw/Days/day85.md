---
title: '#90DaysOfDevOps - 數據服務 - 第 85 天'
published: false
description: 90DaysOfDevOps - 數據服務
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048781
---

## 數據服務

資料庫將是我們在環境中遇到的最常見的數據服務。我想利用本節來探索其中一些不同類型的資料庫以及它們各自的一些用例。我們在整個挑戰中使用和看到了一些。

從應用程式開發的角度來看，選擇正確的數據服務或資料庫將是應用程式性能和可擴展性的巨大決定。

https://www.youtube.com/watch?v=W2Z7fbCLSTw

### 鍵值

鍵值資料庫是一種非關係型資料庫，使用簡單的鍵值方法來存儲數據。鍵值資料庫將數據存儲為鍵值對的集合，其中鍵用作唯一標識符。鍵和值都可以是任何東西，從簡單物件到複雜的複合物件。鍵值資料庫高度可分區，允許在其他類型的資料庫無法達到的規模上進行水平擴展。

鍵值資料庫的一個範例是 Redis。

_Redis 是一個內存數據結構存儲，用作分佈式、內存鍵值資料庫、緩存和消息代理，具有可選的持久性。Redis 支持不同類型的抽象數據結構，如字串、列表、映射、集合、排序集合、HyperLogLogs、位圖、流和空間索引。_

![](Images/Day85_Data1.png)

正如你可以從 Redis 的描述中看到的，這意味著我們的資料庫很快，但我們在空間上受到限制作為權衡。此外，沒有查詢或連接，這意味著數據建模選項非常有限。

最適合：

- 緩存
- 發布/訂閱
- 排行榜
- 購物車

通常用作另一個持久數據層之上的緩存。

### 寬列

寬列資料庫是一個 NoSQL 資料庫，它將數據存儲組織成靈活的列，可以分佈在多個伺服器或資料庫節點上，使用多維映射通過列、行和時間戳來引用數據。

_Cassandra 是一個免費和開源的分佈式寬列存儲 NoSQL 資料庫管理系統，旨在處理跨許多商品伺服器的大量數據，提供高可用性，沒有單點故障。_

![](Images/Day85_Data2.png)

無模式，這意味著可以處理非結構化數據，但這可以被視為某些工作負載的好處。

最適合：

- 時間序列
- 歷史記錄
- 高寫入、低讀取

### 文檔

文檔資料庫（也稱為面向文檔的資料庫或文檔存儲）是將資訊存儲在文檔中的資料庫。

_MongoDB 是一個源可用的跨平台面向文檔的資料庫程式。分類為 NoSQL 資料庫程式，MongoDB 使用類似 JSON 的文檔和可選模式。MongoDB 由 MongoDB Inc. 開發，並在伺服器端公共許可證下獲得許可。_

![](Images/Day85_Data3.png)

NoSQL 文檔資料庫允許企業存儲簡單數據，而無需使用複雜的 SQL 代碼。快速存儲，不影響可靠性。

最適合：

- 大多數應用程式
- 遊戲
- 物聯網

### 關係型

如果你對資料庫不熟悉，但你知道它們，我猜你遇到過關係型資料庫。

關係型資料庫是基於數據關係模型的數字資料庫，由 E. F. Codd 在 1970 年提出。用於維護關係型資料庫的系統是關係型資料庫管理系統。許多關係型資料庫系統都有使用 SQL 查詢和維護資料庫的選項。

_MySQL 是一個開源關係型資料庫管理系統。它的名稱是「My」（聯合創始人 Michael Widenius 的女兒的名字）和「SQL」（結構化查詢語言的縮寫）的組合。_

MySQL 是關係型資料庫的一個範例，還有很多其他選項。

![](Images/Day85_Data4.png)

在研究關係型資料庫時，術語或縮寫 **ACID** 被多次提及，（原子性、一致性、隔離性、持久性）是一組資料庫事務屬性，旨在保證數據有效性，儘管有錯誤、電源故障和其他事故。在資料庫的上下文中，滿足 ACID 屬性的資料庫操作序列（可以被視為數據上的單個邏輯操作）稱為事務。例如，從一個銀行帳戶到另一個銀行帳戶的資金轉移，即使涉及多個更改，如借記一個帳戶和貸記另一個帳戶，也是一個事務。

最適合：

- 大多數應用程式（它已經存在多年，並不意味著它是最好的）

對於非結構化數據或擴展能力，它不是理想的，其中一些其他 NoSQL 提到的為某些工作負載提供了更好的擴展能力。

### 圖

圖資料庫存儲節點和關係而不是表或文檔。數據的存儲就像你可能在白板上草擬想法一樣。你的數據存儲時不受預定義模型的限制，允許非常靈活地思考和使用它。

_Neo4j 是由 Neo4j, Inc. 開發的圖資料庫管理系統。其開發人員將其描述為具有原生圖存儲和處理的 ACID 兼容事務資料庫_

最適合：

- 圖
- 知識圖
- 推薦引擎

### 搜索引擎

在上一節中，我們以 Elasticsearch 的方式使用了搜索引擎資料庫。

搜索引擎資料庫是一種非關係型資料庫，專用於搜索數據內容。搜索引擎資料庫使用索引對數據中的相似特徵進行分類並促進搜索能力。

_Elasticsearch 是一個基於 Lucene 庫的搜索引擎。它提供了一個分佈式、多租戶功能的全文搜索引擎，具有 HTTP Web 界面和無模式 JSON 文檔。_

最適合：

- 搜索引擎
- 自動完成
- 日誌搜索

### 多模型

多模型資料庫是一個資料庫管理系統，旨在支持針對單一、整合後端的多個數據模型。相比之下，大多數資料庫管理系統都圍繞單一數據模型組織，該模型確定如何組織、存儲和操作數據。文檔、圖、關係和鍵值模型是可能由多模型資料庫支持的數據模型的範例。

_Fauna 是一個靈活、開發人員友好的事務資料庫，作為安全且可擴展的雲 API 交付，具有原生 GraphQL。_

最適合：

- 你不必選擇數據模型
- ACID 兼容
- 快速
- 無配置開銷
- 你想如何消費數據，讓雲做繁重的工作

這將結束這個資料庫概述部分，無論你從事什麼行業，你都會遇到資料庫的一個領域。然後我們將採用其中一些範例，並在本節後續部分查看數據管理，特別是這些數據服務的保護和存儲。

我在下面連結了大量資源，你可以誠實地花 90 年時間深入研究所有資料庫類型以及隨之而來的一切。

## 資源

- [Redis Crash Course - the What, Why and How to use Redis as your primary database](https://www.youtube.com/watch?v=OqCK95AS-YE)
- [Redis: How to setup a cluster - for beginners](https://www.youtube.com/watch?v=GEg7s3i6Jak)
- [Redis on Kubernetes for beginners](https://www.youtube.com/watch?v=JmCn7k0PlV4)
- [Intro to Cassandra - Cassandra Fundamentals](https://www.youtube.com/watch?v=YjYWsN1vek8)
- [MongoDB Crash Course](https://www.youtube.com/watch?v=ofme2o29ngU)
- [MongoDB in 100 Seconds](https://www.youtube.com/watch?v=-bt_y4Loofg)
- [What is a Relational Database?](https://www.youtube.com/watch?v=OqjJjpjDRLc)
- [Learn PostgreSQL Tutorial - Full Course for Beginners](https://www.youtube.com/watch?v=qw--VYLpxG4)
- [MySQL Tutorial for Beginners [Full Course]](https://www.youtube.com/watch?v=7S_tz1z_5bA)
- [What is a graph database? (in 10 minutes)](https://www.youtube.com/watch?v=REVkXVxvMQE)
- [What is Elasticsearch?](https://www.youtube.com/watch?v=ZP0NmfyfsoM)
- [FaunaDB Basics - The Database of your Dreams](https://www.youtube.com/watch?v=2CipVwISumA)
- [Fauna Crash Course - Covering the Basics](https://www.youtube.com/watch?v=ihaB7CqJju0)

我們[第 86 天](day86.md)見