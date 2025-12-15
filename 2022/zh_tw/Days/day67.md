---
title: '#90DaysOfDevOps - 使用 Roles 和部署負載平衡器 - 第 67 天'
published: false
description: '90DaysOfDevOps - 使用 Roles 和部署負載平衡器'
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048713
---

## 使用 Roles 和部署負載平衡器

在上一節中，我們涵蓋了 roles 並使用 `ansible-galaxy` 指令幫助為我們將使用的一些 roles 創建資料夾結構。我們完成了一個更整潔的工作儲存庫，用於配置代碼，因為所有內容都隱藏在 role 資料夾中。

但是，我們只使用了 apache2 role，並且有一個工作的 playbook3.yaml 來處理 Web 伺服器。

此時，如果你只使用了 `vagrant up web01 web02`，現在是運行 `vagrant up loadbalancer` 的時候了，這將啟動另一個 Ubuntu 系統，我們將用作負載平衡器/代理。

我們已經在主機檔案中定義了這台新機器，但在它可用之前，我們沒有配置 ssh 密鑰，所以我們還需要在系統啟動並準備好時運行 `ssh-copy-id loadbalancer`。

### Common role

我在昨天課程結束時創建了 `common` role，common 將在所有伺服器上使用，而其他 roles 特定於用例，現在我要安裝的應用程式與虛假的一樣常見，我看不到很多理由，但它顯示了目標。在我們的 common role 資料夾結構中，導航到 tasks 資料夾，你將有一個 main.yml。在此 YAML 中，我們需要將其指向我們的 install_tools.yml 檔案，我們通過添加一行 `- import_tasks: install_tools.yml` 來執行此操作，這曾經是 `include`，但這很快就會被棄用，所以我們使用 import_tasks。

```Yaml
- name: "Install Common packages"
  apt: name={{ item }} state=latest
  with_items:
   - neofetch
   - tree
   - figlet
```

然後在我們的 playbook 中，我們為每個主機塊添加 common role。

```Yaml
- hosts: webservers
  become: yes
  vars:
    http_port: 8000
    https_port: 4443
    html_welcome_msg: "Hello 90DaysOfDevOps - Welcome to Day 66!"
  roles:
    - common
    - apache2
```

### nginx

下一階段是在負載平衡器 VM 上安裝和配置 nginx。與 common 資料夾結構一樣，我們基於上一節有 nginx。

首先，我們將在 playbook 中添加主機塊。此塊將包括我們的 common role，然後是新的 nginx role。

可以在這裡找到 playbook。[playbook4.yml](Days/../Configmgmt/ansible-scenario4/playbook4.yml)

```Yaml
- hosts: webservers
  become: yes
  vars:
    http_port: 8000
    https_port: 4443
    html_welcome_msg: "Hello 90DaysOfDevOps - Welcome to Day 66!"
  roles:
    - common
    - apache2

- hosts: proxy
  become: yes
  roles:
    - common
    - nginx
```

為了使這有意義，我們必須定義我們希望運行的任務，同樣，我們將修改 tasks 中的 main.yml 以指向兩個檔案，一個用於安裝，一個用於配置。

根據我們期望的結果，我修改了一些其他檔案，查看資料夾 [ansible-scenario4](Days/Configmgmt/ansible-scenario4) 以了解所有更改的檔案。你應該檢查 nginx 資料夾中的 tasks、handlers 和 templates 資料夾，你會找到那些額外的更改和檔案。

### 運行更新的 playbook

自昨天以來，我們添加了 common role，現在將在系統上安裝一些套件，然後我們還添加了 nginx role，包括安裝和配置。

讓我們使用 `ansible-playbook playbook4.yml` 運行 playbook4.yml

![](Images/Day67_config1.png)

現在我們已經配置了 Web 伺服器和負載平衡器，我們現在應該能夠訪問 http://192.168.169.134/，這是負載平衡器的 IP 地址。

![](Images/Day67_config2.png)

如果你正在跟隨並且沒有這種狀態，那麼這可能是由於環境中的伺服器 IP 地址。檔案可以在 `templates\mysite.j2` 中找到，看起來類似於下面：你需要使用 Web 伺服器 IP 地址進行更新。

```J2
    upstream webservers {
        server 192.168.169.131:8000;
        server 192.168.169.132:8000;
    }

    server {
        listen 80;

        location / {
                proxy_pass http://webservers;
        }
    }
```

我非常有信心我們安裝的內容都很好，但讓我們使用 ansible 的臨時指令來檢查這些常用工具的安裝。

`ansible loadbalancer -m command -a neofetch`

![](Images/Day67_config3.png)

## 資源

- [What is Ansible](https://www.youtube.com/watch?v=1id6ERvfozo)
- [Ansible 101 - Episode 1 - Introduction to Ansible](https://www.youtube.com/watch?v=goclfp6a2IQ)
- [NetworkChuck - You need to learn Ansible right now!](https://www.youtube.com/watch?v=5hycyr-8EKs&t=955s)
- [Your complete guide to Ansible](https://www.youtube.com/playlist?list=PLnFWJCugpwfzTlIJ-JtuATD2MBBD7_m3u)

上面列出的最終播放列表是本節中很多代碼和想法的來源，這是一個很好的資源和視頻格式的演練。

我們[第 68 天](day68.md)見