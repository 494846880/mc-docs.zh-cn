---
title: 在 Azure 门户中创建认知服务资源
titleSuffix: Azure Cognitive Services
description: 通过在 Azure 门户中创建和订阅资源，开始使用 Azure 认知服务。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
origin.date: 10/23/2019
ms.date: 01/27/2020
ms.author: v-tawe
ms.openlocfilehash: a3636eaf5f8433eca286fe3208b686ca5c4942f1
ms.sourcegitcommit: 3de7d92ac955272fd140ec47b3a0a7b1e287ca14
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/12/2020
ms.locfileid: "84723584"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>使用 Azure 门户创建认知服务资源

使用此快速入门开始使用 Azure 认知服务。 在 Azure 门户中创建认知服务资源后，你将获得用于验证应用程序的终结点和密钥。


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>先决条件

* 有效的 Azure 订阅 - [创建 1 元人民币试用帐户](https://www.azure.cn/pricing/1rmb-trial/)。

## <a name="create-a-new-azure-cognitive-services-resource"></a>新建 Azure 认知服务资源

1. 创建资源。

    #### <a name="multi-service-resource"></a>[多服务资源](#tab/multiservice)
    
    多服务资源在门户中名为“认知服务”。 [创建认知服务资源](https://portal.azure.cn/#create/Microsoft.CognitiveServices)。
    
    此时，多服务资源允许访问以下认知服务：
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | 计算机视觉  | 内容审查器                                    | 人脸               | 语言理解 (LUIS) | 文本分析   |
    | 文本翻译  |
    
    #### <a name="single-service-resource"></a>[单服务资源](#tab/singleservice)

    使用以下链接为可用的认知服务创建资源：

    | 影像                      | 语音                  | 语言                          | 决策             |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|
    | [计算机视觉](https://portal.azure.cn/#create/Microsoft.CognitiveServicesComputerVision) | [语音服务](https://portal.azure.cn/#create/Microsoft.CognitiveServicesSpeechServices) | [语言理解 (LUIS)](https://portal.azure.cn/#create/Microsoft.CognitiveServicesLUIS) | [内容审查器](https://portal.azure.cn/#create/Microsoft.CognitiveServicesContentModerator) |
    | [人脸](https://portal.azure.cn/#create/Microsoft.CognitiveServicesFace) | | [文本分析](https://portal.azure.cn/#create/Microsoft.CognitiveServicesTextAnalytics) | |
    | | | [文本翻译](https://portal.azure.cn/#create/Microsoft.CognitiveServicesTextTranslation) | |
    ***

3. 在“创建”页中提供以下信息： 

    #### <a name="multi-service-resource"></a>[多服务资源](#tab/multiservice)

    |    |    |
    |--|--|
    | **名称** | 认知服务资源的描述性名称。 例如，*MyCognitiveServicesResource*。 |
    | **订阅** | 选择一个可用的 Azure 订阅。 |
    | **位置** | 认知服务实例的位置。 不同位置可能会导致延迟，但不会影响资源的运行时可用性。 |
    | **定价层** | 认知服务帐户的费用取决于你所选的选项和你的使用情况。 有关详细信息，请参阅 API [定价详细信息](https://www.azure.cn/pricing/details/cognitive-services/)。
    | **资源组** | 将包含认知服务资源的 Azure 资源组。 可以创建新组或将其添加到预先存在的组。 |

    ![“创建资源”屏幕](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    单击**创建**。

    #### <a name="single-service-resource"></a>[单服务资源](#tab/singleservice)

    |    |    |
    |--|--|
    | **名称** | 认知服务资源的描述性名称。 例如，*TextAnalyticsResource*。 |
    | **订阅** | 选择一个可用的 Azure 订阅。 |
    | **位置** | 认知服务实例的位置。 不同位置可能会导致延迟，但不会影响资源的运行时可用性。 |
    | **定价层** | 认知服务帐户的费用取决于你所选的选项和你的使用情况。 有关详细信息，请参阅 API [定价详细信息](https://www.azure.cn/pricing/details/cognitive-services/)。
    | **资源组** | 将包含认知服务资源的 Azure 资源组。 可以创建新组或将其添加到预先存在的组。 |

    ![“创建资源”屏幕](media/cognitive-services-apis-create-account/resource_create_screen.png)

    单击**创建**。

    ***


## <a name="get-the-keys-for-your-resource"></a>获取资源的密钥

1. 成功部署资源后，单击“后续步骤”下的“转到资源”。

    ![搜索“认知服务”](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. 从打开的快速入门窗格中，可以访问密钥和终结点。

    ![获取密钥和终结点](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组也会删除该组中包含的任何其他资源。

1. 在 Azure 门户中展开左侧的菜单，打开服务菜单，然后选择“资源组”以显示资源组的列表。
2. 找到包含要删除的资源的资源组
3. 右键单击资源组列表。 选择“删除资源组”并进行确认。

## <a name="see-also"></a>另请参阅

* [对 Azure 认知服务的请求进行身份验证](authentication.md)

* [什么是 Azure 认知服务？](Welcome.md)
* [自然语言支持](language-support.md)

<!-- Update_Description: content update -->