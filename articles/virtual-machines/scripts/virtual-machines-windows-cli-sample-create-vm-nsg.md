---
title: Azure CLI 脚本示例 - 使用内部和外部 NSG 创建两个 VM
description: Azure CLI 脚本示例 - 使用内部和外部 NSG 创建两个 VM
services: virtual-machines-windows
documentationcenter: virtual-machines
manager: gwallace
tags: ''
ms.assetid - ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
origin.date: 02/23/2017
author: rockboyfor
ms.date: 09/07/2020
ms.testscope: yes
ms.testdate: 08/31/2020
ms.author: v-yeche
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c4ef01a3124d8a040330534957d6c67762497ef3
ms.sourcegitcommit: 2eb5a2f53b4b73b88877e962689a47d903482c18
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89414046"
---
# <a name="secure-network-traffic-between-virtual-machines"></a>保护虚拟机之间的网络流量

此脚本创建两个虚拟机，并保护这两个虚拟机的传入流量。 一个虚拟机可在 Internet 上访问，其网络安全组 (NSG) 配置为允许端口 3389 和端口 80 上的流量。 第二个虚拟机无法在 Internet 上访问，其 NSG 配置为仅允许来自第一个虚拟机的流量。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

```azurecli
#!/bin/bash

# Sign in the Azure China Cloud
az cloud set -n AzureChinaCloud
az login

# Update for your admin password
AdminPassword=ChangeYourAdminPassword1

# Create a resource group.
az group create --name myResourceGroup --location chinanorth

# Create a virtual network and front-end subnet.
az network vnet create --resource-group myResourceGroup --name myVnet --address-prefix 10.0.0.0/16 \
--subnet-name mySubnetFrontEnd --subnet-prefix 10.0.1.0/24

# Create a back-end subnet and associate with virtual network. 
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVnet \
  --name mySubnetBackEnd --address-prefix 10.0.2.0/24

# Create a front-end virtual machine.
az vm create --resource-group myResourceGroup --name myVMFrontEnd --image win2016datacenter \
  --admin-username azureuser --admin-password $AdminPassword --vnet-name myVnet --subnet mySubnetFrontEnd \
   --nsg myNetworkSecurityGroupFrontEnd --no-wait

# Create a back-end virtual machine without a public IP address.
az vm create --resource-group myResourceGroup --name myVMBackEnd --image win2016datacenter \
  --admin-username azureuser --admin-password $AdminPassword --public-ip-address "" --vnet-name myVnet \
  --subnet mySubnetBackEnd --nsg myNetworkSecurityGroupBackEnd

# Create front-end NSG rule to allow traffic on port 80.
az network nsg rule create --resource-group myResourceGroup --nsg-name myNetworkSecurityGroupFrontEnd \
  --name http --access allow --protocol Tcp --direction Inbound --priority 200 \
  --source-address-prefix "*" --source-port-range "*" --destination-address-prefix "*" --destination-port-range 80

# Get nsg rule name.
nsgrule=$(az network nsg rule list --resource-group myResourceGroup --nsg-name myNetworkSecurityGroupBackEnd --query [0].name -o tsv)

# Update back-end network security group rule to limit SSH to source prefix (priority 100).
az network nsg rule update --resource-group myResourceGroup --nsg-name myNetworkSecurityGroupBackEnd \
  --name $nsgrule --protocol tcp --direction inbound --priority 100 \
  --source-address-prefix 10.0.1.0/24 --source-port-range '*' --destination-address-prefix '*' \
  --destination-port-range 22 --access allow

# Create backend NSG rule to block all incoming traffic (priority 200).
az network nsg rule create --resource-group myResourceGroup --nsg-name myNetworkSecurityGroupBackEnd \
  --name denyAll --access Deny --protocol Tcp --direction Inbound --priority 200 \
  --source-address-prefix "*" --source-port-range "*" --destination-address-prefix "*" --destination-port-range "*"

```

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源。

```azurecli 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、虚拟机和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [az group create](https://docs.azure.cn/cli/group?view=azure-cli-latest#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az network vnet create](https://docs.azure.cn/cli/network/vnet?view=azure-cli-latest#az-network-vnet-create) | 创建 Azure 虚拟网络和子网。 |
| [az network vnet subnet create](https://docs.azure.cn/cli/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create) | 创建子网。 |
| [az vm create](https://docs.azure.cn/cli/vm?view=azure-cli-latest#az-vm-create) | 创建虚拟机并将其连接到网卡、虚拟网络、子网和 NSG。 此命令还指定要使用的虚拟机映像和管理凭据。  |
| [az network nsg rule update](https://docs.azure.cn/cli/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-update) | 更新 NSG 规则。 在本例中，将更新后端规则，仅从前端子网传递流量。 |
| [az network nsg rule list](https://docs.azure.cn/cli/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-list) | 返回有关网络安全组规则的信息。 在此示例中，规则名称存储在变量中，以便以后在脚本中使用。 |
| [az group delete](https://docs.azure.cn/cli/group?view=azure-cli-latest#az-group-delete)| 删除资源组，包括所有嵌套的资源。 |

<!--CORRECT ON [az group delete](https://docs.azure.cn/cli/group?view=azure-cli-latest#az-group-delete-->

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/cli/index?view=azure-cli-latest)。

可以在 [Azure Windows VM 文档](../windows/cli-samples.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 CLI 脚本示例。

<!-- Update_Description: update meta properties, wording update, update link -->