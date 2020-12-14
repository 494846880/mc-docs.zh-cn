---
author: WenJason
ms.service: storage
ms.topic: include
origin.date: 08/21/2020
ms.date: 12/14/2020
ms.author: v-jay
ms.custom: seo-python-october2019
ms.openlocfilehash: bfcc2e93f4862dcc61860e782daec257ac7519cf
ms.sourcegitcommit: a8afac9982deafcf0652c63fe1615ba0ef1877be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96850801"
---
## <a name="what-is-queue-storage"></a>什么是队列存储？

Azure 队列存储是一项可存储大量消息的服务，用户可以通过经验证的呼叫，使用 HTTP 或 HTTPS 从世界任何地方访问这些消息。 一条队列消息的大小最多可为 64 KB，一个队列中可以包含数百万条消息，直至达到存储帐户的总容量限值。 队列存储通常用于创建要异步处理的积压工作 (backlog)。

## <a name="queue-service-concepts"></a>队列服务概念

Azure 队列服务包含以下组件：

![Azure 队列服务组件](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **存储帐户：** 对 Azure 存储进行的所有访问都要通过存储帐户完成。 有关存储帐户的详细信息，请参阅[存储帐户概述](../articles/storage/common/storage-account-overview.md)。
* **队列：** 一个队列包含一组消息。 所有消息必须位于相应的队列中。 请注意，队列名称必须全部小写。 有关命名队列的信息，请参阅 [命名队列和元数据](https://docs.microsoft.com/rest/api/storageservices/Naming-Queues-and-Metadata)。
* **消息：** 一条消息（无论哪种格式）的最大大小为 64 KB。 消息可以保留在队列中的最长时间为 7 天。 在 2017-07-29 或更高版本中，最大生存时间可以是任何正数，或者是 -1（表示消息不会过期）。 如果省略此参数，则默认的生存时间为 7 天。
* **URL 格式：** 使用以下 URL 格式对队列进行寻址： http://`<storage account>`.queue.core.chinacloudapi.cn/`<queue>`

    可使用以下 URL 访问示意图中的某个队列：

    `http://myaccount.queue.core.chinacloudapi.cn/incoming-orders`
