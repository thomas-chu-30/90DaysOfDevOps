---
title: '#90DaysOfDevOps - Microsoft Azure Security Models - Day 30'
published: false
description: 90DaysOfDevOps - Microsoft Azure Security Models
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049039
---

## Microsoft Azure 安全模型

繼 Microsoft Azure 概述之後，我們將從 Azure 安全開始，看看這如何在我們的日常工作中提供幫助。對於大多數部分，我發現內置角色已經足夠，但知道我們可以創建和使用身份驗證和配置的許多不同領域。我發現 Microsoft Azure 與其他公共雲相比，其 Active Directory 背景相當先進。

這是 Microsoft Azure 似乎與其他公共雲提供商不同的領域之一，在 Azure 中總是 Azure AD。

### 目錄服務

- Azure Active Directory 託管 Microsoft Azure 和其他 Microsoft 雲服務使用的安全主體。
- 身份驗證通過 SAML、WS-Federation、OpenID Connect 和 OAuth2 等協議完成。
- 查詢通過稱為 Microsoft Graph API 的 REST API 完成。
- 租戶有一個 tenant.onmicrosoft.com 默認名稱，但也可以有自定義域名。
- 訂閱與 Azure Active Directory 租戶關聯。

如果我們考慮 AWS 來比較，等效產品將是 AWS IAM（身份和訪問管理），儘管仍然非常不同

Azure AD Connect 提供從 AD 複製帳戶到 Azure AD 的能力。這也可以包括組，有時還包括對象。這可以是細粒度的和過濾的。支持多個林和域。

可以在 Microsoft Azure Active Directory (AD) 中創建雲帳戶，但大多數組織已經在他們自己的本地 Active Directory 中為用戶記帳。

Azure AD Connect 還允許你不僅看到 Windows AD 服務器，還可以看到其他 Azure AD、Google 等。這還提供了與外部人員和組織協作的能力，這稱為 Azure B2B。

Active Directory 域服務和 Microsoft Azure Active Directory 之間的身份驗證選項可以通過密碼哈希進行身份同步。

![](Images/Day30_Cloud1.png)

傳遞密碼哈希是可選的，如果不使用，則需要直通身份驗證。

下面鏈接了一個視頻，詳細介紹了直通身份驗證。

[User sign-in with Azure Active Directory Pass-through Authentication](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-pta)

![](Images/Day30_Cloud2.png)

### 聯合

公平地說，如果你使用 Microsoft 365、Microsoft Dynamics 和本地 Active Directory，那麼理解和集成到 Azure AD 進行聯合是相當容易的。但是，你可能在 Microsoft 生態系統之外使用其他服務。

Azure AD 可以充當這些其他非 Microsoft 應用程序和其他目錄服務的聯合代理。

這將在 Azure 門戶中顯示為企業應用程序，其中有大量選項。

![](Images/Day30_Cloud3.png)

如果你在企業應用程序頁面上向下滾動，你將看到一長串特色應用程序。

![](Images/Day30_Cloud4.png)

此選項還允許"自帶"集成，你正在開發的應用程序或非畫廊應用程序。

我之前沒有研究過這個，但我可以看到，與其他雲提供商和功能相比，這是一個相當大的功能集。

### 基於角色的訪問控制

我們已經在 [Day 29](day29.md) 上涵蓋了我們將在這裡涵蓋的範圍，我們可以根據這些領域之一設置基於角色的訪問控制。

- 訂閱
- 管理組
- 資源組
- 資源

角色可以分為三種，Microsoft Azure 中有許多內置角色。這三個是：

- Owner
- Contributor
- Reader

Owner 和 Contributor 在其範圍邊界上非常相似，但是 owner 可以更改權限。

其他角色特定於某些類型的 Azure 資源以及自定義角色。

我們應該專注於將權限分配給組而不是用戶。

權限是繼承的。

如果我們返回並查看我們創建的"90DaysOfDevOps"資源組並檢查其中的訪問控制 (IAM)，你可以看到我們有一個貢獻者列表和一個客戶用戶訪問管理員，我們確實有一個所有者列表（但我無法顯示這個）

![](Images/Day30_Cloud5.png)

我們還可以檢查我們在這裡分配的角色，如果它們是 BuiltInRoles 以及它們屬於哪個類別。

![](Images/Day30_Cloud6.png)

如果我們想檢查此資源組的帳戶並確保我們希望擁有該訪問權限的帳戶具有正確的權限，或者我們想檢查用戶是否有太多訪問權限，我們還可以使用檢查訪問選項卡。

