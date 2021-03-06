---
title: 排查 Azure 中的 Windows 虚拟机部署问题 | Azure
description: 排查在 Azure 资源管理器部署模型中部署 Windows 虚拟机时遇到的问题。
services: virtual-machines-windows
manager: dcscontentpm
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
origin.date: 11/01/2018
author: rockboyfor
ms.date: 09/07/2020
ms.testscope: yes
ms.testdate: 08/31/2020
ms.author: v-yeche
ms.openlocfilehash: 2eeaf792285373cc397f3903b2945c30a15b2459
ms.sourcegitcommit: 93309cd649b17b3312b3b52cd9ad1de6f3542beb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93106331"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>排查 Azure 中的 Windows 虚拟机部署问题

若要排查 Azure 中虚拟机 (VM) 的部署问题，请查看[常见问题](#top-issues)了解常见故障和解决方法。

如果对本文中的任何观点存在疑问，可以联系 [Azure 支持](https://support.azure.cn/support/contact/)上的 Azure 专家。 或者，也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://support.azure.cn/support/support-azure/)提交请求。

## <a name="top-issues"></a>常见问题
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>群集不支持请求的 VM 大小
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- 使用更小的 VM 大小来重试请求。
- 如果无法更改请求的 VM 大小：
    - 停止可用性集中的所有 VM。 依次单击“资源组”> 资源组 >“资源”> 可用性集 >“虚拟机”> 虚拟机 >“停止”。
    - 在所有 VM 都停止后，创建相应大小的 VM。
    - 先启动新 VM，再选择所有已停止的 VM 并单击“启动”。

## <a name="the-cluster-does-not-have-free-resources"></a>群集没有可用资源
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- 请稍后重试请求。
- 如果新 VM 属于不同的可用性集
    - 在不同的可用性集（位于同一区域）中创建 VM。
    - 将新 VM 添加到同一虚拟网络。

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>如何使用 Windows 客户端映像并将其部署到 Azure？

如果你有相应的 Visual Studio（以前为 MSDN）订阅，可以在 Azure 中使用 Windows 7 或 Windows 10 开展开发/测试方案。 [本文](../windows/client-images.md)概述在 Azure 中运行 Windows 客户端和使用 Azure 库映像所要满足的条件。

<!--Not Available on Windows 8-->

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>如何使用混合使用权益 (HUB) 部署虚拟机？

有多种不同方法可使用 Azure 混合使用权益部署 Windows 虚拟机。

对于企业协议订阅：

•   可从通过 Azure 混合使用权益预配的特定市场映像中部署 VM。

对于企业协议：

•   可以上传自定义 VM，并使用 Resource Manager 模板或 Azure PowerShell 进行部署。

有关详细信息，请参阅以下资源：

 - [Azure 混合使用权益概述](https://www.azure.cn/pricing/hybrid-benefit//)

 - [可下载的常见问题解答](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [适用于 Windows Server 和 Windows 客户端的 Azure 混合使用权益](../windows/hybrid-use-benefit-licensing.md)。

 - [如何在 Azure 中使用混合使用权益](https://docs.microsoft.com/archive/blogs/azureedu/how-can-i-use-the-hybrid-use-benefit-in-azure)

<!--Not Available ## How do I activate my monthly credit for Visual studio Enterprise (BizSpark)-->
<!--Not Available ## How to add Enterprise Dev/Test to my Enterprise Agreement (EA) to get access to Window client images?-->
## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>我的 Windows N 系列 VM 缺少驱动程序

有关基于 Windows 的 VM 的驱动程序的安装说明，请单击[此处](../sizes-gpu.md#supported-operating-systems-and-drivers)。

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>在我的 N 系列 VM 中找不到 GPU 实例

若要利用 Azure N 系列 VM 的 GPU 功能，部署后必须在每个 VM 上安装图形驱动程序。 驱动程序安装信息可以在[这里](../sizes-gpu.md#supported-operating-systems-and-drivers)找到。

## <a name="are-n-series-vms-available-in-my-region"></a>我所在的区域是否提供 N 系列 VM？

可以从[可用产品(按区域)](https://azure.microsoft.com/regions/services) 以及[此处](https://www.azure.cn/pricing/details/virtual-machines)的定价来查看可用性。

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>可以在 Azure 中使用和部署哪些客户端映像，以及如何获取它们？

只要你有相应的 Visual Studio（以前为 MSDN）订阅，就可以在 Azure 中使用 Windows 7 或 Windows 10 开展开发/测试方案。 

- Azure 库的[符合条件的开发/测试产品/服务](../windows/client-images.md#eligible-offers)中提供了 Windows 10 映像。 
- 任一产品类型的 Visual Studio 订阅者也可以[适当地准备和创建](../windows/prepare-for-upload-vhd-image.md) 64 位 Windows 7 或 Windows 10 映像，并[将其上载到 Azure](../windows/upload-generalized-managed.md)。 仅限有效的 Visual Studio 订户用于开发/测试目的。

<!--Not Available on Windows 8-->

[本文](../windows/client-images.md)概述在 Azure 中运行 Windows 客户端和使用 Azure 库映像所要满足的条件。

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>重设 VM 大小时，看不到所需的 VM 大小系列。

当 VM 正在运行时，将其部署到物理服务器。 Azure 区域中的物理服务器被分在常见物理硬件群集组中。 需要将 VM 移到其他硬件群集才能重设 VM 大小，具体操作因部署 VM 所用部署模型而异。

- 对于在经典部署模型中部署的 VM，必须删除并重新部署云服务部署，才能将 VM 大小更改为其他大小系列。

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

- 对于在资源管理器部署模型中部署的 VM，必须先停止可用性集中的所有 VM，然后才能更改可用性集中任何 VM 的大小。

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>在可用性集中部署时，列出的 VM 大小不受支持。

请选择可用性集的群集支持的大小。 建议在创建可用性集时选择所需的最大 VM 大小，并将它率先部署到可用性集。

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>能否将现有经典 VM 添加到可用性集？

是的。 可以将现有经典 VM 添加到新的或现有的可用性集。 有关详细信息，请参阅[将现有虚拟机添加到可用性集](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine)。

## <a name="next-steps"></a>后续步骤
如果对本文中的任何观点存在疑问，可以联系 [Azure 支持](https://support.azure.cn/support/contact/)上的 Azure 专家。

或者，也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://support.azure.cn/support/support-azure/)提交请求。

<!-- Update_Description: update meta properties, wording update, update link -->