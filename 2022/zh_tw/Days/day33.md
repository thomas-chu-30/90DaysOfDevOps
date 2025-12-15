---
title: '#90DaysOfDevOps - Microsoft Azure Networking Models + Azure Management - Day 33'
published: false
description: 90DaysOfDevOps - Microsoft Azure Networking Models + Azure Management
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048706
---

## Microsoft Azure 網絡模型 + Azure 管理

好像今天是 Microsoft Azure 及其 12 週年紀念日！（2022 年 2 月 1 日）無論如何，我們將涵蓋 Microsoft Azure 內的網絡模型以及 Azure 的一些管理選項。到目前為止，我們只使用了 Azure 門戶，但我們已經提到了其他可以用於驅動和創建平台內資源的領域。

## Azure 網絡模型

### 虛擬網絡

- 虛擬網絡是在 Azure 中創建的構造。
- 虛擬網絡有一個或多個分配給它的 IP 範圍。
- 虛擬網絡存在於區域內的訂閱內。
- 在虛擬網絡中創建虛擬子網以分解網絡範圍。
- 虛擬機放置在虛擬子網中。
- 虛擬網絡內的所有虛擬機都可以通信。
- 每個虛擬網絡 65,536 個私有 IP。
- 只為從區域的出口流量付費。（離開區域的數據）
- 支持 IPv4 和 IPv6。
  - IPv6 用於面向公共的和虛擬網絡內。

我們可以將 Azure 虛擬網絡比作 AWS VPC。但是，有一些差異需要注意：

- 在 AWS 中，創建了一個默認 VNet，這在 Microsoft Azure 中不是這種情況，你必須根據要求創建第一個虛擬網絡。
- 默認情況下，Azure 中的所有虛擬機都可以訪問互聯網的 NAT。沒有像 AWS 那樣的 NAT 網關。
- 在 Microsoft Azure 中，沒有私有或公共子網的概念。
- 公共 IP 是可以分配給 vNIC 或負載平衡器的資源。
- 虛擬網絡和子網有自己的 ACL，支持子網級別委派。
- 跨可用性區域的子網，而在 AWS 中，每個可用性區域都有子網。

我們還有虛擬網絡對等。這使跨租戶和區域的虛擬網絡能夠使用 Azure 主幹連接。不是傳遞性的，但可以通過集線器虛擬網絡中的 Azure 防火牆啟用。使用網關傳輸允許對等虛擬網絡連接到連接網絡的連接性，一個例子可能是 ExpressRoute 到本地。

### 訪問控制

- Azure 利用網絡安全組，這些是有狀態的。
- 啟用規則創建，然後分配給網絡安全組
- 應用於子網或 VM 的網絡安全組。
- 當應用於子網時，它仍在虛擬機 NIC 上執行，它不是"邊緣"設備。

![](Images/Day33_Cloud1.png)

- 規則在網絡安全組中組合。
- 基於優先級，可以進行靈活的配置。
- 較低的優先級數字意味著高優先級。
- 大多數邏輯是通過 IP 地址構建的，但也可以使用一些標籤和標籤。

| Description      | Priority | Source Address     | Source Port | Destination Address | Destination Port | Action |
| ---------------- | -------- | ------------------ | ----------- | ------------------- | ---------------- | ------ |
| Inbound 443      | 1005     | \*                 | \*          | \*                  | 443              | Allow  |
| ILB              | 1010     | Azure LoadBalancer | \*          | \*                  | 10000            | Allow  |
| Deny All Inbound | 4000     | \*                 | \*          | \*                  | \*               | DENY   |

我們還有應用程序安全組 (ASGs)

- NSG 專注於 IP 地址範圍，這對於不斷增長的環境可能難以維護。
- ASG 使不同應用程序角色的真實名稱（別名）能夠被定義（Web 服務器、DB 服務器、WebApp1 等）
- 虛擬機 NIC 成為一個或多個 ASG 的成員。

