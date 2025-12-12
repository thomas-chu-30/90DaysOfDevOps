---
title: '#90DaysOfDevOps - Linux Commands for DevOps (Actually everyone) - Day 15'
published: false
description: 90DaysOfDevOps - Linux Commands for DevOps (Actually everyone)
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048834
---

## Linux 命令用於 DevOps（實際上每個人）

我[昨天](day14.md)提到，我們將在終端中花費大量時間使用一些命令來完成工作。

我還提到，使用我們通過 vagrant 配置的 VM，我們可以使用 `vagrant ssh` 並訪問我們的盒子。你需要與我們配置它的目錄相同。

對於 SSH，你不需要用戶名和密碼，只有當你決定登錄 Virtual Box 控制台時才需要。

這是我們想要的位置，如下所示：

![](Images/Day15_Linux1.png)

## 命令

我無法在這裡涵蓋所有命令，有大量文檔涵蓋這些，但如果你在終端中並且只需要了解特定命令的選項，我們有 `man` 頁面，手冊的簡稱。我們可以使用這個來查看我們在本文中涉及的每個命令，以找出每個命令的更多選項。我們可以運行 `man man`，這將為你提供手冊頁面的幫助。要退出 man 頁面，你應該按 `q` 退出。

![](Images/Day15_Linux2.png)
![](Images/Day15_Linux3.png)

`sudo` 如果你熟悉 Windows 和右鍵單擊 `run as administrator`，我們可以將 `sudo` 視為非常類似。當你使用此命令運行命令時，你將以 `root` 身份運行它，它會在運行命令之前提示你輸入密碼。

![](Images/Day15_Linux4.png)

對於一次性工作，如安裝應用程序或服務，你可能需要 `sudo command`，但如果你有幾個任務要處理並且你想在一段時間內以 `sudo` 身份生活怎麼辦？這就是你可以使用 `sudo su` 的地方，再次與 `sudo` 相同，一旦輸入，你將被提示輸入 `root` 密碼。在像我們這樣的測試 VM 中，這很好，但我會發現我們很難長時間以 `root` 身份運行，可能會發生壞事。要退出這個提升的位置，你只需鍵入 `exit`

![](Images/Day15_Linux5.png)

我發現自己一直在使用 `clear`，`clear` 命令完全按照它說的去做，它將清除所有先前命令的屏幕，將你的提示放在頂部並給你一個乾淨的工作區。Windows 我認為在 .mdprompt 中是 `cls`。

![](Images/Day15_Linux6.png)

現在讓我們看看一些命令，我們可以在系統中實際創建東西，然後在終端中可視化它們，首先，我們有 `mkdir`，它將允許我們在系統中創建文件夾。使用以下命令，我們可以在主目錄中創建一個名為 Day15 的文件夾 `mkdir Day15`

![](Images/Day15_Linux7.png)

使用 `cd` 這允許我們更改目錄，所以對於我們移動到新創建的目錄，我們可以使用 `cd Day15` tab 也可以用於自動完成可用的目錄。如果我們想回到開始的地方，我們可以使用 `cd ..`

![](Images/Day15_Linux8.png)

`rmdir` 允許我們刪除目錄，如果我們運行 `rmdir Day15`，那麼文件夾將被刪除（請注意，這只有在文件夾中沒有任何內容時才有效）

![](Images/Day15_Linux9.png)

我確信我們都做過這樣的事情，我們導航到文件系統深處的目錄，不知道我們在哪裡。`pwd` 為我們提供工作目錄的打印輸出，pwd 看起來像密碼，但它代表 print working directory。

![](Images/Day15_Linux10.png)

我們知道如何創建文件夾和目錄，但我們如何創建文件？我們可以使用 `touch` 命令創建文件，如果我們運行 `touch Day15`，這將創建一個文件。忽略 `mkdir`，我們稍後會再次看到這個。

![](Images/Day15_Linux11.png)

`ls` 我可以把我的房子放在這個上，你會多次使用這個命令，這將列出當前目錄中的所有文件和文件夾。讓我們看看我們是否可以看到我們剛剛創建的文件。

![](Images/Day15_Linux12.png)

