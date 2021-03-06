---
title: 流量管理員端點類型 | Microsoft Docs
description: 本文說明可搭配 Azure 流量管理員使用的各類型端點
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: kumud
ms.openlocfilehash: dc76f56b6c05f22a380ff33715fe22e8c72e4891
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508442"
---
# <a name="traffic-manager-endpoints"></a>流量管理員端點
Microsoft Azure 流量管理員可讓您控制如何將網路流量分散到在不同資料中心執行的應用程式部署。 您可以在流量管理員中將每個應用程式部署設定為「端點」。 當流量管理員收到 DNS 要求時，它會選擇可用的端點在 DNS 回應中傳回。 流量管理員會根據目前的端點狀態和流量路由方法來選擇。 如需詳細資訊，請參閱 [流量管理員的運作方式](traffic-manager-how-it-works.md)。

流量管理員支援三種類型的端點：
* **Azure 端點** 用於在 Azure 中裝載的服務。
* **外部端點**IPv4/IPv6 位址的 Fqdn，或可以是內部部署的 Azure 外部，或使用不同的主機服務提供者裝載的服務使用。
* **巢狀端點** 用於合併流量管理員端點，以建立更有彈性的流量路由配置，進而支援更大型且更複雜部署的需求。

在單一流量管理員設定檔中結合不同類型的端點的方式不受限。 每個設定檔可以包含任意混合的端點類型。

下列各節會更深入說明每個端點類型。

## <a name="azure-endpoints"></a>Azure 终结点

在流量管理員中，Azure 端點用於以 Azure 為基礎的服務。 支援下列的 Azure 資源類型︰

* PaaS 雲端服務
* Web 應用程式
* Web 應用程式位置
* PublicIPAddress 資源 (可以直接或透過 Azure Load Balancer 連接至 VM)。 PublicIpAddress 必須已獲指派 DNS 名稱，才能在流量管理員設定檔中使用。

PublicIPAddress 資源是 Azure Resource Manager 資源。 它們不存在於傳統部署模型中。 因此，僅在流量管理員的 Azure Resource Manager 經驗中才支援。 透過 Resource Manager 和傳統部署模型可支援其他端點類型。

