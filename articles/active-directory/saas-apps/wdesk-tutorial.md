---
title: 教學課程：Azure Active Directory 與 Wdesk 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Wdesk 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 71feb455457fdf75fb19121bac1927b42fe38b67
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59265877"
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>教學課程：Azure Active Directory 與 Wdesk 整合

在本教學課程中，您會了解如何整合 Wdesk 與 Azure Active Directory (Azure AD)。
整合 Wdesk 與 Azure AD 有下列優點：

* 您可以在 Azure AD 中控制可存取 Wdesk 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Wdesk (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Wdesk 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 Wdesk 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Wdesk 支援 **SP** 和 **IDP** 起始的 SSO

## <a name="adding-wdesk-from-the-gallery"></a>從資源庫新增 Wdesk

若要設定將 Wdesk 整合到 Azure AD 中，您需要從資源庫將 Wdesk 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Wdesk，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 ，從結果面板中選取 [Wdesk]，然後按一下 [新增] 按鈕以新增應用程式。

     ![結果清單中的 Wdesk](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，使用 Wdesk 設定及測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Wdesk 中相關使用者之間的連結關聯性。

若要使用 Wdesk 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Wdesk 單一登入](#configure-wdesk-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Wdesk 測試使用者](#create-wdesk-test-user)** - 使 Wdesk 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要使用 Wdesk 設定 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Wdesk] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 設定] 區段上，如果您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    ![Wdesk 網域與 URL 單一登入資訊](common/idp-intiated.png)

    a. 在 [識別碼] 文字方塊中，使用下列模式來輸入 URL：`https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. 在 [回覆 URL] 文字方塊中，使用下列模式來輸入 URL：`https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    ![Wdesk 網域與 URL 單一登入資訊](common/metadata-upload-additional-signon.png)

    在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的識別碼、回覆 URL 和登入 URL 來更新這些值。 當您設定 SSO 時，您可以從 WDesk 入口網站取得這些值。

4. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中按一下 [下載]，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

6. 在 [設定 Wdesk] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-wdesk-single-sign-on"></a>設定 Wdesk 單一登入

1. 在不同的網頁瀏覽器視窗中，以安全性系統管理員身分登入 Wdesk。

2. 按一下左下方的 [管理員]，選擇 [帳戶管理員]：
 
     ![設定單一登入](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. 在 Wdesk 管理員中，依序巡覽至 [安全性]、[SAML] > [SAML 設定]：

    ![設定單一登入](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

4. 勾選 [一般設定] 下的 [Enable SAML Single Sign On] \(啟用 SAML 單一登入)：

    ![設定單一登入](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. 在 [Service Provider Details] \(提供者詳細資料) 區段中，執行下列步驟：

    ![設定單一登入](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. 將**登入 URL** 複製並貼入 Azure 入口網站的 [登入 URL] 文字方塊。
   
      b. 將**中繼資料 URL** 複製並貼入 Azure 入口網站的 [識別碼] 文字方塊。
       
      c. 將**取用者 URL** 複製並貼入 Azure 入口網站的 [回覆 URL] 文字方塊。
   
      d. 按一下 Azure 入口網站的 [儲存]，以儲存變更。      

6. 按一下 [Configure IdP Settings] \(設定 IdP 設定) 開啟 [Edit IdP Settings] \(編輯 IdP 設定) 對話方塊。 按一下 [選擇檔案] 找出從 Azure 入口網站儲存的 **Metadata.xml** 檔案，然後將它上傳。
    
    ![設定單一登入](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. 按一下 [儲存變更] 。

    ![設定單一登入](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 brittasimon@yourcompanydomain.extension。 例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Wdesk 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [Wdesk]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Wdesk]。

    ![應用程式清單中的 Wdesk 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-wdesk-test-user"></a>建立 Wdesk 測試使用者

為使 Azure AD 使用者登入 Wdesk，必須將他們佈建到 Wdesk。 在 Wdesk 中，需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以安全性系統管理員身分登入 Wdesk。

2. 巡覽至 [管理員] > [帳戶管理員]。

     ![設定單一登入](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. 按一下 [人員] 下的 [成員]。

4. 現在按一下 [新增成員] 開啟 [新增成員] 對話方塊。 
   
    ![建立 Azure AD 測試使用者](./media/wdesk-tutorial/createuser1.png)  

5. 在 [使用者] 文字方塊中，輸入使用者的使用者名稱，例如 brittasimon@contoso.com，然後按一下 [繼續] 按鈕。

    ![建立 Azure AD 測試使用者](./media/wdesk-tutorial/createuser3.png)

6.  如下所示輸入詳細資料：
  
    ![建立 Azure AD 測試使用者](./media/wdesk-tutorial/createuser4.png)
 
    a. 在 [電子郵件] 文字方塊中，輸入使用者的電子郵件，例如 brittasimon@contoso.com。

    b. 在 [名字] 文字方塊中，輸入使用者的名字，例如 **Britta**。

    c. 在 [姓氏] 文字方塊中，輸入使用者的姓氏，例如 **Simon**。

7. 按一下 [儲存成員] 按鈕。  

    ![建立 Azure AD 測試使用者](./media/wdesk-tutorial/createuser5.png)

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Wdesk] 圖格時，應該會自動登入您已設定 SSO 的 Wdesk。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

