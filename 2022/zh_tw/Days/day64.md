---
title: '#90DaysOfDevOps - Ansible：開始使用 - 第 64 天'
published: false
description: '90DaysOfDevOps - Ansible：開始使用'
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048765
---

## Ansible：開始使用

我們在[昨天的大圖景課程](day63.md)中稍微涵蓋了 Ansible 是什麼，但我們將在這裡在此基礎上開始使用更多資訊。首先 Ansible 來自 RedHat。其次，它是無代理的，通過 SSH 連接並運行指令。第三，它是跨平台的（Linux 和 macOS、WSL2）和開源的（還有一個付費的企業選項）Ansible 推送配置與其他模型相比。

### Ansible 安裝

正如你可能想像的那樣，RedHat 和 Ansible 團隊在記錄 Ansible 方面做得非常出色。這通常從安裝步驟開始，你可以在[這裡](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)找到。記住我們說過 Ansible 是一個無代理自動化工具，該工具部署到稱為「控制節點」的系統，從此控制節點通過 SSH 管理機器和其他設備（可能是網路）。

上面連結的文件確實說明 Windows OS 不能用作控制節點。

對於我的控制節點，至少這個演示，我將使用我們在 [Linux 部分](day20.md)中創建的 Linux VM 作為我的控制節點。

該系統運行 Ubuntu，安裝步驟只需要以下指令。

```Shell
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```

現在我們應該在控制節點上安裝了 ansible，你可以通過運行 `ansible --version` 來檢查這一點，你應該看到類似下面的內容。

![](Images/Day64_config1.png)

在我們開始查看控制環境中的其他節點之前，我們也可以通過對本地機器運行指令來檢查 ansible 的功能 `ansible localhost -m ping` 將使用 [Ansible Module](https://docs.ansible.com/ansible/2.9/user_guide/modules_intro.html)，這是在許多不同系統上執行單個任務的快速方法。我的意思是只有本地主機沒有太多樂趣，但想像你想要獲取某些東西或確保所有系統都啟動，你有 1000+ 伺服器和設備。

![](Images/Day64_config2.png)

或者模組的實際真實使用可能是 `ansible webservers -m service -a "name=httpd state=started"` 這將告訴我們所有 Web 伺服器是否都有 httpd 服務運行。我已經略過了該指令中使用的 webservers 術語。

### hosts

我上面使用 localhost 對系統運行簡單 ping 模組的方式，我無法指定網路上的另一台機器，例如在我使用的環境中，運行 VirtualBox 的 Windows 主機有一個 IP 為 10.0.0.1 的網路適配器，但你可以看到下面我可以通過 ping 到達，但我無法使用 ansible 執行該任務。

![](Images/Day64_config3.png)

為了指定我們的主機或我們希望使用這些任務自動化的節點，我們需要定義它們。我們可以通過導航到系統上的 /etc/ansible 目錄來定義它們。

![](Images/Day64_config4.png)

我們要編輯的檔案是主機檔案，使用文字編輯器我們可以跳入並定義我們的主機。主機檔案包含大量關於如何使用和修改檔案的說明。我們想向下滾動到底部，我們將創建一個名為 [windows] 的新組，我們將為該主機添加 `10.0.0.1` IP 地址。保存檔案。

![](Images/Day64_config5.png)

但是，記住我說過你需要有 SSH 可用才能讓 Ansible 連接到你的系統。正如你在下面看到的，當我運行 `ansible windows -m ping` 時，我們得到不可達，因為通過 SSH 連接失敗。

![](Images/Day64_config6.png)

我現在也開始向我們的清單中添加一些額外的主機，此檔案的另一個名稱，因為這是你將定義所有設備的地方，可能是網路設備、交換機和路由器，例如也會添加到此處並分組。但在我們的主機檔案中，我還添加了訪問 Linux 系統組的憑證。

![](Images/Day64_config7.png)

現在如果我們運行 `ansible Linux -m ping`，我們會得到成功，如下所示。

![](Images/Day64_config8.png)

然後我們有節點要求，這些是你希望自動化配置的目標系統。我們在這些上不為 Ansible 安裝任何東西（我的意思是我們可能正在安裝軟體，但沒有我們需要的 Ansible 客戶端）Ansible 將通過 SSH 建立連接並通過 SFTP 發送任何內容。（如果你願意並且配置了 SSH，你可以使用 SCP vs SFTP。）

### Ansible 指令

你看到我們能夠對 Linux 機器運行 `ansible Linux -m ping` 並獲得響應，基本上，使用 Ansible 我們可以運行許多臨時指令。但你可以對一組系統運行此操作並獲取該資訊。[臨時指令](https://docs.ansible.com/ansible/latest/user_guide/intro_adhoc.html)

如果你發現自己重複指令，或者更糟糕的是，你必須登入單個系統來運行這些指令，那麼 Ansible 可以在那裡提供幫助。例如，下面的簡單指令將為我們添加到 Linux 組的所有系統提供所有作業系統詳細資訊的輸出。
`ansible linux -a "cat /etc/os-release"`

其他用例可能是重啟系統、複製檔案以及管理打包器和用戶。你還可以將臨時指令與 Ansible 模組結合使用。

臨時指令使用聲明式模型，計算並執行達到指定最終狀態所需的操作。它們通過在開始前檢查當前狀態來實現某種形式的冪等性，除非當前狀態與指定的最終狀態不同，否則不執行任何操作。

## 資源

- [What is Ansible](https://www.youtube.com/watch?v=1id6ERvfozo)
- [Ansible 101 - Episode 1 - Introduction to Ansible](https://www.youtube.com/watch?v=goclfp6a2IQ)
- [NetworkChuck - You need to learn Ansible right now!](https://www.youtube.com/watch?v=5hycyr-8EKs&t=955s)

我們[第 65 天](day65.md)見