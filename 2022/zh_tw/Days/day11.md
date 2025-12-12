---
title: '#90DaysOfDevOps - Variables & Constants in Go - Day 11'
published: false
description: 90DaysOfDevOps - Variables & Constants in Go
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048862
---

在我們進入今天的主題之前，我想向 [Techworld with Nana](https://www.youtube.com/watch?v=yyUHQIec83I) 和這個精彩的簡潔 Go 基礎知識之旅表示感謝。

在 [Day8](day08.md) 中，我們設置了環境，在 [Day9](day09.md) 中，我們完成了 Hello #90DaysOfDevOps 代碼，在 [Day10](day10.md) 中，我們查看了 Go 工作區並更深入地了解了編譯和運行代碼。

今天我們將在編寫新程序時查看變量、常量和數據類型。

## Go 中的變量和常量

讓我們從規劃我們的應用程序開始，我認為處理一個告訴我們在 #90DaysOfDevOps 挑戰中還剩多少天的程序是個好主意。

這裡首先要考慮的是，當我們構建應用程序時，我們正在歡迎我們的參與者，並且我們正在向用戶反饋他們已完成的天數，我們可能在整個程序中多次使用術語 #90DaysOfDevOps。這是一個很好的用例，可以將 #90DaysOfDevOps 設為我們程序中的變量。

- 變量用於存儲值。
- 就像一個小盒子，裡面有我們保存的信息或值。
- 然後我們可以在整個程序中使用這個變量，這也有好處，如果這個挑戰或變量發生變化，我們只需要在一個地方更改它。這意味著我們可以通過只更改那個變量值將其轉換為社群中的其他挑戰。

要在我們的 Go 程序中聲明這一點，我們通過使用變量的**關鍵字**來定義值。這將存在於我們稍後將看到的 `func main` 代碼塊中。你可以在這裡找到更多關於[關鍵字](https://go.dev/ref/spec#Keywords)的信息。

記住確保你的變量名稱具有描述性。如果你聲明一個變量，你必須使用它，否則你會得到一個錯誤，這是為了避免可能的死代碼，從未使用的代碼。對於未使用的包也是如此。

```
var challenge = "#90DaysOfDevOps"
```

通過上述設置並使用，正如我們將在下一個代碼片段中看到的那樣，你可以從下面的輸出中看到我們使用了一個變量。

```
package main

import "fmt"

func main() {
    var challenge = "#90DaysOfDevOps"
    fmt.Println("Welcome to", challenge, "")
}
```

你可以在 [day11_example1.go](Go/day11_example1.go) 中找到上面的代碼片段

然後你會從下面看到我們用上面的例子構建了我們的代碼，我們得到了下面顯示的輸出。

![](Images/Day11_Go1.png)

我們也知道我們的挑戰是 90 天，至少對於這個挑戰來說是這樣，但接下來，也許是 100，所以我們也想定義一個變量來幫助我們。但是，對於我們的程序，我們想將其定義為常量。常量就像變量一樣，除了它們的值不能在代碼中更改（我們仍然可以在以後用這段代碼創建一個新應用程序並更改這個常量，但這個 90 在我們運行應用程序時不會改變）

將 `const` 添加到我們的代碼中，並添加另一行代碼來打印這個。

```
package main

import "fmt"

func main() {
    var challenge = "#90DaysOfDevOps"
    const daystotal = 90

    fmt.Println("Welcome to", challenge, "")
    fmt.Println("This is a", daystotal, "challenge")
}
```

你可以在 [day11_example2.go](Go/day11_example2.go) 中找到上面的代碼片段

如果我們再次通過 `go build` 過程並運行，你會看到下面的結果。

![](Images/Day11_Go2.png)

最後，這不會是我們程序的結束，我們將在 [Day12](day12.md) 中回到這裡添加更多功能。我們現在想為我們完成挑戰的天數添加另一個變量。

下面我添加了 `dayscomplete` 變量，其中包含已完成的天數。

```
package main

import "fmt"

func main() {
    var challenge = "#90DaysOfDevOps"
    const daystotal = 90
    var dayscomplete = 11

    fmt.Println("Welcome to", challenge, "")
    fmt.Println("This is a", daystotal, "challenge and you have completed", dayscomplete, "days")
    fmt.Println("Great work")
}
```

你可以在 [day11_example3.go](Go/day11_example3.go) 中找到上面的代碼片段

讓我們再次運行 `go build` 過程，或者你可以只使用 `go run`

![](Images/Day11_Go3.png)

以下是我用來使代碼更易於閱讀和編輯的其他一些示例。到目前為止，我們一直在使用 `Println`，但我們可以通過使用 `Printf` 並使用 `%v` 來簡化這一點，這意味著我們在代碼行的末尾按順序定義變量。我們還使用 `\n` 進行換行。

我使用 `%v` 因為這使用默認值，但還有其他選項可以在 [fmt 包文檔](https://pkg.go.dev/fmt) 中找到，你可以找到代碼示例 [day11_example4.go](Go/day11_example4.go)

變量也可以在你的代碼中以更簡單的格式定義。與定義它是 `var` 和 `type` 不同，你可以按如下方式編寫代碼以獲得相同的功能，但代碼看起來更清晰、更簡單。這僅適用於變量，不適用於常量。

```
func main() {
    challenge := "#90DaysOfDevOps"
    const daystotal = 90
```

## 數據類型

在上面的示例中，我們沒有定義變量的類型，這是因為我們可以在這裡給它一個值，Go 足夠聰明，知道該類型是什麼，或者至少可以根據你存儲的值推斷它是什麼。但是，如果我們希望用戶輸入，這將需要特定類型。

到目前為止，我們在代碼中使用了字符串和整數。整數用於天數，字符串用於挑戰的名稱。

同樣重要的是要注意每種數據類型可以做不同的事情並且行為不同。例如，整數可以相乘，而字符串不能。

有四類

- **基本類型**：數字、字符串和布爾值屬於此類別。
- **聚合類型**：數組和結構屬於此類別。
- **引用類型**：指針、切片、映射、函數和通道屬於此類別。
- **接口類型**

數據類型是編程中的一個重要概念。數據類型指定變量值的大小和類型。

Go 是靜態類型的，這意味著一旦定義了變量類型，它只能存儲該類型的數據。

Go 有三種基本數據類型：

- **bool**：表示布爾值，為 true 或 false
- **Numeric**：表示整數類型、浮點值和復數類型
- **string**：表示字符串值

我發現這個資源在數據類型上非常詳細 [Golang by example](https://golangbyexample.com/all-data-types-in-golang-with-examples/)

我還建議 [Techworld with Nana](https://www.youtube.com/watch?v=yyUHQIec83I&t=2023s) 在這一點上詳細介紹了 Go 中的許多數據類型。

如果我們需要在變量中定義類型，我們可以這樣做：

```
var TwitterHandle string
var DaysCompleted uint
```

因為 Go 在給定值的地方暗示變量，我們可以用以下方式打印這些值：

```
fmt.Printf("challenge is %T, daystotal is %T, dayscomplete is %T\n", conference, daystotal, dayscomplete)
```

有許多不同類型的整數和浮點類型，上面的鏈接將詳細介紹這些。

- **int** = 整數
- **unint** = 正整數
- **floating point types** = 包含小數部分的數字

## 資源

- [StackOverflow 2021 Developer Survey](https://insights.stackoverflow.com/survey/2021)
- [Why we are choosing Golang to learn](https://www.youtube.com/watch?v=7pLqIIAqZD4&t=9s)
- [Jake Wright - Learn Go in 12 minutes](https://www.youtube.com/watch?v=C8LgvuEBraI&t=312s)
- [Techworld with Nana - Golang full course - 3 hours 24 mins](https://www.youtube.com/watch?v=yyUHQIec83I)
- [**NOT FREE** Nigel Poulton Pluralsight - Go Fundamentals - 3 hours 26 mins](https://www.pluralsight.com/courses/go-fundamentals)
- [FreeCodeCamp - Learn Go Programming - Golang Tutorial for Beginners](https://www.youtube.com/watch?v=YS4e4q9oBaU&t=1025s)
- [Hitesh Choudhary - Complete playlist](https://www.youtube.com/playlist?list=PLRAV69dS1uWSR89FRQGZ6q9BR2b44Tr9N)

接下來，我們將開始向我們的程序添加一些用戶輸入功能，以便詢問我們完成了多少天。

我們[Day 12](day12.md)見.
