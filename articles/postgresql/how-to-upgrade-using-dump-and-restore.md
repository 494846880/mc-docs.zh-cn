---
title: 使用转储和还原进行升级 - Azure Database for PostgreSQL - 单一服务器
description: 描述使用转储和还原数据库迁移到更高版本的 Azure Database for PostgreSQL - 单一服务器的脱机升级方法。
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: how-to
origin.date: 11/10/2020
ms.date: 12/14/2020
ms.openlocfilehash: 6f3e8d0ee2e396e0b63f5553c2215e6e64341de8
ms.sourcegitcommit: a8afac9982deafcf0652c63fe1615ba0ef1877be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96850911"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>使用转储和还原升级 PostgreSQL 数据库

通过使用以下方法将数据库迁移到主版本更高的服务器，可以升级部署在 Azure Database for PostgreSQL - 单一服务器中的 PostgreSQL 服务器。
* 使用 PostgreSQL [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 和 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) 的 **脱机** 方法，这种方法会导致迁移数据时停机。 本文档介绍这种升级/迁移方法。
* 使用 [数据库迁移服务](/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal) (DMS) 的 **联机** 方法。 这种方法减少了迁移时的停机时间，使目标数据库与源保持同步，并且可以选择何时进行切换。 但是，使用 DMS 需要满足一些先决条件和限制。 有关详细信息，请参阅 [DMS 文档](/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal)。 

 下表根据数据库大小和方案提供了一些建议。

