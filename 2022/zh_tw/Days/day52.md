---
title: '#90DaysOfDevOps - 設置多節點 Kubernetes 集群 - 第 52 天'
published: false
description: 90DaysOfDevOps - 設置多節點 Kubernetes 集群
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049050
---

## 設置多節點 Kubernetes 集群

我想將這個標題設為「使用 Vagrant 設置多節點 Kubernetes 集群」，但認為可能有點太長了！

在昨天的課程中，我們使用了一個很酷的專案來部署我們的第一個 Kubernetes 集群，並對使用 Kubernetes 時會遇到的最重要的 CLI 工具（kubectl）進行了一些實際操作。

在這裡，我們將使用 VirtualBox 作為基礎，但正如我們在 Linux 部分最後一次談論 Vagrant 時提到的，我們可以使用任何支持的虛擬機管理程式或虛擬化工具。那是 [第 14 天](day14.md)，當時我們為 Linux 部分部署了 Ubuntu 機器。

### 快速回顧 Vagrant

Vagrant 是一個管理虛擬機器生命週期的 CLI 實用程式。我們可以使用 vagrant 在許多不同平台上啟動和關閉虛擬機器，包括 vSphere、Hyper-v、Virtual Box 和 Docker。它確實有其他提供者，但我們將堅持在這裡使用 Virtual Box，所以我們可以開始了。

