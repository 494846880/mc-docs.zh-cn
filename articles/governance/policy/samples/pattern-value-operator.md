---
title: 模式：策略定义中的 value 运算符
description: 此 Azure Policy 模式通过示例介绍了如何在策略定义中使用 value 运算符。
origin.date: 06/29/2020
ms.date: 08/06/2020
ms.author: v-tawe
ms.topic: sample
ms.openlocfilehash: 4a688f89ba92518b30337fc722631584348db726
ms.sourcegitcommit: ac70b12de243a9949bf86b81b2576e595e55b2a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87917218"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Azure Policy 模式：value 运算符

[value](../concepts/definition-structure.md#value) 运算符会对[参数](../concepts/definition-structure.md#parameters)、[支持的模板函数](../concepts/definition-structure.md#policy-functions)或文本进行评估，针对为给定[条件](../concepts/definition-structure.md#conditions)提供的值。

> [!WARNING]
> 如果模板函数  的结果是一个错误，则策略评估会失败。 评估失败是一种隐式**拒绝**。 有关详细信息，请参阅[避免模板失败](../concepts/definition-structure.md#avoiding-template-failures)。

## <a name="sample-policy-definition"></a>示例策略定义

此策略定义添加或替换在资源上的参数 **tagName**（字符串  ）中指定的标记，并从资源所在的资源组继承 **tagName** 的值。 创建或更新资源时，会进行此评估。 充当 [modify](../concepts/effects.md#modify) 效果的修正可以通过[修正任务](../how-to/remediate-resources.md)在现有资源上运行。

```json
{
   "properties": {
       "displayName": "Inherit a tag from the resource group",
       "policyType": "BuiltIn",
       "mode": "Indexed",
       "description": "Adds or replaces the specified tag and value from the parent resource group when any resource is created or updated. Existing resources can be remediated by triggering a remediation task.",
       "metadata": {
           "category": "Tags"
       },
       "parameters": {
           "tagName": {
               "type": "String",
               "metadata": {
                   "displayName": "Tag Name",
                   "description": "Name of the tag, such as 'environment'"
               }
           }
       },
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "[concat('tags[', parameters('tagName'), ']')]",
                       "notEquals": "[resourceGroup().tags[parameters('tagName')]]"
                   },
                   {
                       "value": "[resourceGroup().tags[parameters('tagName')]]",
                       "notEquals": ""
                   }
               ]
           },
           "then": {
               "effect": "modify",
               "details": {
                   "roleDefinitionIds": [
                       "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
                   ],
                   "operations": [{
                       "operation": "addOrReplace",
                       "field": "[concat('tags[', parameters('tagName'), ']')]",
                       "value": "[resourceGroup().tags[parameters('tagName')]]"
                   }]
               }
           }
       }
   }
}
```

### <a name="explanation"></a>说明

```json
"if": {
   "allOf": [{
           "field": "[concat('tags[', parameters('tagName'), ']')]",
           "notEquals": "[resourceGroup().tags[parameters('tagName')]]"
       },
       {
           "value": "[resourceGroup().tags[parameters('tagName')]]",
           "notEquals": ""
       }
   ]
},
```

**value** 运算符用在**属性**的 **policyRule.if** 块中。 在此示例中，[逻辑运算符](../concepts/definition-structure.md#logical-operators) **allOf** 用于说明这两个条件语句都必须为 true 才能产生 **modify** 效果。

**value** 会对模板函数 [resourceGroup()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) 的结果进行评估，其条件是结果 **notEquals** 空值。 如果在父资源组的 **tagName** 中提供的标记名称存在，则条件的评估结果为 true。

## <a name="next-steps"></a>后续步骤

- 查看其他[模式和内置定义](./index.md)。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。