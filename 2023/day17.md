# 模糊測試進階

昨天我們學習了什麼是模糊測試以及如何編寫模糊測試（帶有模糊輸入的單元測試）。
然而，模糊測試不僅僅是單元測試。
我們可以使用這種方法通過模糊發送到我們伺服器的請求來測試我們的 Web 應用程式。

今天，我們將採用實用的方法來模糊測試 Web 伺服器。

不同的工具可以幫助我們做到這一點。

這樣的工具是 [Burp Intruder](https://portswigger.net/burp/documentation/desktop/tools/intruder) 和 [SmartBear](https://smartbear.com/)。
然而，有一些專有工具需要付費許可證才能使用它們。

這就是為什麼在今天的演示中，我們將使用一個簡單的用 Go 編寫的開源 CLI，它受到 Burp Intruder 的啟發並提供類似功能。
它被稱為 [httpfuzz](https://github.com/JonCooperWorks/httpfuzz)。


## 開始使用

這個工具相當簡單。
我們為它提供請求模板（其中我們定義了模糊數據的佔位符）、單詞列表（模糊數據），`httpfuzz` 將渲染請求並將它們發送到我們的伺服器。

首先，我們需要為我們的請求定義一個模板。
創建一個名為 `request.txt` 的檔案，內容如下：

```text
POST / HTTP/1.1
Content-Type: application/json
User-Agent: PostmanRuntime/7.26.3
Accept: */*
Cache-Control: no-cache
Host: localhost:8000
Accept-Encoding: gzip, deflate
Connection: close
Content-Length: 35

{
    "name": "`S9`",
}
```

這是一個有效的 HTTP `POST` 請求，發送到帶有 JSON 主體的 `/` 路由。
主體中的 "\`" 符號定義了一個佔位符，將用我們提供的數據替換。

`httpfuzz` 還可以模糊標頭、路徑和 URL 參數。

接下來，我們需要提供一個將放置在請求中的輸入單詞列表。
創建一個名為 `data.txt` 的檔案，內容如下：

```text
SOME_NAME
Mozilla/5.0 (Linux; Android 7.0; SM-G930VC Build/NRD90M; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/58.0.3029.83 Mobile Safari/537.36
```

在此檔案中，我們定義了兩個將替換到主體中的輸入。
在實際場景中，您應該在這裡放置更多數據以進行適當的模糊測試。

現在我們有了模板和輸入，讓我們運行該工具。
不幸的是，此工具不是作為二進制文件分發的，因此我們必須從源代碼構建它。
克隆儲存庫並運行：

```shell
go build -o httpfuzz cmd/httpfuzz.go
```

（需要在您的機器上安裝最新版本的 Go）。

現在我們有了二進制文件，讓我們運行它：

```shell
./httpfuzz \
   --wordlist data.txt \
   --seed-request request.txt \
   --target-header User-Agent \
   --target-param fuzz \
   --delay-ms 50 \
   --skip-cert-verify \
   --proxy-url http://localhost:8080 \
```

- `httpfuzz` 是我們正在調用的二進制文件。
- `--wordlist data.txt` 是我們提供的輸入檔案。
- `--seed-request requests.txt` 是請求模板。
- `--target-header User-Agent` 告訴 `httpfuzz` 在 `User-Agent` 標頭的位置使用提供的輸入。
- `--target-param fuzz` 告訴 `httpfuzz` 將提供的輸入用作 `fuzz` URL 參數的值。
- `--delay-ms 50` 告訴 `httpfuzz` 在請求之間等待 50 毫秒。
- `--skip-cert-verify` 告訴 `httpfuzz` 不進行任何 TLS 驗證。
- `--proxy-url http://localhost:8080` 告訴 `httpfuzz` 我們的 HTTP 伺服器在哪裡。

我們有 2 個輸入和 3 個放置它們的位置（在主體中、`User-Agent` 標頭和 `fuzz` 參數中）。
這意味著 `httpfuzz` 將生成 6 個請求並將它們發送到我們的伺服器。

讓我們運行它，看看會發生什麼。
我編寫了一個簡單的 Web 伺服器，記錄所有請求，以便我們可以看到進入我們伺服器的內容：

```shell
$ ./httpfuzz \
   --wordlist data.txt \
   --seed-request request.txt \
   --target-header User-Agent \
   --target-param fuzz \
   --delay-ms 50 \
   --skip-cert-verify \
   --proxy-url http://localhost:8080 \

httpfuzz: httpfuzz.go:164: Sending 6 requests
```

伺服器日誌：

```text
-----
Got request to http://localhost:8000/
User-Agent header = [SOME_NAME]
Name = S9
-----
Got request to http://localhost:8000/?fuzz=SOME_NAME
User-Agent header = [PostmanRuntime/7.26.3]
Name = S9
-----
Got request to http://localhost:8000/
User-Agent header = [PostmanRuntime/7.26.3]
Name = SOME_NAME
-----
Got request to http://localhost:8000/
User-Agent header = [Mozilla/5.0 (Linux; Android 7.0; SM-G930VC Build/NRD90M; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/58.0.3029.83 Mobile Safari/537.36]
Name = S9
-----
Got request to http://localhost:8000/?fuzz=Mozilla%2F5.0+%28Linux%3B+Android+7.0%3B+SM-G930VC+Build%2FNRD90M%3B+wv%29+AppleWebKit%2F537.36+%28KHTML%2C+like+Gecko%29+Version%2F4.083+Mobile+Safari%2F537.36
User-Agent header = [PostmanRuntime/7.26.3]
Name = S9
-----
Got request to http://localhost:8000/
User-Agent header = [PostmanRuntime/7.26.3]
Name = Mozilla/5.0 (Linux; Android 7.0; SM-G930VC Build/NRD90M; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/58.0.3029.83 Mobile Safari/537.36
```

我們看到我們收到了 6 個 HTTP 請求。

其中兩個在 `User-Agent` 標頭中具有我們值檔案中的值，4 個具有模板中的默認標頭。
其中兩個在 `fuzz` 查詢參數中具有我們值檔案中的值，4 個具有模板中的默認標頭。
其中兩個在 `Name` 主體屬性中具有我們值檔案中的值，4 個具有模板中的默認標頭。

該工具的輕微改進可能是對這些請求進行不同的排列（例如，一個請求同時具有 `?fuzz=` 和 `User-Agent` 作為值檔案中的值）。

請注意，`httpfuzz` 不會為我們提供有關請求結果的任何資訊。
要弄清楚這一點，我們需要為我們的伺服器設置某種監控，或者編寫一個 `httpfuzz` 插件，以對我們有意義的方式處理結果。
讓我們這樣做。

要編寫自定義插件，我們需要實現 [`Listener`](https://github.com/JonCooperWorks/httpfuzz/blob/master/plugin.go#L13) 接口：

```go
// Listener must be implemented by a plugin to users to hook the request - response transaction.
// The Listen method will be run in its own goroutine, so plugins cannot block the rest of the program, however panics can take down the entire process.
type Listener interface {
    Listen(results <-chan *Result)
}
```

```go
package main

import (
    "bytes"
    "io/ioutil"
    "log"

    "github.com/joncooperworks/httpfuzz"
)

type logResponseCodePlugin struct {
    logger *log.Logger
}

func (b *logResponseCodePlugin) Listen(results <-chan *httpfuzz.Result) {
    for result := range results {
        b.logger.Printf("Got %d response from the server\n", result.Response.StatusCode)
    }
}

// New returns a logResponseCodePlugin plugin that simple logs the response code of the response.
func New(logger *log.Logger) (httpfuzz.Listener, error) {
    return &logResponseCodePlugin{logger: logger}, nil
}
```

現在我們需要先構建我們的插件：

```shell
go build -buildmode=plugin -o log exampleplugins/log/log.go
```

然後我們可以通過 `--post-request` 標誌將其插入到 `httpfuzz` 中：

```shell
$ ./httpfuzz \
   --wordlist data.txt \
   --seed-request request.txt \
   --target-header User-Agent \
   --target-param fuzz \
   --delay-ms 50 \
   --skip-cert-verify \
   --proxy-url http://localhost:8080 \
   --post-request log

httpfuzz: httpfuzz.go:164: Sending 6 requests
httpfuzz: log.go:15: Got 200 response from the server
httpfuzz: log.go:15: Got 200 response from the server
httpfuzz: log.go:15: Got 200 response from the server
httpfuzz: log.go:15: Got 200 response from the server
httpfuzz: log.go:15: Got 200 response from the server
httpfuzz: log.go:15: Got 200 response from the server
```

完成了！
現在我們至少可以看到來自伺服器的響應代碼是什麼。

當然，我們可以編寫更複雜的插件，輸出更多數據，但對於本練習的目的，這就足夠了。

## 總結

模糊測試是一種非常強大的測試技術，遠遠超出了單元測試。

通過用可能暴露我們伺服器中漏洞或缺陷的數據替換有效 HTTP 請求的部分，模糊測試對於測試 HTTP 伺服器非常有用。

有許多工具可以幫助我們模糊測試我們的 Web 應用程式，既有免費的也有付費的。

## 資源

[OWASP: Fuzzing](https://owasp.org/www-community/Fuzzing)

[OWASP: Fuzz Vectors](https://owasp.org/www-project-web-security-testing-guide/v41/6-Appendix/C-Fuzz_Vectors)

[Hacking HTTP with HTTPfuzz](https://medium.com/swlh/hacking-http-with-httpfuzz-67cfd061b616)

[Fuzzing the Stack for Fun and Profit at DefCamp 2019](https://www.youtube.com/watch?v=qCMfrbpuCBk&list=PLnwq8gv9MEKiUOgrM7wble1YRsrqRzHKq&index=33)

[HTTP Fuzzing Scan with SmartBear](https://support.smartbear.com/readyapi/docs/security/scans/types/fuzzing-http.html)

[Fuzzing Session: Finding Bugs and Vulnerabilities Automatically](https://youtu.be/DSJePjhBN5E)

[Fuzzing the CNCF Landscape](https://youtu.be/zIyIZxAZLzo)

在 [Day 18](day18.md) 見。
