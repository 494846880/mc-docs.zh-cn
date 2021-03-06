---
title: 本机代码中的错误 - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的本机代码错误。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
origin.date: 06/15/2020
ms.date: 08/18/2020
ms.openlocfilehash: bed25e0e47732283ac2d71ad6eaa930182f0c055
ms.sourcegitcommit: f4bd97855236f11020f968cfd5fbb0a4e84f9576
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516104"
---
# <a name="errors-in-native-code"></a>本机代码中的错误

Kusto 引擎使用本机代码编写，并使用负的 `HRESULT` 值报告错误。 这些错误在编程 API 中不常见，但可能会发生。 例如，操作失败可能会显示状态“`Exception from HRESULT:` HRESULT-CODE”。

Kusto 本机错误代码使用 Windows `MAKE-HRESULT` 宏来定义，其中：

* Severity 设置为 `1`，
* Facility 设置为 `0xDA`
  
定义了以下错误代码。

|清单常量                  |代码  |Value       |含义                                                                                                        |
|-----------------------------------|------|------------|---------------------------------------------------------------------------------------------------------------|
|`E_EXTENT_LOAD_FAILED`             | `0`  |`0x80DA0000`|无法加载数据区                                                                                 |
|`E_RUNAWAY_QUERY`                  | `1`  |`0x80DA0001`|查询执行已中止，因为它超过了允许的资源数                                              |
|`E_STREAM_FORMAT`                  | `2`  |`0x80DA0002`|无法分析数据流，因为它的格式不正确                                                     |
|`E_QUERY_RESULT_SET_TOO_LARGE`     | `3`  |`0x80DA0003`|此查询的结果集超出了其允许的记录/大小限制                                         |
|`E_STREAM_ENCODING_VERSION`        | `4`  |`0x80DA0004`|无法分析结果流，因为其版本未知                                                 |
|`E_KVDB_ERROR`                     | `5`  |`0x80DA0005`|嵌入的键/值存储组件中出现故障                                                              |
|`E_QUERY_CANCELLED`                | `6`  |`0x80DA0006`|查询已取消                                                                                             |
|`E_LOW_MEMORY_CONDITION`           | `7`  |`0x80DA0007`|出现内存不足的情况                                                                                  |
|`E_WRONG_NUMBER_OF_FIELDS`         | `8`  |`0x80DA0008`|输入流中的字段数目错误                                                                 |
|`E_INPUT_STREAM_TOO_LARGE`         | `9`  |`0x80DA0009`|字段/记录/流的输入大小超出了允许的长度                                          |
|`E_ENTITY_NOT_FOUND`               | `10` |`0x80DA000A`|找不到请求的实体                                                                              |
|`E_CLOSING_QUOTE_MISSING`          | `11` |`0x80DA000B`|提交的用于引入的 csv 流有一个字段缺少引号                                          |
|`E_OVERFLOW`                       | `12` |`0x80DA000C`|表示算术溢出错误。 对于目标类型来说，计算结果太大     |
|`E_INCOMPATIBLE_TOKENIZERS`        | `13` |`0x80DA000D`|由于合并索引的 tokenizer 不兼容，区合并失败                                    |
|`E_DYNAMIC_PROPERTY_BAG_TOO_LARGE` | `14` |`0x80DA000E`|属性包的不同键的组合大小太大                                             |
|`E_RS_BLOCKED_ROWSTOREKEY_ERROR`   | `101`|`0x80DA0065`|已尝试访问被阻止的 RowStore 密钥                                                           |
|`E_RS_SHUTTINGDOWN_ERROR`          | `102`|`0x80DA0066`|RowStore 正在关闭                                                                                      |
|`E_RS_LOCAL_STORAGE_FULL_ERROR`    | `103`|`0x80DA0067`|用于 RowStore 的本地磁盘存储已满                                                                        |
|`E_RS_CANNOT_READ_WRITE_AHEAD_LOG` | `104`|`0x80DA0068`|从 RowStore write-ahead-log 存储读取失败                                                           |
|`E_RS_CANNOT_RETRIEVE_VALUES_ERROR`| `105`|`0x80DA0069`|无法从 RowStore 存储检索值                                                               |
|`E_RS_NOT_READY_ERROR`             | `106`|`0x80DA006A`|RowStore 正在初始化                                                                                       |
|`E_RS_INSERTION_THROTTLED_ERROR`   | `107`|`0x80DA006B`|向 RowStore 插入值受到限制                                                                    |
|`E_RS_READONLY_ERROR`              | `108`|`0x80DA006C`|RowStore 以只读状态附加                                                                        |
|`E_RS_UNAVAILABLE_ERROR`           | `109`|`0x80DA006D`|RowStore 当前不可用                                                                              |
|`E_RS_DOES_NOT_EXIST_ERROR`        | `110`|`0x80DA006E`|RowStore 不存在                                                                                         |
|`E_SB_QUERY_THROTTLED_ERROR`       | `200`|`0x80DA00C8`|沙盒查询受到限制                                                                                  |
|`E_SB_QUERY_CANCELLED`             | `201`|`0x80DA00C9`|沙盒查询已取消                                                                                   |
|`E_UNSUPPORTED_INSTRUCTION_SET`    | `202`|`0x80DA00CA`|此 CPU 不支持引擎的必需指令集                                                   |
