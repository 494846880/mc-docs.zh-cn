---
title: 使用 Azure 数据工厂中的复制活动保留元数据和 ACL
description: 了解如何使用 Azure 数据工厂中的复制活动在复制期间保留元数据和 ACL。
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
origin.date: 05/06/2020
ms.date: 06/15/2020
ms.author: v-jay
ms.openlocfilehash: 3b073a73e56bc457ab6e97cb50af4338555b47d8
ms.sourcegitcommit: 3de7d92ac955272fd140ec47b3a0a7b1e287ca14
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/12/2020
ms.locfileid: "84723473"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>使用 Azure 数据工厂中的复制活动保留元数据和 ACL

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

当使用 Azure 数据工厂复制活动将数据从源复制到接收器时，在以下情况下，还可以保留元数据和 ACL。

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a> 保留数据湖迁移的元数据

在将数据从一个数据湖迁移到另一个数据湖（包括 [Amazon S3](connector-amazon-simple-storage-service.md)、[Azure Blob](connector-azure-blob-storage.md) 和 [Azure data lake Storage Gen2](connector-azure-data-lake-storage.md)）时，可以选择保留文件元数据和数据。

复制活动支持在数据复制期间保留以下属性：

- **所有客户指定的元数据** 
- 以及以下五个数据存储内置系统属性：`contentType`、`contentLanguage`（Amazon S3 除外）、`contentEncoding`、`contentDisposition`、`cacheControl`****。

**处理元数据的差异：** Amazon S3 和 Azure 存储允许在客户指定的元数据键中使用不同的字符集。 当你选择使用复制活动保留元数据时，ADF 会自动将无效字符替换为“_”。

按原样将文件从 Amazon S3/Azure Data Lake Storage Gen2/Azure Blob 复制到二进制格式的 Azure Data Lake Storage Gen2/Azure Blob 时，可以在用于活动创作的“复制活动” > “设置”选项卡或复制数据工具的“设置”页上找到“保留”选项**** **** **** ****。

![复制活动保留元数据](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

下面是复制活动 JSON 配置的示例（请参阅 `preserve`）： 

```json
"activities":[
    {
        "name": "CopyAndPreserveMetadata",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>后续步骤

请参阅其他复制活动文章：

- [复制活动概述](copy-activity-overview.md)
- [复制活动性能](copy-activity-performance.md)
