---
title: '#90DaysOfDevOps - 備份所有平台 - 第 86 天'
published: false
description: 90DaysOfDevOps - 備份所有平台
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049058
---

## 備份所有平台

在整個挑戰中，我們討論了許多不同的平台和環境。所有這些都有一個共同點，那就是它們都需要某種級別的數據保護！

數據保護已經存在很多很多年了，但我們今天擁有的數據財富以及這些數據帶來的價值意味著我們必須確保我們不僅通過擁有多個節點和跨應用程式的高可用性來抵禦基礎設施故障，而且我們還必須考慮如果發生故障情況，我們需要在安全可靠的位置擁有該數據、重要數據的副本。

我們現在似乎聽到了很多關於網絡犯罪和勒索軟體的消息，不要誤會我的意思，這是一個巨大的威脅，我堅持這樣一個事實，即你將受到勒索軟體的攻擊。這不是是否的問題，而是何時的問題。因此，更有理由確保在該時間到來時你的數據是安全的。然而，數據丟失的最常見原因不是勒索軟體或網絡犯罪，而是簡單的意外刪除！

我們都做過，刪除了我們不應該刪除的東西，並立即後悔。

通過我們在挑戰期間討論的所有技術和自動化，保護任何有狀態數據甚至複雜的無狀態配置的要求仍然存在，無論平台如何。

![](Images/Day86_Data1.png)

但我們應該能夠考慮自動化來執行該數據保護，並能夠將其整合到我們的工作流程中。

如果我們看看備份是什麼：

_在資訊技術中，備份或數據備份是計算機數據的副本，在其他地方獲取和存儲，以便在數據丟失事件後可以用於恢復原始數據。動詞形式，指的是這樣做的過程，是「back up」，而名詞和形容詞形式是「backup」。_

如果我們將其分解為最簡單的形式，備份是將數據複製並粘貼到新位置。簡單地說，我可以通過將檔案從 C: 驅動器複製到 D: 驅動器來立即進行備份，然後我將有一個副本，以防 C: 驅動器發生某些事情或檔案中的某些內容被錯誤編輯。我可以恢復到 D: 驅動器上的副本。現在，如果我的計算機死了，C 和 D 驅動器都住在那裡，那麼我沒有受到保護，所以我必須考慮一個解決方案或系統外數據的副本，也許到我房子裡的 NAS 驅動器？但是如果我的房子發生某些事情，也許我需要考慮將它存儲在另一個位置的另一個系統上，也許雲是一個選項。也許我可以將重要檔案的副本存儲在多個位置以降低故障風險？

### 3-2-1 備份方法

