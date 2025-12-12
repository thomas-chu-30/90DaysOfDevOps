---
title: '#90DaysOfDevOps - Building our Lab - Day 26'
published: false
description: 90DaysOfDevOps - Building our Lab
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048762
---

## 構建我們的實驗

我們將繼續使用 EVE-NG 設置我們的模擬網絡，然後希望部署一些設備並開始思考如何自動化這些設備的配置。在 [Day 25](day25.md) 中，我們介紹了使用 VMware Workstation 在機器上安裝 EVE-NG。

### 安裝 EVE-NG 客戶端

還有一個客戶端包，允許我們選擇當我們 SSH 到設備時使用哪個應用程序。它還將設置 Wireshark 以在鏈路之間進行數據包捕獲。你可以為你的操作系統（Windows、macOS、Linux）獲取客戶端包。

[EVE-NG Client Download](https://www.eve-ng.net/index.php/download/)

![](Images/Day26_Networking1.png)

快速提示：如果你使用 Linux 作為客戶端，那麼有這個[客戶端包](https://github.com/SmartFinn/eve-ng-integration)。

安裝很簡單，下一步，下一步，我建議保留默認設置。

### 獲取網絡鏡像

這一步一直是一個挑戰，我遵循了一些我將在最後鏈接的視頻，這些視頻鏈接到一些資源和我們的路由器和交換機鏡像的下載，同時告訴我們如何以及在哪裡上傳它們。

重要的是要注意，我使用所有內容用於教育目的。我建議從網絡供應商下載官方鏡像。

[Blog & Links to YouTube videos](https://loopedback.com/2019/11/15/setting-up-eve-ng-for-ccna-ccnp-ccie-level-studies-includes-multiple-vendor-node-support-an-absolutely-amazing-study-tool-to-check-out-asap/)

[How To Add Cisco VIRL vIOS image to Eve-ng](https://networkhunt.com/how-to-add-cisco-virl-vios-image-to-eve-ng/)

總體而言，這裡的步驟有點複雜，可能會更容易，但上面的博客和視頻會引導你完成將鏡像添加到 EVE-NG 盒子的過程。

我使用 FileZilla 通過 SFTP 將 qcow2 傳輸到 VM。

對於我們的實驗，我們需要 Cisco vIOS L2（交換機）和 Cisco vIOS（路由器）

### 創建實驗

在 EVE-NG Web 界面內，我們將創建新的網絡拓撲。我們將有四個交換機和一個路由器，它將充當我們到外部網絡的網關。

| Node    | IP Address   |
| ------- | ------------ |
| Router  | 10.10.88.110 |
| Switch1 | 10.10.88.111 |
| Switch2 | 10.10.88.112 |
| Switch3 | 10.10.88.113 |
| Switch4 | 10.10.88.114 |

#### 將節點添加到 EVE-NG

當你首次登錄 EVE-NG 時，你會看到類似下面的屏幕，我們想通過創建第一個實驗開始。

![](Images/Day26_Networking2.png)

給你的實驗一個名稱，其他字段是可選的。

![](Images/Day26_Networking3.png)

然後你將看到一個空白畫布來開始創建你的網絡。右鍵單擊畫布並選擇添加節點。

從這裡你將有一個長長的節點選項列表，如果你已經遵循了上面的內容，你將有下面藍色顯示的兩個，其他的將是灰色且不可選擇的。

![](Images/Day26_Networking4.png)

我們想向實驗添加以下內容：

- 1 x Cisco vIOS Router
- 4 x Cisco vIOS Switch

運行簡單的嚮導將它們添加到你的實驗中，它應該看起來像這樣。

![](Images/Day26_Networking5.png)

#### 連接我們的節點

我們現在需要在路由器和交換機之間添加連接。我們可以通過將鼠標懸停在設備上並看到如下所示的連接圖標，然後將其連接到我們希望連接的設備來輕鬆完成此操作。

![](Images/Day26_Networking6.png)

完成連接環境後，你可能還想添加一些方法來使用框或圓來定義物理邊界或位置，這些也可以在右鍵菜單中找到。你還可以添加文本，這在我們想在實驗中定義命名或 IP 地址時很有用。

我繼續使我的實驗看起來像下面這樣。

![](Images/Day26_Networking7.png)

你還會注意到上面的實驗都已關閉，我們可以通過選擇所有內容並右鍵單擊並選擇啟動選中來啟動實驗。

![](Images/Day26_Networking8.png)

一旦我們啟動並運行實驗，你將能夠控制台進入每個設備，你會注意到在這個階段它們非常愚蠢，沒有配置。我們可以通過在每個終端中複製或創建自己的配置來向每個節點添加一些配置。

我將把我的配置留在存儲庫的 Networking 文件夾中供參考。

| Node    | Configuration         |
| ------- | --------------------- |
| Router  | [R1](Networking/R1)   |
| Switch1 | [SW1](Networking/SW1) |
| Switch2 | [SW2](Networking/SW2) |
| Switch3 | [SW3](Networking/SW3) |
| Switch4 | [SW4](Networking/SW4) |

## 資源

- [Free Course: Introduction to EVE-NG](https://www.youtube.com/watch?v=g6B0f_E0NMg)
- [EVE-NG - Creating your first lab](https://www.youtube.com/watch?v=9dPWARirtK8)
- [3 Necessary Skills for Network Automation](https://www.youtube.com/watch?v=KhiJ7Fu9kKA&list=WL&index=122&t=89s)
- [Computer Networking full course](https://www.youtube.com/watch?v=IPvYjXCsTg8)
- [Practical Networking](http://www.practicalnetworking.net/)
- [Python Network Automation](https://www.youtube.com/watch?v=xKPzLplPECU&list=WL&index=126)

我在這裡使用的大多數示例，因為我不是網絡工程師，來自這本廣泛的書，它不是免費的，但我使用一些場景來幫助理解網絡自動化。

- [Hands-On Enterprise Automation with Python (Book)](https://www.packtpub.com/product/hands-on-enterprise-automation-with-python/9781788998512)

我們[Day 27](day27.md)見
