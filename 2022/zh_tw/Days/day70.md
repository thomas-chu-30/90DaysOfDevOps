---
title: '#90DaysOfDevOps - 概述：CI/CD Pipelines - 第 70 天'
published: false
description: 90DaysOfDevOps - 概述 CI/CD Pipelines
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048836
---

## 概述：CI/CD Pipelines

CI/CD（持續整合/持續部署）Pipeline 實現是現代 DevOps 環境的骨幹。

它通過自動化應用程式的構建、測試和部署來橋接開發和運營之間的差距。

我們在挑戰的開場部分涵蓋了很多這種持續的口號。但重申：

持續整合（CI）是一種更現代的軟體開發實踐，其中增量代碼更改更頻繁、更可靠地進行。由持續整合觸發的自動化構建和測試工作流程步驟確保合併到儲存庫的代碼更改是可靠的。

然後該代碼/應用程式作為持續部署過程的一部分快速無縫地交付。

### CI/CD 的重要性？

- 快速高效地交付軟體
- 促進有效的流程，盡快將應用程式推向市場
- 持續的錯誤修復和新功能流，無需等待數月或數年才能發布版本。

開發人員定期進行小而有影響力的更改的能力意味著我們可以更快地獲得修復和更多功能。

### 好的，那麼這意味著什麼？

在 [第 5 天](day05.md) 我們涵蓋了 DevOps 背後的大量理論，正如這裡已經提到的，CI/CD Pipeline 是現代 DevOps 環境的骨幹。

![DevOps](Images/Day5_DevOps8.png)

我想重申上面圖像上的一些關鍵點，現在我們在學習 DevOps 基礎知識的旅程中更進一步。

我們指的是軟體開發生命週期（SDLC）。

步驟通常寫在無限循環內，因為這是一個永遠重複的循環。

循環中的步驟是，開發人員編寫**代碼**，然後它被**構建**或全部編譯在一起，然後對錯誤進行**測試**，然後它被**部署**到生產環境中，最終用戶或客戶使用它（**運營**），然後我們**監控**並收集反饋，最後我們**計劃**圍繞該反饋進行改進**沖洗並重複**。

### 讓我們更深入地了解 CI/CD

### CI

CI 是一種開發實踐，要求開發人員每天多次將代碼整合到共享儲存庫中。

當代碼被編寫並推送到像 Github 或 GitLab 這樣的儲存庫時，這就是魔法開始的地方。

![](Images/Day70_CICD1.png)

代碼由自動化構建驗證，這允許團隊或專案所有者及早發現任何問題。

![](Images/Day70_CICD2.png)

從那裡，代碼被分析並進行一系列自動化測試，三個範例是

- 單元測試測試源代碼的單個單元
- 驗證測試確保軟體滿足或適合預期用途
- 格式測試檢查語法和其他格式錯誤

這些測試被創建為工作流程，然後每次你推送到主分支時都會運行，所以幾乎每個主要開發團隊都有某種 CI/CD 工作流程，記住在開發團隊中，新代碼可能來自世界各地的團隊，在一天中的不同時間，來自從事各種不同專案的開發人員，構建自動化測試工作流程更有效，確保每個人在代碼被接受之前都在同一頁面上。人類每次這樣做需要更長的時間。

![](Images/Day70_CICD3.png)

一旦我們完成測試並且它們成功，那麼我們可以編譯並將它們發送到我們的儲存庫。例如，我使用 Docker Hub，但這可能是任何地方，然後在 pipeline 的 CD 方面利用它。

![](Images/Day70_CICD4.png)

所以這個過程很大程度上歸結為軟體開發過程，我們正在創建應用程式，添加、修復錯誤等，然後更新源控制和版本控制，同時也進行測試。

轉到下一階段是 CD 元素，這越來越是我們通常從任何現成軟體中看到的，我認為如果我們從 Oracle 或 Microsoft 等供應商那裡獲取軟體，我們將看到一個趨勢，我們將從 Docker Hub 類型的儲存庫中消費它，然後我們將使用 CD pipelines 將其部署到我們的環境中。

### CD

現在我們有了測試版本代碼，我們準備部署到野外，正如我所說，軟體供應商將運行此階段，但我堅信這是我們將來部署所需現成軟體的方式。

現在是將代碼發布到環境中的時候了。這將包括生產，但也可能包括其他環境，例如預備。

![](Images/Day70_CICD5.png)

我們的下一步至少是軟體部署 v1 的第 1 天，我們需要確保我們將正確的代碼庫拉取到正確的環境。這可能是從軟體儲存庫（DockerHub）拉取元素，但更可能的是，我們也從另一個代碼儲存庫拉取額外配置，例如應用程式的配置。在下圖中，我們從 DockerHub 拉取軟體的最新版本，然後我們將其發布到我們的環境，同時可能從 Git 儲存庫中獲取配置。我們的 CD 工具正在執行此操作並將所有內容推送到我們的環境。

這很可能不是同時完成的。即我們將進入預備環境，使用我們的配置運行此操作，以確保一切正確，這可能是用於測試的手動步驟，或者它可能再次自動化（讓我們使用自動化），然後才允許此代碼部署到生產環境。

![](Images/Day70_CICD6.png)

然後在此之後，當應用程式的 v2 出現時，我們沖洗並重複步驟，這次我們確保應用程式 + 配置部署到預備環境，確保一切都很好，然後我們部署到生產環境。

### 為什麼使用 CI/CD？

我認為我們可能已經多次涵蓋了好處，但這是因為它自動化了否則必須手動完成的事情，它在潛入主代碼庫之前發現小問題，你可能可以想像，如果你將壞代碼推送到客戶，那麼你將度過糟糕的時光！

它還有助於防止我們稱之為技術債務的東西，這是因為主代碼儲存庫隨著時間的推移不斷構建，那麼在第 1 天採取的快捷修復現在在幾年後是指數級更昂貴的修復，因為現在那個修復的創可貼將如此深深地交織在一起並融入所有代碼庫和邏輯。

### 工具

與其他部分一樣，我們將實際操作一些實現 CI/CD pipeline 過程的工具。

我認為同樣重要的是要注意，並非所有工具都必須同時執行 CI 和 CD，我們將查看 ArgoCD，你猜對了，它在將軟體部署到 Kubernetes 集群的 CD 元素方面非常出色。但像 Jenkins 這樣的東西可以在許多不同的平台上工作。

我計劃查看以下內容：

- Jenkins
- ArgoCD
- GitHub Actions

## 資源

- [Jenkins is the way to build, test, deploy](https://youtu.be/_MXtbjwsz3A)
- [Introduction to Jenkins](https://www.edx.org/course/introduction-to-jenkins)
- [Jenkins.io](https://www.jenkins.io/)
- [ArgoCD](https://argo-cd.readthedocs.io/en/stable/)
- [ArgoCD Tutorial for Beginners](https://www.youtube.com/watch?v=MeU5_k9ssrs)
- [What is Jenkins?](https://www.youtube.com/watch?v=LFDrDnKPOTg)
- [Complete Jenkins Tutorial](https://www.youtube.com/watch?v=nCKxl7Q_20I&t=3s)
- [GitHub Actions](https://www.youtube.com/watch?v=R8_veQiYBjI)
- [GitHub Actions CI/CD](https://www.youtube.com/watch?v=mFFXuXjVgkU)

我們[第 71 天](day71.md)見