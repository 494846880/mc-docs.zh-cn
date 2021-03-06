---
title: 快速入门 - 适用于 .NET 的 Azure Key Vault 证书客户端库（版本 4）
description: 了解如何使用 .NET 客户端库（版本 4）在 Azure Key Vault 中创建、检索和删除证书
author: msmbaldwin
ms.author: v-tawe
origin.date: 09/23/2020
ms.date: 12/11/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: fecc69a37816cb4750f524186e454f7cc21309eb
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105435"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-net-sdk-v4"></a>快速入门：适用于 .NET 的 Azure Key Vault 证书客户端库 (SDK v4)

适用于 .NET 的 Azure Key Vault 证书客户端库入门。 [Azure Key Vault](../general/overview.md) 是一项云服务，它为证书提供了安全的存储。 可以安全地存储密钥、密码、证书和其他证书。 可以通过 Azure 门户创建和管理 Azure Key Vault。 本快速入门介绍如何使用 .NET 客户端库在 Azure 密钥保管库中创建、检索和删除证书

Key Vault 客户端库资源：

[API 参考文档](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.certificates) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [包 (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)

有关 Key Vault 和证书的详细信息，请参阅：
- [Key Vault 概述](../general/overview.md)
- [证书概述](about-certificates.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)
* [.NET Core 3.1 SDK 或更高版本](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/install-azure-cli)
* Key Vault - 可以使用 [Azure 门户](../general/quick-create-portal.md)、[Azure CLI](../general/quick-create-cli.md) 或 [Azure PowerShell](../general/quick-create-powershell.md) 进行创建。

本快速入门使用 `dotnet` 和 Azure CLI

## <a name="setup"></a>设置

本快速入门结合使用 Azure Identity 库和 Azure CLI，向 Azure 服务验证用户身份。 开发人员还可以使用 Visual Studio 或 Visual Studio Code 来验证其调用。有关详细信息，请参阅[使用 Azure Identity 客户端库对客户端进行身份验证](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true)。

### <a name="sign-in-to-azure"></a>登录 Azure

1. 运行 `login` 命令。

    ```azurecli
    az cloud set -n AzureChinaCloud
    az login
    ```

    如果 CLI 可以打开默认浏览器，它将这样做并加载 Azure 登录页。

    否则，请在 [https://login.partner.microsoftonline.cn/common/oauth2/deviceauth](https://login.partner.microsoftonline.cn/common/oauth2/deviceauth) 处打开浏览器页，然后输入终端中显示的授权代码。

2. 在浏览器中使用帐户凭据登录。

### <a name="create-new-net-console-app"></a>创建新的 .NET 控制台应用

1. 在命令外壳中，运行以下命令以创建名为 `key-vault-console-app` 的项目：

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. 切换到新创建的 key-vault-console-app 目录，然后运行以下命令来生成项目：

    ```dotnetcli
    dotnet build
    ```

    生成输出不应包含警告或错误。
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>安装包

在命令外壳中，安装适用于 .NET 的 Azure Key Vault 证书客户端库：

```dotnetcli
dotnet add package Azure.Security.KeyVault.Certificates
```

对于本快速入门，还需要安装适用于 Azure Identity 的 Azure SDK 客户端库：

```dotnetcli
dotnet add package Azure.Identity
```

#### <a name="grant-access-to-your-key-vault"></a>授予对 Key Vault 的访问权限

针对密钥保管库创建一个访问策略，以便为用户帐户授予证书权限

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --certificate-permissions delete get list create purge
```

#### <a name="set-environment-variables"></a>设置环境变量

此应用程序使用 Key Vault 名称作为名为 `KEY_VAULT_NAME` 的环境变量。

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS 或 Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>对象模型

使用适用于 .NET 的 Azure Key Vault 证书客户端库管理证书。 [代码示例](#code-examples)部分介绍了如何创建客户端，以及如何设置、检索和删除证书。

## <a name="code-examples"></a>代码示例

### <a name="add-directives"></a>添加指令

在 Program.cs 的顶部添加以下指令：

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Certificates;
```

### <a name="authenticate-and-create-a-client"></a>进行身份验证并创建客户端

本快速入门使用登录用户向 Key Vault 进行身份验证，这是本地开发的首选方法。 对于部署到 Azure 的应用程序，应将托管标识分配给应用服务或虚拟机。有关详细信息，请参阅[托管标识概述](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

在下面的示例中，密钥保管库的名称将扩展为密钥保管库 URI，格式为“https://\<your-key-vault-name\>.vault.azure.cn”。 此示例使用 ['DefaultAzureCredential()' ](https://docs.microsoft.com/dotnet/api/azure.identity.defaultazurecredential)类，该类允许在具有不同选项的不同环境中使用相同代码提供标识。 有关向密钥保管库进行身份验证的详细信息，请参阅[开发人员指南](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)。

```csharp
string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.cn";

var client = new CertificateClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-certificate"></a>保存证书

在此示例中，为简单起见，可以将自签名证书与默认颁发策略一起使用。 对于此任务，请使用 [StartCreateCertificateAsync](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.certificates.certificateclient.startcreatecertificateasync) 方法。 该方法的首个参数接受证书名和证书策略[证书策略](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.certificates.certificatepolicy)。

```csharp
CertificateOperation operation = await client.StartCreateCertificateAsync("MyCertificate", CertificatePolicy.Default);
var certificate = await operation.WaitForCompletionAsync();
```

> [!NOTE]
> 如果证书名已存在，则上面的代码将创建该证书的新版本。

### <a name="retrieve-a-certificate"></a>检索证书

现在，可以使用 [GetCertificateAsync](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.certificates.certificateclient.getcertificateasync) 方法检索以前创建的证书。

```csharp
var certificate = await client.GetCertificateAsync("myCertificate");
```

### <a name="delete-a-certificate"></a>删除证书

最后，让我们使用 [StartDeleteCertificateAsync](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.certificates.certificateclient.startdeletecertificateasync) 和 [PurgeDeletedCertificateAsync](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.certificates.certificateclient.purgedeletedcertificateasync) 方法从密钥保管库中删除并清除证书。

```csharp
var operation = await client.StartDeleteCertificateAsync("MyCertificate");

// You only need to wait for completion if you want to purge or recover the certificate.
await operation.WaitForCompletionAsync();

var certificate = operation.Value;
await client.PurgeDeletedCertificateAsync(certificate.Name);
```

## <a name="sample-code"></a>示例代码

通过完成以下步骤，将 .NET Core 控制台应用修改为与 Key Vault 交互：

1. 将 Program.cs 中的代码替换为以下代码：

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Certificates;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string certificateName = "myCertificate";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.cn";
    
                var client = new CertificateClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write($"Creating a certificate in {keyVaultName} called '{certificateName}' ...");
                CertificateOperation operation = await client.StartCreateCertificateAsync(certificateName, CertificatePolicy.Default);
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
    
                Console.WriteLine($"Retrieving your certificate from {keyVaultName}.");
                var certificate = await client.GetCertificateAsync(certificateName);
                Console.WriteLine($"Your certificate value is '{certificate.Value.Properties.Version}'.");
    
                Console.Write($"Deleting your certificate from {keyVaultName} ...");
                DeleteCertificateOperation deleteOperation = await client.StartDeleteCertificateAsync(certificateName);
                // You only need to wait for completion if you want to purge or recover the certificate.
                await deleteOperation.WaitForCompletionAsync();
                Console.WriteLine(" done.");

                Console.Write($"Purging your certificate from {keyVaultName} ...");
                await client.PurgeDeletedCertificateAsync(certificateName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>测试和验证

1.  执行以下命令以生成项目

    ```dotnetcli
    dotnet build
    ```

1. 执行以下命令来运行应用。

    ```dotnetcli
    dotnet run
    ```

1. 出现提示时，输入一个密码值。 例如，mySecretPassword。

    随即显示以下输出的变体：

    ```console
    Creating a certificate in mykeyvault called 'myCertificate' ... done.
    Retrieving your certificate from mykeyvault.
    Your certificate version is '8532359bced24e4bb2525f2d2050738a'.
    Deleting your certificate from jl-kv ... done
    ```

## <a name="clean-up-resources"></a>清理资源

可以使用 Azure CLI 或 Azure PowerShell 来删除不再需要的 Key Vault 和相应的资源组。

### <a name="delete-a-key-vault"></a>删除 Key Vault

```azurecli
az keyvault delete --name <your-unique-keyvault-name>
```

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name>
```

### <a name="purge-a-key-vault"></a>清除 Key Vault

```azurecli
az keyvault purge --location eastus --name <your-unique-keyvault-name>
```

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name> -InRemovedState -Location eastus
```

### <a name="delete-a-resource-group"></a>删除资源组

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个密钥保管库，存储了一个证书，然后检索了该证书。 

若要详细了解 Key Vault 以及如何将其与应用集成，请参阅以下文章：

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 阅读[证书概述](about-certificates.md)
- 请参阅[从应用服务应用程序访问 Key Vault 的教程](../general/tutorial-net-create-vault-azure-web-app.md)
- 请参阅[从虚拟机访问 Key Vault 的教程](../general/tutorial-net-virtual-machine.md)
- 参阅 [Azure Key Vault 开发人员指南](../general/developers-guide.md)
- 查看 [Azure Key Vault 最佳做法](../general/best-practices.md)