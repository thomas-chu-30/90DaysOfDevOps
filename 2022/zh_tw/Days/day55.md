---
title: '#90DaysOfDevOps - Kubernetes 中的狀態和 Ingress - 第 55 天'
published: false
description: 90DaysOfDevOps - Kubernetes 中的狀態和 Ingress
tags: 'DevOps, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048779
---

## Kubernetes 中的狀態和 Ingress

在這個 Kubernetes 的結束部分，我們將查看狀態和 ingress。

到目前為止，我們所說的一切都是關於無狀態的，無狀態確實是我們的應用程式不關心它使用哪個網路並且不需要任何永久儲存的地方。而有狀態應用程式和資料庫，例如，為了使此類應用程式正確運行，你需要確保 Pod 可以通過不變的唯一身份（主機名、IP 等）相互訪問。有狀態應用程式的範例包括 MySQL 集群、Redis、Kafka、MongoDB 等。基本上，通過任何儲存數據的應用程式。

### 有狀態應用程式

StatefulSets 表示一組具有唯一、持久身份和穩定主機名的 Pod，Kubernetes 無論它們被調度到哪裡都維護這些。任何給定 StatefulSet Pod 的狀態資訊和其他彈性數據都維護在與 StatefulSet 相關聯的持久磁碟儲存中。

### Deployment vs StatefulSet

- 複製有狀態應用程式更困難。
- 在部署中複製我們的 Pod（無狀態應用程式）是相同且可互換的。
- 使用隨機哈希按隨機順序創建 Pod
- 一個服務將負載平衡到任何 Pod。

當談到 StatefulSets 或有狀態應用程式時，上述情況更困難。

- 不能同時創建或刪除。
- 不能隨機尋址。
- 副本 Pod 不相同

你很快會在我們的演示中看到的是，每個 Pod 都有自己的身份。對於無狀態應用程式，你會看到隨機名稱。例如 `app-7469bbb6d7-9mhxd`，而有狀態應用程式將更符合 `mongo-0`，然後當擴展時，它將創建一個名為 `mongo-1` 的新 Pod。

這些 Pod 是從相同的規範創建的，但它們不可互換。每個 StatefulSet Pod 在任何重新調度中都有一個持久標識符。這是必要的，因為當我們需要諸如資料庫之類的有狀態工作負載時，我們需要讀寫資料庫，我們不能有兩個 Pod 在沒有意識的情況下同時寫入，因為這會給我們數據不一致。我們需要確保在任何給定時間只有一個 Pod 正在寫入資料庫，但是我們可以有多個 Pod 讀取該數據。

StatefulSet 中的每個 Pod 都可以訪問其持久卷和資料庫的副本副本以進行讀取，這會從主節點持續更新。同樣有趣的是，每個 Pod 也會將其 Pod 狀態存儲在此持久卷中，如果 `mongo-0` 死亡，那麼當提供新 Pod 時，它將接管存儲在儲存中的 Pod 狀態。

TLDR；StatefulSets vs Deployments

- 可預測的 Pod 名稱 = `mongo-0`
- 固定的單獨 DNS 名稱
- Pod 身份 - 保留狀態，保留角色
- 複製有狀態應用程式很複雜
  - 有很多事情你必須做：
    - 配置克隆和數據同步。
    - 使遠程共享儲存可用。
    - 管理和備份

### 持久卷 | 聲明 | StorageClass

如何在 Kubernetes 中持久化數據？

我們上面提到，當我們有一個有狀態應用程式時，我們必須在某處存儲狀態，這就是需要卷的地方，開箱即用的 Kubernetes 不提供開箱即用的持久性。

我們需要一個不依賴於 Pod 生命週期的儲存層。此儲存應該可用並可從所有 Kubernetes 節點訪問。儲存也應該在 Kubernetes 集群外部，以便即使 Kubernetes 集群崩潰也能生存。

### 持久卷

- 用於存儲數據的集群資源（如 CPU 和 RAM）。
- 通過 YAML 檔案創建
- 需要實際的物理儲存（NAS）
- 與 Kubernetes 集群的外部整合
- 你可以在儲存中擁有不同類型的儲存。
- PV 不是命名空間的
- 本地儲存可用，但它將特定於集群中的一個節點
- 資料庫持久性應使用遠程儲存（NAS）

### 持久卷聲明

上面單獨的持久卷可以存在並可用，但除非被應用程式聲明，否則不會被使用。

- 通過 YAML 檔案創建
- 持久卷聲明用於 Pod 配置（卷屬性）
- PVC 與 Pod 存在於同一命名空間中
- 卷掛載到 Pod 中
- Pod 可以有多種不同的卷類型（ConfigMap、Secret、PVC）

