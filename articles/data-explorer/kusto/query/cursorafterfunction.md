---
title: cursor_after() - Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍 Azure 数据资源管理器中的 cursor_after()。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
origin.date: 02/19/2020
ms.date: 10/29/2020
zone_pivot_group_filename: zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 7b0a06171f7b604f5b9b466ba81729e64cdf436d
ms.sourcegitcommit: 39288459139a40195d1b4161dfb0bb96f5b71e8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590662"
---
# <a name="cursor_after"></a>cursor_after()

::: zone pivot="azuredataexplorer"

针对表记录的谓词，用于将记录的引入时间与数据库游标值进行比较。

## <a name="syntax"></a>语法

`cursor_after` `(` RHS `)`

## <a name="arguments"></a>参数

* RHS：空字符串文本或有效的数据库游标值。

## <a name="returns"></a>返回

`bool` 类型的标量值，指示记录的引入时间是否在数据库游标 RHS 之后 (`true`) 或非 (`false`)。

**说明**

有关数据库游标的其他详细信息，请参阅[数据库游标](../management/databasecursor.md)。

只能对已启用 [IngestionTime 策略](../management/ingestiontimepolicy.md)的表记录调用此函数。

::: zone-end

::: zone pivot="azuremonitor"

Azure Monitor 不支持此功能

::: zone-end
