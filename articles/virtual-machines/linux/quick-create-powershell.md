---
title: 快速入门 - 使用 Azure PowerShell 创建 Linux VM
description: 本快速入门介绍如何使用 Azure PowerShell 创建 Linux 虚拟机
author: Johnnytechn
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/05/2020
ms.author: v-johya
ms.custom: mvc
ms.openlocfilehash: fee442fd0be6d58ece2b58088ee0d57446164176
ms.sourcegitcommit: 285649db9b21169f3136729c041e4d04d323229a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2020
ms.locfileid: "84683850"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>快速入门：使用 PowerShell 在 Azure 中创建 Linux 虚拟机

Azure PowerShell 模块用于从 PowerShell 命令行或脚本创建和管理 Azure 资源。 本快速入门展示了如何使用 Azure PowerShell 模块在 Azure 中部署 Linux 虚拟机 (VM)。 本快速入门使用 Canonical 提供的 Ubuntu 16.04 LTS 市场映像。 若要查看运行中的 VM，也可以通过 SSH 登录到该 VM 并安装 NGINX Web 服务器。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

## <a name="launch-azure-local-shell"></a>启动 Azure 本地 Shell

若要在本地安装和使用 PowerShell，请运行 `Get-Module -ListAvailable Az.*` 以查找版本。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount -Environment AzureChinaCloud` 来创建与 Azure 的连接。

## <a name="create-ssh-key-pair"></a>创建 SSH 密钥对

需要一个 SSH 密钥对才能完成本快速入门。 如果已有一个 SSH 密钥对，则可以跳过此步骤。

打开 bash shell，使用 [ssh-keygen](https://www.ssh.com/ssh/keygen/) 创建一个 SSH 密钥对。

<!-- Not Available on [Azure Cloud Shell](https://shell.azure.com/bash)--> 

```azurepowershell
ssh-keygen -t rsa -b 2048
```

有关如何创建 SSH 密钥对的更多详细信息，包括 PuTTy 的用法，请参阅[如何将 SSH 密钥与 Windows 配合使用](ssh-from-windows.md)。

<!--Not Available on If you create your SSH key pair using the Cloud Shell, it will be stored in a container image in a [storage account that is automatically created by Cloud Shell](/cloud-shell/persisting-shell-storage). Don't delete the storage account, or the files share within it, until after you have retrieved your keys or you will lose access to the VM. -->
<!--Not Available on Cloud Shell-->

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器：

```azurepowershell
New-AzResourceGroup -Name "myResourceGroup" -Location "ChinaEast"
```

## <a name="create-virtual-network-resources"></a>创建虚拟网络资源

创建虚拟网络、子网和公共 IP 地址。 这些资源用来与 VM 建立网络连接，以及将其连接到 Internet：

```azurepowershell
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Location "ChinaEast" `
  -Name "myVNET" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Location "ChinaEast" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

创建 Azure 网络安全组和流量规则。 网络安全组使用入站和出站规则来保护 VM。 在下面的示例中，将为 TCP 端口 22 创建允许 SSH 连接的入站规则。 为允许传入的 Web 流量，还将为 TCP 端口 80 创建一个入站规则。

```azurepowershell
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleSSH"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 22 `
  -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWWW"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access "Allow"

# Create a network security group
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName "myResourceGroup" `
  -Location "ChinaEast" `
  -Name "myNetworkSecurityGroup" `
  -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

使用 [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) 创建虚拟网络接口卡 (NIC)。 虚拟 NIC 将 VM 连接到子网、网络安全组和公共 IP 地址。

```azurepowershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface `
  -Name "myNic" `
  -ResourceGroupName "myResourceGroup" `
  -Location "ChinaEast" `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

若要在 PowerShell 中创建 VM，请创建一个配置，其中包含要使用的映像、大小和身份验证选项等设置。 然后，系统会使用此配置来生成 VM。

定义 SSH 凭据、OS 信息和 VM 大小。 在此示例中，SSH 密钥存储在 `~/.ssh/id_rsa.pub` 中。 

```azurepowershell
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig `
  -VMName "myVM" `
  -VMSize "Standard_D1" | `
Set-AzVMOperatingSystem `
  -Linux `
  -ComputerName "myVM" `
  -Credential $cred `
  -DisablePasswordAuthentication | `
Set-AzVMSourceImage `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
  -Version "latest" | `
Add-AzVMNetworkInterface `
  -Id $nic.Id

# Configure the SSH key
$sshPublicKey = cat ~/.ssh/id_rsa.pub
Add-AzVMSshPublicKey `
  -VM $vmconfig `
  -KeyData $sshPublicKey `
  -Path "/home/azureuser/.ssh/authorized_keys"
```

现在，组合前面的配置定义来使用 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 创建虚拟机：

```azurepowershell
New-AzVM `
  -ResourceGroupName "myResourceGroup" `
  -Location chinaeast -VM $vmConfig
```

部署 VM 需要数分钟。 部署完成后，请转到下一部分。

## <a name="connect-to-the-vm"></a>连接到 VM

使用公共 IP 地址创建与 VM 的 SSH 连接。 若要查看 VM 的公共 IP 地址，请使用 [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) cmdlet：

```azurepowershell
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

使用创建 SSH 密钥对时使用过的 bash shell（例如本地 bash shell）将 SSH 连接命令粘贴到 shell 中，以便创建一个 SSH 会话。

<!--Not Available on [Azure Cloud Shell](https://shell.azure.com/bash)-->

```bash
ssh azureuser@10.111.12.123
```

出现提示时，请输入登录用户名 *azureuser*。 如果将通行短语与 SSH 密钥配合使用，则需要在出现提示时将其输入。


## <a name="install-nginx"></a>安装 NGINX

若要查看运行中的 VM，请安装 NGINX Web 服务器。 在 SSH 会话中更新包源，然后安装最新的 NGINX 包。

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

完成后，键入 `exit` 以离开 SSH 会话。


## <a name="view-the-web-server-in-action"></a>查看运行中的 Web 服务器

使用所选的 Web 浏览器查看默认的 NGINX 欢迎页。 输入 VM 的公共 IP 地址作为 Web 地址。 可以在 VM 概览页上或此前使用过的 SSH 连接字符串中找到公共 IP 地址。

![NGINX 默认欢迎页](./media/quick-create-cli/nginix-welcome-page.png)

## <a name="clean-up-resources"></a>清理资源

不再需要时，可以使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) cmdlet 删除资源组、VM 和所有相关资源：

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你部署了一台简单的虚拟机、创建了网络安全组及其规则，并安装了一台基本 Web 服务器。 若要详细了解 Azure 虚拟机，请继续学习 Linux VM 教程。

> [!div class="nextstepaction"]
> [Azure Linux 虚拟机教程](./tutorial-manage-vm.md)

<!--Update_Description: update meta properties, wording update, update cmdlet  -->