思考 PV 和 PVC 的另一種方式是

PV 由 Kubernetes 管理員創建
PVC 由用戶或應用程式開發人員創建

我們還有另外兩種類型的卷，我們不會詳細介紹，但值得一提：

### ConfigMaps | Secrets

- Pod 的配置檔案。
- Pod 的證書檔案。

### StorageClass

- 通過 YAML 檔案創建
- 當 PVC 聲明時動態配置持久卷
- 每個儲存後端都有自己的 provisioner
- 儲存後端在 YAML 中定義（通過 provisioner 屬性）
- 抽象底層儲存提供商
- 定義該儲存的參數

### 演練時間

在昨天的課程中，我們演練了創建無狀態應用程式，這裡我們想做同樣的事情，但我們想使用 minikube 集群來部署有狀態工作負載。

回顧我們正在使用的 minikube 指令，以具有使用持久性的能力和附加元件是 `minikube start --addons volumesnapshots,csi-hostpath-driver --apiserver-port=6443 --container-runtime=containerd -p mc-demo --kubernetes-version=1.21.2`

此指令使用 CSI-hostpath-driver，這為我們提供了 storageclass，我稍後會展示。

應用程式的構建如下所示：

![](Images/Day55_Kubernetes1.png)

你可以在這裡找到此應用程式的 YAML 配置檔案 [pacman-stateful-demo.yaml](Kubernetes)

### StorageClass 配置

不過，在開始部署應用程式之前，我們應該再運行一個步驟，那就是確保我們的 storageclass (CSI-hostpath-sc) 是我們的預設值。我們可以首先通過運行 `kubectl get storageclass` 指令來檢查這一點，但開箱即用，minikube 集群將顯示標準 storageclass 作為預設值，所以我們必須使用以下指令更改它。

第一個指令將使我們的 CSI-hostpath-sc storageclass 成為預設值。

`kubectl patch storageclass csi-hostpath-sc -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'`

此指令將從標準 StorageClass 中移除預設註釋。

`kubectl patch storageclass standard -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'`

![](Images/Day55_Kubernetes2.png)

我們從集群中沒有 Pacman 命名空間開始。`kubectl get namespace`

![](Images/Day55_Kubernetes3.png)

然後我們將部署 YAML 檔案。`kubectl create -f pacman-stateful-demo.yaml` 你可以從此指令看到我們正在 Kubernetes 集群中創建幾個物件。

![](Images/Day55_Kubernetes4.png)

我們現在有了新創建的命名空間。

![](Images/Day55_Kubernetes5.png)

然後你可以從下一張圖像和指令 `kubectl get all -n pacman` 中看到，我們的命名空間內發生了幾件事。我們有運行 NodeJS Web 前端的 Pod，我們有運行後端資料庫的 mongo。有服務可以訪問 Pacman 和 mongo 的 Pod。我們有 Pacman 的部署和 mongo 的 statefulset。

![](Images/Day55_Kubernetes6.png)

我們還有持久卷和持久卷聲明，通過運行 `kubectl get pv` 將為我們提供非命名空間的持久卷，運行 `kubectl get pvc -n pacman` 將為我們提供命名空間的持久卷聲明。

![](Images/Day55_Kubernetes7.png)

### 玩遊戲 | 我的意思是訪問我們的關鍵任務應用程式

因為我們使用 Minikube，正如在無狀態應用程式中提到的，在訪問應用程式時我們需要克服一些障礙，但是，如果我們在集群內有 ingress 或負載平衡器的訪問權限，服務會自動從中獲取 IP 以獲得外部訪問。（你可以在上面 Pacman 命名空間中所有組件的圖像中看到這一點）。

對於這個演示，我們將使用端口轉發方法來訪問應用程式。通過打開新終端機並運行以下 `kubectl port-forward svc/pacman 9090:80 -n pacman` 指令，打開瀏覽器，我們現在可以訪問應用程式。如果你在 AWS 或特定位置運行此操作，那麼這也會報告雲和區域以及主機，這等於 Kubernetes 內的 Pod，再次你可以回顧並在我們上面的截圖中看到此 Pod 名稱。

![](Images/Day55_Kubernetes8.png)

現在我們可以去創建一個高分，然後它將存儲在資料庫中。

![](Images/Day55_Kubernetes9.png)

好的，很好，我們有一個高分，但如果我們去刪除 `mongo-0` Pod 會發生什麼？通過運行 `kubectl delete pod mongo-0 -n pacman` 我可以刪除它，如果你仍在應用程式中，你會看到高分至少幾秒鐘不可用。

![](Images/Day55_Kubernetes10.png)

