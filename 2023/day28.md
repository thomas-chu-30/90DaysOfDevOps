# 運行時防禦和監控介紹

歡迎所有 DevOps 和 DevSecOps 愛好者！🙌

我們在這裡學習「運行時防禦」。這是一個巨大的主題，但我們不會被它嚇倒，將在接下來的 7 天中一起學習它。

![](images/day28-0.png)

這個主題被分為主要部分：
* 監控（第 1 天和第 2 天）
* 入侵檢測
* 網路防禦
* 訪問控制
* 應用程式防禦主題（第 6 天和第 7 天）

目標是讓您在這些主題中達到一個水平，您可以開始自己工作。

讓我們開始 😎

# 系統監控和審計

## 為什麼這是「運行時防禦和監控」主題的第一個主題？

監控電腦系統是安全團隊的基本工具，提供對系統內發生的事情的可見性。沒有監控，安全團隊將無法檢測和響應安全事件。

為了說明這一點，考慮物理安全。如果您想保護建築物，您必須在每個入口處 24/7 都有安全人員來控制誰進入建築物。在這個相同的示例中，您還負責控制建築物中每個人的安全性，因此您還必須在周圍有人員。當然，這不能很好地擴展，因此在關鍵位置安裝 CCTV 攝像頭是今天更好的解決方案。

雖然擴展此類物理安全措施很困難，但對於電腦系統，通過安裝監控工具更容易實現。監控提供了對系統的基本控制水平，允許安全團隊檢測問題、理解攻擊模式並維護整體安全性。除了監控之外，還有其他安全措施，如檢測系統，我們可以進一步討論。

詳細說明這一點，以下是監控對運行時安全很重要的關鍵原因：

* 識別安全事件：監控可以幫助組織檢測潛在的安全事件，如惡意軟體感染、未授權訪問嘗試和數據洩露。

* 緩解風險：通過監控安全威脅的跡象，組織可以在它們導致違規或其他安全事件之前採取行動來緩解這些風險。

* 遵守法規：許多行業都受到監管要求的約束，這些要求要求某些安全控制，包括監控和事件響應。

* 改善事件響應：監控提供快速識別和響應安全事件所需的數據，減少違規的影響並允許組織更快地恢復。

* 獲得可見性：監控提供對系統活動的洞察，可用於優化性能、排除故障並識別改進機會。

## 監控和記錄什麼？

理論上，理想的解決方案是記錄系統中發生的所有事情並永遠保存數據。

然而，這不實用。讓我們看看需要監控什麼以及需要記錄什麼事件。

在監控基於雲的電腦服務時，有幾個關鍵組件應該密切監控，以確保系統安全並正常運行。這些組件包括：

控制平面日誌記錄：所有基礎設施的編排都通過此控制平面，始終了解誰在基礎設施級別做了什麼至關重要。它不僅能夠識別惡意活動，還能夠排除系統故障。

操作級別日誌：記錄操作系統級別事件以跟踪系統活動並檢測任何錯誤或安全相關事件，例如失敗的登錄嘗試或系統更改。更深入的日誌包含有關哪個用戶在機器級別做什麼的資訊，這對於識別惡意行為很重要。

網路活動：監控網路流量以識別任何可能表明攻擊或網路妥協的異常或未授權活動。

應用程式活動和性能：監控應用程式活動以檢測攻擊來自應用程式級別時的異常行為。性能監控對於確保服務順利運行並響應可能出現的任何性能問題很重要。

資源利用率：監控系統資源（如 CPU、內存和磁盤空間）的使用情況，以識別瓶頸或其他性能問題。異常活動可能是拒絕服務類攻擊或攻擊者使用計算資源為自己服務的結果。

安全配置：監控安全配置，如防火牆規則和用戶訪問控制，以確保它們正確配置和強制執行。

備份和災難恢復系統：監控備份和災難恢復系統，以確保它們正常運行，並且在發生故障或災難時可以恢復數據。

## 實際實現

在這一部分，我們從理論轉向實踐。