我們如何在 Linux 系統上查找文件？`locate` 將允許我們搜索文件系統。如果我們使用 `locate Day15`，它將報告文件的位置。獎勵回合是，如果你知道文件確實存在但你得到空白結果，則運行 `sudo updatedb`，它將索引文件系統中的所有文件，然後再次運行你的 `locate`。如果你沒有 `locate` 可用，你可以使用此命令安裝它 `sudo apt install mlocate`

![](Images/Day15_Linux13.png)

如何將文件從一個位置移動到另一個位置？`mv` 將允許你移動文件。示例 `mv Day15 90DaysOfDevOps` 將把你的文件移動到 90DaysOfDevOps 文件夾。

![](Images/Day15_Linux14.png)

我們已經移動了文件，但如果我們現在想將其重命名為其他名稱怎麼辦？我們可以再次使用 `mv` 命令...什麼！！！？是的，我們可以簡單地使用 `mv Day15 day15` 來更改為大寫，或者我們可以使用 `mv day15 AnotherDay` 來完全更改它，現在使用 `ls` 檢查文件。

![](Images/Day15_Linux15.png)

夠了，現在讓我們刪除（刪除）我們的文件，如果我們創建了目錄，甚至可能刪除我們的目錄。`rm` 簡單地 `rm AnotherDay` 將刪除我們的文件。我們還將大量使用 `rm -R`，它將遞歸地處理文件夾或位置。我們也可能使用 `rm -R -f` 來強制刪除所有這些文件。劇透如果你運行 `rm -R -f /` 添加 sudo，你可以告別你的系統....！

![](Images/Day15_Linux16.png)

我們已經研究了移動文件，但如果我只想將文件從一個文件夾複製到另一個文件夾怎麼辦，簡單地說，它與 `mv` 命令非常相似，但我們使用 `cp`，所以我們現在可以說 `cp Day15 Desktop`

![](Images/Day15_Linux17.png)

我們已經創建了文件夾和文件，但我們還沒有在文件夾中放入任何內容，我們可以通過幾種方式添加內容，但簡單的方法是 `echo`，我們也可以使用 `echo` 在終端中打印很多東西，我經常使用 echo 來打印系統變量，以至少知道它們是否已設置。我們可以使用 `echo "Hello #90DaysOfDevOps" > Day15`，這將把這個添加到我們的文件中。我們還可以使用 `echo "Commands are fun!" >> Day15` 追加到我們的文件

![](Images/Day15_Linux18.png)

另一個你會經常使用的命令！`cat` 是 concatenate 的簡稱。我們可以使用 `cat Day15` 查看文件內的內容。非常適合快速閱讀那些配置文件。

![](Images/Day15_Linux19.png)

如果你有一個長而複雜的配置文件，並且你想或需要在該文件中快速找到某些內容，而不是閱讀每一行，那麼 `grep` 是你的朋友，這將允許我們使用 `cat Day15 | grep "#90DaysOfDevOps"` 在文件中搜索特定單詞

![](Images/Day15_Linux20.png)

如果你像我一樣經常使用 `clear` 命令，那麼你可能會錯過一些先前運行的命令，我們可以使用 `history` 來找出我們之前運行的所有命令。`history -c` 將刪除歷史記錄。

當你運行 `history` 並且你想選擇特定命令時，你可以使用 `!3` 來選擇列表中的第 3 個命令。

你還可以使用 `history | grep "Command"` 來搜索特定內容。

在服務器上追溯命令何時執行，在歷史文件中為每個命令附加日期和時間可能很有用。

以下系統變量控制此行為：

```
HISTTIMEFORMAT="%d-%m-%Y %T "
```

你可以輕鬆添加到你的 bash_profile：

```
echo 'export HISTTIMEFORMAT="%d-%m-%Y %T "' >> ~/.bash_profile
```

同樣有用的是允許歷史文件變得更大：

```
echo 'export HISTSIZE=100000' >> ~/.bash_profile
echo 'export HISTFILESIZE=10000000' >> ~/.bash_profile
```

![](Images/Day15_Linux21.png)

需要更改密碼？`passwd` 將允許我們更改密碼。請注意，當你像這樣添加密碼時，當它被隱藏時，它不會顯示在 `history` 中，但是如果你的命令有 `-p PASSWORD`，那麼這將在你的 `history` 中可見。

![](Images/Day15_Linux22.png)

