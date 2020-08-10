---
title: include 文件
description: include 文件
services: virtual-machines
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 03/27/2018
ms.date: 08/10/2020
ms.testscope: no
ms.testdate: 07/06/2020
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 321c32c4f92be4b0f3265ede0a2ada6a4188ad71
ms.sourcegitcommit: ac70b12de243a9949bf86b81b2576e595e55b2a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87919269"
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>了解 VM 重启 - 维护和停机
有三种情况可能会导致 Azure 中的虚拟机受影响：计划外硬件维护、意外停机、计划内维护。

* 当 Azure 平台预测硬件或者与物理计算机关联的任何平台组件即将发生故障时，就会发生计划外硬件维护事件。 当预测到故障时，平台会发出计划外硬件维护事件，以便减少对托管在该硬件上的虚拟机的影响。 Azure 使用[实时迁移](/virtual-machines/linux/maintenance-and-updates)技术将虚拟机从故障硬件迁移到正常的物理计算机。 实时迁移是一项 VM 保留操作，只能短时间暂停虚拟机。 将会保留内存、打开的文件以及网络连接，但事件前后的性能可能会降低。 在无法使用实时迁移的情况下，VM 会出现意外停机，如下所述。

* 意外停机指虚拟机的硬件或物理基础设施意外出现故障。 此类故障可能包括：本地网络故障、本地磁盘故障，或者其他机架级别的故障。 检测到此类故障时，Azure 平台会自动将虚拟机迁移到同一数据中心内的正常物理机（进行修复）。 在修复过程中，虚拟机会经历停机（重启），在某些情况下会丢失临时驱动器。 始终会保留附加的 OS 和数据磁盘。

    在发生会影响整个数据中心甚至整个区域的服务中断或灾难时（这种情况很少见），虚拟机也可能会停机。 针对这种情况，Azure 提供了保护选项，包括[配对区域](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions)。
    
    <!-- Not Available on [availability zones](../articles/availability-zones/az-overview.md)-->

* **计划内维护事件**是指由 Azure 对底层 Azure 平台进行的定期更新，用于改进虚拟机运行时所在的平台基础结构的总体可靠性、性能和安全性。 大多数此类更新在执行时不会影响虚拟机或云服务（请参阅 [VM 保留维护](/virtual-machines/windows/preserving-maintenance)）。 虽然 Azure 平台会尝试在所有可能的情况下都使用 VM 保留维护，但在罕见情况下，这些更新需要重启虚拟机，否则无法将所需更新应用到底层基础结构。 在这种情况下，可以在合适的时间窗口为 VM 启动维护，通过“维护-重新部署”操作来执行 Azure 计划内维护。 有关详细信息，请参阅[虚拟机的计划内维护](/virtual-machines/windows/planned-maintenance/)。

要减轻一个或多个此类事件引发的停机所造成的影响，我们建议遵循以下最佳做法以提高虚拟机的可用性：

* [在可用性集中配置多个虚拟机以确保冗余]
* [在可用性集中对 VM 使用托管磁盘]
* [使用计划事件主动响应影响 VM 的事件](../articles/virtual-machines/linux/scheduled-events.md)
* [将每个应用程序层配置到不同的可用性集中]
* [将负载均衡器与可用性集组合在一起]
    
<!-- Not Available on * [Use availability zones to protect from datacenter level failures]-->
<!-- Not Available on Availability Zone -->
<!--Not Avaialble on ## Use availability zones to protect from datacenter level failures-->
    
## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>在可用性集中配置多个虚拟机以确保冗余
可用性集是另一种数据中心配置，用于提供 VM 冗余和可用性。 数据中心内的这种配置可以确保在发生计划内或计划外维护事件时，至少有一个虚拟机可用，并满足 99.95% 的 Azure SLA 要求。 有关详细信息，请参阅[虚拟机的 SLA](https://www.azure.cn/support/sla/virtual-machines/)。

> [!IMPORTANT]
> 可用性集内的单实例虚拟机对于所有操作系统磁盘和数据磁盘都应使用高级 SSD，以便满足至少为 99.9% 的虚拟机连接性 SLA。

<!--Not Available on or Ultra Disk-->

基础 Azure 平台为可用性集中的每个虚拟机分配一个**更新域**和一个**容错域**。 对于给定的可用性集，默认情况下会分配五个非用户可配置的更新域（可以增加 Resource Manager 部署以最多提供 20 个更新域），以指示可同时重新启动的虚拟机和底层物理硬件组。 如果单个可用性集中配置了超过 5 个虚拟机，第 6 个虚拟机放置在第 1 个虚拟机所在的更新域中，第 7 个虚拟机放置在第 2 个虚拟机所在的更新域中，依此类推。 在计划内维护期间，更新域的重启顺序可能不会按序进行，但一次只重启一个更新域。 重启的更新域有 30 分钟的时间进行恢复，此时间过后，就会在另一更新域上启动维护操作。

容错域定义一组共用一个通用电源和网络交换机的虚拟机。 默认情况下，在可用性集中配置的虚拟机隔离在 Resource Manager 部署的最多三个容错域（经典部署的两个容错域）中。 虽然将虚拟机置于可用性集中并不能让应用程序免受特定于操作系统或应用程序的故障的影响，但可以限制潜在物理硬件故障、网络中断或电源中断的影响。

<!--Image reference-->

:::image type="content" source="./media/virtual-machines-common-manage-availability/ud-fd-configuration.png" alt-text="更新域和容错域配置的概念图":::

<a name="use-managed-disks-for-vms-in-an-availability-set"></a>
## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>为可用性集中的 VM 使用托管磁盘
如果当前使用的 VM 没有托管磁盘，则强烈建议[在可用性集中转换 VM，以便使用托管磁盘](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md)。

通过确保可用性集中的 VM 的磁盘彼此之间完全隔离以避免单点故障，[托管磁盘](../articles/virtual-machines/windows/managed-disks-overview.md)为可用性集提供了更佳的可靠性。 为此，会自动将磁盘放置在不同的存储容错域（存储群集）中，并使它们与 VM 容错域一致。 如果某个存储容错域因硬件或软件故障而失败，则只有其磁盘在该存储容错域上的 VM 实例会失败。
:::image type="content" source="./media/virtual-machines-common-manage-availability/md-fd-updated.png" alt-text="托管磁盘 FD":::

<!--MOONCAKE CUSTOMIZTION:  CORRECT ON keep fixed by region currently in China-->

> [!IMPORTANT]
> 托管可用性集的容错域数目前在中国按区域保持固定 - 每个区域两个。 您可以通过运行以下脚本来查看每个区域的容错域。

<!--MOONCAKE CUSTOMIZTION:  CORRECT ON keep fixed by region currently in China-->

```powershell
Get-AzComputeResourceSku | where{$_.ResourceType -eq 'availabilitySets' -and $_.Name -eq 'Aligned'}
```

```azurecli 
az vm list-skus --resource-type availabilitySets --query '[?name==`Aligned`].{Location:locationInfo[0].location, MaximumFaultDomainCount:capabilities[0].value}' -o Table
```

> [!NOTE]
> 在某些情况下，同一可用性集中的 2 个 VM 可能共享同一个容错域。 可以通过进入您的可用性集并检查**容错域**列来确认这一点。
> 这可能是由于部署 VM 时采用了以下顺序而导致的：
> - 部署第一个 VM
> - 停止/解除分配第一个 VM
> - 在这种情况下部署第二个 VM，可能会在与第一个 VM 相同的容错域中创建第二个 VM 的 OS 磁盘，因此第二个 VM 也将位于同一容错域中。 
> 若要避免此问题，建议不要在两次部署之间停止/解除分配 VM。

如果计划使用包含非托管磁盘的 VM，请按下述针对存储帐户的最佳做法进行操作。在这些存储帐户中，VM 的虚拟硬盘 (VHD) 以[页 Blob](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) 形式存储。

1. 将与同一 VM 关联的所有磁盘（OS 和数据）放置在同一存储帐户中
2. 在向存储帐户中添加更多 VHD 之前，请**查看 Azure 存储帐户中非托管磁盘的数量[限制](../articles/storage/blobs/scalability-targets-premium-page-blobs.md)**
3. **为可用性集中的每个 VM 使用单独的存储帐户。** 同一可用性集中的多个 VM 不能共享存储帐户。 不同可用性集中的 VM 共享存储帐户是可以接受的，只要遵循上述最佳做法即可 :::image type="content" source="./media/virtual-machines-common-manage-availability/umd-updated.png" alt-text="托管磁盘 FD":::

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>使用计划事件主动响应影响事件的 VM

如果订阅[计划事件](../articles/virtual-machines/linux/scheduled-events.md)，则将通知 VM 即将发生会对 VM 造成影响的维护事件。 启用计划事件后，可在执行维护活动之前为虚拟机提供最少的时间。 例如，可能会影响 VM 的主机 OS 更新将作为事件排队等候，通知中将详述其影响，以及在未采取任何操作的情况下执行维护的时间。 当 Azure 检测到即将发生可能影响 VM 的硬件失败时，计划事件也会排队等候，以便决定执行修复的时间。 客户可以使用事件在维护前执行任务，例如，保存状态、故障转移到辅助 VM 等。 完成用于妥善处理维护事件的逻辑后，可批准未完成的计划事件，以允许平台继续进行维护。

<!--Not Available on availability zones-->

<a name="combine-a-load-balancer-with-availability-zones-or-sets"></a>
## <a name="combine-a-load-balancer-with-availability-sets"></a>将负载均衡器与可用性集组合在一起
将 [Azure 负载均衡器](../articles/load-balancer/load-balancer-overview.md)与可用性集组合在一起，以获取最大的应用程序复原能力。 Azure 负载均衡器将流量分布到多个虚拟机中。 对于标准层虚拟机来说，Azure 负载均衡器已包括在内。 并非所有虚拟机层都包括 Azure 负载均衡器。 有关对虚拟机进行负载均衡的更多信息，请阅读[对虚拟机进行负载均衡](../articles/virtual-machines/virtual-machines-linux-load-balance.md)。

如果没有将负载均衡器配置为对多个虚拟机上的流量进行平衡，则任何计划内维护事件都会影响唯一的那个处理流量的虚拟机，导致应用程序层中断。 将同一层的多个虚拟机置于相同的负载均衡器和可用性集下可以确保至少有一个虚拟机实例能够持续处理流量。

<!--Not Available on [Load balance VMs across all availability zones by using the Azure CLI](../articles/load-balancer/load-balancer-standard-public-zone-redundant-cli.md)-->

<!-- Link references -->

[在可用性集中配置多个虚拟机以确保冗余]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[将负载均衡器与可用性集组合在一起]: #combine-a-load-balancer-with-availability-zones-or-sets

<!-- Not Avaiable on [Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets -->

[在可用性集中对 VM 使用托管磁盘]: #use-managed-disks-for-vms-in-an-availability-set

<!-- Not Available on [Use availability zones to protect from datacenter level failures]: #use-availability-zones-to-protect-from-datacenter-level-failures-->
<!-- Update_Description: update meta properties, wording update, update link -->