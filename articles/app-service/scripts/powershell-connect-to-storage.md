---
title: Azure PowerShell 指令碼範例 - 將應用程式連線至儲存體帳戶 | Microsoft Docs
description: Azure PowerShell 指令碼範例 - 將 App Service 應用程式連線至儲存體帳戶
services: app-service\web
documentationcenter: ''
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: e4831bdc-2068-4883-9474-0b34c2e3e255
ms.service: app-service
ms.devlang: multiple
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 523ab8aa242b2c5eab2d9c2aa5b61485d84185eb
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106795"
---
# <a name="connect-an-app-service-app-to-a-storage-account"></a>將 App Service 應用程式連線至儲存體帳戶

在此案例中，您將了解如何建立 Azure 儲存體帳戶和 App Service 應用程式。 然後，您會使用應用程式設定來將儲存體帳戶連結到該應用程式。

您可以視需要使用 [Azure PowerShell 指南](/powershell/azure/overview) \(英文\) 中的指示來安裝 Azure PowerShell，然後執行 `Connect-AzAccount` 來建立與 Azure 的連線。

## <a name="sample-script"></a>範例指令碼

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-storage/connect-to-storage.ps1 "Connect an app to a storage account")]

## <a name="clean-up-deployment"></a>清除部署 

執行指令碼範例之後，您可以使用下列命令來移除資源群組、App Service 應用程式及所有相關資源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | 建立 App Service 方案。 |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 建立 App Service 應用程式。 |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | 建立儲存體帳戶。 |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | 取得 Azure 儲存體帳戶的存取金鑰。 |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | 修改 App Service 應用程式的設定。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure PowerShell 範例](../samples-powershell.md)中找到適用於 Azure App Service 的其他 Azure PowerShell 範例。
