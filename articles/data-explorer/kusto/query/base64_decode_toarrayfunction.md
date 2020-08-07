---
title: base64_decode_toarray() - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 base64_decode_toarray()。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 06/22/2019
ms.date: 07/31/2020
ms.openlocfilehash: c7c0e9d82aa0c3b4a8fa4420160360124faf9d51
ms.sourcegitcommit: 4e1bc2e9b2a12dbcc05c52db5dbd1ae290aeb18d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2020
ms.locfileid: "87509251"
---
# <a name="base64_decode_toarray"></a>base64_decode_toarray()

将 base64 字符串解码为长值的数组。

## <a name="syntax"></a>语法

`base64_decode_toarray(`*String*`)`

## <a name="arguments"></a>参数

* *String*：要从 base64 解码为 UTF8 字符串的输入字符串。

## <a name="returns"></a>返回

返回从 base64 字符串解码而来的长值数组。

* 若要将 base64 字符串解码为 UTF-8 字符串，请参阅 [base64_decode_tostring()](base64_decode_tostringfunction.md)
* 若要将字符串编码为 base64 字符串，请参阅 [base64_encode_tostring()](base64_encode_tostringfunction.md)

## <a name="example"></a>示例

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
print Quine=base64_decode_toarray("S3VzdG8=")  
// 'K', 'u', 's', 't', 'o'
```

|Quine|
|-----|
|[75,117,115,116,111]|

如果尝试对由无效的 UTF-8 编码生成的 base64 字符串进行解码，将返回“null”：

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
print Empty=base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|空|
|-----|
||
