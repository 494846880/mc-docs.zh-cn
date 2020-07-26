---
title: 定价和计费模型
description: 概述 Azure 逻辑应用的定价和计费模型的工作原理
services: logic-apps
ms.suite: integration
author: rockboyfor
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
origin.date: 06/25/2020
ms.date: 07/20/2020
ms.testscope: no
ms.testdate: 03/30/2020
ms.author: v-yeche
ms.openlocfilehash: 94367a40e778b115347752d0d39666e66a604475
ms.sourcegitcommit: 31da682a32dbb41c2da3afb80d39c69b9f9c1bc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2020
ms.locfileid: "86414687"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Azure 逻辑应用的定价模型

借助 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)可以创建和运行可在云中缩放的自动化集成工作流。 本文介绍 Azure 逻辑应用的计费和定价方式。 有关定价费率，请参阅[逻辑应用定价](https://www.azure.cn/pricing/details/logic-apps/)。

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>消耗量定价模型

对于在公共的“全局”多租户 Azure 逻辑应用服务中运行的新逻辑应用，只需根据实际使用的资源付费。 这些逻辑应用使用基于消耗量的计划和定价模型。 在逻辑应用中，每一步都是操作，而 Azure 逻辑应用会对逻辑应用中运行的所有操作进行计量。

例如，操作包括：

* [触发器](#triggers)（特殊的操作）。 所有逻辑应用需将一个触发器用作第一个步骤。

* [“内置”或本机操作](../connectors/apis-list.md#built-in)，例如 HTTP、对 Azure Functions 和 API 管理的调用，等等

* 调用[托管连接器](../connectors/apis-list.md#managed-connectors)，例如 Outlook 365、Dropbox 等

* [控制工作流操作](../connectors/apis-list.md#control-workflow)，例如循环、条件语句等

[标准连接器](../connectors/apis-list.md#managed-connectors)按[标准连接器价格](https://www.azure.cn/pricing/details/logic-apps/)收费。 正式发布的[企业连接器](../connectors/apis-list.md#managed-connectors)按[企业连接器价格](https://www.azure.cn/pricing/details/logic-apps/)收费，而公共预览版企业连接器则按[标准连接器价格](https://www.azure.cn/pricing/details/logic-apps/)收费。

详细了解如何在[触发器](#triggers)和[操作](#actions)级别计费。 或者参阅 [Azure 逻辑应用的限制和配置](logic-apps-limits-and-config.md)，了解有关限制的信息。

<!--Not Available on ## Fixed pricing model-->

<!--Not Available on [*integration service environment* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)-->
<a name="connectors"></a>

## <a name="connectors"></a>连接器

Azure 逻辑应用连接器通过提供[触发器](#triggers)和/或[操作](#actions)，帮助逻辑应用访问云中或本地的应用、服务和系统。 连接器分类为“标准”或“企业”连接器。 有关这些连接器的概述，请参阅[适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)。 如果没有预生成的连接器可用于要在逻辑应用中使用的 REST API，则可以创建[自定义连接器](https://docs.microsoft.com/connectors/custom-connectors)，这些连接器只是这些 REST API 的包装器。 自定义连接器按标准连接器计费。 以下部分提供有关触发器和操作的计费方式的详细信息。

<a name="triggers"></a>

## <a name="triggers"></a>触发器

触发器是发生特定事件时创建逻辑应用实例的特殊操作。 触发器以不同方式起作用，从而影响逻辑应用的计量方式。 下面是 Azure 逻辑应用中存在的各种触发器：

* **轮询触发器**：此触发器持续检查终结点是否收到满足创建逻辑应用实例和启动工作流的条件的消息。 即使没有创建逻辑应用实例，逻辑应用也会将每个轮询请求计量为执行。 若要指定轮询间隔，请通过逻辑应用程序设计器设置触发器。

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook 触发器**：此触发器等待客户端向特定的终结点发送请求。 发送到 webhook 终结点的每个请求都会计为操作执行。 例如，请求和 HTTP Webhook 触发器都是 Webhook 触发器。

* 重复周期触发器：此触发器基于在触发器中设置的重复间隔创建逻辑应用实例。 例如，可以设置每隔三天运行的，或者根据更复杂的计划运行的定期触发器。

<a name="actions"></a>

## <a name="actions"></a>操作

Azure 逻辑应用将 HTTP 等“内置”操作作为本机操作进行计量。 例如，内置操作包括 HTTP 调用、来自 Azure Functions 或 API 管理的调用，以及条件、循环和开关语句等控制流步骤。 每个操作具有自身的操作类型。 例如，调用[连接器](https://docs.microsoft.com/connectors)的操作为“ApiConnection”类型。 这些连接器分类为“标准”或“企业”连接器，根据各自的[定价](https://www.azure.cn/pricing/details/logic-apps/)进行计量。 预览版的企业连接器按标准连接器计费。

Azure 逻辑应用将所有成功和不成功的操作作为执行进行计量。 但是，逻辑应用不会计量以下操作：

* 由于未满足条件而跳过的操作
* 由于逻辑应用在完成之前停止而未运行的操作

对于在循环内部运行的操作，Azure 逻辑应用会对循环中每个周期的每个操作进行计数。 例如，假设有一个处理列表的“每个”循环。 逻辑应用通过将列表项的数量乘以循环中的操作数来计量该循环中的操作，并加上启动循环的操作。 因此，包含 10 个项的列表的计算公式为 (10 * 1) + 1，即 11 个操作执行。

## <a name="disabled-logic-apps"></a>禁用的逻辑应用

禁用的逻辑应用在禁用期间不会产生费用，因为它们无法创建新实例。 禁用逻辑应用后，当前正在运行的实例可能需要在一段时间之后才会完全停止。

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>集成帐户

[固定定价模型](https://www.azure.cn/pricing/details/logic-apps/)适用于[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)，此类帐户可用于免费浏览、开发和测试 Azure 逻辑应用中的 [B2B 和 EDI](logic-apps-enterprise-integration-b2b.md) 和 [XML 处理](logic-apps-enterprise-integration-xml.md)功能。 每个 Azure 订阅最多可以有一项[集成帐户的特定限制](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)。 每个集成帐户的存储受到特定的[项目限制](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)，其中包含贸易合作伙伴、协议、映射、架构、程序集、证书、批处理配置等。

Azure 逻辑应用提供“基本”和“标准”集成帐户。 逻辑应用服务级别协议 (SLA) 支持“基本”和“标准”层级，而“免费”层级则不受 SLA 支持并有区域可用性、吞吐量和使用方面的限制。 每个 Azure 区域中可以有多个集成帐户，“免费”层级集成帐户除外。 有关定价费率，请参阅[逻辑应用定价](https://www.azure.cn/pricing/details/logic-apps/)。

<!--Not Available on Free intergration accounts-->
<!--Not Available on [*integration service environment* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md-->

若要在“基本”或“标准”集成帐户之间进行选择，请查看以下用例说明：

<!--Not Available on * **Free**: For when you want to try exploratory scenarios, not production scenarios-->

* **基本**：适用于只需处理消息或充当与大型企业实体建立贸易合作关系的小型企业合作伙伴的情况

* **标准**：适用于 B2B 关系更复杂且需要管理的实体数增加的情况

<a name="data-retention"></a>

## <a name="data-retention"></a>数据保留

存储在逻辑应用的运行历史记录中的所有输入和输出都将根据逻辑应用的[运行保留期](logic-apps-limits-and-config.md#run-duration-retention-limits)进行计费，在集成服务环境 (ISE) 中运行的逻辑应用除外。 在 ISE 中运行的逻辑应用不会产生数据保留成本。 有关定价费率，请参阅[逻辑应用定价](https://www.azure.cn/pricing/details/logic-apps/)。

若要更好地监视逻辑应用的存储消耗，可以执行以下操作：

* 查看逻辑应用每月使用的存储单元数（以 GB 为单位）。
* 在逻辑应用的运行历史记录中查看特定操作的输入和输出的大小。

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>查看逻辑应用存储消耗

1. 在 Azure 门户中，查找并打开逻辑应用。

1. 在逻辑应用的菜单中的“监视”下，选择“指标”。 

1. 从右侧窗格的“图表标题”下的“指标”列表中，选择“按消耗存储的执行操作的使用情况计费”。  

    此指标表示每月计费的存储消耗单元数 (GB)。

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>查看操作输入和输出大小

1. 在 Azure 门户中，查找并打开逻辑应用。

1. 在逻辑应用的菜单中，选择“概述”。

1. 在右侧窗格的“运行历史记录”下，选择具有要检查的输入和输出的运行。

1. 在“逻辑应用运行”下，选择“运行详细信息”。

1. 在“逻辑应用运行详细信息”窗格的“操作表”中（其中列出了每个操作的状态和持续时间），选择要查看的操作。

1. 在“逻辑应用操作”窗格中，找到该操作的输入和输出的大小（分别显示在“输入链接”和“输出链接”下）。

## <a name="next-steps"></a>后续步骤

* [详细了解 Azure 逻辑应用](logic-apps-overview.md)
* [创建第一个逻辑应用](quickstart-create-first-logic-app-workflow.md)

<!-- Update_Description: update meta properties, wording update, update link -->