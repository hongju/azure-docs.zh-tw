---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 880b630ae48eda086f6454f0d7108d27d3403b77
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556580"
---
如果您在非 DHCP 環境中開機設定，請遵循下列步驟來部署虛擬機器為您的 Data Gateway。

1. [連接到裝置的 Windows PowerShell 介面](#connect-to-the-powershell-interface)。
2. 使用`Get-HcsIpAddress`cmdlet 來列出虛擬裝置上啟用的網路介面。 如果您的裝置有已啟用的單一網路介面，系統指派給該介面的預設名稱會是 `Ethernet`。

    下列範例會示範這個指令程式的使用方式：

    ```
    [10.100.10.10]: PS>Get-HcsIpAddress

    OperationalStatus : Up
    Name              : Ethernet
    UseDhcp           : True
    IpAddress         : 10.100.10.10
    Gateway           : 10.100.10.1
    ```

3. 使用 `Set-HcsIpAddress` Cmdlet 來設定網路。 請參閱下列範例：

    ```
    Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1
    ```

