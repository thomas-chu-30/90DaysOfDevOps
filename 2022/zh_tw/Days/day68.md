---
title: '#90DaysOfDevOps - Tags、變數、清單和資料庫伺服器配置 - 第 68 天'
published: false
description: '90DaysOfDevOps - Tags、變數、清單和資料庫伺服器配置'
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048780
---

## Tags、變數、清單和資料庫伺服器配置

### Tags

正如我們在昨天的課程中留下的 playbook，我們需要運行該 playbook 中的每個任務和 play。這意味著我們必須運行 Web 伺服器和負載平衡器 plays 和任務直到完成。

但是，tags 可以讓我們在需要時分離這些。如果我們在環境中有超大和超長的 playbooks，這可能是一個有效的舉措。

在我們的 playbook 檔案中，在這種情況下，我們使用 [ansible-scenario5](Configmgmt/ansible-scenario5/playbook5.yml)

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
  tags: web

- hosts: proxy
  become: yes
  roles:
    - common
    - nginx
  tags: proxy
```

然後我們可以使用 `ansible-playbook playbook5.yml --list-tags` 確認這一點，列表 tags 將概述我們在 playbook 中定義的 tags。

![](Images/Day68_config1.png)

現在如果我們想只針對代理，我們可以通過運行 `ansible-playbook playbook5.yml --tags proxy` 來執行此操作，這將如你在下面看到的，只對代理運行 playbook。

![](Images/Day68_config2.png)

tags 也可以在任務級別添加，所以我們可以在你想要發生什麼和在哪裡發生時獲得細粒度。它可能是以應用程式為重點的 tags，例如，我們可以通過任務並根據安裝、配置或移除來標記任務。你可以使用的另一個非常有用的 tag 是

`tag: always` 這將確保無論你在指令中使用什麼 --tags，如果某些東西標記為 always 值，那麼當你運行 ansible-playbook 指令時，它將始終運行。

使用 tags，我們還可以將多個 tags 捆綁在一起，如果我們選擇運行 `ansible-playbook playbook5.yml --tags proxy,web`，這將運行所有帶有這些 tags 的項目。顯然，在我們的實例中，這將意味著與運行 playbook 相同，但如果我們有多個其他 plays，那麼這將是有意義的。

你也可以定義多個 tag。

### 變數

Ansible 內有兩種類型的變數。

- 用戶創建
- Ansible Facts

### Ansible Facts

每次我們運行 playbooks 時，我們都有一個未定義的任務，稱為「收集事實」，我們可以使用這些變數或事實來使自動化任務發生。

![](Images/Day68_config3.png)

如果我們運行以下 `ansible proxy -m setup` 指令，我們應該看到大量 JSON 格式的輸出。不過，終端機上會有很多資訊要使用，所以我們希望使用 `ansible proxy -m setup >> facts.json` 將其輸出到檔案，你可以在這個儲存庫中看到此檔案，[ansible-scenario5](Configmgmt/ansible-scenario5/facts.json)

![](Images/Day68_config4.png)

如果你打開此檔案，你可以看到我們指令的所有資訊。我們可以獲取 IP 地址、架構和 bios 版本。如果我們想利用這一點並在 playbooks 中使用它，這是有用的資訊。

一個想法是在我們的 nginx 模板 mysite.j2 中潛在地使用這些變數之一，我們在其中硬編碼了 Web 伺服器的 IP 地址。你可以通過在 mysite.j2 中創建 for 循環來執行此操作，這將循環遍歷組 [webservers]，這使我們能夠自動和動態地創建或添加超過 2 個 Web 伺服器到此負載平衡器配置。

```
#Dynamic Config for server {{ ansible_facts['nodename'] }}
    upstream webservers {
  {% for host in groups['webservers'] %}
        server {{ hostvars[host]['ansible_facts']['nodename'] }}:8000;
    {% endfor %}
    }

    server {
        listen 80;

        location / {
                proxy_pass http://webservers;
        }
    }
```

上面的結果看起來與現在相同，但如果我們添加更多 Web 伺服器或移除一個，這將動態更改代理配置。為了使這工作，你需要配置名稱解析。

### 用戶創建

用戶創建的變數是我們自己創建的。如果你查看我們的 playbook，你會看到我們有 `vars:` 然後是我們在那裡使用的 3 個變數列表。

```Yaml
- hosts: webservers
  become: yes
  vars:
    http_port: 8000
    https_port: 4443
    html_welcome_msg: "Hello 90DaysOfDevOps - Welcome to Day 68!"
  roles:
    - common
    - apache2
  tags: web

- hosts: proxy
  become: yes
  roles:
    - common
    - nginx
  tags: proxy
