---
title: 管理 Azure 中 Linux VM 的可用性 | Microsoft Docs
description: 了解如何使用多部虛擬機器，確保 Azure 中 Linux 應用程式的高可用性。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 891c852a-84c0-4940-a61e-ada6e185bf37
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ecddbb54137c018c1acc202e4056672eb626f87d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60613828"
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>管理 Linux 虛擬機器的可用性

了解如何设置和管理多个虚拟机，以确保 Linux 应用程序在 Azure 中的高可用性。 您也可以[管理 Windows 虛擬機器的可用性](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

如需有關使用 CLI 在 Resource Manger 部署模型中建立可用性設定組的指示，請參閱 [azure availset：用來管理可用性設定組的命令](../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets)。

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>後續步驟
若要深入了解如何對虛擬機器進行負載平衡，請參閱 [對虛擬機器進行負載平衡](../virtual-machines-linux-load-balance.md)。

