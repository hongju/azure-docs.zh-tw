---
title: SQL 資料倉儲中的透明資料加密 (T-SQL) | Microsoft Docs
description: SQL 資料倉儲中的透明資料加密 (TDE) (T-SQL)
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/30/2019
ms.author: kavithaj
ms.reviewer: rortloff
ms.openlocfilehash: 1d62bc5cb080f2167f13bd58e9a8a334b8ae5163
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146077"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>開始使用透明資料加密 (TDE)
> [!div class="op_single_selector"]
> * [安全性概觀](sql-data-warehouse-overview-manage-security.md)
> * [驗證](sql-data-warehouse-authentication.md)
> * [加密 (入口網站)](sql-data-warehouse-encryption-tde.md)
> * [加密 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>所需权限
您必須是系統管理員或 dbmanager 角色的成員，才能啟用透明資料加密 (TDE)。

## <a name="enabling-encryption"></a>啟用加密
請遵循下列步驟來啟用 SQL 資料倉儲的 TDE：

1. 使用在 master 資料庫中做為系統管理員或 *dbmanager* 角色成員的登入，連接到裝載資料庫之伺服器上的 **master** 資料庫
2. 執行下列陳述式來加密資料庫。

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>停用加密
請遵循下列步驟來停用 SQL 資料倉儲的 TDE：

1. 使用在 master 資料庫中做為系統管理員或 *dbmanager* 角色成員的登入，連接到 **master** 資料庫
2. 執行下列陳述式來加密資料庫。

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> 變更 TDE 設定之前，必須先恢復暫停的 SQL 資料倉儲。
> 
> 

## <a name="verifying-encryption"></a>確認加密
若要確認 SQL 資料倉儲的加密狀態，請遵循下列步驟：

1. 使用在 master 資料庫中做為系統管理員或 *dbmanager* 角色成員的登入，連接到 **master** 或執行個體資料庫
2. 執行下列陳述式來加密資料庫。

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

```1``` 的結果表示加密的資料庫，```0``` 表示未加密的資料庫。

## <a name="encryption-dmvs"></a>加密 DMV
* [sys.databases][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
