---
title: '#90DaysOfDevOps - Automate tasks with bash scripts - Day 19'
published: false
description: 90DaysOfDevOps - Automate tasks with bash scripts
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048774
---

## 使用 bash 腳本自動化任務

我們今天要使用的 shell 是 bash，但當我們深入探討 ZSH 時，我們明天會介紹另一個 shell。

BASH - **B**ourne **A**gain **Sh**ell

我們幾乎可以將整個 7 天的部分專門用於 shell 腳本，就像程式語言一樣，bash 為我們提供了與其他自動化工具一起工作的能力來完成工作。

我仍然與很多人交談，他們設置了一些複雜的 shell 腳本來使某些事情發生，他們依賴此腳本來處理業務中一些最重要的事情，我不是說我們需要理解 shell/bash 腳本用於此目的，這不是方法。但我們應該學習 shell/bash 腳本，以與我們的自動化工具一起工作並用於臨時任務。

我們在本節中使用的一個例子可能是我們用來創建 VM 的 VAGRANTFILE，我們可以將其包裝到一個簡單的 bash 腳本中，每週一早上刪除並更新它，以便我們每週都有一個新的 Linux VM 副本，我們還可以添加我們在所述 Linux 機器上需要的所有軟體堆棧，等等，所有這些都通過這一個 bash 腳本。

我認為我至少聽到的另一件事是，在所有面試中，動手腳本問題變得越來越明顯。

### 開始

就像我們在這 90 天中涵蓋的許多事情一樣，唯一真正的學習方式是通過實踐。實踐經驗將幫助將所有這些吸收到你的肌肉記憶中。

首先，我們需要一個文本編輯器。在 [Day 17](day17.md) 中，我們涵蓋了可能是兩個最常見的文本編輯器以及如何使用它們的一點。

讓我們直接開始創建我們的第一個 shell 腳本。

`touch 90DaysOfDevOps.sh`

然後是 `nano 90DaysOfDevOps.sh`，這將在 nano 中打開我們的新空白 shell 腳本。同樣，你可以在這裡選擇你選擇的文本編輯器。

所有 bash 腳本的第一行都需要看起來像這樣 `#!/usr/bin/bash`，這是你的 bash 二進制文件的路徑。

但是，你應該通過運行 `which bash` 在終端中檢查這一點，如果你不使用 Ubuntu，那麼你也可以從終端嘗試 `whereis bash`。

但是，你可能會在已創建的 shell 腳本中看到其他列出的路徑，可能包括：

- `#!/bin/bash`
- `#!/usr/bin/env bash`

在我們腳本的下一行中，我喜歡添加註釋並添加腳本的目的或至少一些關於我的信息。你可以通過使用 `#` 來做到這一點，這允許我們在代碼中的特定行上註釋並提供即將到來的命令將做什麼的描述。我發現註釋越多，用戶體驗越好，特別是如果你要分享這個。

我有時使用 figlet，這是我們在 Linux 部分早期安裝的程序，在我們的腳本中創建一些 ascii 藝術來開始。

![](Images/Day19_Linux1.png)

我們在 Linux 部分早期經歷的所有命令（[Day15](day15.md)）都可以在這裡用作測試腳本的簡單命令。

讓我們向腳本添加一個簡單的代碼塊。

```
mkdir 90DaysOfDevOps
cd 90DaysOfDevOps
touch Day19
ls
```

然後你可以保存並退出文本編輯器，如果我們使用 `./90DaysOfDevOps.sh` 運行腳本，你應該收到權限被拒絕的消息。你可以使用 `ls -al` 命令檢查此文件的權限，你可以看到突出顯示我們在此文件上沒有可執行權限。

![](Images/Day19_Linux2.png)

我們可以使用 `chmod +x 90DaysOfDevOps.sh` 更改此設置，然後你會看到 `x` 意味著我們現在可以執行腳本。

![](Images/Day19_Linux3.png)

現在我們可以使用 `./90DaysOfDevOps.sh` 再次運行腳本，運行腳本後，現在已創建新目錄，更改為該目錄，然後創建新文件。

![](Images/Day19_Linux4.png)

非常基本的東西，但你可以開始看到希望這如何用於調用其他工具，作為使你的生活更輕鬆和自動化事情的方法的一部分。

### 變量、條件

本節的很多內容是我們在學習 Golang 時涵蓋的內容的重複，但我認為值得我們在這裡再次深入探討。

- ### 變量

變量使我們能夠定義一次在可能複雜的腳本中使用的特定重複術語。

要添加變量，你只需將其添加到腳本中的乾淨行，如下所示。

`challenge="90DaysOfDevOps"`

