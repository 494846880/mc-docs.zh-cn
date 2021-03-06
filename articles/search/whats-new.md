---
title: Azure 认知搜索中的新增功能
description: 新增功能和增强功能的通告，包括 Azure 搜索服务已重命名为 Azure 认知搜索。
manager: nitinme
author: HeidiSteen
ms.author: v-tawe
ms.service: cognitive-search
ms.topic: overview
origin.date: 11/12/2020
ms.date: 12/10/2020
ms.custom: references_regions
ms.openlocfilehash: 81c33d5538fd2e5ea99aa2b7f00d96223068c05d
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004140"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Azure 认知搜索中的新增功能

了解服务中的新增功能。 请将本页加入书签，以随时了解该服务的最新信息。

<!-- ## November 2020

|Feature&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Category | Description | Availability  |
|------------------------------|----------|-------------|---------------|
|[customer-managed key encryption over indexers, data sources, and skillsets](search-security-manage-encryption-keys.md) | Security | This addition extends customer-managed encryption over the full range of assets created and managed by a search service. Recall that CMK support adds an additional encryption layer on top of base encryption performed and managed by Microsoft. | Generally available using REST api-version=2020-06-30 |  
-->

## <a name="september-2020"></a>2020 年 9 月

<!-- Create an identity for a search service in Azure Active Directory, then use Azure RBAC permissions to grant the identity read-only permissions to Azure data sources. Optionally, choose the [trusted service exception](search-indexer-howto-access-trusted-service-exception.md) capability if IP rules are not an option. -->

