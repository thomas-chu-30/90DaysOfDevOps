## 大局觀：DevSecOps

歡迎來到 2023 年版的第 2 天，在這第一個模組的接下來 6 天中，我們將探討 DevSecOps 的基礎概述。

### 什麼是 DevSecOps？

DevSecOps 是一種軟體開發方法，旨在將開發、安全和運營團隊聚集在一起，以構建和維護安全的軟體應用程式。它基於持續整合、持續交付和持續部署的原則，旨在更快、更頻繁地交付軟體更新和功能。在 DevSecOps 中，安全是軟體開發過程的組成部分，而不是事後的想法。這意味著安全測試、監控和其他安全措施從一開始就內建在軟體開發生命週期（SDLC）中，而不是稍後添加。DevSecOps 旨在改善開發、安全和運營團隊之間的協作和溝通，以創建更高效、更有效的軟體開發過程。

### DevSecOps vs DevOps

我在這裡輕描淡寫地使用「vs」，但如果我們回顧 2022 年版，DevOps 的目標是提高軟體發布的速度、可靠性和品質。

DevSecOps 是 DevOps 理念的延伸，強調將安全實踐整合到軟體開發過程中。DevSecOps 的目標是將安全措施內建到軟體開發過程中，使安全從一開始就是軟體的組成部分，而不是事後的想法。這有助於降低將安全漏洞引入軟體的風險，並使識別和修復任何出現的問題變得更容易。

DevOps 專注於改善開發人員和運營人員之間的協作和溝通，以提高軟體發布的速度、可靠性和品質，而 DevSecOps 專注於將安全實踐整合到軟體開發過程中，以降低安全漏洞的風險並提高軟體的整體安全性。

### 自動化安全

自動化安全是指使用技術來執行安全任務，而無需人工干預。這可以包括監控網路威脅並採取行動阻止它們的安全軟體，或使用人工智慧分析安全錄影並識別異常活動的系統。自動化安全系統旨在使安全流程更高效、更有效，並有助於減少安全人員的工作量。

DevSecOps 所有內容的關鍵組成部分是能夠自動化創建和交付軟體時的許多任務，當我們從一開始就添加安全時，這意味著我們還需要考慮安全的自動化方面。

### 規模化安全（容器和微服務）

我們知道，容器化和微服務所實現的規模和動態基礎設施已經改變了大多數組織的業務方式。

這也是為什麼我們必須將自動化安全帶入我們的 DevOps 原則，以確保滿足特定的容器安全準則。

我的意思是，對於雲原生技術，我們不能只有靜態安全策略和態勢；我們的安全模型也必須與手頭的工作負載以及它的運行方式保持動態。

DevOps 團隊需要包括自動化安全來保護整體環境和數據，以及持續整合和持續交付流程。

