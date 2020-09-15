---
title: 了解 Azure Service Fabric 支持选项
description: 支持的 Azure Service Fabric 群集版本，以及文件支持票证的链接
ms.topic: troubleshooting
origin.date: 08/24/2018
author: rockboyfor
ms.date: 09/14/2020
ms.testscope: yes|no
ms.testdate: 01/13/2020
ms.author: v-yeche
ms.openlocfilehash: d9b6a1a385865175bed4bc8f2c2a680bdb96145d
ms.sourcegitcommit: e1cd3a0b88d3ad962891cf90bac47fee04d5baf5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89655621"
---
# <a name="azure-service-fabric-support-options"></a>Azure Service Fabric 支持选项

我们为用户设置了各种选项，方便其为 Service Fabric 群集（在其上运行应用程序工作负荷）提供相应的支持。 用户需根据所需支持级别以及问题的严重性，选取适当的选项。 

<a name="getlivesitesupportonazure"></a>
## <a name="report-production-issues-or-request-paid-support-for-azure"></a>报告生产问题，或者请求 Azure 付费支持

若要报告部署在 Azure 上的 Service Fabric 群集的问题，请通过 [Azure 门户](https://support.azure.cn/support/support-azure/)开具支持票证。

<!--Duplicated [Azure support portal](https://support.azure.cn/zh-cn/support/support-azure/)-->

了解有关以下方面的详细信息：

- [世纪互联对 Azure 的支持](https://www.azure.cn/support/plans/)。
    
    <!--Not Available on - [Microsoft premier support](https://support.microsoft.com/premier)-->

> [!Note]
> 在青铜级可靠性层级或单节点群集上运行的群集只能用来运行测试性工作负荷。 如果你遇到在青铜可靠性级别或单节点群集上运行的群集的问题，Azure 支持团队会协助你解决问题，但不会进行根本原因分析。 有关更多详细信息，请参阅[群集的可靠性特征](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster)。
>
> 有关生产就绪群集所需满足的要求的详细信息，请参阅[生产就绪情况核对清单](./service-fabric-production-readiness-checklist.md)。

<a name="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>报告生产问题，或者请求独立 Service Fabric 群集的付费支持

若要报告部署在本地或其他云上的 Service Fabric 群集的问题，请通过 [Azure 支持门户](https://support.azure.cn/support/support-azure/)开具专业支持票证。

<!--Not Available on - [Professional Support from Microsoft for on-premises](https://support.microsoft.com/gp/offerprophone?wa=wsignin1.0)-->
<!--Not Available on - [Microsoft premier support](https://support.microsoft.com/premier)-->

<a name="getsupportonissues"></a>
## <a name="report-azure-service-fabric-issues"></a>报告 Azure Service Fabric 问题

我们已设置 GitHub 存储库，用于报告 Service Fabric 问题。  我们还积极监视以下论坛。

### <a name="github-repo"></a>GitHub 存储库 

在 [Service-Fabric-issues git 存储库](https://github.com/Azure/service-fabric-issues)中报告 Azure Service Fabric 问题。 此存储库用于报告和跟踪 Azure Service Fabric 问题，以及进行小型功能请求。 **请勿使用此功能报告实时站点问题**。

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow 和 MSDN 论坛

[StackOverflow 上的 Service Fabric 标记][stackoverflow]和 [MSDN 上的 Service Fabric 论坛][msdn-forum]最适合提问有关平台工作方式以及如何通过该平台完成某些任务的问题。

<!-- Not Available on ### Azure Feedback forum-->

<a name="previewversion"></a>
## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric 预览版本 - 不支持在生产环境中使用

我们会不时发布包含重要功能的版本，希望用户对这些功能提供反馈，这些版本将作为预览版发布。 这些预览版本应仅用于测试目的。 生产群集应始终运行支持的稳定 Service Fabric 版本。 预览版本始终以主版本号和次版本号 255 开头。 例如，如果看到 Service Fabric 版本 255.255.5703.949，则该版本应仅在测试群集中使用且处于预览状态。 这些预览版本也在 [Service Fabric 团队博客](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)上公布，并将提供有关包含的功能的详细信息。
这些预览版本没有付费的支持选项。 使用[报告 Azure Service Fabric 问题](#report-azure-service-fabric-issues)下列出的选项之一提出问题或提供反馈。

## <a name="next-steps"></a>后续步骤

[支持的 Service Fabric 版本](service-fabric-versions.md)

<!--references-->

[Microsoft Q&A question page]: https://docs.microsoft.com/answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric

<!--Not Available on [uservoice-forum]: https://support.azure.cn/support/contact//forums/293901-service-fabric-->
<!--Not Referenced on [acom-docs]: ../service-fabric/index.yml-->
<!--Not Referenced on [sample-repos]: https://aka.ms/servicefabricsamples-->

[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?category=windowsazureplatform

<!-- Update_Description: update meta properties, wording update, update link -->