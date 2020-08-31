---
title: 用于渲染的存储和数据移动
description: 了解用于渲染资产和输出文件工作负荷的各种存储与数据移动选项。
services: batch
ms.service: batch
author: rockboyfor
origin.date: 08/02/2018
ms.date: 08/24/2020
ms.testscope: no
ms.testdate: 08/02/2019
ms.author: v-yeche
ms.topic: how-to
ms.openlocfilehash: 941646666ffa0c7745d992e2c054f8240cc1c682
ms.sourcegitcommit: e633c458126612223fbf7a8853dbf19acc7f0fa5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654967"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>用于渲染资产和输出文件的存储与数据移动选项

有多个选项可以创建场景和资产文件用于渲染池 VM 上的应用程序：

* [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)：
    * 将场景和资产文件从本地文件系统上传到 Blob 存储。 当应用程序由某个任务运行时，会将所需的文件从 Blob 存储复制到 VM，使渲染应用程序能够访问它们。 输出文件由渲染应用程序写入 VM 磁盘，然后复制到 Blob 存储。  如果需要，可将输出文件从 Blob 存储下载到本地文件系统。
    * Azure blob 存储是简单且经济高效的选项，适合小型项目。  由于每个池 VM 上需要所有资产文件，一旦资产文件的数量和大小增加，就需要谨慎确保文件传输尽量高效。  
    
    <!--Not Available on [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)-->
    
