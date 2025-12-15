# 持續映像儲存庫掃描 - 容器註冊表

昨天我們學習了如何將容器映像漏洞掃描整合到我們的 CI/CD 管道中。

今天，我們將了解如何在另一個層級上強制執行映像掃描 - 容器註冊表。

有些容器註冊表會在您推送容器映像時自動掃描它們。
這確保我們將對團隊生產的每個容器映像的漏洞數量有可見性。

讓我們看一下提供此功能的幾個不同註冊表以及如何使用它。

## Docker Hub

[Docker Hub](https://hub.docker.com/) 是第一個容器註冊表。
它是由創建 Docker 的團隊構建的，今天仍然非常受歡迎。

Docker Hub 具有自動漏洞掃描器，由 [Snyk](https://snyk.io/) 提供支持。

這意味著，如果啟用，當您將映像推送到 Docker Hub 時，它將自動掃描，結果將在 UI 中可見。

您可以從 Docker Hub [文檔](https://docs.docker.com/docker-hub/vulnerability-scanning/) 中了解更多關於如何啟用和使用此功能的信息。

**注意：** 此功能不是免費的。
為了使用它，您需要訂閱。

## Harbor

[Harbor](https://goharbor.io/) 是一個開源容器註冊表。
最初在 VMware 開發，現在是 CNCF 的一部分。

它通過 [Trivy](https://github.com/aquasecurity/trivy) 和/或 [Clair](https://github.com/quay/clair) 支持映像掃描。

這在安裝期間配置。
（即使您在安裝期間不啟用映像掃描，也可以稍後配置）。
有關更多資訊，請查看 [文檔](https://goharbor.io/docs/2.0.0/administration/vulnerability-scanning/)。

## AWS ECR

[AWS ECR](https://aws.amazon.com/ecr/) 還支持 [通過 Clair 進行映像掃描](https://docs.aws.amazon.com/AmazonECR/latest/userguide/image-scanning-basic.html)。

## Azure Container Registry

[Azure Container Registry](https://azure.microsoft.com/en-us/products/container-registry) 支持 [通過 Qualys 進行映像掃描](https://azure.microsoft.com/en-us/updates/vulnerability-scanning-for-images-in-azure-container-registry-is-now-generally-available/)。

## GCP

[GCP Container Registry](https://cloud.google.com/container-registry) 還支持 [自動映像掃描](https://cloud.google.com/container-analysis/docs/automated-scanning-howto)。

## 策略強制執行

僅僅掃描映像並在註冊表中可見結果是一件好事，
但如果我們有辦法為這些映像強制執行一些標準會更好。

在 [Day 14](day14.md) 中，我們看到了如何使 `grype` 在映像具有高於某個嚴重性的漏洞時使掃描失敗。

類似的東西也可以在容器註冊表級別強制執行。

例如，[Harbor](https://goharbor.io/) 有 **防止易受攻擊的映像運行** 選項，當啟用時不允許您拉取具有高於某個嚴重性的漏洞的映像。
如果您無法拉取映像，您就無法運行它，因此如果您不想運行易受攻擊的映像，這是一個好規則。
當然，這樣的規則可以有效地阻止您將某些東西部署到您的環境中，因此您需要謹慎使用它。

有關此選項的更多資訊以及如何在 Harbor 中啟用它，您可以在此處閱讀 [here](https://goharbor.io/docs/2.3.0/working-with-projects/project-configuration/)。

對於更細粒度的控制和解除部署阻塞，您可以配置 [每個專案的 CVE 允許列表](https://goharbor.io/docs/2.3.0/working-with-projects/project-configuration/configure-project-allowlist/)。
這將允許某些映像運行，即使它們有漏洞。
然而，這些漏洞將由儲存庫管理員手動策劃和允許列表。

## 總結

掃描容器映像並對其中的漏洞數量有可見性對於安全 SDLC 至關重要。

一個地方是在您的 CI 管道中（如 [Day 21](day21.md) 中所示）。

另一個地方是您的容器註冊表（如今天所見）。

兩者都是好選項，兩者都有其優缺點。
由 DevSecOps 架構師決定哪種方法更適合他們及其威脅模型。
在 [Day 23](day23.md) 見。
