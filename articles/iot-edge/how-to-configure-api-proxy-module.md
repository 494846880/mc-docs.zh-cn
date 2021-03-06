---
title: 配置 API 代理模块 - Azure IoT Edge | Microsoft Docs
description: 了解如何针对 IoT Edge 网关层次结构自定义 API 代理模块。
author: kgremban
manager: philmea
ms.author: v-tawe
origin.date: 11/10/2020
ms.date: 12/03/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 9478550182bc882981f006697b2843d4ee27cc23
ms.sourcegitcommit: 60e70acb6f9604aeef69d2027f7f96a1d7d5b248
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96541207"
---
# <a name="configure-the-api-proxy-module-for-your-gateway-hierarchy-scenario-preview"></a>配置适用于网关层次结构方案的 API 代理模块（预览版）

API 代理模块允许 IoT Edge 设备通过网关发送 HTTP 请求，而不是直接连接到云服务。 本文将介绍配置选项，以便你可以自定义该模块以满足网关层次结构要求。

>[!NOTE]
>此功能要求运行 Linux 容器的 IoT Edge 1.2 版本，该版本为公共预览版。

在某些网络体系结构中，网关后面的 IoT Edge 设备无法直接访问云。 尝试连接到云服务的任何模块都将失败。 API 代理模块将模块连接重新路由到各层网关层次结构，从而支持此配置中的下游 IoT Edge 设备。 它提供了一种安全方法，使客户端可通过 HTTPS 与多个服务通信，无需创建隧道，而是终止每一层的连接。 API 代理模块充当网关层次结构中 IoT Edge 设备之间的代理模块，直到它们到达顶层的 IoT Edge 设备。 此时，在顶层的 IoT Edge 设备上运行的服务将处理这些请求，API 代理模块将代理通过单个端口从本地服务到云中的所有 HTTP 流量。

API 代理模块可以为网关层次结构启用多种方案，包括允许较低层的设备拉取容器映像或将 blob 推送到存储。 可通过配置代理规则来定义数据的路由方式。 本文讨论了几种常见的配置选项。

## <a name="deploy-the-proxy-module"></a>部署代理模块

可从 Microsoft 容器注册表 (MCR) 获取 API 代理模块：`mcr.microsoft.com/azureiotedge-api-proxy:latest`。

