---
title: 規劃 Service Fabric 叢集容量 | Microsoft Docs
description: Service Fabric 叢集容量規劃考量。 Nodetypes、Operations、持久性和可靠性層級
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: chackdan
ms.openlocfilehash: bd76658c939496f27bf3751060c18d17968acd15
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60386786"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Service Fabric 叢集容量規劃考量
對於任何生產部署而言，容量規劃都是一個很重要的步驟。 以下是一些您在該程序中必須考量的項目。

* 您的叢集一開始所需的節點類型的數目
* 每個節點類型 (大小、主要、網際網路對向、VM 數目等) 的屬性
* 叢集的可靠性和持久性的特性

> [!NOTE]
> 您在規劃期間至少應該檢閱所有「不允許」升級的原則值。 這可確保您適當地設定值，並將稍後因無法變更系統組態設定所造成的叢集減弱情況降低。 
> 

讓我們簡短地檢閱各個項目。

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>您的叢集一開始所需的節點類型的數目
首先，您必須找出您要建立之叢集的用途。  您計劃將哪些類型的應用程式部署到此叢集中？ 如果您不清楚叢集的用途，您很可能還未準備好進入容量規劃程序。

建立您的叢集一開始所需的節點類型的數目。  每個節點類型都會對應到虛擬機器擴展集。 然後每個節點類型可以獨立相應增加或相應減少，可以開啟不同組的連接埠，並可以有不同的容量度量。 因此，節點類型數目的決定基本上可歸結為下列考量︰

* 您的應用程式是否有多個服務，而且其中是否有任何服務必須是公開或網際網路對向的服務？ 一般應用程式包含可接收用戶端輸入的前端閘道服務，以及一或多個與前端服務溝通的後端服務。 因此，在此情況下，您最終會有至少兩個節點類型。
* 您 (構成應用程式) 的服務是否有不同的基礎結構需求，例如，更多的 RAM 或更高的 CPU 週期？ 例如，讓我們假設您想要部署的應用程式包含前端服務和後端服務。 前端服務可以在容量較小 (D2 之類的 VM 大小)，且擁有可連線至網際網路之連接埠的 VM 上執行。  不過，需要大量計算的後端服務必須在容量較大 (D4、D6、D15 的 VM 大小)，且不連線至網際網路的 VM 上執行。
  
  在此範例中，您可以決定將所有服務都放在一個節點類型上，但我們建議您將它們放在包含兩個節點類型的叢集上。  這可讓每個節點類型都有不同的屬性，例如，網際網路連線或 VM 大小。 VM 的數目也可以單獨調整。  
* 您無法預測未來，因此請利用您所知道的事實，選擇您的應用程式一開始所需的節點類型的數目。 您之後都可以新增或移除節點類型。 Service Fabric 叢集必須至少有一個節點類型。

## <a name="the-properties-of-each-node-type"></a>每個節點類型的屬性
**節點類型**就像是雲端服務中的角色。 可用來定義定義 VM 的大小、VM 的數目，以及 VM 的屬性。 在 Service Fabric 叢集中定義的每個節點類型都會對應到一個[虛擬機器擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)。  
每個節點類型都是獨特的擴展集，且可以獨立相應增加或相應減少，可以開啟不同組的連接埠，並有不同的容量計量。 如需節點類型與虛擬機器擴展集之間關聯性、如何透過 RDP 連到其中一個執行個體、如何開啟新連接埠等等的詳細資訊，請參閱 [Service Fabric 叢集節點類型](service-fabric-cluster-nodetypes.md)。

Service Fabric 叢集可以包含一個以上的節點類型。 在這種情況下，叢集包含一個主要節點類型和一或多個非主要節點類型。

針對 SF 應用程式，單一節點類型無法可靠地調整為每個虛擬機器擴展集超過 100 個節點；若要可靠地達到 100 個以上的節點，您必須新增更多虛擬機器擴展集。

### <a name="primary-node-type"></a>主要節點類型

Service Fabric 系統服務 (例如，叢集管理員服務或映像存放區服務) 會放在主要節點類型。 

![有兩個節點類型的叢集螢幕擷取畫面][SystemServices]

* 主要節點類型的 **VM 大小下限**取決於您選擇的**持久性層級**。 預設持久性層級為 Bronze。 如需詳細資訊，請參閱[叢集的持久性特性](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)。  
* 主要節點類型的 **VM 數目下限**取決於您選擇的**可靠性層級**。 預設可靠性層級為 Silver。 如需詳細資訊，請參閱[叢集的可靠性特性](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster)。  

在 Azure Resource Manager 範本中，主要節點類型會以 `isPrimary` 屬性設定於[節點類型定義](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object)之下。

### <a name="non-primary-node-type"></a>非主要節點類型

在具有多個節點類型的叢集中，有一個主要節點類型，其餘則是非主要節點類型。

