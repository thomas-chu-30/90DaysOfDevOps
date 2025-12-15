---
title: '#90DaysOfDevOps - Docker 的替代方案 - 第 48 天'
published: false
description: 90DaysOfDevOps - Docker 的替代方案
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048807
---

## Docker 的替代方案

我確實在本節開始時說過我們將使用 Docker，僅僅是因為資源方面有很多，而且社群非常大，但這也是真正使容器流行的出發點。我鼓勵你去觀看一些關於 Docker 的歷史以及它是如何產生的，我發現這非常有用。

但正如我所暗示的那樣，Docker 還有其他替代方案。如果我們考慮 Docker 是什麼以及我們涵蓋的內容。它是一個用於開發、測試、部署和管理應用程式的平台。

我想強調一些 Docker 的替代方案，你可能會或將來會在野外看到。

### Podman

什麼是 Podman？Podman 是一個無守護進程的容器引擎，用於在 Linux 系統上開發、管理和運行 OCI 容器。容器可以以 root 模式或 rootless 模式運行。

我將從 Windows 的角度來看這個，但要知道，像 Docker 一樣，不需要虛擬化，因為它將使用底層 OS，這在 Windows 世界中無法做到。

Podman 可以在 WSL2 下運行，儘管不如 Docker Desktop 的體驗那麼流暢。還有一個 Windows 遠程客戶端，你可以連接到 Linux VM，你的容器將在其中運行。

我在 WSL2 上的 Ubuntu 是 20.04 版本。按照接下來的步驟，你將能夠在 WSL 實例上安裝 Podman。

```Shell
echo "deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_20.04/ /" |
sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list
```

添加 GPG 密鑰

```Shell
curl -L "https://download.opensuse.org/repositories/devel:/kubic:\
/libcontainers:/stable/xUbuntu_20.04/Release.key" | sudo apt-key add -
```

使用 `sudo apt-get update && sudo apt-get upgrade` 指令運行系統更新和升級。最後，我們可以使用 `sudo apt install podman` 安裝 podman

我們現在可以使用很多我們一直用於 docker 的相同指令，請注意我們沒有漂亮的 docker desktop UI。你可以在下面看到我使用了 `podman images`，安裝後我沒有任何東西，然後我使用了 `podman pull ubuntu` 來拉取 ubuntu 容器圖像。

![](Images/Day48_Containers1.png)

然後我們可以使用 `podman run -dit ubuntu` 運行我們的 Ubuntu 圖像，並使用 `podman ps` 查看我們運行的圖像。

![](Images/Day48_Containers2.png)

然後要進入該容器，我們可以運行 `podman attach dazzling_darwin` 你的容器名稱可能會不同。

![](Images/Day48_Containers3.png)

如果你從 docker 遷移到 podman，更改配置檔案以設置 `alias docker=podman` 也很常見，這樣你使用 docker 運行的任何指令都會使用 podman。

### LXC

LXC 是一個容器化引擎，使用戶能夠再次創建多個隔離的 Linux 容器環境。與 Docker 不同，LXC 充當創建多個具有獨立系統檔案和網路功能的 Linux 機器的虛擬機管理程式。在 Docker 之前就存在，然後由於 Docker 的缺點而短暫復興。

LXC 與 docker 一樣輕量級且易於部署。

### Containerd

一個獨立的容器運行時。Containerd 帶來了簡單性和穩健性以及當然的可移植性。Containerd 以前是一個作為 Docker 容器服務一部分運行的工具，直到 Docker 決定將其組件畢業為獨立組件。

Cloud Native Computing Foundation 的一個專案，將其置於與 Kubernetes、Prometheus 和 CoreDNS 等流行容器工具相同的類別中。

### 其他 Docker 工具

我們還可以提及圍繞 Rancher 和 VirtualBox 的工具和選項，但我們可以在另一個時間更詳細地涵蓋它們。

[**Gradle**](https://gradle.org/)

- 構建掃描允許團隊協作調試他們的腳本並追蹤所有構建的歷史。
- 執行選項為團隊提供持續構建的能力，以便每當輸入更改時，任務都會自動執行。
- 自定義儲存庫佈局為團隊提供了將任何檔案目錄結構視為工件儲存庫的能力。

[**Packer**](https://packer.io/)

- 能夠並行創建多個機器圖像以節省開發人員時間並提高效率。
- 團隊可以使用 Packer 的調試器輕鬆調試構建，該調試器檢查失敗並允許團隊在重新啟動構建之前嘗試解決方案。
- 通過外掛程式支持許多平台，因此團隊可以自定義他們的構建。

[**Logspout**](https://github.com/gliderlabs/logspout)

- 日誌記錄工具 - 工具的可自定義性允許團隊將相同的日誌運送到多個目的地。
- 團隊可以輕鬆管理他們的檔案，因為該工具只需要訪問 Docker socket。
- 完全開源且易於部署。

[**Logstash**](https://www.elastic.co/products/logstash)

- 使用 Logstash 的可插入框架自定義你的管道。
- 輕鬆解析和轉換數據以進行分析並提供業務價值。
- Logstash 的多種輸出讓你可以將數據路由到你想要的位置。

[**Portainer**](https://www.portainer.io/)

- 利用預製模板或創建自己的模板來部署應用程式。
- 創建團隊並為團隊成員分配角色和權限。
- 使用工具的儀表板了解每個環境中運行的內容。

## 資源

- [TechWorld with Nana - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=3c-iBn73dDE)
- [Programming with Mosh - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=pTFZFxd4hOI)
- [Docker Tutorial for Beginners - What is Docker? Introduction to Containers](https://www.youtube.com/watch?v=17Bl31rlnRM&list=WL&index=128&t=61s)
- [WSL 2 with Docker getting started](https://www.youtube.com/watch?v=5RQbdMn04Oc)
- [Blog on getting started building a docker image](https://stackify.com/docker-build-a-beginners-guide-to-building-docker-images/)
- [Docker documentation for building an image](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
- [YAML Tutorial: Everything You Need to Get Started in Minute](https://www.cloudbees.com/blog/yaml-tutorial-everything-you-need-get-started)
- [Podman | Daemonless Docker | Getting Started with Podman](https://www.youtube.com/watch?v=Za2BqzeZjBk)
- [LXC - Guide to building an LXC Lab](https://www.youtube.com/watch?v=cqOtksmsxfg)

我們[第 49 天](day49.md)見