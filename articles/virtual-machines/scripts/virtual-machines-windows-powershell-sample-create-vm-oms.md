---
title: Azure PowerShell Windows 脚本示例 - Azure Monitor 日志
description: Azure PowerShell 脚本示例 - Azure Monitor 日志
services: virtual-machines-windows
documentationcenter: virtual-machines
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
origin.date: 12/12/2017
author: rockboyfor
ms.date: 09/07/2020
ms.testscope: yes
ms.testdate: 08/31/2020
ms.author: v-yeche
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: f662e89017eb5f7371bc2708ec0915588bae3939
ms.sourcegitcommit: 2eb5a2f53b4b73b88877e962689a47d903482c18
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89413491"
---
<!--Verify successfully-->
# <a name="create-an-azure-monitor-vm-with-powershell"></a>使用 PowerShell 创建 Azure Monitor VM 

此脚本创建一个 Azure 虚拟机，安装 Log Analytics 代理，并将系统注册到 Log Analytics 工作区。 运行脚本后，该虚拟机会显示在 Azure Monitor 中。 此外，需更新 Log Analytics 工作区 ID 和工作区密钥。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

```powershell
# Sign-in the Azure China Cloud
Connect-AzAccount -Environment AzureChinaCloud

# OMS ID and OMS key
$omsId = "<Replace with your OMS ID>"
$omsKey = "<Replace with your OMS key>"

# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "chinanorth"
$vmName = "myVM"

# Create a user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create a virtual machine
New-AzVM `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -ImageName "Win2016Datacenter" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -SecurityGroupName "myNetworkSecurityGroup" `
  -PublicIpAddressName "myPublicIp" `
  -Credential $cred `
  -OpenPorts 3389

# Install and configure the OMS agent
$PublicSettings = New-Object psobject | Add-Member -PassThru NoteProperty workspaceId $omsId | ConvertTo-Json
$protectedSettings = New-Object psobject | Add-Member -PassThru NoteProperty workspaceKey $omsKey | ConvertTo-Json

Set-AzVMExtension -ExtensionName "OMS" -ResourceGroupName $resourceGroup -VMName $vmName `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 -SettingString $PublicSettings -ProtectedSettingString $protectedSettings `
  -Location $location

```

## <a name="clean-up-deployment"></a>清理部署

运行以下命令来删除资源组、VM 和所有相关资源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建部署。 表中的每一项均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | 创建虚拟机并将其连接到网卡、虚拟网络、子网和网络安全组。 此命令还将打开端口 80 并设置管理凭据。 |
| [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) | 将 VM 扩展添加到虚拟机。 |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组及其中包含的所有资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/)。

可以在 [Azure Windows VM 文档](../windows/powershell-samples.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。

<!-- Update_Description: update meta properties, wording update, update link -->