<!-- | [Managed service identity](search-howto-managed-identities-data-sources.md) | Indexers, security | Create an identity for a search service in Azure Active Directory, then use Azure RBAC permissions to grant access to Azure data sources. This approach eliminates the need for credentials on the connection string. <br><br>An additional way to use a managed service identity is through a [trusted service exception](search-indexer-howto-access-trusted-service-exception.md) if IP rules are not an option. | Generally available. Access this capability when using the portal or [Create Data Source (REST)](https://docs.microsoft.com/rest/api/searchservice/create-data-source) with api-version=2020-06-30. | -->

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 类别 | 说明 | 可用性  |
|------------------------------|----------|-------------|---------------|
| [管理 REST API (2020-08-01)](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) | REST | 新的稳定 REST API 增加了对创建共享专用链接资源的支持。 | 正式发布。 |
| [管理 REST API (2020-08-01-Preview)](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) | REST | 添加了适用于 Azure Functions 和 Azure SQL for MySQL 数据库的共享专用链接资源。 | 公共预览版。 |
| [管理 .NET SDK 4.0](https://docs.microsoft.com/dotnetapi/overview/azure/search/management) | .NET SDK | 适用于管理 SDK 的 Azure SDK 更新，面向 REST API 版本 2020-08-01。 | 正式发布。 |

## <a name="august-2020"></a>2020 年 8 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 类别 | 说明 | 可用性  |
|---------|------------------|-------------|---------------|
| [双重加密](search-security-overview.md#encryption) | 安全性 | 通过对新的搜索服务配置客户管理的密钥 (CMK) 加密，在存储层启用双重加密。 创建新服务，[配置客户管理的密钥并应用于](search-security-manage-encryption-keys.md)索引或同义词映射，可受益于对该内容进行的双重加密。 | 2020 年 8 月 1 日后在以下区域创建的所有搜索服务都公开可用：中国东部 2。 使用门户、管理 REST API 或 SDK 创建服务。 |

## <a name="july-2020"></a>2020 年 7 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 类别 | 说明 | 可用性  |
|---------|------------------|-------------|---------------|
| [Azure.Search.Documents 客户端库](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme) | 用于 .NET 的 Azure SDK | .NET 客户端库，由 Azure SDK 团队发布，旨在保持与其他 .NET 客户端库的一致性。 <br/><br/>版本 11 面向搜索 REST api-version=2020-06-30，但尚不支持知识存储、地理空间类型或 [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder)。 <br/><br/>有关详细信息，请参阅[快速入门：创建索引](search-get-started-dotnet.md)和[升级到 Azure.Search.Documents (v11)](search-dotnet-sdk-migration-version-11.md)。 | 正式发布。 </br> 从 NuGet 安装 [Azure.Search.Documents 包](https://www.nuget.org/packages/Azure.Search.Documents/)。 |
| [azure.search.documents 客户端库](https://docs.microsoft.com/python/api/overview/azure/search-documents-readme)  | 用于 Python 的 Azure SDK| Python 客户端库，由 Azure SDK 团队发布，旨在保持与其他 Python 客户端库的一致性。 <br/><br/>版本 11 面向搜索 REST api-version=2020-06-30。 | 正式发布。 </br> 从 PyPI 安装 [azure-search-documents 包](https://pypi.org/project/azure-search-documents/)。 |
| [@azure/search-documents 客户端库](https://docs.microsoft.com/javascript/api/overview/azure/search-documents-readme)  | 用于 JavaScript 的 Azure SDK | JavaScript 客户端库，由 Azure SDK 团队发布，旨在保持与其他 JavaScript 客户端库的一致性。 <br/><br/>版本 11 面向搜索 REST api-version=2020-06-30。 | 正式发布。 </br> 从 npm 安装 [@azure/search-documents 包](https://www.npmjs.com/package/@azure/search-documents)。 |

## <a name="june-2020"></a>2020 年 6 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 类别 | 说明 | 可用性  |
|---------|------------------|-------------|---------------|
[知识存储](knowledge-store-concept-intro.md) | AI 扩充 | AI 扩充索引器的输出，将内容存储在 Azure 存储中以供其他应用和进程使用。 | 正式发布。 </br> 使用[搜索 REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) 或更高版本，或门户。 |
| [搜索 REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) | REST | REST API 的新稳定版本。 除了知识存储，此版本还包括搜索相关性和评分的增强功能。 | 正式发布。 |
| **Okapi BM25 相关性算法** | 查询 | 新的相关性排名算法自动用于 7 月 15 日之后创建的所有新搜索服务。 对于先前创建的服务，可以通过在索引字段上设置 `similarity` 属性来选择加入。 | 正式发布。 </br> 使用[搜索 REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) 或更高版本，或 REST API 2019-05-06。 |

<!-- | **executionEnvironment** | Security (indexers) | Explicitly set this indexer configuration property to `private` to force all connections to external data sources over a private endpoint. Applicable only to search services that leverage Azure Private Link. | Generally available. </br> Use [Search REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) to set this general configuration parameter. | -->

## <a name="may-2020-microsoft-build"></a>2020 年 5 月（Microsoft Build 大会）

<!--
| [**Debug sessions**](cognitive-search-debug-session.md) | AI enrichment | Debug sessions provide a portal-based interface to investigate and resolve issues with an existing skillset. Fixes created in the debug session can be saved to production skillsets. Get started with [this tutorial](cognitive-search-tutorial-debug-sessions.md). | Public preview, in the portal. |
| [**IP rules for in-bound firewall support**](service-configure-firewall.md) | Security | Limit access to a search service endpoint to specific IP addresses. | Generally available. </br> Use [Management REST API 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) or later, or the portal. |
| [**Azure Private Link for a private search endpoint**](service-create-private-endpoint.md) | Security| Shield a search service from the public internet by running it as a private link resource, accessible only to client apps and other Azure services on the same virtual network. | Generally available. </br> Use [Management REST API 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) or later, or the portal. |
| [**system-managed identity (preview)**](search-howto-managed-identities-data-sources.md) | Security (indexers) | Register a search service as a trusted service with Azure Active Directory to set up connections to supported Azure data source for indexing. Applies to [indexers](search-indexer-overview.md) that ingest content from Azure data sources such as Azure SQL Database, Azure Cosmos DB, and Azure Storage. | Public preview. </br> Use the portal to register the search service. |
-->

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 类别 | 说明 | 可用性  |
|---------|------------------|-------------|---------------|
| [**sessionId 查询参数**](index-similarity-and-scoring.md)，[scoringStatistics=global parameter](index-similarity-and-scoring.md#scoring-statistics) | 查询（相关性） | 将 sessionID 添加到查询中，以建立一个用于计算搜索分数的会话，scoringStatistics=global 用于从所有分区收集分数，以实现更一致的搜索分数计算。 | 正式发布。 </br> 使用[搜索 REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) 或更高版本，或 REST API 2019-05-06。 |
| [featuresMode（预览版）](index-similarity-and-scoring.md#featuresMode-param) | 查询 | 添加此查询参数，展开相关性分数以显示详细信息：每个字段相似度得分、每个字段术语频率和每个字段匹配的唯一标记数。 你可以在自定义评分算法中使用这些数据点。 有关演示此功能的示例，请参阅[添加机器学习 (LearnToRank) 以搜索相关性](https://github.com/Azure-Samples/search-ranking-tutorial)。 | 公共预览版。 </br> 使用[搜索 REST API 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) 或 REST API 2019-05-06-Preview。 |

## <a name="march-2020"></a>2020 年 3 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 类别 | 说明 | 可用性  |
|---------|------------------|-------------|---------------|
| [本机 blob 软删除（预览版）](search-howto-index-changed-deleted-blobs.md) | 索引器 | Azure 认知搜索中的 Azure blob 存储索引器会识别处于软删除状态的 blob，并在编制索引过程中删除相应的搜索文档。 | 公共预览版。 </br> 使用[搜索 REST API 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) 和 REST API 2019-05-06-Preview，并对启用了本机“软删除”的 Azure Blob 数据源运行索引器。 |
| [管理 REST API (2020-03-13)](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) | REST | 用于创建和管理搜索服务的新的稳定 REST API。 添加 IP 防火墙和专用链接支持 | 正式发布。 |

## <a name="february-2020"></a>2020 年 2 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 类别 | 说明 | 可用性  |
|---------|------------------|-------------|---------------|
| [PII 检测（预览版）](cognitive-search-skill-pii-detection.md) | AI 扩充 | 在编制索引期间使用的一项新的认知技能，它可从输入文本中提取个人信息，并可让你通过多种方式在该文本中屏蔽此类信息。 | 公共预览版。 </br> 使用门户或[搜索 REST API 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) 或 REST API 2019-05-06-Preview。 |
| [自定义实体查找（预览版）](cognitive-search-skill-custom-entity-lookup.md )| AI 扩充 | 一项新的认知技能，可在用户自定义的单词和短语列表中查找文本。 它使用此列表为包含任何匹配实体的所有文档加上标签。 该技能还支持一定程度的模糊匹配，应用此匹配方法可以查找类似但不完全相同的匹配项。 | 公共预览版。 </br> 使用门户或[搜索 REST API 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) 或 REST API 2019-05-06-Preview。 |

## <a name="january-2020"></a>2020 年 1 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 类别 | 说明 | 可用性  |
|---------|------------------|-------------|---------------|
| [客户管理的加密密钥](search-security-manage-encryption-keys.md) |安全性 | 除了平台的内置加密外，还添加了额外的加密层。 使用你创建和管理的加密密钥，可以在有效负载到达搜索服务之前对索引内容和同义词映射进行加密。 | 正式发布。 </br> 使用 Search REST API 2019-05-06 或更高版本。 对于托管代码，即使该功能现在不再以预览版提供，但正确的包仍是 [.NET SDK 版本 8.0-preview](search-dotnet-sdk-migration-version-9.md)。 |
| [适用于入站防火墙支持的 IP 规则（预览版）](service-configure-firewall.md) | 安全性 | 将对搜索服务终结点的访问限制为特定的 IP 地址。 预览版 API 在 [CreateOrUpdate API](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service) 中提供 **IpRule** 和 **NetworkRuleSet** 属性。 此预览版功能可在选定的区域中使用。 |  使用 api-version=2019-10-01-Preview 的公共预览版。  |

<!-- | [**Azure Private Link for a private search endpoint (preview)**](service-create-private-endpoint.md) | Security| Shield a search service from the public internet by running it as a private link resource, accessible only to client apps and other Azure services on the same virtual network. | Public preview using api-version=2019-10-01-Preview.  | -->

## <a name="features-in-2019"></a>2019 版中的功能

### <a name="december-2019"></a>2019 年 12 月

+ [创建演示应用（预览版）](search-create-app-portal.md)是门户中用于生成可下载 HTML 文件的新向导，并可对索引进行查询（只读）访问。 文件附带了嵌入式脚本，用于呈现可正常运行的“localhost”式 Web 应用，该应用绑定到搜索服务中的索引。 页面在向导中可配置，可以包含搜索栏、结果区域、边栏导航和自动提示查询支持。 可以脱机修改 HTML 以扩展或者自定义工作流或外观。 演示应用并不易于扩展到包含生产方案中通常需要的安全层和托管层。 你应将其视为验证和测试工具，而不是通往完整客户端应用的捷径。

<!-- + [Create a private endpoint for secure connections (preview)](service-create-private-endpoint.md) explains how to set up a Private Link for secure connections to your search service. This preview feature is available upon request and [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) as part of the solution. -->

### <a name="november-2019---ignite-conference"></a>2019 年 11 月- Ignite 大会

+ [增量扩充（预览版）](cognitive-search-incremental-indexing-conceptual.md)将缓存和有状态性添加到了扩充管道，使你能够处理特定的步骤或阶段，且不丢失已处理的内容。 以前，对扩充管道进行任何更改都需要重新生成整个管道。 使用增量扩充时，会保留高开销分析（尤其是图像分析）的输出。

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [文档提取（预览版）](cognitive-search-skill-document-extraction.md)是编制索引期间使用的一项认知技能，用于从技能集中提取文件内容。 以前，文档破解只能在技能集执行之前发生。 使用此项新增技能，还可以在技能集的执行过程中执行此操作。

+ [文本翻译](cognitive-search-skill-text-translation.md)是编制索引期间使用的一项认知技能，它可以评估文本，并为每个记录返回翻译成指定目标语言的文本。

+ [Power BI 模板](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md)可以在 Power BI Desktop 中立即开始可视化和分析知识存储中的扩充内容。 此模板适用于通过[导入数据向导](knowledge-store-create-portal.md)创建的 Azure 表投影。

+ 索引器现在支持 [Azure Data Lake Storage Gen2（预览版）](search-howto-index-azure-data-lake-storage.md)、[Cosmos DB Gremlin API（预览版）](search-howto-index-cosmosdb.md)和 [Cosmos DB Cassandra API（预览版）](search-howto-index-cosmosdb.md)。 可以使用[此表单](https://aka.ms/azure-cognitive-search/indexer-preview)进行注册。 在我们同意你参与预览计划后，你会收到确认电子邮件。

<!-- ### July 2019 -->

<!-- + Generally available in [Azure Government Cloud](https://docs.microsoft.com/azure/azure-government/documentation-government-services-webandmobile#azure-cognitive-search). -->

<a name="new-service-name"></a>

## <a name="new-service-name"></a>新服务名称

Azure 搜索现已重命名为 **Azure 认知搜索**，以反映认知技能和 AI 处理在核心操作中的更广泛用途（但仍为可选）。 API 版本、NuGet 包、命名空间和终结点未有变化。 新的和现有的搜索解决方案不受服务名称更改的影响。

## <a name="service-updates"></a>服务更新

在 Azure 网站上可以找到 Azure 认知搜索的[服务更新通告](https://updates.azure.cn)。