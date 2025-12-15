---
title: '#90DaysOfDevOps - Terraform 簡介 - 第 57 天'
published: false
description: 90DaysOfDevOps - Terraform 簡介
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048710
---

## Terraform 簡介

「Terraform 是一個用於安全高效地構建、更改和版本化基礎設施的工具」

上面的引用來自 HashiCorp，HashiCorp 是 Terraform 背後的公司。

「Terraform 是一個開源基礎設施即代碼軟體工具，提供一致的 CLI 工作流程來管理數百個雲端服務。Terraform 將雲端 API 編碼為聲明式配置檔案。」

HashiCorp 在 [HashiCorp Learn](https://learn.hashicorp.com/terraform?utm_source=terraform_io&utm_content=terraform_io_hero) 中有一個很好的資源，涵蓋了他們的所有產品，並在你嘗試使用基礎設施即代碼實現某些目標時提供一些很好的演練演示。

所有雲提供商和本地平台通常都為我們提供管理控制台，使我們能夠通過 UI 創建資源，通常這些平台還提供 CLI 或 API 訪問來創建相同的資源，但使用 API 我們可以快速配置。

基礎設施即代碼允許我們連接到這些 API，以所需狀態部署資源。

其他工具但下面不是獨家或詳盡的。如果你有其他工具，請通過 PR 分享。

| 雲端特定                  | 雲端無關 |
| ------------------------- | -------- |
| AWS CloudFormation        | Terraform |
| Azure Resource Manager    | Pulumi   |
| Google Cloud Deployment Manager |          |

這是我們使用 Terraform 的另一個原因，我們希望對我們希望用於演示的雲和平台保持無關，但總的來說也是如此。

## Terraform 概述

Terraform 是一個以配置為重點的工具，Terraform 是一個 CLI，提供能夠配置複雜基礎設施環境的能力。使用 Terraform，我們可以定義存在於本地或遠程（雲端）的複雜基礎設施需求，Terraform 不僅使我們能夠最初構建東西，而且還能在其生命週期內維護和更新這些資源。

我們將在這裡涵蓋高層次，但有關更多詳細資訊和大量資源，你可以前往 [terraform.io](https://www.terraform.io/)

### 編寫

Terraform 允許我們創建聲明式配置檔案，這些檔案將構建我們的環境。檔案使用 HashiCorp 配置語言 (HCL) 編寫，允許使用塊、參數和表達式簡潔地描述資源。我們當然將在部署 VM、容器和 Kubernetes 中詳細研究這些。

### 計劃

能夠檢查上述配置檔案是否將部署我們想要看到的內容，使用 terraform cli 的特定功能來測試該計劃，然後再部署任何內容或更改任何內容。記住 Terraform 是基礎設施的持續工具，如果你想更改基礎設施的方面，你應該通過 terraform 執行此操作，以便所有內容都在代碼中捕獲。

### 應用

一旦你滿意，你可以繼續將此配置應用到 Terraform 內可用的許多提供者。你可以在[這裡](https://registry.terraform.io/browse/providers)看到大量可用的提供者

另一件要提到的事情是，還有可用的模組，這類似於容器圖像，因為這些模組已經創建並在公共中共享，所以你不需要一次又一次地創建它們，只需重用以相同方式在任何地方部署特定基礎設施資源的最佳實踐。你可以在[這裡](https://registry.terraform.io/browse/modules)找到可用的模組

Terraform 工作流程如下所示：（_取自 terraform 網站_）

![](Images/Day57_IAC3.png)

### Terraform vs Vagrant

在此挑戰期間，我們使用了 Vagrant，它恰好是另一個 Hashicorp 開源工具，專注於開發環境。

- Vagrant 是一個專注於管理開發環境的工具

- Terraform 是一個用於構建基礎設施的工具。

可以在官方 [Hashicorp 網站](https://www.vagrantup.com/intro/vs/terraform)上找到這兩個工具的很好比較

## Terraform 安裝

Terraform 的安裝沒有太多內容。

Terraform 是跨平台的，你可以在下面看到在我的 Linux 機器上，我們有幾個選項可以下載和安裝 CLI

![](Images/Day57_IAC2.png)

使用 `arkade` 安裝 Terraform，arkade 是一個方便的小工具，用於將所需的工具、應用程式和 cli 獲取到系統上。簡單的 `arkade get terraform` 將允許更新 terraform（如果可用）或此相同指令也將安裝 Terraform CLI

![](Images/Day57_IAC1.png)

我們將更多地了解 HCL，然後也開始使用 Terraform 在各種平台上創建一些基礎設施資源。

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

我們[第 58 天](day58.md)見