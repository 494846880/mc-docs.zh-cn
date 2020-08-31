---
title: Azure Stack Hub 的 Get-AzureStackSupportConfiguration 特权终结点
description: PowerShell Azure Stack 特权终结点的引用 - Get-AzureStackSupportConfiguration
author: WenJason
ms.topic: reference
origin.date: 04/27/2020
ms.date: 08/31/2020
ms.author: v-jay
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 7442eb3f298a36f4a38e3ae2bfe73c4589929b96
ms.sourcegitcommit: 4e2d781466e54e228fd1dbb3c0b80a1564c2bf7b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88867728"
---
# <a name="get-azurestacksupportconfiguration"></a>Get-AzureStackSupportConfiguration

## <a name="synopsis"></a>摘要
获取支持服务配置设置。

## <a name="syntax"></a>语法

```
Get-AzureStackSupportConfiguration [-IncludeRegistrationObjectId] [-AsJob]
```

## <a name="description"></a>说明
支持服务配置设置。

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1
下面的示例在标记已注册的情况下获取注册详细信息，否则显示为 null。

```
PS  C:\> Get-AzureStackSupportConfiguration
```

## <a name="parameters"></a>parameters

### <a name="-includeregistrationobjectid"></a>-IncludeRegistrationObjectId
可选。
需要建立 Internet 连接。
检索注册标识对象 ID。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-asjob"></a>-AsJob


```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="notes"></a>注释
要求“支持 VM”具有 Internet 连接。

## <a name="next-steps"></a>后续步骤

有关如何访问和使用特权终结点的信息，请参阅[使用 Azure Stack Hub 中的特权终结点](../../operator/azure-stack-privileged-endpoint.md)。
