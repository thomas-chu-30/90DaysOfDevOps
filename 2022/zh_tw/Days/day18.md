---
title: '#90DaysOfDevOps - SSH & Web Server - Day 18'
published: false
description: 90DaysOfDevOps - SSH & Web Server
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048733
---

## SSH 和 Web 服務器

正如我們在整個過程中提到的，你很可能會管理許多遠程 Linux 服務器，因此，你需要確保與這些遠程服務器的連接是安全的。在本節中，我們想涵蓋每個人都應該知道的 SSH 基礎知識，這將幫助你建立到遠程系統的安全隧道。

- 使用 SSH 設置連接
- 傳輸文件
- 創建你的私鑰

### SSH 介紹

- 安全 shell
- 網絡協議
- 允許安全通信
- 可以保護任何網絡服務
- 通常用於遠程命令行訪問

在我們的環境中，如果你一直在跟隨，我們已經在使用 SSH，但這都是通過我們的 vagrant 配置配置和自動化的，所以我們只需要運行 `vagrant ssh` 就可以訪問我們的遠程虛擬機。

如果我們的遠程機器不在與我們的工作站相同的系統上，而是在遠程位置，也許是基於雲的系統或在我們只能通過互聯網訪問的數據中心中運行，我們需要一種安全的方式來訪問系統以管理它。

SSH 在客戶端和服務器之間提供安全隧道，這樣壞人就不能攔截任何東西。

![](Images/Day18_Linux1.png)

服務器有一個服務器端 SSH 服務始終運行並監聽特定 TCP 端口（22）。

如果我們使用客戶端以正確的憑據或 SSH 密鑰連接，那麼我們就可以訪問該服務器。

### 向我們的系統添加橋接網絡適配器

為了在當前的 virtual box VM 中使用它，我們需要向機器添加橋接網絡適配器。

關閉虛擬機，在 Virtual Box 中右鍵單擊你的機器並選擇設置。在新窗口中，然後選擇網絡。

![](Images/Day18_Linux2.png)

現在重新啟動機器，你現在將在本地機器上有一個 IP 地址。你可以使用 `IP addr` 命令確認這一點。

### 確認 SSH 服務器正在運行

我們知道 SSH 已經在我們的機器上配置，因為我們一直在使用 vagrant 使用它，但我們可以通過運行來確認

`sudo systemctl status ssh`

![](Images/Day18_Linux3.png)

如果你的系統沒有 SSH 服務器，那麼你可以通過發出此命令來安裝它 `sudo apt install OpenSSH-server`

然後你想確保如果防火牆正在運行，我們的 SSH 是允許的。我們可以使用 `sudo ufw allow ssh` 來做到這一點，這在我們的配置中不是必需的，因為我們通過 vagrant 配置自動化了這一點。

### 遠程訪問 - SSH 密碼

現在我們的 SSH 服務器正在監聽端口 22 以接收任何傳入的連接請求，並且我們已經添加了橋接網絡，我們可以使用 putty 或本地機器上的 SSH 客戶端通過 SSH 連接到我們的系統。

