---
author: glecaros
ms.service: cognitive-services
ms.topic: include
origin.date: 10/15/2020
ms.date: 11/20/2020
ms.author: v-tawe
ms.openlocfilehash: 34abc0e3c31adc7c0e4962bb2618117a5238657c
ms.sourcegitcommit: c2c9dc65b886542d220ae17afcb1d1ab0a941932
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94977649"
---
本指南介绍如何安装用于 Linux 的[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>系统要求

Linux：请参阅[受支持的 Linux 分发和目标体系结构](~/articles/cognitive-services/speech-service/speech-sdk.md)的列表。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，你需要：

* gcc
* [Go 二进制文件（1.13 或更高版本）](https://golang.org/dl/)

* 支持的 Linux 平台会要求安装某些库（要求安装 `libssl` 以获取安全套接字层支持；要求安装 `libasound2` 以获取声音支持）。 请参阅下面的发行版，了解安装这些库的正确版本所需的命令。

   * 在 Ubuntu/Debian 上：

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

     如果 libssl1.0.0 不可用，则改为安装 libssl1.0.x（其中 x 大于 0）或 libssl1.1。

   * 在 RHEL/CentOS 上：

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> - 在 RHEL/CentOS 7 上，按照[如何为语音 SDK 配置 RHEL/CentOS 7](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md) 上的说明进行操作。
> - 在 RHEL/CentOS 8 上，按照[如何配置 OpenSSL for Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md) 上的说明进行操作。

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]


## <a name="configure-go-environment"></a>配置 Go 环境

1. 由于绑定依赖于 `cgo`，因此需要设置环境变量，以便 Go 可以找到 SDK：

   ```sh
   export CGO_CFLAGS="-I$SPEECHSDK_ROOT/include/c_api"
   export CGO_LDFLAGS="-L$SPEECHSDK_ROOT/lib -lMicrosoft.CognitiveServices.Speech.core"
   ```

1. 此外，为了运行包含 SDK 的应用程序，我们需要告诉 OS 在哪里可以找到这些库：

   ```sh
   export LD_LIBRARY_PATH="$SPEECHSDK_ROOT/lib/<arch>:$LD_LIBRARY_PATH"
   ```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [windows](../quickstart-list-go.md)]