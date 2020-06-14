---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/05/2020
ms.author: v-junlch
ms.openlocfilehash: 3462eb83a355f9df7e467bceda17c28ad5bc8de5
ms.sourcegitcommit: f1a76ee3242698123a3d77f44c860db040b48f70
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "84563708"
---
## <a name="publish-the-project-to-azure"></a>将项目发布到 Azure

在本部分，你将在 Azure 订阅中创建一个函数应用和相关资源，然后部署代码。 

> [!IMPORTANT]
> 发布到现有函数应用将覆盖该应用在 Azure 中的内容。 


1. 在活动栏中选择“Azure”图标，然后在“Azure:函数”区域中，选择“部署到函数应用...”按钮。

    ![将项目发布到 Azure](./media/functions-publish-project-vscode/function-app-publish-project.png)

1. 根据提示提供以下信息：

    + **选择文件夹**：从工作区中选择一个文件夹，或浏览到包含函数应用的文件夹。 如果已打开有效的函数应用，则不会看到此信息。

    + **选择订阅**：选择要使用的订阅。 如果只有一个订阅，则不会看到此项。

    + **在 Azure 中选择函数应用**：选择`+ Create new Function App`。 （请不要选择本文中未介绍的 `Advanced` 选项。）
      
    + **输入函数应用的全局唯一名称**：键入在 URL 路径中有效的名称。 将对你键入的名称进行验证，以确保其在 Azure Functions 中是唯一的。 
        
    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **选择运行时**：选择你一直在本地运行的 Node.js 版本。 可以使用 `node --version` 命令来检查你的版本。
    ::: zone-end

    + **选择新资源的位置**：为了获得更好的性能，请选择你附近的[区域](https://azure.microsoft.com/regions/)。 
    
1.  完成后，将使用基于函数应用名称的名称在订阅中创建以下 Azure 资源：
    
    + 一个资源组：相关资源的逻辑容器。
    + 一个标准 Azure 存储帐户：用于维护项目的状态和其他信息。
    + 一个消耗计划：用于定义无服务器函数应用的基础主机。 
    + 一个函数应用：提供用于执行函数代码的环境。 可以通过函数应用将函数分组为逻辑单元，以便在同一托管计划中更轻松地管理、部署和共享资源。

    创建函数应用并应用了部署包之后，会显示一个通知。 
    
1. 在此通知中选择“查看输出”以查看创建和部署结果，其中包括你创建的 Azure 资源。 如果错过了通知，请选择右下角的响铃图标以再次查看。

    ![创建完成通知](./media/functions-publish-project-vscode/function-create-notifications.png)

