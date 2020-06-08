---
title: Azure Data Lake Storage Gen2 中可用的 Blob 存储功能 | Microsoft Docs
description: 了解哪些 Blob 存储功能可用于 Azure Data Lake storage Gen2
author: WenJason
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
origin.date: 05/10/2020
ms.date: 06/01/2020
ms.author: v-jay
ms.reviewer: stewu
ms.openlocfilehash: 0afd168dfe0a3f72495b202aac038f90049e258b
ms.sourcegitcommit: be0a8e909fbce6b1b09699a721268f2fc7eb89de
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2020
ms.locfileid: "84199732"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 中可用的 Blob 存储功能

Blob 存储功能（例如[诊断日志记录](../common/storage-analytics-logging.md)、[访问层](storage-blob-storage-tiers.md)和  [Blob 存储生命周期管理策略](storage-lifecycle-management-concepts.md)）现在适用于具有分层命名空间的帐户。 因此，你可以在 Blob 存储帐户上启用分层命名空间，而不会失去对这些功能的访问权限。

下表列出了可用于 Azure Data Lake Storage Gen2 的 Blob 存储功能。 随着支持的不断扩展，这些表中出现的项将随着时间的推移而变化。 若要详细了解与功能的预览状态相关的特定问题，请参阅[已知问题](data-lake-storage-known-issues.md)一文。

## <a name="supported-blob-storage-features"></a>支持的 Blob 存储功能

> [!NOTE]
> 支持级别仅指 Data Lake Storage Gen2 支持该功能的方式。 

|Blob 存储功能 |支持级别 |相关文章 |
|---------------|-------------------|---|
|热访问层|正式发布|[Azure Blob 存储：热、冷、存档访问层](storage-blob-storage-tiers.md)|
|冷访问层|正式发布|[Azure Blob 存储：热、冷、存档访问层](storage-blob-storage-tiers.md)|
|指标（经典）|正式发布|[Azure 存储分析指标（经典）](../common/storage-analytics-metrics.md?toc=%2fstorage%2fblobs%2ftoc.json)|
|Azure Monitor 中的指标|正式发布|[Azure Monitor 中的 Azure 存储指标](../common/storage-metrics-in-azure-monitor.md?toc=%2fstorage%2fblobs%2ftoc.json)|
|Blob 存储 PowerShell 命令|正式发布|[快速入门：使用 PowerShell 上传、下载和列出 blob](storage-quickstart-blobs-powershell.md)|
|Blob 存储 Azure CLI 命令|正式发布|[快速入门：使用 Azure CLI 创建、下载和列出 blob](storage-quickstart-blobs-cli.md)|
|Blob 存储 API|正式发布|[快速入门：适用于 .NET 的 Azure Blob 存储客户端库 v12](storage-quickstart-blobs-dotnet.md)<br>[快速入门：使用 Java v12 SDK 管理 blob](storage-quickstart-blobs-java.md)<br>[快速入门：使用 Python v12 SDK 管理 blob](storage-quickstart-blobs-python.md)<br>[快速入门：在 Node.js 中使用 JavaScript v12 SDK 管理 blob](storage-quickstart-blobs-nodejs.md)|
|诊断日志|正式发布|[Azure 存储分析日志记录](../common/storage-analytics-logging.md?toc=%2fstorage%2fblobs%2ftoc.json)|
|存档访问层|预览|[Azure Blob 存储：热、冷、存档访问层](storage-blob-storage-tiers.md)|
|生命周期管理策略|预览|[管理 Azure Blob 存储生命周期](storage-lifecycle-management-concepts.md)|
|更改源|尚不支持|[Azure Blob 存储中的更改源支持](storage-blob-change-feed.md)|
|帐户故障转移|尚不支持|[灾难恢复和帐户故障转移](../common/storage-disaster-recovery-guidance.md?toc=%2fstorage%2fblobs%2ftoc.json)|
|Blob 容器 ACL|尚不支持|[Set Container ACL](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|自定义域|尚不支持|[将自定义域映射到 Azure Blob 存储终结点](storage-custom-domain-name.md)|
|不可变存储|尚不支持|[使用不可变的存储来存储业务关键型 Blob 数据](storage-blob-immutable-storage.md)|
|快照|尚不支持|[在 .NET 中创建和管理 blob 快照](storage-blob-snapshots.md)|
|软删除|尚不支持|[Azure 存储 Blob 的软删除](storage-blob-soft-delete.md)|
|静态网站|尚不支持|[Azure 存储中的静态网站托管](storage-blob-static-website.md)|
|登录 Azure Monitor|尚不支持|目前不可用|

## <a name="see-also"></a>另请参阅

- [Azure Data Lake Storage Gen2 的已知问题](data-lake-storage-known-issues.md)
- [支持 Azure Data Lake Storage Gen2 的 Azure 服务](data-lake-storage-supported-azure-services.md)
- [支持 Azure Data Lake Storage Gen2 的开源平台](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage 的多协议访问](data-lake-storage-multi-protocol-access.md)