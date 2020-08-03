---
title: 使用服务终结点限制访问
description: 使用 Azure 虚拟网络中的服务终结点限制对 Azure 容器注册表的访问。 服务终结点访问是高级服务层级的一项功能。
ms.topic: article
origin.date: 05/04/2020
ms.date: 07/27/2020
ms.testscope: yes
ms.testdate: 07/27/2020
ms.author: v-yeche
ms.openlocfilehash: 3f0de6efe9a7b7198a07b577196f2d580586e61e
ms.sourcegitcommit: 5726d3b2e694f1f94f9f7d965676c67beb6ed07c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/21/2020
ms.locfileid: "86863415"
---
<!--Verified successfully-->
# <a name="restrict-access-to-a-container-registry-using-a-service-endpoint-in-an-azure-virtual-network"></a>使用 Azure 虚拟网络中的服务终结点限制对容器注册表的访问

[Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)为 Azure 资源和本地资源提供安全的专用网络。 使用[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)可以保护容器注册表的公共 IP 地址，仅在自己的虚拟网络中对其进行访问。 此终结点为流量提供通过 Azure 主干网络到达资源的最优路径。 虚拟网络和子网的标识也随每个请求进行传输。

本文介绍如何在虚拟网络中配置容器注册表服务终结点（预览版）。 

> [!IMPORTANT]
> Azure 容器注册表现支持 [Azure 专用链接](container-registry-private-link.md)，允许将来自虚拟网络的专用终结点放置在注册表上。 可以使用专用 IP 地址从虚拟网络内部访问专用终结点。 在大多数网络方案中，我们建议使用专用终结点，而不是服务终结点。

“高级”容器注册表服务层级支持配置注册表服务终结点。 若要了解注册表服务层和限制，请参阅 [Azure 容器注册表服务层](container-registry-skus.md)。

## <a name="preview-limitations"></a>预览版限制

* 目前尚未打算在未来开发 Azure 容器注册表的服务终结点。 我们推荐改用[专用终结点](container-registry-private-link.md)。
* 不能使用 Azure 门户在注册表上配置服务终结点。
* 只有 [Azure Kubernetes 服务](../aks/intro-kubernetes.md)群集或 Azure [虚拟机](../virtual-machines/linux/overview.md)可以用作主机，以使用服务终结点访问容器注册表。 其他 Azure 服务（包括 Azure 容器实例）不受支持。
* 每个注册表最多支持 100 条网络访问规则。

## <a name="prerequisites"></a>先决条件

* 若要使用本文中所述的 Azure CLI 步骤，需要安装 Azure CLI 版本 2.0.58 或更高版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

* 如果还没有容器注册表，请创建一个（需要高级层），并推送示例映像，如来自 Docker Hub 的 `hello-world`。 例如，使用 [Azure 门户][quickstart-portal]或 [Azure CLI][quickstart-cli] 创建注册表。 

* 若要使用其他 Azure 订阅中的服务终结点限制注册表访问，请在该订阅中注册 Azure 容器注册表的资源提供程序。 例如：

    ```azurecli
    az account set --subscription <Name or ID of subscription of virtual network>

    az provider register --namespace Microsoft.ContainerRegistry
    ``` 

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="configure-network-access-for-registry"></a>为注册表配置网络访问

在本部分中，将容器注册表配置为允许从 Azure 虚拟网络中的子网进行访问。 我们提供了使用 Azure CLI 和 Azure 门户的等效步骤。

### <a name="allow-access-from-a-virtual-network---cli"></a>允许从虚拟网络进行访问 - CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>将服务终结点添加到子网

创建 VM 时，Azure 默认情况下会在同一个资源组中创建虚拟网络。 虚拟网络的名称基于虚拟机的名称。 例如，如果将虚拟机命名为 myDockerVM，则默认虚拟网络名称为 myDockerVMVNET，且子网名为 myDockerVMSubnet。 在 Azure 门户中对此进行验证或使用 [az network vnet list][az-network-vnet-list] 命令验证：

```azurecli
az network vnet list \
  --resource-group myResourceGroup \
  --query "[].{Name: name, Subnet: subnets[0].name}"
```

输出：

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

使用 [az network vnet subnet update][az-network-vnet-subnet-update] 命令将 Microsoft.ContainerRegistry 服务终结点添加到子网。 使用以下命令替换虚拟网络和子网的名称：

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

使用 [az network vnet subnet show][az-network-vnet-subnet-show] 命令检索子网的资源 ID。 在后面的步骤中，你将使用此 ID 配置网络访问规则。

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

输出：

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="change-default-network-access-to-registry"></a>更改默认网络对注册表的访问权限

默认情况下，Azure 容器注册表允许来自任何网络上的主机的连接。 要将访问权限仅授予所选网络，请将默认操作更改为拒绝访问。 请将以下 [az acr update][az-acr-update] 命令中的占位符替换为你的注册表名称：

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>向注册表添加网络规则

使用 [az acr network-rule add][az-acr-network-rule-add] 命令向注册表添加允许从 VM 子网进行访问的网络规则。 使用以下命令替换容器注册表的名称和子网的资源 ID： 

 ```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --subnet <subnet-resource-id>
```

