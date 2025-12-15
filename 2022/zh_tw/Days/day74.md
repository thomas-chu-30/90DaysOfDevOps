---
title: '#90DaysOfDevOps - Hello World - Jenkinsfile 應用程式 Pipeline - 第 74 天'
published: false
description: 90DaysOfDevOps - Hello World - Jenkinsfile 應用程式 Pipeline
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048744
---

## Hello World - Jenkinsfile 應用程式 Pipeline

在上一節中，我們在 Jenkins 中構建了一個簡單的 Pipeline，它會將我們的 docker 圖像從公共 GitHub 儲存庫中的 dockerfile 推送到我們的私有 Dockerhub 儲存庫。

在本節中，我們想更進一步，我們想使用簡單的應用程式實現以下目標。

### 目標

- Dockerfile (Hello World)
- Jenkinsfile
- 當 GitHub 儲存庫更新時觸發的 Jenkins Pipeline
- 使用 GitHub 儲存庫作為源。
- 運行 - 克隆/獲取儲存庫、構建、測試、部署階段
- 使用增量版本號部署到 DockerHub
- 延伸目標是部署到我們的 Kubernetes 集群（這將涉及另一個作業和使用 GitHub 憑證的 manifest 儲存庫）

### 第一步

我們有 [GitHub 儲存庫](https://github.com/MichaelCade/Jenkins-HelloWorld) 這目前包含我們的 Dockerfile 和我們的 index.html

![](Images/Day74_CICD1.png)

使用上面的內容，這是我們在 Pipeline 中使用的源，現在我們也想將 Jenkins Pipeline 腳本添加到我們的 GitHub 儲存庫中。

![](Images/Day74_CICD2.png)

現在回到我們的 Jenkins 儀表板，我們將創建一個新的 pipeline，但現在不是粘貼我們的腳本，我們將使用「Pipeline script from SCM」然後我們將使用下面的配置選項。

作為參考，我們將使用 `https://github.com/MichaelCade/Jenkins-HelloWorld.git` 作為儲存庫 URL。

![](Images/Day74_CICD3.png)

此時我們可以點擊保存並應用，然後我們將能夠手動運行我們的 Pipeline，構建上傳到 DockerHub 儲存庫的新 Docker 圖像。

但是，我還想確保我們設置了一個計劃，每當我們的儲存庫或源代碼發生變化時，我想觸發構建。我們可以使用 webhooks 或我們可以使用計劃的拉取。

這是一個重要的考慮因素，因為如果你使用昂貴的雲資源來保存 pipeline，並且你對代碼儲存庫進行大量更改，那麼你將產生大量成本。我們知道這是一個演示環境，這就是為什麼我使用「poll scm」選項。（另外，我相信使用 minikube 我缺乏使用 webhooks 的能力）

![](Images/Day74_CICD4.png)

自昨天課程以來我改變的一件事是，我想現在將圖像上傳到公共儲存庫，在這種情況下將是 michaelcade1\90DaysOfDevOps，我的 Jenkinsfile 已經有了這個更改。從前面的部分，我已經移除了任何現有的演示容器圖像。

![](Images/Day74_CICD5.png)

回到這裡，我們創建了 Pipeline，然後如先前所示，我們添加了配置。

![](Images/Day74_CICD6.png)

在這個階段，我們的 Pipeline 從未運行，你的階段視圖將看起來像這樣。

![](Images/Day74_CICD7.png)

現在讓我們觸發「Build Now」按鈕。我們的階段視圖將顯示我們的階段。

![](Images/Day74_CICD8.png)

如果我們然後前往 DockerHub 儲存庫，我們應該有 2 個新的 Docker 圖像。我們應該有 Build ID 1 和 latest，因為對於我們基於「Upload to DockerHub」創建的每個構建，我們使用 Jenkins Build_ID 環境變數發送版本，我們也發出 latest。

![](Images/Day74_CICD9.png)

讓我們去在 GitHub 儲存庫中創建對 index.html 檔案的更新，如下所示，我將讓你去找出 index.html 的版本 1 在說什麼。

![](Images/Day74_CICD10.png)

如果我們回到 Jenkins 並再次選擇「Build Now」。我們將看到我們的 #2 構建是否成功。

![](Images/Day74_CICD11.png)

然後快速查看 DockerHub，我們可以看到我們有標記的版本 2 和我們的 latest 標記。

![](Images/Day74_CICD12.png)

值得注意的是，我已經在 Kubernetes 集群中添加了一個密鑰，使我能夠訪問和身份驗證以將 docker 構建推送到 DockerHub。如果你正在跟隨，你應該為你的帳戶重複此過程，並且還對與我的儲存庫和帳戶關聯的 Jenkinsfile 進行更改。

## 資源

- [Jenkins is the way to build, test, deploy](https://youtu.be/_MXtbjwsz3A)
- [Jenkins.io](https://www.jenkins.io/)
- [ArgoCD](https://argo-cd.readthedocs.io/en/stable/)
- [ArgoCD Tutorial for Beginners](https://www.youtube.com/watch?v=MeU5_k9ssrs)
- [What is Jenkins?](https://www.youtube.com/watch?v=LFDrDnKPOTg)
- [Complete Jenkins Tutorial](https://www.youtube.com/watch?v=nCKxl7Q_20I&t=3s)
- [GitHub Actions](https://www.youtube.com/watch?v=R8_veQiYBjI)
- [GitHub Actions CI/CD](https://www.youtube.com/watch?v=mFFXuXjVgkU)

我們[第 75 天](day75.md)見