然後可以在作為網絡安全組一部分的規則中使用 ASG 來控制通信流，並且仍然可以使用 NSG 功能（如服務標籤）。

| Action | Name               | Source     | Destination | Port         |
| ------ | ------------------ | ---------- | ----------- | ------------ |
| Allow  | AllowInternettoWeb | Internet   | WebServers  | 443(HTTPS)   |
| Allow  | AllowWebToApp      | WebServers | AppServers  | 443(HTTPS)   |
| Allow  | AllowAppToDB       | AppServers | DbServers   | 1443 (MSSQL) |
| Deny   | DenyAllinbound     | Any        | Any         | Any          |

### 負載平衡

Microsoft Azure 有兩個獨立的負載平衡解決方案。（第一方，Azure 市場中還有第三方。）兩者都可以使用面向外部或面向內部的端點運行。

- Load Balancer（第 4 層）支持基於哈希的分發和端口轉發。
- App Gateway（第 7 層）支持 SSL 卸載、基於 cookie 的會話親和性和基於 URL 的內容路由等功能。

此外，使用 App Gateway，你可以可選地使用 Web 應用程序防火牆組件。

## Azure 管理工具

我們在理論時間的大部分時間都花在了 Azure 門戶上，我建議當涉及遵循 DevOps 文化和流程時，這些任務中的很多，特別是圍繞供應，將通過 API 或命令行工具完成。我想觸及一些我們可用的其他管理工具，因為當我們自動化 Azure 環境的供應時，我們需要知道這一點。

### Azure 門戶

Microsoft Azure 門戶是一個基於 Web 的控制台，提供命令行工具的替代方案。你可以在 Azure 門戶中管理訂閱。從簡單的 Web 應用到複雜的雲部署，構建、管理和監控一切。你在門戶中會發現的另一件事是這些麵包屑，正如之前提到的，JSON 是所有 Azure 資源的基礎，你可能從門戶開始以了解功能、服務和功能，但後來了解下面的 JSON 以納入自動化工作流。

![](Images/Day33_Cloud2.png)

還有 Azure Preview 門戶，可以用於查看和測試新的和即將推出的服務和增強功能。

![](Images/Day33_Cloud3.png)

### PowerShell

在我們進入 Azure PowerShell 之前，值得首先介紹 PowerShell。PowerShell 是一個任務自動化和配置管理框架、命令行 shell 和腳本語言。我們可能會，我敢說這類似於我們在 Linux 部分圍繞 shell 腳本涵蓋的內容。PowerShell 最初在 Windows OS 上發現，但現在它是跨平台的。

Azure PowerShell 是一組用於直接從 PowerShell 命令行管理 Azure 資源的 cmdlet。

我們可以在下面看到，你可以使用 PowerShell 命令 `Connect-AzAccount` 連接到訂閱

![](Images/Day33_Cloud4.png)

然後如果我們想找到一些與 Azure VM 相關的特定命令，我們可以運行以下命令。你可以花幾個小時學習和了解有關此 PowerShell 程式語言的更多信息。

![](Images/Day33_Cloud5.png)

