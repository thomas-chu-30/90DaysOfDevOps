---
title: '#90DaysOfDevOps - 數據和應用程式移動性 - 第 90 天'
published: false
description: 90DaysOfDevOps - 數據和應用程式移動性
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048748
---

## 數據和應用程式移動性

#90DaysOfDevOps 挑戰的第 90 天！在這最後一節中，我將涵蓋數據和應用程式的移動性。我將特別專注於 Kubernetes，但跨平台和平台之間的移動性要求是一個不斷增長的要求，並且在現場看到。

用例是「我想將工作負載、應用程式和數據從一個位置移動到另一個位置」，原因很多，可能是成本、風險或為業務提供更好的服務。

在本節中，我們將採用工作負載，我們將查看將 Kubernetes 工作負載從一個集群移動到另一個集群，但在這樣做時，我們將更改應用程式在目標位置上的方式。

它使用了我們在[災難恢復](day89.md)中經歷的許多特徵

### **要求**

我們當前的 Kubernetes 集群無法處理需求，我們的成本正在飆升，這是一個業務決定，我們希望將生產 Kubernetes 集群移動到災難恢復位置，位於不同的公共雲上，這將提供擴展能力，但也以更便宜的價格。我們還可以利用目標雲中可用的一些原生雲服務。

我們當前的關鍵任務應用程式（Pac-Man）有一個資料庫（MongoDB），並且在慢速存儲上運行，我們希望移動到更新的更快存儲層。

當前的 Pac-Man（NodeJS）前端擴展不是很好，我們希望在新位置增加可用 Pod 的數量。

### 開始

我們有我們的簡報，事實上，我們的導入已經到達災難恢復 Kubernetes 集群。

我們需要做的第一項工作是刪除我們在第 89 天為災難恢復測試執行的恢復操作。

我們可以使用 `kubectl delete ns pacman` 在「standby」minikube 集群上執行此操作。

![](Images/Day90_Data1.png)

要開始，請進入 Kasten K10 儀表板，並選擇 Applications 卡片。從下拉列表中選擇「Removed」

![](Images/Day90_Data2.png)

然後我們獲得可用恢復點的列表。我們將選擇可用的那個，因為它包含我們的關鍵任務數據。（在此範例中，我們只有一個恢復點。）

![](Images/Day90_Data3.png)

當我們處理災難恢復過程時，我們將所有內容保留為預設值。但是，如果你有一個需要轉換應用程式的災難恢復過程，這些額外的恢復選項就在那裡。在這種情況下，我們有要求更改存儲和副本數量。

![](Images/Day90_Data4.png)

選擇「Apply transforms to restored resources」選項。

![](Images/Day90_Data5.png)

碰巧我們想要執行的轉換的兩個內建範例是我們要求所需的。

![](Images/Day90_Data6.png)

第一個要求是在主集群上，我們使用稱為 `csi-hostpath-sc` 的 Storage Class，在新集群中，我們希望使用 `standard`，所以我們可以在這裡進行更改。

![](Images/Day90_Data7.png)

看起來不錯，點擊底部的創建轉換按鈕。

![](Images/Day90_Data8.png)

下一個要求是我們希望將 Pac-Man 前端部署擴展到「5」

![](Images/Day90_Data9.png)

如果你正在跟隨，你應該看到我們兩個轉換，如下所示。

![](Images/Day90_Data10.png)

你現在可以從下面的圖像中看到，我們將恢復下面列出的所有工件，如果我們想要，我們也可以對我們想要恢復的內容進行細粒度控制。點擊「Restore」按鈕

![](Images/Day90_Data11.png)

同樣，我們將被要求確認操作。

![](Images/Day90_Data12.png)

最後要顯示的是，如果我們現在返回終端機並查看集群，你可以看到我們現在有 5 個 Pacman Pod，我們的 storageclass 現在設置為 standard vs csi-hostpath-sc

![](Images/Day90_Data13.png)

可以通過轉換實現許多不同的選項。這不僅可以跨越遷移，還可以跨越災難恢復、測試和開發類型的場景等等。

### API 和自動化

我沒有談論利用 API 和自動化其中一些任務的能力，但這些選項存在，並且在整個 UI 中，一些麵包屑提供了利用 API 進行自動化任務的指令集。

關於 Kasten K10 需要注意的重要一點是，在部署時，它部署在 Kubernetes 集群內，然後可以通過 Kubernetes API 調用。

這使我們結束了關於存儲和保護數據的部分。

## 資源

- [Kubernetes Backup and Restore made easy!](https://www.youtube.com/watch?v=01qcYSck1c4&t=217s)
- [Kubernetes Backups, Upgrades, Migrations - with Velero](https://www.youtube.com/watch?v=zybLTQER0yY)
- [7 Database Paradigms](https://www.youtube.com/watch?v=W2Z7fbCLSTw&t=520s)
- [Disaster Recovery vs. Backup: What's the difference?](https://www.youtube.com/watch?v=07EHsPuKXc0)
- [Veeam Portability & Cloud Mobility](https://www.youtube.com/watch?v=hDBlTdzE6Us&t=3s)

### **結束語**

當我結束這個挑戰時，我想繼續徵求反饋，以確保資訊始終相關。

我也感謝有很多主題我無法涵蓋或無法深入探討圍繞 DevOps 主題的內容。

這意味著我們總是可以明年再次嘗試這個挑戰，並找到另一個 90 天的內容和演練來完成。

### 下一步是什麼？

首先，休息一段時間不寫作，我在 2022 年 1 月 1 日開始這個挑戰，並在 2022 年 3 月 31 日 19:50 BST 完成！這是一個艱苦的工作。但正如我所說並長期以來所說，如果這些內容幫助一個人，那麼公開學習總是值得的！

我對下一步該做什麼有一些想法，希望它在 GitHub 儲存庫之外有生命，我們可以考慮創建電子書，甚至可能是實體書。

我也知道我們需要重新審視每個帖子，並在實現任何類似的事情之前確保所有內容在語法上都是正確的。如果有人確實知道如何將 markdown 轉換為打印或電子書，將非常感謝反饋。

一如既往，保持問題和 PR 的到來。

謝謝！
@MichaelCade1

- [GitHub](https://github.com/MichaelCade)
- [Twitter](https://twitter.com/MichaelCade1)