---
title: Azure Data Factory 中的取得中繼資料活動 | Microsoft Docs
description: 了解如何使用 GetMetadata 活動，以及在 Data Factory 管線中。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: jingwang
ms.openlocfilehash: 78f63b4f46fe5479d4d0fd5849ad80536d8a137c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61346838"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Azure Data Factory 中的取得中繼資料活動

GetMetadata 活動可用來取出 Azure Data Factory 中任何資料的**中繼資料**。 這個活動可以在下列案例使用：

- 驗證任何資料的中繼資料資訊
- 當資料準備好/可用時觸發管線

下列功能可用於控制流程：

- GetMetadata 活動的輸出可用於條件運算式，以執行驗證。
- 透過 Do-Until 迴圈滿足條件時，可以觸發管線

## <a name="supported-capabilities"></a>支援的功能

GetMetadata 活動以資料集作為必要的輸入，並輸出可用的中繼資料資訊作為活動輸出。 目前支援下列具有對應可擷取中繼資料的連接器，而且支援的中繼資料大小上限為 **1 MB**。

>[!NOTE]
>如果您在自封式 Integration Runtime 上執行 GetMetadata 活動，在 3.6 或以上的版本上才支援最新功能。 

### <a name="supported-connectors"></a>支援的連接器

**檔案儲存體：**

| 連接器/中繼資料 | itemName<br>(檔案/資料夾) | itemType<br>(檔案/資料夾) | size<br>(檔案) | created<br>(檔案/資料夾) | lastModified<br>(檔案/資料夾) |childItems<br>(資料夾) |contentMD5<br>(檔案) | structure<br/>(檔案) | columnCount<br>(檔案) | exists<br>(檔案/資料夾) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Amazon S3 | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| Google Cloud Storage | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| Azure Blob | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| Azure Data Lake Storage Gen1 | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Azure Data Lake Storage Gen2 | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Azure 檔案儲存體 | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| 檔案系統 | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| SFTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| FTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

- Amazon S3 和 Google Sloud 儲存體`lastModified`套用至貯體和金鑰，但不是虛擬資料夾; 和`exists`套用至貯體和金鑰，但沒有前置詞或虛擬資料夾。
- 針對 Azure Blob，`lastModified` 會套用至容器和 Blob，但是不會套用至虛擬資料夾。

**關聯式資料庫：**

| 連接器/中繼資料 | structure | columnCount | exists |
|:--- |:--- |:--- |:--- |
| Azure SQL Database | √ | √ | √ |
| Azure SQL Database 受控執行個體 | √ | √ | √ |
| Azure SQL 資料倉儲 | √ | √ | √ |
| SQL Server | √ | √ | √ |

### <a name="metadata-options"></a>中繼資料選項

您可以在 GetMetadata 活動欄位清單中指定以下中繼資料類型，以進行擷取：

| 中繼資料類型 | 描述 |
|:--- |:--- |
| itemName | 檔案或資料夾的名稱。 |
| itemType | 檔案或資料夾的類型。 輸出值是 `File` 或 `Folder`。 |
| size | 檔案的大小 (位元組)。 僅適用於檔案。 |
| created | 檔案或資料夾的建立日期時間。 |
| lastModified | 檔案或資料夾的上次修改日期時間。 |
| childItems | 所指定資料夾內子資料夾和檔案的清單。 僅適用於資料夾。 輸出值為每個子項目的名稱和類型清單。 |
| contentMD5 | 檔案的 MD5。 僅適用於檔案。 |
| structure | 檔案或關聯式資料庫資料表內的資料結構。 輸出值為資料行名稱和資料行類型的清單。 |
| columnCount | 檔案或關聯式資料表內的資料行數目。 |
| exists| 檔案/資料夾/資料表存在與否。 請注意，如果 GetaMetadata 欄位清單中指定了 "exists"，即使當項目 (檔案/資料夾/資料表) 不存在，活動也不會失敗；反之，它會在輸出中傳回 `exists: false`。 |

>[!TIP]
>當您想要驗證檔案/資料夾/資料表是否存在時，請在 GetMetadata 活動欄位清單中指定 `exists`，然後就可以從活動輸出檢查 `exists: true/false` 結果。 如果欄位清單中未設定 `exists`，則 GetMetadata 活動會在找不到物件時失敗。

## <a name="syntax"></a>語法

**GetMetadata 活動：**

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```

**資料集：**

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>類型屬性

目前 GetMetadata 活動可以擷取以下類型的中繼資料資訊。

屬性 | 描述 | 必要項
-------- | ----------- | --------
欄位清單 | 列出所需的中繼資料資訊類型。 如需所支援中繼資料hapi 詳細資訊，請參閱[中繼資料選項](#metadata-options)一節。 | 是 
資料集 | GetMetadata 活動要擷取其中繼資料活動的參考資料集。 如需所支援連接器的資訊，請參閱[支援的功能](#supported-capabilities)一節。如需資料集語法的詳細資訊，請參閱連接器主題。 | 是

## <a name="sample-output"></a>範例輸出

GetMetadata 結果顯示在活動輸出中。 以下兩個範例包含欄位清單中所選取的詳細中繼資料選項作為參考。 若要在後續活動中使用結果，請使用 `@{activity('MyGetMetadataActivity').output.itemName}` 模式。

### <a name="get-a-files-metadata"></a>取得檔案的中繼資料

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>取得資料夾的中繼資料

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟
請參閱 Data Factory 支援的其他控制流程活動： 

- [執行管線活動](control-flow-execute-pipeline-activity.md)
- [For Each 活動](control-flow-for-each-activity.md)
- [查閱活動](control-flow-lookup-activity.md)
- [Web 活動](control-flow-web-activity.md)
