---
title: Kusto RestrictedViewAccess 策略管理 - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 RestrictedViewAccess 策略。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/24/2020
ms.date: 08/18/2020
ms.openlocfilehash: 19aa21d3a5fc6b324069d4d392fb65ca9a9c0875
ms.sourcegitcommit: f4bd97855236f11020f968cfd5fbb0a4e84f9576
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88515669"
---
# <a name="restricted_view_access-policy-command"></a>restricted_view_access 策略命令

RestrictedViewAccess 策略的信息可参见[此处](../management/restrictedviewaccesspolicy.md)。

用于在数据库中的表上启用或禁用策略的控制命令如下所示：

启用/禁用策略：
```kusto
.alter table TableName policy restricted_view_access true|false
```

启用/禁用多个表的策略：
```kusto
.alter tables (TableName1, ..., TableNameN) policy restricted_view_access true|false
```

查看策略：
```kusto
.show table TableName policy restricted_view_access  

.show table * policy restricted_view_access  
```

删除策略（与禁用行为等效）：
```kusto
.delete table TableName policy restricted_view_access  
```
