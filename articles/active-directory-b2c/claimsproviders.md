---
title: ClaimsProvider  - Azure Active Directory B2C | Microsoft Docs
description: 指定 Azure Active Directory B2C 中自訂原則的 ClaimsProvider 元素。
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: ababd7e9f1de33eb8679e583c2db18d2992cfb1c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64699636"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

宣告提供者包含一組[技術設定檔](technicalprofiles.md)。 每個宣告提供者必須有一或多個技術設定檔，以決定與該宣告提供者進行通訊所需的端點以及通訊協定。 宣告提供者可以有多個技術設定檔。 例如，可能定義了多個技術設定檔，因為宣告提供者支援多個通訊協定，各種端點具有不同的功能，或是在不同的保證層級釋出不同的宣告。 它可能會在一個使用者旅程圖中接受釋出機密宣告，但在另一個則不接受。

```XML
<ClaimsProviders>
  <ClaimsProvider>
    <Domain>Domain name</Domain>
    <DisplayName>Display name</DisplayName>
    <TechnicalProfiles>
      </TechnicalProfile>
        ...
      </TechnicalProfile>
        ...
    </TechnicalProfiles>
  </ClaimsProvider>
  ...
</ClaimsProviders>
```

**ClaimsProvider** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1:n | 在各種不同的使用者旅程圖中可供運用的合格宣告提供者。 |

## <a name="claimsprovider"></a>ClaimsProvider

**ClaimsProvider** 元素包含下列子元素：

| 元素 | 發生次數 | 描述 |
| ------- | ---------- | ----------- |
| 網域 | 0:1 | 一個字串，其中包含宣告提供者的網域名稱。 例如，如果您的宣告提供者包含 Facebook 技術設定檔，則網域名稱為 Facebook.com。 此網域名稱會用於宣告提供者中定義的所有技術設定檔，除非技術設定檔加以覆寫。 網域名稱也可在 **domain_hint** 中受到參考。 如需詳細資訊，請參閱[使用 Azure Active Directory B2C 設定直接登入](direct-signin.md)的**將登入重新導向至社交提供者**一節。 |
| DisplayName | 0:1 | 一個字串，其中包含可對使用者顯示的宣告提供者名稱。 |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | 受到宣告提供者支援的一組技術設定檔 |

**ClaimsProvider**組織您的技術設定檔如何與宣告提供者相關聯。 下列範例顯示具有 Azure Active Directory 技術設定檔的 Azure Active Directory 宣告提供者：

```XML
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-Common">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      ...
    </TechnicalProfile>
      ...
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePhoneNumberUsingObjectId">
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

下列範例顯示具有 **Facebook-OAUTH** 技術設定檔的 Facebook 宣告提供者。

```XML
<ClaimsProvider>
  <Domain>facebook.com</Domain>
  <DisplayName>Facebook</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Facebook-OAUTH">
      <DisplayName>Facebook</DisplayName>
      <Protocol Name="OAuth2" />
        ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
