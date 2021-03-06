---
title: 在 Azure Stack Hub 上部署支持图形处理单元 (GPU) 的 IoT 模块
description: 如何在 Azure Stack Hub 上部署支持图形处理单元 (GPU) 的 IoT 模块
author: WenJason
ms.author: v-jay
ms.service: azure-stack
ms.topic: how-to
origin.date: 11/13/2020
ms.date: 12/07/2020
ms.reviewer: gara
ms.lastreviewed: 11/13/2020
ms.openlocfilehash: 2fcd91b3c9af3bb9702f1fea3341167e3e1353c1
ms.sourcegitcommit: a1f565fd202c1b9fd8c74f814baa499bbb4ed4a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96508269"
---
# <a name="deploy-a-gpu-enabled-iot-module-on-azure-stack-hub"></a>在 Azure Stack Hub 上部署支持 GPU 的 IoT 模块

使用支持 GPU 的 Azure Stack Hub，可将处理器密集型模块部署到在 IoT Edge 运行的 Linux 设备。 GPU 优化型 VM 大小是具有单个或多个 NVIDIA GPU 的专用 VM。 本文介绍如何使用 GPU 优化型 VM 运行计算密集型、图形密集型和可视化工作负载。

在开始之前，你需要一个可访问 Azure 和 Azure Stack Hub、Azure 容器注册表 (ACR) 和 IoT 中心的 Azure Active Directory (Azure AD) 订阅。

本文内容：
  - 安装支持 GPU 的 Linux VM 并安装正确的驱动程序。
  - 安装 Docker 并在运行时启用 GPU。
  - 将 IoT 设备连接到 IoT 中心并从 IoT 市场安装模型：`Getting started with GPUs`。
  - 使用 Azure IoT 资源管理器从本地计算机安装和监视设备。
  - 另外，还可使用 Visual Studio Code 中的 Azure IoT 扩展安装和监视设备。

## <a name="prerequisites"></a>先决条件

你的 Azure Stack Hub 实例、Azure 和本地开发计算机中需要具有以下资源。

### <a name="azure-stack-hub-and-azure"></a>Azure Stack Hub 和 Azure

  - 以用户身份在具有 NVIDA GPU 的 Azure Stack Hub 集成系统中使用 Azure Active Directory (Azure AD) 的订阅。 以下芯片适用于 IoT 中心：
    - NCv3
    - NCas_v4

    有关 Azure Stack Hub 上的 GPU 的详细信息，请参阅 [Azure Stack Hub 上的图形处理单元 (GPU) VM](gpu-vms-about.md)。
  - 全局 Azure 订阅。 如果没有全局 Azure 订阅，请在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。
- 一个 [Azure 容器注册表 (ACR)](/container-registry/)。 记下 ACR 登录服务器、用户名和密码。
-   全局 Azure 中的免费或标准层 [IoT 中心](/iot-hub/iot-hub-create-through-portal)。

### <a name="a-development-machine"></a>一台开发计算机

