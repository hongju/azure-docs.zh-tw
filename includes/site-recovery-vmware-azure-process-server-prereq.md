---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 96cba4e077be8b7658c270b09b177a845e16c8b0
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165022"
---
本文假設：

1. 已經建立內部部署網路與 Azure 虛擬網路之間的**站對站 VPN** 或 **Express Route** 連線。
2. 您的使用者帳戶有權限在虛擬機器容錯移轉至的 Azure 訂用帳戶中建立新的虛擬機器。
3. 您的訂用帳戶至少有 4 個核心可用來加快新的處理序伺服器虛擬機器。
4. 您有**設定伺服器複雜密碼**可用。

> [!TIP]
> 確保您能夠從虛擬機器已容錯移轉至的 Azure 虛擬網路，連接設定伺服器 (在內部部署執行) 的連接埠 443。
