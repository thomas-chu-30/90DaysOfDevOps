---
title: '#90DaysOfDevOps - 暫存與更改 - 第 38 天'
published: false
description: 90DaysOfDevOps - 暫存與更改
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049042
---

## 暫存與更改

我們已經涵蓋了一些基礎知識，但將內容整理成實際操作步驟對我來說更有助於學習和理解我們為什麼要這樣做。在我們進入任何基於 git 的服務（如 GitHub）之前，git 在本地工作站上也有其強大的功能。

我們將使用在 git 課程開始時創建的專案資料夾，並逐步介紹我們可以用 git 執行的一些簡單步驟。我們在本地機器上創建了一個資料夾，並使用 `git init` 指令初始化它。

![](Images/Day38_Git1.png)

我們現在也可以看到，初始化資料夾後，我們在目錄中有一個隱藏資料夾。

![](Images/Day38_Git2.png)

這是 git 儲存庫的詳細資訊以及我們的分支和提交資訊的儲存位置。

### 暫存檔案

然後我們開始處理空資料夾，也許我們在第一天的工作中添加一些原始碼。我們創建 readme.md 檔案，我們可以在目錄中看到該檔案，接下來我們檢查 `git status`，它知道新的 readme.md 檔案，但我們還沒有提交該檔案。

![](Images/Day38_Git3.png)

我們可以使用 `git add README.md` 指令暫存我們的 readme.md 檔案，然後我們可以看到需要提交的更改，這是我們以前沒有的，以及一個綠色的新檔案。

![](Images/Day38_Git4.png)

接下來我們想要提交這個，這是我們的第一次提交或我們專案的第一個快照。我們可以使用 `git commit -m "有意義的訊息"` 指令來執行此操作，這樣我們就可以輕鬆查看每次提交的更改。另外，請注意黃色交叉現在變成了綠色勾選標記。這是我終端機中使用的主題顯示方式，這是我們在 Linux 部分涵蓋的內容。

![](Images/Day38_Git5.png)

### 提交更改

我們很可能想要添加更多檔案或更改目錄中的檔案。我們已經在上面進行了第一次提交。但現在我們要添加更多詳細資訊和更多檔案。

我們可以重複之前的過程，創建或編輯檔案 > `git add .` 將所有檔案添加到暫存區，然後 `git commit -m "有意義的訊息"`，這樣就可以正常運作。但是為了能夠在提交時提供有意義的訊息說明發生了什麼變化，你可能不想寫出類似 `git commit -m "嗯，我更改了一些程式碼，因為它不起作用，當我修復它時，我還添加了一些新內容到 readme.md 以確保每個人都知道使用者體驗，然後我泡了一杯茶。"` 這樣的訊息。我的意思是這樣也可以，但可能應該更具描述性，這裡的首選方法是使用文字編輯器添加此訊息。

如果我們在執行 `git add` 後執行 `git commit`，它將打開我們的預設文字編輯器，在我這裡是 nano。以下是我添加一些更改到檔案的步驟，執行 `git status` 顯示哪些已暫存，哪些未暫存。然後我使用 `git add` 將檔案添加到暫存區，然後執行 `git commit`，這會打開 nano。

![](Images/Day38_Git6.png)

當 nano 打開時，你可以添加簡短和詳細的描述，然後保存檔案。

![](Images/Day38_Git7.png)

### 提交最佳實踐

在何時提交和頻繁提交之間需要取得平衡。我們不想等到專案結束才提交，每次提交都應該有意義，而且它們也不應該與不相關的任務耦合在一起。如果你有錯誤修復和拼寫錯誤，請確保它們是兩個獨立的提交，這是最佳實踐。

讓提交訊息有意義。

在措辭方面，團隊或你自己應該堅持每次提交使用相同的措辭。

### 跳過暫存區

我們是否總是要在提交之前暫存更改？

答案是肯定的，但不要將其視為捷徑，你必須 100% 確定不需要該快照來回滾，這是有風險的做法。

![](Images/Day38_Git8.png)

### 刪除檔案

那麼從專案中刪除檔案呢？也許我們的目錄中有另一個已提交的檔案，但現在專案不再需要或使用它，作為最佳實踐，我們應該刪除它。

僅僅因為我們從目錄中刪除檔案，git 仍然知道這個檔案，我們也需要從儲存庫中刪除它。你可以看到下面的工作流程。

![](Images/Day38_Git9.png)

如果你有一個包含許多移動檔案和資料夾的大型專案，要記住或處理這些可能會有點麻煩。我們可以使用 `git rm oldcode.ps1` 這個指令一步完成。

![](Images/Day38_Git10.png)

### 重新命名或移動檔案

在我們的作業系統中，我們可以重新命名和移動檔案。毫無疑問，我們會不時需要在專案中執行此操作。類似於刪除，雖然有兩步過程，我們在作業系統上更改檔案，然後我們必須修改並確保暫存區或檔案正確添加。步驟如下：

![](Images/Day38_Git11.png)

但是，就像從作業系統刪除檔案然後從 git 儲存庫刪除一樣，我們也可以使用 git 指令執行此重新命名。

![](Images/Day38_Git12.png)

### 忽略檔案

我們可能需要忽略專案中的某些檔案或資料夾，這些檔案或資料夾可能在本地使用，或者如果我們要與整個專案共享，只會浪費空間，一個很好的例子是日誌。我也認為可以將其用於你不想公開共享或跨團隊共享的機密資訊。

我們可以通過將資料夾或檔案添加到專案目錄中的 `.gitignore` 檔案來忽略檔案。

![](Images/Day38_Git13.png)

然後你可以打開 `.gitignore` 檔案，看到我們有 logs/ 目錄。但我們也可以在這裡添加其他檔案和資料夾來忽略。

![](Images/Day38_Git14.png)

然後我們可以看到 `git status` 並查看發生了什麼。

![](Images/Day38_Git15.png)

還有一些方法你可能需要返回並忽略檔案和資料夾，也許你確實想要共享 logs 資料夾，但後來意識到你不想這樣做。如果你有一個先前追蹤的資料夾，現在想要忽略它，則必須使用 `git rm --cached` 從暫存區中刪除檔案和資料夾。

### 簡短狀態

我們一直在大量使用 `git status` 來了解暫存區中有什麼和沒有什麼，這是一個非常全面的指令，包含大量詳細資訊。大多數時候，你只想知道什麼已被修改或什麼是新的？我們可以使用 `git status -s` 來獲取此詳細資訊的簡短狀態。我通常會在系統上設置別名，只使用 `git status -s` 而不是更詳細的指令。

![](Images/Day38_Git16.png)

在明天的文章中，我們將繼續查看這些常見 git 指令的簡短範例。

## 資源

- [What is Version Control?](https://www.youtube.com/watch?v=Yc8sCSeMhi4)
- [Types of Version Control System](https://www.youtube.com/watch?v=kr62e_n6QuQ)
- [Git Tutorial for Beginners](https://www.youtube.com/watch?v=8JJ101D3knE&t=52s)
- [Git for Professionals Tutorial](https://www.youtube.com/watch?v=Uszj_k0DGsg)
- [Git and GitHub for Beginners - Crash Course](https://www.youtube.com/watch?v=RGOj5yH7evk&t=8s)
- [Complete Git and GitHub Tutorial](https://www.youtube.com/watch?v=apGV9Kg7ics)
- [Git cheatsheet](https://www.atlassian.com/git/tutorials/atlassian-git-cheatsheet)

我們[第 39 天](day39.md)見