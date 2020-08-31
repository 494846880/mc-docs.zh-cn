---
title: invoke 运算符 - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 invoke 运算符。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/13/2020
ms.date: 08/18/2020
ms.openlocfilehash: 16c2ed61ee488be7c2fa585099de5b6471c95a64
ms.sourcegitcommit: f4bd97855236f11020f968cfd5fbb0a4e84f9576
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516048"
---
# <a name="invoke-operator"></a>invoke 运算符

调用接收 `invoke` 的源作为表格参数自变量的 lambda。

```kusto
T | invoke foo(param1, param2)
```

> [!NOTE]
> 若要更详细地了解如何声明可接受表格参数的 lambda 表达式，请参阅 [let 语句](./letstatement.md)。
 
## <a name="syntax"></a>语法

`T | invoke` *function*`(`[*param1*`,` *param2*]`)`

## <a name="arguments"></a>参数

* *T*：表格源。
* function：要计算的 lambda 表达式名称或函数名。
* param1、param2 ...：其他 lambda 参数。

## <a name="returns"></a>返回

返回已计算的表达式的结果。

## <a name="example"></a>示例

下面的示例演示如何使用 `invoke` 运算符来调用 lambda 表达式：

<!-- csl: https://help.kusto.chinacloudapi.cn:443/KustoMonitoringPersistentDatabase -->
```kusto
// clipped_average(): calculates percentiles limits, and then makes another 
//                    pass over the data to calculate average with values inside the percentiles
let clipped_average = (T:(x: long), lowPercentile:double, upPercentile:double)
{
   let high = toscalar(T | summarize percentiles(x, upPercentile));
   let low = toscalar(T | summarize percentiles(x, lowPercentile));
   T 
   | where x > low and x < high
   | summarize avg(x) 
};
range x from 1 to 100 step 1
| invoke clipped_average(5, 99)
```

|avg_x|
|---|
|52|
