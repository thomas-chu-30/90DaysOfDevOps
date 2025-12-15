---
title: '#90DaysOfDevOps - 測試、工具和替代方案 - 第 62 天'
published: false
description: '90DaysOfDevOps - 測試、工具和替代方案'
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049053
---

## 測試、工具和替代方案

當我們結束基礎設施即代碼部分時，我們必須提到測試代碼、可用的各種工具，然後是 Terraform 的一些替代方案來實現這一點。正如我在本節開始時所說，我的重點是 Terraform，因為首先它是免費和開源的，其次它是跨平台且與環境無關的。但還有其他替代方案應該考慮，但總體目標是讓人們意識到這是部署基礎設施的方式。

### 代碼腐化

我想在本節中涵蓋的第一個領域是代碼腐化，與應用程式代碼不同，基礎設施即代碼可能會被使用，然後很長時間不再使用。讓我們舉一個例子，我們將使用 Terraform 在 AWS 中部署 VM 環境，完美，它第一次工作，我們有了環境，但這個環境不會經常更改，所以代碼被留下，狀態可能或希望存儲在中央位置，但代碼不會更改。

如果基礎設施中發生某些變化怎麼辦？但它是帶外完成的，或者環境中的其他事物發生變化。

- 帶外更改
- 未固定的版本
- 已棄用的依賴項
- 未應用的更改

### 測試

從代碼腐化延續下來的另一個巨大領域，總的來說，是測試 IaC 並確保所有領域按應有的方式工作的能力。

首先，有一些內建測試指令我們可以查看：

| 指令              | 描述                                                                                |
| ----------------- | ----------------------------------------------------------------------------------- |
| `terraform fmt`   | 將 Terraform 配置檔案重寫為規範格式和樣式。                                          |
| `terraform validate` | 驗證目錄中的配置檔案，僅參考配置                                                      |
| `terraform plan`  | 創建執行計劃，讓你預覽 Terraform 計劃進行的更改                                       |
| Custom validation | 驗證輸入變數以確保它們符合你的預期                                                     |

我們還有一些 Terraform 外部可用的測試工具：

