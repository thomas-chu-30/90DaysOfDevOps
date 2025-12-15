# 簽名

簽名過程涉及...嗯，用密鑰簽名工件，然後驗證此工件未被篡改。

在這種情況下，「工件」可以是任何東西

- [代碼](https://venafi.com/machine-identity-basics/what-is-code-signing/#item-1)
- [git commit](https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-commits)
- [容器映像](https://docs.sigstore.dev/cosign/overview/)

簽名和驗證簽名確保我們從註冊表中拉取的工件（容器）與我們推送的相同。
這保護我們免受供應鏈和中間人攻擊，在這些攻擊中我們下載了與我們想要的不同的東西。

CI 工作流程將如下所示：

0. 開發人員將代碼推送到 Git
1. CI 將代碼構建為容器
2. **CI 使用我們的私鑰簽名容器**
3. CI 將簽名的容器推送到我們的註冊表

然後當我們想要部署此映像時：

1. 拉取映像
2. **使用我們的公鑰驗證簽名**
   1. 如果簽名不匹配，部署失敗 - 映像可能已損壞
3. 如果簽名匹配，繼續部署

此工作流程基於公私鑰加密。
當您使用私鑰簽名某些內容時，每個可以訪問您的公鑰的人都可以驗證這是由您簽名的。

由於公鑰是...嗯，公開的，這意味著每個人。

## 不簽名映像的危險

如果您不簽名容器映像，存在有人會用另一個惡意映像替換您儲存庫中的映像的危險。

例如，您可以將 `my-repo/my-image:1.0.0` 映像推送到您的儲存庫，但映像標籤，即使是版本化的（如 `1.0.0`）也是可變的。
因此，可以訪問您儲存庫的攻擊者可以推送另一個映像，以相同的方式標記它，這樣它將覆蓋您的映像。
然後，當您去部署此映像時，將部署的映像是攻擊者偽造的映像。
這可能是一個惡意的映像。
例如，一個有惡意軟體、竊取數據或使用您的基礎設施挖掘加密貨幣的映像。

這個問題可以通過簽名映像來解決，因為當您簽名映像時，您可以稍後驗證您拉取的是您最初上傳的內容。

所以讓我們看看如何通過一個名為 [cosign](https://docs.sigstore.dev/cosign/overview/) 的工具來做到這一點。

## 簽名容器映像

首先，下載工具，按照您操作系統的說明 [here](https://docs.sigstore.dev/cosign/installation/)。

如果您沒有密鑰對，請生成一個：

```console
cosign generate-key-pair
```

這將在當前資料夾中輸出兩個檔案：

- `cosign.key` - 您的私鑰。
不要與任何人分享。
- `cosign.pub` - 您的公鑰。
與需要它的人分享。

我們可以使用私鑰簽名映像：

```console
$ cosign sign --key cosign.key asankov/signed
Enter password for private key:

Pushing signature to: index.docker.io/asankov/signed
```

此命令簽名了 `asankov/signed` 容器映像並將簽名推送到容器儲存庫。

## 驗證簽名

現在我們已經簽名了映像，讓我們驗證簽名。

為此，我們需要我們的公鑰：

```console
$ cosign verify --key=cosign.pub asankov/signed | jq

Verification for index.docker.io/asankov/signed:latest --
The following checks were performed on each of these signatures:
  - The cosign claims were validated
  - The signatures were verified against the specified public key
[
  {
    "critical": {
      "identity": {
        "docker-reference": "index.docker.io/asankov/signed"
      },
      "image": {
        "docker-manifest-digest": "sha256:93d62c92b70efc512379cf89317eaf41b8ce6cba84a5e69507a95a7f15708506"
      },
      "type": "cosign container image signature"
    },
    "optional": null
  }
]
```

此命令的輸出向我們顯示映像是由我們預期的密鑰簽名的。
由於我們是唯一可以訪問我們私鑰的人，這意味著除了我們之外沒有人可以將此映像和簽名推送到容器儲存庫。
因此，此映像的內容自我們推送以來未被篡改。

讓我們嘗試驗證一個我們沒有簽名的映像。

```console
$ cosign verify --key=cosign.pub asankov/not-signed
Error: no matching signatures:

main.go:62: error during command execution: no matching signatures:
```

正如預期的那樣，`cosign` 無法驗證此映像的簽名（因為沒有簽名）。

在此示例中，此映像（`asankov/not-signed`）根本沒有簽名，但如果有人使用與我們用來驗證它的密鑰不同的密鑰簽名此映像，我們也會得到相同的錯誤。

### 在 Kubernetes 中驗證簽名

在上一個示例中，我們是手動驗證簽名的。
然而，這僅適用於演示目的或使用工具進行實驗。

在實際場景中，您希望此驗證在部署時自動完成。

幸運的是，有許多 `cosign` 整合可以做到這一點。

例如，如果我們使用 Kubernetes，我們可以部署一個驗證 webhook，它將審計所有新部署並驗證它們使用的容器映像是簽名的。

對於 Kubernetes，您可以從 3 個現有整合中選擇 - [Gatekeeper](https://github.com/sigstore/cosign-gatekeeper-provider)、[Kyverno](https://kyverno.io/docs/writing-policies/verify-images/) 或 [Conaisseur](https://github.com/sse-secure-systems/connaisseur#what-is-connaisseur)。
您可以根據您的偏好或如果您已經將它們用於其他目的來選擇三者之一。

## 需要注意的危險

與其他一切一樣，簽名映像不是萬能的，不會解決所有安全問題。

仍然存在您的私鑰可能洩露的問題，在這種情況下，每個人都可以簽名所有內容，它仍然會通過您的簽名檢查。

然而，將簽名整合到您的工作流程中增加了另一層防禦，並為攻擊者增加了另一個需要跳過的障礙。

## 總結

簽名工件通過允許您驗證工件的完整性來防止供應鏈和中間人攻擊。

[Sigstore](https://sigstore.dev/) 和 [cosign](https://docs.sigstore.dev/cosign/overview/) 是簽名工件的有用工具，它們帶有許多整合可供選擇。
在 [Day 25](day25.md) 見。
