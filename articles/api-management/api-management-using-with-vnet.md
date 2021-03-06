---
title: 如何将 Azure API 管理与虚拟网络配合使用
description: 了解如何在 Azure API 管理中设置与虚拟网络的连接并通过它访问 Web 服务。
services: api-management
documentationcenter: ''
author: Johnnytechn
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/01/2020
ms.author: v-johya
ms.custom: references_regions
ms.openlocfilehash: e2ca6c6c52f5b4cea1345e0ea9ce930ca86df6c8
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96432611"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>如何将 Azure API 管理与虚拟网络配合使用
使用 Azure 虚拟网络 (VNET) 可将你的任何 Azure 资源置于可以控制其访问权限但无法通过 Internet 路由的网络中。 然后，可以使用各种 VPN 技术将这些网络连接到本地网络。 若要了解有关 Azure 虚拟网络的详细信息，请先了解以下信息：[Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)。

可以将 Azure API 管理部署到虚拟网络 (VNET) 内部，以便它可以访问该网络中的后端服务。 可以将开发人员门户和 API 网关配置为可以从 Internet 访问或只能在虚拟网络内访问。

> [!NOTE]
> API 导入文档 URL 必须托管在可公开访问的 Internet 地址上。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>先决条件

若要执行本文中所述的步骤，必须具有：

+ 一个有效的 Azure 订阅。

    如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

+ 一个 APIM 实例。 有关详细信息，请参阅[创建 Azure API 管理实例](get-started-create-service-instance.md)。

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>启用 VNET 连接

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>使用 Azure 门户启用 VNET 连接

1. 转到 [Azure 门户](https://portal.azure.cn)，查找 API 管理实例。 搜索并选择“API 管理服务”。

2. 选择你的 API 管理实例。

3. 选择“虚拟网络”。
4. 配置要在虚拟网络内部署的 API 管理实例。

    ![API 管理的虚拟网络菜单][api-management-using-vnet-menu]
5. 选择所需的访问类型：

    * **OFF**：这是默认值。 API 管理未部署到虚拟网络中。

    * **外部**：可以通过外部负载均衡器从公共 Internet 访问 API 管理网关和开发人员门户。 网关可以访问虚拟网络中的资源。

        ![公共对等互连][api-management-vnet-public]

    * **内部**：只能通过内部负载均衡器从虚拟网络内部访问 API 管理网关和开发人员门户。 网关可以访问虚拟网络中的资源。

        ![专用对等互连][api-management-vnet-private]

6. 如果选择了“外部”或“内部”，则会看到一个列表，其中包含预配了 API 管理服务的所有区域。  选择一个 **位置**，然后选取其 **虚拟网络** 和 **子网**。 虚拟网络列表中填充了所配置区域中设置的 Azure 订阅中可用的经典虚拟网络和资源管理器虚拟网络。

    > [!IMPORTANT]
    > 将 Azure API 管理实例部署到 资源管理器 VNET 时，该服务必须位于一个除了 Azure API 管理实例之外不包含其他资源的专用子网中。 如果尝试将 Azure API 管理实例部署到包含其他资源的资源管理器 VNET 子网，则部署会失败。

    然后，选择“应用”。 API 管理实例的“虚拟网络”页通过新的虚拟网络和子网选项进行更新。

    ![选择 VPN][api-management-setup-vpn-select]

7. 在顶部导航栏中选择“保存”，然后选择“应用网络配置”。

> [!NOTE]
> 每次启用或禁用 VNET 时，API 管理实例的 VIP 地址都会更改。
> 当 API 管理从 **外部** 移到 **内部** 时，VIP 地址也会更改，反之亦然。
>

> [!IMPORTANT]
> 如果从 VNET 中删除 API 管理或更改在其中部署的 API 管理，则之前使用的 VNET 可最多 6 小时保持锁定状态。 在此期间，无法删除该 VNET 或向其部署新资源。 对于使用 api-version 2018-01-01 及更早版本的客户端，此行为是正确的。 使用 api-version 2019-01-01 及更高版本的客户端，在删除关联的 API 管理服务后，将释放 VNET。

## <a name="deploy-api-management-into-external-vnet"></a><a name="deploy-apim-external-vnet"> </a>将 API 管理部署到外部 VNET

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-external-vnet%2Fazuredeploy.json)

* **在 VNET 内创建 API 管理服务**：使用 cmdlet [New-AzApiManagement](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagement) 在 VNET 内创建 Azure API 管理服务。

