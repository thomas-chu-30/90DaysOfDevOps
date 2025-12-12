---
title: '#90DaysOfDevOps - The Go Workspace - Day 10'
published: false
description: 90DaysOfDevOps - The Go Workspace
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048701
---

### Go 工作區

在 [Day 8](day08.md) 中，我們簡要介紹了 Go 工作區，以啟動並運行 Go 以演示 `Hello #90DaysOfDevOps`，但我們應該更多地解釋 Go 工作區。

記住我們選擇了默認設置，然後我們創建了我們的 Go 文件夾，在已經定義的 GOPATH 中，但在現實中，這個 GOPATH 可以更改為你想要的任何位置。

如果你運行

```
echo $GOPATH
```

輸出應該類似於我的（可能用戶名不同），即：

```
/home/michael/projects/go
```

然後在這裡，我們創建了 3 個目錄。**src**、**pkg** 和 **bin**

![](Images/Day10_Go1.png)

**src** 是存儲所有 Go 程序和項目的地方。這處理所有 Go 存儲庫的命名空間包管理。這是你在我們的工作站上看到我們有 Hello 文件夾用於 Hello #90DaysOfDevOps 項目的地方。

![](Images/Day10_Go2.png)

**pkg** 是已安裝或曾經安裝在程序中的包的歸檔文件所在的位置。這有助於根據使用的包是否已修改來加快編譯過程。

![](Images/Day10_Go3.png)

**bin** 是存儲所有編譯的二進制文件的位置。

![](Images/Day10_Go4.png)

我們的 Hello #90DaysOfDevOps 不是一個複雜的程序，所以這裡是一個更複雜的 Go 程序的示例，取自另一個值得一看的優秀資源 [GoChronicles](https://gochronicles.com/)

![](Images/Day10_Go5.png)

此頁面還詳細介紹了為什麼以及如何佈局是這樣的，它還更深入地介紹了我們未提及的其他文件夾 [GoChronicles](https://gochronicles.com/project-structure/)

### 編譯和運行代碼

在 [Day 9](day09.md) 中，我們還簡要介紹了編譯代碼，但我們可以在這裡更深入地了解。

要運行我們的代碼，我們首先必須**編譯**它。在 Go 中有三種方法可以做到這一點。

- go build
- go install
- go run

在我們進入上述編譯階段之前，我們需要查看 Go 安裝為我們提供了什麼。

當我們在 Day 8 上安裝 Go 時，我們安裝了稱為 Go 工具的東西，它由幾個程序組成，讓我們構建和處理我們的 Go 源文件。其中一個工具是 `Go`

值得注意的是，你可以安裝不在標準 Go 安裝中的其他工具。

如果你打開命令提示符並鍵入 `go`，你應該看到類似下面的圖像，然後你會在下面看到"其他幫助主題"，現在我們不需要擔心這些。

![](Images/Day10_Go6.png)

你可能還記得我們在 Day 8 上已經使用了至少兩個這些工具。

![](Images/Day10_Go7.png)

我們想了解更多的是 build、install 和 run。

![](Images/Day10_Go8.png)

- `go run` - 此命令編譯並運行由命令行上指定的 .go 文件組成的主包。命令被編譯到臨時文件夾。
- `go build` - 編譯包和依賴項，編譯當前目錄中的包。如果 Go 項目包含 `main` 包，它將創建可執行文件並將其放在當前目錄中，如果不是，它會將可執行文件放在 `pkg` 文件夾中，並且可以被其他 Go 程序導入和使用。`go build` 還使你能夠為任何 Go 支持的操作系統平台構建可執行文件。
- `go install` - 與 go build 相同，但會將可執行文件放在 `bin` 文件夾中。

我們已經運行了 go build 和 go run，但如果你願意，請隨時在這裡再次運行它們，如上所述，`go install` 將可執行文件放在我們的 bin 文件夾中。

![](Images/Day10_Go9.png)

希望如果你正在跟隨，你正在觀看下面的一個播放列表或視頻。我正在從所有這些中提取一些內容，並將它們轉換為我的筆記，以便我可以理解 Golang 語言的基礎知識。下面的資源可能會讓你更好地理解你總體需要的許多領域，但我正在嘗試記錄 7 天或 7 小時的旅程，其中包含我發現的有趣內容。

## 資源

- [StackOverflow 2021 Developer Survey](https://insights.stackoverflow.com/survey/2021)
- [Why we are choosing Golang to learn](https://www.youtube.com/watch?v=7pLqIIAqZD4&t=9s)
- [Jake Wright - Learn Go in 12 minutes](https://www.youtube.com/watch?v=C8LgvuEBraI&t=312s)
- [Techworld with Nana - Golang full course - 3 hours 24 mins](https://www.youtube.com/watch?v=yyUHQIec83I)
- [**NOT FREE** Nigel Poulton Pluralsight - Go Fundamentals - 3 hours 26 mins](https://www.pluralsight.com/courses/go-fundamentals)
- [FreeCodeCamp - Learn Go Programming - Golang Tutorial for Beginners](https://www.youtube.com/watch?v=YS4e4q9oBaU&t=1025s)
- [Hitesh Choudhary - Complete playlist](https://www.youtube.com/playlist?list=PLRAV69dS1uWSR89FRQGZ6q9BR2b44Tr9N)

我們[Day 11](day11.md)見.
