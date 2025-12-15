---
title: '#90DaysOfDevOps - Docker 圖像的結構 - 第 45 天'
published: false
description: 90DaysOfDevOps - Docker 圖像的結構
tags: 'DevOps, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048777
---
## Docker 圖像的結構

在上一節中，我們涵蓋了一些基礎知識，關於如何使用 Docker Desktop 結合 DockerHub 來部署和運行一些經過驗證的圖像。回顧一下圖像是什麼，如果我繼續提到它們，你不會忘記它們。

Docker 圖像是一個只讀模板，包含一組用於創建可以在 Docker 平台上運行的容器的指令。它提供了一種方便的方式來打包應用程式和預配置的伺服器環境，你可以用於私人使用或與其他 Docker 用戶公開共享。Docker 圖像也是任何首次使用 Docker 的人的起點。

如果我們想創建自己的 Docker 圖像怎麼辦？為了做到這一點，我們將創建一個 Dockerfile。你看到了我們如何獲取 Ubuntu 容器圖像並添加我們的軟體，我們將擁有帶有我們想要的軟體的容器圖像，一切都很好，但是，如果該容器關閉或丟棄，那麼所有那些軟體更新和安裝都會消失，沒有可重複的版本我們所做的工作。所以這對於展示功能很有用，但它不能幫助在多個環境中傳輸圖像，每次運行容器時都安裝相同的軟體集。

### 什麼是 Dockerfile

dockerfile 是一個文字檔案，包含你通常手動執行以構建 docker 圖像的指令。Docker 可以通過讀取我們在 dockerfile 中的指令自動構建圖像。

構成 docker 圖像的每個檔案都被稱為一層。這些層形成一系列圖像，分階段相互構建。每一層都依賴於緊鄰其下方的層。你的層的順序是 docker 圖像生命週期管理效率的關鍵。

我們應該將最常更改的層組織在堆疊中盡可能高的位置，這是因為當你對圖像中的層進行更改時，Docker 不僅會重建該特定層，還會重建從它構建的所有層。因此，對頂層的更改涉及重建整個圖像的最少工作量。

每次 docker 從圖像啟動容器時（就像我們昨天運行的），它都會添加一個可寫層，稱為容器層。這存儲整個運行時對容器的所有更改。這一層是活動運行容器和源圖像本身之間的唯一區別。任意數量的同類容器可以共享對同一底層圖像的訪問，同時維護它們的狀態。

回到我們昨天使用的 Ubuntu 圖像範例。我們可以多次運行相同的指令，在第一個容器上我們可以安裝 pinta，在第二個容器上我們可以安裝 figlet，有兩個不同的應用程式，不同的目的，不同的大小等。我們部署的每個容器共享相同的圖像，但不是相同的狀態，然後當我們移除容器時，該狀態就會消失。

![](Images/Day45_Containers1.png)

遵循上面使用 Ubuntu 圖像的範例，但也遵循 DockerHub 和其他第三方儲存庫中可用的許多其他預構建容器圖像。這些圖像通常被稱為父圖像。它是構建所有其他層的基礎，並為我們的容器環境提供基本構建塊。

除了一組單獨的層檔案外，Docker 圖像還包含一個稱為 manifest 的額外檔案。這本質上是 JSON 格式的圖像描述，包含圖像標籤、數位簽名以及如何為不同類型的主機平台配置容器的詳細資訊等資訊。

![](Images/Day45_Containers2.png)

### 如何創建 docker 圖像

我們可以通過兩種方式創建 docker 圖像。我們可以稍微動態地使用我們昨天開始的過程，我們選擇基線圖像啟動該容器，並安裝我們希望在容器上擁有的所有軟體和依賴項。

然後我們可以使用 `docker commit container name`，然後我們在 docker images 和我們的 docker desktop images 標籤下擁有此圖像的本地副本。

超級簡單，我不會推薦這種方法，除非你想了解這個過程，這種方式管理生命週期管理會非常困難，而且有很多手動配置/重新配置。但這是構建 docker 圖像最快和最簡單的方法。非常適合測試、故障排除、驗證依賴項等。

我們打算構建圖像的方式是通過 dockerfile。這為我們提供了一種乾淨、緊湊和可重複的方式來創建我們的圖像。更容易的生命週期管理，易於整合到持續整合和持續交付流程中。但正如你可能猜到的那樣，這比第一個提到的過程要困難一些。

