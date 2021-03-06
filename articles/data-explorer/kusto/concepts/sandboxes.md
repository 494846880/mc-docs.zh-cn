---
title: 沙盒 - Azure 数据资源管理器
description: 本文介绍了 Azure 数据资源管理器中的沙盒。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 03/30/2020
ms.date: 10/29/2020
ms.openlocfilehash: 8a4fe0a344be7737b5254b4ebab457dafd114e27
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97003655"
---
# <a name="sandboxes"></a>沙盒

Kusto 的数据引擎服务可为需要安全隔离的特定流运行沙盒。
这些流的示例包括用户定义的使用 [Python 插件](../query/pythonplugin.md)或 [R 插件](../query/rplugin.md)运行的脚本。

为了运行这些沙盒，Kusto 使用了 Microsoft 的 [Drawbridge](https://www.microsoft.com/research/project/drawbridge/) 项目的演化版本。 其他 Microsoft 服务使用此解决方案在多租户环境中运行用户定义的对象。

在沙盒中运行的流不是隔离的。 它们也是本地的（靠近数据）。 这意味着不存在远程调用所增加的额外延迟。

## <a name="prerequisites"></a>先决条件

* 数据引擎不得启用[磁盘加密](../../security.md#data-encryption)。
  * 将来应该会支持并行运行这两项功能。
* 用于运行沙盒的所需包（映像）将部署到数据引擎的每个节点，并需要专用的 SSD 空间来运行
  * 估计大小为 20GB，也就是 D14_v2 VM 的 SSD 容量的大约 2.5%，或 L16_v1 VM 的 SSD 容量的 0.7%。
  * 这会影响群集的数据容量，并可能会影响群集的[成本](https://www.azure.cn/pricing/details/data-explorer)。

## <a name="runtime"></a>运行时

* 沙盒化的查询运算符可以使用一个或多个沙盒来完成其执行。
  * 沙盒仅用于单个运行，不在多个运行之间共享，并且在该运行完成后会释放。
  * 当查询首次需要沙盒来完成其执行时，沙盒会在节点上以延迟方式初始化。
    * 这意味着，在节点上使用沙盒的插件的第一次执行会经历一个短的预热期。
  * 当节点重启时（例如，在服务升级过程中重启），将释放该节点上所有正在运行的沙盒。
* 每个节点都维护预定义数量的沙盒，这些沙盒已就绪，可用于运行传入的请求。
  * 使用某个沙盒后，将自动提供一个新的沙盒来替代它。
* 如果没有预先分配的沙盒可用来为某个查询运算符提供服务，则会限制该查询运算符，直到有新的沙盒可用。 有关详细信息，请参阅[错误](#errors)。 对于每个沙盒，新的沙盒分配最多可能需要 10-15 秒，具体取决于 SKU 和数据节点上的可用资源。

## <a name="limitations"></a>限制

对于每种沙盒，可以使用群集级[沙盒策略](../management/sandboxpolicy.md)来控制某些限制。

* **每个节点的沙盒数：** 每个节点的沙盒数是有限的。
  * 如果没有可用的沙盒，则会对发出的请求进行限制。
* **网络：** 沙盒不能与虚拟机 (VM) 上的或其外部的任何资源交互。
  * 沙盒不能与另一个沙盒交互。
* **CPU：** 沙盒的主机处理器可供沙盒消耗的最大 CPU 速率是有限的（默认值为 `50%`）。
  * 达到此限制时，将限制沙盒对 CPU 的使用，但执行将继续。
* **内存：** 沙盒的主机 RAM 可供沙盒消耗的最大 RAM 量是有限的（默认值为 `20GB`）。
  * 达到该限制将导致沙盒终止，并出现查询执行错误。
* **磁盘：** 沙盒具有附加到它的唯一独立目录。 它无法访问主机的文件系统。
  * 该唯一文件夹提供与沙盒类型匹配的映像/包的访问权限。 例如，不可自定义的 Python 或 R 包。
* **子进程：** 将阻止沙盒生成子进程。

> [!NOTE]
> 与沙盒一起使用的资源不仅取决于作为请求的一部分进行处理的数据的大小，还取决于沙盒中运行的逻辑以及它所使用的库的实现。
> 例如，对于 `python` 和 `r` 插件，后者是指用户提供的脚本以及脚本在运行时使用的 Python 或 R 库。

## <a name="errors"></a>错误

|ErrorCode                 |状态                     |Message                                                                                            |可能的原因                                                                                                    |
|--------------------------|---------------------------|---------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
|E_SB_QUERY_THROTTLED_ERROR|TooManyRequests (429)      |由于限制，沙盒查询被中止。 在进行某些回退后重试可能会成功   |目标节点上没有可用的沙盒。 新沙盒应在几秒钟内变得可用         |
|E_SB_QUERY_THROTTLED_ERROR|TooManyRequests (429)      |“{kind}”类型的沙盒尚未初始化                                            |沙盒策略最近发生了更改。 遵守新策略的新沙盒将在几秒钟内变得可用|
|                          |InternalServiceError (520) |由于初始化沙盒时出错，沙盒查询已中止                         |意外的基础结构故障。 如果问题仍然存在，请创建支持请求                         |
