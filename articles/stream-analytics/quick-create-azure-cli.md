---
title: 快速入门 - 使用 Azure CLI 创建 Azure 流分析作业
description: 本快速入门介绍如何使用 Azure CLI 创建 Azure 流分析作业。
services: stream-analytics
ms.service: stream-analytics
author: Johnnytechn
ms.author: v-johya
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 12/11/2020
ms.openlocfilehash: 954fc6421fab2e007ca323f67a35b0196bcb62f1
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105304"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-using-the-azure-cli"></a>快速入门：使用 Azure CLI 创建 Azure 流分析作业

在本快速入门中，将使用 Azure CLI 定义一个流分析作业，以便筛选温度读数高于 27 的实时传感器消息。 流分析作业会从 IoT 中心读取数据，对数据进行转换，然后将数据写回到 Blob 存储中的容器。 在本快速入门中使用的输入数据由 Raspberry Pi 联机模拟器生成。

## <a name="before-you-begin"></a>开始之前

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

## <a name="prepare-your-environment"></a>准备环境

1. 登录。

   如果使用的是 CLI 的本地安装，请使用 [az login](/cli/reference-index#az-login) 命令登录。

    ```azurecli
    az login
    ```

    遵循终端中显示的步骤完成身份验证过程。

2. 安装 Azure CLI 扩展。

   使用 Azure CLI 的扩展引用时，必须先安装该扩展。  借助 Azure CLI 扩展，可访问尚未在核心 CLI 中提供的试验性和预发布的命令。  若要详细了解包含更新和卸载的扩展，请参阅[使用 Azure CLI 的扩展](/cli/azure-cli-extensions-overview)。

   运行以下命令，安装[流分析扩展](/cli/ext/stream-analytics/stream-analytics)：

    ```azurecli
    az extension add --name stream-analytics
    ```

   运行以下命令，安装 [Azure IoT 扩展](/cli/ext/azure-iot)：

    ```azurecli
    az extension add --name azure-iot
    ```

3. 创建资源组。

   必须将所有 Azure 资源部署到资源组。 使用资源组可以组织和管理相关的 Azure 资源。

   对于本快速入门，请使用以下 [az group create](/cli/group#az-group-create) 命令在 chinanorth 位置创建名为 streamanalyticsrg 的资源组 ：

   ```azurecli
   az group create --name streamanalyticsrg --location chinanorth
   ```

## <a name="prepare-the-input-data"></a>对输入数据进行准备

在定义流分析作业之前，请准备用于作业输入的数据。

以下 Azure CLI 代码块是准备作业所需的输入数据的命令。 查看介绍代码的部分。

1. 使用 [az iot hub create](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub) 命令创建 IoT 中心。 此示例创建名为 **MyASAIoTHub** 的 IoT 中心。 由于 IoT 中心名称是唯一的，因此需使用你自己的 IoT 中心名称。 将 SKU 设置为 F1 即可使用免费层，前提是它在订阅中可用。 否则，请选择下一个最低的层。

    ```azurecli
    az iot hub create --name "MyASAIoTHub" --resource-group streamanalyticsrg --sku S1
    ```

    创建 IoT 中心以后，请使用 [az iot hub show-connection-string](/cli/iot/hub) 命令获取 IoT 中心连接字符串。 复制整个连接字符串并将其保存。这样，在将 IoT 中心作为输入添加到流分析作业时，就可以使用该字符串。

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

2. 使用 [az iothub device-identity create](../iot-hub/quickstart-send-telemetry-c.md#register-a-device) 命令将设备添加到 IoT 中心。 此示例创建名为 **MyASAIoTDevice** 的设备。

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

3. 使用 [az iot hub device-identity show-connection-string](/cli/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) 命令获取设备连接字符串。 复制整个连接字符串并将其保存。这样，在创建 Raspberry Pi 模拟器时，就可以使用该字符串。

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **输出示例：**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-a-blob-storage-account"></a>创建 Blob 存储帐户

以下 Azure CLI 代码块创建用于作业输出的 blob 存储帐户。 查看介绍代码的部分。

1. 使用 [az storage account create](/cli/storage/account) 命令创建常规用途存储帐户。 常规用途的存储帐户可用于以下四种服务：Blob、文件、表和队列。

   请务必将尖括号中的占位符值替换为你自己的值：

   ```azurecli
   az storage account create \
       --name <storage-account> \
       --resource-group streamanalyticsrg \
       --location chinanorth \
       --sku Standard_LRS \
       --encryption-services blob
   ```
<!--Not available in MC: Standard_ZRS-->

2. 通过运行 [az storage account keys list](/cli/storage/account/keys) 命令获取存储帐户的密钥。 保存此密钥以便在下一步中使用。

   ```azurecli
   az storage account keys list -g streamanalyticsrg -n <storage-account>
   ```

3. 可以使用 [az storage container create](/cli/storage/container) 命令创建用于存储 blob 的容器。 使用存储帐户密钥来授权操作创建容器。 有关使用 Azure CLI 授权数据操作的详细信息，请参阅[使用 Azure CLI 授权访问 blob 或队列数据](../storage/common/authorize-data-operations-cli.md)。

   ```azurecli
   az storage container create \
       --account-name <storage-account> \
       --name sample-container \
       --account-key <key>
       --auth-mode key
   ```

## <a name="create-a-stream-analytics-job"></a>创建流分析作业

以下 Azure CLI 代码块创建流分析作业。 查看介绍代码的部分

1. 使用 [az stream-analytics job create](/cli/ext/stream-analytics/stream-analytics/job#ext-stream-analytics-az-stream-analytics-job-create) 命令创建流分析作业。

```azurecli
az stream-analytics job create \
    --resource-group streamanalyticsrg 
    --name streamanalyticsjob \
    --location chinanorth \
    --output-error-policy "Drop" \
    --events-outoforder-policy "Drop" \
    --events-outoforder-max-delay 5 \
    --events-late-arrival-max-delay 16 \     
    --data-locale "en-US"
```

## <a name="configure-input-to-the-job"></a>配置作业输入

使用 [az stream-analytics input](/cli/ext/stream-analytics/stream-analytics/input#ext-stream-analytics-az-stream-analytics-input-create) cmdlet 将输入添加到作业。 此 cmdlet 使用作业名称、作业输入名称、资源组名称和作业输入定义作为参数。 作业输入定义是一个 JSON 文件，其中包含配置作业的输入所需的属性。 在此示例中，需将 IoT 中心创建为输入。

在本地计算机上创建名为 `datasource.json` 的文件，并向其添加以下 JSON 数据。 确保将 `sharedAccessPolicyKey` 的值替换为在上一部分保存的 IoT 中心设备连接字符串的 `SharedAccessKey` 部分。

```json
{
    "type": "Microsoft.Devices/IotHubs",
    "properties": {
        "iotHubNamespace": "iothub",
        "sharedAccessPolicyName": "iothubowner",
        "sharedAccessPolicyKey": "sharedAccessPolicyKey=",
        "consumerGroupName": "sdkconsumergroup",
        "endpoint": "messages/events"
    }
}
```

在本地计算机上创建名为 `serialization.json` 的文件，并向其添加以下 JSON 数据。

```json
{
     "type": "Json",
     "properties": {
         "encoding": "UTF8"
     }
}
```

接下来运行 `az stream-analytics input create` cmdlet。 确保用存储作业输入定义 JSON 文件的路径替换 `datasource` 变量的值，用存储序列化 JSON 文件的路径替换 `serialization` 变量的值。

```azurecli
az stream-analytics input create \
    --resource-group streamanalyticsrg 
    --job-name streamanalyticsjob \
    --name asaiotinput \
    --type Stream \
    --datasource datasource.json \
    --serialization serialization.json
```

## <a name="configure-output-to-the-job"></a>配置作业输出

使用 [az stream-analytics output create](/cli/ext/stream-analytics/stream-analytics/output#ext-stream-analytics-az-stream-analytics-output-create) cmdlet 将输出添加到作业。 此 cmdlet 使用作业名称、作业输出名称、资源组名称和作业输出定义作为参数。 作业输出定义是一个 JSON 文件，其中包含配置作业的输出所需的属性。 此示例使用 Blob 存储作为输出。

在本地计算机上创建名为 `datasink.json` 的文件，并向其添加以下 JSON 数据。 确保将 `accountKey` 的值替换为存储帐户的访问密钥，该密钥是存储在 $storageAccountKey 值中的值。

```json
{
    "type": "Microsoft.Storage/Blob",
    "properties": {
        "storageAccounts": [
            {
                "accountName": "<storage-account>",
                "accountKey": "accountKey=="
            }
        ],
        "container": "state",
        "pathPattern": "{date}/{time}",
        "dateFormat": "yyyy/MM/dd",
        "timeFormat": "HH"
    }
}
```

接下来运行 `az stream-analytics output` cmdlet。 确保用存储作业输出定义 JSON 文件的路径替换 `datasource` 变量的值，用存储序列化 JSON 文件的路径替换 `serialization` 变量的值。

```azurecli
az stream-analytics output create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name asabloboutput \
    --datasource datasink.json \
    --serialization serialization.json
```

## <a name="define-the-transformation-query"></a>定义转换查询

使用 [az stream-analytics transformation create](/cli/ext/stream-analytics/stream-analytics/transformation#ext-stream-analytics-az-stream-analytics-transformation-create) cmdlet 将转换添加到作业。 此 cmdlet 使用作业名称、作业转换名称、资源组名称和作业转换定义作为参数。 

运行 `az stream-analytics transformation create` cmdlet。

```azurecli
az stream-analytics transformation create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name Transformation \
    --streaming-units "6" \
    --transformation-query "SELECT * INTO asabloboutput FROM asaiotinput HAVING Temperature > 27"
```
## <a name="run-the-iot-simulator"></a>运行 IoT 模拟器

1. 打开 [Raspberry Pi Azure IoT 联机模拟器](https://azure-samples.github.io/raspberry-pi-web-simulator/)。

2. 将第 15 行的占位符替换为在上一部分保存的整个 Azure IoT 中心设备连接字符串。

3. 单击 **“运行”** 。 输出会显示传感器数据和发送到 IoT 中心的消息。

    ![Raspberry Pi Azure IoT 联机模拟器](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>启动流分析作业并检查输出

使用 [az stream-analytics job start](/cli/ext/stream-analytics/stream-analytics/job#ext-stream-analytics-az-stream-analytics-job-start) cmdlet 启动作业。 此 cmdlet 使用作业名称、资源组名称、输出启动模式和启动时间作为参数。 `OutputStartMode` 接受的值为 `JobStartTime`、`CustomTime` 或 `LastOutputEventTime`。

以下 cmdlet 在运行以后会返回 `True` 作为输出（如果作业启动）。 在存储容器中，创建的输出文件夹包含已转换的数据。

```azurecli
az stream-analytics job start \
    --resource-group streamanalyticsrg \
    --name streamanalyticsjob \
    --output-start-mode JobStartTime
```

## <a name="clean-up-resources"></a>清理资源

若不再需要资源组、流式处理作业以及所有相关资源，请将其删除。 删除作业可避免对作业使用的流单元进行计费。 如果计划在将来使用该作业，可以跳过删除它的操作，暂时只需停止该作业。 如果不打算继续使用该作业，请运行以下 cmdlet，删除本快速入门创建的所有资源：

```azurecli
az group delete \
    --name streamanalyticsrg \
    --no-wait
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你使用 Azure CLI 部署了一个简单的流分析作业。 你还可以使用 [Azure 门户](stream-analytics-quick-create-portal.md)部署流分析作业。

若要了解如何配置其他输入源并执行实时检测，请继续阅读以下文章：

> [!div class="nextstepaction"]
> [使用 Azure 流分析实时检测欺诈行为](stream-analytics-real-time-fraud-detection.md)

