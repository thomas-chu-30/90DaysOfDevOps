---
title: '#90DaysOfDevOps - 程式碼的社交網路 - 第 40 天'
published: false
description: 90DaysOfDevOps - 程式碼的社交網路
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049044
---

## 程式碼的社交網路

探索 GitHub | GitLab | BitBucket

今天我想介紹一些基於 git 的服務，我們可能都聽說過，並且預期我們也會日常使用。

然後我們將使用之前課程的一些知識，將我們的資料副本移動到每個主要服務。

我將這部分稱為「程式碼的社交網路」，讓我解釋為什麼？

### GitHub

最常見的至少對我來說是 GitHub，GitHub 是一個基於 Web 的 git 託管服務。它最常被軟體開發人員用來儲存他們的程式碼。具有 git 版本控制功能的原始碼管理以及許多額外功能。它允許團隊或開源貢獻者輕鬆溝通，並提供編碼的社交層面。（因此有社交網路這個標題）自 2018 年起，GitHub 成為 Microsoft 的一部分。

GitHub 已經存在了相當長的一段時間，成立於 2007/2008 年。如今平台上有超過 4000 萬用戶。

GitHub 主要功能

- 程式碼儲存庫
- Pull Requests
- 專案管理工具集 - Issues
- CI / CD 管道 - GitHub Actions

