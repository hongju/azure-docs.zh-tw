---
title: Azure Batch 工作開始事件 | Microsoft Docs
description: Batch 工作開始事件的參考。
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
origin.date: 04/20/2017
ms.date: 05/15/2018
ms.author: v-junlch
ms.openlocfilehash: d50a0a7082e409084fd966370934a638ca9bb013
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549864"
---
# <a name="task-start-event"></a>工作開始事件

 一旦排程器已排程工作在計算節點上執行時，就會發出此事件。 請注意，如果工作已重試或重新排入佇列，則系統會為相同的工作再次發出此事件，但重試計數與系統工作版本將會隨之更新。


 下列範例顯示工作開始事件內文。

```
{
    "jobId": "job-0000000001",
    "id": "task-5",
    "taskType": "User",
    "systemTaskVersion": 0,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "retryCount": 0
    }
}
```

|元素名稱|類型|注意|
|------------------|----------|-----------|
|jobId|字串|包含工作的作業識別碼。|
|id|字串|工作識別碼。|
|taskType|字串|工作類型。 它可以是“JobManager”（指示它是作业管理器任务），也可以是“User”（指示它并非作业管理器任务）。|
|systemTaskVersion|Int32|這是作業上的內部重試計數器。 Batch 服務可在內部重試工作，以處理暫時性問題。 這些問題包含內部排程錯誤，或嘗試從不正常狀態的計算節點復原。|
|[nodeInfo](#nodeInfo)|複雜類型|包含工作執行所在計算節點的相關資訊。|
|[multiInstanceSettings](#multiInstanceSettings)|複雜類型|指定工作為需要多個計算節點的多重執行個體工作。  請參閱 [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) (英文) 以取得詳細資訊。|
|[constraints](#constraints)|複雜類型|套用至此工作的執行限制。|
|[executionInfo](#executionInfo)|複雜類型|包含執行工作的相關資訊。|

###  <a name="nodeInfo"></a> nodeInfo

|元素名稱|類型|注意|
|------------------|----------|-----------|
|poolId|字串|工作執行所在集區的識別碼。|
|nodeId|字串|工作執行所在節點的識別碼。|

###  <a name="multiInstanceSettings"></a> multiInstanceSettings

|元素名稱|類型|注意|
|------------------|----------|-----------|
|numberOfInstances|Int|工作需要的計算節點數目。|

###  <a name="constraints"></a> constraints

|元素名稱|類型|注意|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|工作重試次數上限。 如果工作的結束代碼不是零，Batch 服務會重試工作。<br /><br /> 請注意，這個值會特別控制重試次數。 Batch 服務會嘗試工作一次，然後可一直重試直到達此限制。 例如，如果重試計數上限為 3，則 Batch 可嘗試工作最多 4 次 (一次首次嘗試，3 次重試)。<br /><br /> 如果重試計數上限為 0，則 Batch 服務不會重試工作。<br /><br /> 如果重試計數上限為 -1，則 Batch 服務會無限制地重試工作。<br /><br /> 預設值為 0 (不重試)。|

###  <a name="executionInfo"></a> executionInfo

|元素名稱|類型|注意|
|------------------|----------|-----------|
|retryCount|Int32|Batch 服務已重試工作的次數。 如果工作結束時的結束代碼不是零，便會重試工作，直到次數達指定的 MaxTaskRetryCount|

<!-- Update_Description: update metedata properties -->