---
title: Azure 数据资源管理器数据可视化
description: 了解可视化 Azure 数据资源管理器数据的不同方式
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
origin.date: 06/02/2020
ms.date: 07/08/2020
ms.openlocfilehash: 0e3c62f61b5ed05b5fd10f5466fee4508b0300a6
ms.sourcegitcommit: f4bd97855236f11020f968cfd5fbb0a4e84f9576
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88515667"
---
# <a name="data-visualization-with-azure-data-explorer"></a>使用 Azure 数据资源管理器进行数据可视化 

Azure 数据资源管理器是一项适用于日志和遥测数据的快速且高度可缩放的数据探索服务，可以用来针对大量数据构建复杂的分析解决方案。 Azure 数据资源管理器集成各种可视化工具，因此可以用来可视化数据，并在组织内共享结果。 该数据可以转换成能够对业务造成影响的可操作见解。

数据可视化和报告是数据分析过程中的关键步骤。 Azure 数据资源管理器支持许多项 BI 服务，因此你可以使用最适合自己方案和预算的一项。

<!--
## Azure Data Explorer dashboards

Azure Data Explorer dashboards is a web application that enables you to run queries and build dashboards in the stand-alone web application, the [Web UI](web-query-data.md). Azure Data Explorer dashboards provide three main advantages:

* Natively export queries from the Web UI to Azure Data Explorer dashboards. 
* Explore the data in the Web UI.
* Optimized dashboard rendering performance.

For more information see, [Visualize data with Azure Data Explorer dashboards](azure-data-explorer-dashboards.md).
-->

## <a name="kusto-query-language-visualizations"></a>Kusto 查询语言可视化

Kusto 查询语言 [`render operator`](/data-explorer/kusto/query/renderoperator) 提供各种可视化效果（如表格、饼图和条形图）来描述查询结果。 查询可视化用于异常情况检测和预测、机器学习等。

## <a name="power-bi"></a>Power BI

Azure 数据资源管理器提供使用各种方法连接到 [Power BI](https://powerbi.microsoft.com) 的功能： 

  * [内置的本机 Power BI 连接器](power-bi-connector.md)

  * [将查询从 Azure 数据资源管理器导入 Power BI](power-bi-imported-query.md)
 
  * [SQL 查询](power-bi-sql-query.md)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure 数据资源管理器提供了使用[内置本机 Excel 连接器](excel-connector.md)连接到 [Microsoft Excel](https://products.office.com/excel)，或者从 Azure 数据资源管理器[导入查询](excel-blank-query.md)到 Excel 的功能。

<!-- ## Grafana -->

<!-- [Grafana](https://grafana.com) provides an Azure Data Explorer plugin that enables you to visualize data from Azure Data Explorer. You [set up Azure Data Explorer as a data source for Grafana, and then visualize the data](/data-explorer/grafana).  -->

## <a name="odbc-connector"></a>ODBC 连接器

Azure 数据资源管理器提供了[开放式数据库连接 (ODBC) 连接器](connect-odbc.md)，因此任何支持 ODBC 的应用程序都可以连接到 Azure 数据资源管理器。

## <a name="tableau"></a>Tableau

Azure 数据资源管理器提供使用 [ODBC 连接器](connect-odbc.md)连接到 [Tableau](https://www.tableau.com) 的功能，然后可[在 Tableau 中直观显示数据](tableau.md)。

## <a name="qlik"></a>Qlik

Azure 数据资源管理器提供使用 [ODBC 连接器](connect-odbc.md)连接到 [Qlik](https://www.qlik.com) 的功能，然后可创建 Qlik Sense 仪表板并直观显示数据。

<!-- > [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  -->

## <a name="sisense"></a>Sisense

Azure 数据资源管理器提供使用 JDBC 连接器连接到 [Sisense](https://www.sisense.com) 的功能。 请[将 Azure 数据资源管理器设置为 Sisense 的数据源，然后将数据可视化](sisense.md)。

## <a name="redash"></a>Redash

可以使用 [Redash](https://redash.io/) 来构建仪表板以及将数据可视化。 [将 Azure 数据资源管理器设置为 Redash 的数据源，然后将数据可视化](redash.md)。
