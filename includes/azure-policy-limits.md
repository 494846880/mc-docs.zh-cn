---
title: include 文件
description: include 文件
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
origin.date: 04/23/2020
ms.date: 05/29/2020
ms.author: v-tawe
ms.openlocfilehash: dab841d42366f33b78a9e4fc261611c5d95eccca
ms.sourcegitcommit: be0a8e909fbce6b1b09699a721268f2fc7eb89de
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2020
ms.locfileid: "84199259"
---
Azure Policy 的每个对象类型都有一个最大计数。 _作用域_条目是指订阅或[管理组](../articles/governance/management-groups/overview.md)。

| Where | 对象 | 最大计数 |
|---|---|---|
| 作用域 | 策略定义 | 500 |
| 作用域 | 计划定义 | 100 |
| 租户 | 计划定义 | 1,000 |
| 作用域 | 策略或计划分配 | 100 |
| 策略定义 | parameters | 20 个 |
| 计划定义 | 策略 | 100 |
| 计划定义 | parameters | 100 |
| 策略或计划分配 | 排除项（不在范围内的项） | 400 |
| 策略规则 | 嵌套式条件语句 | 512 |
| 修正任务 | 资源 | 500 |
