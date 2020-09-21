---
title: 快速入门：创建服务器 - Azure CLI - Azure Database for PostgreSQL - 单一服务器
description: 在本快速入门指南中，你将使用 Azure CLI 创建 Azure Database for PostgreSQL 服务器。
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
origin.date: 06/25/2019
ms.date: 09/14/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 544898788219cd76631ac9f096ace3fe135719a0
ms.sourcegitcommit: 5116a603d3cac3cbc2e2370ff857f871f8f51a5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512939"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-cli"></a>快速入门：使用 Azure CLI 创建 Azure Database for PostgreSQL 服务器

> [!TIP]
> 请考虑使用更简单的 [az postgres up](https://docs.microsoft.com/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI 命令（当前为预览版）。 试用[快速入门](./quickstart-create-server-up-azure-cli.md)。

## <a name="prerequisites"></a>先决条件
本文要求运行 Azure CLI 2.0 或更高版本。 若要查看安装的版本，请运行 `az --version` 命令。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/install-azure-cli)。 

需要使用 [az login](/cli/reference-index?view=azure-cli-latest#az-login) 命令登录到你的帐户。 请注意 id 属性，该属性指的是 Azure 帐户的订阅 ID。 

```azurecli
az login
```

使用 [az account set](/cli/account) 命令选择帐户下的特定订阅 ID。 记下 az login 输出中的 id 值，以用作命令中 subscription 参数的值  。 

```azurecli
az account set --subscription <subscription id>
```

如果有多个订阅，请选择应计费的资源所在的相应订阅。 若要获取所有订阅，请使用 [az account list](/cli/account?view=azure-cli-latest#az-account-list)。

## <a name="create-an-azure-database-for-postgresql-server"></a>创建 Azure Database for PostgreSQL 服务器

使用 [az group create](/cli/group?view=azure-cli-latest#az-group-create) 命令创建 [Azure 资源组](../azure-resource-manager/management/overview.md)，然后在此资源组中创建 PostgreSQL 服务器。 应提供唯一名称。 以下示例在 `chinaeast2` 位置创建名为 `myresourcegroup` 的资源组。

```azurecli
az group create --name myresourcegroup --location chinaeast2
```

使用 [az postgres server create](/cli/postgres/server) 命令创建 [Azure Database for PostgreSQL 服务器](overview.md)。 一个服务器可以包含多个数据库。

```azurecli
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location chinaeast2 --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
下面是上述参数的详细信息： 

**设置** | **示例值** | **说明**
---|---|---
name | mydemoserver | 标识 Azure Database for PostgreSQL 服务器的唯一名称。 服务器名称只能包含小写字母、数字和连字符 (-) 字符。 必须包含 3 到 63 个字符。
resource-group | myresourcegroup | Azure 资源组的名称。
location | chinaeast2 | 服务器的 Azure 位置。
admin-user | myadmin | 用于管理员登录的用户名。 它不能为“azure_superuser”、“admin”、“administrator”、“root”、“guest”或“public”     。
admin-password | *安全密码* | 管理员用户的密码。 密码必须包含以下三个类别的 8 到 128 个字符：英文大写字母、英文小写字母、数字和非字母数字字符。
sku-name|GP_Gen5_2| 定价层和计算配置的名称。 请遵循简写约定 {pricing tier} _{compute generation}_ {vCores}。 有关详细信息，请参阅 [Azure Database for PostgreSQL 定价](https://azure.cn/pricing/details/postgresql/server/)。

>[!IMPORTANT] 
>- 服务器上的默认 PostgreSQL 版本为 9.6。 若要查看支持的所有版本，请参阅[支持的 PostgreSQL 主要版本](/postgresql/concepts-supported-versions)。
>- 若要查看 az postgres server create 命令的所有参数，请参阅[此参考文档](/cli/postgres/server?view=azure-cli-latest#az-postgres-server-create)
>- 默认情况下，服务器上启用 SSL。 有关 SSL 的详细信息，请参阅[配置 SSL 连接](./concepts-ssl-connection-security.md)。

## <a name="configure-a-server-level-firewall-rule"></a>配置服务器级防火墙规则 
默认情况下，创建的服务器使用防火墙规则进行保护，无法公开访问。 可以使用 [az postgres server firewall-rule create](/cli/postgres/server/firewall-rule) 命令在服务器上配置防火墙规则，以便为本地环境提供访问权限以连接到服务器。 

以下示例创建名为 `AllowMyIP` 的防火墙规则，该规则允许从特定的 IP 地址 (192.168.0.1) 进行连接。 替代与要从其进行连接的地址相对应的 IP 地址或 IP 地址范围。  如果你不知道如何查找 IP 地址，请转到 [https://whatismyipaddress.com/](https://whatismyipaddress.com/) 以获取 IP 地址。


```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
>  请确保网络防火墙允许 Azure Database for PostgreSQL 服务器使用的端口 5432，以避免连接问题。 

## <a name="get-the-connection-information"></a>获取连接信息

若要连接到服务器，需要提供主机信息和访问凭据。
```azurecli
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

结果采用 JSON 格式。 记下 administratorLogin 和 fullyQualifiedDomainName。
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.chinacloudapi.cn",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
  "location": "chinaeast2",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-azure-database-for-postgresql-server-using-psql"></a>使用 psql 连接到 Azure Database for PostgreSQL 服务器
[psql](https://www.postgresql.org/docs/current/static/app-psql.html) 为常用客户端，用于连接到 PostgreSQL 服务器。 可在本地环境中结合使用 psql 和  连接到服务器。 已使用新的 PostgreSQL 服务器创建一个空数据库“postgres”，可用于连接 psql，如下所示 

   ```bash
   psql --host=mydemoserver.postgres.database.chinacloudapi.cn --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > 如果更喜欢使用 URL 路径连接到 Postgres，则 URL 会使用 `%40` 对用户名中的 @ 符号进行编码。 例如，psql 的连接字符串将是：
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.chinacloudapi.cn:5432/postgres
   > ```


## <a name="clean-up-resources"></a>清理资源
如果不需要将这些资源用于其他快速入门/教程，则可通过运行以下命令将其删除： 

```azurecli
az group delete --name myresourcegroup
```

若要删除新创建的服务器，可运行 [az postgres server delete](/cli/postgres/server) 命令。
```azurecli
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [使用导出和导入功能迁移数据库](./howto-migrate-using-export-and-import.md)
>
> [使用 Node.JS 应用进行连接](./connect-nodejs.md)

