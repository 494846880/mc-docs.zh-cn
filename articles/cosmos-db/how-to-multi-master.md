---
title: 如何在 Azure Cosmos DB 中配置多主数据库
description: 了解如何在 Azure Cosmos DB 中使用不同 SDK 为应用程序配置多主数据库。
author: rockboyfor
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 12/02/2019
ms.date: 06/22/2020
ms.author: v-yeche
ms.custom: tracking-python
ms.openlocfilehash: de07a80c015a181761d1275bac3a7285497a49ac
ms.sourcegitcommit: 48b5ae0164f278f2fff626ee60db86802837b0b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85098473"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>在使用 Azure Cosmos DB 的应用程序配置多主数据库

创建启用了多个写入区域的帐户后，必须在应用程序中对 DocumentClient 的 ConnectionPolicy 进行两处更改，以启用 Azure Cosmos DB 中的多主数据库和多宿主功能。 在 ConnectionPolicy 中，将 UseMultipleWriteLocations 设置为 true，并将部署应用程序的区域的名称传递给 SetCurrentLocation。 这将根据传入位置的地理接近性填充 PreferredLocations 属性。 如果稍后将新区域添加到帐户中，则无需更新或重新部署应用程序，它将自动检测距离较近的区域，并在发生区域事件时自动定位到该区域。

> [!Note]
> 最初配置有单个写入区域的 Cosmos 帐户可以配置为多个写入区域（即多主数据库）且停机时间为零。 若要了解详细信息，请参阅[配置多个写入区域](how-to-manage-database-account.md#configure-multiple-write-regions)

<a name="netv2"></a>
## <a name="net-sdk-v2"></a>.NET SDK v2

若要在应用程序中启用多主数据库，请将 `UseMultipleWriteLocations` 设置为 `true`。 此外，将 `SetCurrentLocation` 设置为在其中部署应用程序并复制 Azure Cosmos DB 的区域：

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("China North 2");
```

<a name="netv3"></a>
## <a name="net-sdk-v3"></a>.NET SDK v3

若要在应用程序中启用多主数据库，请将 `ApplicationRegion` 设置为在其中部署应用程序并复制 Cosmos DB 的区域：

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.ChinaNorth2,
    });
```

（可选）可以使用 `CosmosClientBuilder` 和 `WithApplicationRegion` 来获得相同的结果：

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.ChinaNorth2);
CosmosClient client = cosmosClientBuilder.Build();
```

<a name="java4-multi-master"></a>
## <a name="java-v4-sdk"></a>Java V4 SDK

若要在应用程序中启用多主数据库，请在客户端生成器中调用 `.multipleWriteRegionsEnabled(true)` 和 `.preferredRegions(preferredRegions)`，其中 `preferredRegions` 是包含一个元素的 `List`，即正在部署应用程序和复制 Cosmos DB 的区域：

# <a name="async"></a>[异步](#tab/api-async)

   [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) 异步 API

   ```java

   ArrayList<String> preferredRegions = new ArrayList<String>();
   preferredRegions.add(region);

   CosmosAsyncClient client =
           new CosmosClientBuilder()
                   .endpoint(HOST)
                   .key(MASTER_KEY)
                   .multipleWriteRegionsEnabled(true)
                   .preferredRegions(preferredRegions)
                   .buildAsyncClient();

   ```

# <a name="sync"></a>[Sync](#tab/api-sync)

   [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) 同步 API

   ```java

   ArrayList<String> preferredRegions = new ArrayList<String>();
   preferredRegions.add(region);

   CosmosClient client =
           new CosmosClientBuilder()
                   .endpoint(HOST)
                   .key(MASTER_KEY)
                   .multipleWriteRegionsEnabled(true)
                   .preferredRegions(preferredRegions)
                   .buildClient();

   ```

--- 

<a name="java2-milti-master"></a>
## <a name="async-java-v2-sdk"></a>Async Java V2 SDK

Java V2 SDK 使用 Maven [com.microsoft.azure::azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)。 若要在应用程序中启用多主数据库，请设置 `policy.setUsingMultipleWriteLocations(true)` 并将 `policy.setPreferredLocations` 设置为在其中部署应用程序并复制 Cosmos DB 的区域：

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

<a name="javascript"></a>
## <a name="nodejs-javascript-and-typescript-sdks"></a>Node.js、JavaScript 和 TypeScript SDK

若要在应用程序中启用多主数据库，请将 `connectionPolicy.UseMultipleWriteLocations` 设置为 `true`。 此外，将 `connectionPolicy.PreferredLocations` 设置为在其中部署应用程序并复制 Cosmos DB 的区域：

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

<a name="python"></a>
## <a name="python-sdk"></a>Python SDK

若要在应用程序中启用多主数据库，请将 `connection_policy.UseMultipleWriteLocations` 设置为 `true`。 此外，将 `connection_policy.PreferredLocations` 设置为在其中部署应用程序并复制 Cosmos DB 的区域。

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>后续步骤

请阅读以下文章：

* [使用会话令牌在 Azure Cosmos DB 中管理一致性](how-to-manage-consistency.md#utilize-session-tokens)
* [Azure Cosmos DB 中的冲突类型和解决策略](conflict-resolution-policies.md)
* [Azure Cosmos DB 中的高可用性](high-availability.md)
* [Azure Cosmos DB 中的一致性级别](consistency-levels.md)
* [在 Azure Cosmos DB 中选择适当的一致性级别](consistency-levels-choosing.md)
* [Azure Cosmos DB 中的一致性、可用性和性能权衡](consistency-levels-tradeoffs.md)
* [各种一致性级别的可用性和性能权衡](consistency-levels-tradeoffs.md)
* [全局缩放预配的吞吐量](scaling-throughput.md)
* [多区域分布：揭秘](global-dist-under-the-hood.md)

<!-- Update_Description: update meta properties, wording update, update link -->