---
title: '#90DaysOfDevOps - Managing your Linux System, Filesystem & Storage - Day 16'
published: false
description: '90DaysOfDevOps - Managing your Linux System, Filesystem & Storage'
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048702
---

## 管理你的 Linux 系統、文件系統和存儲

到目前為止，我們已經簡要概述了 Linux 和 DevOps，然後使用 Vagrant [(Day 14)](day14.md) 設置了我們的實驗環境，然後我們觸及了當你在終端中完成工作時將在你的日常工具包中的一小部分命令 [(Day 15)](day15.md)。

在這裡，我們將研究維護 Linux 系統的三個關鍵領域：更新、安裝軟體、了解系統文件夾的用途，我們還將查看存儲。

## 管理 Ubuntu 和軟體

我們要查看的第一件事是如何更新我們的操作系統。你們中的大多數人會熟悉 Windows OS 和 macOS 中的這個過程，這在 Linux 桌面和服務器上看起來略有不同。

我們將查看 apt 包管理器，這是我們將在 Ubuntu VM 上用於更新和軟體安裝的工具。

一般來說，至少在開發工作站上，我在任何軟體安裝之前運行此命令，以確保我擁有來自中央存儲庫的最新可用更新。

`sudo apt-get update`

![](Images/Day16_Linux1.png)

現在我們有一個更新的 Ubuntu VM，並安裝了最新的 OS 更新。我們現在想在此處安裝一些軟體。

讓我們選擇 `figlet`，這是一個生成文本橫幅的程序。

如果我們在終端中鍵入 `figlet`，你會看到我們在系統上沒有安裝它。

![](Images/Day16_Linux2.png)

不過，你會從上面看到它確實為我們提供了一些可以嘗試的 `apt` 安裝選項。這是因為在默認存儲庫中有一個名為 figlet 的程序。讓我們嘗試 `sudo apt install figlet`

![](Images/Day16_Linux3.png)

我們現在可以使用我們的 `figlet` 應用程序，如下所示。

![](Images/Day16_Linux4.png)

如果我們想刪除它或任何軟體安裝，我們也可以通過 `apt` 包管理器來做到這一點。

`sudo apt remove figlet`

![](Images/Day16_Linux5.png)

還有第三方存儲庫，我們也可以添加到我們的系統中，我們開箱即用的是 Ubuntu 默認存儲庫。

例如，如果我們想在 Ubuntu VM 上安裝 vagrant，我們現在無法做到，你可以在下面看到第一個命令。然後我們添加密鑰以信任 HashiCorp 存儲庫，然後將存儲庫添加到我們的系統。

![](Images/Day16_Linux6.png)

一旦我們添加了 HashiCorp 存儲庫，我們就可以繼續運行 `sudo apt install vagrant` 並在我們的系統上安裝 vagrant。

![](Images/Day16_Linux7.png)

在軟體安裝方面有很多選項，包管理器的不同選項，內置於 Ubuntu 中，我們也可以使用 snaps 進行軟體安裝。

希望這能讓你了解如何在 Linux 上管理 OS 和軟體安裝。

## 文件系統說明

Linux 由配置文件組成，如果你想更改任何內容，那麼你更改這些配置文件。

在 Windows 上，你有 C: 驅動器，這就是我們認為的根。在 Linux 上，我們有 `/`，這是我們將在 Linux 系統上找到重要文件夾的地方。

![](Images/Day16_Linux8.png)

- `/bin` - binary 的簡稱，bin 文件夾是系統需要的二進制文件所在的位置，可執行文件和工具大多會在這裡找到。

![](Images/Day16_Linux9.png)

- `/boot` - 系統啟動所需的所有文件。如何啟動，以及從哪個驅動器啟動。

![](Images/Day16_Linux10.png)

- `/dev` - 你可以在這裡找到設備信息，這是你可以找到磁盤驅動器指針的地方，`sda` 將是你的主 OS 磁盤。

![](Images/Day16_Linux11.png)

- `/etc` 可能是 Linux 系統上最重要的文件夾，這是大多數配置文件所在的位置。

![](Images/Day16_Linux12.png)

