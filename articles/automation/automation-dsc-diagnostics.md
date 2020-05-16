---
title: 将 Azure Automation State Configuration 报表数据转发到 Azure Monitor 日志
description: 本文演示如何将 Desired State Configuration (DSC) 报表数据从 Azure Automation State Configuration 发送到 Azure Monitor 日志，以便为用户提供附加见解和管理信息。
services: automation
ms.service: automation
ms.subservice: dsc
author: WenJason
ms.author: v-jay
origin.date: 11/06/2018
ms.date: 05/11/2020
ms.topic: conceptual
manager: digimobile
ms.openlocfilehash: f4645302e92f119cb07754c1f707a5eadf4004dc
ms.sourcegitcommit: 7443ff038ea8afe511f7419d9c550d27fb642246
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "83001644"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>将 Azure Automation State Configuration 报表数据转发到 Azure Monitor 日志

Azure Automation State Configuration 会将节点状态数据保留 30 天。 如果希望节点状态数据能够保留更长的时间，则可将其发送到 Log Analytics 工作区。 节点和节点配置中的单个 DSC 资源的符合性状态可以通过 Azure 门户或 PowerShell 查看。 

Azure Monitor 日志可以更直观地显示 Automation State Configuration 数据的运行情况，并且有助于更快地解决事件。 可以使用 Azure Monitor 日志进行以下操作：

- 获取托管节点和单个资源的符合性信息。
- 基于符合性状态触发电子邮件或警报。
- 编写跨托管节点的高级查询。
- 关联自动化帐户的符合性状态
- 使用自定义视图和搜索查询直观地显示 Runbook 结果、Runbook 作业状态，以及其他相关的关键指标。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>先决条件

若要开始将 Automation State Configuration 报表发送到 Azure Monitor 日志，需要准备：

- 2016 年 11 月或之后发布的 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) (v2.3.0) 版本。
- 一个 Azure 自动化帐户。 有关详细信息，请参阅 [Azure 自动化简介](automation-intro.md)。
- 具有“自动化和控制”服务产品的 Log Analytics 工作区。 有关详细信息，请参阅 [Azure Monitor 中的 Log Analytics 入门](/azure-monitor/log-query/get-started-portal)。
- 至少一个 Azure Automation State Configuration 节点。 有关详细信息，请参阅[登记由 Azure Automation State Configuration 管理的计算机](automation-dsc-onboarding.md)。
- [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) 模块版本 2.7.0.0 或更高版本。 有关安装步骤，请参阅[排查 Azure 自动化 Desired State Configuration 问题](./troubleshoot/desired-state-configuration.md)。

## <a name="set-up-integration-with-azure-monitor-logs"></a>设置与 Azure Monitor 日志的集成

若要开始将数据从 Azure Automation State Configuration 导入到 Azure Monitor 日志，请完成以下步骤：

