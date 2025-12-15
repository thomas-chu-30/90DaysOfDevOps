# 容器映像掃描

容器映像由映像清單、檔案系統和映像配置組成。[1](https://opencontainers.org/about/overview/)

例如，Java 應用程式的容器映像的檔案系統將具有 Linux 檔案系統、JVM 和代表我們應用程式的 JAR/WAR 檔案。

如果我們使用容器，CI/CD 管道的重要部分應該是掃描這些容器以查找已知漏洞的過程。
這可以為我們提供有關容器內漏洞數量的寶貴資訊，並可以幫助我們防止將易受攻擊的應用程式部署到生產環境，並因這些漏洞而被駭客攻擊。

讓我們以 2021 年底發現的 [Log4Shell](https://en.wikipedia.org/wiki/Log4Shell) 漏洞為例。
不過多詳細說明，在您的應用程式中存在此漏洞意味著攻擊者可以在您的伺服器上執行任意代碼。
更糟糕的是，此漏洞位於最受歡迎的 Java 庫之一 - [Log4j](https://logging.apache.org/log4j/2.x/) 中。
非常糟糕！

那麼我們如何知道我們是否易受攻擊？

答案是 **映像掃描**。

映像掃描過程包括查看容器內部，獲取已安裝套件列表（可能是 Linux 套件，但也可能是 Java、Go、JavaScript 套件等），將套件列表與每個套件的已知漏洞數據庫進行交叉引用，最後為給定的容器映像生成漏洞列表。

有許多開源和專有的映像掃描器，您可以安裝並立即開始掃描您的容器映像，無論是在本地機器上還是在 CI/CD 管道中。
兩個最受歡迎的是 [Trivy](https://github.com/aquasecurity/trivy) 和 [Grype](https://github.com/anchore/grype)。
一些專有的是 [Snyk](https://docs.snyk.io/products/snyk-container/snyk-cli-for-container-security)（需要帳戶，有免費層）和 [VMware Carbon Black](https://carbonblack.vmware.com/resource/carbon-black-cloud-container-security-faq#overview)（需要帳戶，無免費層）。

掃描容器映像就像安裝其中一個並運行一樣簡單：

```console
$ grype ubuntu:latest
 ✔ Vulnerability DB        [updated]
 ✔ Pulled image
 ✔ Loaded image
 ✔ Parsed image
 ✔ Cataloged packages      [101 packages]
 ✔ Scanned image           [16 vulnerabilities]
NAME          INSTALLED                 FIXED-IN  TYPE  VULNERABILITY   SEVERITY
bash          5.1-6ubuntu1                        deb   CVE-2022-3715   Medium
coreutils     8.32-4.1ubuntu1                     deb   CVE-2016-2781   Low
gpgv          2.2.27-3ubuntu2.1                   deb   CVE-2022-3219   Low
libc-bin      2.35-0ubuntu3.1                     deb   CVE-2016-20013  Negligible
libc6         2.35-0ubuntu3.1                     deb   CVE-2016-20013  Negligible
libncurses6   6.3-2                               deb   CVE-2022-29458  Negligible
libncursesw6  6.3-2                               deb   CVE-2022-29458  Negligible
libpcre3      2:8.39-13ubuntu0.22.04.1            deb   CVE-2017-11164  Negligible
libsystemd0   249.11-0ubuntu3.6                   deb   CVE-2022-3821   Medium
libtinfo6     6.3-2                               deb   CVE-2022-29458  Negligible
libudev1      249.11-0ubuntu3.6                   deb   CVE-2022-3821   Medium
login         1:4.8.1-2ubuntu2                    deb   CVE-2013-4235   Low
ncurses-base  6.3-2                               deb   CVE-2022-29458  Negligible
ncurses-bin   6.3-2                               deb   CVE-2022-29458  Negligible
passwd        1:4.8.1-2ubuntu2                    deb   CVE-2013-4235   Low
zlib1g        1:1.2.11.dfsg-2ubuntu9.2            deb   CVE-2022-42800  Medium
```

使用此命令，我們掃描了 `ubuntu:latest` 容器映像，發現它有 16 個漏洞。

每個漏洞都有一個嚴重性，基於其 [CVSS](https://nvd.nist.gov/vuln-metrics/cvss) 分數。
嚴重性從 `Low` 到 `Critical` 不等。

16 個漏洞可能看起來很多，但請注意，它們中沒有一個具有 `Critical` 嚴重性。

我們還看到結果表的 `FIXED-IN` 列是空的。
這意味著這些漏洞中沒有一個在其套件的新版本中得到修復。

這是預期的，因為 `ubuntu:latest` 是 Ubuntu 官方容器映像的最新版本。
通常這些映像會定期更新，因此您不應該期望在其中看到許多漏洞（至少不是有可用修復的漏洞）。

對於較舊的映像、隨機映像、不受大公司支持的映像或您自己沒有維護的映像，情況可能並非如此。

例如，如果我們掃描 Docker Hub 上 `springio` 組織的某個 2 年前的隨機映像，我們會看到潛伏著更多的漏洞：

```console
$ grype springio/petclinic:latest
 ✔ Vulnerability DB        [no update available]
 ✔ Pulled image
 ✔ Loaded image
 ✔ Parsed image
 ✔ Cataloged packages      [213 packages]
 ✔ Scanned image           [167 vulnerabilities]
NAME              INSTALLED                 FIXED-IN                   TYPE          VULNERABILITY        SEVERITY
bash              4.4.18-2ubuntu1.2                                    deb           CVE-2022-3715        Medium
bash              4.4.18-2ubuntu1.2         4.4.18-2ubuntu1.3          deb           CVE-2019-18276       Low
coreutils         8.28-1ubuntu1                                        deb           CVE-2016-2781        Low
dpkg              1.19.0.5ubuntu2.3         1.19.0.5ubuntu2.4          deb           CVE-2022-1664        Medium
e2fsprogs         1.44.1-1ubuntu1.3         1.44.1-1ubuntu1.4          deb           CVE-2022-1304        Medium
gcc-8-base        8.4.0-1ubuntu1~18.04                                 deb           CVE-2020-13844       Medium
gpgv              2.2.4-1ubuntu1.4          2.2.4-1ubuntu1.6           deb           CVE-2022-34903       Medium
gpgv              2.2.4-1ubuntu1.4          2.2.4-1ubuntu1.5           deb           CVE-2019-13050       Low
gpgv              2.2.4-1ubuntu1.4                                     deb           CVE-2022-3219        Low
gzip              1.6-5ubuntu1              1.6-5ubuntu1.2             deb           CVE-2022-1271        Medium
h2                1.4.200                   2.0.202                    java-archive  GHSA-7rpj-hg47-cx62  High
h2                1.4.200                   2.0.206                    java-archive  GHSA-h376-j262-vhq6  Critical
h2                1.4.200                                              java-archive  CVE-2021-23463       Critical
h2                1.4.200                                              java-archive  CVE-2021-42392       Critical
h2                1.4.200                                              java-archive  CVE-2022-23221       Critical
h2                1.4.200                   2.1.210                    java-archive  GHSA-45hx-wfhj-473x  Critical
jackson-databind  2.11.4                    2.12.7.1                   java-archive  GHSA-jjjh-jjxp-wpff  High
jackson-databind  2.11.4                    2.12.7.1                   java-archive  GHSA-rgv9-q543-rqg4  High
jackson-databind  2.11.4                                               java-archive  CVE-2022-42004       High
jackson-databind  2.11.4                                               java-archive  CVE-2020-36518       High
jackson-databind  2.11.4                                               java-archive  CVE-2022-42003       High
jackson-databind  2.11.4                    2.12.6.1                   java-archive  GHSA-57j2-w4cx-62h2  High
jquery            2.2.4                                                java-archive  CVE-2019-11358       Medium
jquery            2.2.4                                                java-archive  CVE-2020-11022       Medium
jquery            2.2.4                                                java-archive  CVE-2015-9251        Medium
jquery            2.2.4                                                java-archive  CVE-2020-11023       Medium
jquery            2.2.4                                                java-archive  CVE-2007-2379        Medium
jquery-ui         1.11.4                                               java-archive  CVE-2021-41184       Medium
jquery-ui         1.11.4                                               java-archive  CVE-2016-7103        Medium
jquery-ui         1.11.4                                               java-archive  CVE-2021-41182       Medium
jquery-ui         1.11.4                                               java-archive  CVE-2021-41183       Medium
libc-bin          2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2020-29562       Low
libc-bin          2.27-3ubuntu1.4                                      deb           CVE-2016-20013       Negligible
libc-bin          2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2020-6096        Low
libc-bin          2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2020-27618       Low
libc-bin          2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2022-23218       Low
libc-bin          2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2016-10228       Negligible
libc-bin          2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2019-25013       Low
libc-bin          2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2021-3326        Low
libc-bin          2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2021-3999        Medium
libc-bin          2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2022-23219       Low
libc-bin          2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2021-35942       Low
libc-bin          2.27-3ubuntu1.4                                      deb           CVE-2009-5155        Negligible
libc-bin          2.27-3ubuntu1.4                                      deb           CVE-2015-8985        Negligible
libc6             2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2021-3999        Medium
libc6             2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2016-10228       Negligible
libc6             2.27-3ubuntu1.4                                      deb           CVE-2009-5155        Negligible
libc6             2.27-3ubuntu1.4                                      deb           CVE-2016-20013       Negligible
libc6             2.27-3ubuntu1.4                                      deb           CVE-2015-8985        Negligible
libc6             2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2021-3326        Low
libc6             2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2021-35942       Low
libc6             2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2020-27618       Low
libc6             2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2020-6096        Low
libc6             2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2020-29562       Low
libc6             2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2022-23218       Low
libc6             2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2022-23219       Low
libc6             2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2019-25013       Low
libcom-err2       1.44.1-1ubuntu1.3         1.44.1-1ubuntu1.4          deb           CVE-2022-1304        Medium
libext2fs2        1.44.1-1ubuntu1.3         1.44.1-1ubuntu1.4          deb           CVE-2022-1304        Medium
libgcc1           1:8.4.0-1ubuntu1~18.04                                 deb           CVE-2020-13844       Medium
libgcrypt20       1.8.1-4ubuntu1.2          1.8.1-4ubuntu1.3           deb           CVE-2021-40528       Medium
libgcrypt20       1.8.1-4ubuntu1.2          1.8.1-4ubuntu1.3           deb           CVE-2021-33560       Low
libgmp10          2:6.1.2+dfsg-2            2:6.1.2+dfsg-2ubuntu0.1    deb           CVE-2021-43618       Low
libgnutls30       3.5.18-1ubuntu1.4         3.5.18-1ubuntu1.6          deb           CVE-2021-4209        Low
libgnutls30       3.5.18-1ubuntu1.4                                    deb           CVE-2018-16868       Low
libgnutls30       3.5.18-1ubuntu1.4         3.5.18-1ubuntu1.6          deb           CVE-2022-2509        Medium
libhogweed4       3.4-1ubuntu0.1            3.4.1-0ubuntu0.18.04.1     deb           CVE-2021-3580        Medium
libhogweed4       3.4-1ubuntu0.1            3.4.1-0ubuntu0.18.04.1     deb           CVE-2018-16869       Low
liblz4-1          0.0~r131-2ubuntu3         0.0~r131-2ubuntu3.1        deb           CVE-2021-3520        Medium
liblzma5          5.2.2-1.3                 5.2.2-1.3ubuntu0.1         deb           CVE-2022-1271        Medium
libncurses5       6.1-1ubuntu1.18.04                                   deb           CVE-2019-17594       Negligible
libncurses5       6.1-1ubuntu1.18.04                                   deb           CVE-2021-39537       Negligible
libncurses5       6.1-1ubuntu1.18.04                                   deb           CVE-2022-29458       Negligible
libncurses5       6.1-1ubuntu1.18.04                                   deb           CVE-2019-17595       Negligible
libncursesw5      6.1-1ubuntu1.18.04                                   deb           CVE-2019-17595       Negligible
libncursesw5      6.1-1ubuntu1.18.04                                   deb           CVE-2021-39537       Negligible
libncursesw5      6.1-1ubuntu1.18.04                                   deb           CVE-2022-29458       Negligible
libncursesw5      6.1-1ubuntu1.18.04                                   deb           CVE-2019-17594       Negligible
libnettle6        3.4-1ubuntu0.1            3.4.1-0ubuntu0.18.04.1     deb           CVE-2021-3580        Medium
libnettle6        3.4-1ubuntu0.1            3.4.1-0ubuntu0.18.04.1     deb           CVE-2018-16869       Low
libpcre3          2:8.39-9                                             deb           CVE-2017-11164       Negligible
libpcre3          2:8.39-9                  2:8.39-9ubuntu0.1          deb           CVE-2020-14155       Negligible
libpcre3          2:8.39-9                  2:8.39-9ubuntu0.1          deb           CVE-2019-20838       Low
libsepol1         2.7-1                     2.7-1ubuntu0.1             deb           CVE-2021-36086       Low
libsepol1         2.7-1                     2.7-1ubuntu0.1             deb           CVE-2021-36085       Low
libsepol1         2.7-1                     2.7-1ubuntu0.1             deb           CVE-2021-36087       Low
libsepol1         2.7-1                     2.7-1ubuntu0.1             deb           CVE-2021-36084       Low
libss2            1.44.1-1ubuntu1.3         1.44.1-1ubuntu1.4          deb           CVE-2022-1304        Medium
libssl1.1         1.1.1-1ubuntu2.1~18.04.9  1.1.1-1ubuntu2.1~18.04.15  deb           CVE-2022-0778        High
libssl1.1         1.1.1-1ubuntu2.1~18.04.9  1.1.1-1ubuntu2.1~18.04.20  deb           CVE-2022-2097        Medium
libssl1.1         1.1.1-1ubuntu2.1~18.04.9  1.1.1-1ubuntu2.1~18.04.13  deb           CVE-2021-3712        Medium
libssl1.1         1.1.1-1ubuntu2.1~18.04.9  1.1.1-1ubuntu2.1~18.04.13  deb           CVE-2021-3711        High
libssl1.1         1.1.1-1ubuntu2.1~18.04.9  1.1.1-1ubuntu2.1~18.04.17  deb           CVE-2022-1292        Medium
libssl1.1         1.1.1-1ubuntu2.1~18.04.9  1.1.1-1ubuntu2.1~18.04.19  deb           CVE-2022-2068        Medium
libstdc++6        8.4.0-1ubuntu1~18.04                                 deb           CVE-2020-13844       Medium
libsystemd0       237-3ubuntu10.47          237-3ubuntu10.56           deb           CVE-2022-2526        Medium
libsystemd0       237-3ubuntu10.47                                     deb           CVE-2022-3821        Medium
libsystemd0       237-3ubuntu10.47          237-3ubuntu10.49           deb           CVE-2020-13529       Low
libsystemd0       237-3ubuntu10.47          237-3ubuntu10.49           deb           CVE-2021-33910       High
libtinfo5         6.1-1ubuntu1.18.04                                   deb           CVE-2019-17595       Negligible
libtinfo5         6.1-1ubuntu1.18.04                                   deb           CVE-2021-39537       Negligible
libtinfo5         6.1-1ubuntu1.18.04                                   deb           CVE-2019-17594       Negligible
libtinfo5         6.1-1ubuntu1.18.04                                   deb           CVE-2022-29458       Negligible
libudev1          237-3ubuntu10.47          237-3ubuntu10.49           deb           CVE-2020-13529       Low
libudev1          237-3ubuntu10.47          237-3ubuntu10.49           deb           CVE-2021-33910       High
libudev1          237-3ubuntu10.47                                     deb           CVE-2022-3821        Medium
libudev1          237-3ubuntu10.47          237-3ubuntu10.56           deb           CVE-2022-2526        Medium
locales           2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2022-23219       Low
locales           2.27-3ubuntu1.4                                      deb           CVE-2016-20013       Negligible
locales           2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2021-3999        Medium
locales           2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2016-10228       Negligible
locales           2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2019-25013       Low
locales           2.27-3ubuntu1.4                                      deb           CVE-2009-5155        Negligible
locales           2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2021-35942       Low
locales           2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2022-23218       Low
locales           2.27-3ubuntu1.4                                      deb           CVE-2015-8985        Negligible
locales           2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2020-27618       Low
locales           2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2020-29562       Low
locales           2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2021-3326        Low
locales           2.27-3ubuntu1.4           2.27-3ubuntu1.5            deb           CVE-2020-6096        Low
log4j-api         2.13.3                                               java-archive  CVE-2021-45105       Medium
log4j-api         2.13.3                                               java-archive  CVE-2021-44832       Medium
log4j-to-slf4j    2.13.3                                               java-archive  CVE-2021-44832       Medium
log4j-to-slf4j    2.13.3                                               java-archive  CVE-2021-45105       Medium
logback-core      1.2.3                     1.2.9                      java-archive  GHSA-668q-qrv7-99fm  Medium
login             1:4.5-1ubuntu2                                       deb           CVE-2013-4235        Low
login             1:4.5-1ubuntu2            1:4.5-1ubuntu2.2           deb           CVE-2018-7169        Low
ncurses-base      6.1-1ubuntu1.18.04                                   deb           CVE-2022-29458       Negligible
ncurses-base      6.1-1ubuntu1.18.04                                   deb           CVE-2019-17595       Negligible
ncurses-base      6.1-1ubuntu1.18.04                                   deb           CVE-2019-17594       Negligible
ncurses-base      6.1-1ubuntu1.18.04                                   deb           CVE-2021-39537       Negligible
ncurses-bin       6.1-1ubuntu1.18.04                                   deb           CVE-2021-39537       Negligible
ncurses-bin       6.1-1ubuntu1.18.04                                   deb           CVE-2022-29458       Negligible
ncurses-bin       6.1-1ubuntu1.18.04                                   deb           CVE-2019-17595       Negligible
ncurses-bin       6.1-1ubuntu1.18.04                                   deb           CVE-2019-17594       Negligible
openssl           1.1.1-1ubuntu2.1~18.04.9  1.1.1-1ubuntu2.1~18.04.13  deb           CVE-2021-3712        Medium
openssl           1.1.1-1ubuntu2.1~18.04.9  1.1.1-1ubuntu2.1~18.04.19  deb           CVE-2022-2068        Medium
openssl           1.1.1-1ubuntu2.1~18.04.9  1.1.1-1ubuntu2.1~18.04.20  deb           CVE-2022-2097        Medium
openssl           1.1.1-1ubuntu2.1~18.04.9  1.1.1-1ubuntu2.1~18.04.13  deb           CVE-2021-3711        High
openssl           1.1.1-1ubuntu2.1~18.04.9  1.1.1-1ubuntu2.1~18.04.15  deb           CVE-2022-0778        High
openssl           1.1.1-1ubuntu2.1~18.04.9  1.1.1-1ubuntu2.1~18.04.17  deb           CVE-2022-1292        Medium
passwd            1:4.5-1ubuntu2            1:4.5-1ubuntu2.2           deb           CVE-2018-7169        Low
passwd            1:4.5-1ubuntu2                                       deb           CVE-2013-4235        Low
perl-base         5.26.1-6ubuntu0.5         5.26.1-6ubuntu0.6          deb           CVE-2020-16156       Medium
snakeyaml         1.27                                                 java-archive  GHSA-w37g-rhq8-7m4j  Medium
snakeyaml         1.27                      1.32                       java-archive  GHSA-9w3m-gqgf-c4p9  Medium
snakeyaml         1.27                      1.31                       java-archive  GHSA-3mc7-4q67-w48m  High
snakeyaml         1.27                      1.31                       java-archive  GHSA-c4r9-r8fh-9vj2  Medium
snakeyaml         1.27                      1.31                       java-archive  GHSA-hhhw-99gj-p3c3  Medium
snakeyaml         1.27                      1.31                       java-archive  GHSA-98wm-3w3q-mw94  Medium
spring-core       5.3.6                                                java-archive  CVE-2022-22950       Medium
spring-core       5.3.6                                                java-archive  CVE-2022-22965       Critical
spring-core       5.3.6                                                java-archive  CVE-2021-22096       Medium
spring-core       5.3.6                                                java-archive  CVE-2022-22968       Medium
spring-core       5.3.6                                                java-archive  CVE-2022-22970       Medium
spring-core       5.3.6                                                java-archive  CVE-2022-22971       Medium
spring-core       5.3.6                                                java-archive  CVE-2021-22118       High
spring-core       5.3.6                                                java-archive  CVE-2016-1000027     Critical
spring-core       5.3.6                                                java-archive  CVE-2021-22060       Medium
tar               1.29b-2ubuntu0.2          1.29b-2ubuntu0.3           deb           CVE-2021-20193       Low
zlib1g            1:1.2.11.dfsg-0ubuntu2    1:1.2.11.dfsg-0ubuntu2.2   deb           CVE-2022-37434       Medium
zlib1g            1:1.2.11.dfsg-0ubuntu2    1:1.2.11.dfsg-0ubuntu2.1   deb           CVE-2018-25032       Medium
zlib1g            1:1.2.11.dfsg-0ubuntu2                               deb           CVE-2022-42800       Medium
```

在這裡，我們不僅看到更多的 `Critical` 漏洞，而且還看到其中許多在它們所來自的依賴項的新版本中得到了修復。
這意味著簡單地提升給定依賴項的版本將消除漏洞並使我們的映像更安全。

當然，這並不總是那麼簡單。
有時，依賴項的新版本可能包含需要更改源代碼的破壞性 API 更改，它可能包含導致我們與依賴項交互方式中的錯誤的行為更改，或者它可能引入我們想要避免的錯誤，直到修復。

另一件值得提及的事情是，這種類型的掃描只能檢測 _已知_ 漏洞。
也就是說，由安全研究人員發現並已分配 CVE 的漏洞。
可能仍然存在未知的漏洞，只是潛伏在您的代碼/依賴項中（Log4Shell 自 2013 年以來一直存在，直到 2021 年才被發現）。

總之，映像掃描不是萬能的。
如果映像掃描器告訴您映像中有 0 個漏洞，這並不意味著您是 100% 安全的。

此外，緩解漏洞可能就像提升依賴項的版本（或降級一個）一樣簡單，但有時可能會更棘手，因為該版本提升可能需要更改您的代碼。

## CVE

在掃描器提供的漏洞表中，我們看到以 `CVE-` 開頭的內容：

```text
bash  4.4.18-2ubuntu1.2  deb  CVE-2022-3715  Medium
```

[**CVE**](https://cve.mitre.org/) 代表 **C**ommon **V**ulnerability and **E**xposures。

這是一個允許我們跟踪漏洞並能夠輕鬆搜索它們的系統。

每次發現新漏洞時，它都會由 [CNA](https://www.cve.org/ProgramOrganization/CNAs)（CVE 編號機構）分配一個 CVE，並與包含該漏洞的所有組件相關聯。

完成此操作後，此資訊會傳播到漏洞數據庫，並可由映像掃描器利用來警告我們容器中存在的 CVE/漏洞。

## 總結

現在我們知道為什麼映像掃描很重要以及它如何幫助我們更安全。
在 [Day 15](day15.md) 中，我們將更深入地了解映像掃描器的工作原理，查看 SBOM 和漏洞數據庫等內容。
