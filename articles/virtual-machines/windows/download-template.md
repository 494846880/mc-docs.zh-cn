---
title: 下载 Azure VM 模板
description: 使用门户或 PowerShell 下载 VM 的模板。
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
origin.date: 11/17/2017
author: rockboyfor
ms.date: 09/07/2020
ms.testscope: yes
ms.testdate: 08/31/2020
ms.author: v-yeche
ms.openlocfilehash: eb9f276eb9e4be61ad203f12ebb109f2340ca685
ms.sourcegitcommit: 22e1da9309795e74a91b7241ac5987a802231a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89463121"
---
# <a name="download-the-template-for-a-vm"></a>下载 VM 模板
使用门户或 PowerShell 在 Azure 中创建 VM 时，系统会自动创建一个 Resource Manager 模板。 可以使用此模板快速复制部署。 该模板包含有关资源组中所有资源的信息。 对于虚拟机而言，这意味着该模板包含为在该资源组中支持该 VM 而创建的所有资源，包括网络资源。

## <a name="download-the-template-using-the-portal"></a>使用门户下载模板
1. 登录到 [Azure 门户](https://portal.azure.cn/)。
2. 在左侧菜单中，选择“虚拟机”。
3. 从列表中选择虚拟机。
4. 选择“导出模板”。
5. 从顶部菜单中选择“下载”，将 .zip 文件保存到本地计算机。
6. 打开该 .zip 文件，将文件解压缩到某个文件夹。 该 .zip 文件包含：

    * parameters.json
    * template.json

template.json 文件是模板。

## <a name="download-the-template-using-powershell"></a>使用 PowerShell 下载模板
也可以使用 [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) cmdlet 下载 .json 模板文件。 可以使用 `-path` 参数提供 .json 文件的文件名和路径。 本示例演示如何将名为 **myResourceGroup** 的资源组的模板下载到本地计算机上的 **C:\users\public\downloads** 文件夹。

```powershell
Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>后续步骤
若要详细了解如何使用模板部署资源，请参阅 [Resource Manager 模板演练](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)。

<!-- Update_Description: update meta properties, wording update, update link -->