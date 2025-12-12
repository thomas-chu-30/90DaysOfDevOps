---
title: '#90DaysOfDevOps - Text Editors - nano vs vim - Day 17'
published: false
description: 90DaysOfDevOps - Text Editors - nano vs vim
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048703
---

## 文本編輯器 - nano vs vim

你的大多數 Linux 系統都將是服務器，這些服務器不會有 GUI。我在上一節中也提到，Linux 主要由配置文件組成，要進行更改，你需要能夠編輯這些配置文件以更改系統上的任何內容。

那裡有很多選項，但我認為我們應該涵蓋可能是兩個最常見的終端文本編輯器。我使用過這兩個編輯器，對我來說，我發現 `nano` 在進行快速更改時是簡單按鈕，但 `vim` 具有如此廣泛的功能集。

### nano

- 並非在所有系統上都可用。
- 非常適合入門。

如果你運行 `nano 90DaysOfDevOps.txt`，我們將創建一個空的新文件，從這裡我們可以添加我們的文本，我們在下面有說明，說明我們想對該文件做什麼。

![](Images/Day17_Linux1.png)

我們現在可以使用 `control x + enter`，然後運行 `ls`，你現在可以看到我們的新文本文件。

![](Images/Day17_Linux2.png)

我們現在可以對該文件運行 `cat` 來讀取我們的文件。然後我們可以使用相同的 `nano 90DaysOfDevOps.txt` 來添加其他文本或修改你的文件。

對我來說，在配置文件上進行小更改時，nano 非常簡單。

### vim

可能是最常見的文本編輯器？UNIX 文本編輯器 vi 的兄弟，從 1976 年開始，我們在 vim 中獲得了很多功能。

- 幾乎在所有 Linux 發行版上都支持。
- 功能強大！你可能會找到一個完整的 7 小時課程，只涵蓋 vim。

我們可以使用 `vim` 命令進入 vim，或者如果我們想編輯新的 txt 文件，我們可以運行 `vim 90DaysOfDevOps.txt`，但你首先會看到底部缺少幫助菜單。

第一個問題可能是"我如何退出 vim？"這將是 `escape`，如果我們沒有進行任何更改，那麼它將是 `:q`

![](Images/Day17_Linux3.png)

你從 `normal` 模式開始，還有其他模式 `command, normal, visual, insert`，如果我們想添加文本，我們需要從 `normal` 切換到 `insert`，我們需要按 `i`，如果你添加了一些文本並想保存這些更改，那麼你會按 escape 然後 `:wq`

![](Images/Day17_Linux4.png)

![](Images/Day17_Linux5.png)

你可以使用 `cat` 命令確認這一點，以檢查你是否已保存這些更改。

vim 有一些很酷的快速功能，如果你知道快捷方式，它可以讓你非常快速地完成瑣碎的任務，這本身就是一門課程。假設我們添加了一個重複單詞列表，現在我們需要更改它，也許這是一個配置文件，我們重複了一個網絡名稱，現在這已經改變了，我們想快速更改它。我在這個例子中使用單詞 day。

![](Images/Day17_Linux6.png)

現在我們想用 90DaysOfDevOps 替換那個單詞，我們可以通過按 `ESC` 並鍵入 `:%s/Day/90DaysOfDevOps` 來做到這一點

![](Images/Day17_Linux7.png)

當你按 Enter 時的結果是單詞 day 然後被替換為 90DaysOfDevOps。

![](Images/Day17_Linux8.png)

複製和粘貼對我來說是一個大開眼界的時刻。複製不是複製，它是拉取。我們可以在正常模式下使用鍵盤上的 `yy` 進行複製。`p` 在同一行粘貼，`P` 在新行粘貼。

你也可以通過選擇要刪除的行數後跟 `dd` 來刪除這些行

還可能有時候你需要搜索文件，現在我們可以使用 `grep`，如前一節中提到的，但我們也可以使用 vim。我們可以使用 `/word`，這將找到第一個匹配項，要導航到下一個，你將使用 `n` 鍵，依此類推。

對於 vim，這甚至沒有觸及表面，我能給出的最大建議是親自動手並盡可能多地使用 vim。

一個常見的面試問題是你在 Linux 中最喜歡的文本編輯器是什麼，我會確保你至少對兩者都有這些知識，這樣你就可以回答，說 nano 是因為它很簡單是可以的。至少你表現出理解文本編輯器是什麼的能力。但親自動手使用它們以更熟練。

在 vim 中導航的另一個提示是我們可以使用 `H,J,K,L` 以及我們的箭頭鍵。

## 資源

- [Vim in 100 Seconds](https://www.youtube.com/watch?v=-txKSRn0qeA)
- [Vim tutorial](https://www.youtube.com/watch?v=IiwGbcd8S7I)
- [Learn the Linux Fundamentals - Part 1](https://www.youtube.com/watch?v=kPylihJRG70)
- [Linux for hackers (don't worry you don't need to be a hacker!)](https://www.youtube.com/watch?v=VbEx7B_PTOE)

我們[Day18](day18.md)見
