---
title: include 文件
description: include 文件
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
origin.date: 05/11/2020
ms.date: 12/10/2020
ms.author: v-tawe
ms.custom: include file
ms.openlocfilehash: ab8c8008cb1f7f3dbe9ee89d20b9b98f1af29407
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004225"
---
搜索服务受磁盘空间限制，或者受索引或索引器最大数目的硬限制，具体取决于哪一个限制先实施。 下表描述了存储限制。 有关最大对象限制，请参阅[按资源限制](../articles/search/search-limits-quotas-capacity.md#index-limits)。

| 资源 | 免费 | 基本<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| 服务级别协议 (SLA)<sup>2</sup>  |否 |是 |是 |是 |是 |是 |是 |是 |
| 每个分区的存储 |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| 每个服务的分区数 |空值 |1 |12 |12 |12 |3 |12 |12 |
| 分区大小 |空值 |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| 副本 |空值 |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> 基本层有一个固定分区。 可以使用其他搜索单位来添加较大查询量的副本。

<sup>2</sup> 服务级别协议对专用资源的可计费服务有效。 免费服务和预览功能没有 SLA。 对于可计费服务，SLA 将在用户为服务预配足够冗余时生效。 查询（读取）SLA 需要两个或多个副本。 查询和索引（读-写）SLA 需要不少于三个副本。 分区数不属于 SLA 相关考虑因素。 