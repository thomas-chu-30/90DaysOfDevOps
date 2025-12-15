---
title: '#90DaysOfDevOps - Docker 容器、Provisioners 和模組 - 第 60 天'
published: false
description: '90DaysOfDevOps - Docker 容器、Provisioners 和模組'
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049052
---

## Docker 容器、Provisioners 和模組

在 [第 59 天](day59.md) 我們使用 Terraform 在本地免費 VirtualBox 環境中配置了虛擬機器。在本節中，我們將部署一個帶有一些配置的 Docker 容器到本地 Docker 環境。

### Docker 演示

首先，我們將使用下面的代碼塊，下面的結果是我們希望將一個簡單的 Web 應用程式部署到 docker 中並發布它，以便我們的網路可以訪問它。我們將使用 nginx，我們將在筆記型電腦上的 localhost 和端口 8000 上外部提供此功能。我們使用來自社群的 docker 提供者，你可以看到我們使用的 docker 圖像也在配置中說明。

```
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "2.16.0"
    }
  }
}

provider "docker" {}

resource "docker_image" "nginx" {
  name         = "nginx:latest"
  keep_locally = false
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "tutorial"
  ports {
    internal = 80
    external = 8000
  }
}
```

第一個任務是使用 `terraform init` 指令將提供者下載到本地機器。

![](Images/Day60_IAC1.png)

然後我們運行 `terraform apply`，然後是 `docker ps`，你可以看到我們有一個運行的容器。

![](Images/Day60_IAC2.png)

如果我們然後打開瀏覽器，我們可以導航到 `http://localhost:8000/`，你會看到我們可以訪問 NGINX 容器。

![](Images/Day60_IAC3.png)

你可以在 [Docker Provider](https://registry.terraform.io/providers/kreuzwerker/docker/latest/docs/resources/container) 上找到更多資訊

上面是使用 Terraform 加 Docker 可以完成的非常簡單的演示，以及我們現在如何在 Terraform 狀態下管理它。我們在容器部分涵蓋了 docker-compose，在某種程度上，這與基礎設施即代碼以及 Kubernetes 之間有一些交叉。

為了展示這一點以及 Terraform 如何處理更多複雜性，我們將採用我們使用 docker-compose 創建的 WordPress 和 MySQL 的 docker-compose 檔案，我們將把它放到 Terraform。你可以找到 [docker-wordpress.tf](2022/Days/IaC/Docker-WordPress/docker-WordPress.tf)

```
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "2.16.0"
    }
  }
}

provider "docker" {}

variable wordpress_port {
  default = "8080"
}

resource "docker_volume" "db_data" {
  name = "db_data"
}

resource "docker_network" "wordpress_net" {
  name = "wordpress_net"
}

resource "docker_container" "db" {
  name  = "db"
  image = "mysql:5.7"
  restart = "always"
  network_mode = "wordpress_net"
  env = [
     "MYSQL_ROOT_PASSWORD=wordpress",
     "MYSQL_PASSWORD=wordpress",
     "MYSQL_USER=wordpress",
     "MYSQL_DATABASE=wordpress"
  ]
  mounts {
    type = "volume"
    target = "/var/lib/mysql"
    source = "db_data"
    }
}

resource "docker_container" "wordpress" {
  name  = "wordpress"
  image = "wordpress:latest"
  restart = "always"
  network_mode = "wordpress_net"
  env = [
    "WORDPRESS_DB_HOST=db:3306",
    "WORDPRESS_DB_USER=wordpress",
    "WORDPRESS_DB_NAME=wordpress",
    "WORDPRESS_DB_PASSWORD=wordpress"
  ]
  ports {
    internal = "80"
    external = "${var.wordpress_port}"
  }
}
```

我們再次將此放在新資料夾中，然後運行 `terraform init` 指令以下載所需的 provisioners。

![](Images/Day60_IAC4.png)

然後我們運行 `terraform apply` 指令，然後查看 docker ps 輸出，我們應該看到新創建的容器。

![](Images/Day60_IAC5.png)

然後我們也可以導航到 WordPress 前端。就像我們在容器部分使用 docker-compose 進行此過程時一樣，我們現在可以進行設置，我們的 WordPress 文章將存儲在 MySQL 資料庫中。

![](Images/Day60_IAC6.png)

現在我們已經詳細涵蓋了容器和 Kubernetes，我們可能知道這對於測試來說是可以的，但如果你要運行網站，你不會單獨使用容器來執行此操作，你會考慮使用 Kubernetes 來實現這一點，接下來我們將查看使用 Terraform 與 Kubernetes。

### Provisioners

Provisioners 的存在是為了如果某些東西不能是聲明式的，我們有一種方法可以將其解析到我們的部署中。

如果你沒有其他替代方案，並且將此複雜性添加到代碼中是去的地方，那麼你可以通過運行類似於以下代碼塊的內容來執行此操作。

```
resource "docker_container" "db" {
  # ...

  provisioner "local-exec" {
    command = "echo The server's IP address is ${self.private_ip}"
  }
}

```

remote-exec provisioner 在創建遠程資源後在其上調用腳本。這可以用於特定於 OS 的東西，或者可以用於包裝配置管理工具。雖然注意到我們在它們的 provisioners 中涵蓋了其中一些。

[有關 provisioners 的更多詳細資訊](https://www.terraform.io/language/resources/provisioners/syntax)

- file
- local-exec
- remote-exec
- vendor
  - ansible
  - chef
  - puppet

### 模組

模組是容納多個一起使用的資源的容器。模組由同一目錄中的 .tf 檔案集合組成。

模組是分離基礎設施資源的好方法，也能夠拉入已經創建的第三方模組，所以你不需要重新發明輪子。

例如，如果我們想使用相同的專案來構建一些 VM、VPC、安全組，然後還有一個 Kubernetes 集群，我們可能希望將資源拆分為模組，以更好地定義資源以及它們的分組位置。

模組的另一個好處是你可以獲取這些模組並在其他專案上使用它們，或公開共享它們以幫助社群。

我們正在將基礎設施分解為組件，組件在這裡被稱為模組。

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

我們[第 61 天](day61.md)見