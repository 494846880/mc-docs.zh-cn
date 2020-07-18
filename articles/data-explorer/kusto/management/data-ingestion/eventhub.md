---
title: 从事件中心引入 - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的“从事件中心引入”功能。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 04/01/2020
ms.date: 07/01/2020
ms.openlocfilehash: 09a2934695a5ed0551eb340f8d901d20b521d359
ms.sourcegitcommit: 9bc3e55f01e0999f05e7b4ebaea95f3ac91d32eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86226275"
---
# <a name="ingest-from-event-hub"></a>从事件中心引入

[Azure 事件中心](https://docs.azure.cn/event-hubs/event-hubs-about)是大数据流式处理平台和事件引入服务。 Azure 数据资源管理器通过客户管理的事件中心提供持续引入。

## <a name="data-format"></a>数据格式

* 将以 [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) 对象的形式从事件中心读取数据。
* 事件有效负载可以包含一个或多个记录，这些记录将以 [Azure 数据资源管理器支持的格式](../../../ingestion-supported-formats.md)之一进行引入。
* 可以使用 `GZip` 压缩算法来压缩数据。 必须以 `Compression` [引入属性](#ingestion-properties)的形式进行指定。

> [!Note]
> * 压缩格式（Avro、Parquet、ORC）不支持数据压缩。
> * 压缩数据不支持自定义编码和嵌入式[系统属性](#event-system-properties-mapping)。

## <a name="ingestion-properties"></a>引入属性

引入属性会指示引入过程、数据路由到的位置以及数据处理方式。 可以使用 [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties) 指定事件引入的[引入属性](../../../ingestion-properties.md)。 可以设置以下属性：

|属性 |说明|
|---|---|
| 表 | 现有目标表的名称（区分大小写）。 替代“`Data Connection`”边栏选项卡上设置的“`Table`”。 |
| 格式 | 数据格式。 替代“`Data Connection`”边栏选项卡上设置的“`Data format`”。 |
| IngestionMappingReference | 要使用的现有[引入映射](../create-ingestion-mapping-command.md)的名称。 替代“`Data Connection`”边栏选项卡上设置的“`Column mapping`”。|
| 压缩 | 数据压缩。`None`（默认值）或 `GZip` 压缩。|
| 编码 | 数据编码，默认值为 UTF8。 可以是 [.NET 支持的任何编码](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)。 |
| 标记（预览版） | 将要与引入的数据（格式设置为 JSON 数组字符串）关联的[标记](../extents-overview.md#extent-tagging)的列表。 使用标记时存在[性能影响](../extents-overview.md#performance-notes-1)。 |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.azure.cn/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="events-routing"></a>事件路由

设置到 Azure 数据资源管理器群集的事件中心连接时，请指定目标表属性（表名、数据格式、压缩和映射）。 数据的默认路由也称为 `static routing`。
还可以使用事件属性指定每个事件的目标表属性。 连接将按照 [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties) 中指定的要求动态路由数据，替代此事件的静态属性。

在以下示例中设置事件中心详细信息，并将天气指标数据发送到 `WeatherMetrics` 表。
数据采用 `json` 格式。 `mapping1` 在 `WeatherMetrics` 表中预定义。

```csharp
var eventHubNamespaceConnectionString=<connection_string>;
var eventHubName=<event_hub>;

// Create the data
var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = 32 }; 
var data = JsonConvert.SerializeObject(metric);

// Create the event and add optional "dynamic routing" properties
var eventData = new EventData(Encoding.UTF8.GetBytes(data));
eventData.Properties.Add("Table", "WeatherMetrics");
eventData.Properties.Add("Format", "json");
eventData.Properties.Add("IngestionMappingReference", "mapping1");
eventData.Properties.Add("Tags", "['mydatatag']");

// Send events
var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubNamespaceConnectionString, eventHubName);
eventHubClient.Send(eventData);
eventHubClient.Close();
```

## <a name="event-system-properties-mapping"></a>事件系统属性映射

系统属性在事件排队时存储由事件中心服务设置的属性。 Azure 数据资源管理器事件中心连接会将所选属性嵌入置于表中的数据中。

> [!Note]
> * 单记录事件支持系统属性。
> * 压缩数据不支持系统属性。
> * 对于 `csv` 映射，属性将按下表中列出的顺序添加到记录的开头。 对于 `json` 映射，将根据下表中的属性名称添加属性。

### <a name="event-hub-exposes-the-following-system-properties"></a>事件中心公开以下系统属性

|属性 |数据类型 |说明|
|---|---|---|
| x-opt-enqueued-time |datetime | 将事件排队时的 UTC 时间 |
| x-opt-sequence-number |long | 事件中心分区流中的事件的逻辑序列号
| x-opt-offset |string | 事件与事件中心分区流之间的偏移量。 偏移量标识符在事件中心流的分区中独一无二 |
| x-opt-publisher |string | 发布服务器名称（如果消息已发送到发布服务器终结点） |
| x-opt-partition-key |string |存储了事件的相应分区的分区键 |

如果在表的“数据源”部分中选择了“事件系统属性”，则必须在表架构和映射中包含这些属性。

**表架构示例**

如果数据包含以下内容，则使用表架构命令创建或更改表架构：
* `Timespan`、`Metric` 和 `Value` 列  
* `x-opt-enqueued-time` 和 `x-opt-offset` 属性

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

**CSV 映射示例**

运行以下命令，将数据添加到记录的开头。
属性将按上表中列出的顺序添加到记录的开头。
序号值对于 CSV 映射很重要，其中的列序号将根据已映射的系统属性而更改。

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
 
JSON 映射示例

使用出现在“数据连接”边栏选项卡的“事件系统属性”列表中的系统属性名称添加数据。 运行：

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

## <a name="create-event-hub-connection"></a>创建事件中心连接

> [!Note]
> 为了获得最佳性能，请在 Azure 数据资源管理器群集所在的区域中创建所有资源。

### <a name="create-an-event-hub"></a>创建事件中心

[创建事件中心](https://docs.azure.cn/event-hubs/event-hubs-create)（如果还没有事件中心）。 可以在有关如何[创建事件中心](../../../ingest-data-event-hub.md#create-an-event-hub)的操作指南中找到模板。

> [!Note]
> * 分区计数不可更改，因此在设置分区计数时应考虑长期规模。
> * 使用者组对于每个使用者来说必须独一无二。 创建专用于 Azure 数据资源管理器连接的使用者组。

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>与 Azure 数据资源管理器的数据引入连接

* 通过 Azure 门户：[连接到事件中心](../../../ingest-data-event-hub.md#connect-to-the-event-hub)。
* 使用 Azure 数据资源管理器管理 .NET SDK：[添加事件中心数据连接](../../../data-connection-event-hub-csharp.md#add-an-event-hub-data-connection)
* 使用 Azure 数据资源管理器管理 Python SDK：[添加事件中心数据连接](../../../data-connection-event-hub-python.md#add-an-event-hub-data-connection)
* 使用 ARM 模板：使用[用于添加事件中心数据连接的 Azure 资源管理器模板](../../../data-connection-event-hub-resource-manager.md#azure-resource-manager-template-for-adding-an-event-hub-data-connection)

> [!Note]
> 如果选择了“我的数据包括路由信息”，则必须提供必要的[路由](#events-routing)信息作为事件属性的一部分。

> [!Note]
> 设置连接后，它将从在其创建后排队的事件开始引入数据。

#### <a name="generating-data"></a>生成数据

* 请参阅可生成数据并将其发送到事件中心的[示例应用](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)。

事件可以包含一个或多个记录（直到达到事件的大小限制）。 在下面的示例中，我们将发送两个事件，每个事件有五个追加的记录：

```csharp
var events = new List<EventData>();
var data = string.Empty;
var recordsPerEvent = 5;
var rand = new Random();
var counter = 0;

for (var i = 0; i < 10; i++)
{
    // Create the data
    var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = rand.Next(-30, 50) }; 
    var data += JsonConvert.SerializeObject(metric) + Environment.NewLine;
    counter++;

    // Create the event
    if (counter == recordsPerEvent)
    {
        var eventData = new EventData(Encoding.UTF8.GetBytes(data));
        events.Add(eventData);

        counter = 0;
        data = string.Empty;
    }
}

// Send events
eventHubClient.SendAsync(events).Wait();
```
