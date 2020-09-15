---
title: PowerShell：配置 Azure SQL 数据库的活动异地复制
description: 使用 Azure PowerShell 示例脚本为 Azure SQL 数据库设置活动异地复制并进行故障转移。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: WenJason
ms.author: v-jay
ms.reviewer: carlrab
origin.date: 03/12/2019
ms.date: 09/14/2020
ms.openlocfilehash: 3b2480cef5859e9f33ad4875650b0212f9a07e1b
ms.sourcegitcommit: d5cdaec8050631bb59419508d0470cb44868be1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90014290"
---
# <a name="use-powershell-to-configure-active-geo-replication-for-a-database-in-azure-sql-database"></a>使用 PowerShell 为 Azure SQL 数据库中的数据库配置活动异地复制

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

此 Azure PowerShell 脚本示例为 Azure SQL 数据库中的数据库配置活动异地复制，并将其故障转移到数据库的辅助副本。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

本教程需要 Az PowerShell 1.4.0 或更高版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps)（安装 Azure PowerShell 模块）。 此外，还需要运行 `Connect-AzAccount -EnvironmentName AzureChinaCloud` 以创建与 Azure 的连接。

## <a name="sample-scripts"></a>示例脚本

```azurepowershell
# Connect-AzAccount -Environment AzureChinaCloud
$SubscriptionId = ''
# Set the resource group name and location for your primary server
$primaryResourceGroupName = "myPrimaryResourceGroup-$(Get-Random)"
$primaryLocation = "chinaeast"
# Set the resource group name and location for your secondary server
$secondaryResourceGroupName = "mySecondaryResourceGroup-$(Get-Random)"
$secondaryLocation = "chinanorth"
# Set an admin login and password for your servers
$adminSqlLogin = "SqlAdmin"
$password = "ChangeYourAdminPassword1"
# Set server names - the logical server names have to be unique in the system
$primaryServerName = "primary-server-$(Get-Random)"
$secondaryServerName = "secondary-server-$(Get-Random)"
# The sample database name
$databasename = "mySampleDatabase"
# The ip address range that you want to allow to access your servers
$primaryStartIp = "0.0.0.0"
$primaryEndIp = "0.0.0.0"
$secondaryStartIp = "0.0.0.0"
$secondaryEndIp = "0.0.0.0"

# Set subscription 
Set-AzContext -SubscriptionId $subscriptionId 

# Create two new resource groups
$primaryResourceGroup = New-AzResourceGroup -Name $primaryResourceGroupName -Location $primaryLocation
$secondaryResourceGroup = New-AzResourceGroup -Name $secondaryResourceGroupName -Location $secondaryLocation

# Create two new logical servers with a system wide unique server name
$primaryServer = New-AzSqlServer -ResourceGroupName $primaryResourceGroupName `
    -ServerName $primaryServerName `
    -Location $primaryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminSqlLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
$secondaryServer = New-AzSqlServer -ResourceGroupName $secondaryResourceGroupName `
    -ServerName $secondaryServerName `
    -Location $secondaryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminSqlLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create a server firewall rule for each server that allows access from the specified IP range
$primaryserverfirewallrule = New-AzSqlServerFirewallRule -ResourceGroupName $primaryResourceGroupName `
    -ServerName $primaryservername `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $primaryStartIp -EndIpAddress $primaryEndIp
$secondaryserverfirewallrule = New-AzSqlServerFirewallRule -ResourceGroupName $secondaryResourceGroupName `
    -ServerName $secondaryservername `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $secondaryStartIp -EndIpAddress $secondaryEndIp

# Create a blank database with S0 performance level on the primary server
$database = New-AzSqlDatabase  -ResourceGroupName $primaryResourceGroupName `
    -ServerName $primaryServerName `
    -DatabaseName $databasename -RequestedServiceObjectiveName "S0"

# Establish Active Geo-Replication
$database = Get-AzSqlDatabase -DatabaseName $databasename -ResourceGroupName $primaryResourceGroupName -ServerName $primaryServerName
$database | New-AzSqlDatabaseSecondary -PartnerResourceGroupName $secondaryResourceGroupName -PartnerServerName $secondaryServerName -AllowConnections "All"

# Initiate a planned failover
$database = Get-AzSqlDatabase -DatabaseName $databasename -ResourceGroupName $secondaryResourceGroupName -ServerName $secondaryServerName
$database | Set-AzSqlDatabaseSecondary -PartnerResourceGroupName $primaryResourceGroupName -Failover

# Monitor Geo-Replication config and health after failover
$database = Get-AzSqlDatabase -DatabaseName $databasename -ResourceGroupName $secondaryResourceGroupName -ServerName $secondaryServerName
$database | Get-AzSqlDatabaseReplicationLink -PartnerResourceGroupName $primaryResourceGroupName -PartnerServerName $primaryServerName

# Remove the replication link after the failover
$database = Get-AzSqlDatabase -DatabaseName $databasename -ResourceGroupName $secondaryResourceGroupName -ServerName $secondaryServerName
$secondaryLink = $database | Get-AzSqlDatabaseReplicationLink -PartnerResourceGroupName $primaryResourceGroupName -PartnerServerName $primaryServerName
$secondaryLink | Remove-AzSqlDatabaseSecondary

# Clean up deployment 
#Remove-AzResourceGroup -ResourceGroupName $primaryResourceGroupName
#Remove-AzResourceGroup -ResourceGroupName $secondaryResourceGroupName
```

## <a name="clean-up-deployment"></a>清理部署

使用以下命令删除资源组及其相关的所有资源。

```powershell
Remove-AzResourceGroup -ResourceGroupName $primaryresourcegroupname
Remove-AzResourceGroup -ResourceGroupName $secondaryresourcegroupname
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlserver) | 创建托管数据库和弹性池的服务器。 |
| [New-AzSqlElasticPool](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlelasticpool) | 创建弹性池。 |
| [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) | 更新数据库属性，或者将数据库移入、移出弹性池或在弹性池之间移动。 |
| [New-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary)| 为现有数据库创建辅助数据库，并开始数据复制。 |
| [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase)| 获取一个或多个数据库。 |
| [Set-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary)| 将辅助数据库切换为主数据库，启动故障转移。|
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) | 获取 Azure SQL 数据库和资源组或逻辑 SQL 服务器之间的异地复制链路。 |
| [Remove-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) | 终止数据库和指定的辅助数据库之间的数据复制。 |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/)。

可以在 [Azure SQL 数据库 PowerShell 脚本](../powershell-script-content-guide.md)中找到更多 SQL 数据库 PowerShell 脚本示例。
