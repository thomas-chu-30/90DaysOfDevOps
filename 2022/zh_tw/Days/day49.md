---
title: '#90DaysOfDevOps - 概述：Kubernetes - 第 49 天'
published: false
description: 90DaysOfDevOps - 概述 Kubernetes
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049049
---

## 概述：Kubernetes

在上一節中，我們涵蓋了容器，容器在擴展和編排方面存在不足。我們能做的最好的就是使用 docker-compose 一起啟動多個容器。當談到 Kubernetes（一個容器編排器）時，這為我們提供了以自動化方式或基於應用程式和服務負載進行擴展的能力。

作為一個平台，Kubernetes 提供了根據你的需求和所需狀態編排容器的能力。我們將在本節中涵蓋 Kubernetes，因為它作為下一波基礎設施正在快速增長。我還建議從 DevOps 的角度來看，Kubernetes 只是你需要基本了解的一個平台，你還需要了解裸機、虛擬化以及很可能還有基於雲端的服務。Kubernetes 只是運行我們應用程式的另一個選項。

### 什麼是容器編排？

我提到了 Kubernetes，也提到了容器編排，Kubernetes 是技術，而容器編排是技術背後的概念或過程。Kubernetes 不是唯一的容器編排平台，我們還有 Docker Swarm、HashiCorp Nomad 等。但 Kubernetes 正在不斷發展，所以我想涵蓋 Kubernetes，但想說這不是唯一的一個。

### 什麼是 Kubernetes？

如果你是 Kubernetes 的新手，你應該閱讀的第一件事是官方文件，一年多前我真正深入研究 Kubernetes 的經驗是這將是一條陡峭的學習曲線。來自虛擬化和儲存背景，我思考這感覺多麼令人生畏。

但社群、免費學習資源和文件都很棒。[Kubernetes.io](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/)

_Kubernetes 是一個可移植、可擴展的開源平台，用於管理容器化工作負載和服務，促進聲明式配置和自動化。它擁有一個龐大、快速增長的生態系統。Kubernetes 服務、支持和工具廣泛可用。_

從上述引用中要注意的重要事項，Kubernetes 是開源的，有著豐富的歷史，可以追溯到 Google，該公司將專案捐贈給了 Cloud Native Computing Foundation (CNCF)，現在已經由開源社群以及大型企業供應商推進，為使 Kubernetes 成為今天的樣子做出了貢獻。

我上面提到容器很棒，在上一節中，我們談到了容器和容器圖像如何改變和加速雲原生系統的採用。但僅容器無法為你提供應用程式所需的生產就緒體驗。Kubernetes 為我們提供以下功能：

- **服務發現和負載平衡** Kubernetes 可以使用 DNS 名稱或 IP 地址公開容器。如果對容器的流量很高，Kubernetes 可以負載平衡並分發網路流量，以便部署穩定。

- **儲存編排** Kubernetes 允許你自動掛載你選擇的儲存系統，例如本地儲存、公共雲提供商等。

- **自動推出和回滾** 你可以使用 Kubernetes 描述已部署容器的所需狀態，它可以以受控速率將實際狀態更改為所需狀態。例如，你可以自動化 Kubernetes 為部署創建新容器，移除現有容器並將其所有資源採用到新容器。

- **自動 bin 打包** 你為 Kubernetes 提供一個節點集群，它可以用它來運行容器化任務。你告訴 Kubernetes 每個容器需要多少 CPU 和內存 (RAM)。Kubernetes 可以將容器安裝到你的節點上，以最好地利用你的資源。

- **自修復** Kubernetes 重啟失敗的容器，替換容器，殺死不響應用戶定義健康檢查的容器，並且在它們準備好服務之前不會向客戶端公佈它們。

- **機密和配置管理** Kubernetes 允許你儲存和管理敏感資訊，例如密碼、OAuth 令牌和 SSH 密鑰。你可以部署和更新機密和應用程式配置，而無需重建容器圖像，也無需在堆疊配置中公開機密。

