# 動手實踐 HashiCorp Vault

快速說明，感謝 Bryan 為密鑰管理部分開頭，@MichaelCade1 在這裡總結並提供一些動手場景，以便我們可以獲得一些實際的接觸點和 HashiCorp Vault 的場景。

我在這裡涵蓋的很多內容可以在現有的 [HashiCorp 教程](https://developer.hashicorp.com/vault) 中找到。HashiCorp 的這些資源還將涵蓋更多不同的場景。

## HashiCorp Vault CLI

我想首先介紹的是在本地機器上安裝 HashiCorp Vault CLI。我們有一個很好的 [HashiCorp 在本地機器上安裝 CLI 的教程](https://developer.hashicorp.com/vault/tutorials/getting-started/getting-started-install)。

我在我的 MacOS 設備上，但上面的鏈接中也顯示了其他說明。

`brew tap hashicorp/tap`

`brew install hashicorp/tap/vault`

現在當您運行 `vault` 命令時，您應該可以訪問 vault 二進制檔案和子命令。

![](images\day39-1.png)

## 在 Kubernetes 上啟動 Vault

如果您一直在關注此專案中的任何其他部分，您可能會看到我是 Minikube 的忠實粉絲，當涉及到為學習或本地開發目的啟動本地 Kubernetes 集群時。

Vault 絕不專屬於 Kubernetes，但我想涵蓋這個場景，HashiCorp 還有一個用於 Vault 的雲端 SaaS 選項，但它可以部署在許多地方。

我使用下面的命令來啟動我的 minikube，您可以使用 `minikube start`，但這是我用於其他演示的首選。

`minikube start --addons volumesnapshots,csi-hostpath-driver --apiserver-port=6443 --container-runtime=containerd -p demo --kubernetes-version=1.26.0`

現在我們希望我們的集群已經啟動並運行，我們將使用 helm 將 vault 部署到我們的集群。快速搜索 helm 安裝步驟將為您提供如何實現這一點的方法。如果您在 macos 上，則可以使用 homebrew 安裝

`brew install helm`

添加 Hashicorp helm 儲存庫。

`helm repo add hashicorp https://helm.releases.hashicorp.com`

儲存庫中有幾個選項和圖表可供我們使用，通過運行 `helm search repo hashicorp` 您將找到可用的 hashicorp 產品和圖表。

![](images\day39-2.png)

對於本教程的其餘部分，我們將採用這個[教程](https://developer.hashicorp.com/vault/tutorials/kubernetes/kubernetes-minikube-raft) 但是我們將把 vault 部署到專用的命名空間，而不是預設命名空間。

我們將首先使用以下命令創建此檔案。

```
cat > helm-vault-raft-values.yml <<EOF
server:
  affinity: ""
  ha:
    enabled: true
    raft: 
      enabled: true
EOF
```

我已將此檔案添加到儲存庫中名為 day39 的資料夾中。

我們現在將使用以下命令使用 helm 部署 vault。

`helm install vault hashicorp/vault --namespace vault --values helm-vault-raft-values.yml --create-namespace`

通過運行 `kubectl get pods -n vault` 您應該看到下面的輸出，預期看到 3 個 Pod 處於 0/1 運行狀態，我們接下來將處理這個問題。

![](images/day39-3.png)


接下來，我們將使用以下命令初始化 vault-0，

***"此命令生成一個根密鑰，將其分解為密鑰份額 -key-shares=1，然後設置解封 Vault 所需的密鑰份額數量 -key-threshold=1。這些密鑰份額以 JSON 格式作為解封密鑰寫入輸出"***

```
kubectl exec vault-0 -n vault -- vault operator init \
    -key-shares=1 \
    -key-threshold=1 \
    -format=json > cluster-keys.json
```

然後我們可以使用 jq 再次顯示解封密鑰，如果您想在 macos 上安裝它，可以使用 `brew install jq`

在您的本地機器上，您現在將看到一個名為 cluster-keys.json 的檔案。顯示密鑰的命令是：

`jq -r ".unseal_keys_b64[]" cluster-keys.json`

輸出應該如下所示，

![](images/day39-4.png)

我們將根據該密鑰創建一個變數

`VAULT_UNSEAL_KEY=$(jq -r ".unseal_keys_b64[]" cluster-keys.json)`

然後我們可以使用以下命令解封我們的 vault-0 pod：

`kubectl exec vault-0 -n vault -- vault operator unseal $VAULT_UNSEAL_KEY`

這應該顯示

![](images/day39-5.png)

在此階段，如果您運行 `kubectl get pods -n vault`，您現在應該看到 vault-0 pod 處於 1/1 運行狀態。

我們現在將使用以下命令將其他兩個 pod vault-1 和 vault-2 加入到我們的 raft 集群中。

```
kubectl exec -ti vault-1 -n vault -- vault operator raft join http://vault-0.vault-internal:8200

kubectl exec -ti vault-2 -n vault -- vault operator raft join http://vault-0.vault-internal:8200
```

我們現在可以使用以下命令解封上面提到的兩個 vault pod。

```
kubectl exec vault-1 -n vault -- vault operator unseal $VAULT_UNSEAL_KEY

kubectl exec vault-2 -n vault -- vault operator unseal $VAULT_UNSEAL_KEY
```

再看一下 `kubectl get pods -n vault` 命令。下面是所有最近命令的輸出。

![](images/day39-6.png)

## 啟用鍵值密鑰引擎

為了啟用密鑰引擎，我們需要使用 root token。這也被導出到我們之前看到並用於解封密鑰的 cluster-keys.json 檔案中。如果沒有跟隨，您可以在 day39 資料夾中看到此 JSON 檔案。

`jq -r ".root_token" cluster-keys.json`

我們現在必須 exec 進入我們的 vault-0 pod 以啟用密鑰引擎。

`kubectl exec --stdin=true --tty=true vault-0 -n vault -- /bin/sh`

![](images/day39-7.png)


`vault secrets enable -path=secret kv-v2`

## 為我們的應用程式創建新密鑰

作為一個簡單的測試，我們想在 Kubernetes 集群中創建一個應用程式，在其自己的命名空間中，然後與其自己命名空間中的 vault 通信。

這是連結教程中未定義的一件事，我想提供更多真實的使用案例，因為是的，可以使用預設命名空間，但這並不意味著應該使用它。

`vault kv put secret/devwebapp/config username='90DaysOfDevOps' password='90DaysOfDevOps'`

我們可以使用以下命令確認我們剛剛創建的內容：

`vault kv get secret/devwebapp/config`

您可以在下面的終端中看到上面運行的命令。

![](images/day39-8.png)

接下來我們需要啟用 Kubernetes 身份驗證方法。

`vault auth enable kubernetes`

配置 Kubernetes 身份驗證方法以使用 Kubernetes API 的位置。

```
vault write auth/kubernetes/config \
    kubernetes_host="https://$KUBERNETES_PORT_443_TCP_ADDR:443"
```

我們現在可以創建名為 devwebapp 的政策，該政策啟用對路徑 secret/data/devwebapp/config 的密鑰的讀取能力

```
vault policy write devwebapp - <<EOF
path "secret/data/devwebapp/config" {
  capabilities = ["read"]
}
EOF
```

創建一個名為 devweb-app 的 Kubernetes 身份驗證角色，這已從 Hashicorp 的教程中採用，但請注意我們定義了一個非預設的命名空間。

```
vault write auth/kubernetes/role/devweb-app \
        bound_service_account_names=internal-app \
        bound_service_account_namespaces=webdevapp \
        policies=devwebapp \
        ttl=24h
```
現在我們可以退出我們的 vault-0 pod。

`exit`

## 部署我們的應用程式

如前所述，現在回到我們的 Kubernetes 集群，是時候創建和部署我們的應用程式以完成此演示。

首先，使用以下命令創建應用程式命名空間

`kubectl create ns devwebapp`

我們現在將創建我們的服務帳戶。

`kubectl create sa internal-app -n devwebapp`

現在對於我們的應用程式，我們將創建以下 yaml 檔案，您將在 day39 資料夾中找到它。

```
cat > devwebapp.yaml <<EOF
---
apiVersion: v1
kind: Pod
metadata:
  name: devwebapp
  labels:
    app: devwebapp
  annotations:
    vault.hashicorp.com/agent-inject: "true"
    vault.hashicorp.com/role: "devweb-app"
    vault.hashicorp.com/agent-inject-secret-credentials.txt: "secret/data/devwebapp/config"
spec:
  serviceAccountName: internal-app
  containers:
    - name: devwebapp
      image: jweissig/app:0.0.1
EOF
```
我們將使用以下命令將其部署到我們新創建的命名空間。

`kubectl create -f devwebapp.yaml -n devwebapp`

檢查 Pod 的狀態。

`kubectl get pods -n devwebapp`

最後，我們可以確認我們在應用程式中存儲了正確的憑證。

`kubectl exec --stdin=true --tty=true devwebapp -n devwebapp -c devwebapp -- cat /vault/secrets/credentials.txt`

可以在下面看到對此的確認，但希望您看到與我下面相同的輸出。

![](images/day39-9.png)

我還想添加來自 Nathanael Frappart 的資源，Nathanael 更詳細地涵蓋了所有內容。

[Vault kubernetes auth with AKS](https://nfrappart.github.io/2023/07/20/kubernetes-auth-aks.html)

請參閱 [Day 40](day40.md)
