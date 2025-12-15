---
title: '#90DaysOfDevOps - 安裝與配置 Git - 第 36 天'
published: false
description: 90DaysOfDevOps - 安裝與配置 Git
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048738
---

## 安裝與配置 Git

Git 是一個開源的跨平台版本控制工具。如果你像我一樣使用 Ubuntu 或大多數 Linux 環境，你可能會發現已經安裝了 git，但我們還是要進行安裝和配置的步驟。

即使你的系統上已經安裝了 git，確保我們使用的是最新版本也是個好主意。

### 安裝 Git

正如前面提到的，Git 是跨平台的，我們將介紹 Windows 和 Linux 的安裝，但你也可以在[這裡](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)找到 macOS 的安裝說明。

對於 [Windows](https://git-scm.com/download/win)，我們可以從官方網站下載安裝程式。

你也可以在 Windows 機器上使用 `winget`，把它想成你的 Windows 應用程式套件管理工具。

在安裝任何東西之前，讓我們先看看 Windows 機器上的版本。打開 PowerShell 視窗並執行 `git --version`

![](Images/Day36_Git1.png)

我們也可以檢查 WSL Ubuntu 版本的 Git。

![](Images/Day36_Git2.png)

在撰寫本文時，最新的 Windows 版本是 `2.35.1`，所以我們需要進行一些更新，我將進行說明。我預期 Linux 也是如此。

我下載了最新的安裝程式並執行安裝精靈，將在此記錄。重要的是，git 會在安裝最新版本之前先卸載舊版本。

這意味著下面顯示的過程在很大程度上與從零開始安裝 git 相同。

這是一個非常簡單的安裝過程。下載後雙擊並開始。閱讀 GNU 授權協議。但請記住這是免費和開源的軟體。

![](Images/Day36_Git3.png)

現在我們可以選擇想要安裝的額外元件，並與 git 建立關聯。在 Windows 上，我總是確保安裝 Git Bash，因為這允許我們在 Windows 上執行 bash 腳本。

![](Images/Day36_Git4.png)

然後我們可以選擇要使用的 SSH 執行檔。我保留使用內建的 OpenSSH，你可能在 Linux 部分看到過。

![](Images/Day36_Git5.png)

然後我們有一些實驗性功能可以選擇啟用，對我來說我不需要它們，所以我不啟用它們，你可以隨時透過安裝程式回來啟用這些功能。

![](Images/Day36_Git6.png)

安裝完成，我們現在可以選擇打開 Git Bash 和/或最新的發布說明。

![](Images/Day36_Git7.png)

最後檢查是在我們的 PowerShell 視窗中查看我們現在擁有的 git 版本。

![](Images/Day36_Git8.png)

非常簡單，現在我們已經是最新版本了。在我們的 Linux 機器上，我們似乎有點落後，所以我們也可以進行更新過程。

我只需執行 `sudo apt-get install git` 指令。

![](Images/Day36_Git9.png)

你也可以執行以下命令，這將添加 git 儲存庫以進行軟體安裝。

```
sudo add-apt-repository ppa:git-core/ppa -y
sudo apt-get update
sudo apt-get install git -y
git --version
```

### 配置 Git

當我們第一次使用 git 時，我們必須定義一些設定，

- 名稱
- 電子郵件
- 預設編輯器
- 行尾符號

這可以在三個層級完成

- System = 所有用戶
- Global = 當前用戶的所有儲存庫
- Local = 當前儲存庫

範例：
`git config --global user.name "Michael Cade"`
`git config --global user.email Michael.Cade@90DaysOfDevOPs.com"`
根據你的作業系統將決定預設文字編輯器。在我的 Ubuntu 機器上，如果不設定下一個命令，預設使用 nano。下面的命令將此更改為 Visual Studio Code。

`git config --global core.editor "code --wait"`

現在如果我們想查看所有 git 配置，那麼我們可以使用以下命令。

`git config --global -e`

![](Images/Day36_Git10.png)

在任何機器上，這個檔案都會被命名為 `.gitconfig`，在我的 Windows 機器上，你會在用戶帳戶目錄中找到它。

![](Images/Day36_Git11.png)

### Git 理論

我在昨天的文章中提到了有其他版本控制類型，我們可以將它們分為兩種類型。一種是客戶端-伺服器（Client-Server），另一種是分布式（Distributed）。

### 客戶端-伺服器版本控制

在 git 出現之前，客戶端-伺服器是版本控制的標準方法。一個例子是 [Apache Subversion](https://subversion.apache.org/)，這是一個成立於 2000 年的開源版本控制系統。

在這種客戶端-伺服器版本控制模型中，第一步是開發人員從伺服器下載原始碼和實際檔案。這不會消除衝突，但確實消除了衝突的複雜性以及如何解決它們。

![](Images/Day36_Git12.png)

現在舉例來說，假設我們有兩個開發人員處理相同的檔案，其中一個贏得競賽並首先提交或上傳他們的檔案到伺服器以及他們的新更改。當第二個開發人員去更新時，他們會遇到衝突。

![](Images/Day36_Git13.png)

所以現在開發人員需要拉取第一個開發人員的程式碼更改，然後在解決衝突後再提交。

![](Images/Day36_Git15.png)

### 分布式版本控制

Git 不是唯一的分布式版本控制系統。但它是事實上的標準。

Git 的一些主要優勢是：

- 快速
- 智能
- 靈活
- 安全

與客戶端-伺服器版本控制模型不同，每個開發人員都下載整個源儲存庫，意味著一切。提交歷史、所有分支等等。

![](Images/Day36_Git16.png)

## 資源

- [What is Version Control?](https://www.youtube.com/watch?v=Yc8sCSeMhi4)
- [Types of Version Control System](https://www.youtube.com/watch?v=kr62e_n6QuQ)
- [Git Tutorial for Beginners](https://www.youtube.com/watch?v=8JJ101D3knE&t=52s)
- [Git for Professionals Tutorial](https://www.youtube.com/watch?v=Uszj_k0DGsg)
- [Git and GitHub for Beginners - Crash Course](https://www.youtube.com/watch?v=RGOj5yH7evk&t=8s)
- [Complete Git and GitHub Tutorial](https://www.youtube.com/watch?v=apGV9Kg7ics)

我們[第 37 天](day37.md)見