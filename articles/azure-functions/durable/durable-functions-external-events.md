---
title: 在 Durable Functions 中處理外部事件 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 擴充中處理外部事件。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: eb024e11b78d13d5ab4544c634acef2ade8141c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730781"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>在 Durable Functions (Azure Functions) 中處理外部事件

協調器函式能夠等候和接聽外部事件。 [Durable Functions](durable-functions-overview.md) 的這項功能通常有助於處理人為互動或其他外部觸發程序。

> [!NOTE]
> 外部事件都是單向的非同步作業。 不適用於傳送事件的用戶端需要來自協調器函式的同步回應的狀況。

## <a name="wait-for-events"></a>等候事件

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) 方法可讓協調器函式以非同步方式等候和接聽外部事件。 接聽協調器函式會宣告事件的「名稱」和預期收到的「資料形式」。

### <a name="c"></a>C#

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

上述範例會接聽特定單一事件，並於收到該事件時採取動作。

您可以同時接聽多個事件，例如，下列範例中會等候三個可能的事件通知之一。

#### <a name="c"></a>C#

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

前一個範例會接聽多個事件中的「任何」事件。 也可以等候「所有」事件。

#### <a name="c"></a>C#

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) 會無限期地等候一些輸入。  等候時可以安心卸載函式應用程式。 當此協調流程執行個體有事件抵達時，就會自動甦醒並立即處理事件。

> [!NOTE]
> 如果函數應用程式使用使用情況方案，則協調器函式等候來自 `WaitForExternalEvent` (.NET) 或 `waitForExternalEvent` (JavaScript) 的工作時，不論等多久都不會產生費用。

在 .NET 中，如果事件裝載無法轉換為預期的類型 `T`，則會擲回例外狀況。

## <a name="send-events"></a>傳送事件

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 類別的 [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) 方法會傳送 `WaitForExternalEvent` (.NET) 或 `waitForExternalEvent` (JavaScript) 等待的事件。  `RaiseEventAsync` 方法接受 *eventName* 和 *eventData* 作為參數。 事件資料必須是 JSON 可序列化。

以下的範例佇列觸發函式會將「核准」事件傳送至協調器函式執行個體。 協調流程執行個體識別碼來自佇列訊息的本文。

### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

在內部，`RaiseEventAsync` (.NET) 或 `raiseEvent` (JavaScript) 會將訊息加入佇列，供等候協調器函式取用。 如果執行個體不是在等候指定的「事件名稱」，事件訊息就會新增至記憶體內部佇列。 如果協調流程執行個體稍後開始接聽該「事件名稱」，它將會檢查佇列是否有事件訊息。

> [!NOTE]
> 如果沒有具有指定「執行個體識別碼」的協調流程執行個體，則會捨棄事件訊息。 如需這個行為的詳細資訊，請參閱 [GitHub 問題](https://github.com/Azure/azure-functions-durable-extension/issues/29)。 

> [!WARNING]
> 以 JavaScript 在本機開發時，您必須將環境變數 `WEBSITE_HOSTNAME` 設定為 `localhost:<port>`，例如 `localhost:7071`，以在 `DurableOrchestrationClient` 上使用方法。 如需此需求的詳細資訊，請參閱 [GitHub 問題](https://github.com/Azure/azure-functions-durable-js/issues/28) \(英文\)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何設定永久性協調流程](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [執行等候外部事件的範例](durable-functions-phone-verification.md)

> [!div class="nextstepaction"]
> [執行等候人為互動的範例](durable-functions-phone-verification.md)