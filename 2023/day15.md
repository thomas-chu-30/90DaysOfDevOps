# 容器映像掃描進階

## SBOM

**SBOM** 代表 **S**oftware **B**ill **O**f **M**aterials（軟體物料清單）。

它是構成軟體應用程式或系統的所有組件的列表。
它包括有關用於構建軟體的各種第三方庫、框架和其他開源或專有組件的資訊。
SBOM 還可以包括有關這些組件的版本、其許可證資訊以及任何已知漏洞或安全問題的詳細資訊。

SBOM 的目標是列出這些組件，為軟體用戶提供對軟體產品中包含的內容的可見性，並允許他們避免可能因安全或法律原因而有害的組件。

在過去幾年，在幾次大型供應鏈攻擊之後，SBOM 的使用變得更加普遍 [今年](https://www.immuniweb.com/blog/5-biggest-supply-chain-attacks-in-2022-so-far.html) 和 [去年](https://cyolo.io/blog/top-5-supply-chain-attacks-of-2021/)。

在容器映像的上下文中，容器映像的 SBOM 將包含：

- 容器中安裝的 Linux 套件和庫
- 為容器中運行的應用程式安裝的特定語言套件（例如 Python 套件、Go 套件等）

有工具可以幫助您從容器映像中提取 SBOM。

其中一個工具是 [syft](https://github.com/anchore/syft)。

例如，我們可以使用 syft 為 `ubuntu:latest` 容器映像生成 SBOM：

```console
$ syft ubuntu
 ✔ Parsed image
 ✔ Cataloged packages      [101 packages]
NAME                 VERSION                                  TYPE
adduser              3.118ubuntu5                             deb
apt                  2.4.8                                    deb
base-files           12ubuntu4.2                              deb
base-passwd          3.5.52build1                             deb
bash                 5.1-6ubuntu1                             deb
bsdutils             1:2.37.2-4ubuntu3                        deb
coreutils            8.32-4.1ubuntu1                          deb
dash                 0.5.11+git20210903+057cd650a4ed-3build1  deb
debconf              1.5.79ubuntu1                            deb
debianutils          5.5-1ubuntu2                             deb
diffutils            1:3.8-0ubuntu2                           deb
dpkg                 1.21.1ubuntu2.1                          deb
e2fsprogs            1.46.5-2ubuntu1.1                        deb
findutils            4.8.0-1ubuntu3                           deb
gcc-12-base          12.1.0-2ubuntu1~22.04                    deb
gpgv                 2.2.27-3ubuntu2.1                        deb
grep                 3.7-1build1                              deb
gzip                 1.10-4ubuntu4.1                          deb
hostname             3.23ubuntu2                              deb
init-system-helpers  1.62                                     deb
libacl1              2.3.1-1                                  deb
libapt-pkg6.0        2.4.8                                    deb
libattr1             1:2.5.1-1build1                          deb
libaudit-common      1:3.0.7-1build1                          deb
libaudit1            1:3.0.7-1build1                          deb
libblkid1            2.37.2-4ubuntu3                          deb
libbz2-1.0           1.0.8-5build1                             deb
libc-bin             2.35-0ubuntu3.1                          deb
libc6                2.35-0ubuntu3.1                          deb
libcap-ng0           0.7.9-2.2build3                          deb
libcap2              1:2.44-1build3                           deb
libcom-err2          1.46.5-2ubuntu1.1                        deb
libcrypt1            1:4.4.27-1                               deb
libdb5.3             5.3.28+dfsg1-0.8ubuntu3                  deb
libdebconfclient0    0.261ubuntu1                             deb
libext2fs2           1.46.5-2ubuntu1.1                        deb
libffi8              3.4.2-4                                  deb
libgcc-s1            12.1.0-2ubuntu1~22.04                    deb
libgcrypt20          1.9.4-3ubuntu3                           deb
libgmp10             2:6.2.1+dfsg-3ubuntu1                    deb
libgnutls30          3.7.3-4ubuntu1.1                         deb
libgpg-error0        1.43-3                                   deb
libgssapi-krb5-2     1.19.2-2                                 deb
libhogweed6          3.7.3-1build2                            deb
libidn2-0            2.3.2-2build1                            deb
libk5crypto3         1.19.2-2                                 deb
libkeyutils1         1.6.1-2ubuntu3                           deb
libkrb5-3            1.19.2-2                                 deb
libkrb5support0      1.19.2-2                                 deb
liblz4-1             1.9.3-2build2                            deb
liblzma5             5.2.5-2ubuntu1                           deb
libmount1            2.37.2-4ubuntu3                          deb
libncurses6          6.3-2                                    deb
libncursesw6         6.3-2                                    deb
libnettle8           3.7.3-1build2                            deb
libnsl2              1.3.0-2build2                            deb
libp11-kit0          0.24.0-6build1                           deb
libpam-modules       1.4.0-11ubuntu2                          deb
libpam-modules-bin   1.4.0-11ubuntu2                          deb
libpam-runtime       1.4.0-11ubuntu2                          deb
libpam0g             1.4.0-11ubuntu2                          deb
libpcre2-8-0         10.39-3ubuntu0.1                         deb
libpcre3             2:8.39-13ubuntu0.22.04.1                 deb
libprocps8           2:3.3.17-6ubuntu2                        deb
libseccomp2          2.5.3-2ubuntu2                           deb
libselinux1          3.3-1build2                              deb
libsemanage-common   3.3-1build2                              deb
libsemanage2         3.3-1build2                              deb
libsepol2            3.3-1build1                              deb
libsmartcols1        2.37.2-4ubuntu3                          deb
libss2               1.46.5-2ubuntu1.1                        deb
libssl3              3.0.2-0ubuntu1.7                         deb
libstdc++6           12.1.0-2ubuntu1~22.04                    deb
libsystemd0          249.11-0ubuntu3.6                        deb
libtasn1-6           4.18.0-4build1                           deb
libtinfo6            6.3-2                                    deb
libtirpc-common      1.3.2-2ubuntu0.1                         deb
libtirpc3            1.3.2-2ubuntu0.1                          deb
libudev1             249.11-0ubuntu3.6                        deb
libunistring2        1.0-1                                    deb
libuuid1             2.37.2-4ubuntu3                          deb
libxxhash0           0.8.1-1                                  deb
libzstd1             1.4.8+dfsg-3build1                       deb
login                1:4.8.1-2ubuntu2                          deb
logsave              1.46.5-2ubuntu1.1                        deb
lsb-base             11.1.0ubuntu4                            deb
mawk                 1.3.4.20200120-3                         deb
mount                2.37.2-4ubuntu3                          deb
ncurses-base         6.3-2                                    deb
ncurses-bin          6.3-2                                    deb
passwd               1:4.8.1-2ubuntu2                         deb
perl-base            5.34.0-3ubuntu1.1                        deb
procps               2:3.3.17-6ubuntu2                        deb
sed                  4.8-1ubuntu2                             deb
sensible-utils       0.0.17                                   deb
sysvinit-utils       3.01-1ubuntu1                            deb
tar                  1.34+dfsg-1build3                        deb
ubuntu-keyring       2021.03.26                               deb
usrmerge             25ubuntu2                                deb
util-linux           2.37.2-4ubuntu3                          deb
zlib1g               1:1.2.11.dfsg-2ubuntu9.2                 deb
```

我們看到 SBOM 不僅包含容器映像內安裝的套件和庫，
還列出了它們的類型和版本。
我們現在可以使用此列表與漏洞數據庫進行交叉引用，以查看容器內是否有任何漏洞。

那麼什麼是 **漏洞數據庫**？

## 漏洞數據庫

漏洞數據庫是關於軟體、硬體和其他系統中已知漏洞的資訊集合。
它通常包括有關漏洞性質的詳細資訊，例如漏洞類型、漏洞的嚴重性以及漏洞的潛在影響。
漏洞數據庫還可能包括有關如何利用漏洞的資訊，以及有關漏洞的任何可用補丁或修復的資訊。

一些漏洞數據庫是 [vuldb.com](https://vuldb.com/)、[NIST](https://nvd.nist.gov/vuln)、[cvedetails.com](https://www.cvedetails.com/) 和 [Snyk Vulnerability Database](https://security.snyk.io/)。

它們提供 API 或可以下載的原始數據，並將我們 SBOM 中的套件與漏洞資訊進行交叉引用。
這樣，我們可以發現我們的任何套件是否有我們需要關心的漏洞。

通常我們還可以找到有關引入此漏洞的庫版本的資訊，以及它是否在新版本中已修復。
使用此資訊，我們可以決定是否更新/降級我們的依賴項以緩解漏洞。
正如我們在 [Day 14](./day14.md) 中已經確定的那樣，更新依賴項並不總是簡單的，因為有時此更新會帶來行為或 API 更改。

關於漏洞的另一個重要資訊是它的 **CVSS 分數**。

## CVSS

**CVSS** 代表 **C**ommon **V**ulnerability **S**coring **S**ystem（通用漏洞評分系統）。

它提供了一種捕獲漏洞主要特徵並產生反映其嚴重性的數值分數的方法。
然後可以將數值分數轉換為定性表示（例如低、中、高和關鍵），以幫助組織正確評估和優先排序其漏洞管理流程。

基本上，一個漏洞可能比另一個漏洞更嚴重。
我們需要一個系統，可以根據漏洞的利用難易程度和可能造成的損害程度客觀地對漏洞進行排名。

這就是 CVSS 的用武之地。

CVSS v3 定義了 8 個標準，基於這些標準計算 CVSS 分數。
這些標準是：

### 攻擊向量

反映可以利用漏洞的上下文。

可能的值：**Network(N)**、**Adjacent(A)**、**Local(L)**、**Physical(P)**

### 攻擊複雜性

描述攻擊者控制之外必須存在的條件才能利用漏洞。

可能的值：**Low(L)**、**High(H)**

### 所需權限

描述攻擊者在成功利用漏洞之前必須擁有的權限級別。

可能的值：**None(N)**、**Low(L)**、**High(H)**

### 用戶交互

要求用戶（攻擊者除外）參與成功破壞易受攻擊的組件。

可能的值：**None(N)**、**Required(R)**

### 範圍

一個軟體組件中的漏洞影響超出其手段或權限的資源的能力。

可能的值：**Unchanged(U)**、**Changed(C)**

### 機密性

由於成功利用漏洞而對軟體組件管理的資訊資源的機密性的影響。

可能的值：**None(N)**、**Low(L)**、**High(H)**

### 完整性

對成功利用漏洞的完整性的影響。

可能的值：**None(N)**、**Low(L)**、**High(H)**

### 可用性

由於成功利用漏洞而對受影響組件的可用性造成的影響。

可能的值：**None(N)**、**Low(L)**、**High(H)**

這 8 個向量的組合決定了 CVSS 分數。
它在 0 到 10 之間。
0 是最低可能的，10 是最高（最關鍵）。

[這裡](https://www.first.org/cvss/calculator/3.0) 您可以找到 CVSS 計算器，您可以在其中計算每個漏洞的分數。

## 資源

<https://www.nist.gov/itl/executive-order-improving-nations-cybersecurity>

<https://www.aquasec.com/cloud-native-academy/supply-chain-security/sbom/>


在 [Day 16](day16.md) 中，我們將深入了解「Fuzzing」或模糊測試。