- [tflint](https://github.com/terraform-linters/tflint)

  - 查找可能的錯誤
  - 警告已棄用的語法和未使用的聲明。
  - 強制執行最佳實踐和命名約定。

掃描工具

- [checkov](https://www.checkov.io/) - 掃描雲基礎設施配置以在部署前發現配置錯誤。
- [tfsec](https://aquasecurity.github.io/tfsec/v1.4.2/) - Terraform 代碼的靜態分析安全掃描器。
- [terrascan](https://github.com/accurics/terrascan) - 基礎設施即代碼的靜態代碼分析器。
- [terraform-compliance](https://terraform-compliance.com/) - 一個輕量級、安全和合規性重點的測試框架，針對 terraform 以啟用基礎設施即代碼的負面測試能力。
- [snyk](https://docs.snyk.io/products/snyk-infrastructure-as-code/scan-terraform-files/scan-and-fix-security-issues-in-terraform-files) - 掃描 Terraform 代碼以查找配置錯誤和安全問題

託管雲產品

- [Terraform Sentinel](https://www.terraform.io/cloud-docs/sentinel) - 與 HashiCorp 企業產品整合的嵌入式策略即代碼框架。它啟用細粒度、基於邏輯的策略決策，並可以擴展以使用來自外部來源的資訊。

### 自動化測試

- [Terratest](https://terratest.gruntwork.io/) - Terratest 是一個 Go 庫，提供測試基礎設施的模式和輔助函數
- Terratest 使為基礎設施代碼編寫自動化測試變得更容易。它為常見的基礎設施測試提供各種輔助函數和模式。
- 在 2022/Days/IaC/Terratest 找到代碼
- 運行此應用程式
  * git clone #repo_url# <br />
  * cd test  <br />
  * go mod init "<MODULE_NAME>"  <br />
  **MODULE_NAME 將是 github.com/<YOUR_USERNAME>/<YOUR_REPO_NAME>**  <br />
  * go mod init github.com/<FOLDER-PATH>  <br/>
  * go run

--------------------------------------------------------------------------------------------------------------------------------------------------------------------

go mod init "<MODULE_NAME>" 將在 test 資料夾中創建 go.mod 檔案。 <br />
* go.mod 檔案是 GoLang 中依賴管理的根。
* 專案中需要或將要使用的所有模組都在 go.mod 檔案中維護。
* 它為我們將在專案中使用/導入的所有套件創建條目。
* 它減少了手動獲取每個依賴項的工作。

在第一次運行 **go test** 時，你將獲得創建的 go.sum 檔案。 <br />
* go.sum 檔案在第一次執行 **go test** 或 **go build** 時創建。
* 它安裝所有具有特定版本（最新）的套件
* 我們不需要編輯或修改此檔案。

值得提及

- [Terraform Cloud](https://cloud.hashicorp.com/products/terraform) - Terraform Cloud 是 HashiCorp 的託管服務產品。它消除了從業者、團隊和組織在生產中使用 Terraform 時對不必要的工具和文件的需求。

- [Terragrunt](https://terragrunt.gruntwork.io/) - Terragrunt 是一個薄包裝器，提供額外工具來保持配置 DRY，使用多個 Terraform 模組，並管理遠程狀態。

- [Atlantis](https://www.runatlantis.io/) - Terraform Pull Request 自動化

### 替代方案

我們在第 57 天開始本節時提到有一些替代方案，我非常計劃在完成此挑戰後探索這一點。

| 雲端特定                  | 雲端無關 |
| ------------------------- | -------- |
| AWS CloudFormation        | Terraform |
| Azure Resource Manager    | Pulumi   |
| Google Cloud Deployment Manager |          |

我在上述列表中可能使用 AWS CloudFormation 最多，並且是 AWS 原生的，但我沒有使用除 Terraform 之外的其他工具。正如你可以想像的那樣，雲端特定版本在該特定雲端中非常好，但如果你有多個雲端環境，那麼你將難以遷移這些配置，或者你將為 IaC 工作擁有多個管理平面。

我認為對我來說一個有趣的下一步是花一些時間了解更多關於 [Pulumi](https://www.pulumi.com/) 的資訊

來自他們網站上的 Pulumi 比較

> 「Terraform 和 Pulumi 都提供所需狀態基礎設施即代碼模型，其中代碼表示所需的基礎設施狀態，部署引擎將此所需狀態與堆疊的當前狀態進行比較，並確定需要創建、更新或刪除的資源。」

我能看到的最大區別是，與 HashiCorp 配置語言 (HCL) 不同，Pulumi 允許使用通用程式語言，如 Python、TypeScript、JavaScript、Go 和 .NET。

快速概述 [Introduction to Pulumi: Modern Infrastructure as Code](https://www.youtube.com/watch?v=QfJTJs24-JM) 我喜歡你被提示的輕鬆和選擇，並想更深入地了解這一點。

這結束了基礎設施即代碼部分，接下來我們轉向與配置管理的一點重疊，特別是當我們通過配置管理的大圖景時，我們將使用 Ansible 來完成其中一些任務和演示。

## 資源

我在下面列出了很多資源，我認為這個主題已經被涵蓋了很多次，如果你有其他資源，請務必通過 PR 提出你的資源，我很樂意審查並將它們添加到列表中。

- [What is Infrastructure as Code? Difference of Infrastructure as Code Tools](https://www.youtube.com/watch?v=POPP2WTJ8es)
- [Terraform Tutorial | Terraform Course Overview 2021](https://www.youtube.com/watch?v=m3cKkYXl-8o)
- [Terraform explained in 15 mins | Terraform Tutorial for Beginners](https://www.youtube.com/watch?v=l5k1ai_GBDE)
- [Terraform Course - From BEGINNER to PRO!](https://www.youtube.com/watch?v=7xngnjfIlK4&list=WL&index=141&t=16s)
- [HashiCorp Terraform Associate Certification Course](https://www.youtube.com/watch?v=V4waklkBC38&list=WL&index=55&t=111s)
- [Terraform Full Course for Beginners](https://www.youtube.com/watch?v=EJ3N-hhiWv0&list=WL&index=39&t=27s)
- [KodeKloud - Terraform for DevOps Beginners + Labs: Complete Step by Step Guide!](https://www.youtube.com/watch?v=YcJ9IeukJL8&list=WL&index=16&t=11s)
- [Terraform Simple Projects](https://terraform.joshuajebaraj.com/)
- [Terraform Tutorial - The Best Project Ideas](https://www.youtube.com/watch?v=oA-pPa0vfks)
- [Awesome Terraform](https://github.com/shuaibiyy/awesome-terraform)
- [Pulumi - IaC in your favorite programming language!](https://www.youtube.com/watch?v=vIjeiDcsR3Q&t=51s)

我們[第 63 天](day63.md)見