---
title: Azure 排程器中的限制、配額與閾值
description: 了解 Azure 排程器的限制、配額、預設值，以及節流閾值
services: scheduler
ms.service: scheduler
author: WenJason
ms.author: v-jay
ms.reviewer: klam
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.topic: article
origin.date: 08/18/2016
ms.date: 11/12/2018
ms.openlocfilehash: 478afb20f3dabec74d66d00bec325408ce6604fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531301"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Azure 排程器中的限制、配額以及節流閾值

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 會取代 Azure 排程器，Azure 排程器之後將無法使用。 若要排定作業，請[改為嘗試 Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md)。 

## <a name="limits-quotas-and-thresholds"></a>限制、配額與閾值

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>x-ms-request-id 標頭

每一個對排程器服務提出的要求都會傳回名為 **x-ms-request-id** 的回應標頭。此標頭包含專門識別要求的不透明值。 因此，如果要求一直失敗，並且您確認要求的格式正確，則可以透過提供 **x-ms-request-id** 回應標頭值並包含下列詳細資料，向 Microsoft 回報錯誤： 

* **x-ms-request-id** 值
* 提出要求的大約時間 
* Azure 訂用帳戶、作業集合和作業識別碼 
* 要求所嘗試的作業類型

## <a name="see-also"></a>請參閱

* [什麼是 Azure 排程器？](scheduler-intro.md)
* [Azure 排程器概念、術語及實體階層](scheduler-concepts-terms.md)
