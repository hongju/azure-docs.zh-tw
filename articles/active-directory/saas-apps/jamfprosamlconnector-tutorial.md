---
title: 教學課程：Azure Active Directory 與 Jamf Pro 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Jamf Pro 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18b575b74c80499f2ddd6648bf051b5245077d2f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58906135"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>教學課程：Azure Active Directory 與 Jamf Pro 整合

在本教學課程中，您將了解如何整合 Jamf Pro 與 Azure Active Directory (Azure AD)。
Jamf Pro 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Jamf Pro 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Jamf Pro (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Jamf Pro 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Jamf Pro 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Jamf Pro 支援由 **SP 和 IDP** 初始化的 SSO

## <a name="adding-jamf-pro-from-the-gallery"></a>從資源庫新增 Jamf Pro

若要設定 Jamf Pro 與 Azure AD 整合，您需要從資源庫將 Jamf Pro 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Jamf Pro，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Jamf Pro**，從結果面板中選取 [Jamf Pro]，然後按一下 [新增] 按鈕以新增應用程式。

     ![結果清單中的 Jamf Pro](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，搭配 Jamf Pro 設定及測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Jamf Pro 中相關使用者之間的連結關聯性。

若要搭配 Jamf Pro 設定及測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Jamf Pro 單一登入](#configure-jamf-pro-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Jamf Pro 測試使用者](#create-jamf-pro-test-user)** - 使 Jamf Pro 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要搭配 Jamf Pro 設定 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Jamf Pro] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態] 區段上，若您想要以 **IDP** 起始模式進行設定，請執行下列步驟：

    ![Jamf Pro 網域及 URL 單一登入資訊](common/idp-intiated.png)

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`https://<subdomain>.jamfcloud.com/saml/metadata`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<subdomain>.jamfcloud.com/saml/SSO`

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<subdomain>.jamfcloud.com`

    ![Jamf Pro 網域及 URL 單一登入資訊](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的識別碼、回覆 URL 和登入 URL 來更新這些值。 您將會從 Jamf Pro 入口網站的 [單一登入] 區段取得實際的識別碼值，本教學課程稍後將說明此值。 您可以從識別碼值擷取實際的 **subdomain** 值，並在登入 URL 和回覆 URL 中使用該 **subdomain** 資訊。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

6. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]，並將其儲存在您的電腦上。

    ![憑證下載連結](common/copy-metadataurl.png)

### <a name="configure-jamf-pro-single-sign-on"></a>設定 Jamf Pro 單一登入

1. 若要自動執行 Jamf Pro 內的設定，您必須按一下 [安裝延伸模組] 來安裝「我的應用程式安全登入瀏覽器延伸模組」。

    ![image](./media/jamfprosamlconnector-tutorial/install_extension.png)

2. 將延伸模組新增至瀏覽器之後，按一下 [安裝 Jamf Pro] 便會將您導向到 Jamf Pro 應用程式。 請從該處提供用以登入 Jamf Pro 的管理員認證。 瀏覽器延伸模組將會自動為您設定應用程式，並自動執行步驟 3 到 7。

    ![image](./media/jamfprosamlconnector-tutorial/d1_saml.png)

3. 如果您想要手動設定 Jamf Pro，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 Jamf Pro 公司網站，然後執行下列步驟：

4. 按一下頁面右上角的**設定圖示**。

    ![Jamf Pro 設定](./media/jamfprosamlconnector-tutorial/configure1.png)

5. 按一下 [單一登入]。

    ![Jamf Pro 設定](./media/jamfprosamlconnector-tutorial/configure2.png)

6. 在 [單一登入] 頁面上，執行下列步驟：

    ![Jamf Pro 單一登入](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. 選取 [Jamf Pro 伺服器] 來啟用單一登入存取。

    b. 選取 [允許所有使用者略過]，就不會將使用者重新導向至識別提供者登入頁面來進行驗證，但可改為直接登入 Jamf Pro。 當使用者嘗試透過識別提供者存取 Jamf Pro 時，就會進行 IdP 啟始的 SSO 驗證和授權。

    c. 從 [使用者對應：**SAML]** 中，選取 [NameID] 選項。 根據預設，此設定會設定為 [NameID]，但您可以定義自訂屬性。

    d. 針對 [使用者對應：**JAMF PRO]** 選取 [電子郵件]。 Jamf Pro 會以下列方式對應由 IdP 所傳送的 SAML 屬性：依使用者或依群組。 當使用者嘗試存取 Jamf Pro，Jamf Pro 預設會從識別提供者中取得該使用者的相關資訊，並比對 Jamf Pro 使用者帳戶。 如果傳入的使用者帳戶不存在於 Jamf Pro 中，則會進行群組名稱比對。

    e. 將值 `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` 貼到 [群組屬性名稱] 文字方塊中。

7. 在同一個頁面上，捲動至 [單一登入] 區段下方的 [識別提供者]，然後執行下列步驟：

    ![Jamf Pro 設定](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. 從 [身分識別提供者] 下拉式功能表中，選取 [其他] 作為選項。

    b. 在 [其他提供者] 文字方塊中，輸入 **Azure AD**。

    c. 從 [身分識別提供者中繼資料來源] 下拉式清單中選取 [中繼資料 URL] 作為選項，然後在下列文字方塊中，貼上您從 Azure 入口網站複製的**應用程式同盟中繼資料 URL** 值。

    d. 複製**實體識別碼**值，並將它貼到 Azure 入口網站上 [Jamf Pro 網域及 URL] 區段中的 [識別碼 (實體識別碼)]  文字方塊。

    > [!NOTE]
    > 此處的模糊值為子網域部分。請使用此值來完成 Azure 入口網站上 [Jamf Pro 網域及 URL] 區段中的登入 URL 和回覆 URL。

    e. 按一下 [檔案] 。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon\@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Jamf Pro 的存取權授與 Britta Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [Jamf Pro]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Jamf Pro]。

    ![應用程式清單中的 Jamf Pro 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-jamf-pro-test-user"></a>建立 Jamf Pro 測試使用者

若要讓 Azure AD 使用者能夠登入 Jamf Pro，必須將他們佈建到 Jamf Pro。 在 Jamf Pro 的案例中，佈建是一個手動工作。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 Jamf Pro 公司網站。

2. 按一下頁面右上角的**設定圖示**。

    ![新增員工](./media/jamfprosamlconnector-tutorial/configure1.png)

3. 按一下 [Jamf Pro 使用者帳戶和群組]。

    ![新增員工](./media/jamfprosamlconnector-tutorial/user1.png)

4. 按一下 [新增] 。

    ![新增員工](./media/jamfprosamlconnector-tutorial/user2.png)

5. 選取 [建立標準帳戶]。

    ![新增員工](./media/jamfprosamlconnector-tutorial/user3.png)

6. 在 [新增帳戶] 對話方塊上，執行下列步驟：

    ![新增員工](./media/jamfprosamlconnector-tutorial/user4.png)

    a. 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的全名。

    b. 針對您的組織，選取 [存取層級]、[權限集合] 及 [存取狀態] 的適當選項。

    c. 在 [全名] 文字方塊中，輸入 Britta Simon 的全名。

    d. 在 [電子郵件地址] 文字方塊中，輸入 Britta Simon 帳戶的電子郵件地址。

    e. 在 [密碼] 文字方塊中，輸入使用者的密碼。

    f. 在 [確認密碼] 文字方塊中，輸入使用者的密碼。

    g. 按一下 [檔案] 。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Jamf Pro] 圖格時，應該會自動登入您已設定 SSO 的 Jamf Pro。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
