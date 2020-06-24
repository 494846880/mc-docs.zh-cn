---
title: 使用 CLI 在 OS 磁盘之间交换
description: 使用 CLI 更改由 Azure 虚拟机使用的操作系统磁盘。
author: Johnnytechn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: article
ms.date: 06/05/2020
ms.author: v-johya
ms.openlocfilehash: db760f31bca1bbf5aad0617c4ecf5e35b7304bb5
ms.sourcegitcommit: 285649db9b21169f3136729c041e4d04d323229a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2020
ms.locfileid: "84684027"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>使用 CLI 更改由 Azure VM 使用的 OS 磁盘


如果有现有 VM，但希望将磁盘交换为备份磁盘或其他 OS 磁盘，则可使用 Azure CLI 交换 OS 磁盘。 无需删除和重新创建 VM。 甚至可在另一资源组中使用托管磁盘，只要该磁盘尚未使用。

需要停止/取消分配 VM，然后才可将该托管磁盘的资源 ID 替换为其他托管磁盘的资源 ID。 

请确保 VM 大小和存储类型与要附加的磁盘兼容。 例如，如果要使用的磁盘位于高级存储中，则 VM 需要能使用高级存储（如 DS 系列大小）。

本文需要 Azure CLI 2.0.25 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.azure.cn/cli/install-azure-cli?view=azure-cli-latest)。 


使用 [az disk list](https://docs.azure.cn/cli/disk?view=azure-cli-latest#az-disk-list) 获取资源组中的磁盘列表。

```azurecli
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


在交换磁盘之前，使用 [az vm stop](https://docs.azure.cn/cli/vm?view=azure-cli-latest#az-vm-stop) 停止\取消分配 VM。

```azurecli
az vm stop \
   -n myVM \
   -g myResourceGroup
```


使用 [az vm update](https://docs.azure.cn/cli/vm?view=azure-cli-latest#az-vm-update) 以及新磁盘的完整资源 ID 获取 `--osdisk` 参数 

```azurecli 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
使用 [az vm start](https://docs.azure.cn/cli/vm?view=azure-cli-latest#az-vm-start) 重启 VM：

```azurecli
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**后续步骤**

要创建磁盘副本，请参阅[拍摄磁盘快照](snapshot-copy-managed-disk.md)。

<!-- Update_Description: wording update, update meta properties -->