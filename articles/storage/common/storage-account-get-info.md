---
title: 使用 .NET 获取存储帐户类型和 SKU 名称
titleSuffix: Azure Storage
description: 了解如何使用 .NET 客户端库获取 Azure 存储帐户类型和 SKU 名称。
services: storage
author: WenJason
ms.author: v-jay
origin.date: 08/06/2019
ms.date: 07/20/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.openlocfilehash: 09e29b5fc27bd03a9c40cabc7952389f5304e253
ms.sourcegitcommit: 31da682a32dbb41c2da3afb80d39c69b9f9c1bc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2020
ms.locfileid: "86414647"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>使用 .NET 获取存储帐户类型和 SKU 名称

本文介绍如何使用[用于 .NET 的 Azure 存储客户端库](https://docs.azure.cn/zh-cn/dotnet/api/overview/storage?view=azure-dotnet)获取 Blob 的 Azure 存储帐户类型和 SKU 名称。

从版本 2018-03-28 开始，服务版本上提供帐户信息。

## <a name="about-account-type-and-sku-name"></a>关于帐户类型和 SKU 名称

**帐户类型**：有效的帐户类型包括 `BlobStorage`、`BlockBlobStorage`、`FileStorage`、`Storage` 和 `StorageV2`。 [Azure 存储帐户概述](storage-account-overview.md)提供详细信息，包括对各种存储帐户的说明。

**SKU 名称**：有效的 SKU 名称包括 `Premium_LRS`、`Standard_GRS`、`Standard_LRS` 和 `Standard_RAGRS`。

## <a name="retrieve-account-information"></a>检索帐户信息

若要获取与 Blob 关联的存储帐户类型和 SKU 名称，请调用 [GetAccountProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) 或 [GetAccountPropertiesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet) 方法。

以下代码示例检索并显示只读帐户属性。

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>后续步骤

了解可以通过 [Azure 门户](https://portal.azure.cn)和 Azure REST API 在存储帐户上执行的其他操作。

- [“获取帐户信息”操作 (REST)](https://docs.microsoft.com/rest/api/storageservices/get-account-information)