* 非主要節點類型的 **VM 大小下限**取決於您選擇的**持久性層級**。 預設持久性層級為 Bronze。 如需詳細資訊，請參閱[叢集的持久性特性](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)。  
* 非主要節點類型的 **VM 數目下限**為 1。 不過，您應該根據您想要在這個節點類型中執行的應用程式/服務的複本數目，選擇這個數目。 部署叢集之後，節點類型中的 VM 數目可能會增加。

## <a name="the-durability-characteristics-of-the-cluster"></a>叢集的持久性特性
持久性層級用來向系統指示您的 VM 對於基本 Azure 基礎結構所擁有的權限。 在主要節點類型中，此權限可讓 Service Fabric 暫停會影響系統服務及具狀態服務的仲裁需求的任何 VM 層級基礎結構要求 (例如，VM 重新開機、VM 重新安裝映像，或 VM 移轉)。 在非主要節點類型中，此權限可讓 Service Fabric 暫停會影響具狀態服務之仲裁需求的任何 VM 層級基礎結構要求 (例如，VM 重新開機、VM 重新安裝映像和 VM 移轉)。

| 耐久性層級  | 所需的 VM 數目下限 | 支援的 VM SKU                                                                  | 您對虛擬機器擴展集所做的更新                               | Azure 所起始的更新和維護                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Gold             | 5                              | 單一客戶專用的完整節點 SKU (例如 L32s、GS5、G5、DS15_v2、D15_v2) | 可以延遲到 Service Fabric 叢集核准為止 | 每一 UD 可以暫停 2 小時，讓複本有額外時間從先前的失敗中復原 |
| Silver           | 5                              | 單一核心或更多核心的 VM                                                        | 可以延遲到 Service Fabric 叢集核准為止 | 無法延遲很長的期間                                                    |
| Bronze           | 1                              | 全部                                                                                | Service Fabric 叢集不會延遲           | 無法延遲很長的期間                                                    |

> [!WARNING]
> 執行 Bronze 持久性的節點類型「沒有權限」。 這表示不會停止或延遲對您的無狀態工作負載造成影響的基礎結構作業 (可能會影響工作負載)。 針對僅執行無狀態工作負載的節點類型，只能使用 Bronze。 對於生產工作負載，建議執行 Silver 或以上的層級。 
> 
> 無論是任何持久性層級，虛擬機器擴展集上的[解除配置](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate)作業都會終結叢集

**使用 Silver 或 Gold 持久性層級的優點**
 
- 減少相應縮小作業的必要步驟數目 (也就是會自動呼叫節點停用及 Remove-ServiceFabricNodeState)。
- 降低因客戶起始的就地 VM SKU 變更作業或 Azure 基礎結構作業而導致的資料遺失風險。

**使用 Silver 或 Gold 持久性層級的缺點**
 
- 將您的虛擬機器擴展集部署至其他相關 Azure 資源的速度，可能會因來自您叢集中或是基礎結構層級的問題，而造成延遲、逾時，或是完全封鎖。 
- 因 Azure 基礎結構作業期間的自動化節點停用，而增加[複本生命週期事件](service-fabric-reliable-services-lifecycle.md) (例如主要交換) 的數目。
- 當 Azure 平台軟體更新或硬體維護活動正在進行時，使節點停止服務一段期間。 您可能會在這些活動期間看到含有正在停用/已停用狀態的節點。 這會暫時減少叢集的容量，但應該不會影響叢集或應用程式的可用性。

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>使用 Silver 或 Gold 持久性層級的建議時機

針對所有裝載您預期會經常進行相應縮小 (減少 VM 執行個體計數)，且偏好延遲部署作業並減少容量以簡化相應縮小作業之具狀態服務的節點類型，使用 Silver 或 Gold 持久性。 相應放大案例 (新增 VM 執行個體) 並不會影響您對耐久性層級的選擇，只有相應縮小才會。

### <a name="changing-durability-levels"></a>變更持久性層級
- 持久性層級為 Silver 或 Gold 的節點類型無法降級為 Bronze。
- 從 Bronze 升級至 Silver 或 Gold 可能需要幾小時的時間。
- 變更持久性層級時，請務必同時在虛擬機器擴展集資源的 Service Fabric 擴充設定中，和 Service Fabric 叢集資源的節點類型定義中更新層級。 這些值必須相符。

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>針對您已設定為 Silver 或 Gold 耐久性層級之節點類型的作業建議。

