---
title: 使用模板参考
description: 使用 Azure 资源管理器模板参考来创建模板。
author: rockboyfor
origin.date: 04/23/2020
ms.date: 06/22/2020
ms.topic: tutorial
ms.author: v-yeche
ms.custom: seodec18
ms.openlocfilehash: 98853e6df737c2a2a618e603d3c93298c6ca08d5
ms.sourcegitcommit: 48b5ae0164f278f2fff626ee60db86802837b0b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85098714"
---
# <a name="tutorial-utilize-the-resource-manager-template-reference"></a>教程：利用资源管理器模板参考

了解如何查找模板架构信息，以及如何使用该信息创建 Azure 资源管理器 (ARM) 模板。

在本教程中，请使用 Azure 快速入门模板中提供的基础模板。 可以使用模板参考文档来自定义模板。

![资源管理器模板参考部署存储帐户](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

本教程涵盖以下任务：

> [!div class="checklist"]
> * 打开快速入门模板
> * 了解模板
> * 查找模板参考
> * 编辑模板
> * 部署模板

如果没有 Azure 订阅，请在开始前[创建一个试用帐户](https://www.azure.cn/pricing/1rmb-trial/)。

## <a name="prerequisites"></a>先决条件

若要完成本文，需要做好以下准备：

* 包含资源管理器工具扩展的 Visual Studio Code。 请参阅[使用 Visual Studio Code 创建 ARM 模板](use-vs-code-to-create-template.md)。

## <a name="open-a-quickstart-template"></a>打开快速入门模板

[Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates/)是 ARM 模板的存储库。 无需从头开始创建模板，只需找到一个示例模板并对其自定义即可。 本快速入门中使用的模板称为[创建标准存储帐户](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/)。 该模板定义 Azure 存储帐户资源。

1. 在 Visual Studio Code 中，选择“文件”>“打开文件”。  
1. 在“文件名”中粘贴以下 URL： 

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. 选择“打开”以打开该文件。 
1. 选择“文件”>“另存为”，将该文件作为 **azuredeploy.json** 保存到本地计算机。  

## <a name="understand-the-schema"></a>了解架构

1. 在 VS Code 中将模板折叠到根级别。 你使用最简单的结构，其中包含以下元素：

    ![资源管理器模板的最简单结构](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * **$schema**：指定描述模板语言版本的 JSON 架构文件所在的位置。
    * **contentVersion**：为此元素指定任意值，以便记录模板中的重要更改。
    * **parameters**：指定执行部署以自定义资源部署时提供的值。
    * **variables**：指定在模板中用作 JSON 片段以简化模板语言表达式的值。
    * **resources**：指定已在资源组中部署或更新的资源类型。
    * **outputs**：指定部署后返回的值。

1. 展开“resources”  。 已定义 `Microsoft.Storage/storageAccounts` 资源。 SKU 名称使用参数值。  此参数称为 **storageAccountType**。

    ![资源管理器模板存储帐户定义](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

1. 展开 **parameters** 即可查看 **storageAccountType** 是如何定义的。 此参数有四个允许的值。 需找到其他允许的值，然后修改参数定义。

    ![资源管理器模板存储帐户资源 SKU](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus-old.png)

<!--Not Available on ## Find the template reference-->
<!--Not Available on ## Edit the template-->
## <a name="deploy-the-template"></a>部署模板

有关部署过程，请参阅 Visual Studio Code 快速入门中的[部署模板](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template)部分。 部署模板时，请使用新添加的值指定 **storageAccountType** 参数，例如 **Premium_ZRS**。 如果使用原始快速入门模板，部署会失败，因为 Premium_ZRS  在Azure 中国云上是不允许使用的值。  若要传递参数值，请将以下开关添加到部署命令：

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
--parameters storageAccountType='Premium_LRS'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
-storageAccountType "Premium_LRS"
```

<!--CORRCT TO UPDATE TO Premium_LRS-->
---

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。 
3. 选择资源组名称。  应会看到，该资源组中总共有六个资源。
4. 在顶部菜单中选择“删除资源组”。 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用模板参考来自定义现有的模板。 若要了解如何创建多个存储帐户实例，请参阅：

> [!div class="nextstepaction"]
> [创建多个实例](./template-tutorial-create-multiple-instances.md)

<!-- Update_Description: new article about template tutorial use template reference -->
<!--NEW.date: 06/16/2020-->