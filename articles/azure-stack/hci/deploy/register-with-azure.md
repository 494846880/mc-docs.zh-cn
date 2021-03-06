---
title: 将 Azure Stack HCI 连接到 Azure
description: 如何使用 Azure 注册 Azure Stack HCI。
author: WenJason
ms.author: v-jay
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
origin.date: 11/23/2020
ms.date: 12/07/2020
ms.openlocfilehash: 914551c2f0d8ca692c68e5ed12ee217adb8715f2
ms.sourcegitcommit: a1f565fd202c1b9fd8c74f814baa499bbb4ed4a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96508073"
---
# <a name="connect-azure-stack-hci-to-azure"></a>将 Azure Stack HCI 连接到 Azure

> 适用于：Azure Stack HCI v20H2

根据 Azure 在线服务条款，Azure Stack HCI 作为 Azure 服务提供，需要在安装后 30 天内进行注册。 本主题介绍如何向 [Azure Arc](https://azure.microsoft.com/services/azure-arc/) 注册 Azure Stack HCI 群集，以实现监视、支持、计费和混合服务。 注册后，将创建一个 Azure 资源管理器资源来表示每个本地 Azure Stack HCI 群集，从而有效地将 Azure 管理平面扩展到 Azure Stack HCI。 信息会定期在 Azure 资源和本地群集之间进行同步。 

## <a name="prerequisites-for-registration"></a>注册的先决条件

在创建 Azure Stack HCI 群集之前，将无法向 Azure 注册。 节点可以是物理计算机或虚拟机，但是它们必须具有统一可扩展固件接口 (UEFI)，这意味着不能使用 Hyper-V 第 1 代虚拟机。 Azure Arc 注册是 Azure Stack HCI 的本机功能，因此不需要代理。

### <a name="internet-access"></a>Internet 访问权限

Azure Stack HCI 节点需要连接到云才能连接到 Azure。 例如，出站 ping 应成功：

```PowerShell
C:\> ping bing.com
```

### <a name="azure-subscription"></a>Azure 订阅

如果还没有 Azure 帐户，请[创建一个试用订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。 

可以使用任何类型的现有订阅：
- [提前支付](https://www.azure.cn/offers/ms-mc-arz-33p/)订阅
- 通过企业协议 (EA) 获取的订阅
- 通过云解决方案提供商 (CSP) 计划获取的订阅

### <a name="azure-active-directory-permissions"></a>Azure Active Directory 权限

需要具有 Azure Active Directory 权限才能完成注册过程。 如果还没有这些权限，请让 Azure AD 管理员向你授予或委托这些权限。 有关详细信息，请参阅[管理 Azure 注册](../manage/manage-azure-registration.md#azure-active-directory-permissions)。

## <a name="register-using-powershell"></a>使用 PowerShell 注册

使用以下过程在 Azure 中注册 Azure Stack HCI 群集：

1. 连接到群集节点之一，具体方式为打开 PowerShell 会话并输入以下命令：

   ```PowerShell
   Enter-PSSession <server-name>
   ```

2. 安装适用于 Azure Stack HCI 的 PowerShell 模块：

   ```PowerShell
   Install-WindowsFeature RSAT-Azure-Stack-HCI
   ```

3. 安装所需的 cmdlet。 若要从公共预览版映像部署 Azure Stack HCI，需要使用 Az.StackHCI PowerShell 模块版本 0.3.1：

   ```PowerShell
   Install-Module -Name Az.StackHCI -RequiredVersion 0.3.1
   ```

   如果你已在群集中的每台服务器上安装了 [2020 年 11 月 23 日预览版更新 (KB4586852)](../release-notes.md)，现在正要在 Azure 中注册群集，则可以安全地使用最新版 Az.StackHCI：

   ```PowerShell
   Install-Module -Name Az.StackHCI
   ```

   > [!NOTE]
   > 1. 你可能会看到一条提示，例如“是否希望 PowerShellGet 立即安装并导入 NuGet 提供程序?”， 你应该回答“是(Y)”。
   > 2. 系统可能还会提示“是否确定要从 'PSGallery' 安装模块?”，你应该回答“是(Y)”。
   > 3. 最后，你可能会假定安装整个 Az 模块将包括 StackHCI 子模块，但情况并非是这样 。 根据标准 Azure PowerShell 约定，不会自动包括预览版中的子模块，因此你需要显式请求 Az.StackHCI，如上所示。

4. 执行实际注册：

   ```PowerShell
   Register-AzStackHCI  -SubscriptionId "<subscription_ID>" [-ResourceName] [-ResourceGroupName]
   ```

   如果使用了此语法，则会以当前用户的身份在默认的 Azure 区域和云环境中使用 Azure 资源和资源组的智能默认名称注册本地群集（本地服务器是该群集的成员），不过你可以根据需要为此命令添加参数来指定这些值。

   请记住，运行 `Register-AzStackHCI` cmdlet 的用户必须具有 [Azure Active Directory 权限](../manage/manage-azure-registration.md#azure-active-directory-permissions)，否则注册过程将不会完成；相反，它将退出并使注册挂起以等待管理员同意。 授权后，只需重新运行 `Register-AzStackHCI` 即可完成注册。

5. 使用 Azure 进行身份验证

   若要完成注册过程，需要使用 Azure 帐户进行身份验证（登录）。 帐户需要有权访问在上述第 4 步中指定的 Azure 订阅才能继续进行注册。 复制所提供的代码，在另一台设备（如电脑或手机）上导航到 microsoft.com/devicelogin，输入该代码，然后在那里登录。 这与 Microsoft 用于输入方式受限的其他设备（例如 Xbox）的体验是相同的。

注册工作流将检测到你的登录并继续完成注册。 然后，你应该能够在 Azure 门户中看到你的群集。

## <a name="next-steps"></a>后续步骤

现在可以执行以下操作：

- [验证群集](validate.md)
- [创建卷](../manage/create-volumes.md)
