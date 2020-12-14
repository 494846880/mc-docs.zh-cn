---
title: 在 .NET 中创建和管理 blob 快照
titleSuffix: Azure Storage
description: 了解如何使用 .NET 客户端库在指定时刻及时创建 blob 的只读快照以备份 blob 数据。
services: storage
author: WenJason
ms.service: storage
ms.topic: how-to
origin.date: 08/27/2020
ms.date: 12/14/2020
ms.author: v-jay
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 48ffd70d20f86048161933de37eeedacc5360c89
ms.sourcegitcommit: a8afac9982deafcf0652c63fe1615ba0ef1877be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96850773"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>在 .NET 中创建和管理 blob 快照

快照是在某一时间点拍摄的只读版本的 Blob。 本文介绍如何使用[适用于 .NET 的 Azure 存储客户端库](/dotnet/api/overview/storage)创建和管理 blob 快照。

有关 Azure 存储中 blob 快照的详细信息，请参阅 [Blob 快照](snapshots-overview.md)。

## <a name="create-a-snapshot"></a>创建快照

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

要使用 12.x 版用于 .NET 的 Azure 存储客户端库创建块 blob 快照，请使用以下任一方法：

- [CreateSnapshot](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshot)
- [CreateSnapshotAsync](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshotasync)

以下代码示例演示如何在版本 12.x 中创建快照。 包含对 [Azure.Identity](https://www.nuget.org/packages/azure.identity) 库的引用，以使用 Azure AD 凭据授权对服务的请求。

```csharp
private static async Task CreateBlockBlobSnapshot(string accountName, string containerName, string blobName, Stream data)
{
    const string blobServiceEndpointSuffix = ".blob.core.chinacloudapi.cn";
    Uri containerUri = new Uri("https://" + accountName + blobServiceEndpointSuffix + "/" + containerName);

    // Get a container client object and create the container.
    BlobContainerClient containerClient = new BlobContainerClient(containerUri,
        new DefaultAzureCredential());
    await containerClient.CreateIfNotExistsAsync();

    // Get a blob client object.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Upload text to create a block blob.
        await blobClient.UploadAsync(data);

        // Add blob metadata.
        IDictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "ApproxBlobCreatedDate", DateTime.UtcNow.ToString() },
            { "FileType", "text" }
        };
        await blobClient.SetMetadataAsync(metadata);

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        await blobClient.CreateSnapshotAsync();
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

要使用 11.x 版用于 .NET 的 Azure 存储客户端库创建块 blob 快照，请使用以下任一方法：

- [CreateSnapshot](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

以下代码示例演示如何在版本 11.x 中创建快照。 该示例在创建快照时为其指定了额外的元数据。

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        Console.WriteLine(snapshot.SnapshotQualifiedStorageUri.PrimaryUri);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

---

## <a name="delete-snapshots"></a>删除快照

若要删除 blob，必须先删除该 blob 的所有快照。 可以单独删除快照，或指定在删除源 Blob 时删除所有快照。 如果尝试删除仍包含快照的 Blob，会发生错误。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

要使用 12.x 版用于 .NET 的 Azure 存储客户端库删除 blob 及其快照，请使用以下任一方法，并包括 [DeleteSnapshotsOption](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.models.deletesnapshotsoption) 枚举：

- [删除](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.delete)
- [DeleteAsync](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteasync)
- [DeleteIfExists](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexists)
- [DeleteIfExistsAsync](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexistsasync)

以下代码示例演示如何在 .NET 中删除 blob 及其快照，其中 `blobClient` 是 [BlobClient](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobclient) 类型的对象：

```csharp
await blobClient.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, default);
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

要使用 11.x 版用于 .NET 的 Azure 存储客户端库删除 blob 及其快照，请使用以下任一 blob 删除方法，并包括 [DeleteSnapshotsOption](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption) 枚举：

- [删除](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

以下代码示例演示如何在 .NET 中删除 blob 及其快照，其中 `blockBlob` 是 [CloudBlockBlob][dotnet_CloudBlockBlob] 类型的对象：

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

---

## <a name="next-steps"></a>后续步骤

- [blob 快照](snapshots-overview.md)
- [blob 的软删除](./soft-delete-blob-overview.md)