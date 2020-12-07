---
title: 使用 Azure Cosmos 帐户的帐户密钥和连接字符串
description: 使用 Azure Cosmos 帐户的帐户密钥和连接字符串
ms.service: cosmos-db
ms.topic: sample
origin.date: 07/29/2020
author: rockboyfor
ms.date: 12/07/2020
ms.testscope: yes
ms.testdate: 08/10/2020
ms.author: v-yeche
ms.openlocfilehash: 9d179c4ea649c3fe38f73682ebd5cf97bf7cd135
ms.sourcegitcommit: bbe4ee95604608448cf92dec46c5bfe4b4076961
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96598444"
---
<!--Verify successfully-->
# <a name="work-with-account-keys-and-connection-strings-for-an-azure-cosmos-account-using-azure-cli"></a>通过 Azure CLI 使用 Azure Cosmos 帐户的帐户密钥和连接字符串

[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.9.1 或更高版本。

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]


## <a name="sample-script"></a>示例脚本

此脚本演示四个操作。

- 列出所有帐户密钥
- 列出只读帐户密钥
- 列出连接字符串
- 重新生成帐户密钥

> [!NOTE]
> 此示例演示了如何使用 SQL (Core) API 帐户，但帐户密钥和连接字符串操作在 Cosmos DB 中的所有数据库 API 中都是相同的。

```azurecli
#!/bin/bash
# Reference: az cosmosdb | https://docs.azure.cn/cli/cosmosdb
# --------------------------------------------------
#
# Account key operations for an Azure Cosmos account
#
#

# Account key operations:
# List all account keys
# List read only account keys
# List connection strings
# Regenerate account keys

# Sign in the Azure China Cloud
az cloud set -n AzureChinaCloud
az login

# Resource group and Cosmos account variables
uniqueId=$RANDOM
resourceGroupName="Group-$uniqueId"
location='chinanorth2'
accountName="cosmos-$uniqueId" #needs to be lower case

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a Cosmos DB account with default values
# Use appropriate values for --kind or --capabilities for other APIs
az cosmosdb create -n $accountName -g $resourceGroupName

read -p "Press any key to list account keys"
# List all account keys
az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName

read -p "Press any key to list read only account keys"
# List read-only keys
az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type read-only-keys

read -p "Press any key to list connection strings"
# List connection strings
az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings

read -p "Press any key to regenerate secondary account keys"
# Regenerate secondary account keys
# key-kind values: primary, primaryReadonly, secondary, secondaryReadonly
az cosmosdb keys regenerate \
    -n $accountName \
    -g $resourceGroupName \
    --key-kind secondary

```

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```azurecli
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.azure.cn/cli/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az cosmosdb create](https://docs.azure.cn/cli/cosmosdb#az_cosmosdb_create) | 创建 Azure Cosmos DB 帐户。 |
| [az cosmosdb keys list](https://docs.microsoft.com/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) | 列出 Azure Cosmos DB 帐户的密钥。 |
| [az cosmosdb list-read-only-keys](https://docs.azure.cn/cli/cosmosdb#az_cosmosdb_list_read_only_keys) | 列出 Azure Cosmos DB 帐户的只读密钥。 |
| [az cosmosdb list-connection-strings](https://docs.azure.cn/cli/cosmosdb#az_cosmosdb_list_connection_strings) | 列出 Azure Cosmos DB 帐户的连接字符串。 |
| [az cosmosdb regenerate-key](https://docs.azure.cn/cli/cosmosdb#az_cosmosdb_regenerate_key) | 重新生成 Azure Cosmos DB 帐户的密钥。 |
| [az group delete](https://docs.azure.cn/cli/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure Cosmos DB CLI 的详细信息，请参阅 [Azure Cosmos DB CLI 文档](https://docs.azure.cn/cli/cosmosdb)。

可以在 [Azure Cosmos DB CLI GitHub 存储库](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)中找到所有 Azure Cosmos DB CLI 脚本示例。

<!-- Update_Description: update meta properties, wording update, update link -->