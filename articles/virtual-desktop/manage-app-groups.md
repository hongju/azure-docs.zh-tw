---
title: 管理 Windows 虛擬桌面預覽版的應用程式群組 - Azure
description: 描述如何設定 Azure Active Directory 中的 Windows 虛擬桌面預覽版租用戶。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: f0cdd28be8c6e7390aa26fdc2dfbf32ec5542c2d
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233915"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>教學課程：管理 Windows 虛擬桌面預覽版的應用程式群組

針對新 Windows 虛擬桌面預覽版主機集區建立的預設應用程式群組也會發佈完整桌面。 此外，您可以為主機集區建立一或多個 RemoteApp 應用程式群組。 遵循本教學課程來建立 RemoteApp 應用程式群組及發佈個別 [開始] 功能表應用程式。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立 RemoteApp 群組。
> * 授與 RemoteApp 的存取權。

開始之前，[下載並匯入 Windows 虛擬桌面 PowerShell 模組](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)Windows 虛擬桌面模組，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。

## <a name="create-a-remoteapp-group"></a>建立 RemoteApp 群組

1. 執行下列 PowerShell Cmdlet 來建立新的空白 RemoteApp 應用程式群組。

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (選用) 若要確認已建立應用程式群組，您可以執行下列 Cmdlet 來查看主機集區的所有應用程式群組清單。

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. 執行下列 Cmdlet 來取得主機集區的虛擬機器映像上的 [開始] 功能表應用程式清單。 請記下 **FilePath**、**IconPath**、**IconIndex** 的值，以及您要發佈的應用程式的其他重要資訊。

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. 執行下列 Cmdlet 以根據 appalias 安裝應用程式。 當您執行執行步驟 3 的輸出時，appalias 隨即顯示。

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (選擇性) 執行下列 Cmdlet 將新的 RemoteApp 發佈到步驟 1 中建立的應用程式群組。

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. 若要確認已發佈應用程式，請執行下列 Cmdlet。

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. 針對您要對此應用程式群組發佈的每個應用程式，重複步驟 1–5。
8. 執行下列 Cmdlet 來授與使用者存取應用程式群組中 Remoteapp 應用程式的權限。

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何建立應用程式群組、以 RemoteApps 填入群組，以及將使用者指派給應用程式群組。 若要深入了解如何登入 Windows 虛擬桌面，請繼續進行「連線到 Windows 虛擬桌面」操作說明。

- [連線到 Windows 7 和 Windows 10 上的遠端桌面用戶端](connect-windows-7-and-10.md)
- [連線到 Windows 虛擬桌面預覽版 Web 用戶端](connect-web.md)
