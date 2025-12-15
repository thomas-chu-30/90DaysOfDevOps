---
title: '#90DaysOfDevOps - Ansible Playbooks - 第 65 天'
published: false
description: 90DaysOfDevOps - Ansible Playbooks
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049054
---

### Ansible Playbooks

在本節中，我們將查看我至少可以看到的 Ansible 的主要原因，我的意思是使用單個指令並命中許多不同的伺服器來執行簡單的指令（例如重啟一長串伺服器並節省必須單獨連接到每個伺服器的麻煩）是很棒的。

但是，實際採用裸作業系統並聲明我們希望在那個系統上運行的軟體和服務，並確保它們都在所需狀態下運行呢？

這就是 ansible playbooks 的用武之地。Playbook 使我們能夠獲取伺服器組並對該組執行配置和安裝任務。

### Playbook 格式

Playbook > Plays > Tasks

對於任何來自體育背景的人，你可能遇到過 playbook 這個術語，playbook 告訴團隊你將如何玩，由各種 plays 和 tasks 組成，如果我們將 plays 視為運動或遊戲中的固定套路，tasks 與每個 play 相關聯，你可以有多個 tasks 組成一個 play，在 playbook 中，你可能有多個不同的 plays。

這些 playbooks 用 YAML（YAML 不是標記語言）編寫，你會發現我們到目前為止涵蓋的許多部分，特別是容器和 Kubernetes，都以 YAML 格式的配置檔案為特色。

讓我們看一下一個名為 playbook.yml 的簡單 playbook。

```Yaml
- name: Simple Play
  hosts: localhost
  connection: local
  tasks:
    - name: Ping me
      ping:
    - name: print os
      debug:
        msg: "{{ ansible_os_family }}"
```

你會在 [simple_play](days/../Configmgmt/simple_play.yml) 找到上面的檔案。如果我們然後使用 `ansible-playbook simple_play.yml` 指令，我們將演練以下步驟。

![](Images/Day65_config1.png)

你可以看到第一個「收集步驟」任務發生了，但我們沒有觸發或要求這個？此模組由 playbooks 自動調用以收集有關遠程主機的有用變數。[ansible.builtin.setup](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/setup_module.html)

我們的第二個任務是設置 ping，這不是 ICMP ping，而是一個 python 腳本，在成功連接到遠程或 localhost 時報告 `pong`。[ansible.builtin.ping](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/ping_module.html)

然後我們的第三個或第二個定義的任務，因為第一個將運行，除非你禁用，是打印告訴我們 OS 的消息。在此任務中，我們使用條件，我們可以針對所有不同類型的作業系統運行此 playbook，這將返回 OS 名稱。我們只是為了方便而消息化此輸出，但我們可以添加一個任務來說明：

```Yaml
tasks:
  - name: "shut down Debian flavoured systems"
    command: /sbin/shutdown -t now
    when: ansible_os_family == "Debian"
```

### Vagrant 設置我們的環境

我們將使用 Vagrant 設置節點環境，我將保持在合理的 4 個節點，但你可以看到這可能很容易是 300 或 3000，這就是 Ansible 和其他配置管理工具能夠配置伺服器的力量。

你可以在此處找到此檔案（[Vagrantfile](Configmgmt/Vagrantfile)）

```Vagrant
Vagrant.configure("2") do |config|
  servers=[
    {
      :hostname => "db01",
      :box => "bento/ubuntu-21.10",
      :ip => "192.168.169.130",
      :ssh_port => '2210'
    },
    {
      :hostname => "web01",
      :box => "bento/ubuntu-21.10",
      :ip => "192.168.169.131",
      :ssh_port => '2211'
    },
    {
      :hostname => "web02",
      :box => "bento/ubuntu-21.10",
      :ip => "192.168.169.132",
      :ssh_port => '2212'
    },
    {
      :hostname => "loadbalancer",
      :box => "bento/ubuntu-21.10",
      :ip => "192.168.169.134",
      :ssh_port => '2213'
    }

  ]

config.vm.base_address = 600

  servers.each do |machine|

    config.vm.define machine[:hostname] do |node|
      node.vm.box = machine[:box]
      node.vm.hostname = machine[:hostname]

      node.vm.network :public_network, bridge: "Intel(R) Ethernet Connection (7) I219-V", ip: machine[:ip]
      node.vm.network "forwarded_port", guest: 22, host: machine[:ssh_port], id: "ssh"

      node.vm.provider :virtualbox do |v|
        v.customize ["modifyvm", :id, "--memory", 2048]
        v.customize ["modifyvm", :id, "--name", machine[:hostname]]
      end
    end
  end

end
```

