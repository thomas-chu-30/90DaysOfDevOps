---
title: '#90DaysOfDevOps - The Big Picture: DevOps and Linux - Day 14'
published: false
description: 90DaysOfDevOps - The Big Picture DevOps and Linux
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049033
---

## 大局：DevOps 和 Linux

Linux 和 DevOps 共享非常相似的文化和觀點；兩者都專注於定制和可擴展性。Linux 的這兩個方面對 DevOps 都特別重要。

很多技術都從 Linux 開始，特別是如果它們與軟體開發或管理基礎設施相關。

同樣，許多開源項目，特別是 DevOps 工具，從一開始就是為在 Linux 上運行而設計的。

從 DevOps 的角度或任何運營角色的角度來看，你會遇到 Linux，我會說主要是這樣。WinOps 有它的位置，但大多數時候你將管理和部署 Linux 服務器。

我已經每天使用 Linux 幾年了，但我一直使用的桌面機器一直是 macOS 或 Windows。然而，當我轉入我現在所在的雲原生角色時，我冒險確保我的筆記本電腦完全基於 Linux 並作為我的日常驅動器，雖然我仍然需要 Windows 用於基於工作的應用程序，而且我的很多音頻和視頻設備不能在 Linux 上運行，但我強迫自己全職運行 Linux 桌面，以便更好地掌握我們將在接下來 7 天中涉及的許多事情。

## 開始

我並不是建議你像我一樣做，因為有更容易且破壞性更小的選項，但我會說，採取全職步驟會迫使你更快地學習如何在 Linux 上使事情工作。

在這 7 天的大部分時間裡，我將在 Windows 機器上的 Virtual Box 中部署一個虛擬機。我還將部署 Linux 發行版的桌面版本，而你將管理的大多數 Linux 服務器可能是沒有 GUI 的服務器，一切都是基於 shell 的。但是，正如我在開始時所說，我們在這 90 天中涵蓋的許多工具都從 Linux 開始，我也強烈鼓勵你為了學習體驗而深入運行 Linux 桌面。