- `/home` - 這是你可以找到用戶文件夾和文件的地方。我們有我們的 vagrant 用戶文件夾。這是你可以找到 `Documents` 和 `Desktop` 文件夾的地方，我們在命令部分中使用了這些文件夾。

![](Images/Day16_Linux13.png)

- `/lib` - 我們提到 `/bin` 是二進制文件和可執行文件所在的位置，而 `/lib` 是你將找到這些共享庫的位置。

![](Images/Day16_Linux14.png)

- `/media` - 這是我們將找到可移動設備的地方。

![](Images/Day16_Linux15.png)

- `/mnt` - 這是一個臨時掛載點。我們將在下一個存儲部分中更詳細地介紹這一點。

![](Images/Day16_Linux16.png)

- `/opt` - 可選軟體包。你會注意到這裡我們有一些 vagrant 和 virtual box 軟體存儲在這裡。

![](Images/Day16_Linux17.png)

- `/proc` - 內核和進程信息，類似於 `/dev`

![](Images/Day16_Linux18.png)

- `/root` - 要獲得訪問權限，你需要 sudo 進入此文件夾。root 的主文件夾。

![](Images/Day16_Linux19.png)

- `/run` - 應用程序狀態的佔位符。

![](Images/Day16_Linux20.png)

- `/sbin` - Sudo bin，類似於 bin 文件夾，但這些工具旨在用於系統上的提升超級用戶權限。

![](Images/Day16_Linux21.png)

- `/tmp` - 臨時文件。

![](Images/Day16_Linux22.png)

- `/usr` - 如果我們作為標準用戶安裝了軟體包，它通常會安裝在 `/usr/bin` 位置。

![](Images/Day16_Linux23.png)

- `/var` - 我們的應用程序安裝在 `bin` 文件夾中。我們需要某個地方來存儲所有日誌文件，這就是 `/var`

![](Images/Day16_Linux24.png)

## 存儲

當我們來到 Linux 系統或任何系統時，我們可能想知道可用的磁盤以及這些磁盤上有多少可用空間。接下來的幾個命令將幫助我們識別、使用和管理存儲。

- `lsblk` 列出塊設備。`sda` 是我們的物理磁盤，然後 `sda1, sda2, sda3` 是該磁盤上的分區。

![](Images/Day16_Linux25.png)

- `df` 為我們提供有關這些分區的更多詳細信息，總計、已使用和可用。你可以在這裡解析其他標誌，我通常使用 `df -h` 來為我們提供數據的人類可讀輸出。

![](Images/Day16_Linux26.png)

如果你要向系統添加新磁盤，這與 Windows 相同，你需要在磁盤管理中格式化磁盤，在 Linux 終端中，你可以通過使用 `sudo mkfs -t ext4 /dev/sdb` 來做到這一點，其中 sdb 與我們新添加的磁盤相關。

然後我們需要掛載我們新格式化的磁盤，以便它可以使用。我們將在之前提到的 `/mnt` 文件夾中執行此操作，我們將在那裡創建一個目錄 `sudo mkdir NewDisk`，然後我們將使用 `sudo mount /dev/sdb newdisk` 將磁盤掛載到該位置。

還可能你需要安全地從系統中卸載存儲，而不是僅僅從配置中拉出它。我們可以使用 `sudo umount /dev/sdb` 來做到這一點

如果你不想卸載該磁盤，並且你將使用此磁盤進行數據庫或其他持久性用例，那麼你希望它在重新啟動系統時存在。為了實現這一點，我們需要將此磁盤添加到我們的 `/etc/fstab` 配置文件中以使其持久化，如果你不這樣做，當機器重新啟動時它將不可用，你必須手動完成上述過程。數據仍將在磁盤上，但除非你將配置添加到此文件，否則它不會自動掛載。

一旦你編輯了 `fstab` 配置文件，你可以使用 `sudo mount -a` 檢查你的工作，如果沒有錯誤，那麼你的更改現在將在重啟後持久化。

我們將在未來的會話中介紹如何使用文本編輯器編輯文件。

## 資源

- [Learn the Linux Fundamentals - Part 1](https://www.youtube.com/watch?v=kPylihJRG70)
- [Linux for hackers (don't worry you don't need to be a hacker!)](https://www.youtube.com/watch?v=VbEx7B_PTOE)

我們[Day17](day17.md)見
