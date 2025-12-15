---
title: '#90DaysOfDevOps - Kubernetes 應用程式部署 - 第 54 天'
published: false
description: 90DaysOfDevOps - Kubernetes 應用程式部署
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048764
---

## Kubernetes 應用程式部署

現在我們終於可以將一些應用程式部署到我們的集群中了，有人會說這就是 Kubernetes 存在的原因，用於應用程式交付。

這裡的想法是，我們可以獲取容器圖像，現在將它們作為 Pod 部署到我們的 Kubernetes 集群中，以利用 Kubernetes 作為容器編排器。

### 將應用程式部署到 Kubernetes

有幾種方法可以將應用程式部署到 Kubernetes 集群中，我們將涵蓋兩種最常見的方法，即 YAML 檔案和 Helm charts。

我們將使用 minikube 集群進行這些應用程式部署。我們將逐步介紹一些先前提到的 Kubernetes 組件或構建塊。

在整個部分和容器部分中，我們都討論了圖像和 Kubernetes 的好處，以及我們如何在這個平台上輕鬆處理擴展。

在這個第一步中，我們將簡單地在 minikube 集群中創建一個無狀態應用程式。我們將在第一次演示中使用事實上的標準無狀態應用程式 `nginx`，我們將配置一個 Deployment，這將為我們提供 Pod，然後我們還將創建一個服務，這將允許我們導航到由 nginx Pod 託管的簡單 Web 伺服器。所有這些都將包含在命名空間中。

![](Images/Day54_Kubernetes1.png)

### 創建 YAML

在第一個演示中，我們想用 YAML 定義我們所做的一切，我們可以有一個關於 YAML 的完整部分，但我將略過這一點，並在最後留下一些資源，這些資源將更詳細地涵蓋 YAML。

我們可以將以下內容創建為一個 YAML 檔案，或者我們可以為應用程式的每個方面分解它，即這可以是命名空間、部署和服務創建的單獨檔案，但在這個檔案中，下面我們使用 `---` 在一個檔案中分隔這些。你可以找到此檔案位於[這裡](Kubernetes)（檔案名：- nginx-stateless-demo.YAML）

