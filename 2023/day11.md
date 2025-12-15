# Day 11：使用 OWASP Dependency Check 實現 SCA

### OWASP Dependency Check

OWASP Dependency Check 是一個開源工具，用於檢查專案依賴項中的已知漏洞。它可用於識別具有已知漏洞的依賴項，並確定應用程式中是否暴露了任何這些漏洞。

該工具通過掃描專案的依賴項並與已知漏洞數據庫進行比對來工作。如果發現漏洞，該工具將報告漏洞以及相關的 CVE（通用漏洞和暴露）標識符，這是公開已知網路安全漏洞的標準化標識符。

要使用 OWASP Dependency Check，您需要將其作為構建過程的一部分。有多種構建工具的整合可用，包括 Maven、Gradle 和 Ant。您也可以使用命令行界面來掃描您的依賴項。

OWASP Dependency Check 對於識別應用程式依賴的第三方庫和框架中的漏洞特別有用。如果這些類型的依賴項沒有得到適當管理，它們可能會將漏洞引入您的應用程式。通過定期掃描您的依賴項，您可以確保了解任何漏洞並採取措施解決它們。

重要的是要注意，OWASP Dependency Check 不能替代安全編碼實踐，應與其他安全措施一起使用。定期更新依賴項也很重要，以確保您使用的是最安全的可用版本。

### 將 Dependency Check 與 GitHub Actions 整合

要將 Dependency Check 與 GitHub Actions 一起使用，您可以在儲存庫的 `.github/workflows` 目錄中創建一個工作流程檔案。以下是一個示例工作流程，在每次推送到 `main` 分支時運行 Dependency Check：

```yaml
name: Dependency-Check
on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened]
jobs:
  dependency-check:
    name: Dependency-Check
    runs-on: ubuntu-latest
    steps: 
      - name: Download OWASP Dependency Check
        run: |
          VERSION=$(curl -s https://jeremylong.github.io/DependencyCheck/current.txt)
          curl -sL "https://github.com/jeremylong/DependencyCheck/releases/download/v$VERSION/dependency-check-$VERSION-release.zip" --output dependency-check.zip
          unzip dependency-check.zip
      - name: Run Dependency Check
        run: |
          ./dependency-check/bin/dependency-check.sh --out report.html --scan .
          rm -rf dependency-check*

      - name: Upload Artifacts
        uses: actions/upload-artifact@v2
        with:
          name: artifacts
          path: report.html
```

此工作流程執行以下操作：

1. 定義一個名為 `Dependency-Check` 的工作流程，在每次推送到 `main` 分支時運行。
2. 指定工作流程應在 `ubuntu-latest` 運行器上運行。
3. 下載並安裝 Dependency Check。
4. 在當前目錄（`.`）上運行 Dependency Check，並在 report.html 檔案中生成報告。
5. 刪除下載的 Dependency Check 檔案。
6. 將報告檔案上傳為工件。

您可以從 Artifacts 下載報告並在瀏覽器中打開它。

![](images/day11-1.png)

您可以自定義此工作流程以滿足您的需求。例如，您可以指定不同的分支來運行工作流程，或指定不同的依賴項進行檢查。您還可以配置 Dependency Check 以特定格式（例如 HTML、XML、JSON）生成報告並將其保存到儲存庫。

### 資源

- [Dependency Check Documentation](https://jeremylong.github.io/DependencyCheck/)
- [Source Code of the repo I used for SCA implementation](https://github.com/prateekjaindev/nodejs-todo-app-demo)

在下一部分 [Day 12](day12.md) 中，我們將討論安全代碼審查。