這樣，當我們在代碼中使用 `$challenge` 時，如果我們更改變量，它將在整個過程中反映出來。

![](Images/Day19_Linux5.png)

如果我們現在運行 `sh` 腳本，你會看到添加到腳本中的打印輸出。

![](Images/Day19_Linux6.png)

我們還可以要求用戶輸入，可以使用以下方式設置變量：

```
echo "Enter your name"
read name
```

這將把輸入定義為變量 `$name`，然後我們可以在以後使用它。

- ### 條件

也許我們想找出誰在我們的挑戰中以及他們完成了多少天，我們可以使用 `if` `if-else` `else-if` 條件來定義這一點，這是我們在腳本中定義的內容。

```
#!/bin/bash
#  ___   ___  ____                   ___   __ ____              ___
# / _ \ / _ \|  _ \  __ _ _   _ ___ / _ \ / _|  _ \  _____   __/ _ \ _ __  ___
#| (_) | | | | | | |/ _` | | | / __| | | | |_| | | |/ _ \ \ / / | | | '_ \/ __|
# \__, | |_| | |_| | (_| | |_| \__ \ |_| |  _| |_| |  __/\ V /| |_| | |_) \__ \
#   /_/ \___/|____/ \__,_|\__, |___/\___/|_| |____/ \___| \_/  \___/| .__/|___/
#                         |___/                                     |_|
#
# This script is to demonstrate bash scripting!

# Variables to be defined

ChallengeName=#90DaysOfDevOps
TotalDays=90

# User Input

echo "Enter Your Name"
read name
echo "Welcome $name to $ChallengeName"
echo "How Many Days of the $ChallengeName challenge have you completed?"
read DaysCompleted

if [ $DaysCompleted -eq 90 ]
then
  echo "You have finished, well done"
elif [ $DaysCompleted -lt 90 ]
then
  echo "Keep going you are doing great"
else
  echo "You have entered the wrong amount of days"
fi
```

你還可以從上面看到我們正在運行一些比較或相互檢查值以進入下一階段。我們在這裡有不同的選項值得注意。

- `eq` - 如果兩個值相等，將返回 TRUE
- `ne` - 如果兩個值不相等，將返回 TRUE
- `gt` - 如果第一個值大於第二個值，將返回 TRUE
- `ge` - 如果第一個值大於或等於第二個值，將返回 TRUE
- `lt` - 如果第一個值小於第二個值，將返回 TRUE
- `le` - 如果第一個值小於或等於第二個值，將返回 TRUE

我們也可能使用 bash 腳本來確定有關文件和文件夾的信息，這稱為文件條件。

- `-d file` 如果文件是目錄，則為 True
- `-e file` 如果文件存在，則為 True
- `-f file` 如果提供的字符串是文件，則為 True
- `-g file` 如果在文件上設置了組 id，則為 True
- `-r file` 如果文件可讀，則為 True
- `-s file` 如果文件具有非零大小，則為 True

```
FILE="90DaysOfDevOps.txt"
if [ -f "$FILE" ]
then
  echo "$FILE is a file"
else
  echo "$FILE is not a file"
fi
```

![](Images/Day19_Linux7.png)

如果我們仍然在目錄中有該文件，我們應該得到第一個 echo 命令返回。但如果我們刪除該文件，那麼我們應該得到第二個 echo 命令。

![](Images/Day19_Linux8.png)

希望你可以看到這如何在搜索系統中的特定項目時為你節省時間。

我在 GitHub 上發現了這個令人驚嘆的存儲庫，它有似乎無窮無盡的腳本 [DevOps Bash Tools](https://github.com/HariSekhon/DevOps-Bash-tools/blob/master/README.md)

### 示例

**場景**：我們有一家名為"90DaysOfDevOps"的公司，我們已經運行了一段時間，現在是時候在未來幾週內將團隊從 1 人擴展到更多人，到目前為止，我是唯一一個知道入職流程的人，所以我們想通過自動化其中一些任務來減少瓶頸。

**要求**：

- 用戶可以作為命令行參數傳入。
- 使用命令行參數的名稱創建用戶。
- 密碼可以解析為命令行參數。
- 為用戶設置密碼
- 顯示成功創建帳戶的消息。

讓我們從使用 `touch create_user.sh` 創建 shell 腳本開始

在我們繼續之前，讓我們還使用 `chmod +x create_user.sh` 使其可執行

然後我們可以使用 `nano create_user.sh` 開始編輯我們為已設置的場景編寫的腳本。

我們可以查看第一個要求"用戶可以作為命令行參數傳入"，我們可以使用以下內容

```
#! /usr/bin/bash