| **数据库/方案** | **转储/还原（脱机）** | **DMS（联机）** |
| ------ | :------: | :-----: |
| 你的数据库很小，可以承受升级时停机  | X | |
| 小型数据库 (< 10 GB)  | X | X | 
| 中小型数据库 (10 GB - 100 GB) | X | X |
| 大型数据库 (> 100 GB) |  | X |
| 可以承受升级时停机（与数据库大小无关） | X |  |
| 能否满足 DMS [先决条件](/tutorial-azure-postgresql-to-azure-postgresql-online-portal#prerequisites)，包括重启？ |  | X |
| 能否在升级过程中避开 DDL 和未记录的表？ | |  X |

本指南提供了一些脱机迁移方法和示例，说明如何从源服务器迁移到运行更高版本 PostgreSQL 的目标服务器。

> [!NOTE]
> 可以通过多种方式执行 PostgreSQL 转储和还原。 你可以选择使用本指南提供的方法之一进行迁移，也可以根据自己的需求选择任何替代方法。 有关包含附加参数的详细转储和还原语法，请参阅文章 [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 和 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)。 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-database-for-postgresql"></a>将转储和还原与 Azure Database for PostgreSQL 配合使用的先决条件
 
若要逐步执行本操作指南，你需要：

- 运行要升级的 9.5、9.6 或 10 的源 PostgreSQL 数据库
- 具有所需主版本 [Azure Database for PostgreSQL 服务器](quickstart-create-server-database-portal.md)的目标 PostgreSQL 数据库服务器。 
- PostgreSQL 客户端系统，用于运行转储和还原命令。
  - 它可以是安装了 PostgreSQL 以及 [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 和 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) 命令行实用程序的 Linux 或 Windows 客户端。 
- PostgreSQL 客户端最好在与源服务器和目标服务器相同的区域中运行。 


## <a name="additional-details-and-considerations"></a>其他详细信息和注意事项
- 通过单击门户中的“连接字符串”，可以找到源数据库和目标数据库的连接字符串。 
- 你可能正在服务器中运行多个数据库。 可以通过连接到源服务器并运行 `\l` 来查找数据库列表。
- 在目标数据库服务器中创建相应的数据库。
- 可以跳过 `azure_maintenance` 或模板数据库的升级。
- 请参阅上面的表，确定数据库是否适合这种迁移模式。
- 如果要使用 Azure CLI，请注意会话会在 20 分钟后超时。 如果数据库大小小于 10 GB，则有可能在会话不超时的情况下完成升级。否则，可能必须通过其他方法（例如每 10-15 分钟按一次 <Enter> 键）使会话保持打开状态。 


## <a name="example-database-used-in-this-guide"></a>本指南中使用的示例数据库

本指南使用以下源服务器和目标服务器以及数据库名称进行举例说明。

 | **说明** | **值** |
 | ------- | ------- |
 | 源服务器 (v9.5) | pg-95.postgres.database.chinacloudapi.cn |
 | 源数据库 | bench5gb |
 | 源数据库大小 | 5 GB |
 | 源用户名 | pg@pg-95 |
 | 目标服务器 (v11) | pg-11.postgres.database.chinacloudapi.cn |
 | 目标数据库 | bench5gb |
 | 目标用户名 | pg@pg-11 |

## <a name="upgrade-your-databases-using-offline-migration-methods"></a>使用脱机迁移方法升级数据库
可以选择使用此部分中所述的方法之一进行升级。 执行任务时，可以使用以下提示。

- 如果对源数据库和目标数据库使用相同的密码，则可以设置 `PGPASSWORD=yourPassword` 环境变量。  这样就不必在每次运行命令（如 psql、pg_dump 和 pg_restore）时都提供密码。  同样，也可以设置 `PGUSER`、`PGSSLMODE` 等其他变量。请参阅 [PostgreSQL 环境变量](https://www.postgresql.org/docs/11/libpq-envars.html)。
  
- 如果 PostgreSQL 服务器需要 TLS/SSL 连接（默认情况下在 Azure Database for PostgreSQL 服务器中启用），请设置环境变量 `PGSSLMODE=require`，以便 pg_restore 工具使用 TLS 进行连接。 如果不使用 TLS，错误可能会显示为 `FATAL:  SSL connection is required. Please specify SSL options and retry.`

- 在 Windows 命令行中，在运行 pg_restore 命令之前运行命令 `SET PGSSLMODE=require`。 在 Linux 或 Bash 中，在运行 pg_restore 命令之前运行命令 `export PGSSLMODE=require`。

### <a name="method-1-migrate-using-dump-file"></a>方法 1：使用转储文件进行迁移

此方法涉及两个步骤。 第一步是从源服务器创建转储。 第二步是将转储文件还原到目标服务器。 有关更多详细信息，请参阅[使用转储和还原进行迁移](howto-migrate-using-dump-and-restore.md)文档。 如果你的数据库较大，并且客户端系统具有足够的存储空间来存储转储文件，则建议使用此方法。

### <a name="method-2-migrate-using-streaming-the-dump-data-to-the-target-database"></a>方法 2：使用将转储数据流式传输到目标数据库的方法进行迁移

数据库转储直接流式传输到目标数据库服务器，并且不将转储存储在客户端中。 因此，这种方法可用于存储空间有限的客户端。 

1. 使用 `\l` 命令确保目标服务器中存在数据库。 如果数据库不存在，则创建数据库。
   ```azurecli
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```SQL
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. 使用管道将转储和还原作为单个命令行运行。 
    ```azurecli
    pg_dump -Fc -v --mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    例如，应用于对象的

    ```azurecli
    pg_dump -Fc -v --host=pg-95.postgres.database.chinacloudapi.cn --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore -v --no-owner --host=pg-11.postgres.database.chinacloudapi.cn --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. 升级（迁移）过程完成后，即可使用目标服务器测试应用程序。 
4. 对服务器内的所有数据库重复此过程。

 例如，下表说明了使用流式传输转储方法进行迁移所需的时间。 系统使用 [pgbench](https://www.postgresql.org/docs/10/pgbench.html) 填充示例数据。 与 pgbench 生成的表和索引相比，你的数据库可能具有不同数量的对象且大小各异，因此强烈建议测试数据库的转储和还原，以了解升级数据库所需的实际时间。 

| **数据库大小** | **大约需要的时间** | 
| ----- | ------ |
| 1 GB  | 1-2 分钟 |
| 5 GB | 8-10 分钟 |
| 10 GB | 15-20 分钟 |
| 50 GB | 1-1.5 小时 |
| 100 GB | 2.5-3 小时|
   
### <a name="method-3-migrate-using-parallel-dump-and-restore"></a>方法 3：使用并行转储和还原进行迁移 

如果数据库中有几个较大的表，你想对该数据库并行执行转储和还原过程，则可以考虑使用此方法。 客户端系统还需要有足够的存储空间来容纳备份转储。 此并行转储和还原过程减少了完成整个迁移所需的时间。 例如，使用方法 1 和 2 完成 50 GB pgbench 数据库的迁移需要 1-1.5 个小时，而使用此方法所需的时间不到 30 分钟。

1. 对于源服务器中的每个数据库，在目标服务器上创建一个对应的数据库。

   ```bash
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    postgresl> create database myDB;
   ```
   例如，应用于对象的
    ```bash
    psql "host=pg-11.postgres.database.chinacloudapi.cn port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"

    postgres> create database bench5gb;
    postgres> \q
    ```

2. 以目录格式运行 pg_dump 命令，其中作业数 = 4（数据库中的表数）。 对于更大的计算层和更多的表，可以将其增加到更大的数目。 该 pg_dump 将创建一个目录，用于存储每个作业的压缩文件。

    ```bash
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    例如，应用于对象的
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.chinacloudapi.cn --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. 然后在目标服务器上还原备份。
    ```bash
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    例如，应用于对象的
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.chinacloudapi.cn --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

## <a name="next-steps"></a>后续步骤

- 对目标数据库功能满意后，可以删除旧的数据库服务器。 
- 如果要使用与源服务器相同的数据库终结点，则在删除旧的源数据库服务器后，可以使用旧的数据库服务器名称创建只读副本。 建立稳定状态后，可以停止副本，这会将副本服务器提升为独立服务器。 有关更多详细信息，请参阅[复制](./concepts-read-replicas.md)。
- 请记住先在测试环境中测试和验证这些命令，然后再将其用于生产。