使用 dockerfile 方法更符合實際世界的企業級容器部署。

dockerfile 是一個三步過程，你創建 dockerfile 並添加組裝圖像所需的指令。

下表顯示了我們將使用或你最可能使用的某些 dockerfile 語句。

| 指令      | 目的                                                                                                   |
| --------- | ------------------------------------------------------------------------------------------------------ |
| FROM      | 指定父圖像。                                                                                           |
| WORKDIR   | 為 Dockerfile 中遵循的任何指令設置工作目錄。                                                           |
| RUN       | 安裝容器所需的任何應用程式和套件。                                                                   |
| COPY      | 從特定位置複製檔案或目錄。                                                                           |
| ADD       | 與 COPY 相同，但也能處理遠程 URL 和解壓縮壓縮檔案。                                                   |
| ENTRYPOINT | 容器啟動時始終執行的指令。如果未指定，預設為 /bin/sh -c                                            |
| CMD       | 傳遞給入口點的參數。如果未設置 ENTRYPOINT（預設為 /bin/sh -c），CMD 將是容器執行的指令。            |
| EXPOSE    | 定義通過哪個端口訪問容器應用程式。                                                                   |
| LABEL     | 向圖像添加元數據。                                                                                     |

現在我們有了如何構建第一個 dockerfile 的詳細資訊，我們可以創建一個工作目錄並創建我們的 dockerfile。我在這個儲存庫中創建了一個工作目錄，你可以看到我必須瀏覽的檔案和資料夾。[Containers](Containers)

在這個目錄中，我將創建一個 .dockerignore 檔案，類似於我們在上一節中使用的 .gitignore。此檔案將列出在 Docker 構建過程中可能創建的任何檔案，你想從最終構建中排除這些檔案。

記住，關於容器的一切都是關於緊湊、盡可能快，沒有膨脹。

我想創建一個非常簡單的 Dockerfile，佈局如下，也可以在上面連結的資料夾中找到。

```
# Use the official Ubuntu 18.04 as base
FROM ubuntu:18.04
# Install nginx and curl
RUN apt-get update && apt-get upgrade -y
RUN apt-get install -y nginx curl
RUN rm -rf /var/lib/apt/lists/*
```

在你的終端機中導航到此目錄，然後運行 `docker build -t 90daysofdevops:0.1 .` 我們使用 `-t` 然後設置圖像名稱和標籤。

![](Images/Day45_Containers3.png)

現在我們已經創建了圖像，我們可以使用 Docker Desktop 或我們可以使用 docker 命令列來運行我們的圖像。我使用了 Docker Desktop，我已經啟動了一個容器，你可以看到我們在容器的 cli 中有 `curl` 可用。

![](Images/Day45_Containers4.png)

在 Docker Desktop 中，還可以利用 UI 對此新圖像執行更多任務。

![](Images/Day45_Containers5.png)

我們可以檢查我們的圖像，這樣做你會看到很多 dockerfile 和我們想要在容器內運行的程式碼行。

![](Images/Day45_Containers6.png)

我們有一個 pull 選項，現在這對我們來說會失敗，因為這個圖像沒有託管在任何地方，所以我們會得到錯誤。但是，我們確實有一個 Push to hub，這將使我們能夠將圖像推送到 DockerHub。

如果你使用與我們之前運行的相同的 `docker build`，那麼這也不會工作，你需要構建指令是 `docker build -t {{username}}/{{imagename}}:{{version}}`

![](Images/Day45_Containers7.png)

然後，如果我們去查看我們的 DockerHub 儲存庫，你可以看到我們剛剛推送了一個新圖像。現在在 Docker Desktop 中，我們將能夠使用該 pull 標籤。

![](Images/Day45_Containers8.png)

## 資源

- [TechWorld with Nana - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=3c-iBn73dDE)
- [Programming with Mosh - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=pTFZFxd4hOI)
- [Docker Tutorial for Beginners - What is Docker? Introduction to Containers](https://www.youtube.com/watch?v=17Bl31rlnRM&list=WL&index=128&t=61s)
- [WSL 2 with Docker getting started](https://www.youtube.com/watch?v=5RQbdMn04Oc)
- [Blog on gettng started building a docker image](https://stackify.com/docker-build-a-beginners-guide-to-building-docker-images/)
- [Docker documentation for building an image](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

我們[第 46 天](day46.md)見