以下列表取自 [RedHat 部落格文章](https://www.redhat.com/en/topics/devops/what-is-devsecops)

- 標準化和自動化環境：每個服務都應該具有盡可能少的權限，以最小化未授權的連接和訪問。

- 集中化用戶身份和訪問控制功能：緊密的訪問控制和集中化身份驗證機制對於保護微服務至關重要，因為身份驗證在多個點啟動。

- 將運行微服務的容器彼此隔離並與網路隔離：這包括傳輸中和靜態數據，因為兩者都可能成為攻擊者的高價值目標。

- 加密應用程式和服務之間的數據：具有整合安全功能的容器編排平台有助於最小化未授權訪問的機會。

- 引入安全 API 網關：安全 API 增加了授權和路由可見性。通過減少暴露的 API，組織可以減少攻擊面。

### 安全現在很熱門

無論您的背景如何，您都會看到的一件事是，安全在整個行業都很熱門，這部分與出現在全球新聞中的安全漏洞以及大品牌受到安全漏洞影響或遵循潛在的不良做法，允許不良行為者進入這些公司的網路有關。公平地說，或者至少從我的角度來看，軟體的創建現在比以往任何時候都更容易實現和獲得。但在創建軟體時，它越來越容易受到漏洞等的影響，這允許不良行為者造成破壞，有時會將數據作為贖金或關閉企業造成混亂。到目前為止，我們已經討論了什麼是 DevSecOps，但我認為也值得探索攻擊向量的網路安全方面，以及為什麼我們保護我們的軟體供應鏈以幫助避免這些網路攻擊。

### 網路安全 vs DevSecOps

正如標題所示，這並不是真正的「vs」，而是兩個主題之間的差異。但我認為提出這一點很重要，因為這將解釋為什麼安全必須成為 DevOps 流程、原則和方法的一部分。

網路安全是保護電腦系統和網路免受數位攻擊、盜竊和損壞的實踐。它涉及識別和解決漏洞、實施安全措施以及監控系統威脅。

另一方面，DevSecOps 是開發、安全和運營實踐的組合。這是一種旨在將安全整合到開發過程中的理念，而不是將其視為單獨的步驟。這涉及開發、安全和運營團隊在整個軟體開發生命週期（SDLC）中的協作。

網路安全和 DevSecOps 之間的一些關鍵差異包括：

**重點**：網路安全主要專注於保護系統免受外部威脅，而 DevSecOps 專注於將安全整合到開發過程中。

**範圍**：網路安全涵蓋更廣泛的主題，包括網路安全、數據安全、應用程式安全等。另一方面，DevSecOps 特別專注於改善軟體開發和部署的安全性。

**方法**：網路安全通常涉及在開發過程完成後實施安全措施，而 DevSecOps 涉及從一開始就將安全整合到開發過程中。

**協作**：網路安全通常涉及 IT 和安全團隊之間的協作，而 DevSecOps 涉及開發、安全和運營團隊之間的協作。

## 資源

在 90 天的過程中，我們將有一個每日資源列表，其中將帶來相關內容，這些內容將有助於繼續這些主題，以及您可以前往了解更多資訊的地方。

- [TechWorld with Nana - What is DevSecOps? DevSecOps explained in 8 Mins](https://www.youtube.com/watch?v=nrhxNNH5lt0&list=PLsKoqAvws1pvg7qL7u28_OWfXwqkI3dQ1&index=1&t=19s)

- [What is DevSecOps?](https://www.youtube.com/watch?v=J73MELGF6u0&list=PLsKoqAvws1pvg7qL7u28_OWfXwqkI3dQ1&index=2&t=1s)

- [freeCodeCamp.org - Web App Vulnerabilities - DevSecOps Course for Beginners](https://www.youtube.com/watch?v=F5KJVuii0Yw&list=PLsKoqAvws1pvg7qL7u28_OWfXwqkI3dQ1&index=3&t=67s)

- [The Importance of DevSecOps and 5 Steps to Doing it Properly (DevSecOps EXPLAINED)](https://www.youtube.com/watch?v=KaoPQLyWq_g&list=PLsKoqAvws1pvg7qL7u28_OWfXwqkI3dQ1&index=4&t=13s)

- [Continuous Delivery - What is DevSecOps?](https://www.youtube.com/watch?v=NdvMUcWNlFw&list=PLsKoqAvws1pvg7qL7u28_OWfXwqkI3dQ1&index=5&t=6s)

- [Cloud Advocate - What is DevSecOps?](https://www.youtube.com/watch?v=a2y4Oj5wrZg&list=PLsKoqAvws1pvg7qL7u28_OWfXwqkI3dQ1&index=6)

- [Cloud Advocate - DevSecOps Pipeline CI Process - Real world example!](https://www.youtube.com/watch?v=ipe08lFQZU8&list=PLsKoqAvws1pvg7qL7u28_OWfXwqkI3dQ1&index=7&t=204s)

希望這能讓您對這個模組的期望有所了解，上面的一些資源將有助於提供有關該主題的更多深度，在 [Day 3](day03.md) 的文章中，我們將探討攻擊者的思考方式，這就是為什麼我們必須從一開始就保護。
