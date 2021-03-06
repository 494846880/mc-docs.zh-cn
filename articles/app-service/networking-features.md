---
title: 网络功能
description: 了解 Azure 应用服务中的网络功能，以及需要通过哪些功能来满足安全性或其他功能需求。
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
origin.date: 10/18/2020
ms.date: 11/30/2020
ms.author: v-tawe
ms.custom: seodec18
ms.openlocfilehash: 9101753350f90c3f789b1181d8a3c5b7e78e84a3
ms.sourcegitcommit: f1d0f81918b8c6fca25a125c17ddb80c3a7eda7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96306478"
---
# <a name="app-service-networking-features"></a>应用服务网络功能

可通过多种方式在 Azure 应用服务中部署应用程序。 默认情况下，应用服务中托管的应用可以直接通过 Internet 进行访问，并且只能访问 Internet 托管的终结点。 但对于许多应用程序，需要控制入站和出站网络流量。 应用服务中有几个功能可帮助你满足这些需求。 难点在于，应该使用哪种功能来解决给定的问题。 本文将帮助你根据一些示例用例来确定要使用的功能。

Azure 应用服务有两种主要部署类型： 
- 多租户公共服务在“免费”、“共享”、“基本”、“标准”、“高级”、“PremiumV2”和“PremiumV3”定价 SKU 中托管应用服务计划。 
- 单租户应用服务环境 (ASE) 直接在 Azure 虚拟网络中托管隔离的 SKU 应用服务计划。 

你所用的功能将取决于你是在多租户服务还是 ASE 中操作。 

## <a name="multitenant-app-service-networking-features"></a>多租户应用服务网络功能 

Azure 应用服务是一种分布式系统。 处理传入 HTTP 或 HTTPS 请求的角色称为前端。 托管客户工作负荷的角色称为辅助角色。 应用服务部署中的所有角色均存在于多租户网络中。 由于同一应用服务缩放单元中包含许多不同的客户，因此无法将应用服务网络直接连接到你的网络。 

在不连接网络的情况下，你需要使用相应的功能来处理应用程序通信的各个方面。 处理向应用发出的请求的功能不可用于解决从应用发出调用时出现的问题 。 同理，解决从应用发出的调用的问题的功能，不可用于解决向应用发出请求的问题。  

| 入站功能 | 出站功能 |
|---------------------|-------------------|
| 应用分配的地址 | 混合连接 |
| 访问限制 | 需要网关的 VNet 集成 |

<!-- | Service Endpoints | VNet Integration | -->
<!-- | Private endpoints || -->

除了记录的异常之外，你还可以结合使用所有这些功能。 可以混合使用这些功能来解决问题。

## <a name="use-cases-and-features"></a>用例和功能

对于任何给定的用例，可能都有几种方法可以解决问题。 选择最佳功能有时会超出用例本身。 以下入站用例演示了如何使用应用服务网络功能来解决有关控制传入应用的流量的问题：

| 入站用例 | 功能 |
|---------------------|-------------------|
| 支持应用的基于 IP 的 SSL 需求 | 应用分配的地址 |
| 支持应用的非共享专用入站地址 | 应用分配的地址 |
| 从一组妥善定义的地址限制对应用的访问 | 访问限制 |
| 限制从虚拟网络中的资源访问应用 | ILB ASE |
| 在虚拟网络中的专用 IP 上公开你的应用 | ILB ASE </br> 包含服务终结点的应用程序网关上用于入站通信的专用 IP |
| 使用 Web 应用程序防火墙 (WAF) 保护应用 | 应用程序网关和 ILB ASE </br> 包含服务终结点的应用程序网关 </br> 提供访问限制的 Azure Front Door |
| 对发往不同区域中的应用的流量进行负载均衡 | 具有访问限制的 Azure Front Door | 
| 对同一区域中的流量进行负载均衡 | [包含服务终结点的应用程序网关][appgwserviceendpoints] | 

以下出站用例演示如何使用应用服务网络功能来解决应用的出站访问需求：

| 出站用例 | 功能 |
|---------------------|-------------------|
| 访问位于同一区域的 Azure 虚拟网络中的资源 | VNet 集成 </br> ASE |
| 访问位于不同区域的 Azure 虚拟网络中的资源 | 需要网关的 VNet 集成 </br> ASE 和虚拟网络对等互连 |
| 访问通过服务终结点保护的资源 | ASE |
| 访问未连接到 Azure 的专用网络中的资源 | 混合连接 |
| 跨 Azure ExpressRoute 线路访问资源 | VNet 集成 </br> ASE | 
| 保护来自 Web 应用的出站流量 | VNet 集成和网络安全组 </br> ASE | 
| 路由来自 Web 应用的出站流量 | VNet 集成和路由表 </br> ASE | 