- 使叢集和應用程式持續保持良好的狀況，並確保應用程式會及時回應所有[服務複本生命週期事件](service-fabric-reliable-services-lifecycle.md) (例如當組建中的複本陷入停滯)。
- 採用更安全的方式來進行 VM SKU 變更 (相應增加/減少)：更改虚拟机规模集的 VM SKU 在本质上是不安全的操作，应尽量避免。 以下為您可以遵循以避免發生常見問題的程序。
    - **对于非主节点类型：** 建议创建新的虚拟机规模集，修改服务放置约束以包括新的虚拟机规模集/节点类型，然后将旧的虚拟机规模集实例计数降低到零，一次一个节点（这是为了确保删除节点不会影响群集的可靠性）。
    - **对于主节点类型：** 建议不要更改主节点类型的 VM SKU。 不支援變更主要節點類型的 SKU。 如果是基於容量的原因而需要新的 SKU，我們建議新增更多執行個體。 如果不能的話，從舊叢集建立新的叢集並[還原應用程式狀態](service-fabric-reliable-services-backup-restore.md) (如果適用的話)。 您不需要還原任何系統服務狀態，它們會在您部署應用程式到新叢集時重新建立。 如果您在叢集上執行無狀態應用程式，請將您的應用程式部署至新叢集。  您不需還原任何項目。 如果您決定採取不支援的做法，並想要變更 VM SKU，請修改虛擬機器擴展集模型定義以反映新的 SKU。 如果您的叢集只有單一節點類型，請確保所有具狀態應用程式會及時回應所有[服務複本生命週期事件](service-fabric-reliable-services-lifecycle.md) (例如當組建中的複本陷入停滯)，且您的服務複本重建期間為少於五分鐘 (針對 Silver 持久性層級)。 
    
- 針對所有啟用 Gold 或 Silver 持久性的虛擬機器擴展集維持至少五個節點。
- 持久性層級為 Silver 或 Gold 的每個虛擬機器擴展集，都必須對應到它自己在 Service Fabric 叢集中的節點類型。 將多個虛擬機器擴展集對應到單一節點類型，將會使 Service Fabric 叢集與 Azure 基礎結構之間的協調無法正常運作。
- 請勿隨機刪除 VM 執行個體，而一律使用虛擬機器擴展集的相應減少功能。 刪除隨機的 VM 執行個體可能會在 UD 和 FD 上的 VM 執行個體中產生不平衡。 此不平衡可能會嚴重影響系統對服務執行個體/服務複本正確進行負載平衡的能力。
- 如果您使用自動調整功能，則請設定規則使系統一次只會針對一個節點進行相應縮小 (移除 VM 執行個體)。 一次相應減少超過一個執行個體並不安全。
- 如果刪除或取消配置主要節點類型上的 VM，絕對不能將已配置的 VM 計數減少至可靠性層級所需數量以下。 在持久性層級為 Silver 或 Gold 的擴展集中，這些作業將會無限期地遭到封鎖。

## <a name="the-reliability-characteristics-of-the-cluster"></a>叢集的可靠性特性
可靠性層級用來設定您想要在此叢集中的主要節點類型上執行的系統服務複本數目。 副本数越大，群集中的系统服务越可靠。  

可靠性層級可以採用以下的值：

* Platinum - 執行包含七個目標複本集的系統服務
* Gold - 執行包含七個目標複本集的系統服務
* Silver - 執行包含五個目標複本集的系統服務 
* Bronze - 執行包含三個目標複本集的系統服務

> [!NOTE]
> 您選擇的可靠性層級會決定您的主要節點類型必須具備的節點數目下限。 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>可靠性層級的建議

當您增加或減少叢集大小 (所有節點類型的 VM 執行個體總和) 時，必須將叢集的可靠性從一個層級更新到另一個層級。 如此一來就會觸發變更系統服務複本集計數所需的叢集升級。 請先等候升級完成，再對叢集進行任何其他變更，例如新增節點。  您可以在 Service Fabric Explorer 上或執行 [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) 監視升級的進度

以下是選擇可靠性層級的建議。  種子節點數目也會設定為適用於可靠性層的節點數目下限。  例如，針對具有 Gold 可靠性的叢集，會有 7 個種子節點。

| **叢集節點數目** | **可靠性層級** |
| --- | --- |
| 1 |不指定「可靠性層級」參數，系統會進行計算 |
| 3 |Bronze |
| 5 或 6|Silver |
| 7 或 8 |Gold |
| 9 以上 |Platinum |

## <a name="primary-node-type---capacity-guidance"></a>主要節點類型 - 容量指引

以下是規劃主要節點類型容量的指引：

