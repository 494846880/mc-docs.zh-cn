---
title: 对 Azure 专用链接服务源 IP 地址禁用网络策略
description: 了解如何对 Azure 专用链接禁用网络策略。
services: private-link
ms.service: private-link
ms.topic: how-to
origin.date: 09/16/2019
author: rockboyfor
ms.date: 09/07/2020
ms.testscope: no
ms.testdate: 06/15/2020
ms.author: v-yeche
ms.openlocfilehash: 01785961ff24c4149f362e0ddbc860fcf0c66361
ms.sourcegitcommit: 6e88e0e41b35d160a09f7a906ca3b7e837f51803
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89412113"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>对专用链接服务源 IP 禁用网络策略

若要为专用链接服务选择源 IP 地址，子网上需要 `privateLinkServiceNetworkPolicies` 显式禁用设置。 此设置仅适用于特定专用 IP 地址，该地址已选择作为专用链接服务的源 IP 地址。 对于子网中的其他资源，访问权限基于网络安全组 (NSG) 安全规则定义进行控制。 

使用任何 Azure 客户端（PowerShell、CLI 或模板）时，需要通过额外的步骤来更改此属性。 可以使用 Azure PowerShell 和 Azure CLI 的本地安装来禁用此策略，也可以使用 Azure 资源管理器模板。  

<!--Not Available on the local Shell from the Azure portal, or -->

按照以下步骤为名为 *myVirtualNetwork* 的虚拟网络禁用专用链接服务网络策略，并在名为 *myResourceGroup* 的资源组中托管默认子网。 

## <a name="using-azure-powershell"></a>使用 Azure PowerShell
本部分介绍如何使用 Azure PowerShell 禁用子网专用终结点策略。
在代码中，将“默认”替换为虚拟子网的名称。

```azurepowershell
$virtualSubnetName = "default"
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"

($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $virtualSubnetName} ).privateLinkServiceNetworkPolicies = "Disabled"  

$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>使用 Azure CLI
本部分介绍如何使用 Azure CLI 禁用子网专用终结点策略。
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>使用模板
本部分介绍如何使用 Azure 资源管理器模板禁用子网专用终结点策略。
```json
{ 
    "name": "myVirtualNetwork", 
    "type": "Microsoft.Network/virtualNetworks", 
    "apiVersion": "2019-04-01", 
    "location": "chinaeast2", 
    "properties": { 
        "addressSpace": { 
            "addressPrefixes": [ 
                "10.0.0.0/16" 
             ] 
        }, 
        "subnets": [ 
               { 
                 "name": "default", 
                 "properties": { 
                        "addressPrefix": "10.0.0.0/24", 
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 

```
## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure 专用终结点](private-endpoint-overview.md)

<!-- Update_Description: update meta properties, wording update, update link -->