---
title: '#90DaysOfDevOps - Ansible 的其他內容 - Automation Controller (Tower)、AWX、Vault - 第 69 天'
published: false
description: '90DaysOfDevOps - Ansible 的其他內容 - Automation Controller (Tower)、AWX、Vault'
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048714
---

## Ansible 的其他內容 - Automation Controller (Tower)、AWX、Vault

結束配置管理部分，我想查看處理 Ansible 時可能遇到的其他領域。

有很多產品組成了 Ansible Automation 平台。

Red Hat Ansible Automation Platform 是在組織中構建和運營自動化的基礎。該平台包括實現企業範圍自動化所需的所有工具。

![](Images/Day69_config1.png)

我將嘗試在本文章中涵蓋其中一些。但有關更多資訊，官方 Red Hat Ansible 網站將有更多資訊。[Ansible.com](https://www.ansible.com/?hsLang=en-us)

### Ansible Automation Controller | AWX

我將這兩個捆綁在一起，因為 Automation Controller 和 AWX 在它們提供的內容上非常相似。

AWX 專案或簡稱 AWX 是一個開源社群專案，由 Red Hat 贊助，使你能夠更好地控制環境中的 Ansible 專案。AWX 是從中派生出 automation controller 組件的上游專案。

如果你正在尋找企業解決方案，那麼你將尋找 Automation Controller，或者你可能以前聽說過這個作為 Ansible Tower。Ansible Automation Controller 是 Ansible Automation Platform 的控制平面。

AWX 和 Automation Controller 都帶來了以下功能，超越了我們在本節中迄今為止涵蓋的所有內容。

- 用戶界面
- 基於角色的訪問控制
- 工作流程
- CI/CD 整合

Automation Controller 是企業產品，你為支持付費。

我們將查看在 minikube Kubernetes 環境中部署 AWX。

### 部署 Ansible AWX

AWX 不需要部署到 Kubernetes 集群，來自 ansible 的 AWX [github](https://github.com/ansible/awx) 將為你提供該詳細資訊。但是，從版本 18.0 開始，AWX Operator 是安裝 AWX 的首選方式。

首先，我們需要一個 minikube 集群。如果你在 Kubernetes 部分跟隨，我們可以通過使用 `minikube start --cpus=4 --memory=6g --addons=ingress` 指令創建新的 minikube 集群來執行此操作。

![](Images/Day69_config2.png)

官方 [Ansible AWX Operator](https://github.com/ansible/awx-operator) 可以在這裡找到。正如安裝說明中所述，你應該克隆此儲存庫，然後運行部署。

我分叉了上面的 repo，然後運行了 `git clone https://github.com/MichaelCade/awx-operator.git` 我的建議是你做同樣的事情，不要使用我的儲存庫，因為我可能會更改內容或它可能不存在。

在克隆的儲存庫中，你會找到一個 awx-demo.yml 檔案，我們需要將 `NodePort` 更改為 `ClusterIP`，如下所示：

```Yaml
---
apiVersion: awx.ansible.com/v1beta1
kind: AWX
metadata:
  name: awx-demo
spec:
  service_type: ClusterIP
```

下一步是定義我們將部署 awx operator 的命名空間，使用 `export NAMESPACE=awx` 指令，然後是 `make deploy`，我們將開始部署。

![](Images/Day69_config3.png)

在檢查中，我們有新的命名空間，並且我們的 awx-operator-controller pod 在命名空間中運行。`kubectl get pods -n awx`

![](Images/Day69_config4.png)

在克隆的儲存庫中，你會找到一個名為 awx-demo.yml 的檔案，我們現在想將其部署到 Kubernetes 集群和 awx 命名空間中。`kubectl create -f awx-demo.yml -n awx`

![](Images/Day69_config5.png)

你可以使用 `kubectl get pods -n awx -w` 關注進度，這將持續視覺監控正在發生的事情。

當一切運行時，你應該有類似於你在下面看到的圖像。

![](Images/Day69_config6.png)

現在我們應該能夠在運行新終端機 `minikube service awx-demo-service --url -n $NAMESPACE` 後訪問我們的 awx 部署，以通過 minikube ingress 公開此功能。

![](Images/Day69_config7.png)

如果我們然後打開瀏覽器到該地址 []，你可以看到我們被提示輸入用戶名和密碼。

![](Images/Day69_config8.png)

預設用戶名是 admin，要獲取密碼，我們可以運行以下指令來獲取此 `kubectl get secret awx-demo-admin-password -o jsonpath="{.data.password}" -n awx| base64 --decode`

![](Images/Day69_config9.png)

這為你提供了一個 UI，可以在集中位置管理 playbook 和配置管理任務，它還允許你作為團隊一起工作，而不是我們到目前為止在這裡所做的，我們從一個 ansible 控制站運行。

這是你可以去並花更多時間演練此工具內功能的另一個領域。

我將指出來自 Jeff Geerling 的一個很好的資源，它更詳細地介紹了使用 Ansible AWX。[Ansible 101 - Episode 10 - Ansible Tower and AWX](https://www.youtube.com/watch?v=iKmY4jEiy_A&t=752s)

在這個視頻中，他還詳細介紹了 Automation Controller（以前是 Ansible Tower）和 Ansible AWX（免費和開源）之間的差異。

### Ansible Vault

`ansible-vault` 允許我們加密和解密 Ansible 數據檔案。在本節中，我們跳過並將一些敏感資訊放在純文字中。

內建於 Ansible 二進位檔的是 `ansible-vault`，它允許我們掩蓋此敏感資訊。

![](Images/Day69_config10.png)

Secrets Management 已逐漸成為另一個應該花更多時間的領域，與 HashiCorp Vault 或 AWS Key Management Service 等工具一起。我將標記這是一個需要更深入研究的領域。

我將再次連結來自 Jeff Geerling 的一個很好的資源和演示來演練 [Ansible 101 - Episode 6 - Ansible Vault and Roles](https://www.youtube.com/watch?v=JFweg2dUvqM)

### Ansible Galaxy（文件）

現在，我們已經使用 `ansible-galaxy` 為演示專案創建了一些 roles 和檔案結構。但我們還有 [Ansible Galaxy 文件](https://galaxy.ansible.com/docs/)

「Galaxy 是查找和共享 Ansible 內容的中心。」

### Ansible 測試

- [Ansible Molecule](https://molecule.readthedocs.io/en/latest/) - molecule 專案旨在幫助開發和測試 Ansible roles

- [Ansible Lint](https://ansible-lint.readthedocs.io/en/latest/) - 用於 linting playbooks、roles 和 collections 的 CLI 工具

### 其他資源

- [Ansible Documentation](https://docs.ansible.com/ansible/latest/index.html)

## 資源

- [What is Ansible](https://www.youtube.com/watch?v=1id6ERvfozo)
- [Ansible 101 - Episode 1 - Introduction to Ansible](https://www.youtube.com/watch?v=goclfp6a2IQ)
- [NetworkChuck - You need to learn Ansible right now!](https://www.youtube.com/watch?v=5hycyr-8EKs&t=955s)
- [Your complete guide to Ansible](https://www.youtube.com/playlist?list=PLnFWJCugpwfzTlIJ-JtuATD2MBBD7_m3u)

上面列出的最終播放列表是本節中很多代碼和想法的來源，這是一個很好的資源和視頻格式的演練。

這篇文章結束了我們對配置管理的了解，接下來我們轉向 CI/CD Pipelines 以及我們可能看到和使用的一些工具和流程，以實現應用程式開發和發布的工作流程。

我們[第 70 天](day70.md)見