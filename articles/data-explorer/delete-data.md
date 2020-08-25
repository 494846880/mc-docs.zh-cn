---
title: 从 Azure 数据资源管理器中删除数据
description: 本文介绍了 Azure 数据资源管理器中的删除方案，包括清除、删除盘区和基于保留期的删除。
author: orspod
ms.author: v-tawe
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
origin.date: 03/12/2020
ms.date: 05/09/2020
ms.openlocfilehash: 7446f18369e3e59a5f433861990345172c1c7aa9
ms.sourcegitcommit: f4bd97855236f11020f968cfd5fbb0a4e84f9576
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88515835"
---
# <a name="delete-data-from-azure-data-explorer"></a>从 Azure 数据资源管理器中删除数据

Azure 数据资源管理器支持本文中所述的各种删除方案。 

## <a name="delete-data-using-the-retention-policy"></a>使用保留策略删除数据

Azure 数据资源管理器会根据[保留策略](/data-explorer/kusto/management/retentionpolicy)自动删除数据。 此方法是最有效且无障碍的数据删除方式。 请在数据库或表级别设置保留策略。

假设某个数据库或表的保留期设置为 90 天。 如果只需要 60 天的数据，请如下所述删除更旧的数据：

```kusto
alter-merge database <DatabaseName> policy retention softdelete = 60d

alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>通过删除盘区来删除数据

[盘区（数据分片）](/data-explorer/kusto/management/extents-overview)是数据存储在其中的内部结构。 每个盘区可容纳多达数百万条记录。 可以使用[删除盘区命令](/data-explorer/kusto/management/extents-commands#drop-extents)逐个或者按组删除盘区。 

### <a name="examples"></a>示例

你可以删除表中的所有行或只删除特定盘区。

* 删除表中的所有行：

    ```kusto
    .drop extents from TestTable
    ```

* 删除特定盘区：

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>使用清除删除各个数据行

可以使用[数据清除](/data-explorer/kusto/concepts/data-purge)来删除各个行。 删除不是即时的，并且需要大量的系统资源。 因此，建议仅将其用于合规性方案。  