我們可能還想向系統添加新用戶，我們可以使用 `useradd` 來做到這一點，我們必須使用 `sudo` 命令添加用戶，我們可以使用 `sudo useradd NewUser` 添加新用戶

![](Images/Day15_Linux23.png)

創建組再次需要 `sudo`，我們可以使用 `sudo groupadd DevOps`，然後如果我們想將新用戶添加到該組，我們可以通過運行 `sudo usermod -a -G DevOps` 來做到這一點，`-a` 是添加，`-G` 是組名。

![](Images/Day15_Linux24.png)

我們如何將用戶添加到 `sudo` 組，這將是非常罕見的情況，但要這樣做，它將是 `usermod -a -G sudo NewUser`

### 權限

讀取、寫入和執行是我們在 Linux 系統上的所有文件和文件夾上擁有的權限。

完整列表：

- 0 = 無 `---`
- 1 = 僅執行 `--X`
- 2 = 僅寫入 `-W-`
- 3 = 寫入和執行 `-WX`
- 4 = 僅讀取 `R--`
- 5 = 讀取和執行 `R-X`
- 6 = 讀取和寫入 `RW-`
- 7 = 讀取、寫入和執行 `RWX`

你還會看到 `777` 或 `775`，這些代表與上面列表相同的數字，但每個代表**用戶 - 組 - 每個人**

讓我們看一下我們的文件。`ls -al Day15` 你可以看到上面提到的 3 個組，用戶和組有讀取和寫入，但每個人只有讀取。

![](Images/Day15_Linux25.png)

我們可以使用 `chmod` 更改這個，如果你在系統上創建大量二進制文件並且需要提供執行這些二進制文件的能力，你可能會發現自己這樣做。`chmod 750 Day15` 現在運行 `ls -al Day15` 如果你想為整個文件夾運行這個，那麼你可以使用 `-R` 來遞歸地執行。

![](Images/Day15_Linux26.png)

如何更改文件的所有者？我們可以使用 `chown` 進行此操作，如果我們想將 `Day15` 的所有權從用戶 `vagrant` 更改為 `NewUser`，我們可以運行 `sudo chown NewUser Day15`，再次可以使用 `-R`。

![](Images/Day15_Linux27.png)

你會遇到的一個命令是 `awk`，當你有一個輸出而你只需要其中的特定數據時，它會真正派上用場。比如運行 `who` 我們得到帶有信息的行，但也許我們只需要名稱。我們可以運行 `who | awk '{print $1}'` 來獲取第一列的列表。

![](Images/Day15_Linux28.png)

如果你正在尋找從標準輸入讀取數據流，然後生成和執行命令行；這意味著它可以獲取命令的輸出並將其作為另一個命令的參數傳遞。`xargs` 是此用例的有用工具。例如，如果我想獲得系統上所有 Linux 用戶帳戶的列表，我可以運行。`cut -d: -f1 < /etc/passwd` 並獲得我們在下面看到的長列表。

![](Images/Day15_Linux29.png)

如果我想壓縮該列表，我可以通過在命令中使用 `xargs` 來做到這一點，例如 `cut -d: -f1 < /etc/passwd | sort | xargs`

![](Images/Day15_Linux30.png)

我也沒有提到 `cut` 命令，這允許我們從文件的每一行中刪除部分。它可以用於按字節位置、字符和字段剪切行的部分。`cut -d " " -f 2 list.txt` 命令允許我們刪除我們擁有的第一個字母，只顯示我們的數字。這裡可以使用此命令的許多組合，我確信我花了太多時間嘗試使用此命令，而我可以更快地手動提取數據。

![](Images/Day15_Linux31.png)

還要注意，如果你鍵入命令並且你不再對它滿意並且你想重新開始，只需按 control + c，這將取消該行並讓你重新開始。

## 資源

- [Learn the Linux Fundamentals - Part 1](https://www.youtube.com/watch?v=kPylihJRG70)
- [Linux for hackers (don't worry you don't need to be a hacker!)](https://www.youtube.com/watch?v=VbEx7B_PTOE)

我們[Day16](day16.md)見

這已經是一個相當重的列表了，但我可以安全地說，我在日常工作中使用了所有這些命令，無論是管理 Linux 服務器還是在我的 Linux 桌面上，當你在 Windows 或 macOS 中時，導航 UI 非常容易，但在 Linux 服務器中，它們不存在，一切都是通過終端完成的。
