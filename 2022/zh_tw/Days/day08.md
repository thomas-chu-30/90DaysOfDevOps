---
title: '#90DaysOfDevOps - Setting up your DevOps environment for Go & Hello World - Day 8'
published: false
description: 90DaysOfDevOps - Setting up your DevOps environment for Go & Hello World
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048857
---

## 為 Go 設置你的 DevOps 環境和 Hello World

在我們深入了解 Go 的一些基礎知識之前，我們應該在工作站上安裝 Go，並做每個"學習編程 101"模塊教我們做的事情，即創建 Hello World 應用程序。由於這將是逐步在工作站上安裝 Go 的過程，我們將嘗試用圖片記錄該過程，以便人們可以輕鬆跟隨。

首先，讓我們前往 [go.dev/dl](https://go.dev/dl/)，你將看到一些可用的下載選項。

![](Images/Day8_Go1.png)

如果我們走到這一步，你可能知道你在運行哪個工作站操作系統，所以選擇適當的下載，然後我們可以開始安裝。我在這個演練中使用 Windows，基本上，從下一個屏幕開始，我們現在可以保留所有默認設置。**（我會注意到在撰寫本文時這是最新版本，所以截圖可能已過時）**

![](Images/Day8_Go2.png)

還要注意，如果你確實安裝了舊版本的 Go，你必須在安裝之前刪除它，Windows 在安裝程序中內置了此功能，並將刪除和安裝作為一個步驟。

完成後，你現在應該打開命令提示符/終端，我們想檢查我們是否已安裝 Go。如果你沒有看到我們在下面看到的輸出，則 Go 未安裝，你需要追溯你的步驟。

`go version`

![](Images/Day8_Go3.png)

接下來，我們想檢查 Go 的環境。這總是很好的檢查，以確保你的工作目錄配置正確，如下所示，我們需要確保你的系統上有以下目錄。

![](Images/Day8_Go4.png)

你檢查了嗎？你在跟隨嗎？如果你嘗試導航到那裡，你可能會得到類似下面的內容。

![](Images/Day8_Go5.png)

好的，讓我們創建該目錄，為了方便起見，我將在 PowerShell 終端中使用 mkdir 命令。我們還需要在 Go 文件夾內創建 3 個文件夾，如下所示。

![](Images/Day8_Go6.png)

現在我們已經安裝了 Go，並且我們的 Go 工作目錄已準備好行動。我們現在需要一個集成開發環境（IDE）現在有許多可用的，你可以使用，但最常見的和我使用的是 Visual Studio Code 或 Code。你可以在[這裡](https://www.youtube.com/watch?v=vUn5akOlFXQ)了解更多關於 IDE 的信息。

如果你還沒有在工作站上下載並安裝 VSCode，那麼你可以通過前往[這裡](https://code.visualstudio.com/download)來完成。如下所示，你有不同的操作系統選項。

![](Images/Day8_Go7.png)

與 Go 安裝非常相似，我們將下載並安裝並保留默認設置。完成後，你可以打開 VSCode，你可以選擇打開文件並導航到我們上面創建的 Go 目錄。

![](Images/Day8_Go8.png)

你可能會收到關於信任的彈出窗口，如果你想閱讀它，然後點擊"是，信任作者"。（不過，如果你開始打開你不信任的東西，我不會負責！）

現在你應該看到我們之前創建的三個文件夾，我們現在想做的是右鍵單擊 src 文件夾並創建一個名為 `Hello` 的新文件夾

![](Images/Day8_Go9.png)

到目前為止，我會說這很容易嗎？現在我們將創建我們的第一個 Go 程序，對我們在這個下一階段放入的任何內容都不了解。

接下來，在你的 `Hello` 文件夾中創建一個名為 `main.go` 的文件。一旦你在 main.go 上按 Enter，你將被問是否要安裝 Go 擴展和包，你也可以檢查我們在幾步之前創建的空 pkg 文件，並注意我們現在應該在那裡有一些新包？

![](Images/Day8_Go10.png)

現在讓我們開始這個 Hello World 應用程序，將以下代碼複製到你的新 main.go 文件中並保存。

```
package main

import "fmt"

func main() {
    fmt.Println("Hello #90DaysOfDevOps")
}
```

現在我理解上面的內容可能完全沒有意義，但我們將在以後的日子裡更多地介紹函數、包等。現在，讓我們運行我們的應用程序。回到終端並在我們的 Hello 文件夾中，我們現在可以檢查一切是否正常工作。使用下面的命令，我們可以檢查我們的通用學習程序是否正常工作。

```
go run main.go
```

![](Images/Day8_Go11.png)

不過這還沒有結束，如果我們現在想獲取我們的程序並在其他 Windows 機器上運行它怎麼辦？我們可以通過使用以下命令構建我們的二進制文件來做到這一點

```
go build main.go
```

![](Images/Day8_Go12.png)

如果我們運行它，我們會看到相同的輸出：

```bash
$ ./main.exe
Hello #90DaysOfDevOps
```

## 資源

- [StackOverflow 2021 Developer Survey](https://insights.stackoverflow.com/survey/2021)
- [Why we are choosing Golang to learn](https://www.youtube.com/watch?v=7pLqIIAqZD4&t=9s)
- [Jake Wright - Learn Go in 12 minutes](https://www.youtube.com/watch?v=C8LgvuEBraI&t=312s)
- [Techworld with Nana - Golang full course - 3 hours 24 mins](https://www.youtube.com/watch?v=yyUHQIec83I)
- [**NOT FREE** Nigel Poulton Pluralsight - Go Fundamentals - 3 hours 26 mins](https://www.pluralsight.com/courses/go-fundamentals)
- [FreeCodeCamp - Learn Go Programming - Golang Tutorial for Beginners](https://www.youtube.com/watch?v=YS4e4q9oBaU&t=1025s)
- [Hitesh Choudhary - Complete playlist](https://www.youtube.com/playlist?list=PLRAV69dS1uWSR89FRQGZ6q9BR2b44Tr9N)

我們[Day 9](day09.md)見.

![](Images/Day8_Go13.png)
