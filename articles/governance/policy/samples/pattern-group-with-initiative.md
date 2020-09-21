---
title: 模式：使用计划将策略定义分组
description: 此 Azure Policy 模式通过示例介绍了如何将策略定义分组到计划中。
origin.date: 06/29/2020
ms.date: 09/15/2020
ms.author: v-tawe
ms.topic: sample
ms.openlocfilehash: 8deed13912a6647ad21f6504b10fab704e4a316f
ms.sourcegitcommit: f5d53d42d58c76bb41da4ea1ff71e204e92ab1a7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90523999"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Azure Policy 模式：将策略定义分组

一个计划是一组策略定义。 通过将相关的策略定义分组到单个对象中，可以创建本应是多个分配的单个分配。

## <a name="sample-initiative-definition"></a>示例计划定义

此计划部署两个策略定义，每个都使用 **tagName** 和 **tagValue** 参数。 计划本身有两个参数：**costCenterValue** 和 **productNameValue**。
这些计划参数分别为每个分组的策略定义提供。 此设计可最大程度地重复使用现有策略定义，同时限制在需要的情况下为实施它们而创建的分配数。

```json
{
   "properties": {
       "displayName": "Billing Tags Policy Initiative",
       "description": "Specify cost Center tag and product name tag",
       "parameters": {
           "costCenterValue": {
               "type": "String",
               "metadata": {
                   "displayName": "required value for Cost Center tag"
               }
           },
           "productNameValue": {
               "type": "String",
               "metadata": {
                   "displayName": "required value for product Name tag"
               }
           }
       },
       "policyDefinitions": [{
               "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
               "parameters": {
                   "tagName": {
                       "value": "costCenter"
                   },
                   "tagValue": {
                       "value": "[parameters('costCenterValue')]"
                   }
               }
           },
           {
               "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
               "parameters": {
                   "tagName": {
                       "value": "costCenter"
                   },
                   "tagValue": {
                       "value": "[parameters('costCenterValue')]"
                   }
               }
           },
           {
               "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
               "parameters": {
                   "tagName": {
                       "value": "productName"
                   },
                   "tagValue": {
                       "value": "[parameters('productNameValue')]"
                   }
               }
           },
           {
               "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
               "parameters": {
                   "tagName": {
                       "value": "productName"
                   },
                   "tagValue": {
                       "value": "[parameters('productNameValue')]"
                   }
               }
           }
       ]
   }
}
```

### <a name="explanation"></a>说明

#### <a name="initiative-parameters"></a>计划参数

计划可以定义自己的参数，然后将其传递给分组的策略定义。
在此示例中，**costCenterValue** 和 **productNameValue** 都定义为计划参数。 分配计划后，会提供这些值。

```json
"parameters": {
   "costCenterValue": {
       "type": "String",
       "metadata": {
           "displayName": "required value for Cost Center tag"
       }
   },
   "productNameValue": {
       "type": "String",
       "metadata": {
           "displayName": "required value for product Name tag"
       }
   }
},
```

#### <a name="includes-policy-definitions"></a>包括策略定义

如果策略定义接受参数，则每个包含的策略定义都必须提供 **policyDefinitionId** 和一个 **parameters** 数组。 在下面的代码片段中，包含的策略定义使用两个参数：**tagName** 和 **tagValue**。 **tagName** 使用文本进行定义，但 **tagValue** 使用计划定义的参数 **costCenterValue**。 对值进行这样的传递可提高重用性。

```json
{
   "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
   "parameters": {
       "tagName": {
           "value": "costCenter"
       },
       "tagValue": {
           "value": "[parameters('costCenterValue')]"
       }
   }
},
```

## <a name="next-steps"></a>后续步骤

- 查看其他[模式和内置定义](./index.md)。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。