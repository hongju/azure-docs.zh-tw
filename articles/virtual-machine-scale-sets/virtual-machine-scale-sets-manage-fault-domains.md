---
title: 管理 Azure 虛擬機器擴展集中的容錯網域 | Microsoft Docs
description: 了解如何在建立虛擬機器擴展集時選擇正確的 FD 數目。
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: drewm
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: rajraj
ms.openlocfilehash: bab264769576b6e5478236c452d7de920d887c1a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60617974"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>為虛擬機器擴展集選擇正確的容錯網域數目
在沒有任何區域 (zone) 的 Azure 區域 (region) 中，預設會建立具有五個容錯網域的虛擬機器擴展集。 若為支援區域性虛擬機器擴展集部署的區域 (region)，每個區域 (zone) 容錯網域計數的預設值為 1。 在此情況下，FD = 1 表示屬於擴展集的 VM 執行個體會儘可能分散於許多機架。

您也可以考慮讓擴展集容錯網域數目與受控磁碟容錯網域數目保持一致。 如果整個受控磁碟容錯網域停止運作，此一致狀態有助於防止仲裁遺失。 您可將 FD 計數設定為小於或等於每個區域中可用的受控磁碟容錯網域數目。 請參考這份[文件](../virtual-machines/windows/manage-availability.md)，了解各區域的受控磁碟容錯網域數目。

## <a name="rest-api"></a>REST API
您可以將 `properties.platformFaultDomainCount` 屬性設定為 1、 2 或 3 (如未指定，預設值為 5)。 請參考[這裡](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)的 REST API 文件。

## <a name="azure-cli"></a>Azure CLI
您可以將 `--platform-fault-domain-count` 參數設定為 1、 2 或 3 (如未指定，預設值為 5)。 請參考[這裡](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)的 Azure CLI 文件。

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

建立及設定所有擴展集資源和 VM 需要幾分鐘的時間。

## <a name="next-steps"></a>後續步驟
- 深入了解 Azure 環境的[可用性和備援功能](../virtual-machines/windows/regions-and-availability.md)。
