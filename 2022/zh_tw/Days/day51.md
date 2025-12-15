---
title: '#90DaysOfDevOps - 部署你的第一個 Kubernetes 集群 - 第 51 天'
published: false
description: 90DaysOfDevOps - 部署你的第一個 Kubernetes 集群
tags: 'DevOps, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048778
---

## 部署你的第一個 Kubernetes 集群

在這篇文章中，我們將使用 minikube 在本地機器上啟動並運行 Kubernetes 集群，這將為我們在 Kubernetes 部分的其餘部分提供一個基線 Kubernetes 集群，儘管我們稍後也會在 VirtualBox 中部署 Kubernetes 集群。選擇這種方法與在公共雲中啟動託管 Kubernetes 集群相比的原因是，即使使用免費層，這也會花費金錢，不過如果你想在上一節 [第 50 天](day50.md) 中啟動該環境，我分享了一些部落格。

### 什麼是 Minikube？

> 「minikube 在 macOS、Linux 和 Windows 上快速設置本地 Kubernetes 集群。我們自豪地專注於幫助應用程式開發人員和新的 Kubernetes 用戶。」

你可能不適合上述情況，但我發現 minikube 是一個很棒的小工具，如果你只想以 Kubernetes 方式測試某些東西，你可以輕鬆部署應用程式，它們有一些令人驚嘆的附加元件，我也將涵蓋這些。