#A user can be passed in as a command line argument
echo "$1"
```

![](Images/Day19_Linux9.png)

繼續使用 `./create_user.sh Michael` 運行此命令，當你運行腳本時，用你的名字替換 Michael。

![](Images/Day19_Linux10.png)

接下來，我們可以採用第二個要求"使用命令行參數的名稱創建用戶"，這可以通過 `useradd` 命令完成。`-m` 選項是創建用戶主目錄為 /home/username

```
#! /usr/bin/bash

#A user can be passed in as a command line argument
echo "$1 user account being created."

#A user is created with the name of the command line argument
sudo useradd -m "$1"

```

警告：如果你不提供用戶帳戶名稱，它會出錯，因為我們沒有填充變量 `$1`

然後我們可以使用 `awk -F: '{ print $1}' /etc/passwd` 命令檢查此帳戶是否已創建。

![](Images/Day19_Linux11.png)

我們的下一個要求是"密碼可以解析為命令行參數。"首先，我們永遠不會在生產中這樣做，這更多是為了讓我們在實驗室中完成一系列要求以理解。

```
#! /usr/bin/bash

#A user can be passed in as a command line argument
echo "$1 user account being created."

#A user is created with the name of the command line argument
sudo useradd -m "$1"

#A password can be parsed as a command line argument.
sudo chpasswd <<< "$1":"$2"
```

如果我們然後使用兩個參數運行此腳本 `./create_user.sh 90DaysOfDevOps password`

你可以從下面的圖像中看到，我們執行了腳本，它創建了我們的用戶和密碼，然後我們手動跳轉到該用戶並使用 `whoami` 命令確認。

![](Images/Day19_Linux12.png)

最後一個要求是"顯示成功創建帳戶的消息。"我們已經在代碼的頂行有了這個，我們可以在上面的屏幕截圖中看到我們有一個 `90DaysOfDevOps user account being created` 顯示。這是我們使用 `$1` 參數進行測試時留下的。

現在，此腳本可用於快速入職並在 Linux 系統上設置新用戶。但也許不是一些歷史人員必須完成此工作，然後必須為其他人提供新的用戶名或密碼，我們可以添加一些我們之前早些時候涵蓋的用戶輸入來捕獲我們的變量。

```
#! /usr/bin/bash

echo "What is your intended username?"
read  username
echo "What is your password"
read  password

#A user can be passed in as a command line argument
echo "$username user account being created."

#A user is created with the name of the command line argument
sudo useradd -m $username

#A password can be parsed as a command line argument.
sudo chpasswd <<< $username:$password
```

隨著步驟變得更加互動，

![](Images/Day19_Linux14.png)

只是為了完成這個，也許我們確實想輸出一個成功的輸出來說我們的新用戶帳戶已經完成創建。

![](Images/Day19_Linux15.png)

我確實注意到的一件事是我們在輸入時顯示密碼，我們可以通過在代碼行中使用 `-s` 標誌來隱藏它 `read -s password`

![](Images/Day19_Linux16.png)

如果你確實想刪除你為實驗室目的創建的用戶，那麼你可以使用 `sudo userdel test_user` 來做到這一點

[Example Script](Linux/create-user.sh)

再次，我不是說這將是你在日常工作中創建的內容，但這是我想到的，它會突出顯示你可以使用 shell 腳本的靈活性。

考慮一下你每天、每週或每月做的任何可重複任務，以及你如何更好地自動化它，第一個選項可能是在進入更複雜的領域之前使用 bash 腳本。

我創建了一個非常簡單的 bash 文件，幫助我在本地機器上使用 minikube 啟動 Kubernetes 集群，以及數據服務和 Kasten K10，以幫助演示圍繞數據管理的需求和需求。[Project Pace](https://github.com/MichaelCade/project_pace/blob/main/singlecluster_demo.sh) 但我覺得在這裡提出這不合適，因為我們還沒有涵蓋 Kubernetes。

## 資源

- [Bash in 100 seconds](https://www.youtube.com/watch?v=I4EWvMFj37g)
- [Bash script with practical examples - Full Course](https://www.youtube.com/watch?v=TPRSJbtfK4M)
- [Client SSH GUI - Remmina](https://remmina.org/)
- [The Beginner's guide to SSH](https://www.youtube.com/watch?v=2QXkrLVsRmk)
- [Vim in 100 Seconds](https://www.youtube.com/watch?v=-txKSRn0qeA)
- [Vim tutorial](https://www.youtube.com/watch?v=IiwGbcd8S7I)
- [Learn the Linux Fundamentals - Part 1](https://www.youtube.com/watch?v=kPylihJRG70)
- [Linux for hackers (don't worry you don't need to be a hacker!)](https://www.youtube.com/watch?v=VbEx7B_PTOE)

我們[Day20](day20.md)見
