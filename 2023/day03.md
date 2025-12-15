## 像攻擊者一樣思考

昨天我們涵蓋了什麼是 DevSecOps，在這篇文章中，我們將探討攻擊者的一些特徵。為了思考攻擊者，我們必須像攻擊者一樣思考。

### 攻擊者的特徵

首先，所有企業和軟體對攻擊者來說都是攻擊向量，沒有安全的地方，我們只能讓地方更安全，對人們來說攻擊的吸引力更小。

![](images/day03-2.jpg)
***[圖片來源](https://www.trainerize.me/articles/outrun-bear/)***

考慮到這一點，攻擊者是一個持續的威脅！

攻擊者將通過按特定順序運行攻擊來識別安全漏洞，以獲得訪問權限、提取數據並成功完成他們的任務。

攻擊者可能很幸運，但他們絕對會進行針對性攻擊。

妥協可能是緩慢而持久的，也可能是快速達到違規。並非所有攻擊都會相同。

### 攻擊者的動機

作為 DevOps 團隊，您將配置基礎設施和軟體，並保護這些環境，可能跨越多個雲、虛擬化和平台上的容器化。

我們必須考慮以下問題：

- **如何**攻擊者會攻擊我們？
- **為什麼**攻擊者會攻擊我們？
- **什麼**我們擁有的對攻擊者有價值的東西？

攻擊者的動機也會因攻擊者而異。我的意思是，這可能只是為了樂趣...我們可能都經歷過，在學校時，只是深入網路尋找更多資訊。誰有故事要講？

但正如我們在媒體上看到的那樣，攻擊更傾向於針對企業和組織的貨幣、欺詐甚至政治攻擊。

在 Kubernetes 領域，我們甚至看到攻擊者利用和使用環境的計算能力來挖掘加密貨幣。

這次攻擊的核心可能是 **數據**

公司的數據對公司來說可能非常有價值，但也可能在野外。這就是為什麼我們如此強調保護這些數據，確保數據安全並加密。

### 攻擊地圖

我們現在有了動機和攻擊者或攻擊者群體的一些特徵，如果這是一個計劃好的攻擊，那麼您將需要一個計劃，您將需要識別您要攻擊的服務和數據。

**攻擊**地圖是對電腦網路攻擊的視覺表示。它顯示攻擊的各個階段、攻擊者使用的工具和技術，以及進入和退出網路的點。攻擊地圖可用於分析過去攻擊的細節、識別網路中的漏洞，並規劃針對未來攻擊的防禦。它們還可用於向非技術利益相關者（如高管或法律團隊）傳達有關攻擊的資訊。

您可以從上面的描述中看到，攻擊地圖應該在雙方或兩個團隊上創建（從團隊角度來說，這是我將在後面的文章中涵蓋的內容）

如果您要創建家庭網路或企業的攻擊地圖，您想要捕獲的一些內容將是：

- 捕獲應用程式的圖形表示，包括所有通信流程和使用的技術。

- 潛在漏洞和攻擊區域的列表。

- 考慮應用程式內每個連接/互動的機密性、完整性和可用性。

- 映射攻擊/漏洞

攻擊地圖可能看起來像這樣，帶有一個解釋每個數字代表什麼的鍵。

![](images/day03-1.png)

從這個地圖中，我們可能會考慮拒絕服務或一些惡意內部攻擊，以及訪問 S3 存儲桶以防止應用程式保存數據或導致它保存壞數據。

這個地圖永遠不是最終的，就像您的應用程式通過反饋不斷前進一樣，這個攻擊地圖也需要進行測試，這提供了反饋，反過來意味著安全態勢針對這些攻擊得到加強。您可以將此稱為安全反饋循環中的「持續響應」。

至少，我們應該遵循良好、更好、最佳模型來改善安全態勢。

- **良好** - 識別需要內建到軟體中的安全設計約束和控制，以減少攻擊。

- **更好** - 優先考慮並在軟體週期中稍後發現的問題中內建安全。

- **最佳** - 將自動化內建到腳本部署中以檢測問題、單元測試、安全測試、黑盒測試

安全是一個設計約束——儘管是一個不方便的約束。

## 資源

- [devsecops.org](https://www.devsecops.org/)

- [TechWorld with Nana - What is DevSecOps? DevSecOps explained in 8 Mins](https://www.youtube.com/watch?v=nrhxNNH5lt0&list=PLsKoqAvws1pvg7qL7u28_OWfXwqkI3dQ1&index=1&t=19s)

- [What is DevSecOps?](https://www.youtube.com/watch?v=J73MELGF6u0&list=PLsKoqAvws1pvg7qL7u28_OWfXwqkI3dQ1&index=2&t=1s)

- [freeCodeCamp.org - Web App Vulnerabilities - DevSecOps Course for Beginners](https://www.youtube.com/watch?v=F5KJVuii0Yw&list=PLsKoqAvws1pvg7qL7u28_OWfXwqkI3dQ1&index=3&t=67s)

- [The Importance of DevSecOps and 5 Steps to Doing it Properly (DevSecOps EXPLAINED)](https://www.youtube.com/watch?v=KaoPQLyWq_g&list=PLsKoqAvws1pvg7qL7u28_OWfXwqkI3dQ1&index=4&t=13s)

- [Continuous Delivery - What is DevSecOps?](https://www.youtube.com/watch?v=NdvMUcWNlFw&list=PLsKoqAvws1pvg7qL7u28_OWfXwqkI3dQ1&index=5&t=6s)

- [Cloud Advocate - What is DevSecOps?](https://www.youtube.com/watch?v=a2y4Oj5wrZg&list=PLsKoqAvws1pvg7qL7u28_OWfXwqkI3dQ1&index=6)

- [Cloud Advocate - DevSecOps Pipeline CI Process - Real world example!](https://www.youtube.com/watch?v=ipe08lFQZU8&list=PLsKoqAvws1pvg7qL7u28_OWfXwqkI3dQ1&index=7&t=204s)

在 [Day 4](day04.md) 見
