---
title: 包含檔案
description: 包含檔案
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 359347e41264711a6ac0fa4d2dd0c3633590e917
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488020"
---
無法從伺服器存取 Azure 檔案同步服務時，可能會發生此錯誤。 您可以透過下列步驟，針對此錯誤進行疑難排解：

1. 確認 Windows 服務 `FileSyncSvc.exe` 未遭到防火牆封鎖。
2. 確認連接埠 443 已開放給 Azure 檔案同步服務的傳出連線。 您可以透過 `Test-NetConnection` Cmdlet 完成這項作業。 下列 `<azure-file-sync-endpoint>` 預留位置的 URL 可以在 [Azure 檔案同步 Proxy 和防火牆設定](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall)文件中找到。 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. 確定 Proxy 設定已如預期般設定。 這可以透過 `Get-StorageSyncProxyConfiguration` Cmdlet 完成這項作業。 有關針對 Azure 檔案同步進行 Proxy 設定的詳細資訊，可以在 [Azure 檔案同步 Proxy 和防火牆設定](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall)中找到。

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
    
4. 如需針對網路連線進行疑難排解的其他協助，請連絡網路系統管理員。