```

但是，我們可以通過將變數移動到它們的檔案來保持 playbook 沒有變數。我們將這樣做，但我們將移動到 [ansible-scenario6](Configmgmt/ansible-scenario6) 資料夾。在該資料夾的根目錄中，我們將創建一個 group_vars 資料夾。然後我們將創建另一個名為 all 的資料夾（所有組都將獲得這些變數）。在那裡，我們將創建一個名為 `common_variables.yml` 的檔案，我們將從 playbook 複製變數到此檔案。從 playbook 中移除它們以及 vars:。

```Yaml
http_port: 8000
https_port: 4443
html_welcome_msg: "Hello 90DaysOfDevOps - Welcome to Day 68!"
```

因為我們將其關聯為全局變數，我們也可以在這裡添加 NTP 和 DNS 伺服器。變數是從我們創建的資料夾結構設置的。你可以在下面看到我們的 Playbook 現在看起來多麼乾淨。

```Yaml
- hosts: webservers
  become: yes
  roles:
    - common
    - apache2
  tags: web

- hosts: proxy
  become: yes
  roles:
    - common
    - nginx
  tags: proxy
```

這些變數之一是 http_port，我們可以在 mysite.j2 中的 for 循環中再次使用它，如下所示：

```J2
#Dynamic Config for server {{ ansible_facts['nodename'] }}
    upstream webservers {
  {% for host in groups['webservers'] %}
        server {{ hostvars[host]['ansible_facts']['nodename'] }}:{{ http_port }};
    {% endfor %}
    }

    server {
        listen 80;

        location / {
                proxy_pass http://webservers;
        }
    }
```

我們還可以在 roles/apache2/templates/index.HTML.j2 檔案中定義 ansible fact，以便我們可以了解我們在哪個 Web 伺服器上。

```J2
<html>

<h1>{{ html_welcome_msg }}! I'm webserver {{ ansible_facts['nodename'] }} </h1>

</html>
```

運行 `ansible-playbook playbook6.yml` 指令與我們的變數更改的結果意味著當我們命中負載平衡器時，你可以看到我們命中了組中的任一 Web 伺服器。

![](Images/Day68_config5.png)

我們還可以添加一個名為 host_vars 的資料夾，並創建 web01.yml，並在每個主機基礎上具有特定消息或更改其外觀（如果我們希望）。

### 清單檔案

到目前為止，我們使用 /etc/ansible 資料夾中的預設 hosts 檔案來確定主機。但是，我們可以為不同環境創建不同的檔案，例如生產和預備。我不會創建更多環境。但我們可以創建主機檔案。

我們可以為不同的伺服器和節點清單創建多個檔案。我們將使用 `ansible-playbook -i dev playbook.yml` 調用這些，你還可以在主機檔案中定義變數，然後打印出來或在 playbooks 的其他地方利用該變數，例如在我正在跟隨的範例和培訓課程中，他們已將在主機檔案中創建的環境變數添加到負載平衡器網頁模板中，以將環境顯示為網頁消息的一部分。

### 部署資料庫伺服器

我們還有一台尚未啟動和配置的機器。我們可以使用 `vagrant up db01` 從 Vagrantfile 所在的位置執行此操作。當它啟動並可訪問時，我們需要確保使用 `ssh-copy-id db01` 複製 SSH 密鑰，以便我們可以訪問它。

我們將從 [ansible-scenario7](Configmgmt/ansible-scenario7) 資料夾工作

然後讓我們使用 `ansible-galaxy init roles/mysql` 為名為「MySQL」的新 role 創建資料夾結構。

在我們的 playbook 中，我們將為資料庫配置添加新的 play 塊。我們在 /etc/ansible/hosts 檔案中定義了組資料庫。然後我們指示資料庫組具有 role common 和名為 MySQL 的新 role，我們在上一步中創建了它。我們還用資料庫標記資料庫組，這意味著正如我們之前討論的那樣，如果我們希望，我們可以選擇只針對這些 tags 運行。

```Yaml
- hosts: webservers
  become: yes
  roles:
    - common
    - apache2
  tags:
    web

- hosts: proxy
  become: yes
  roles:
    - common
    - nginx
  tags:
    proxy

- hosts: database
  become: yes
  roles:
    - common
    - mysql
  tags: database
```

在我們的 roles 資料夾結構中，你現在將自動創建樹，我們需要填充以下內容：

Handlers - main.yml

```Yaml
# handlers file for roles/mysql
- name: restart mysql
  service:
    name: mysql
    state: restarted
```

Tasks - install_mysql.yml、main.yml 和 setup_mysql.yml

install_mysql.yml - 此任務將在那裡安裝 MySQL 並確保服務正在運行。

```Yaml
- name: "Install Common packages"
  apt: name={{ item }} state=latest
  with_items:
   - python3-pip
   - mysql-client
   - python3-mysqldb
   - libmysqlclient-dev

