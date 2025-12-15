---
title: '#90DaysOfDevOps - 實際操作備份和恢復 - 第 87 天'
published: false
description: 90DaysOfDevOps - 實際操作備份和恢復
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048717
---

## 實際操作備份和恢復

在上一節中，我們觸及了 [Kopia](https://kopia.io/) 一個開源備份工具，我們使用它將一些重要數據發送到本地 NAS 和一些基於雲的物件存儲。

在本節中，我想進入 Kubernetes 備份的世界。這是我們在挑戰早期涵蓋的平台 [The Big Picture: Kubernetes](Days/day49.md)。

我們將再次使用 minikube 集群，但這次我們將利用一些可用的附加組件。

### Kubernetes 集群設置

要設置 minikube 集群，我們將發出 `minikube start --addons volumesnapshots,csi-hostpath-driver --apiserver-port=6443 --container-runtime=containerd -p 90daysofdevops --kubernetes-version=1.21.2`，你會注意到我們正在使用 `volumesnapshots` 和 `csi-hostpath-driver`，因為我們將在進行備份時充分利用這些。

此時我知道我們還沒有部署 Kasten K10，但我們想在集群啟動時發出以下指令，但我們想註釋 volumesnapshotclass，以便 Kasten K10 可以使用它。

```Shell
kubectl annotate volumesnapshotclass csi-hostpath-snapclass \
    k10.kasten.io/is-snapshot-class=true
```

我們還將使用以下內容將預設 storageclass 從標準預設 storageclass 更改為 csi-hostpath storageclass。

```Shell
kubectl patch storageclass csi-hostpath-sc -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'

kubectl patch storageclass standard -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'
```

![](Images/Day87_Data1.png)

### 部署 Kasten K10

添加 Kasten Helm 儲存庫

`helm repo add kasten https://charts.kasten.io/`

我們也可以在這裡使用 `arkade kasten install k10`，但為了演示的目的，我們將運行以下步驟。[更多詳細資訊](https://blog.kasten.io/kasten-k10-goes-to-the-arkade)

創建命名空間並部署 K10，請注意這將需要大約 5 分鐘

`helm install k10 kasten/k10 --namespace=kasten-io --set auth.tokenAuth.enabled=true --set injectKanisterSidecar.enabled=true --set-string injectKanisterSidecar.namespaceSelector.matchLabels.k10/injectKanisterSidecar=true --create-namespace`

![](Images/Day87_Data1.png)

你可以通過運行以下指令來觀察 Pod 啟動。

`kubectl get pods -n kasten-io -w`

![](Images/Day87_Data3.png)

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

### 部署我們的有狀態應用程式

使用我們在 Kubernetes 部分中使用的有狀態應用程式。

![](Images/Day55_Kubernetes1.png)

你可以在這裡找到此應用程式的 YAML 配置檔案-> [pacman-stateful-demo.yaml](Kubernetes/pacman-stateful-demo.yaml)

![](Images/Day87_Data8.png)

我們可以使用 `kubectl get all -n pacman` 來檢查我們的 Pod 啟動。

![](Images/Day87_Data9.png)

在新終端機中，我們可以端口轉發 pacman 前端。`kubectl port-forward svc/pacman 9090:80 -n pacman`

在瀏覽器上打開另一個標籤頁到 http://localhost:9090/

![](Images/Day87_Data10.png)

花時間在後端 MongoDB 資料庫中記錄一些高分。

![](Images/Day87_Data11.png)

### 保護我們的高分

現在我們在資料庫中有一些關鍵任務數據，我們不想失去它。我們可以使用 Kasten K10 來保護整個應用程式。

如果我們返回 Kasten K10 儀表板標籤頁，你會看到我們的應用程式數量現在已從 1 增加到 2，因為我們的 Pacman 應用程式已添加到 Kubernetes 集群中。

![](Images/Day87_Data12.png)

如果你點擊 Applications 卡片，你將看到在集群中自動發現的應用程式。

![](Images/Day87_Data13.png)

使用 Kasten K10，我們可以利用基於存儲的快照，並將副本導出到物件存儲選項。

對於演示，我們將在集群中創建手動存儲快照，然後我們可以在高分中添加一些惡意數據，以模擬意外錯誤或不是嗎？

首先，我們可以使用下面的手動快照選項。

![](Images/Day87_Data14.png)

對於演示，我將保留所有預設值

![](Images/Day87_Data15.png)

回到儀表板，你會在作業運行時獲得狀態報告，然後完成時應該看起來像這個一樣成功。

![](Images/Day87_Data16.png)

### 故障場景

我們現在可以通過簡單地在應用程式中添加規定的不良更改來對關鍵任務數據進行致命更改。

正如你可以從下面看到的，我們有兩個輸入，我們可能不希望在生產關鍵任務資料庫中。

![](Images/Day87_Data17.png)

### 恢復數據

這是一個簡單的演示，在某種程度上不現實，儘管你見過刪除資料庫有多容易嗎？

現在我們想讓高分列表看起來更乾淨一些，以及我們在犯錯誤之前擁有的樣子。

回到 Applications 卡片和 Pacman 標籤頁，我們現在有 1 個恢復點可以使用來恢復。

![](Images/Day87_Data18.png)

當你選擇恢復時，你可以看到與該應用程式關聯的所有快照和導出。

![](Images/Day87_Data19.png)

選擇該恢復，將出現一個側邊窗口，我們將保留預設設置並點擊恢復。

![](Images/Day87_Data20.png)

確認你想要執行此操作。

![](Images/Day87_Data21.png)

然後你可以返回儀表板並查看恢復的進度。你應該看到類似這樣的內容。

![](Images/Day87_Data22.png)

但更重要的是，我們關鍵任務應用程式中的高分列表看起來如何。你必須再次啟動 Pacman 的端口轉發，正如我們之前涵蓋的那樣。

![](Images/Day87_Data23.png)

一個超級簡單的演示，只是真正觸及了 Kasten K10 在備份方面可以實現的表面。我將在未來創建一些更深入的視頻內容，涉及其中一些領域。我們還將使用 Kasten K10 來突出數據管理方面的其他突出領域，當涉及到災難恢復和數據的移動性時。

接下來，我們將查看應用程式一致性。

## 資源

- [Kubernetes Backup and Restore made easy!](https://www.youtube.com/watch?v=01qcYSck1c4&t=217s)
- [Kubernetes Backups, Upgrades, Migrations - with Velero](https://www.youtube.com/watch?v=zybLTQER0yY)
- [7 Database Paradigms](https://www.youtube.com/watch?v=W2Z7fbCLSTw&t=520s)
- [Disaster Recovery vs. Backup: What's the difference?](https://www.youtube.com/watch?v=07EHsPuKXc0)
- [Veeam Portability & Cloud Mobility](https://www.youtube.com/watch?v=hDBlTdzE6Us&t=3s)

我們[第 88 天](day88.md)見