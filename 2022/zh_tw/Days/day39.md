---
title: '#90DaysOfDevOps - 查看、取消暫存、丟棄與還原 - 第 39 天'
published: false
description: '90DaysOfDevOps - 查看、取消暫存、丟棄與還原'
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048827
---

## 查看、取消暫存、丟棄與還原

繼續昨天我們結束的地方，討論 git 的一些指令以及如何在專案中利用 git。記住，我們還沒有接觸 GitHub 或任何其他基於 git 的服務，這都是為了幫助你目前在本地控制專案，但當我們開始整合到這些工具時，它們都會變得有用。

### 查看已暫存和未暫存的更改

在提交之前查看已暫存和未暫存的程式碼是個好習慣。我們可以通過執行 `git diff --staged` 指令來執行此操作。

![](Images/Day39_Git1.png)

這會顯示我們所做的所有更改以及我們添加或刪除的所有新檔案。

修改檔案中的更改用 `---` 或 `+++` 表示，你可以在下面看到我們剛剛添加了一些文字，這意味著它們是新行。

![](Images/Day39_Git2.png)

我們也可以執行 `git diff` 來比較暫存區和工作目錄。如果我們對新添加的檔案 code.txt 進行一些更改並添加一些文字行。

![](Images/Day39_Git3.png)

如果我們執行 `git diff`，我們會比較並看到下面的輸出。

![](Images/Day39_Git4.png)

### 視覺化差異工具

對我來說，上面的內容更令人困惑，所以我更願意使用視覺化工具。

一些視覺化差異工具：

- KDiff3
- P4Merge
- WinMerge（僅限 Windows）
- VSCode

要在 git 中設定此功能，我們執行以下指令 `git config --global diff.tool vscode`

我們將執行上述指令，並在啟動 VScode 時設定一些參數。

![](Images/Day39_Git5.png)

我們也可以使用 `git config --global -e` 檢查我們的配置。

![](Images/Day39_Git6.png)

然後我們可以使用 `git difftool` 現在打開我們的差異視覺化工具。

![](Images/Day39_Git7.png)

這會在差異頁面上打開我們的 VScode 編輯器並比較兩者，我們只修改了一個檔案，從沒有內容到現在在右側添加一行程式碼。

![](Images/Day39_Git8.png)

我發現這種方法更容易追蹤更改，這類似於我們在查看基於 git 的服務（如 GitHub）時會看到的內容。

我們也可以使用 `git difftool --staged` 來比較暫存區與已提交的檔案。

![](Images/Day39_Git9.png)

然後我們可以在提交之前循環查看已更改的檔案。

![](Images/Day39_Git10.png)

我使用 VScode 作為我的 IDE，像大多數 IDE 一樣，它們內建了此功能，你很少需要從終端機執行這些指令，儘管如果你由於某種原因沒有安裝 IDE，這會很有幫助。

### 查看歷史

我們之前提到了 `git log`，它將為我們提供儲存庫中所有提交的全面視圖。

![](Images/Day39_Git11.png)

每個提交都有其唯一的十六進位字串，對儲存庫是唯一的。在這裡你可以看到我們正在處理的分支，以及作者、日期和提交訊息。

我們還有 `git log --oneline`，這給了我們一個更短的十六進位字串版本，我們可以在其他 `diff` 指令中使用。我們也只有一行描述或提交訊息。

![](Images/Day39_Git12.png)

我們可以通過執行 `git log --oneline --reverse` 將此反轉為從第一次提交開始，現在我們在頁面頂部看到我們的第一次提交。

![](Images/Day39_Git13.png)

### 查看提交

如果你一直注意遵循最佳實踐並且添加了有意義的提交訊息，能夠查看提交訊息是很棒的，但是，還有 `git show` 指令，允許我們檢查和查看提交。

我們可以使用 `git log --oneline --reverse` 來獲取提交列表。然後我們可以使用這些並執行 `git show <commit ID>`

![](Images/Day39_Git14.png)

該指令的輸出將如下所示，包含提交的詳細資訊、作者和更改內容。

![](Images/Day39_Git15.png)

我們也可以使用 `git show HEAD~1`，其中 1 是我們想要從當前版本向後多少步。

如果你想要檔案的一些詳細資訊，這很棒，但如果我們想要列出整個快照目錄的樹狀結構中的所有檔案。我們可以使用 `git ls-tree HEAD~1` 指令來實現這一點，再次從最後一次提交向後一個快照。我們可以在下面看到我們有兩個 blobs，這些表示檔案，而 tree 將表示目錄。你也可以在此資訊中看到提交和標籤。

![](Images/Day39_Git16.png)

然後我們可以使用上述內容來深入查看並使用 `git show` 指令查看檔案（blobs）的內容。

![](Images/Day39_Git17.png)

然後將顯示該特定版本的檔案內容。

![](Images/Day39_Git18.png)

### 取消暫存檔案

有時你可能使用了 `git add .` 但有些檔案你不想提交到該快照。在下面的範例中，我已經將 newfile.txt 添加到我的暫存區，但我還沒有準備好提交此檔案，所以我將使用 `git restore --staged newfile.txt` 來撤銷 `git add` 步驟。

![](Images/Day39_Git19.png)

我們也可以對修改的檔案（如 main.js）執行相同操作並取消暫存提交，請參閱上面我們有一個綠色的 M 表示已修改，然後下面我們正在取消暫存這些更改。

