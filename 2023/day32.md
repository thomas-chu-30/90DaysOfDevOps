# 漏洞和補丁管理

漏洞和補丁管理是確保計算機系統和網路安全的關鍵流程。簡單來說，它涉及識別、優先排序和解決軟體和硬體系統中的弱點或缺陷，這些弱點或缺陷可能被攻擊者利用來獲得未授權訪問或執行其他惡意活動。

漏洞可能由於各種原因而出現，例如編碼錯誤、配置錯誤或設計缺陷。一旦漏洞被發現，攻擊者可能會利用它來獲得訪問權限或竊取數據。這就是為什麼識別和優先排序漏洞並採取必要步驟通過應用供應商發布的補丁或更新來緩解它們至關重要。

為了有效管理漏洞和補丁，DevOps 團隊需要建立一個識別和優先排序漏洞的系統。這可以通過使用漏洞掃描器或手動評估來實現。在優先排序漏洞後，團隊需要評估其風險級別並規劃補救措施。

應用補丁或更新來解決漏洞是關鍵步驟，在生產環境中部署之前，在測試環境中測試補丁很重要。這有助於確保補丁不會引入新問題或與其他軟體組件發生衝突。

為了長期維護系統的安全性，持續監控和審查至關重要。這有助於及時識別新漏洞並確保系統保持安全。總而言之，有效的漏洞和補丁管理是 DevOps 工程師強健網路安全策略的關鍵組成部分，有助於確保計算機系統和網路的安全性和完整性。

# 監控漏洞和錯誤配置

漏洞管理的第一步是在運行時期監控當前狀態。

讓我們假設我們一直在使用的技術堆疊：
* Linux/Mac 主機
* Kubernetes (Minikube)
* 容器化應用程式

考慮到這個堆疊，我們需要在主機級別、Kubernetes 組件和配置以及容器級別監控漏洞。

## 主機級別漏洞掃描

有多種解決方案和專案可以幫助您找出主機漏洞。要決定選擇哪一個，您需要了解您的約束和需求。

一些範例包括：
* [AWS Inspector](https://aws.amazon.com/inspector/)，如果您在 AWS EC2 上運行主機，這是一個不錯的選擇。您可以在 AWS 控制台或 API 中開啟它並獲得結果
* [OpenVAS](https://www.openvas.org/) 是一個開源漏洞掃描器，是「後來成為閉源」專案 Nessus 的分支。它是容器化的
* [Vuls](https://vuls.io/) 另一個開源漏洞掃描器和管理系統。您可以將其安裝在集中式組件中，讓它通過 SSH 連接到所有主機並掃描它們。
* [lynis](https://cisofy.com/lynis/) 是一個簡單但強大的單主機掃描器

以下是一個範例，說明如何使用 `lynis` 掃描您的機器。我們將使用套件管理器安裝它：

Ubuntu/Debian：
```bash
sudo apt install lynis
```

RHEL/CentOs：
```bash
sudo yum install lynis
```

現在運行主機審計：
```bash
sudo lynis audit system
```

它將返回您的主機系統報告，其中包含潛在的安全問題：

![](images/day32-1.png)

這對於了解此類工具可以為您提供什麼很有幫助，但我鼓勵您考慮一個更複雜的系統，您需要管理多個主機，最好使用集中式解決方案來概覽整個系統（如上述範例中提到的）。

## Kubernetes 漏洞和錯誤配置

管理 Kubernetes 集群中的漏洞需要多方面的方法。

保持 Kubernetes 及其組件最新很重要：確保 Kubernetes 版本及其組件（如 etcd、kube-apiserver、kubelet 等）與最新的補丁和安全修復保持同步。這可以通過遵循 Kubernetes 發布說明並盡快將集群升級到最新版本來實現。

一般來說，這些組件像任何其他專案一樣在漏洞數據庫中進行追蹤，主機掃描器很可能會找到相關漏洞，因此您應該能夠使用良好的主機掃描器追蹤它們。

審計 Kubernetes 配置很簡單，有多種工具可以使用：
* Kubescape - 用於 Kubernetes 安全的 CNCF 專案，其 CLI 可以即時反饋潛在的安全和合規問題
* Trivy - Aqua Security 的開源掃描器，用於映像、雲端和 Kubernetes 掃描
* Checkov - Bridgecrew 的開源工具，掃描雲端基礎設施和 Kubernetes

我們將看到使用 Kubescape 掃描集群的簡單方法：

```
kubescape installation in macOs M1 and M2 chip error fixed

[kubescape](https://github.com/kubescape/kubescape)

```

```bash
curl -s https://raw.githubusercontent.com/kubescape/kubescape/master/install.sh | /bin/bash
kubescape scan --enable-host-scan --verbose
```

![](https://raw.githubusercontent.com/kubescape/kubescape/master/docs/img/summary.png)

## 應用程式漏洞

掃描應用程式映像是確保整個應用程式堆疊安全的重要方面。應用程式映像是基於容器的部署系統的基本組成部分，它們用於將應用程式及其所有依賴項打包成單個可部署的工件。這些映像通常從公共或私有儲存庫中拉取，可能包含漏洞、錯誤配置或其他可能被攻擊者利用的安全問題。

映像掃描工具可以分析映像中的已知漏洞和錯誤配置，以及其他問題，如不安全的軟體庫、過時的套件或預設密碼。這些工具將映像的內容與已知漏洞數據庫進行比較，並可以提供有關問題嚴重性的資訊。

Trivy 專案有一個運算符，可以監控 Kubernetes 集群中容器中的應用程式漏洞。

安裝它非常簡單：
```bash
helm repo add aqua https://aquasecurity.github.io/helm-charts/
helm repo update
helm install trivy-operator aqua/trivy-operator \
  --namespace trivy-system \
  --create-namespace \
  --set="trivy.ignoreUnfixed=true" \
  --version 0.11.1
```

安裝後，您可以通過 CRD 使用 kubectl 訪問漏洞：
```bash
kubectl get vulnerabilityreports --all-namespaces -o wide
```
![](images/day32-2.png)

[Day 33](day33.md)。
