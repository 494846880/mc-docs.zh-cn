---
title: 连接到 Azure Kubernetes 服务 - Azure Database for MySQL
description: 了解如何将 Azure Kubernetes 服务与 Azure Database for MySQL 连接
author: WenJason
ms.author: v-jay
ms.service: mysql
ms.topic: conceptual
origin.date: 07/14/2020
ms.date: 08/10/2020
ms.openlocfilehash: 638cb9910d9a1e4d1d1e17384635381845af9694
ms.sourcegitcommit: 3cf647177c22b24f76236c57cae19482ead6a283
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88029624"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>连接 Azure Kubernetes 服务和 Azure Database for MySQL

> [!NOTE]
> 将要查看的是 Azure Database for MySQL 的新服务。 若要查看经典 MySQL Database for Azure 的文档，请访问[此页](https://docs.azure.cn/zh-cn/mysql-database-on-azure/)。

Azure Kubernetes 服务 (AKS) 提供可在 Azure 中使用的托管 Kubernetes 群集。 下面是将 AKS 和 Azure Database for MySQL 一起使用以创建应用程序时要考虑的一些选项。


## <a name="accelerated-networking"></a>加速网络
在 AKS 群集中使用支持加速网络的底层 VM。 在 VM 上启用加速网络时，可以降低延迟、降低抖动和降低 VM 上的 CPU 利用率。 详细了解加速网络的工作原理、支持的 OS 版本以及 [Linux](../virtual-network/create-vm-accelerated-networking-cli.md) 支持的 VM 实例。

从 2018 年 11 月起，AKS 支持在这些受支持的 VM 实例上启用加速网络。 默认情况下，加速网络已在使用这些 VM 的新 AKS 群集上启用。

可以确认 AKS 群集是否已加速了网络：
1. 转到 Azure 门户并选择 AKS 群集。
2. 选择“属性”选项卡。
3. 复制**基础结构资源组**的名称。
4. 使用门户搜索栏找到并打开基础结构资源组。
5. 在该资源组中选择一个 VM。
6. 转到 VM 的“网络”  选项卡。
7. 确认是否已启用**加速网络**。

也可以通过 Azure CLI 使用以下两个命令：
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
输出将是 AKS 创建的包含网络接口的生成资源组。 获取“nodeResourceGroup”名称并在下一个命令中使用它。 **EnableAcceleratedNetworking** 将是 true 或 false：
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```


## <a name="next-steps"></a>后续步骤
- [创建 Azure Kubernetes 服务群集](../aks/kubernetes-walkthrough.md)
- 了解如何[使用 OSBA 和 Azure Database for MySQL 从 Helm 图表安装 WordPress](/aks/)
