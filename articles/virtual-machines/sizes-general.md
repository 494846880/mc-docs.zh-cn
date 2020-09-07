---
title: Azure VM 大小 - 常规用途 | Azure
description: 列出了 Azure 中虚拟机可用的不同常规用途大小。 列出了有关此系列中各大小的 vCPU 数、数据磁盘数和 NIC 数以及存储吞吐量和网络带宽的信息。
author: rockboyfor
ms.service: virtual-machines
ms.subservice: sizes
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
origin.date: 02/20/2020
ms.date: 08/31/2020
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.openlocfilehash: 079de0052dba0bf6a6b218889585203e00fceff5
ms.sourcegitcommit: 63a4bc7c501fb6dd54a31d39c87c0e8692ac2eb0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89052441"
---
<!--Verified successfully-->
<!--Partical Content from verified-->
# <a name="general-purpose-virtual-machine-sizes"></a>常规用途虚拟机大小

常规用途 VM 大小提供均衡的 CPU 与内存之比。 适用于测试和开发、小到中型数据库和低到中等流量 Web 服务器。 本文提供了有关常规用途计算产品/服务的信息。

- [Av2 系列](av2-series.md) VM 可以部署在各种不同的硬件类型和处理器上。 A 系列 VM 的 CPU 性能和内存配置非常适合部署和测试等入门级工作负荷。 根据硬件限制大小，为运行中的实例提供一致的处理器性能，不论硬件部署的位置。 若要判断此大小部署所在的物理硬件，请从虚拟机中查询虚拟硬件。 示例用例包括开发和测试服务器、低流量 Web 服务器、中小型数据库、概念证明和代码存储库。

    <!--Not Available on A8 - A11 VMs are planned for retirement on 3/2021-->

- [B 系列可突增](sizes-b-series-burstable.md) VM 非常适合于并非持续需要 CPU 完全性能的工作负荷，例如 Web 服务器、小型数据库以及开发和测试环境。 这些工作负荷通常具有可突增的性能要求。 B 系列使这些客户能够购买具有高性价比基线性能的 VM 大小，允许 VM 实例在 VM 使用的性能小于其基线性能时积累积分。 如果 VM 已累积了积分，则 VM 可以在应用程序需要更高的 CPU 性能时突增到 VM 的基线之上，使用最多达到 100% 的 vCPU。

    <!--Not Available on [Dav4 and Dasv4-series](dav4-dasv4-series.md)-->
    <!--Not Available on [DCv2-series](dcv2-series.md)-->

- [Dv2 和 Dsv2 系列](dv2-dsv2-series.md) VM 是原 D 系列的后续产品，具有更强大的 CPU 和最优 CPU 到内存配置，使其适合于大多数生产工作负荷。 Dv2 系列比 D 系列快大约 35%。 Dv2 系列在第 2 代英特尔® 至强® 铂金 8272CL (Cascade Lake)、英特尔® 至强® 8171M 2.1GHz (Skylake)、英特尔® 至强® E5-2673 v4 2.3 GHz (Broadwell) 或英特尔® 至强® E5-2673 v3 2.4 GHz (Haswell) 处理器上运行，并采用英特尔睿频加速技术 2.0。 Dv2 系列的内存和磁盘配置与 D 系列相同。

- [Dv3 和 Dsv3 系列](dv3-dsv3-series.md) VM 在采用超线程配置的第 2 代英特尔® 至强® 铂金 8272CL (Cascade Lake)、英特尔® 至强® 8171M 2.1GHz (Skylake)、英特尔® 至强® E5-2673 v4 2.3 GHz (Broadwell) 或英特尔® 至强® E5-2673 v3 2.4 GHz (Haswell) 处理器上运行，为大多数常规用途工作负载提供更好的价值定位。 在磁盘和网络限制已基于核心进行了调整以适应超线程技术的同时，内存已扩展（从 ~3.5 GiB/vCPU 到 4 GiB/vCPU）。 Dv3 系列不再具有 D/Dv2 系列的高内存 VM 大小，那些已移至内存优化的 [Ev3 和 Esv3 系列](ev3-esv3-series.md)。
  
    <!--Not Available on - [Dv4 and Dsv4-series](dv4-dsv4-series.md)-->

    <!--Not Available on - [Ddv4 and Ddsv4-series](ddv4-ddsv4-series.md)-->

## <a name="other-sizes"></a>其他大小

- [计算优化](sizes-compute.md)
- [内存优化](sizes-memory.md)
    
    <!--Not Available on - [Storage optimized](sizes-storage.md)-->
    
- [GPU 优化](sizes-gpu.md)

    <!--Not Available on - [High performance compute](sizes-hpc.md)-->
    
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。

有关 Azure 如何命名其 VM 的详细信息，请参阅 [Azure 虚拟机大小命名约定](./vm-naming-conventions.md)。

<!-- Update_Description: update meta properties, wording update, update link -->