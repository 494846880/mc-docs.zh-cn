---
title: 专用 SQL 池中的表数据类型
description: 有关在专用 SQL 池中定义表数据类型的建议。
services: synapse-analytics
author: WenJason
manager: digimobile
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
origin.date: 01/06/2020
ms.date: 11/30/2020
ms.author: v-jay
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 711077ae1ba4117fc4131ee4be3dd824fa5aa750
ms.sourcegitcommit: dabbf66e4507a4a771f149d9f66fbdec6044dfbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96153102"
---
# <a name="table-data-types-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中专用 SQL 池的表数据类型 

本文涵盖了在专用 SQL 池中定义表数据类型的建议。

## <a name="supported-data-types"></a>支持的数据类型

专用 SQL 池支持最常用的数据类型。 有关受支持数据类型的列表，请参阅 CREATE TABLE 语句中的[数据类型](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true#DataTypes)。

## <a name="minimize-row-length"></a>最大限度地减小行长度

最大限度地减小数据类型大小可以缩短行长度，从而获得更好的查询性能。 使用适合数据的最小数据类型。

- 避免使用较大默认长度定义字符列。 例如，如果最长的值是 25 个字符，则将列定义为 VARCHAR(25)。
- 仅需要 VARCHAR 时请避免使用 [NVARCHAR](https://docs.microsoft.com/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。
- 尽可能使用 NVARCHAR(4000) 或 VARCHAR(8000)，而非 NVARCHAR(MAX) 或 VARCHAR(MAX)。

如果使用 PolyBase 外部表来加载表，则定义的表行长度不能超过 1 MB。 当数据长度可变的行超过 1 MB 时，可使用 BCP 而不是 PolyBase 加载行。

## <a name="identify-unsupported-data-types"></a>识别不支持的数据类型

如果从另一个 SQL 数据库迁移你的数据库，你可能会发现专用 SQL 池不支持的数据类型。 可以使用以下查询查明现有 SQL 架构不支持的数据类型：

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>对不受支持的数据类型的解决方法

以下列表显示了专用 SQL 池不支持的数据类型，同时提供了不受支持的数据类型的有效替代数据类型。

| 不支持的数据类型 | 解决方法 |
| --- | --- |
| [geometry](https://docs.microsoft.com/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varbinary](https://docs.microsoft.com/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [geography](https://docs.microsoft.com/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](https://docs.microsoft.com/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [hierarchyid](https://docs.microsoft.com/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](https://docs.microsoft.com/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(4000) |
| [image](https://docs.microsoft.com/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varbinary](https://docs.microsoft.com/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [text](https://docs.microsoft.com/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varchar](https://docs.microsoft.com/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [ntext](https://docs.microsoft.com/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[nvarchar](https://docs.microsoft.com/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [sql_variant](https://docs.microsoft.com/sql/t-sql/data-types/sql-variant-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |将列拆分成多个强类型化列。 |
| [table](https://docs.microsoft.com/sql/t-sql/data-types/table-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |转换成暂时表。 |
| [timestamp](https://docs.microsoft.com/sql/t-sql/data-types/date-and-time-types) |重写代码来使用 [datetime2](https://docs.microsoft.com/sql/t-sql/data-types/datetime2-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 和 [CURRENT_TIMESTAMP](https://docs.microsoft.com/sql/t-sql/functions/current-timestamp-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 函数。 仅支持使用常量作为默认值，因此，不能将 current_timestamp 定义为默认约束。 如果需要从 timestamp 类型的列迁移行版本值，请为 NOT NULL 或 NULL 行版本值使用 [BINARY](https://docs.microsoft.com/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)(8) 或 [VARBINARY](https://docs.microsoft.com/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(8)。 |
| [xml](https://docs.microsoft.com/sql/t-sql/xml/xml-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varchar](https://docs.microsoft.com/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [用户定义的类型](https://docs.microsoft.com/sql/relational-databases/native-client/features/using-user-defined-types?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |尽可能转换回本机数据类型。 |
| 默认值 | 默认值仅支持文本和常量。 |

## <a name="next-steps"></a>后续步骤

有关开发表的详细信息，请参阅[表概述](sql-data-warehouse-tables-overview.md)。
