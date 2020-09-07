---
title: Azure PowerShell 脚本示例 - 下载设备配置模板 | Microsoft Docs
description: 了解如何使用 PowerShell 脚本下载用于连接的 VPN 设备配置模板。
services: vpn-gateway
documentationcenter: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
origin.date: 01/09/2020
ms.date: 09/07/2020
ms.author: v-jay
ms.openlocfilehash: 3795d44604d908507951fb3b31580ff26b40afd0
ms.sourcegitcommit: 22e1da9309795e74a91b7241ac5987a802231a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462942"
---
# <a name="download-vpn-device-template-using-powershell"></a>使用 PowerShell 下载 VPN 设备模板

此脚本下载用于连接的 VPN 设备模板

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```azurepowershell
# Declare variables
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration template for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG `
-DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要所创建的资源，请使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) 命令删除资源组。 这将删除资源组及其包含的所有资源。

```azurepowershell
Remove-AzResourceGroup -Name TestRG1
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建部署。 表中的每一项均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [Get-AzVirtualNetworkGatewaySupportedVpnDevice](https://docs.microsoft.com/powershell/module/az.network/Get-azVirtualNetworkGatewaySupportedVpnDevice) | 列出所有可用的 VPN 设备模型和版本。 |
| [Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript](https://docs.microsoft.com/powershell/module/az.network/Get-azVirtualNetworkGatewayConnectionVpnDeviceConfigScript) | 下载用于连接的配置模板。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/)。
