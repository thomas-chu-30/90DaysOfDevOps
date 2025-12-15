---
title: '#90DaysOfDevOps - 應用程式專注備份 - 第 88 天'
published: false
description: 90DaysOfDevOps - 應用程式專注備份
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048749
---

## 應用程式專注備份

我們已經花了一些時間談論數據服務或數據密集型應用程式，例如 [第 85 天](day85.md) 上的資料庫。對於這些數據服務，我們必須考慮如何管理一致性，特別是當涉及到應用程式一致性時。

在這篇文章中，我們將深入探討圍繞一致保護應用程式數據的要求。

為此，我們選擇的工具將是 [Kanister](https://kanister.io/)

![](Images/Day88_Data1.png)

### 介紹 Kanister

Kanister 是 Kasten 的一個開源專案，使我們能夠管理（備份和恢復）Kubernetes 上的應用程式數據。你可以將 Kanister 作為 helm 應用程式部署到 Kubernetes 集群中。

Kanister 使用 Kubernetes 自定義資源，部署 Kanister 時安裝的主要自定義資源是

- `Profile` - 是存儲備份和從中恢復的目標位置。最常見的是物件存儲。
- `Blueprint` - 要採取的備份和恢復資料庫的步驟應在 Blueprint 中維護
- `ActionSet` - 是將目標備份移動到我們的 profile 以及恢復操作的動作。

### 執行演練

在我們實際操作之前，我們應該查看 Kanister 在保護應用程式數據時採用的工作流程。首先，我們的控制器使用 helm 部署到 Kubernetes 集群中，Kanister 存在於其命名空間內。我們採用我們的 Blueprint，其中有很多社群支持的藍圖可用，我們將很快更詳細地涵蓋這一點。然後我們有我們的資料庫工作負載。

![](Images/Day88_Data2.png)

然後我們創建我們的 ActionSet。

![](Images/Day88_Data3.png)

ActionSet 允許我們針對特定數據服務運行在藍圖中定義的操作。

![](Images/Day88_Data4.png)

ActionSet 反過來使用 Kanister 函數（KubeExec、KubeTask、Resource Lifecycle）並將備份推送到目標儲存庫（Profile）。

![](Images/Day88_Data5.png)

如果該操作完成/失敗，相應的狀態會在 Actionset 中更新。

![](Images/Day88_Data6.png)

### 部署 Kanister

我們將再次使用 minikube 集群來實現此應用程式備份。如果你從上一節中仍然運行它，那麼我們可以繼續使用它。

在撰寫本文時，我們已達到圖像版本 `0.75.0`，使用以下 helm 指令，我們將把 kanister 安裝到 Kubernetes 集群中。

`helm install kanister --namespace kanister kanister/kanister-operator --set image.tag=0.75.0 --create-namespace`

![](Images/Day88_Data7.png)

我們可以使用 `kubectl get pods -n kanister` 來確保 Pod 啟動並運行，然後我們還可以檢查自定義資源定義現在可用（如果你只安裝了 Kanister，那麼你會看到突出顯示的 3）

![](Images/Day88_Data8.png)

### 部署資料庫

通過 helm 部署 MySQL：

```Shell
APP_NAME=my-production-app
kubectl create ns ${APP_NAME}
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install mysql-store bitnami/mysql --set primary.persistence.size=1Gi,volumePermissions.enabled=true --namespace=${APP_NAME}
kubectl get pods -n ${APP_NAME} -w
```

![](Images/Day88_Data9.png)

用初始數據填充 MySQL 資料庫，並運行以下內容：

```Shell
MYSQL_ROOT_PASSWORD=$(kubectl get secret --namespace ${APP_NAME} mysql-store -o jsonpath="{.data.mysql-root-password}" | base64 --decode)
MYSQL_HOST=mysql-store.${APP_NAME}.svc.cluster.local
MYSQL_EXEC="mysql -h ${MYSQL_HOST} -u root --password=${MYSQL_ROOT_PASSWORD} -DmyImportantData -t"
echo MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
```

### 創建 MySQL 客戶端

我們將運行另一個容器圖像作為我們的客戶端

```Shell
APP_NAME=my-production-app
kubectl run mysql-client --rm --env APP_NS=${APP_NAME} --env MYSQL_EXEC="${MYSQL_EXEC}" --env MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD} --env MYSQL_HOST=${MYSQL_HOST} --namespace ${APP_NAME} --tty -i --restart='Never' --image  docker.io/bitnami/mysql:latest --command -- bash
```

```Shell
注意：如果你已經有一個現有的 MySQL 客戶端 Pod 正在運行，請使用指令刪除

kubectl delete pod -n ${APP_NAME} mysql-client
```

### 向 MySQL 添加數據

```Shell
echo "create database myImportantData;" | mysql -h ${MYSQL_HOST} -u root --password=${MYSQL_ROOT_PASSWORD}
MYSQL_EXEC="mysql -h ${MYSQL_HOST} -u root --password=${MYSQL_ROOT_PASSWORD} -DmyImportantData -t"
echo "drop table Accounts" | ${MYSQL_EXEC}
echo "create table if not exists Accounts(name text, balance integer); insert into Accounts values('nick', 0);" |  ${MYSQL_EXEC}
echo "insert into Accounts values('albert', 112);" | ${MYSQL_EXEC}
echo "insert into Accounts values('alfred', 358);" | ${MYSQL_EXEC}
echo "insert into Accounts values('beatrice', 1321);" | ${MYSQL_EXEC}
echo "insert into Accounts values('bartholomew', 34);" | ${MYSQL_EXEC}
echo "insert into Accounts values('edward', 5589);" | ${MYSQL_EXEC}
echo "insert into Accounts values('edwin', 144);" | ${MYSQL_EXEC}
echo "insert into Accounts values('edwina', 233);" | ${MYSQL_EXEC}
echo "insert into Accounts values('rastapopoulos', 377);" | ${MYSQL_EXEC}
echo "select * from Accounts;" |  ${MYSQL_EXEC}
exit
```

你應該能夠看到如下所示的一些數據。

![](Images/Day88_Data10.png)

### 創建 Kanister Profile

Kanister 提供 CLI `kanctl` 和另一個實用程式 `kando`，用於從藍圖和這兩個實用程式與物件存儲提供商交互。

[CLI 下載](https://docs.kanister.io/tooling.html#tooling)

我已經創建了一個 AWS S3 桶，我們將用作 profile 目標和恢復位置。我將使用環境變數，這樣我仍然可以向你們展示我使用 `kanctl` 創建 kanister profile 的指令。

`kanctl create profile s3compliant --access-key $ACCESS_KEY --secret-key $SECRET_KEY --bucket $BUCKET --region eu-west-2 --namespace my-production-app`

![](Images/Day88_Data11.png)

### Blueprint 時間

別擔心，你不需要從頭開始創建你的，除非你的數據服務沒有在 [Kanister Examples](https://github.com/kanisterio/kanister/tree/master/examples) 中列出，但無論如何，社群貢獻是這個專案獲得關注的方式。

我們將使用的藍圖將是下面的。

```Shell
apiVersion: cr.kanister.io/v1alpha1
kind: Blueprint
metadata:
  name: mysql-blueprint
actions:
  backup:
    outputArtifacts:
      mysqlCloudDump:
        keyValue:
          s3path: "{{ .Phases.dumpToObjectStore.Output.s3path }}"
    phases:
    - func: KubeTask
      name: dumpToObjectStore
      objects:
        mysqlSecret:
          kind: Secret
          name: '{{ index .Object.metadata.labels "app.kubernetes.io/instance" }}'
          namespace: '{{ .StatefulSet.Namespace }}'
      args:
        image: ghcr.io/kanisterio/mysql-sidecar:0.75.0
        namespace: "{{ .StatefulSet.Namespace }}"
        command:
        - bash
        - -o
        - errexit
        - -o
        - pipefail
        - -c
        - |
          s3_path="/mysql-backups/{{ .StatefulSet.Namespace }}/{{ index .Object.metadata.labels "app.kubernetes.io/instance" }}/{{ toDate "2006-01-02T15:04:05.999999999Z07:00" .Time  | date "2006-01-02T15-04-05" }}/dump.sql.gz"
          root_password="{{ index .Phases.dumpToObjectStore.Secrets.mysqlSecret.Data "mysql-root-password" | toString }}"
          mysqldump --column-statistics=0 -u root --password=${root_password} -h {{ index .Object.metadata.labels "app.kubernetes.io/instance" }} --single-transaction --all-databases | gzip - | kando location push --profile '{{ toJson .Profile }}' --path ${s3_path} -
          kando output s3path ${s3_path}
  restore:
    inputArtifactNames:
    - mysqlCloudDump
    phases:
    - func: KubeTask
      name: restoreFromBlobStore
      objects:
        mysqlSecret:
          kind: Secret
          name: '{{ index .Object.metadata.labels "app.kubernetes.io/instance" }}'
          namespace: '{{ .StatefulSet.Namespace }}'
      args:
        image: ghcr.io/kanisterio/mysql-sidecar:0.75.0
        namespace: "{{ .StatefulSet.Namespace }}"
        command:
        - bash
        - -o
        - errexit
        - -o
        - pipefail
        - -c
        - |
          s3_path="{{ .ArtifactsIn.mysqlCloudDump.KeyValue.s3path }}"
          root_password="{{ index .Phases.restoreFromBlobStore.Secrets.mysqlSecret.Data "mysql-root-password" | toString }}"
          kando location pull --profile '{{ toJson .Profile }}' --path ${s3_path} - | gunzip | mysql -u root --password=${root_password} -h {{ index .Object.metadata.labels "app.kubernetes.io/instance" }}
  delete:
    inputArtifactNames:
    - mysqlCloudDump
    phases:
    - func: KubeTask
      name: deleteFromBlobStore
      args:
        image: ghcr.io/kanisterio/mysql-sidecar:0.75.0
        namespace: "{{ .Namespace.Name }}"
        command:
        - bash
        - -o
        - errexit
        - -o
        - pipefail
        - -c
        - |
          s3_path="{{ .ArtifactsIn.mysqlCloudDump.KeyValue.s3path }}"
          kando location delete --profile '{{ toJson .Profile }}' --path ${s3_path}
```

要添加這個，我們將使用 `kubectl create -f mysql-blueprint.yml -n kanister` 指令

![](Images/Day88_Data12.png)

### 創建我們的 ActionSet 並保護我們的應用程式

我們現在將使用定義此應用程式備份的 ActionSet 對 MySQL 數據進行備份。在與控制器相同的命名空間中創建 ActionSet。

`kubectl get profiles.cr.kanister.io -n my-production-app` 此指令將向我們顯示我們之前創建的 profile，我們可以在這裡配置多個 profiles，所以我們可能想為不同的 ActionSets 使用特定的 profiles

然後我們將使用 `kanctl` 使用以下指令創建我們的 ActionSet

`kanctl create actionset --action backup --namespace kanister --blueprint mysql-blueprint --statefulset my-production-app/mysql-store --profile my-production-app/s3-profile-dc5zm --secrets mysql=my-production-app/mysql-store`

你可以從上面的指令中看到，我們正在定義添加到命名空間的藍圖，`my-production-app` 命名空間中的 statefulset，以及進入 MySQL 應用程式的密鑰。

![](Images/Day88_Data13.png)

通過獲取 ActionSet 名稱並使用此指令檢查 ActionSet 的狀態 `kubectl --namespace kanister describe actionset backup-qpnqv`

最後，我們可以去確認我們現在在 AWS S3 桶中有數據。

![](Images/Day88_Data14.png)

### 恢復

我們需要在恢復任何東西之前造成一些損壞，我們可以通過刪除表來執行此操作，也許是意外，也許不是。

連接到我們的 MySQL Pod。

```Shell
APP_NAME=my-production-app
kubectl run mysql-client --rm --env APP_NS=${APP_NAME} --env MYSQL_EXEC="${MYSQL_EXEC}" --env MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD} --env MYSQL_HOST=${MYSQL_HOST} --namespace ${APP_NAME} --tty -i --restart='Never' --image  docker.io/bitnami/mysql:latest --command -- bash
```

你可以看到我們的 importantdata DB 在那裡，使用 `echo "SHOW DATABASES;" | ${MYSQL_EXEC}`

然後要刪除，我們運行了 `echo "DROP DATABASE myImportantData;" | ${MYSQL_EXEC}`

並通過幾次嘗試顯示我們的資料庫來確認這已經消失。

![](Images/Day88_Data15.png)

我們現在可以使用 Kanister 使用 `kubectl get actionset -n kanister` 來查找我們之前採取的 ActionSet 名稱，讓重要數據重新運行。然後我們將創建一個恢復 ActionSet 來恢復我們的數據，使用 `kanctl create actionset -n kanister --action restore --from "backup-qpnqv"`

![](Images/Day88_Data16.png)

我們可以使用以下指令連接到資料庫來確認我們的數據已恢復。

```Shell
APP_NAME=my-production-app
kubectl run mysql-client --rm --env APP_NS=${APP_NAME} --env MYSQL_EXEC="${MYSQL_EXEC}" --env MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD} --env MYSQL_HOST=${MYSQL_HOST} --namespace ${APP_NAME} --tty -i --restart='Never' --image  docker.io/bitnami/mysql:latest --command -- bash
```

現在我們在 MySQL 客戶端內，我們可以發出 `echo "SHOW DATABASES;" | ${MYSQL_EXEC}`，我們可以看到資料庫已恢復。我們還可以發出 `echo "select * from Accounts;" | ${MYSQL_EXEC}` 來檢查資料庫的內容，我們的重要數據已恢復。

![](Images/Day88_Data17.png)

在下一篇文章中，我們將查看 Kubernetes 內的災難恢復。

## 資源

- [Kanister Overview - An extensible open-source framework for app-lvl data management on Kubernetes](https://www.youtube.com/watch?v=wFD42Zpbfts)
- [Application Level Data Operations on Kubernetes](https://community.cncf.io/events/details/cncf-cncf-online-programs-presents-cncf-live-webinar-kanister-application-level-data-operations-on-kubernetes/)
- [Kubernetes Backup and Restore made easy!](https://www.youtube.com/watch?v=01qcYSck1c4&t=217s)
- [Kubernetes Backups, Upgrades, Migrations - with Velero](https://www.youtube.com/watch?v=zybLTQER0yY)
- [7 Database Paradigms](https://www.youtube.com/watch?v=W2Z7fbCLSTw&t=520s)
- [Disaster Recovery vs. Backup: What's the difference?](https://www.youtube.com/watch?v=07EHsPuKXc0)
- [Veeam Portability & Cloud Mobility](https://www.youtube.com/watch?v=hDBlTdzE6Us&t=3s)

我們[第 89 天](day89.md)見