對於這篇文章的其餘部分，我們將專注於在 Virtual Box 環境中啟動並運行 Ubuntu Desktop 虛擬機。現在我們可以只下載 [Virtual Box](https://www.virtualbox.org/) 並從鏈接的站點獲取最新的 [Ubuntu ISO](https://ubuntu.com/download)，然後繼續構建我們的桌面環境，但這對我們來說不會很 DevOps，對吧？

使用大多數 Linux 發行版的另一個好理由是它們是免費和開源的。我們也選擇 Ubuntu，因為它可能是部署最廣泛的發行版，不考慮移動設備和企業 RedHat Enterprise 服務器。我可能在那裡錯了，但考慮到 CentOS 和那裡的歷史，我敢打賭 Ubuntu 在列表中名列前茅，而且超級簡單。

## 介紹 HashiCorp Vagrant

Vagrant 是一個 CLI 實用程序，用於管理虛擬機的生命週期。我們可以使用 vagrant 在許多不同的平台上啟動和關閉虛擬機，包括 vSphere、Hyper-v、Virtual Box 和 Docker。它確實有其他提供者，但我們將在這裡堅持使用 Virtual Box，所以我們可以開始了。

我們需要做的第一件事是在我們的機器上安裝 Vagrant，當你訪問下載頁面時，你會看到列出的所有操作系統供你選擇。[HashiCorp Vagrant](https://www.vagrantup.com/downloads) 我使用的是 Windows，所以我為我的系統獲取了二進制文件並繼續在我的系統上安裝它。

接下來，我們還需要安裝 [Virtual Box](https://www.virtualbox.org/wiki/Downloads)。同樣，這也可以安裝在許多不同的操作系統上，選擇這個和 vagrant 的一個好理由是，如果你運行 Windows、macOS 或 Linux，那麼我們在這裡為你提供支持。

兩個安裝都非常簡單，兩者都有很棒的社群，所以如果你有問題，請隨時聯繫，我也可以嘗試提供幫助。

> 如果你使用的是 m1 macOS，我建議使用 [multiplass](https://multipass.run/) 而不是 Vagrant 和 VirtualBox。（參考：https://github.com/MichaelCade/90DaysOfDevOps/issues/365）

## 我們的第一個 VAGRANTFILE

VAGRANTFILE 描述了我們想要部署的機器類型。它還定義了此機器的配置和供應。

當涉及到保存這些並組織你的 VAGRANTFILE 時，我傾向於將它們放在我工作區的文件夾中。你可以在下面看到這在我的系統上的樣子。希望遵循這一點，你會使用 Vagrant 並看到啟動不同系統的便利性，它也非常適合被稱為 Linux 桌面發行版跳躍的兔子洞。

![](Images/Day14_Linux1.png)

讓我們看一下那個 VAGRANTFILE，看看我們正在構建什麼。

```

Vagrant.configure("2") do |config|

  config.vm.box = "chenhan/ubuntu-desktop-20.04"

  config.vm.provider :virtualbox do |v|

   v.memory  = 8096

   v.cpus    = 4

   v.customize ["modifyvm", :id, "--vram", "128"]

end

end

```

這總體上是一個非常簡單的 VAGRANTFILE。我們說我們想要一個特定的"box"，box 可能是你正在尋找的系統的公共鏡像或私有構建。你可以在 [Vagrant boxes 公共目錄](https://app.vagrantup.com/boxes/search) 中找到一長串公開可用的"boxes"

下一行我們說我們想使用特定的提供者，在這種情況下是 `VirtualBox`。我們還將機器的內存定義為 `8GB`，CPU 數量定義為 `4`。我的經驗告訴我，如果你遇到顯示問題，你可能還想添加以下行。這會將視頻內存設置為你想要的，我會將其提高到 `128MB`，但這取決於你的系統。

```

v.customize ["modifyvm", :id, "--vram", ""]

```

我還將這個特定的 vagrant 文件的副本放在了 [Linux Folder](Linux/VAGRANTFILE) 中

## 供應我們的 Linux 桌面

我們現在準備啟動並運行我們的第一台機器，在我們工作站的終端中。在我的情況下，我在 Windows 機器上使用 PowerShell。導航到你的項目文件夾，你將在其中找到 VAGRANTFILE。到達那裡後，你可以鍵入命令 `vagrant up`，如果一切正常，你會看到類似這樣的內容。

![](Images/Day14_Linux2.png)

這裡要補充的另一件事是網絡將在你的虛擬機上設置為 `NAT`。在這個階段，我們不需要了解 NAT，我計劃在網絡會話中有一個完整的會話來討論它。知道當涉及到在你的家庭網絡上獲取機器時，這是簡單按鈕，它也是 Virtual Box 上的默認網絡模式。你可以在 [Virtual Box 文檔](https://www.virtualbox.org/manual/ch06.html#network_nat) 中找到更多信息

一旦 `vagrant up` 完成，我們現在可以使用 `vagrant ssh` 直接跳轉到我們新 VM 的終端。

![](Images/Day14_Linux3.png)

這是我們在接下來幾天中將進行大部分探索的地方，但我也想深入探討我為你的開發工作站所做的一些定制，當你將其作為日常驅動器運行時，這會使你的生活變得更加簡單，當然，除非你有一個很酷的非標準終端，否則你真的在 DevOps 中嗎？

但只是為了確認在 Virtual Box 中，當你選擇 VM 時，你應該看到登錄提示。

![](Images/Day14_Linux4.png)

哦，如果你走到這一步並且你一直在問"用戶名和密碼是什麼？"

- 用戶名 = vagrant

- 密碼 = vagrant

明天我們將深入了解一些命令及其作用，終端將是使一切發生的地方。

## 資源

- [Learn the Linux Fundamentals - Part 1](https://www.youtube.com/watch?v=kPylihJRG70)
- [Linux for hackers (don't worry you don't need to be a hacker!)](https://www.youtube.com/watch?v=VbEx7B_PTOE)

隨著我們的進行，會有很多資源，就像 Go 資源一樣，我通常會將它們保留為免費內容，以便我們都可以在這裡參與和學習。

正如我提到的，接下來我們將查看我們在 Linux 環境中可能每天使用的命令。

我們[Day15](day15.md)見
