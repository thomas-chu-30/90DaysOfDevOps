---
title: '#90DaysOfDevOps - Docker 圖像與 Docker Desktop 實作 - 第 44 天'
published: false
description: 90DaysOfDevOps - Docker 圖像與 Docker Desktop 實作
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048708
---

## Docker 圖像與 Docker Desktop 實作

我們現在已經在系統上安裝了 Docker Desktop。（如果你運行 Linux，那麼你仍然有選項，但沒有 GUI，但 docker 確實可以在 Linux 上工作。）[在 Ubuntu 上安裝 Docker Engine](https://docs.docker.com/engine/install/ubuntu/)（也提供其他發行版。）

在這篇文章中，我們將開始在環境中部署一些圖像。回顧一下什麼是 Docker 圖像 - Docker 圖像是用於在 Docker 容器中執行程式碼的檔案。Docker 圖像作為構建 Docker 容器的一組指令，就像模板一樣。Docker 圖像也是使用 Docker 時的起點。

現在是去 [DockerHub](https://hub.docker.com/) 創建帳戶的好時機

![](Images/Day44_Containers1.png)

DockerHub 是一個用於處理 Docker 及其組件的集中式資源。最常見的是作為託管 docker 圖像的註冊表。但這裡有很多額外服務，可以部分用於自動化或整合到 GitHub 以及安全掃描。

如果你在登入後向下滾動，你將看到容器圖像列表，你可能會看到 MySQL、hello-world 等的資料庫圖像。將這些視為很好的基線圖像，或者你可能只需要一個資料庫圖像，你最好使用官方圖像，這意味著你不需要創建自己的圖像。

![](Images/Day44_Containers2.png)

我們可以更深入地查看可用圖像的視圖，並在類別、作業系統和架構中搜索。我下面突出顯示的一件事是官方圖像，這應該讓你對這個容器圖像的來源放心。

![](Images/Day44_Containers3.png)

我們也可以搜索特定的圖像，例如，WordPress 可能是我們想要的良好基線圖像，我們可以在頂部執行此操作並找到與 WordPress 相關的所有容器圖像。下面是我們也有經過驗證的發布者的通知。

- 官方圖像 - Docker 官方圖像是 Docker 開源和「即插即用」解決方案儲存庫的精選集合。

- 經過驗證的發布者 - 來自經過驗證的發布者的高質量 Docker 內容。這些產品由商業實體直接發布和維護。

![](Images/Day44_Containers4.png)

### 探索 Docker Desktop

我們已經在系統上安裝了 Docker Desktop，如果你打開它，我預期除非你已經安裝了它，否則你會看到類似於下面圖像的內容。正如你所看到的，我們沒有運行的容器，我們的 docker 引擎正在運行。

![](Images/Day44_Containers5.png)

因為這對我來說不是全新安裝，我確實已經下載了一些圖像並在我的系統上可用。你可能在這裡看不到任何東西。

![](Images/Day44_Containers6.png)

在遠程儲存庫下，這是你可以找到存儲在 docker hub 中的任何容器圖像的地方。你可以從下面看到我沒有任何圖像。

![](Images/Day44_Containers7.png)

我們也可以在 dockerhub 網站上澄清這一點，並確認我們在那裡沒有任何儲存庫。

![](Images/Day44_Containers8.png)

接下來，我們有 Volumes 標籤，如果你有需要持久化的容器，那麼這是我們可以將這些卷添加到本地檔案系統或共享檔案系統的地方。

![](Images/Day44_Containers9.png)

在撰寫本文時，還有一個 Dev Environments 標籤，這將幫助你與團隊協作，而不是在不同的 git 分支之間移動。我們不會涵蓋這個。

![](Images/Day44_Containers10.png)

回到第一個標籤，你可以看到有一個我們可以運行的指令，這是一個入門容器。讓我們在終端機中運行 `docker run -d -p 80:80 docker/getting-started`。

![](Images/Day44_Containers11.png)

如果我們再次檢查我們的 docker desktop 視窗，我們將看到我們有一個運行的容器。

![](Images/Day44_Containers12.png)

你可能已經注意到我正在使用 WSL2，為了能夠使用它，你需要確保在設定中啟用了它。

![](Images/Day44_Containers13.png)

如果我們現在再次檢查我們的 Images 標籤，你現在應該看到一個名為 docker/getting-started 的使用中圖像。

![](Images/Day44_Containers14.png)

回到 Containers/Apps 標籤，點擊你運行的容器。默認情況下，你將看到日誌，在頂部，你有一些選項可以選擇，在我們的情況下，我非常有信心這是在此容器中運行的網頁，所以我們將選擇在瀏覽器中打開。

![](Images/Day44_Containers15.png)

當我們點擊上面的按鈕時，確實應該打開一個網頁，訪問你的 localhost 並顯示類似於下面的內容。

這個容器還有關於我們的容器和圖像的更多詳細資訊。

![](Images/Day44_Containers16.png)

我們現在已經運行了我們的第一個容器。到目前為止沒有什麼太可怕的。如果我們想從 DockerHub 拉取其中一個容器圖像怎麼辦？也許有一個 `hello world` docker 容器我們可以使用。

我繼續並停止了入門容器，不是因為它佔用了大量資源，而是為了整潔，當我們進行更多步驟時。

回到我們的終端機，讓我們繼續運行 `docker run hello-world`，看看會發生什麼。

你可以看到我們本地沒有圖像，所以我們拉取了它，然後我們收到一條寫入容器圖像的訊息，其中包含有關它如何啟動和運行的一些資訊以及一些參考點的連結。

![](Images/Day44_Containers17.png)

但是，如果我們現在去查看 Docker Desktop，我們沒有運行的容器，但我們確實有一個使用 hello-world 訊息的已退出容器，這意味著它啟動了，傳遞了訊息，然後終止了。

![](Images/Day44_Containers18.png)

最後一次，讓我們去檢查圖像標籤，看看我們在系統本地有一個新的 hello-world 圖像，這意味著如果我們再次在終端機中運行 `docker run hello-world` 指令，我們不需要拉取任何東西，除非版本發生變化。

![](Images/Day44_Containers19.png)

來自 hello-world 容器的訊息提出了運行更雄心勃勃的東西的挑戰。

接受挑戰！

![](Images/Day44_Containers20.png)

在終端機中運行 `docker run -it ubuntu bash`，我們將運行一個容器化的 Ubuntu 版本，但不是作業系統的完整副本。你可以在 [DockerHub](https://hub.docker.com/_/ubuntu) 上找到有關此特定圖像的更多資訊

你可以在下面看到，當我們運行指令時，我們現在有一個互動式提示（`-it`），並且我們有一個進入容器的 bash shell。

![](Images/Day44_Containers21.png)

我們有一個 bash shell，但我們沒有更多，這就是為什麼這個容器圖像小於 30MB。

![](Images/Day44_Containers22.png)

但我們仍然可以使用這個圖像，我們仍然可以使用我們的 apt 套件管理器安裝軟體，我們可以更新我們的容器圖像並升級。

![](Images/Day44_Containers23.png)

或者也許我們想將一些軟體安裝到我們的容器中，我在這裡選擇了一個非常糟糕的例子，因為 pinta 是一個圖像編輯器，它超過 200MB，但希望你能理解我在做什麼。這將大大增加我們容器的大小，但我們仍然會在 MB 中，而不是在 GB 中。

![](Images/Day44_Containers24.png)

我希望這能給你一個 Docker Desktop 的概述，以及當你用簡單的使用案例分解它時容器世界並不可怕，我們確實需要涵蓋一些網路、安全性和我們擁有的其他選項，而不僅僅是下載容器圖像並像這樣使用它們。在本節結束時，我們希望製作一些東西並將其上傳到我們的 DockerHub 儲存庫並能夠部署它。

## 資源

- [TechWorld with Nana - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=3c-iBn73dDE)
- [Programming with Mosh - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=pTFZFxd4hOI)
- [Docker Tutorial for Beginners - What is Docker? Introduction to Containers](https://www.youtube.com/watch?v=17Bl31rlnRM&list=WL&index=128&t=61s)
- [WSL 2 with Docker getting started](https://www.youtube.com/watch?v=5RQbdMn04Oc)

我們[第 45 天](day45.md)見