## <a name="verify-access-to-the-registry"></a>验证对注册表的访问

等待几分钟以更新配置后，请验证 VM 是否可以访问容器注册表。 建立与 VM 的 SSH 连接，并运行 [az acr login][az-acr-login] 命令以登录注册表。 

```bash
az acr login --name mycontainerregistry
```

可以执行注册表操作（如运行 `docker pull`），以从注册表拉取示例映像。 替换适用于注册表的映像和标记值，并以注册表登录服务器名称（全部小写）作为前缀：

```bash
docker pull mycontainerregistry.azurecr.cn/hello-world:v1
``` 

Docker 已成功将映像拉取到 VM。

此示例演示了如何通过网络访问规则访问专用容器注册表。 但是，无法从未配置网络访问规则的登录主机访问注册表。 如果尝试使用 `az acr login` 命令或 `docker login` 命令从另一个主机登录，则输出类似于以下内容：

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.cn/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>还原默认注册表访问

若要将注册表还原为默认允许访问，请删除配置的所有网络规则。 然后，设置默认操作以允许访问。 我们提供了使用 Azure CLI 和 Azure 门户的等效步骤。

### <a name="restore-default-registry-access---cli"></a>还原默认注册表访问 - CLI

#### <a name="remove-network-rules"></a>删除网络规则

若要查看为注册表配置的网络规则列表，请运行以下 [az acr network-rule list][az-acr-network-rule-list] 命令：

```azurecli
az acr network-rule list --name mycontainerregistry 
```

对于配置的每个规则，运行 [az acr network-rule remove][az-acr-network-rule-remove] 命令将其删除。 例如：

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="allow-access"></a>允许访问

在以下 [az acr update][az-acr-update] 命令中，替换注册表的名称：
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

## <a name="clean-up-resources"></a>清理资源

如果所有 Azure 资源都是在同一资源组中创建的并且不再需要，你以选择使用单个 [az group delete](https://docs.azure.cn/cli/group?view=azure-cli-latest#az-group-delete) 命令删除资源：

```azurecli
az group delete --name myResourceGroup
```

若要在门户中清理资源，请导航到 myResourceGroup 资源组。 加载资源组后，单击“删除资源组”以删除该资源组和其中存储的资源。

## <a name="next-steps"></a>后续步骤

* 要使用虚拟网络中的专用终结点限制对注册表的访问，请参阅[为 Azure 容器注册表配置 Azure 专用链接](container-registry-private-link.md)。
* 如需设置从客户端防火墙后访问注册表的规则，请参阅[配置从防火墙后访问 Azure 容器注册表的规则](container-registry-firewall-access-rules.md)。

<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png

<!-- LINKS - External -->

[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
[terms-of-use]: https://www.azure.cn/support/legal/subscription-agreement/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->

[azure-cli]: https://docs.azure.cn/cli/install-azure-cli?view=azure-cli-latest
[az-acr-create]: https://docs.azure.cn/cli/acr?view=azure-cli-latest#az-acr-create
[az-acr-show]: https://docs.azure.cn/cli/acr?view=azure-cli-latest#az-acr-show
[az-acr-repository-show]: https://docs.azure.cn/cli/acr/repository?view=azure-cli-latest#az-acr-repository-show
[az-acr-repository-list]: https://docs.azure.cn/cli/acr/repository?view=azure-cli-latest#az-acr-repository-list
[az-acr-login]: https://docs.azure.cn/cli/acr?view=azure-cli-latest#az-acr-login
[az-acr-network-rule-add]: https://docs.azure.cn/cli/acr/network-rule/?view=azure-cli-latest#az-acr-network-rule-add
[az-acr-network-rule-remove]: https://docs.azure.cn/cli/acr/network-rule/?view=azure-cli-latest#az-acr-network-rule-remove
[az-acr-network-rule-list]: https://docs.azure.cn/cli/acr/network-rule/?view=azure-cli-latest#az-acr-network-rule-list
[az-acr-run]: https://docs.azure.cn/cli/acr?view=azure-cli-latest#az-acr-run
[az-acr-update]: https://docs.azure.cn/cli/acr?view=azure-cli-latest#az-acr-update
[az-ad-sp-create-for-rbac]: https://docs.azure.cn/cli/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac
[az-group-create]: https://docs.azure.cn/cli/group?view=azure-cli-latest
[az-role-assignment-create]: https://docs.azure.cn/cli/role/assignment?view=azure-cli-latest#az-role-assignment-create
[az-vm-create]: https://docs.azure.cn/cli/vm?view=azure-cli-latest#az-vm-create
[az-network-vnet-subnet-show]: https://docs.azure.cn/cli/network/vnet/subnet/?view=azure-cli-latest#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: https://docs.azure.cn/cli/network/vnet/subnet/?view=azure-cli-latest#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: https://docs.azure.cn/cli/network/vnet/subnet/?view=azure-cli-latest#az-network-vnet-subnet-show
[az-network-vnet-list]: https://docs.azure.cn/cli/network/vnet/?view=azure-cli-latest#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.cn

<!-- Update_Description: new article about container registry vnet -->
<!--NEW.date: 07/27/2020-->