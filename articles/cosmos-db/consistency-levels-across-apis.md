---
title: 一致性级别和 Azure Cosmos DB API
description: 了解 Azure Cosmos DB 与 Apache Cassandra、MongoDB 中不同 API 之间的一致性级别映射
author: rockboyfor
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 04/23/2020
ms.date: 06/22/2020
ms.author: v-yeche
ms.reviewer: sngun
ms.openlocfilehash: 7989f35d34add5a4641f1d0ff5762d1a776b166e
ms.sourcegitcommit: 48b5ae0164f278f2fff626ee60db86802837b0b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85098326"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>一致性级别和 Azure Cosmos DB API

Azure Cosmos DB 为常用数据库提供对与线路协议兼容的 API 的本机支持。 这些数据库包括 MongoDB、Apache Cassandra、Gremlin 和 Azure 表存储。 这些数据库既没有提供准确定义的一致性模型，也没有为一致性级别提供由 SLA 支持的保证。 它们通常仅提供 Azure Cosmos DB 提供的五个一致性模型的一个子集。 

使用 SQL API、Gremlin API 和表 API 时，会使用 Azure Cosmos 帐户上配置的默认一致性级别。 

使用 Cassandra API 或 Azure Cosmos DB 的 MongoDB API 时，应用程序会获得一整套分别由 Apache Cassandra 和 MongoDB 提供的一致性级别，其一致性和持续性保证甚至更强。 本文档介绍了与 Apache Cassandra 和 MongoDB 一致性级别对应的 Azure Cosmos DB 一致性级别。

<a name="cassandra-mapping"></a>
## <a name="mapping-between-apache-cassandra-and-azure-cosmos-db-consistency-levels"></a>Apache Cassandra 与 Azure Cosmos DB 一致性级别之间的映射

与 Azure Cosmos DB 不同，Apache Cassandra 本身并不提供精确定义的一致性保证。  与之相反，Apache Cassandra 提供一个写入一致性级别和一个读取一致性级别，以便进行高可用性、一致性和延迟方面的权衡。 使用 Azure Cosmos DB 的 Cassandra API 时： 

* Apache Cassandra 的写入一致性级别映射到在 Azure Cosmos 帐户上配置的默认一致性级别。 写入操作 (CL) 的一致性不能因请求的不同而（即按请求）更改。

* Azure Cosmos DB 会将 Cassandra 客户端驱动程序指定的读取一致性级别动态映射到根据读取请求动态配置的某个 Azure Cosmos DB 一致性级别。 

下表演示了在使用 Cassandra API 时，如何将本机 Cassandra 一致性级别映射到 Azure Cosmos DB 的一致性级别：  

[![Cassandra 一致性模型映射](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

<a name="mongo-mapping"></a>
## <a name="mapping-between-mongodb-and-azure-cosmos-db-consistency-levels"></a>MongoDB 与 Azure Cosmos DB 一致性级别之间的映射

与 Azure Cosmos DB 不一样，本机 MongoDB 并不提供精确定义的一致性保证。 与之相反，本机 MongoDB 允许用户配置下述一致性保证：写入关注、读取关注以及 isMaster 指令 - 目的是将读取操作定向到主副本或辅助副本，以便实现所需的一致性级别。 

使用 Azure Cosmos DB 的 API for MongoDB 时，MongoDB 驱动程序会将写入区域视为主副本，所有其他区域为读取副本。 可以选择将哪个与 Azure Cosmos 帐户关联的区域作为主副本。 

在使用 Azure Cosmos DB 的 API for MongoDB 时：

* 写入关注映射到在 Azure Cosmos 帐户上配置的默认一致性级别。

* Azure Cosmos DB 会将 MongoDB 客户端驱动程序指定的读取关注动态映射到根据读取请求动态配置的某个 Azure Cosmos DB 一致性级别。 

* 可以将与 Azure Cosmos 帐户关联的特定区域批注为“主区域”，方法是将该区域设置为第一个可写区域。 

下表演示了在使用 Azure Cosmos DB 的 API for MongoDB 时，如何将本机 MongoDB 写入/读取关注映射到 Azure Cosmos 的一致性级别：

[![MongoDB 一致性模型映射](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>后续步骤

详细了解 Azure Cosmos DB API 与开源 API 之间的一致性级别和兼容性。 请参阅以下文章：

* [各种一致性级别的可用性和性能权衡](consistency-levels-tradeoffs.md)
* [Azure Cosmos DB 的用于 MongoDB 的 API 支持的 MongoDB 功能](mongodb-feature-support.md)
* [Azure Cosmos DB Cassandra API 支持的 Apache Cassandra 功能](cassandra-support.md)

<!-- Update_Description: update meta properties, wording update -->