# IAST 和 DAST 結合使用 - 實驗時間

在學習了 IAST 和 DAST 是什麼之後，是時候動手實踐並執行一個練習，在這個練習中我們使用這些過程在真實應用程式中查找漏洞。

**注意：** 沒有開源 IAST 實現，因此我們必須使用商業解決方案。
別擔心，有一個免費層，因此您可以在不支付任何費用的情況下跟隨實驗。

本實驗基於此 [儲存庫](https://github.com/rstatsinger/contrast-java-webgoat-docker)。

它包含一個易受攻擊的 Java 應用程式以供測試和利用，Docker 和 Docker Compose 用於輕鬆設置，以及 [Contrast Community Edition](https://www.contrastsecurity.com/contrast-community-edition?utm_campaign=ContrastCommunityEdition&utm_source=GitHub&utm_medium=WebGoatLab) 用於 IAST 解決方案。

## 先決條件

- [Docker](https://www.docker.com/products/docker-desktop/)
- [Docker Compose](https://docs.docker.com/compose/)
- Contrast CE 帳戶。在此處免費註冊 [here](https://www.contrastsecurity.com/contrast-community-edition?utm_campaign=ContrastCommunityEdition&utm_source=GitHub&utm_medium=WebGoatLab)。

**注意：** 本文的作者和 90 Days of DevOps 計劃的作者與 Contrast Security 沒有任何關聯或隸屬關係。
我們使用此商業解決方案，因為沒有開源解決方案，並且因為這個有一個不需要支付或提供信用卡的免費層。

1. 由於沒有開源 IAST 實現，我們將使用一個具有一些免費許可證的商業實現。為此，您需要 2 個組件：
   來自這裡的 IAST 解決方案 - <https://github.com/rstatsinger/contrast-java-webgoat-docker>。您需要在 mac 或 linux 環境中安裝 docker 和 docker-compose（本實驗在 Mint 上進行測試）。請按照 README 在 Contrast 中創建帳戶。

## 開始使用

首先，克隆 [儲存庫](https://github.com/rstatsinger/contrast-java-webgoat-docker)。

從 Contrast Security 獲取您的憑證。
點擊右上角您的姓名 -> `Organization Settings` -> `Agent`。
獲取 `Agent Username`、`Agent Service Key` 和 `API Key` 的值。

在新克隆的儲存庫中的 `.env.template` 檔案中替換這些值。

**注意：** 這些值是機密的。
不要將它們提交到 Git。
最好將 `.env.template` 放在 `.gitignore` 下，這樣您就不會意外提交這些值。

## 運行易受攻擊的應用程式

要運行易受攻擊的應用程式，請運行：

```sh
./run.sh
```

或

```sh
docker compose up
```

準備就緒後，應用程式 UI 將在 <http://localhost:8080/WebGoat> 上可訪問。

## 造成一些損害

現在我們有了一個易受攻擊的應用程式，讓我們嘗試利用它。

1. 從 [這裡](https://www.zaproxy.org/download/) 安裝 ZAP Proxy

   一種簡單的方法是通過 DAST 掃描器。
   其中一個掃描器是 [ZAP Proxy](https://www.zaproxy.org/)。
   它是一個免費的開源 Web 應用程式掃描器。

2. 從 [這裡](https://github.com/Grunny/zap-cli) 安裝 `zap-cli`

   接下來，安裝 `zap-cli`。
   `zap-cli` 是 ZAP Proxy 的開源 CLI。

3. 運行 ZAP proxy

   從其安裝位置運行 ZAP Proxy。
   在 Linux Mint 中，默認情況下它在 `/opt/zaproxy`。
   在 MacOS 中，它在 `Applications` 中。

4. 為 `ZAP_API_KEY` 和 `ZAP_PORT` 設置環境變數

   從 ZAP Proxy 獲取這些值。
   轉到 `Options...` -> `API` 以獲取 API 密鑰。

   轉到 `Options...` -> `Network` -> `Local Servers/Proxies` 以配置並獲取端口。

5. 使用 `zap-cli` 運行幾個命令

   例如：

   ```sh
   zap-cli quick-scan -s all --ajax-spider -r http://127.0.0.1:8080/WebGoat/login.mvc
   ```

   或者，您可以按照 [儲存庫](https://github.com/rstatsinger/contrast-java-webgoat-docker/blob/master/Lab-WebGoat.pdf) 中的說明
   對易受攻擊的應用程式造成一些損害。

6. 在 Constrast 中觀察發現

   無論哪種方式，如果您轉到 Contrast 中應用程式的 **Vulnerabilities** 標籤，您應該能夠看到 Contrast 檢測到了漏洞
   並警告您採取一些行動。

## 獎勵：映像掃描

我們看到 IAST 解決方案如何通過觀察應用程式的行為幫助我們檢測攻擊。
讓我們看看我們是否可以在第一時間做一些事情來防止這些攻擊。

我們用於此演示的易受攻擊的應用程式被打包為容器。
讓我們通過我們在 [Day 14](day14.md) 和 [Day 15](day15.md) 中學習的 `grype` 掃描器掃描此容器，看看結果。

```sh
$ grype contrast-java-webgoat-docker-webgoat
 ✔ Vulnerability DB        [no update available]
 ✔ Loaded image
 ✔ Parsed image
 ✔ Cataloged packages      [316 packages]
 ✔ Scanned image           [374 vulnerabilities]
NAME                 INSTALLED               FIXED-IN                TYPE          VULNERABILITY        SEVERITY
apt                  1.8.2.3                                         deb           CVE-2011-3374        Negligible
axis                 1.4                                             java-archive  GHSA-55w9-c3g2-4rrh  Medium
axis                 1.4                                             java-archive  GHSA-96jq-75wh-2658  Medium
bash                 5.0-4                                           deb           CVE-2019-18276       Negligible
bash                 5.0-4                   (won't fix)             deb           CVE-2022-3715        High
bsdutils             1:2.33.1-0.1                                    deb           CVE-2022-0563        Negligible
bsdutils             1:2.33.1-0.1            (won't fix)             deb           CVE-2021-37600       Low
commons-beanutils    1.8.3                                           java-archive  CVE-2014-0114        High
commons-beanutils    1.8.3                                           java-archive  CVE-2019-10086       High
commons-beanutils    1.8.3                   1.9.2                   java-archive  GHSA-p66x-2cv9-qq3v  High
commons-beanutils    1.8.3                   1.9.4                   java-archive  GHSA-6phf-73q6-gh87  High
commons-collections  3.2.1                                           java-archive  CVE-2015-6420        High
commons-collections  3.2.1                   3.2.2                   java-archive  GHSA-6hgm-866r-3cjv  High
commons-collections  3.2.1                   3.2.2                   java-archive  GHSA-fjq5-5j5f-mvxh  Critical
commons-fileupload   1.3.1                                           java-archive  CVE-2016-1000031     Critical
commons-fileupload   1.3.1                                           java-archive  CVE-2016-3092        High
commons-fileupload   1.3.1                   1.3.2                   java-archive  GHSA-fvm3-cfvj-gxqq  High
commons-fileupload   1.3.1                   1.3.3                   java-archive  GHSA-7x9j-7223-rg5m  Critical
commons-io           2.4                                             java-archive  CVE-2021-29425       Medium
commons-io           2.4                     2.7                     java-archive  GHSA-gwrp-pvrq-jmwv  Medium
coreutils            8.30-3                                          deb           CVE-2017-18018       Negligible
coreutils            8.30-3                  (won't fix)             deb           CVE-2016-2781        Low
curl                 7.64.0-4+deb10u3                                deb           CVE-2021-22922       Negligible
curl                 7.64.0-4+deb10u3                                deb           CVE-2021-22923       Negligible
<truncated>
```

正如我們所看到的，此映像充滿了漏洞。

如果我們深入研究每一個，我們會看到我們有像 RCE（遠程代碼執行）、SQL 注入、XML 外部實體漏洞等漏洞。

## 週總結

IAST 和 DAST 是重要的方法，可以幫助我們通過監控應用程式的行為在應用程式中查找漏洞。
這是在應用程式已經部署後完成的。

容器映像掃描可以幫助我們根據容器內存在的庫在應用程式中查找漏洞。

映像掃描和 IAST/DAST 不是相互排斥的。
它們在安全 SDLC 中都有自己的位置，可以幫助我們在攻擊者之前發現不同的問題。

在 [Day 21](day21.md) 見。