```Yaml
apiVersion: v1
kind: Namespace
metadata:
  name: nginx
  "labels": {
    "name": "nginx"
  }
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  namespace: nginx
spec:
  selector:
    app: nginx-deployment
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

### 檢查我們的集群

在部署任何內容之前，我們應該確保沒有名為 `nginx` 的現有命名空間，我們可以通過運行 `kubectl get namespace` 指令來執行此操作，正如你在下面看到的，我們沒有名為 `nginx` 的命名空間

![](Images/Day54_Kubernetes2.png)

### 部署應用程式的時間

現在我們準備將應用程式部署到 minikube 集群，這個相同的過程將適用於任何其他 Kubernetes 集群。

我們需要導航到 YAML 檔案位置，然後我們可以運行 `kubectl create -f nginx-stateless-demo.yaml`，然後你會看到已創建 3 個物件，我們有一個命名空間、部署和服務。

![](Images/Day54_Kubernetes3.png)

讓我們再次運行指令以查看集群中可用的命名空間 `kubectl get namespace`，你現在可以看到我們有新的命名空間。

![](Images/Day54_Kubernetes5.png)

如果我們然後使用 `kubectl get pods -n nginx` 檢查命名空間的 Pod，你會看到我們有 1 個 Pod 處於就緒和運行狀態。

![](Images/Day54_Kubernetes4.png)

我們也可以通過運行 `kubectl get service -n nginx` 來檢查我們的服務是否已創建

![](Images/Day54_Kubernetes6.png)

最後，我們可以檢查我們的部署，部署是我們保持所需配置的位置和方式。

![](Images/Day54_Kubernetes7.png)

上面需要一些值得了解的指令，但你也可以使用 `kubectl get all -n nginx` 來查看我們用該 YAML 檔案部署的所有內容。

![](Images/Day54_Kubernetes8.png)

你會注意到上面我們還有一個 replicaset，在我們的部署中，我們定義了我們想要部署的圖像副本數量。這最初設置為 1，但如果我們想快速擴展應用程式，我們可以通過幾種方式執行此操作。

我們可以使用 `kubectl edit deployment nginx-deployment -n nginx` 編輯檔案，這將在終端機中打開文字編輯器，並使你能夠修改部署。

![](Images/Day54_Kubernetes9.png)

如果在終端機中的文字編輯器中保存上述內容時沒有問題並且使用了正確的格式，那麼你應該在命名空間中看到額外的部署。

![](Images/Day54_Kubernetes10.png)

我們也可以使用 kubectl 和 `kubectl scale deployment nginx-deployment --replicas=10 -n nginx` 更改副本數量

![](Images/Day54_Kubernetes11.png)

如果我們希望使用任一方法，我們也可以使用此方法將應用程式縮小回 1。我使用了編輯選項，但你也可以使用上面的 scale 指令。

![](Images/Day54_Kubernetes12.png)

希望在這裡你可以看到用例，不僅啟動和關閉非常快，而且我們能夠快速擴展和縮小應用程式。如果這是一個 Web 伺服器，我們可以在繁忙時擴展，在負載安靜時縮小。

### 公開我們的應用程式

但是我們如何訪問我們的 Web 伺服器？

如果你查看上面的服務，你會看到沒有可用的外部 IP，所以我們不能只是打開網頁瀏覽器並期望它神奇地出現在那裡。對於訪問，我們有幾個選項。

**ClusterIP** - 你看到的 IP 是 ClusterIP，這在集群的內部網路上。只有集群內的東西才能到達此 IP。

**NodePort** - 使用 NAT 在集群中每個選定節點的同一端口上公開服務。

**LoadBalancer** - 在當前雲中創建外部負載平衡器，我們使用 minikube，但如果你構建了自己的 Kubernetes 集群，即我們在 VirtualBox 中所做的，你需要將 LoadBalancer（如 metallb）部署到集群中以提供此功能。

**Port-Forward** - 我們還有端口轉發的能力，這允許你從 localhost 訪問和與內部 Kubernetes 集群進程交互。此選項僅用於測試和故障查找。

我們現在有幾個選項可以選擇，Minikube 有一些限制或差異，我應該說與功能齊全的 Kubernetes 集群相比。

我們可以簡單地運行以下指令，使用本地工作站進行端口轉發訪問。

`kubectl port-forward deployment/nginx-deployment -n nginx 8090:80`

![](Images/Day54_Kubernetes13.png)

請注意，當你運行上面的指令時，此終端機現在不可用，因為這充當到你本地機器和端口的端口轉發。

![](Images/Day54_Kubernetes14.png)

我們現在將專門使用 Minikube 演練如何公開應用程式。我們也可以使用 minikube 創建一個 URL 來連接到服務 [更多詳細資訊](https://minikube.sigs.k8s.io/docs/commands/service/)

首先，我們將使用 `kubectl delete service nginx-service -n nginx` 刪除服務

接下來，我們將使用 `kubectl expose deployment nginx-deployment --name nginx-service --namespace nginx --port=80 --type=NodePort` 創建一個新服務，請注意這裡我們將使用 expose 並將類型更改為 NodePort。

![](Images/Day54_Kubernetes15.png)

最後在新的終端機中運行 `minikube --profile='mc-demo' service nginx-service --URL -n nginx` 為我們的服務創建隧道。

![](Images/Day54_Kubernetes16.png)

打開瀏覽器或控制並點擊終端機中的連結。

![](Images/Day54_Kubernetes17.png)

### Helm

Helm 是我們可以部署應用程式的另一種方式。被稱為「Kubernetes 的套件管理器」你可以在[這裡](https://helm.sh/)找到更多資訊

Helm 是 Kubernetes 的套件管理器。Helm 可以被視為 Kubernetes 的 yum 或 apt 等效項。Helm 部署 charts，你可以將其視為打包的應用程式，它是預配置應用程式資源的藍圖，可以作為一個易於使用的 chart 部署。然後你可以使用一組不同的配置部署 chart 的另一個版本。

他們有一個網站，你可以在其中瀏覽所有可用的 Helm charts，當然，你可以創建自己的。文件也很清晰簡潔，不像我第一次在這個領域的所有其他新詞中聽到 helm 這個術語時那麼令人生畏。

讓 Helm 啟動並運行或安裝非常簡單。簡單。你可以在這裡找到幾乎所有發行版的二進位檔和下載連結，包括你的 RaspberryPi arm64 設備。

或者你可以使用安裝程式腳本，這裡的好處是將下載並安裝最新版本的 helm。

```Shell
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3

chmod 700 get_helm.sh

./get_helm.sh
```

最後，還有使用套件管理器作為應用程式管理器的選項，mac 的 homebrew，windows 的 chocolatey，Ubuntu/Debian 的 apt，還有 snap 和 pkg。

到目前為止，Helm 似乎是在集群中下載和安裝不同測試應用程式的首選方式。

這裡要連結的一個好資源是 [ArtifactHUB](https://artifacthub.io/)，這是一個查找、安裝和發布 Kubernetes 套件的資源。我還將提到 [KubeApps](https://kubeapps.com/)，這是一個顯示 helm charts 的 UI。

### 我們將在 Kubernetes 系列中涵蓋的內容

我們已經開始涵蓋下面提到的一些內容，但我們明天將在第二個集群部署中進行更多實際操作，然後我們可以開始將應用程式部署到我們的集群中。

- Kubernetes 架構
- Kubectl 指令
- Kubernetes YAML
- Kubernetes Ingress
- Kubernetes Services
- Helm 套件管理器
- 持久儲存
- 有狀態應用程式

## 資源

如果你有使用過的免費資源，請隨時通過 PR 將它們添加到儲存庫中，我很樂意包含它們。

- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [TechWorld with Nana - Kubernetes Tutorial for Beginners [FULL COURSE in 4 Hours]](https://www.youtube.com/watch?v=X48VuDVv0do)
- [TechWorld with Nana - Kubernetes Crash Course for Absolute Beginners](https://www.youtube.com/watch?v=s_o8dwzRlu4)
- [Kunal Kushwaha - Kubernetes Tutorial for Beginners | What is Kubernetes? Architecture Simplified!](https://www.youtube.com/watch?v=KVBON1lA9N8)

我們[第 55 天](day55.md)見