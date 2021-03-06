---
title: Web 搜尋端點
titleSuffix: Azure Cognitive Services
description: Web 搜尋 API 端點的摘要。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 11/14/2018
ms.author: v-gedod
ms.openlocfilehash: 8c3fd0fc091edbc4323315f636ed2f4fea7d822a
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55880783"
---
# <a name="web-search-endpoint"></a>Web 搜尋端點

**Web 搜尋 API** 會傳回網頁、新聞、影像、影片和[實體](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)。 實體包含人員、地點或主題的摘要資訊。

## <a name="endpoint"></a>端點

若要使用 Bing API 取得 Web 搜尋結果，請將 `GET` 要求傳送至下列端點。 標頭和 URL 參數可定義進一步的規格。

**端點**：傳回 `?q=""` 所定義使用者搜尋查詢的相關 Web 結果。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

端點：如需標頭、參數、市場代碼、回應物件、錯誤等詳細資料，請參閱 [Bing Web API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) \(英文\) 參考。

## <a name="response-json"></a>回應 JSON

Web 搜尋要求的回應包含作為 JSON 物件的結果。 剖析結果時，需要可處理各類型元素的程序。 如需相關範例，請參閱[教學課程](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app)和[原始程式碼](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search)。

## <a name="next-steps"></a>後續步驟

**Bing** API 支援根據類型傳回結果的搜尋動作。 所有搜尋端點會傳回作為 JSON 回應物件的結果。  所有端點均可支援依照經度、緯度和搜尋半徑傳回特定語和位置的查詢。

如需每個端點支援的參數完整相關資訊，請參閱每種類型的參考頁面。
如需透過範例了解使用 Web 搜尋 API 的基本要求，請參閱[搜尋 Web 快速入門](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web)。
