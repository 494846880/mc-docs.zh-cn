---
title: Azure CLI 脚本示例 - 创建 Linux VM
description: Azure CLI 脚本示例 - 创建 Linux VM
services: virtual-machines-linux
documentationcenter: virtual-machines
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
origin.date: 02/27/2017
author: rockboyfor
ms.date: 09/07/2020
ms.testscope: yes
ms.testdate: 08/31/2020
ms.author: v-yeche
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 474bd75bf53be42605f2e0e567fb130b8700a15d
ms.sourcegitcommit: 2eb5a2f53b4b73b88877e962689a47d903482c18
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89413973"
---
# <a name="create-a-fully-configured-virtual-machine"></a>创建完全配置的虚拟机

此脚本使用 Ubuntu 操作系统创建 Azure 虚拟机。 运行脚本后，可通过 SSH 访问虚拟机。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

```azurecli
#!/bin/bash

# Sign in the Azure China Cloud
az cloud set -n AzureChinaCloud
az login

# Create a resource group.
az group create --name myResourceGroup --location chinanorth

# Create a virtual network.
az network vnet create --resource-group myResourceGroup --name myVnet --subnet-name mySubnet

# Create a public IP address.
az network public-ip create --resource-group myResourceGroup --name myPublicIP

# Create a network security group.
az network nsg create --resource-group myResourceGroup --name myNetworkSecurityGroup

# Create a virtual network card and associate with public IP address and NSG.
az network nic create \
  --resource-group myResourceGroup \
  --name myNic \
  --vnet-name myVnet \
  --subnet mySubnet \
  --network-security-group myNetworkSecurityGroup \
  --public-ip-address myPublicIP

# Create a new virtual machine, this creates SSH keys if not present.
az vm create --resource-group myResourceGroup --name myVM --nics myNic --image UbuntuLTS --generate-ssh-keys

# Open port 22 to allow SSh traffic to host.
az vm open-port --port 22 --resource-group myResourceGroup --name myVM
```

## <a name="clean-up-deployment"></a>清理部署

运行以下命令来删除资源组、VM 和所有相关资源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、虚拟机和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [az group create](https://docs.azure.cn/cli/group?view=azure-cli-latest#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az network vnet create](https://docs.azure.cn/cli/network/vnet?view=azure-cli-latest#az-network-vnet-create) | 创建 Azure 虚拟网络和子网。 |
| [az network public-ip create](https://docs.azure.cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) | 使用静态 IP 地址和关联的 DNS 名称创建公共 IP 地址。 |
| [az network nsg create](https://docs.azure.cn/cli/network/nsg?view=azure-cli-latest#az-network-nsg-create) | 创建网络安全组 (NSG)，这是 Internet 和虚拟机之间的安全边界。 |
| [az network nsg rule create](https://docs.azure.cn/cli/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) | 创建 NSG 规则以允许入站流量。 在此示例中，为 SSH 流量打开端口 22。 |
| [az network nic create](https://docs.azure.cn/cli/network/nic?view=azure-cli-latest#az-network-nic-create) | 创建虚拟网卡并将其连接到虚拟网络、子网和 NSG。 |
| [az vm create](https://docs.azure.cn/cli/vm?view=azure-cli-latest#az-vm-create) | 创建虚拟机并将其连接到网卡、虚拟网络、子网和 NSG。 此命令还指定要使用的虚拟机映像和管理凭据。  |
| [az group delete](https://docs.azure.cn/cli/group?view=azure-cli-latest#az-group-delete) | 删除资源组，包括所有嵌套的资源。 |

<!--CORRECT ON [az group delete](https://docs.azure.cn/cli/group?view=azure-cli-latest#az-group-delete)-->

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/cli/index?view=azure-cli-latest)。

可以在 [Azure Linux VM 文档](../linux/cli-samples.md?toc=%2fvirtual-machines%2flinux%2ftoc.json)中找到其他虚拟机 CLI 脚本示例。

<!--Update_Description: update meta properties, update cmdlet -->