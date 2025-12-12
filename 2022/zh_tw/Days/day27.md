---
title: '#90DaysOfDevOps - Getting Hands-On with Python & Network - Day 27'
published: false
description: 90DaysOfDevOps - Getting Hands-On with Python & Network
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048735
---

## 親自動手使用 Python 和網絡

在網絡基礎知識的最後一部分中，我們將使用在 [Day 26](day26.md) 創建的實驗環境涵蓋一些自動化任務和工具

我們將使用 SSH 隧道從客戶端連接到設備，而不是 telnet。在客戶端和設備之間創建的 SSH 隧道是加密的。我們還在 Linux 部分的 [Day 18](day18.md) 中介紹了 SSH。

## 訪問我們的虛擬模擬環境

為了與交換機交互，我們要么需要在 EVE-NG 網絡內有一個工作站，要么你可以在那裡部署一個安裝了 Python 的 Linux 盒子來執行自動化（[在 EVE-NG 內設置 Linux 的資源](https://www.youtube.com/watch?v=3Qstk3zngrY)），或者你可以像我一樣做一些事情並定義一個雲以從工作站訪問。

![](Images/Day27_Networking3.png)

為此，我們右鍵單擊畫布，選擇網絡，然後選擇"Management(Cloud0)"，這將橋接到我們的家庭網絡。

![](Images/Day27_Networking4.png)

但是，我們在這個網絡內沒有任何東西，所以我們需要從新網絡向每個設備添加連接。（我的網絡知識需要更多關注，我覺得你可以只對頂部路由器執行下一步，然後通過這一個電纜連接到網絡的其餘部分？）

然後我已經登錄到每個設備，並且我已經為適用於雲進入位置的接口運行了以下命令。

```
enable
config t
int gi0/0
IP add DHCP
no sh
exit
exit
sh ip int br
```

最後一步為我們提供來自家庭網絡的 DHCP 地址。我的設備網絡列表如下：

| Node    | IP Address   | Home Network IP |
| ------- | ------------ | --------------- |
| Router  | 10.10.88.110 | 192.168.169.115 |
| Switch1 | 10.10.88.111 | 192.168.169.178 |
| Switch2 | 10.10.88.112 | 192.168.169.193 |
| Switch3 | 10.10.88.113 | 192.168.169.125 |
| Switch4 | 10.10.88.114 | 192.168.169.197 |

### SSH 到網絡設備

有了上述內容，我們現在可以使用工作站連接到家庭網絡上的設備。我使用 Putty，但也可以訪問其他終端，如 git bash，它們為我提供了 SSH 到設備的能力。

下面你可以看到我們與路由器設備（R1）的 SSH 連接。

![](Images/Day27_Networking5.png)

### 使用 Python 從設備收集信息

我們如何利用 Python 的第一個例子是從所有設備收集信息，特別是我希望能夠連接到每一個並運行一個簡單的命令來為我提供接口配置和設置。我已經將此腳本存儲在這裡 [netmiko_con_multi.py](Networking/netmiko_con_multi.py)

現在當我運行這個時，我可以看到所有設備上的每個端口配置。

![](Images/Day27_Networking6.png)

如果你有很多不同的設備，這可能很方便，創建這一個腳本，以便你可以集中控制並快速理解所有配置在一個地方。

### 使用 Python 配置設備

上面很有用，但使用 Python 配置設備怎麼樣，在我們的場景中，我們在 `SW1` 和 `SW2` 之間有一個中繼端口，再次想像如果這要在許多相同的交換機上完成，我們想要自動化這一點，而不必手動連接到每個交換機來進行配置更改。

我們可以使用 [netmiko_sendchange.py](Networking/netmiko_sendchange.py) 來實現這一點。這將通過 SSH 連接並在我們的 `SW1` 上執行該更改，這也將更改為 `SW2`。

![](Images/Day27_Networking7.png)

現在對於那些查看代碼的人，你會看到消息出現並告訴我們 `sending configuration to device`，但沒有確認這已經發生，我們可以向腳本添加額外的代碼以在交換機上執行該檢查和驗證，或者我們可以修改之前的腳本以向我們顯示這一點。[netmiko_con_multi_vlan.py](Networking/netmiko_con_multi_vlan.py)

![](Images/Day27_Networking8.png)

### 備份設備配置

另一個用例是捕獲我們的網絡配置並確保我們有這些備份，但同樣我們不想連接到網絡上的每個設備，所以我們也可以使用 [backup.py](Networking/backup.py) 自動化這一點。你還需要填充 [backup.txt](Networking/backup.txt) 與你想要備份的 IP 地址。

運行腳本，你應該看到類似下面的內容。

![](Images/Day27_Networking9.png)

那可能只是我在 python 中編寫一個簡單的打印腳本，所以我應該也向你展示備份文件。

![](Images/Day27_Networking10.png)

### Paramiko

廣泛使用的 Python SSH 模塊。你可以在官方 GitHub 鏈接[這裡](https://github.com/paramiko/paramiko)找到更多信息

我們可以使用 `pip install paramiko` 命令安裝此模塊。

![](Images/Day27_Networking1.png)

我們可以通過輸入 Python shell 並導入 paramiko 模塊來驗證安裝。

![](Images/Day27_Networking2.png)

### Netmiko

netmiko 模塊專門針對網絡設備，而 paramiko 是處理 SSH 連接的更廣泛工具。

Netmiko，我們在上面與 paramiko 一起使用，可以使用 `pip install netmiko` 安裝

Netmiko 支持許多網絡供應商和設備，你可以在 [GitHub Page](https://github.com/ktbyers/netmiko#supports) 上找到支持的設備列表

### 其他模塊

還值得提及一些我們沒有機會查看的其他模塊，但它們在網絡自動化方面提供了更多功能。

`netaddr` 用於處理和操作 IP 地址，同樣安裝很簡單，使用 `pip install netaddr`

你可能會發現自己想在 excel 電子表格中存儲大量交換機配置，`xlrd` 將允許腳本讀取 excel 工作簿並將行和列轉換為矩陣。`pip install xlrd` 來安裝模塊。

一些我沒有機會查看的網絡自動化用例可以在[這裡](https://github.com/ktbyers/pynet/tree/master/presentations/dfwcug/examples)找到

我認為這結束了 #90DaysOfDevOps 的網絡部分，網絡是我一段時間沒有觸及的一個領域，還有很多要涵蓋的內容，但我希望在我的筆記和整個過程中共享的資源之間對某些人有所幫助。

## 資源

- [Free Course: Introduction to EVE-NG](https://www.youtube.com/watch?v=g6B0f_E0NMg)
- [EVE-NG - Creating your first lab](https://www.youtube.com/watch?v=9dPWARirtK8)
- [3 Necessary Skills for Network Automation](https://www.youtube.com/watch?v=KhiJ7Fu9kKA&list=WL&index=122&t=89s)
- [Computer Networking full course](https://www.youtube.com/watch?v=IPvYjXCsTg8)
- [Practical Networking](http://www.practicalnetworking.net/)
- [Python Network Automation](https://www.youtube.com/watch?v=xKPzLplPECU&list=WL&index=126)

我在這裡使用的大多數示例，因為我不是網絡工程師，來自這本廣泛的書，它不是免費的，但我使用一些場景來幫助理解網絡自動化。

- [Hands-On Enterprise Automation with Python (Book)](https://www.packtpub.com/product/hands-on-enterprise-automation-with-python/9781788998512)

我們[Day 28](day28.md)見，我們將開始研究雲計算並對該主題和可用的內容有很好的掌握和基礎知識。
