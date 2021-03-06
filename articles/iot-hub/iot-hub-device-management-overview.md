---
title: 透過 Azure IoT 中樞進行裝置管理的概觀 | Microsoft Docs
description: Azure IoT 中樞的裝置管理概觀︰企業裝置生命週期及裝置管理模式，例如重新啟動、恢復出廠預設值、韌體更新、設定、裝置對應項、查詢、作業。
author: bzurcher
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
origin.date: 08/24/2017
ms.date: 10/29/2018
ms.author: v-yiso
ms.openlocfilehash: bdc55af23568b5785a831e81f352400c728c902e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60400917"
---
# <a name="overview-of-device-management-with-iot-hub"></a>IoT 中樞的裝置管理概觀

Azure IoT 中樞提供的功能和擴充性模型，可讓裝置與後端開發人員建置穩健的裝置管理解決方案。 裝置包羅萬象，從受限的感應器和單一用途的微控制器，到可路由傳送裝置群組通訊的強大閘道都是其中一份子。  此外，各產業 IoT 操作員的使用案例和需求大不相同。  儘管有此差異，IoT 中樞的裝置管理會提供一些功能、模式和程式碼程式庫，來滿足各種裝置和使用者的需求。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

建立成功企業 IoT 解決方案的關鍵在於，針對操作員該如何持續管理其裝置集合提供策略。 IoT 操作員需要簡單可靠的工具和應用程式，讓他們能夠專注於作業中更為重要的層面上。 本文提供：

- Azure IoT 中樞裝置管理方法的簡短概觀。
- 常見裝置管理原則的說明。
- 裝置生命週期的說明。
- 常見裝置管理模式的概觀。

## <a name="device-management-principles"></a>裝置管理原則
IoT 本身伴隨著一組獨特的管理挑戰，因此每個企業級解決方案都必須符合下列原則︰

![裝置管理原則圖形](media/iot-hub-device-management-overview/image4.png)

- **规模和自动化**：IoT 解决方案需要可以自动执行日常任务的简单工具，使相对数量少的操作人员可以管理数百万台设备。 每一天，操作員無不期望能夠從遠端大量處理裝置作業，並且只在發生需要他們直接關注的問題時才接獲通知。

- **开放性和兼容性**：此设备生态系统的多样性超乎寻常。 管理工具必須經過量身打造，才能配合數量眾多的裝置類別、平台和通訊協定。 操作員必須能夠支援許多類型的裝置，不論是最受限的內嵌單一處理晶片，乃至功能完整而強大的電腦。
- **上下文感知**：IoT 环境是动态的，不断变化。 因此服務可靠性非常重要。 裝置管理作業必須考量下列因素，以確保維護停機時間不會影響重要的商業運作或造成危險情況︰
    * SLA 維護期間
    * 網路和電源狀態
    * 使用中狀況
    * 裝置地理位置
- **为许多角色提供服务**：必须为 IoT 操作角色的独特工作流和进程提供支持。 操作人員必須和諧地配合內部 IT 部門指定的限制。  他們也必須找出向主管和其他商務管理角色呈現即時裝置作業資訊的永續方式。

## <a name="device-lifecycle"></a>裝置的生命週期
有一組通用於所有企業 IoT 專案的一般裝置管理階段。 在 Azure IoT 中，裝置生命週期內有五個階段︰

![Azure IoT 裝置生命週期的五個階段︰計劃、佈建、設定、監視、淘汰](./media/iot-hub-device-management-overview/image5.png)

在上述每個階段內，應滿足數個裝置操作員需求才能提供完整的解決方案︰

* **计划**：使操作员能够创建可让他们轻松且精确地进行查询的设备元数据方案，并针对一组设备进行批量管理操作。 您可以使用裝置對應項，以標記和屬性的形式來儲存此裝置中繼資料。
  
    進階閱讀： 
    * [開始使用攣生裝置](iot-hub-node-node-twin-getstarted.md)
    * [了解攣生裝置](iot-hub-devguide-device-twins.md)
    * [如何使用裝置對應項屬性](tutorial-device-twins.md)
    * [IoT 解決方案內的裝置設定最佳做法](iot-hub-configuration-best-practices.md)

