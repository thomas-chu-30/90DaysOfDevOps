---
title: '#90DaysOfDevOps - Microsoft Azure Hands-On Scenarios - Day 34'
published: false
description: 90DaysOfDevOps - Microsoft Azure Hands-On Scenarios
tags: 'DevOps, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048763
---

## Microsoft Azure 親自動手場景

過去 6 天一直專注於 Microsoft Azure 和一般公共雲，這個基礎中的很多內容必須包含大量理論來理解 Azure 的構建塊，但這也將很好地轉換到其他主要雲提供商。

我在最開始時提到了獲得公共雲的基礎知識並至少從一個提供商開始，如果你在不同的雲之間跳舞，那麼我相信你會很容易迷失，而選擇一個你會理解基礎知識，當你有了這些，跳入其他雲並加速你的學習是相當容易的。

在這最後一節中，我將從這裡的這個頁面中選擇我的親自動手場景，這是 Microsoft 創建的參考，用於準備 [AZ-104 Microsoft Azure Administrator](https://microsoftlearning.github.io/AZ-104-MicrosoftAzureAdministrator/)

這裡有一些諸如容器和 Kubernetes 之類的內容，我們還沒有詳細涵蓋，所以我不想跳入那裡。

在之前的文章中，我們已經創建了模塊 1、2 和 3 的大部分內容。

### 虛擬網絡

遵循 [Module 04](https://microsoftlearning.github.io/AZ-104-MicrosoftAzureAdministrator/Instructions/Labs/LAB_04-Implement_Virtual_Networking.html)：

我經歷了上述內容，並更改了一些 #90DaysOfDevOps 的命名。我也沒有使用 Cloud Shell，而是在我的 Windows 機器上使用我前幾天創建的新用戶使用 Azure CLI 登錄。

你可以使用 `az login` 來執行此操作，這將打開瀏覽器並讓你進行身份驗證。

然後我創建了一個 PowerShell 腳本和模塊的一些參考，用於構建下面的一些任務。你可以在這個文件夾中找到相關文件。
(Cloud\01VirtualNetworking)

請確保更改腳本中的文件位置以適合你的環境。

在第一個階段，我們的環境中沒有創建虛擬網絡或虛擬機，我的資源組中只配置了 cloud shell 存儲位置。

我首先運行我的 [PowerShell script](Cloud/01VirtualNetworking/Module4_90DaysOfDevOps.ps1)

![](Images/Day34_Cloud1.png)

- 任務 1：創建和配置虛擬網絡

![](Images/Day34_Cloud2.png)

- 任務 2：將虛擬機部署到虛擬網絡

![](Images/Day34_Cloud3.png)

- 任務 3：配置 Azure VM 的私有和公共 IP 地址

![](Images/Day34_Cloud4.png)

- 任務 4：配置網絡安全組

![](Images/Day34_Cloud5.png)
![](Images/Day34_Cloud6.png)

- 任務 5：為內部名稱解析配置 Azure DNS

![](Images/Day34_Cloud7.png)
![](Images/Day34_Cloud8.png)

### 網絡流量管理

遵循 [Module 06](https://microsoftlearning.github.io/AZ-104-MicrosoftAzureAdministrator/Instructions/Labs/LAB_06-Implement_Network_Traffic_Management.html)：

下一個演練，從上一個演練我們進入資源組並刪除了資源，如果你沒有像我一樣設置用戶帳戶只訪問那一個資源組，你可以跟隨模塊將名稱更改為 `90Days*`，這將刪除所有資源和資源組。這將是我對以下每個實驗的過程。

對於這個實驗，我還創建了一個 PowerShell 腳本和模塊的一些參考，用於構建下面的一些任務。你可以在這個文件夾中找到相關文件。
(Cloud\02TrafficManagement)

- 任務 1：供應實驗環境

我首先運行我的 [PowerShell script](Cloud/02TrafficManagement/Mod06_90DaysOfDevOps.ps1)

![](Images/Day34_Cloud9.png)

- 任務 2：配置集線器和輻射網絡拓撲

![](Images/Day34_Cloud10.png)

- 任務 3：測試虛擬網絡對等的傳遞性

對於這個，我的 90DaysOfDevOps 組由於權限無法訪問 Network Watcher，我期望這是因為 Network Watchers 是那些不綁定到資源組的資源之一，這就是我們為此用戶覆蓋 RBAC 的地方。我將 East US Network Watcher 貢獻者角色添加到 90DaysOfDevOps 組。

![](Images/Day34_Cloud11.png)
![](Images/Day34_Cloud12.png)
![](Images/Day34_Cloud13.png)

^ 這是預期的，因為兩個輻射虛擬網絡不會相互對等（虛擬網絡對等不是傳遞性的）。

- 任務 4：在集線器和輻射拓撲中配置路由

我在這裡遇到了另一個問題，我的帳戶無法運行腳本，因為我的用戶在 90DaysOfDevOps 組內，我不確定為什麼，所以我確實跳回了我的主管理員帳戶。90DaysOfDevOps 組是 90DaysOfDevOps 資源組中所有內容的所有者，所以想了解為什麼我無法在 VM 內運行命令？

![](Images/Day34_Cloud14.png)
![](Images/Day34_Cloud15.png)

然後我能夠返回到我的 michael.cade@90DaysOfDevOps.com 帳戶並繼續這個部分。這裡我們再次運行相同的測試，但現在的結果是可達的。

![](Images/Day34_Cloud16.png)

- 任務 5：實現 Azure Load Balancer

![](Images/Day34_Cloud17.png)
![](Images/Day34_Cloud18.png)

- 任務 6：實現 Azure Application Gateway

![](Images/Day34_Cloud19.png)
![](Images/Day34_Cloud20.png)

### Azure 存儲

遵循 [Module 07](https://microsoftlearning.github.io/AZ-104-MicrosoftAzureAdministrator/Instructions/Labs/LAB_07-Manage_Azure_Storage.html)：

對於這個實驗，我還創建了一個 PowerShell 腳本和模塊的一些參考，用於構建下面的一些任務。你可以在這個文件夾中找到相關文件。
(Cloud\03Storage)

- 任務 1：供應實驗環境

我首先運行我的 [PowerShell script](Cloud/03Storage/Mod07_90DaysOfDeveOps.ps1)

![](Images/Day34_Cloud21.png)

- 任務 2：創建和配置 Azure 存儲帳戶

![](Images/Day34_Cloud22.png)

- 任務 3：管理 blob 存儲

![](Images/Day34_Cloud23.png)

- 任務 4：管理 Azure 存儲的身份驗證和授權

![](Images/Day34_Cloud24.png)
![](Images/Day34_Cloud25.png)

我等待這個被允許有點不耐煩，但它最終確實工作了。

![](Images/Day34_Cloud26.png)

- 任務 5：創建和配置 Azure Files 共享

在運行命令時，這不適用於 michael.cade@90DaysOfDevOps.com，所以我使用了我的提升帳戶。

![](Images/Day34_Cloud27.png)
![](Images/Day34_Cloud28.png)
![](Images/Day34_Cloud29.png)

- 任務 6：管理 Azure 存儲的網絡訪問

![](Images/Day34_Cloud30.png)

### 無服務器（實現 Web 應用）

遵循 [Module 09a](https://microsoftlearning.github.io/AZ-104-MicrosoftAzureAdministrator/Instructions/Labs/LAB_09a-Implement_Web_Apps.html)：

- 任務 1：創建 Azure web 應用

![](Images/Day34_Cloud31.png)

- 任務 2：創建暫存部署槽

![](Images/Day34_Cloud34.png)

- 任務 3：配置 web 應用部署設置

![](Images/Day34_Cloud33.png)

- 任務 4：將代碼部署到暫存部署槽

![](Images/Day34_Cloud32.png)

- 任務 5：交換暫存槽

![](Images/Day34_Cloud35.png)

- 任務 6：配置和測試 Azure web 應用的自動擴展

我使用的這個腳本可以在 (Cloud/04Serverless) 中找到

![](Images/Day34_Cloud36.png)

這結束了 Microsoft Azure 和一般公共雲的部分。我想說，我在攻擊和處理這些場景時有很多樂趣。

## 資源

- [Hybrid Cloud and MultiCloud](https://www.youtube.com/watch?v=qkj5W98Xdvw)
- [Microsoft Azure Fundamentals](https://www.youtube.com/watch?v=NKEFWyqJ5XA&list=WL&index=130&t=12s)
- [Google Cloud Digital Leader Certification Course](https://www.youtube.com/watch?v=UGRDM86MBIQ&list=WL&index=131&t=10s)
- [AWS Basics for Beginners - Full Course](https://www.youtube.com/watch?v=ulprqHHWlng&t=5352s)

接下來，我們將深入探討版本控制系統，特別是圍繞 git，然後還有代碼存儲庫概述，我們將選擇 GitHub 因為這是我的首選選項。

我們[Day 35](day35.md)見