首先，無論你的工作站 OS 是什麼，你都可以運行 minikube。首先，前往[這裡的專案頁面](https://minikube.sigs.k8s.io/docs/start/)。你的第一個選項是選擇安裝方法。我沒有使用這種方法，但你可能會選擇與我的方式相比（我的方式即將到來）。

下面提到，你需要有一個「容器或虛擬機器管理器，例如 Docker、Hyper kit、Hyper-V、KVM、Parallels、Podman、VirtualBox 或 VMware」，這是 MiniKube 將運行的位置，簡單的選項，除非在儲存庫中說明，否則我使用 Docker。你可以使用以下[連結](https://docs.docker.com/get-docker/)在系統上安裝 Docker。

![](Images/Day51_Kubernetes1.png)

### 我安裝 minikube 和其他先決條件的方式…

我已經使用 arkade 一段時間來獲取所有這些 Kubernetes 工具和 CLI，你可以在這個 [github 儲存庫](https://github.com/alexellis/arkade) 上查看安裝步驟以開始使用 Arkade。我也在其他需要安裝某些東西的部落格文章中提到了這一點。只需點擊 arkade get 然後查看你的工具或 cli 是否可用的簡單性很方便。在 Linux 部分，我們談到了套件管理器以及獲取軟體的過程，你可以將 Arkade 視為所有應用程式和 Kubernetes CLI 的市場。在你的系統上擁有的非常方便的小工具，用 Golang 編寫且跨平台。

![](Images/Day51_Kubernetes2.png)

作為 arkade 中可用應用程式長列表的一部分，minikube 是其中之一，因此使用簡單的 `arkade get minikube` 指令，我們現在正在下載二進位檔，我們可以開始了。

![](Images/Day51_Kubernetes3.png)

我們還需要 kubectl 作為我們工具的一部分，所以你也可以通過 arkade 獲取它，或者我相信 minikube 文件會將其作為上面提到的 curl 指令的一部分下載。我們稍後會在文章中更多地介紹 kubectl。

### 啟動並運行 Kubernetes 集群

對於這個特定部分，我想涵蓋在本地機器上啟動並運行 Kubernetes 集群時可用的選項。我們可以簡單地運行以下指令，它會為你啟動一個集群。

minikube 在命令列上使用，簡單地說，一旦你安裝了所有內容，你就可以運行 `minikube start` 來部署你的第一個 Kubernetes 集群。你將在下面看到 Docker Driver 是預設值，這是我們將運行嵌套虛擬化節點的位置。我在文章開頭提到了其他可用選項，當你想要擴展此本地 Kubernetes 集群需要什麼樣子時，其他選項會有所幫助。

單個 Minikube 集群在此實例中將由單個 docker 容器組成，該容器將在一個實例中具有控制平面節點和工作節點。而通常你會分離這些節點。我們將在下一節中涵蓋這一點，我們將查看仍然是家庭實驗室類型的 Kubernetes 環境，但更接近生產架構。

![](Images/Day51_Kubernetes4.png)

我已經提到過幾次，我喜歡 minikube 是因為可用的附加元件，能夠使用簡單的指令部署集群，包括從一開始就需要的所有附加元件，這有助於我每次都部署相同的所需設置。

下面你可以看到這些附加元件的列表，我通常使用 `CSI-host path-driver` 和 `volumesnapshots` 附加元件，但你可以看到下面的長列表。當然，這些附加元件通常可以使用 Helm 再次部署，這是我們稍後在 Kubernetes 部分將涵蓋的內容，但這使事情變得更加簡單。

![](Images/Day51_Kubernetes5.png)

我還在我的專案中定義了一些額外的配置，apiserver 設置為 6433 而不是隨機 API 端口，我還將容器運行時定義為 containerd，但是 docker 是預設值，CRI-O 也可用。我還設置了特定的 Kubernetes 版本。

![](Images/Day51_Kubernetes6.png)

現在我們準備使用 minikube 部署我們的第一個 Kubernetes 集群。不過我之前提到，你還需要 `kubectl` 來與集群交互。你可以使用 arkade 安裝 kubectl，指令為 `arkade get kubectl`

![](Images/Day51_Kubernetes7.png)

或者你可以從以下位置下載跨平台版本

- [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux)
- [macOS](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos)
- [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows)

一旦你安裝了 kubectl，我們就可以使用像 `kubectl get nodes` 這樣的簡單指令與集群交互。

![](Images/Day51_Kubernetes8.png)

### 什麼是 kubectl？

我們現在已經啟動並運行了 minikube | Kubernetes 集群，我已經要求你安裝 Minikube，我至少解釋了它的作用，但我沒有解釋 kubectl 是什麼以及它的作用。

kubectl 是一個用於或允許你與 Kubernetes 集群交互的 cli，我們在這裡使用它來與我們的 minikube 集群交互，但我們也會使用 kubectl 來與公共雲中的企業集群交互。

我們使用 kubectl 來部署應用程式並檢查和管理集群資源。可以在 Kubernetes 官方文件上找到更好的 [kubectl 概述](https://kubernetes.io/docs/reference/kubectl/overview/)。

kubectl 與我們在早期文章中簡要介紹的控制平面節點上找到的 API 伺服器交互。

### kubectl 速查表

除了官方文件外，我在尋找 kubectl 指令時也一直打開這個頁面。[Unofficial Kubernetes](https://unofficial-kubernetes.readthedocs.io/en/latest/)

| 列出資源              |                                            |
| --------------------- | ------------------------------------------ |
| kubectl get nodes     | 列出集群中的所有節點                       |
| kubectl get namespaces | 列出集群中的所有命名空間                   |
| kubectl get pods      | 列出預設命名空間集群中的所有 Pod            |
| kubectl get pods -n name | 列出「name」命名空間中的所有 Pod            |

| 創建資源                    |                                             |
| --------------------------- | ------------------------------------------- |
| kubectl create namespace name | 創建一個名為「name」的命名空間              |
| kubectl create -f [filename] | 從 JSON 或 YAML 檔案創建資源：              |

| 編輯資源                |                   |
| ----------------------- | ----------------- |
| kubectl edit svc/servicename | 編輯服務 |

| 資源的更多詳細資訊 |                                                         |
| ------------------ | ------------------------------------------------------- |
| kubectl describe nodes | 詳細顯示任意數量資源的狀態， |

| 刪除資源          |                                                  |
| ----------------- | ------------------------------------------------ |
| kubectl delete pod | 移除資源，這可以從 stdin 或檔案中進行 |

你會發現自己想知道某些 kubectl 指令的簡短名稱，例如 `-n` 是 `namespace` 的簡短名稱，這使得鍵入指令更容易，而且如果你正在編寫任何腳本，你可以擁有更整潔的程式碼。

| 簡短名稱 | 完整名稱                  |
| -------- | ------------------------- |
| csr      | certificatesigningrequests |
| cs       | componentstatuses          |
| cm       | configmaps                 |
| ds       | daemonsets                 |
| deploy   | deployments                |
| ep       | endpoints                  |
| ev       | events                     |
| hpa      | horizontalpodautoscalers   |
| ing      | ingresses                  |
| limits   | limitranges                |
| ns       | namespaces                 |
| no       | nodes                      |
| pvc      | persistentvolumeclaims     |
| pv       | persistentvolumes          |
| po       | pods                       |
| pdb      | poddisruptionbudgets       |
| psp      | podsecuritypolicies        |
| rs       | replicasets                |
| rc       | replicationcontrollers     |
| quota    | resourcequotas             |
| sa       | serviceaccounts            |
| svc      | services                   |

最後要補充的是，我創建了另一個圍繞 minikube 的專案，以幫助我快速啟動演示環境來顯示數據服務並使用 Kasten K10 保護這些工作負載，[Project Pace](https://github.com/MichaelCade/project_pace) 可以在那裡找到，我很樂意收到你的反饋或互動，它還顯示或包括一些自動化方式來部署你的 minikube 集群並創建不同的數據服務應用程式。

接下來，我們將使用 VirtualBox 將多個節點部署到虛擬機器中，但我們將在那裡按下簡單按鈕，就像我們在 Linux 部分所做的那樣，我們使用 vagrant 快速啟動機器並按照我們想要的方式部署軟體。

我昨天將此列表添加到文章中，這些是我圍繞部署不同 Kubernetes 集群所做的演練部落格。

- [Kubernetes playground – How to choose your platform](https://vzilla.co.uk/vzilla-blog/building-the-home-lab-kubernetes-playground-part-1)
- [Kubernetes playground – Setting up your cluster](https://vzilla.co.uk/vzilla-blog/building-the-home-lab-kubernetes-playground-part-2)
- [Getting started with Amazon Elastic Kubernetes Service (Amazon EKS)](https://vzilla.co.uk/vzilla-blog/getting-started-with-amazon-elastic-kubernetes-service-amazon-eks)
- [Getting started with Microsoft Azure Kubernetes Service (AKS)](https://vzilla.co.uk/vzilla-blog/getting-started-with-microsoft-azure-kubernetes-service-aks)
- [Getting Started with Microsoft AKS – Azure PowerShell Edition](https://vzilla.co.uk/vzilla-blog/getting-started-with-microsoft-aks-azure-powershell-edition)
- [Getting started with Google Kubernetes Service (GKE)](https://vzilla.co.uk/vzilla-blog/getting-started-with-google-kubernetes-service-gke)
- [Kubernetes, How to – AWS Bottlerocket + Amazon EKS](https://vzilla.co.uk/vzilla-blog/kubernetes-how-to-aws-bottlerocket-amazon-eks)
- [Getting started with CIVO Cloud](https://vzilla.co.uk/vzilla-blog/getting-started-with-civo-cloud)
- [Minikube - Kubernetes Demo Environment For Everyone](https://vzilla.co.uk/vzilla-blog/project_pace-kasten-k10-demo-environment-for-everyone)
- [Minikube - Deploy Minikube Using Vagrant and Ansible on VirtualBox](https://medium.com/techbeatly/deploy-minikube-using-vagrant-and-ansible-on-virtualbox-infrastructure-as-code-2baf98188847)

### 我們將在 Kubernetes 系列中涵蓋的內容

我們已經開始涵蓋下面提到的一些內容，但我們明天將在第二個集群部署中進行更多實際操作，然後我們可以開始將應用程式部署到我們的集群中。

- Kubernetes 架構
- Kubectl 指令
- Kubernetes YAML
- Kubernetes Ingress
- Kubernetes Services
- Helm 套件管理器
- 持久儲存
- 有狀態應用程式

## 資源

如果你有使用過的免費資源，請隨時通過 PR 將它們添加到儲存庫中，我很樂意包含它們。

- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [TechWorld with Nana - Kubernetes Tutorial for Beginners [FULL COURSE in 4 Hours]](https://www.youtube.com/watch?v=X48VuDVv0do)
- [TechWorld with Nana - Kubernetes Crash Course for Absolute Beginners](https://www.youtube.com/watch?v=s_o8dwzRlu4)
- [Kunal Kushwaha - Kubernetes Tutorial for Beginners | What is Kubernetes? Architecture Simplified!](https://www.youtube.com/watch?v=KVBON1lA9N8)
- [Techbeatly - Deploy Minikube Using Vagrant and Ansible on VirtualBox](https://www.youtube.com/watch?v=xPLQqHbp9BM&t=371s)

我們[第 52 天](day52.md)見