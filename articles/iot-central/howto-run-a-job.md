---
title: 在 Azure IoT Central 應用程式中建立及執行工作 | Microsoft Docs
description: Azure IoT Central 工作可讓您執行大量裝置管理功能，例如更新裝置屬性、設定或執行命令。
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 03/18/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: ec7033719316bb186408ea78f6dabac43c383491
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60519275"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>在 Azure IoT Central 應用程式中建立及執行工作

您可以使用 Microsoft Azure IoT Central 利用工作來大規模管理您的已連線裝置。 工作可讓您執行大量更新裝置內容、 設定和命令。 這篇文章會引導您如何開始使用您自己的應用程式中的工作。

## <a name="create-and-run-a-job"></a>建立及執行工作

本節說明如何建立及執行工作。 它說明如何增加多個 refrigerated 販賣的風扇速度。

1. 從瀏覽窗格中瀏覽至 [工作]。

1. 選取  **+ 新增**來建立新的作業。

    ![建立新工作](./media/howto-run-a-job/createnewjob.png)

1. 輸入名稱和描述來識別您要建立的工作。

1. 選取您想要套用至您工作的裝置集合。 選取該裝置設定之後，您會看到右邊填入 裝置集合中的裝置。 如果您選取損毀的裝置集合時，沒有任何裝置顯示，而且您會看到您的裝置集已損毀的訊息。

1. 接下來，選擇要定義 （設定、 屬性或命令） 的作業類型。 選取  **+** 旁邊的作業類型選取並加入您的作業。

    ![設定工作](./media/howto-run-a-job/configurejob.png)

1. 在右側，選擇您想要執行作業的裝置。 選取頂端的核取方塊，所有的裝置會選取整個裝置集合中。 選取核取方塊，接近**名稱**，會選取目前頁面上的所有裝置。

1. 選取您的裝置之後, 選擇**執行**或是**儲存**。 作業現在會出現在您的主要**作業**頁面。 這個檢視中，您可以看到您目前正在執行的作業和任何先前執行作業的記錄。 您執行的作業一律會顯示在清單頂端。 可以隨時重新繼續編輯，或執行開啟已儲存的工作。

    ![檢視工作](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > 您可以檢視您先前執行的作業記錄最多 30 天。

1. 若要取得您工作的概觀，請選取作業，以檢視從清單中。 本概觀包含工作詳細資料、 裝置和裝置狀態的值。 本概觀中，您也可以從選取**下載工作詳細資料**下載.csv 檔案的作業詳細資料，包括裝置和其狀態的值。 這項資訊可用於疑難排解。

    ![檢視裝置狀態](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>停止執行中的工作

若要停止執行中的工作，請選取它，並選擇**停止**面板上。 工作狀態會變更以反映作業已停止。

   ![停止作業](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>執行已停止的作業

若要執行的作業，目前已停止，選取 已停止的工作。 選擇**執行**面板上。 工作狀態會變更以反映工作現在正在執行一次。

   ![繼續的作業](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>複製工作

若要複製已建立的現有作業，請從 [主要工作] 頁面中選取它，然後選取**複製**。 工作組態的新複本時，會開啟您要編輯的。 您可以儲存或執行新的工作。 如果任何變更已對您所選的裝置集合，它們被反映在供您編輯此複製作業。

   ![複製作業](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>檢視工作狀態

建立作業之後，**狀態**與作業的最新狀態訊息的資料行更新。 下表列出可能的狀態值：

| 狀態訊息       | 狀態意義                                          |
| -------------------- | ------------------------------------------------------- |
| Completed            | 此工作已在所有裝置上執行。              |
| Failed               | 此工作失敗而未在裝置上完整執行。  |
| Pending              | 此工作尚未尚未開始執行的裝置上。         |
| 執行中              | 此工作目前正在裝置上執行。             |
| 已停止              | 此工作已被使用者手動停止。           |

狀態訊息的後面是作業中的裝置的概觀。 下表列出可能的裝置狀態的值：

| 狀態訊息       | 狀態意義                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Succeeded            | 在成功執行作業的裝置數目。       |
| Failed               | 此工作已失敗上執行的裝置數目。       |

### <a name="view-the-device-status"></a>檢視裝置狀態

若要檢視作業和所有受影響的裝置的狀態，請選取 [工作]。 若要下載.csv 檔案，其中包含作業的詳細資訊，包括裝置和其狀態的值，清單選取**下載作業詳細資料**。 每個裝置名稱旁邊，您會看到下列狀態訊息的其中一個：

| 狀態訊息       | 狀態意義                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Completed            | 工作已在此裝置上執行。                                     |
| Failed               | 工作在此裝置上執行失敗。 錯誤訊息會顯示更多的資訊。  |
| Pending              | 此裝置上還未執行作業。                                   |

> [!NOTE]
> 如果裝置已遭刪除，您無法選取裝置，然後它會顯示為已刪除的裝置識別碼。

## <a name="next-steps"></a>後續步驟

既然您已了解如何建立 Azure IoT Central 應用程式中的工作，以下是一些後續步驟：

- [使用裝置集合](howto-use-device-sets.md)
- [管理您的裝置](howto-manage-devices.md)
- [建立裝置範本版本](howto-version-devicetemplate.md)
