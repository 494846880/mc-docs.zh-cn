---
title: 创建 Azure DNS 区域和记录 - Azure 资源管理器模板（ARM 模板）
titleSuffix: Azure DNS
description: 了解如何在 Azure DNS 中创建 DNS 区域和记录。 这是有关使用 Azure 资源管理器模板（ARM 模板）创建和管理第一个 DNS 区域和记录的分步快速入门。
services: dns
author: WenJason
ms.service: dns
ms.topic: quickstart
origin.date: 09/8/2020
ms.date: 11/16/2020
ms.author: v-jay
ms.custom: subject-armqs
ms.openlocfilehash: 09fba5d1ba1d1c41eedd7618deb814167ebabf2b
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96432510"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-an-arm-template"></a>使用 ARM 模板创建 Azure DNS 区域和记录

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）来创建 DNS 区域以及其中的 `A` 记录。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-dns-new-zone%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-azure-dns-new-zone)。

本快速入门将使用后缀 `azurequickstart.org` 创建唯一的 DNS 区域。 指向两个 IP 地址的 `A` 记录也将放置在该区域中。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newZoneName": {
      "type": "string",
      "defaultValue": "[concat(uniqueString(resourceGroup().id), '.azurequickstart.org')]",
      "metadata": {
        "description": "The name of the DNS zone to be created.  Must have at least 2 segements, e.g. hostname.org"
      }
    },
    "newRecordName": {
      "type": "string",
      "defaultValue": "www",
      "metadata": {
        "description": "The name of the DNS record to be created.  The name is relative to the zone, not the FQDN."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Network/dnsZones",
      "apiVersion": "2018-05-01",
      "name": "[parameters('newZoneName')]",
      "location": "global"
    },
    {
      "type": "Microsoft.Network/dnsZones/A",
      "apiVersion": "2018-05-01",
      "name": "[concat(parameters('newZoneName'), '/', parameters('newRecordName'))]",
      "location": "global",
      "dependsOn": [
        "[parameters('newZoneName')]"
      ],
      "properties": {
        "TTL": 3600,
        "ARecords": [
          {
            "ipv4Address": "1.2.3.4"
          },
          {
            "ipv4Address": "1.2.3.5"
          }
        ]
      }
    }
  ],
  "outputs": {
    "nameServers": {
      "type": "array",
      "value": "[reference(parameters('newZoneName')).nameServers]"
    }
  }
}
```

该模板中已定义了两个 Azure 资源：

- [**Microsoft.Network/dnsZones**](https://docs.microsoft.com/azure/templates/microsoft.network/dnsZones)
- [**Microsoft.Network/dnsZones/A**](https://docs.microsoft.com/azure/templates/microsoft.network/dnsZones/A)：用于在区域中创建 `A` 记录。

## <a name="deploy-the-template"></a>部署模板

1. 按照说明登录到 Azure。 

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. chinaeast2)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-dns-new-zone/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    等到控制台中显示提示。

1. 从上一个代码块中选择“复制”，以复制 PowerShell 脚本。

1. 右键单击 shell 控制台窗格，然后选择“粘贴”。

1. 输入相应的值。

    模板部署使用指向两个 IP 地址的 `A` 记录创建一个区域。 资源组名称是追加了 **rg** 的项目名称。

    部署模板需要几秒钟时间。 完成后，输出类似于：

    :::image type="content" source="./media/dns-getstarted-template/create-dns-zone-powershell-output.png" alt-text="Azure DNS 区域资源管理器模板 PowerShell 部署输出":::

使用 Azure PowerShell 部署模板。 除了 Azure PowerShell，还可以使用 Azure 门户、Azure CLI 和 REST API。 若要了解其他部署方法，请参阅[部署模板](../azure-resource-manager/templates/deploy-portal.md)。

## <a name="validate-the-deployment"></a>验证部署

1. 登录 [Azure 门户](https://portal.azure.cn)。

1. 从左侧窗格中选择“资源组”。

1. 选择你在上一部分中创建的资源组。 默认资源组名称是追加了 **rg** 的项目名称。

1. 资源组应包含以下资源：

    :::image type="content" source="./media/dns-getstarted-template/resource-group-dns-zone.png" alt-text="DNS 区域部署资源组":::

1. 选择带有后缀 `azurequickstart.org` 的 DNS 区域，验证该区域是否使用引用值 `1.2.3.4` 和 `1.2.3.5` 的 `A` 记录正确创建。

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-overview.png" alt-text="DNS 区域部署":::

1. 复制上一步中其中一个名称服务器名称。

1. 打开一个命令提示符，并运行以下命令：

   ```cmd
   nslookup www.<dns zone name> <name server name>
   ```

   例如： 。

   ```cmd
   nslookup www.2lwynbseszpam.azurequickstart.org ns1-01.azure-dns.cn.
   ```

   应会看到类似以下屏幕截图的内容：

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-validation.png" alt-text="DNS 区域 nslookup":::

主机名 `www.2lwynbseszpam.azurequickstart.org` 解析为 `1.2.3.4` 和 `1.2.3.5`，正如你配置的那样。 此结果表明名称解析正常工作。

## <a name="clean-up-resources"></a>清理资源

当不再需要使用 DNS 区域创建的资源时，请删除资源组。 这会删除该 DNS 区域和所有相关资源。

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```azurepowershell
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们创建了：

- DNS 区域
- `A` 记录

使用 ARM 模板创建第一个 DNS 区域和记录之后，接下来即可在自定义域中为 Web 应用创建记录。

> [!div class="nextstepaction"]
> [在自定义域中为 web 应用创建 DNS 记录](./dns-web-sites-custom-domain.md)
