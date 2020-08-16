---
title: prev() - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 prev()。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/13/2020
ms.date: 08/06/2020
ms.openlocfilehash: 91e1802ca17fc36618f4242d4af7c8758831820a
ms.sourcegitcommit: 7ceeca89c0f0057610d998b64c000a2bb0a57285
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87841668"
---
# <a name="prev"></a>prev()

返回指定行中特定列的值。
指定的行与[序列化行集](./windowsfunctions.md#serialized-row-set)中的当前行存在指定的偏移量。

**语法**

有几种可能性。

* `prev(column)`

* `prev(column, offset)`

* `prev(column, offset, default_value)`

**参数**

* `column`：要从中获取值的列。

* `offset`：行中向后的偏移量。 如果未指定偏移量，则使用默认偏移量 1。

* `default_value`：在没有要从中获取值的前面行时使用的默认值。 如果未指定默认值，则使用 null。

**示例**

```kusto
Table | serialize | extend prevA = prev(A,1)
| extend diff = A - prevA
| where diff > 1

Table | serialize prevA = prev(A,1,10)
| extend diff = A - prevA
| where diff <= 10
```
