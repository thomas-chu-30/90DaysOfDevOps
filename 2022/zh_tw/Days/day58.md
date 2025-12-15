---
title: '#90DaysOfDevOps - HashiCorp 配置語言 (HCL) - 第 58 天'
published: false
description: 90DaysOfDevOps - HashiCorp 配置語言 (HCL)
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048741
---

## HashiCorp 配置語言 (HCL)

在我們開始使用 Terraform 製作東西之前，我們必須稍微深入了解 HashiCorp 配置語言 (HCL)。到目前為止，在我們的挑戰中，我們已經研究了一些不同的腳本和程式語言，這是另一個。我們觸及了 [Go 程式語言](day07.md)，然後是 [bash 腳本](day19.md)，當談到[網路自動化](day27.md)時，我們甚至觸及了一點 python。

現在我們必須涵蓋 HashiCorp 配置語言 (HCL)，如果這是你第一次看到這種語言，它可能看起來有點令人生畏，但它非常簡單且非常強大。

當我們在本節中移動時，我們將使用可以在系統上本地運行的範例，無論你使用什麼 OS，我們將使用 VirtualBox，儘管這不是你通常與 Terraform 一起使用的基礎設施平台。但是，在本地運行這是免費的，將允許我們實現我們在這篇文章中尋找的內容。我們也可以將這篇文章的概念擴展到 docker 或 Kubernetes。

不過，總的來說，你會或應該使用 Terraform 在公共雲（AWS、Google、Microsoft Azure）中部署基礎設施，但也在虛擬化環境中，如（VMware、Microsoft Hyper-V、Nutanix AHV）。在公共雲中，Terraform 允許我們做的遠不止虛擬機器自動部署，我們可以創建所有必需的基礎設施，如 PaaS 工作負載和所有必需的網路資產，如 VPC 和安全組。

Terraform 有兩個重要方面，我們有代碼，我們將在這篇文章中深入了解，然後我們還有狀態。這兩者一起可以稱為 Terraform 核心。然後我們有希望與之通信並部署到的環境，這使用 Terraform 提供者執行，在上一節中簡要提到，但我們有 AWS 提供者，我們有 Azure 提供者等。有數百個。

### 基本 Terraform 用法

讓我們看一下 Terraform `.tf` 檔案，看看它們是如何組成的。我們將演練的第一個範例將是將資源部署到 AWS 的代碼，這還需要在系統上安裝並為帳戶配置 AWS CLI。

### 提供者

