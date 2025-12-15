---
title: '#90DaysOfDevOps - Docker 網路與安全性 - 第 47 天'
published: false
description: 90DaysOfDevOps - Docker 網路與安全性
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049078
---

## Docker 網路與安全性

在這個容器課程中，到目前為止我們已經讓事情發生了，但我們沒有從網路角度查看事情如何在幕後工作，我們也沒有觸及安全性，這就是本次課程的計劃。

### Docker 網路基礎

打開終端機，然後鍵入指令 `docker network`，這是配置和管理容器網路的主要指令。

從下面，你可以看到這是我們如何使用該指令，以及所有可用的子指令。我們可以創建新網路、列出現有網路、檢查和移除網路。

![](Images/Day47_Containers1.png)

讓我們看一下自安裝以來我們擁有的現有網路，因此開箱即用的 Docker 網路看起來像使用 `docker network list` 指令。

每個網路都獲得唯一 ID 和 NAME。每個網路也與單個驅動程式相關聯。請注意，「bridge」網路和「host」網路與其各自的驅動程式具有相同的名稱。

![](Images/Day47_Containers2.png)

接下來，我們可以使用 `docker network inspect` 指令更深入地查看我們的網路。

運行 `docker network inspect bridge`，我可以獲取該特定網路名稱的所有配置詳細資訊。這包括名稱、ID、驅動程式、連接的容器，你可以看到更多內容。

![](Images/Day47_Containers3.png)

### Docker：橋接網路

正如你上面看到的，Docker Desktop 的標準安裝為我們提供了一個名為 `bridge` 的預構建網路，如果你回顧 `docker network list` 指令，你會看到名為 bridge 的網路與 `bridge` 驅動程式相關聯。僅僅因為它們有相同的名稱並不意味著它們是同一件事。連接但不是同一件事。

上面的輸出還顯示 bridge 網路的作用域是本地。這意味著該網路僅存在於此 Docker 主機上。所有使用 bridge 驅動程式的網路都是如此 - bridge 驅動程式提供單主機網路。

使用 bridge 驅動程式創建的所有網路都基於 Linux 網橋（也稱為虛擬交換機）。

### 連接容器

預設情況下，bridge 網路分配給新容器，這意味著除非你指定網路，否則所有容器都將連接到 bridge 網路。

讓我們使用指令 `docker run -dt ubuntu sleep infinity` 創建一個新容器

上面的 sleep 指令只會讓容器在後台運行，這樣我們就可以擺弄它。

![](Images/Day47_Containers4.png)

如果我們然後使用 `docker network inspect bridge` 檢查我們的 bridge 網路，你會看到我們有一個與我們剛剛部署的容器匹配的容器，因為我們沒有指定網路。

![](Images/Day47_Containers5.png)

我們也可以使用 `docker exec -it 3a99af449ca2 bash` 進入容器，你必須使用 `docker ps` 來獲取你的容器 ID。

從這裡，我們的圖像沒有任何東西可以 ping，所以我們需要運行以下指令。`apt-get update && apt-get install -y iputils-ping` 然後 ping 一個外部介面地址。`ping -c5 www.90daysofdevops.com`

![](Images/Day47_Containers6.png)

為了清理這個，我們可以運行 `docker stop 3a99af449ca2` 再次使用 `docker ps` 查找你的容器 ID，但這將移除我們的容器。

### 配置 NAT 以進行外部連接

在此步驟中，我們將啟動一個新的 NGINX 容器，並將 Docker 主機上的端口 8080 映射到容器內部的端口 80。這意味著到達 Docker 主機端口 8080 的流量將被傳遞到容器內部的端口 80。

通過運行 `docker run --name web1 -d -p 8080:80 nginx` 基於官方 NGINX 圖像啟動一個新容器

![](Images/Day47_Containers7.png)

通過運行 `docker ps` 檢查容器狀態和端口映射

![](Images/Day47_Containers8.png)

頂行顯示運行 NGINX 的新 web1 容器。注意容器運行的指令以及端口映射 - `0.0.0.0:8080->80/tcp` 將所有主機介面上的端口 8080 映射到 web1 容器內部的端口 80。此端口映射有效地使容器的 Web 服務可以從外部來源訪問（通過 Docker 主機在端口 8080 上的 IP 地址）。

