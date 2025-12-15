---
title: '#90DaysOfDevOps - 災難恢復 - 第 89 天'
published: false
description: 90DaysOfDevOps - 災難恢復
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048718
---

## 災難恢復

我們已經提到不同的故障場景將需要不同的恢復要求。當涉及到火災、洪水和血液場景時，我們可以將這些視為主要是災難情況，在這種情況下，我們可能需要工作負載在完全不同的位置盡快啟動並運行，或者至少具有接近零的恢復時間目標（RTO）。

這只有在自動化將完整應用程式堆疊複製到備用環境時才能大規模實現。

這允許跨雲區域、雲提供商或在本地和雲基礎設施之間進行快速故障轉移。

保持到目前為止的主題，我們將專注於如何使用 Kasten K10 使用我們在幾節前部署和配置的 minikube 集群來實現這一點。

然後我們將創建另一個也安裝了 Kasten K10 的 minikube 集群，作為我們的備用集群，理論上可以是任何位置。

Kasten K10 還具有內建功能，以確保如果運行它的 Kubernetes 集群發生某些事情，目錄數據被複製並在新集群中可用 [K10 災難恢復](https://docs.kasten.io/latest/operating/dr.html)。

### 向 K10 添加物件存儲

我們需要做的第一件事是添加一個物件存儲桶作為備份的目標位置。這不僅充當異地位置，而且我們還可以將其用作災難恢復源數據以從中恢復。

我已經清理了我們在上一節中為 Kanister 演示創建的 S3 桶。

![](Images/Day89_Data1.png)

端口轉發以訪問 K10 儀表板，打開新終端機以運行以下指令：

`kubectl --namespace kasten-io port-forward service/gateway 8080:8000`

Kasten 儀表板將在 `http://127.0.0.1:8080/k10/#/` 可用

![](Images/Day87_Data4.png)

要使用儀表板進行身份驗證，我們現在需要可以通過以下指令獲取的令牌。

```Shell
TOKEN_NAME=$(kubectl get secret --namespace kasten-io|grep k10-k10-token | cut -d " " -f 1)
TOKEN=$(kubectl get secret --namespace kasten-io $TOKEN_NAME -o jsonpath="{.data.token}" | base64 --decode)

echo "Token value: "
echo $TOKEN
```

![](Images/Day87_Data5.png)

現在我們獲取此令牌並將其輸入到瀏覽器中，然後你將被提示輸入電子郵件和公司名稱。

![](Images/Day87_Data6.png)

然後我們可以訪問 Kasten K10 儀表板。

![](Images/Day87_Data7.png)

現在我們回到了 Kasten K10 儀表板，我們可以添加位置 profile，選擇頁面頂部的「Settings」和「New Profile」。

![](Images/Day89_Data2.png)

你可以從下面的圖像中看到，當涉及到此位置 profile 的位置時，我們有一個選擇，我們將選擇 Amazon S3，我們將添加我們的敏感訪問憑證、區域和桶名稱。

![](Images/Day89_Data3.png)

如果我們在 New Profile 創建窗口上向下滾動，你會看到，我們還可以啟用不可變備份，它利用 S3 Object Lock API。對於這個演示，我們不會使用它。

![](Images/Day89_Data4.png)

點擊「Save Profile」，你現在可以看到我們新創建或添加的位置 profile，如下所示。

![](Images/Day89_Data5.png)

### 創建策略以保護 Pac-Man 應用到物件存儲

在上一節中，我們只創建了 Pac-Man 應用程式的臨時快照，因此我們需要創建一個備份策略，該策略將把應用程式備份發送到我們新創建的物件存儲位置。

如果你返回儀表板並選擇 Policy 卡片，你將看到如下所示的螢幕。選擇「Create New Policy」。

![](Images/Day89_Data6.png)

首先，我們可以給策略一個有用的名稱和描述。我們還可以定義備份頻率，為了演示目的，我使用按需。

![](Images/Day89_Data7.png)

接下來，我們想通過快照導出啟用備份，這意味著我們想將數據發送到位置 profile。如果你有多個，你可以選擇要將備份發送到哪一個。

![](Images/Day89_Data8.png)

接下來，我們通過名稱或標籤選擇應用程式，我將按名稱選擇並選擇所有資源。

![](Images/Day89_Data9.png)

在 Advanced settings 下，我們不會使用這些中的任何一個，但基於我們[昨天對 Kanister 的演練](https://github.com/MichaelCade/90DaysOfDevOps/blob/main/Days/day88.md)，我們也可以利用 Kanister 作為 Kasten K10 的一部分來獲取那些應用程式一致的數據副本。

![](Images/Day89_Data10.png)

最後，選擇「Create Policy」，你現在將在 Policy 窗口中看到策略。

![](Images/Day89_Data11.png)

在創建的策略底部，你將有「Show import details」，我們需要此字串才能導入到備用集群。現在將其複製到安全的地方。

![](Images/Day89_Data12.png)

在我們繼續之前，我們只需要選擇「run once」來將備份發送到物件存儲桶。

![](Images/Day89_Data13.png)

下面，截圖只是為了顯示成功的備份和數據導出。

![](Images/Day89_Data14.png)

### 創建新的 MiniKube 集群並部署 K10

然後我們需要部署第二個 Kubernetes 集群，這可以是任何支持的 Kubernetes 版本，包括 OpenShift，為了教育，我們將使用非常免費的 MiniKube 版本，名稱不同。

使用 `minikube start --addons volumesnapshots,csi-hostpath-driver --apiserver-port=6443 --container-runtime=containerd -p standby --kubernetes-version=1.21.2`，我們可以創建新集群。

![](Images/Day89_Data15.png)

然後我們可以使用以下內容在此集群中部署 Kasten K10：

`helm install k10 kasten/k10 --namespace=kasten-io --set auth.tokenAuth.enabled=true --set injectKanisterSidecar.enabled=true --set-string injectKanisterSidecar.namespaceSelector.matchLabels.k10/injectKanisterSidecar=true --create-namespace`

這需要一段時間，但與此同時，我們可以使用 `kubectl get pods -n kasten-io -w` 來觀察 Pod 進入運行狀態的進度。

值得注意的是，因為我們使用 MiniKube，當我們運行導入策略時，應用程式只會運行，此備用集群上的 storageclass 是相同的。但是，我們將在最後一節中涵蓋移動性和轉換。

當 Pod 啟動並運行時，我們可以遵循我們在其他集群的先前步驟中經歷的步驟。

端口轉發以訪問 K10 儀表板，打開新終端機以運行以下指令

`kubectl --namespace kasten-io port-forward service/gateway 8080:8000`

Kasten 儀表板將在 `http://127.0.0.1:8080/k10/#/` 可用

![](Images/Day87_Data4.png)

要使用儀表板進行身份驗證，我們現在需要可以通過以下指令獲取的令牌。

```Shell
TOKEN_NAME=$(kubectl get secret --namespace kasten-io|grep k10-k10-token | cut -d " " -f 1)
TOKEN=$(kubectl get secret --namespace kasten-io $TOKEN_NAME -o jsonpath="{.data.token}" | base64 --decode)

echo "Token value: "
echo $TOKEN
```

![](Images/Day87_Data5.png)

現在我們獲取此令牌並將其輸入到瀏覽器中，然後你將被提示輸入電子郵件和公司名稱。

![](Images/Day87_Data6.png)

然後我們可以訪問 Kasten K10 儀表板。

![](Images/Day87_Data7.png)

### 將 Pac-Man 導入新的 MiniKube 集群

此時，我們現在能夠在備用集群中創建導入策略，並連接到物件存儲備份並確定我們希望這看起來如何。

首先，我們添加我們在另一個集群上早期經歷的位置 Profile，在這裡展示深色模式以顯示生產系統和 DR 備用位置之間的差異。

![](Images/Day89_Data16.png)

現在我們返回儀表板並進入 policies 標籤頁以創建新策略。

![](Images/Day89_Data17.png)

根據下面的圖像創建導入策略。完成後，我們可以創建策略。這裡有選項可以在導入後恢復，有些人可能想要這個選項，這將在完成時恢復到我們的備用集群中。我們還可以在恢復時更改應用程式的配置，這是我在 [第 90 天](day90.md) 中記錄的內容。

![](Images/Day89_Data18.png)

我選擇按需導入，但你可以設置何時希望此導入發生的計劃。因此，我將運行一次。

![](Images/Day89_Data19.png)

你可以看到下面成功的導入策略作業。

![](Images/Day89_Data20.png)

如果我們現在返回儀表板並進入 Applications 卡片，我們可以選擇下拉列表，在下面看到「Removed」，你將在這裡看到我們的應用程式。選擇 Restore

![](Images/Day89_Data21.png)

在這裡，我們可以看到我們可用的恢復點；這是我們在主集群上針對 Pac-Man 應用程式運行的備份作業。

![](Images/Day89_Data22.png)

我不會更改任何預設值，因為我想在下一節中更詳細地涵蓋這一點。

![](Images/Day89_Data23.png)

當你點擊「Restore」時，它將提示你確認。

![](Images/Day89_Data24.png)

我們可以看到下面我們在備用集群中，如果我們檢查 Pod，我們可以看到我們有運行的應用程式。

![](Images/Day89_Data25.png)

然後我們可以端口轉發（在真實/生產環境中，你不需要此步驟來訪問應用程式，你將使用 ingress）

![](Images/Day89_Data26.png)

接下來，我們將查看應用程式移動性和轉換。

## 資源

- [Kubernetes Backup and Restore made easy!](https://www.youtube.com/watch?v=01qcYSck1c4&t=217s)
- [Kubernetes Backups, Upgrades, Migrations - with Velero](https://www.youtube.com/watch?v=zybLTQER0yY)
- [7 Database Paradigms](https://www.youtube.com/watch?v=W2Z7fbCLSTw&t=520s)
- [Disaster Recovery vs. Backup: What's the difference?](https://www.youtube.com/watch?v=07EHsPuKXc0)
- [Veeam Portability & Cloud Mobility](https://www.youtube.com/watch?v=hDBlTdzE6Us&t=3s)

我們[第 90 天](day90.md)見