* **预配**：安全地为 IoT 中心预配新设备，使操作员能够立即发现设备功能。  使用 IoT 中樞身分識別登錄來建立富有彈性的裝置身分識別與認證，並利用作業 (Job) 大量執行此作業 (Operation)。 建置一些裝置，經由裝置對應項中的裝置屬性來報告其功能和狀況。
  
    進階閱讀： 
    * [管理裝置身分識別](iot-hub-devguide-identity-registry.md)
    * [大量管理裝置身分識別](iot-hub-bulk-identity-mgmt.md)
    * [如何使用裝置對應項屬性](tutorial-device-twins.md)
    * [IoT 解決方案內的裝置設定最佳做法](iot-hub-configuration-best-practices.md)
    * [Azure IoT 中樞裝置佈建服務](https://azure.microsoft.com/documentation/services/iot-dps)

* **設定**：在确保正常运行和安全的同时，便于对设备进行批量配置更改和固件更新。 使用所需的屬性或透過直接方法和廣播作業，大量執行這些裝置管理作業。
  
    進階閱讀：
    * [如何使用裝置對應項屬性](tutorial-device-twins.md)
    * [大規模設定和監視 IoT 裝置](iot-hub-auto-device-config.md)
    * [IoT 解決方案內的裝置設定最佳做法](iot-hub-configuration-best-practices.md)

* **监视**：监视总体设备集合运行状况和正在进行的操作的状态，并针对可能需要操作员注意的问题向操作员发出警报。  套用裝置對應項，可讓裝置報告更新作業的即時作業狀況和狀態。 建置強大的儀表板報告，以使用裝置對應項查詢來呈現最即時的問題。
  
    進階閱讀： 
    * [如何使用裝置對應項屬性](tutorial-device-twins.md)
    * [裝置對應項、作業和訊息路由的 IoT 中樞查詢語言](iot-hub-devguide-query-language.md)
    * [大規模設定和監視 IoT 裝置](iot-hub-auto-device-config.md)
    * [IoT 解決方案內的裝置設定最佳做法](iot-hub-configuration-best-practices.md)

* **停用**：在发生故障或完成升级周期后（或在服务生存期结束时）更换或停用设备。  如果實體裝置正被取代，則使用裝置對應項來維護裝置資訊，若正在淘汰中則加以封存。 使用 IoT 中樞身分識別登錄，安全地撤銷裝置身分識別與認證。
  
    進階閱讀： 
    * [如何使用裝置對應項屬性](tutorial-device-twins.md)
    * [管理裝置身分識別](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>裝置管理模式

IoT 中樞可實現下列這套裝置管理模式。 [裝置管理教學課程](iot-hub-node-node-device-management-get-started.md)會更詳細地說明如何擴充這些模式來符合確切的案例，以及如何根據這些核心範本來設計新模式。

* **重启**：后端应用通过直接方法通知设备它已开始重启。  裝置會使用報告的屬性來更新裝置的重新開機狀態。
  
    ![裝置管理重新啟動模式圖形](./media/iot-hub-device-management-overview/reboot-pattern.png)

* **恢复出厂设置**：后端应用通过直接方法通知设备它已开始恢复出厂设置。 裝置會使用報告的屬性來更新裝置的恢復出廠預設值狀態。
  
    ![裝置管理恢復出廠預設值模式圖形](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **設定**：后端应用使用所需属性来配置设备上运行的软件。 裝置會使用報告的屬性來更新裝置的組態狀態。
  
    ![裝置管理設定模式圖形](./media/iot-hub-device-management-overview/configuration-pattern.png)

* **固件更新**：后端应用根据自动设备管理配置来选择接收更新的设备、指示设备在哪里查找更新，以及监视更新过程。 裝置會起始多步驟程序，以下載、驗證和套用韌體映像，並於重新連線至 IoT 中樞服務之前將裝置重新開機。 透過多步驟程序，裝置會使用報告的屬性來更新裝置的進度與狀態。
  
    ![裝置管理韌體更新模式圖形](media/iot-hub-device-management-overview/fwupdate-pattern.png)

* **报告进度和状态**：解决方案后端在一组设备上运行设备孪生查询，报告设备上运行的操作的状态和进度。
  
    ![裝置管理報告進度和狀態模式圖形](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="next-steps"></a>後續步驟
IoT 中樞針對裝置管理所提供的功能、模式和程式碼程式庫，可讓您建立 IoT 應用程式，以滿足企業 IoT 操作員在裝置生命週期內各階段的需求。

若要繼續了解 IoT 中樞內的裝置管理功能，請參閱[開始使用裝置管理](iot-hub-node-node-device-management-get-started.md)教學課程。