我將使用這個[部落格和儲存庫](https://devopscube.com/kubernetes-cluster-vagrant/)作為基線來演練配置。但是，我建議如果這是你第一次部署 Kubernetes 集群，那麼也許也要研究如何手動執行此操作，然後至少你知道這是什麼樣子。雖然我會說，隨著 Kubernetes 的每個版本發布，這個 Day 0 操作和努力變得更加高效。我將這與 VMware 和 ESX 的日子進行比較，以及你如何需要至少一天來部署 3 個 ESX 伺服器，現在我們可以在一個小時內啟動並運行。當談到 Kubernetes 時，我們正朝著那個方向前進。

### Kubernetes 實驗室環境

我已經在 [Kubernetes 資料夾](Kubernetes) 中上傳了我們將用於構建環境的 vagrantfile。獲取這個並在終端機中導航到此目錄。我再次使用 Windows，所以我將使用 PowerShell 來執行我的工作站指令與 vagrant。如果你沒有 vagrant，那麼你可以使用 arkade，我們昨天在安裝 minikube 和其他工具時涵蓋了這一點。一個簡單的指令 `arkade get vagrant` 應該會看到你下載並安裝最新版本的 vagrant。

當你在目錄中時，你可以簡單地運行 `vagrant up`，如果所有配置都正確，那麼你應該在終端機中看到以下啟動。

![](Images/Day52_Kubernetes1.png)

在終端機中，你將看到幾個步驟正在進行，但與此同時，讓我們看看我們在這裡構建的內容。

![](Images/Day52_Kubernetes2.png)

從上面你可以看到我們將構建 3 個虛擬機器，我們將有一個控制平面節點，然後是兩個工作節點。如果你回到 [第 49 天](day49.md)，你將看到我們在圖像中看到的這些區域的更多描述。

同樣在圖像中，我們表示我們的 kubectl 訪問將來自集群外部並命中 kube apiserver，而實際上作為 vagrant 配置的一部分，我們在這些節點中的每一個上部署 kubectl，以便我們可以從每個節點內部訪問集群。

構建此實驗室的過程可能需要 5 分鐘到 30 分鐘，具體取決於你的設置。

我很快也會涵蓋腳本，但如果你查看 vagrant 檔案，你會注意到我們正在調用 3 個腳本作為部署的一部分，這實際上是創建集群的地方。我們已經看到使用 vagrant 部署虛擬機器和使用 vagrant boxes 進行 OS 安裝是多麼容易，但能夠在部署過程中運行 shell 腳本是自動化這些實驗室構建變得非常有趣的地方。

完成後，我們可以 ssh 到我們的一個節點 `vagrant ssh master`，從終端機應該可以讓你訪問，預設用戶名和密碼是 `vagrant/vagrant`

你也可以使用 `vagrant ssh node01` 和 `vagrant ssh node02` 來訪問工作節點，如果你希望的話。

![](Images/Day52_Kubernetes3.png)

現在我們在新集群的上述節點之一中，我們可以發出 `kubectl get nodes` 來顯示我們的 3 節點集群及其狀態。

![](Images/Day52_Kubernetes4.png)

此時，我們有一個運行的 3 節點集群，有 1 個控制平面節點和 2 個工作節點。

### Vagrantfile 和 Shell 腳本演練

如果我們看一下我們的 vagrantfile，你會看到我們正在定義幾個工作節點、VirtualBox 內橋接網路的網路 IP 地址，然後是一些命名。另一個你會注意到的是，我們也在調用一些我們想在特定主機上運行的腳本。

```
NUM_WORKER_NODES=2
IP_NW="10.0.0."
IP_START=10

Vagrant.configure("2") do |config|
    config.vm.provision "shell", inline: <<-SHELL
        apt-get update -y
        echo "$IP_NW$((IP_START))  master-node" >> /etc/hosts
        echo "$IP_NW$((IP_START+1))  worker-node01" >> /etc/hosts
        echo "$IP_NW$((IP_START+2))  worker-node02" >> /etc/hosts
    SHELL
    config.vm.box = "bento/ubuntu-21.10"
    config.vm.box_check_update = true

    config.vm.define "master" do |master|
      master.vm.hostname = "master-node"
      master.vm.network "private_network", ip: IP_NW + "#{IP_START}"
      master.vm.provider "virtualbox" do |vb|
          vb.memory = 4048
          vb.cpus = 2
          vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      end
      master.vm.provision "shell", path: "scripts/common.sh"
      master.vm.provision "shell", path: "scripts/master.sh"
    end

    (1..NUM_WORKER_NODES).each do |i|
      config.vm.define "node0#{i}" do |node|
        node.vm.hostname = "worker-node0#{i}"
        node.vm.network "private_network", ip: IP_NW + "#{IP_START + i}"
        node.vm.provider "virtualbox" do |vb|
            vb.memory = 2048
            vb.cpus = 1
            vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        end
        node.vm.provision "shell", path: "scripts/common.sh"
        node.vm.provision "shell", path: "scripts/node.sh"
      end
    end
  end
```

讓我們分解正在運行的那些腳本。我們在上面的 VAGRANTFILE 中列出了三個腳本，在特定節點上運行。

`master.vm.provision "shell", path: "scripts/common.sh"`

上面的腳本將專注於讓節點準備好，它將在我們所有 3 個節點上運行，它將移除任何現有的 Docker 組件並重新安裝 Docker 和 ContainerD 以及 kubeadm、kubelet 和 kubectl。此腳本還將更新系統上的現有軟體套件。

`master.vm.provision "shell", path: "scripts/master.sh"`

master.sh 腳本只會在控制平面節點上運行，此腳本將使用 kubeadm 指令創建 Kubernetes 集群。它還將準備配置上下文以訪問此集群，我們接下來將涵蓋這一點。

`node.vm.provision "shell", path: "scripts/node.sh"`

這只是將獲取主節點創建的配置並將我們的節點加入 Kubernetes 集群，此加入過程再次使用 kubeadm 和另一個可以在 config 資料夾中找到的腳本。

### 訪問 Kubernetes 集群

現在我們已經部署了兩個集群，我們有在上一節中部署的 minikube 集群，還有我們剛剛部署到 VirtualBox 的新 3 節點集群。

此外，你將在機器上訪問的配置檔案，你從中運行 vagrant，包含我們如何從工作站訪問集群。

在我們展示之前，讓我談談上下文。

![](Images/Day52_Kubernetes5.png)

上下文很重要，從桌面或筆記型電腦訪問 Kubernetes 集群的能力是必需的。那裡有很多不同的選項，人們使用不同的作業系統作為他們的日常驅動程式。

預設情況下，Kubernetes CLI 客戶端（kubectl）使用 C:\Users\username\.kube\config 來儲存 Kubernetes 集群詳細資訊，例如端點和憑證。如果你已部署集群，你將能夠在該位置看到此檔案。但如果你一直在使用主節點通過 SSH 或其他方法運行所有 kubectl 指令，那麼這篇文章希望有助於你掌握能夠與工作站連接的能力。

然後我們需要從集群獲取 kubeconfig 檔案，或者我們也可以在部署後從配置檔案中獲取它，通過 SCP 獲取此檔案的內容，或者只是打開一個控制台會話到主節點並複製到本地 Windows 機器。

![](Images/Day52_Kubernetes6.png)

然後我們想要獲取該配置檔案的副本並將其移動到我們的 `$HOME/.kube/config` 位置。

![](Images/Day52_Kubernetes7.png)

現在從你的本地工作站，你將能夠運行 `kubectl cluster-info` 和 `kubectl get nodes` 來驗證你對集群的訪問。

![](Images/Day52_Kubernetes8.png)

這不僅允許從 Windows 機器進行連接和控制，而且還允許我們進行一些端口轉發以從 Windows 機器訪問某些服務

如果你對如何在工作站上管理多個集群感興趣，那麼我在[這裡](https://vzilla.co.uk/vzilla-blog/building-the-home-lab-kubernetes-playground-part-6)有更詳細的演練。

我已經添加了這個列表，這些是我圍繞部署不同 Kubernetes 集群所做的演練部落格。

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

我們[第 53 天](day53.md)見