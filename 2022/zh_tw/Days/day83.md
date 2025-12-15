---
title: '#90DaysOfDevOps - 數據可視化 - Grafana - 第 83 天'
published: false
description: 90DaysOfDevOps - 數據可視化 - Grafana
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048767
---

## 數據可視化 - Grafana

我們在本節中看到了很多 Kibana。但我們也必須花一些時間來涵蓋 Grafana。但它們並不相同，它們也不完全相互競爭。

Kibana 的核心功能是數據查詢和分析。使用各種方法，用戶可以搜索 Elasticsearch 中索引的數據，以查找特定事件或數據中的字串，以進行根本原因分析和診斷。基於這些查詢，用戶可以使用 Kibana 的可視化功能，允許用戶以各種不同的方式可視化數據，使用圖表、表格、地理地圖和其他類型的可視化。

Grafana 最初是 Kibana 的一個分支，Grafana 的目標是提供對指標（也稱為監控）的支持，當時 Kibana 沒有提供。

Grafana 是一個免費的開源數據可視化工具。我們通常看到 Prometheus 和 Grafana 一起在現場，但我們也可能看到 Grafana 與 Elasticsearch 和 Graphite 一起。

這兩個工具之間的關鍵區別是日誌記錄與監控，我們開始本節時使用 Nagios 涵蓋監控，然後進入 Prometheus，然後進入日誌記錄，我們涵蓋了 ELK 和 EFK 堆疊。

Grafana 專注於分析和可視化指標，如系統 CPU、內存、磁碟和 I/O 利用率。該平台不允許全文數據查詢。Kibana 運行在 Elasticsearch 之上，主要用於分析日誌消息。

正如我們已經發現的 Kibana 很容易部署以及有選擇部署位置的選擇，Grafana 也是如此。

兩者都支持在 Linux、Mac、Windows、Docker 上安裝或從源構建。

毫無疑問還有其他的，但 Grafana 是我看到跨越虛擬、雲和雲原生平台的工具，所以我想在本節中涵蓋這個。

### Prometheus Operator + Grafana 部署

我們已經在本節中涵蓋了 Prometheus，但由於我們經常看到這些配對，我想啟動一個環境，至少讓我們可以看到我們可以在可視化中顯示哪些指標。我們知道監控我們的環境很重要，但單獨在 Prometheus 或任何指標工具中查看這些指標將很麻煩，並且不會擴展。這就是 Grafana 的用武之地，為我們提供在 Prometheus 資料庫中收集和存儲的那些指標的交互式可視化。

通過這種可視化，我們可以為環境創建自定義圖表、圖形和警報。在此演練中，我們將使用 minikube 集群。

我們將首先將其克隆到本地系統。使用 `git clone https://github.com/prometheus-operator/kube-prometheus.git` 和 `cd kube-prometheus`

![](Images/Day83_Monitoring1.png)

第一個任務是在 minikube 集群中創建命名空間 `kubectl create -f manifests/setup` 如果你沒有在之前的章節中跟隨，我們可以使用 `minikube start` 在這裡啟動一個新集群。

![](Images/Day83_Monitoring2.png)

接下來，我們將使用 `kubectl create -f manifests/` 指令部署演示所需的所有內容，正如你可以看到的，這將在集群中部署許多不同的資源。

![](Images/Day83_Monitoring3.png)

然後我們需要等待 Pod 啟動並處於運行狀態，我們可以使用 `kubectl get pods -n monitoring -w` 指令來關注 Pod。

![](Images/Day83_Monitoring4.png)

當一切都在運行時，我們可以使用 `kubectl get pods -n monitoring` 指令檢查所有 Pod 都處於運行和健康狀態。

![](Images/Day83_Monitoring5.png)

通過部署，我們部署了幾個服務，我們將在演示的後續部分使用這些服務，你可以使用 `kubectl get svc -n monitoring` 指令檢查這些服務。

![](Images/Day83_Monitoring6.png)

最後，讓我們使用 `kubectl get all -n monitoring` 指令檢查在新監控命名空間中部署的所有資源。

![](Images/Day83_Monitoring7.png)

