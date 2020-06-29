---
title: 创建用于 Azure Cosmos DB 的 Cassandra 密钥空间和表的资源锁
description: 创建用于 Azure Cosmos DB 的 Cassandra 密钥空间和表的资源锁
author: rockboyfor
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
origin.date: 06/03/2020
ms.date: 06/22/2020
ms.author: v-yeche
ms.openlocfilehash: f4993209012094122b469f18b8698711382379a9
ms.sourcegitcommit: 48b5ae0164f278f2fff626ee60db86802837b0b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101981"
---
<!--Verified successfully-->
# <a name="create-a-resource-lock-for-azure-cosmos-cassandra-api-keyspace-and-table-using-azure-cli"></a>使用 Azure CLI 创建用于 Azure Cosmos Cassandra API 密钥空间和表的资源锁

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

选择在本地安装并使用 CLI 时，本主题要求运行 Azure CLI 2.6.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.azure.cn/cli/install-azure-cli?view=azure-cli-latest)。

<!--CORRECT ON When you choose to install-->

> [!IMPORTANT]
> 对于使用任何 Cassandra SDK、CQL Shell 或 Azure 门户进行连接的用户所做的更改，除非先在启用了 `disableKeyBasedMetadataWriteAccess` 属性的情况下锁定 Cosmos DB 帐户，否则资源锁无效。 要详细了解如何启用此属性，请参阅[防止 SDK 更改](../../../role-based-access-control.md#preventing-changes-from-cosmos-sdk)。

## <a name="sample-script"></a>示例脚本

```azurecli
#!/bin/bash
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
keyspaceName='keyspace1'
tableName='table1'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
keyspaceParent="databaseAccounts/$accountName"
tableParent="databaseAccounts/$accountName/cassandraKeyspaces/$keyspaceName"
keyspaceLockName="$keyspaceName-Lock"
tableLockName="$tableName-Lock"

# Create a delete lock on keyspace
az lock create --name $keyspaceLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/cassandraKeyspaces \
    --lock-type $lockType \
    --parent $keyspaceParent \
    --resource $keyspaceName

# Create a delete lock on table
az lock create --name $tableLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/tables \
    --lock-type $lockType \
    --parent $tableParent \
    --resource $tableName

# List all locks on a Cosmos account
az lock list --resource-group $resourceGroupName \
    --resource-name $accountName \
    --namespace Microsoft.DocumentDB \
    --resource-type databaseAccounts

# Delete lock on keyspace
lockid=$(az lock show --name $keyspaceLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/cassandraKeyspaces \
        --resource $keyspaceName --parent $keyspaceParent \
        --output tsv --query id)
az lock delete --ids $lockid

# Delete lock on table
lockid=$(az lock show --name $tableLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/tables \
        --resource-name $tableName \
        --parent $tableParent \
        --output tsv --query id)
az lock delete --ids $lockid

```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [az lock create](https://docs.azure.cn/cli/lock?view=azure-cli-latest#az-lock-create) | 创建锁。 |
| [az lock list](https://docs.azure.cn/cli/lock?view=azure-cli-latest#az-lock-list) | 列出锁信息。 |
| [az lock show](https://docs.azure.cn/cli/lock?view=azure-cli-latest#az-lock-show) | 显示锁的属性。 |
| [az lock delete](https://docs.azure.cn/cli/lock?view=azure-cli-latest#az-lock-delete) | 删除锁。 |

## <a name="next-steps"></a>后续步骤

-[锁定资源以防止意外更改](../../../../azure-resource-manager/management/lock-resources.md)

-[Azure Cosmos DB CLI 文档](https://docs.azure.cn/cli/cosmosdb?view=azure-cli-latest)。

-[Azure Cosmos DB CLI GitHub 存储库](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)。

<!-- Update_Description: new article about lock -->
<!--NEW.date: 06/22/2020-->