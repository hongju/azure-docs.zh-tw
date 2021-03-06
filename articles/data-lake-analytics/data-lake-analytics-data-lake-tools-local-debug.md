---
title: 在本機為 Azure Data Lake Analytics 程式碼偵錯
description: 了解如何使用 Azure Data Lake Tools for Visual Studio 在本機工作站上為 U-SQL 作業偵錯。
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 0827311218202de447e5cf27356e00c4da020e94
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472986"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>在本機為 Azure Data Lake Analytics 程式碼偵錯

您可以使用 Azure Data Lake Tools for Visual Studio 在工作站上執行 Azure Data Lake Analytics 程式碼並對它進行偵錯，如同在 Azure Data Lake Analytics 服務中的作法一般。

了解如何[在本機電腦上執行 U-SQL 指令碼](data-lake-analytics-data-lake-tools-local-run.md)。

## <a name="debug-scripts-and-c-assemblies-locally"></a>在本機偵錯指令碼和 C# 組件

您可以在不需向 Azure Data Lake Analytics 服務提交並註冊 C# 組件的情況下對它們進行偵錯。 您可以同時在程式碼後置檔案和參考的 C# 專案中設定中斷點。

### <a name="debug-local-code-in-a-code-behind-file"></a>對程式碼後置檔案中的本機程式碼進行偵錯

1. 在程式碼後置檔案中設定中斷點。
2. 選取 **F5** 以在本機對指令碼進行偵錯。

> [!NOTE]
   > 下列程序僅適用於 Visual Studio 2015。 在舊版 Visual Studio 中，您可能需要手動加入 **PDB** 檔案。  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>對參考的 C# 專案中的本機程式碼進行偵錯

1. 建立 C# 組件專案，並建置它來產生輸出 **DLL** 檔案。
2. 使用 U-SQL 陳述式來註冊該 **DLL** 檔案：

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. 在 C# 程式碼中設定中斷點。
4. 選取 **F5** 以透過於本機參考 C# **DLL** 檔案來對指令碼進行偵錯。


## <a name="next-steps"></a>後續步驟

- 如需更複雜查詢的範例，請參閱[使用 Azure Data Lake Analytics 來分析網站記錄](data-lake-analytics-analyze-weblogs.md)。
- 若要檢視作業詳細資料，請參閱[針對 Azure Data Lake Analytics 作業使用作業瀏覽器和作業檢視](data-lake-analytics-data-lake-tools-view-jobs.md)。
- 若要使用頂點執行檢視，請參閱[在 Data Lake Tools for Visual Studio 中使用頂點執行檢視](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)。