### <a name="default-networking-behavior"></a>默认网络行为

Azure 应用服务缩放单元为每个部署中的多个客户提供支持。 “免费”和“共享”SKU 计划在多租户辅助角色上托管客户工作负荷。 “基本”和更高的计划仅托管专用于一个应用服务计划的客户工作负荷。 如果你有“标准”应用服务计划，该计划中的所有应用将在同一个辅助角色上运行。 如果横向扩展辅助角色，将在应用服务计划中每个实例的新辅助角色上复制该应用服务计划中的所有应用。 

#### <a name="outbound-addresses"></a>出站地址

辅助角色 VM 在很大程度上按应用服务计划划分。 “免费”、“共享”、“基本”、“标准”和“高级”计划均使用相同的辅助角色 VM 类型。 “PremiumV2”计划使用其他 VM 类型。 “PremiumV3”还使用其他 VM 类型。 更改 VM 系列时，将获得一组不同的出站地址。 如果从“标准”扩展到“PremiumV2”，则出站地址将会更改。 如果从“PremiumV2”扩展到“PremiumV3”，出站地址会更改。 在一些较旧的缩放单元中，当从“标准”扩展到“PremiumV2”时，入站和出站地址都将更改。 

有多个地址可用于出站调用。 应用的属性中列出了应用进行出站调用所使用的出站地址。 应用服务部署中同一辅助角色 VM 系列上运行的所有应用将共享这些地址。 如果要查看应用可能会在缩放单元中使用的所有地址，可通过名为 `possibleOutboundAddresses` 的属性查看，其中列出了这些地址。 

![显示应用属性的屏幕截图。](media/networking-features/app-properties.png)

应用服务包含许多用于管理服务的终结点。  这些地址发布在单独的文档中，同时包含在 `AppServiceManagement` IP 服务标记中。 仅在需要允许此类流量的应用服务环境中使用 `AppServiceManagement` 标记。 将在 `AppService` IP 服务标记中跟踪应用服务的入站地址。 没有任何 IP 服务标记包含应用服务使用的出站地址。 

