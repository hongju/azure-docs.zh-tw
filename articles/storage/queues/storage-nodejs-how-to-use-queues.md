---
title: 如何使用 Node.js-Azure 儲存體中的佇列儲存體
description: 了解如何使用 Azure 佇列服務來建立和刪除佇列，以及插入、取得和刪除訊息。 範例以 Node.js 撰寫。
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 01afe1ab7b9028f3f77d52f7d6f8ced27f6a79c7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142704"
---
# <a name="how-to-use-queue-storage-from-nodejs"></a>如何使用 Node.js 的佇列儲存體
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>概觀
本指南示範如何使用 Microsoft Azure 佇列服務執行常見案例。 相关示例是使用 Node.js API 编写的。 所涵蓋的案例包括「插入」、「查看」、「取得」和「刪除」佇列訊息，以及「建立和刪除佇列」。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>建立 Node.js 應用程式
建立空白的 Node.js 應用程式。 如需有關建立 Node.js 應用程式的指示，請參閱[在 Azure App Service 中建立 Node.js Web 應用程式](../../app-service/app-service-web-get-started-nodejs.md)、使用 Windows PowerShell 來[建立 Node.js 應用程式並部署至 Azure 雲端服務](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)，或 [Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)。

## <a name="configure-your-application-to-access-storage"></a>設定您的應用程式以存取儲存體
若要使用 Azure 儲存體，您需要 Azure Storage SDK for Node.js，這包含一組便利程式庫，能與儲存體 REST 服務通訊。

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>使用 Node Package Manager (NPM) 取得套件
1. 使用命令列介面，例如 **PowerShell** (Windows)、**終端機** (Mac) 或 **Bash** (Unix)，瀏覽到您建立範例應用程式的資料夾。
2. 在命令視窗中輸入 **npm install azure-storage** 。 此命令的輸出類似下列範例。
 
    ```bash
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    ```

3. 您可以手動執行 **ls** 命令，確認已建立 **node\_modules** 資料夾。 該資料夾中有 **azure-storage** 封裝，當中包含存取儲存體所需的程式庫。

### <a name="import-the-package"></a>匯入封裝
使用記事本或其他文字編輯器，將以下內容新增至您要使用儲存體之應用程式的 **server.js** 檔案頂端：

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>設定 Azure 儲存體連接
azure 模組會讀取環境變數 AZURE\_STORAGE\_ACCOUNT 和 AZURE\_STORAGE\_ACCESS\_KEY，或 AZURE\_STORAGE\_CONNECTION\_STRING，以取得連接 Azure 儲存體帳戶所需的資訊。 如果未設定這些環境變數，則在呼叫 **createQueueService**時必須指定帳戶資訊。

## <a name="how-to-create-a-queue"></a>如何：创建队列
下列程式碼會建立一個 **QueueService** 物件，其讓您能夠使用佇列。

```javascript
var queueSvc = azure.createQueueService();
```

請使用 **createQueueIfNotExists** 方法，此方法會傳回指定的佇列 (如果佇列已經存在)，或以指定的名稱建立新佇列 (如果佇列不存在)。

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

如果建立佇列，則 `result.created` 為 true。 如果佇列已存在，則 `result.created` 為 false。

### <a name="filters"></a>筛选器
可以將選用性的篩選操作套用到使用 **QueueService** 執行的操作。 篩選作業可包括記錄、自動重試等等。篩選器是使用簽章實作方法的物件：

```javascript
function handle (requestOptions, next)
```

在对请求选项执行预处理后，该方法需要调用“next”并且传递具有以下签名的回调：

```javascript
function (returnObject, finalCallback, next)
```

在此回调中，在处理 returnObject（来自对服务器的请求的响应）后，回调需要调用 next（如果它已存在）以继续处理其他筛选器或只调用 finalCallback 以结束服务调用。

Azure SDK for Node.js 包含了實作重試邏輯的兩個篩選器：**ExponentialRetryPolicyFilter** 和 **LinearRetryPolicyFilter**。 以下會建立使用 **ExponentialRetryPolicyFilter** 的 **QueueService** 物件：

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>如何：在队列中插入消息
若要將訊息插入佇列，請使用 **createMessage** 方法建立新訊息，然後將該訊息加到佇列中。

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>如何：扫视下一条消息
透過呼叫 **peekMessages** 方法，您可以在佇列前面查看訊息，而無需將它從佇列中移除。 **peekMessages** 預設會查看單一訊息。

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

`result` 包含訊息。

> [!NOTE]
> 當佇列中沒有任何訊息時，使用 **peekMessages** 並不會傳回錯誤，不過也不會傳回任何訊息。
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>如何：將下一個訊息清除佇列
處理訊息是兩階段的過程：

1. 從佇列中清除訊息。
2. 刪除訊息。

若要從佇列中清除訊息，請使用 **getMessages**。 這樣會使訊息從佇列中隱藏起來，而不讓其他用戶端處理它。 當應用程式處理訊息之後，請呼叫 **deleteMessage** 從佇列中刪除它。 下列範例會取得訊息，接著刪除訊息：

