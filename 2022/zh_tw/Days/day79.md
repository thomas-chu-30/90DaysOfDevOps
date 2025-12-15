---
title: '#90DaysOfDevOps - 概述：日誌管理 - 第 79 天'
published: false
description: 90DaysOfDevOps - 概述日誌管理
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049057
---

## 概述：日誌管理

基礎設施監控挑戰和解決方案的延續，日誌管理是整體可觀察性拼圖的另一個拼圖塊。

### 日誌管理和聚合

讓我們談談兩個核心概念，第一個是日誌聚合，這是一種從許多不同服務收集和標記應用程式日誌並到易於搜索的單一儀表板的方法。

必須在應用程式性能管理系統中構建的第一個系統之一是日誌聚合。應用程式性能管理是 DevOps 生命週期的一部分，其中事物已經構建和部署，你需要確保它們持續工作，因此為它們分配了足夠的資源，並且不會向用戶顯示錯誤。在大多數生產部署中，許多相關事件跨服務發出日誌，在 Google，單個搜索可能在返回給用戶之前命中 10 個不同的服務，如果你得到意外的搜索結果，這可能意味著 10 個服務中任何一個的邏輯問題，日誌聚合幫助像 Google 這樣的公司在生產中診斷問題，他們構建了一個單一儀表板，可以將每個請求映射到唯一 ID，所以如果你搜索某些東西，你的搜索將獲得唯一 ID，然後每次該搜索通過不同的服務時，該服務將該 ID 連接到他們當前正在做的事情。

這就是好的日誌聚合平台的本質，有效地從發出日誌的任何地方收集日誌，並在再次出現故障時使它們易於搜索。

### 範例應用程式

我們的範例應用程式是一個 Web 應用程式，我們有一個典型的前端和後端，在 MongoDB 資料庫中存儲關鍵數據。

如果用戶告訴我們頁面變白並打印錯誤消息，我們將很難用當前的堆疊診斷問題，用戶需要手動向我們發送錯誤，我們需要將其與其他三個服務中的相關日誌匹配。

### ELK

讓我們看一下 ELK，一個流行的開源日誌聚合堆疊，以其三個組件 elasticsearch、logstash 和 kibana 命名，如果我們在與範例應用程式相同的環境中安裝它。

Web 應用程式將連接到前端，然後連接到後端，後端將發送日誌到 logstash，然後這三個組件的工作方式

### elk 的組件

Elasticsearch、logstash 和 Kibana 是所有服務都將日誌發送到 logstash，logstash 獲取這些日誌，這些是應用程式發出的文字。例如，在 Web 應用程式中，當你訪問網頁時，網頁可能會記錄此訪問者在這個時間訪問此頁面，這就是日誌消息的範例，這些日誌將被發送到 logstash。

Logstash 然後會從中提取內容，所以對於該日誌消息，用戶在**時間**做了**事情**。它會提取時間並提取消息並提取用戶，並將這些都包含為標籤，所以消息將是標籤和消息的物件，這樣你就可以輕鬆搜索它們，可以找到特定用戶發出的所有請求，但 logstash 本身不存儲東西，它將東西存儲在 elasticsearch 中，這是一個用於查詢文字的高效資料庫，elasticsearch 將結果公開為 Kibana，Kibana 是一個連接到 elasticsearch 的 Web 伺服器，允許管理員作為 DevOps 人員或團隊中的其他人，待命工程師在生產中每當有重大故障時查看日誌。你作為管理員將連接到 Kibana，Kibana 將查詢 elasticsearch 以獲取與你想要的任何內容匹配的日誌。

你可以說嘿 Kibana 在搜索欄中我想找到錯誤，kibana 會說 elasticsearch 找到包含字串錯誤的消息，然後 elasticsearch 將返回已由 logstash 填充的結果。Logstash 將從所有其他服務發送這些結果。

### 我們如何使用 elk 診斷生產問題

用戶說當我嘗試使用 elk 設置執行此操作時，我看到了錯誤代碼一二三四五六七，我們必須去 kibana 在搜索欄中輸入一二三四五六七，按 Enter，然後這將向我們顯示與該對應的日誌，其中一個日誌可能會說內部伺服器錯誤返回一二三四五六七，我們會看到發出該日誌的服務是後端，我們會看到該日誌發出的時間，所以我們可以去該日誌中的時間，我們可以查看後端中其上下的消息，然後我們可以看到用戶請求發生的事情的更好圖片，我們能夠重複此過程，轉到其他服務，直到我們找到導致用戶問題的原因。

### 安全性和日誌訪問

拼圖的重要部分是確保日誌僅對管理員（或需要訪問的用戶和組）可見，日誌可能包含敏感資訊，如令牌，只有經過身份驗證的用戶才應該訪問它們，你不會想要在沒有某種身份驗證方式的情況下將 Kibana 暴露到網際網路。

### 日誌管理工具範例

日誌管理平台的範例有

- Elasticsearch
- Logstash
- Kibana
- Fluentd - 流行的開源選擇
- Datadog - 託管產品，通常在大型企業中使用，
- LogDNA - 託管產品
- Splunk

雲提供商還提供日誌記錄，如 AWS CloudWatch Logs、Microsoft Azure Monitor 和 Google Cloud Logging。

日誌管理是應用程式和基礎設施環境整體可觀察性的關鍵方面，用於診斷生產中的問題，安裝像 ELK 或 CloudWatch 這樣的交鑰匙解決方案相對簡單，它使診斷和分類生產中的問題變得更加容易。

## 資源

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

我們[第 80 天](day80.md)見