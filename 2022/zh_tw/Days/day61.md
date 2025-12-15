---
title: '#90DaysOfDevOps - Kubernetes 和多環境 - 第 61 天'
published: false
description: 90DaysOfDevOps - Kubernetes 和多環境
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048743
---

## Kubernetes 和多環境

到目前為止，在基礎設施即代碼部分，我們已經研究了部署虛擬機器，儘管是到 VirtualBox，但前提確實是相同的，因為我們在代碼中定義我們希望虛擬機器看起來的樣子，然後我們部署。Docker 容器也是如此，在本節中，我們將查看如何使用 Terraform 與 Kubernetes 支持的資源交互。

我一直在使用 Terraform 在 3 個主要雲提供商中部署我的 Kubernetes 集群用於演示目的，你可以找到儲存庫 [tf_k8deploy](https://github.com/MichaelCade/tf_k8deploy)

但是，你也可以使用 Terraform 與 Kubernetes 集群內的物件交互，這可以使用 [Kubernetes provider](https://registry.terraform.io/providers/hashicorp/kubernetes/latest/docs) 或使用 [Helm provider](https://registry.terraform.io/providers/hashicorp/helm/latest) 來管理你的 chart 部署。

現在我們可以使用 `kubectl`，正如我們在之前的章節中所示。但在 Kubernetes 環境中使用 Terraform 有一些好處。

- 統一工作流程 - 如果你使用 Terraform 部署集群，你可以使用相同的工作流程和工具在 Kubernetes 集群內部署

- 生命週期管理 - Terraform 不僅是配置工具，它還將啟用更改、更新和刪除。

### 簡單的 Kubernetes 演示

就像我們在上一個課程中創建的演示一樣，我們現在可以將 nginx 部署到 Kubernetes 集群中，我將再次使用 minikube 進行演示。我們創建 Kubernetes.tf 檔案，你可以在[資料夾](2022/Days/IaC/Kubernetes/Kubernetes.tf)中找到它

在該檔案中，我們將定義 Kubernetes provider，我們將指向 kubeconfig 檔案，創建一個名為 nginx 的命名空間，然後我們將創建一個包含 2 個副本的部署，最後是服務。

```
terraform {
  required_providers {
    kubernetes = {
      source  = "hashicorp/kubernetes"
      version = ">= 2.0.0"
    }
  }
}
provider "kubernetes" {
  config_path = "~/.kube/config"
}
resource "kubernetes_namespace" "test" {
  metadata {
    name = "nginx"
  }
}
resource "kubernetes_deployment" "test" {
  metadata {
    name      = "nginx"
    namespace = kubernetes_namespace.test.metadata.0.name
  }
  spec {
    replicas = 2
    selector {
      match_labels = {
        app = "MyTestApp"
      }
    }
    template {
      metadata {
        labels = {
          app = "MyTestApp"
        }
      }
      spec {
        container {
          image = "nginx"
          name  = "nginx-container"
          port {
            container_port = 80
          }
        }
      }
    }
  }
}
resource "kubernetes_service" "test" {
  metadata {
    name      = "nginx"
    namespace = kubernetes_namespace.test.metadata.0.name
  }
  spec {
    selector = {
      app = kubernetes_deployment.test.spec.0.template.0.metadata.0.labels.app
    }
    type = "NodePort"
    port {
      node_port   = 30201
      port        = 80
      target_port = 80
    }
  }
}
```

我們在新專案資料夾中要做的第一件事是運行 `terraform init` 指令。

![](Images/Day61_IAC1.png)

然後在我們運行 `terraform apply` 指令之前，讓我向你展示我們沒有命名空間。

![](Images/Day61_IAC2.png)

當我們運行 apply 指令時，這將在 Kubernetes 集群內創建這 3 個新資源，命名空間、部署和服務。

![](Images/Day61_IAC3.png)

我們現在可以查看集群內已部署的資源。

![](Images/Day61_IAC4.png)

現在因為我們使用 minikube，正如你在上一節中看到的，當我們嘗試使用 docker 網路進行 ingress 時，這有其局限性。但如果我們簡單地發出 `kubectl port-forward -n nginx svc/nginx 30201:80` 指令並打開瀏覽器到 `http://localhost:30201/`，我們應該看到我們的 NGINX 頁面。

![](Images/Day61_IAC5.png)

如果你想嘗試 Terraform 和 Kubernetes 的更詳細演示，那麼 [HashiCorp Learn 網站](https://learn.hashicorp.com/tutorials/terraform/kubernetes-provider) 非常適合運行。

### 多環境

如果我們想採用我們已經運行的任何演示，但現在希望有特定的生產、預備和開發環境看起來相同並利用此代碼，有兩種方法可以使用 Terraform 實現這一點

- `terraform workspaces` - 單個後端內的多個命名部分

- 檔案結構 - 目錄佈局提供分離，模組提供重用。

不過，上述每一種都有其優缺點。

### terraform workspaces

優點

- 易於開始
- 方便的 terraform.workspace 表達式
- 最小化代碼重複

缺點

- 容易出現人為錯誤（我們試圖通過使用 TF 來消除這一點）
- 狀態存儲在同一後端內
- 代碼庫沒有明確顯示部署配置。

### 檔案結構

優點

- 後端隔離
  - 改進的安全性
  - 減少人為錯誤的可能性
- 代碼庫完全代表已部署狀態

缺點

- 需要多個 terraform apply 來配置環境
- 更多代碼重複，但可以通過模組最小化。

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

我們[第 62 天](day62.md)見