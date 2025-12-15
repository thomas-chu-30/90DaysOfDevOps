---
title: '#90DaysOfDevOps - Fluentd & FluentBit - 第 81 天'
published: false
description: 90DaysOfDevOps - Fluentd & FluentBit
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048716
---

## Fluentd & FluentBit

我想在本可觀察性部分探索的另一個數據收集器是 [Fluentd](https://docs.fluentd.org/)。一個開源的統一日誌層。

Fluentd 有四個關鍵功能，使其適合構建乾淨、可靠的日誌管道：

使用 JSON 統一日誌：Fluentd 嘗試盡可能將數據結構化為 JSON。這允許 Fluentd 統一處理日誌數據的所有方面：收集、過濾、緩衝和跨多個來源和目標輸出日誌。下游數據處理使用 JSON 要容易得多，因為它有足夠的結構可以訪問，而不會強制使用嚴格的模式。

可插拔架構：Fluentd 有一個靈活的插件系統，允許社群擴展其功能。超過 300 個社群貢獻的插件將數十個數據源連接到數十個數據輸出，根據需要操作數據。通過使用插件，你可以立即更好地利用日誌。

所需的最少資源：數據收集器應該是輕量級的，以便在繁忙的機器上舒適地運行。Fluentd 是用 C 和 Ruby 的組合編寫的，需要最少的系統資源。標準實例在 30-40MB 內存上運行，可以處理每秒/核心 13,000 個事件。

內建可靠性：不應該發生數據丟失。Fluentd 支持基於內存和文件的緩衝，以防止節點間數據丟失。Fluentd 還支持強大的故障轉移，可以設置為高可用性。

[安裝 Fluentd](https://docs.fluentd.org/quickstart#step-1-installing-fluentd)

### 應用程式如何記錄數據？

- 寫入檔案。`.log` 檔案（沒有工具和規模很難分析）
- 直接記錄到資料庫（每個應用程式必須配置正確的格式）
- 第三方應用程式（NodeJS、NGINX、PostgreSQL）

這就是為什麼我們想要一個統一日誌層。

FluentD 允許上述 3 種日誌數據類型，並為我們提供收集、處理和將它們發送到目標的能力，例如將日誌發送到 Elastic、MongoDB 或 Kafka 資料庫。

任何數據、任何數據源都可以發送到 FluentD，並且可以發送到任何目標。FluentD 不綁定到任何特定的來源或目標。

在我對 Fluentd 的研究中，我不斷遇到 Fluent bit 作為另一個選項，看起來如果你想將日誌工具部署到 Kubernetes 環境中，那麼 fluent bit 會為你提供該功能，儘管 fluentd 也可以部署到容器以及伺服器。

[Fluentd & Fluent Bit](https://docs.fluentbit.io/manual/about/fluentd-and-fluent-bit)

Fluentd 和 Fluentbit 將使用輸入插件將該數據轉換為 Fluent Bit 格式，然後我們有輸出插件到任何輸出目標，例如 elasticsearch。

我們還可以在配置之間使用標籤和匹配。

我看不到使用 fluentd 的好理由，似乎 Fluent Bit 是開始的最佳方式。儘管它們可以在某些架構中一起使用。

### Kubernetes 中的 Fluent Bit

Kubernetes 中的 Fluent Bit 作為 DaemonSet 部署，這意味著它將在集群中的每個節點上運行。每個節點上的每個 Fluent Bit Pod 然後將讀取該節點上的每個容器並收集所有可用的日誌。它還將從 Kubernetes API Server 收集元數據。

Kubernetes 註釋可以在應用程式的配置 YAML 中使用。

首先，我們可以從 fluent helm 儲存庫部署。`helm repo add fluent https://fluent.github.io/helm-charts`，然後使用 `helm install fluent-bit fluent/fluent-bit` 指令安裝。

![](Images/Day81_Monitoring1.png)

在我的集群中，我還在我的預設命名空間中運行 Prometheus（用於測試目的），我們需要確保我們的 fluent-bit Pod 啟動並運行。我們可以使用 `kubectl get all | grep fluent` 來執行此操作，這將向我們顯示我們運行的 Pod、服務和我們之前提到的 daemonset。

![](Images/Day81_Monitoring2.png)

為了讓 fluentbit 知道從哪裡獲取日誌，我們有一個配置檔案，在這個 Kubernetes 的 fluentbit 部署中，我們有一個類似配置檔案的 configmap。

![](Images/Day81_Monitoring3.png)

該 ConfigMap 將看起來像：

```
Name:         fluent-bit
Namespace:    default
Labels:       app.kubernetes.io/instance=fluent-bit
              app.kubernetes.io/managed-by=Helm
              app.kubernetes.io/name=fluent-bit
              app.kubernetes.io/version=1.8.14
              helm.sh/chart=fluent-bit-0.19.21
Annotations:  meta.helm.sh/release-name: fluent-bit
              meta.helm.sh/release-namespace: default

Data
====
custom_parsers.conf:
----
[PARSER]
    Name docker_no_time
    Format json
    Time_Keep Off
    Time_Key time
    Time_Format %Y-%m-%dT%H:%M:%S.%L

fluent-bit.conf:
----
[SERVICE]
    Daemon Off
    Flush 1
    Log_Level info
    Parsers_File parsers.conf
    Parsers_File custom_parsers.conf
    HTTP_Server On
    HTTP_Listen 0.0.0.0
    HTTP_Port 2020
    Health_Check On

[INPUT]
    Name tail
    Path /var/log/containers/*.log
    multiline.parser docker, cri
    Tag kube.*
    Mem_Buf_Limit 5MB
    Skip_Long_Lines On

[INPUT]
    Name systemd
    Tag host.*
    Systemd_Filter _SYSTEMD_UNIT=kubelet.service
    Read_From_Tail On

[FILTER]
    Name Kubernetes
    Match kube.*
    Merge_Log On
    Keep_Log Off
    K8S-Logging.Parser On
    K8S-Logging.Exclude On

[OUTPUT]
    Name es
    Match kube.*
    Host elasticsearch-master
    Logstash_Format On
    Retry_Limit False

[OUTPUT]
    Name es
    Match host.*
    Host elasticsearch-master
    Logstash_Format On
    Logstash_Prefix node
    Retry_Limit False

Events:  <none>
```

我們現在可以將 Pod 端口轉發到 localhost 以確保我們有連接性。首先使用 `kubectl get pods | grep fluent` 獲取 Pod 的名稱，然後使用 `kubectl port-forward fluent-bit-8kvl4 2020:2020` 打開網頁瀏覽器到 `http://localhost:2020/`

![](Images/Day81_Monitoring4.png)

我還發現了這篇很棒的中型文章，涵蓋了更多關於 [Fluent Bit](https://medium.com/kubernetes-tutorials/exporting-kubernetes-logs-to-elasticsearch-using-fluent-bit-758e8de606af) 的內容

## 資源

- [Understanding Logging: Containers & Microservices](https://www.youtube.com/watch?v=MMVdkzeQ848)
- [The Importance of Monitoring in DevOps](https://www.devopsonline.co.uk/the-importance-of-monitoring-in-devops/)
- [Understanding Continuous Monitoring in DevOps?](https://medium.com/devopscurry/understanding-continuous-monitoring-in-devops-f6695b004e3b)
- [DevOps Monitoring Tools](https://www.youtube.com/watch?v=Zu53QQuYqJ0)
- [Top 5 - DevOps Monitoring Tools](https://www.youtube.com/watch?v=4t71iv_9t_4)
- [How Prometheus Monitoring works](https://www.youtube.com/watch?v=h4Sl21AKiDg)
- [Introduction to Prometheus monitoring](https://www.youtube.com/watch?v=5o37CGlNLr8)
- [Promql cheat sheet with examples](https://www.containiq.com/post/promql-cheat-sheet-with-examples)
- [Log Management for DevOps | Manage application, server, and cloud logs with Site24x7](https://www.youtube.com/watch?v=J0csO_Shsj0)
- [Log Management what DevOps need to know](https://devops.com/log-management-what-devops-teams-need-to-know/)
- [What is ELK Stack?](https://www.youtube.com/watch?v=4X0WLg05ASw)
- [Fluentd simply explained](https://www.youtube.com/watch?v=5ofsNyHZwWE&t=14s)
- [Fluent Bit explained | Fluent Bit vs Fluentd](https://www.youtube.com/watch?v=B2IS-XS-cc0)

我們[第 82 天](day82.md)見