---
title: Azure Cosmos DB 的区域可用性
description: 本文说明了 Azure Cosmos DB 存在的区域以及不同云环境。
author: rockboyfor
ms.author: v-yeche
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 07/23/2019
ms.date: 08/17/2020
ms.testscope: no
ms.testdate: ''
ms.custom: seodec18
ms.openlocfilehash: f7e1919b61e97b039ce2e698b1d71acbee00e361
ms.sourcegitcommit: 84606cd16dd026fd66c1ac4afbc89906de0709ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88222447"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Azure Cosmos DB 的区域可用性

Azure Cosmos DB 是 Azure 中的一项基础服务，默认情况下，在[所有提供 Azure 的区域](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=,china-non-regional,china-east,china-east-2,china-north,china-north-2,&regions=,china-non-regional,china-east,china-east-2,china-north,china-north-2,&regions=all)均可使用。

<!--Not Available on :::image type="content" source="./media/regional-presence/regional-presence.png" alt-text="Azure Cosmos DB regional availability":::-->
Cosmos DB 在提供给客户的所有五种不同的 Azure 云环境中均可使用：

* Microsoft 与中国最大的 Internet 提供商世纪互联展开独特合作，推出了 Azure 中国世纪互联****。

## <a name="regional-presence-with-multiple-region-distribution"></a>采用多区域分布的区域覆盖

Azure Cosmos DB 公开的所有 API（包括 SQL、MongoDB、Cassandra、Gremlin 和表）默认情况下在所有 Azure 中国区域均可用。 例如，所有 Azure 中国区域中的 Azure Cosmos DB 可以公开 MongoDB 和 Cassandra API。

<!-- Not Available on Cassandra, Gremlin, and Azure Table storage-->
<!-- Not Available on Germany, Government, and Department of Defense (DoD) regions-->

Azure Cosmos DB 是[多区域分布式](distribute-data-globally.md)数据库服务。 可将任意数量的 Azure 区域与 Azure Cosmos 帐户相关联，并且数据会自动且透明地得到复制。 可随时向 Azure Cosmos 帐户添加或从中删除区域。 借助统包多区域分布功能和多主控复制协议，Azure Cosmos DB 能够在第 99 百分位提供不到 10 毫秒的读写延迟、提供 99.999 的读写可用性并能够在与 Azure Cosmos 帐户相关的所有区域中灵活扩展预配的读写吞吐量。 Azure Cosmos DB 还提供五种定义完善的一致性模型，可以选择对数据应用特定的一致性模型。 最后，Azure Cosmos DB 是业内唯一一种提供综合[服务级别协议 (SLA)](https://www.azure.cn/support/sla/cosmos-db/) 的数据库服务，包括预配的吞吐量、第 99 百分位的延迟、高可用性和一致性。 以上功能在所有 Azure 云中推出。

多区域复制支持仅限于 Azure 中国世纪互联云环境。

<!--Not Available on  For example, Azure Cosmos DB accounts in *Azure public* can replicate to any Azure Region within *Azure public* - but cannot be replicated to Azure Regions in *Azure China 21Vianet*.-->

## <a name="next-steps"></a>后续步骤

现可通过以下文章了解 Azure Cosmos DB 的核心概念：

* [多区域数据分布](distribute-data-globally.md)
* [如何管理 Azure Cosmos DB 帐户](manage-account.md)
* [为 Azure Cosmos 容器和数据库预配吞吐量](set-throughput.md)
* [Azure Cosmos DB SLA](https://www.azure.cn/support/sla/cosmos-db/)

<!-- Update_Description: update meta properties, wording update, update link -->