Microsoft 有一些關於從 PowerShell 開始和供應服務的優秀快速入門[這裡](https://docs.microsoft.com/en-us/powershell/azure/get-started-azureps?view=azps-7.1.0)

### Visual Studio Code

像許多人一樣，正如你們都看到的，我的首選 IDE 是 Visual Studio Code。

Visual Studio Code 是 Microsoft 為 Windows、Linux 和 macOS 製作的免費源代碼編輯器。

你將在下面看到，Visual Studio Code 中內置了許多集成和工具，你可以使用它們與 Microsoft Azure 及其內的服務進行交互。

![](Images/Day33_Cloud6.png)

### Cloud Shell

Azure Cloud Shell 是一個交互式、經過身份驗證、可通過瀏覽器訪問的 shell，用於管理 Azure 資源。它提供了選擇最適合你工作方式的 shell 體驗的靈活性。

![](Images/Day33_Cloud7.png)

你可以從下面看到，當我們首次在門戶內啟動 Cloud Shell 時，我們可以在 Bash 和 PowerShell 之間選擇。

![](Images/Day33_Cloud8.png)

要使用雲 shell，你必須在訂閱中提供一些存儲。

當你選擇使用雲 shell 時，它正在啟動一台機器，這些機器是臨時的，但你的文件以兩種方式持久化；通過磁盤鏡像和掛載的文件共享。

![](Images/Day33_Cloud9.png)

- Cloud Shell 在按會話、按用戶提供的臨時主機上運行
- Cloud Shell 在沒有交互活動 20 分鐘後超時
- Cloud Shell 需要掛載 Azure 文件共享
- Cloud Shell 對 Bash 和 PowerShell 使用相同的 Azure 文件共享
- Cloud Shell 為每個用戶帳戶分配一台機器
- Cloud Shell 使用保存在文件共享中的 5-GB 鏡像持久化 $HOME
- 權限在 Bash 中設置為常規 Linux 用戶

以上內容複製自 [Cloud Shell Overview](https://docs.microsoft.com/en-us/azure/cloud-shell/overview)

### Azure CLI

最後，我想涵蓋 Azure CLI，Azure CLI 可以安裝在 Windows、Linux 和 macOS 上。安裝後，你可以鍵入 `az` 後跟其他命令來創建、更新、刪除和查看 Azure 資源。

當我最初進入 Azure 學習時，我對存在 Azure PowerShell 和 Azure CLI 感到有點困惑。

我也希望從社群中獲得一些反饋。但我看到的方式是，Azure PowerShell 是添加到 Windows PowerShell 或 PowerShell Core 的模塊（也可在其他 OS 上使用，但不是全部），而 Azure CLI 是一個跨平台命令行程序，連接到 Azure 並執行那些命令。

這兩個選項有不同的語法，儘管它們從我能看到的和我所做的可以做非常相似的任務。

例如，從 PowerShell 創建虛擬機將使用 `New-AzVM` cmdlet，而 Azure CLI 將使用 `az VM create`。

你之前看到我在系統上安裝了 Azure PowerShell 模塊，但我也安裝了 Azure CLI，可以通過我的 Windows 機器上的 PowerShell 調用。

![](Images/Day33_Cloud10.png)

正如我們已經提到的，這裡的要點是關於選擇正確的工具。Azure 運行在自動化上。你在門戶內執行的每個操作都會在某處轉換為執行讀取、創建、修改或刪除資源的代碼。

Azure CLI

- 跨平台命令行界面，可安裝在 Windows、macOS、Linux 上
- 在 Windows PowerShell、Cmd、Bash 和其他 Unix shell 中運行。

Azure PowerShell

- 跨平台 PowerShell 模塊，在 Windows、macOS、Linux 上運行
- 需要 Windows PowerShell 或 PowerShell

如果你的環境中無法使用 PowerShell 但可以使用 .mdor bash，那麼 Azure CLI 將是你的選擇。

接下來，我們將所有理論都經歷一遍，並創建一些場景並在 Azure 中親自動手。

## 資源

- [Hybrid Cloud and MultiCloud](https://www.youtube.com/watch?v=qkj5W98Xdvw)
- [Microsoft Azure Fundamentals](https://www.youtube.com/watch?v=NKEFWyqJ5XA&list=WL&index=130&t=12s)
- [Google Cloud Digital Leader Certification Course](https://www.youtube.com/watch?v=UGRDM86MBIQ&list=WL&index=131&t=10s)
- [AWS Basics for Beginners - Full Course](https://www.youtube.com/watch?v=ulprqHHWlng&t=5352s)

我們[Day 34](day34.md)見
