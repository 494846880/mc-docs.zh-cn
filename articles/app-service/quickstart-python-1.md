---
title: 快速入门：创建适用于 Linux 的 Python 应用
description: 将 Python 应用部署到应用服务中的 Linux 容器即可开始使用 Azure 应用服务。
ms.topic: quickstart
origin.date: 09/22/2020
ms.date: 11/30/2020
ms.author: v-tawe
ms.custom: seo-python-october2019, cli-validate, devx-track-python
zone_pivot_groups: python-frameworks-01
adobe-target: true
ms.openlocfilehash: 7052bfea0c7d17069d8e7a8ce401d6cc31faea5f
ms.sourcegitcommit: a1f565fd202c1b9fd8c74f814baa499bbb4ed4a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96507304"
---
<!--Verified Successfully-->
# <a name="quickstart-create-a-python-app-in-azure-app-service-for-linux"></a>快速入门：在适用于 Linux 的 Azure 应用服务中创建 Python 应用

在本快速入门中，需将 Python Web 应用部署到 [Linux 上的应用服务](overview.md#app-service-on-linux)，该版本提供了一项高度可缩放、自我修补的 Azure Web 托管服务。 在 Mac、Linux 或 Windows 计算机上，可使用本地 [Azure 命令行界面 (CLI)](https://docs.azure.cn/cli/install-azure-cli) 通过 Flask 或 Django 框架来部署示例。 配置的 Web 应用使用免费的应用服务层，因此本文中的操作不会产生任何费用。

> [!TIP]
> 如果希望通过 IDE 部署应用，请参阅[从 Visual Studio Code 将 Python 应用部署到应用服务](https://docs.microsoft.com/azure/developer/python/tutorial-deploy-app-service-on-linux-01)。

## <a name="set-up-your-initial-environment"></a>设置初始环境

1. 具有活动订阅的 Azure 帐户。 [创建试用订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn/)。
1. 安装 <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 或更高版本</a>。
1. 安装 <a href="https://docs.azure.cn/cli/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 或更高版本，使用它可以在任何 shell 中运行命令来预配和配置 Azure 资源。

打开终端窗口并检查 Python 版本是否为 3.6 或更高版本：

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

检查 Azure CLI 版本是否为 2.0.80 或更高版本：

```azurecli
az --version
```

然后通过 CLI 登录到 Azure：

```azurecli
az cloud set -n AzureChinaCloud
az login
```

此命令将打开浏览器以获取凭据。 命令完成后，会显示包含订阅相关信息的 JSON 输出。

登录后，可以使用 Azure CLI 运行 Azure 命令，处理订阅中的资源。

[存在问题？请告诉我们。](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clone-the-sample"></a>克隆示例

使用以下命令克隆示例存储库，并导航到示例文件夹。 （如果尚未安装 git，请[安装 git](https://git-scm.com/downloads)。）

::: zone pivot="python-framework-flask"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

然后导航到该文件夹：

```terminal
cd python-docs-hello-world
```
::: zone-end

::: zone pivot="python-framework-django"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-django
```

然后导航到该文件夹：

```terminal
cd python-docs-hello-django
```
::: zone-end

示例包含 Azure 应用服务在启动应用时可以识别的框架特定代码。 有关详细信息，请参阅[容器启动过程](configure-language-python.md#container-startup-process)。

[存在问题？请告诉我们。](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="deploy-the-sample"></a>部署示例

使用 `az webapp up` 命令在本地文件夹 (python-docs-hello-world) 中部署代码：

```azurecli
az webapp up --sku F1 --name <app-name>
```

- 如果无法识别 `az` 命令，请确保按照[设置初始环境](#set-up-your-initial-environment)中所述安装 Azure CLI。
- 如果无法识别 `webapp` 命令，请确保 Azure CLI 版本为 2.0.80 或更高版本。 如果不是，请[安装最新版本](https://docs.azure.cn/cli/install-azure-cli)。
- 将 `<app_name>` 替换为在整个 Azure 中均唯一的名称（有效字符为 `a-z`、`0-9` 和 `-`）。 良好的模式是结合使用公司名称和应用标识符。
- `--sku F1` 参数在“免费”定价层上创建 Web 应用。 省略此参数可使用更快的高级层，这会按小时计费。
- 可以选择包含参数 `--location <location-name>`，其中 `<location_name>` 是可用的 Azure 区域。 可以运行 [`az account list-locations`](https://docs.azure.cn/cli/appservice#az_appservice_list_locations) 命令来检索 Azure 帐户的允许区域列表。
- 如果看到错误“无法自动检测应用的运行时堆栈”，请确保在包含 requirements.txt 文件的 python-docs-hello-world 文件夹 (Flask) 或 python-docs-hello-django 文件夹 (Django) 中运行该命令 。 （请参阅[通过 az webapp up 解决自动检测问题](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md) (GitHub)。）

此命令可能需要花费几分钟时间完成。 运行时，它提供以下相关信息：创建资源组、应用服务计划和托管应用，配置日志记录，然后执行 ZIP 部署。 然后，它会显示消息“可以通过 http://&lt;app-name&gt;.chinacloudsites.cn 启动应用”（该网址是 Azure 上应用的 URL）。

![az webapp up 命令的示例输出](./media/quickstart-python/az-webapp-up-output.png)

[存在问题？请告诉我们。](https://aka.ms/FlaskCLIQuickstartHelp)

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>浏览到应用

在 Web 浏览器中使用以下 URL 浏览到已部署的应用程序：`http://<app-name>.chinacloudsites.cn`。 最初启动应用需要几分钟时间。

Python 示例代码在使用内置映像的应用服务中运行 Linux 容器。

![在 Azure 中运行示例 Python 应用](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

祝贺你！ 现已将 Python 应用部署到应用服务。

[存在问题？请告诉我们。](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="run-the-sample"></a>运行示例 

::: zone pivot="python-framework-flask"
1. 确保位于 python-docs-hello-world 文件夹中。 

1. 创建虚拟环境并安装依赖项：

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    如果遇到“[Errno 2] 没有此类文件或目录:“requirements.txt”。”，请确保位于 python-docs-hello-world 文件夹中。

1. 运行开发服务器。

    ```terminal  
    flask run
    ```
    
    默认情况下，该服务器假定应用的条目模块位于示例使用的 app.py 中。 （如果使用其他模块名称，请将 `FLASK_APP` 环境变量设置为该名称。）

1. 打开 Web 浏览器并转到 `http://localhost:5000/` 处的示例应用。 该应用显示“Hello, World!”消息。

    ![在本地运行示例 Python 应用](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. 在终端窗口中，按 Ctrl+C 退出开发服务器 。
::: zone-end

::: zone pivot="python-framework-django"
1. 确保位于 python-docs-hello-django 文件夹中。 

1. 创建虚拟环境并安装依赖项：

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    如果遇到“[Errno 2] 没有此类文件或目录:“requirements.txt”。”，请确保位于 python-docs-hello-django 文件夹中。
    
1. 运行开发服务器。

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    python3 manage.py runserver
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -3 manage.py runserver
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -3 manage.py runserver
    ```

    ---    

1. 打开 Web 浏览器并转到 `http://localhost:8000/` 处的示例应用。 该应用显示“Hello, World!”消息。

    ![在本地运行示例 Python 应用](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. 在终端窗口中，按 Ctrl+C 退出开发服务器 。
::: zone-end

[存在问题？请告诉我们。](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="redeploy-updates"></a>重新部署更新

在本部分中，你将对代码进行少量更改，然后将代码重新部署到 Azure。 代码更改包括 `print` 语句，用于生成要在下一部分使用的日志记录输出。

::: zone pivot="python-framework-flask"
在编辑器中打开 app.py，并更新 `hello` 函数以匹配以下代码。 

```python
def hello():
    print("Handling request to home page.")
    return "Hello, Azure!"
```
::: zone-end
::: zone pivot="python-framework-django"
在编辑器中打开 hello/views.py，并更新 `hello` 函数以匹配以下代码。

```python
def hello(request):
    print("Handling request to home page.")
    return HttpResponse("Hello, Azure!")
```
::: zone-end

保存更改，然后再次使用 `az webapp up` 命令重新部署应用：

```azurecli
az webapp up
```

此命令使用本地缓存在 .azure/config 文件中的值，包括应用名称、资源组和应用服务计划。

部署完成后，切换回打开到 `http://<app-name>.chinacloudsites.cn` 的浏览器窗口。 刷新页面，刷新后的页面应显示修改后的消息：

![在 Azure 中运行更新的示例 Python 应用](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

[存在问题？请告诉我们。](https://aka.ms/FlaskCLIQuickstartHelp)

> [!TIP]
> Visual Studio Code 为 Python 和 Azure 应用服务提供了功能强大的扩展，简化了将 Python Web 应用部署到应用服务的过程。 有关详细信息，请参阅[将 Python 应用从 Visual Studio Code 部署到应用服务](https://docs.microsoft.com/azure/developer/python/tutorial-deploy-app-service-on-linux-01)。

## <a name="stream-logs"></a>流式传输日志

可以访问应用内和运行应用的容器所生成的控制台日志。 这些日志包括使用 `print` 语句生成的任何输出。

若要流式传输日志，请运行 [az webapp log tail](https://docs.azure.cn/cli/webapp/log?view=azure-cli-latest&preserve-view=true#az_webapp_log_tail) 命令：

```azurecli
az webapp log tail
```

还可以在 `az webapp up` 命令中包含 `--logs` 参数，以在部署时自动打开日志流。

在浏览器中刷新应用以生成控制台日志，其中包括描述对应用的 HTTP 请求的消息。 如果未立即显示输出，请在 30 秒后重试。

也可通过浏览器在 `https://<app-name>.scm.chinacloudsites.cn/api/logs/docker` 中检查日志文件。

若要随时停止日志流式处理，请在终端中按 Ctrl+C。

[存在问题？请告诉我们。](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="manage-the-azure-app"></a>管理 Azure 应用

转到 <a href="https://portal.azure.cn" target="_blank">Azure 门户</a>管理已创建的应用。 搜索并选择“应用服务”。

![在 Azure 门户中导航到应用服务](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

选择 Azure 应用名称。

![在 Azure 门户的应用程序服务中导航到 Python 应用](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

选择该应用将打开其“概述”页，你可以在其中执行基本的管理任务，例如浏览、停止、启动、重启和删除。

![在 Azure 门户的“概述”页中管理 Python 应用](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

应用服务菜单提供了用于配置应用的不同页面。

[存在问题？请告诉我们。](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clean-up-resources"></a>清理资源

在前面的步骤中，你在资源组中创建了 Azure 资源。 资源组根据位置得名，例如“appsvc_rg_Linux_chinanorth”。 如果使用 F1 免费层以外的应用服务 SKU，这些资源会持续产生费用（请参阅[应用服务定价](https://www.azure.cn/pricing/details/app-service/linux/)）。

如果你认为将来不再需要这些资源，请运行以下命令删除资源组：

```azurecli
az group delete --no-wait
```

此命令使用 .azure/config 文件中缓存的资源组名称。

`--no-wait` 参数允许此命令在操作完成之前返回。

[存在问题？请告诉我们。](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 PostgreSQL 的 Python (Django) Web 应用](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [配置 Python 应用](configure-language-python.md)

> [!div class="nextstepaction"]
> [将用户登录添加到 Python Web 应用](../active-directory/develop/quickstart-v2-python-webapp.md)

<!--[Tutorial: Run Python app in custom container](tutorial-custom-container.md)-->

<!-- Update_Description: new article about quickstart python 1 -->
<!--NEW.date: 11/30/2020-->