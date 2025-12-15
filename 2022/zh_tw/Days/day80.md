---
title: '#90DaysOfDevOps - ELK Stack - 第 80 天'
published: false
description: 90DaysOfDevOps - ELK Stack
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048746
---

## ELK Stack

在本節中，我們將對我們提到的一些選項進行更多實際操作。

ELK Stack 是 3 個獨立工具的組合：

- [Elasticsearch](https://www.elastic.co/what-is/elasticsearch) 是一個分佈式、免費和開源的搜索和分析引擎，適用於所有類型的數據，包括文字、數值、地理空間、結構化和非結構化。

- [Logstash](https://www.elastic.co/logstash/) 是一個免費和開源的伺服器端數據處理管道，它從多種來源攝取數據，轉換它，然後將其發送到你最喜歡的「stash」。

- [Kibana](https://www.elastic.co/kibana/) 是一個免費和開源的用戶界面，可讓你可視化 Elasticsearch 數據並導航 Elastic Stack。從跟踪查詢負載到了解請求通過應用程式的方式，無所不能。

ELK stack 讓我們可靠、安全地從任何來源、任何格式獲取數據，然後實時搜索、分析和可視化它。

除了上述組件外，你還可能看到 Beats，這些是安裝在邊緣主機上的輕量級代理，用於收集不同類型的數據以轉發到堆疊中。

- 日誌：需要分析的伺服器日誌被識別

- Logstash：收集日誌和事件數據。它甚至解析和轉換數據

- ElasticSearch：來自 Logstash 的轉換數據被存儲、搜索和索引。

- Kibana 使用 Elasticsearch DB 來探索、可視化和共享

![](Images/Day80_Monitoring8.png)

[圖片取自 Guru99](https://www.guru99.com/elk-stack-tutorial.html)

一個很好的資源解釋了這一點 [Is the Complete Guide to the ELK Stack](https://logz.io/learn/complete-guide-elk-stack/)

隨著 beats 的添加，ELK Stack 現在也被稱為 Elastic Stack。

對於實際操作場景，有很多地方可以部署 Elastic Stack，但我們將使用 docker-compose 在系統上本地部署。

[使用 Docker Compose 啟動 Elastic Stack](https://www.elastic.co/guide/en/elastic-stack-get-started/current/get-started-stack-docker.html#get-started-docker-tls)

![](Images/Day80_Monitoring1.png)

你可以在這裡找到我使用的原始檔案和演練 [deviantony/docker-elk](https://github.com/deviantony/docker-elk)

現在我們可以運行 `docker-compose up -d`，第一次運行將需要拉取圖像。

![](Images/Day80_Monitoring2.png)

如果你跟隨此儲存庫或我使用的儲存庫，你將有密碼「changeme」或在我的儲存庫中密碼為「90DaysOfDevOps」。用戶名是「elastic」

幾分鐘後，我們可以導航到 `http://localhost:5601/`，這是我們的 Kibana 伺服器 / Docker 容器。

![](Images/Day80_Monitoring3.png)

你的初始主螢幕將看起來像這樣。

![](Images/Day80_Monitoring4.png)

在標題為「Get started by adding integrations」的部分下，有一個「try sample data」點擊這個，我們可以添加下面顯示的一個。

![](Images/Day80_Monitoring5.png)

我將選擇「Sample weblogs」，但這真的是為了了解你可以進入 ELK stack 的數據集。

當你選擇「Add Data」時，需要一段時間來填充一些數據，然後你有「View Data」選項和下拉列表中查看該數據的可用方式列表。

![](Images/Day80_Monitoring6.png)

正如儀表板視圖上所述：

**範例日誌數據**

> 此儀表板包含供你使用的範例數據。你可以查看它、搜索它並與可視化交互。有關 Kibana 的更多資訊，請查看我們的文件。

![](Images/Day80_Monitoring7.png)

這使用 Kibana 可視化已通過 Logstash 添加到 ElasticSearch 的數據。這不是唯一的選項，但我想部署並查看這個。

我們將在某個時候涵蓋 Grafana，你將看到兩者之間的一些數據可視化相似性，你還看到了 Prometheus。

我在 Elastic Stack 和 Prometheus + Grafana 之間得到的關鍵要點是 Elastic Stack 或 ELK Stack 專注於日誌，而 Prometheus 專注於指標。

我正在閱讀來自 MetricFire 的這篇文章 [Prometheus vs. ELK](https://www.metricfire.com/blog/prometheus-vs-elk/) 以更好地理解不同的產品。

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

我們[第 81 天](day81.md)見