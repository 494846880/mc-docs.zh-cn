---
title: 调整查询和索引工作负荷的容量
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索中调整分区和副本计算机资源，其中每个资源按照可计费搜索单位定价。
manager: nitinme
author: HeidiSteen
ms.author: v-tawe
ms.service: cognitive-search
ms.topic: conceptual
origin.date: 09/08/2020
ms.date: 11/27/2020
ms.openlocfilehash: 663c131b480f42a307ee2da95172814d392a1e53
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96300716"
---
# <a name="adjust-the-capacity-of-an-azure-cognitive-search-service"></a>调整 Azure 认知搜索服务的容量

在[预配搜索服务](search-create-service-portal.md)和锁定特定的定价层之前，请花几分钟时间来了解容量如何工作以及如何调整副本和分区来适应工作负荷波动。

容量是[所选层](search-sku-tier.md)（层确定硬件特征）以及预期工作负荷所需的副本和分区组合的一个功能因素。 你可以单独增加或减少副本或分区数。 增加或减少容量所需的时间为 15 分钟到几个小时，具体取决于调整的层和大小。

修改副本和分区的分配时，建议使用 Azure 门户。 该门户针对允许的组合强制实施限制，使其低于层的上限。 但是，如果需要使用基于脚本或基于代码的预配方法，[Azure PowerShell](search-manage-powershell.md) 或[管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/services) 是替代的解决方案。

## <a name="concepts-search-units-replicas-partitions-shards"></a>概念：搜索单位、副本、分区、分片

容量以搜索单位表示，可以通过分区和副本的组合进行分配，并使用基础分片机制来支持灵活的配置：

| 概念  | 定义|
|----------|-----------|
|搜索单位 | 总可用容量（36 个单位）的单一增量。 它还是 Azure 认知搜索服务的计费单位。 至少需要一个单位才能运行服务。|
|*副本* | 是搜索服务的实例，主要用于对查询操作进行负载均衡。 每个副本承载着索引的一个副本。 如果分配三个副本，则可以使用索引的三个副本来为查询请求提供服务。|
|分区 | 为读/写操作（例如，在重建或刷新索引时进行的此类操作）提供物理存储和 I/O。 每个分区都有总体索引的一个切片。 如果分配三个分区，则索引将划分为三个部分。 |
|分片 | 索引的一个区块。 Azure 认知搜索将每个索引划分为分片，以便更快地添加分区（通过将分片移动到新的搜索单位）。|

下图显示了副本、分区、分片与搜索单位之间的关系。 它显示了一个示例，该示例说明了在具有两个副本和两个分区的服务中，单个索引如何跨越四个搜索单位。 这四个搜索单位每个都只存储索引的一半分片。 左列中的搜索单位存储分片的第一半，构成第一个分区，而右列中的搜索单位存储分片的第二半，构成第二个分区。 由于有两个副本，因此每个索引分片有两个副本。 顶部行中的搜索单位存储着一个副本，构成第一个副本，而底部行中的搜索单位存储着另一个副本，构成第二个副本。

:::image type="content" source="media/search-capacity-planning/shards.png" alt-text="搜索索引是跨分区进行分片的。":::

上图只是一个示例。 分区和副本有许多可能的组合，最多可包含 36 个搜索单位（总计）。

在认知搜索中，分片管理是实现细节且不可配置，但知道索引是分片的有助于你了解排名和自动完成行为中的偶然异常：

