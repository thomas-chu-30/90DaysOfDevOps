---
title: '#90DaysOfDevOps - 開源工作流程 - 第 41 天'
published: false
description: 90DaysOfDevOps - 開源工作流程
tags: 'DevOps, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048806
---

## 開源工作流程

希望通過 Git 的最後 7 個部分，我們對 git 是什麼有了更好的理解，然後像 GitHub 這樣的基於 git 的服務如何與 git 整合以提供原始碼儲存庫，同時也是更廣泛社群協作程式碼和專案的方式。

當我們瀏覽 GitHub 基礎知識時，我們經歷了分叉隨機專案並對本地儲存庫進行更改的過程。在這裡，我們想要更進一步，為開源專案做出貢獻。記住，貢獻不一定需要是錯誤修復或程式碼功能，也可以是文件。每一點都有幫助，它也允許你動手使用我們涵蓋的一些 git 功能。

## Fork 專案

我們要做的第一件事是找到一個我們可以貢獻的專案。我最近一直在介紹 [Kanister Project](https://github.com/kanisterio/kanister)，我想將現在在 YouTube 上的演示分享到專案中的主要 readme.md 檔案。

首先，我們需要 fork 專案。讓我們進行該過程。我將導航到上面分享的連結並 fork 儲存庫。

![](Images/Day41_Git1.png)

我們現在擁有整個儲存庫的副本。

![](Images/Day41_Git2.png)

作為參考，在 readme.md 檔案上，最初列出的演示只有這兩個，所以我們需要用我們的過程來修復這個問題。

![](Images/Day41_Git3.png)

## 克隆到本地機器

現在我們有了 fork，我們可以將其下載到本地，然後開始對檔案進行編輯。使用儲存庫上的 code 按鈕，我們可以獲取 URL，然後在我們希望放置儲存庫的目錄中使用 `git clone url`。

![](Images/Day41_Git4.png)

## 進行更改

我們的專案在本地，所以我們可以打開 VSCode 或你選擇的 IDE 或文字編輯器來添加你的修改。

![](Images/Day41_Git5.png)

readme.md 檔案是用 markdown 語言編寫的，因為我正在修改其他人的專案，我將遵循現有的專案格式來添加我們的內容。

![](Images/Day41_Git6.png)

## 測試你的更改

作為最佳實踐，我們必須測試我們的更改，如果你對應用程式進行程式碼更改，這完全有意義，你會希望確保應用程式在程式碼更改後仍然正常運作，我們還必須確保文件已格式化並且看起來正確。

在 vscode 中，我們可以添加許多外掛程式，其中一個是預覽 markdown 頁面的功能。

![](Images/Day41_Git7.png)

## 將更改推送到我們分叉的儲存庫

我們沒有權限將更改直接推送到 Kanister 儲存庫，所以我們必須採用這條路線。現在我對我們的更改感到滿意，我們可以執行一些現在眾所周知的 git 指令。

![](Images/Day41_Git8.png)

現在我們回到 GitHub 再次檢查更改，然後貢獻回主專案。

看起來不錯。

![](Images/Day41_Git9.png)

現在我們可以回到我們分叉的 Kanister 儲存庫的頂部，我們可以看到我們比 kanisterio:master 分支領先 1 個提交。

![](Images/Day41_Git10.png)

接下來，我們點擊上面突出顯示的 contribute 按鈕。我們看到「Open Pull Request」選項

![](Images/Day41_Git11.png)

## 開啟 Pull Request

下一張圖片中有很多事情發生，左上角你現在可以看到我們在原始或主儲存庫中。然後你可以看到我們正在比較什麼，那就是原始主分支和我們分叉的儲存庫。然後我們有一個創建 pull request 按鈕，我們很快就會回到這裡。我們有單個提交，但如果這是更多更改，你可能在這裡有多個提交。然後我們有我們在 readme.md 檔案中進行的更改。

![](Images/Day41_Git12.png)

我們已經審查了上述更改，我們準備通過點擊綠色按鈕來創建 pull request。

然後根據專案的維護者如何在他們的儲存庫上設置他們的 Pull Request 功能，你可能會有也可能不會有一個模板，為你提供維護者希望看到的指標。

這再次是你想要對你所做的工作進行有意義描述的地方，清晰簡潔但有足夠的細節。你可以看到我已經做了一個簡單的更改概述，我已經勾選了文件。

![](Images/Day41_Git13.png)

## 創建 Pull Request

我們現在準備創建我們的 pull request。在點擊頁面頂部的「Create Pull Request」後，你將看到你的 pull request 的摘要。

![](Images/Day41_Git14.png)

向下滾動，你可能會看到一些自動化正在進行，在這種情況下，我們需要審查並且一些檢查正在進行。我們可以看到 Travis CI 正在進行中，構建已經開始，這將檢查我們的更新，確保在合併任何內容之前，我們不會因為我們的添加而破壞東西。

![](Images/Day41_Git15.png)

這裡要注意的另一件事是上面截圖中的紅色，可能看起來有點令人生畏，看起來好像你犯了錯誤！別擔心，你沒有破壞任何東西，我在這裡最大的提示是這個過程是為了幫助你和專案的維護者。如果你犯了錯誤，至少根據我的經驗，維護者會聯繫並建議下一步該怎麼做。

這個 pull request 現在是公開的，每個人都可以看到 [added Kanister presentation/resource #1237](https://github.com/kanisterio/kanister/pull/1237)

我將在合併和 pull requests 被接受之前發布這個，所以也許我們可以為任何仍在關注並可以添加成功 PR 圖片的人提供一個小獎品？

1. 將此儲存庫 Fork 到你自己的 GitHub 帳戶
2. 添加你的圖片和可能的文字
3. 將更改推送到你分叉的儲存庫
4. 創建一個我會看到並批准的 PR。
5. 我會想一些獎品

以下是成功的 PR 圖片：

![](Images/Day41_Git16.png)

這結束了我們對 Git 和 GitHub 的介紹，接下來我們將深入容器，從大圖開始了解如何以及為什麼使用容器，同時也了解虛擬化以及我們是如何到達這裡的。

## 資源

- [Learn GitLab in 3 Hours | GitLab Complete Tutorial For Beginners](https://www.youtube.com/watch?v=8aV5AxJrHDg)
- [BitBucket Tutorials Playlist](https://www.youtube.com/watch?v=OMLh-5O6Ub8&list=PLaD4FvsFdarSyyGl3ooAm-ZyAllgw_AM5)
- [What is Version Control?](https://www.youtube.com/watch?v=Yc8sCSeMhi4)
- [Types of Version Control System](https://www.youtube.com/watch?v=kr62e_n6QuQ)
- [Git Tutorial for Beginners](https://www.youtube.com/watch?v=8JJ101D3knE&t=52s)
- [Git for Professionals Tutorial](https://www.youtube.com/watch?v=Uszj_k0DGsg)
- [Git and GitHub for Beginners - Crash Course](https://www.youtube.com/watch?v=RGOj5yH7evk&t=8s)
- [Complete Git and GitHub Tutorial](https://www.youtube.com/watch?v=apGV9Kg7ics)
- [Git cheatsheet](https://www.atlassian.com/git/tutorials/atlassian-git-cheatsheet)

我們[第 42 天](day42.md)見