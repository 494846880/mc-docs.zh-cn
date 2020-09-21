---
title: 使用 Azure CLI 打开 VM 的端口
description: 了解如何使用 Azure CLI 为 VM 打开端口/创建终结点。
author: Johnnytechn
manager: gwallace
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
origin.date: 12/13/2017
ms.date: 09/03/2020
ms.author: v-johya
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1235fe3d3f25f3b0fe8afea4e82867860138ee82
ms.sourcegitcommit: f45809a2120ac7a77abe501221944c4482673287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2020
ms.locfileid: "90057554"
---
# <a name="open-ports-and-endpoints-to-a-vm-with-the-azure-cli"></a>使用 Azure CLI 打开 VM 的端口和终结点

通过在子网或 VM 网络接口上创建网络筛选器可为 Azure 中的虚拟机 (VM) 打开端口或创建终结点。 将这些筛选器（控制入站和出站流量）放在网络安全组中，并附加到将接收流量的资源。 让我们在端口 80 上使用 Web 流量的常见示例。 本文说明如何使用 Azure CLI 打开 VM 的端口。 

若要创建网络安全组和规则，需要安装最新的 [Azure CLI](https://docs.azure.cn/cli/install-az-cli2?view=azure-cli-latest)，并使用 [az login](https://docs.azure.cn/cli/reference-index?view=azure-cli-latest#az-login) 登录到 Azure 帐户。

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

在以下示例中，请将示例参数名称替换成自己的值。 示例参数名称包括 *myResourceGroup*、*myNetworkSecurityGroup* 和 *myVnet*。


## <a name="quickly-open-a-port-for-a-vm"></a>为 VM 快速打开一个端口
如果需要在开发/测试方案中为 VM 快速打开一个端口，可以使用 [az vm open-port](https://docs.azure.cn/cli/vm?view=azure-cli-latest#az-vm-open-port) 命令。 此命令创建一个网络安全组，添加一项规则，然后将其应用到 VM 或子网。 以下示例在名为 *myResourceGroup* 的资源组中打开名为 *myVM* 的 VM 上的端口 *80*。

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

若要对规则进行更多的控制，例如定义源 IP 地址范围，请继续执行本文中的其他步骤。


## <a name="create-a-network-security-group-and-rules"></a>创建网络安全组和规则
使用 [az network nsg create](https://docs.azure.cn/cli/network/nsg?view=azure-cli-latest#az-network-nsg-create)创建网络安全组。 以下示例在 *chinaeast* 位置创建名为 *myNetworkSecurityGroup* 的网络安全组：

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location chinaeast \
    --name myNetworkSecurityGroup
```

借助 [az network nsg rule create](https://docs.azure.cn/cli/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) 添加规则以允许 HTTP 流量流向 Web 服务器（或者根据自己的情况（例如 SSH 访问或数据库连接）来调整此规则）。 以下示例创建一个名为 *myNetworkSecurityGroupRule* 的规则，以允许端口 80 上的 TCP 流量：

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>对 VM 应用网络安全组
借助 [az network nic update](https://docs.azure.cn/cli/network/nic?view=azure-cli-latest#az-network-nic-update) 将网络安全组与 VM 的网络接口 (NIC) 相关联。 以下示例将名为 *myNic* 的现有 NIC 与名为 *myNetworkSecurityGroup* 的网络安全组相关联：

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

或者，也可以借助 [az network vnet subnet update](https://docs.azure.cn/cli/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) 将网络安全组与虚拟网络的子网相关联，而不是只与单个 VM 上的网络接口相关联。 以下示例将 *myVnet* 虚拟网络中名为 *mySubnet* 的现有子网与名为 *myNetworkSecurityGroup* 的网络安全组相关联：

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>有关网络安全组的详细信息
利用此处的快速命令，可以让流向 VM 的流量开始正常运行。 网络安全组提供许多出色的功能和粒度来控制资源的访问。 可以[在此处详细了解如何创建网络安全组和 ACL 规则](tutorial-virtual-network.md#secure-network-traffic)。

对于高可用性 Web 应用程序，应将 VM 放置在 Azure 负载均衡器后。 当负载均衡器向 VM 分配流量时，网络安全组可以筛选流量。 有关详细信息，请参阅[如何在 Azure 中均衡 Linux 虚拟机负载以创建高可用性应用程序](tutorial-load-balancer.md)。

## <a name="next-steps"></a>后续步骤
在本示例中，创建了简单的规则来允许 HTTP 流量。 下列文章更介绍了有关创建更详细环境的信息：

* [Azure Resource Manager 概述](../../azure-resource-manager/management/overview.md)
* [什么是网络安全组 (NSG)？](../../virtual-network/security-overview.md)

<!-- Update_Description: update meta properties, wording update, update link -->