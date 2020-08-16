---
title: format_bytes() - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 format_bytes()。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/13/2020
ms.date: 08/06/2020
ms.openlocfilehash: 3c16137fdf8f67ff52d6d231512fdf45d644bbb4
ms.sourcegitcommit: 7ceeca89c0f0057610d998b64c000a2bb0a57285
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87841448"
---
# <a name="format_bytes"></a>format_bytes()

将数字的格式设置为表示数据大小（以字节为单位）的字符串。

```kusto
format_bytes(1024) == '1 KB'"
```

**语法**

`format_bytes(`*value* [`,` *precision* [`,` *units*]]`)`

**参数**

* `value`：要格式化为数据大小（以字节为单位）的数字。
* `precision`：（可选）值将舍入到的位数。 （默认值为 0）。
* `units`：（可选）字符串格式设置将使用的目标数据大小的单位（`Bytes`、`KB`、`MB`、`GB`、`TB`、`PB`）。 如果参数为空，将根据输入值自动选择单位。

**返回**

包含格式结果的字符串。

**示例**

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
print 
v1 = format_bytes(564),
v2 = format_bytes(10332, 1),
v3 = format_bytes(20010332),
v4 = format_bytes(20010332, 2),
v5 = format_bytes(20010332, 0, "KB")
```

|v1|v2|v3|v4|v5|
|---|---|---|---|---|
|564 字节|10.1 KB|19 MB|19.08 MB|19541 KB|
