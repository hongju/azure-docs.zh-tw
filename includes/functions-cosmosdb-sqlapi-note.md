---
title: 包含檔案
description: 包含檔案
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c54aa861a47b11756f05e003e9b944df6c5b0e28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61342847"
---
目前僅支援將 Azure Cosmos DB 繫結用於 SQL API。 對於其他所有的 Azure Cosmos DB API，您均應對您的 API 使用靜態用戶端，以從函式存取資料庫，包括 [Azure Cosmos DB 的 MongoDB 版 API](../articles/cosmos-db/mongodb-introduction.md)、[Cassandra API](../articles/cosmos-db/cassandra-introduction.md)、[Gremlin API](../articles/cosmos-db/graph-introduction.md) 和[資料表 API](../articles/cosmos-db/table-introduction.md)。
