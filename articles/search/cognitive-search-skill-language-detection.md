---
title: 语言检测认知技能
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索中的 AI 扩充管道中，计算非结构化的文本，并针对每个文本，返回语言标识符和表示分析强度的得分。
manager: nitinme
author: luiscabrer
ms.author: v-tawe
ms.service: cognitive-search
ms.topic: conceptual
origin.date: 06/17/2020
ms.date: 09/10/2020
ms.openlocfilehash: 535b82164bea6b39d636de79716b727c6995d5e8
ms.sourcegitcommit: 78c71698daffee3a6b316e794f5bdcf6d160f326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90021115"
---
#   <a name="language-detection-cognitive-skill"></a>语言检测认知技能

语言检测  技能检测输入文本的语言，并报告在请求中提交的每个文档的单一语言代码。 语言代码配有表示分析长度的得分。 此技能使用认知服务中的[文本分析](../cognitive-services/text-analytics/overview.md)提供的机器学习模型。

当需要提供文本的语言作为其他技能（例如，[情绪分析技能](cognitive-search-skill-sentiment.md)或[文本拆分技能](cognitive-search-skill-textsplit.md)）的输入时，此功能尤其有用。

语言检测利用必应的自然语言处理库，此类库超出为文本分析列出的[受支持的语言和区域](../cognitive-services/text-analytics/language-support.md)的数目。 语言的具体列表未发布，但包含所有广泛传播的语言，以及变体、方言和某些区域性的和文化性的语言。 如果你的内容是采用不常用的语言表达的，可以[尝试语言检测 API](https://chinaeast2.dev.cognitive.azure.cn/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)，看是否会返回一个代码。 无法检测到的语言的响应为 `unknown`。

> [!NOTE]
> 通过增大处理频率、添加更多文档或添加更多 AI 算法来扩大范围时，需要[附加可计费的认知服务资源](cognitive-search-attach-cognitive-services.md)。 调用认知服务中的 API 以及在 Azure 认知搜索中的文档破解阶段提取图像时，会产生费用。 提取文档中的文本不会产生费用。
>
> 当你行使内置技能时，我们会按现有的[认知服务预付费价格](https://www.azure.cn/pricing/details/cognitive-services/)收费。 图像提取定价如 [Azure 认知搜索定价页](https://www.azure.cn/pricing/details/search)所述。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>数据限制
记录的最大大小应为 50,000 个字符，通过 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length) 进行测量。 如果在将数据发送到语言检测技能之前需要拆分数据，可以使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-inputs"></a>技能输入

参数区分大小写。

| 输入     | 说明 |
|--------------------|-------------|
| `text` | 要分析的文本。|

## <a name="skill-outputs"></a>技能输出

| 输出名称    | 说明 |
|--------------------|-------------|
| `languageCode` | 标识语言的 ISO 6391 语言代码。 例如，“en”。 |
| `languageName` | 语言的名称。 例如，“英语”。 |
| `score` | 一个介于 0 和 1 之间的值。 正确标识语言的可能性。 如果句子中有混合语言，得分可能会低于 1。  |

##  <a name="sample-definition"></a>示例定义

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
      }

    ]
  }
```

##  <a name="sample-input"></a>示例输入

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      }
    ]
```


##  <a name="sample-output"></a>示例输出

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      }
    ]
}
```


## <a name="error-cases"></a>错误案例
如果以不支持的语言表达文本，会生成错误，且不返回任何语言标识符。

## <a name="see-also"></a>另请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
