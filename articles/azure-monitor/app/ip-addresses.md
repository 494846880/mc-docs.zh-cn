---
title: Application Insights 和 Log Analytics 使用的 IP 地址 | Azure Docs
description: Application Insights 所需的服务器防火墙例外
ms.topic: conceptual
author: lingliw
manager: digimobile
origin.date: 12/19/2019
ms.date: 12/30/2019
ms.author: v-lingwu
ms.openlocfilehash: 0070a66367ceffbe88d53f75f4cc7192c89b7bc7
ms.sourcegitcommit: be0a8e909fbce6b1b09699a721268f2fc7eb89de
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2020
ms.locfileid: "84199708"
---
# <a name="ip-addresses-used-by-application-insights-and-log-analytics"></a>Application Insights 和 Log Analytics 使用的 IP 地址
[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 服务使用许多 IP 地址。 如果要监视的应用托管在防火墙后面，可能需要知道这些 IP 地址。

> [!NOTE]
> 尽管这些地址是静态的，但我们可能随时需要更改它们。 除了需要入站防火墙规则的可用性监视和 Webhook 之外，所有 Application Insights 流量都表示出站流量。

> [!TIP]
> 如果使用的是 Azure 网络安全组，则可以使用 Azure [网络服务标记](/virtual-network/service-tags-overview
)管理访问权限。 如果要管理混合/本地资源的访问权限，则可以下载 [JSON 文件](/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files)形式的相应 IP 地址列表（每周更新一次）： 为了涵盖本文中的所有异常，需要使用服务标记：“ActionGroup”、“ApplicationInsightsAvailability”、“AzureMonitor”。

或者，可以通过将 https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/azure-monitor/app/ip-addresses.md.atom 添加到喜欢的 RSS/ATOM 阅读器来订阅此页面作为 RSS 源，以获取有关最新更改的通知。


## <a name="outgoing-ports"></a>传出端口
需要在服务器防火墙中打开某些传出端口，允许 Application Insights SDK 和/或状态监视器将数据发送到门户：

| 目的 | URL | IP | 端口 |
| --- | --- | --- | --- |
| 遥测 |dc.services.visualstudio.com<br/>dc.applicationinsights.chinacloudapi.cn |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221<br/>52.167.221.184<br/>52.169.64.244<br/>40.85.218.175<br/>104.211.92.54<br/>52.175.198.74<br/>51.140.6.23<br/>40.71.12.231<br/>13.69.65.22<br/>13.78.108.165<br/>13.70.72.233<br/>20.44.8.7<br/>13.86.218.248<br/>40.79.138.41<br/>52.231.18.241<br/>13.75.38.7<br/>102.133.162.117<br/>40.73.171.20<br/>102.133.155.50 | 443 |
| 实时指标流 |rt.services.visualstudio.com<br/>rt.applicationinsights.chinacloudapi.cn |23.96.28.38<br/>13.92.40.198 |443 |

## <a name="status-monitor"></a>状态监视器
状态监视器配置 - 仅在进行更改时需要。

| 目的 | URL | IP | 端口 |
| --- | --- | --- | --- |
| 配置 |`management.core.chinacloudapi.cn` | |`443` |
| 配置 |`management.chinacloudapi.cn` | |`443` |
| 配置 |`login.chinacloudapi.cn` | |`443` |
| 配置 |`login.partner.microsoftonline.cn` | |`443` |
| 配置 |`secure.aadcdn.partner.microsoftonline-p.cn` | |`443` |
| 配置 |`auth.gfx.ms` | |`443` |
| 配置 |`login.live.com` | |`443` |
| 安装 | `globalcdn.nuget.org`、`packages.nuget.org`、`api.nuget.org/v3/index.json`、`nuget.org`、`api.nuget.org`、`dc.services.vsallin.net` | |`443` |

## <a name="availability-tests"></a>可用性测试
这是用于运行[可用性 Web 测试](../../azure-monitor/app/monitor-web-app-availability.md)的地址列表。 如果想要对应用运行 Web 测试，但 Web 服务器局限于为特定的客户端提供服务，则必须允许来自可用性测试服务器的传入流量。

### <a name="service-tag"></a>服务标记

如果使用的是 Azure 网络安全组，则只需添加一个“入站端口规则”（选择“服务标记”作为“源”，选择“ApplicationInsightsAvailability”作为“源服务标记”）以允许来自 Application Insights 可用性测试的流量。

>[!div class="mx-imgBorder"]
>![在“设置”下，选择“入站安全规则”，然后选择选项卡顶部的“添加”](./media/ip-addresses/add-inbound-security-rule.png)

## <a name="application-insights-api"></a>Application Insights API
| 目的 | URI | IP | 端口 |
| --- | --- | --- | --- |
| API |`api.applicationinsights.io`<br/>`api1.applicationinsights.io`<br/>`api2.applicationinsights.io`<br/>`api3.applicationinsights.io`<br/>`api4.applicationinsights.io`<br/>`api5.applicationinsights.io`<br/>`dev.applicationinsights.io`<br/>`dev.applicationinsights.microsoft.com`<br/>`dev.aisvc.visualstudio.com`<br/>`www.applicationinsights.io`<br/>`www.applicationinsights.microsoft.com`<br/>`www.aisvc.visualstudio.com`<br/>`api.loganalytics.io`<br/>`*.api.loganalytics.io`<br/>`dev.loganalytics.io`<br>`docs.loganalytics.io`<br/>`www.loganalytics.io` |20.37.52.188 <br/> 20.37.53.231 <br/> 20.36.47.130 <br/> 20.40.124.0 <br/> 20.43.99.158 <br/> 20.43.98.234 <br/> 13.70.127.61 <br/> 40.81.58.225 <br/> 20.40.160.120 <br/> 23.101.225.155 <br/> 52.139.8.32 <br/> 13.88.230.43 <br/> 52.230.224.237 <br/> 52.242.230.209 <br/> 52.173.249.138 <br/> 52.229.218.221 <br/> 52.229.225.6 <br/> 23.100.94.221 <br/> 52.188.179.229 <br/> 52.226.151.250 <br/> 52.150.36.187 <br/> 40.121.135.131 <br/> 20.44.73.196 <br/> 20.41.49.208 <br/> 40.70.23.205 <br/> 20.40.137.91 <br/> 20.40.140.212 <br/> 40.89.189.61 <br/> 52.155.118.97 <br/> 52.156.40.142 <br/> 23.102.66.132 <br/> 52.231.111.52 <br/> 52.231.108.46 <br/> 52.231.64.72 <br/> 52.162.87.50 <br/> 23.100.228.32 <br/> 40.127.144.141 <br/> 52.155.162.238 <br/> 137.116.226.81 <br/> 52.185.215.171 <br/> 40.119.4.128 <br/> 52.171.56.178 <br/> 20.43.152.45 <br/> 20.44.192.217 <br/> 13.67.77.233 <br/> 51.104.255.249 <br/> 51.104.252.13 <br/> 51.143.165.22 <br/> 13.78.151.158 <br/> 51.105.248.23 <br/> 40.74.36.208 <br/> 40.74.59.40 <br/> 13.93.233.49 <br/> 52.247.202.90 |80,443 |
| Azure Pipeline 批注扩展 |aigs1.aisvc.visualstudio.com |动态|443 | 

## <a name="application-insights-analytics"></a>Application Insights Analytics

| 目的 | URI | IP | 端口 |
| --- | --- | --- | --- |
| 分析门户 | analytics.applicationinsights.io | 动态 | 80,443 |
| CDN | applicationanalytics.azureedge.net | 动态 | 80,443 |
| 媒体 CDN | applicationanalyticsmedia.azureedge.net | 动态 | 80,443 |

注意：*.applicationinsights.io 域为 Application Insights 团队所有。

## <a name="log-analytics-portal"></a>Log Analytics 门户

| 目的 | URI | IP | 端口 |
| --- | --- | --- | --- |
| 门户 | portal.loganalytics.io | 动态 | 80,443 |
| CDN | applicationanalytics.azureedge.net | 动态 | 80,443 |

注意：*.loganalytics.io 域由 Log Analytics 团队拥有。

## <a name="application-insights-azure-portal-extension"></a>Application Insights Azure 门户扩展

| 目的 | URI | IP | 端口 |
| --- | --- | --- | --- |
| Application Insights 扩展 | stamp2.app.insightsportal.visualstudio.com | 动态 | 80,443 |
| Application Insights 扩展 CDN | insightsportal-prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | 动态 | 80,443 |

## <a name="application-insights-sdks"></a>Application Insights SDK

| 目的 | URI | IP | 端口 |
| --- | --- | --- | --- |
| Application Insights JS SDK CDN | az416426.vo.msecnd.net | 动态 | 80,443 |
| Application Insights Java SDK | aijavasdk.blob.core.chinacloudapi.cn | 动态 | 80,443 |

## <a name="action-group-webhooks"></a>操作组 Webhook

| 目的 | IP | 端口
| --- | --- | --- |
| 警报 | 13.72.19.232 <br/>13.106.57.181<br/>13.106.54.3<br/>13.106.54.19<br/>13.106.38.142<br/>13.106.38.148<br/>13.106.57.196<br/>13.106.57.197<br/>52.244.68.117<br/>52.244.65.137<br/>52.183.31.0<br/>52.184.145.166<br/>51.4.138.199<br/>51.5.148.86<br/>51.5.149.19 | 443 |

## <a name="profiler"></a>探查器

| 目的 | URI | IP | 端口 |
| --- | --- | --- | --- |
| Agent | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| 门户 | gateway.azureserviceprofiler.net | 动态 | 443
| 存储 | *.core.chinacloudapi.cn | 动态 | 443

## <a name="snapshot-debugger"></a>快照调试器

> [!NOTE]
> 探查器和快照调试器共享同一组 IP 地址。

| 目的 | URI | IP | 端口 |
| --- | --- | --- | --- |
| Agent | ppe.azureserviceprofiler.net<br/>*.ppe.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| 门户 | ppe.gateway.azureserviceprofiler.net | 动态 | 443
| 存储 | *.core.chinacloudapi.cn | 动态 | 443

