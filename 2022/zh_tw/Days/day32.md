---
title: '#90DaysOfDevOps - Microsoft Azure Storage Models - Day 32'
published: false
description: 90DaysOfDevOps - Microsoft Azure Storage Models
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048775
---

## Microsoft Azure 存儲模型

### 存儲服務

- Azure 存儲服務由存儲帳戶提供。
- 存儲帳戶主要通過 REST API 訪問。
- 存儲帳戶必須有一個唯一的名稱，該名稱是 DNS 名稱的一部分 `<Storage Account name>.core.windows.net`
- 各種複製和加密選項。
- 位於資源組內

我們可以通過在 Azure 門戶頂部的搜索欄中搜索 Storage Group 來創建存儲組。

![](Images/Day32_Cloud1.png)

然後我們可以運行步驟來創建存儲帳戶，記住此名稱需要唯一，還需要全部小寫，沒有空格，但可以包含數字。

![](Images/Day32_Cloud2.png)

我們還可以選擇我們希望對存儲帳戶和我們在此存儲的任何內容的冗餘級別。列表越往下，選項越昂貴，但數據的傳播也越多。

甚至默認冗餘選項也為我們提供了 3 份數據副本。

[Azure Storage Redundancy](https://docs.microsoft.com/en-us/azure/storage/common/storage-redundancy)

上述鏈接的摘要如下：

- **本地冗餘存儲** - 在主區域的單個數據中心內複製數據三次。
- **地理冗餘存儲** - 使用 LRS 在主區域的單個物理位置內同步複製數據三次。
- **區域冗餘存儲** - 在主區域的三個 Azure 可用性區域中同步複製 Azure 存儲數據。
- **地理區域冗餘存儲** - 結合跨可用性區域的冗餘提供的高可用性以及地理複製提供的區域中斷保護。GZRS 存儲帳戶中的數據在主區域的三個 Azure 可用性區域中複製，並且還複製到第二個地理區域以保護免受區域災難。

![](Images/Day32_Cloud3.png)

只是回到性能選項。我們有標準和高級可供選擇。我們在演練中選擇了標準，但高級為你提供了一些特定選項。

![](Images/Day32_Cloud4.png)

然後在下拉菜單中，你可以看到我們有三個選項可供選擇。

![](Images/Day32_Cloud5.png)

存儲帳戶還有更多高級選項，但現在我們不需要進入這些領域。這些選項圍繞加密和數據保護。

### 託管磁盤

可以通過幾種不同的方式實現存儲訪問。

通過以下方式進行身份驗證訪問：

- 用於完全控制的共享密鑰。
- 用於委派的細粒度訪問的共享訪問簽名。
- Azure Active Directory（可用時）

公共訪問：

- 還可以授予公共訪問權限以啟用匿名訪問，包括通過 HTTP。
- 一個例子是在塊 blob 中託管基本內容和文件，以便瀏覽器可以查看和下載此數據。

如果你從另一個 Azure 服務訪問存儲，流量保持在 Azure 內。

當談到存儲性能時，我們有兩種不同的類型：

- **標準** - 最大 IOPS 數量
- **高級** - 保證的 IOPS 數量

IOPS => 每秒輸入/輸出操作。

在為任務選擇正確的存儲時，還需要考慮非託管磁盤和託管磁盤之間的差異。

### 虛擬機存儲

- 虛擬機 OS 磁盤通常存儲在持久存儲上。
- 一些無狀態工作負載不需要持久存儲，降低延遲是更大的好處。
- 有一些 VM 支持在節點本地存儲上創建的臨時 OS 託管磁盤。
  - 這些也可以與 VM 規模集一起使用。

託管磁盤是持久的塊存儲，可與 Azure 虛擬機一起使用。你可以擁有 Ultra Disk Storage、Premium SSD、Standard SSD 或 Standard HDD。它們還具有一些特性。

- 快照和鏡像支持
- SKU 之間的簡單移動
- 與可用性集結合時更好的可用性
- 根據磁盤大小而非消耗的存儲計費。

## 歸檔存儲

- **冷層** - 冷層存儲可用於塊和追加 blob。
  - 較低的存儲成本
  - 較高的交易成本。
- **歸檔層** - 歸檔存儲可用於塊 BLOB。
  - 這是在每個 BLOB 的基礎上配置的。
  - 更便宜的成本，更長的數據檢索延遲。
  - 與常規 Azure 存儲相同的數據持久性。
  - 可以根據需要啟用自定義數據分層。

### 文件共享

從上面創建存儲帳戶，我們現在可以創建文件共享。

![](Images/Day32_Cloud6.png)

這將在 Azure 中提供 SMB2.1 和 3.0 文件共享。

可在 Azure 內使用，也可通過 SMB3 和對互聯網開放的端口 445 在外部使用。

在 Azure 中提供共享文件存儲。

可以使用標準 SMB 客戶端以及 REST API 進行映射。

你可能還會注意到 [Azure NetApp Files](https://vzilla.co.uk/vzilla-blog/azure-netapp-files-how)（SMB 和 NFS）

### 緩存和媒體服務

Azure Content Delivery Network 提供靜態 Web 內容的緩存，位置遍佈世界各地。

Azure Media Services 提供媒體轉碼技術以及播放服務。

## Microsoft Azure 數據庫模型

回到 [Day 28](day28.md)，我們涵蓋了各種服務選項。其中之一是 PaaS（平台即服務），你可以在其中抽象大量基礎設施和操作系統，你將控制應用程序或在這種情況下是數據庫模型。

### 關係數據庫

Azure SQL Database 提供基於 Microsoft SQL Server 的關係數據庫即服務。

這是運行最新 SQL 分支的 SQL，具有數據庫兼容級別，可在需要特定功能版本時使用。

關於如何配置這一點有幾個選項，我們可以提供單個數據庫，在實例中提供一個數據庫，而彈性池允許共享容量池並集體擴展的多個數據庫。

這些數據庫實例可以像常規 SQL 實例一樣訪問。

為 MySQL、PostgreSQL 和 MariaDB 提供額外的託管產品。

![](Images/Day32_Cloud7.png)

### NoSQL 解決方案

Azure Cosmos DB 是一個與方案無關的 NoSQL 實現。

99.99% SLA

全球分佈的數據庫，在全球任何地方的第 99 百分位數具有單位數延遲，具有自動歸屬。

利用分區鍵進行數據的分區/分片/分發。

支持各種數據模型（文檔、鍵值、圖形、列友好）

支持各種 API（DocumentDB SQL、MongoDB、Azure Table Storage 和 Gremlin）

![](Images/Day32_Cloud9.png)

基於 [CAP theorem](https://en.wikipedia.org/wiki/CAP_theorem) 提供各種一致性模型。

![](Images/Day32_Cloud8.png)

### 緩存

在不深入研究 Redis 等緩存系統的情況下，我想提到 Microsoft Azure 有一項名為 Azure Cache for Redis 的服務。

Azure Cache for Redis 提供基於 Redis 軟體的內存數據存儲。

- 它是開源 Redis Cache 的實現。
  - 託管的、安全的 Redis 緩存實例。
  - 提供不同的層
  - 必須更新應用程序以利用緩存。
  - 針對具有高讀取要求相比寫入的應用程序。
  - 基於鍵值存儲。

![](Images/Day32_Cloud10.png)

我感謝過去幾天在 Microsoft Azure 上進行了大量筆記和理論，但我想在我們進入這些組件如何組合和工作的實際方面之前涵蓋構建塊。

在我們可以啟動一些基於場景的服務部署之前，我們還有一點關於網絡的理論。我們還想查看一些與 Microsoft Azure 交互的不同方式，而不僅僅是使用我們迄今為止一直在使用的門戶。

## 資源

- [Hybrid Cloud and MultiCloud](https://www.youtube.com/watch?v=qkj5W98Xdvw)
- [Microsoft Azure Fundamentals](https://www.youtube.com/watch?v=NKEFWyqJ5XA&list=WL&index=130&t=12s)
- [Google Cloud Digital Leader Certification Course](https://www.youtube.com/watch?v=UGRDM86MBIQ&list=WL&index=131&t=10s)
- [AWS Basics for Beginners - Full Course](https://www.youtube.com/watch?v=ulprqHHWlng&t=5352s)

我們[Day 33](day33.md)見
