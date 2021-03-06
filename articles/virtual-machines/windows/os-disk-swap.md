---
title: 使用 PowerShell 交換 Azure VM 的作業系統磁碟 | Microsoft Docs
description: 使用 PowerShell 變更 Azure 虛擬機器所使用的作業系統磁碟。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: 73aab0750d97981d6684d04415683435bbd28797
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2019
ms.locfileid: "55980409"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>使用 PowerShell 變更 Azure VM 所使用的作業系統磁碟

如果您目前有虛擬機器，但想要將磁碟交為備份磁碟或另一個作業系統 磁碟，則可使用 Azure PowerShell 來交換作業系統磁碟。 您不需要刪除及重新建立虛擬機器。 甚至可以使用另一個資源群組中的受控磁碟，只要該磁碟並非使用中即可。

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

必須停止\解除配置虛擬機器，然後才能使用不同受控磁碟的資源識別碼取代該受控磁碟的資源識別碼。

請確定虛擬機器大小和儲存類型能和您想要附加的磁碟相容。 舉例而言，如果您想要使用的磁碟是進階儲存體，虛擬機器就需能支援進階儲存體 (例如 DS 系列的大小)。 

使用 [Get AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk) 取得資源群組中的磁碟清單

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
取得想要使用的磁碟名稱後，即可將之設為虛擬機器的作業系統磁碟。 此範例會停止\解除配置名為 *myVM* 的虛擬機器，並將名為 *newDisk* 的磁碟指派做為新的作業系統磁碟。 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**後續步驟**

若要建立磁碟複本，請參閱[製作磁碟的快照](snapshot-copy-managed-disk.md)。