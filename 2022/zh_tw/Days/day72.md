---
title: '#90DaysOfDevOps - 實際操作 Jenkins - 第 72 天'
published: false
description: 90DaysOfDevOps - 實際操作 Jenkins
tags: 'DevOps, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048829
---

## 實際操作 Jenkins

今天的計劃是實際操作 Jenkins，並作為 CI pipeline 的一部分讓某些事情發生，查看一些我們可以使用的範例代碼庫。

### 什麼是 pipeline？

在開始之前，我們需要知道當談到 CI 時什麼是 pipeline，我們已經在昨天的課程中使用以下圖像涵蓋了這一點。

![](Images/Day71_CICD4.png)

我們想採用上述過程或步驟，我們想自動化它們以最終獲得結果，這意味著我們有一個可以交付給客戶、最終用戶等的已部署應用程式。

這個自動化過程使我們能夠通過版本控制到達用戶和客戶。每個更改、功能增強、錯誤修復等都通過這個自動化過程，確認一切正常，無需太多手動干預來確保我們的代碼是好的。

這個過程涉及以可靠和可重複的方式構建軟體，以及通過多個測試和部署階段推進構建的軟體（稱為「構建」）。

Jenkins pipeline 被寫入稱為 Jenkinsfile 的文字檔案中。它本身應該提交到源控制儲存庫。這也被稱為 Pipeline as code，我們也可以非常類似地將其與我們幾週前涵蓋的 Infrastructure as code 進行比較。

