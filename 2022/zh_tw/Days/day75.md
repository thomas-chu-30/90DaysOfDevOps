---
title: '#90DaysOfDevOps - GitHub Actions 概述 - 第 75 天'
published: false
description: 90DaysOfDevOps - GitHub Actions 概述
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049070
---

## GitHub Actions 概述

在本節中，我想繼續並查看我們剛剛花時間做的事情的不同方法。GitHub Actions 是我們在本節中將專注的內容。

GitHub Actions 是一個 CI/CD 平台，允許我們在 pipeline 中構建、測試和部署以及其他任務。它具有構建和測試 GitHub 儲存庫的工作流程概念。你還可以使用 GitHub Actions 根據儲存庫中發生的事件驅動其他工作流程。

### 工作流程

總的來說，在 GitHub Actions 中，我們的任務稱為**工作流程**。

- **工作流程**是可配置的自動化過程。
- 定義為 YAML 檔案。
- 包含並運行一個或多個**作業**
- 將在儲存庫中的**事件**觸發時運行，或可以手動運行
- 每個儲存庫可以有多個工作流程
- **工作流程**將包含**作業**，然後是實現該**作業**的**步驟**
- 在我們的**工作流程**中，我們還將有一個**運行器**，我們的**工作流程**在其上運行。

例如，你可以有一個**工作流程**來構建和測試 pull requests，另一個**工作流程**在每次創建發布時部署應用程式，還有另一個**工作流程**在有人打開新問題時添加標籤。

### 事件

事件是儲存庫中觸發工作流程運行的特定事件。

### 作業

作業是工作流程中在運行器上執行的步驟集。

### 步驟

作業中的每個步驟可以是執行的 shell 腳本或操作。步驟按順序執行，它們相互依賴。

### 操作

可重複的自定義應用程式用於頻繁重複的任務。

### 運行器

運行器是運行工作流程的伺服器，每個運行器一次運行一個作業。GitHub Actions 提供運行 Ubuntu Linux、Microsoft Windows 和 macOS 運行器的能力。你還可以在特定 OS 或硬體上託管自己的運行器。

下面你可以看到這看起來如何，我們有觸發工作流程的事件 > 我們的工作流程由兩個作業組成 > 在我們的作業中，我們有步驟，然後我們有操作。

![](Images/Day75_CICD1.png)

### YAML

在我們開始實際用例之前，讓我們快速查看上面圖像的 YAML 檔案範例形式。

我已經添加了 # 到註釋，我們可以在其中找到 YAML 工作流程的組件。

```Yaml
#Workflow
name: 90DaysOfDevOps
#Event
on: [push]
#Jobs
jobs:
  check-bats-version:
    #Runners
    runs-on: ubuntu-latest
    #Steps
    steps:
        #Actions
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
        with:
          node-version: '14'
      - run: npm install -g bats
      - run: bats -v
```

### 實際操作 GitHub Actions

我認為當談到 GitHub Actions 時有很多選項，是的，當談到構建、測試和部署代碼以及之後的持續步驟時，它將滿足你的 CI/CD 需求。

我可以看到很多選項和其他自動化任務，我們可以使用 GitHub Actions 來完成。

### 使用 GitHub Actions 進行代碼 Linting

一個選項是確保儲存庫中的代碼乾淨整潔。這將是我們的第一個範例演示。

我將使用本節資源中連結的一些範例代碼，我們將使用 `GitHub/super-linter` 來檢查我們的代碼。

