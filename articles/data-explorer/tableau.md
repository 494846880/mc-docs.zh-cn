---
title: 使用 Azure 数据资源管理器 ODBC 连接器直观显示 Tableau 数据
description: 本文介绍如何使用开放式数据库连接 (ODBC) 连接到 Azure 数据资源管理器以在 Tableau 中直观显示数据。
author: orspod
ms.author: v-tawe
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
origin.date: 06/03/2019
ms.date: 05/09/2020
ms.openlocfilehash: 9076430974312b18cd8955bce95869c2943ae23d
ms.sourcegitcommit: bfbd6694da33f703481386f2a3f16850c4e94bfa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/15/2020
ms.locfileid: "83417564"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>在 Tableau 中直观显示来自 Azure 数据资源管理器的数据

 [Tableau](https://www.tableau.com/) 是一种商业智能可视化分析平台。 若要从 Tableau 连接到 Azure 数据资源管理器，并从示例群集引入数据，请使用 SQL Server 开放式数据库连接 (ODBC) 驱动程序。 

## <a name="prerequisites"></a>必备条件

若要完成本文，需要满足以下条件：

* [使用 ODBC 连接到 Azure 数据资源管理器](connect-odbc.md)，即使用 SQL Server ODBC 驱动程序从 Tableau 连接到 Azure 数据资源管理器。 

* Tableau 桌面版、完整版或[试用](https://www.tableau.com/products/desktop/download)版。

* 包括 StormEvents 示例数据的群集。 有关详细信息，请参阅[创建 Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)和[将示例数据引入 Azure 数据资源管理器](ingest-sample-data.md)。

    [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>在 Tableau 中直观显示数据 

在配置完 ODBC 后，可以将示例数据引入 Tableau。

1. 在 Tableau Desktop 的左侧菜单中，选择“其他数据库(ODBC)”。 

    ![与 ODBC 连接](media/tableau/connect-odbc.png)

1. 至于“DSN”，请选择为 ODBC 创建的数据源，然后选择“登录”。  

    ![ODBC 登录](media/tableau/odbc-sign-in.png)

1. 至于“数据库”，请选择在示例群集上创建的数据库，例如“TestDatabase”。   至于“架构”，请选择“dbo”；至于“表”，请选择“StormEvents”示例表。    

    ![选择数据库和表](media/tableau/select-database-table.png)

1. Tableau 现在会显示示例数据的架构。 选择“立即更新”，将数据引入 Tableau。 

    ![更新数据](media/tableau/update-data.png)

    将数据引入以后，Tableau 会显示类似于下图的数行数据。

    ![结果集](media/tableau/result-set.png)

1. 现在可以根据你从 Azure 数据资源管理器引入的数据在 Tableau 中创建可视化效果。 有关详细信息，请参阅 [Tableau 学习](https://www.tableau.com/learn)。

## <a name="next-steps"></a>后续步骤

* [Azure 数据资源管理器的编写查询](write-queries.md)