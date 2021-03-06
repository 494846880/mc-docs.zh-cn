---
title: 快速入门：配置工作负荷隔离 - T-SQL
description: 使用 T-SQL 配置工作负荷隔离。
services: synapse-analytics
author: WenJason
manager: digimobile
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
origin.date: 04/27/2020
ms.date: 07/06/2020
ms.author: v-jay
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 87d2d2d04b3940c4bfe427c3f94fe137e8026b52
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96432650"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>快速入门：使用 T-SQL 配置工作负荷隔离

在本快速入门中，你将快速创建工作负荷组和分类器，以便保留资源以进行数据加载。 工作负荷组将向数据加载分配 20% 的系统资源。  工作负荷分类器将向数据加载工作负荷组分配请求。  它们对数据加载实现 20% 的隔离，这两项资源都必定满足 SLA 要求。

如果没有 Azure 订阅，请在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

> [!NOTE]
> 在 Azure Synapse Analytics 中创建一个 Synapse SQL 实例可能会产生一个新的可计费服务。  有关详细信息，请参阅 [Azure Synapse Analytics 定价](https://azure.cn/pricing/details/sql-data-warehouse/)。

## <a name="prerequisites"></a>先决条件

本快速入门假定你已在 Azure Synapse 中有 Synapse SQL 实例，并且具有 CONTROL DATABASE 权限。 如果需要创建一个 SQL 数据仓库，可使用[创建并连接 - 门户](create-data-warehouse-portal.md)创建名为“mySampleDataWarehouse”的数据仓库。

## <a name="create-login-for-dataloads"></a>为 DataLoads 创建登录名

使用 [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 在 `master` 数据库中为“ELTLogin”创建 SQL Server 身份验证登录名。

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>创建用户

在 mySampleDataWarehouse 中[创建用户](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)“ELTLogin”

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>创建工作负荷组

为隔离度为 20% 的 DataLoads 创建[工作负荷组](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5)
;
```

## <a name="create-a-workload-classifier"></a>创建工作负荷分类器

创建[工作负荷分类器](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)以将 ELTLogin 映射到 DataLoads 工作负荷组。

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>查看现有工作负荷组、分类器和运行时值

```sql
--Workload groups
SELECT * FROM
sys.workload_management_workload_groups

--Workload classifiers
SELECT * FROM
sys.workload_management_workload_classifiers

--Run-time values
SELECT * FROM
sys.dm_workload_management_workload_groups_stats
```

## <a name="clean-up-resources"></a>清理资源

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

我们会针对数据仓库中存储的数据，按数据仓库单位收费。 这些计算和存储资源是分开计费的。

- 如果想要将数据保留在存储中，可以在不使用 SQL 池时暂停计算。 如果暂停计算资源，则你只需支付数据存储费用。 准备好处理数据时，可以恢复计算。
- 如果不想支付将来的费用，则可以删除数据仓库。

## <a name="next-steps"></a>后续步骤

- 现已创建一个工作负荷组。 以 ELTLogin 身份运行一些查询，以查看其执行情况。 若要查看查询和分配的工作负荷组，请参阅 [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql/?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。
- 有关 Synapse SQL 工作负载管理的详细信息，请参阅[工作负载管理](sql-data-warehouse-workload-management.md)和[工作负载隔离](sql-data-warehouse-workload-isolation.md)。
