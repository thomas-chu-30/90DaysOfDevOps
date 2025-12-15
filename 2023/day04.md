## <span style="color:red">紅隊</span> vs. <span style="color:blue">藍隊</span>

我在上一節中提到的內容，是指 <span style="color:red">**紅**</span> 隊和 <span style="color:blue">**藍**</span> 隊。在安全領域，<span style="color:red">**紅**</span> 隊和 <span style="color:blue">**藍**</span> 隊作為攻擊者和防禦者工作，以改善組織的安全性。

兩個團隊都以不同的方式致力於改善組織的安全態勢。

<span style="color:red">**紅**</span> 隊扮演攻擊者的角色，試圖在代碼或基礎設施中尋找漏洞，並試圖突破網路安全防禦。

<span style="color:blue">**藍**</span> 隊防禦這些攻擊，並在事件發生時響應。

![](images\day04-2.jpg)
***[圖片來源](https://hackernoon.com/introducing-the-infosec-colour-wheel-blending-developers-with-red-and-blue-security-teams-6437c1a07700)***

### 好處

理解和改善公司安全態勢的一個非常好的方法是在 <span style="color:red">**紅**</span> 隊和 <span style="color:blue">**藍**</span> 隊之間運行這些練習。整個想法是這個場景是為了模擬真實攻擊。這種方法將有助於的一些領域如下：

- 漏洞
- 強化網路安全
- 在檢測和隔離攻擊方面獲得經驗
- 建立詳細的響應計劃
- 提高整體公司安全意識

### <span style="color:red">紅隊</span>

NIST（國家標準與技術研究院）將 <span style="color:red">**紅**</span> 隊描述為：

「一組被授權和組織起來模擬潛在對手對企業安全態勢的攻擊或利用能力的人員。」

他們在場景或攻擊模擬中扮演不良行為者。

當我們談論 <span style="color:red">**紅**</span> 隊和 <span style="color:blue">**藍**</span> 隊時，它可能比軟體生命週期的 DevSecOps 流程和原則更廣泛，但了解這一點不會有害，DevSecOps 的實踐將確保整體上您有更好的安全態勢。

<span style="color:red">**紅**</span> 隊的任務是像攻擊者一樣思考，這是我們在上一節中涵蓋的。思考社交工程，包括企業內更廣泛的團隊，以操縱和獲得對網路和服務的訪問權限。

<span style="color:red">**紅**</span> 隊的一個關鍵基礎是理解軟體開發。理解和知道應用程式是如何構建的，您將能夠識別可能的弱點，然後編寫您的程式來嘗試獲得訪問權限並利用。除此之外，您可能聽說過「滲透測試」或「pen testing」這個術語，<span style="color:red">**紅**</span> 隊的總體目標是識別並嘗試利用環境中已知的漏洞。隨著開源軟體的興起，這是我想在幾節課後涵蓋的另一個領域。

### <span style="color:blue">藍隊</span>

NIST（國家標準與技術研究院）將 <span style="color:blue">**藍**</span> 隊描述為：

「負責通過維持其對一組模擬攻擊者的安全態勢來防禦企業資訊系統使用的團隊。」

<span style="color:blue">**藍**</span> 隊扮演防禦角色，他們將分析企業中當前的安全態勢，然後採取行動改善它，以阻止那些外部攻擊。在 <span style="color:blue">**藍**</span> 隊中，您還將專注於持續監控（我們在 2022 年底關於 DevOps 的內容中涵蓋的內容）監控違規行為並在發生時響應。

作為 <span style="color:blue">**藍**</span> 隊的一部分，您將必須了解您正在保護的資產以及如何最好地保護它們。在當今的 IT 環境中，我們有許多不同的選項來運行我們的工作負載、應用程式和數據。

- 評估風險 - 通過風險評估的形式將使您很好地了解企業內最關鍵的資產是什麼。

- 威脅情報 - 那裡有什麼威脅？有成千上萬的漏洞可能沒有解決方案，您如何在不損害用例和業務需求的情況下降低這些服務的風險？

### 網路安全色輪

隨著網路安全在所有大品牌受到打擊的重要性不斷增長，企業內的安全需要的不僅僅是 <span style="color:red">**紅**</span> 隊和 <span style="color:blue">**藍**</span> 隊。

![](images\day04-1.png)
***[圖片來源](https://hackernoon.com/introducing-the-infosec-colour-wheel-blending-developers-with-red-and-blue-security-teams-6437c1a07700)***

- <span style="color:yellow">**黃隊**</span> 是我們的建設者，開發安全系統和應用程式的工程師和開發人員。

「我們有我們的 <span style="color:red">**紅**</span> 隊和 <span style="color:blue">**藍**</span> 隊，就像我們一直以來一樣，但現在隨著 <span style="color:yellow">**黃**</span> 隊的引入，我們可以有次要的彩色團隊（橙色、綠色和紫色），專門用於混合攻擊者、防禦者和編碼者之間的技能——使代碼更安全，組織更安全。」

上面的摘要取自文章末尾列出的頂部資源。

<span style="color:red">**紅**</span>、<span style="color:blue">**藍**</span>、<span style="color:yellow">**黃**</span> 是原色，將它們組合起來，我們開始理解其他顏色或次要顏色的作用，同樣在那個第一個連結中有很好的解釋。

- <span style="color:purple">**紫隊**</span> - 特殊團隊！如果您將 <span style="color:blue">**藍**</span> 和 <span style="color:red">**紅**</span> 結合，您會得到 <span style="color:purple">**紫**</span>。如果您將防禦與進攻整合，並且您在團隊之間協作和分享知識，您整體上提供更好的態勢。

- <span style="color:green">**綠隊**</span> - 反饋循環，<span style="color:green">**綠**</span> 隊將從 <span style="color:blue">**藍**</span> 隊獲取見解，並與 <span style="color:yellow">**黃**</span> 隊密切合作以提高效率。將 <span style="color:blue">**藍**</span> 和 <span style="color:green">**綠**</span> 混合，您會 <span style="color:purple">**得到**</span> 什麼？

- <span style="color:orange">**橙隊**</span> - 就像 <span style="color:green">**綠**</span> 隊與 <span style="color:blue">**藍**</span> 隊合作進行反饋一樣，<span style="color:orange">**橙**</span> 隊與 <span style="color:red">**紅**</span> 隊合作，並將他們學到的東西傳遞給 <span style="color:yellow">**黃**</span> 隊，以便在他們的代碼中建立更好的安全性。

當我開始研究這個時，我意識到也許我正在遠離 DevOps 主題，但請任何在 DevSecOps 領域的人，這有用嗎？正確嗎？您有什麼要補充的嗎？

顯然，在整個過程中，我們計劃深入探討 DevSecOps 和不同階段的更多具體內容，所以我注意不要涵蓋那些將在未來課程中涵蓋的領域。

也請添加任何其他資源。

## 資源

- [Introducing the InfoSec colour wheel — blending developers with red and blue security teams.](https://hackernoon.com/introducing-the-infosec-colour-wheel-blending-developers-with-red-and-blue-security-teams-6437c1a07700)


在 [Day 5](day05.md) 見。
