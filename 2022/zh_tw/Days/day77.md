---
title: '#90DaysOfDevOps - 概述：監控 - 第 77 天'
published: false
description: 90DaysOfDevOps - 概述監控
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048715
---

## 概述：監控

在本節中，我們將討論監控，它是什麼以及為什麼需要它？

### 什麼是監控？

監控是密切關注整個基礎設施的過程

### 為什麼需要它？

讓我們假設我們正在管理一千台伺服器，這些包括各種專業伺服器，如應用程式伺服器、資料庫伺服器和 Web 伺服器。我們還可以通過其他服務和不同平台（包括公共雲產品和 Kubernetes）進一步複雜化這一點。

![](Images/Day77_Monitoring1.png)

我們負責確保伺服器上的所有服務、應用程式和資源都按應有的方式運行。

![](Images/Day77_Monitoring2.png)

我們如何做到這一點？有三種方式：

- 手動登入所有伺服器並檢查有關服務進程和資源的所有數據。
- 編寫一個為我們登入伺服器並檢查數據的腳本。

這兩種選項都需要我們付出相當大的努力，

第三個選項更容易，我們可以使用市場上可用的監控解決方案。

Nagios 和 Zabbix 是可能可用的解決方案，它們允許我們擴展監控基礎設施以包括我們想要的任意數量的伺服器。

### Nagios

Nagios 是一個基礎設施監控工具，由同名公司製造。此工具的開源版本稱為 Nagios core，而商業版本稱為 Nagios XI。[Nagios 網站](https://www.nagios.org/)

該工具允許我們監控伺服器並查看它們是否被充分利用，或者是否有任何需要解決的失敗任務。

![](Images/Day77_Monitoring3.png)

本質上，監控使我們能夠實現這兩個目標，檢查伺服器和服務的狀態，並確定基礎設施的健康狀況，它還為我們提供了完整基礎設施的 40,000 英尺視圖，以查看我們的伺服器是否啟動並運行，應用程式是否正常工作，Web 伺服器是否可達。

它會告訴我們，在特定伺服器上，磁碟在過去 10 週內增加了 10%，它將在接下來的四到五天內完全耗盡，我們很快就會無法響應，當你的磁碟或伺服器處於關鍵狀態時，它會提醒我們，以便我們可以採取適當的措施來避免可能的中斷。

在這種情況下，我們可以釋放一些磁碟空間，確保我們的伺服器不會失敗，我們的用戶不會受到影響。

對於大多數監控工程師來說，困難的問題是我們監控什麼？或者相反，我們不監控什麼？

每個系統都有幾個資源，我們應該密切關注哪些，哪些我們可以視而不見，例如，是否有必要監控 CPU 使用率，答案是肯定的，顯然，這仍然是一個必須做出的決定，是否有必要監控系統中打開的端口數量，我們可能或可能不需要，這取決於情況，如果它是一個通用伺服器，我們可能不需要，但如果它是一個 Web 伺服器，我們可能需要。

### 持續監控

監控不是新項目，即使持續監控也一直是許多企業多年來採用的理想。

當談到監控時，有三個關鍵關注領域。

- 基礎設施監控
- 應用程式監控
- 網路監控

需要注意的重要一點是，有很多可用的工具，我們在本節中提到了兩個通用系統和工具，但還有很多。監控解決方案的真正好處來自於你花時間確保你正在回答我們應該監控什麼和不應該監控什麼的問題。

我們可以在任何平台上開啟監控解決方案，它將開始獲取資訊，但如果該資訊太多，那麼你將難以從該解決方案中受益，你必須花時間配置它。

在下一節中，我們將實際操作監控工具，看看我們可以開始監控什麼。

## 資源

- [The Importance of Monitoring in DevOps](https://www.devopsonline.co.uk/the-importance-of-monitoring-in-devops/)
- [Understanding Continuous Monitoring in DevOps?](https://medium.com/devopscurry/understanding-continuous-monitoring-in-devops-f6695b004e3b)
- [DevOps Monitoring Tools](https://www.youtube.com/watch?v=Zu53QQuYqJ0)
- [Top 5 - DevOps Monitoring Tools](https://www.youtube.com/watch?v=4t71iv_9t_4)
- [How Prometheus Monitoring works](https://www.youtube.com/watch?v=h4Sl21AKiDg)
- [Introduction to Prometheus monitoring](https://www.youtube.com/watch?v=5o37CGlNLr8)

我們[第 78 天](day78.md)見