+ 排名异常：搜索评分首先在分片级别计算，然后聚合成单个结果集。 根据分片内容的特征，一个分片中的匹配项的排名可能高于另一个分片中的匹配项。 如果你在搜索结果中发现不直观的排名，则很可能是由于分片的影响，尤其是在索引较小的情况下。 你可以通过选择[在整个索引中全局计算评分](index-similarity-and-scoring.md#scoring-statistics-and-sticky-sessions)来避免这些排名异常，但这样做会导致性能下降。

+ 自动完成异常：自动完成查询（根据仅输入了一部分内容的单词的前几个字符进行匹配）接受一个模糊参数，该参数允许有微小的拼写差异。 对于自动完成，模糊匹配被限制为当前分片中的字词。 例如，如果某个分片包含“Microsoft”，并且输入了部分字词“micor”，则搜索引擎将针对该分片中的“Microsoft”进行匹配，但不会在包含索引剩余部分的其他分片中进行匹配。

## <a name="when-to-add-nodes"></a>何时添加节点

最初为服务分配了由一个分区和一个副本组成的最低级别的资源。

单个服务必须具有足够的资源才能处理所有工作负荷（索引和查询）。 没有任何工作负荷在后台运行。 如果查询请求在性质上不频繁，则可以计划索引编制，但如果不这样做，服务也不会排定任务的优先级。 此外，在内部更新服务或节点时，一定程度的冗余也会销蚀查询性能。

根据惯例，搜索应用程序所需的副本数往往多过分区数，尤其是在服务操作偏向于查询工作负荷的情况下。 [高可用性](#HA)部分将解释原因。

> [!NOTE]
添加更多的副本或分区会增加运行服务的成本，并可能在结果的排序方式上引入细微变化。 请务必查看[定价计算器](https://www.azure.cn/pricing/calculator/)来了解添加更多节点对计费造成的影响。 [以下图表](#chart)可帮助你交叉参考特定配置所需的搜索单位数。 有关其他副本如何影响查询处理的详细信息，请参阅[排序结果](search-pagination-page-layout.md#ordering-results)。

## <a name="how-to-allocate-replicas-and-partitions"></a>如何分配副本和分区

1. 登录到 [Azure 门户](https://portal.azure.cn/)，并选择搜索服务。

1. 在“设置”中，打开“规模”页以修改副本和分区。   

   以下屏幕截图显示了预配有一个副本和分区的标准服务。 底部的公式指示正在使用多少个搜索单位 (1)。 如果单位价格为 $100（非实际价格），则运行此服务的每月成本平均为 $100。

   ![显示当前值的“规模”页](media/search-capacity-planning/1-initial-values.png "显示当前值的“规模”页")

1. 使用滑块增加或减少分区数。 底部的公式指示正在使用多少个搜索单位。

   此示例各使用一个副本和分区将容量翻倍。 请注意搜索单位计数；现在有 4 个搜索单位，因为计费公式是副本数乘以分区数 (2 x 2)。 将容量翻倍不仅仅会使运行服务的成本翻倍。 如果搜索单位的成本是 $100，则新的每月费用将是 $400。

   有关每个层的当前单位成本，请访问[定价页](https://www.azure.cn/pricing/details/search/)。

   ![添加副本和分区](media/search-capacity-planning/2-add-2-each.png "添加副本和分区")

1. 选择“保存”以确认更改。

   ![确认对规模和计费所做的更改](media/search-capacity-planning/3-save-confirm.png "确认对规模和计费所做的更改")

   更改容量需要花费几个小时才能完成。 一旦启动更改过程，就无法将其取消；系统不会实时监视副本和分区的调整。 但是，在更改过程中，会一直显示以下消息。

   ![门户中的状态消息](media/search-capacity-planning/4-updating.png "门户中的状态消息")

> [!NOTE]
> 预配服务后，无法升级到更高的层。 必须在新层中创建搜索服务，并重新加载索引。 有关服务预配的帮助，请参阅[在门户中创建 Azure 认知搜索服务](search-create-service-portal.md)。
>
> 此外，分区和副本是由服务在内部专门管理的。 不存在处理器关联或者将工作负荷分配到特定节点的概念。
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>分区和副本组合

“基本”服务可以包含一个分区以及最多三个副本，上限为三个 SU。 唯一可调整的资源是副本。 至少需要两个副本才能实现查询的高可用性。

所有“标准”和“存储优化”搜索服务可以采用副本和分区的以下组合，但不能超过 36 个 SU 的限制。 

|   | **1 个分区** | **2 个分区** | **3 个分区** | **4 个分区** | **6 个分区** | **12 个分区** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 个副本** |1 个 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 个副本** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 个副本** |3 SU |6 SU |9 SU |12 SU |18 SU |36 个 SU |
| **4 个副本** |4 SU |8 SU |12 SU |16 SU |24 SU |不适用 |
| **5 副本** |5 SU |10 SU |15 SU |20 SU |30 SU |不适用 |
| **6 个副本** |6 SU |12 SU |18 SU |24 SU |36 个 SU |不适用 |
| **12 副本** |12 SU |24 SU |36 个 SU |不适用 |空值 |不适用 |

Azure 网站上详细说明了 SU、定价和容量。 有关详细信息，请参阅 [Pricing Details](https://www.azure.cn/pricing/details/search/)（定价详细信息）。

> [!NOTE]
> 副本数和分区数必须能被 12 整除（具体而言，为 1、2、3、4、6、12）。 这是因为，Azure 认知搜索将每个索引预先分割为 12 个分片，以便将其平均分散到所有分区。 例如，如果服务有三个分区，而你创建了新索引，则每个分区将包含该索引的四个分片。 Azure 认知搜索为索引分片的方法属于实现细节，在将来的版本中可能发生变化。 尽管目前的分区数为 12，但请不要料想将来该数字永远都是 12。
>

<a id="HA"></a>

## <a name="high-availability"></a>高可用性

由于扩展的过程比较简单而且相对较快，因此我们通常建议从一个分区以及一个或两个副本开始，并随着不断构建查询卷而进行扩展。 查询工作负荷主要是在副本上运行。 如果需要更高的吞吐量或高可用性，也许需要增加副本。

针对高可用性的一般建议是：

* 对于只读工作负荷（查询），需要有两个副本才能实现高可用性

* 对于读/写工作负荷（查询以及添加、更新或删除单个文档时的索引编制），需有三个或更多个副本才能实现高可用性

Azure 认知搜索的服务级别协议 (SLA) 针对查询操作，以及由文档添加、更新或删除操作构成的索引更新。

基本层最多能有一个分区和三个副本。 如果希望灵活地立即响应对索引编制和查询吞吐量的需求波动，请考虑使用标准层中的一个。  如果你发现存储要求的增长速度大大超过了可用的吞吐量，请考虑使用某个“存储优化”层。

## <a name="disaster-recovery"></a>灾难恢复

目前没有任何内置的机制可实现灾难恢复。 添加分区或副本并不是实现灾难恢复目标的正确策略。 最常见的方法是通过在另一区域中设置另一个搜索服务，在服务级别添加冗余。 与索引重建期间的可用性一样，重定向或故障转移逻辑必须来自代码。

## <a name="estimate-replicas"></a>评估副本

在生产服务中，应分配三个副本来满足 SLA 的要求。 如果遇到查询性能缓慢的问题，可以添加副本，使更多的索引副本联机，以支持更大的查询工作负荷，并通过多个副本对请求进行负载均衡。

我们不会提供有关需要多少个副本来适应查询负载的指导。 查询性能取决于查询复杂性和争用资源的工作负荷。 尽管添加副本会明显提高性能，但结果不一定有线性改善：添加三个副本并不保证带来三倍的吞吐量。

有关评估解决方案的 QPS 的指导，请参阅[性能缩放](search-performance-optimization.md)和[监视查询](search-monitor-queries.md)

## <a name="estimate-partitions"></a>评估分区

[所选的层](search-sku-tier.md)确定了分区大小和速度，每个层已根据一组适合不同方案的特征进行优化。 如果选择更高端的层，所需的分区数可能比使用 S1 时更少。 需要通过自我引导式测试解答的一个问题是，对于性能而言，使用更大且更昂贵的分区，是否比在较低层上预配的服务中使用两个更廉价的分区更好。

需要以近乎实时的速度刷新数据的搜索应用程序，需要的分区数在比例上要多于副本。 添加分区可将读/写操作分配到更多的计算资源。 此外，还能提供更多磁盘空间来存储更多的索引和文档。

索引越大，查询所需的时间就越长。 因此，可能发现，每次增加分区都需要按比例少量增加副本。 查询和查询卷的复杂性影响查询执行的速度。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [选择 Azure 认知搜索的定价层](search-sku-tier.md)