[Jenkins Pipeline 定義](https://www.jenkins.io/doc/book/pipeline/#ji-toolbar)

### 部署 Jenkins

我在部署 Jenkins 時有一些樂趣，你會注意到從[文件](https://www.jenkins.io/doc/book/installing/)中，你可以在哪裡安裝 Jenkins 有很多選項。

考慮到我手頭有 minikube，我們已經多次使用它，我也想將此用於此任務。（也是免費的！）儘管 [Kubernetes 安裝](https://www.jenkins.io/doc/book/installing/kubernetes/)中給出的步驟讓我碰壁並且沒有讓事情啟動並運行，你可以在我在這裡記錄我的步驟時比較兩者。

第一步是啟動並運行 minikube 集群，我們可以簡單地使用 `minikube start` 指令執行此操作。

![](Images/Day72_CICD1.png)

我添加了一個包含所有 YAML 配置和值的資料夾，可以在[這裡](CICD/Jenkins)找到。現在我們有了集群，我們可以運行以下內容來創建 jenkins 命名空間。`kubectl create -f jenkins-namespace.yml`

![](Images/Day72_CICD2.png)

我們將使用 Helm 將 Jenkins 部署到集群中，我們在 Kubernetes 部分涵蓋了 helm。我們首先需要添加 jenkinsci helm 儲存庫 `helm repo add jenkinsci https://charts.jenkins.io`，然後更新我們的 charts `helm repo update`。

![](Images/Day72_CICD3.png)

Jenkins 背後的想法是它將為其 pipelines 保存狀態，你可以在沒有持久性的情況下運行上述 helm 安裝，但如果這些 Pod 重新啟動、更改或修改，那麼你創建的任何 pipeline 或配置都將丟失。我們將使用 jenkins-volume.yml 檔案創建一個用於持久性的卷，使用 `kubectl apply -f jenkins-volume.yml` 指令。

![](Images/Day72_CICD4.png)

我們還需要一個服務帳戶，我們可以使用此 YAML 檔案和指令創建。`kubectl apply -f jenkins-sa.yml`

![](Images/Day72_CICD5.png)

在這個階段，我們可以使用 helm chart 進行部署，我們首先使用 `chart=jenkinsci/jenkins` 定義我們的 chart，然後我們將使用此指令進行部署，其中 jenkins-values.yml 包含我們之前部署到集群的持久性和服務帳戶。`helm install jenkins -n jenkins -f jenkins-values.yml $chart`

![](Images/Day72_CICD6.png)

在這個階段，我們的 Pod 將拉取圖像，但 Pod 將無法訪問儲存，因此無法開始配置以啟動和運行 Jenkins。

這就是文件沒有幫助我大量理解需要發生什麼的地方。但我們可以看到我們沒有權限啟動 Jenkins 安裝。

![](Images/Day72_CICD7.png)

要修復上述問題或解決它，我們需要確保我們提供訪問權限或正確的權限，以便我們的 Jenkins Pod 能夠寫入我們建議的此位置。我們可以通過使用 `minikube ssh` 來執行此操作，這將使我們進入我們正在運行的 minikube docker 容器，然後使用 `sudo chown -R 1000:1000 /data/jenkins-volume`，我們可以確保在數據卷上設置了權限。

![](Images/Day72_CICD8.png)

上述過程應該修復 Pod，但是，如果沒有，你可以使用 `kubectl delete pod jenkins-0 -n jenkins` 指令強制刷新 Pod。此時，你應該有 2/2 運行的 Pod，稱為 jenkins-0。

![](Images/Day72_CICD9.png)

我們現在需要管理員密碼，我們可以使用以下指令獲取此密碼。`kubectl exec --namespace jenkins -it svc/jenkins -c jenkins -- /bin/cat /run/secrets/chart-admin-password && echo`

![](Images/Day72_CICD10.png)

現在打開一個新終端機，因為我們將使用 `port-forward` 指令允許我們從工作站獲得訪問權限。`kubectl --namespace jenkins port-forward svc/jenkins 8080:8080`

![](Images/Day72_CICD11.png)

我們現在應該能夠打開瀏覽器並登入 `http://localhost:8080`，並使用用戶名：admin 和我們在上一步中收集的密碼進行身份驗證。

![](Images/Day72_CICD12.png)

當我們進行身份驗證後，我們的 Jenkins 歡迎頁面應該看起來像這樣：

![](Images/Day72_CICD13.png)

從這裡，我建議前往「Manage Jenkins」，你會看到「Manage Plugins」，這將有一些可用的更新。選擇所有這些插件並選擇「Download now and install after restart」

![](Images/Day72_CICD14.png)

如果你想更進一步並使用 shell 腳本自動化 Jenkins 的部署，這個很棒的儲存庫在 Twitter 上與我分享 [mehyedes/nodejs-k8s](https://github.com/mehyedes/nodejs-k8s/blob/main/docs/automated-setup.md)

### Jenkinsfile

現在我們已經在 Kubernetes 集群中部署了 Jenkins，我們現在可以回去思考這個 Jenkinsfile。

每個 Jenkinsfile 可能都會這樣開始，這首先是你定義 pipeline 步驟的地方，在這種情況下，你有 Build > Test > Deploy。但除了使用 `echo` 指令調用特定階段外，我們沒有做任何其他事情。

```

Jenkinsfile (Declarative Pipeline)

pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building..'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}

```

在我們的 Jenkins 儀表板中，選擇「New Item」給項目一個名稱，我將使用「echo1」，我將建議這是一個 Pipeline。

![](Images/Day72_CICD15.png)

點擊確定，然後你將有標籤（General、Build Triggers、Advanced Project Options 和 Pipeline），對於簡單測試，我們只對 Pipeline 感興趣。在 Pipeline 下，你可以添加腳本，我們可以將上面的腳本複製並粘貼到框中。

正如我們上面所說，這不會做太多，但它會向我們展示 Build > Test > Deploy 的階段

![](Images/Day72_CICD16.png)

點擊保存，我們現在可以使用下面突出顯示的 build now 運行構建。

![](Images/Day72_CICD17.png)

我們還應該打開終端機並運行 `kubectl get pods -n jenkins` 來查看那裡發生了什麼。

![](Images/Day72_CICD18.png)

好的，非常簡單的東西，但我們現在可以看到我們的 Jenkins 部署和安裝正常工作，我們可以開始在這裡看到 CI pipeline 的構建塊。

在下一節中，我們將構建一個 Jenkins Pipeline。

## 資源

- [Jenkins is the way to build, test, deploy](https://youtu.be/_MXtbjwsz3A)
- [Jenkins.io](https://www.jenkins.io/)
- [ArgoCD](https://argo-cd.readthedocs.io/en/stable/)
- [ArgoCD Tutorial for Beginners](https://www.youtube.com/watch?v=MeU5_k9ssrs)
- [What is Jenkins?](https://www.youtube.com/watch?v=LFDrDnKPOTg)
- [Complete Jenkins Tutorial](https://www.youtube.com/watch?v=nCKxl7Q_20I&t=3s)
- [GitHub Actions](https://www.youtube.com/watch?v=R8_veQiYBjI)
- [GitHub Actions CI/CD](https://www.youtube.com/watch?v=mFFXuXjVgkU)

我們[第 73 天](day73.md)見