---
title: include 文件
description: include 文件
services: virtual-machines-windows
author: rockboyfor
ms.service: virtual-machines-windows
ms.topic: include
origin.date: 02/11/2019
ms.date: 08/10/2020
ms.testscope: no
ms.testdate: 05/18/2020
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 2ee55082b5fe130a4c9b57da1d5dc20f58d91daa
ms.sourcegitcommit: ac70b12de243a9949bf86b81b2576e595e55b2a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "88951280"
---
## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla (CUDA) 驱动程序

仅下表中列出的操作系统支持适用于 NCv3 系列 VM 的 NVIDIA Tesla (CUDA) 驱动程序。 在本文发布时，驱动程序下载链接是最新的。 有关最新驱动程序，请访问 [NVIDIA](https://www.nvidia.com/) 网站。

<!-- Not Available on NC, NCv2, and ND-series-->
<!-- Not Available on (optional for NV-series)-->

> [!TIP]
> 作为一种在 Windows Server VM 上手动安装 CUDA 驱动程序的替代方法，可以部署 Azure [数据科学虚拟机](../articles/machine-learning/data-science-virtual-machine/overview.md)映像。 用于 Windows Server 2016 的 DSVM 版本预安装 NVIDIA CUDA 驱动程序、CUDA 深度神经网络库和其他工具。

| 操作系统 | 驱动程序 |
| -------- |------------- |
| Windows Server 2016 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

<!-- Not Available on ### NVIDIA GRID drivers-->

<!-- Not Available on NV-series-->
<!-- Update_Description: update meta properties, wording update -->