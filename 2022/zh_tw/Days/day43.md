---
title: '#90DaysOfDevOps - 什麼是 Docker 與安裝 - 第 43 天'
published: false
description: 90DaysOfDevOps - 什麼是 Docker 與安裝
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048739
---

## 什麼是 Docker 與安裝

在上一篇文章中，我至少提到了一次 Docker，這是因為 Docker 在使容器流行方面具有創新性，儘管它們已經存在了很長時間。

我們將在這裡使用和解釋 docker，但我們也應該提到 [Open Container Initiative (OCI)](https://www.opencontainers.org/)，這是一個行業標準組織，鼓勵創新，同時避免供應商鎖定的危險。感謝 OCI，我們在選擇容器工具鏈時有選擇，包括 Docker、[CRI-O](https://cri-o.io/)、[Podman](http://podman.io/)、[LXC](https://linuxcontainers.org/) 等。

Docker 是一個用於構建、運行和管理容器的軟體框架。術語「docker」可以指工具（指令和守護進程）或 Dockerfile 檔案格式。

我們將在這裡使用 Docker Personal，它是免費的（用於教育和學習）。這包括我們需要涵蓋的所有基礎知識，以獲得容器和工具的良好知識基礎。

可能值得分解我們將使用的一些「docker」工具以及它們的用途。術語 docker 可以指整個 docker 專案，這是一個供開發人員和管理員開發、運送和運行應用程式的平台。它也可能是對在主機上運行的 docker 守護進程的引用，該進程管理圖像和容器，也稱為 Docker Engine。

### Docker Engine

Docker Engine 是一種開源容器化技術，用於構建和容器化應用程式。Docker Engine 作為客戶端-伺服器應用程式運行：

- 具有長時間運行的守護進程 dockerd 的伺服器。
- API 指定程式可以用來與 Docker 守護進程通信和指示的介面。
- 命令列介面 (CLI) 客戶端 docker。

以上內容取自官方 Docker 文件以及特定的 [Docker Engine 概述](https://docs.docker.com/engine/)

### Docker Desktop

我們有適用於 Windows 和 macOS 系統的 docker desktop。一個易於安裝的輕量級 docker 開發環境。一個利用主機作業系統虛擬化功能的本機 OS 應用程式。

如果你想在 Windows 或 macOS 上構建、調試、測試、打包和運送 Dockerized 應用程式，這是最好的解決方案。

在 Windows 上，我們還可以利用 WSL2 和 Microsoft Hyper-V。我們將在過程中介紹一些 WSL2 的好處。

由於與主機作業系統上的虛擬化功能的整合，docker 提供了使用 Linux 作業系統運行容器的能力。

### Docker Compose

Docker compose 是一個工具，允許你在多個容器上運行更複雜的應用程式。能夠使用單個檔案和指令啟動應用程式的好處。

### Docker Hub

一個用於處理 Docker 及其組件的集中式資源。最常見的是作為託管 docker 圖像的註冊表。但這裡有很多額外服務，可以部分用於自動化或整合到 GitHub 以及安全掃描。

### Dockerfile

dockerfile 是一個文字檔案，包含你通常手動執行以構建 docker 圖像的指令。Docker 可以通過讀取我們在 dockerfile 中的指令自動構建圖像。

## 安裝 Docker Desktop

[docker 文件](https://docs.docker.com/engine/install/) 很棒，如果你剛剛開始，你應該查看並閱讀。我們將在帶有 WSL2 的 Windows 上使用 Docker Desktop。我已經在我們這裡使用的機器上完成了安裝過程。

![](Images/Day43_Containers1.png)

在你繼續安裝之前，請注意系統要求，[在 Windows 上安裝 Docker Desktop](https://docs.docker.com/desktop/windows/install/) 如果你使用的是 macOS，包括基於 M1 的 CPU 架構，你也可以查看 [在 macOS 上安裝 Docker Desktop](https://docs.docker.com/desktop/mac/install/)

我將在另一台 Windows 機器上運行 Docker Desktop 安裝過程，並在下面記錄該過程。
### Windows

- 選擇 windows 作為設備的作業系統。

   <img src = Images/Day43_operatingSystem.png>

- 導航到你想要保存安裝程式的資料夾並保存。

- 運行安裝程式並等待幾秒鐘，然後為 WSL 授予訪問權限。
    <img src = Images/Day43_EnableWSL.png>

- 點擊確定，安裝將開始。
    <img src = Images/Day43_install.png>

- Docker Desktop 已成功安裝在你的設備上。你現在可以在終端機上運行指令「docker」來檢查安裝是否成功。
    <img src = Images/Day43_check.png>

## 資源

- [TechWorld with Nana - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=3c-iBn73dDE)
- [Programming with Mosh - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=pTFZFxd4hOI)
- [Docker Tutorial for Beginners - What is Docker? Introduction to Containers](https://www.youtube.com/watch?v=17Bl31rlnRM&list=WL&index=128&t=61s)
- [WSL 2 with Docker getting started](https://www.youtube.com/watch?v=5RQbdMn04Oc)

我們[第 44 天](day44.md)見