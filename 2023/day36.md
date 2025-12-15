# 使用 HashiCorp Vault 保護密鑰

在密鑰管理領域，HashiCorp Vault 往往是事實上的標準，特別是在 DevOps 環境中。像 Jenkins、Terraform 甚至 Kubernetes 這樣的工具都與 Vault 有緊密的整合，允許您從基礎設施即代碼管道中安全地存儲和檢索密鑰。憑藉功能齊全的 REST API，將其整合到現有工作流程中相對簡單。因此，密鑰被整合並從安全平台檢索，同時改善組織的安全態勢。

## 部署簡單的 Vault 環境

當您需要開始使用 HashiCorp Vault 時，您有很多選擇。根據您的技能集和可用環境，Vault 可以部署在物理伺服器、虛擬機器（Amazon EC2、VMware vSphere 等）或容器化環境（如 Docker 或 Kubernetes）上。如果您只想使用 Vault「dev」模式玩轉臨時實例，您甚至可以在筆記型電腦/桌面上啟動 Vault。無論您的偏好如何，將 Vault 與您的應用程式和管道整合將非常相似。

## 在 Kubernetes 上部署 Vault

當談到在 Kubernetes 上部署 Vault 時，HashiCorp 通過提供官方 Helm chart 使其變得非常簡單。這個 [Helm chart](https://github.com/hashicorp/vault-helm) 可以根據您的需求進行自定義，並允許您簡單地將整個 Vault 環境「安裝」到您的 k8s 集群中。就個人而言，我偏好在本地機器上使用 [minikube](https://minikube.sigs.k8s.io/docs/start/) 快速啟動 k8s 集群，並最終快速部署 Vault 環境用於測試。如果您想要自定義 Helm chart 的範例，請查看我 GitHub 上的這個[簡單範例](https://github.com/btkrausen/hashicorp/blob/master/vault/k8s/values.yml)，它是為 OpenShift 環境編寫的。可以添加更高級的配置，但此範例應該能讓您快速上手。

## 在虛擬機器上部署

如果 Kubernetes 不是您的菜，Vault 在傳統虛擬機器上也完美運行。它不像 Helm chart 那麼簡單，但擁有腳本和生產級別的配置檔案確實簡化了流程。從不同的 Linux 發行版到 Windows，您可以選擇幾乎任何您熟悉的操作系統在您的環境中運行 Vault。Vault 二進制檔案可以從 [HashiCorp 發布頁面](https://releases.hashicorp.com/vault) 下載。這個網站使手動或通過自動化方式下載 Vault 變得容易。Vault 只需要放在您的 **$PATH** 中即可開始使用。

除了安裝 Vault 之外，還有一些您需要考慮的額外要求。這些要求與在伺服器上運行任何服務時需要的類似，例如配置檔案、服務檔案等。以下是一些需要考慮的要求：
* 每個伺服器的 [Vault 配置檔案](https://github.com/btkrausen/hashicorp/blob/master/vault/scripts/install_vault.sh)
* 用於管理服務（啟動、停止等）的 [system](https://github.com/btkrausen/hashicorp/blob/master/vault/config_files/vault.service)（或類似）檔案
* 用於存儲配置檔案和日誌的目錄
* 運行 Vault 服務的本地非 root 用戶
* 用戶目錄的權限/所有權

作為一個在 Vault 上工作了六年多並是 HashiCorp 社區的專職成員的人，我在 [我的 GitHub](https://github.com/btkrausen/hashicorp) 上提供了所有這些供您使用。這些是我用於生產級別部署的檔案，因此它們在現實世界中經過了嘗試和測試。請隨意使用這些作為起點，並根據您的需求進行自定義。

> 如果這一切看起來令人困惑，您可以查看我的 [HashiCorp Vault 入門課程](https://btk.me/v)，了解所有這些配置（以及更多內容！），同時獲得動手實驗的訪問權限。

## Vault 已啟動並運行。下一步是什麼？

一旦您的 Vault 啟動並運行，在整合您的工作流程和應用程式之前，需要完成幾個步驟。當任何 Vault 集群首次啟動時，需要對其進行初始化。簡而言之，初始化是 Vault 創建加密密鑰、解封或恢復密鑰，並為您準備存儲後端的時候。初始化集群最簡單的方法是使用 'vault operator init' 命令。如果您在生產環境中執行此操作，請考慮使用 PGP 密鑰加密生成的解封/恢復密鑰，以確保沒有人可以訪問所有密鑰（雙關語！）。查看 [我的視頻這裡](https://youtu.be/QErHqzGH1mk) 了解如何安全地執行此步驟。Vault 初始化還將返回初始 root token，此時這是獲得 Vault 訪問權限的唯一身份驗證機制。

![](images/day36-1.png)

一旦 Vault 被初始化，您距離使用 Vault 提供的所有功能只有一步之遙。如果您決定使用自動解封，您已經完成了！沒有自動解封，Vault 需要被解封。解封是重建用於解密加密密鑰的根密鑰的過程，該密鑰最終存儲在記憶體中，用於讀寫存儲後端的數據。如果 Vault 被封存，它基本上無用，所以這是一個重要步驟。您可以使用 'vault operator unseal' 命令解封 Vault。Vault 將請求一個「解封密鑰」，這是在上面討論的初始化過程中返回的。執行此步驟三次（假設使用預設值）以解封 Vault。順便說一下，當您準備好時，您可以[遷移到自動解封機制](https://youtu.be/HolUACQutpk)。一旦 Vault 被解封，我們可以開始配置各種組件，如身份驗證方法、密鑰引擎和審計設備。更多內容將在後面的日子中討論！

![](images/day36-2.png)

在結束這一天之前，我想提及和討論一些額外的主題。有些不是實驗環境所必需的，但應該考慮（或要求！）用於生產環境：

* 到處使用 TLS。繼續從受信任的（內部）CA 鑄造證書，從一開始就實踐良好的安全性。如果您喜歡運行易受攻擊和不安全的環境，或者如果您在實驗室中運行或用於測試目的，Vault 支援通過在配置檔案中設置 'tls_disable = true' 來禁用 TLS（監聽器節）。

* 使用 root token 可以完全、無限制地訪問 Vault 中的任何內容。root token 是高度特權的，不應該共享。事實上，它不會針對任何 Vault 政策進行評估。因此，建議使用 root token 執行初始配置，即身份驗證方法，然後應該撤銷該 token。如果需要，[可以生成另一個 root token](https://youtu.be/rcQsNinVl0Y)。

* 如果不需要冗餘和高可用性，單個 Vault 節點可能足以滿足實驗環境。如果服務正常運行時間至關重要，請使用至少 3 個節點（假設使用整合存儲）。我建議生產集群使用五個節點，以提供 n + 2 冗餘，這意味著您可以失去多達兩個節點，Vault 服務仍然保持功能。

請參閱 [Day 37](day37.md)。
