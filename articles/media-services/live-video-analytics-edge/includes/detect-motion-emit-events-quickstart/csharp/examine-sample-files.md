---
ms.openlocfilehash: 6a7a67a375d5c728dcd3fc3b6bca97bdc3c459a2
ms.sourcegitcommit: 2eb5a2f53b4b73b88877e962689a47d903482c18
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89413427"
---
1. 在 Visual Studio Code 中，转到 src/edge。 你将看到 .env 文件以及一些部署模板文件。

    部署模板是指边缘设备的部署清单，其中使用变量指定某些属性。 该 .env 文件包含这些变量的值。
1. 转到 src/cloud-to-device-console-app 文件夹。 你可在此处看到 appsettings.json 文件和一些其他文件：

    * c2d-console-app.csproj - Visual Studio Code 的项目文件。
    * operations.json - 希望程序运行的操作的列表。
    * Program.cs - 示例程序代码。 此代码：
    
      * 加载应用设置。
      * 在 IoT Edge 模块上调用实时视频分析公开的直接方法。 可以通过调用模块的[直接方法](../../../direct-methods.md)来使用该模块分析实时视频流。
      * 暂停以检查“终端”窗口中程序的输出，并检查“输出”窗口中模块生成的事件 。
      * 调用直接方法以清理资源。   