* **在 VNET 内部署现有 API 管理服务**：使用 cmdlet [Update-AzApiManagementRegion](https://docs.microsoft.com/powershell/module/az.apimanagement/update-azapimanagementregion) 将现有 Azure API 管理服务移到虚拟网络内。

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>连接到虚拟网络中托管的 Web 服务
将 API 管理服务连接到 VNET 后，访问 VNET 中的后端服务与访问公共服务无异。 在创建新的 API 或编辑现有 API 时，只需将 Web 服务的本地 IP 地址或主机名（如果为 VNET 配置了 DNS 服务器）键入到“Web 服务 URL”字段。

![从 VPN 添加 API][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"></a>常见网络配置问题
下面是将 API 管理服务部署到虚拟网络时可能会发生的常见错误配置问题的列表。

* **自定义 DNS 服务器设置**：API 管理服务依赖于多项 Azure 服务。 当 API 管理托管在包含自定义 DNS 服务器的 VNET 中时，API 管理需要解析这些 Azure 服务的主机名。 请根据[此指南](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)进行自定义 DNS 设置。 有关参考信息，请参阅下面的端口表和其他网络要求。

> [!IMPORTANT]
> 如果计划对 VNET 使用自定义 DNS 服务器，应在向其部署 API 管理服务 **之前** 完成该设置。 否则，需要在每次通过运行[应用网络配置操作](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/apimanagementservice/applynetworkconfigurationupdates)更改 DNS 服务器时更新 API 管理服务

* **API 管理所需的端口**：可以使用 [网络安全组][Network Security Group]控制其中部署了 API 管理的子网的入站和出站流量。 如果其中的任一端口不可用，API 管理可能无法正常工作且不可访问。 将 API 管理与 VNET 配合使用时，另一个常见的错误配置问题是阻止了这些端口中的一个或多个。

<a name="required-ports"> </a>在 VNET 中托管 API 管理服务实例时，将使用下表中的端口。

| 源/目标端口 | 方向          | 传输协议 |   [服务标记](../virtual-network/network-security-groups-overview.md#service-tags) <br> 源/目标   | 目的 (\*)                                                 | 虚拟网络类型 |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | 入站            | TCP                | INTERNET/VIRTUAL_NETWORK            | 客户端与 API 管理的通信                      | 外部             |
| * / 3443                     | 入站            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Azure 门户和 PowerShell 的管理终结点         | 外部和内部  |
| * / 443                  | 出站           | TCP                | VIRTUAL_NETWORK / Storage             | **与 Azure 存储的依赖关系**                             | 外部和内部  |
| * / 443                  | 出站           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Azure Active Directory](api-management-howto-aad.md)（如果适用）                   | 外部和内部  |
| * / 1433                     | 出站           | TCP                | VIRTUAL_NETWORK / SQL                 | **访问 Azure SQL 终结点**                           | 外部和内部  |
| * / 5671, 5672, 443          | 出站           | TCP                | VIRTUAL_NETWORK / EventHub            | [事件中心策略日志](api-management-howto-log-event-hubs.md)和监视代理的依赖项 | 外部和内部  |
| * / 445                      | 出站           | TCP                | VIRTUAL_NETWORK / Storage             | 与适用于 [GIT](api-management-configuration-repository-git.md) 的 Azure 文件共享的依赖关系                      | 外部和内部  |
| * / 443, 12000                     | 出站           | TCP                | VIRTUAL_NETWORK / AzureCloud            | 运行状况和监视扩展         | 外部和内部  |
| * / 1886、443                     | 出站           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | 发布[诊断日志和指标](api-management-howto-use-azure-monitor.md)、[资源运行状况](../service-health/resource-health-overview.md)和 [Application Insights](api-management-howto-app-insights.md)                   | 外部和内部  |
| * / 25、587、25028                       | 出站           | TCP                | VIRTUAL_NETWORK/INTERNET            | 连接到 SMTP 中继以发送电子邮件                    | 外部和内部  |
| * / 6381 - 6383              | 入站和出站 | TCP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | 访问 Redis 服务以获取计算机之间的[缓存](api-management-caching-policies.md)策略         | 外部和内部  |
| * / 4290              | 入站和出站 | UDP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | 同步用于计算机之间的[速率限制](api-management-access-restriction-policies.md#LimitCallRateByKey)策略的计数器         | 外部和内部  |
| * / *                        | 入站            | TCP                | AZURE_LOAD_BALANCER/VIRTUAL_NETWORK | Azure 基础结构负载均衡器                          | 外部和内部  |

>[!IMPORTANT]
> “用途”为 **粗体** 的端口是成功部署 API 管理服务所必需的。 不过，如果阻止其他端口，将导致使用和监视运行中服务以及提供承诺的 SLA 的能力降级。

+ **TLS 功能**：若要启用 TLS/SSL 证书链生成和验证，API 管理服务需要到 ocsp.msocsp.com、mscrl.microsoft.com 和 crl.microsoft.com 的出站网络连接。 如果上传到 API 管理的任何证书包含指向 CA 根的完整链，则此依赖项不是必需的。

+ **DNS 访问**：需要端口 53 上的出站访问权限才能与 DNS 服务器通信。 如果 VPN 网关的另一端存在自定义 DNS 服务器，则该 DNS 服务器必须可从承载 API 管理的子网访问。

+ **指标和运行状况监视**：到 Azure 监视终结点的出站网络连接，可在以下域中解析。 如表所示，这些 URL 在 AzureMonitor 服务标记下表示，用于网络安全组。

    | Azure 环境 | 终结点                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure 中国世纪互联     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>global.prod.microsoftmetrics.com（新增）</li><li>global.metrics.nsatc.net（将弃用）</li><li>shoebox2.prod.microsoftmetrics.com（新增）</li><li>shoebox2.metrics.nsatc.net（将弃用）</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com（新增）</li><li>prod3.metrics.nsatc.net（将弃用）</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.cn</li></ul>                                                                                                                                                                                                                                                |

  >[!IMPORTANT]
  > 以上群集中 dns 区域从 .nsatc.net 到 .microsoftmetrics.com 的更改主要是 DNS 更改 。 群集的 IP 地址将不会更改。

+ **区域服务标记**：允许与存储、SQL 和事件中心服务标记建立出站连接的 NSG 规则可以使用与 API 管理实例所在区域相对应的那些标记的区域版本（例如，“中国北部”区域中 API 管理实例的对应区域版本为 Storage.ChinaNorth）。 在多区域部署中，每个区域中的 NSG 应该允许发送到该区域或主要区域的服务标记的流量。

+ **SMTP 中继**：在主机 `smtpi-co1.msn.com`、`smtpi-ch1.msn.com`、`smtpi-db3.msn.com`、`smtpi-sin.msn.com`、`ies.global.microsoft.com` 下解析的 SMTP 中继的出站网络连接

+ **开发人员门户 CAPTCHA**：在主机 `client.hip.live.com` 和 `partner.hip.live.com` 下解析的开发人员门户 CAPTCHA 的出站网络连接。

+ **Azure 门户诊断**：若要在从虚拟网络内部使用 API 管理扩展时从 Azure 门户启用诊断日志流，需要允许在端口 443 上对 `dc.services.visualstudio.com` 进行出站访问。 这有助于排查使用扩展时可能遇到的问题。

+ **Azure 负载均衡器**：`Developer` SKU 不要求允许来自服务标记 `AZURE_LOAD_BALANCER` 的入站请求，因为我们只在其后部署一个计算单元。 但当扩展到更高级的 SKU（如 `Premium`）时，来自 [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) 的入站请求变得至关重要，因为负载均衡器的运行状况探测失败将导致部署失败。

+ **Application Insights**：如果在 API 管理上启用了 [Azure Application Insights](api-management-howto-app-insights.md) 监视，则需要允许从虚拟网络到 [遥测终结点](../azure-monitor/app/ip-addresses.md#outgoing-ports)的出站连接。 

+ **使用 Express Route 或网络虚拟设备强制隧道流量发往本地防火墙**：客户的常用配置是定义自己的默认路由 (0.0.0.0/0)，强制来自 API 管理所委托子网的所有流量流经本地防火墙或流向网络虚拟设备。 此流量流一定会中断与 Azure API 管理的连接，因为出站流量会在本地被阻止，或者通过“网络地址转换”功能发送到不再与各种 Azure 终结点一起工作的一组无法识别的地址。 此解决方案要求你执行多项操作：

  * 在部署 API 管理服务的子网上启用服务终结点。 需为 Azure SQL、Azure 存储、Azure 事件中心和 Azure 服务总线启用[服务终结点][ServiceEndpoints]。 直接从 API 管理委托的子网启用这些服务的终结点可以让它们使用 Azure 主干网络，为服务流量提供优化的路由。 如果将服务终结点与强制隧道 API 管理配合使用，则不会将上述 Azure 服务流量进行强制隧道传输。 其他 API 管理服务依赖项流量会通过强制隧道重定向，不能丢失，否则 API 管理服务会功能失常。
    
  * 所有控制平面流量（从 Internet 到 API 管理服务的管理终结点）都会通过特定的一组由 API 管理托管的入站 IP 进行路由。 当流量被强制进行隧道传输时，响应不会对称地映射回这些入站源 IP。 为了克服此限制，我们需要添加以下用户定义的路由 ([UDR][UDRs])，通过将这些主机路由的目标设置为“Internet”来将流量传回 Azure。 用于控制平面流量的入站 IP 集是记录在案的[控制平面 IP 地址](#control-plane-ips)

  * 对于被强制进行隧道传输的其他 API 管理服务依赖项，应该有一种方法来解析主机名并访问该终结点。 其中包括：
      - 指标和运行状况监视
      - Azure 门户诊断
      - SMTP 中继
      - 开发人员门户验证码

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>疑难解答
* **初始设置**：如果在某个子网中初次部署 API 管理服务未成功，建议首先在同一子网中部署一个虚拟机。 接下来，在虚拟机中部署远程桌面，并验证是否与 Azure 订阅中的以下每个资源建立了连接
    * Azure 存储 Blob
    * Azure SQL 数据库
    * Azure 存储表

  > [!IMPORTANT]
  > 在验证连接后，在将 API 管理部署到子网中之前，请确保删除子网中部署的所有资源。

* **增量更新**：对网络进行更改时，请参阅 [NetworkStatus API](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/networkstatus)，验证 API 管理服务是否尚未丧失对所依赖的任何关键资源的访问权限。 连接状态应每 15 分钟更新一次。

* **资源导航链接**：部署到资源管理器样式的 VNET 子网中时，API 管理会通过创建一个资源导航链接来保留子网。 如果子网已包含来自其他提供程序的资源，则部署将 **失败**。 类似地，将 API 管理服务移动到其他子网中或删除它时，将会删除该资源导航链接。

## <a name="subnet-size-requirement"></a><a name="subnet-size"> </a> 子网大小要求
Azure 会保留每个子网中的某些 IP 地址，不可以使用这些地址。 子网的第一个和最后一个 IP 地址仅为协议一致性而保留，其他三个地址用于 Azure 服务。 有关详细信息，请参阅[使用这些子网中的 IP 地址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

除了 Azure VNET 基础结构使用的 IP 地址外，子网中的每个 Api 管理实例还会针对开发人员 SKU 使用每个高级 SKU 单位两个 IP 地址和一个单 IP 地址。 每个实例为外部负载均衡器保留一个附加的 IP 地址。 部署到内部虚拟网络时，它需要对内部负载均衡器使用额外的 IP 地址。

根据上面给定的计算，可以在其中部署 API 管理的子网的最小大小为 /29，它提供 3 个可用的 IP 地址。

API 管理的每个额外缩放单元都需要另外两个 IP 地址。

## <a name="routing"></a><a name="routing"> </a> 路由
+ 负载均衡公共 IP 地址 (VIP) 也将保留，用于访问所有服务终结点。
+ 将使用子网 IP 范围中的一个 IP 地址 (DIP) 来访问 vnet 中的资源，并使用一个公共 IP 地址 (VIP) 来访问 vnet 外部的资源。
+ 可以在 Azure 门户中的“概述/概要”边栏选项卡上找到负载均衡公共 IP 地址。

## <a name="limitations"></a><a name="limitations"> </a>限制
* 包含 API 管理实例的子网不能包含任何其他 Azure 资源类型。
* 子网和 API 管理服务必须在同一个订阅中。
* 包含 API 管理实例的子网不能在订阅之间移动。
* 对于在内部虚拟网络模式下配置的多区域 API 管理部署，用户负责管理多个区域之间的负载均衡，因为路由归他们拥有。
* 由于平台限制，从另一个区域中的全局对等互连 VNET 中的资源到内部模式下的 API 管理服务的连接将不起作用。 有关详细信息，请参阅[一个虚拟网络中的资源无法与对等互连虚拟网络中 Azure 内部负载均衡器通信](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"> </a> 控制平面 IP 地址

IP 地址由 **Azure 环境** 划分。 允许入站请求时，标记为 Global 的 IP 地址必须与区域特定的 IP 地址一起获得允许。

| **Azure 环境**|   **区域**|  **IP 地址**|
|-----------------|-------------------------|---------------|
| Azure 中国世纪互联| 中国北部（全球）| 139.217.51.16|
| Azure 中国世纪互联| 中国东部（全球）| 139.217.171.176|
| Azure 中国世纪互联| 中国北部| 40.125.137.220|
| Azure 中国世纪互联| 中国东部| 40.126.120.30|
| Azure 中国世纪互联| 中国北部 2| 40.73.41.178|
| Azure 中国世纪互联| 中国东部 2| 40.73.104.4|

## <a name="related-content"></a><a name="related-content"> </a>相关内容
* [使用 Vpn 网关将虚拟网络连接到后端](../vpn-gateway/design.md#s2smulti)
* [通过不同的部署模型连接虚拟网络](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [如何使用 API 检查器跟踪 Azure API 管理中的调用](api-management-howto-api-inspector.md)
* [虚拟网络常见问题解答](../virtual-network/virtual-networks-faq.md)
* [服务标记](../virtual-network/network-security-groups-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/network-security-groups-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags

