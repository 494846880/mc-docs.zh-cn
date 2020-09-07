---
title: 为 Azure Stack Hub 生成证书签名请求
description: 了解如何在 Azure Stack Hub 集成系统中为 Azure Stack Hub PKI 证书生成证书签名请求。
author: WenJason
ms.topic: article
origin.date: 09/10/2019
ms.date: 08/31/2020
ms.author: v-jay
ms.reviewer: ppacent
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: 49ef23398bf672f396daea07f013cb60b84c0e2a
ms.sourcegitcommit: 4e2d781466e54e228fd1dbb3c0b80a1564c2bf7b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88867961"
---
# <a name="generate-certificate-signing-requests-for-azure-stack-hub"></a>为 Azure Stack Hub 生成证书签名请求

可以使用 Azure Stack Hub 就绪性检查器工具创建适合于 Azure Stack Hub 部署的证书签名请求 (CSR)。 应该花费足够的时间来请求、生成并验证证书，以便在部署之前进行测试。 可以[从 PowerShell 库](https://aka.ms/AzsReadinessChecker)获取工具。

可以使用 Azure Stack Hub 就绪性检查器工具 (AzsReadinessChecker) 请求以下证书：

- **标准证书请求**，根据[为新部署生成证书签名请求](azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-new-deployments)。
- **续订证书请求**，根据[生成证书签名请求以续订证书](azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-certificate-renewal)。
- **平台即服务**：可以请求 [Azure Stack Hub 公钥基础结构证书要求 - 可选的 PaaS 证书](azure-stack-pki-certs.md#optional-paas-certificates)中指定的证书的平台即服务 (PaaS) 名称。

## <a name="prerequisites"></a>必备条件

在为 Azure Stack Hub 部署生成 PKI 证书的任何 CSR 之前，系统应符合以下先决条件：

- Azure Stack Hub 就绪性检查器
- 证书属性：
  - 区域名称
  - 外部完全限定的域名 (FQDN)
  - 主题
- Windows 10 或 Windows Server 2016 或更高版本

  > [!NOTE]  
  > 从证书颁发机构收回证书时，需要在同一个系统上完成[准备 Azure Stack Hub PKI 证书](azure-stack-prepare-pki-certs.md)中的步骤！

## <a name="generate-certificate-signing-requests-for-new-deployments"></a>为新部署生成证书签名请求

使用以下步骤来为新 Azure Stack Hub PKI 证书准备证书签名请求：

1. 在 PowerShell 提示符（5.1 或更高版本）下，运行以下 cmdlet 安装 AzsReadinessChecker：

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. 声明**使用者**。 例如：

    ```powershell  
    $subject = "C=US,ST=Washington,L=Redmond,O=Microsoft,OU=Azure Stack Hub"
    ```

    > [!NOTE]  
    > 如果提供公用名 (CN)，则会在每个证书请求上配置它。 如果省略了 CN，则会在证书请求上配置 Azure Stack Hub 服务的第一个 DNS 名称。

3. 声明已存在的输出目录。 例如：

    ```powershell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. 声明标识系统。

    Azure Active Directory (Azure AD)：

    ```powershell
    $IdentitySystem = "AAD"
    ```

    Active Directory 联合身份验证服务 (AD FS)：

    ```powershell
    $IdentitySystem = "ADFS"
    ```
    > [!NOTE]  
    > 只有 CertificateType 部署需要此参数。

5. 声明用于 Azure Stack Hub 部署的**区域名称**和**外部 FQDN**。

    ```powershell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ```

    > [!NOTE]  
    > `<regionName>.<externalFQDN>` 构成了 Azure Stack Hub 中所有外部 DNS 名称创建位置的基础。 在此示例中，门户将是 `portal.east.azurestack.contoso.com`。  

6. 若要为部署生成证书签名请求，请执行以下命令：

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    若要为其他 Azure Stack Hub 服务生成证书请求，请更改 `-CertificateType` 的值。 例如：

    ```powershell  
    # App Services
    New-AzsHubAppServicesCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # DBAdapter
    New-AzsHubDbAdapterCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # EventHubs
    New-AzsHubEventHubsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # IoTHub
    New-AzsHubIoTHubCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory
    ```

7. 另外，对于开发/测试环境，若要生成具有多个使用者可选名称的单个证书请求，请添加 **-RequestType SingleCSR** 参数和值（**不**建议用于生产环境）：

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -RequestType SingleCSR -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

8.  查看输出：

    ```powershell  
    Starting Certificate Request Process for Deployment
    CSR generating for following SAN(s): *.adminhosting.east.azurestack.contoso.com,*.adminvault.east.azurestack.contoso.com,*.blob.east.azurestack.contoso.com,*.hosting.east.azurestack.contoso.com,*.queue.east.azurestack.contoso.com,*.table.east.azurestack.contoso.com,*.vault.east.azurestack.contoso.com,adminmanagement.east.azurestack.contoso.com,adminportal.east.azurestack.contoso.com,management.east.azurestack.contoso.com,portal.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\[*redacted*]\Documents\AzureStackCSR\Deployment_east_azurestack_contoso_com_SingleCSR_CertRequest_20200710165538.req
    Certreq.exe output: CertReq: Request Created
    ```

9.  将生成的 **.REQ** 文件提交到 CA（内部或公共 CA）。 **New-AzsCertificateSigningRequest** 的输出目录包含提交到证书颁发机构时所需的 CSR。 此目录还包含一个子目录，其中包含生成证书请求期间使用的 INF 文件，供你参考。 请确保 CA 使用符合 [Azure Stack Hub PKI 要求](azure-stack-pki-certs.md)的生成请求来生成证书。

## <a name="generate-certificate-signing-requests-for-certificate-renewal"></a>生成证书签名请求以续订证书

使用以下步骤准备证书签名请求，以便续订现有 Azure Stack Hub PKI 证书：

1. 在 PowerShell 提示符（5.1 或更高版本）下，运行以下 cmdlet 安装 AzsReadinessChecker：

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. 声明 stampEndpoint。 例如：

    ```powershell  
    $stampEndpoint = 'portal.east.azurestack.contoso.com'
    ```

    > [!NOTE]  
    > 上述终结点需要 HTTPS 连接。
    > 上述终结点应与证书类型所需的证书之一匹配，例如，对于部署证书，需要 portal.region.domain 终结点；对于 AppServices，需要 sso.appservices.region.domain 等。绑定到终结点的证书将用于克隆属性，例如主题、密钥长度和签名算法。  只需要一个现有终结点，所有签名请求将创建所有必要的证书。

3. 声明已存在的输出目录。 例如：

    ```powershell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. 若要为部署生成证书签名请求，请执行以下命令：

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory
    ```

    若要为其他 Azure Stack Hub 服务生成证书请求，请使用：

    ```powershell  
    # App Services
    New-AzsHubAppServicesCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory

    # DBAdapter
    New-AzsHubDBAdapterCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory

    # EventHubs
    New-AzsHubEventHubsCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory

    # IoTHub
    New-AzsHubIotHubCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory
    ```

5. 另外，对于开发/测试环境，若要生成具有多个使用者可选名称的单个证书请求，请添加 **-RequestType SingleCSR** 参数和值（**不**建议用于生产环境）：

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -StampEndpoint $stampendpoint -OutputRequestPath $OutputDirectory -RequestType SingleCSR
    ```

6.  查看输出：

    ```powershell  
    Querying StampEndpoint portal.east.azurestack.contoso.com for existing certificate
    Starting Certificate Request Process for Deployment
    CSR generating for following SAN(s): *.adminhosting.east.azurestack.contoso.com,*.adminvault.east.azurestack.contoso.com,*.blob.east.azurestack.contoso.com,*.hosting.east.azurestack.contoso.com,*.queue.east.azurestack.contoso.com,*.table.east.azurestack.contoso.com,*.vault.east.azurestack.contoso.com,adminmanagement.east.azurestack.contoso.com,adminportal.east.azurestack.contoso.com,management.east.azurestack.contoso.com,portal.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\[*redacted*]\Documents\AzureStackCSR\Deployment_east_azurestack_contoso_com_SingleCSR_CertRequest_20200710122723.req
    Certreq.exe output: CertReq: Request Created
    ```

7.  将生成的 **.REQ** 文件提交到 CA（内部或公共 CA）。 **New-AzsCertificateSigningRequest** 的输出目录包含提交到证书颁发机构时所需的 CSR。 此目录还包含一个子目录，其中包含生成证书请求期间使用的 INF 文件，供你参考。 请确保 CA 使用符合 [Azure Stack Hub PKI 要求](azure-stack-pki-certs.md)的生成请求来生成证书。

## <a name="next-steps"></a>后续步骤

[准备 Azure Stack Hub PKI 证书](azure-stack-prepare-pki-certs.md)
