---
title: 订阅移动后更改密钥保管库租户ID - Azure 密钥保管库 | Azure
description: 了解如何在订阅移至不同的租户后切换密钥保管库的租户 ID
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
origin.date: 08/12/2019
ms.date: 07/01/2020
ms.author: v-tawe
ms.openlocfilehash: 2f7a21400fd99c7eaa8658f5faa6238116fd8ef9
ms.sourcegitcommit: 4f84bba7e509a321b6f68a2da475027c539b8fd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2020
ms.locfileid: "85796226"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>订阅移动后更改密钥保管库租户 ID

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


在订阅中创建新的密钥保管库时，该密钥保管库自动绑定到该订阅的默认 Azure Active Directory 租户 ID。 所有访问策略条目也都绑定到此租户 ID。 

如果将 Azure 订阅从租户 A 移到租户 B，租户 B 中的主体（用户和应用程序）无法访问现有的密钥保管库。要解决此问题，需要执行以下操作：

* 将与订阅中所有现有密钥保管库关联的租户 ID 更改到租户 B。
* 删除所有现有的访问策略条目。
* 添加与租户 B 关联的新访问策略条目。

例如，如果你在已从租户 A 移到租户 B 的订阅中有密钥保管库“myvault”，则可以使用 Azure PowerShell 更改租户 ID 并删除旧的访问策略。

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.
````

也可以使用 Azure CLI。

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
$tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

既然保管库已与正确的租户 ID 关联，并且旧的访问策略条目已删除，请使用 Azure PowerShell [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) cmdlet 或 Azure CLI [az keyvault set-policy](/cli/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令设置新的访问策略条目。

如果使用 Azure 资源的托管标识，则还需要将其更新为新的 Azure AD 租户。 有关托管标识的详细信息，请参阅[使用托管标识提供 Key Vault 身份验证](managed-identity.md)。


如果使用的是 MSI，则还必须更新 MSI 标识，因为旧标识将不再位于相应的 AAD 租户中。

## <a name="next-steps"></a>后续步骤

如果在 Azure Key Vault 方面有任何问题，请访问 [Azure Key Vault 论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)。
