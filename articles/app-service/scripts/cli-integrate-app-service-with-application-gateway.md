---
title: Azure CLI 脚本示例 - 将应用服务与应用程序网关集成 | Microsoft Docs
description: Azure CLI 脚本示例 - 将应用服务与应用程序网关集成
services: appservice
documentationcenter: appservice
author: madsd
manager: ccompy
editor: ''
tags: azure-service-management
ms.assetid: 6c16d6f8-3c08-4a59-858e-684a2ceccb5f
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
origin.date: 12/09/2019
ms.date: 08/13/2020
ms.author: v-tawe
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 7054d3a45d922c23b056df907c24fa5de0f65de3
ms.sourcegitcommit: 9d9795f8a5b50cd5ccc19d3a2773817836446912
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88228334"
---
# <a name="integrate-app-service-with-application-gateway-using-cli"></a>使用 CLI 将应用服务与应用程序网关集成

此示例脚本将创建一个 Azure 应用服务 Web 应用、Azure 虚拟网络和应用程序网关。 然后，它将 Web 应用的流量限制为只能源自应用程序网关子网。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

<!-- [!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)] -->

如果选择在本地安装并使用 CLI，需要 Azure CLI 2.0.74 版或更高版本。 要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

```azurecli
#/bin/bash

# This script requires minimum Azure CLI version 2.0.74

# Variables
resourceGroupName="yourResourceGroup"
appName="webappwithgateway$RANDOM"
location="chinaeast"

# Create a Resource Group 
az group create --name $resourceGroupName --location $location

# Create network resources
az network vnet create \
    --resource-group $resourceGroupName \
    --name myVNet \
    --location $location \
    --address-prefix 10.0.0.0/16 \
    --subnet-name myAGSubnet \
    --subnet-prefix 10.0.1.0/24

az network public-ip create \
    --resource-group $resourceGroupName --location $location \
    --name myAGPublicIPAddress --dns-name $appName --sku Standard

# Create an App Service Plan
az appservice plan create --resource-group $resourceGroupName \
    --name myAppServicePlan --location $location --sku S1

# Create a Web App
az webapp create --resource-group $resourceGroupName \
    --name $appName --plan myAppServicePlan

appFqdn=$(az webapp show --name $appName --resource-group $resourceGroupName --query defaultHostName -o tsv)

# Create an Application Gateway
az network application-gateway create \
    --resource-group $resourceGroupName \
    --name myAppGateway \
    --location $location \
    --vnet-name myVNet \
    --subnet myAGsubnet \
    --min-capacity 2 \
    --sku Standard_v2 \
    --http-settings-cookie-based-affinity Disabled \
    --frontend-port 80 \
    --http-settings-port 80 \
    --http-settings-protocol Http \
    --public-ip-address myAGPublicIPAddress \
    --servers $appFqdn

az network application-gateway http-settings update \
    --resource-group $resourceGroupName --gateway-name myAppGateway \
    --name appGatewayBackendHttpSettings \
    --host-name-from-backend-pool

# Apply Access Restriction to Web App
az webapp config access-restriction add \
    --resource-group $resourceGroupName --name $appName \
    --priority 200 --rule-name gateway-access \
    --subnet myAGSubnet --vnet-name myVNet

# Get the App Gateway Fqdn
az network public-ip show \
    --resource-group $resourceGroupName \
    --name myAGPublicIPAddress \
    --query {AppGatewayFqdn:dnsSettings.fqdn} \
    --output table
```

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、应用服务应用、Cosmos DB 和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [`az group create`](https://docs.azure.cn/cli/group?view=azure-cli-latest#az-group-create) | 创建用于存储所有资源的资源组。 |
| [`az network vnet create`](https://docs.azure.cn/cli/network/vnet?view=azure-cli-latest#az-network-vnet-create) | 创建虚拟网络。 |
| [`az network public-ip create`](https://docs.azure.cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) | 创建公共 IP 地址。 |
| [`az network public-ip show`](https://docs.azure.cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-show) | 显示公共 IP 地址的详细信息。 |
| [`az appservice plan create`](https://docs.azure.cn/cli/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | 创建应用服务计划。 |
| [`az webapp create`](https://docs.azure.cn/cli/webapp?view=azure-cli-latest#az-webapp-create) | 创建应用服务 Web 应用。 |
| [`az webapp show`](https://docs.azure.cn/cli/webapp?view=azure-cli-latest#az-webapp-show) | 显示应用服务 Web 应用的详细信息。 |
| [`az webapp config access-restriction add`](https://docs.azure.cn/cli/webapp/config/access-restriction?view=azure-cli-latest#az-webapp-config-access-restriction-add) | 将访问限制添加到应用服务 Web 应用。 |
| [`az network application-gateway create`](https://docs.azure.cn/cli/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-create) | 创建应用程序网关。 |
| [`az network application-gateway http-settings update`](https://docs.azure.cn/cli/network/application-gateway/http-settings?view=azure-cli-latest#az-network-application-gateway-http-settings-update) | 更新应用程序网关 HTTP 设置。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/cli/)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