使用 `vagrant up` 指令在 VirtualBox 中啟動這些機器，你可能能夠添加更多內存，你可能還想為每台機器定義不同的 private_network 地址，但這在我的環境中有效。記住我們的控制盒是我們在 Linux 部分部署的 Ubuntu 桌面。

如果你資源受限，那麼你也可以運行 `vagrant up web01 web02` 只啟動我們在這裡使用的 Web 伺服器。

### Ansible 主機配置

現在我們已經準備好環境，我們可以檢查 ansible，為此，我們將使用 Ubuntu 桌面（你可以使用這個，但你也可以使用網路上訪問下面網路的任何基於 Linux 的機器）作為控制，讓我們也在 ansible hosts 檔案中將新節點添加到我們的組，你可以將此檔案視為清單，替代方案可能是另一個清單檔案，作為 ansible 指令的一部分使用 `-i filename` 調用，這可能比使用主機檔案有用，因為你可以為不同環境（可能是生產、測試和預備）擁有不同的檔案。因為我們使用預設 hosts 檔案，我們不需要指定，因為這將是使用的預設值。

我已將以下內容添加到預設 hosts 檔案。

```Text
[control]
ansible-control

[proxy]
loadbalancer

[webservers]
web01
web02

[database]
db01

```

![](Images/Day65_config2.png)

在繼續之前，我們想確保可以對節點運行指令，讓我們運行 `ansible nodes -m command -a hostname` 這個簡單的指令將測試我們有連接性並報告我們的主機名。

另外，請注意，我已將這些節點和 IP 添加到 Ubuntu 控制節點內的 /etc/hosts 檔案中，以確保連接性。我們可能還需要從 Ubuntu 盒為每個節點進行 SSH 配置。

```Text
192.168.169.140 ansible-control
192.168.169.130 db01
192.168.169.131 web01
192.168.169.132 web02
192.168.169.133 loadbalancer
```

![](Images/Day65_config3.png)

在這個階段，我們想演練在控制和伺服器節點之間設置 SSH 密鑰。這是我們接下來要做的事情，這裡的另一種方法是在主機檔案中添加變數以提供用戶名和密碼。我建議不要這樣做，因為這永遠不會是最佳實踐。

要設置 SSH 並在節點之間共享，請按照以下步驟操作，你將被提示輸入密碼（`vagrant`），你可能需要多次點擊 `y` 來接受。

`ssh-keygen`

![](Images/Day65_config5.png)

`ssh-copy-id localhost`

![](Images/Day65_config6.png)

現在如果你已打開所有 VM，那麼你可以運行 `ssh-copy-id web01 && ssh-copy-id web02 && ssh-copy-id loadbalancer && ssh-copy-id db01` 這將提示你輸入密碼，在我們的情況下，我們的密碼是 `vagrant`

我沒有運行所有 VM，只運行 Web 伺服器，所以我發出了 `ssh-copy-id web01 && ssh-copy-id web02`

![](Images/Day65_config7.png)

在運行任何 playbooks 之前，我喜歡確保與我的組有簡單的連接性，所以我運行了 `ansible webservers -m ping` 來測試連接性。

![](Images/Day65_config4.png)

### 我們的第一個「真正的」Ansible Playbook

我們的第一個 Ansible playbook 將配置 Web 伺服器，我們已將這些分組在主機檔案中的 [webservers] 分組下。

在運行 playbook 之前，我們可以確認 web01 和 web02 沒有安裝 apache。下面截圖的頂部顯示了我在 ansible 控制中創建的資料夾和檔案佈局，以運行此 playbook，我們有 `playbook1.yml`，然後在 templates 資料夾中，我們有 `index.html.j2` 和 `ports.conf.j2` 檔案。你可以在儲存庫中上面列出的資料夾中找到這些檔案。

然後我們 SSH 到 web01 檢查是否安裝了 apache？

![](Images/Day65_config8.png)

你可以從上面看到我們在 web01 上沒有安裝 apache，所以我們可以通過運行下面的 playbook 來修復這個。