![](Images/Day30_Cloud7.png)

### Microsoft Defender for Cloud

- Microsoft Defender for Cloud（以前稱為 Azure Security Center）提供對整個 Azure 環境安全性的見解。

- 單一儀表板，用於查看所有 Azure 和非 Azure 資源（通過 Azure Arc）的整體安全運行狀況和安全強化指導。

- 免費層包括持續評估和安全建議。

- 受保護資源類型的付費計劃（例如服務器、AppService、SQL、存儲、容器、KeyVault）。

我已經切換到另一個訂閱來查看 Azure Security Center，你可以看到這裡基於我很少的資源，我在一個地方有一些建議。

![](Images/Day30_Cloud8.png)

### Azure Policy

- Azure Policy 是一項 Azure 原生服務，有助於強制執行組織標準並大規模評估合規性。

- 集成到 Microsoft Defender for Cloud。Azure Policy 審計不合規資源並應用補救措施。

- 通常用於管理資源一致性、監管合規性、安全性、成本和管理標準。

- 使用 JSON 格式存儲評估邏輯並確定資源是否合規，以及對不合規採取的任何操作（例如 Audit、AuditIfNotExists、Deny、Modify、DeployIfNotExists）。

- 免費使用。例外是 Azure Arc 連接的資源，按服務器/月為 Azure Policy Guest Configuration 使用付費。

### 親自動手

我已經出去購買了 www.90DaysOfDevOps.com，我想將此域名添加到我的 Azure Active Directory 門戶，[使用 Azure Active Directory 門戶添加自定義域名](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/add-custom-domain)

![](Images/Day30_Cloud9.png)

現在，我們可以在新的 Active Directory 域上創建新用戶。

![](Images/Day30_Cloud10.png)

現在我們想為所有新的 90DaysOfDevOps 用戶在一個組中創建一個組。我們可以按照下面創建一個組，注意我使用的是"Dynamic User"，這意味著 Azure AD 將查詢用戶帳戶並動態添加它們，而不是分配，這是手動將用戶添加到組的地方。

![](Images/Day30_Cloud11.png)

在創建查詢時有很多選項，我計劃簡單地查找主體名稱並確保名稱包含 @90DaysOfDevOps.com。

![](Images/Day30_Cloud12.png)

現在因為我們已經為 michael.cade@90DaysOfDevOps.com 創建了用戶帳戶，我們可以驗證規則正在工作。為了比較，我還添加了另一個我關聯到另一個域的帳戶，你可以看到由於此規則，我們的用戶不會落在這個組中。

![](Images/Day30_Cloud13.png)

我已經添加了一個新的 user1@90DaysOfDevOps.com，如果我們去檢查組，我們可以看到我們的成員。

![](Images/Day30_Cloud14.png)

如果我們有這個要求 x100，那麼我們不想在控制台中完成所有這些，我們想要利用批量選項來創建、邀請和刪除用戶，或者你想要研究 PowerShell 來實現這種自動化方法以擴展。

現在我們可以去資源組並指定在 90DaysOfDevOps 資源組上，我們希望所有者是我們剛剛創建的組。

![](Images/Day30_Cloud15.png)

我們同樣可以在這裡拒絕分配對資源組的訪問。

現在如果我們使用新用戶帳戶登錄 Azure 門戶，你可以看到我們只能訪問 90DaysOfDevOps 資源組，而不能訪問之前圖片中看到的其他資源組，因為我們沒有訪問權限。

![](Images/Day30_Cloud16.png)

如果這是可以訪問 Azure 門戶內資源的用戶，上面是很好的，但並非每個用戶都需要知道門戶，但要檢查訪問，我們可以使用 [Apps Portal](https://myapps.microsoft.com/) 這是我們測試的單一登錄門戶。

![](Images/Day30_Cloud17.png)

你可以使用你的品牌自定義此門戶，這可能是我們稍後會回來的內容。

## 資源

- [Hybrid Cloud and MultiCloud](https://www.youtube.com/watch?v=qkj5W98Xdvw)
- [Microsoft Azure Fundamentals](https://www.youtube.com/watch?v=NKEFWyqJ5XA&list=WL&index=130&t=12s)
- [Google Cloud Digital Leader Certification Course](https://www.youtube.com/watch?v=UGRDM86MBIQ&list=WL&index=131&t=10s)

我們[Day 31](day31.md)見
