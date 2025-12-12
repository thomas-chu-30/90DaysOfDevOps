---
title: '#90DaysOfDevOps - Tweet your progress with our new App - Day 13'
published: false
description: 90DaysOfDevOps - Tweet your progress with our new App
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048865
---

## 使用我們的新應用程序發布你的進度

在查看這種程式語言的最後一天，我們只是觸及了這種語言的表面，但我認為正是在這個開始階段，我們需要感興趣和興奮，並想要更深入地研究它。

在過去的幾天裡，我們為應用程序提出了一個小想法，並為其添加了功能，在本節中，我想利用我們提到的那些包，為我們的應用程序創建功能，不僅在屏幕上為你提供進度更新，還發送一條包含挑戰詳情和你的狀態的推文。

## 添加發布進度的能力

我們需要做的第一件事是設置我們的開發者 API 訪問 Twitter 以使其工作。

前往 [Twitter Developer Platform](https://developer.twitter.com) 並使用你的 Twitter 帳號和詳細信息登錄。進入後，你應該看到類似下面的內容，但沒有我已經創建的應用程序。

![](Images/Day13_Go1.png)

從這裡，你可能還想請求提升訪問權限，這可能需要一些時間，但對我來說非常快。

接下來，我們應該選擇 Projects & Apps 並創建我們的應用程序。限制取決於你擁有的帳戶訪問權限，使用基本訪問權限你只有一個應用程序和一個項目，使用提升訪問權限你可以有 3 個應用程序。

![](Images/Day13_Go2.png)

為你的應用程序命名

![](Images/Day13_Go3.png)

然後你將獲得這些 API 令牌，你必須將它們保存在安全的地方。（我已經刪除了這個應用程序）我們稍後將在 Go 應用程序中使用這些。

![](Images/Day13_Go4.png)

現在我們已經創建了我們的應用程序，（我確實必須更改我的應用程序名稱，因為上面截圖中的名稱已被使用，這些名稱需要是唯一的）

![](Images/Day13_Go5.png)

我們之前收集的密鑰被稱為我們的消費者密鑰，我們還需要我們的訪問令牌和密鑰。我們可以使用"Keys & Tokens"選項卡收集此信息。

![](Images/Day13_Go6.png)

好的，我們現在在 Twitter 開發者門戶中完成了。確保你保持密鑰安全，因為我們稍後會需要它們。

## Go Twitter Bot

記住我們在應用程序中開始的代碼 [day13_example1](Go/day13_example1.go)，但首先，我們需要檢查我們有正確的代碼來讓某些東西發布推文

我們現在需要考慮代碼，以將我們的輸出或消息以推文的形式發送到 Twitter。我們將使用 [go-twitter](https://github.com/dghubble/go-twitter)，這是 Twitter API 的 Go 客戶端庫。

在將其放入我們的主應用程序之前測試這個，我在我們的 `src` 文件夾中創建了一個名為 go-twitter-bot 的新目錄，在文件夾上發出 `go mod init github.com/michaelcade/go-Twitter-bot`，然後創建了一個 `go.mod` 文件，然後我們可以開始編寫我們的新 main.go 並測試這個。

我們現在需要從 Twitter 開發者門戶收集的那些密鑰、令牌和密鑰。我們將在環境變量中設置這些。這將取決於你運行的操作系統：

我收到了一些關於環境變量的問題，這裡有一篇博客文章更詳細地介紹了這一點，以便你可以理解正在發生的事情。[How To Set Environment Variables](https://www.twilio.com/blog/2017/01/how-to-set-environment-variables.html)

Windows

```
set CONSUMER_KEY
set CONSUMER_SECRET
set ACCESS_TOKEN
set ACCESS_TOKEN_SECRET
```

Linux / macOS

```
export CONSUMER_KEY
export CONSUMER_SECRET
export ACCESS_TOKEN
export ACCESS_TOKEN_SECRET
```

在這個階段，你可以查看 [day13_example2](Go/day13_example2.go) 的代碼，但你會看到這裡我們使用結構來定義我們的密鑰、密鑰和令牌。

然後我們有一個 `func` 來解析這些憑據並與 Twitter API 建立連接

然後根據成功，我們將發送一條推文。

```
package main

import (
    // other imports
    "fmt"
    "log"
    "os"

    "github.com/dghubble/go-twitter/twitter"
    "github.com/dghubble/oauth1"
)

// Credentials stores all of our access/consumer tokens
// and secret keys needed for authentication against
// the twitter REST API.
type Credentials struct {
    ConsumerKey       string
    ConsumerSecret    string
    AccessToken       string
    AccessTokenSecret string
}

// getClient is a helper function that will return a twitter client
// that we can subsequently use to send tweets, or to stream new tweets
// this will take in a pointer to a Credential struct which will contain
// everything needed to authenticate and return a pointer to a twitter Client
// or an error
func getClient(creds *Credentials) (*twitter.Client, error) {
    // Pass in your consumer key (API Key) and your Consumer Secret (API Secret)
    config := oauth1.NewConfig(creds.ConsumerKey, creds.ConsumerSecret)
    // Pass in your Access Token and your Access Token Secret
    token := oauth1.NewToken(creds.AccessToken, creds.AccessTokenSecret)

    httpClient := config.Client(oauth1.NoContext, token)
    client := twitter.NewClient(httpClient)

    // Verify Credentials
    verifyParams := &twitter.AccountVerifyParams{
        SkipStatus:   twitter.Bool(true),
        IncludeEmail: twitter.Bool(true),
    }

    // we can retrieve the user and verify if the credentials
    // we have used successfully allow us to log in!
    user, _, err := client.Accounts.VerifyCredentials(verifyParams)
    if err != nil {
        return nil, err
    }

    log.Printf("User's ACCOUNT:\n%+v\n", user)
    return client, nil
}
func main() {
    fmt.Println("Go-Twitter Bot v0.01")
    creds := Credentials{
        AccessToken:       os.Getenv("ACCESS_TOKEN"),
        AccessTokenSecret: os.Getenv("ACCESS_TOKEN_SECRET"),
        ConsumerKey:       os.Getenv("CONSUMER_KEY"),
        ConsumerSecret:    os.Getenv("CONSUMER_SECRET"),
    }

    client, err := getClient(&creds)
    if err != nil {
        log.Println("Error getting Twitter Client")
        log.Println(err)
    }

    tweet, resp, err := client.Statuses.Update("A Test Tweet from the future, testing a #90DaysOfDevOps Program that tweets, tweet tweet", nil)
    if err != nil {
        log.Println(err)
    }
    log.Printf("%+v\n", resp)
    log.Printf("%+v\n", tweet)
}

```

根據正在發生的事情，上面的代碼會給你一個錯誤，或者它會成功，你將發送一條包含代碼中概述的消息的推文。

## 將兩者配對 - Go-Twitter-Bot + 我們的應用程序

現在我們需要在 `main.go` 中合併這兩個，我確信有人在大喊有更好的方法來做到這一點，請對此發表評論，因為你可以在項目中擁有多個 `.go` 文件，這可能有意義，但這有效。

你可以看到合併的代碼庫 [day13_example3](Go/day13_example3.go)，但我也會在下面顯示它。

```
package main

import (
    // other imports
    "fmt"
    "log"
    "os"

    "github.com/dghubble/go-twitter/twitter"
    "github.com/dghubble/oauth1"
)

// Credentials stores all of our access/consumer tokens
// and secret keys needed for authentication against
// the twitter REST API.
type Credentials struct {
    ConsumerKey       string
    ConsumerSecret    string
    AccessToken       string
    AccessTokenSecret string
}

// getClient is a helper function that will return a twitter client
// that we can subsequently use to send tweets, or to stream new tweets
// this will take in a pointer to a Credential struct which will contain
// everything needed to authenticate and return a pointer to a twitter Client
// or an error
func getClient(creds *Credentials) (*twitter.Client, error) {
    // Pass in your consumer key (API Key) and your Consumer Secret (API Secret)
    config := oauth1.NewConfig(creds.ConsumerKey, creds.ConsumerSecret)
    // Pass in your Access Token and your Access Token Secret
    token := oauth1.NewToken(creds.AccessToken, creds.AccessTokenSecret)

    httpClient := config.Client(oauth1.NoContext, token)
    client := twitter.NewClient(httpClient)

    // Verify Credentials
    verifyParams := &twitter.AccountVerifyParams{
        SkipStatus:   twitter.Bool(true),
        IncludeEmail: twitter.Bool(true),
    }

    // we can retrieve the user and verify if the credentials
    // we have used successfully allow us to log in!
    user, _, err := client.Accounts.VerifyCredentials(verifyParams)
    if err != nil {
        return nil, err
    }

    log.Printf("User's ACCOUNT:\n%+v\n", user)
    return client, nil
}
func main() {
    creds := Credentials{
        AccessToken:       os.Getenv("ACCESS_TOKEN"),
        AccessTokenSecret: os.Getenv("ACCESS_TOKEN_SECRET"),
        ConsumerKey:       os.Getenv("CONSUMER_KEY"),
        ConsumerSecret:    os.Getenv("CONSUMER_SECRET"),
    }
    {
        const DaysTotal int = 90
        var remainingDays uint = 90
        challenge := "#90DaysOfDevOps"

        fmt.Printf("Welcome to the %v challenge.\nThis challenge consists of %v days\n", challenge, DaysTotal)

        var TwitterName string
        var DaysCompleted uint

        // asking for user input
        fmt.Println("Enter Your Twitter Handle: ")
        fmt.Scanln(&TwitterName)

        fmt.Println("How many days have you completed?: ")
        fmt.Scanln(&DaysCompleted)

        // calculate remaining days
        remainingDays = remainingDays - DaysCompleted

        //fmt.Printf("Thank you %v for taking part and completing %v days.\n", TwitterName, DaysCompleted)
        //fmt.Printf("You have %v days remaining for the %v challenge\n", remainingDays, challenge)
        // fmt.Println("Good luck")

        client, err := getClient(&creds)
        if err != nil {
            log.Println("Error getting Twitter Client, this is expected if you did not supply your Twitter API tokens")
            log.Println(err)
        }

        message := fmt.Sprintf("Hey I am %v I have been doing the %v for %v days and I have %v Days left", TwitterName, challenge, DaysCompleted, remainingDays)
        tweet, resp, err := client.Statuses.Update(message, nil)
        if err != nil {
            log.Println(err)
        }
        log.Printf("%+v\n", resp)
        log.Printf("%+v\n", tweet)
    }

}
```

這應該會產生一條推文，但如果你沒有提供環境變量，那麼你應該會收到如下錯誤。

![](Images/Day13_Go7.png)

一旦你修復了這個問題，或者如果你選擇不與 Twitter 進行身份驗證，那麼你可以使用我們昨天完成的代碼。成功時的終端輸出將類似於：

![](Images/Day13_Go8.png)

生成的推文應該看起來像這樣：

![](Images/Day13_Go9.png)

## 如何為多個操作系統編譯

接下來我想介紹這個問題，"你如何為多個操作系統編譯？" Go 的優點是它可以輕鬆地為許多不同的操作系統編譯。你可以通過運行以下命令獲得完整列表：

```
go tool dist list
```

到目前為止使用我們的 `go build` 命令很好，它將使用 `GOOS` 和 `GOARCH` 環境變量來確定主機以及應該為其構建構建。但我們也可以使用下面的代碼作為示例創建其他二進制文件。

```
GOARCH=amd64 GOOS=darwin go build -o ${BINARY_NAME}_0.1_darwin main.go
GOARCH=amd64 GOOS=linux go build -o ${BINARY_NAME}_0.1_linux main.go
GOARCH=amd64 GOOS=windows go build -o ${BINARY_NAME}_0.1_windows main.go
GOARCH=arm64 GOOS=linux go build -o ${BINARY_NAME}_0.1_linux_arm64 main.go
GOARCH=arm64 GOOS=darwin go build -o ${BINARY_NAME}_0.1_darwin_arm64 main.go
```

這將為你提供上述所有平台的目錄中的二進制文件。然後你可以使用這個並創建一個 makefile，每當你向代碼添加新功能和特性時構建這些二進制文件。我已經包含了 [makefile](Go/makefile)

這是我用來創建你現在可以在[存儲庫](https://github.com/MichaelCade/90DaysOfDevOps/releases)上看到的發布版本。

## 資源

- [StackOverflow 2021 Developer Survey](https://insights.stackoverflow.com/survey/2021)
- [Why we are choosing Golang to learn](https://www.youtube.com/watch?v=7pLqIIAqZD4&t=9s)
- [Jake Wright - Learn Go in 12 minutes](https://www.youtube.com/watch?v=C8LgvuEBraI&t=312s)
- [Techworld with Nana - Golang full course - 3 hours 24 mins](https://www.youtube.com/watch?v=yyUHQIec83I)
- [**NOT FREE** Nigel Poulton Pluralsight - Go Fundamentals - 3 hours 26 mins](https://www.pluralsight.com/courses/go-fundamentals)
- [FreeCodeCamp - Learn Go Programming - Golang Tutorial for Beginners](https://www.youtube.com/watch?v=YS4e4q9oBaU&t=1025s)
- [Hitesh Choudhary - Complete playlist](https://www.youtube.com/playlist?list=PLRAV69dS1uWSR89FRQGZ6q9BR2b44Tr9N)
- [A great repo full of all things DevOps & exercises](https://github.com/bregman-arie/devops-exercises)
- [GoByExample - Example based learning](https://gobyexample.com/)
- [go.dev/tour/list](https://go.dev/tour/list)
- [go.dev/learn](https://go.dev/learn/)

這結束了 7 天的程式語言學習！還有很多可以涵蓋的內容，我希望你能夠繼續完成上述內容，並能夠理解 Go 程式語言的其他一些方面。

接下來，我們將重點轉向 Linux 以及我們都應該知道的一些基礎知識。

我們[Day 14](day14.md)見.
