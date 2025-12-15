---
title: '#90DaysOfDevOps - 實際操作監控工具 - 第 78 天'
published: false
description: 90DaysOfDevOps - 實際操作監控工具
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049056
---

## 實際操作監控工具

在上一節中，我談到了監控的大圖景，我查看了 Nagios，這樣做有兩個原因。第一個是這是我多年來聽說過很多次的軟體，所以想更多地了解它的功能。

今天我將深入了解 Prometheus，我在雲原生領域看到越來越多的 Prometheus，但它也可以用來照顧 Kubernetes 等之外的物理資源。

### Prometheus - 監控幾乎所有東西

首先，Prometheus 是開源的，可以幫助你監控容器和基於微服務的系統，以及物理、虛擬和其他服務。Prometheus 背後有一個大型社群。

Prometheus 有大量的[整合和導出器](https://prometheus.io/docs/instrumenting/exporters/)關鍵是將現有指標導出為 Prometheus 指標。除此之外，它還支持多種程式語言。

拉取方法 - 如果你正在與數千個微服務或系統和服務交談，推送方法通常是你看到服務推送到監控系統的地方。這帶來了一些挑戰，包括網路洪水、高 CPU 以及單點故障。拉取為我們提供了更好的體驗，Prometheus 將從每個服務上的指標端點拉取。

我們再次看到用於 Prometheus 配置的 YAML。

![](Images/Day78_Monitoring7.png)

稍後，你將看到當部署到 Kubernetes 時這看起來如何，特別是我們有**PushGateway**，它從我們的作業/導出器拉取指標。

我們有**AlertManager**，它推送警報，這是我們可以整合到外部服務（如電子郵件、slack 和其他工具）的地方。

然後我們有 Prometheus 伺服器，它管理從 PushGateway 拉取那些拉取指標，然後將那些推送警報發送到 AlertManager。Prometheus 伺服器還在本地磁碟上存儲數據。雖然可以利用遠程儲存解決方案。

然後我們還有 PromQL，這是用於與指標交互的語言，這可以在 Prometheus Web UI 中稍後看到，但你還將在本節稍後看到這如何在數據可視化工具（如 Grafana）中使用。

### 部署 Prometheus 的方法

安裝 Prometheus 的各種方法，[下載部分](https://prometheus.io/download/) Docker 圖像也可用。

`docker run --name prometheus -d -p 127.0.0.1:9090:9090 prom/prometheus`

但我們將專注於部署到 Kubernetes。這也有一些選項。

- 創建配置 YAML 檔案
- 使用 Operator（所有 Prometheus 組件的管理器）
- 使用 helm chart 部署 operator

### 部署到 Kubernetes

我們將再次在本地使用 minikube 集群進行此快速簡單的安裝。與之前與 minikube 的接觸點一樣，我們將使用 helm 部署 Prometheus helm chart。

`helm repo add prometheus-community https://prometheus-community.github.io/helm-charts`

![](Images/Day78_Monitoring1.png)

正如你可以從上面看到的，我們還運行了 helm repo update，我們現在準備使用 `helm install stable prometheus-community/prometheus` 指令將 Prometheus 部署到 minikube 環境中。

![](Images/Day78_Monitoring2.png)

幾分鐘後，你會看到幾個新 Pod 出現，對於這個演示，我已部署到預設命名空間，我通常會將其推送到其命名空間。

![](Images/Day78_Monitoring3.png)

一旦所有 Pod 都在運行，我們還可以查看 Prometheus 的所有已部署方面。

![](Images/Day78_Monitoring4.png)

現在為了訪問 Prometheus Server UI，我們可以使用以下指令進行端口轉發。

```Shell
export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9090
```

當我們第一次打開瀏覽器到 `http://localhost:9090` 時，我們看到以下非常空白的螢幕。

![](Images/Day78_Monitoring5.png)

因為我們已部署到 Kubernetes 集群，我們將自動從 Kubernetes API 獲取指標，所以我們可以使用一些 PromQL 至少確保我們正在捕獲指標 `container_cpu_usage_seconds_total`

![](Images/Day78_Monitoring6.png)

學習 PromQL 並將其付諸實踐的時間很短，這非常像我之前提到的，獲得指標很棒，監控也是如此，但你必須知道你在監控什麼以及為什麼，以及你不監控什麼以及為什麼！

我想回到 Prometheus，但現在，我認為我們需要考慮日誌管理和數據可視化，以便稍後將我們帶回 Prometheus。

## 資源

- [The Importance of Monitoring in DevOps](https://www.devopsonline.co.uk/the-importance-of-monitoring-in-devops/)
- [Understanding Continuous Monitoring in DevOps?](https://medium.com/devopscurry/understanding-continuous-monitoring-in-devops-f6695b004e3b)
- [DevOps Monitoring Tools](https://www.youtube.com/watch?v=Zu53QQuYqJ0)
- [Top 5 - DevOps Monitoring Tools](https://www.youtube.com/watch?v=4t71iv_9t_4)
- [How Prometheus Monitoring works](https://www.youtube.com/watch?v=h4Sl21AKiDg)
- [Introduction to Prometheus monitoring](https://www.youtube.com/watch?v=5o37CGlNLr8)
- [Promql cheat sheet with examples](https://www.containiq.com/post/promql-cheat-sheet-with-examples)

我們[第 79 天](day79.md)見