---
title: Microsoft Threat Modeling Tool 版本 2020/02/11 - Azure
description: 阐述 Threat Modeling Tool 的发行说明
author: jegeib
ms.author: v-tawe
ms.service: security
ms.topic: article
ms.date: 06/03/2020
origin.date: 02/25/2020
ms.openlocfilehash: 31719cd3804d3ba3f265d3ca73854b137266faba
ms.sourcegitcommit: 79c99a9ea013b3c74706a1038a505f4eea2aaac4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2020
ms.locfileid: "84439611"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Threat Modeling Tool 更新版 7.3.00206.1 - 2020/02/11

Microsoft Threat Modeling Tool (TMT) 的版本 7.3.00206.1 已于 2020 年 2 月 11 日发布，其中包含以下更改：

- Bug 修复

## <a name="notable-bug-fixes"></a>重要的 bug 修复

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>与预期范围外的优先级值相关的错误

一些客户反馈说，打开 Threat Modeling Tool 2016 或自定义模板中创建的文件时收到以下错误消息：

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

此问题已在此版本中得到解决。

## <a name="system-requirements"></a>系统要求

- 支持的操作系统
  - [Microsoft Windows 10 周年更新](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)或更高版本
- 所需的 .NET 版本
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 或更高版本
- 其他要求
  - 需要建立 Internet 连接才能接收工具和模板的更新。

## <a name="documentation-and-feedback"></a>文档和反馈

- Threat Modeling Tool 的文档位于 [docs.azure.cn](https://docs.azure.cn/security/azure-security-threat-modeling-tool)，其中包含[有关如何使用该工具](https://docs.azure.cn/security/azure-security-threat-modeling-tool-getting-started)的信息。

## <a name="next-steps"></a>后续步骤

下载最新版本的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。
