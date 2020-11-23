---
title: 方案 - 路由到共享服务 VNet
titleSuffix: Azure Virtual WAN
description: 路由方案 - 设置路由以访问共享服务 VNet，其中包含你希望每个 VNet 和分支访问的工作负荷。
services: virtual-wan
ms.service: virtual-wan
ms.topic: conceptual
origin.date: 09/22/2020
author: rockboyfor
ms.date: 11/16/2020
ms.testscope: no
ms.testdate: 09/28/2020
ms.author: v-yeche
ms.custom: fasttrack-edit
ms.openlocfilehash: 89d70c815914813b6d826191ba20495d6d21709d
ms.sourcegitcommit: 39288459139a40195d1b4161dfb0bb96f5b71e8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590794"
---
<!--Verified successfully for only charactors-->
# <a name="scenario-route-to-shared-services-vnets"></a>方案：路由到共享服务 VNet

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 此方案的目标是设置路由以访问共享服务 VNet，其中包含你希望每个 VNet 和分支 (VPN/ER/P2S) 访问的工作负荷。 这些共享工作负荷的示例可能包括具有域控制器或文件共享等服务的虚拟机。

<!--Not Available on  [Azure Private Endpoints](../private-link/private-endpoint-overview.md)-->

有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="design"></a><a name="design"></a>设计

我们可以使用连接性矩阵来汇总此方案的各项要求：

**连接性矩阵**

| 源             | 到:   |*隔离的 VNet*|*共享 VNet*|*分支*|
|---|---|---|---|---|
|**隔离的 VNet**|&#8594;|        | 直接 | 直接 |
|**共享 VNet** |&#8594;| 直接 | 直接 | 直接 |
|**分支** |&#8594;| 直接 | 直接 | 直接 |

上表中的各单元格描述了虚拟 WAN 连接（流的“源”端，行标题）是否与目标（流的“目标”端，斜体形式的列标题）通信。 在此方案中，没有防火墙或网络虚拟设备，因此通信直接通过虚拟 WAN 进行（因此在表中使用“直接”一词）。

与[隔离 VNet 方案](scenario-isolate-vnets.md)类似，此连接性矩阵提供了两种不同的行模式，它们会转换为两个路由表（共享服务 VNet 和分支具有相同的连接要求）。 虚拟 WAN 已经具有默认路由表，因此我们还需要一个自定义路由表，在本例中，我们将其称为 RT_SHARED。

VNet 将与 RT_SHARED 路由表关联。 由于它们需要连接到分支和共享服务 VNet，因此共享服务 VNet 和分支需要传播到 RT_SHARED（否则 VNet 无法了解分支前缀和共享 VNet 前缀）。 由于分支始终关联到默认路由表，并且共享服务 VNet 的连接要求相同，因此我们也将共享服务 VNet 关联到默认路由表。

因此，最终设计如下：

* 隔离的虚拟网络：
    * 关联的路由表：RT_SHARED
    * 传播到路由表：**默认**
* 共享服务虚拟网络：
    * 关联的路由表：**默认**
    * 传播到路由表：RT_SHARED 和 Default
* 分支：
    * 关联的路由表：**默认**
    * 传播到路由表：RT_SHARED 和 Default

> [!NOTE]
> 如果将虚拟 WAN 部署在多个区域，则需要在每个中心创建 RT_SHARED 路由表，并且需要使用传播标签将每个共享服务 VNet 和分支连接中的路由传播到每个虚拟中心的路由表。

有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="workflow"></a><a name="workflow"></a>工作流

若要配置此方案，请考虑以下步骤：

1. 标识共享服务 VNet。
2. 创建一个自定义路由表。 在本例中，我们将路由表称为 RT_SHARED。 有关创建路由表的步骤，请参阅[如何配置虚拟中心路由](how-to-virtual-hub-routing.md)。 使用以下值作为准则：

    * **关联**
        * 对于除共享服务 VNet ***外的 VNet***，请选择要隔离的 VNet。 这意味着，所有这些 VNet（共享服务 VNet 除外）都能够基于 RT_SHARED 路由表的路由来访问目标。

    * **传播**
        * 对于分支，除了可能已经选择的任何其他路由表之外，还需要将路由传播到此路由表。 此步骤可让 RT_SHARED 路由表了解所有分支连接（VPN/ER/用户 VPN）中的路由。
        * 对于 VNet，请选择“共享服务 VNet”。 此步骤可让 RT_SHARED 路由表了解共享服务 VNet 连接中的路由。

最终的路由配置如下图所示：

:::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="共享服务 VNet" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

<!-- Update_Description: update meta properties, wording update, update link -->