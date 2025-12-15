---
title: '#90DaysOfDevOps - Microsoft Azure Compute Models - Day 31'
published: false
description: 90DaysOfDevOps - Microsoft Azure Compute Models
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049040
---

## Microsoft Azure 計算模型

繼昨天涵蓋 Microsoft Azure 內安全模型的基礎知識之後，今天我們將查看 Azure 中可用的各種計算服務。

### 服務可用性選項

本節對我來說很親近，因為我在數據管理中的角色。與本地一樣，確保服務的可用性至關重要。

- 高可用性（區域內的保護）
- 災難恢復（區域之間的保護）
- 備份（從時間點恢復）

Microsoft 在地緣政治邊界內部署多個區域。

Azure 服務可用性的兩個概念。集合和區域。

可用性集 - 在數據中心內提供彈性

可用性區域 - 在區域內的數據中心之間提供彈性。

### 虛擬機

最可能是任何人在公共雲中的起點。

- 提供來自各種系列和大小、具有不同功能的 VM（有時很令人困惑）[Azure 中虛擬機的大小](https://docs.microsoft.com/en-us/azure/virtual-machines/sizes)
- 有許多不同的選項和 VM 的重點，從高性能、低延遲到高內存選項 VM。
- 我們還有一個可突發的 VM 類型，可以在 B 系列下找到。這對於大部分時間可以具有低 CPU 要求但可能每月需要一次性能峰值要求的工作負載非常有用。
- 虛擬機放置在虛擬網絡上，可以提供與任何網絡的連接。
- Windows 和 Linux 客戶 OS 支持。
- 當涉及特定 Linux 發行版時，還有 Azure 調整的內核。[Azure Tuned Kernals](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/endorsed-distros#azure-tuned-kernels)

### 模板化

我之前提到過，Microsoft Azure 背後或下面的所有內容都是 JSON。

有幾種不同的管理門戶和控制台，我們可以使用它們來創建資源，首選路由將是通過 JSON 模板。

增量或完整模式中的冪等部署 - 即可重複的期望狀態。

有大量模板可以導出已部署的資源定義。我喜歡將此模板功能視為類似 AWS CloudFormation 或可能是 Terraform 用於多雲選項。我們將在基礎設施即代碼部分更多地涵蓋 Terraform。

### 擴展

自動擴展是公共雲的一個重要功能，能夠關閉你未使用的資源或在需要時啟動它們。

在 Azure 中，對於 IaaS，我們有一個名為虛擬機規模集 (VMSS) 的東西。這允許基於計劃和指標從黃金標準鏡像自動創建和擴展。

這對於更新 windows 是理想的，這樣你就可以更新鏡像並以最少的影響推出它們。

其他服務，如 Azure App Services，內置了自動擴展。

### 容器

我們還沒有將容器作為用例進行涵蓋，以及它們在 DevOps 學習旅程中可以和應該需要什麼以及如何需要，但我們需要提到 Azure 有一些特定的以容器為重點的服務要提及。

Azure Kubernetes Service (AKS) - 提供託管 Kubernetes 解決方案，無需擔心控制平面或基礎集群管理的管理。稍後還有更多關於 Kubernetes 的內容。

Azure Container Instances - 按秒計費的容器即服務。運行鏡像並將其與虛擬網絡集成，無需容器編排。

Service Fabric - 具有許多功能，但包括容器實例的編排。

Azure 還擁有 Container Registry，它為 Docker 鏡像、Helm charts、OCI Artifacts 和鏡像提供私有註冊表。當我們到達容器部分時，還有更多關於此的內容。

我們還應該提到，許多容器服務實際上也可能在幕後利用容器，但這從你需要管理的內容中抽象出來。

這些提到的以容器為重點的服務，我們在所有其他公共雲中也發現類似的服務。

### 應用服務

- Azure Application Services 提供應用程序託管解決方案，提供建立服務的簡單方法。
- 自動部署和擴展。
- 支持基於 Windows 和 Linux 的解決方案。
- 服務在具有類型和大小的 App Service Plan 中運行。
- 許多不同的服務，包括 web 應用、API 應用和移動應用。
- 支持部署槽，用於可靠的測試和推廣。

### 無服務器計算

對我來說，無服務器是一個令人興奮的下一步，我非常感興趣學習更多關於它的知識。

無服務器的目標是我們只為函數的運行時付費，不必一直運行虛擬機或 PaaS 應用程序。我們只需在需要時運行函數，然後它就消失了。

Azure Functions - 提供無服務器代碼。如果我們記得回到我們對公共雲的第一次查看，我們會記住管理的抽象層，對於無服務器函數，你只需要管理代碼。

事件驅動且規模巨大，我計劃在稍後希望親自動手時構建一些東西。

為許多 Azure 和第三方服務提供輸入和輸出綁定。

支持許多不同的程式語言。（C#、NodeJS、Python、PHP、batch、bash、Golang 和 Rust。或任何可執行文件）

Azure Event Grid 使邏輯能夠從服務和事件中觸發。

Azure Logic App 提供基於圖形的工作流和集成。

我們還可以查看 Azure Batch，它可以在 Windows 和 Linux 節點上運行大規模作業，具有一致的管理和調度。

## 資源

- [Hybrid Cloud and MultiCloud](https://www.youtube.com/watch?v=qkj5W98Xdvw)
- [Microsoft Azure Fundamentals](https://www.youtube.com/watch?v=NKEFWyqJ5XA&list=WL&index=130&t=12s)
- [Google Cloud Digital Leader Certification Course](https://www.youtube.com/watch?v=UGRDM86MBIQ&list=WL&index=131&t=10s)
- [AWS Basics for Beginners - Full Course](https://www.youtube.com/watch?v=ulprqHHWlng&t=5352s)

我們[Day 32](day32.md)見