![](Images/Day39_Git20.png)

我發現在 90DaysOfDevOps 期間這個指令非常有用，因為我有時會提前工作，我想為下一天做筆記，但我不想提交並推送到公共 GitHub 儲存庫。

### 丟棄本地更改

有時我們可能會進行更改，但我們對這些更改不滿意，我們想丟棄它們。我們將再次使用 `git restore` 指令，我們將能夠從快照或先前版本還原檔案。我們可以對目錄執行 `git restore .`，我們將從快照還原所有內容，但請注意我們的未追蹤檔案仍然存在。沒有名為 newfile.txt 的先前檔案被追蹤。

![](Images/Day39_Git21.png)

現在要刪除 newfile.txt 或任何未追蹤的檔案。我們可以使用 `git clean`，我們會收到警告。

![](Images/Day39_Git22.png)

或者，如果我們知道後果，那麼我們可能想要執行 `git clean -fd` 來強制刪除所有目錄。

![](Images/Day39_Git23.png)

### 將檔案還原到較早的版本

正如我們一直暗示的那樣，Git 可以幫助你做的很大一部分是能夠從快照還原檔案的副本（這不是備份，但它是一個非常快的還原點）我的建議是你還應該使用備份解決方案在其他位置保存程式碼的副本。

作為範例，讓我們刪除目錄中最重要的檔案，請注意我們使用基於 Unix 的指令從目錄中刪除此檔案，而不是 git 指令。

![](Images/Day39_Git24.png)

現在我們的工作目錄中沒有 readme.md。我們可以使用 `git rm readme.md`，這會反映在我們的 git 資料庫中。讓我們也從這裡刪除它以模擬它被完全刪除。

![](Images/Day39_Git25.png)

現在讓我們用訊息提交此更改，並證明我們的工作目錄或暫存區中不再有任何內容。

![](Images/Day39_Git26.png)

犯了錯誤，我們現在需要這個檔案回來！

我們可以使用 `git undo` 指令來撤銷最後一次提交，但如果那是一段時間之前呢？我們可以使用 `git log` 指令找到我們的提交，然後我們發現檔案在最後一次提交中，但我們不希望所有提交都被撤銷，所以我們可以使用此指令 `git restore --source=HEAD~1 README.md` 來專門查找檔案並從快照還原它。

你可以看到使用此過程，我們現在在工作目錄中有檔案回來了。

![](Images/Day39_Git27.png)

我們現在有一個新的未追蹤檔案，我們可以使用前面提到的指令來追蹤、暫存和提交我們的檔案和更改。

### Rebase vs Merge

這似乎是 Git 中最大的頭痛問題，以及何時在 git 儲存庫中使用 rebase 與使用 merge。

首先要知道的是 `git rebase` 和 `git merge` 都解決了相同的問題。兩者都是將更改從一個分支整合到另一個分支。但是，它們以不同的方式執行此操作。

讓我們從新專用分支中的新功能開始。Main 分支繼續進行新的提交。

![](Images/Day39_Git28.png)

這裡的簡單選擇是使用 `git merge feature main`，這會將 main 分支合併到 feature 分支中。

![](Images/Day39_Git29.png)

合併很容易，因為它是非破壞性的。現有分支不會以任何方式更改。但是，這也意味著每次需要合併上游更改時，feature 分支都會有一個不相關的合併提交。如果 main 非常繁忙或活躍，這將或可能污染 feature 分支歷史。

作為替代選項，我們可以使用以下方式將 feature 分支 rebase 到 main 分支上

```
git checkout feature
git rebase main
```

這會移動 feature 分支（整個 feature 分支），有效地合併 main 中的所有新提交。但是，不使用合併提交，rebase 通過為原始分支中的每個提交創建全新的提交來重寫專案歷史。

![](Images/Day39_Git30.png)

rebase 的最大好處是更清晰的專案歷史。它還消除了不必要的合併提交。當你比較最後兩張圖片時，你可以遵循可以說更清晰的線性專案歷史。

儘管這仍然不是一個必然的結論，選擇更清晰的歷史也伴隨著權衡，如果你不遵循 [The Golden rule of rebasing](https://www.atlassian.com/git/tutorials/merging-vs-rebasing#the-golden-rule-of-rebasing) 重寫專案歷史可能會對你的協作工作流程造成災難性的影響。而且，不太重要的是，rebase 失去了合併提交提供的上下文——你無法看到何時將上游更改合併到功能中。

## 資源

- [What is Version Control?](https://www.youtube.com/watch?v=Yc8sCSeMhi4)
- [Types of Version Control System](https://www.youtube.com/watch?v=kr62e_n6QuQ)
- [Git Tutorial for Beginners](https://www.youtube.com/watch?v=8JJ101D3knE&t=52s)
- [Git for Professionals Tutorial](https://www.youtube.com/watch?v=Uszj_k0DGsg)
- [Git and GitHub for Beginners - Crash Course](https://www.youtube.com/watch?v=RGOj5yH7evk&t=8s)
- [Complete Git and GitHub Tutorial](https://www.youtube.com/watch?v=apGV9Kg7ics)
- [Git cheatsheet](https://www.atlassian.com/git/tutorials/atlassian-git-cheatsheet)
- [Exploring the Git command line – A getting started guide](https://veducate.co.uk/exploring-the-git-command-line/)

我們[第 40 天](day40.md)見