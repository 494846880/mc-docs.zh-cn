---
title: 升级到最新代系
description: 将 Azure Synapse Analytics 专用 SQL 池升级到最新一代 Azure 硬件和存储体系结构。
services: synapse-analytics
author: WenJason
manager: digimobile
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
origin.date: 02/19/2019
ms.date: 11/30/2020
ms.author: v-jay
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: b59151c533f42b7eb4dcb523cc62b66f99dd00c8
ms.sourcegitcommit: dabbf66e4507a4a771f149d9f66fbdec6044dfbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96153091"
---
# <a name="optimize-performance-by-upgrading-dedicated-sql-pool-in-azure-synapse-analytics"></a>通过升级 Azure Synapse Analytics 中的专用 SQL 池优化性能

将专用 SQL 池升级到最新一代 Azure 硬件和存储体系结构。

## <a name="why-upgrade"></a>为什么升级？

现在可以在 Azure 门户中针对[受支持的区域](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)无缝地升级到专用 SQL 池“计算优化第 2 代”层级。 如果你的区域不支持自助升级，则可以升级到受支持的区域，或者等待你的区域支持自助升级。 请立即升级来利用最新一代 Azure 硬件和增强的存储体系结构，包括更快的性能、更高的可伸缩性以及无限的列式存储。

> [!IMPORTANT]
> 此升级适用于[受支持的区域](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)中的“计算优化第 1 代”层级专用 SQL 池。

## <a name="before-you-begin"></a>准备阶段

1. 检查你的[区域](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)是否支持从第 1 代到第 2 代迁移。 请注意自动迁移日期。 为了避免与自动化流程发生冲突，请在自动化流程开始日期之前计划手动迁移。
2. 如果你所在的区域尚不受支持，请继续检查你的区域是否已添加到受支持的区域中，或者[使用还原来升级](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal)到受支持的区域。
3. 如果你的区域受支持，请[通过 Azure 门户进行升级](#upgrade-in-a-supported-region-using-the-azure-portal)
4. 使用以下映射，根据“弹性优化第 1 代”层级数据仓库的当前性能级别为专用 SQL 池选择建议的性能级别：

   | “计算优化第 1 代”层级 | “计算优化第 2 代”层级 |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c            |
   |            DW600            |           DW500c            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!NOTE]
> 建议的性能级别不是直接转换。 例如，我们建议从 DW600 转换到 DW500c。

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>使用 Azure 门户在受支持的区域中进行升级

- 通过 Azure 门户从第 1 代迁移到第 2 代是永久性的。 无法通过任何流程返回到第 1 代。
- 专用 SQL 池必须处于运行状态才能迁移到第 2 代

