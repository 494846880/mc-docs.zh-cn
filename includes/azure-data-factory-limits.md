---
title: include 文件
description: include 文件
services: data-factory
author: WenJason
ms.service: data-factory
ms.topic: include
origin.date: 01/08/2020
ms.date: 05/11/2020
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: eb46262a63ed95cb836017cd35d054be9023ab2a
ms.sourcegitcommit: 134afb420381acd8d6ae56b0eea367e376bae3ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/15/2020
ms.locfileid: "83435422"
---
Azure 数据工厂是一项多租户服务，具有以下默认限制，目的是确保客户订阅不受彼此工作负荷的影响。 若要将限制提高到订阅的最高限制，请联系支持部门。

### <a name="version-2"></a>版本 2

| 资源 | 默认限制 | 最大限制 |
| -------- | ------------- | ------------- |
| Azure 订阅中的数据工厂 | 800 | 请[联系支持人员](https://support.azure.cn/zh-cn/support/contact/)。 |
| 数据工厂中的实体（例如管道、数据集、触发器、链接服务以及集成运行时）的总数 | 5,000 | 请[联系支持人员](https://support.azure.cn/zh-cn/support/contact/)。 |
| 一个订阅中 Azure-SSIS Integration Runtime 的总 CPU 内核数 | 256 | 请[联系支持人员](https://support.azure.cn/zh-cn/support/contact/)。 |
| 每个数据工厂的并行管道运行数（在工厂中的所有管道之间共享） | 10,000  | 请[联系支持人员](https://support.azure.cn/zh-cn/support/contact/)。 |
| 每个 [Azure Integration Runtime 区域](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)的每个订阅的并发外部活动运行数<br><small>外部活动在集成运行时上托管，但在链接服务（包括存储过程、HDInsights、Web 等）上执行。</small> | 3000 | 请[联系支持人员](https://support.azure.cn/zh-cn/support/contact/)。 |
| 每个 [Azure Integration Runtime 区域](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)的每个订阅的并发管道活动运行数 <br><small>管道活动在集成运行时上执行，包括 Lookup、GetMetadata 和 Delete。</small>| 1000 | 请[联系支持人员](https://support.azure.cn/zh-cn/support/contact/)。 |
| 每个 [Azure Integration Runtime 区域](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)的每个订阅的并发创作操作数<br><small>包括测试连接、浏览器文件夹列表和表列表、预览数据。 | 200 | 请[联系支持人员](https://support.azure.cn/zh-cn/support/contact/)。 |
| 每个管道的最大活动数，包括容器的内部活动 | 40 | 40 |
| 可以针对单个自承载集成运行时创建的最大链接集成运行时数 | 100 | 请[联系支持人员](https://support.azure.cn/zh-cn/support/contact/)。 |
| 每个管道的最大参数个数 | 50 | 50 |
| ForEach 项 | 100,000 | 100,000 |
| ForEach 并行度 | 20 个 | 50 |
| 每个管道的最大排队运行数 | 100 | 100 |
| 每个表达式的字符数 | 8,192 | 8,192 |
| 最小翻转窗口触发间隔 | 15 分钟 | 15 分钟 |
| 管道活动运行的最大超时时间 | 7 天 | 7 天 |
| 管道对象的每个对象字节数<sup>2</sup> | 200 KB | 200 KB |
| 数据集和链接服务对象的每个对象字节数<sup>2</sup> | 100 KB | 2,000 KB |
| 每个复制活动运行的数据集成单元数<sup>1</sup> | 256 | 请[联系支持人员](https://support.azure.cn/zh-cn/support/contact/)。 |
| 编写 API 调用 | 1,200/小时<br/><br/> 此限制是由 Azure 资源管理器而不是 Azure 数据工厂所强加的。 | 请[联系支持人员](https://support.azure.cn/zh-cn/support/contact/)。 |
| 读取 API 调用 | 12,500/小时<br/><br/> 此限制是由 Azure 资源管理器而不是 Azure 数据工厂所强加的。 | 请[联系支持人员](https://support.azure.cn/zh-cn/support/contact/)。 |
| 每分钟监视的查询数 | 1,000 | 请[联系支持人员](https://support.azure.cn/zh-cn/support/contact/)。 |
| 每分钟的实体 CRUD 操作数 | 50 | 请[联系支持人员](https://support.azure.cn/zh-cn/support/contact/)。 |

<sup>1</sup> 数据集成单元 (DIU) 用于云到云复制操作，详见[数据集成单元（版本 2）](../articles/data-factory/copy-activity-performance.md#data-integration-units)。 有关计费的信息，请参阅 [Azure 数据工厂定价](https://www.azure.cn/pricing/details/data-factory/)。

<sup>2</sup> 管道、数据集和链接服务对象代表工作负荷的逻辑分组。 对这些对象的限制与可以使用 Azure 数据工厂移动或处理的数据量无关。 可以缩放数据工厂以处理 PB 量级的数据。

