---
title: 沙盒策略 - Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍了 Azure 数据资源管理器中的沙盒策略。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 03/24/2020
ms.date: 08/18/2020
ms.openlocfilehash: b811cdccf88c656c296c75a8fd0ed1c798c26fab
ms.sourcegitcommit: f4bd97855236f11020f968cfd5fbb0a4e84f9576
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88515652"
---
# <a name="sandbox-policy-command"></a>沙盒策略命令

使用以下命令可管理 Kusto 引擎服务中的[沙盒](../concepts/sandboxes.md)和[沙盒策略](sandboxpolicy.md)。

命令需要 [AllDatabasesAdmin](access-control/role-based-authorization.md) 权限。

## <a name="sandbox-policy"></a>沙盒策略

### <a name="show-cluster-policy-sandbox"></a>.show cluster policy sandbox

在群集级别显示所有已配置的沙盒策略。

```kusto
.show cluster policy sandbox
```

### <a name="alter-cluster-policy-sandbox"></a>.alter cluster policy sandbox

在群集级别修改沙盒策略集合。

```kusto
.alter cluster policy sandbox @'['
  '{'
    '"SandboxKind": "PythonExecution",'
    '"IsEnabled": true,'
    '"TargetCountPerNode": 4,'
    '"MaxCpuRatePerSandbox": 50,'
    '"MaxMemoryMbPerSandbox": 10240'
  '},'
  '{'
    '"SandboxKind": "RExecution",'
    '"IsEnabled": true,'
    '"TargetCountPerNode": 4,'
    '"MaxCpuRatePerSandbox": 50,'
    '"MaxMemoryMbPerSandbox": 10240'
  '}'
']'
```

### <a name="drop-cluster-policy-sandbox"></a>.drop cluster policy sandbox

若要删除所有沙盒策略，请使用以下命令：

```kusto
.delete cluster policy sandbox
```

