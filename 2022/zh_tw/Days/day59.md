---
title: '#90DaysOfDevOps - 使用 Terraform 和變數創建 VM - 第 59 天'
published: false
description: 90DaysOfDevOps - 使用 Terraform 和變數創建 VM
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049051
---

## 使用 Terraform 和變數創建 VM

在本節中，我們將使用 terraform 在 VirtualBox 內創建一個 VM 或兩個 VM。這不正常，VirtualBox 是一個工作站虛擬化選項，這不會是 Terraform 的用例，但我目前在高空 36,000 英尺，儘管我在雲端這麼高的地方部署了公共雲資源，但在筆記型電腦上本地執行此操作要快得多。

純粹用於演示目的，但概念是相同的，我們將擁有所需的狀態配置代碼，然後我們將針對 VirtualBox 提供者運行它。過去，我們在這裡使用了 vagrant，我在本節開始時涵蓋了 vagrant 和 terraform 之間的差異。

### 在 VirtualBox 中創建虛擬機器

我們要做的第一件事是創建一個名為 VirtualBox 的新資料夾，然後我們可以創建一個 VirtualBox.tf 檔案，這將是我們定義資源的地方。下面的代碼可以在 VirtualBox 資料夾中找到，名為 VirtualBox.tf，將在 Virtualbox 中創建 2 個 VM。

你可以在[這裡](https://registry.terraform.io/providers/terra-farm/virtualbox/latest/docs/resources/vm)找到有關社群 VirtualBox 提供者的更多資訊

```
terraform {
  required_providers {
    virtualbox = {
      source = "terra-farm/virtualbox"
      version = "0.2.2-alpha.1"
    }
  }
}

# There are currently no configuration options for the provider itself.

resource "virtualbox_vm" "node" {
  count     = 2
  name      = format("node-%02d", count.index + 1)
  image     = "https://app.vagrantup.com/ubuntu/boxes/bionic64/versions/20180903.0.0/providers/virtualbox.box"
  cpus      = 2
  memory    = "512 mib"

  network_adapter {
    type           = "hostonly"
    host_interface = "vboxnet1"
  }
}

output "IPAddr" {
  value = element(virtualbox_vm.node.*.network_adapter.0.ipv4_address, 1)
}

output "IPAddr_2" {
  value = element(virtualbox_vm.node.*.network_adapter.0.ipv4_address, 2)
}

```

現在我們已經定義了代碼，我們現在可以在資料夾上執行 `terraform init` 來下載 Virtualbox 的提供者。

![](Images/Day59_IAC1.png)

你還需要在系統上安裝 VirtualBox。然後我們可以運行 `terraform plan` 來查看代碼將為我們創建的內容。然後是 `terraform apply`，下面的圖像顯示了完成的過程。

![](Images/Day59_IAC2.png)

在 Virtualbox 中，你現在將看到 2 個虛擬機器。

![](Images/Day59_IAC3.png)

### 更改配置

讓我們在部署中添加另一個節點。我們可以簡單地更改 count 行以顯示我們所需的新節點數量。當我們運行 `terraform apply` 時，它看起來像下面這樣。

![](Images/Day59_IAC4.png)

完成後，在 VirtualBox 中你可以看到我們現在有 3 個節點啟動並運行。

![](Images/Day59_IAC5.png)

當我們完成時，我們可以使用 `terraform destroy` 清理這個，我們的機器將被移除。

![](Images/Day59_IAC6.png)

### 變數和輸出

我們確實在上一節中運行 hello-world 範例時提到了輸出。但我們可以在這裡更詳細地了解。

但這裡我們也可以使用許多其他變數，還有幾種不同的方式可以定義變數。

- 我們可以使用 `terraform plan` 或 `terraform apply` 指令手動輸入變數

- 我們可以在塊內的 .tf 檔案中定義它們

- 我們可以在系統內使用環境變數，使用 `TF_VAR_NAME` 作為格式。

- 我的偏好是在專案資料夾中使用 terraform.tfvars 檔案。

- 有一個 \*auto.tfvars 檔案選項

- 或者我們可以在運行 `terraform plan` 或 `terraform apply` 時使用 `-var` 或 `-var-file` 定義。

從下往上移動將是定義變數的順序。

我們還提到狀態檔案將包含敏感資訊。我們可以將敏感資訊定義為變數，我們可以將其定義為敏感。

```
variable "some resource"  {
    description = "something important"
    type= string
    sensitive = true

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

我們[第 60 天](day60.md)見