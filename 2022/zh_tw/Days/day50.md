---
title: '#90DaysOfDevOps - 選擇你的 Kubernetes 平台 - 第 50 天'
published: false
description: 90DaysOfDevOps - 選擇你的 Kubernetes 平台
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049046
---

## 選擇你的 Kubernetes 平台

我想使用這個課程來分解一些平台，或者也許分發是這裡使用的更好術語，Kubernetes 世界中的一個挑戰是消除複雜性。

Kubernetes the hard way 演練如何從零構建到功能齊全的 Kubernetes 集群，這是非常極端的，但越來越多至少我交談的人希望消除這種複雜性並運行託管的 Kubernetes 集群。那裡的問題是它花費更多錢，但好處可能是如果你使用託管服務，你是否需要了解底層節點架構以及從控制平面節點的角度來看正在發生什麼，通常你無法訪問它。

然後我們有本地開發分發，使我們能夠使用我們的系統並運行 Kubernetes 的本地版本，以便開發人員可以擁有完整的工作環境來在它們為之設計的平台上運行他們的應用程式。

所有這些概念的總體基礎是它們都是 Kubernetes 的一種風味，這意味著我們應該能夠根據需要自由遷移和移動我們的工作負載以滿足我們的需求。

我們的很多選擇也取決於已經做出的投資。我也提到了開發人員體驗，但一些在我們的筆記型電腦上運行的本地 Kubernetes 環境非常適合在不花費任何錢的情況下掌握這項技術。

### 裸機集群

對許多人來說，一個選項可能是將你的 Linux OS 直接運行到幾個物理伺服器上以創建我們的集群，它也可以是 Windows，但我沒有聽到太多關於 Windows、容器和 Kubernetes 的採用率。如果你是一家企業，並且你已經做出了 CAPEX 決定購買你的物理伺服器，那麼這可能是你在構建 Kubernetes 集群時的方式，管理和管理方面意味著你將不得不自己構建並從頭開始管理一切。

### 虛擬化

無論是測試和學習環境還是企業就緒的 Kubernetes 集群，虛擬化都是一個很好的方式，通常是啟動虛擬機器作為你的節點的能力，然後將它們集群在一起。你擁有底層架構、虛擬化的效率和速度，以及利用現有支出。例如，VMware 為虛擬機器和 Kubernetes 提供了多種風味的出色解決方案。

我的第一個 Kubernetes 集群是基於虛擬化構建的，使用 Microsoft Hyper-V 在我擁有的一台舊伺服器上，它能夠運行幾個 VM 作為我的節點。

### 本地桌面選項

在桌面或筆記型電腦上運行本地 Kubernetes 集群時，有幾個選項。如前所述，這為開發人員提供了查看應用程式外觀的能力，而無需擁有多個昂貴或複雜的集群。就個人而言，這是我使用很多的一個，特別是，我一直在使用 minikube。它有一些很棒的功能和附加元件，改變了你啟動和運行某些東西的方式。

### Kubernetes 託管服務

我已經提到了虛擬化，這可以通過本地的虛擬機管理程式來實現，但我們從前面的部分知道，我們也可以利用公共雲中的 VM 作為我們的節點。我在這裡談論的 Kubernetes 託管服務是我們從大型超規模提供商看到的產品，也從 MSP 中移除終端用戶的管理和控制層，這可能是從終端用戶移除控制平面，這就是 Amazon EKS、Microsoft AKS 和 Google Kubernetes Engine (GKE) 發生的情況。

### 選擇太多

我的意思是選擇很好，但在某些時候事情變得令人難以承受，這不是對上面列出的每個類別中所有選項的深入了解。除了上述之外，我們還有來自 Red Hat 的 OpenShift，這個選項可以在上面列出的所有主要雲提供商的所有選項中運行，並且可能今天為管理員提供了最佳的整體可用性，無論集群部署在哪裡。

那麼從學習的角度來看，你從哪裡開始，正如我所說，我從虛擬化路線開始，但那是因為我可以訪問一台物理伺服器，我可以將其用於此目的，我感謝並且實際上，從那時起我不再有這個選項。

我現在的實際建議是使用 Minikube 作為第一個選項或 Kind (Kubernetes in Docker)，但 Minikube 為我們提供了一些額外的好處，幾乎抽象了複雜性，因為我們可以使用附加元件並快速構建東西，然後當我們完成時可以將其刪除，我們可以運行多個集群，我們幾乎可以在任何地方運行它，跨平台和硬體無關。

我在學習 Kubernetes 的過程中經歷了一段旅程，所以我將在這裡留下平台選擇和具體細節，列出我嘗試過的選項，以便更好地了解 Kubernetes 平台以及它可以在哪裡運行。我可能對下面的部落格文章做的是再看一下這些更新並將它們更多地帶到這裡，而不是將它們連結到部落格文章。

- [Kubernetes playground – How to choose your platform](https://vzilla.co.uk/vzilla-blog/building-the-home-lab-kubernetes-playground-part-1)
- [Kubernetes playground – Setting up your cluster](https://vzilla.co.uk/vzilla-blog/building-the-home-lab-kubernetes-playground-part-2)
- [Getting started with Amazon Elastic Kubernetes Service (Amazon EKS)](https://vzilla.co.uk/vzilla-blog/getting-started-with-amazon-elastic-kubernetes-service-amazon-eks)
- [Getting started with Microsoft Azure Kubernetes Service (AKS)](https://vzilla.co.uk/vzilla-blog/getting-started-with-microsoft-azure-kubernetes-service-aks)
- [Getting Started with Microsoft AKS – Azure PowerShell Edition](https://vzilla.co.uk/vzilla-blog/getting-started-with-microsoft-aks-azure-powershell-edition)
- [Getting started with Google Kubernetes Service (GKE)](https://vzilla.co.uk/vzilla-blog/getting-started-with-google-kubernetes-service-gke)
- [Kubernetes, How to – AWS Bottlerocket + Amazon EKS](https://vzilla.co.uk/vzilla-blog/kubernetes-how-to-aws-bottlerocket-amazon-eks)
- [Getting started with CIVO Cloud](https://vzilla.co.uk/vzilla-blog/getting-started-with-civo-cloud)
- [Minikube - Kubernetes Demo Environment For Everyone](https://vzilla.co.uk/vzilla-blog/project_pace-kasten-k10-demo-environment-for-everyone)

### 我們將在 Kubernetes 系列中涵蓋的內容

- Kubernetes 架構
- Kubectl 指令
- Kubernetes YAML
- Kubernetes Ingress
- Kubernetes Services
- Helm 套件管理器
- 持久儲存
- 有狀態應用程式

## 資源

- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [TechWorld with Nana - Kubernetes Tutorial for Beginners [FULL COURSE in 4 Hours]](https://www.youtube.com/watch?v=X48VuDVv0do)
- [TechWorld with Nana - Kubernetes Crash Course for Absolute Beginners](https://www.youtube.com/watch?v=s_o8dwzRlu4)
- [Kunal Kushwaha - Kubernetes Tutorial for Beginners | What is Kubernetes? Architecture Simplified!](https://www.youtube.com/watch?v=KVBON1lA9N8)

我們[第 51 天](day51.md)見