还可直接从 Azure 市场部署 API 代理模块：[IoT Edge API 代理](https://market.azure.cn)。

## <a name="understand-the-proxy-module"></a>了解代理模块

API 代理模块利用 nginx 反向代理通过网络层路由数据。 代理内嵌在模块中，这意味着模块映像需要支持代理配置。 例如，如果代理正在侦听某个端口，则该模块需要打开该端口。

代理的开头为模块中嵌入的默认配置文件。 可使用[模块孪生](../iot-hub/iot-hub-devguide-module-twins.md)将新配置传递到该模块。 此外，还可使用环境变量在部署期间启用或禁用配置设置。

本文首先重点介绍默认配置文件以及如何使用环境变量来启用其设置。 然后我们将在最后讨论如何自定义配置文件。

### <a name="default-configuration"></a>默认配置

API 代理模块附带了支持常见方案并允许自定义的默认配置。 可通过模块的环境变量控制默认配置。

当前，默认环境变量包括：

| 环境变量 | 说明 |
| -------------------- | ----------- |
| `PROXY_CONFIG_ENV_VAR_LIST` | 列出要在以逗号分隔的列表中更新的所有变量。 此步骤可防止意外修改错误的配置设置。
| `NGINX_DEFAULT_PORT` | 更改 nginx 代理侦听的端口。 如果更新此环境变量，请确保所选端口在模块 dockerfile 中也处于公开状态，并在部署清单中声明为端口绑定。<br><br>默认端口为 443。<br><br>从 Azure 市场部署时，默认端口将更新为 8000，以防止与 edgeHub 模块冲突。 有关详细信息，请参阅[最小化开放端口数](#minimize-open-ports)。 |
| `DOCKER_REQUEST_ROUTE_ADDRESS` | 路由 docker 请求的地址。 将顶层设备上的此变量修改为指向注册表模块。<br><br>默认值为父主机名。 |
| `BLOB_UPLOAD_ROUTE_ADDRESS` | 路由 blob 注册表请求的地址。 将顶层设备上的此变量修改为指向 blob 存储模块。<br><br>默认值为父主机名。 |

## <a name="minimize-open-ports"></a>最小化开放端口数

为了最大程度地减少开放端口的数量，API 代理模块应转发所有 HTTPS 流量（端口 443），包括面向 edgeHub 模块的流量。 API 代理模块默认配置为重新路由端口 443 上的所有 edgeHub 流量。

使用以下步骤来配置部署，以最大程度地减少开放端口数：

1. 更新 edgeHub 模块设置，使其不绑定在端口 443 上，否则将出现端口绑定冲突。 默认情况下，edgeHub 模块绑定在端口443、5671 和 8883 上。 删除端口 443 绑定，其余两个绑定不变：

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. 配置 API 代理模块，使其绑定在端口 443 上。

   1. 将 NGINX_DEFAULT_PORT 环境变量的值设置为 `443`。
   1. 更新容器创建选项，使其绑定端口 443。

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

如果不需要将开放端口数减至最低，则可以让 edgeHub 模块使用端口 443，并将 API 代理模块配置为侦听另一个端口。 例如，API 代理模块可以通过将 NGINX_DEFAULT_PORT 环境变量的值设置为 `8000` 并为端口 8000 创建端口绑定，来侦听端口 8000。

## <a name="enable-container-image-download"></a>启用容器映像下载

API 代理模块的一个常见用例是启用较低层的 IoT Edge 设备来拉取容器映像。 此方案使用 [Docker 注册表模块](https://hub.docker.com/_/registry)从云中检索容器映像，并将它们缓存在顶层。 API 代理转发从较低的层（由顶层的注册表模块提供服务）下载容器映像的所有 HTTPS 请求。

此方案要求下游 IoT Edge 设备指向域名 `$upstream`，后面是 API 代理模块端口号，而不是映像的容器注册表。 例如：`$upstream:8000/azureiotedge-api-proxy:1.0`。

<!-- This use case is demonstrated in the tutorial [Create a hierarchy of IoT Edge devices using gateways](tutorial-nested-iot-edge.md). -->

在顶层配置以下模块：

* Docker 注册表模块
  * 为模块配置一个易记名称（如“注册表”），并公开该模块中的一个端口以接收请求。
  * 配置要映射到容器注册表的模块。
* API 代理模块
  * 配置以下环境变量：

    | “属性” | “值” |
    | ---- | ----- |
    | `DOCKER_REQUEST_ROUTE_ADDRESS` | 注册表模块名称和开放端口。 例如，`registry:5000`。 |
    | `NGINX_DEFAULT_PORT` | Nginx 代理对其进行侦听以获取来自下游设备的请求的端口。 例如，`8000`。 |

  * 配置以下 createOptions：

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

对于此方案，在任何较低层上配置以下模块：

* API 代理模块
  * 配置以下环境变量：

    | “属性” | “值” |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Nginx 代理对其进行侦听以获取来自下游设备的请求的端口。 例如，`8000`。 |

  * 配置以下 createOptions：

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```  

## <a name="enable-blob-upload"></a>启用 blob 上传

API 代理模块的另一个用例是启用较低层的 IoT Edge 设备来上传 blob。 此用例支持在较低层设备上使用故障排除功能，如上传模块日志或上传支持捆绑包。

此方案使用顶层的 [IoT Edge 上的 Azure Blob 存储](https://market.azure.cn)模块来处理 blob 创建和上载。

在顶层配置以下模块：

* IoT Edge 上的 Azure Blob 存储模块。
* API 代理模块
  * 配置以下环境变量：

    | “属性” | “值” |
    | ---- | ----- |
    | `BLOB_UPLOAD_ROUTE_ADDRESS` | Blob 存储模块名称和开放端口。 例如，`azureblobstorageoniotedge:1102`。 |
    | `NGINX_DEFAULT_PORT` | Nginx 代理对其进行侦听以获取来自下游设备的请求的端口。 例如，`8000`。 |

  * 配置以下 createOptions：

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

对于此方案，在任何较低层上配置以下模块：

* API 代理模块
  * 配置以下环境变量：

    | “属性” | “值” |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Nginx 代理对其进行侦听以获取来自下游设备的请求的端口。 例如，`8000`。 |

  * 配置以下 createOptions：

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

使用以下步骤将支持捆绑包或日志文件上传到位于顶层的 blob 存储模块：

1. 使用 Azure 存储资源管理器或 REST API 创建 blob 容器。 有关详细信息，请参阅[使用 IoT Edge 上的 Azure Blob 存储在边缘中存储数据](how-to-store-data-blob.md)。
1. 根据[检索 IoT Edge 部署中的日志](how-to-retrieve-iot-edge-logs.md)中的步骤来请求日志或支持捆绑包上传，但使用域名 `$upstream` 和开放代理端口来代替 blob 存储模块地址。 例如：

   ```json
   {
      "schemaVersion": "1.0",
      "sasUrl": "https://$upstream:8000/myBlobStorageName/myContainerName?SAS_key",
      "since": "2d",
      "until": "1d",
      "edgeRuntimeOnly": false
   }
   ```

## <a name="edit-the-proxy-configuration"></a>编辑代理配置

默认配置文件内嵌在 API 代理模块中，但你可以使用模块孪生通过云将新配置传递到该模块。

当你编写自己的配置时，仍可以使用环境来调整每个部署的设置。 使用以下语法：

* 使用 `${MY_ENVIRONMENT_VARIABLE}` 检索环境变量的值。
* 使用条件语句根据环境变量的值启用或禁用设置：

   ```conf
   #if_tag ${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 1
   #endif_tag ${MY_ENVIRONMENT_VARIABLE}

   #if_tag !${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 0
   #endif_tag !${MY_ENVIRONMENT_VARIABLE}
   ```

当 API 代理模块分析代理配置时，它首先使用替换将 `PROXY_CONFIG_ENV_VAR_LIST` 中列出的所有环境变量替换为它们所提供的值。 然后，将替换 `#if_tag` 和 `#endif_tag` 对之间的所有内容。 然后，该模块向 nginx 反向代理提供经过分析的配置。

若要动态更新代理配置，请使用以下步骤：

1. 编写配置文件。 可使用此默认模板作为参考：[nginx_default_config.conf](https://github.com/Azure/iotedge/blob/master/edge-modules/api-proxy-module/templates/nginx_default_config.conf)
1. 复制配置文件的文本，并将其转换为 base64。
1. 将已编码的配置文件粘贴为模块孪生中 `proxy_config` 所需属性的值。

   ![将已编码的配置文件粘贴为 proxy_config 属性的值](./media/how-to-configure-api-proxy-module/change-config.png)

<!-- ## Next steps

Use the API proxy module to [Connect a downstream IoT Edge device to an Azure IoT Edge gateway](how-to-connect-downstream-iot-edge-device.md). -->