現在似乎是談論 3-2-1 規則或備份方法的好時機。我做了一個[閃電演講](https://www.youtube.com/watch?v=5wRt1bJfKBw)涵蓋這個主題。

我們之前已經提到了一些關於為什麼需要保護數據的極端原因，但下面列出了更多：

![](Images/Day86_Data2.png)

這讓我談論 3-2-1 方法。我的數據的第一個副本或備份應該盡可能接近我的生產系統，這樣做的原因是基於恢復速度，再次回到關於意外刪除的原始點，這將是最常見的恢復原因。但我希望將其存儲在原始或生產系統之外的合適的第二媒體上。

然後我們想確保我們還將數據副本發送到外部或異地，這是第二個位置出現的地方，無論是另一個房子、建築物、數據中心還是公共雲。

![](Images/Day86_Data3.png)

### 備份責任

我們很可能都聽說過關於不需要備份的所有神話，比如「一切都是無狀態的」我的意思是如果一切都是無狀態的，那麼業務是什麼？沒有資料庫？Word 文檔？企業中的每個人都有一級責任來確保他們受到保護，但這很可能會歸結為運營團隊為關鍵任務應用程式和數據提供備份過程。

另一個好的是「高可用性是我的備份，我們已經在集群中構建了多個節點，這不可能會宕機！」除了當你對資料庫犯錯誤並且這在集群中的所有節點上複製時，或者有火災、洪水或血液情況，這意味著集群不再可用，隨之而來的是重要數據。這不是關於固執，而是關於了解數據和服務，絕對每個人都應該將高可用性和容錯納入他們的架構，但這不能替代備份的需要！

複製也可能似乎為我們提供了數據的異地副本，也許上面提到的集群確實跨越多個位置，但是，第一個意外錯誤仍然會在那裡複製。但是，備份要求應該與環境內的應用程式複製或系統複製並存。

現在，說了這麼多，你也可以走向另一個極端，將數據副本發送到太多位置，這不僅會花費成本，還會增加被攻擊的風險，因為你的表面積現在大大擴展了。

無論如何，誰負責備份？這在每個企業中都會不同，但應該有人承擔理解備份要求的責任。但也要了解恢復計劃！

### 沒人在乎，直到每個人都在乎

備份是一個典型的例子，沒人關心備份，直到你需要恢復某些東西。除了備份數據的要求外，我們還需要考慮如何恢復！

對於我們的文字文檔範例，我們談論的是非常小的檔案，因此來回複製的能力很容易且快速。但如果我們談論的是 100GB 以上的檔案，那麼這將需要時間。此外，我們必須考慮需要恢復的級別，如果我們以虛擬機器為例。

我們有整個虛擬機器，我們有作業系統、應用程式安裝，然後如果這是資料庫伺服器，我們還將有一些資料庫檔案。如果我們犯了錯誤並在資料庫中插入了錯誤的代碼行，我可能不需要恢復整個虛擬機器，我想對我恢復的內容進行細粒度控制。

### 備份場景

我想現在開始構建一個場景來保護一些數據，具體來說，我想保護本地機器上的一些檔案（在這種情況下是 Windows，但我將使用的工具不僅是免費和開源的，而且是跨平台的）我想確保它們受到保護到我在家中本地擁有的 NAS 設備，但也進入雲中的物件存儲桶。

我想備份這個重要數據，它恰好是 90DaysOfDevOps 的儲存庫，是的，它也被發送到 GitHub，這可能是你現在閱讀的地方，但如果我的機器死了，GitHub 也宕機了怎麼辦？任何人如何能夠閱讀內容，而且我如何能夠將該數據恢復到另一個服務？

![](Images/Day86_Data5.png)

有很多工具可以幫助我們實現這一點，但我將使用一個名為 [Kopia](https://kopia.io/) 的開源備份工具，它將使我們能夠加密、去重和壓縮備份，同時能夠將它們發送到許多位置。

你可以在[這裡](https://github.com/kopia/kopia/releases)找到要下載的發布版本，在撰寫本文時，我將使用 v0.10.6。

### 安裝 Kopia

有一個 Kopia CLI 和 GUI，我們將使用 GUI，但知道你也可以為那些不提供 GUI 的 Linux 伺服器擁有 CLI 版本。

我將使用 `KopiaUI-Setup-0.10.6.exe`

真正快速的下一步安裝，然後當你打開應用程式時，你會看到選擇你希望用作備份儲存庫的存儲類型的選擇。

![](Images/Day86_Data6.png)

### 設置儲存庫

首先，我們想使用本地 NAS 設備設置儲存庫，我們將使用 SMB 來執行此操作，但我相信我們也可以使用 NFS。

![](Images/Day86_Data7.png)

在下一個螢幕上，我們將定義密碼，此密碼用於加密儲存庫內容。

![](Images/Day86_Data8.png)

現在我們已經配置了儲存庫，我們可以觸發臨時快照來開始將數據寫入其中。

![](Images/Day86_Data9.png)

首先，我們需要輸入要快照的路徑，在我們的情況下，我們想複製 `90DaysOfDevOps` 資料夾。我們很快就會回到調度方面。

![](Images/Day86_Data10.png)

我們可以定義快照保留。

![](Images/Day86_Data11.png)

也許有一些檔案或檔案類型我們希望排除。

![](Images/Day86_Data12.png)

如果我們想定義一個計劃，我們可以在下一個螢幕上執行此操作，當你首次創建此快照時，這是定義的打開頁面。

![](Images/Day86_Data13.png)

你將看到可以在這裡處理的其他幾個設置。

![](Images/Day86_Data14.png)

選擇立即快照，數據將寫入你的儲存庫。

![](Images/Day86_Data15.png)

### 異地備份到 S3

使用 Kopia，我們可以通過 UI 似乎一次只能配置一個儲存庫。但通過 UI，我們可以發揮創意，擁有多個儲存庫配置檔案可供選擇，以實現我們在物件存儲中擁有本地和異地副本的目標。

我選擇將數據發送到的物件存儲是 Google Cloud Storage。我首先登入我的 Google Cloud Platform 帳戶並創建了一個存儲桶。我已經在系統上安裝了 Google Cloud SDK，但運行 `gcloud auth application-default login` 對我進行了身份驗證。

![](Images/Day86_Data16.png)

然後我使用 Kopia 的 CLI 來顯示我在上一步中添加 SMB 儲存庫後儲存庫的當前狀態。我使用 `"C:\Program Files\KopiaUI\resources\server\kopia.exe" --config-file=C:\Users\micha\AppData\Roaming\kopia\repository.config repository status` 指令執行此操作。

![](Images/Day86_Data17.png)

我們現在準備為演示替換儲存庫的配置，如果我們想要一個長期解決方案來命中這兩個儲存庫，我們可能會做的是我們將創建一個 `smb.config` 檔案和一個 `object.config` 檔案，並能夠運行這兩個指令來將數據副本發送到每個位置。要添加我們的儲存庫，我們運行了 `"C:\Program Files\KopiaUI\resources\server\kopia.exe" --config-file=C:\Users\micha\AppData\Roaming\kopia\repository.config repository create gcs --bucket 90daysofdevops`

上面的指令考慮到我們創建的 Google Cloud Storage 桶稱為 `90daysofdevops`

![](Images/Day86_Data18.png)

現在我們已經創建了新儲存庫，我們可以運行 `"C:\Program Files\KopiaUI\resources\server\kopia.exe" --config-file=C:\Users\micha\AppData\Roaming\kopia\repository.config repository status` 指令，現在將顯示 GCS 儲存庫配置。

![](Images/Day86_Data19.png)

我們接下來需要做的是創建快照並將其發送到我們新創建的儲存庫。使用 `"C:\Program Files\KopiaUI\resources\server\kopia.exe" --config-file=C:\Users\micha\AppData\Roaming\kopia\repository.config kopia snapshot create "C:\Users\micha\demo\90DaysOfDevOps"` 指令，我們可以啟動此過程。你可以在下面的瀏覽器中看到我們的 Google Cloud Storage 桶現在有基於我們備份的 kopia 檔案。

![](Images/Day86_Data20.png)

通過上述過程，我們可以滿足將重要數據發送到 2 個不同位置的要求，其中 1 個在 Google Cloud Storage 中異地，當然我們仍然在不同的媒體類型上擁有數據的生產副本。

### 恢復

恢復是另一個考慮因素，非常重要，Kopia 為我們提供了不僅恢復到現有位置而且恢復到新位置的能力。

如果我們運行指令 `"C:\Program Files\KopiaUI\resources\server\kopia.exe" --config-file=C:\Users\micha\AppData\Roaming\kopia\repository.config snapshot list`，這將列出我們當前在配置的儲存庫（GCS）中擁有的快照

![](Images/Day86_Data21.png)

然後我們可以使用 `"C:\Program Files\KopiaUI\resources\server\kopia.exe" --config-file=C:\Users\micha\AppData\Roaming\kopia\repository.config mount all Z:` 指令直接從 GCS 掛載這些快照。

![](Images/Day86_Data22.png)

我們還可以使用 `kopia snapshot restore kdbd9dff738996cfe7bcf99b45314e193` 恢復快照內容

上面的指令非常長，這是因為我使用的是 KopiaUI 版本的 kopia.exe，正如在演練頂部解釋的那樣，你可以下載 kopia.exe 並將其放入路徑中，這樣你就可以只使用 `kopia` 指令。

在下一節中，我們將專注於保護 Kubernetes 內的工作負載。

## 資源

- [Kubernetes Backup and Restore made easy!](https://www.youtube.com/watch?v=01qcYSck1c4&t=217s)
- [Kubernetes Backups, Upgrades, Migrations - with Velero](https://www.youtube.com/watch?v=zybLTQER0yY)
- [7 Database Paradigms](https://www.youtube.com/watch?v=W2Z7fbCLSTw&t=520s)
- [Disaster Recovery vs. Backup: What's the difference?](https://www.youtube.com/watch?v=07EHsPuKXc0)
- [Veeam Portability & Cloud Mobility](https://www.youtube.com/watch?v=hDBlTdzE6Us&t=3s)

我們[第 87 天](day87.md)見