### <a name="before-you-begin"></a>准备阶段

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- 登录到 [Azure 门户](https://portal.azure.cn/)。
- 确保专用 SQL 池处于运行状态 - 它必须处于运行状态才能迁移到第 2 代

### <a name="powershell-upgrade-commands"></a>PowerShell 升级命令

1. 如果要升级的“计算优化第 1 代”层级专用 SQL 池已暂停，请[恢复专用 SQL 池](pause-and-resume-compute-portal.md)。

2. 做好停机几分钟的准备。

3. 识别任何对计算优化第 1 代性能级别的代码引用，并将其修改为等效的计算优化第 2 代性能级别。 下面是升级前需要更新代码引用的两个示例：

   原始 Gen1 PowerShell 命令：

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   修改为：

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE]
   > -RequestedServiceObjectiveName“DW300”更改为 - RequestedServiceObjectiveName“DW300c  ”
   >

   原始 Gen1 T-SQL 命令：

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   修改为：

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ;
   ```

   > [!NOTE]
   > SERVICE_OBJECTIVE = “DW300”更改为 SERVICE_OBJECTIVE = “DW300c” 

## <a name="start-the-upgrade"></a>开始升级

1. 在 Azure 门户中转到你的“计算优化第 1 代”层级专用 SQL 池。 如果要升级的“计算优化第 1 代”层级专用 SQL 池已暂停，请[恢复专用 SQL 池](pause-and-resume-compute-portal.md)。
2. 在“任务”选项卡下选择“升级到第 2 代”  卡：![Upgrade_1](./media/upgrade-to-latest-generation/upgrade-to-gen2-1.png)

   > [!NOTE]
   > 如果在“任务”选项卡下未看到“升级到第 2 代”  卡，则你的订阅类型在当前区域中受到限制。
   > 请联系 Azure 支持来将你的订阅列入允许列表。

3. 在升级之前，请确保工作负荷已完成运行并处于静止状态。 在专用 SQL 池作为“计算优化第 2 代”层级专用 SQL 池回到联机状态之前，会出现几分钟的停机。 选择“升级”  ：

   ![Upgrade_2](./media/upgrade-to-latest-generation/upgrade-to-gen2-2.png)

4. 通过在 Azure 门户中检查状态来 **监视升级**：

   ![Upgrade3](./media/upgrade-to-latest-generation/upgrade-to-gen2-3.png)

   升级过程的第一个步骤将经历缩放操作（“升级 - 脱机”），其中，所有会话都将终止并且连接将会断开。

   升级过程的第二个步骤是数据迁移（“升级 - 联机”）。 数据迁移是一个联机缓慢执行的后台进程。 此进程使用本地 SSD 缓存，缓慢地将列式数据从旧的存储体系结构移动到新的存储体系结构。 在此期间，专用 SQL 池会处于联机状态，方便进行查询和加载。 你的数据将可供查询，无论它是否已迁移。 数据迁移以可变速率进行，具体取决于数据大小、性能级别和列存储段的数目。

5. **可选建议**：在缩放操作完成后，你可以加速执行数据迁移后台进程。 可以通过以更大的 SLO 和资源类对要查询的所有主要列存储表运行 [Alter Index rebuild](sql-data-warehouse-tables-index.md) 来强制数据移动。 与缓慢执行的后台进程（可能需要数小时才能完成，具体取决于表的数量和大小）相对比，此操作是 **脱机** 的。 但是，一旦完成，数据迁移将更快，因为新的增强的存储体系结构具有高质量的行组。

> [!NOTE]
> Alter Index rebuild 是一项脱机操作，在重新生成完成之前，这些表将不可用。

以下查询会生成加快数据迁移时所需的 Alter Index Rebuild 命令：

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON ['
       + Schema_name(tbl.schema_id) + '].['
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE
                                                         WHEN (
                                                     (SELECT Count(*)
                                                      FROM   sys.partitions
                                                             part2
                                                      WHERE  part2.index_id
                                                             = idx.index_id
                                                             AND
                                                     idx.object_id =
                                                     part2.object_id)
                                                     > 1 ) THEN
              ' PARTITION = '
              + Cast(part.partition_number AS NVARCHAR(256))
              ELSE ''
                                                       END ) + '; SELECT ''[' +
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' +
              Object_name(idx.object_id) + '] ' + (
              CASE
                WHEN ( (SELECT Count(*)
                        FROM   sys.partitions
                               part2
                        WHERE
                     part2.index_id =
                     idx.index_id
                     AND idx.object_id
                         = part2.object_id) > 1 ) THEN
              ' PARTITION = '
              + Cast(part.partition_number AS NVARCHAR(256))
              + ' completed'';'
              ELSE ' completed'';'
                                                    END )
FROM   sys.indexes idx
       INNER JOIN sys.tables tbl
               ON idx.object_id = tbl.object_id
       LEFT OUTER JOIN sys.partitions part
                    ON idx.index_id = part.index_id
                       AND idx.object_id = part.object_id
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE';
```

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>通过 Azure 门户使用还原从 Azure 地理区域进行升级

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>使用 Azure 门户创建用户定义的还原点

1. 登录到 [Azure 门户](https://portal.azure.cn/)。

2. 导航到要为其创建还原点的专用 SQL 池。

3. 在“概述”部分的顶部，选择“+ 新建还原点”  。

    ![新建还原点](./media/upgrade-to-latest-generation/creating_restore_point_0.png)

4. 为还原点指定一个名称。

    ![还原点的名称](./media/upgrade-to-latest-generation/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>使用 Azure 门户还原活动或暂停的数据库

1. 登录到 [Azure 门户](https://portal.azure.cn/)。
2. 导航到要从其进行还原的专用 SQL 池。
3. 在“概述”部分的顶部，选择“还原”  。

    ![ 还原概述](./media/upgrade-to-latest-generation/restoring_0.png)

4. 选择“自动还原点”或“用户定义的还原点”。   对于用户定义的还原点，请“选择用户定义的还原点”  或“新建用户定义的还原点”  。 对于服务器，请选择“新建”  ，然后在支持第 2 代的地理区域中选择一台服务器。

    ![自动还原点](./media/upgrade-to-latest-generation/restoring_1.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>使用 PowerShell 从 Azure 地理区域还原

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要恢复数据库，请使用 [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) cmdlet。

> [!NOTE]
> 可以执行到第 2 代的异地还原！ 若要执行此操作，请将一个第 2 代 ServiceObjectiveName（例如 DW1000 **c**）指定为可选参数。

1. 打开 Windows PowerShell。
2. 连接到 Azure 帐户，并列出与帐户关联的所有订阅。
3. 选择包含要还原的数据库的订阅。
4. 获取要恢复的数据库。
5. 为数据库创建恢复请求，指定一个第 2 代 ServiceObjectiveName。
6. 验证异地还原的数据库的状态。

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> 若要在完成还原后配置数据库，请参阅 [Configure your database after recovery](../../azure-sql/database/disaster-recovery-guidance.md?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)（在恢复后配置数据库）。

如果源数据库启用了 TDE，则已恢复的数据库会启用 TDE。


如果数据仓库出现任何问题，请联系 Azure 支持。

## <a name="next-steps"></a>后续步骤

升级后的专用 SQL 池处于联机状态。 若要利用增强的体系结构，请参阅[用于工作负荷管理的资源类](resource-classes-for-workload-management.md)。
