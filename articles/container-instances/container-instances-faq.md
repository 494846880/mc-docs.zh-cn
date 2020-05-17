---
title: 常见问题
description: 有关 Azure 容器实例服务的常见问题解答
author: rockboyfor
ms.topic: article
origin.date: 04/10/2020
ms.date: 04/30/2020
ms.author: v-yeche
ms.openlocfilehash: 5d7d4043b8ea1d276453f94e835e72857a3178ef
ms.sourcegitcommit: 2d8950c6c255361eb6c66406988e25c69cf4e0f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2020
ms.locfileid: "83392230"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>有关 Azure 容器实例的常见问题解答

本文解答有关 Azure 容器实例的常见问题。

## <a name="deployment"></a>部署

### <a name="how-large-can-my-container-image-be"></a>容器映像的最大大小是什么？

可在 Azure 容器实例上部署的容器映像的最大大小为 15 GB。 根据部署时的确切可用性，也许可以部署更大的映像，但不保证可以做到这一点。

容器映像的大小会影响部署所需的时间，因此，一般情况下，我们会尽可能地保留较小的容器映像。

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>如何加速容器的部署？

由于部署速度的主要决定因素之一是映像大小，因此请找到减小大小的办法。 删除不需要的层，或者减小映像中的层大小（选择较精简的基础 OS 映像）。 例如，如果运行 Linux 容器，请考虑使用 Alpine 作为基础映像，而不是使用完整的 Ubuntu Server。

<!--Not Available on  Similarly, for Windows containers, use a Nano Server base image if possible. -->

还应查看 Azure 容器映像中预缓存映像的列表（通过[列出缓存的映像](https://docs.microsoft.com/rest/api/container-instances/listcachedimages) API 获取）。 也许可以换出某个预缓存映像的映像层。 

有关如何减少容器启动时间，请参阅[更详细的指南](container-instances-troubleshooting.md#container-takes-a-long-time-to-start)。

<!--Not Available on ### What Windows base OS images are supported?-->

<!--Not Available on #### Windows Server 2016 base image-->
<!--Not Available on #### Windows Server 2019 and client base images (preview)-->

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>应在容器中使用哪个 .NET 或 .NET Core 映像层？ 

使用符合要求的最小映像。 对于 Linux，可以使用 *runtime-alpine* .NET Core 映像，从 .NET Core 2.1 版本开始就已支持此映像。

<!--Not Available on  For Windows, if you are using the full .NET Framework, then you need to use a Windows Server Core image (runtime-only image, such as  *4.7.2-windowsservercore-ltsc2016*). Runtime-only images are smaller but do not support workloads that require the .NET SDK.-->

## <a name="availability-and-quotas"></a>可用性和配额

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>应为容器或容器组分配多少核心和内存？

这实际上取决于工作负荷。 请从较小的数量着手，并测试容器的性能如何。 [监视 CPU 和内存资源用量](container-instances-monitor.md)，然后根据在容器中部署的进程类型增加核心或内存。 

另外，请务必检查所部署到的区域的[资源可用性](container-instances-region-availability.md#availability---general)，以确定每个容器组的可用 CPU 核心数和内存上限。 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>ACI 在哪个底层基础结构上运行？

Azure 容器实例旨在用作无服务器按需容器服务，因此，我们希望你专注于开发容器，而不用考虑基础结构！ 对于那些有好奇心的或者想要比较性能的客户，可以在采用各种 SKU 的 Azure VM 集（主要是 F 和 D 系列）上运行 ACI。 我们会持续开发和优化服务，预计这种情况将来会改变。 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>我想要在 ACI 上部署几千个核心 - 是否可以提高配额？

在某些情况下是可以提高的。 有关当前的配额以及可以请求提高的限制，请参阅[配额和限制](container-instances-quotas.md)一文。

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>是否可以部署 4 个以上的核心和 16 GB 以上的 RAM？

还不可以。 目前这些数字是容器组的上限。 如有具体的要求和请求，请联系 Azure 支持部门。 

### <a name="when-will-aci-be-in-a-specific-region"></a>ACI 何时在特定的区域推出？

[此处](container-instances-region-availability.md#availability---general)发布了当前推出的区域。 如果你在特定的区域需要满足某项要求，请联系 Azure 支持部门。

## <a name="features-and-scenarios"></a>功能和方案

### <a name="how-do-i-scale-a-container-group"></a>如何缩放容器组？

目前，容器或容器组不可缩放。 如果需要运行更多实例，请使用我们的 API 进行自动化，并创建更多请求以在服务中创建容器组。 

<!--Not Available on ### What features are available to instances running in a custom VNet?-->
<!--Not Available on [deploy container groups in an Azure virtual network](container-instances-vnet.md)-->

## <a name="pricing"></a>定价

### <a name="when-does-the-meter-start-running"></a>计量器何时开始运行？

计算的容器组持续时间从我们开始提取你的第一个容器映像（对于新部署）或重启容器组（如果已部署）开始，到容器组停止为止。 请参阅[容器实例定价](https://www.azure.cn/pricing/details/container-instances/)中的详细信息。

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>停止容器后是否会停止计费？

停止整个容器组后，计量器将停止运行。 只要容器组中的容器正在运行，我们就会保留资源，以防需要再次启动容器。 

## <a name="next-steps"></a>后续步骤

* [详细了解](container-instances-overview.md) Azure 容器实例。
* [排查 Azure 容器实例中的常见问题](container-instances-troubleshooting.md)

<!-- Update_Description: update meta properties, wording update, update link -->