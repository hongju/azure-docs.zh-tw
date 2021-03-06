---
title: 以最短停機時間移轉至適用於 PostgreSQL 的 Azure 資料庫
description: 本文說明如何使用 Azure 資料庫移轉服務，以最短的停機時間執行從 PostgreSQL 資料庫到「適用於 PostgreSQL 的 Azure 資料庫」的移轉。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/07/2018
ms.openlocfilehash: ceb64781dc7e5243f785ad239c24e5f21b0481ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60421190"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>以最短停機時間移轉至適用於 PostgreSQL 的 Azure 資料庫
您可以使用 [Azure 資料庫移轉服務](https://aka.ms/get-dms) (DMS) 新導入的**持續同步功能**，以最短的停機時間執行從 PostgreSQL 到「適用於 PostgreSQL 的 Azure 資料庫」的移轉。 這項功能可縮短應用程式所造成的停機時間。

## <a name="overview"></a>概觀
Azure DMS 會執行「適用於 PostgreSQL 的 Azure 資料庫」內部部署的初始載入，然後在應用程式仍在執行時持續將任何新交易同步至 Azure。 當資料同步至目標 Azure 端後，您應短暫停止應用程式 (最短停機時間)，等待最後一批資料 (從您停止應用程式，到應用程式實際上無法再接收任何新流量為止) 同步至目標端，然後再更新連接字串以指向 Azure。 當您完成時，應用程式將會在 Azure 上執行！

![透過 Azure 資料庫移轉服務進行持續同步](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>後續步驟
- 檢視[使用 Microsoft Azure 將應用程式現代化](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102)影片，影片中會示範如何將 PostgreSQL 應用程式移轉至適用於 PostgreSQL 的 Azure 資料庫。
- 請參閱[在線上使用 DMS 將 PostgreSQL 遷移至適用於 PostgreSQL 的 Azure 資料庫](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online)教學課程。