在定價方面，GitHub 為用戶提供不同級別的定價。更多資訊可以在[定價](https://github.com/pricing)頁面找到。

在這方面，我們將涵蓋免費層級。

我將在此演練中使用我已經創建的 GitHub 帳戶，如果你沒有帳戶，那麼在 GitHub 首頁有註冊選項和一些簡單的設置步驟。

### GitHub 首頁

當你首次登入 GitHub 帳戶時，你會看到一個包含許多小工具的頁面，為你提供想要查看或執行的選項。首先我們有「All Activity」（所有活動），這將讓你了解儲存庫或與你的組織或帳戶相關的活動正在發生什麼。

![](Images/Day40_Git1.png)

接下來，我們有程式碼儲存庫，無論是我們自己的還是我們最近互動過的儲存庫。我們也可以快速創建新儲存庫或搜尋儲存庫。

![](Images/Day40_Git2.png)

然後我們有最近的活動，對我來說，這些是我最近創建或貢獻的 issues 和 pull requests。

![](Images/Day40_Git3.png)

在頁面的右側，我們有一些可能感興趣的儲存庫推薦，最可能是基於你最近的活動或自己的專案。

![](Images/Day40_Git4.png)

老實說，我很少在我剛剛看到和描述的首頁上，儘管我現在看到這個動態訊息可能真的很有用，可以在某些專案上幫助更好地與社群互動。

接下來，如果我們想進入 GitHub 個人資料，我們可以導航到右上角，在你的圖像上，有一個下拉選單，允許你導航你的帳戶。從這裡訪問你的個人資料，選擇「Your Profile」（你的個人資料）

![](Images/Day40_Git5.png)

接下來，你的個人資料頁面將出現，預設情況下，除非你更改配置，否則你不會看到我有的內容，我添加了一些功能，顯示我在 [vZilla](https://vzilla.co.uk) 上的最近部落格文章，以及我在 [YouTube](https://m.youtube.com/c/MichaelCade1) 頻道上的最新影片。

你不會花太多時間查看自己的個人資料，但這是一個很好的個人資料頁面，可以在你的網路中分享，這樣他們可以看到你正在處理的酷專案。

![](Images/Day40_Git6.png)

然後我們可以深入了解 GitHub 的基礎構建塊，即儲存庫。在這裡，你會看到你的儲存庫，如果你有私有儲存庫，它們也會顯示在這個長列表中。

![](Images/Day40_Git7.png)

由於儲存庫對 GitHub 非常重要，讓我選擇一個最近相當活躍的儲存庫，並介紹一些我們可以在這裡使用的核心功能，除了我在本地系統上的 git 中編輯「程式碼」時已經使用的所有功能。

首先，從上一個視窗，我選擇了 90DaysOfDevOps 儲存庫，我們可以看到這個視圖。你可以從這個視圖中看到我們有很多資訊，中間有主要的程式碼結構，顯示儲存在儲存庫中的檔案和資料夾。我們的 readme.md 顯示在底部。在頁面的右側，我們有一個關於部分，其中儲存庫有描述和目的。然後在下面有很多資訊，顯示有多少人為專案加星標、分叉和關注。

![](Images/Day40_Git8.png)

如果我們向下滾動一點，你還會看到我們有 Releases（發布），這些來自挑戰的 golang 部分。我們的專案中沒有任何套件，我們在這裡列出了貢獻者。（感謝社群協助我的拼寫和事實檢查）然後我們再次有使用的語言，這些來自挑戰的不同部分。

![](Images/Day40_Git9.png)

在頁面頂部，你會看到一個標籤列表。這些可能會有所不同，可以修改為只顯示你需要的那些。你會在這裡看到我沒有使用所有這些，我應該刪除它們以確保我的整個儲存庫整潔。

首先，我們有程式碼標籤，這是我們剛剛討論的，但這些標籤在瀏覽儲存庫時始終可用，這非常有用，所以我們可以快速輕鬆地在各部分之間跳轉。接下來，我們有 issues 標籤。

Issues 讓你在 GitHub 上追蹤你的工作，開發發生的地方。在這個特定的儲存庫中，你可以看到我有一些專注於添加圖表或拼寫錯誤的 issues，但我們也有一個說明需要或要求儲存庫中文版本的 issue。

如果這是一個程式碼儲存庫，那麼這是向維護者提出關注或問題的好地方，但請記住要仔細考慮和詳細說明你報告的內容，並盡可能提供詳細資訊。

![](Images/Day40_Git10.png)

下一個標籤是 Pull Requests，Pull requests 讓你可以告訴其他人你已經推送到儲存庫分支的更改。這是某人可能分叉你的儲存庫、進行更改（例如錯誤修復或功能增強）或在這個儲存庫的許多情況下只是拼寫錯誤的地方。

我們稍後會介紹分叉。

![](Images/Day40_Git11.png)

我相信下一個標籤是相當新的？但我認為對於像 #90DaysOfDevOps 這樣的專案，這可以幫助指導內容旅程，也可以幫助社群在他們完成學習旅程時。我為挑戰的每個部分創建了一些討論組，以便人們可以參與並討論。

![](Images/Day40_Git12.png)

Actions 標籤將使你能夠從 GitHub 內構建、測試和部署程式碼以及更多內容。GitHub Actions 將是我們在挑戰的 CI/CD 部分中涵蓋的內容，但這是我們可以在這裡設置一些配置以自動化步驟的地方。

在我的主要 GitHub 個人資料上，我正在使用 GitHub Actions 來獲取最新的部落格文章和 YouTube 影片，以保持該主螢幕的更新。

![](Images/Day40_Git13.png)

我在上面提到了 GitHub 不僅是原始碼儲存庫，還是專案管理工具，Project 標籤使我們能夠構建專案表格看板類型的看板，以便我們可以將 issues 和 PR 連結起來，更好地協作專案並具有這些任務的可見性。

![](Images/Day40_Git14.png)

我知道 issues 對我來說似乎是記錄功能請求的好地方，它們確實是，但 wiki 頁面允許為專案制定全面的路線圖，概述當前狀態，並通常更好地記錄你的專案，無論是故障排除還是操作指南類型的內容。

![](Images/Day40_Git15.png)

不太適用於這個專案，但 Security 標籤是為了確保貢獻者知道如何處理某些任務，我們可以在這裡定義策略，還可以進行程式碼掃描附加元件，以確保你的程式碼例如不包含機密環境變數。

![](Images/Day40_Git16.png)

對我來說，insights 標籤很棒，它提供了有關儲存庫的大量資訊，從正在進行的活動量到提交和 issues，但它還報告儲存庫的流量。你可以在左側看到一個列表，允許你深入了解儲存庫的指標。

![](Images/Day40_Git17.png)

最後，我們有 Settings 標籤，這是我們可以深入了解如何運行儲存庫的地方，我目前是儲存庫的唯一維護者，但我們可以在這裡分享這個責任。我們可以在這裡定義整合和其他此類任務。

![](Images/Day40_Git18.png)

這是 GitHub 的超快速概述，我認為還有一些其他我可能提到的領域需要更詳細的解釋。如前所述，GitHub 擁有數百萬個儲存庫，主要是儲存原始碼，這些可以是公開或私有訪問。

### Forking

我將在明天的課程中更深入地介紹開源，但任何程式碼儲存庫的一個重要部分是與社群協作的能力。讓我們想想這個場景，我想要一個儲存庫的副本，因為我想對它進行一些更改，也許我想修復一個錯誤，或者也許我想更改一些東西以用於我有的用例，這可能不是原始程式碼維護者預期的用例。這就是我們所說的分叉儲存庫。Fork 是儲存庫的副本。分叉儲存庫允許你自由地進行更改實驗，而不會影響原始專案。

讓我回到登入後的首頁，看看其中一個推薦的儲存庫。

![](Images/Day40_Git19.png)

如果我們點擊該儲存庫，我們將看到與我們剛剛在 90DaysOfDevOps 儲存庫中看到的相同外觀。

![](Images/Day40_Git20.png)

如果我們注意到下面有三個選項，我們有 watch、fork 和 star。

- Watch - 當儲存庫發生事情時更新。
- Fork - 儲存庫的副本。
- Star - 「我認為你的專案很酷」

![](Images/Day40_Git21.png)

鑑於我們想要此儲存庫的副本來工作的場景，我們將點擊 fork 選項。如果你是多個組織的成員，則必須選擇分叉將在哪裡進行，我將選擇我的個人資料。

![](Images/Day40_Git22.png)

現在我們有了儲存庫的副本，我們可以自由地工作並根據需要進行更改。這將是我們之前簡要提到的 pull request 過程的開始，但我們將在明天更詳細地介紹它。

![](Images/Day40_Git23.png)

好的，我聽到你說，但如果它在網站上，我如何對這個儲存庫和程式碼進行更改，好吧，你可以通過網站進行編輯，但它不會與在本地系統上使用你最喜歡的 IDE 和你最喜歡的顏色主題相同。為了在本地機器上獲取此儲存庫的副本，我們將執行儲存庫的克隆。這將使我們能夠在本地工作，然後將更改推送到我們分叉的儲存庫副本。

在獲取此程式碼的副本時，我們有幾個選項，如下所示。

有一個本地版本的 GitHub Desktop，它為你提供視覺化桌面應用程式來追蹤更改，並在本地和 GitHub 之間推送和拉取更改。

對於這個小演示，我將使用我們在那裡看到的 HTTPS URL。

![](Images/Day40_Git24.png)

現在在我們的本地機器上，我將導航到我願意下載此儲存庫的目錄，然後執行 `git clone url`

![](Images/Day40_Git25.png)

現在我們可以將其帶到 VScode 對其進行一些更改。

![](Images/Day40_Git26.png)

現在讓我們進行一些更改，我想更改所有這些連結並用其他東西替換它們。

![](Images/Day40_Git27.png)

現在如果我們回到 GitHub 並在該儲存庫中找到我們的 readme.md，你應該能夠看到我對檔案進行的一些更改。

![](Images/Day40_Git28.png)

在這個階段，這可能已經完成，我們可能對我們的更改感到滿意，因為我們是唯一會使用我們新更改的人，但也許這是一個錯誤更改，如果是這種情況，那麼我們將希望通過 Pull Request 來貢獻，以通知原始儲存庫維護者我們的更改，並查看他們是否接受我們的更改。

我們可以通過使用下面突出顯示的 contribute 按鈕來執行此操作。當我們研究開源工作流程時，我將在明天更詳細地介紹這一點。

![](Images/Day40_Git29.png)

我花了很長時間查看 GitHub，我聽到你們中的一些人說，但其他選項呢！

好吧，還有其他選項，我將找一些資源來涵蓋其中一些的基礎知識。在你的旅程中，你會遇到 GitLab 和 BitBucket 等服務，雖然它們是基於 git 的服務，但它們有各自的差異。

你還會遇到託管選項。在這裡，我最常看到 GitLab 作為託管版本，與 GitHub Enterprise 相比（我不相信有免費的託管 GitHub？）

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

我們[第 41 天](day41.md)見