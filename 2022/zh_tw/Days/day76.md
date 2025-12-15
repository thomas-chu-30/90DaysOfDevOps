---
title: '#90DaysOfDevOps - ArgoCD 概述 - 第 76 天'
published: false
description: 90DaysOfDevOps - ArgoCD 概述
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048809
---

## ArgoCD 概述

「Argo CD 是一個聲明式、GitOps 持續交付工具，用於 Kubernetes」

版本控制是這裡的關鍵，曾經對環境進行即興更改並且沒有回憶起該更改，因為燈亮著並且一切都是綠色的，你繼續繼續前進？曾經進行更改並破壞一切或部分一切？你可能知道你進行了更改，你可以快速回滾更改，那個壞腳本或拼寫錯誤。現在曾經大規模地這樣做，也許不是你，或者也許沒有立即發現，現在業務正在遭受損失。因此，版本控制很重要。不僅如此，「應用程式定義、配置和環境應該是聲明式的，並且版本控制。」除此之外（來自 ArgoCD），他們還提到「應用程式部署和生命週期管理應該是自動化的、可審計的，並且易於理解。」

從運營背景但已經在基礎設施即代碼方面做了很多工作，這是確保所有這些好東西在持續部署/交付工作流程中得到照顧的下一步。

[什麼是 ArgoCD](https://argo-cd.readthedocs.io/en/stable/)

### 部署 ArgoCD

我們將再次在本地使用值得信賴的 minikube Kubernetes 集群進行此部署。

```Shell
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

![](Images/Day76_CICD1.png)

確保所有 ArgoCD Pod 都啟動並運行 `kubectl get pods -n argocd`

![](Images/Day76_CICD2.png)

另外，讓我們檢查我們在命名空間中部署的所有內容 `kubectl get all -n argocd`

![](Images/Day76_CICD3.png)

當上面看起來不錯時，我們然後應該考慮通過端口轉發訪問它。使用 `kubectl port-forward svc/argocd-server -n argocd 8080:443` 指令。在新終端機中執行此操作。

然後打開新的網頁瀏覽器並前往 `https://localhost:8080`

![](Images/Day76_CICD4.png)

要登入，你需要用戶名 admin，然後獲取你創建的密鑰作為密碼，使用 `kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo`

![](Images/Day76_CICD5.png)

一旦你登入，你將擁有空白的 CD 畫布。

![](Images/Day76_CICD6.png)

### 部署我們的應用程式

現在我們已經啟動並運行了 ArgoCD，我們現在可以開始使用它從 Git 儲存庫以及 Helm 部署應用程式。

我想部署的應用程式是 Pac-Man，是的，沒錯，著名的遊戲，我在很多演示中使用它來進行數據管理，這不會是我們最後一次看到 Pac-Man。

你可以在這裡找到 [Pac-Man](https://github.com/MichaelCade/pacman-tanzu.git) 的儲存庫。

與其使用截圖逐步進行，我認為創建一個演練視頻來涵蓋此特定應用程式部署所採取的步驟會更容易。

[ArgoCD Demo - 90DaysOfDevOps](https://www.youtube.com/watch?v=w6J413_j0hA)

注意 - 在視頻中，有一個服務永遠不滿足，因為應用程式健康狀況是健康的，這是因為為 Pacman 服務設置的 LoadBalancer 類型是 pending，在 Minikube 中我們沒有配置負載平衡器。如果你想測試這個，你可以將服務的 YAML 更改為 ClusterIP 並使用端口轉發來玩遊戲。

這結束了 CICD Pipelines 部分，我感覺目前業界對這個領域有很多關注，你還會聽到圍繞 GitOps 的術語，也與 CICD 中一般使用的方法相關。

下一節我們進入的是圍繞可觀察性，另一個概念或領域並不新鮮，但隨著我們以不同方式看待環境，它變得越來越重要。

## 資源

- [Jenkins is the way to build, test, deploy](https://youtu.be/_MXtbjwsz3A)
- [Jenkins.io](https://www.jenkins.io/)
- [ArgoCD](https://argo-cd.readthedocs.io/en/stable/)
- [ArgoCD Tutorial for Beginners](https://www.youtube.com/watch?v=MeU5_k9ssrs)
- [What is Jenkins?](https://www.youtube.com/watch?v=LFDrDnKPOTg)
- [Complete Jenkins Tutorial](https://www.youtube.com/watch?v=nCKxl7Q_20I&t=3s)
- [GitHub Actions](https://www.youtube.com/watch?v=R8_veQiYBjI)
- [GitHub Actions CI/CD](https://www.youtube.com/watch?v=mFFXuXjVgkU)

我們[第 77 天](day77.md)見