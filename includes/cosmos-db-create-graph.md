---
title: 包含檔案
description: 包含檔案
services: cosmos-db
author: rockboyfor
ms.service: cosmos-db
ms.topic: include
origin.date: 04/13/2018
ms.date: 03/18/2019
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 9800848b24523a0eb5992e64ce1580cc53b0e3a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60891425"
---
<!--Verify sucessfully-->
您現在可以在 Azure 入口網站中使用 [資料總管] 工具，建立圖形資料庫。 

1. 按一下 [資料總管] > [新增圖形]。

    [新增圖形] 區域會顯示在最右邊，您可能需要向右捲動才會看到。

    ![Azure 入口網站資料總管 [新增圖形] 頁面](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer-graph.png)

2. 在 [新增圖形] 頁面上，輸入新圖形的設定。

    設定|建議的值|說明
    ---|---|---
    資料庫識別碼|sample-database|輸入 *sample-database* 作為新資料庫的名稱。 資料庫名稱的長度必須介於 1 到 255 個字元，且不能包含 `/ \ # ?` 或尾端空格。
    圖形識別碼|sample-graph|輸入 *sample-graph* 作為新集合的名稱。 圖形名稱與資料庫識別碼具有相同的字元需求。
    儲存體容量|固定 (10 GB)|保持使用預設值 [固定 (10 GB)]。 此值是資料庫的儲存體容量。
    Throughput|400 RU|將輸送量變更為每秒 400 個要求單位 (RU/秒)。 如果您想要降低延遲，稍後可以相應增加輸送量。

3. 填妥表單後，按一下 [確定]。

<!--Verify sucessfully-->
<!--Update_Description: new articles on  -->
<!--ms.date: 03/18/2019-->