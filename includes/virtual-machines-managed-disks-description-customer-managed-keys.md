---
title: 包含文件
description: 包含文件
services: virtual-machines
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 06/05/2020
ms.date: 08/10/2020
ms.testscope: yes|no
ms.testdate: 08/10/2020null
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: cca1216cb0a6ba31ad558ce5d22a9fa2defeacec
ms.sourcegitcommit: b5ea35dcd86ff81a003ac9a7a2c6f373204d111d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88946900"
---
<!--Verified successfully-->
<!--Only Charactors Content from the verified articles-->

可以选择使用自己的密钥在每个托管磁盘的级别管理加密。 使用客户托管密钥对托管磁盘进行服务器端加密提供了与 Azure Key Vault 的集成体验。

<!--Not Available on  You can either import your RSA keys to your Key Vault or generate new RSA keys in Azure Key Vault.-->
<!--Not Available on  [your RSA keys](../../key-vault/keys/hsm-protected-keys.md)-->

Azure 托管磁盘使用[信封加密](../articles/storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)以完全透明的方式处理加密和解密。 它使用基于 AES 256 的数据加密密钥 (DEK) 对数据进行加密，DEK 反过来使用你的密钥进行保护。 存储服务生成数据加密密钥，并使用 RSA 加密通过客户托管密钥对其进行加密。 通过信封加密，可以根据合规性策略定期轮替（更改）密钥，而不会影响 VM。 轮替密钥时，存储服务会使用新的客户托管密钥对数据加密密钥进行重新加密。 

<!--Not Available on [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)-->

#### <a name="full-control-of-your-keys"></a>完全控制密钥

必须授予对 Key Vault 中的托管磁盘的访问权限，才能使用你的密钥来加密和解密 DEK。 这允许你完全控制数据和密钥。 可以随时禁用密钥或撤销对托管磁盘的访问权限。 还可以通过 Azure Key Vault 监视来审核加密密钥用法，以确保仅托管磁盘或其他受信任的 Azure 服务访问你的密钥。

禁用或删除密钥后，包含使用该密钥的磁盘的任何 VM 都会自动关闭。 之后，VM 将无法使用，除非再次启用密钥或分配新密钥。

对于高级 SSD、标准 SSD 和标准 HDD：禁用或删除密钥后，包含使用该密钥的磁盘的任何 VM 都会自动关闭。 之后，VM 将无法使用，除非再次启用密钥或分配新密钥。    

<!--Not Available on For ultra disks, when you disable or delete a key, any VMs with ultra disks using the key won't automatically shut down. Once you deallocate and restart the VMs then the disks will stop using the key and then VMs won't come back online. To bring the VMs back online, you must assign a new key or enable the existing key.-->

下图显示了托管磁盘如何借助 Azure Active Directory 和 Azure Key Vault 使用客户托管密钥发出请求：

:::image type="content" source="media/virtual-machines-managed-disks-description-customer-managed-keys/customer-managed-keys-sse-managed-disks-workflow.png" alt-text="托管磁盘和客户管理的密钥工作流。管理员创建 Azure Key Vault，然后创建并设置磁盘加密集。该集与 VM 关联，这允许磁盘使用 Azure AD 进行身份验证":::

下表更详细地介绍了该图：

1. Azure Key Vault 管理员创建密钥保管库资源。
1. 密钥保管库管理员可以将 RSA 密钥导入 Key Vault，也可以在 Key Vault 中生成新的 RSA 密钥。
1. 该管理员创建磁盘加密集资源的实例，指定 Azure Key Vault ID 和密钥 URL。 磁盘加密集是为了简化托管磁盘的密钥管理而引入的新资源。 
1. 创建磁盘加密集时，将在 Azure Active Directory (AD) 中创建[系统分配的托管标识](../articles/active-directory/managed-identities-azure-resources/overview.md)，并将其与磁盘加密集相关联。 
1. 然后，Azure Key Vault 管理员授予托管标识权限，以在密钥保管库中执行操作。
1. VM 用户可以通过将磁盘与磁盘加密集相关联来创建磁盘。 VM 用户还可以通过将现有资源的客户托管密钥与磁盘加密集相关联来启用客户托管密钥的服务器端加密。 
1. 托管磁盘使用托管标识将请求发送到 Azure Key Vault。
1. 若要读取或写入数据，托管磁盘会将请求发送到 Azure Key Vault 以加密（包装）和解密（解包）数据加密密钥，以便执行数据的加密和解密。 

若要撤销对客户托管密钥的访问权限，请参阅 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) 和 [Azure Key Vault CLI](https://docs.azure.cn/cli/keyvault?view=azure-cli-latest)。 撤销访问权限会实际阻止对存储帐户中所有数据的访问权限，因为 Azure 存储无法访问加密密钥。

<!-- Update_Description: new article about virtual machines managed disks description customer managed keys -->
<!--NEW.date: 08/10/2020-->