* 文件系统或文件共享：
    * 根据 VM 操作系统和性能/规模要求，选项包括 [Azure 文件](/storage/files/storage-files-introduction)、使用附有 NFS 磁盘的 VM、使用附有分布式文件系统（例如 GlusterFS）磁盘的多个 VM，或使用第三方产品/服务。
    * [Avere Systems](https://www.averesystems.com/) 已由 Microsoft 收购，在不久的将来，将会推出理想的解决方案进行大规模的高性能渲染。  借助 Avere 解决方案能够创建可与 Blob 存储或本地 NAS 设备配合工作的基于 Azure 的 NFS 或 SMB 缓存。
        
        <!--CORRECT ON Microsoft-->
    
    * 使用文件系统可以直接在文件系统中读取或写入文件，或者在文件系统与池 VM 之间复制文件。
    * 使用共享文件系统可以在要使用的项目与作业之间共享大量的资产，而渲染任务只需访问所需的内容。

## <a name="using-azure-blob-storage"></a>使用 Azure Blob 存储

应使用 Blob 存储帐户或常规用途 v2 存储帐户。  根据[此博客文章](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)中所述，为这两种类型的存储帐户配置的限制可以明显高于常规用途 v1 存储帐户。  配置后，更高的限制将会提供更好的性能和可伸缩性，尤其是有多个池 VM 访问存储帐户时。

### <a name="copying-files-between-client-and-blob-storage"></a>在客户端与 Blob 存储之间复制文件

<!--MOONCAKE: SCORECARD the azcopy command-line-->

若要向/从 Azure 存储复制文件，可以使用各种机制，包括存储 Blob API、[Azure 存储数据移动库](https://github.com/Azure/azure-storage-net-data-movement)、适用于 [Windows](../storage/common/storage-use-azcopy-v10.md) 或 [Linux](../storage/common/storage-use-azcopy-v10.md) 的 azcopy 命令行工具、[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)和 [Azure Batch Explorer](https://azure.github.io/BatchExplorer/)。

<!--MOONCAKE: SCORECARD the azcopy command-line-->

例如，使用 azcopy 可按如下所示转移文件夹中的所有资产：

`azcopy /source:. /dest:https://account.blob.core.chinacloudapi.cn/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

如果只想复制已修改的文件，可以使用 /XO 参数：

`azcopy /source:. /dest:https://account.blob.core.chinacloudapi.cn/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>将输入资产文件从 Blob 存储复制到 Batch 池 VM

可通过多种不同的方法复制文件，最佳的方法取决于作业资产的大小。
最简单的方法是将每个作业的所有资产文件复制到池 VM：

* 如果有些文件是某个作业独有的，但作业的所有任务都需要这些文件，则可以指定[作业准备任务](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)来复制所有文件。  当第一个作业任务已在 VM 上执行，但未针对后续作业任务再次运行时，将运行作业准备任务一次。
* 应指定一个[作业释放任务](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)，以便在完成作业后删除每个作业的文件；这可以避免所有作业资产文件占满 VM 磁盘。
* 如果有多个作业使用相同的资产，而每个作业的资产只是发生了增量更改，则仍会复制所有资产文件，即使只是更新了一部分文件。  存在大量的大型资产文件时，这种方法比较低效。

如果在作业之间重复使用资产文件，而作业之间只是发生了增量更改，则更有效但略微繁琐的方法是在 VM 上的共享文件夹中存储资产，并同步已更改的文件。

* 作业准备任务会结合 /XO 参数，使用 azcopy 向 AZ_BATCH_NODE_SHARED_DIR 环境变量指定的 VM 共享文件夹执行复制。  这只会将已更改的文件复制到每个 VM。
* 必须考虑所有资产的大小，以确保可将其放在池 VM 的临时驱动器上。

Azure Batch 内置支持在存储帐户与 Batch 池 VM 之间复制文件。  任务[资源文件](https://docs.microsoft.com/rest/api/batchservice/job/add#resourcefile)将文件从存储复制到池 VM。可为作业准备任务指定这些资源文件。  遗憾的是，如果存在数百个文件，有可能会达到限制，因而任务失败。  如果存在大量的资产，我们建议在作业准备任务中使用 azcopy 命令行，这样就可以使用通配符，且没有限制。

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>将输出文件从 Batch 池 VM 复制到 Blob 存储

可以使用[输出文件](https://docs.microsoft.com/rest/api/batchservice/task/add#outputfile)将文件从池 VM 复制到存储。  任务完成后，可将一个或多个文件从 VM 复制到指定的存储帐户。  应复制渲染的输出，但可能还需要存储日志文件。

<!--Not Avaialble on ## Using a blobfuse virtual file system for Linux VM pools-->

### <a name="accessing-files"></a>访问文件

作业任务使用装载的文件系统指定输入文件和输出文件的路径。

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>将输入资产文件从 Blob 存储复制到 Batch 池 VM

由于文件只是 Azure 存储中的 Blob，因此可以使用标准的 Blob API、工具和 UI（例如，azcopy、存储资源管理器、Batch Explorer 等）在本地文件系统与 Blob 存储之间复制文件。

## <a name="using-azure-files-with-windows-vms"></a>对 Windows VM 使用 Azure 文件

[Azure 文件](../storage/files/storage-files-introduction.md)在云中提供可通过 SMB 协议访问的完全托管式文件共享。  Azure 文件基于 Azure Blob 存储；它非常[经济高效](https://www.azure.cn/pricing/details/storage/files/)，可以使用数据复制将其配置到另一个区域，以实现全局冗余。  应评审[缩放目标](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets)，以确定是否应该针对特定的预测池大小和资产文件数量使用 Azure 文件。

此[文档](../storage/files/storage-how-to-use-files-windows.md)介绍了如何装载 Azure 文件共享。

### <a name="mounting-an-azure-files-share"></a>装载 Azure 文件共享

若要在 Batch 中使用，每次运行某个任务时，都需要执行装载操作，因为无法保持任务之间的连接。  为实现此目的，最简单的方法是通过池配置中的启动任务，使用 cmdkey 来持久保存凭据，然后在每个任务之前装载共享。

池模板中 cmdkey 的示例用法（经过转义，以便在 JSON 文件中使用）- 请注意，将 cmdkey 调用与 net use 调用隔开时，启动任务的用户上下文必须与用于运行任务的上下文相同：

```
"startTask": {
  "commandLine": "cmdkey /add:storageaccountname.file.core.chinacloudapi.cn
    /user:AZURE\\markscuscusbatch /pass:storage_account_key",
  "userIdentity":{
    "autoUser": {
      "elevationLevel": "nonadmin",
      "scope": "pool"
    }
}
```

示例作业任务命令行：
```
"commandLine":"net use S:
  \\\\storageaccountname.file.core.chinacloudapi.cn\\rendering &
3dsmaxcmdio.exe -v:5 -rfw:0 -10 -end:10
  -bitmapPath:\"s:\\3dsMax\\Dragon\\Assets\"
  -outputName:\"s:\\3dsMax\\Dragon\\RenderOutput\\dragon.jpg\"
  -w:1280 -h:720
  \"s:\\3dsMax\\Dragon\\Assets\\Dragon_Character_Rig.max\""
```

### <a name="accessing-files"></a>访问文件

作业任务使用装载的文件系统指定输入文件和输出文件的路径（使用映射驱动器或 UNC 路径）。

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>将输入资产文件从 Blob 存储复制到 Batch 池 VM

<!--MOONCAKE: SCORECARD REQUIREMENT for example,-->

支持 Azure 存储的所有主要 API 和工具（例如 azcopy、Azure CLI、存储资源管理器、Azure PowerShell、Batch Explorer 等）都支持 Azure 文件存储。

<!--Not Available on [Azure File Sync](../storage/files/storage-sync-files-planning.md)-->

## <a name="next-steps"></a>后续步骤

有关存储选项的详细信息，请参阅深度文档：

* [Azure blob 存储](../storage/blobs/storage-blobs-introduction.md)

    <!--Not Available on * [Blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)-->
    
* [Azure 文件](../storage/files/storage-files-introduction.md)

<!-- Update_Description: update meta properties, wording update, update link -->