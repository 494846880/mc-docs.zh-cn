---
title: Azure PowerShell 脚本示例 - 根据前缀删除容器 | Microsoft Docs
description: 查看一个示例，该示例说明如何使用 Azure PowerShell 根据容器名称中的前缀删除 Azure Blob 存储。
services: storage
author: WenJason
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
origin.date: 06/13/2017
ms.date: 08/24/2020
ms.author: v-jay
ms.openlocfilehash: 770a83754066d5e4029247db0e583253610b11e7
ms.sourcegitcommit: ecd6bf9cfec695c4e8d47befade8c462b1917cf0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2020
ms.locfileid: "88753294"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>根据容器名称前缀删除容器

此脚本根据容器名称的前缀删除 Azure Blob 存储中的容器。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

```powershell
# this script will show how to delete containers with a specific prefix 
# the prefix this will search for is "image". 
# before running this, you need to create a storage account, create some containers,
#    some having the same prefix so you can test this
# note: this retrieves all of the matching containers in one command 
#       if you are going to run this against a storage account with a lot of containers
#       (more than a couple hundred), use continuation tokens to retrieve
#       the list of containers. We will be adding a sample showing that scenario in the future.

# these are for the storage account to be used
#   and the prefix for which to search
$resourceGroup = "containerdeletetestrg"
$location = "China East"
$storageAccountName = "containerdeletetest"
$prefix = "image"

# get a reference to the storage account and the context
$storageAccount = Get-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName
$ctx = $storageAccount.Context 

# list all containers in the storage account 
Write-Host "All containers"
Get-AzStorageContainer -Context $ctx | select Name

# retrieve list of containers to delete
$listOfContainersToDelete = Get-AzStorageContainer -Context $ctx -Prefix $prefix

# write list of containers to be deleted 
Write-Host "Containers to be deleted"
$listOfContainersToDelete | select Name

# delete the containers; this pipes the result of the listing of the containers to delete
#    into the Remove-AzStorageContainer command. It handles all of the containers in the list.
Write-Host "Deleting containers"
$listOfContainersToDelete | Remove-AzStorageContainer -Context $ctx 

# show list of containers not deleted 
Write-Host "All containers not deleted"
Get-AzStorageContainer -Context $ctx | select Name
```

## <a name="clean-up-deployment"></a>清理部署

运行以下命令，删除资源组、其余容器和所有相关资源。

```powershell
Remove-AzResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令根据容器名称前缀删除容器。 表中的每一项均链接到命令特定的文档。

| Command | 说明 |
|---|---|
| [Get-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccount) | 获取资源组或订阅中的指定存储帐户或所有存储帐户。 |
| [Get-AzStorageContainer](https://docs.microsoft.com/powershell/module/az.storage/Get-AzStorageContainer) | 列出与存储帐户关联的存储容器。 |
| [Remove-AzStorageContainer](https://docs.microsoft.com/powershell/module/az.storage/Remove-AzStorageContainer) | 删除指定的存储容器。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/)。

有关其他存储 PowerShell 脚本示例，可参阅 [Azure Blob 存储的 PowerShell 示例](../blobs/storage-samples-blobs-powershell.md)。
