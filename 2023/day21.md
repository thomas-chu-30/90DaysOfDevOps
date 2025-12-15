# 持續映像儲存庫掃描

在 [Day 14](day14.md) 中，我們學習了什麼是容器映像掃描以及為什麼它很重要。
我們還了解了像 Grype 和 Trivy 這樣的工具，它們幫助我們掃描容器映像。

然而，在現代 SDLC 中，DevSecOps 工程師很少會手動掃描容器映像，例如，他們不會在本地運行 Grype 和 Trivy 並查看每個漏洞。
相反，他們會將映像掃描配置為 CI/CD 管道的一部分。
這樣，他們可以確保所有由管道構建的映像也會被映像掃描器掃描。
然後這些結果可以由另一個系統發送，DevSecOps 工程師可以在那裡查看它們並根據結果採取一些行動。

示例 CI/CD 管道可能如下所示：

0. _開發人員推送代碼_
1. Lint 代碼
2. 構建代碼
3. 測試代碼
4. 構建工件（容器映像、helm charts 等）
5. 掃描工件
6. （可選）將掃描結果發送到某處
7. （可選）驗證掃描結果，如果驗證失敗則使管道失敗
8. 將工件推送到儲存庫

掃描或驗證步驟（步驟 6 和 7）的失敗意味著我們的容器不會被推送到我們的儲存庫，我們無法使用我們提交的代碼。

今天，我們將了解如何設置這樣的管道以及什麼樣的配置是合理的。

## 使用 Grype 設置 CI/CD 管道