- name: Ensure mysql-server is installed latest version
  apt: name=mysql-server state=latest

- name: Installing python module MySQL-python
  pip:
    name: PyMySQL

- name: Ensure mysql-server is running
  service:
    name: mysql
    state: started
```

main.yml 是一個指針檔案，將建議我們從這些檔案中 import_tasks。

```Yaml
# tasks file for roles/mysql
- import_tasks: install_mysql.yml
- import_tasks: setup_mysql.yml
```

setup_mysql.yml - 此任務將創建我們的資料庫和資料庫用戶。

```Yaml
- name: Create my.cnf configuration file
  template: src=templates/my.cnf.j2 dest=/etc/mysql/conf.d/mysql.cnf
  notify: restart mysql

- name: Create database user with name 'devops' and password 'DevOps90' with all database privileges
  community.mysql.mysql_user:
    login_unix_socket: /var/run/mysqld/mysqld.sock
    login_user: "{{ mysql_user_name }}"
    login_password: "{{ mysql_user_password }}"
    name: "{{db_user}}"
    password: "{{db_pass}}"
    priv: '*.*:ALL'
    host: '%'
    state: present

- name: Create a new database with name '90daysofdevops'
  mysql_db:
    login_user: "{{ mysql_user_name }}"
    login_password: "{{ mysql_user_password }}"
    name: "{{ db_name }}"
    state: present
```

你可以從上面看到我們使用一些變數來確定一些配置，例如密碼、用戶名和資料庫，這些都存儲在我們的 group_vars/all/common_variables.yml 檔案中。

```Yaml
http_port: 8000
https_port: 4443
html_welcome_msg: "Hello 90DaysOfDevOps - Welcome to Day 68!"

mysql_user_name: root
mysql_user_password: "vagrant"
db_user: devops
db_pass: DevOps90
db_name: 90DaysOfDevOps
```

我們在 templates 資料夾中還有 my.cnf.j2 檔案，如下所示：

```J2
[mysql]
bind-address = 0.0.0.0
```

### 運行 playbook

現在我們已經啟動並運行了 VM，並且配置檔案已就位，我們現在準備運行 playbook，如果我們運行以下 `ansible-playbook playbook7.yml`，這將包括我們之前所做的一切，或者我們可以選擇只部署到資料庫組，使用 `ansible-playbook playbook7.yml --tags database` 指令，這將只運行我們的新配置檔案。

我只針對資料庫 tag 運行，但我遇到了一個錯誤。此錯誤告訴我們沒有安裝 pip3（Python）。我們可以通過將其添加到我們的 common tasks 並安裝來修復這個問題

![](Images/Day68_config6.png)

我們修復了上面的問題並再次運行 playbook，我們有一個成功的更改。

![](Images/Day68_config7.png)

我們可能應該確保新配置的 db01 伺服器上的一切都是我們想要的。我們可以從控制節點使用 `ssh db01` 指令執行此操作。

要連接到 MySQL，我使用了 `sudo /usr/bin/mysql -u root -p` 並在提示符處給出了 root 的 vagrant 密碼。

當我們連接時，讓我們首先確保我們創建了名為 DevOps 的用戶。`select user, host from mysql.user;`

![](Images/Day68_config8.png)

現在我們可以發出 `SHOW DATABASES;` 指令來查看我們也創建的新資料庫。

![](Images/Day68_config9.png)

我使用 root 連接，但我們現在也可以使用 DevOps 帳戶登入，同樣使用 `sudo /usr/bin/MySQL -u devops -p`，但這裡的密碼是 DevOps90。

我發現的一件事是在我們的 `setup_mysql.yml` 中，我必須添加行 `login_unix_socket: /var/run/mysqld/mysqld.sock` 才能成功連接到 db01 MySQL 實例，現在每次我運行這個時，它在創建用戶時報告更改，任何建議將不勝感激。

## 資源

- [What is Ansible](https://www.youtube.com/watch?v=1id6ERvfozo)
- [Ansible 101 - Episode 1 - Introduction to Ansible](https://www.youtube.com/watch?v=goclfp6a2IQ)
- [NetworkChuck - You need to learn Ansible right now!](https://www.youtube.com/watch?v=5hycyr-8EKs&t=955s)
- [Your complete guide to Ansible](https://www.youtube.com/playlist?list=PLnFWJCugpwfzTlIJ-JtuATD2MBBD7_m3u)

上面列出的最終播放列表是本節中很多代碼和想法的來源，這是一個很好的資源和視頻格式的演練。

我們[第 69 天](day69.md)見