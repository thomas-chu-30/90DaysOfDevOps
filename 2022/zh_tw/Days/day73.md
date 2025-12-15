---
title: '#90DaysOfDevOps - 構建 Jenkins Pipeline - 第 73 天'
published: false
description: 90DaysOfDevOps - 構建 Jenkins Pipeline
tags: 'DevOps, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048766
---

## 構建 Jenkins Pipeline

在上一節中，我們將 Jenkins 部署到 Minikube 集群，並設置了一個非常基本的 Jenkins Pipeline，除了回顯 Pipeline 的階段外，它沒有做太多事情。

你可能還看到在 Jenkins Pipeline 創建中有一些可用的範例腳本。

![](Images/Day73_CICD1.png)

第一個演示腳本是「Declarative (Kubernetes)」，你可以看到下面的階段。

```Yaml
// Uses Declarative syntax to run commands inside a container.
pipeline {
    agent {
        kubernetes {
            // Rather than inline YAML, in a multibranch Pipeline you could use: yamlFile 'jenkins-pod.yaml'
            // Or, to avoid YAML:
            // containerTemplate {
            //     name 'shell'
            //     image 'ubuntu'
            //     command 'sleep'
            //     args 'infinity'
            // }
            yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: shell
    image: ubuntu
    command:
    - sleep
    args:
    - infinity
'''
            // Can also wrap individual steps:
            // container('shell') {
            //     sh 'hostname'
            // }
            defaultContainer 'shell'
        }
    }
    stages {
        stage('Main') {
            steps {
                sh 'hostname'
            }
        }
    }
}
```

你可以看到下面運行此 Pipeline 時發生的結果。

![](Images/Day73_CICD2.png)

### 作業創建

#### 目標

