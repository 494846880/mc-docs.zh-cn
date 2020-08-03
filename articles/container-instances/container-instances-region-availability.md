---
title: 资源可用性（按区域）
description: Azure 容器实例服务的计算和内存资源在不同 Azure 区域的可用性。
ms.topic: article
origin.date: 04/27/2020
ms.date: 08/03/2020
ms.author: v-yeche
ms.openlocfilehash: 772d79e3b0cb445d2f5c006991e9e990af47a022
ms.sourcegitcommit: 692b9bad6d8e4d3a8e81c73c49c8cf921e1955e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87426408"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Azure 容器实例在 Azure 区域的资源可用性

本文按目标操作系统详细介绍了 Azure 容器实例计算、内存和存储资源在 Azure 区域中的可用性。 

提供的值是指部署一个[容器组](container-instances-container-groups.md)时可以使用的最大资源。 在本文发布时，值是最新的。 

> [!NOTE]
> 在这些资源限制内创建的容器组受部署区域内可用性的限制。 某个区域负载较重时，部署实例时可能会失败。 若要减少此类部署失败，请尝试部署具有较低资源设置的实例，或稍后尝试部署，或在具有可用资源的其他区域中进行部署。

若要了解部署中的配额和其他限制，请参阅 [Azure 容器实例的配额和限制](container-instances-quotas.md)。

## <a name="availability---general"></a>可用性 - 常规

以下区域和最大资源可供包含 Linux 和[受支持的](container-instances-faq.md#what-windows-base-os-images-are-supported)基于 Windows Server 2016 的容器的容器组使用。

<!--AVAILABLE ON CHINAEAST2 SITE till on 07/21/2020-->

| 区域 | 操作系统 | 最大 CPU | 最大内存 (GB) | 存储器 (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| 中国东部 2 | Linux | 2 | 8 | 50 |
| 中国东部 2 | Windows | 2 | 8 | 50 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>可用性 - Windows Server 2019 LTSC，1809 部署（预览版）

以下区域和最大资源可供包含基于 Windows Server 2019 的容器（预览版）的容器组使用。

| 区域 | 操作系统 | 最大 CPU | 最大内存 (GB) | 存储器 (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| 中国东部 2 | Windows | 2 | 3.5 | 20 个 |

<a name="availability---virtual-network-deployment"></a>
## <a name="availability---virtual-network-deployment"></a>可用性 - 虚拟网络部署

以下区域和最大资源可供部署在 [Azure 虚拟网络](container-instances-vnet.md)中的容器组使用。

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>可用性 - GPU 资源（预览版）

以下是使用 [GPU 资源](container-instances-gpu.md)（预览版）部署的容器组可以使用的区域和最大资源数。

> [!IMPORTANT]
> GPU 资源仅在请求后可用。 若要请求访问 GPU 资源，请提交 [Azure 支持请求][azure-support]。

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>后续步骤

如果希望看到更多的区域，或者希望提高资源可用性，请通过 [aka.ms/aci/feedback](https://aka.ms/aci/feedback) 告知我们的团队。

有关容器实例部署故障排除的信息，请参阅[排查 Azure 容器实例的部署问题](container-instances-troubleshooting.md)。

[azure-support]: https://support.azure.cn/support/support-azure/

<!-- Update_Description: update meta properties, wording update, update link -->