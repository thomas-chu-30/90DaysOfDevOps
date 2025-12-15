# 回顧

昨天我們討論了為什麼監控、日誌記錄和審計是運行時防禦的基礎。簡而言之：如果不了解正在發生的事情，您無法保護實時系統。我們昨天構建了一個帶有 Prometheus 和 Grafana 的 Minikube 集群。我們今天繼續在此堆疊上構建。
讓我們開始 😎

# 應用程式日誌記錄

應用程式日誌從許多角度都很重要。這是運營商了解他們在基礎設施上運行的應用程式內部發生的事情的方式。出於同樣的原因，從安全角度來看，保留應用程式日誌很重要，因為它們提供了系統活動的詳細記錄，可用於檢測和調查安全事件。

通過分析應用程式日誌，安全團隊可以識別異常或可疑活動，例如失敗的登錄嘗試、對敏感數據的訪問嘗試或其他潛在惡意操作。日誌還可以幫助追蹤安全漏洞的來源，包括攻擊者何時以及如何獲得對系統的訪問權限，以及他們在進入後採取了什麼行動。

此外，應用程式日誌可以幫助滿足合規要求，例如與數據保護和隱私相關的要求。通過保留詳細日誌，組織可以證明他們正在採取必要步驟來保護敏感數據並遵守法規。

Loki 是 Grafana 堆疊中的一個組件，它使用 Promtail 收集在 Kubernetes 集群中運行的 Pod 的日誌，並像 Prometheus 對指標一樣存儲它們。

要在集群上安裝帶有 Promtail 的 Loki，請安裝以下 Helm chart。

```bash
helm install loki --namespace=monitoring grafana/loki-stack
```

這將在您的 Minikube 中放置一個 Promtail 和一個 Loki 實例，並開始收集日誌。請注意，此安裝不是生產級別的，這裡是為了演示功能。

您應該看到 Pod 已準備就緒：
```bash
$ kubectl get pods | grep loki
loki-0                                               1/1     Running       0             8m25s
loki-promtail-mpwgq                                  1/1     Running       0             8m25s
```

現在轉到您的 Grafana UI（就像我們昨天做的那樣）：

```bash
kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
minikube service grafana-np --url
```

獲取管理員密碼的秘密（如果您還沒有更改它）並打印服務的 URL，然後轉到 URL 並登錄。

為了在 Grafana 中查看日誌，我們需要將 Loki 掛鉤為「數據源」，就像我們昨天對 Prometheus 所做的那樣。

![](images/day29-1.gif)

現在在這裡添加一個新的 Loki 數據源。

在默認配置中唯一需要更改的是 Loki 服務的端點，在我們的情況下是 http://loki:3100，請參見下文：

![](images/day29-2.png)

現在點擊「Save & test」，您的 Grafana 現在應該已連接到 Loki。

您可以在「Explore」屏幕（點擊左側菜單中的 Explore）中探索您的日誌。

為了嘗試我們的集中式日誌記錄系統，我們將檢查 Etcd 容器在過去一小時內何時進行了壓縮。

在屏幕頂部選擇 Loki 源（探索標題的左側），並從查詢構建器模式（視覺構建器）切換到代碼。

在查詢欄位中添加以下行：
```
{container="etcd"} |= `compaction`
```
然後點擊屏幕右上角的「run query」。

您應該在瀏覽器中看到日誌，如下所示：

![](images/day29-3.png)


完成了！您有一個日誌記錄系統 ;-)


# 應用程式行為監控

我們開始從一般監控需求轉向用於安全目的的低級應用程式監控。現代的方法是使用 eBPF 監控細粒度的應用程式行為。

使用 eBPF（擴展的 Berkeley 數據包過濾器）監控應用程式從安全角度很重要，因為它提供了一種強大而靈活的方式來監控和分析應用程式和底層系統的行為。以下是 eBPF 對應用程式監控和安全很重要的原因：