- 創建一個簡單的應用程式並將其存儲在 GitHub 公共儲存庫 [https://github.com/scriptcamp/kubernetes-kaniko.git](https://github.com/scriptcamp/kubernetes-kaniko.git)

- 使用 Jenkins 構建我們的 docker 容器圖像並將其推送到 docker hub。（為此，我們將使用私有儲存庫）

為了在運行或使用 Minikube 的 Kubernetes 集群中實現這一點，我們需要使用稱為 [Kaniko](https://github.com/GoogleContainerTools/kaniko#running-kaniko-in-a-kubernetes-cluster) 的東西。不過，一般來說，如果你在真實的 Kubernetes 集群中使用 Jenkins 或你在伺服器上運行它，那麼你可以指定一個代理，它將為你提供執行 docker 構建指令並將其上傳到 DockerHub 的能力。

考慮到上述情況，我們還將使用 GitHub 憑證將密鑰部署到 Kubernetes。

```Shell
kubectl create secret docker-registry dockercred \
    --docker-server=https://index.docker.io/v1/ \
    --docker-username=<dockerhub-username> \
    --docker-password=<dockerhub-password>\
    --docker-email=<dockerhub-email>
```

我想分享來自 [DevOpsCube.com](https://devopscube.com/build-docker-image-kubernetes-pod/) 的另一個很好的資源，演練我們將在這裡涵蓋的大部分內容。

### 向 Jenkins 添加憑證

但是，如果你在與我們不同的 Jenkins 系統上，那麼你可能希望在 Jenkins 內定義憑證，然後在 Pipelines 和配置中多次使用它們。我們可以使用我們在創建時確定的 ID 在 Pipelines 中引用這些憑證。我繼續並逐步創建了 DockerHub 和 GitHub 的用戶條目。

首先選擇「Manage Jenkins」，然後選擇「Manage Credentials」

![](Images/Day73_CICD3.png)

你會在頁面中心看到 Stores scoped to Jenkins，在這裡點擊 Jenkins。

![](Images/Day73_CICD4.png)

現在選擇 Global Credentials (Unrestricted)

![](Images/Day73_CICD5.png)

然後在左上角，你有 Add Credentials

![](Images/Day73_CICD6.png)

填寫帳戶的詳細資訊，然後選擇確定，記住 ID 是當你想調用此憑證時將引用的內容。我在這裡的建議也是你使用特定的令牌訪問而不是密碼。

![](Images/Day73_CICD7.png)

對於 GitHub，你應該使用 [Personal Access Token](https://vzilla.co.uk/vzilla-blog/creating-updating-your-github-personal-access-token)

我發現創建這些帳戶的過程不是很直觀，所以即使我們不使用，我也想分享這個過程，因為從 UI 中不清楚。

### 構建 pipeline

我們已將 DockerHub 憑證作為密鑰部署到 Kubernetes 集群中，我們將在 pipeline 的 docker 部署到 DockerHub 階段調用它。

Pipeline 腳本是你在下面可以看到的，這反過來可能成為位於 GitHub 儲存庫中的 Jenkinsfile，你也可以看到它列在 pipeline 的 Get the project 階段中。

```Yaml
podTemplate(yaml: '''
    apiVersion: v1
    kind: Pod
    spec:
      containers:
      - name: maven
        image: maven:3.8.1-jdk-8
        command:
        - sleep
        args:
        - 99d
      - name: kaniko
        image: gcr.io/kaniko-project/executor:debug
        command:
        - sleep
        args:
        - 9999999
        volumeMounts:
        - name: kaniko-secret
          mountPath: /kaniko/.docker
      restartPolicy: Never
      volumes:
      - name: kaniko-secret
        secret:
            secretName: dockercred
            items:
            - key: .dockerconfigjson
              path: config.json
''') {
  node(POD_LABEL) {
    stage('Get the project') {
      git url: 'https://github.com/scriptcamp/kubernetes-kaniko.git', branch: 'main'
      container('maven') {
        stage('Test the project') {
          sh '''
          echo pwd
          '''
        }
      }
    }

    stage('Build & Test the Docker Image') {
      container('kaniko') {
        stage('Deploy to DockerHub') {
          sh '''
            /kaniko/executor --context `pwd` --destination michaelcade1/helloworld:latest
          '''
        }
      }
    }

  }
}
```

要在 Jenkins 儀表板上啟動，我們需要選擇「New Item」

![](Images/Day73_CICD8.png)

然後我們將給項目一個名稱，選擇 Pipeline，然後點擊確定。

![](Images/Day73_CICD9.png)

我們不會選擇任何 general 或 build triggers，但可以嘗試這些，因為有一些有趣的計劃和其他配置可能很有用。

![](Images/Day73_CICD10.png)

我們只對最後的 Pipeline 標籤感興趣。

![](Images/Day73_CICD11.png)

在 Pipeline 定義中，我們將把上面擁有的 pipeline 腳本複製並粘貼到 Script 部分，然後點擊保存。

![](Images/Day73_CICD12.png)

接下來，我們將選擇頁面左側的「Build Now」選項。

![](Images/Day73_CICD13.png)

你現在應該等待一小段時間，不到一分鐘。你應該在狀態下看到我們在上面腳本中定義的階段。

![](Images/Day73_CICD14.png)

更重要的是，如果我們現在前往 DockerHub 並檢查我們是否有新構建。

![](Images/Day73_CICD15.png)

總的來說，確實需要一段時間才能弄清楚，但我想堅持下去以獲得實際操作並演練任何人都可以使用 minikube 和訪問 GitHub 和 dockerhub 運行的場景。

我用於此演示的 DockerHub 儲存庫是私有的。但在下一節中，我想推進其中一些階段，讓它們做一些事情，而不僅僅是打印出 `pwd` 並運行一些測試和構建階段。

## 資源

- [Jenkins is the way to build, test, deploy](https://youtu.be/_MXtbjwsz3A)
- [Jenkins.io](https://www.jenkins.io/)
- [ArgoCD](https://argo-cd.readthedocs.io/en/stable/)
- [ArgoCD Tutorial for Beginners](https://www.youtube.com/watch?v=MeU5_k9ssrs)
- [What is Jenkins?](https://www.youtube.com/watch?v=LFDrDnKPOTg)
- [Complete Jenkins Tutorial](https://www.youtube.com/watch?v=nCKxl7Q_20I&t=3s)
- [GitHub Actions](https://www.youtube.com/watch?v=R8_veQiYBjI)
- [GitHub Actions CI/CD](https://www.youtube.com/watch?v=mFFXuXjVgkU)

我們[第 74 天](day74.md)見