[# PuTTy installation Guide](https://www.cuit.columbia.edu/putty)。

![](Images/Day18_Linux4.png)

然後點擊打開，如果這是你第一次通過此 IP 地址連接到此系統，你將收到此警告。我們知道這是我們的系統，所以你可以選擇是。

![](Images/Day18_Linux5.png)

然後我們被提示輸入用戶名（vagrant）和密碼（默認密碼 - vagrant）下面你會看到我們現在使用我們的 SSH 客戶端（Putty）使用用戶名和密碼連接到我們的機器。

![](Images/Day18_Linux6.png)

在這個階段，我們從遠程客戶端連接到我們的 VM，我們可以在系統上發出命令。

### 遠程訪問 - SSH 密鑰

上面是訪問系統的簡單方法，但它仍然依賴於用戶名和密碼，如果某些惡意行為者獲得此信息以及系統的公共地址或 IP，那麼它可能很容易受到損害。這就是 SSH 密鑰的首選之處。

SSH 密鑰意味著我們提供密鑰對，以便客戶端和服務器都知道這是受信任的設備。

創建密鑰很容易。在我們的本地機器（Windows）上，我們可以發出以下命令，實際上如果你在任何系統上安裝了 ssh-client，我相信這個相同的命令會起作用？

`ssh-keygen -t ed25519`

我不會在這裡深入探討 `ed25519` 是什麼以及意味著什麼，但如果你想了解更多關於[密碼學](https://en.wikipedia.org/wiki/EdDSA#Ed25519)的信息，你可以搜索

![](Images/Day18_Linux7.png)

此時，我們創建的 SSH 密鑰存儲在 `C:\Users\micha/.ssh/` 中

但是要將其與我們的 Linux VM 鏈接，我們需要複製密鑰。我們可以通過使用 `ssh-copy-id vagrant@192.168.169.135` 來做到這一點

我使用 Powershell 在 Windows 客戶端上創建我的密鑰，但這裡沒有 `ssh-copy-id` 可用。有一些方法可以在 Windows 上執行此操作，在線搜索會為你找到替代方案，但我只會在 Windows 機器上使用 git bash 進行複製。

![](Images/Day18_Linux8.png)

我們現在可以返回 Powershell 來測試我們的連接現在使用我們的 SSH 密鑰工作，不需要密碼。

`ssh vagrant@192.168.169.135`

![](Images/Day18_Linux9.png)

如果需要，我們可以通過使用密碼短語進一步保護這一點。我們還可以進一步說根本沒有密碼，這意味著只允許通過 SSH 的密鑰對。你可以在以下配置文件中實現這一點。

`sudo nano /etc/ssh/sshd_config`

這裡有一行 `PasswordAuthentication yes`，這將被 `#` 註釋掉，你應該取消註釋並將 yes 更改為 no。然後你需要使用 `sudo systemctl reload sshd` 重新加載 SSH 服務

## 設置 Web 服務器

與我們上面使用 SSH 所做的沒有特別相關，但我想包括這一點，因為這又是另一項你可能覺得有點令人生畏的任務，但它真的不應該。

我們有我們的 Linux 遊樂場 VM，在這個階段，我們想向我們的 VM 添加一個 apache webserver，以便我們可以從它託管一個簡單的網站，為我的家庭網絡提供服務。請注意，此網頁將無法從互聯網訪問，這可以完成，但這裡不會涵蓋。

你可能還會看到這被稱為 LAMP 堆棧。

- **L**inux 操作系統
- **A**pache Web 服務器
- **m**ySQL 數據庫
- **P**HP

### Apache2

Apache2 是一個開源 HTTP 服務器。我們可以使用以下命令安裝 apache2。

`sudo apt-get install apache2`

為了確認 apache2 已正確安裝，我們可以運行 `sudo service apache2 restart`

然後使用來自 SSH 演練的橋接網絡地址打開瀏覽器並轉到該地址。我的是 `http://192.168.169.135/`

![](Images/Day18_Linux10.png)

### mySQL

MySQL 是一個數據庫，我們將在其中存儲簡單網站的數據。要安裝 MySQL，我們應該使用以下命令 `sudo apt-get install mysql-server`

### PHP

PHP 是一種服務器端腳本語言，我們將使用它與 MySQL 數據庫交互。最後的安裝是使用 `sudo apt-get install php libapache2-mod-php php-mysql` 安裝 PHP 和依賴項

我們想要進行的第一個配置更改是開箱即用的 apache 使用 index.html，我們希望它使用 index.php。

我們將使用 `sudo nano /etc/apache2/mods-enabled/dir.conf`，我們將把 index.php 移動到列表中的第一項。

![](Images/Day18_Linux11.png)

重啟 apache2 服務 `sudo systemctl restart apache2`

現在讓我們確認我們的系統已正確配置 PHP。使用此命令創建以下文件，這將在 nano 中打開一個空白文件。

`sudo nano /var/www/html/90Days.php`

然後複製以下內容並使用 control + x 退出並保存文件。

```
<?php
phpinfo();
?>
```

現在再次導航到你的 Linux VM IP，在 URL 末尾添加 90Days.php。`http://192.168.169.135/90Days.php` 如果 PHP 配置正確，你應該看到類似下面的內容。

![](Images/Day18_Linux12.png)

### WordPress 安裝

然後我通過本教程在我們的 LAMP 堆棧上啟動 WordPress，如果演練中沒有正確顯示，下面顯示了一些命令 [How to install WordPress on Ubuntu with LAMP](https://blog.ssdnodes.com/blog/how-to-install-wordpress-on-ubuntu-18-04-with-lamp-tutorial/)

`sudo mysql -u root -p`

`CREATE DATABASE wordpressdb;`

`CREATE USER 'admin-user'@'localhost' IDENTIFIED BY 'password';`

`GRANT ALL PRIVILEGES ON wordpressdb.* TO 'admin-user'@'localhost';`

`FLUSH PRIVILEGES;`

`EXIT;`

`sudo apt install php-curl php-gd php-mbstring php-xml php-xmlrpc php-soap php-intl php-zip`

`sudo systemctl restart apache2`

`cd /var/www`

`sudo curl -O https://wordpress.org/latest.tar.gz`

`sudo tar -xvf latest.tar.gz`

`sudo rm latest.tar.gz`

此時你在鏈接文章的第 4 步，你需要按照步驟確保 WordPress 目錄的所有正確權限都已到位。

因為這只是內部的，你不需要在此步驟中"生成安全密鑰"。轉到第 5 步，即將 Apache 配置更改為 WordPress。

然後，如果一切配置正確，你將能夠通過內部網絡地址訪問並運行 WordPress 安裝。

## 資源

- [Client SSH GUI - Remmina](https://remmina.org/)
- [The Beginner's guide to SSH](https://www.youtube.com/watch?v=2QXkrLVsRmk)
- [Vim in 100 Seconds](https://www.youtube.com/watch?v=-txKSRn0qeA)
- [Vim tutorial](https://www.youtube.com/watch?v=IiwGbcd8S7I)
- [Learn the Linux Fundamentals - Part 1](https://www.youtube.com/watch?v=kPylihJRG70)
- [Linux for hackers (don't worry you don't need to be a hacker!)](https://www.youtube.com/watch?v=VbEx7B_PTOE)

我們[Day19](day19.md)見