```Yaml
name: Super-Linter

on: push

jobs:
  super-lint:
    name: Lint code base
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Run Super-Linter
        uses: github/super-linter@v3
        env:
          DEFAULT_BRANCH: main
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

**github/super-linter**
你可以從上面看到，對於我們的一個步驟，我們有一個名為 GitHub/super-linter 的操作，這是指向已經由社群編寫的步驟。你可以在這裡找到更多關於此的資訊 [Super-Linter](https://github.com/github/super-linter)

「此儲存庫用於運行 Super-Linter 的 GitHub Action。它是用 bash 編寫的各種 linters 的簡單組合，以幫助驗證源代碼。」

同樣在上面的代碼片段中，它提到了 GITHUB_TOKEN，所以我有興趣找出為什麼以及它做什麼和需要什麼。

「注意：如果你在工作流程中傳遞環境變數 `GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}`，那麼 GitHub Super-Linter 將在 pull request 的 Checks 部分標記每個 linter 運行的狀態。沒有這個，你只會看到完整運行的整體狀態。**不需要設置 GitHub Secret，因為它是由 GitHub 自動設置的，只需要傳遞給操作。**」

粗體文字在這個階段很重要。我們正在使用它，但我們不需要在儲存庫內設置任何環境變數。

我們將使用在 Jenkins 演示中使用的儲存庫進行測試。[Jenkins-HelloWorld](https://github.com/MichaelCade/Jenkins-HelloWorld)

這是我們在 Jenkins 課程中留下的儲存庫。

![](Images/Day75_CICD2.png)

為了利用這一點，我們必須使用上面的 Actions 標籤從市場中選擇，我將很快涵蓋，或者我們可以使用上面的 super-linter 代碼創建我們的檔案，要創建你自己的，你必須在儲存庫中的確切位置創建新檔案。`.github/workflows/workflow_name` 顯然確保 workflow_name 對你來說是有用的識別，在這裡我們可以有很多不同的工作流程對我們的儲存庫執行不同的作業和任務。

我們將創建 `.github/workflows/super-linter.yml`

![](Images/Day75_CICD3.png)

然後我們可以粘貼我們的代碼並將代碼提交到儲存庫，如果我們然後前往 Actions 標籤，我們現在將看到我們的 Super-Linter 工作流程列在下面，

![](Images/Day75_CICD4.png)

我們在代碼中定義，當我們向儲存庫推送任何內容時，此工作流程將運行，因此在將 super-linter.yml 推送到儲存庫時，我們觸發了工作流程。

![](Images/Day75_CICD5.png)

正如你可以從上面看到的，我們有一些錯誤，很可能是我的黑客能力與我的編碼能力相比。

雖然這不是我的代碼，至少還沒有，在運行這個並得到錯誤時，我發現了這個[問題](https://github.com/github/super-linter/issues/2255)

第二次嘗試，我將 Super-Linter 的版本從版本 3 更改為 4，並再次運行了任務。

![](Images/Day75_CICD6.png)

正如預期的那樣，我的黑客編碼帶來了一些問題，你可以在[工作流程](https://github.com/MichaelCade/Jenkins-HelloWorld/runs/5600278515?check_suite_focus=true)中看到它們

我想展示當工作流程中的某些內容失敗或報告錯誤時，我們儲存庫現在的樣子。

![](Images/Day75_CICD7.png)

現在如果我們解決代碼中的問題並推送更改，我們的工作流程將再次運行（你可以從圖像中看到，解決我們的「錯誤」需要一段時間）刪除檔案可能不推薦，但這是顯示問題已解決的非常快速的方法。

![](Images/Day75_CICD8.png)

如果你點擊上面突出顯示的新工作流程按鈕，這將為大量操作打開大門。你可能在整個挑戰中注意到的一件事是，我們不想重新發明輪子，我們想站在巨人的肩膀上，廣泛分享我們的代碼、自動化和技能，使我們的生活更輕鬆。

![](Images/Day75_CICD9.png)

哦，我沒有向你展示當我們的工作流程成功時儲存庫上的綠色勾號。

![](Images/Day75_CICD10.png)

我認為這從基礎角度涵蓋了 GitHub Actions 的內容，但如果你像我一樣，那麼你可能會看到 GitHub Actions 還可以用於自動化許多其他任務。

接下來，我們將涵蓋 CD 的另一個領域，我們將研究 ArgoCD 以將應用程式部署到我們的環境中。

## 資源

- [Jenkins is the way to build, test, deploy](https://youtu.be/_MXtbjwsz3A)
- [Jenkins.io](https://www.jenkins.io/)
- [ArgoCD](https://argo-cd.readthedocs.io/en/stable/)
- [ArgoCD Tutorial for Beginners](https://www.youtube.com/watch?v=MeU5_k9ssrs)
- [What is Jenkins?](https://www.youtube.com/watch?v=LFDrDnKPOTg)
- [Complete Jenkins Tutorial](https://www.youtube.com/watch?v=nCKxl7Q_20I&t=3s)
- [GitHub Actions](https://www.youtube.com/watch?v=R8_veQiYBjI)
- [GitHub Actions CI/CD](https://www.youtube.com/watch?v=mFFXuXjVgkU)

我們[第 76 天](day76.md)見