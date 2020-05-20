---
title: 确定导致非符合性的原因
description: 有多种可能的原因会导致资源不合规。 了解如何查明导致不合规的原因。
ms.author: v-tawe
origin.date: 04/26/2019
ms.date: 02/17/2020
ms.topic: how-to
ms.openlocfilehash: 2a839f0155192a1ba1af7523fc3dc819489ed1b6
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "79293054"
---
# <a name="determine-causes-of-non-compliance"></a>确定导致非符合性的原因

在判定某个 Azure 资源不符合某个策略规则时，了解该资源不符合该规则的哪个部分会很有帮助。 这还有助于了解哪项更改改变了以前合规的资源，导致它现在不合规。 可通过两种方式查找此信息：

> [!div class="checklist"]
> - [合规性详细信息](#compliance-details)

<!-- no change history in mc portal -->
<!-- > - [Change history (Preview)](#change-history) -->

## <a name="compliance-details"></a>合规性详细信息

当某个资源不合规时，“策略合规性”页中会提供该资源的合规性详细信息。  合规性详细信息窗格包含以下信息：

- 资源详细信息，例如名称、类型、位置和资源 ID
- 上次评估当前策略分配时的合规状态和时间戳
- 资源不合规的原因列表 

> [!IMPORTANT]
> 由于不合规资源的合规性详细信息显示有关该资源的属性的当前值，因此，用户必须对资源**类型**拥有**读取**操作权限。 例如，如果不合规的资源为 **Microsoft.Compute/virtualMachines**，则用户必须拥有 **Microsoft/virtualMachines/read** 操作权限。 如果用户没有所需的操作权限，将显示访问权限错误。

若要查看合规性详细信息，请执行以下步骤：

1. 在 Azure 门户中单击“所有服务”，然后搜索并选择“策略”，启动 Azure Policy 服务。  

1. 在“概述”或“合规性”页上，选择**合规性状态**为“不合规”的策略。 

1. 在“策略合规性”页的“资源合规性”选项卡下，右键单击**合规性状态**为“不合规”的资源或选择其对应的省略号。  然后选择“查看合规性详细信息”。 

   ![“查看合规性详细信息”选项](../media/determine-non-compliance/view-compliance-details.png)

1. “合规性详细信息”窗格将显示最近评估当前策略分配中的资源时的信息。  在此示例中，**Microsoft.Sql/servers/version** 字段值为 _12.0_，而策略定义预期该值为 _14.0_。 如果资源出于多种原因而不合规，此窗格中会列出每种原因。

   ![“合规性详细信息”窗格和不合规的原因](../media/determine-non-compliance/compliance-details-pane.png)

   对于 **auditIfNotExists** 或 **deployIfNotExists** 策略定义，详细信息包含 **details.type** 属性和所有可选属性。 有关列表，请参阅 [auditIfNotExists 属性](../concepts/effects.md#auditifnotexists-properties)和 [deployIfNotExists 属性](../concepts/effects.md#deployifnotexists-properties)。 “上次评估的资源”是定义的 **details** 节中的相关资源。

   部分 **deployIfNotExists** 定义示例：

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   ![“合规性详细信息”窗格 - *ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> 若要保护数据，当属性值是机密时，当前值将显示星号。 

这些详细信息将解释资源当前不合规的原因，但不显示何时对该资源做出了更改，导致它不合规。

### <a name="compliance-reasons"></a>合规性原因

以下矩阵将每个可能的原因映射到策略定义中的控制 [条件：](../concepts/definition-structure.md#conditions)

|Reason | 条件 |
|-|-|
|当前值必须包含目标值作为键。 |containsKey，或 notContainsKey 的**求反** |
|当前值必须包含目标值。 |contains，或 notContains 的**求反** |
|当前值必须等于目标值。 |equals，或 notEquals 的**求反** |
|当前值必须小于目标值。 |less，或 greaterOrEquals 的**求反** |
|当前值必须大于或等于目标值。 |greaterOrEquals，或 less 的**求反** |
|当前值必须大于目标值。 |greater，或 lessOrEquals 的**求反** |
|当前值必须小于或等于目标值。 |lessOrEquals，或 greater 的**求反** |
|必须存在当前值。 |exists |
|当前值必须在目标值中。 |in，或 notIn 的**求反** |
|当前值必须与目标值类似。 |like，或 notLike 的**求反** |
|当前值必须与目标值匹配（区分大小写）。 |match，或 notMatch 的**求反** |
|当前值必须与目标值匹配（不区分大小写）。 |matchInsensitively，或 notMatchInsensitively 的**求反** |
|当前值不得包含目标值作为键。 |notContainsKey，或 containsKey 的**求反**|
|当前值不得包含目标值。 |notContains，或 contains 的**求反** |
|当前值不得等于目标值。 |notEquals，或 equals 的**求反** |
|不得存在当前值。 |exists 的**求反**  |
|当前值不得在目标值中。 |notIn，或 in 的**求反** |
|当前值不得与目标值类似。 |notLike，或 like 的**求反** |
|当前值不得与目标值匹配（区分大小写）。 |notMatch，或 match 的**求反** |
|当前值不得与目标值匹配（不区分大小写）。 |notMatchInsensitively，或 matchInsensitively 的**求反** |
|没有与策略定义中的效果详细信息匹配的相关资源。 |在 **then.details.type** 中定义的类型的、与策略规则的 **if** 部分中定义的资源相关的资源不存在。 |

## <a name="compliance-details-for-guest-configuration"></a>Guest Configuration 的合规性详细信息

对于 _Guest Configuration_ 类别中的 _auditIfNotExists_ 策略，可能会在 VM 中评估多个设置，而你需要查看每个设置的详细信息。 例如，如果你要审核密码策略列表，而其中只有一个策略的状态为“不合规”，则你需要知道哪个特定的密码策略不合规，以及不合规的原因。 

此外，你可能无权直接登录到 VM，但需要报告 VM 为何不合规。 

### <a name="azure-portal"></a>Azure 门户

首先，遵循前面部分所述的有关查看策略合规性详细信息的相同步骤。

在“合规性详细信息”窗格视图中，单击“上次评估的资源”链接。  

   ![查看 auditIfNotExists 定义详细信息](../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png)

“来宾分配”页将显示提供的所有合规性详细信息。  视图中的每一行代表在计算机中执行的一项评估。 “原因”列中会显示一条短语，描述来宾分配为何不合规。   例如，如果你要审核密码策略，则“原因”列将显示包含每项设置的当前值的文本。 

![查看合规性详细信息](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

也可以从 Azure PowerShell 查看合规性详细信息。 首先，确保已安装 Guest Configuration 模块。

```powershell
Install-Module Az.GuestConfiguration
```

可使用以下命令查看 VM 的所有来宾分配的当前状态：

```powershell
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

如果只想查看描述 VM 为何不合规的原因短语，请仅返回 Reason 子属性。  

```powershell
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

还可以输出来宾分配在该计算机范围内的合规性历史记录。 此命令的输出包括每份 VM 报告的详细信息。

> [!NOTE]
> 输出中可能会返回大量的数据。 建议将输出存储在变量中。

```powershell
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname>
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
[Preview]: Audit that an application is installed inside Windows VMs      NonCompliant                       02/10/2019 12:00:38 PM 02/10/2019 12:00:41 PM VM01  ../17fg0...
<truncated>
```

若要简化此视图，请使用 **ShowChanged** 参数。 此命令的输出仅包括报告，后接合规性状态的变化。

```powershell
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname> -ShowChanged
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/10/2019 10:00:38 PM 02/10/2019 10:00:41 PM VM01  ../12ab0...
Audit that an application is installed inside Windows VMs.                Compliant                          02/09/2019 11:00:38 AM 02/09/2019 11:00:39 AM VM01  ../e3665...
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/09/2019 09:00:20 AM 02/09/2019 09:00:23 AM VM01  ../15ze1...
```

<!-- no change history tag -->
<!-- ## <a name="change-history"/>Change history (Preview) -->
## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](../samples/index.md)中查看示例。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。
- 了解如何[以编程方式创建策略](programmatically-create.md)。
- 了解如何[获取合规性数据](get-compliance-data.md)。
- 了解如何[修正不合规的资源](remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