```Yaml
- hosts: webservers
  become: yes
  vars:
    http_port: 8000
    https_port: 4443
    html_welcome_msg: "Hello 90DaysOfDevOps"
  tasks:
  - name: ensure apache is at the latest version
    apt:
      name: apache2
      state: latest

  - name: write the apache2 ports.conf config file
    template:
      src: templates/ports.conf.j2
      dest: /etc/apache2/ports.conf
    notify:
    - restart apache

  - name: write a basic index.html file
    template:
      src: templates/index.html.j2
      dest: /var/www/html/index.html
    notify:
    - restart apache

  - name: ensure apache is running
    service:
      name: apache2
      state: started

  handlers:
    - name: restart apache
      service:
        name: apache2
        state: restarted
```

分解上面的 playbook：

- `- hosts: webservers` 這表示我們運行此 playbook 的組是一個名為 webservers 的組
- `become: yes` 意味著運行 playbook 的用戶將在遠程系統上成為 root。你將被提示輸入 root 密碼。
- 然後我們有 `vars`，這定義了我們在整個 Web 伺服器中想要的一些環境變數。

接下來，我們開始任務，

- 任務 1 是確保 apache 運行最新版本
- 任務 2 是從 templates 資料夾中找到的源寫入 ports.conf 檔案。
- 任務 3 是創建一個基本的 index.html 檔案
- 任務 4 是確保 apache 正在運行

最後，我們有一個 handlers 部分，[Handlers: Running operations on change](https://docs.ansible.com/ansible/latest/user_guide/playbooks_handlers.html)

「有時你希望任務只在機器上進行更改時運行。例如，如果任務更新該服務的配置，你可能希望重啟服務，但如果配置未更改，則不重啟。Ansible 使用 handlers 來解決此用例。Handlers 是僅在通知時運行的任務。每個 handler 應該有一個全局唯一的名稱。」

在這個階段，你可能會想我們已經部署了 5 個 VM（包括充當 Ansible 控制的 Ubuntu Desktop 機器）其他系統將在本節的其餘部分發揮作用。

### 運行我們的 Playbook

我們現在準備對節點運行 playbook。要運行 playbook，我們可以使用 `ansible-playbook playbook1.yml` 我們已在 playbook 內定義了 playbook 將運行的主機，這將演練我們定義的任務。

當指令完成時，我們會得到一個顯示我們的 plays 和 tasks 的輸出，這可能需要一些時間，你可以從下面的圖像中看到，這需要一段時間來安裝我們的所需狀態。

![](Images/Day65_config9.png)

然後我們可以通過跳入節點並檢查節點上已安裝的軟體來再次檢查這一點。

![](Images/Day65_config10.png)

只是為了完成這個，因為我們已經用上面的內容部署了兩個獨立的 Web 伺服器，我們現在可以導航到我們定義的相應 IP 並獲得我們的新網站。

![](Images/Day65_config11.png)

我們將在本節的其餘部分中在此 playbook 的基礎上構建。我也對採用 Ubuntu 桌面並查看是否可以使用 Ansible 引導應用程式和配置感興趣，所以我們也可能觸及這一點。你看到我們可以在指令中使用本地主機，我們也可以對本地主機運行 playbooks，例如。

這裡要添加的另一件事是，我們實際上只使用 Ubuntu VM，但 Ansible 與目標系統無關。我們之前提到的管理系統的替代方案可能是逐個伺服器（當你超過大量伺服器時不可擴展，即使有 3 個節點也很痛苦）我們也可以使用 shell 腳本，我們在 Linux 部分再次涵蓋了這一點，但這些節點可能不同，所以是的，可以完成，但然後有人需要維護和管理這些腳本。Ansible 是免費的，並且擊中了簡單按鈕，而不是必須擁有專門的腳本。

## 資源

- [What is Ansible](https://www.youtube.com/watch?v=1id6ERvfozo)
- [Ansible 101 - Episode 1 - Introduction to Ansible](https://www.youtube.com/watch?v=goclfp6a2IQ)
- [NetworkChuck - You need to learn Ansible right now!](https://www.youtube.com/watch?v=5hycyr-8EKs&t=955s)
- [Your complete guide to Ansible](https://www.youtube.com/playlist?list=PLnFWJCugpwfzTlIJ-JtuATD2MBBD7_m3u)

上面列出的最終播放列表是本節中很多代碼和想法的來源，這是一個很好的資源和視頻格式的演練。

我們[第 66 天](day66.md)見