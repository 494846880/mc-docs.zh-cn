---
title: 转换为图像目录
titleSuffix: Azure Machine Learning
description: 了解如何使用“转换为图像目录”模块将数据集转换为图像目录格式。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: fa788629dd297c2de3a54868141a8ecead921477
ms.sourcegitcommit: 9d9795f8a5b50cd5ccc19d3a2773817836446912
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88228466"
---
# <a name="convert-to-image-directory"></a>转换为图像目录

本文介绍如何使用“转换为图像目录”模块将图像数据集转换为“图像目录”数据类型，该类型采用图像相关任务（例如 Azure 机器学习设计器（预览版）中的图像分类）中的标准化数据格式。

## <a name="how-to-use-convert-to-image-directory"></a>如何使用“转换为图像目录”模块  

1.  将“转换为图像目录”模块添加到试验中。 可以在模块列表的“计算机视觉/图像数据转换”类别中找到此模块。 

2.  [注册一个图像数据集](/machine-learning/how-to-create-register-datasets)，并将其连接到模块输入端口。 请确保输入数据集中有图像。 
    支持以下数据集格式：

    - 具有以下扩展名的压缩文件：“.zip”、“.tar”、“.gz”、“.bz2”。
    - 包含图像的文件夹。 强烈建议先压缩此类文件夹，然后再使用压缩文件作为数据集。

    > [!WARNING]
    > 不能使用“导入数据”模块导入图像数据集，因为“导入数据”模块的输出类型是“DataFrame 目录”，它只包含文件路径字符串。
    

    > [!NOTE]
    > 如果在监督式学习中使用图像数据集，则需要标签。
    > 对于图像分类任务，如果此图像数据集以 Torchvision ImageFolder 格式进行组织，则可以在模块输出中将标签生成为图像“类别”。 否则，只保存不带标签的图像。 下面是一个示例，说明了如何组织图像数据集以获取标签，并使用图像类别作为子文件夹名称。 有关详细信息，请参阅 [torchvision 数据集](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder)。
    >
    > ```
    > root/dog/xxx.png
    > root/dog/xxy.png
    > root/dog/xxz.png
    >
    > root/cat/123.png
    > root/cat/nsdf3.png
    > root/cat/asd932_.png
    > ```

3.  提交管道。

## <a name="results"></a>结果

“转换为图像目录”模块的输出为“图像目录”格式，可连接到其他与图像相关的模块（其输入端口格式也是“图像目录”）。

## <a name="technical-notes"></a>技术说明 

###  <a name="expected-inputs"></a>预期输入  

| 名称          | 类型                  | 说明   |
| ------------- | --------------------- | ------------- |
| 输入数据集 | 任何目录、Zip 文件 | 输入数据集 |

###  <a name="output"></a>输出  

| 名称                   | 类型           | 说明            |
| ---------------------- | -------------- | ---------------------- |
| 输出图像目录 | 图像目录 | 输出图像目录 |

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
