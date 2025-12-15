---
title: '#90DaysOfDevOps - 概述：IaC - 第 56 天'
published: false
description: 90DaysOfDevOps - 概述 IaC
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048709
---

## 概述：IaC

人類會犯錯誤！自動化是出路！

你今天如何構建系統？

如果你今天失去一切，你的計劃是什麼，物理機器、虛擬機器、雲端 VM、雲端 PaaS 等等？

你需要多長時間來替換一切？

基礎設施即代碼提供了一個解決方案，能夠做到這一點，同時也能夠測試這一點，我們不應該將此與備份和恢復混淆，但就基礎設施和環境而言，你的平台，我們應該能夠啟動它們並將它們視為牛而不是寵物。

TLDR；是我們可以使用代碼重建整個環境。

如果我們還記得從一開始我們就說過 DevOps 總的來說是一種打破障礙以安全快速地將系統交付到生產環境的方式。

基礎設施即代碼幫助我們交付系統，我們已經談了很多流程和工具。IaC 為我們帶來了更多熟悉的工具，以實現流程的這一部分。

我們將在本節中專注於基礎設施即代碼。你可能還會聽到這被稱為基礎設施來自代碼或配置即代碼。我認為最知名的術語可能是基礎設施即代碼。

### 寵物 vs 牛

如果我們看一下 DevOps 之前，如果我們有構建新應用程式的需求，我們將需要手動準備伺服器。

- 部署 VM | 物理伺服器並安裝作業系統
- 配置網路
- 創建路由表
- 安裝軟體和更新
- 配置軟體
- 安裝資料庫

這將是由系統管理員執行的手動過程。應用程式越大，所需的資源和伺服器越多，啟動這些系統所需的手動工作就越多。這將花費大量的人力和時間，而且作為企業，你必須為構建此環境的資源付費。正如我以「人類會犯錯誤！自動化是出路！」開場本節。

從上述初始設置階段開始，你還要維護這些伺服器。

- 更新版本
- 部署新版本
- 數據管理
- 應用程式恢復
- 添加、移除和擴展伺服器
- 網路配置

添加多個測試和開發環境的複雜性。

這就是基礎設施即代碼的用武之地，上述情況非常像我們會像照顧寵物一樣照顧那些伺服器的時代，人們甚至給它們起寵物名字或至少給它們起名字，因為它們會存在一段時間，它們希望成為「家庭」的一部分一段時間。

使用基礎設施即代碼，我們可以端到端自動化所有這些任務。基礎設施即代碼是一個概念，一些工具執行此基礎設施的自動配置，此時如果伺服器發生問題，你丟棄它並啟動一個新的。這個過程是自動化的，伺服器完全按照代碼中定義的方式。此時我們不關心它們被稱為什麼，它們在現場服務於它們的目的，直到它們不再在現場，我們有另一個來替換它，無論是因為故障還是因為我們更新了部分或全部應用程式。

這可以用於幾乎所有平台，虛擬化、基於雲端的工作負載以及雲原生基礎設施，如 Kubernetes 和容器。

### 基礎設施配置

並非所有 IaC 都涵蓋下面的所有內容，你會發現我們在本節中將使用的工具實際上只涵蓋下面的前 2 個領域；Terraform 是我們將涵蓋的工具，這使我們能夠從零開始，在代碼中定義基礎設施應該是什麼樣子，然後部署它，它還將使我們能夠管理基礎設施，並且最初部署應用程式，但在那時它將失去對應用程式的追蹤，這就是下一節的用武之地，像 Ansible 這樣的配置管理工具可能在這方面效果更好。

不提前跳躍，像 chef、puppet 和 ansible 這樣的工具最適合處理初始應用程式設置，然後管理這些應用程式及其配置。

初始安裝和軟體配置

- 啟動新伺服器
- 網路配置
- 創建負載平衡器
- 基礎設施級別的配置

### 已配置基礎設施的配置

- 在伺服器上安裝應用程式
- 準備伺服器以部署應用程式。

### 應用程式部署

- 部署和管理應用程式
- 維護階段
- 軟體更新
- 重新配置

### IaC 工具之間的差異

聲明式 vs 程序式

程序式

- 逐步指令
- 創建伺服器 > 添加伺服器 > 進行此更改

聲明式

- 聲明結果
- 2 個伺服器

可變（寵物）vs 不可變（牛）

可變

- 更改而不是替換
- 通常壽命長

不可變

- 替換而不是更改
- 可能壽命短

這就是為什麼我們有很多不同的基礎設施即代碼選項，因為沒有一個工具可以統治它們。

我們將主要使用 terraform 並進行實際操作，因為這是開始看到基礎設施即代碼在運行時的好處的最佳方式。實際操作也是學習你將要編寫代碼的技能的最佳方式。

接下來，我們將在進行一些實際操作之前先查看 Terraform 的 101。

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

我們[第 57 天](day57.md)見