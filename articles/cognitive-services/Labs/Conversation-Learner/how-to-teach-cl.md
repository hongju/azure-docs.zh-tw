---
title: 如何教導對話學習模組 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解如何教導對話學習模組。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8c55bb27ce5a413c5ceae22371ad61a5acf47281
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220258"
---
# <a name="how-to-teach-with-conversation-learner"></a>如何教導對話學習模組 

本文件說明對話學習模組可辨識的訊號，並說明其學習方式。  

教導可分成兩個不同的步驟：實體擷取和動作選取。

## <a name="entity-extraction"></a>實體擷取

實際上，對話學習模組會使用 [LUIS](https://www.luis.ai) 進行實體擷取。  如果您熟悉 LUIS，則可將其體驗運用於對話學習模組中的實體擷取上。

實體擷取模型可辨識使用者語句內的*內容*和*上下文*。  例如，如果「西雅圖」一詞已在某個語句 (例如「西雅圖的天氣如何」) 中標示為城市，實體擷取即可在另一個語句 (例如「西雅圖的人口數」) 中將這個相同的內容 (「西雅圖」) 辨識為城市，即使兩個語句有很大的差異亦然。  相反地，如果 "Francis" 已在「安排與 Francis 會面」中辨識為名稱，則可在類似的上下文 (例如「設定與 Robin 會面」) 中辨識先前未曾出現的新名稱。  機器學習會根據訓練範例推斷何時應處理內容和 (或) 上下文。

目前，實體擷取只能辨識現行語句的內容。  與動作選取 (說明如下) 不同，它無法辨識對話歷程記錄，例如先前的系統回合、先前的使用者回合，或先前辨識的實體。  因此，實體擷取的行為會由所有語句「共用」。  例如，如果使用者語句「我想要蘋果」在一個使用者語句中將「蘋果」標示為實體類型「水果」，則實體擷取模型會預期此語句 (「我想要蘋果」) 應一律將「蘋果」標示為「水果」。

如果實體擷取的行為不符合預期，可能的解決方式如下：

- 首先應嘗試新增更多訓練範例 - 特別是顯示常見實體上下文 (前後字組) 或例外狀況的範例
- 如果適當，請考慮將「預期的實體」屬性新增至動作。  如需詳細資訊，請參閱關於「預期的實體」的教學課程。
- 雖然使用程式碼將手動處理方式新增至 `EntityExtractionCallback` 以擷取實體是可行的，但建議在別無他法時才採用此方法，因為此方法在系統成熟時無法獲得機器學習改善的效益。

## <a name="action-selection"></a>動作選取

動作選取會使用遞迴式類神經網路，而以所有對話歷程記錄作為輸入。  因此，動作選取是可設定狀態的程序，並且可辨識先前的使用者語句、實體值與系統語句。  

學習程序會自然傾向使用某些訊號。  換句話說，如果對話學習模組可以使用「較慣用的」訊號來說明動作選取決策，則會這麼做；否則將會使用「較不慣用的」訊號。

下表列出對話學習模組中的所有訊號，以及由動作選取使用的訊號。  請注意，使用者語句中的字組順序會被忽略。

訊號 | 喜好設定 (1 = 最慣用) | 注意
--- | --- | --- 
上一個回合中的系統動作 | 1 | 
存在於目前回合中的實體 | 1 | 
這是否為第一個回合 | 1 |
目前使用者語句中字組的完全相符項目 | 2 | 
目前使用者語句中意義相近的字組 | 3 | 
上一個回合之前的系統動作 | 4 |
在目前回合之前的回合中存在的實體 | 4 | 
目前回合之前的使用者語句 | 5 | 

> [!NOTE]
> 動作選取不會採用系統動作的內容 -- 文字、卡片內容，或是 API 名稱或行為 -- 僅限系統動作的識別。  因此，變更動作的內容並不會改變動作選取模型的行為。
>
> 此外，不會使用實體的內容/值，只會使用其存在性/不存在性。

如果動作選取的行為不符合預期，可能的解決方式如下：

- 新增更多訓練對話，特別是說明應注意哪些訊號動作選取的對話。  例如，如果動作選取應優先選取某個訊號，請提供範例以說明優先處理的訊號將處於相同狀態，其他訊號則處於不同狀態。  有些序列可能需要少量訓練對話以進行學習。
- 將「必要」和「不合格」實體新增至動作定義。  這樣可以限制動作可供使用的時機，並且有助於表示商務規則和某些常識模式。 

## <a name="updates-to-models"></a>模型的更新

每當您在 UI 中新增或編輯實體、動作或訓練對話時，都會產生重新訓練實體擷取模型和動作選取模型的要求。  此要求會放到佇列上，並以非同步方式進行重新訓練。  有可用的新模型時，將會從該點開始將該模型用於實體擷取和動作選取。  此重新訓練程序通常需要約 5 秒的時間，但如果模型較複雜，或訓練服務的負載較高，時間可能會拉長。

由於訓練是以非同步方式進行的，因此您已完成的編輯可能不會立即反映。  如果實體擷取或動作選取未根據您在最後 5-10 秒所做的變更依預期運作，原因可能就是這種非同步的反映。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [預設值和界限](./cl-values-and-boundaries.md)
