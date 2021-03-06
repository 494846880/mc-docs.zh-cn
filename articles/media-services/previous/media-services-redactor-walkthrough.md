---
title: 使用 Azure 媒体分析进行面部修订演练 | Microsoft Docs
description: 本主题提供分步指导，引导用户使用 Azure 媒体服务资源管理器 (AMSE) 和 Azure Media Redactor Visualizer（开源工具）运行完整修正工作流。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
origin.date: 03/20/2019
ms.date: 11/30/2020
ms.author: v-jay
ms.reviewer: juliako
ms.openlocfilehash: 40c75818f1731e1fe047bbcc1b8a1292d3e56b41
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96300280"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>使用 Azure 媒体分析进行面部修订演练

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>概述

**Azure 媒体修订器** 是一种 [Azure 媒体分析](media-services-analytics-overview.md)媒体处理器 (MP)，可用于在云中进行可缩放的面部修订。 使用面部修订，可对视频进行修改，使所选个人的面部模糊显示。 用户可能想要在公共安全和新闻媒体场景中使用面部修订服务。 对于时长仅几分钟但包含多张面孔的镜头，进行手动面部修订可能需要几个小时，但使用此服务仅需几个简单步骤即可完成该过程。 有关详细信息，请参阅[此](https://azure.microsoft.com/blog/azure-media-redactor/)博客。

有关 **Azure Media Redactor** 的详细信息，请参阅 [面部修正概述](media-services-face-redaction.md)主题。

本主题提供分步指导，引导用户使用 Azure 媒体服务资源管理器 (AMSE) 和 Azure Media Redactor Visualizer（开源工具）运行完整修正工作流。

有关详细信息，请参阅[此](https://azure.microsoft.com/blog/redaction-preview-available-globally)博客。

## <a name="azure-media-services-explorer-workflow"></a>Azure 媒体服务资源管理器工作流

使用 Redactor 的最简单方法是使用 GitHub 上的开源 AMSE 工具。 如果不需要访问批注 json 或 jpg 面部图像，可通过 **合并** 模式运行简化的工作流。

### <a name="download-and-setup"></a>下载和安装

1. 从[此处](https://aka.ms/amseforv2)下载 AMSE for AMS v2 工具。
1. 使用服务密钥登录媒体服务帐户。

    若要获取帐户名称和密钥信息，请转到 [Azure 门户](https://portal.azure.cn/)并选择 AMS 帐户。 然后，选择“设置”>“密钥”。 “管理密钥”窗口显示帐户名称、主密钥和辅助密钥。 复制帐户名称和主密钥的值。

![屏幕截图显示 Microsoft Azure 媒体服务，你可在其中输入帐户名和密钥。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>第一步 – 分析模式

1. 通过“资产”–>“上传”或拖放上传媒体文件。 
1. 右键单击，并使用“媒体分析”–>“Azure Media Redactor”–>“分析模式”处理媒体文件。 


![屏幕截图显示一个菜单，其中包含 Azure 媒体编修器的过程资产。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![屏幕截图显示 Azure 媒体编修器，其中已选择“第一步 - 分析模式”。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

输出包括带有面部位置数据的批注 json 文件以及检测到的每张脸的 jpg。 

![屏幕截图显示了分析的输出。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>第二步 – 修正模式

1. 将原始视频资产上传到第一步中的输出，并设置为主要资产。 

    ![屏幕截图显示“上传”和“设置为主要”按钮。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. （可选）上传“Dance_idlist.txt”文件，它包含要修订的 ID 的换行符分隔列表。 

    ![屏幕截图显示上传文本文件的选项。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. （可选）编辑 annotations.json 文件，例如增加边界框边界。 
4. 右键单击第一步中的输出资产，选择“Redactor”，在“修正”  模式下运行。 

    ![屏幕截图显示 Azure 媒体编修器，其中已选择“第二步 - 修正模式”。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. 下载或共享最终修正的输出资产。 

    ![屏幕截图显示“下载”按钮。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Azure Media Redactor Visualizer 开源工具

一种开源[可视化工具](https://github.com/Microsoft/azure-media-redactor-visualizer)，可帮助开发人员通过分析使用批注格式和使用输出。

克隆存储库后，若要运行项目，需要从其[官方网站](https://ffmpeg.org/download.html)下载 FFMPEG。

如果开发人员要分析 JSON 批注数据，可在 Models.MetaData 内查看样本代码示例。

### <a name="set-up-the-tool"></a>设置工具

1.  下载并生成整个解决方案。 

    ![屏幕截图显示从菜单中选择了“生成解决方案”。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  从[此处](https://ffmpeg.org/download.html)下载 FFMPEG。 此项目最初使用具有静态链接的版本 be1d324 (2016-10-04) 开发。 
3.  将 ffmpeg.exe 和 ffprobe.exe 复制到与 AzureMediaRedactor.exe 相同的输出文件夹。 

    ![屏幕截图显示文件夹的内容，其中包括 ffmpeg 和 ffprobe。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. 运行 AzureMediaRedactor.exe。 

### <a name="use-the-tool"></a>使用工具

1. 在分析模式下使用 Redactor MP 在 Azure 媒体服务帐户中处理视频。 
2. 下载原始视频文件和“修正 - 分析”作业的输出。 
3. 运行可视化工具应用程序并选择上述文件。 

    ![屏幕截图显示 Azure 媒体编修器正在上传文件。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. 预览文件。 通过右侧边栏选择要进行模糊处理的人脸。 
    
    ![屏幕截图显示 Azure 媒体编修器，可在其中预览并选择要进行模糊处理的人脸。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  将更新底部文本字段，使其带有面部 ID。 创建名为“idlist.txt”的文件，将这些 ID 作为换行符分隔列表。 

    >[!NOTE]
    > idlist.txt 应保存在 ANSI 中。 可以使用记事本保存在 ANSI 中。
    
6.  将此文件上传到步骤 1 中的输出资产。 也将原始视频上传到此资产并设置为主要资产。 
7.  在“修正”模式下对此资产运行修正作业，以获取最终修正的视频。 

## <a name="next-steps"></a>后续步骤 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="related-links"></a>相关链接
[Azure 媒体服务分析概述](media-services-analytics-overview.md)

[Azure 媒体分析演示](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[宣布推出 Azure 媒体分析的面部修订](https://azure.microsoft.com/blog/azure-media-redactor/)
