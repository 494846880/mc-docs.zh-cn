---
title: 从 IoT 中心引入 - Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍了 Azure 数据资源管理器中的“从 IoT 中心引入”功能。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 08/13/2020
ms.date: 08/18/2020
ms.openlocfilehash: bd3690f34a5267868752b324017f8f42c7a856c0
ms.sourcegitcommit: f4bd97855236f11020f968cfd5fbb0a4e84f9576
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556515"
---
# <a name="connect-to-iot-hub"></a>连接到 IoT 中心

[Azure IoT 中心](https://docs.azure.cn/iot-hub/about-iot-hub)是一项托管服务，承载在云中，充当中央消息中心，用于 IoT 应用程序与其管理的设备之间的双向通信。 Azure 数据资源管理器使用其[与事件中心兼容的内置终结点](https://docs.azure.cn/iot-hub/iot-hub-devguide-messages-d2c#routing-endpoints)提供从客户托管 IoT 中心进行的连续引入。

IoT 引入管道需要完成几个步骤。 首先，创建一个 IoT 中心，并将设备注册到此 IoT 中心。 然后，创建 Azure 数据资源管理器目标表，使用给定的[引入属性](#set-ingestion-properties)将[特定格式的数据](#data-format)引入到该表中。 IoT 中心连接需要知道[事件路由](#set-events-routing)才能连接到 Azure 数据资源管理器表。 根据[事件系统属性映射](#set-event-system-properties-mapping)，使用选定的属性嵌入数据。 可以通过 [Azure 门户](ingest-data-iot-hub.md)使用 [C#](data-connection-iot-hub-csharp.md) 或 [Python](data-connection-iot-hub-python.md) 以编程方式来管理此过程，也可以使用 [Azure 资源管理器模板](data-connection-iot-hub-resource-manager.md)来这样做。


## <a name="create-iot-hub-connection"></a>创建 IoT 中心连接

> [!Note]
> 为了获得最佳性能，请在 Azure 数据资源管理器群集所在的区域中创建所有资源。

[创建 IoT 中心](ingest-data-iot-hub.md#create-an-iot-hub)（如果还没有 IoT 中心）。

> [!Note]
> * `device-to-cloud partitions` 计数不可更改，因此在设置分区计数时应考虑长期规模。
> * 使用者组对于每个使用者来说必须独一无二。 创建专用于 Azure 数据资源管理器连接的使用者组。 在 Azure 门户中找到你的资源，然后转到“`Built-in endpoints`”，以便添加新的使用者组。

## <a name="data-format"></a>数据格式

* 将以 [EventData](https://docs.azure.cn/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) 对象的形式从事件中心终结点读取数据。
* 事件有效负载可以采用 [Azure 数据资源管理器支持的格式](ingestion-supported-formats.md)之一。
* 请参阅[支持的压缩](ingestion-supported-formats.md#supported-data-compression-formats)。
  原始的未压缩数据大小应该是 blob 元数据的一部分，否则 Azure 数据资源管理器会对其进行估算。 每个文件的引入未压缩大小限制为 4 GB。  

## <a name="set-ingestion-properties"></a>设置引入属性

引入属性指示引入过程。 将数据路由到何处以及如何处理数据。 可以使用 [EventData.Properties](https://docs.azure.cn/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties) 指定事件引入的[引入属性](ingestion-properties.md)。 可以设置以下属性：

|属性 |说明|
|---|---|
| 表 | 现有目标表的名称（区分大小写）。 替代“`Data Connection`”边栏选项卡上设置的“`Table`”。 |
| 格式 | 数据格式。 替代“`Data Connection`”边栏选项卡上设置的“`Data format`”。 |
| IngestionMappingReference | 要使用的现有[引入映射](kusto/management/create-ingestion-mapping-command.md)的名称。 替代“`Data Connection`”边栏选项卡上设置的“`Column mapping`”。|
| 编码 |  数据编码，默认值为 UTF8。 可以是 [.NET 支持的任何编码](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)。 |

## <a name="set-events-routing"></a>设置事件路由

设置到 Azure 数据资源管理器群集的 IoT 中心连接时，请指定目标表属性（表名、数据格式和映射）。 这是用于你的数据的默认路由，也称为“静态路由”。
还可以使用事件属性指定每个事件的目标表属性。 连接将按照 [EventData.Properties](https://docs.azure.cn/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties) 中指定的要求动态路由数据，替代此事件的静态属性。

> [!Note]
> 如果选择了“我的数据包括路由信息”，则必须提供必要的路由信息作为事件属性的一部分。

## <a name="set-event-system-properties-mapping"></a>设置事件系统属性映射

系统属性是一个集合，用于存储收到事件时由 IoT 中心服务设置的属性。 Azure 数据资源管理器 IoT 中心连接会将所选属性嵌入置于表中的数据中。

> [!Note]
> 对于 `csv` 映射，属性将按下表中列出的顺序添加到记录的开头。 对于 `json` 映射，将根据下表中的属性名称添加属性。

### <a name="iot-hub-exposes-the-following-system-properties"></a>IoT 中心公开了以下系统属性：

|属性 |说明|
|---|---|
| message-id | 用户可设置的消息标识符，用于请求-答复模式。 |
| sequence-number | IoT 中心分配给每条云到设备消息的编号（对每个设备队列是唯一的）。 |
| to | 在云到设备消息中指定的目标。 |
| absolute-expiry-time | 消息过期的日期和时间。 |
| iothub-enqueuedtime | IoT 中心收到设备到云消息的日期和时间。 |
| correlation-id| 响应消息中的字符串属性，通常包含采用“请求-答复”模式的请求的 MessageId。 |
| user-id| 用于指定消息的源的 ID。 |
| iothub-ack| 反馈消息生成器。 |
| iothub-connection-device-id| IoT 中心对设备到云的消息设置的 ID。 它包含发送了消息的设备的 deviceId。 |
| iothub-connection-auth-generation-id| IoT 中心对设备到云的消息设置的 ID。 它包含发送了消息的设备的 connectionDeviceGenerationId（取决于设备标识属性）。 |
| iothub-connection-auth-method| 由 IoT 中心对设备到云的消息设置的身份验证方法。 此属性包含用于验证发送消息的设备的身份验证方法的相关信息。 |

如果在表的“数据源”部分中选择了“事件系统属性”，则必须在表架构和映射中包含这些属性。

### <a name="examples"></a>示例 

#### <a name="table-schema-example"></a>表架构示例

如果数据包含三列（`Timespan`、`Metric` 和 `Value`）并且包含的属性是 `x-opt-enqueued-time` 和 `x-opt-offset`，请使用以下命令创建或更改表架构：

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

#### <a name="csv-mapping-example"></a>CSV 映射示例

运行以下命令，将数据添加到记录的开头。 记下序号值：属性将按上表中列出的顺序添加到记录的开头。 这对于 CSV 映射很重要，其中的列序号将根据已映射的系统属性而更改。

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
#### <a name="json-mapping-example"></a>JSON 映射示例

使用出现在“数据连接”边栏选项卡“事件系统属性”列表中的系统属性名称添加数据。 运行以下命令：

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```

### <a name="generate-data"></a>生成数据

* 请参阅用于模拟设备并生成数据的[示例项目](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Quickstarts/simulated-device)。

## <a name="next-steps"></a>后续步骤

可以通过多种方法将数据引入 IoT 中心。 有关每种方法的演练，请参阅以下链接。

* [将数据从 IoT 中心引入到 Azure 数据资源管理器](ingest-data-iot-hub.md)
* [使用 C#（预览版）为 Azure 数据资源管理器创建 IoT 中心数据连接](data-connection-iot-hub-csharp.md)
* [使用 Python 为 Azure 数据资源管理器创建 IoT 中心数据连接（预览版）](data-connection-iot-hub-python.md)
* [使用 Azure 资源管理器模板为 Azure 数据资源管理器创建 IoT 中心数据连接](data-connection-iot-hub-resource-manager.md)
