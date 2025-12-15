# Day 9：使用 SonarCloud 實現 SAST

SonarCloud 是一個基於雲的平台，提供靜態代碼分析，幫助開發人員在他們的專案中查找和修復代碼品質問題。它設計用於與各種編程語言和工具配合使用，包括 Java、C#、JavaScript 等。

SonarCloud 提供一系列功能來幫助開發人員改善代碼品質，包括：

- **靜態代碼分析**：SonarCloud 分析專案的源代碼，檢查諸如編碼風格違規、潛在錯誤、安全漏洞和其他問題。它為開發人員提供詳細的問題報告，以及如何修復它們的建議。
- **代碼審查**：SonarCloud 與代碼審查工具（如 GitHub pull requests）整合，允許開發人員在代碼合併到主分支之前從同行那裡獲得反饋。這有助於在開發過程早期發現問題，降低錯誤和其他問題進入生產環境的風險。
- **持續整合**：SonarCloud 可以整合到持續整合（CI）管道中，允許它在每次代碼提交時自動運行靜態代碼分析。這有助於開發人員及早發現問題並快速修復，改善代碼庫的整體品質。
- **協作**：SonarCloud 包括團隊協作工具，例如將問題分配給特定團隊成員並跟踪代碼審查和問題解決進度的能力。
- **自定義**：SonarCloud 允許開發人員自定義用於靜態代碼分析的規則和配置，以便他們可以根據團隊的特定需求和編碼標準定制分析。

總的來說，SonarCloud 對於希望改善代碼品質並降低問題進入生產環境風險的開發人員來說是一個有價值的工具。它幫助團隊協作並在開發過程早期發現問題，從而實現更快、更高效的開發，並在最終產品中減少錯誤。

在此處閱讀更多關於 SonarCloud 的資訊 [here](https://docs.sonarcloud.io/)

### 將 SonarCloud 與 GitHub Actions 整合

- 使用您的 GitHub 帳戶註冊 [SonarCloud](https://sonarcloud.io/) 帳戶。
- 從儀表板，點擊「從 GitHub 導入組織」
    
![](images/day09-1.png)
    
- 授權並安裝 SonarCloud 應用程式以訪問您的 GitHub 帳戶。
    
![](images/day09-2.png)
    
- 選擇您要分析的儲存庫（免費層僅支持公共儲存庫），然後點擊「安裝」
![](images/day09-3.png)
    
- 現在您可以在 SonarCloud 中創建組織。

![](images/day09-4.png)
![](images/day09-5.png)

- 現在點擊「分析新專案」

![](images/day09-6.png)

- 點擊設置以添加專案。

![](images/day09-7.png)

- 現在在 SonarCloud 儀表板上您可以看到專案。

![](images/day09-8.png)

- 要設置 GitHub Actions，點擊專案，然後點擊 **Information** > **Last analysis method**

![](images/day09-9.png)

- 點擊 **GitHub Actions**

![](images/day09-10.png)

- 這將顯示一些將 SonarCloud 與 GitHub actions 整合的步驟。在頂部您會看到 SONAR_TOKEN，我們稍後會將其添加為 Github Secrets。

![](images/day09-11.png)

- 接下來您會看到 GitHub Workflow 的 yaml 文件

![](images/day09-12.png)

- 您還會看到一個配置文件，我們必須將其添加到源代碼儲存庫中

![](images/day09-13.png)
![](images/day09-14.png)

- 在頁面底部，禁用自動分析
![](images/day09-15.png)

- 現在轉到源代碼儲存庫，在根目錄中添加以下配置 `sonar-project.properties`。

```yaml
sonar.projectKey=prateekjaindev_nodejs-todo-app-demo
sonar.organization=prateekjaindev

# This is the name and version displayed in the SonarCloud UI.
#sonar.projectName=nodejs-todo-app-demo
#sonar.projectVersion=1.0

# Path is relative to the sonar-project.properties file. Replace "\" by "/" on Windows.
#sonar.sources=.

# Encoding of the source code. Default is default system encoding
#sonar.sourceEncoding=UTF-8
```

- 在 `.github/workflows` 目錄中更新或添加具有以下作業的 GitHub actions 工作流程

```yaml
name: SonarScan
on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened]
jobs:
  sonarcloud:
    name: SonarCloud
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 0  # Shallow clones should be disabled for a better relevancy of analysis
      - name: SonarCloud Scan
        uses: SonarSource/sonarcloud-github-action@master
        env:
           GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}  # Needed to get PR information, if any
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
```
- 現在轉到 GitHub 並添加名為 SOANR_TOKEN 的 GitHub Secret。
![](images/day09-16.png)
- 一旦您提交更改，工作流程就會觸發。
![](images/day09-17.png)
- 現在在每次提交後，您可以在 SonarCloud 儀表板上檢查更新的報告。
![](images/day09-18.png)

### 品質閘道

品質閘道是一個指標，告訴您您的代碼是否滿足專案所需的最低品質水準。它由一組應用於每次分析結果的條件組成。如果分析結果滿足或超過品質閘道條件，則顯示 **Passed** 狀態，否則顯示 **Failed** 狀態。

默認情況下，SonarCloud 帶有一個默認品質閘道「Sonar way」。您可以在組織設置中編輯或創建新的。
![](images/day09-19.png)
### 資源

- [SonarCloud Documentation](https://docs.sonarcloud.io/)
- [How to create Quality gates on SonarQube](https://www.youtube.com/watch?v=8_Xt9vchlpY)
- [Source Code of the repo I used for SAST implementation](https://github.com/prateekjaindev/nodejs-todo-app-demo)

在下一部分 [Day 10](day10.md) 中，我們將討論軟體組合分析（SCA）。