1. 通过 PowerShell 登录 Azure 帐户。 请参阅[使用 Azure PowerShell 进行登录](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。
1. 通过运行以下 PowerShell cmdlet 获取你的自动化帐户的资源 ID。 如果你有多个自动化帐户，请选择你要配置的帐户的资源 ID。

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. 通过运行以下 PowerShell cmdlet 获取你的 Log Analytics 工作区的资源 ID。 如果你有多个工作区，请选择你要配置的工作区的资源 ID。

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. 运行以下 PowerShell cmdlet，将 `<AutomationResourceId>` 和 `<WorkspaceResourceId>` 替换为前面各个步骤中的 `ResourceId` 值。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. 若要停止将数据从 Azure Automation State Configuration 导入到 Azure Monitor 日志，请运行以下 PowerShell cmdlet。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>查看 Automation State Configuration 日志

为 Automation State Configuration 数据设置与 Azure Monitor 日志的集成后，可以通过在 State configuration (DSC) 页面左窗格的“监视”  部分中选择“日志”  来查看这些日志。

![日志](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

此时会打开“日志搜索”窗格，其中的查询区域的作用域为你的自动化帐户资源。 可以通过搜索 Azure Monitor 日志在 State Configuration 日志中搜索 DSC 操作。 DSC 操作的记录存储在 `AzureDiagnostics` 表中。 例如，若要查找不符合的节点，请键入以下查询。

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

筛选详细信息：

* 基于 `DscNodeStatusData` 进行筛选可返回每个 State Configuration 节点的操作。
* 基于 `DscResourceStatusData` 进行筛选可返回在应用于该资源的节点配置中调用的每个 DSC 资源的操作。 
* 基于 `DscResourceStatusData` 进行筛选可返回失败的任何 DSC 资源的错误信息。

若要详细了解如何构建日志查询来查找数据，请参阅 [Azure Monitor 中的日志查询概述](/azure-monitor/log-query/log-query-overview)。

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>State Configuration 符合性检查失败时发送一封电子邮件

我们的一家重要客户提出的请求是，当 DSC 配置出现问题时能够发送电子邮件或短信。

若要创建警报规则，需要首先针对应调用警报的 State Configuration 报表记录创建日志搜索。 单击“新建警报规则”  按钮以创建并配置警报规则。

1. 在“Log Analytics 工作区概述”页中，单击“日志”  。
1. 通过在查询字段中键入以下搜索，针对警报创建日志搜索查询：`AzureDiagnostics | where Category=='DscNodeStatus' and NodeName_s=='DSCTEST1' and OperationName=='DscNodeStatusData' and ResultType=='Failed'`

   如果已设置在工作区中收集来自多个自动化帐户或订阅的日志，则可以按照订阅或自动化帐户来为警报分组。 在搜索 `DscNodeStatusData` 记录时从 `Resource` 字段派生自动化帐户名称。
1. 若要打开“创建规则”  屏幕，请单击页面顶部的“新建警报规则”  。 

有关警报配置选项的详细信息，请参阅[创建警报规则](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)。

### <a name="find-failed-dsc-resources-across-all-nodes"></a>在所有节点中查找失败的 DSC 资源

使用 Azure Monitor 日志的一个优点是，可以在节点中搜索失败的检查。 若要查找失败的 DSC 资源的所有实例，请执行以下操作：

1. 在“Log Analytics 工作区概述”页面上，单击“日志”  。
1. 在查询字段中键入以下搜索，针对警报创建日志搜索查询：`AzureDiagnostics | where Category=='DscNodeStatus' and OperationName=='DscResourceStatusData' and ResultType=='Failed'`

### <a name="view-historical-dsc-node-status"></a>查看历史 DSC 节点状态

若要可视化一段时间内的 DSC 节点状态历史记录，可以使用以下查询：

`AzureDiagnostics | where ResourceProvider=="MICROSOFT.AUTOMATION" and Category=="DscNodeStatus" and ResultType!="started" | summarize count() by ResultType, bin(TimeGenerated, 1h)`

此查询显示一段时间内的节点状态的图表。

## <a name="azure-monitor-logs-records"></a>Azure Monitor 日志记录

Azure 自动化诊断在 Azure Monitor 日志中创建两种类别的记录：

* 节点状态数据 (`DscNodeStatusData`)
* 资源状态数据 (`DscResourceStatusData`)

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| 属性 | 说明 |
| --- | --- |
| TimeGenerated |符合性检查运行的日期和时间。 |
| OperationName |`DscNodeStatusData`。 |
| ResultType |一个值，指示节点是否合规。 |
| NodeName_s |托管节点的名称。 |
| NodeComplianceStatus_s |一个状态值，指定节点是否合规。 |
| DscReportStatus |一个状态值，指示合规性检查是否已成功运行。 |
| ConfigurationMode | 用于将配置应用到节点的模式。 可能的值包括： <ul><li>`ApplyOnly`：DSC 将应用配置，且不执行进一步操作，除非有新配置被推送到目标节点或从服务器请求新配置。 首次应用新配置后，DSC 将不检查以前配置状态的偏离。 在 `ApplyOnly` 值生效之前，DSC 会尝试应用配置，直到成功。 </li><li>`ApplyAndMonitor`：这是默认值。 LCM 将应用任意新配置。 首次应用新配置后，如果目标节点偏离所需状态，DSC 将在日志中报告差异。 在 `ApplyAndMonitor` 值生效之前，DSC 会尝试应用配置，直到成功。</li><li>`ApplyAndAutoCorrect`：DSC 将应用任何新配置。 首次应用新配置后，如果目标节点偏离所需状态，DSC 将在日志中报告差异，然后重新应用当前配置。</li></ul> |
| HostName_s | 托管节点的名称。 |
| IPAddress | 托管节点的 IPv4 地址。 |
| Category | `DscNodeStatus`。 |
| 资源 | Azure 自动化帐户的名称。 |
| Tenant_g | 一个 GUID，用于标识调用方的租户。 |
| NodeId_g | 标识托管节点的 GUID。 |
| DscReportId_g | 标识报表的 GUID。 |
| LastSeenTime_t | 上一次查看报表的日期和时间。 |
| ReportStartTime_t | 报表开始的日期和时间。 |
| ReportEndTime_t | 报表完成的日期和时间。 |
| NumberOfResources_d | 在应用于节点的配置中调用的 DSC 资源数。 |
| SourceSystem | 源系统，用于标识 Azure Monitor 日志收集数据的方式。 对于 Azure 诊断，此项始终为“`Azure`”。 |
| ResourceId |Azure 自动化帐户的资源标识符。 |
| ResultDescription | 此操作的资源说明。 |
| SubscriptionId | 自动化帐户的 Azure 订阅 ID (GUID)。 |
| resourceGroup | 自动化帐户的资源组的名称。 |
| ResourceProvider | MICROSOFT.AUTOMATION。 |
| ResourceType | AUTOMATIONACCOUNTS。 |
| CorrelationId | 一个 GUID，用作合规性报告的关联标识符。 |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| 属性 | 说明 |
| --- | --- |
| TimeGenerated |符合性检查运行的日期和时间。 |
| OperationName |`DscResourceStatusData`。|
| ResultType |资源是否符合。 |
| NodeName_s |托管节点的名称。 |
| Category | DscNodeStatus。 |
| 资源 | Azure 自动化帐户的名称。 |
| Tenant_g | 一个 GUID，用于标识调用方的租户。 |
| NodeId_g |标识托管节点的 GUID。 |
| DscReportId_g |标识报表的 GUID。 |
| DscResourceId_s |DSC 资源实例的名称。 |
| DscResourceName_s |DSC 资源的名称。 |
| DscResourceStatus_s |DSC 资源是否具有符合性。 |
| DscModuleName_s |包含 DSC 资源的 PowerShell 模块的名称。 |
| DscModuleVersion_s |包含 DSC 资源的 PowerShell 模块的版本。 |
| DscConfigurationName_s |应用于节点的配置的名称。 |
| ErrorCode_s | 资源失败时的错误代码。 |
| ErrorMessage_s |资源失败时的错误消息。 |
| DscResourceDuration_d |DSC 资源运行的时间（以秒为单位）。 |
| SourceSystem | Azure Monitor 日志收集数据的方式。 对于 Azure 诊断，此项始终为“`Azure`”。 |
| ResourceId |Azure 自动化帐户的标识符。 |
| ResultDescription | 此操作的说明。 |
| SubscriptionId | 自动化帐户的 Azure 订阅 ID (GUID)。 |
| resourceGroup | 自动化帐户的资源组的名称。 |
| ResourceProvider | MICROSOFT.AUTOMATION。 |
| ResourceType | AUTOMATIONACCOUNTS。 |
| CorrelationId |一个 GUID，用作合规性报告的关联 ID。 |


## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅 [Azure Automation State Configuration](automation-dsc-overview.md)。
- 有关入门信息，请参阅 [Azure 自动化 State Configuration 入门](automation-dsc-getting-started.md)。
- 若要了解如何编译 DSC 配置，以便将它们分配给目标节点，请参阅[在 Azure 自动化 State Configuration 中编译配置](automation-dsc-compile.md)。
- 有关 PowerShell cmdlet 参考，请参阅 [Azure Automation State Configuration cmdlet](https://docs.microsoft.com/powershell/module/azurerm.automation/#automation)
- 有关定价信息，请参阅 [Azure Automation State Configuration 定价](https://azure.cn/pricing/details/automation/)。
- 若要查看在持续部署管道中使用 Azure Automation State Configuration 的示例，请参阅[使用 Azure Automation State Configuration 和 Chocolatey 进行持续部署](automation-dsc-cd-chocolatey.md)。
- 若要详细了解如何使用 Azure Monitor 日志构造不同的搜索查询和查看 Automation State Configuration 日志，请参阅 [Azure Monitor 日志中的日志搜索](/azure-monitor/log-query/log-query-overview)
- 若要了解有关 Azure Monitor 日志和数据收集源的详细信息，请参阅[在 Azure Monitor 日志中收集 Azure 存储数据概述](../azure-monitor/platform/collect-azure-metrics-logs.md)。
