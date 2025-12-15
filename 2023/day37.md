# 使用 HashiCorp Vault 的密鑰引擎

雖然 HashiCorp Vault 中有多個重要的組件，但沒有一個比密鑰引擎更基礎。這是因為密鑰引擎負責提供您想要從 Vault 獲得的功能。所有其他組件，如身份驗證方法、審計設備和存儲後端，都可以視為支援組件，最終提供訪問密鑰引擎的機制。

## 啟用密鑰引擎

預設情況下，Vault 不啟用任何密鑰引擎。因此，密鑰引擎所需的任何功能都需要您啟用它。您可以根據需要啟用任意數量的密鑰引擎，並且可以啟用多個相同類型的密鑰引擎（只要它們在不同的路徑上啟用），這在 Vault 客戶中非常常見。許多人會啟用 10 個或 100 個鍵值密鑰引擎，以按團隊、業務部門或應用程式隔離密鑰。或者，您可以使用單個鍵值密鑰引擎，並在其下分離每個團隊。這完全取決於您或適合您組織的任何方式。

由於密鑰引擎是 Vault 的核心功能，HashiCorp 已經包含許多開箱即用的密鑰引擎。如果您需要額外的功能，可以使用社區或供應商支援的插件。Vault 支援的最普遍的密鑰引擎範例包括：

•	鍵值（Key/Value）– 存儲和檢索靜態、長期有效的憑證
•	PKI – 將 Vault 配置為中間/頒發 CA 以自動化 TLS 證書
•	Transit – 在整個組織中集中您的加密工作流程
•	AWS/Azure/GCP – 動態生成 AWS 憑證以訪問您的帳戶
•	數據庫（Database）– 針對您的數據庫動態生成憑證

您可以使用 `vault secrets enable <secret_engine_type>` 命令啟用密鑰引擎。您也可以直接在 Vault UI 中啟用和配置許多密鑰引擎。

![](images/day37-1.png)

## 使用 KV 密鑰引擎

作為一個在 Vault 上工作了相當長一段時間的顧問，我還沒有遇到過不想使用鍵值密鑰引擎的客戶/客戶。我將此歸因於這樣一個事實：大多數組織幾十年來一直在使用靜態、長期有效的憑證（可能不會很快消失！），並且需要一個地方來安全地存儲它們。在使用鍵值密鑰引擎時，請記住它不會與任何外部源交互以複製/更新/檢索密鑰。它只是一個鍵值存儲，您可以通過 UI、CLI 或 API 請求存儲、更新和檢索您的憑證。

讓我們啟用一個 KV 密鑰引擎（版本 2）來看看它是如何工作的，我們將開始從中存儲和檢索密鑰。在命令行上，使用 `vault secrets enable -path=90daysofdevops kv-v2`。這在 `90daysofdevops` 路徑上啟用 KVv2 密鑰引擎，這意味著與此密鑰引擎的所有交互都將使用 `90daysofdevops/` 路徑，例如讀取、寫入或更新密鑰。

![](images/day37-2.png)

## 將數據寫入 KV 密鑰引擎

我們現在有一個新的、空白的鍵值存儲來存儲我們的密鑰。我們可以在此路徑下以任何我們想要的方式組織這些密鑰。許多組織選擇按團隊或應用程式組織，但與您的團隊討論以確定最適合您組織的結構。這裡最大的因素是簡化將允許/拒絕訪問這些密鑰的政策。如果您可以編寫簡單的政策或甚至模板化的政策，它將使您的 Day 2 運維變得更加容易。在這個範例中，讓我們假設我們有三個不同的團隊使用此鍵值密鑰引擎，**cloud** 團隊、**automation** 團隊和 **development** 團隊。

讓我們為 cloud 團隊將我們的第一個密鑰寫入 KV 密鑰引擎。同樣，就像 Vault 中的任何其他內容一樣，您可以在三個 Vault 介面中的任何一個上執行此操作：UI、CLI 或 API。要寫入密鑰，您可以使用以下命令：

`vault kv put 90daysofdevops/cloud/aws secret_key=ABC123 access_key=987zyx`

![](images/day37-3.png)

