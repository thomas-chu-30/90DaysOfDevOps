---
title: '#90DaysOfDevOps - Python for Network Automation - Day 25'
published: false
description: 90DaysOfDevOps - Python for Network Automation
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049038
---

## Python 用於網絡自動化

Python 是用於自動化網絡操作的標準語言。

雖然它不僅僅用於網絡自動化，但當你尋找資源時，它似乎無處不在，正如之前提到的，如果不是 Python，那麼通常是 Ansible，它也是用 Python 編寫的。

我想我已經提到過這一點，但在"學習程式語言"部分，我選擇了 Golang 而不是 Python，原因是我公司正在用 Go 開發，所以這是我學習的一個好理由，但如果情況不是這樣，那麼 Python 會佔用那段時間。

- 可讀性和易用性 - Python 似乎只是有意義。代碼中似乎沒有圍繞 `{}` 的要求來開始和結束塊。再加上像 VS Code 這樣的強大 IDE，當你想運行一些 python 代碼時，你有一個相當容易的開始。

Pycharm 可能是這裡值得提及的另一個 IDE。

- 庫 - Python 的可擴展性是這裡真正的金礦，我之前提到這不僅僅用於網絡自動化，但實際上，有大量庫適用於各種設備和配置。你可以在這裡看到大量 [PyPi](https://pypi.python.org/pypi)

當你想將庫下載到工作站時，你使用名為 `pip` 的工具連接到 PyPI 並在本地下載它。網絡供應商如 Cisco、Juniper 和 Arista 開發了庫以促進對其設備的訪問。

- 強大且高效 - 記住在 Go 的日子裡，我經歷了"Hello World"場景，我們經歷了我認為 6 行代碼？在 Python 中它是

```
print('hello world')
```

將上述所有要點結合起來，應該很容易看出為什麼 Python 通常被稱為自動化工作的事實上的工具。

我認為重要的是要注意，幾年前可能有一些腳本可能與你的網絡設備交互，以自動化配置備份或收集日誌以及對設備的其他見解。我們在這裡談論的自動化有點不同，這是因為整體網絡環境也發生了變化，以更好地適應這種思維方式並啟用更多自動化。

- 軟體定義網絡 - SDN 控制器負責將控制平面配置交付給網絡上的所有設備，這意味著任何網絡更改只有一個聯繫點，不再需要 telnet 或 SSH 到每個設備，並且依賴人類來執行此操作，這具有可重複的失敗或配置錯誤的機會。

- 高級編排 - 從那些 SDN 控制器上升一級，這允許服務級別的編排，然後是此編排層與你選擇的平台（VMware、Kubernetes、公共雲等）的集成。

- 基於策略的管理 - 你想要什麼？期望狀態是什麼？你描述這一點，系統有所有關於如何找出它成為期望狀態的詳細信息。

## 設置實驗環境

不是每個人都有權訪問物理路由器、交換機和其他網絡設備。

我想讓我們能夠查看一些預先提到的工具，但也親自動手學習如何自動化網絡配置。

當談到選項時，我們可以從幾個中選擇。

- [GNS3 VM](https://www.gns3.com/software/download-vm)
- [Eve-ng](https://www.eve-ng.net/)
- [Unimus](https://unimus.net/) 不是實驗環境，但是一個有趣的概念。

我們將使用 [Eve-ng](https://www.eve-ng.net/) 構建我們的實驗，正如之前提到的，你可以使用物理設備，但老實說，虛擬環境意味著我們可以有一個沙箱環境來測試許多不同的場景。此外，能夠使用不同的設備和拓撲可能會很有趣。

我們將在 EVE-NG 上使用社區版完成所有工作。

### 開始

社區版以 ISO 和 OVF 格式提供[下載](https://www.eve-ng.net/index.php/download/)

我們將使用 OVF 下載，但使用 ISO，可以選擇在裸機服務器上構建，而無需虛擬機管理程序。

![](Images/Day25_Networking1.png)

對於我們的演練，我們將使用 VMware Workstation，因為我通過我的 vExpert 擁有許可證，但你同樣可以使用 VMware Player 或[文檔](https://www.eve-ng.net/index.php/documentation/installation/system-requirement/)中提到的任何其他選項。不幸的是，我們不能使用我們之前使用的 Virtual box！

這也是我在 Virtual Box 上使用 GNS3 時遇到的問題，儘管支持。

[Download VMware Workstation Player - FREE](https://www.vmware.com/uk/products/workstation-player.html)

[VMware Workstation PRO](https://www.vmware.com/uk/products/workstation-pro.html) 還注意到有一個免費的評估期！

### 在 VMware Workstation PRO 上安裝

現在我們已經下載並安裝了虛擬機管理程序軟體，並且我們已經下載了 EVE-NG OVF。如果你使用 VMware Player，請告訴我這個過程是否相同。

我們現在準備開始配置。

打開 VMware Workstation，然後選擇 `file` 和 `open`

![](Images/Day25_Networking2.png)

當你下載 EVE-NG OVF 鏡像時，它將在壓縮文件中。將內容提取到其文件夾中，使其看起來像這樣。

![](Images/Day25_Networking3.png)

導航到你下載 EVE-NG OVF 鏡像的位置並開始導入。

給它一個可識別的名稱並將虛擬機存儲在系統上的某個位置。

![](Images/Day25_Networking4.png)

導入完成後，將處理器數量增加到 4，將分配的內存增加到 8 GB。（如果最新版本在導入後不是這種情況，則編輯 VM 設置）

此外，確保啟用了 Virtualise Intel VT-x/EPT 或 AMD-V/RVI 複選框。此選項指示 VMware workstation 將虛擬化標誌傳遞給客戶 OS（嵌套虛擬化）這是我在 Virtual Box 上使用 GNS3 時遇到的問題，儘管我的 CPU 允許這樣做。

![](Images/Day25_Networking5.png)

### 啟動和訪問

旁注和兔子洞：記住我提到這不會與 VirtualBox 一起工作！好吧，是的，在 VMware Workstation 和 EVE-NG 上遇到了同樣的問題，但這不是虛擬化平台的錯！

我在 Windows 機器上運行 WSL2，這似乎消除了能夠在環境中運行任何嵌套內容的能力。我對為什麼 Ubuntu VM 確實運行感到困惑，因為在使用 WSL2 時，它似乎消除了 CPU 的 Intel VT-d 虛擬化方面。

為了解決這個問題，我們可以在 Windows 機器上運行以下命令並重新啟動系統，請注意，雖然這已關閉，但你將無法使用 WSL2。

`bcdedit /set hypervisorlaunchtype off`

當你想返回並使用 WSL2 時，你需要運行此命令並重新啟動。

`bcdedit /set hypervisorlaunchtype auto`

這兩個命令都應該以管理員身份運行！

好的，回到節目，你現在應該在 VMware Workstation 中有一台已啟動的機器，你應該有一個看起來類似於此的提示。

![](Images/Day25_Networking6.png)

在上面的提示中，你可以使用：

username = root
password = eve

然後你將被要求再次提供 root 密碼，這將用於稍後 SSH 到主機。

然後我們可以更改主機名。

![](Images/Day25_Networking7.png)

接下來，我們定義一個 DNS 域名，我使用了下面的一個，但我不確定這是否需要在以後更改。

![](Images/Day25_Networking8.png)

然後我們配置網絡，我選擇靜態，以便在重啟後給定的 IP 地址將是持久的。

![](Images/Day25_Networking9.png)

最後一步，從可從工作站訪問的網絡提供靜態 IP 地址。

![](Images/Day25_Networking10.png)

這裡有一些額外的步驟，你必須為網絡提供子網掩碼、默認網關和 DNS。

完成後它將重新啟動，當它恢復時，你可以獲取靜態 IP 地址並將其放入瀏覽器中。

![](Images/Day25_Networking11.png)

GUI 的默認用戶名是 `admin`，密碼是 `eve`，而 SSH 的默認用戶名是 `root`，密碼是 `eve`，但如果你在設置期間更改了，這將被更改。

![](Images/Day25_Networking12.png)

我選擇 HTML5 作為控制台而不是本機，因為當你導航不同的控制台時，這將在瀏覽器中打開一個新選項卡。

接下來我們將：

- 安裝 EVE-NG 客戶端包
- 將一些網絡鏡像加載到 EVE-NG
- 構建網絡拓撲
- 添加節點
- 連接節點
- 開始構建 Python 腳本
- 查看 telnetlib、Netmiko、Paramiko 和 Pexpect

## 資源

- [Free Course: Introduction to EVE-NG](https://www.youtube.com/watch?v=g6B0f_E0NMg)
- [EVE-NG - Creating your first lab](https://www.youtube.com/watch?v=9dPWARirtK8)
- [3 Necessary Skills for Network Automation](https://www.youtube.com/watch?v=KhiJ7Fu9kKA&list=WL&index=122&t=89s)
- [Computer Networking full course](https://www.youtube.com/watch?v=IPvYjXCsTg8)
- [Practical Networking](http://www.practicalnetworking.net/)
- [Python Network Automation](https://www.youtube.com/watch?v=xKPzLplPECU&list=WL&index=126)

我們[Day 26](day26.md)見
