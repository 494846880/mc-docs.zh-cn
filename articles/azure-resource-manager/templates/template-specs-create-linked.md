---
title: 创建具有链接模板的模板规格
description: 了解如何创建具有链接模板的模板规格。
ms.topic: conceptual
origin.date: 07/22/2020
ms.date: 08/24/2020
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.openlocfilehash: f3dba82ee47f8920e6103a5e02914148bd118241
ms.sourcegitcommit: 601f2251c86aa11658903cab5c529d3e9845d2e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88807938"
---
<!--Waiting for approval via sending request on 08/25/2020-->
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>教程：创建具有链接模板的模板规格（预览）

了解如何创建具有[链接模板](linked-templates.md#linked-template)的[模板规格](template-specs.md)。 使用模板规格与组织中的其他用户共享 ARM 模板。 本文介绍如何使用部署资源的新 `relativePath` 属性创建模板规格以打包主模板及其链接模板。

<!--Not Available on [deployment resource](https://docs.microsoft.com/azure/templates/microsoft.resources/deployments)-->

## <a name="prerequisites"></a>先决条件

具有活动订阅的 Azure 帐户。 [免费创建帐户](https://www.azure.cn/pricing/1rmb-trial)。

> [!NOTE]
> 模板规格当前提供预览版。 若要使用它，必须[注册预览版](https://aka.ms/templateSpecOnboarding)。

## <a name="create-linked-templates"></a>创建链接模板

创建主模板和链接模板。

若要链接某个模板，请向主模板中添加一个部署资源。 在 `templateLink` 属性中，根据父模板的路径指定链接模板的相对路径。

<!--Not Available on [deployments resource](https://docs.microsoft.com/azure/templates/microsoft.resources/deployments)-->

该链接模板名为 linkedTemplate.json，存储在主模板所在的路径中名为 artifacts 的子文件夹中 。  你可以使用 relativePath 的以下值之一：

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

`relativePath` 属性始终与声明 `relativePath` 的模板文件相关，因此，如果有另一个 linkedTemplate2.json 从 linkedTemplate.json 调用，并且 linkedTemplate2.json 存储在相同的 artifacts 子文件夹中，则在 linkedTemplate.json 中指定的 relativePath 仅仅是 `linkedTemplate2.json`。

1. 使用以下 JSON 创建主模板。 将主模板作为 azuredeploy.json 保存到本地计算机。 本教程假设你已将主模板保存到路径 c:\Templates\linkedTS\azuredeploy.json，但你可以使用任何路径。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "chinanorth2",
          "metadata":{
            "description": "Specify the location for the resources."
          }
        },
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "metadata":{
            "description": "Specify the storage account type."
          }
        }
      },
      "variables": {
        "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Web/serverfarms",
          "apiVersion": "2016-09-01",
          "name": "[variables('appServicePlanName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "B1",
            "tier": "Basic",
            "size": "B1",
            "family": "B",
            "capacity": 1
          },
          "kind": "linux",
          "properties": {
            "perSiteScaling": false,
            "reserved": true,
            "targetWorkerCount": 0,
            "targetWorkerSizeId": 0
          }
        },
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "createStorage",
          "properties": {
            "mode": "Incremental",
            "templateLink": {
              "relativePath": "artifacts/linkedTemplate.json"
            },
            "parameters": {
              "storageAccountType": {
                "value": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      ]
    }
    ```

    > [!NOTE]
    > `Microsoft.Resources/deployments` 的 apiVersion 必须是 2020-06-01 或更高版本。

1. 在保存主模板的文件夹中创建名为 artifacts 的目录。
1. 使用以下 JSON 创建链接模板：

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
      "variables": {
        "storageAccountEndPoint": "https://core.chinacloudapi.cn/",

        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "apiVersion": "2019-04-01",
          "name": "[variables('storageAccountName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "[parameters('storageAccountType')]"
          },
          "kind": "StorageV2",
          "properties": {}
        }
      ],
      "outputs": {

        "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
        }
      }
    }
    ```

1. 将模板作为 linkedTemplate.json 保存在 artifacts 文件夹中 。

## <a name="create-template-spec"></a>创建模板规格

模板规格存储在资源组中。  创建资源组，然后使用以下脚本创建模板规格。 模板规格的名称为 webSpec。

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location chinanorth2

New-AzTemplateSpec `
  -ResourceGroupName templateSpecRG `
  -Name webSpec `
  -Version "1.0.0.0" `
  -Location chinanorth2 `
  -TemplateJsonFile "c:\Templates\linkedTS\azuredeploy.json"
```

完成后，可以从 Azure 门户或使用以下 cmdlet 查看模板规格：

```powershell
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

## <a name="deploy-template-spec"></a>部署模板规格

现在即可部署模板规格。部署模板规格就像部署它包含的模板一样，只不过你传入了模板规格的资源 ID。使用相同的部署命令，并在需要时为模板规格传递参数值。

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location chinanorth2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

## <a name="next-steps"></a>后续步骤

若要了解如何将模板规格部署为链接模板，请参阅[教程：将模板规格部署为链接模板](template-specs-deploy-linked-template.md)。

<!-- Update_Description: new article about template specs create linked -->
<!--NEW.date: 08/24/2020-->