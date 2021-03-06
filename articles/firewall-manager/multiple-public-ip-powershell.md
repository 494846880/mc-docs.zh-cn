---
title: 使用 Azure PowerShell 部署具有多个公共 IP 地址的 Azure 防火墙
description: 部署具有多个公共 IP 地址的防火墙以保护虚拟中心
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: d5d4560a5d0da660a1f6261e430f49bcf631e6ad
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105436"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses"></a>部署具有多个公共 IP 地址的 Azure 防火墙

若要使用 Azure 防火墙保护虚拟中心，可以使用 Azure PowerShell 部署具有多个公共 IP 地址的防火墙。


## <a name="deploy-the-firewall"></a>部署防火墙

按照以下 Azure PowerShell 示例部署具有多个公共 IP 地址的 Azure 防火墙，以保护虚拟中心。

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID> 

$rgName = <resource group name> 
$vHub = Get-AzVirtualHub -Name <hub name> 
$vHubId = $vHub.Id 
$fwpips = New-AzFirewallHubPublicIpAddress -Count 3
$hubIpAddresses = New-AzFirewallHubIpAddress -PublicIPs $fwpips 
$fw = New-AzFirewall -Name <firewall name> -ResourceGroupName $rgName `
     -Location <location> -Sku AZFW_Hub -HubIPAddresses $hubIpAddresses `
     -VirtualHubId $vHubId 
```

### <a name="update-a-public-ip-address"></a>更新公共 IP 地址

可以使用 Azure PowerShell 来更新 Azure 防火墙的公共 IP 地址。 以下示例从防火墙中删除一个公共 IP 地址。 它一开始有三个公共 IP 地址。

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID>

$azfw = get-azfirewall -Name <firewall name> -ResourceGroupName <resource group name>
$ip1 = New-AzFirewallPublicIpAddress -Address <first ip address to keep>
$ip2 = New-AzFirewallPublicIpAddress -Address <second ip address to keep>
$ipAddresses = $ip1,$ip2
$azfw.HubIPAddresses.publicIPs.Addresses = $ipAddresses
$azfw.HubIPAddresses.publicIPs.count = 2
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="next-steps"></a>后续步骤

[什么是安全虚拟中心？](secured-virtual-hub.md)