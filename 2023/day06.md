## 動手實作：構建一個弱應用程式

沒有人真的會去構建一個弱或易受攻擊的應用程式...對吧？

不，這是正確的答案，沒有人應該或會去構建一個弱應用程式，也沒有人打算使用帶來自己漏洞的套件或其他開源軟體。

在這個 DevSecOps 的最後介紹部分，我想嘗試構建並提高對一些可能被忽略的錯誤配置和弱點的認識。然後在接下來的 84 天甚至更早，我們將聽到安全領域的一些主題專家關於如何防止壞事和弱應用程式被創建。

### 構建我們的第一個弱應用程式

<span style="color:red">**重要訊息：此練習旨在突出應用程式中的不良和弱點，請在家嘗試，但請注意這是不良實踐**</span>

在這個階段，我不會詳細介紹我的軟體開發環境。我通常會在啟用 WSL2 的 Windows 上使用 VScode。然後我們可能會使用 Vagrant 來配置專用的計算實例到 VirtualBox，所有這些我在 #90DaysOfDevOps 的 2022 部分中都有涵蓋，主要在 Linux 部分。

### 不良編碼實踐或編碼不良實踐

將代碼複製貼上到 GitHub 非常容易！

有多少人會端到端檢查他們在代碼中包含的套件？

我們還必須考慮：

- 我們是否信任用戶/維護者
- 不在我們的代碼上驗證輸入
- 硬編碼密鑰 vs 環境變數或密鑰管理
- 未經驗證就信任代碼
- 將您的密鑰添加到公共儲存庫（有多少人這樣做過？）

現在回到整體主題，DevSecOps，我們所做或努力實現的一切都是應用程式或軟體的更快迭代，但這意味著我們可以更快地引入缺陷和風險。

我們還可能使用代碼部署我們的基礎設施，另一個風險是在這裡包含不良代碼，讓不良行為者通過缺陷進入。

部署還將包括應用程式配置管理，這是另一個可能的缺陷層級。

然而！更快的迭代可以並且確實意味著更快的修復。

### OWASP - 開放 Web 應用程式安全專案

*"[OWASP](https://owasp.org/) 是一個非營利基金會，致力於改善軟體的安全性。通過社群主導的開源軟體專案、全球數百個本地分會、數萬名成員以及領先的教育和培訓會議，OWASP 基金會是開發人員和技術人員保護 Web 的來源。"*

