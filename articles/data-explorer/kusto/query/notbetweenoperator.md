---
title: notbetween 运算符 - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 notbetween 运算符。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 10/23/2018
ms.date: 08/06/2020
ms.openlocfilehash: 4c12f8be969bd8259a70f87745936d11bd82a992
ms.sourcegitcommit: 7ceeca89c0f0057610d998b64c000a2bb0a57285
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87841525"
---
# <a name="not-between-operator-between"></a>not-between 运算符 (!between)

匹配包含范围外的输入。

```kusto
Table1 | where Num1 !between (1 .. 10)
Table1 | where Time !between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`!between` 可以对任何数值、日期时间或时间跨度表达式执行运算。
 
**语法**

*T* `|` `where` *expr* `!between` `(`*leftRange*` .. `*rightRange*`)`   
 
如果 expr 表达式为时间跨度，则提供另一个糖衣语法：

*T* `|` `where` *expr* `!between` `(`*leftRangeDateTime*` .. `*rightRangeTimespan*`)`   

**参数**

* *T* - 待匹配记录的表格输入。
* *expr* - 要筛选的表达式。
* *leftRange* - 左侧范围（含）的表达式。
* *rightRange* - 右侧范围（含）的表达式。

**返回**

T 中的行，其（expr  <  leftRange 或 expr  >  rightRange）的谓词的计算结果为 `true`    。

**示例**  

**使用“!between”运算符筛选数值**  

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
range x from 1 to 10 step 1
| where x !between (5 .. 9)
```

|x|
|---|
|1|
|2|
|3|
|4|
|10|

**使用“between”运算符筛选日期时间**  

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|计数|
|---|
|58590|

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. 3d)
| count 
```

|计数|
|---|
|58590|
