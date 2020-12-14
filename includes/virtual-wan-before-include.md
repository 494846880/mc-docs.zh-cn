---
title: include 文件
description: include 文件
services: virtual-wan
ms.service: virtual-wan
ms.topic: include
origin.date: 10/06/2020
author: rockboyfor
ms.date: 10/26/2020
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 6f2e2f1bdf9c318d8f197e951eaaa7c93c1bedbd
ms.sourcegitcommit: 87b6bb293f39c5cfc2db6f38547220a13816d78f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96478088"
---
<!--Verified Successfully-->
* 你拥有一个要连接到的虚拟网络。 确认本地网络的任何子网都不会与要连接到的虚拟网络重叠。 若要在 Azure 门户中创建虚拟网络，请参阅[快速入门](../articles/virtual-network/quick-create-portal.md)。

* 虚拟网络不包含任何虚拟网络网关。 如果虚拟网络包含网关（VPN 或 ExpressRoute），则必须删除所有网关。 此配置要求将虚拟网络改为连接到虚拟 WAN 中心网关。

* 获取中心区域的 IP 地址范围。 该中心是虚拟 WAN 创建和使用的虚拟网络。 为中心指定的地址范围不能与要连接到的任何现有虚拟网络重叠。 此外，它也不能与本地连接到的地址范围重叠。 如果不熟悉本地网络配置中的 IP 地址范围，则咨询能够提供此类详细信息的人员。

* 如果没有 Azure 订阅，请创建一个[试用版订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

<!-- Update_Description: new article about virtual wan before include -->
<!--NEW.date: 10/26/2020-->