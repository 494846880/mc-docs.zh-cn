---
title: Azure HDInsight 的网络安全组 (NSG) 服务标记
description: 使用 HDInsight 服务标记可以允许从 HDInsight 运行状况和管理服务节点发往群集的入站流量，无需将 IP 地址显式添加到网络安全组。
author: hrasheed-msft
ms.author: v-yiso
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
origin.date: 03/10/2020
ms.date: 04/06/2020
ms.openlocfilehash: 5f042a6ce7120bdc4d62821b42f5a3c2b23af96d
ms.sourcegitcommit: 5f07189f06a559d5617771e586d129c10276539e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94552233"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>Azure HDInsight 的 NSG 服务标记

网络安全组 (NSG) 的 Azure HDInsight 服务标记是运行状况和管理服务的 IP 地址组。 这些组有助于尽量降低创建安全规则时的复杂性。 [服务标记](../virtual-network/network-security-groups-overview.md#service-tags)允许来自特定 IP 的入站流量，而无需输入 NSG 中的每个[管理 IP 地址](hdinsight-management-ip-addresses.md)。

HDInsight 服务会管理这些服务标记。 你无法创建自己的服务标记，也无法修改现有标记。 Microsoft 会管理与服务标记匹配的地址前缀，并会在地址发生更改时自动更新服务标记。

如果要使用特定区域，并且此页面上尚未记录服务标记，则可以使用[服务标记发现 API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) 查找服务标记。 还可以下载[服务标记 JSON 文件](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)并搜索所需区域。

## <a name="get-started-with-service-tags"></a>开始使用服务标记

在网络安全组中使用服务标记有两个选项：

- 使用单个全局 HDInsight 服务标记：此选项会向 HDInsight 服务用来监视所有区域中的群集的所有 IP 地址开放虚拟网络。 此选项是最简单的方法，但如果你有严格的安全要求，它可能不适合。

- 使用多个区域性服务标记：此选项仅向 HDInsight 在该特定区域中使用的 IP 地址开放虚拟网络。 但是，如果你使用多个区域，则需要向虚拟网络中添加多个服务标记。

## <a name="use-a-single-global-hdinsight-service-tag"></a>使用单个全局 HDInsight 服务标记

开始在 HDInsight 群集中使用服务标记的最简单方法是将全局标记 `HDInsight` 添加到 NSG 规则。

1. 在 [Azure 门户](https://portal.azure.cn/)中，选择你的网络安全组。

1. 在“设置”下，依次选择“入站安全规则”、“+ 添加”。  

1. 在“源”下拉列表中，选择“服务标记”。 

1. 在“源服务标记”下拉列表中，选择“HDInsight”。 

    ![从 Azure 门户添加服务标记](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

此标记包含 HDInsight 可用的所有区域的运行状况和管理服务的 IP 地址。 此标记可确保无论群集创建于何处，群集都可以与必要的运行状况和管理服务通信。

## <a name="use-regional-hdinsight-service-tags"></a>使用区域性 HDInsight 服务标记

如果全局标记选项由于你需要更严格的权限而不可行，则只能允许适用于相应区域的服务标记。 可能有多个服务标记，具体取决于在其中创建群集的区域。

若要了解要为区域添加哪些服务标记，请阅读本文的以下部分。

### <a name="use-a-single-regional-service-tag"></a>使用单个区域性服务标记

如果群集位于此表中列出的某个区域中，则只需向 NSG 添加一个区域性服务标记。

| 国家/地区 | 区域 | 服务标记 |
| ---- | ---- | ---- |
| 中国 | 中国东部 2 | HDInsight.ChinaEast2 |
| &nbsp; | 中国北部 2 | HDInsight.ChinaNorth2 |

### <a name="use-multiple-regional-service-tags"></a>使用多个区域性服务标记

如果上表中未列出你创建群集时所在的区域，则需要允许多个区域性服务标记。 需要使用多个是因为不同区域的资源提供程序的安排不同。

其余区域根据它们使用的区域性服务标记划分为各个组。

#### <a name="group-1"></a>组 1

中国北部和中国东部区域中的群集需要允许两个服务标记：`HDInsight.ChinaNorth` 和 `HDInsight.ChinaEast`。
## <a name="next-steps"></a>后续步骤

- [网络安全组：服务标记](../virtual-network/network-security-groups-overview.md#security-rules)
- [为 Azure HDInsight 群集创建虚拟网络](hdinsight-create-virtual-network.md)
