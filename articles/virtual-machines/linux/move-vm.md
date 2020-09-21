---
title: 使用 Azure CLI 移动 VM
description: 使用 Azure CLI 将 VM 移至另一个 Azure 订阅或资源组。
author: Johnnytechn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
origin.date: 09/12/2018
ms.date: 09/03/2020
ms.author: v-johya
ms.openlocfilehash: 487a322a12c7a916cc05ce66f18ff4272603dfc6
ms.sourcegitcommit: f45809a2120ac7a77abe501221944c4482673287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2020
ms.locfileid: "90057566"
---
# <a name="move-a-vm-to-another-subscription-or-resource-group"></a>将 VM 移到其他订阅或资源组
本文逐步说明如何在资源组或订阅之间移动虚拟机 (VM)。 如果在个人订阅中创建了 VM，现在想要将其移到公司的订阅，则在订阅之间移动 VM 会很方便。

> [!IMPORTANT]
>在移动过程中会创建新的资源 ID。 移动 VM 后，需要更新工具和脚本以使用新的资源 ID。
>


## <a name="use-the-azure-cli-to-move-a-vm"></a>使用 Azure CLI 移动 VM

在使用 Azure CLI 移动 VM 之前，需确保源订阅和目标订阅存在于同一租户中。 若要检查这两个订阅是否具有相同的租户 ID，请使用 [az account show](https://docs.azure.cn/cli/account?view=azure-cli-latest#az-account-show) 命令。

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

```azurecli
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
如果源和目标订阅的租户 ID 不相同，则必须联系[支持人员](https://portal.azure.cn/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)才能将资源移动到新租户。

若要成功移动 VM，需要移动 VM 及其所有支持资源。 使用 [az resource list](https://docs.azure.cn/cli/resource?view=azure-cli-latest#az-resource-list) 命令列出资源组中的所有资源及其 ID。 这有助于通过管道将此命令的输出发送到文件，以便将 ID 复制并粘贴到后续命令中。

```azurecli
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

若要将 VM 及其资源移到其他资源组，请使用 [az resource move](https://docs.azure.cn/cli/resource?view=azure-cli-latest#az-resource-move) 命令。 以下示例说明如何移动 VM 及其所需的大多数通用资源。 使用 **-ids** 参数，并针对要移动的资源传入逗号分隔的 ID 列表（不包含空格）。

```azurecli
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

如果要将 VM 及其资源移到其他订阅，请添加 **--destination-subscriptionId** 参数来指定目标订阅。

当系统要求你确认是否要移动指定的资源时，请输入 **Y** 进行确认。

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>后续步骤
可以在资源组和订阅之间移动许多不同类型的资源。 有关详细信息，请参阅[将资源移到新资源组或订阅](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。

<!-- Update_Description: update meta properties, wording update, update link -->