讓我們看一下 [Grype](https://github.com/anchore/grype) 掃描器。
Grype 是由 [Anchore](https://anchore.com/) 公司維護的開源掃描器。

### 使用 Grype 掃描映像

使用 Grype 掃描容器映像就像運行一樣簡單：

```shell
grype <IMAGE>
```

例如，如果我們想掃描 `ubuntu:20.04` 映像，我們可以運行：

```shell
$ grype ubuntu:20.04

 ✔ Vulnerability DB        [no update available]
 ✔ Pulled image
 ✔ Loaded image
 ✔ Parsed image
 ✔ Cataloged packages      [92 packages]
 ✔ Scanned image           [19 vulnerabilities]

NAME          INSTALLED                 FIXED-IN  TYPE  VULNERABILITY   SEVERITY
coreutils     8.30-3ubuntu2                       deb   CVE-2016-2781   Low
gpgv          2.2.19-3ubuntu2.2                   deb   CVE-2022-3219   Low
libc-bin      2.31-0ubuntu9.9                     deb   CVE-2016-20013  Negligible
libc6         2.31-0ubuntu9.9                     deb   CVE-2016-20013  Negligible
libncurses6   6.2-0ubuntu2                        deb   CVE-2021-39537  Negligible
libncurses6   6.2-0ubuntu2                        deb   CVE-2022-29458  Negligible
libncursesw6  6.2-0ubuntu2                        deb   CVE-2021-39537  Negligible
libncursesw6  6.2-0ubuntu2                        deb   CVE-2022-29458  Negligible
libpcre3      2:8.39-12ubuntu0.1                  deb   CVE-2017-11164  Negligible
libsystemd0   245.4-4ubuntu3.19                   deb   CVE-2022-3821   Medium
libtinfo6     6.2-0ubuntu2                        deb   CVE-2021-39537  Negligible
libtinfo6     6.2-0ubuntu2                        deb   CVE-2022-29458  Negligible
libudev1      245.4-4ubuntu3.19                   deb   CVE-2022-3821   Medium
login         1:4.8.1-1ubuntu5.20.04.4            deb   CVE-2013-4235   Low
ncurses-base  6.2-0ubuntu2                        deb   CVE-2021-39537  Negligible
ncurses-base  6.2-0ubuntu2                        deb   CVE-2022-29458  Negligible
ncurses-bin   6.2-0ubuntu2                        deb   CVE-2021-39537  Negligible
ncurses-bin   6.2-0ubuntu2                        deb   CVE-2022-29458  Negligible
passwd        1:4.8.1-1ubuntu5.20.04.4            deb   CVE-2013-4235   Low
```

當然，您已經知道這一點，因為我們在 [Day 14](day14.md) 中做過。

然而，此命令只會輸出漏洞並以成功代碼退出。
因此，如果這在 CI/CD 管道中，即使我們有許多漏洞，管道也會成功。

運行管道的人必須打開它，查看日誌並手動確定結果是否正常。
這很繁瑣且容易出錯。

讓我們看看如何為掃描結果強制執行一些規則。

### 為掃描的映像強制執行規則

正如我們已經確定的，僅僅掃描映像除了讓我們了解映像中漏洞的數量之外，並沒有多大作用。
但是如果我們想為容器映像強制執行一組規則怎麼辦？

例如，一個好的規則是「映像不應該有關鍵漏洞」或「映像不應該有可用修復的漏洞」。

幸運的是，這也是 Grype 開箱即用支持的東西。
我們可以使用 `--fail-on <SEVERITY>` 標誌告訴 Grype，如果在掃描期間發現嚴重性高於或等於我們指定的漏洞，則以非零退出代碼退出。
這將使我們的管道失敗，工程師必須查看結果並修復某些內容才能使其通過。

讓我們試試看。
我們將使用 `springio/petclinic:latest` 映像，我們已經發現它有很多漏洞。
您可以返回 [Day 14](day14.md) 或自己掃描它以查看確切有多少。

如果映像有 `CRITICAL` 漏洞，我們希望管道失敗。
我們將這樣運行掃描：

```shell
$ grype springio/petclinic:latest --fail-on critical
 ✔ Vulnerability DB        [no update available]
 ✔ Loaded image
 ✔ Parsed image
 ✔ Cataloged packages      [212 packages]
 ✔ Scanned image           [168 vulnerabilities]

NAME        INSTALLED FIXED-IN TYPE         VULNERABILITY    SEVERITY
spring-core 5.3.6              java-archive CVE-2016-1000027 Critical
spring-core 5.3.6              java-archive CVE-2022-22965   Critical
...
1 error occurred:
    * discovered vulnerabilities at or above the severity threshold

$ echo $?
1
```

我們在這裡看到兩件事：

- 除了結果之外，Grype 還輸出了一個錯誤，告訴我們此掃描違反了我們定義的規則（沒有 CRITICAL 漏洞）
- Grype 以退出代碼 1 退出，這表示失敗。
  如果這是一個 CI 管道，它會失敗。

當這種情況發生時，我們將被阻止合併我們的代碼並將我們的容器推送到註冊表。
這意味著我們需要採取一些行動來修復失敗，以便我們可以完成任務並推送我們的更改。

讓我們看看我們的選項是什麼。

### 修復管道

一旦我們遇到阻止我們發布容器的漏洞，我們有幾種方法可以根據漏洞進行處理。

#### 1. 漏洞有修復

最好的情況是此漏洞在我們依賴的庫的新版本中已經修復。

這樣一個漏洞是這個：

```text
NAME      INSTALLED FIXED-IN TYPE         VULNERABILITY       SEVERITY
snakeyaml 1.27      1.31     java-archive GHSA-3mc7-4q67-w48m High
```

這是一個 `High` 嚴重性漏洞。
它來自 Java 套件 `snakeyaml`，版本 `1.27`。
Grype 告訴我們此漏洞在同一庫的版本 `1.31` 中已修復。

在這種情況下，我們可以在 `pom.xml` 或 `build.gradle` 檔案中升級此庫的版本，
測試我們的代碼以確保新版本不會破壞任何東西，
然後再次提交代碼。

這將構建我們容器的新版本，重新掃描它，希望這次漏洞不會出現，我們的掃描將成功。

### 2. 漏洞沒有修復，但它不危險

有時我們遇到的漏洞將沒有可用的修復。
這些是所謂的零日漏洞，在修復可用之前被披露。

我們可以在初始掃描結果中看到其中兩個：

```text
NAME        INSTALLED FIXED-IN TYPE         VULNERABILITY    SEVERITY
spring-core 5.3.6              java-archive CVE-2016-1000027 Critical
spring-core 5.3.6              java-archive CVE-2022-22965   Critical
```

當我們遇到這樣的漏洞時，我們需要評估它的嚴重程度並計算在軟體中包含該漏洞的發布風險。

我們可以確定該漏洞不會對我們的軟體及其用戶構成任何危險。
這樣一種情況可能是當漏洞需要物理訪問伺服器才能被利用時。
如果我們確信我們的物理伺服器足夠安全，攻擊者無法訪問它們，我們可以安全地忽略此漏洞。

在這種情況下，我們可以告訴 Grype 忽略此漏洞，不要因為它而使掃描失敗。

我們可以通過 `grype.yaml` 配置文件來做到這一點，我們可以在其中列出我們想要忽略的漏洞：

```yaml
ignore:
  # This is the full set of supported rule fields:
  - vulnerability: CVE-2016-1000027
    fix-state: unknown
    package:
      name: spring-core
      version: 5.3.6
      type: java-archive
  # We can list as many of these as we want
  - vulnerability: CVE-2022-22965
  # Or list whole packages which we want to ignore
  - package:
      type: gem
```

將此放在我們的配置檔案中並重新運行掃描將使我們的管道變綠。

然而，重要的是我們要跟踪此檔案，不要忽略有修復的漏洞。
例如，當此漏洞的修復發布時，最好我們升級我們的依賴項並從我們的應用程式中刪除此漏洞。

這樣，我們將確保我們的應用程式盡可能安全，並且沒有漏洞可能比我們最初認為的更嚴重。

### 3. 漏洞沒有修復，而且它確實危險

最壞的情況是如果我們遇到一個沒有修復的漏洞，而且它確實危險，並且有可能被利用。

在這種情況下，沒有正確的舉動。
我們能做的最好的事情是與我們的安全團隊坐下來制定行動計劃。

我們可能決定在漏洞修復之前最好什麼都不做。
我們可能決定手動修補一些東西，以便我們至少消除部分危險。
這真的取決於情況。

有時，零日漏洞已經在您部署的應用程式中。
在這種情況下，凍結部署不會有幫助，因為您的應用程式已經易受攻擊。

這就是 Log4Shell 漏洞的情況，該漏洞在 2021 年底被發現，但自 2013 年以來一直存在於 Log4j 中。
幸運的是，幾個小時內就有修復可用，但下次我們可能不會這麼幸運。

## 總結

正如我們在 [Day 14](day14.md) 中已經學到的，掃描容器映像以查找漏洞很重要，因為它可以為您提供有關映像安全態勢的寶貴見解。

今天我們了解到，將其作為 CI/CD 管道的一部分並為映像中的漏洞強制執行一些基本規則會更好。

最後，我們討論了當我們發現漏洞時可以採取的步驟。

明天我們將查看開箱即用支持此掃描的容器註冊表，以及掃描其他類型的工件。
在 [Day 22](day22.md) 見。
