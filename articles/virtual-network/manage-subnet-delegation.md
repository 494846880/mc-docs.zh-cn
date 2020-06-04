---
title: 在 Azure 虚拟网络中添加或删除子网委托
titlesuffix: Azure Virtual Network
description: 了解如何在 Azure 中添加或删除为服务委托的子网。
services: virtual-network
documentationcenter: na
author: rockboyfor
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 11/06/2019
ms.date: 04/13/2020
ms.author: v-yeche
ms.openlocfilehash: 6d5a877ac4065d71b0f953728b32950aeb7b7161
ms.sourcegitcommit: 564739de7e63e19a172122856ebf1f2f7fb4bd2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82093317"
---
# <a name="add-or-remove-a-subnet-delegation"></a>添加或删除子网委托

子网委派为服务提供了显式权限，以便能够在部署服务时使用唯一标识符在子网中创建服务专属资源。 本文介绍如何添加或删除为 Azure 服务委托的子网。

<!--Not Avaialable on ## Portal-->
<!--Not Avaialble on ## Azure CLI-->
<!--CLI cmdlete should be az network vnet subnet show AZ NETWORK VNET SUBNET SHOW-->

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="connect-to-azure"></a>连接到 Azure

```powershell
  Connect-AzAccount -Environment AzureChinaCloud
```

### <a name="create-a-resource-group"></a>创建资源组
使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzResourceGroup) 创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

<!--CORRECT ON [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzResourceGroup)-->

以下示例在“chinaeast”位置创建名为“myResourceGroup”的资源组：

```powershell
  New-AzResourceGroup -Name myResourceGroup -Location chinaeast
```
### <a name="create-virtual-network"></a>创建虚拟网络

使用 [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest) 在 **myResourceGroup** 中创建名为 **myVnet** 的虚拟网络，该虚拟网络包含使用 [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) 配置的名为 **mySubnet** 的子网。 虚拟网络的 IP 地址空间是 **10.0.0.0/16**。 虚拟网络中的子网是 **10.0.0.0/24**。  

```powershell
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location chinaeast -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>权限

如果没有创建要委托给 Azure 服务的子网，则需要以下权限：`Microsoft.Network/virtualNetworks/subnets/write`。

内置的[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fvirtual-network%2ftoc.json#network-contributor)角色也包含必需的权限。

### <a name="delegate-a-subnet-to-an-azure-service"></a>将子网委托给 Azure 服务

在此部分，我们将上一部分创建的子网委托给 Azure 服务。 

请使用 [Add-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/add-azdelegation?view=latest) 通过名为 **myDelegation** 的委托（委托到 Azure 服务）对名为 **mySubnet** 的子网进行更新。  在此示例中，**Microsoft.DBforPostgreSQL/serversv2** 用于示例委托：

```powershell
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
使用 [Get-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) 验证委托：

```powershell
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  ProvisioningState : Succeeded
  ServiceName       : Microsoft.DBforPostgreSQL/serversv2
  Actions           : {Microsoft.Network/virtualNetworks/subnets/join/action}
  Name              : myDelegation
  Etag              : W/"9cba4b0e-2ceb-444b-b553-454f8da07d8a"
  Id                : /subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/myDelegation

```
### <a name="remove-subnet-delegation-from-an-azure-service"></a>从 Azure 服务中删除子网委托

使用 [Remove-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/remove-azdelegation?view=latest) 从名为 **mySubnet** 的子网中删除委托：

```powershell
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
使用 [Get-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) 验证是否已删除委托：

```powershell
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>后续步骤
- 了解如何[在 Azure 中管理子网](virtual-network-manage-subnet.md)。

<!-- Update_Description: update meta properties, wording update, update link -->
