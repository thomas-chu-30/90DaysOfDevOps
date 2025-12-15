---
title: '#90DaysOfDevOps - Rancher 概述 - 實作 - 第 53 天'
published: false
description: 90DaysOfDevOps - Rancher 概述 - 實作
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048742
---

## Rancher 概述 - 實作

在本節中，我們將查看 Rancher，到目前為止，我們所做的一切都在 cli 中使用 kubectl，但我們有一些很好的 UI 和多集群管理工具，可以為我們的運營團隊提供集群管理的良好可見性。

根據他們的[網站](https://rancher.com/)，Rancher 是

> Rancher 是一個完整的軟體堆疊，適用於採用容器的團隊。它解決了跨任何基礎設施管理多個 Kubernetes 集群的運營和安全挑戰，同時為 DevOps 團隊提供運行容器化工作負載的整合工具。

Rancher 使我們能夠從幾乎任何位置部署生產級 Kubernetes 集群，然後提供集中式身份驗證、訪問控制和可觀察性。我在上一節中提到，當談到 Kubernetes 以及你應該或可以在哪裡運行它們時，幾乎有令人難以置信的選擇，看看 Rancher，它們在哪裡並不重要。

### 部署 Rancher

我們需要做的第一件事是在本地工作站上部署 Rancher，有幾種方式和位置可以選擇來執行此步驟，對我來說，我想使用我的本地工作站並將 rancher 作為 docker 容器運行。通過運行下面的指令，我們將拉取容器圖像，然後可以訪問 rancher UI。

其他 rancher 部署方法可用 [Rancher Quick-Start-Guide](https://rancher.com/docs/rancher/v2.6/en/quick-start-guide/deployment/)

`sudo docker run -d --restart=unless-stopped -p 80:80 -p 443:443 --privileged rancher/rancher`

正如你在我們的 Docker Desktop 中看到的，我們有一個運行的 rancher 容器。

![](Images/Day53_Kubernetes1.png)

### 訪問 Rancher UI

使用上面運行的容器，我們應該能夠通過網頁導航到它。`https://localhost` 將顯示如下所示的登入頁面。

![](Images/Day53_Kubernetes2.png)

按照下面的說明獲取所需的密碼。因為我使用 Windows，我選擇使用 Windows 的 bash，因為需要 grep 指令。

![](Images/Day53_Kubernetes3.png)

然後我們可以使用上面的密碼登入，下一頁是我們可以定義新密碼的地方。

![](Images/Day53_Kubernetes4.png)

完成上述操作後，我們將登入並可以看到我們的開場畫面。作為 Rancher 部署的一部分，我們還將看到一個本地 K3s 集群被配置。

![](Images/Day53_Kubernetes5.png)

### Rancher 快速導覽

我們首先要查看的是本地部署的 K3S 集群，你可以在下面看到我們對集群內部發生的事情有很好的視覺效果。這是預設部署，我們還沒有向此集群部署任何內容。你可以看到它由 1 個節點組成，有 5 個部署。然後你還可以看到有關 Pod、核心和內存的一些統計資訊。

![](Images/Day53_Kubernetes6.png)

在左側選單中，我們還有一個 Apps & Marketplace 標籤，這允許我們選擇我們想在集群上運行的應用程式，如前所述，Rancher 為我們提供了運行或管理幾個不同集群的能力。使用市場，我們可以非常輕鬆地部署應用程式。

![](Images/Day53_Kubernetes7.png)

另一件要提到的事情是，如果你確實需要訪問 Rancher 管理的任何集群，在右上角你可以打開一個 kubectl shell 到選定的集群。

![](Images/Day53_Kubernetes8.png)

### 創建新集群

在過去的兩個課程中，我們在本地創建了一個 minikube 集群，我們使用 Vagrant 和 VirtualBox 創建了一個 3 節點 Kubernetes 集群，使用 Rancher 我們也可以創建集群。在 [Rancher 資料夾](Kubernetes/Rancher) 中，你將找到額外的 vagrant 檔案，這些檔案將構建相同的 3 個節點，但沒有創建 Kubernetes 集群的步驟（我們希望 Rancher 為我們執行此操作）

但是，我們確實希望安裝 docker 並更新 OS，所以你仍然會看到 `common.sh` 腳本在我們的每個節點上運行。這也將安裝 Kubeadm、Kubectl 等。但它不會運行 Kubeadm 指令來創建並將我們的節點加入集群。

我們可以導航到我們的 vagrant 資料夾位置，我們可以簡單地運行 `vagrant up`，這將開始在 VirtualBox 中創建我們的 3 個 VM 的過程。

![](Images/Day53_Kubernetes9.png)

現在我們已經有了節點或 VM 並準備就緒，我們可以使用 Rancher 創建我們的新 Kubernetes 集群。創建集群的第一個畫面為你提供了一些關於集群位置的選項，即你是否使用公共雲託管 Kubernetes 服務、vSphere 或其他東西。

![](Images/Day53_Kubernetes10.png)

我們將選擇「custom」，因為我們沒有使用整合平台之一。開場頁面是你定義集群名稱的地方（下面說本地，但你不能使用本地，我們的集群稱為 vagrant。）你可以在這裡定義 Kubernetes 版本、網路提供商和其他一些配置選項來啟動並運行 Kubernetes 集群。

![](Images/Day53_Kubernetes11.png)

下一頁將為你提供註冊碼，需要在每個節點上運行，並啟用適當的服務。etcd、control-plane 和 worker。對於我們的主節點，我們需要 etcd 和 control-plane，所以指令可以在下面看到。

![](Images/Day53_Kubernetes12.png)

```
sudo docker run -d --privileged --restart=unless-stopped --net=host -v /etc/kubernetes:/etc/kubernetes -v /var/run:/var/run  rancher/rancher-agent:v2.6.3 --server https://10.0.0.1 --token mpq8cbjjwrj88z4xmf7blqxcfmwdsmq92bmwjpphdkklfckk5hfwc2 --ca-checksum a81944423cbfeeb92be0784edebba1af799735ebc30ba8cbe5cc5f996094f30b --etcd --controlplane
```

如果網路配置正確，那麼你應該很快在 rancher 儀表板中看到以下內容，表明第一個主節點現在正在註冊，集群正在創建。

![](Images/Day53_Kubernetes13.png)

然後我們可以使用以下指令為每個工作節點重複註冊過程，一段時間後，你將擁有啟動並運行的集群，並能夠利用市場來部署應用程式。

```
sudo docker run -d --privileged --restart=unless-stopped --net=host -v /etc/kubernetes:/etc/kubernetes -v /var/run:/var/run  rancher/rancher-agent:v2.6.3 --server https://10.0.0.1 --token mpq8cbjjwrj88z4xmf7blqxcfmwdsmq92bmwjpphdkklfckk5hfwc2 --ca-checksum a81944423cbfeeb92be0784edebba1af799735ebc30ba8cbe5cc5f996094f30b --worker
```

![](Images/Day53_Kubernetes14.png)

在過去的 3 個課程中，我們使用了幾種不同的方式來啟動並運行 Kubernetes 集群，在剩餘的日子裡，我們將查看平台的應用程式方面，可以說是最重要的。我們將研究服務以及能夠在 Kubernetes 中配置和使用我們的服務。

我被告知，圍繞引導 rancher 節點的要求需要這些 VM 具有 4GB ram，否則它們會崩潰循環，我已經更新了，因為我們的工作節點有 2GB。

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

我們[第 54 天](day54.md)見