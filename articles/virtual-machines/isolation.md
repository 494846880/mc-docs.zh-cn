---
title: Azure 中 VM 的隔离
description: 了解 Azure 中 VM 隔离的工作原理。
author: rockboyfor
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
origin.date: 09/18/2019
ms.date: 08/10/2020
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.openlocfilehash: 174ffe941d02763907ab03f9b78b422848977007
ms.sourcegitcommit: 63a4bc7c501fb6dd54a31d39c87c0e8692ac2eb0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89052368"
---
<!--Verified successfully-->
<!--Rename articles content only-->
# <a name="virtual-machine-isolation-in-azure"></a>Azure 中的虚拟机隔离

Azure 计算提供独立于特定硬件类型并专用于单个客户的虚拟机大小。 独立大小依赖于特定的硬件代系并在其上运行，在硬件代系失效时弃用。

独立虚拟机大小最适合为满足符合性和法规要求等原因而需要与其他客户的工作负载高度隔离的工作负载。  使用独立大小可保证你的虚拟机将是在特定服务器实例上唯一运行的虚拟机。 

另外，由于独立大小的 VM 很大，客户可以选择使用[对嵌套虚拟机的 Azure 支持](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)来细分这些 VM 的资源。

<a name="vm-isolation-size"></a>

当前的独立虚拟机产品/服务包括：
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms

    <!--Not Available on * Standard_GS5-->
    <!--Not Available on * Standard_G5-->

* Standard_F72s_v2

> [!NOTE]
> 独立虚拟机大小的生命周期受硬件限制。 详情请参阅下文

## <a name="deprecation-of-isolated-vm-sizes"></a>弃用独立 VM 大小
由于独立 VM 大小是硬件绑定大小，Azure 将在正式弃用这些大小前 12 个月进行提醒。  Azure 还将为我们的下一个硬件版本提供已更新的独立大小，客户可以考虑将其工作负载转移到该版本上。

| 大小 | 隔离停用日期 | 
| --- | --- |
| Standard_DS15_v2<sup>1</sup> | 2020 年 5 月 15 日 |
| Standard_D15_v2<sup>1</sup>  | 2020 年 5 月 15 日 |

<sup>1</sup> 有关 Standard_DS15_v2 和 Standard_D15_v2 隔离停用计划的详细信息，请参阅常见问题解答

## <a name="faq"></a>常见问题解答
### <a name="q-is-the-size-going-to-get-retired-or-only-isolation-feature-is"></a>问：是要停用大小还是只停用“隔离”功能？
**答**：如果虚拟机大小没有“i”下标，则只会停用“隔离”功能。 如果不需要隔离，则不需要执行任何操作，VM 将继续按预期工作。 例如 Standard_DS15_v2、Standard_D15_v2、Standard_M128ms 等。如果虚拟机大小包括“i”下标，那么该大小将被停用。

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>问：当我的虚拟机落脚于非隔离的硬件上时，是否会出现停机？
**答**：如果不需要隔离，就不需要采取任何行动，也不会有停机时间。

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>问：迁移到非独立虚拟机是否有成本增量？
**答**：否

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>问：其他独立大小将于何时停用？
**答**：我们将在官方弃用独立大小前 12 个月进行提醒。

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>问：我是依赖于白银或黄金耐久性层级的 Azure Service Fabric 客户。 此更改是否会影响我？
**答**：否。 Service Fabric 的[耐久性层级](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)提供的保证即使在此更改发生后也将继续履行。 如果你出于其他原因而需要物理硬件隔离，可能仍需采取上述措施之一。 

<!--Not Available on ### Q: What are the milestones for D15_v2 or DS15_v2 isolation retirement?-->

## <a name="next-steps"></a>后续步骤

客户还可以选择利用[对嵌套虚拟机的 Azure 支持](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)，对这些独立的虚拟机资源进一步细分。

<!-- Update_Description: update meta properties, wording update, update link -->