在我們的 `.tf` 檔案結構的頂部，通常稱為 `main.tf`，至少直到我們使事情變得更加複雜。在這裡，我們將定義我們之前提到的提供者。正如你所看到的，AWS 提供者的來源是 `hashicorp/aws`，這意味著提供者由 hashicorp 自己維護或已發布。預設情況下，你將引用 [Terraform Registry](https://registry.terraform.io/) 中可用的提供者，你也可以編寫自己的提供者，並在本地使用它們，或自行發布到 Terraform Registry。

```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.0"
    }
  }
}
```

我們也可能在這裡添加一個區域，以確定我們希望配置到哪個 AWS 區域，我們可以通過添加以下內容來執行此操作：

```
provider "aws" {
  region = "ap-southeast-1" //需要部署資源的區域
}
```

### Terraform 資源

- terraform 配置檔案的另一個重要組件，它描述一個或多個基礎設施物件，如 EC2、負載平衡器、VPC 等。

- 資源塊聲明給定類型（"aws_instance"）的資源，具有給定的本地名稱（"90daysofdevops"）。

- 資源類型和名稱一起作為給定資源的標識符。

```
resource "aws_instance" "90daysofdevops" {
  ami               = data.aws_ami.instance_id.id
  instance_type     = "t2.micro"
  availability_zone = "us-west-2a"
  security_groups   = [aws_security_group.allow_web.name]
  user_data         = <<-EOF
                #! /bin/bash
                sudo yum update
                sudo yum install -y httpd
                sudo systemctl start httpd
                sudo systemctl enable httpd
                echo "
<h1>Deployed via Terraform</h1>

" | sudo tee /var/www/html/index.html
        EOF
  tags = {
    Name = "Created by Terraform"
  }
}
```

你可以從上面看到，我們還在運行 `yum` 更新並將 `httpd` 安裝到我們的 ec2 實例中。

如果我們現在查看完整的 main.tf 檔案，它可能看起來像這樣。

```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.27"
    }
  }

  required_version = ">= 0.14.9"
}

provider "aws" {
  profile = "default"
  region  = "us-west-2"
}

resource "aws_instance" "90daysofdevops" {
  ami           = "ami-830c94e3"
  instance_type = "t2.micro"
  availability_zone = "us-west-2a"
    user_data         = <<-EOF
                #! /bin/bash
                sudo yum update
                sudo yum install -y httpd
                sudo systemctl start httpd
                sudo systemctl enable httpd
                echo "
<h1>Deployed via Terraform</h1>

" | sudo tee /var/www/html/index.html
        EOF
  tags = {
    Name = "Created by Terraform"

  tags = {
    Name = "ExampleAppServerInstance"
  }
}
```

上面的代碼將在 AWS 中作為 ec2 實例部署一個非常簡單的 Web 伺服器，這和任何其他類似配置的好處是我們可以重複這個，我們每次都會得到相同的輸出。除了我搞砸代碼的機會之外，上面沒有人類交互。

我們可以看一下一個超級簡單的範例，一個你可能永遠不會使用的範例，但讓我們無論如何都幽默一下。就像所有好的腳本和程式語言一樣，我們應該從 hello-world 場景開始。

```
terraform {
  # This module is now only being tested with Terraform 0.13.x. However, to make upgrading easier, we are setting
  # 0.12.26 as the minimum version, as that version added support for required_providers with source URLs, making it
  # forwards compatible with 0.13.x code.
  required_version = ">= 0.12.26"
}

# website::tag::1:: The simplest possible Terraform module: it just outputs "Hello, World!"
output "hello_world" {
  value = "Hello, 90DaysOfDevOps from Terraform"
}
```

你會在 IAC 資料夾下的 hello-world 中找到此檔案，但開箱即用，這不會簡單地工作，有一些指令我們需要運行來使用 terraform 代碼。

在你的終端機中導航到已創建 main.tf 的資料夾，這可能來自此儲存庫，或者你可以使用上面的代碼創建一個新的。

當在該資料夾中時，我們將運行 `terraform init`

我們需要在任何我們有或運行任何 terraform 代碼之前執行此操作。初始化配置目錄下載並安裝配置中定義的提供者，在這種情況下，我們沒有提供者，但在上面的範例中，這將為此配置下載 AWS 提供者。

![](Images/Day58_IAC1.png)

下一個指令將是 `terraform plan`

`terraform plan` 指令創建一個執行計劃，讓你預覽 Terraform 計劃對基礎設施進行的更改。

你可以簡單地在下面看到，使用我們的 hello-world 範例，我們將看到輸出，如果這是一個 AWS ec2 實例，我們將看到我們將創建的所有步驟。

![](Images/Day58_IAC2.png)

此時，我們已經初始化了儲存庫，並且在需要的地方下載了提供者，我們已經運行了測試演練以確保這是我們想要看到的，所以現在我們可以運行並部署代碼。

`terraform apply` 允許我們執行此操作，此指令有一個內建的安全措施，這將再次為你提供計劃視圖，說明將要發生什麼，這需要你做出回應以說「是」繼續。

![](Images/Day58_IAC3.png)

當我們鍵入 yes 輸入值時，我們的代碼被部署。不是那麼令人興奮，但你可以看到我們有在代碼中定義的輸出。

![](Images/Day58_IAC4.png)

現在我們沒有部署任何東西，我們沒有添加、更改或銷毀任何東西，但如果我們這樣做了，我們也會在上面看到這一點。但是，如果我們已經部署了某些東西，我們想要擺脫我們部署的所有東西，我們可以使用 `terraform destroy` 指令。同樣，這有安全性，你必須鍵入 yes，儘管你可以在 `apply` 和 `destroy` 指令的末尾使用 `--auto-approve` 來繞過手動干預。但我建議只在學習和測試時使用此捷徑，因為一切都會消失，有時比構建時更快。

從這個，我們已經涵蓋了 Terraform CLI 的 4 個指令。

- `terraform init` = 使用提供者準備專案資料夾
- `terraform plan` = 顯示將要創建和更改的內容，基於我們的代碼在下一個指令期間。
- `terraform apply` = 將繼續部署代碼中定義的資源。
- `terraform destroy` = 將銷毀我們在專案中創建的資源

我們還涵蓋了代碼檔案的兩個重要方面。

- providers = terraform 如何通過 API 與終端平台通信
- resources = 我們想用代碼部署的內容

運行 `terraform init` 時要注意的另一件事是查看資料夾前後的樹狀結構，看看發生了什麼以及我們在哪裡存儲提供者和模組。

### Terraform 狀態

我們還需要了解在目錄內創建的狀態檔案，對於這個 hello world 範例，我們的狀態檔案很簡單。這是一個 JSON 檔案，它是根據 Terraform 的世界表示。狀態會樂意展示你的敏感數據，所以要小心，作為最佳實踐，在上傳到 GitHub 之前將 `.tfstate` 檔案放在 `.gitignore` 資料夾中。

預設情況下，狀態檔案如你所見存在於與專案代碼相同的目錄中，但它也可以作為選項遠程存儲。在生產環境中，這很可能是一個共享位置，如 S3 儲存桶。

另一個選項可能是 Terraform Cloud，這是一個付費託管服務。（最多 5 個用戶免費）

在遠程位置存儲狀態的優點是我們得到：

- 敏感數據加密
- 協作
- 自動化
- 但是，它可能會帶來增加的複雜性

```JSON
{
  "version": 4,
  "terraform_version": "1.1.6",
  "serial": 1,
  "lineage": "a74296e7-670d-0cbb-a048-f332696ca850",
  "outputs": {
    "hello_world": {
      "value": "Hello, 90DaysOfDevOps from Terraform",
      "type": "string"
    }
  },
  "resources": []
}
```

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

我們[第 59 天](day59.md)見