---
title: 快速入门 - 使用 Azure 资源管理器模板（ARM 模板）创建流量管理器
description: 本快速入门文章介绍如何使用 Azure 资源管理器模板（ARM 模板）创建 Azure 流量管理器。
services: traffic-manager
ms.service: traffic-manager
ms.topic: quickstart
ms.custom: subject-armqs
origin.date: 09/01/2020
author: rockboyfor
ms.date: 11/16/2020
ms.testscope: yes
ms.testdate: 09/28/2020
ms.author: v-yeche
ms.openlocfilehash: 06abfde7f66d1ea1481d1ac51112131e24dc5276
ms.sourcegitcommit: a1f565fd202c1b9fd8c74f814baa499bbb4ed4a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96507747"
---
<!--Verified Successfully-->
# <a name="quickstart-create-a-traffic-manager-profile-using-an-arm-template"></a>快速入门：使用 ARM 模板创建流量管理器配置文件

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）创建具有使用性能路由方法的外部终结点的流量管理器配置文件。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署到 Azure":::](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-traffic-manager-external-endpoint%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-traffic-manager-external-endpoint)。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "uniqueDnsName": {
      "type": "string",
      "metadata": {
        "description": "Relative DNS name for the traffic manager profile, must be globally unique."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Network/trafficManagerProfiles",
      "apiVersion": "2018-08-01",
      "name": "ExternalEndpointExample",
      "location": "global",
      "properties": {
        "profileStatus": "Enabled",
        "trafficRoutingMethod": "Performance",
        "dnsConfig": {
          "relativeName": "[parameters('uniqueDnsName')]",
          "ttl": 30
        },
        "monitorConfig": {
          "protocol": "HTTPS",
          "port": 443,
          "path": "/",
          "expectedStatusCodeRanges": [
            {
              "min": 200,
              "max": 202
            },
            {
              "min": 301,
              "max": 302
            }
          ]
        },
        "endpoints": [
          {
            "type": "Microsoft.Network/TrafficManagerProfiles/ExternalEndpoints",
            "name": "endpoint1",
            "properties": {
              "target": "www.microsoft.com",
              "endpointStatus": "Enabled",
              "endpointLocation": "chinaeast2"
            }
          },
          {
            "type": "Microsoft.Network/TrafficManagerProfiles/ExternalEndpoints",
            "name": "endpoint2",
            "properties": {
              "target": "docs.microsoft.com",
              "endpointStatus": "Enabled",
              "endpointLocation": "chinaeast"
            }
          }
        ]
      }
    }
  ]
}
```

<!--Not Available on * [**Microsoft.Network/trafficManagerProfiles**](https://docs.microsoft.com/azure/templates/microsoft.network/trafficmanagerprofiles)-->
<!--Not Available on To find more templates that are related to Azure Traffic Manager, see [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)-->

## <a name="deploy-the-template"></a>部署模板

<!--Not Available on 1. Select **Try it** from the following code block to open Azure Cloud Shell,-->

1. 打开本地 Shell，然后按照说明登录到 Azure。 

    ```powershell
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. chinaeast)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-traffic-manager-external-endpoint/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    等到控制台中显示提示。

1. 从上一个代码块中选择“复制”，以复制 PowerShell 脚本。

1. 右键单击 shell 控制台窗格，然后选择“粘贴”。

1. 输入相应的值。

    模板部署将创建包含两个外部终结点的配置文件。 Endpoint1 使用位于“中国北部”的目标终结点 `www.microsoft.com` 。 Endpoint2 使用位于“中国东部”的目标终结点 `docs.microsoft.com` 。

    资源组名称是追加了 **rg** 的项目名称。

    > [!NOTE]
    > “uniqueDNSname”需要是全局唯一名称，才能成功部署模板。 如果部署失败，请从步骤 1 重新开始。

    部署模板需要几分钟时间。 完成后，输出类似于：

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-arm-powershell-output.png" alt-text="Azure 流量管理器资源管理器模板 PowerShell 部署输出":::

使用 Azure PowerShell 部署模板。 除了 Azure PowerShell，还可以使用 Azure 门户、Azure CLI 和 REST API。 若要了解其他部署方法，请参阅[部署模板](../azure-resource-manager/templates/deploy-portal.md)。

## <a name="validate-the-deployment"></a>验证部署

1. 使用 [Get-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile) 确定流量管理器配置文件的 DNS 名称。

    ```powershell
    Get-AzTrafficManagerProfile -Name ExternalEndpointExample -ResourceGroupName $resourceGroupName | Select RelativeDnsName
    ```

    复制 **RelativeDnsName** 值。 流量管理器配置文件的 DNS 名称为 `<relativednsname>.trafficmanager.cn`。

1. 在本地 PowerShell 中，运行以下命令，将 {relativeDNSname} 变量替换为 `<relativednsname>.trafficmanager.cn`。

    ```powershell
    Resolve-DnsName -Name {relativeDNSname} | Select-Object NameHost | Select -First 1
    ```

    你应该获得的 NameHost 为 `www.microsoft.com` 或 `docs.microsoft.com`，具体取决于哪个区域离你更近。

1. 若要检查是否可以解析为其他终结点，请禁用在上一步中获得的目标的终结点。 将 {endpointName} 替换为 endpoint1 或 endpoint2，分别禁用 `www.microsoft.com` 或 `docs.microsoft.com` 的目标  。

    ```powershell
    Disable-AzTrafficManagerEndpoint -Name {endpointName} -Type ExternalEndpoints -ProfileName ExternalEndpointExample -ResourceGroupName $resourceGroupName -Force
    ```

1. 在本地 PowerShell 中再次运行步骤 2 中的命令。 这一次你应该获得其他终结点的其他 NameHost。

## <a name="clean-up-resources"></a>清理资源

如果不再需要流量管理器配置文件，请删除资源组。 这会删除流量管理器配置文件和所有相关资源。

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```powershell
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何创建流量管理器配置文件，

若要详细了解如何路由流量，请继续学习流量管理器教程。

> [!div class="nextstepaction"]
> [流量管理器教程](tutorial-traffic-manager-improve-website-response.md)

<!-- Update_Description: update meta properties, wording update, update link -->