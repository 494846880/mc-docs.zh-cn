---
title: 从时间点检索键值对
titleSuffix: Azure App Configuration
description: 在 Azure 应用配置中使用时间点快照检索旧的键值对，前者会记录键值的更改。
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: ce1c7cd6a9882c1cb132c67c747fe34c3dc011d4
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105216"
---
# <a name="point-in-time-snapshot"></a>时间点快照

Azure 应用程序配置将维护键值更改记录。 此记录提供键值更改的时间线。 可以重新构造任何键值的历史记录，并在键值历史记录周期（免费层存储为 7 天，标准层存储为 30 天）内的任何时刻提供其过去的值。 通过此功能，可以向后查看并检索旧的键值。 例如，可以恢复最近部署前使用的配置设置，以便将应用程序回滚到以前的配置。

## <a name="key-value-retrieval"></a>键值检索

可以使用 Azure 门户或 CLI 检索过去的键值。 在 Azure CLI 中，使用 `az appconfig revision list`，并添加适当的参数来检索所需的值。  通过提供存储名称 (`--name <app-config-store-name>`) 或使用连接字符串 (`--connection-string <your-connection-string>`) 来指定 Azure 应用程序配置实例。 通过指定特定的时间点 (`--datetime`)，并通过指定要返回的最大项数 (`--top`) 来限制输出。

<!--Not Available on If you don't have Azure CLI installed locally, you can optionally use [Azure local Shell](../cloud-shell/overview.md)-->

检索所记录的所有键值更改。

```azurecli
az appconfig revision list --name <your-app-config-store-name>.
```

检索对键 `environment` 和标签 `test` 和 `prod` 所做的所有记录的更改。

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment --label test,prod
```

检索层次结构键空间 `environment:prod` 中的所有记录的更改。

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment:prod:* 
```

检索特定时间点的键 `color` 的所有记录的更改。

```azurecli
az appconfig revision list --connection-string <your-app-config-connection-string> --key color --datetime "2019-05-01T11:24:12Z" 
```

检索所记录的最近 10 个键值更改，并仅返回 `key`、`label` 和 `last_modified` 时间戳的值。

```azurecli
az appconfig revision list --name <your-app-config-store-name> --top 10 --fields key label last_modified
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建一个 ASP.NET Core Web 应用](./quickstart-aspnet-core-app.md)  
