---
title: 使用 Microsoft 身分識別平台存取安全資源，而不需要使用者互動 |Azure
description: 使用 OAuth 2.0 驗證通訊協定的 Microsoft 身分識別平台實作，以建置 web 應用程式。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 485f79f79c52067e89fa0a606e76a533c312fb84
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545104"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Microsoft 身分識別平台和 OAuth 2.0 用戶端認證流程

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

您可以使用 RFC 6749 中指定的 [OAuth 2.0 用戶端認證授與](https://tools.ietf.org/html/rfc6749#section-4.4) (有時稱為「雙方 OAuth」，透過使用應用程式識別碼來存取 Web 主控資源。 這類型的授與通常用於必須在背景中執行 (不需與使用者直接互動) 的伺服器對伺服器互動。 這些類型的應用程式通常稱為*精靈*或*服務帳戶*。

OAuth 2.0 用戶端認證授與流程可允許 Web 服務 (機密用戶端) 在呼叫另一個 Web 服務時，使用它自己的認證來進行驗證，而不是模擬使用者。 在此案例中，用戶端通常是中介層 Web 服務、精靈服務或網站。 對於較高層級的保證，Microsoft 身分識別平台也可讓呼叫服務使用憑證 (而非共用密碼) 做為認證。

> [!NOTE]
> Microsoft 身分識別平台端點不支援所有的 Azure AD 案例和功能。 若要判斷您是否應該使用 Microsoft 身分識別平台的端點，請參閱[Microsoft 身分識別平台限制](active-directory-v2-limitations.md)。

在較為典型的「三腳 OAuth」中，用戶端應用程式會獲得代表特定使用者存取資源的權限。 此權限是由使用者委派給應用程式 (通常是在[同意](v2-permissions-and-consent.md)過程中)。 不過，在用戶端認證 (*二方 OAuth*) 流程中，權限會直接授與應用程式本身。 當應用程式向資源出示權杖時，資源會強制要求應用程式本身具備執行動作的授權，而不是使用者。

## <a name="protocol-diagram"></a>通訊協定圖表

整個用戶端認證流程看起來類似下圖。 我們會在本文稍後說明每個步驟。

![用戶端認證流程](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>取得直接授權

應用程式通常會收到直接授權，而可透過下列兩種方式之一存取資源：

* [在資源中透過存取控制清單 (ACL)](#access-control-lists)
* [在 Azure AD 中透過應用程式權限指派](#application-permissions)

這兩種方法是 Azure AD 中最常見的方法，並建議用於執行用戶端認證流程的用戶端和資源。 資源也可以選擇以其他方法授權其用戶端。 每個資源伺服器都可以選擇最適合其應用程式的方法。

### <a name="access-control-lists"></a>存取控制清單

資源提供者可能會根據它所知的應用程式 (用戶端) 識別碼清單，強制執行授權檢查並授與特定層級的存取權。 當資源從 Microsoft 身分識別平台端點收到權杖時，它可以將權杖解碼，並擷取用戶端應用程式識別碼`appid`和`iss`宣告。 然後，它會將應用程式與它所保有的存取控制清單 (ACL) 進行比較。 各資源之間的 ACL 細微性及方法可能有極大的差異。

常見的使用案例是使用 ACL 來針對 Web 應用程式或 Web API 執行測試。 Web API 可能只會將完整權限中的一部分授與特定用戶端。 若要在 API 上執行端對端測試，請建立測試用戶端取得從 Microsoft 身分識別平台端點的權杖，然後將它們傳送到 API。 接著，API 會檢查 ACL 是否有測試用戶端的應用程式識別碼，以便提供 API 完整功能的完整存取權。 如果您使用這類 ACL，請務必不要只驗證呼叫者的 `appid` 值，還要驗證權杖的 `iss` 值是否可信任。

對於需要存取具有個人 Microsoft 帳戶之取用者使用者所擁有資料的精靈和服務帳戶來說，這種授權相當常見。 對於組織所擁有的資料，建議您透過應用程式權限取得必要的授權。

### <a name="application-permissions"></a>應用程式權限

您可以不使用 ACL，而是使用 API 來公開一組應用程式權限。 應用程式權限是由組織的系統管理員授與應用程式，並且只能用來存取該組織及其員工所擁有的資料。 例如，Microsoft Graph 會公開數個可執行下列操作的應用程式權限︰

* 讀取所有信箱中的郵件
* 讀取和寫入所有信箱中的郵件
* 以任何使用者身分傳送郵件
* 讀取目錄資料

如需有關應用程式權限的詳細資訊，請參閱 [Microsoft Graph](https://developer.microsoft.com/graph)。

若要在您的應用程式中使用應用程式權限，請遵循後續小節中討論的步驟。

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>在應用程式註冊入口網站中要求權限

1. 註冊並建立透過新的應用程式[應用程式註冊 （預覽） 體驗](quickstart-register-app.md)。
2. 請移至您的應用程式中的應用程式註冊 （預覽） 體驗。 瀏覽至**憑證與祕密**區段，然後新增**新的用戶端祕密**，因為您必須至少一個要求權杖的用戶端祕密。
3. 找出 [API 權限] 區段，然後新增您應用程式所需的**應用程式權限**。
4. [儲存] 應用程式註冊。

#### <a name="recommended-sign-the-user-into-your-app"></a>建議使用：將使用者登入您的應用程式

通常，當您建置使用應用程式權限的應用程式時，應用程式會需要一個可供系統管理員核准應用程式權限的頁面或檢視。 此頁面可以是應用程式登入流程的一部分、應用程式設定的一部分，或是專用的「連接」流程。 在許多情況下，應用程式只在使用者利用工作或學校 Microsoft 帳戶登入之後顯示此「連接」檢視是很合理的。

如果您將使用者登入您的應用程式時，您可以識別使用者所屬的組織到之前要求使用者核准應用程式權限。 雖然這並非絕對必要，但這麼做可協助您為使用者建立更直覺式的體驗。 若要將使用者登入，請依照我們[Microsoft 身分識別平台通訊協定教學課程](active-directory-v2-protocols.md)。

#### <a name="request-the-permissions-from-a-directory-admin"></a>向目錄管理員要求權限

當您準備好向組織的系統管理員要求權限時，您可以將使用者重新導向至 Microsoft 身分識別平台*系統管理員同意端點*。

> [!TIP]
> 嘗試在 Postman 中執行這項要求！ （為了獲得最佳結果，使用您自己的應用程式識別碼-教學課程的應用程式將不會要求很有用的權限）。[![在 Postman 中執行](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser.
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| 參數 | 條件 | 說明 |
| --- | --- | --- |
| `tenant` | 必要項 | 您想要要求權限的目錄租用戶。 這可以採用 GUID 或易記名稱格式。 如果不知道用户属于哪个租户并想让他们登录到任一租户，请使用 `common`。 |
| `client_id` | 必要項 | **應用程式 （用戶端） 識別碼**可[Azure 入口網站-應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)指派給您的應用程式的體驗。 |
| `redirect_uri` | 必要項 | 您想要傳送回應以供應用程式處理的重新導向 URI。 它必須與您在入口網站中註冊的其中一個重新導向 URI 完全相符，只是它必須是採用 URL 編碼，並且可以有額外的路徑區段。 |
| `state` | 建議 | 要求中包含的值，也會隨權杖回應傳回。 它可以是您所想要內容中的字串。 此狀態用於在驗證要求出現之前，於應用程式中編碼使用者的狀態資訊，例如之前所在的網頁或檢視。 |

此時，Azure AD 會強制只有租用戶系統管理員可以登入完成要求。 系統會請系統管理員核准您在應用程式註冊入口網站中，為您應用程式要求的所有直接應用程式權限。

##### <a name="successful-response"></a>成功的回應

如果系統管理員為您的應用程式核准權限，則成功的回應看起來會像這樣︰

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| 參數 | 說明 |
| --- | --- |
| `tenant` | 將應用程式所要求的權限授與應用程式的目錄租用戶 (採用 GUID 格式)。 |
| `state` | 一個包含在要求中而將一併在權杖回應中傳回的值。 它可以是您所想要內容中的字串。 此狀態用於在驗證要求出現之前，於應用程式中編碼使用者的狀態資訊，例如之前所在的網頁或檢視。 |
| `admin_consent` | 設定為 [True]。 |

##### <a name="error-response"></a>錯誤回應

如果系統管理員沒有為您的應用程式核准權限，則失敗的回應看起來會像這樣︰

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| 參數 | 說明 |
| --- | --- |
| `error` | 您可用來分類錯誤類型並對錯誤做出反應的錯誤碼字串。 |
| `error_description` | 可協助您識別錯誤根本原因的特定錯誤訊息。 |

從應用程式佈建端點收到成功回應後，您的應用程式便已取得它所要求的直接應用程式權限。 現在，您可以針對您想要的資源要求權杖。

## <a name="get-a-token"></a>获取令牌

取得應用程式的必要授權後，請繼續取得 API 的存取權杖。 若要使用的用戶端認證授與取得權杖，請傳送 POST 要求至`/token`Microsoft 身分識別平台端點：

> [!TIP]
> 嘗試在 Postman 中執行這項要求！ （為了獲得最佳結果，使用您自己的應用程式識別碼-教學課程的應用程式將不會要求很有用的權限）。[![在 Postman 中執行](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>第一種情況︰使用共用密碼的存取權杖要求

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| 參數 | 條件 | 說明 |
| --- | --- | --- |
| `tenant` | 必要項 | 應用程式預期要對其執行作業的目錄租用戶 (以 GUID 或網域名稱格式)。 |
| `client_id` | 必要項 | 指派給應用程式的應用程式識別碼。 您可以在用來註冊應用程式的入口網站中找到這項資訊。 |
| `scope` | 必要項 | 在這個要求中針對 `scope` 參數傳遞的值應該是您所要資源的資源識別碼 (應用程式識別碼 URI)，並附加 `.default` 尾碼。 就 Microsoft Graph 範例而言，值為 `https://graph.microsoft.com/.default`。 <br/>這個值會告訴 Microsoft 身分識別平台端點的所有直接應用程式權限已為您的應用程式，端點將應該發出給與您想要使用的資源相關聯的權杖。 若要深入了解有關 `/.default` 範圍，請參閱[同意文件](v2-permissions-and-consent.md#the-default-scope)。 |
| `client_secret` | 必要項 | 您應用程式產生的應用程式註冊入口網站中用戶端祕密。 用戶端密碼必須在傳送之前先進行 URL 編碼。 |
| `grant_type` | 必要項 | 必須設為 `client_credentials`。 |

### <a name="second-case-access-token-request-with-a-certificate"></a>第二種情況︰使用憑證的存取權杖要求

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| 參數 | 條件 | 說明 |
| --- | --- | --- |
| `tenant` | 必要項 | 應用程式預期要對其執行作業的目錄租用戶 (以 GUID 或網域名稱格式)。 |
| `client_id` | 必要項 |指派給應用程式的應用程式 (用戶端) 識別碼。 |
| `scope` | 必要項 | 在這個要求中針對 `scope` 參數傳遞的值應該是您所要資源的資源識別碼 (應用程式識別碼 URI)，並附加 `.default` 尾碼。 就 Microsoft Graph 範例而言，值為 `https://graph.microsoft.com/.default`。 <br/>這個值會通知 Microsoft 身分識別平台端點的所有直接應用程式權限已為您的應用程式，它應該發出的權杖與您想要使用的資源相關聯的項目。 若要深入了解有關 `/.default` 範圍，請參閱[同意文件](v2-permissions-and-consent.md#the-default-scope)。 |
| `client_assertion_type` | 必要項 | 此值必須設定為 `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`。 |
| `client_assertion` | 必要項 | 您必須建立判斷提示 (JSON Web 權杖)，並使用註冊的憑證來簽署，以作為應用程式的認證。 請參閱[憑證認證](active-directory-certificate-credentials.md)，以了解如何註冊您的憑證與判斷提示的格式。|
| `grant_type` | 必要項 | 必須設為 `client_credentials`。 |

請注意，在透過共用密碼要求的情況中，參數幾乎相同，不同之處在於使用下列兩個參數來取代 client_secret 參數：client_assertion_type 和 client_assertion。

### <a name="successful-response"></a>成功的回應

成功的回應看起來會像這樣：

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| 參數 | 說明 |
| --- | --- |
| `access_token` | 所要求的存取權杖。 应用可以使用此令牌验证受保护的资源，例如验证 Web API。 |
| `token_type` | 表示權杖類型值。 唯一的輸入身分識別平台支援是由 Microsoft `bearer`。 |
| `expires_in` | 存取權杖的有效時間長度 (以秒為單位)。 |

### <a name="error-response"></a>錯誤回應

錯誤回應看起來像這樣：

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 參數 | 說明 |
| --- | --- |
| `error` | 您可用來分類發生的錯誤類型並對錯誤做出反應的錯誤碼字串。 |
| `error_description` | 可協助您識別驗證錯誤根本原因的特定錯誤訊息。 |
| `error_codes` | 可協助進行診斷的 STS 特定錯誤碼清單。 |
| `timestamp` | 錯誤發生時間。 |
| `trace_id` | 可協助進行診斷的要求唯一識別碼。 |
| `correlation_id` | 可協助跨元件進行診斷的要求唯一識別碼。 |

> [!NOTE]
> 為了讓您的應用程式能夠接收 v2 語彙基元中，您可以更新應用程式從 azure 入口網站中的資訊清單檔。 您可以將屬性加入`accessTokenAcceptedVersion`並將值設定為 2 指定為`"accessTokenAcceptedVersion": 2`。 請檢查 發行項[應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-reference)若要深入了解相同。 預設應用程式目前會將收到的 v1 語彙基元。 如果這不定義在 API/Web 應用程式資訊清單中，它的資訊清單中的這個屬性的預設值為 1，因此應用程式將會收到 v1 的語彙基元。  


## <a name="use-a-token"></a>使用權杖

既然您已取得權杖，請使用該權杖來對資源提出要求。 當權杖到期時，只要重複執行對 `/token` 端點的要求，即可取得全新的存取權杖。

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>程式碼範例和其他文件

讀取 Microsoft 驗證程式庫中的[用戶端認證概觀文件](https://aka.ms/msal-net-client-credentials)

| 範例 | 平台 |說明 |
|--------|----------|------------|
|[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | .NET core 2.1 主控台 | 一個簡單的.NET Core 應用程式，它會顯示使用應用程式的身分識別查詢 Microsoft Graph 之租用戶的使用者，而不是代表使用者。 該範例也會說明使用憑證進行驗證的變化。 |
|[active-directory-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | 一個 Web 應用程式，它使用應用程式的身分識別同步處理 Microsoft Graph 中的資料，而不是代表使用者。 |
