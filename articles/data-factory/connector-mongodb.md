---
title: 使用 Azure Data Factory 從 MongoDB 複製資料 | Microsoft Docs
description: 了解如何使用 Azure Data Factory 管線中的複製活動，將資料從 MongoDB 複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 12/20/2018
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: ca6040bb74839f30a2f1b13297f6037f05240c67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61400435"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>使用 Azure Data Factory 從 MongoDB 複製資料

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 MongoDB 資料庫複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

>[!IMPORTANT]
>ADF 發行這個新版的 MongoDB 連接器，以提供更好的原生 MongoDB 支援。 如果您在解決方案中使用先前的 MongoDB 連接器 (依原樣為其提供回溯相容性的支援)，請參閱 [MongoDB 連接器 (舊版)](connector-mongodb-legacy.md) 一文。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 MongoDB 資料庫複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 MongoDB 連接器所支援的**版本最高可達 3.4 版**。

## <a name="prerequisites"></a>必要條件

若要從不可公開存取的 MongoDB 資料庫複製資料，您必須設定一個「自我裝載 Integration Runtime」。 如需詳細資料，請參閱[自我裝載 Integration Runtime](create-self-hosted-integration-runtime.md) 一文。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 MongoDB 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 MongoDB 已連結服務支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type |類型屬性必須設定為：**MongoDbV2** |是 |
| connectionString |指定 MongoDB 連接字串，例如 `mongodb://[username:password@]host[:port][/[database][?options]]`。 如需詳細資訊，請參閱 [MongoDB 手冊中關於連接字串的內容](https://docs.mongodb.com/manual/reference/connection-string/)。 <br/><br />將此欄位標記為 **SecureString** 類型，將它安全地儲存在 Data Factory 中。 您也可以[參考 Azure Key Vault 中儲存的認證](store-credentials-in-key-vault.md)。 |是 |
| database | 您要存取的資料庫名稱。 | 是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 您可以使用「自我裝載 Integration Runtime」或 Azure Integration Runtime (如果您的資料存放區是可公開存取的)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

**範例：**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://[username:password@]host[:port][/[database][?options]]"
            },
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需定義資料集的區段和屬性完整清單，請參閱[資料集和連結服務](concepts-datasets-linked-services.md)。 以下是針對 MongoDB 資料集支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為：**MongoDbV2Collection** | 是 |
| collectionName |MongoDB 資料庫中集合的名稱。 |是 |

**範例：**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 MongoDB 來源所支援的屬性清單。

### <a name="mongodb-as-source"></a>MongoDB 作為來源

複製活動的 **source** 區段支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為：**MongoDbV2Source** | 是 |
| filter | 使用查詢運算子指定選取範圍篩選。 若要傳回集合中的所有文件，請省略此參數，或傳遞空白文件 ({})。 | 否 |
| cursorMethods.project | 指定要在文件中傳回以便投影的欄位。 若要傳回比對文件中的所有欄位，請省略此參數。 | 否 |
| cursorMethods.sort | 指定查詢傳回比對文件的順序。 請參閱 [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)。 | 否 |
| cursorMethods.limit | 指定伺服器傳回的文件數目上限。 請參閱 [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit)。  | 否 |
| cursorMethods.skip | 指定要跳過的文件數，以及 MongoDB 開始傳回結果的位置。 請參閱 [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip)。 | 否 |
| batchSize | 指定要在回應的每個批次中從 MongoDB 執行個體傳回的文件數目。 在大部分情況下，修改批次大小不會影響使用者或應用程式。 Cosmos DB 限制每個批次的大小不能超過 40 MB，也就是文件大小的 batchSize 總數，因此如果您的文件大小很大，請降低此值。 | 否<br/>(預設為 **100**) |

>[!TIP]
>ADF 支援在 **Strict 模式**中取用 BSON 文件。 請確定您的篩選查詢處於 Strict 模式，而非 Shell 模式。 您可以在 [MongoDB 手冊](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html) \(英文\) 上找到更詳細的說明。

**範例：**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "MongoDbV2Source",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="export-json-documents-as-is"></a>依原樣匯出 JSON 文件

您可以使用這個 MongoDB 連接器，從 MongoDB 集合依原樣將 JSON 文件匯出至各種檔案型存放區或 Azure Cosmos DB。 若要達成這類無結構描述的複製，請略過資料集中的「結構」(也稱為「結構描述」) 區段，以及複製活動中的結構描述對應。

## <a name="schema-mapping"></a>結構描述對應

若要將資料從 MongoDB 複製到表格式接收器，請參閱[結構描述對應](copy-activity-schema-and-type-mapping.md#schema-mapping)。

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。
