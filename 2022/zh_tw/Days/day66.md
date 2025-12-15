---
title: '#90DaysOfDevOps - Ansible Playbooks 繼續... - 第 66 天'
published: false
description: 90DaysOfDevOps - Ansible Playbooks 繼續...
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048712
---

## Ansible Playbooks（繼續）

在上一節中，我們開始使用 Vagrantfile 創建小型實驗室來部署 4 台機器，我們使用在該節中創建的 Linux 機器作為 ansible 控制系統。

我們還演練了一些 playbooks 場景，最後我們有了一個使 web01 和 web02 成為獨立 Web 伺服器的 playbook。

![](Images/Day66_config1.png)

### 保持整潔

在我們進入進一步的自動化和部署之前，我們應該涵蓋保持 playbook 精簡和整潔的能力，以及如何將任務和處理程序分離到子資料夾中。

我們將把任務複製到資料夾內的檔案中。

```Yaml
- name: ensure apache is at the latest version
  apt: name=apache2 state=latest

- name: write the apache2 ports.conf config file
  template:
    src=templates/ports.conf.j2
    dest=/etc/apache2/ports.conf
  notify: restart apache

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
```

處理程序也是如此。

```Yaml
- name: restart apache
  service:
    name: apache2
    state: restarted
```

然後在我們現在命名為 `playbook2.yml` 的 playbook 中，我們指向這些檔案。所有這些都可以在 [ansible-scenario2](Days/../Configmgmt/ansible-scenario2/) 找到

你可以在控制機器上測試這個。如果你從儲存庫複製了檔案，你應該注意到「write a basic index.html file」中發生了一些變化

![](Images/Day66_config2.png)

讓我們找出我做了什麼簡單的更改。使用 `curl web01:8000`

![](Images/Day66_config3.png)

我們剛剛整理了 playbook，並開始分離可能使 playbook 在大規模時非常令人難以承受的區域。

### Roles 和 Ansible Galaxy

目前我們已經部署了 4 個 VM，我們已將其中 2 個 VM 配置為 Web 伺服器，但我們還有一些更具體的功能，即資料庫伺服器和負載平衡器或代理。為了做到這一點並整理儲存庫，我們可以在 Ansible 中使用 roles。

為此，我們將使用 `ansible-galaxy` 指令，該指令用於管理共享儲存庫中的 ansible roles。

![](Images/Day66_config4.png)

我們將使用 `ansible-galaxy` 為 apache2 創建一個 role，這是我們將放置 Web 伺服器特定內容的地方。

![](Images/Day66_config5.png)

上面的指令 `ansible-galaxy init roles/apache2` 將創建我們上面顯示的資料夾結構。我們的下一步是我們需要將現有任務和模板移動到新結構中的相關資料夾。

![](Images/Day66_config6.png)

複製和粘貼很容易移動這些檔案，但我們還需要對 tasks/main.yml 進行更改，以便我們將其指向 apache2_install.yml。

我們還需要更改 playbook 以引用新 role。在 playbook1.yml 和 playbook2.yml 中，我們以不同方式確定任務和處理程序，因為我們在兩個版本之間更改了這些。我們需要更改 playbook 以使用此 role，如下所示：

```Yaml
- hosts: webservers
  become: yes
  vars:
    http_port: 8000
    https_port: 4443
    html_welcome_msg: "Hello 90DaysOfDevOps - Welcome to Day 66!"
  roles:
    - apache2
```

![](Images/Day66_config7.png)

我們現在可以再次運行 playbook，這次使用新的 playbook 名稱 `ansible-playbook playbook3.yml`，你會注意到棄用，我們可以接下來修復它。

![](Images/Day66_config8.png)

好的，雖然我們的 playbook 運行了，但我們現在應該修復我們的方式，為此，我已將 tasks/main.yml 中的 include 選項更改為現在是 import_tasks，如下所示。

![](Images/Day66_config9.png)

你可以在 [ansible-scenario3](Days/Configmgmt/ansible-scenario3) 找到這些檔案

我們還將在使用 `ansible-galaxy` 時創建更多 roles，我們將創建：

- common = 用於所有伺服器（`ansible-galaxy init roles/common`）
- nginx = 用於負載平衡器（`ansible-galaxy init roles/nginx`）

![](Images/Day66_config10.png)

我將把這個留在這裡，在下一節中，我們將開始處理我們已部署但尚未做任何事情的節點。

## 資源

- [What is Ansible](https://www.youtube.com/watch?v=1id6ERvfozo)
- [Ansible 101 - Episode 1 - Introduction to Ansible](https://www.youtube.com/watch?v=goclfp6a2IQ)
- [NetworkChuck - You need to learn Ansible right now!](https://www.youtube.com/watch?v=5hycyr-8EKs&t=955s)
- [Your complete guide to Ansible](https://www.youtube.com/playlist?list=PLnFWJCugpwfzTlIJ-JtuATD2MBBD7_m3u)

上面列出的最終播放列表是本節中很多代碼和想法的來源，這是一個很好的資源和視頻格式的演練。

我們[第 67 天](day67.md)見