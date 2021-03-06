---
title: 快速入門：探索 Azure 時間序列深入解析預覽版示範環境 | Microsoft Docs
description: 了解 Azure 時間序列深入解析預覽版示範環境。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: dbdbfc797d37ed38936d6cfd354383d412c6b52d
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205811"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>快速入門：探索 Azure 時間序列深入解析預覽版示範環境

本快速入門可讓您開始使用 Azure 時間序列深入解析預覽版。 透過免費的示範，您將導覽時間序列深入解析預覽版中已新增的主要功能。

此預覽版示範環境包含虛構公司 Contoso，這家公司經營兩個風力渦輪機發電廠，各有 10 個渦輪機。 每台渦輪機都有 20 個感應器，每分鐘向 Azure IoT 中樞回報資料。 感應器會收集有關天氣狀況、葉片俯仰和偏擺位置的資訊。 此外也會收集發電機效能、變速箱行為，以及安全監視器的相關資訊。

 您將了解如何使用時間序列深入解析，在 Contoso 資料中尋找可操作的見解。 此外也將進行簡短的根本原因分析，以準確地預測重大故障並執行維護工作。

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>在示範環境中來探索時間序列深入解析

時間序列深入解析預覽版總管會顯示歷史資料和根本原因分析。 開始進行之前：

1. 建立 [免費的 Azure 帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)  (如果尚未建立)。

1. 瀏覽至  [Contoso 風力發電廠示範](https://insights.timeseries.azure.com/preview/samples)環境。  

1. 如果出現提示，請使用您的 Azure 帳戶認證登入時間序列深入解析。

## <a name="work-with-historical-data"></a>使用歷史資料

1. 觀察 **Contoso Plant 1** 中的風力渦輪機 **W7**。  

    * 將檢視範圍更新為 **1/1/17 20:00 至 3/10/17 20:00 (UTC)**。
    * 選取 [Contoso Plant 1] > [W7] > [發電機系統] > [GeneratorSpeed] 感應器。 然後，檢閱產生的值。

      [![Contoso Plant 1 中的 W7](media/v2-update-quickstart/quickstart-one.png)](media/v2-update-quickstart/quickstart-one.png#lightbox)

1. 近期，Contoso 發現風力發電機 **W7** 曾經起火。 關於起火的直接成因，意見並不一致。 經仔細檢查，我們發現起火警示感應器在起火期間是啟動的。

    * 將檢視範圍更新為 **3/9/17 20:00 至 3/10/17 20:00 (UTC)**。
    * 選取 [安全系統] > [FireAlert]感應器。

      [![Contoso 發現風力渦輪機 W7 曾經起火](media/v2-update-quickstart/quickstart-two.png)](media/v2-update-quickstart/quickstart-two.png#lightbox)

1. 檢閱起火時間點前後的其他事件，以了解發生了哪些狀況。 油壓和作用中警告在起火之前都急遽升高。

    * 選取 [變槳系統] > [HydraulicOilPressure] 感應器。
    * 選取 [變槳系統] > [ActiveWarning] 感應器。

      [![檢閱同一時間點前後的其他事件](media/v2-update-quickstart/quickstart-three.png)](media/v2-update-quickstart/quickstart-three.png#lightbox)

1. 油壓和作用中警告感應器在起火之前都急遽升高。 展開顯示的時間序列，以查看其他導致起火的徵象。 兩個感應器都持續波動一段時間，而顯現持續而有安全疑慮的模式。

    * 將檢視範圍更新為 **2/24/17 20:00 至 3/10/17 20:00 (UTC)**。

      [![油壓和作用中警告感應器也急遽升高](media/v2-update-quickstart/quickstart-four.png)](media/v2-update-quickstart/quickstart-four.png#lightbox)

1. 查看兩年的歷史資料時，會發現有另一個起火事件具有相同的感應器波動。

    * 將檢視範圍更新為 **1/1/16 至 12/31/17** (所有資料)。

      [![尋找歷史模式](media/v2-update-quickstart/quickstart-five.png)](media/v2-update-quickstart/quickstart-five.png#lightbox)

透過 Azure 時間序列深入解析和感應器遙測，我們發現歷史資料中隱藏了長時間且有問題的趨勢。 透過這些新的深入解析，我們可以：

> [!div class="checklist"]
> * 說明實際發生的狀況。
> * 更正問題。
> * 設置妥善的警示通知系統。

## <a name="root-cause-analysis"></a>根本原因分析

1. 在某些情況下，必須經由複雜的分析，才能在資料中找出細微的線索。 選取 **6/25** 這個日期的風車 **W6**

    * 將檢視範圍更新為 **6/1/17 20:00 至 7/1/17 20:00 (UTC)**。
    * 然後，選取 [Contoso Plant 1] > [W6] > [安全系統] > [VoltageActuatorSwitchWarning] 感應器。

      [![更新檢視範圍並選取 W6](media/v2-update-quickstart/quickstart-six.png)](media/v2-update-quickstart/quickstart-six.png#lightbox)

1. 警告指出發電機輸出的電壓有問題。 以目前的時間間隔來看，發電機的整體電力輸出均在正常參數內運作。 藉由增加時間間隔，將會形成另一種模式，而出現明確的下降。

    * 移除 **VoltageActuatorSwitchWarning** 感應器。
    * 選取 [發電機系統] > [ActivePower] 感應器。
    * 將時間間隔更新為 **3d**。

      [![將時間間隔更新為 3d](media/v2-update-quickstart/quickstart-seven.png)](media/v2-update-quickstart/quickstart-seven.png#lightbox)

1. 藉由擴充時間範圍，我們可以判斷問題是否已停止或繼續。

    * 將時間範圍延伸至 60 天。

      [![將時間範圍延伸至 60 天](media/v2-update-quickstart/quickstart-eight.png)](media/v2-update-quickstart/quickstart-eight.png#lightbox)

1. 我們可以新增其他感應器資料點，以提供更詳盡的內容。 我們可以檢視的感應器愈多，就愈能充分了解問題的本質。 我們將置放標記，以查看實際的值。 

    * 選取 [發電機系統] > [GridVoltagePhase1]、[GridVoltagePhase2] 和 [GridVoltagePhase3] 感應器。
    * 在可見區域的最後一個資料點上置放標記。

      [![置放標記](media/v2-update-quickstart/quickstart-nine.png)](media/v2-update-quickstart/quickstart-nine.png#lightbox)

    有三個電壓感應器同等地在正常參數內運作。 看來問題出在 **GridVoltagePhase3** 感應器上。

1. 藉由新增內容相關度高的資料，就更能看出階段 3 的下降就是問題所在。 現在，我們已可將此問題交由維護團隊處理，並為其提供警告成因的可靠線索。  

    * 更新顯示畫面，使所有**發電機系統**感應器以相同的圖表比例重疊。

       [![更新顯示畫面以包含所有項目](media/v2-update-quickstart/quickstart-ten.png)](media/v2-update-quickstart/quickstart-ten.png#lightbox)

## <a name="next-steps"></a>後續步驟

您已準備好建立自己的時間序列深入解析預覽版環境：

> [!div class="nextstepaction"]
> [規劃時間序列深入解析預覽版環境](time-series-insights-update-plan.md)

了解如何瀏覽示範及其功能：

> [!div class="nextstepaction"]
> [時間序列深入解析預覽版總管](time-series-insights-update-explorer.md)
