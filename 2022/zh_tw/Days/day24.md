---
title: '#90DaysOfDevOps - Network Automation - Day 24'
published: false
description: 90DaysOfDevOps - Network Automation
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048805
---

## 網絡自動化

### 網絡自動化基礎

網絡自動化的主要驅動因素

- 實現敏捷性
- 降低成本
- 消除錯誤
- 確保合規性
- 集中管理

自動化採用過程對每個企業都是特定的。在部署自動化方面沒有一刀切的方法，識別和接受最適合你組織的方法的能力對於朝著維護或創建更敏捷的環境前進至關重要，重點應該始終放在業務價值和最終用戶體驗上。（我們在開始時就整個 DevOps 以及文化變革和這帶來的自動化過程說過類似的話）

要分解這一點，你需要識別你試圖自動化的任務或流程將如何實現並改善最終用戶體驗或業務價值，同時遵循逐步系統的方法。

"如果你不知道你要去哪裡，任何道路都會帶你到那裡。"

有一個你試圖實現的框架或設計結構，知道你的最終目標是什麼，然後逐步朝著實現該目標努力，根據業務結果在各個階段衡量自動化成功。

圍繞現有應用程序構建概念模型，不需要在真空中圍繞自動化設計概念，因為它們需要應用於你的應用程序、服務和基礎設施，所以開始構建概念並圍繞你現有的基礎設施、你現有的應用程序對它們進行建模。

### 網絡自動化方法

我們應該識別任務並對網絡變更請求執行發現，以便你有最常見的問題和問題來自動化解決方案。

- 列出當前正在手動處理的所有變更請求和工作流程。
- 確定最常見、耗時且容易出錯的活動。
- 通過採用業務驅動的方法來優先考慮請求。
- 這是構建自動化流程的框架，什麼必須自動化，什麼不能自動化。

然後我們應該劃分任務並分析不同的網絡功能如何工作以及如何相互交互。

- 基礎設施/網絡團隊在多個層面接收變更票據以部署應用程序。
- 基於網絡服務，將它們劃分為不同的領域並了解它們如何相互交互。
  - 應用程序優化
  - ADC（應用程序交付控制器）
  - 防火牆
  - DDI（DNS、DHCP、IPAM 等）
  - 路由
  - 其他
- 識別各種依賴關係以解決業務和文化差異並引入跨團隊協作。

可重用策略，定義並簡化可重用的服務任務、流程和輸入/輸出。

- 為各種服務、流程和輸入/輸出定義產品。
- 簡化部署過程將減少新工作負載和現有工作負載的上市時間。
- 一旦你有了標準流程，它可以被排序並與個別請求對齊，以實現多線程方法和交付。

將策略與特定業務活動相結合。實施此策略如何幫助業務？節省時間？省錢？提供更好的業務結果？

- 確保服務任務是可互操作的。
- 關聯增量服務任務，以便它們對齊以創建業務服務。
- 允許靈活地按需關聯和重新關聯服務任務。
- 部署自助服務功能並為提高運營效率鋪平道路。
- 允許多種技術技能集在監督和合規的情況下繼續貢獻。

**迭代**策略和流程，在保持可用性和服務的同時添加和改進。

- 通過自動化現有任務從小處著手。
- 熟悉自動化流程，以便你可以識別其他可以從自動化中受益的領域。
- 迭代你的自動化計劃，在保持所需可用性的同時逐步增加敏捷性。
- 採用增量方法為成功鋪平道路！

編排網絡服務！

- 需要自動化部署流程以快速交付應用程序。
- 創建敏捷服務環境需要跨技術技能集管理不同元素。
- 準備端到端編排，提供對自動化和部署順序的控制。

## 網絡自動化工具

這裡的好消息是，對於大多數部分，我們在這裡用於網絡自動化的工具通常與我們將用於其他自動化領域的工具相同，或者我們已經涵蓋的或我們將在未來的會話中涵蓋的。

操作系統 - 正如我在整個挑戰中所做的那樣，我專注於使用 Linux OS 進行大部分學習，這些原因在 Linux 部分給出，但幾乎所有我們將接觸的工具，儘管是跨 OS 平台，也許今天它們都從基於 Linux 的應用程序或工具開始。

集成開發環境（IDE）- 除了我建議 Visual Studio Code 作為你的 IDE 之外，這裡沒有太多要說的，基於為許多不同語言提供的廣泛插件。

配置管理 - 我們還沒有進入配置管理部分，但很明顯 Ansible 是管理和自動化配置的這個領域的最愛。Ansible 是用 Python 編寫的，但你不需要了解 Python。

- 無代理
- 只需要 SSH
- 大型支持社群
- 大量網絡模塊
- 僅推送模型
- 使用 YAML 配置
- 開源！

[Link to Ansible Network Modules](https://docs.ansible.com/ansible/2.9/modules/list_of_network_modules.html)

我們還將在配置管理部分中觸及 **Ansible Tower**，將其視為 Ansible 的 GUI 前端。

CI/CD - 我們將再次更多地介紹圍繞此的概念和工具，但至少在這裡提及這一點很重要，因為這不僅跨越網絡，還跨越服務和平台的所有供應。

特別是，Jenkins 提供或似乎是網絡自動化的流行工具。

- 監控 git 存儲庫的更改，然後啟動它們。

版本控制 - 我們稍後將再次深入探討。

- Git 在本地設備上提供代碼的版本控制 - 跨平台
- GitHub、GitLab、BitBucket 等是在線網站，你可以在其中定義存儲庫並上傳代碼。

語言 | 腳本 - 我們在這裡沒有涵蓋的一件事是 Python 作為一種語言，我決定基於我的情況深入探討 Go 作為程式語言，我想說 Golang 和 Python 之間非常接近，Python 似乎是網絡自動化的贏家。

- Nornir 是這裡要提到的，一個用 Python 編寫的自動化框架。這似乎承擔了 Ansible 的角色，但專門圍繞網絡自動化。[Nornir documentation](https://nornir.readthedocs.io/en/latest/)

分析 API - Postman 是分析 RESTful API 的優秀工具。幫助構建、測試和修改 API。

- POST >>> 創建資源對象。
- GET >>> 檢索資源。
- PUT >>> 創建或替換資源。
- PATCH >>> 創建或更新資源對象。
- Delete >>> 刪除資源

[Postman tool Download](https://www.postman.com/downloads/)

### 其他要提及的工具

[Cisco NSO (Network Services Orchestrator)](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-services-orchestrator/index.html)

[NetYCE - Simplify Network Automation](https://netyce.com/)

[Network Test Automation](https://pubhub.devnetcloud.com/media/genie-feature-browser/docs/#/)

在接下來的 3 天裡，我計劃更實際地處理我們涵蓋的一些內容，並圍繞 Python 和網絡自動化做一些工作。

我們還沒有涵蓋所有網絡主題，但希望這足夠廣泛，可以跟隨並繼續從我在下面添加的資源中學習。

## 資源

- [3 Necessary Skills for Network Automation](https://www.youtube.com/watch?v=KhiJ7Fu9kKA&list=WL&index=122&t=89s)
- [Computer Networking full course](https://www.youtube.com/watch?v=IPvYjXCsTg8)
- [Practical Networking](http://www.practicalnetworking.net/)
- [Python Network Automation](https://www.youtube.com/watch?v=xKPzLplPECU&list=WL&index=126)

我們[Day 25](day25.md)見
