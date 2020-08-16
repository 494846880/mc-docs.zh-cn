---
title: tohex() - Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍 Azure 数据资源管理器中的 tohex()。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/13/2020
ms.date: 08/06/2020
ms.openlocfilehash: c3fe5081b66fe862077dea54f8e8ec6a0c5c5c1c
ms.sourcegitcommit: 7ceeca89c0f0057610d998b64c000a2bb0a57285
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87841695"
---
# <a name="tohex"></a>tohex()

将输入转换为十六进制字符串。

```kusto
tohex(256) == '100'
tohex(-256) == 'ffffffffffffff00' // 64-bit 2's complement of -256
tohex(toint(-256), 8) == 'ffffff00' // 32-bit 2's complement of -256
tohex(256, 8) == '00000100'
tohex(256, 2) == '100' // Exceeds min length of 2, so min length is ignored.
```

**语法**

`tohex(`*Expr*`, [`,` *MinLength*]`)`

**参数**

* Expr：int 或 long 值，将转换为十六进制字符串。  不支持其他类型。
* MinLength：一个数值，表示要包含在输出中的前导字符数。  支持 1 到 16 之间的值，大于 16 的值会被截断成 16。  如果字符串比没有前导字符的 minLength 长，则实际上会忽略 minLength。  负数形式的最小值只能用其基础数据大小来表示。因此，对于 int（32 位），minLength 最小值为 8；对于 long（64 位），minLength 最小值为 16。

**返回**

如果转换成功，则结果将是一个字符串值。
如果转换不成功，则结果将为 Null。