這裡沒有萬能的解決方案，每個系統都有自己的工具。我們將使用 Kubernetes 作為基礎設施，使用 [Microservices demo](https://github.com/GoogleCloudPlatform/microservices-demo) 應用程式。

### 控制平面監控

Kubernetes 有一個稱為 [audit logs](https://kubernetes.io/docs/tasks/debug/debug-cluster/audit/) 的事件審計基礎設施。

Kubernetes API 伺服器有一個稱為 `Audit Policy` 的配置，它告訴 API 伺服器要記錄什麼。日誌可以存儲在檔案中或發送到 webhook。

我們在示例中使用 Minikube，為了測試這個，我們將審計日誌發送到 API 伺服器的 `stdout`（這是它的日誌）。

```bash
mkdir -p ~/.minikube/files/etc/ssl/certs
cat <<EOF > ~/.minikube/files/etc/ssl/certs/audit-policy.yaml
# Log all requests at the Metadata level.
apiVersion: audit.k8s.io/v1
kind: Policy
rules:
- level: RequestResponse
EOF
minikube start --extra-config=apiserver.audit-policy-file=/etc/ssl/certs/audit-policy.yaml --extra-config=apiserver.audit-log-path=-
```

您可以使用此 Kubectl 命令跟隨日誌：
```bash
kubectl logs kube-apiserver-minikube -n kube-system | grep audit.k8s.io/v1
```

每個 API 操作都記錄到流中。

這是一個事件「在默認命名空間中獲取所有密鑰」的示例：
```json
{"kind":"Event","apiVersion":"audit.k8s.io/v1","level":"RequestResponse","auditID":"8e526e77-1fd9-43c3-9714-367fde233c99","stage":"RequestReceived","requestURI":"/api/v1/namespaces/default/secrets?limit=500","verb":"list","user":{"username":"minikube-user","groups":["system:masters","system:authenticated"]},"sourceIPs":["192.168.49.1"],"userAgent":"kubectl/v1.25.4 (linux/amd64) kubernetes/872a965","objectRef":{"resource":"secrets","namespace":"default","apiVersion":"v1"},"requestReceivedTimestamp":"2023-02-11T20:34:11.015389Z","stageTimestamp":"2023-02-11T20:34:11.015389Z"}
```

正如您所看到的，基礎設施請求的所有關鍵方面都記錄在這裡（誰、什麼、何時）。

將其存儲在檔案中不實用。審計日誌通常被運送到日誌記錄系統和數據庫以供以後使用。託管 Kubernetes 服務使用自己的「雲日誌記錄」服務來捕獲 Kubernetes 審計日誌。在原生 Kubernetes 中，您可以使用 Promtail 將日誌運送到 Prometheus，如 [這裡](https://www.bionconsulting.com/blog/monitoring-and-gathering-metrics-from-kubernetes-auditlogs) 所述。

### 資源監控

Kubernetes 生態系統支持多種監控資源和日誌記錄的方式，然而，最常見的示例是 Prometheus（日誌記錄和事件數據庫）和 Grafana（UI 和儀表板）。這兩個開源工具是圍繞監控的多個任務的簡單一站式商店。

開箱即用，我們將獲得資源監控 Kubernetes 節點。

這是我們在上一部分啟動的 Minikube 上安裝它的方式。確保，您之前已安裝 `helm`。

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana https://grafana.github.io/helm-charts
helm install prometheus prometheus-community/prometheus
helm install grafana grafana/grafana
kubectl expose service grafana --type=NodePort --target-port=3000 --name=grafana-np
```

現在，這些服務應該已安裝。

要訪問 Grafana UI，首先獲取第一個密碼

```bash
kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

然後登錄到 UI
```bash
minikube service grafana-np --url
```

![](images/day28-1.png)

登錄後，轉到「Data sources/Prometheus」並將我們的 Prometheus 服務添加為源。URL 必須設置為 `http://prometheus-server`，然後點擊「save & test」。

現在，要設置資源儀表板，轉到「Dashboards」側邊菜單並選擇「Import」。在這裡您可以導入預製儀表板。例如，可以通過在 `Import via grafana.com` 欄位中輸入數字 `6126` 並點擊 `Load` 按鈕來導入節點指標。

![](images/day28-2.png)

在此處瀏覽 Grafana 以獲取更多儀表板 [here](https://grafana.com/grafana/dashboards/)。

# 接下來...

明天我們將繼續到應用程式級別。應用程式日誌和行為監控將成為焦點。我們將繼續使用相同的設置並更深入地進入兔子洞 😄
在 [Day 29](day29.md) 見。
