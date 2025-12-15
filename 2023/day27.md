# 網路漏洞掃描

在 [Day 25](day25.md) 中，我們了解到漏洞掃描是掃描網路或系統以識別任何現有安全漏洞的過程。
我們還了解到網路漏洞掃描是系統漏洞掃描的子集，例如我們只掃描系統的網路部分。

今天我們將深入了解什麼是網路漏洞掃描以及如何進行。

## 網路漏洞掃描

**網路漏洞掃描**是識別網路上弱點的過程，這些弱點是威脅行為者可能利用的潛在目標。

曾經，在雲之前，網路安全很容易（某種程度上，良好的安全從來都不容易）。
您在數據中心周圍構建一個巨大的防火牆，只允許流量到適當的入口點，並假設設法進入內部的所有內容都是合法的。

這種方法有一個巨大的缺陷 - 如果攻擊者設法穿過牆，就沒有更多的防線來阻止他們。

如今，這種方法甚至更不起作用。
隨著雲和微服務架構，網路中的參與者呈指數級增長。

這要求我們改變思維方式，並在構建安全系統時採用新流程和工具。

這樣一個過程是 **網路漏洞掃描**。
執行此操作的工具稱為 **網路漏洞掃描器**。

## 網路漏洞掃描如何工作？

漏洞掃描軟體依賴於已知漏洞數據庫和針對它們的自動化測試。
掃描器將掃描您網路上的各種設備和主機，識別設備類型和操作系統，並探測相關漏洞。

掃描可能純粹是基於網路的，從更廣泛的互聯網（外部掃描）或從您的本地內聯網內部（內部掃描）進行。
當掃描器已提供憑證以將自己驗證為主機或設備的合法用戶時，它可能是深度檢查。

## 漏洞管理

執行掃描並發現漏洞後，下一步是解決它們。
這是漏洞管理階段。

漏洞可能被標記為誤報，例如掃描器報告了不真實的東西。
它可能被確認，然後由安全團隊評估。

許多漏洞可以通過修補來解決，但不是全部。
成本/效益分析應該是流程的一部分，因為並非所有漏洞在每個環境中都是安全風險，並且可能有業務原因導致您無法安裝給定的補丁。
如果掃描器報告修復漏洞的替代方法（例如，禁用服務或通過防火牆阻止端口），這將很有用。

## 注意事項

與容器映像漏洞掃描類似，網路漏洞掃描測試您的系統以查找 _已知_ 漏洞。
所以它不會找到任何尚未報告的東西。

此外，它不會保護您免受將管理面板暴露到互聯網並使用默認密碼之類的事情。
（儘管我假設一些網路掃描器足夠聰明，可以測試不應該暴露的知名端點）。

歸根結底，由您來了解您的系統，並了解測試和保護它的方法。
工具只能做到這麼多。

## 網路掃描器

以下是用於此目的的網路掃描器列表。

**注意：** 此列表上的工具不是免費和開源的，但它們中的大多數都有免費試用，您可以使用它們來評估它們。

- [Intruder Network Vulnerability Scanner](https://www.intruder.io/network-vulnerability-scanner)
- [SecPod SanerNow Vulnerability Management](https://www.secpod.com/vulnerability-management/)
- [ManageEngine Vulnerability Manager Plus](https://www.manageengine.com/vulnerability-management/)
- [Domotz](https://www.domotz.com/features/network-security.php)
- [Microsoft Defender for Endpoint](https://www.microsoft.com/en-us/security/business/endpoint-security/microsoft-defender-endpoint)
- [Rapid7 InsightVM](https://www.rapid7.com/products/insightvm/)

## 總結

與我們在前一天談論的所有安全流程一樣，網路掃描不是萬能的。
如果您不照顧系統安全的其他方面，使用網路掃描器不會使您安全。

此外，使用像網路掃描器這樣的工具並不意味著您不需要安全團隊。

恰恰相反，良好的安全 SDLC 從啟用安全團隊針對系統運行該類工具開始。
然後他們還將負責對結果進行分類，並與需要修復漏洞的相關團隊合作。
這將通過修補系統、關閉不必要的漏洞或以更安全的方式重新架構系統來完成。

## 資源

<https://www.comparitech.com/net-admin/free-network-vulnerability-scanners/>

<https://www.rapid7.com/solutions/network-vulnerability-scanner/>

在 [Day 28](day28.md) 見。
