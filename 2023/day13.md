# Day 13：其他安全編碼實踐

## Git Secret 掃描

掃描儲存庫以查找密鑰是指搜索代碼儲存庫（例如 GitHub 或 GitLab）中可能無意中提交並推送到儲存庫的敏感資訊的過程。這可能包括敏感數據，例如密碼、API 密鑰和私有加密密鑰。

該過程通常使用自動化工具完成，這些工具掃描代碼以查找指示存在敏感資訊的特定模式或關鍵字。此過程的目標是識別並刪除儲存庫中可能已暴露的任何密鑰，以保護免受潛在的違規或未授權訪問。

### 使用 Gitleaks 進行 Git Secret 掃描

Gitleaks 是一個可以作為 GitHub Action 添加到您的 GitHub 儲存庫的工具，它掃描您的代碼庫以查找敏感資訊，例如憑證、令牌和其他密鑰。該操作在您的代碼庫上運行 gitleaks 工具，檢查可能意外提交到您的儲存庫的任何敏感資訊。

要設置 Gitleaks GitHub Action，您需要在儲存庫的 `.github/workflows/git-secret-scan.yml` 目錄中創建一個新的工作流程檔案。工作流程檔案應包含以下內容：

```yaml
name: gitleaks
on:
  pull_request:
  push:
jobs:
  scan:
    name: gitleaks
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
      - uses: gitleaks/gitleaks-action@v2
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```


此工作流程執行以下操作：

1. 定義一個名為 `Dependency-Check` 的工作流程，在每次推送到 `main` 分支時運行。
2. 指定工作流程應在 `ubuntu-latest` 運行器上運行。
3. 為整個儲存庫運行 gitleaks 掃描
4. 如果檢測到任何密鑰，此操作將失敗。

在我的演示中，我在 .env 檔案中添加了 AWS 密鑰，因此管道失敗了。

![](images/day13-1.png)

其他 Git 掃描工具

- [**AWS git-secrets**](https://github.com/awslabs/git-secrets)
- **[GitGuardian ggshield](https://github.com/GitGuardian/ggshield)**
- **[TruffleHog](https://github.com/trufflesecurity/trufflehog)**

### 資源
- [Gitleaks GitHub](https://github.com/zricethezav/gitleaks)
- [Gitleaks GitHub Action](https://github.com/gitleaks/gitleaks-action)
## 使用 Hadolint 創建更好的 Dockerfile

Hadolint 是一個用於 Dockerfile 的 linter，它檢查常見錯誤並提供改進建議。它可以直接從命令行使用，整合到 CI/CD 管道中，或整合到代碼編輯器和 IDE 中以進行實時 linting。

要在 Github Actions 中使用 hadolint 設置 linting，您可以按照以下步驟操作：

1. 在您的儲存庫中創建一個新的工作流程檔案，例如 `.github/workflows/dockerfile-lint.yml`
2. 在此檔案中，添加以下代碼以設置 Github Actions 工作流程：

```yaml
name: Lint Dockerfile
on:
  push:
    branches:
      - main
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2 
      - uses: hadolint/hadolint-action@v2.1.0
        with:
          dockerfile: Dockerfile
```

1. 此工作流程將在每次推送到「main」分支時運行，並將在「Dockerfile」檔案上運行 hadolint 命令。
2. 提交新的工作流程檔案並將其推送到您的儲存庫。
3. 下次您將更改推送到「main」分支時，Github Actions 將運行 linting 作業，如果發現您的 Dockerfile 有任何問題，將提供反饋。

### 資源

- [Hadolint GitHub](https://github.com/hadolint/hadolint)
- [Hadolint Online](https://hadolint.github.io/hadolint/)
- [Top 20 Dockerfile best practices](https://sysdig.com/blog/dockerfile-best-practices/)

接下來，我們將開始我們的 **持續構建、整合、測試**，從 [Day 14](day14.md) 開始，涵蓋來自 [Anton Sankov](https://twitter.com/a_sankov) 的容器映像掃描。
