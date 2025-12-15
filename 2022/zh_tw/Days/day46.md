---
title: '#90DaysOfDevOps - Docker Compose - 第 46 天'
published: false
description: 90DaysOfDevOps - Docker Compose
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048740
---

## Docker Compose

如果你有一個包含單一用例所需的一切的自包含圖像，運行一個容器的能力可能會很棒，當你希望在不同容器圖像之間構建多個應用程式時，事情就變得有趣了。例如，如果我有一個網站前端但需要後端資料庫，我可以將所有內容放在一個容器中，但更好和更有效的是為資料庫設置其容器。

這就是 Docker Compose 的用武之地，它是一個工具，允許你在多個容器上運行更複雜的應用程式。能夠使用單個檔案和指令啟動應用程式的好處。我在這篇文章中將要演練的範例來自 [Docker QuickStart 範例應用程式（快速入門：Compose 和 WordPress）](https://docs.docker.com/samples/wordpress/)。

在這個第一個範例中，我們將：

- 使用 Docker Compose 啟動 WordPress 和單獨的 MySQL 實例。
- 使用一個名為 `docker-compose.yml` 的 YAML 檔案
- 構建專案
- 通過瀏覽器配置 WordPress
- 關閉和清理

### 安裝 Docker Compose

如前所述，Docker Compose 是一個工具，如果你在 macOS 或 Windows 上，那麼 compose 包含在你的 Docker Desktop 安裝中。但是，你可能希望在 Windows 伺服器主機或 Linux 伺服器上運行容器，在這種情況下，你可以使用這些說明進行安裝 [安裝 Docker Compose](https://docs.docker.com/compose/install/)

為了確認我們在系統上安裝了 `docker-compose`，我們可以打開終端機並簡單地鍵入上述指令。

![](Images/Day46_Containers1.png)

### Docker-Compose.yml (YAML)

接下來要討論的是 docker-compose.yml，你可以在儲存庫的容器資料夾中找到它。但更重要的是，我們需要大致討論一下 YAML。

YAML 幾乎可以有自己的課程，因為你會在很多不同的地方找到它。但大部分情況下

「YAML 是一種對所有程式語言友好的人類可讀數據序列化語言。」

它通常用於配置檔案和某些儲存或傳輸數據的應用程式中。毫無疑問，你遇到過提供相同配置檔案的 XML 檔案。YAML 提供了最小語法，但針對相同的用例。

YAML Ain't Markup Language (YAML) 是一種序列化語言，在過去幾年中穩步增長。物件序列化能力使其成為 JSON 等語言的可行替代方案。

YAML 首字母縮略詞是 Yet Another Markup Language 的簡寫。但維護者將其重命名為 YAML Ain't Markup Language，以更多地強調其面向數據的功能。

無論如何，回到 docker-compose.yml 檔案。這是關於在單一系統上部署多個容器時我們想要做什麼的配置檔案。

直接從上面連結的教程中，你可以看到檔案的內容如下：

```
version: "3.9"

services:
  DB:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress

  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    volumes:
      - wordpress_data:/var/www/html
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
volumes:
  db_data: {}
  wordpress_data: {}
```

我們聲明一個版本，然後這個 docker-compose.yml 檔案的大部分由我們的服務組成，我們有一個 DB 服務和一個 WordPress 服務。你可以看到每個都有一個與版本標籤相關聯的圖像定義。我們現在也在配置中引入狀態，與我們第一次演練不同，但現在我們將創建卷，以便我們可以在那裡儲存資料庫。

然後我們有一些環境變數，例如密碼和用戶名。這些檔案可能變得非常複雜，但 YAML 配置檔案簡化了整體外觀。

### 構建專案

接下來，我們可以回到終端機，並可以使用 docker-compose 工具的一些指令。導航到你的目錄，你的 docker-compose.yml 檔案所在的位置。

從終端機，我們可以簡單地運行 `docker-compose up -d`，這將開始拉取這些圖像並建立多容器應用程式的過程。

此指令中的 `-d` 表示分離模式，這意味著運行指令在或將在後台運行。

![](Images/Day46_Containers2.png)

如果我們現在運行 `docker ps` 指令，你可以看到我們有 2 個運行的容器，一個是 WordPress，另一個是 MySQL。

![](Images/Day46_Containers3.png)

接下來，我們可以通過打開瀏覽器並前往 `http://localhost:8000` 來驗證我們已經啟動並運行 WordPress，你應該看到 WordPress 設置頁面。

![](Images/Day46_Containers4.png)

我們可以進行 WordPress 的設置，然後我們可以開始在下面的控制台中根據需要構建我們的網站。

![](Images/Day46_Containers5.png)

如果我們打開一個新標籤並導航到我們之前所做的相同地址 `http://localhost:8000`，我們現在將看到一個簡單的預設主題，帶有我們的網站標題「90DaysOfDevOps」，然後是一個範例文章。

![](Images/Day46_Containers6.png)

在進行任何更改之前，打開 Docker Desktop 並導航到 volumes 標籤，在這裡你將看到與我們的容器相關聯的兩個卷，一個用於 WordPress，一個用於 DB。

![](Images/Day46_Containers7.png)

我當前的 wordpress 主題是「Twenty Twenty-Two」，我想將其更改為「Twenty Twenty」回到儀表板，我們可以進行這些更改。

![](Images/Day46_Containers8.png)

我還要在我的網站上添加一篇新文章，下面你看到我們新網站的最新版本。

![](Images/Day46_Containers9.png)

### 清理或不清理

如果我們現在使用指令 `docker-compose down`，這將關閉我們的容器。但會保留我們的卷。

![](Images/Day46_Containers10.png)

我們可以在 Docker Desktop 中確認我們的卷仍然存在。

![](Images/Day46_Containers11.png)

如果我們想要重新啟動，那麼我們可以從同一目錄中發出 `docker up -d` 指令，我們將重新啟動並運行應用程式。

![](Images/Day46_Containers12.png)

然後我們在瀏覽器中導航到 `http://localhost:8000` 的相同地址，並注意到我們的新文章和主題更改都仍然存在。

![](Images/Day46_Containers13.png)

如果我們想擺脫容器和這些卷，那麼發出 `docker-compose down --volumes` 也將銷毀卷。

![](Images/Day46_Containers14.png)

現在當我們再次使用 `docker-compose up -d` 時，我們將啟動，但是，圖像仍然在我們的系統本地，所以你不需要從 DockerHub 儲存庫重新拉取它們。

我知道當我開始深入研究 docker-compose 及其功能時，我對它在容器編排工具（如 Kubernetes）旁邊或與它們的位置感到困惑，好吧，我們在這個短演示中所做的一切都專注於一個主機，我們在本地桌面機器上運行 WordPress 和 DB。我們沒有多個虛擬機器或多個物理機器，我們也無法輕鬆地上下擴展應用程式的需求。

我們的下一節將涵蓋 Kubernetes，但我們首先還有幾天的一般容器內容。

這也是多個整合的 docker-compose 應用程式範例的絕佳資源。[Awesome-Compose](https://github.com/docker/awesome-compose)

在上述儲存庫中，有一個很好的範例，將在單節點中部署 Elasticsearch、Logstash 和 Kibana (ELK)。

我已將檔案上傳到 [Containers 資料夾](2022/Days/Containers/elasticsearch-logstash-kibana/) 當你本地有此資料夾時，導航到那裡，你可以簡單地使用 `docker-compose up -d`

![](Images/Day46_Containers15.png)

然後我們可以使用 `docker ps` 檢查我們是否有那些運行的容器

![](Images/Day46_Containers16.png)

現在我們可以為每個容器打開瀏覽器：

![](Images/Day46_Containers17.png)

要移除所有內容，我們可以使用 `docker-compose down` 指令。

## 資源

- [TechWorld with Nana - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=3c-iBn73dDE)
- [Programming with Mosh - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=pTFZFxd4hOI)
- [Docker Tutorial for Beginners - What is Docker? Introduction to Containers](https://www.youtube.com/watch?v=17Bl31rlnRM&list=WL&index=128&t=61s)
- [WSL 2 with Docker getting started](https://www.youtube.com/watch?v=5RQbdMn04Oc)
- [Blog on getting started building a docker image](https://stackify.com/docker-build-a-beginners-guide-to-building-docker-images/)
- [Docker documentation for building an image](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
- [YAML Tutorial: Everything You Need to Get Started in Minute](https://www.cloudbees.com/blog/yaml-tutorial-everything-you-need-get-started)

我們[第 47 天](day47.md)見