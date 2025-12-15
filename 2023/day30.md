# 回顧

我們昨天深入設置了 Minikube 中的 Falco。這是一個在運行時檢測應用程式和容器行為的優秀工具。我們將其輸出導出到集群中的 Prometheus 實例，並在專用的 Grafana 儀表板中查看結果。

今天，我們將在 Falco 中設置一些規則和警報，並查看檢測和警報如何工作。

您的咖啡在身邊嗎？您有駭客連帽衫嗎？讓我們開始吧 😈

# 使用 Falco 進行運行時檢測

Falco 是一個強大的開源工具，專為 Kubernetes 運行時安全而設計。以下是 Falco 是保護 Kubernetes 環境的好選擇的一些原因。Falco 提供對 Kubernetes 環境中安全威脅和潛在漏洞的實時檢測。它使用基於規則的引擎來檢測和警報可疑活動，允許您快速響應安全事件。

Falco 允許您創建針對環境特定需求定制的自定義規則。這允許您以適合您獨特需求的方式檢測和響應安全威脅和潛在漏洞。Falco 提供有關安全事件的豐富元數據，包括有關容器、pod、命名空間和其他詳細資訊的資訊。這使得調查和響應安全事件變得容易。

## 使用內建規則檢測惡意事件

此時您應該已經準備好所有移動部件：
* Prometheus
* Grafana
* Falco

讓我們做一些對生產系統來說有點不尋常的事情。我們將在工作負載上打開一個 shell，並在容器的運行時安裝一個套件。

讓我們安裝一個極簡的 Nginx 部署：
```bash
kubectl create deployment nginx --image=nginx:1.19
```

現在在 Nginx 部署的 Pod 內打開一個 shell：
```bash
kubectl exec -it `kubectl get pod | grep nginx | awk '{print $1}'` -- bash
```

並使用 APT 在 Pod 上安裝「curl」：
```bash
apt update && apt install -y curl
```

由於我們使用 Falco 監控應用程式行為，它應該看到所有這些活動，並且確實如此！讓我們回到我們的 Grafana（參見前幾天以了解如何重新連接）。

在 Grafana 中，轉到「explore」屏幕。確保您使用 Prometheus 數據源。

在查詢構建器中選擇指標「falco_events」和標籤過濾器「k8s_pod_name」，並將過濾器設置為您的 Nginx Pod 名稱。

您現在將看到來自此 Pod 的所有 Falco 事件

![](images/day30-1.png)

注意導致事件的規則，其中您會看到「Launch Package Management Process in Container」規則失敗。此事件是由於我們上面的 `apt install` 命令生成的。


請注意這裡以欣賞這裡的潛力。通過安裝這個經過驗證的開源堆疊，您可以創建一個完整的運行時監控系統，並實時了解您想要監控和保護的系統中正在發生的事情！


## 創建自定義規則


假設您或您的安全團隊想知道是否在 Pod 之一中調用了 CLI 工具 `curl`（這在生產集群中應該很少發生，但攻擊者會使用它向自己報告資訊）。

我們需要編寫一個「Falco 規則」來檢測它。

以下是添加自定義 Falco 規則的基本步驟：

### 創建規則
首先，創建一個新的規則檔案，定義您想要檢測的行為。Falco 規則以 YAML 格式編寫，通常包括行為的描述、觸發規則的一組條件，以及觸發規則時生成的輸出消息。

要使用 Falco 規則檢測執行「apt」命令，您可以創建一個具有以下內容的新規則檔案：

```yaml
customRules:
  rules-curl.yaml: |-
    - rule: DetectCurlCommandExecution
      desc: Detects the execution of the "curl" command
      condition: spawned_process and proc.name == curl
      output: "Curl command executed: %proc.cmdline"
      priority: WARNING
```

讓我們深入了解一下我們在這裡有什麼。

Falco 在 Linux 內核中檢測事件並將它們發送到其規則引擎。規則引擎遍歷所有規則並嘗試將它們與事件匹配。如果找到匹配的事件，Falco 本身會觸發基於規則的事件。這些是我們在 Prometheus/Grafana 中看到的條目。在我們的自定義規則中，`condition` 欄位是規則的「心臟」，它用於將規則與事件匹配。

在這種情況下，我們使用了一個名為 `spawned_process` 的宏，如果事件是用戶空間到內核的系統調用以生成新進程（`execve` 和朋友），則它評估為 `true`。第二個條件是新進程的名稱，它匹配 `curl`。

要安裝此新規則，請使用以下 Helm 命令將其添加到我們當前的部署中：
```bash
helm upgrade --install  falco falcosecurity/falco --set driver.kind=ebpf --set-file certs.server.key=$PWD/server.key,certs.server.crt=$PWD/server.crt,certs.ca.crt=$PWD/ca.crt --set falco.grpc.enabled=true,falco.grpcOutput.enabled=true,falco.grpc_output.enabled=true -f <PATH_TO_RULE_YAML>
```

確保 Falco Pod 已重啟並正常運行。

讓我們返回到 Nginx pod 內的 shell。
```bash
kubectl exec -it `kubectl get pod | grep nginx | awk '{print $1}'` -- bash
```

我們之前在這裡安裝了 `curl`，所以我們現在可以調用它並模擬惡意行為。
```bash
curl https://google.com
```

帶有我們新規則的 Falco 應該已經捕獲了此事件，因此您應該返回 Grafana 並檢查 Falco 儀表板：


![](images/day30-2.png)

完成了！

您已經在 Falco 中實現並應用了自定義規則！！！

我希望這一部分讓您深入了解這個系統是如何工作的。

# 接下來

明天我們將遠離應用程式的世界，轉向網路層，到時見！
直到 [Day 31](day31.md)。
