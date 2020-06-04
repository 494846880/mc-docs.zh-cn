---
title: 创建多个资源实例
description: 了解如何创建 Azure 资源管理器模板用于创建多个 Azure 资源实例。
author: rockboyfor
origin.date: 04/08/2020
ms.date: 04/30/2020
ms.topic: tutorial
ms.author: v-yeche
ms.openlocfilehash: cce23fdfaa0327999840969f578109dc4bc30c4c
ms.sourcegitcommit: b469d275694fb86bbe37a21227e24019043b9e88
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82596067"
---
# <a name="tutorial-create-multiple-resource-instances-with-arm-templates"></a>教程：使用 ARM 模板创建多个资源实例

了解如何在 Azure 资源管理器 (ARM) 模板中进行迭代操作，以创建 Azure 资源的多个实例。 在本教程中，你将修改一个模板，以便创建三个存储帐户实例。

![“Azure 资源管理器创建多个实例”示意图](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

本教程涵盖以下任务：

> [!div class="checklist"]
> * 打开快速入门模板
> * 编辑模板
> * 部署模板

如果没有 Azure 订阅，请在开始前[创建一个试用帐户](https://www.azure.cn/pricing/1rmb-trial/)。

## <a name="prerequisites"></a>先决条件

若要完成本文，需要做好以下准备：

* 包含资源管理器工具扩展的 Visual Studio Code。 请参阅[使用 Visual Studio Code 创建 ARM 模板](use-vs-code-to-create-template.md)。

## <a name="open-a-quickstart-template"></a>打开快速入门模板

[Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates/)是 ARM 模板的存储库。 无需从头开始创建模板，只需找到一个示例模板并对其自定义即可。 本快速入门中使用的模板称为[创建标准存储帐户](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/)。 该模板定义 Azure 存储帐户资源。

1. 在 Visual Studio Code 中，选择“文件”>“打开文件”。 
2. 在“文件名”中粘贴以下 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. 选择“打开”以打开该文件。
4. 在模板中定义了一个“Microsoft.Storage/storageAccounts”资源。
    
   <!-- Not Available on [template reference](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)-->

5. 选择“文件”>“另存为”，将该文件作为 **azuredeploy.json** 保存到本地计算机。 

## <a name="edit-the-template"></a>编辑模板

现有模板创建一个存储帐户。 请通过自定义模板来创建三个存储帐户。

在 Visual Studio Code 中进行以下四项更改：

![Azure 资源管理器创建多个实例](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. 将 `copy` 元素添加到存储帐户资源定义。 在 copy 元素中，为此循环指定迭代次数和变量。 计数值必须是不超过 800 的正整数。
2. `copyIndex()` 函数返回循环中的当前迭代。 使用索引作为名称前缀。 `copyIndex()` 从零开始。 若要偏移索引值，可以在 copyIndex() 函数中传递一个值。 例如 *copyIndex(1)* 。
3. 删除 **variables** 元素，因为不再需要使用它。
4. 删除 **outputs** 元素。 不再需要它。

完成的模板如下所示：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    }
  ]
}
```

有关创建多个实例的详细信息，请参阅[在 ARM 模板中部署资源或属性的多个实例](./copy-resources.md)

## <a name="deploy-the-template"></a>部署模板

有关部署过程，请参阅 Visual Studio Code 快速入门中的[部署模板](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template)部分。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要列出所有三个存储帐户，请省略 --name 参数：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource group name:" &&
read projectName &&
resourceGroupName="${projectName}rg" &&
az storage account list --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
$resourceGroupName = "${projectName}rg"
Get-AzStorageAccount -ResourceGroupName $resourceGroupName
Write-Host "Press [ENTER] to continue ..."
```

---

将存储帐户名称与模板中的名称定义进行比较。

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。  应会看到，该资源组中总共有三个资源。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

在本教程中，我们已了解如何创建多个存储帐户实例。  在下一篇教程中，我们将开发包含多个资源和多个资源类型的模板。 某些资源具有依赖的资源。

> [!div class="nextstepaction"]
> [创建依赖资源](./template-tutorial-create-templates-with-dependent-resources.md)

<!-- Update_Description: update meta properties, wording update, update link -->
