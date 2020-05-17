---
title: 为 Stretch Database 启用透明数据加密
description: 为 Azure 上的 SQL Server Stretch Database 启用透明数据加密 (TDE)
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
origin.date: 06/14/2016
ms.date: 05/18/2020
author: rockboyfor
ms.author: v-yeche
ms.reviewer: jroth
manager: digimobile
ms.custom: seo-lt-2019
ms.openlocfilehash: 04461e957094e8a4b49884efbfc94b2f95bc24d0
ms.sourcegitcommit: bfbd6694da33f703481386f2a3f16850c4e94bfa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/15/2020
ms.locfileid: "83417784"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>为 Azure 上的 Stretch Database 启用透明数据加密 (TDE)
> [!div class="op_single_selector"]
> * [Azure 门户](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

透明数据加密 (TDE) 无需更改应用程序，即可对静止的数据库、关联的备份和事务日志执行实时加密和解密，帮助防止恶意活动的威胁。

TDE 使用称为数据库加密密钥的对称密钥来加密整个数据库的存储。 数据库加密密钥由内置服务器证书保护。 内置服务器证书对每个 Azure 服务器都是唯一的。 Azure 至少每隔 90 天自动轮换这些证书。 有关 TDE 的一般说明，请参阅[透明数据加密 (TDE)]。

## <a name="enabling-encryption"></a>启用加密

对于存储从启用延伸的 SQL Server 数据库迁移的数据的 Azure 数据库，若要启用 TDE，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.cn)中打开数据库
2. 在数据库边栏选项卡中，单击“设置”  按钮
3. 选择“透明数据加密”选项  ![][1]
4. 选择“打开”  设置，然后选择“保存”  ![][2]

## <a name="disabling-encryption"></a>禁用加密

对于存储从启用延伸的 SQL Server 数据库迁移的数据的 Azure 数据库，若要禁用 TDE，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.cn)中打开数据库
2. 在数据库边栏选项卡中，单击“设置”  按钮
3. 选择“**透明数据加密**选项
4. 选择“**关闭**”设置，并选择“**保存**”

<!--Anchors-->

[透明数据加密 (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx

<!--Image references-->

[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png

<!--Link references-->

<!-- Update_Description: update meta properties, wording update, update link -->