現在如果我回到我的遊戲，我可以創建一個新遊戲並查看我的高分。不過，你真正相信我的唯一方法是如果你嘗試一下並在社交媒體上分享你的高分！

![](Images/Day55_Kubernetes11.png)

使用部署，我們可以使用我們在上一節中涵蓋的指令來擴展它，但特別是在這裡，特別是如果你想舉辦一個巨大的 Pacman 派對，那麼你可以使用 `kubectl scale deployment pacman --replicas=10 -n pacman` 來擴展它

![](Images/Day55_Kubernetes12.png)

### Ingress 解釋

在我們結束 Kubernetes 之前，我還想談談 Kubernetes 的一個巨大方面，那就是 ingress。

### 什麼是 ingress？

到目前為止，在我們的範例中，我們使用了端口轉發，或者我們在 minikube 中使用了特定指令來訪問應用程式，但在生產中這不會工作。我們將需要一種更好的方式來在多個用戶的情況下大規模訪問應用程式。

我們還談到了 NodePort 作為一個選項，但同樣這應該只用於測試目的。

Ingress 為我們提供了一種更好的公開應用程式的方式，這允許我們在 Kubernetes 集群內定義路由規則。

對於 ingress，我們將創建一個轉發請求到應用程式的內部服務。

### 何時需要 ingress？

如果你使用雲提供商，託管 Kubernetes 產品，他們很可能會為集群提供 ingress 選項，或者他們為你提供負載平衡器選項。你不必自己實現這一點，這是託管 Kubernetes 的好處之一。

如果你正在運行集群，那麼你需要配置入口點。

### 在 Minikube 上配置 Ingress

在我名為 mc-demo 的特定運行集群上，我可以運行以下指令在集群上啟用 ingress。

`minikube --profile='mc-demo' addons enable ingress`

![](Images/Day55_Kubernetes13.png)

如果我們現在檢查命名空間，你會看到我們有一個新的 ingress-nginx 命名空間。`kubectl get ns`

![](Images/Day55_Kubernetes14.png)

現在我們必須創建 ingress YAML 配置以命中我們的 Pacman 服務，我已將此檔案添加到儲存庫 [pacman-ingress.yaml](Kubernetes)

然後我們可以在 ingress 命名空間中使用 `kubectl create -f Pacman-ingress.yaml` 創建它

![](Images/Day55_Kubernetes15.png)

然後如果我們運行 `kubectl get ingress -n Pacman`

![](Images/Day55_Kubernetes16.png)

然後我被告知，因為我們在 Windows 的 WSL2 上運行 minikube，我們必須使用 `minikube tunnel --profile=mc-demo` 創建 minikube 隧道

但我仍然無法訪問 192.168.49.2 並玩我的 Pacman 遊戲。

如果任何人已經或可以在 Windows 和 WSL 上使其工作，我將不勝感激反饋。我將在儲存庫上為此提出問題，一旦我有時間和修復，我會回來。

更新：我覺得這個部落格有助於識別可能導致此問題在 WSL 上不工作的原因 [Configuring Ingress to run Minikube on WSL2 using Docker runtime](https://hellokube.dev/posts/configure-minikube-ingress-on-wsl2/)

## 資源

如果你有使用過的免費資源，請隨時通過 PR 將它們添加到儲存庫中，我很樂意包含它們。

- [Kubernetes StatefulSet simply explained](https://www.youtube.com/watch?v=pPQKAR1pA9U)
- [Kubernetes Volumes explained](https://www.youtube.com/watch?v=0swOh5C3OVM)
- [Kubernetes Ingress Tutorial for Beginners](https://www.youtube.com/watch?v=80Ew_fsV4rM)
- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [TechWorld with Nana - Kubernetes Tutorial for Beginners [FULL COURSE in 4 Hours]](https://www.youtube.com/watch?v=X48VuDVv0do)
- [TechWorld with Nana - Kubernetes Crash Course for Absolute Beginners](https://www.youtube.com/watch?v=s_o8dwzRlu4)
- [Kunal Kushwaha - Kubernetes Tutorial for Beginners | What is Kubernetes? Architecture Simplified!](https://www.youtube.com/watch?v=KVBON1lA9N8)

這結束了我們的 Kubernetes 部分，我們可以涵蓋 Kubernetes 的額外內容還有很多，7 天給了我們基礎知識，但人們正在通過 [100DaysOfKubernetes](https://100daysofkubernetes.io/overview.html) 進行，你可以在那裡真正深入了解細節。

接下來，我們將查看基礎設施即代碼以及它從 DevOps 角度發揮的重要作用。

我們[第 56 天](day56.md)見