打開新終端機，我們現在準備訪問 Grafana 工具並開始收集和可視化一些指標，使用的指令是`kubectl --namespace monitoring port-forward svc/grafana 3000`

![](Images/Day83_Monitoring8.png)

打開瀏覽器並導航到 http://localhost:3000，你將被提示輸入用戶名和密碼。

![](Images/Day83_Monitoring9.png)
預設用戶名和密碼是

```
Username: admin
Password: admin
```

但是，你將在首次登入時被要求提供新密碼。你將看到的初始螢幕或主頁將為你提供一些探索領域以及一些有用的資源，以快速了解 Grafana 及其功能。請注意「Add your first data source」和「create your first dashboard」小部件，我們稍後將使用它們。

![](Images/Day83_Monitoring10.png)

你會發現已經有一個 prometheus 數據源已添加到我們的 Grafana 數據源中，但是，因為我們使用 minikube，我們還需要端口轉發 prometheus，以便它在我們的 localhost 上可用，打開新終端機，我們可以運行以下指令。`kubectl --namespace monitoring port-forward svc/prometheus-k8s 9090` 如果在 Grafana 的主頁上，我們現在進入小部件「Add your first data source」，從這裡我們將選擇 Prometheus。

![](Images/Day83_Monitoring11.png)

對於我們的新數據源，我們可以使用地址 http://localhost:9090，我們還需要將下拉列表更改為瀏覽器，如下面突出顯示的。

![](Images/Day83_Monitoring12.png)

在頁面底部，我們現在可以點擊保存並測試。如果 Prometheus 的端口轉發正在工作，這應該會給我們下面看到的結果。

![](Images/Day83_Monitoring13.png)

返回主頁並找到「Create your first dashboard」選項，選擇「Add a new panel」

![](Images/Day83_Monitoring14.png)

你可以從下面看到我們已經從 Grafana 數據源收集，但我們想從 Prometheus 數據源收集指標，選擇數據源下拉列表並選擇我們新創建的「Prometheus-1」

![](Images/Day83_Monitoring15.png)

如果你然後選擇 Metrics browser，你將有一個長長的指標列表，這些指標是從與 minikube 集群相關的 Prometheus 收集的。

![](Images/Day83_Monitoring16.png)

對於演示，我將找到一個為我們提供一些關於系統資源輸出的指標，`cluster:node_cpu:ratio{}` 為我們提供集群中節點的一些詳細資訊，並證明此整合正在工作。

![](Images/Day83_Monitoring17.png)

一旦你對此可視化感到滿意，你可以點擊右上角的應用按鈕，然後你將此圖表添加到儀表板。你可以繼續添加其他圖表和其他圖表，以提供所需的視覺效果。

![](Images/Day83_Monitoring18.png)

但是，我們可以利用數千個以前創建的儀表板，我們可以使用這些儀表板，這樣我們就不需要重新發明輪子。

![](Images/Day83_Monitoring19.png)

如果我們搜索 Kubernetes，我們將看到一個長長的預構建儀表板列表，我們可以從中選擇。

![](Images/Day83_Monitoring20.png)

我們選擇了 Kubernetes API Server 儀表板，並將數據源更改為適合我們新添加的 Prometheus-1 數據源，我們可以看到下面顯示的一些指標。

![](Images/Day83_Monitoring21.png)

### 警報

你還可以利用我們部署的 alertmanager 將警報發送到 slack 或其他整合，為此，你需要使用以下詳細資訊端口轉發 alertmanager 服務。

`kubectl --namespace monitoring port-forward svc/alertmanager-main 9093`
`http://localhost:9093`

這結束了我們關於所有可觀察性事物的部分，我個人發現本節突出了這個主題有多廣泛，但同樣重要的是，這對我們的角色有多重要，無論是指標、日誌記錄還是跟踪，你都需要對我們廣泛環境中發生的事情有一個很好的了解，特別是當它們可以隨著我們在其他部分已經涵蓋的所有自動化而急劇變化時。

接下來，我們將研究數據管理以及 DevOps 原則在數據管理方面也需要考慮的方式。

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

我們[第 84 天](day84.md)見