-   可以使用自己的计算机或虚拟机，具体取决于开发首选项。 开发计算机需支持嵌套虚拟化。 此功能是运行 Docker（本文中使用的容器引擎）所必需的。

  - 开发计算机需要以下资源：
      - [Python 3.x](https://www.python.org/downloads/)
      - 用于安装 Python 程序包的 [Pip](https://pypi.org/project/pip/)。 这是随 Python 安装一起安装的。 如果已安装 Pip，则可能需要升级到最新版本。 可以使用 pip 本身进行升级。 键入：`pip install --upgrade pip`。
      - [Azure CLI 2.0](/cli/install-azure-cli?view=azure-cli-latest)
      - [Git](https://git-scm.com/downloads)
      - [Docker](https://docs.docker.com/get-docker/)
      - [Visual Studio Code](https://code.visualstudio.com/)
      - 适用于 Visual Studio Code 的 [Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
      - 适用于 Visual Studio Code 的 [Python 扩展包](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python-extension-pack)

## <a name="register-an-iot-edge-device"></a>注册 IoT Edge 设备

使用单独的设备来托管 IoT Edge 设备。 使用单独的设备可分隔开发计算机与 IoT Edge 设备，更准确地反映部署方案。 

使用 Linux VM 在 Azure 中创建 IoT Edge 设备：

1.  在 Azure Stack Hub 上[创建 N 系列 Linux 服务器 VM](/azure-stack/user/azure-stack-quick-linux-portal)。 为服务器安装组件时，将通过 SSH 与服务器进行交互。 有关详细信息，请参阅[使用 SSH 公钥](/azure-stack/user/azure-stack-dev-start-howto-ssh-public-key)。

2.  [创建并注册 IoT Edge 设备](/iot-edge/how-to-register-device)

## <a name="prepare-a-gpu-enabled-vm"></a>准备支持 GPU 的 VM

1. 按照[在运行 Linux 的 N 系列 VM 上安装 NVIDIA GPU 驱动程序](/virtual-machines/linux/n-series-driver-setup)一文中的步骤，在 N 系列 Linux 服务器上安装 NVIDA GPU 驱动程序。

    > [!NOTE]  
    > 你将使用 bash 命令行来安装软件。 请记下这些命令，因为你将使用相同的命令在支持 GPU 的 VM 上的 Docker 中运行的容器上安装驱动程序

2.  在 Azure Stack Hub 中的 N 系列 Linux 服务器上安装最新的 IoT Edge 运行时。 有关说明，请参阅[在基于 Debian 的 Linux 系统上安装 Azure IoT Edge 运行时](/iot-edge/how-to-install-iot-edge-linux#install-the-latest-runtime-version)

## <a name="install-docker"></a>安装 Docker

在支持 GPU 的 VM 上安装 Docker。 你将在 VM 上的容器中从 IoT Edge 市场运行该模块。

必须安装 Docker 19.02 或更高版本。 Docker 运行时现在支持 NVIDIA GPU。 要详细了解 Docker 中的 GPU，请参阅 Docker 文档中的文章：[内存、CPU 和 GPU 的运行时选项](https://docs.docker.com/config/containers/resource_constraints/#gpu)。

### <a name="install-docker"></a>安装 Docker

Docker 容器可以在任意位置运行：在客户数据中心本地、在外部服务提供商或在 Azure 云中。 Docker 映像容器可以在 Linux 和 Windows 上本机运行。 但是，Windows 映像仅能在 Windows 主机上运行，Linux 映像可以在 Linux 主机和 Windows 主机上运行（到目前为止，使用 Hyper-V Linux VM），其中主机是指服务器或 VM。 有关详细信息，请参阅[什么是 Docker？](https://docs.microsoft.com/dotnet/architecture/microservices/container-docker-introduction/docker-defined)。

1. 使用 SSH 客户端连接到 N 系列 Linux 服务器。

1.  更新 apt 索引和列表。

    ```bash  
    sudo apt-get update
    ```

1.  提取计算机上现有包的新版本。

    ```bash  
    sudo apt-get upgrade
    ```

2.  安装添加 Docker 的 apt 存储库所需的依赖项。

    ```bash  
    sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
    ```

3.  添加 Docker 的 GPG 密钥。

    ```bash  
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

4.  添加 Docker 的 apt 存储库。

    ```bash  
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

5.  更新 apt 索引和列表，并安装 Docker Community Edition。

    ```bash  
    sudo apt-get update 
    sudo apt-get install docker-ce
    ```

6.  通过检查 Docker 版本来验证安装。

    ```bash  
    docker -v
    ```

7. 在 Docker 中公开可用的 GPU 资源。

    ```bash
    docker run -it --rm --gpus all ubuntu nvidia-smi
    ```

## <a name="get-the-item-from-the-marketplace"></a>从市场获取项

返回到 Azure 门户，然后使用 IoT 市场将模型添加到边缘设备。 从菜单中选择“市场模块”。 然后搜索 `Getting started with GPUs`，并按照说明添加模块。

有关说明，请参阅[选择设备并添加模块](/iot-edge/how-to-deploy-modules-portal#select-device-and-add-modules)

## <a name="enable-monitoring"></a>启用监视

1. 下载 [Azure IoT 资源管理器](/iot-pnp/howto-use-iot-explorer)，然后将应用程序连接到 IoT 中心。

2. 选择 IoT 设备，并从导航菜单导航到“遥测”。

3. 选择“启动”以开始监视来自 IoT Edge 设备的输出。

![有效安装](media/gpu-deploy-sample-module/user-azure-iot-explorer-gpu.png)

## <a name="monitor-the-module-optional"></a>监视模块（可选）  

1. 在 VS Code 命令面板中，运行“Azure IoT 中心: 选择 IoT 中心”。

2. 选择包含要配置的 IoT Edge 设备的订阅和 IoT 中心。 在这种情况下，请选择用于部署 Azure Stack Edge 设备的订阅，并选择为 Azure Stack Edge 设备创建的 IoT Edge 设备。 在前面的步骤中通过 Azure 门户配置计算时，会发生这种情况。

3. 在 VS Code 资源管理器中，展开“Azure IoT 中心”部分。 在“设备”下，应会看到与 Azure Stack Edge 设备对应的 IoT Edge 设备。 

    1. 选择该设备，右键单击，然后选择“开始监视内置事件终结点”。

    2. 转到“设备”>“模块”，应会看到“GPU 模块”正在运行 。

    3. VS Code 终端还应显示 IoT 中心事件，这些事件显示为 Azure Stack Edge 设备的监视输出。

    ![有效安装是](media/gpu-deploy-sample-module/gpu-monitor-events-output.png)

    你可看到 GPU 执行同一组操作（5000 次形状转换迭代）所用时间比 CPU 所用时间要少得多。

## <a name="next-steps"></a>后续步骤

  - 详细了解 [Azure Stack Hub 上的图形处理单元 (GPU) 虚拟机 (VM)](gpu-vms-about.md)

  - 详细了解 Azure Stack Hub、Data Box Edge 和智能边缘，[计算的未来：智能云和智能边缘](https://azure.microsoft.com/overview/future-of-cloud)

  - 要详细了解混合云应用程序，请参阅[混合云解决方案](https://docs.microsoft.com/hybrid/app-solutions/)