如果我們查看他們最新的數據集和他們的 [前 10 名](https://owasp.org/www-project-top-ten/)，我們可以看到以下導致事情變壞和錯誤的大項目。

1. 破壞的訪問控制
2. 加密失敗
3. 注入（2020 年 #1）
4. 不安全設計（2021 年新增）
5. 安全錯誤配置
6. 易受攻擊和過時的組件（2020 年 #9）
7. 識別和身份驗證失敗（2020 年 #2）
8. 軟體和數據完整性失敗（2021 年新增）
9. 安全日誌記錄和監控失敗（2020 年 #10）
10. 伺服器端請求偽造（SSRF）

### 回到應用程式

<span style="color:red">**上面的警告仍然有效，我將把它部署到本地 VirtualBox VM 如果您決定將其部署到雲實例，請首先小心，其次知道如何將您的雲提供商鎖定到您自己的遠程 IP！**</span>

好的，我認為警告已經足夠了，我確信在接下來的幾週中，當我們更深入地討論這個主題時，我們可能會看到更多紅色警告。

我將使用的應用程式將來自 [DevSecOps.org](https://github.com/devsecops/bootcamp/blob/master/Week-2/README.md) 這是他們幾年前的一個訓練營，但仍然允許我們展示一個壞應用程式是什麼樣子。

能夠看到一個壞的或弱的應用程式意味著我們可以開始理解如何保護它。

再次，我將在本地機器上使用 VirtualBox，我將使用以下 vagrantfile（這裡連結到 vagrant 介紹）

第一個警報是這個 vagrant box 是在 2 年多前創建的！

```
Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.vm.provider :virtualbox do |v|
   v.memory  = 8096
   v.cpus    = 4
end
end
```
如果導航到此資料夾，您可以使用 `vagrant up` 在您的環境中啟動您的 centos7 機器。

![](images/day06-1.png)


然後我們需要訪問我們的機器，您可以使用 `vagrant ssh` 來完成此操作

然後我們將安裝 MariaDB 作為本地數據庫在我們的應用程式中使用。

`sudo yum -y install mariadb mariadb-server mariadb-devel`

使用以下命令啟動服務

`sudo systemctl start mariadb.service`

我們必須安裝一些依賴項，這也是我必須更改訓練營建議的地方，因為 NodeJS 在當前儲存庫中不可用。

`sudo yum -y install links`
`sudo yum install --assumeyes epel-release`
`sudo yum install --assumeyes nodejs`

您可以使用 `node -v` 和 `npm -v` 確認您已安裝 node（npm 應該作為依賴項安裝）

對於這個應用程式，我們將使用 ruby，這是我們尚未完全涵蓋的語言，我們不會真正深入了解它，我會嘗試找到一些好的資源並在下面添加。

使用以下命令安裝

`curl -L https://get.rvm.io | bash -s stable`

使用上面的命令，您可能會被要求添加密鑰，請按照這些步驟操作。

為了讓我們使用 rvm，我們需要執行以下操作：

`source /home/vagrant/.rvm/scripts/rvm`

最後，使用以下命令安裝它

`rvm install ruby-2.7`

這個冗長過程的原因基本上是因為我們正在使用的 centos7 box 很舊，舊的 ruby 在正常儲存庫等中提供。

使用以下命令檢查安裝和版本

`ruby --version`

我們接下來需要 Ruby on Rails 框架，可以使用以下命令獲取。

`gem install rails`

接下來，我們需要 git，我們可以使用以下命令獲取

`sudo yum install git`

僅供記錄，不確定是否需要，我的機器上也安裝了 Redis，因為我正在做其他事情，但它實際上可能仍然需要，所以這些是步驟。

```
sudo yum install epel-release
sudo yum install redis
```

上面的可能與 turbo streams 有關，但我沒有時間了解更多關於 ruby on rails 的內容。

現在讓我們最終創建我們的應用程式（為了記錄，我經歷了很多以確保這些步驟在我的系統上工作，所以我向您發送所有運氣）

使用以下命令創建應用程式，隨您喜歡命名

`rails new myapp --skip-turbolinks --skip-spring --skip-test-unit -d mysql `

接下來，我們將創建數據庫和架構：

```
cd myapp
bundle exec rake db:create
bundle exec rake db:migrate
```

然後我們可以使用 `bundle exec rails server -b 0.0.0.0` 運行我們的應用程式

![](images/day06-2.png)

然後打開瀏覽器訪問該 box，我必須將我的 VirtualBox VM 網路更改為橋接而不是 NAT，這樣我才能導航到它，而不是使用 vagrant ssh。

![](images/day06-3.png)

現在我們需要 **scaffold** 一個基本模型

Scaffold 是一組自動生成的文件，形成 Rails 專案的基本結構。

我們使用以下命令執行此操作：

```
bundle exec rails generate scaffold Bootcamp name:string description:text dates:string
bundle exec rake db:migrate
```

![](images/day06-4.png)

在 config/routes.rb 中添加默認路由

`root bootcamps#index`

![](images/day06-5.png)

現在編輯 app/views/bootcamps/show.html.erb 並使描述欄位成為原始欄位。添加以下內容。

```
<p>
  <strong>Description:</strong>
  <%=raw @bootcamp.description %>
</p>
```
現在為什麼這一切都相關是因為在描述欄位中使用 raw 意味著這個欄位現在成為潛在的 XSS 目標。或跨站腳本。

這可以通過影片更好地解釋 [What is Cross-Site Scripting?](https://youtu.be/DxsmEXicXEE)

訓練營的其餘部分繼續添加搜索功能，這也增加了 XSS 攻擊的能力，這是您可以嘗試在 [易受攻擊的應用程式](https://www.softwaretestinghelp.com/cross-site-scripting-xss-attack-test/) 上進行演示攻擊的另一個很好的例子。

### 創建搜索功能

在 app/controllers/bootcamps_controller.rb 中，我們將在 index 方法中添加以下邏輯：

```
def index
  @bootcamps = Bootcamp.all
  if params[:search].to_s != ''
    @bootcamps = Bootcamp.where("name LIKE '%#{params[:search]}%'")
  else
    @bootcamps = Bootcamp.all
  end
end
```

在 app/views/bootcamps/index.html.erb 中，我們將添加搜索欄位：

```
<h1>Search</h1>
<%= form_tag(bootcamps_path, method: "get", id: "search-form") do %>
  <%= text_field_tag :search, params[:search], placeholder: "Search Bootcamps" %>
  <%= submit_tag "Search Bootcamps"%>
<% end %>

<h1>Listing Bootcamps</h1>
```

非常感謝 [DevSecOps.org](https://www.devsecops.org/) 這是我找到舊但很好的演練的地方，上面有一些調整，那裡還有更多資訊可以找到。

有了這個比預期更長的演練，我將移交給下一節和作者，以突出如何不做這個，以及如何確保我們不會將不良代碼或漏洞發布到野外。

## 資源

- [devsecops.org](https://www.devsecops.org/)

- [TechWorld with Nana - What is DevSecOps? DevSecOps explained in 8 Mins](https://www.youtube.com/watch?v=nrhxNNH5lt0&list=PLsKoqAvws1pvg7qL7u28_OWfXwqkI3dQ1&index=1&t=19s)

- [What is DevSecOps?](https://www.youtube.com/watch?v=J73MELGF6u0&list=PLsKoqAvws1pvg7qL7u28_OWfXwqkI3dQ1&index=2&t=1s)

- [freeCodeCamp.org - Web App Vulnerabilities - DevSecOps Course for Beginners](https://www.youtube.com/watch?v=F5KJVuii0Yw&list=PLsKoqAvws1pvg7qL7u28_OWfXwqkI3dQ1&index=3&t=67s)

- [The Importance of DevSecOps and 5 Steps to Doing it Properly (DevSecOps EXPLAINED)](https://www.youtube.com/watch?v=KaoPQLyWq_g&list=PLsKoqAvws1pvg7qL7u28_OWfXwqkI3dQ1&index=4&t=13s)

- [Continuous Delivery - What is DevSecOps?](https://www.youtube.com/watch?v=NdvMUcWNlFw&list=PLsKoqAvws1pvg7qL7u28_OWfXwqkI3dQ1&index=5&t=6s)

- [Cloud Advocate - What is DevSecOps?](https://www.youtube.com/watch?v=a2y4Oj5wrZg&list=PLsKoqAvws1pvg7qL7u28_OWfXwqkI3dQ1&index=6)

- [Cloud Advocate - DevSecOps Pipeline CI Process - Real world example!](https://www.youtube.com/watch?v=ipe08lFQZU8&list=PLsKoqAvws1pvg7qL7u28_OWfXwqkI3dQ1&index=7&t=204s)

在 [Day 7](day07.md) 見，我們將開始關於安全編碼的新部分。
