---
title: '#90DaysOfDevOps - Microsoft Azure Fundamentals - Day 29'
published: false
description: 90DaysOfDevOps - Microsoft Azure Fundamentals
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048705
---

## Microsoft Azure 基礎知識

在我們開始之前，Twitter 投票的獲勝者是 Microsoft Azure，因此頁面標題。這很接近，看到結果在 24 小時內出現也很有趣。

![](Images/Day29_Cloud1.png)

我想說，就涵蓋這個主題而言，這將使我更好地理解和更新 Microsoft Azure 上可用的服務，當談到我今天的工作時，我傾向於 Amazon AWS。但是，我已經為所有三個主要雲提供商留下了我已經準備好的資源。

我確實知道還有更多，投票只包括這 3 個，特別是關於 Oracle Cloud 的一些評論。我很想聽到更多關於在野外使用的其他雲提供商的信息。

### 基礎知識

- 提供公共雲服務
- 地理分佈（全球 60+ 個區域）
- 通過互聯網和/或專用連接訪問
- 多租戶模型
- 基於消費的計費 -（按需付費 | 按增長付費）
- 大量服務類型和產品以滿足不同需求。

- [Microsoft Azure Global Infrastructure](https://infrastructuremap.microsoft.com/explore)

儘管我們談到了 SaaS 和混合雲，但我們不打算在這裡涵蓋這些主題。

開始並跟隨的最佳方法是點擊鏈接，這將使你能夠啟動 [Microsoft Azure Free Account](https://azure.microsoft.com/en-gb/free/)

### 區域

我在上面鏈接了交互式地圖，但我們可以看到下面圖像中 Microsoft Azure 平台在全球提供的區域範圍。

![](Images/Day29_Cloud2.png)
_圖片取自 [Microsoft Docs - 01/05/2021](https://docs.microsoft.com/en-us/azure/networking/microsoft-global-network)_

你還會看到幾個"主權"雲，這意味著它們沒有鏈接或能夠與其他區域通信，例如，這些將與政府相關，如 `AzureUSGovernment` 還有 `AzureChinaCloud` 和其他。

當我們在 Microsoft Azure 內部署服務時，我們將為幾乎所有內容選擇一個區域。但是，重要的是要注意，並非每個服務在每個區域都可用。你可以看到[按區域提供的產品](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=all)，在我撰寫本文時，在 West Central US 我們不能使用 Azure Databricks。

我還在上面的"幾乎所有內容"中提到，某些服務與區域相關，如 Azure Bot Services、Bing Speech、Azure Virtual Desktop、Static Web Apps 等。

在幕後，一個區域可能由多個數據中心組成。這些將被稱為可用性區域。

在下面的圖像中，你會看到，這再次取自 Microsoft 官方文檔，它描述了區域是什麼以及它如何由可用性區域組成。但是，並非所有區域都有多個可用性區域。

![](Images/Day29_Cloud3.png)

Microsoft 文檔非常好，你可以在[區域和可用性區域](https://docs.microsoft.com/en-us/azure/availability-zones/az-overview)這裡閱讀更多內容。

### 訂閱

記住我們提到 Microsoft Azure 是一個消費模型雲，你會發現所有主要雲提供商都遵循此模型。

如果你是一家企業，那麼你可能想要或已經與 Microsoft 建立了企業協議，以使你的公司能夠使用這些 Azure 服務。

如果你像我一樣，並且你正在使用 Microsoft Azure 進行教育，那麼我們還有其他幾個選項。

我們有 [Microsoft Azure Free Account](https://azure.microsoft.com/en-gb/free/)，它通常為你提供一些免費的雲信用額度，可以在 Azure 中花費一段時間。

還有使用 Visual Studio 訂閱的能力，這可能每月給你一些免費信用額度，以及你對 Visual Studio 的年度訂閱，這在幾年前通常被稱為 MSDN。[Visual Studio](https://azure.microsoft.com/en-us/pricing/member-offers/credit-for-visual-studio-subscribers/)

最後，還有交出信用卡並擁有按需付費模型。[Pay-as-you-go](https://azure.microsoft.com/en-us/pricing/purchase-options/pay-as-you-go/)

訂閱可以被視為不同訂閱之間的邊界，可能是成本中心，但完全不同的環境。訂閱是創建資源的地方。

### 管理組

管理組使我們能夠在 Azure Active Directory (AD) 或租戶環境中劃分控制。管理組允許我們控制策略、基於角色的訪問控制 (RBAC) 和預算。

訂閱屬於這些管理組，因此你可以在 Azure AD 租戶中擁有多個訂閱，這些訂閱然後也可以控制策略、RBAC 和預算。

### 資源管理器和資源組

#### Azure 資源管理器

- 基於 JSON 的 API，構建在資源提供者之上。
- 資源屬於資源組並共享共同的生命週期。
- 並行性
- 基於 JSON 的部署是聲明性的、冪等的，並理解資源之間的依賴關係以控制創建和順序。

#### 資源組

- 每個 Azure 資源管理器資源存在於一個且僅一個資源組中！
- 資源組在一個區域中創建，可以包含來自區域外部的資源。
- 資源可以在資源組之間移動
- 資源組不會與其他資源組隔離，資源組之間可以進行通信。
- 資源組還可以控制策略、RBAC 和預算。

### 親自動手

讓我們去連接並確保我們有一個**訂閱**可用。我們可以檢查我們簡單的開箱即用**管理組**，然後我們可以去在我們首選的**區域**中創建一個新的專用**資源組**。

當我們首次登錄 [Azure portal](https://portal.azure.com/#home) 時，你會在頂部看到搜索資源、服務和文檔的能力。

![](Images/Day29_Cloud4.png)

我們將首先查看我們的訂閱，你會看到這裡我使用的是 Visual Studio Professional 訂閱，它每月給我一些免費信用額度。

![](Images/Day29_Cloud5.png)

如果我們進入那個，你會得到更廣泛的視圖並了解正在發生的事情或可以用訂閱做什麼，我們可以看到計費信息，左側有控制功能，你可以在其中定義 IAM 訪問控制，進一步向下有更多可用資源。

![](Images/Day29_Cloud6.png)

可能有一個場景，你有多個訂閱，你想在一個下管理它們，這是可以使用管理組來劃分責任組的地方。在我的下面，你可以看到只有我的租戶根組和我的訂閱。

你還會在上一個圖像中看到，父管理組是租戶根組上使用的相同 id。

![](Images/Day29_Cloud7.png)

接下來我們有資源組，這是我們組合資源並可以在一個地方輕鬆管理它們的地方。我為各種其他項目創建了幾個。

![](Images/Day29_Cloud8.png)

對於我們將在接下來的幾天中要做的事情，我們想創建資源組。這可以通過在上一個圖像上點擊創建選項在此控制台中輕鬆完成。

![](Images/Day29_Cloud9.png)

進行驗證步驟，然後你有機會查看創建，然後創建。你還會看到底部"下載模板以進行自動化"，這使我們能夠獲取 JSON 格式，以便如果我們想要，我們可以稍後以自動化方式執行這個簡單的操作，我們稍後也會涵蓋這一點。

![](Images/Day29_Cloud10.png)

點擊創建，然後在我們的資源組列表中，我們現在有"90DaysOfDevOps"組準備好在下一會話中執行操作。

![](Images/Day29_Cloud11.png)

## 資源

- [Hybrid Cloud and MultiCloud](https://www.youtube.com/watch?v=qkj5W98Xdvw)
- [Microsoft Azure Fundamentals](https://www.youtube.com/watch?v=NKEFWyqJ5XA&list=WL&index=130&t=12s)
- [Google Cloud Digital Leader Certification Course](https://www.youtube.com/watch?v=UGRDM86MBIQ&list=WL&index=131&t=10s)
- [AWS Basics for Beginners - Full Course](https://www.youtube.com/watch?v=ulprqHHWlng&t=5352s)

我們[Day 30](day30.md)見