```javascript
queueSvc.getMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
    var message = results[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

> [!NOTE]
> 依預設，訊息只會隱藏 30 秒，之後又會被其他用戶端看見。 您可以使用具有 **getMessages** 的 `options.visibilityTimeout` 指定其他值。
> 
> [!NOTE]
> 當佇列中沒有任何訊息時，使用 **getMessages** 並不會傳回錯誤，不過也不會傳回任何訊息。
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>如何：更改已排队消息的内容
您可以使用 **updateMessage**在佇列中就地變更訊息內容。 下列範例會更新訊息的文字：

```javascript
queueSvc.getMessages('myqueue', function(error, getResults, getResponse){
  if(!error){
    // Got the message
    var message = getResults[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, updateResults, updateResponse){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>如何：清除佇列訊息的其他選項
自訂從佇列中擷取訊息的方法有兩種：

* `options.numOfMessages` - 擷取一批訊息 (最多 32 個)。
* `options.visibilityTimeout` - 設定較長或較短的隱藏逾時。

下列範例使用 **getMessages** 方法，在一次呼叫中取得 15 個訊息。 接著它會使用 for 迴圈處理每個訊息。 另外，對此方法傳回的所有訊息，將隱藏逾時設為五分鐘。

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in result){
      // text is available in result[index].messageText
      var message = results[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, deleteResponse){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

## <a name="how-to-get-the-queue-length"></a>如何：获取队列长度
**getQueueMetadata** 會傳回佇列的中繼資料，包括在佇列中等待的大約訊息數目。

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>如何：列出佇列
若要擷取佇列清單，請使用 **listQueuesSegmented**。 若要擷取依特定首碼篩選的清單，請使用 **listQueuesSegmentedWithPrefix**。

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

若無法傳回所有佇列，`result.continuationToken` 可做為 **listQueuesSegmented** 的第一個參數，或 **listQueuesSegmentedWithPrefix** 的第二個參數，以擷取更多結果。

## <a name="how-to-delete-a-queue"></a>如何：删除队列
若要刪除佇列及其內含的所有訊息，請在佇列物件上呼叫 **deleteQueue** 方法。

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

若要從佇列中清除所有訊息但不要刪除，請使用 **clearMessages**。

## <a name="how-to-work-with-shared-access-signatures"></a>作法：使用共用存取簽章
共用存取簽章 (SAS) 可安全地提供對佇列的精確存取，而不必提供您的儲存體帳戶名稱或金鑰。 SAS 通常用來提供對佇列的有限存取，例如允許行動應用程式提交訊息。

信任的應用程式 (例如雲端型服務) 會使用 **QueueService** 的 **generateSharedAccessSignature** 來產生 SAS，並提供它給不信任或不完全信任的應用程式。 例如行動應用程式。 SAS 是使用原則來產生，該原則描述 SAS 有效期間的開始和結束日期，以及授與 SAS 持有者的存取等級。

下列範例會產生新的共用存取原則，讓 SAS 持有者將訊息新增至佇列，並於建立它之後的 100 分鐘過期。

```javascript
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};

var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
var host = queueSvc.host;
```

請注意，也必須提供主機資訊，因為 SAS 持有者嘗試存取佇列時需要此資訊。

用戶端應用程式接著以 **QueueServiceWithSAS** 來使用 SAS，對佇列執行操作。 下列範例會連線到佇列並建立訊息。

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

因為產生的 SAS 具有新增權限，若嘗試讀取、更新或刪除訊息，則會傳回錯誤。

### <a name="access-control-lists"></a>存取控制清單
还可以使用访问控制列表 (ACL) 为 SAS 设置访问策略。 若您要允許用戶端存取佇列，但對每個用戶端提供不同的存取原則，則這會很有用。

ACL 是使用一组访问策略实施的，每个策略都有一个关联的 ID。 下列範例定義兩個原則，其中一個用於 'user1'，另一個用於 'user2'：

```javascript
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

下列範例會取得 **myqueue** 的目前 ACL，然後使用 **setQueueAcl** 來新增新的原則。 此方法允許：

```javascript
var extend = require('extend');
queueSvc.getQueueAcl('myqueue', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

設定 ACL 之後，您可以根據原則的識別碼來建立 SAS。 以下示例为“user2”创建新的 SAS：

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>後續步驟
了解佇列儲存體的基礎概念之後，請參考下列連結以了解有關更複雜的儲存工作。

* 造訪 [Azure 儲存體團隊部落格][Azure Storage Team Blog]。
* 請造訪 GitHub 上的 [Azure Storage SDK for Node][Azure Storage SDK for Node] 儲存機制。



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: https://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[在 Azure App Service 中建立 Node.js Web 應用程式](../../app-service/app-service-web-get-started-nodejs.md)

[建立 Node.js 應用程式並部署到 Azure 雲端服務](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