現在我們需要實際主機的 IP 地址，我們可以通過進入我們的 WSL 終端機並使用 `IP addr` 指令來做到這一點。

![](Images/Day47_Containers9.png)

然後我們可以獲取此 IP 並打開瀏覽器並前往 `http://172.25.218.154:8080/` 你的 IP 可能不同。這確認 NGINX 可以訪問。

![](Images/Day47_Containers10.png)

我從 2017 年 DockerCon 的這個網站上獲取了這些說明，但它們今天仍然相關。但是，其餘的演練涉及 Docker Swarm，我不會在這裡研究它。[Docker Networking - DockerCon 2017](https://github.com/docker/labs/tree/master/dockercon-us-2017/docker-networking)

### 保護你的容器

容器為你的工作負載提供安全的環境，而不是完整的伺服器配置。它們提供了將應用程式分解為更小的、鬆散耦合的組件的能力，每個組件彼此隔離，這有助於整體減少攻擊面。

但它們並非不受試圖利用系統的黑客的影響。我們仍然需要了解技術的安全陷阱並維護最佳實踐。

### 遠離 root 權限

我們部署的所有容器都使用 root 權限來運行容器內的進程。這意味著它們對你的容器和主機環境具有完全的管理訪問權限。現在進行演練，我們知道這些系統不會長時間運行。但你看到了啟動和運行是多麼容易。

我們可以在流程中添加一些步驟，使非 root 用戶成為我們的首選最佳實踐。創建 dockerfile 時，我們可以創建用戶帳戶。你也可以在儲存庫的 containers 資料夾中找到此範例。

```
# Use the official Ubuntu 18.04 as base
FROM ubuntu:18.04
RUN apt-get update && apt-get upgrade -y
RUN groupadd -g 1000 basicuser && useradd -r -u 1000 -g basicuser basicuser
USER basicuser
```

我們也可以使用 `docker run --user 1009 ubuntu` Docker run 指令會覆蓋你在 Dockerfile 中指定的任何用戶。因此，在以下範例中，你的容器將始終以最低權限運行 - 前提是用戶標識符 1009 也具有最低權限級別。

但是，此方法不能解決圖像本身的基本安全缺陷。因此，最好在 Dockerfile 中指定非 root 用戶，以便容器始終安全運行。

### 私有註冊表

我們在 DockerHub 中大量使用公共註冊表的另一個領域，由你的組織設置的容器圖像私有註冊表意味著你可以託管你希望的位置，或者也有此類的託管服務，但總的來說，這為你和你的團隊提供了對可用圖像的完全控制。

DockerHub 非常適合為你提供基線，但它只會為你提供基本服務，你必須對圖像發布者有很多信任。

### 精簡與乾淨

儘管與安全性無關，但我在整個過程中都提到了這一點。但容器的大小也可能在攻擊面方面影響安全性，如果你的應用程式中有不使用的資源，那麼你不需要它們在容器中。

這也是我對拉取 `latest` 圖像的主要關注，因為這也會給圖像帶來很多膨脹。DockerHub 確實顯示儲存庫中每個圖像的壓縮大小。

檢查 `docker image` 是一個很好的指令來查看圖像的大小。

![](Images/Day47_Containers11.png)

## 資源

- [TechWorld with Nana - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=3c-iBn73dDE)
- [Programming with Mosh - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=pTFZFxd4hOI)
- [Docker Tutorial for Beginners - What is Docker? Introduction to Containers](https://www.youtube.com/watch?v=17Bl31rlnRM&list=WL&index=128&t=61s)
- [WSL 2 with Docker getting started](https://www.youtube.com/watch?v=5RQbdMn04Oc)
- [Blog on getting started building a docker image](https://stackify.com/docker-build-a-beginners-guide-to-building-docker-images/)
- [Docker documentation for building an image](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
- [YAML Tutorial: Everything You Need to Get Started in Minute](https://www.cloudbees.com/blog/yaml-tutorial-everything-you-need-get-started)

我們[第 48 天](day48.md)見