![显示应用服务入站和出站流量的关系图。](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>应用分配的地址 

应用分配的地址功能是基于 IP 的 SSL 功能的一个分支。 可以通过使用应用设置 SSL 来访问它。 此功能可用于基于 IP 的 SSL 调用。 你还可以使用它来为你的应用分配唯一的地址。 

![显示应用分配的地址的关系图。](media/networking-features/app-assigned-address.png)

使用应用分配的地址时，流量仍会流经相同的前端角色，这些角色处理所有传入应用服务缩放单元的流量。 但分配给应用的地址仅供该应用使用。 此功能的用例：

* 支持应用的基于 IP 的 SSL 需求。
* 为应用设置未共享的专用地址。

若要了解如何在应用上设置地址，请参阅[在 Azure 应用服务中添加 TLS/SSL 证书][appassignedaddress]。 

### <a name="access-restrictions"></a>访问限制 

访问限制使你可以筛选“入站”请求。 筛选操作在前端角色上发生，这些前端角色位于运行应用的辅助角色的上游。 由于前端角色位于辅助角色的上游，因此你可将访问限制视为网络级的应用保护机制。 

使用该功能，你可以生成按优先顺序评估的允许和拒绝规则列表。 它类似于 Azure 网络中的网络安全组 (NSG) 功能。 可以在 ASE 或多租户服务中使用此功能。 将其与 ILB ASE 或专用终结点一起使用时，可以限制从专用地址块进行访问。
> [!NOTE]
> 每个应用最多可以配置 512 个访问限制规则。 

![说明访问限制的关系图。](media/networking-features/access-restrictions.png)

#### <a name="ip-based-access-restriction-rules"></a>基于 IP 的访问限制规则

要限制可用于访问应用的 IP 地址时，基于 IP 的访问限制功能非常有用。 IPv4 和 IPv6 均受支持。 此功能的一些用例：
* 从一组定义完善的地址限制对应用的访问。 
* 限制对通过负载均衡服务（例如 Azure Front Door）进入的流量的访问。 若要将入站流量锁定为 Azure Front Door，请创建规则以允许来自 147.243.0.0/16 和 2a01:111:2050::/44 的流量。 

若要了解如何启用此功能，请参阅[配置访问限制][iprestrictions]。

<!--#### Access restriction rules based on service endpoints -->

<!--### Private Endpoint-->

### <a name="hybrid-connections"></a>混合连接

应用可以通过应用服务混合连接向指定的 TCP 终结点发出 *出站* 调用。 终结点可以位于本地、虚拟网络中，或者允许通过端口 443 向 Azure 发出出站流量的任何位置。 若要使用此功能，需要在 Windows Server 2012 或更高版本的主机上安装名为混合连接管理器的中继代理。 混合连接管理器需要能够通过端口 443 访问 Azure 中继。 可以从门户中的应用服务混合连接 UI 下载混合连接管理器。 

![显示混合连接网络流的关系图。](media/networking-features/hybrid-connections.png)

应用服务混合连接构建在 Azure 中继混合连接功能的基础之上。 应用服务使用一种特殊形式的功能，该功能仅支持从应用向 TCP 主机和端口发出出站调用。 只需在安装混合连接管理器的主机上解析此主机和端口。 

当应用服务中的应用在混合连接中定义的主机和端口上执行 DNS 查找时，流量将自动重定向，以流经混合连接并传出混合连接管理器。 若要了解详细信息，请参阅[应用服务混合连接][hybridconn]。

此功能通常用于：

* 通过 VPN 或 ExpressRoute 访问未连接到 Azure 的专用网络中的资源。
* 支持将本地应用迁移到应用服务，而无需移动支持数据库。  
* 根据混合连接配置为单个主机和端口提供安全性得到改进的访问。 大多数网络功能都开放了对网络的访问权限。 使用混合连接，只能访问单个主机和端口。
* 实现其他出站连接方法无法实现的方案。
* 以允许应用轻松使用本地资源的方式在应用服务中执行开发。 

由于使用此功能可以访问本地资源且无需在入站防火墙中开放额外的端口，因此它非常受开发人员的青睐。 其他出站应用服务网络功能与 Azure 虚拟网络相关。 混合连接不依赖于通过虚拟网络。 它可用于更广泛的网络需求。 

请注意，应用服务混合连接并不知道你基于它所执行的操作。 因此，你可以使用它来访问数据库、Web 服务或大型机上的任意 TCP 套接字。 此功能在本质上是通过隧道传输 TCP 数据包。 

混合连接在开发活动中很常用，但也用于生产应用程序中。 它非常适用于访问 Web 服务或数据库，但不适用于需要创建许多连接的情况。 

### <a name="gateway-required-vnet-integration"></a>需要网关的 VNet 集成 

需要网关的应用服务 VNet 集成使你的应用可以向 Azure 虚拟网络发出出站请求。 该功能的工作原理是通过点到站点 VPN 将运行应用的主机连接到虚拟网络中的虚拟网络网关。 配置该功能时，应用将获取分配给每个实例的点到站点地址之一。 使用此功能可以访问位于任何区域的经典或 Azure 资源管理器虚拟网络中的资源。 

![说明网关所需的 VNet 集成的关系图。](media/networking-features/gw-vnet-integration.png)

此功能解决了访问其他虚拟网络中资源的问题。 甚至可以使用它通过虚拟网络连接到其他虚拟网络或本地网络。 它不适用于 ExpressRoute 连接的虚拟网络，但它确实适用于站点到站点 VPN 连接的网络。 通常，不适合从应用服务环境 (ASE) 中的应用使用此功能，因为 ASE 已在虚拟网络中。 此功能的用例：

* 访问 Azure 虚拟网络中专用 IP 上的资源。 
* 访问本地资源（如果存在站点到站点 VPN）。 
* 访问对等互连虚拟网络中的资源。 

启用此功能后，应用将使用配置了目标虚拟网络的 DNS 服务器。 有关此功能的详细信息，请参阅[应用服务 VNet 集成][vnetintegrationp2s]。 

<!-- ### VNet Integration

Gateway-required VNet Integration is useful, but it doesn't solve the problem of accessing resources across ExpressRoute. On top of needing to reach across ExpressRoute connections, there's a need for apps to be able to make calls to services secured by service endpoint. Another VNet Integration capability can meet these needs. 

The new VNet Integration feature enables you to place the back end of your app in a subnet in a Resource Manager virtual network in the same region as your app. This feature isn't available from an App Service Environment, which is already in a virtual network. Use cases for this feature:

* Access resources in Resource Manager virtual networks in the same region.
* Access resources that are secured with service endpoints. 
* Access resources that are accessible across ExpressRoute or VPN connections.
* Help to secure all outbound traffic. 
* Force tunnel all outbound traffic. 

![VNet Integration](media/networking-features/vnet-integration.png)

To learn more, see [App Service VNet Integration][vnetintegration]. -->

### <a name="app-service-environment"></a>应用服务环境 

应用服务环境 (ASE) 是在虚拟网络中运行的 Azure 应用服务的单租户部署。 某些情况下，此功能：

* 访问虚拟网络中的资源。
* 跨 ExpressRoute 访问资源。
* 使用虚拟网络中的专用地址公开应用。 
* 跨服务终结点访问资源。 

使用 ASE 时，无需使用 VNet 集成或服务终结点等功能，因为 ASE 已在虚拟网络中。 若要通过服务终结点访问 SQL 或 Azure 存储等资源，请在 ASE 子网中启用服务终结点。 如果要访问虚拟网络中的资源，则无需进行任何其他配置。 跨 ExpressRoute 访问资源时，你已位于虚拟网络中，因此无需在 ASE 或其中的应用中进行任何配置。 

由于可以在专用 IP 地址上公开 ILB ASE 中的应用，因此，可以轻松添加 WAF 设备，以便只在 Internet 中公开所需的应用，并帮助其余应用保持安全状态。 此功能可帮助简化多层应用程序的开发。 

目前，某些功能无法通过多租户服务实现，但是可以在 ASE 中实现。 下面是一些示例：

* 在专用 IP 地址上公开应用。
* 使用应用不提供的网络控制来帮助保护所有出站流量。
* 在单租户服务中托管应用。 
* 纵向扩展到多租户服务无法支持的更多实例。 
* 加载专用 CA 客户端证书，供应用通过专用 CA 保护的终结点使用。
* 在无法在应用级别禁用功能的情况下，跨系统中托管的所有应用强制实施 TLS 1.1。 
* 为 ASE 中所有不与客户共享的应用提供专用的出站地址。 

![说明虚拟网络中 ASE 的关系图。](media/networking-features/app-service-environment.png)

ASE 提供最佳的隔离和专用应用托管，但它确实涉及了一些管理难题。 在使用可运行的 ASE 之前需要考虑的事项包括：

 * ASE 在虚拟网络内运行，但它在虚拟网络外具有依赖项。 必须允许这些依赖项。 有关详细信息，请参阅[应用服务环境的网络注意事项][networkinfo]。
 * ASE 无法像多租户服务那样即时缩放。 需要预测缩放需求，而不要被动地进行缩放。 
 * ASE 具有较高的前期成本。 若要充分利用 ASE，应该计划将多个工作负荷放入一个 ASE，而不要将它用于不太重要的工作。
 * ASE 中的应用无法选择性地限制对 ASE 中某些应用的访问，但可以限制对其他应用的访问。
 * ASE 位于子网中，任何网络规则将应用到传入和传出该 ASE 的所有流量。 如果只想要为一个应用分配入站流量规则，请使用访问限制。 

## <a name="combining-features"></a>组合功能 

可以结合使用多租户服务的所述功能来解决更复杂的用例。 此处描述了两个较常见用例，但它们只是示例。 了解每项功能的作用后，便几乎可以满足所有的系统体系结构需求。

### <a name="place-an-app-into-a-virtual-network"></a>将应用放入虚拟网络

你可能想知道如何将应用放入虚拟网络。 如果将应用放入虚拟网络，则应用的入站和出站终结点将位于虚拟网络内。 ASE 是解决此问题的最佳方法。 但你可以通过组合功能来满足多租户服务中的大多数需求。 例如，可通过以下方式使用专用入站和出站地址托管仅限 Intranet 的应用程序：

<!-- * Securing inbound traffic to your app with service endpoints  -->

* 使用服务终结点保护应用的入站流量。 
* 使用新的 VNet 集成功能，以使应用的后端位于你的虚拟网络中。 

此部署样式不会针对发往 Internet 的出站流量提供专用地址，也无法让你锁定来自应用的所有出站流量。 它将主要实现只能通过 ASE 实现的目的。 

### <a name="create-multitier-applications"></a>创建多层应用程序

多层应用程序是只能从前端层访问其中的 API 后端应用的应用程序。 可通过两种方法创建多层应用程序。 这两种方法都是首先使用 VNet 集成将前端 Web 应用连接到虚拟网络中的子网。 这样做可以使 Web 应用调用虚拟网络。 前端应用连接到虚拟网络后，需要确定如何锁定对 API 应用程序的访问。 可以执行以下操作：

* 在同一 ILB ASE 中托管前端和 API 应用，并使用应用程序网关将前端应用公开到 Internet。
* 在多租户服务中托管前端，在 ILB ASE 中托管后端。
* 在多租户服务中托管前端和 API 应用。

如果同时托管多层应用程序的前端和 API 应用，则可以：

- 使用虚拟网络中的专用终结点公开 API 应用程序：

  ![说明如何在两层应用中使用专用终结点的关系图。](media/networking-features/multi-tier-app-private-endpoint.png)

- 使用服务终结点来确保发往 API 应用的入站流量仅来自前端 Web 应用使用的子网：

  ![说明如何使用服务终结点来帮助保护应用的关系图。](media/networking-features/multi-tier-app.png)

下面是帮助你决定使用哪种方法的一些注意事项：

* 使用服务终结点时，只需将 API 应用的流量安全地传输到集成子网。 这有助于保护 API 应用的安全，但前端应用的数据仍可能外泄到应用服务中的其他应用。
* 使用专用终结点时，有两个子网在运行，这会增加复杂性。 此外，专用终结点是顶级资源，会增加管理开销。 使用专用终结点的好处是不存在数据外泄的可能性。 

任何一种方法都可用于多个前端。 在较小的规模上，服务终结点更易于使用，因为直接在前端集成子网上为 API 应用启用服务终结点即可。 当你添加更多前端应用时，需要调整每个 API 应用以包含集成子网的服务终结点。 使用专用终结点时，会更加复杂，但在设置专用终结点之后，无需对 API 应用进行任何更改。 

### <a name="line-of-business-applications"></a>业务线应用程序

业务线 (LOB) 应用程序通常是不面向 Internet 公开（无法通过 Internet 进行访问）的内部应用程序。 这些应用程序从公司网络内部调用，可能实行严格的访问控制。 如果使用 ILB ASE，可以轻松托管业务线应用程序。 如果使用多租户服务，可以使用专用终结点，也可以使用与应用程序网关合并的服务终结点。 将服务终结点与应用程序网关结合使用而不是使用专用终结点有两个原因：
* 你需要为 LOB 应用配置 WAF 保护。
* 你需要对 LOB 应用的多个实例进行负载均衡。

如果这两种需求都不适用，那么最好使用专用终结点。 使用应用服务中提供的专用终结点，你可以在虚拟网络中的专用地址上公开你的应用。 可跨 ExpressRoute 和 VPN 连接来访问你放置在虚拟网络中的专用终结点。 

配置专用终结点将在专用地址上公开你的应用，但你需要配置 DNS 以便从本地访问该地址。 要正常使用此配置，需要将包含专用终结点的 Azure DNS 专用区域转发到本地 DNS 服务器。 Azure DNS 专用区域不支持区域转发，但可以使用可实现该用途的 DNS 服务器来支持区域转发。 [DNS 转发器](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder/)模板支持更轻松地将 Azure DNS 专用区域转发到本地 DNS 服务器。

## <a name="app-service-ports"></a>应用服务端口

如果扫描应用服务，将会发现为入站连接公开的几个端口。 在多租户服务中无法阻止或控制对这些端口的访问。 下面是公开端口的列表：

| 使用 | 端口 |
|----------|-------------|
|  HTTP/HTTPS  | 80、443 |
|  管理 | 454、455 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Visual Studio 远程调试  |  4020, 4022, 4024 |
|  Web 部署服务 | 8172 |
|  基础结构使用 | 7654、1221 |

<!--Links-->

[appassignedaddress]: ./configure-ssl-certificate.md
[iprestrictions]: ./app-service-ip-restrictions.md
[serviceendpoints]: ./app-service-ip-restrictions.md
[hybridconn]: ./app-service-hybrid-connections.md
[vnetintegrationp2s]: ./web-sites-integrate-with-vnet.md
[vnetintegration]: ./web-sites-integrate-with-vnet.md
[networkinfo]: ./environment/network-info.md
[appgwserviceendpoints]: ./networking/app-gateway-with-service-endpoints.md
[privateendpoints]: ./networking/private-endpoint.md
