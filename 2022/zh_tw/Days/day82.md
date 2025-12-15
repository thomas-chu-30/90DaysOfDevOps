---
title: '#90DaysOfDevOps - EFK Stack - 第 82 天'
published: false
description: 90DaysOfDevOps - EFK Stack
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049059
---

### EFK Stack

在上一節中，我們談到了 ELK Stack，它使用 Logstash 作為堆疊中的日誌收集器，在 EFK Stack 中，我們將其替換為 FluentD 或 FluentBit。

我們在本節中的任務是使用 EFK 監控我們的 Kubernetes 日誌。

### EFK 概述

我們將把以下內容部署到 Kubernetes 集群中。

![](Images/Day82_Monitoring1.png)

EFK 堆疊是 3 個軟體捆綁在一起的集合，包括：

- Elasticsearch：NoSQL 資料庫用於存儲數據並提供搜索和查詢日誌的界面。

- Fluentd：Fluentd 是用於統一日誌層的開源數據收集器。Fluentd 允許你統一數據收集和消費，以更好地使用和理解數據。

- Kibana：用於管理和統計日誌的界面。負責從 elasticsearch 讀取資訊。

### 在 Minikube 上部署 EFK

我們將使用值得信賴的 minikube 集群來部署 EFK 堆疊。讓我們在系統上使用 `minikube start` 啟動集群。我使用的是啟用了 WSL2 的 Windows OS。

![](Images/Day82_Monitoring2.png)

我創建了 [efk-stack.yaml](Days/Monitoring/../../Monitoring/EFK%20Stack/efk-stack.yaml)，其中包含我們需要將 EFK 堆疊部署到集群中的所有內容，使用 `kubectl create -f efk-stack.yaml` 指令，我們可以看到所有內容正在部署。

![](Images/Day82_Monitoring3.png)

根據你的系統以及你是否已經運行過此操作並已拉取圖像，你現在應該在繼續之前觀察 Pod 進入就緒狀態，你可以使用以下指令檢查進度。`kubectl get pods -n kube-logging -w` 這可能需要幾分鐘。

![](Images/Day82_Monitoring4.png)

上面的指令讓我們可以關注事情，但我喜歡通過運行以下 `kubectl get pods -n kube-logging` 指令來澄清一切正常，以確保所有 Pod 現在都啟動並運行。

![](Images/Day82_Monitoring5.png)

一旦我們所有的 Pod 都啟動並運行，在這個階段，我們應該看到

- 3 個與 ElasticSearch 相關的 Pod
- 1 個與 Fluentd 相關的 Pod
- 1 個與 Kibana 相關的 Pod

我們還可以使用 `kubectl get all -n kube-logging` 來顯示命名空間中的所有內容，如前所述，fluentd 作為 daemonset 部署，kibana 作為 deployment，Elasticsearch 作為 statefulset。

![](Images/Day82_Monitoring6.png)

現在我們所有的 Pod 都啟動並運行了，我們現在可以在新終端機中發出端口轉發指令，以便我們可以訪問 kibana 儀表板。請注意，你的 Pod 名稱將與我們在這裡看到的指令不同。`kubectl port-forward kibana-84cf7f59c-v2l8v 5601:5601 -n kube-logging`

![](Images/Day82_Monitoring7.png)

我們現在可以打開瀏覽器並導航到此地址，`http://localhost:5601` 你將看到下面看到的螢幕，或者你可能確實看到範例數據螢幕或繼續並自己配置。無論哪種方式，都可以查看該測試數據，這是我們在上一節中查看 ELK 堆疊時涵蓋的內容。

![](Images/Day82_Monitoring8.png)

接下來，我們需要點擊左側菜單上的「discover」標籤，並將「\*」添加到我們的索引模式。通過點擊「Next step」繼續下一步。

![](Images/Day82_Monitoring9.png)

在 2 步中的第 2 步，我們將從下拉列表中選擇 @timestamp 選項，因為這將按時間過濾我們的數據。當你點擊創建模式時，可能需要幾秒鐘才能完成。

![](Images/Day82_Monitoring10.png)

如果我們現在在幾秒鐘後返回「discover」標籤，你應該開始看到來自 Kubernetes 集群的數據。

![](Images/Day82_Monitoring11.png)

現在我們已經啟動並運行了 EFK 堆疊，並且我們通過 Fluentd 從 Kubernetes 集群收集日誌，我們還可以查看如果通過點擊左上角的 Kibana 徽標導航到主螢幕，我們可以選擇的其他來源，你將看到與我們首次登入時看到的相同頁面。

我們可以從其他插件或來源添加 APM、日誌數據、指標數據和安全事件。

![](Images/Day82_Monitoring12.png)

如果我們選擇「Add log data」，那麼我們可以看到下面我們有很多關於從哪裡獲取日誌的選擇，你可以看到那裡提到了 Logstash，它是 ELK 堆疊的一部分。

![](Images/Day82_Monitoring13.png)

在指標數據下，你會發現你可以為 Prometheus 和許多其他服務添加來源。

### APM（應用程式性能監控）

還有收集 APM（應用程式性能監控）的選項，它從應用程式內部收集深入的性能指標和錯誤。它允許你實時監控數千個應用程式的性能。

我不會在這裡深入探討 APM，但你可以在 [Elastic 網站](https://www.elastic.co/observability/application-performance-monitoring)上找到更多資訊

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

我們[第 83 天](day83.md)見