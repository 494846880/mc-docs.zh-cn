---
title: 创建具有静态公共 IP 地址的 VM - PowerShell | Azure
description: 了解如何使用 PowerShell 创建具有静态公共 IP 地址的 VM。
services: virtual-network
documentationcenter: na
author: rockboyfor
manager: digimobile
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 08/08/2018
ms.date: 06/15/2020
ms.author: v-yeche
ms.openlocfilehash: 44f3d2d5abc4b66d1056757013e6944f3d1ae58e
ms.sourcegitcommit: ff67734e01c004be575782b4812cfe857e435f4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2020
ms.locfileid: "84487079"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>使用 PowerShell 创建具有静态公共 IP 地址的虚拟机

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

可以创建具有静态公共 IP 地址的虚拟机。 使用公共 IP 地址可以通过 Internet 来与虚拟机通信。 分配静态公共 IP 地址而非动态地址可以确保地址永远不会改变。 详细了解[静态公共 IP 地址](virtual-network-ip-addresses-overview-arm.md#allocation-method)。 若要将分配给现有虚拟机的公共 IP 地址从动态更改为静态，或者要使用专用 IP 地址，请参阅[添加、更改或删除 IP 地址](virtual-network-network-interface-addresses.md)。 公共 IP 地址会产生[少许费用](https://www.azure.cn/pricing/details/ip-addresses/)，可为每个订阅使用的公共 IP 地址数有[限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="create-a-virtual-machine"></a>创建虚拟机

可以从本地计算机完成以下步骤。 若要使用本地计算机，请确保[安装了 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fvirtual-network%2ftoc.json)。 

1. 打开命令会话并使用 `Connect-AzAccount -Environment AzureChinaCloud` 登录到 Azure。
2. 使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 命令创建资源组。 以下示例在“中国东部”Azure 区域中创建一个资源组：

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location ChinaEast
    ```

3. 使用 [New-AzVM](https://docs.microsoft.com/powershell/module/az.Compute/New-azVM) 命令创建虚拟机。 `-AllocationMethod "Static"` 选项向虚拟机分配静态公共 IP 地址。 以下示例使用名为 *myPublicIpAddress* 的静态、基本 SKU 公共 IP 地址创建 Windows Server 虚拟机。 出现提示时，提供要用作虚拟机的登录凭据的用户名和密码：

    ```powershell
    New-AzVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "China East" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
    ```

    如果公共 IP 地址必须是标准 SKU，则必须在单独的步骤中[创建公共 IP 地址](virtual-network-public-ip-address.md#create-a-public-ip-address)、[创建网络接口](virtual-network-network-interface.md#create-a-network-interface)、[向网络接口分配公共 IP 地址](virtual-network-network-interface-addresses.md#add-ip-addresses)，然后[使用网络接口创建虚拟机](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm)。 详细了解[公共 IP 地址 SKU](virtual-network-ip-addresses-overview-arm.md#sku)。 如果虚拟机将添加到公共 Azure 负载均衡器的后端池，则虚拟机公共 IP 地址的 SKU 必须与负载均衡器的公共 IP 地址的 SKU 相匹配。 有关详细信息，请参阅 [Azure 负载均衡器](../load-balancer/load-balancer-overview.md?toc=%2fvirtual-network%2ftoc.json)。

    <!--Not Available on #skus-->
    
4. 使用 [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) 查看分配的公共 IP 地址并确认它创建为静态地址：

    ```powershell
    Get-AzPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
    ```

    Azure 从你在其中创建虚拟机的区域使用的地址中分配了一个公共 IP 地址。 可以下载 Azure [中国](https://www.microsoft.com/download/confirmation.aspx?id=57062)云的范围（前缀）列表。
    
    <!--CORRECT ON [China](https://www.microsoft.com/download/confirmation.aspx?id=57062)-->
    
> [!WARNING]
> 不要修改虚拟机操作系统中的 IP 地址设置。 操作系统不知道 Azure 公共 IP 地址。 虽然可以向操作系统添加专用 IP 地址设置，但除非必要，否则我们建议不要这样做，而只能阅读[向操作系统添加专用 IP 地址](virtual-network-network-interface-addresses.md#private)之后才执行此操作。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，请使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) 将其删除：

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure 中的[公共 IP 地址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)
- 详细了解[专用 IP 地址](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses)以及如何为 Azure 虚拟机分配[静态公共 IP 地址](virtual-network-network-interface-addresses.md#add-ip-addresses)
- 详细了解如何创建 [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fvirtual-network%2ftoc.json) 和 [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fvirtual-network%2ftoc.json) 虚拟机

<!-- Update_Description: wording update, update link -->