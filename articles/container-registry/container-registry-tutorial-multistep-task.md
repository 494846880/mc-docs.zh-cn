---
title: 教程 - 多步骤 ACR 任务
description: 本教程介绍如何配置一个 Azure 容器注册表任务，以便在向 Git 存储库提交源代码时，在云中自动触发多步骤工作流来生成、运行和推送容器映像。
ms.topic: tutorial
origin.date: 11/24/2020
author: rockboyfor
ms.date: 12/14/2020
ms.author: v-yeche
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 5784c85b62de05a6983bc74e4db8a0dae86bbf01
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004036"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>教程：提交源代码时在云中运行多步骤容器工作流

除[快速任务](container-registry-tutorial-quick-task.md)以外，ACR 任务还支持在向 Git 存储库提交源代码时可自动触发的多步骤多容器式工作流。 

本教程介绍如何使用示例 YAML 文件来定义多步骤任务，以便在提交源代码时生成、运行一个或多个容器映像并将其推送到注册表。 若要创建仅在提交代码时自动生成单个映像的任务，请参阅[教程：提交源代码时在云中自动化容器映像生成](container-registry-tutorial-build-task.md)。 有关 ACR 任务的概述，请参阅[使用 ACR 任务自动执行 OS 和框架修补](container-registry-tasks-overview.md)。

本教程的内容：

> [!div class="checklist"]
> * 使用 YAML 文件定义多步骤任务
> * 创建任务
> * （可选）将凭据添加到该任务，以便能够访问注册表
> * 测试任务
> * 查看任务状态
> * 使用代码提交触发任务

