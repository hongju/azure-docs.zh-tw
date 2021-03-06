---
title: 包含檔案
description: 包含檔案
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 0b3af1bd7b3ab48074432d9e39552d72c6664b98
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58124726"
---
將資源部署至 Azure 時，您有極大的彈性可決定所要部署的資源類型、資源所在之處，以及如何設定資源。 不過，此種彈性可能會使得組織所擁有的選項超出您的規劃。 當您考慮將資源部署至 Azure 時，可能會思考下列問題：

* 如何符合某些國家/地區中資料主權的法律需求？
* 如何控制成本？
* 如何確保他人不會不慎變更重要系統？
* 如何追蹤資源成本並準確地計費？

本文可解決這些問題。 具體而言，您可以：

> [!div class="checklist"]
> * 將使用者指派給角色，並將角色指派給範圍，讓使用者有權限能夠執行預期的動作，但僅止於此。
> * 針對訂用帳戶中的資源，套用規定慣例的原則。
> * 鎖定系統不可或缺的資源。
> * 標記資源，讓您可以依照對組織有意義的價值來追蹤資源。

本文著重於實作治理所要進行的工作。 如需更廣泛的概念討論，請參閱 [Azure 中的治理](../articles/security/governance-in-azure.md)。 
