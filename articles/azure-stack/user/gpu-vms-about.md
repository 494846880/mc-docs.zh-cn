---
title: Azure Stack Hub 上的图形处理单元 (GPU) 虚拟机 (VM)
description: Azure Stack Hub 中的 GPU 计算参考。
author: WenJason
ms.author: digimobile
ms.service: azure-stack
ms.topic: reference
origin.date: 07/07/2020
ms.date: 08/31/2020
ms.reviewer: kivenkat
ms.lastreviewed: 07/07/2020
ms.openlocfilehash: 0d305393e840a0e2a73e76141ead946009a66f29
ms.sourcegitcommit: 4e2d781466e54e228fd1dbb3c0b80a1564c2bf7b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871688"
---
# <a name="graphics-processing-unit-gpu-virtual-machine-vm-on-azure-stack-hub"></a>Azure Stack Hub 上的图形处理单元 (GPU) 虚拟机 (VM)

*适用于：Azure Stack 集成系统*

在本文中，可以了解 Azure Stack Hub 多节点系统上支持哪些图形处理单元(GPU) 模型。 还可以找到有关安装与 GPU 配合使用的驱动程序的说明。 Azure Stack Hub 中的 GPU 支持实现了诸如人工智能、训练、推理和数据可视化之类的解决方案。 AMD Radeon Instinct Mi25 可用于支持图形密集型应用程序，如 Autodesk AutoCAD。

可以在公共预览期间从三个 GPU 模型中进行选择。 它们分别为 NVIDIA V100、NVIDIA T4 和 AMD Mi25 GPU。 这些物理 GPU 支持以下 Azure N 系列虚拟机 (VM) 类型，如下所示：
- [NCv3](/virtual-machines/ncv3-series)
- NCas_T4_v3

> [!IMPORTANT]  
> 公共预览版目前支持 Azure Stack Hub GPU。 若要参与预览，请完成 [aka.ms/azurestackhubgpupreview](https://aka.ms/azurestackhubgpupreview) 上的表单。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 

## <a name="ncv3"></a>NCv3

NCv3 系列 VM 采用 NVIDIA Tesla V100 GPU。 客户可将这些更新的 GPU 用于传统的 HPC 工作负荷，例如油藏模拟、DNA 测序、蛋白质分析、Monte Carlo 模拟和其他工作负荷。 

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大 NIC 数 |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v3    | 6  | 112 | 736  | 1 | 16 | 12 | 4 |
| Standard_NC12s_v3   | 12 | 224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v3   | 24 | 448 | 2948 | 4 | 64 | 32 | 8 |

## <a name="ncas_t4_v3"></a>NCas_T4_v3

采用这种新的 NVIDIA T4 VM 大小可以在 Azure Stack Hub 上运行轻型 ML、推理和可视化工作负载。 当前，无法通过门户将此 VM 大小用于部署，而需要改用 powershell/cli。


| 大小 | vCPU | 内存:GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大 NIC 数 | 
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 | 1 | 16 | 8 | 4 | 
| Standard_NC8as_T4_v3 |4 |56 | 1 | 16 | 16 | 8 | 
| Standard_NC16as_T4_v3 |4 |112 | 1 | 16 | 32 | 8 | 
| Standard_NC64as_T4_v3 |4 |448 | 4 | 64 | 32 | 8 | 


## <a name="patch-and-update-fru-behavior-of-vms"></a>VM 的修补升级以及 FRU 行为 

GPU VM 将在执行 Azure Stack Hub 的修补升级 (PnU) 以及硬件更换 (FRU) 等操作期间停机。 下表介绍了在这些活动期间观察到的 VM 状态，以及用户在这些操作后可以执行（以使这些 VM 再次可用）的手动操作。 

| 操作 | PnU - 快速更新 | PnU - 完全更新、OEM 更新 | FRU | 
| --- | --- | --- | --- | 
| VM 状态  | 在更新期间和更新后不可用，无需手动启动操作 | 在更新期间不可用。 在更新后可用，需要手动操作 | 在更新期间不可用。 在更新后可用，需要手动操作| 
| 手动操作 | 如果 VM 需要在更新期间可用，并且可用 GPU 分区存在，则可通过单击“重启”按钮从门户重启 VM。 在更新后，使用“重启”按钮从门户重启 VM | 在更新期间无法使 VM 可用。 完成更新后，需要使用“停止”按钮停止对 VM 解除分配，并使用“开始”按钮开始备份 | 在更新期间无法使 VM 可用。完成更新后，需要使用“停止”按钮停止对 VM 解除分配，并使用“开始”按钮开始备份。| 

## <a name="guest-driver-installation"></a>来宾驱动程序安装 

### <a name="nvidia"></a>NVIDIA

需要安装有 NVIDIA 驱动程序才能在 VM 上运行 CUDA 或 GRID 工作负载。 在使用该扩展在 VM 上安装 GRID 驱动程序之前，请确保已设置了相应的 GRID 许可证以及许可证服务器。 可参阅[此文档](https://docs.nvidia.com/grid/ls/latest/grid-license-server-user-guide/index.html)，了解如何设置许可证服务器。 CUDA 驱动程序不需要许可证服务器。

需要在 VM 上手动安装 NVIDIA CUDA 驱动程序和 GRID 驱动程序。 Tesla CUDA 驱动程序可从 NVIDIA [下载网站](https://www.nvidia.com/Download/index.aspx)获取。 可以通过 NVIDIA 应用程序中心下载 GRID 驱动程序，前提是具有所需的许可证。

## <a name="next-steps"></a>后续步骤 

[Azure Stack VM 功能](azure-stack-vm-considerations.md) 