Kubernetes 為你提供了一個彈性運行分散式系統的框架。

容器編排管理容器的部署、放置和生命週期。

它還有許多其他職責：

- 集群管理將主機聯合成一個目標。

- 調度管理通過調度器在節點之間分發容器。
- 服務發現知道容器所在的位置，並在它們之間分發客戶端請求。

- 複製確保有正確數量的節點和容器可用於請求的工作負載。

- 健康管理檢測並替換不健康的容器和節點。

### 主要 Kubernetes 組件

Kubernetes 是一個容器編排器，用於配置、管理和擴展應用程式。你可以使用它來管理節點集群中容器化應用程式的生命週期，節點是工作機器的集合，例如 VM 或物理機器。

你的應用程式可能需要許多其他資源來運行，例如卷、網路和機密，可以幫助你連接到資料庫、與防火牆後端通信並保護密鑰。使用 Kubernetes，你可以將這些資源添加到應用程式。應用程式需要的基礎設施資源以聲明方式管理。

Kubernetes 的關鍵範例是其聲明式模型。你提供你想要的狀態，Kubernetes 使其實現。如果你需要五個實例，你不會自己啟動五個單獨的實例。相反，你告訴 Kubernetes 你需要五個實例，Kubernetes 自動協調狀態。如果你的實例之一出現問題並失敗，Kubernetes 仍然知道你想要的狀態並在可用節點上創建實例。

### 節點

#### 控制平面

每個 Kubernetes 集群都需要一個控制平面節點，控制平面的組件對集群做出全局決策（例如，調度），以及檢測和響應集群事件。

![](Images/Day49_Kubernetes1.png)

#### 工作節點

運行 Kubernetes 工作負載的工作機器。它可以是物理（裸機）機器或虛擬機器 (VM)。每個節點可以託管一個或多個 Pod。Kubernetes 節點由控制平面管理

![](Images/Day49_Kubernetes2.png)

還有其他節點類型，但我不會在這裡涵蓋它們。

#### kubelet

在集群中每個節點上運行的代理。它確保容器在 Pod 中運行。

kubelet 接受通過各種機制提供的一組 PodSpecs，並確保在那些 PodSpecs 中描述的容器正在運行並且健康。kubelet 不管理不是由 Kubernetes 創建的容器。

![](Images/Day49_Kubernetes3.png)

#### kube-proxy

kube-proxy 是在集群中每個節點上運行的網路代理，實現 Kubernetes Service 概念的一部分。

kube-proxy 維護節點上的網路規則。這些網路規則允許從集群內或外的網路會話到你的 Pod 的網路通信。

如果存在並且可用，kube-proxy 使用作業系統數據包過濾層。否則，kube-proxy 自己轉發流量。

![](Images/Day49_Kubernetes4.png)

#### 容器運行時

容器運行時是負責運行容器的軟體。

Kubernetes 支持多種容器運行時：Docker、containerd、CRI-O 和 Kubernetes CRI（容器運行時介面）的任何實現。

![](Images/Day49_Kubernetes5.png)

### 集群

集群是一組節點，其中節點可以是物理機器或虛擬機器。每個節點都將具有容器運行時（Docker），並且還將運行 kubelet 服務，這是一個從主控制器接收指令的代理（稍後會有更多內容）和一個代理，用於將連接到 Pod 從另一個組件（服務，我們稍後會看到）代理。

我們的控制平面可以高可用，與工作節點相比將包含一些獨特的角色，最重要的是 kube API 伺服器，這是任何通信發生以獲取資訊或將資訊推送到我們的 Kubernetes 集群的地方。

#### Kube API-Server

Kubernetes API 伺服器驗證和配置 API 物件的數據，包括 Pod、服務、複製控制器等。API 伺服器服務 REST 操作，並提供集群共享狀態的前端，所有其他組件通過它進行交互。

#### 調度器

