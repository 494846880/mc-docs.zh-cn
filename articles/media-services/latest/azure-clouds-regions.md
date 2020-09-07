---
title: Azure 媒体服务 v3 可用的云和区域
description: 本文讨论 Azure 媒体服务 v3 可用的 Azure 云和区域。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
origin.date: 5/28/2020
ms.date: 09/07/2020
ms.author: v-jay
ms.custom: references_regions
ms.openlocfilehash: 69695381fbf0d477237b15cc2a32c94b2e1d5286
ms.sourcegitcommit: 2eb5a2f53b4b73b88877e962689a47d903482c18
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89413950"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>存在 Azure 媒体服务 v3 的云和区域

Azure 媒体服务 v3 可通过 Azure 资源管理器清单在 Azure 中国世纪互联中使用。 但是，并非所有媒体服务功能都可在 Azure 云中使用。 本文档概述了主要媒体服务 v3 组件的可用性。

## <a name="feature-availability-in-azure-clouds"></a>Azure 云中的功能可用性

| 功能|Azure 中国世纪互联|
| --- | --- |
| Azure EventGrid | 不可用 |
| VideoAnalyzerPreset | 不可用 |
| AudioAnalyzerPreset | 不可用 |
| [StandardEncoderPreset](encoding-concept.md) | 可用 |
| [LiveEvents](live-streaming-overview.md) | 可用 |
| [StreamingEndpoints](streaming-endpoint-concept.md) | 可用 |

## <a name="regionsgeographieslocations"></a>区域/地域/位置

[部署 Azure 媒体服务的区域](https://azure.microsoft.com/global-infrastructure/services/?regions=china-non-regional,china-east,china-east-2,china-north,china-north-2&products=media-services)

### <a name="region-code-name"></a>区域代码名

如果需要提供**位置**参数，则需要提供区域代码名称作为**位置**值。 若要获取你的帐户所在的并且应当将你的调用路由到的区域的代码名称，可以在 [Azure CLI](/cli/?view=azure-cli-latest) 中运行以下命令行

```azurecli
az account list-locations
```

运行上面所示的命令行后，会得到所有 Azure 区域的列表。 导航到具有你要查找的 *displayName* 的 Azure 区域，并将其 *name* 值用于**位置**参数。

例如，对于 Azure 区域“中国东部”（如下所示），会将“chinaeast”用于 **location** 参数。

```json
   {
      "displayName": "China East",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/chinaeast",
      "latitude": "31.3209",
      "longitude": "121.5891",
      "name": "chinaeast",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>终结点  

从 Azure 中国云连接到媒体服务帐户时，需要了解以下终结点。

### <a name="azure-china-21vianet"></a>Azure 中国世纪互联

| 服务 | 端点 |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| 身份验证 | `https://login.chinacloudapi.cn/` |
| 令牌受众 |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>另请参阅

* [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)
* [Azure 地域](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>后续步骤

[媒体服务 v3 概述](media-services-overview.md)
