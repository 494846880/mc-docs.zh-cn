---
title: Azure CLI 示例 - 创建虚拟机规模集
description: 此脚本使用 Ubuntu 操作系统和相关网络资源（包括负载均衡器）创建 Azure 虚拟机规模集。
author: mimckitt
ms.author: v-junlch
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: cli
ms.date: 06/22/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 7e6b603f1353a17ee706f55d3e901df0130ec361
ms.sourcegitcommit: 43db4001be01262959400663abf8219e27e5cb8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2020
ms.locfileid: "85241488"
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli"></a>使用 Azure CLI 创建虚拟机规模集
此脚本使用 Ubuntu 操作系统和相关网络资源（包括负载均衡器）创建 Azure 虚拟机规模集。 运行脚本后，可通过 SSH 访问 VM 实例。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

## <a name="sample-script"></a>示例脚本
```azurecli
#!/bin/bash

# Create a resource group
az group create --name myResourceGroup --location chinanorth

# Create a scale set
# Network resources such as an Azure load balancer are automatically created
az vmss create `
  --resource-group myResourceGroup `
  --name myScaleSet `
  --image UbuntuLTS `
  --upgrade-policy-mode automatic `
  --admin-username azureuser `
  --generate-ssh-keys `
  --vm-sku Standard_DS1
```

## <a name="clean-up-deployment"></a>清理部署
运行以下命令可删除资源组、规模集和所有相关资源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明
此脚本使用以下命令创建资源组、虚拟机规模集和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [az group create](/cli/ad/group) | 创建用于存储所有资源的资源组。 |
| [az vmss create](/cli/vmss) | 创建虚拟机规模集并将其连接到虚拟网络、子网和网络安全组。 负载均衡器也会被创建，以将流量分配到多个 VM 实例。 此命令还指定要使用的 VM 映像和管理凭据。  |
| [az group delete](/cli/ad/group) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/overview)。