- **要在 Azure 中运行任何生产工作负荷的 VM 实例数：** 必须将最小主节点类型大小指定为 5，将可靠性层指定为“银级”。  
- **要在 Azure 中執行測試工作負載的 VM 執行個體數目︰** 您可以指定最小的主要節點類型大小 1 或 3。 以特殊組態執行的一個節點叢集，因此不支援該叢集的相應放大。 一個節點叢集有沒有可靠性且在您的 Resource Manager 範本中，您必須移除/不指定該組態 (不設定組態值並不足夠)。 如果您透過入口網站設定一個節點叢集，則會自動處理組態。 一和三個節點叢集不支援執行生產工作負載。 
- **VM SKU：** 主节点类型是运行系统服务的位置，因此，在为它选择 VM SKU 时，必须考虑到你要在群集中放置的总峰值负载。 我用比喻來闡明我的意思 - 將主要節點類型想像成您的「肺」，它供應氧氣給您的腦，如果腦沒有足夠的氧氣，您的身體就會出問題。 

由於叢集的容量需求取決於您打算在叢集中執行的工作負載，因此我們無法為您特定的工作負載提供確切的指引，但以下是概括性的指引，可協助您開始進行

生產工作負載： 

- 建議將叢集主要 NodeType 專用於系統服務，並使用放置限制式，將應用程式部署到次要 NodeTypes。
- 建議的 VM SKU 是標準 D3 或標準 D3_V2 或對等項目，並搭配至少 14 GB 的本機 SSD。
- 支援使用的最小 VM SKU 是標準 D1 或標準 D1_V2 或對等項目，並搭配至少 14 GB 的本機 SSD。 
- 14-GB 的本機 SSD 是最低需求。 我們建議的最小值為 50 GB。 針對您的工作負載，特別是在執行 Windows 容器時，則需要較大的磁碟。 
- 局部核心 VM SKU 不支援生產工作負載，例如標準 A0。
- 基於效能理由，標準 A1 SKU 不支援生產工作負載。
- 不支援低優先順序的 VM。

> [!WARNING]
> 在執行的叢集上變更主要節點虛擬機器 SKU 大小是一種調整作業，且會記錄在[虛擬機器擴展集相應放大](virtual-machine-scale-set-scale-node-type-scale-out.md)文件中。

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>非主要節點類型 - 具狀態工作負載的容量指引

本指引適用於使用 Service Fabric [可靠集合或可靠動作項目](service-fabric-choose-framework.md)的具狀態工作負載 (在非主要節點類型中執行)。

**VM 实例数：** 对于有状态生产工作负荷，建议使用最小值和目标副本计数 5 运行它们。 這表示在穩定狀態下，最後每個容錯網域和升級網域中會有一個複本 (來自複本集)。 主要節點類型的整個可靠性層級概念，是為系統服務指定此設定。 因此，相同的考量也適用於您的具狀態服務。

因此，對於生產工作負載，如果您執行具狀態工作負載，建議的最小非主要節點類型大小為 5。

**VM SKU：** 这是运行应用程序服务的节点类型，因此，为其选择的 VM SKU 必须将你计划在每个节点中放置的峰值负荷考虑在内。 節點類型的容量需求取決於您打算在叢集中執行的工作負載，因此，我們無法為您的特定工作負載提供確切的指引，但以下是可協助您開始進行的概括性指引

生產工作負載 

- 建議的 VM SKU 是標準 D3 或標準 D3_V2 或對等項目，並搭配至少 14 GB 的本機 SSD。
- 支援使用的最小 VM SKU 是標準 D1 或標準 D1_V2 或對等項目，並搭配至少 14 GB 的本機 SSD。 
- 生产工作负荷不支持不完整的核心 VM SKU，例如标准 A0。
- 基於效能理由，標準 A1 SKU 不支援生產工作負載。

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>非主要節點類型 - 無狀態工作負載的容量指引

這個指引適用於您在非主要節點類型上執行的無狀態工作負載。

**VM 实例数：** 对于无状态生产工作负荷，支持的最小非主节点类型大小为 2。 這可讓您執行應用程式的兩個無狀態執行個體，在遺失 VM 執行個體的情況下，您的服務仍可繼續運作。 

**VM SKU：** 这是运行应用程序服务的节点类型，因此，为其选择的 VM SKU 必须将你计划在每个节点中放置的峰值负荷考虑在内。 節點類型的容量需求取決於您打算在叢集中執行的工作負載。 我們無法提供適用於您特定工作負載的確切指引。  不過，以下是可協助您開始使用的概括性指引。

生產工作負載 

- 建議的 VM SKU 是標準 D3 或標準 D3_V2 或對等項目。 
- 支援使用的最小 VM SKU 是標準 D1 或標準 D1_V2 或對等項目。 
- 局部核心 VM SKU 不支援生產工作負載，例如標準 A0。
- 基於效能理由，標準 A1 SKU 不支援生產工作負載。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>後續步驟
一旦您完成容量規劃並設定叢集之後，請閱讀︰

* [Service Fabric 叢集安全性](service-fabric-cluster-security.md)
* [Service Fabric 叢集調整](service-fabric-cluster-scaling.md)
* [災害復原規劃](service-fabric-disaster-recovery.md)
* [Nodetypes 與虛擬機器擴展集的關聯性](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