1. 細粒度監控：eBPF 允許對系統和應用程式活動進行細粒度監控，包括網路流量、系統調用和其他事件。這允許您實時識別和分析安全威脅和潛在漏洞。

2. 相對較低的開銷：eBPF 的開銷非常低，使其非常適合在生產環境中使用。它可以用於監控和分析系統和應用程式行為，而不會影響性能或大規模可靠性。

3. 可自定義的分析：eBPF 允許您創建自定義分析和監控工具，這些工具針對應用程式和環境的特定需求進行定制。這允許您以適合您獨特需求的方式識別和分析安全威脅和潛在漏洞。

4. 實時分析：eBPF 提供實時分析和監控，允許您在安全威脅和潛在漏洞發生時檢測和響應它們。這有助於您最小化安全事件的影響並防止數據丟失或其他負面結果。

Falco 是一個備受尊敬的專案，它在您的 Kubernetes 節點上安裝代理並在 eBPF 級別監控應用程式。

在這一部分，我們將在 Minikube 中安裝 Falco 並將其收集的數據傳送到 Prometheus（最終，Grafana）。這一部分基於這個優秀的 [教程](https://falco.org/blog/falco-kind-prometheus-grafana/)。

為了安裝 Falco，您需要為 Falco 與其導出器之間的客戶端-伺服器通信創建私鑰和證書。

我們將為此使用 `falcoctl`，但是如果您願意，可以使用 `openssl` 生成證書和密鑰。

要安裝 `falcoctl`，請運行以下命令（如果您在 amd64 CPU 上運行 Linux，否則請查看 [這裡](https://github.com/falcosecurity/falcoctl#installation)）：
```bash
LATEST=$(curl -sI https://github.com/falcosecurity/falcoctl/releases/latest | awk '/location: /{gsub("\r","",$2);split($2,v,"/");print substr(v[8],2)}')
curl --fail -LS "https://github.com/falcosecurity/falcoctl/releases/download/v${LATEST}/falcoctl_${LATEST}_linux_amd64.tar.gz" | tar -xz
sudo install -o root -g root -m 0755 falcoctl /usr/local/bin/falcoctl
```

現在生成密鑰對：
```bash
FALCOCTL_NAME=falco-grpc.default.svc.cluster.local FALCOCTL_PATH=$PWD falcoctl tls install
```

我們需要添加 Falco Helm 儲存庫並安裝 Falco 服務和導出器：
```bash
helm repo add falcosecurity https://falcosecurity.github.io/charts
helm repo update
helm install falco falcosecurity/falco --set driver.kind=ebpf --set-file certs.server.key=$PWD/server.key,certs.server.crt=$PWD/server.crt,certs.ca.crt=$PWD/ca.crt --set falco.grpc.enabled=true,falco.grpcOutput.enabled=true,falco.grpc_output.enabled=true
helm install falco-exporter  --set-file certs.ca.crt=$PWD/ca.crt,certs.client.key=$PWD/client.key,certs.client.crt=$PWD/client.crt falcosecurity/falco-exporter
```

確保所有 Falco Pod 運行正常
```bash
$ kubectl get pods  | grep falco
falco-exporter-mlc5h                                 1/1     Running       3 (32m ago)   38m
falco-mlvc4                                          2/2     Running       0             31m
```

由於 Prometheus 自動檢測導出器並且我們已經添加了 Prometheus 數據源，我們可以直接轉到 Grafana 並安裝 [Falco 儀表板](https://grafana.com/grafana/dashboards/11914-falco-dashboard/)。

轉到「Dashboard」左側菜單並點擊導入。在「Import via grfana.com」中插入 ID `11914` 並點擊「load」。

現在您應該在 Grafana 中看到 Falco 事件！😎

![](images/day29-4.png)


# 接下來...

明天我們將研究如何在運行時檢測攻擊。
明天見 😃 [Day 30](day30.md)。
