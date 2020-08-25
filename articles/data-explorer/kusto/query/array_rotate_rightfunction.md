---
title: array_rotate_right() - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 array_rotate_right()。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 08/11/2019
ms.date: 08/18/2020
ms.openlocfilehash: 35855e3eca18a2de61b1573b7d7bc19e3cbfb1ab
ms.sourcegitcommit: f4bd97855236f11020f968cfd5fbb0a4e84f9576
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516115"
---
# <a name="array_rotate_right"></a>array_rotate_right()

`array_rotate_right()` 将数组中的值向右旋转。

## <a name="syntax"></a>语法

`array_rotate_right(`arr, rotate_count`)` 

## <a name="arguments"></a>参数

* arr：要拆分的输入数组，必须是动态数组。
* rotate_count：整数，用于指定数组元素将向右旋转的位置数。 如果该值为负数，则元素将向左旋转。

## <a name="returns"></a>返回

所包含元素数与原始数组中的元素数相同的动态数组，其中每个元素根据 rotate_count 进行旋转。

**另请参阅**

* 要向左旋转数组，请参阅 [array_rotate_left()](array_rotate_leftfunction.md)。
* 要向左移动数组，请参阅 [array_shift_left()](array_shift_leftfunction.md)。
* 要向右移动数组，请参阅 [array_shift_right()](array_shift_rightfunction.md)。

## <a name="examples"></a>示例

* 向右旋转两个位置：

    <!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_right(arr, 2)
    ```
    
    |arr|arr_rotated|
    |---|---|
    |[1,2,3,4,5]|[4,5,1,2,3]|

* 使用负 rotate_count 值向左旋转两个位置：

    <!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_right(arr, -2)
    ```
    
    |arr|arr_rotated|
    |---|---|
    |[1,2,3,4,5]|[3,4,5,1,2]|