在這個範例中，我們寫入了兩個鍵值對，一個用於我們的 _secret_key_，一個用於我們的 _access_key_，每個都有其各自的值。這些密鑰現在存儲的路徑是 `90daysofdevops/data/cloud/aws`。不要讓那個 _data_ 段欺騙您。在使用 KVv2 密鑰引擎時這是必需的。`vault kv put` 掩蓋了在我們的路徑中添加 `data` 的需要，但這是您從 API 讀取和編寫政策以訪問此路徑時需要檢索密鑰的路徑。

讓我們為 development 團隊寫入一些數據。在這種情況下，開發團隊決定根據他們負責的應用程式組織密鑰。他們需要為應用程式部署存儲證書和私鑰，以及另一個應用程式的靜態數據庫憑證。

`vault kv put 90daysofdevops/development/apps/customer-app user=db-admin password=P@ssw0rd1`

`vault kv put 90daysofdevops/development/apps/cache-app cert="----BEGIN CERTIFICATE----asdfwefsadfa23jjklnsdfkl2jns9n2a32sdaffgrsyhgsfwefdsan---END CERTIFICATE----" private_key="---BEGIN PRIVATE KEY----nwljdwsxcvmnrejhq394hdsfjfgadjkhfajkl----END PRIVATE KEY---"`
![](images/day37-4.png)

在這個範例中，我們將不同類型的密鑰寫入了兩個不同的路徑：
•	`90daysofdevops/development/apps/customer-app`
•	`90daysofdevops/development/apps/cache-app`

同樣，您和您的團隊應該決定如何在組織中組織和存儲憑證。通常，Vault 運算符「擁有」路徑的組織到某個級別，然後允許團隊以他們認為合適的方式組織他們的數據。不同的團隊是不同的，可能會有不同的需求。

## 從 KV 密鑰引擎讀取數據

好的，現在我們已經將密鑰寫入我們的 KV 密鑰引擎，我們想要讀取這些密鑰是有意義的。這些密鑰可能由開發人員手動檢索。它們可能由 Azure DevOps 管道在 AKS 上配置容器化應用程式時檢索。無論哪種方式，數據都被某些東西使用並用於部署或配置。

讀取密鑰時，您將使用與將密鑰寫入 KV 密鑰引擎時相同的路徑。路徑會根據您使用的是 CLI 還是 API 而略有不同（我知道，這有點煩人！），但這並不太令人困惑。API 調用必須包含我們之前提到的 _data_ 路徑。在這個範例中，我們現在假設使用 CLI。但是，請記住，許多編排器或配置工具都包含與 Vault 的原生整合，例如 Jenkins 插件或 AzureDevOps 擴展。GitLab 還包括與 HashiCorp Vault 服務的緊密整合。

要使用 Vault CLI 讀取為 cloud 團隊存儲的密鑰，請使用以下命令：

`vault kv get 90daysofdevops/cloud/aws`

![](images/day37-5.png)

請注意響應的一些關鍵組件。Vault 顯示 API 路徑（包括 _data_ 段）、與密鑰關聯的元數據（因為這是鍵值版本 2 密鑰引擎）以及我們想要的數據（密鑰！）。但是如果我們只想返回密鑰的值而不包括其他資訊怎麼辦？我們可以通過幾種方式做到這一點。

第一種方法是在命令行上使用 -field 標誌：

`vault kv get -field=secret_key 90daysofdevops/cloud/aws`

![](images/day37-6.png)

在這種情況下，Vault 只返回 `secret_key` 的值，這對於我們不想解析整個響應的自動化作業可能很有幫助。我們可以輕鬆做到這一點的另一種方法是請求響應為 JSON 格式，並使用 `jq` 解析數據以獲得我們需要的內容：

`vault kv get -format=json 90daysofdevops/cloud/aws | jq -r '.data.data.secret_key'`

![](images/day37-7.png)

## 總結

我希望這個快速教程讓您了解如何使用 Vault 的 KV 密鑰引擎從應用程式中寫入和讀取數據。當然，KV 密鑰引擎中還有許多額外的配置和設置，您可以用來改善數據的安全性和組織，但這至少應該讓您開始。

> 別忘了查看我的 [YouTube 頻道](https://btk.me/yt)，該頻道展示了 Vault 和其他工具的許多不同配置。計劃繼續向此頻道添加更多內容，所以請訂閱並告訴我還想看什麼。

請參閱 [Day 38](day38.md)。