本教程假设你已完成[前面教程](container-registry-tutorial-quick-task.md)中的任务。 如果尚未完成，请先完成前面教程[先决条件](container-registry-tutorial-quick-task.md#prerequisites)部分中的步骤，再继续操作。

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

## <a name="create-a-multi-step-task"></a>创建多步骤任务

完成启用 ACR 任务以读取提交状态及在存储库中创建 Webhook 所需的步骤后，请创建一个用于触发容器映像生成、运行和推送的多步骤任务。

### <a name="yaml-file"></a>YAML 文件

在 [YAML 文件](container-registry-tasks-reference-yaml.md)中定义多步骤任务的步骤。 本教程的第一个示例多步骤任务在 `taskmulti.yaml` 文件中定义，该文件位于克隆的 GitHub 存储库的根目录：

```yml
version: v1.1.0
steps:
# Build target image
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push image
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
```

此多步骤任务执行以下操作：

1. 运行 `build` 步骤，以基于工作目录中的 Dockerfile 生成映像。 该映像面向 `Run.Registry`（运行任务的注册表），并以唯一的 ACR 任务运行 ID 进行标记。 
1. 运行 `cmd` 步骤，以在临时容器中运行该映像。 此示例在后台启动一个长时间运行的容器，返回容器 ID，然后停止该容器。 在实际方案中，可以包含用于测试运行中容器的步骤，以确保该容器正常运行。
1. 在 `push` 步骤中，将生成的映像推送到运行注册表。

### <a name="task-command"></a>任务命令

首先，使用适用于环境的值填充这些 shell 环境变量。 此步骤并非必须执行的步骤，但它能让在此教程中执行多个 Azure CLI 命令更容易。 如果未填充这些环境变量，则每当示例命令中出现每个值，都必须手动替换该值。

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

现在，请执行以下 [az acr task create][az-acr-task-create] 命令创建该任务：

```azurecli
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

此任务指定，每当向 `--context` 指定的主分支存储库提交代码时，ACR 任务都要基于该分支中的代码运行该多步骤任务。 存储库根目录中的 `--file` 指定的 YAML 文件将定义步骤。 

成功的 [az acr task create][az-acr-task-create] 命令的输出应如下所示：

```output
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2020-11-20T03:14:31.763887+00:00",
  "credentials": null,
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskmulti",
  "location": "chinanorth",
  "name": "example1",
  "platform": {
    "architecture": "amd64",
    "os": "linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "status": "Enabled",
  "step": {
    "baseImageDependencies": null,
    "contextAccessToken": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git",
    "taskFilePath": "taskmulti.yaml",
    "type": "FileTask",
    "values": [],
    "valuesFilePath": null
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "master",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git",
          "sourceControlAuthProperties": null,
          "sourceControlType": "Github"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-multi-step-workflow"></a>测试多步骤工作流

若要测试多步骤任务，请执行 [az acr task run][az-acr-task-run] 命令手动将其触发：

```azurecli
az acr task run --registry $ACR_NAME --name example1
```

默认情况下，执行此命令时，`az acr task run` 命令会将日志流式传输到控制台。 输出显示每个任务步骤的运行进度。 以下输出经过简化，只显示关键步骤。

```output
Queued a run with ID: cab
Waiting for an agent...
2020/11/20 00:03:31 Downloading source code...
2020/11/20 00:03:33 Finished downloading source code
2020/11/20 00:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2020/11/20 00:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2020/11/20 00:03:34 Successfully set up Docker network: acb_default_network
2020/11/20 00:03:34 Setting up Docker configuration...
2020/11/20 00:03:34 Successfully set up Docker configuration
2020/11/20 00:03:34 Logging in to registry: myregistry.azurecr.cn
2020/11/20 00:03:35 Successfully logged into myregistry.azurecr.cn
2020/11/20 00:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:35 Scanning for dependencies...
2020/11/20 00:03:36 Successfully scanned dependencies
2020/11/20 00:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB
[...]
Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.cn/hello-world:cf19
2020/11/20 00:03:43 Successfully executed container: acb_step_0
2020/11/20 00:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2020/11/20 00:03:44 Successfully executed container: acb_step_1
2020/11/20 00:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:44 Pushing image: myregistry.azurecr.cn/hello-world:cf19, attempt 1
[...]
2020/11/20 00:03:46 Successfully pushed image: myregistry.azurecr.cn/hello-world:cf19
2020/11/20 00:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2020/11/20 00:03:46 Populating digests for step ID: acb_step_0...
2020/11/20 00:03:47 Successfully populated digests for step ID: acb_step_0
2020/11/20 00:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2020/11/20 00:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2020/11/20 00:03:47 The following dependencies were found:
2020/11/20 00:03:47
- image:
    registry: myregistry.azurecr.cn
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cab was successful after 18s
```

## <a name="trigger-a-build-with-a-commit"></a>使用命令触发生成

通过手动运行任务对其进行测试后，可通过更改源代码手动触发该任务。

首先，确保你位于包含[存储库][sample-repo]的本地克隆的目录中：

```console
cd acr-build-helloworld-node
```

接下来执行以下命令，创建新文件，并将其提交和推送给你在 GitHub 上的存储库分支：

```console
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

执行 `git push` 命令时可能需要提供 GitHub 凭据。 提供 GitHub 用户名并输入之前为密码创建的个人访问令牌 (PAT)。

```azurecli
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

将提交内容推送到存储库后，ACR 任务创建的 Webhook 将在 Azure 容器注册表中触发并启动该任务。 显示当前正在运行的任务的日志，以验证和监视生成进度：

```azurecli
az acr task logs --registry $ACR_NAME
```

输出结果类似于以下内容，显示当前执行（或最近执行）的任务：

```output
Showing logs of the last created run.
Run ID: cad

[...]

Run ID: cad was successful after 37s
```

## <a name="list-builds"></a>生成列表

若要查看 ACR 任务对注册表完成的任务运行列表，请运行 [az acr task list-runs][az-acr-task-list-runs] 命令：

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

该命令产生的输出应如下所示。 将显示 ACR 任务已执行的运行，并在最近执行的任务的 TRIGGER 列中显示“Git Commit”：

```output
RUN ID    TASK            PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------------  ---------- ---------   ---------  --------------------  ----------
cad       example1        linux       Succeeded  Commit     2020-11-20T00:22:15Z  00:00:35
cac       taskhelloworld  linux       Succeeded  Commit     2020-11-20T00:22:15Z  00:00:22
cab       example1        linux       Succeeded  Manual     2020-11-20T00:18:36Z  00:00:47
```

## <a name="create-a-multi-registry-multi-step-task"></a>创建多注册表多步骤任务

默认情况下，ACR 任务有权从运行任务的注册表推送或提取映像。 你可能想要运行一个除了面向运行注册表以外，还面向其他一个或多个注册表的多步骤任务。 例如，你可能需要在一个注册表中生成映像，并在由生产系统访问的另一个注册表中存储具有不同标记的映像。 此示例演示如何创建此类任务，并提供另一个注册表的凭据。

如果没有另一个注册表，请为此示例创建一个注册表。 如果需要注册表，请参阅[前面教程](container-registry-tutorial-quick-task.md)，或[快速入门：使用 Azure CLI 创建容器注册表](container-registry-get-started-azure-cli.md)。

若要创建任务，需要注册表登录服务器的名称，其格式为“mycontainerregistrydate.azurecr.cn”（全小写）。 此示例使用第二个注册表来存储按生成日期标记的映像。

### <a name="yaml-file"></a>YAML 文件

本教程的第二个示例多步骤任务在 `taskmulti-multiregistry.yaml` 文件中定义，该文件位于克隆的 GitHub 存储库的根目录：

```yml
version: v1.1.0
steps:
# Build target images
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
- build: -t {{.Values.regDate}}/hello-world:{{.Run.Date}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push images
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
  - {{.Values.regDate}}/hello-world:{{.Run.Date}}
```

此多步骤任务执行以下操作：

1. 运行两个 `build` 步骤，以基于工作目录中的 Dockerfile 生成映像：
    * 第一个映像面向 `Run.Registry`（运行任务的注册表），并以 ACR 任务运行 ID 进行标记。 
    * 第二个映像面向创建任务时设置的（或通过传递给 `az acr task create` 的外部 `values.yaml` 文件提供的）`regDate` 值标识的注册表。 此映像以运行日期进行标记。
1. 运行 `cmd` 步骤，以运行某个生成的容器。 此示例在后台启动一个长时间运行的容器，返回容器 ID，然后停止该容器。 在实际方案中，可以测试正在运行的容器，以确保该容器正常运行。
1. 在 `push` 步骤中，将生成的第一个映像推送到运行注册表，将生成的第二个映像推送到 `regDate` 标识的注册表。

### <a name="task-command"></a>任务命令

使用前面定义的 shell 环境变量，通过执行以下 [az acr task create][az-acr-task-create] 命令创建任务。 请将 *mycontainerregistrydate* 替换为你的注册表名称。

```azurecli
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti-multiregistry.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.cn
```

### <a name="add-task-credential"></a>添加任务凭据

若要将映像推送到 `regDate` 值标识的注册表，请使用 [az acr task credential add][az-acr-task-credential-add] 命令将该注册表的登录凭据添加到任务。

对于此示例，建议创建一个有权访问该注册表且范围为 *AcrPush* 角色的 [服务主体](container-registry-auth-service-principal.md)，以便能够推送映像。 若要创建服务主体，请参阅此 [Azure CLI 脚本](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh)。

在以下 `az acr task credential add` 命令中传递服务主体应用程序 ID 和密码。 请确保用第二个注册表的名称更新登录服务器名称 mycontainerregistrydate：

```azurecli
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.cn \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

该 CLI 会返回添加的注册表登录服务器的名称。

### <a name="test-the-multi-step-workflow"></a>测试多步骤工作流

与在前面的示例中一样，若要测试多步骤任务，请执行 [az acr task run][az-acr-task-run] 命令手动将其触发。 若要通过向 Git 存储库提交代码来触发任务，请参阅[通过提交触发生成](#trigger-a-build-with-a-commit)部分。

```azurecli
az acr task run --registry $ACR_NAME --name example2
```

默认情况下，执行此命令时，`az acr task run` 命令会将日志流式传输到控制台。 与前面一样，输出将显示每个任务步骤的运行进度。 该输出已经过简化，只显示关键步骤。

输出：

```output
Queued a run with ID: cf1g
Waiting for an agent...
2020/11/20 04:33:39 Downloading source code...
2020/11/20 04:33:41 Finished downloading source code
2020/11/20 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2020/11/20 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2020/11/20 04:33:43 Successfully set up Docker network: acb_default_network
2020/11/20 04:33:43 Setting up Docker configuration...
2020/11/20 04:33:44 Successfully set up Docker configuration
2020/11/20 04:33:44 Logging in to registry: mycontainerregistry.azurecr.cn
2020/11/20 04:33:45 Successfully logged into mycontainerregistry.azurecr.cn
2020/11/20 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.cn
2020/11/20 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.cn
2020/11/20 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:47 Scanning for dependencies...
2020/11/20 04:33:47 Successfully scanned dependencies
2020/11/20 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB
[...]
Successfully tagged mycontainerregistry.azurecr.cn/hello-world:cf1g
2020/11/20 04:33:55 Successfully executed container: acb_step_0
2020/11/20 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:55 Scanning for dependencies...
2020/11/20 04:33:56 Successfully scanned dependencies
2020/11/20 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB
[...]
Successfully tagged mycontainerregistrydate.azurecr.cn/hello-world:20190503-043342z
2020/11/20 04:33:57 Successfully executed container: acb_step_1
2020/11/20 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2020/11/20 04:33:58 Successfully executed container: acb_step_2
2020/11/20 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:58 Launching container with name: acb_step_3
test
2020/11/20 04:34:09 Successfully executed container: acb_step_3
2020/11/20 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:34:09 Pushing image: mycontainerregistry.azurecr.cn/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.cn/hello-world]
[...]
2020/11/20 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.cn/hello-world:cf1g
2020/11/20 04:34:12 Pushing image: mycontainerregistrydate.azurecr.cn/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.cn/hello-world]
[...]
2020/11/20 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.cn/hello-world:20190503-043342z
2020/11/20 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2020/11/20 04:34:19 Populating digests for step ID: acb_step_0...
2020/11/20 04:34:21 Successfully populated digests for step ID: acb_step_0
2020/11/20 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2020/11/20 04:34:21 Populating digests for step ID: acb_step_1...
2020/11/20 04:34:23 Successfully populated digests for step ID: acb_step_1
2020/11/20 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2020/11/20 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2020/11/20 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2020/11/20 04:34:23 The following dependencies were found:
2020/11/20 04:34:23
- image:
    registry: mycontainerregistry.azurecr.cn
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc
- image:
    registry: mycontainerregistrydate.azurecr.cn
    repository: hello-world
    tag: 20190503-043342z
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何创建一个在向 Git 存储库提交源代码时可自动触发的多个步骤多容器式任务。 有关多步骤任务的高级功能，包括并行和依赖性步骤执行，请参阅 [ACR 任务 YAML 参考](container-registry-tasks-reference-yaml.md)。 请转到下一教程来了解如何创建任务，用于在更新容器映像的基础映像时触发生成。

> [!div class="nextstepaction"]
> [在更新基础映像时自动生成](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->

[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->

[azure-cli]: https://docs.azure.cn/cli/install-azure-cli
[az-acr-task]: https://docs.azure.cn/cli/acr/task
[az-acr-task-create]: https://docs.azure.cn/cli/acr/task#az_acr_task_create
[az-acr-task-run]: https://docs.azure.cn/cli/acr/task#az_acr_task_run
[az-acr-task-list-runs]: https://docs.azure.cn/cli/acr/task#az_acr_task_list_runs
[az-acr-task-credential-add]: https://docs.azure.cn/cli/acr/task/credential#az_acr_task_credential_add    
[az-login]: https://docs.azure.cn/cli/reference-index#az_login

<!-- IMAGES -->

[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png

<!-- Update_Description: update meta properties, wording update, update link -->