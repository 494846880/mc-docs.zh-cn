---
title: Azure 媒体服务旧组件 | Microsoft Docs
description: 本主题介绍 Azure 媒体服务旧组件。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 02/27/2020
ms.date: 09/07/2020
ms.author: v-jay
ms.openlocfilehash: 8f99e0ad031da09cbfccf6acaf21a84c76d214dd
ms.sourcegitcommit: 2eb5a2f53b4b73b88877e962689a47d903482c18
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89413680"
---
# <a name="azure-media-services-legacy-components"></a>Azure 媒体服务旧组件

随着时间的推移，对媒体服务组件进行了稳定的改进和增强。 因此，某些旧组件已经停用。 可以在以下文章中找到有关如何将应用程序从旧组件迁移到当前组件的说明。
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>旧组件的停用计划和迁移指南

我们宣布弃用 Windows Azure 媒体编码器 (WAME) 和 Azure 媒体编码器 (AME) 媒体处理器。 这些处理器将于 2020 年 3 月 31 日停用。

* [从 Windows Azure 媒体编码器迁移到 Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [从 Azure 媒体编码器迁移到 Media Encoder Standard](migrate-azure-media-encoder.md)

我们还宣布停用以下媒体分析媒体处理器： 
 
|媒体处理器名称|停用日期|附加说明|
|---|---|
|[Azure Media Indexer](media-services-index-content.md)|2023 年 3 月 1 日|此媒体处理器将被 Azure 媒体服务视频索引器替换。|
|[动作检测](media-services-motion-detection.md)|2020 年 6 月 1 日|目前无替换计划。|
|[视频摘要](media-services-video-summarization.md)|2020 年 6 月 1 日|目前无替换计划。|
|[视频光学字符识别](media-services-video-optical-character-recognition.md)|2020 年 6 月 1 日|此媒体处理器将被 Azure 媒体服务视频索引器替换。 另外，请考虑使用 Azure 媒体服务 v3 API。|
|[面部检测器](media-services-face-and-emotion-detection.md)|2020 年 6 月 1 日|此媒体处理器将被 Azure 媒体服务视频索引器替换。 另外，请考虑使用 Azure 媒体服务 v3 API。|
|[内容审查器](media-services-content-moderation.md)|2020 年 6 月 1 日|此媒体处理器将被 Azure 媒体服务视频索引器替换。 另外，请考虑使用 Azure 媒体服务 v3 API。|

## <a name="next-steps"></a>后续步骤

[有关从媒体服务 v2 迁移到 v3 的指导](../latest/migrate-from-v2-to-v3.md)