使用 Azure 端點時，流量管理員會偵測「傳統」IaaS VM、雲端服務或 Web 應用程式何時停止和啟動。 此狀態會反映在端點狀態中。 如需詳細資訊，請參閱[流量管理員端點監視](traffic-manager-monitoring.md#endpoint-and-profile-status)。 當基礎服務停止時，流量管理員不會執行端點健康情況檢查或將流量導向端點。 已停止的執行個體不會產生流量管理員計費事件。 重新啟動服務時，計費會繼續，端點也能夠接收流量。 這項偵測並不適用於 PublicIpAddress 端點。

## <a name="external-endpoints"></a>外部端點

外部端點會使用其中一個 IPv4/IPv6 位址的 Fqdn，或 Azure 外部的服務。 使用 IPv4/IPv6 位址端點可讓流量管理員無需端點的 DNS 名稱便能檢查端點的健康情況。 如此一來，流量管理員便可以在於回應中傳回該端點時，回應具有 A/AAAA 記錄的查詢。 Azure 外部的服務可以包含在內部部署託管或具有不同提供者的服務。 外部端點可以單獨使用，或在相同的流量管理員設定檔中與 Azure 端點結合使用 (指定為 IPv4 或 IPv6 位址的端點除外，因為它們只能作為外部端點)。 結合 Azure 端點與外部端點可以解決各種情況：

* 使用 Azure 以主動-主動或主動-被動容錯移轉模式，為現有的內部部署應用程式提供提升的備援性。 
* 將流量路由至沒有與其相關聯之 DNS 名稱的端點。 此外，透過移除執行第二個 DNS 查詢以取得回傳 DNS 名稱之 IP 位址的需求，來減少整體的 DNS 查詢延遲。 
* 在 Azure 中為世界各地的使用者提供更低的應用程式延遲，並將現有的內部部署應用程式延伸至其他地理位置。 如需詳細資訊，請參閱[流量管理員「效能」流量路由](traffic-manager-routing-methods.md#performance)。
* 使用 Azure 以連續方式或「高載至雲端」解決方案來因應需求遽增的情形，來為現有的內部部署應用程式提供額外容量。

在某些情況下，使用外部端點來參考 Azure 服務很實用 (如需範例，請參閱[常見問題集](traffic-manager-faqs.md#traffic-manager-endpoints))。 在此情況下，健康情況檢查是以 Azure 端點費率計費 (而不是外部端點費率)。 但是，不同於 Azure 端點，如果您停止或刪除基礎服務，健康檢查會持續計費，直到您在流量管理員中停用或刪除端點為止。

## <a name="nested-endpoints"></a>巢狀端點

巢狀端點結合多個流量管理員設定檔，以建立有彈性的流量路由配置，並支援更大型和更複雜部署的需求。 透過巢狀端點，「子」設定檔會當做端點新增至「父」設定檔。 子設定檔和父設定檔都可以包含任何類型的其他端點，包括其他巢狀設定檔。 如需詳細資訊，請參閱 [巢狀流量管理員設定檔](traffic-manager-nested-profiles.md)。

## <a name="web-apps-as-endpoints"></a>Web Apps 做為端點

將 Web Apps 設定為流量管理員中的端點時，適用其他一些考量：

1. 只有「標準」SKU 或更高的 Web Apps 才能搭配流量管理員使用。 嘗試新增較低 SKU 的 Web 應用程式會失敗。 將現有 Web 應用程式的 SKU 降級，將會導致流量管理員不再將流量傳送到該 Web 應用程式。 如需支援計劃相關的詳細資訊，請參閱 [App Service 方案](https://azure.microsoft.com/pricing/details/app-service/plans/)
2. 當端點收到 HTTP 要求時，它會使用要求中的 'host’ 標頭來決定哪個 Web 應用程式應該處理要求。 Host 標頭包含用來起始要求的 DNS 名稱，例如 'contosoapp.azurewebsites.net'。 若要讓 Web 應用程式使用不同的 DNS 名稱，此 DNS 名稱必須註冊為該應用程式的自訂網域名稱。 將 Web 應用程式端點新增為 Azure 端點時，將會自動為該應用程式註冊流量管理員設定檔 DNS 名稱。 刪除端點時，會自動移除此註冊。
3. 每個流量管理員設定檔最多可以有來自每個 Azure 區域的一個 Web 應用程式端點。 若要克服此限制，您可以將 Web 應用程式設定為外部端點。 如需詳細資訊，請參閱[常見問題集](traffic-manager-faqs.md#traffic-manager-endpoints)。

## <a name="enabling-and-disabling-endpoints"></a>启用和禁用终结点

在流量管理員中停用端點，有利於從處於維護模式或正在重新部署的端點中暫時移除流量。 一旦端點再次執行，即可重新啟用。

可透過流量管理員入口網站、PowerShell、CLI 或 REST API 來啟用和停用端點。

> [!NOTE]
> 停用 Azure 端點會與其在 Azure 中的部署狀態無關。 Azure 服務 (例如，VM 或 Web 應用程式) 即使在流量管理員中停用，仍會持續執行，且能夠接收流量。 流量可以直接定址到服務執行個體，而不必透過流量管理員設定檔 DNS 名稱。 如需詳細資訊，請參閱 [流量管理員的運作方式](traffic-manager-how-it-works.md)。

目前，每個端點接收流量的能力取決於下列因素︰

* 設定檔狀態 (啟用/停用)
* 端點狀態 (啟用/停用)
* 該端點的健康情況檢查結果

如需詳細資訊，請參閱 [流量管理員端點監視](traffic-manager-monitoring.md#endpoint-and-profile-status)。

> [!NOTE]
> 由于流量管理器是在 DNS 级别工作的，因此不可能影响任何终结点的现有连接。 當端點無法使用時，流量管理員會將新連接導向另一個可用的端點。 不過，已停用或狀況不良端點背後的主機可透過現有的連接來繼續接收流量，直到這些工作階段終止為止。 應用程式應該限制工作階段持續時間，以排光現有連接的流量。

如果設定檔中的所有端點都已停用，或設定檔本身已停用，流量管理員會傳送 'NXDOMAIN' 回應給新的 DNS 查詢。


## <a name="next-steps"></a>後續步驟

* 了解 [流量管理員的運作方式](traffic-manager-how-it-works.md)。
* 了解「流量管理員」的 [端點監視和自動容錯移轉](traffic-manager-monitoring.md)。
* 了解「流量管理員」的 [流量路由方法](traffic-manager-routing-methods.md)。
