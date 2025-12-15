---
title: '#90DaysOfDevOps - 什麼是 Jenkins？- 第 71 天'
published: false
description: 90DaysOfDevOps - 什麼是 Jenkins？
tags: 'DevOps, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048745
---

## 什麼是 Jenkins？

Jenkins 是一個持續整合工具，允許持續開發、測試和部署新創建的代碼。

我們可以通過兩種方式實現這一點，即夜間構建或持續開發。第一個選項是我們的開發人員在一天中開發他們的任務，到了設定日結束時，他們將更改推送到源代碼儲存庫。然後在夜間，我們運行單元測試並構建軟體。這可能被視為整合所有代碼的舊方式。

![](Images/Day71_CICD1.png)

另一個選項和首選方式是我們的開發人員仍然將更改提交到源代碼，然後當該代碼提交完成時，構建過程會持續啟動。

![](Images/Day71_CICD2.png)

上述方法意味著，在世界各地分佈的開發人員，我們每天沒有固定的時間必須停止提交代碼更改。這就是 Jenkins 作為 CI 伺服器來控制這些測試和構建過程的地方。

![](Images/Day71_CICD3.png)

我知道我們在這裡談論 Jenkins，但我也想添加一些其他的，也許稍後再查看，以了解為什麼我看到 Jenkins 是最受歡迎的，為什麼是這樣，其他的可以做什麼超過 Jenkins。

- TravisCI - 一個託管、分佈式的持續整合服務，用於構建和測試託管在 GitHub 上的軟體專案。
- Bamboo - 可以並行運行多個構建以加快編譯速度，內建功能可連接到儲存庫，並具有 Ant 和 Maven 的構建任務。
- Buildbot - 是一個開源框架，用於自動化軟體構建、測試和發布過程。它用 Python 編寫，支持跨多個平台的分佈式、並行作業執行。
- Apache Gump - 特定於 Java 專案，設計用於每晚構建和測試這些 Java 專案。確保所有專案在 API 和功能級別都兼容。

因為我們現在將專注於 Jenkins - Jenkins 再次像上述所有工具一樣是開源的，並且是用 Java 編寫的自動化伺服器。它用於通過持續整合自動化軟體開發過程，並促進持續交付。

### Jenkins 的功能

正如你可能期望的那樣，Jenkins 有很多跨越許多領域的功能。

**易於安裝** - Jenkins 是一個自包含的基於 Java 的程式，準備在 Windows、macOS 和 Linux 作業系統上運行。

**易於配置** - 通過 Web 界面輕鬆設置和配置，包括錯誤檢查和內建幫助。

**插件** - 更新中心提供大量插件，並與 CI / CD 工具鏈中的許多工具整合。

**可擴展** - 除了可用的插件外，Jenkins 可以通過該插件架構進行擴展，這為其用途提供了幾乎無限的選項。

**分佈式** - Jenkins 輕鬆地在多台機器上分佈工作，有助於加快跨多個平台的構建、測試和部署。

### Jenkins Pipeline

你已經看到這個 pipeline，但用於更廣泛的範圍，我們還沒有談論特定工具。

你將向 Jenkins 提交代碼，然後它將構建應用程式，進行所有自動化測試，然後在每個步驟完成時發布和部署該代碼。Jenkins 是允許此過程自動化的工具。

![](Images/Day71_CICD4.png)

### Jenkins 架構

首先，不想重新發明輪子，[Jenkins 文件](https://www.jenkins.io/doc/developer/architecture/) 始終是開始的地方，但我將在這裡也記下我的筆記和學習。

Jenkins 可以安裝在許多不同的作業系統上，Windows、Linux 和 macOS，但也可以作為 Docker 容器和在 Kubernetes 內部署。[安裝 Jenkins](https://www.jenkins.io/doc/book/installing/)

當我們進入這個時，我們可能會查看在模擬部署到 Kubernetes 的 minikube 集群中安裝 Jenkins。但這將取決於我們在本節其餘部分中組合的場景。

現在讓我們分解下面的圖像。

步驟 1 - 開發人員將更改提交到源代碼儲存庫。

步驟 2 - Jenkins 定期檢查儲存庫並拉取任何新代碼。

步驟 3 - 構建伺服器然後將代碼構建為可執行檔案，在此範例中，我們使用 maven 作為知名的構建伺服器。另一個要涵蓋的領域。

步驟 4 - 如果構建失敗，則將反饋發送回開發人員。

步驟 5 - Jenkins 然後將構建的應用程式部署到測試伺服器，在此範例中，我們使用 selenium 作為知名的測試伺服器。另一個要涵蓋的領域。

步驟 6 - 如果測試失敗，則將反饋傳遞給開發人員。

步驟 7 - 如果測試成功，那麼我們可以將它們發布到生產環境。

這個循環是持續的，這就是允許應用程式在幾分鐘內更新而不是幾小時、幾天、幾個月和幾年的原因！

![](Images/Day71_CICD5.png)

如果你需要，Jenkins 的架構還有很多內容，它們具有主從功能，這使主節點能夠將任務分發到從屬 Jenkins 環境。

作為參考，Jenkins 是開源的，將有很多需要支持的企業，CloudBees 是 Jenkins 的企業版本，為付費企業客戶帶來支持以及可能的其他功能。

客戶中的一個範例是 Bosch，你可以在[這裡](https://assets.ctfassets.net/vtn4rfaw6n2j/case-study-boschpdf/40a0b23c61992ed3ee414ae0a55b6777/case-study-bosch.pdf)找到 Bosch 案例研究

我將尋找一個逐步的應用程式範例，我們可以使用它來演練使用 Jenkins，然後也將此與其他一些工具一起使用。

## 資源

- [Jenkins is the way to build, test, deploy](https://youtu.be/_MXtbjwsz3A)
- [Jenkins.io](https://www.jenkins.io/)
- [ArgoCD](https://argo-cd.readthedocs.io/en/stable/)
- [ArgoCD Tutorial for Beginners](https://www.youtube.com/watch?v=MeU5_k9ssrs)
- [What is Jenkins?](https://www.youtube.com/watch?v=LFDrDnKPOTg)
- [Complete Jenkins Tutorial](https://www.youtube.com/watch?v=nCKxl7Q_20I&t=3s)
- [GitHub Actions](https://www.youtube.com/watch?v=R8_veQiYBjI)
- [GitHub Actions CI/CD](https://www.youtube.com/watch?v=mFFXuXjVgkU)

我們[第 72 天](day72.md)見