Kubernetes 調度器是一個控制平面進程，將 Pod 分配給節點。調度器根據約束和可用資源確定調度隊列中每個 Pod 的有效放置節點。然後調度器對每個有效節點進行排名，並將 Pod 綁定到合適的節點。

#### 控制器管理器

Kubernetes 控制器管理器是一個守護進程，嵌入了 Kubernetes 附帶的核心控制循環。在機器人和自動化應用中，控制循環是一個非終止循環，調節系統的狀態。在 Kubernetes 中，控制器是一個控制循環，通過 apiserver 監視集群的共享狀態，並進行更改以嘗試將當前狀態移動到所需狀態。

#### etcd

一致且高度可用的鍵值儲存，用作 Kubernetes 的所有集群數據的後端儲存。

![](Images/Day49_Kubernetes6.png)

#### kubectl

為了從 CLI 角度管理這個，我們有 kubectl，kubectl 與 API 伺服器交互。

Kubernetes 命令列工具 kubectl 允許你針對 Kubernetes 集群運行指令。你可以使用 kubectl 部署應用程式、檢查和管理集群資源以及查看日誌。

![](Images/Day49_Kubernetes7.png)

### Pods

Pod 是形成邏輯應用程式的一組容器。例如，如果你有一個運行 NodeJS 容器的 Web 應用程式，還有一個 MySQL 容器，那麼這兩個容器都將位於單個 Pod 中。Pod 還可以共享公共數據卷，它們還共享相同的網路命名空間。請記住，Pod 是短暫的，它們可以由主控制器啟動和關閉。Kubernetes 使用簡單但有效的方法通過標籤（名稱 - 值）的概念來識別 Pod。

- Pod 處理容器的卷、機密和配置。

- Pod 是短暫的。它們旨在在死亡時自動重啟。

- 當應用程式由 ReplicationSet 水平擴展時，Pod 被複製。每個 Pod 將運行相同的容器程式碼。

- Pod 存在於工作節點上。

![](Images/Day49_Kubernetes8.png)

### Deployments

- 你可以決定運行 Pod，但當它們死亡時，它們就死了。

- Deployment 將使你的 Pod 持續運行。

- Deployments 允許你更新運行的應用程式而不停機。

- Deployments 還指定了當 Pod 死亡時重啟 Pod 的策略

![](Images/Day49_Kubernetes9.png)

### ReplicaSets

- Deployment 也可以創建 ReplicaSet

- ReplicaSet 確保你的應用程式具有所需數量的 Pod

- ReplicaSets 將根據 Deployment 創建和擴展 Pod

- Deployments、ReplicaSets 和 Pods 不是互斥的，但可以是

### StatefulSets

- 你的應用程式是否需要你保留有關其狀態的資訊？

- 資料庫需要狀態

- StatefulSet 的 Pod 不可互換。

- 每個 Pod 都有一個唯一的、持久的標識符，控制器在任何重新調度時都保持該標識符。

![](Images/Day49_Kubernetes10.png)

### DaemonSets

- DaemonSets 用於持續進程

- 它們在每個節點上運行一個 Pod。

- 添加到集群的每個新節點都會啟動一個 Pod

- 對於背景任務（如監控和日誌收集）很有用

- 每個 Pod 都有一個唯一的、持久的標識符，控制器在任何重新調度時都保持該標識符。

![](Images/Day49_Kubernetes11.png)

### Services

- 訪問 Pod 的單一端點

- 一種統一的方式，將流量路由到集群，最終路由到 Pod 列表。

- 通過使用服務，可以啟動和關閉 Pod，而不會影響任何東西。

這只是圍繞 Kubernetes 基本構建塊的快速概述和筆記，我們可以獲取這些知識並添加儲存和 Ingress 等其他領域來增強我們的應用程式，但我們也有許多關於 Kubernetes 集群運行位置的選擇。下一節將專注於這些選項，我可以在哪裡運行 Kubernetes 集群，同時也探索儲存的一些具體內容。

![](Images/Day49_Kubernetes12.png)

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

我們[第 50 天](day50.md)見