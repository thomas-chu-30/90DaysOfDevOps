---
title: '#90DaysOfDevOps - Dev workstation setup - All the pretty things - Day 20'
published: false
description: 90DaysOfDevOps - Dev workstation setup - All the pretty things
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048734
---

## 開發工作站設置 - 所有漂亮的東西

不要與我們以這種方式設置 Linux 服務器混淆，但我也想展示我們在 Linux 桌面中擁有的選擇和靈活性。

我已經使用 Linux 桌面將近一年了，我已經按照我想要的方式從外觀和感覺的角度配置了它。使用我們在 Virtual Box 上的 Ubuntu VM，我們可以運行我對日常驅動器進行的一些定制。

我已經整理了一個 YouTube 視頻來完成其餘部分，因為有些人可能能夠更好地跟隨：

[![Click to access YouTube Video](Images/Day20_YouTube.png)](https://youtu.be/jeEslAtHfKc)

開箱即用，我們的系統看起來像下面這樣：

![](Images/Day20_Linux1.png)

我們還可以在下面看到我們的默認 bash shell，

![](Images/Day20_Linux2.png)

這很多都歸結為 dotfiles，我們將在本系列的最後一個 Linux 會話中涵蓋這一點。

### dotfiles

首先，我想深入研究 dotfiles，我在之前的一天說過，Linux 由配置文件組成。這些 dotfiles 是 Linux 系統和應用程序的配置文件。

我還想補充一點，dotfiles 不僅用於定制和使桌面看起來漂亮，還有一些 dotfile 更改和配置可以幫助你提高生產力。

正如我提到的，許多軟體程序將它們的配置存儲在這些 dotfiles 中。這些 dotfiles 有助於管理功能。

每個 dotfile 都以 `.` 開頭。你可能可以猜測命名來自哪裡？

到目前為止，我們一直在使用 bash 作為我們的 shell，這意味著你將在主文件夾中有一個 .bashrc 和 .bash_profile。你可以在下面看到我們系統上的一些 dotfiles。

![](Images/Day20_Linux3.png)

我們將更改我們的 shell，所以我們稍後會看到一個新的 `.zshrc` 配置 dotfile。

但現在你知道如果我們提到 dotfiles，你知道它們是配置文件。我們可以使用它們向命令提示符添加別名以及不同位置的路徑。有些人發布他們的 dotfiles，以便它們公開可用。你可以在我的 GitHub [MichaelCade/dotfiles](https://github.com/MichaelCade/dotfiles) 上找到我的，在這裡你會找到我的自定義 `.zshrc` 文件，我選擇的終端是 terminator，它在文件夾中也有一些配置文件，然後還有一些背景選項。

### ZSH

正如我在整個互動中提到的，到目前為止我們一直在使用 bash shell，這是 Ubuntu 的默認 shell。ZSH 非常相似，但它確實比 bash 有一些好處。

Zsh 具有交互式 Tab 完成、自動文件搜索、正則表達式集成、定義命令範圍的高級簡寫以及豐富的主題引擎等功能。

我們可以使用 `apt` 包管理器在系統上安裝 zsh。讓我們繼續從 bash 終端運行 `sudo apt install zsh`。我將從 VM 控制台內執行此操作，而不是通過 SSH 連接。

當安裝命令完成時，你可以在終端內運行 `zsh`，這將啟動 shell 配置腳本。

![](Images/Day20_Linux4.png)

我選擇了 `1` 來回答上述問題，現在我們有更多選項。

![](Images/Day20_Linux5.png)

你可以從此菜單中看到，我們可以進行一些開箱即用的編輯，以使 ZSH 配置為我們的需求。

如果你用 `0` 退出嚮導，然後使用 `ls -al | grep .zshrc`，你應該看到我們有一個新的配置文件。

現在我們想使 zsh 成為每次打開終端時的默認 shell，我們可以通過運行以下命令來更改 shell `chsh -s $(which zsh)`，然後我們需要註銷並重新登錄以使更改生效。

當你重新登錄並打開終端時，它應該看起來像這樣。我們還可以通過運行 `which $SHELL` 確認我們的 shell 現在已更改。

![](Images/Day20_Linux6.png)

我通常在每個我啟動的 Ubuntu 桌面上執行此步驟，並且總體上發現，不進一步，zsh shell 比 bash 快一點。

### OhMyZSH

接下來，我們想讓事情看起來更好一點，並添加一些功能來幫助我們在終端內移動。

OhMyZSH 是一個免費開源的框架，用於管理你的 zsh 配置。有很多插件、主題和其他東西，只是讓與 zsh shell 的交互變得更加愉快。

你可以了解更多關於 [ohmyzsh](https://ohmyz.sh/) 的信息

讓我們安裝 Oh My ZSH，我們有幾個選項，`curl` `wget` 或 `fetch`，我們在系統上有前兩個可用，但我將使用 `curl` 引導

`sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`

當你運行上述命令時，你應該看到類似下面的輸出。

![](Images/Day20_Linux7.png)

現在我們可以繼續開始為我們的體驗放置主題，Oh My ZSH 附帶了 100 多個，但我所有應用程序和所有內容的首選是 Dracula 主題。

我還想補充一點，在使用 Oh My ZSH 時，這兩個插件是必須的。

`git clone https://github.com/zsh-users/zsh-autosuggestions.git $ZSH_CUSTOM/plugins/zsh-autosuggestions`

`git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting`

`nano ~/.zshrc`

編輯插件以現在包括 `plugins=(git zsh-autosuggestions zsh-syntax-highlighting)`

## Gnome 擴展

我還使用 Gnome 擴展，特別是下面的列表

[Gnome extensions](https://extensions.gnome.org)

    - Caffeine
    - CPU Power Manager
    - Dash to Dock
    - Desktop Icons
    - User Themes

## 軟體安裝

我使用 `apt` 在機器上安裝的程序簡短列表

    - VSCode
    - azure-cli
    - containerd.io
    - docker
    - docker-ce
    - google-cloud-sdk
    - insomnia
    - packer
    - terminator
    - terraform
    - vagrant

### Dracula 主題

這個網站是我目前唯一使用的主題。看起來清晰、乾淨，一切看起來都很棒。[Dracula Theme](https://draculatheme.com/) 當你在機器上使用許多其他程序時，它也會為你提供支持。

從上面的鏈接，我們可以在網站上搜索 zsh，你會找到至少兩個選項。

按照列出的說明手動安裝或使用 git。然後你最終需要按照以下方式編輯 `.zshrc` 配置文件。

![](Images/Day20_Linux8.png)

接下來你想要 [Gnome Terminal Dracula theme](https://draculatheme.com/gnome-terminal)，這裡也有所有說明。

對我來說，記錄每一步需要很長時間，所以我創建了一個視頻演練過程。（**點擊下面的圖像**）

[![](Images/Day20_YouTube.png)](https://youtu.be/jeEslAtHfKc)

如果你走到這一步，那麼我們現在已經完成了 #90DaysOfDevOps 的 Linux 部分。再次，我對這裡的反饋和資源添加持開放態度。

我也認為通過視頻向你展示很多步驟比在這裡寫下來更容易，你對此有何看法？我確實有一個目標，就是回顧這些日子，並在可能的情況下創建視頻演練來添加，並更好地解釋和展示我們涵蓋的一些內容。你怎麼看？

## 資源

- [Bash in 100 seconds](https://www.youtube.com/watch?v=I4EWvMFj37g)
- [Bash script with practical examples - Full Course](https://www.youtube.com/watch?v=TPRSJbtfK4M)
- [Client SSH GUI - Remmina](https://remmina.org/)
- [The Beginner's guide to SSH](https://www.youtube.com/watch?v=2QXkrLVsRmk)
- [Vim in 100 Seconds](https://www.youtube.com/watch?v=-txKSRn0qeA)
- [Vim tutorial](https://www.youtube.com/watch?v=IiwGbcd8S7I)
- [Learn the Linux Fundamentals - Part 1](https://www.youtube.com/watch?v=kPylihJRG70)
- [Linux for hackers (don't worry you don't need to be a hacker!)](https://www.youtube.com/watch?v=VbEx7B_PTOE)

明天我們開始 7 天的網絡深入探討，我們將尋求為自己提供圍繞 DevOps 的網絡基礎知識和理解。

我們[Day21](day21.md)見
