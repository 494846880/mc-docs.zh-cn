---
title: 复制活动中的会话日志
description: 了解如何在 Azure 数据工厂的复制活动中启用会话日志。
services: data-factory
documentationcenter: ''
author: WenJason
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
origin.date: 11/11/2020
ms.date: 11/23/2020
ms.author: v-jay
ms.openlocfilehash: fb4aa5d66903948b954976a47231f201b366c6d9
ms.sourcegitcommit: c89f1adcf403f5845e785064350136698eed15b8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94680610"
---
#  <a name="session-log-in-copy-activity"></a>复制活动中的会话日志

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

你可以在复制活动中记录复制的文件名，这有助于进一步确保数据不仅能成功地从源复制到目标存储，还可以在源和目标存储之间保持一致，方法是在复制活动会话日志中查看复制的文件。  

如果在复制活动中启用容错设置以跳过出错的数据，则还可以记录跳过的文件和跳过的行。  可以在[复制活动中的容错](copy-activity-fault-tolerance.md)中获取更多详细信息。 

## <a name="configuration"></a>Configuration
以下示例提供了一个 JSON 定义，用于在复制活动中启用会话日志： 

```json
"typeProperties": {
    "source": {
        "type": "BinarySource",
        "storeSettings": {
            "type": "AzureDataLakeStoreReadSettings",
            "recursive": true
        },
        "formatSettings": {
            "type": "BinaryReadSettings"
        }
    },
    "sink": {
        "type": "BinarySink",
        "storeSettings": {
            "type": "AzureBlobFSWriteSettings"
        }
    },                    
    "skipErrorFile": {
        "fileForbidden": true,
        "dataInconsistency": true
    },
    "validateDataConsistency": true,
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    }
}
```

Property | 说明 | 允许的值 | 必选
-------- | ----------- | -------------- | -------- 
enableCopyActivityLog | 当设置为 true 时，你将有机会记录复制的文件、跳过的文件或跳过的行。  | True<br/>False（默认值） | 否
logLevel | “Info”将记录所有复制的文件、跳过的文件和跳过的行。 “Warning”将仅记录跳过的文件和跳过的行。  | 信息<br/>警告（默认） | 否
enableReliableLogging | 如果为 true，则在将每个文件复制到目标后，处于可靠模式的复制活动会立即刷新日志。  在复制活动中启用了可靠日志记录模式的情况下复制大量文件时，复制吞吐量应该会受影响，因为每次复制文件都需要执行双重写入操作。 一个请求是写入到目标存储，另一个请求是写入到日志存储。  最大努力模式下的复制活动会在一段时间内使用批量记录刷新日志，在这段时间内，复制吞吐量受到的影响会小得多。 在此模式下，不能保证日志记录的完整性和及时性，因为当复制活动失败时，最后一批日志事件有可能尚未刷新到日志文件。 此时，你会看到复制到目标的一些文件未被记录。  | True<br/>False（默认值） | 否
logLocationSettings | 一组属性，可用于指定存储会话日志的位置。 | | 否
linkedServiceName | [Azure Blob 存储](connector-azure-blob-storage.md#linked-service-properties)或 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) 的链接服务，用于存储会话日志文件。 | `AzureBlobStorage` 或 `AzureBlobFS` 类型链接服务的名称，指代用于存储日志文件的实例。 | 否
path | 日志文件的路径。 | 指定用于存储日志文件的路径。 如果未提供路径，服务会为用户创建一个容器。 | 否


## <a name="monitoring"></a>监视

### <a name="output-from-copy-activity"></a>复制活动的输出
在复制活动完全运行后，可以从每个复制活动运行的输出中看到日志文件的路径。 可以从以下路径中找到日志文件：`https://[your-blob-account].blob.core.chinacloudapi.cn/[logFilePath]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`。  日志文件将是 csv 文件。 

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```

### <a name="the-schema-of-the-log-file"></a>日志文件的架构

下面是日志文件的架构。

列 | 说明 
-------- | -----------  
时间戳 | ADF 读取、写入或跳过对象时的时间戳。
Level | 此项的日志级别。 它可以是“Warning”或“Info”。
OperationName | 每个对象上的 ADF 复制活动操作行为。 它可以是“FileRead”、“FileWrite”、“FileSkip”或“TabularRowSkip”。
OperationItem | 文件名或跳过的行。
消息 | 已从源存储中读取文件或已将文件写入到目标存储时会显示的更多信息。 它也可能是文件或行被跳过的原因。

下面是日志文件的示例。 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-10-19 08:39:13.6688152,Info,FileRead,"sample1.csv","Start to read file: {""Path"":""sample1.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:39:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
2020-10-19 08:40:13.6688152,Info,FileRead,"sample2.csv","Start to read file: {""Path"":""sample2.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:40:13.9003981,Info,FileWrite,"sample2.csv","Start to write file from source file: sample2.csv."
2020-10-19 08:45:17.6508407,Info,FileRead,"sample2.csv","Complete reading file successfully. "
2020-10-19 08:45:28.7390083,Info,FileWrite,"sample2.csv","Complete writing file from source file: sample2.csv. File is successfully copied."
```
从上面的日志文件中，你可以看到 sample1.csv 已跳过，因为无法验证它在源存储和目标存储之间是否一致。 你可以获取有关 sample1.csv 变得不一致的原因的更多详细信息，这是因为当 ADF 复制活动同时复制时，其他应用程序正在更改 sample1.csv。 你还可以看到 sample2.csv 已成功地从源复制到目标存储。

你可以使用多个分析引擎进一步分析日志文件。  下面的几个示例使用 SQL 查询来分析日志文件，方法是将 csv 日志文件导入到 SQL 数据库，其中的表名称可以是 SessionLogDemo。  

-   显示复制的文件列表。 
```sql
select OperationItem from SessionLogDemo where Message like '%File is successfully copied%'
```

-   显示在特定时间范围内复制的文件列表。 
```sql
select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%'
```

-   显示某个特定文件及其复制时间和元数据。 
```sql
select * from SessionLogDemo where OperationItem='<file name>'
```

-   显示在某个时间范围内复制的文件的列表及其元数据。 
```sql
select * from SessionLogDemo where OperationName='FileRead' and Message like 'Start to read%' and OperationItem in (select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%')
```

-   显示跳过的文件列表。 
```sql
select OperationItem from SessionLogDemo where OperationName='FileSkip'
```

-   提供跳过特定文件的原因。 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip'
```

-   显示由于“blob 文件不存在”这一原因跳过的文件列表。 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip' and Message like '%UserErrorSourceBlobNotExist%'
```

-   显示需要的复制时间最长的文件名。
```sql
select top 1 OperationItem, CopyDuration=DATEDIFF(SECOND, min(TIMESTAMP), max(TIMESTAMP)) from SessionLogDemo group by OperationItem order by CopyDuration desc
```


## <a name="next-steps"></a>后续步骤
请参阅其他复制活动文章：

- [复制活动概述](copy-activity-overview.md)
- [复制活动容错](copy-activity-fault-tolerance.md)
- [复制活动数据一致性](copy-activity-data-consistency.md)