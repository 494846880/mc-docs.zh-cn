---
title: 映像标记最佳做法
description: 将映像推送到 Azure 容器注册表并从 其中拉取映像时对 Docker 容器映像进行标记和版本控制的最佳做法
author: rockboyfor
ms.topic: article
origin.date: 07/10/2019
ms.date: 12/09/2019
ms.author: v-yeche
ms.openlocfilehash: da0f9b1d8ed99db712ce4d65e252c5dd2dcc627b
ms.sourcegitcommit: 3cf647177c22b24f76236c57cae19482ead6a283
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88029626"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>有关对容器映像进行标记和版本控制的建议

将容器映像推送到容器注册表，然后部署这些映像时，需要为映像标记和版本控制制定一个策略。 本文介绍两种方法，其中的每种方法在容器生命周期内都适用：

* **稳定标记** - 可重复使用的标记，例如，这些标记可指示类似于 *mycontainerimage:1.0* 的主要版本或次要版本。
* **唯一标记** - 对要推送到注册表的每个映像使用不同标记，例如 *mycontainerimage:abc123*。

## <a name="stable-tags"></a>稳定标记

**建议**：使用稳定标记可以维护容器版本的**基础映像**。 请避免在部署中使用稳定标记，因为这些标记会持续接收更新，并可能导致生产环境出现不一致情况。

稳定标记意味着开发人员或生成系统可以持续提取特定的标记，从而持续获取更新。  “稳定”并不意味着内容已冻结。 相反，“稳定”表示该映像应该根据该版本的意图保持稳定。 为了保持“稳定”，可对映像进行维护，以应用安全修补程序或框架更新。

### <a name="example"></a>示例

某个框架团队交付了版本 1.0。 他们知道，将来他们将会提供更新，其中包括次要更新。 为了支持给定主要版本和次要版本的稳定标记，它们创建了两组稳定标记。

* `:1` - 主要版本的稳定标记。 `1` 表示“最新的”1.* 版本。
* `:1.0` - 版本1.0 的稳定标记，可让开发人员绑定到 1.0 的更新，而不会在 1.1 发布时前滚到 1.1。

该团队还使用了 `:latest` 标记，无论当前主要版本是什么，该标记都会指向最新的稳定标记。

有基础映像更新可用，或者发布了框架的任何类型的维护版本时，使用稳定标记的映像将更新到最新摘要（代表该版本的最新稳定版本）。

在这种情况下，会继续维护主要和次要标记。 在基础映像方案中，这使得映像所有者可以提供经过维护的映像。

## <a name="unique-tags"></a>唯一标记

**建议**：对**部署**使用唯一标记，尤其是可在多个节点上缩放的环境中。 你可能想要特意部署一致的组件版本。 如果容器重启或者业务流程协调程序横向扩展了多个实例，则主机不会意外地提取与其他节点不一致的较新版本。

唯一标记仅仅表示推送到注册表的每个映像具有唯一的标记。 不会重复使用这些标记。 可以遵循多种模式来生成唯一标记，其中包括：

* **日期时间戳** - 此方法相当常见，因为使用它可以清楚地判断映像的生成时间。 但是，如何将它关联到生成系统呢？ 是否需要查找同时完成的生成？ 处于哪个时区？ 所有生成系统是否已校准为 UTC 时间？
* **Git 提交** - 在开始支持基础映像更新之前可以使用此方法。 如果基础映像更新已发生，则会使用与上一生成相同的 Git 提交来启动生成系统。 但是，基础映像包含新内容。 Git 提交通常提供半稳定标记。 
* **清单摘要** - 推送到容器注册表的每个容器映像关联到某个清单，该清单由唯一的 SHA-256 哈希或摘要标识。 尽管摘要是唯一的，但它很长且难于阅读，并且与生成环境不相关联。
* **生成 ID** - 这可能是最佳选项，因为此 ID 可能是增量式的，使你能够关联到特定的生成来查找所有项目和日志。 但与清单摘要一样，用户很难阅读它。

  如果组织使用多个生成系统，此选项的一种变通方式是使用生成系统名称作为标记的前缀：`<build-system>-<build-id>`。 例如，可将 API 团队的 Jenkins 生成系统与 Web 团队的 Azure Pipelines 生成系统中的生成区分开来。

## <a name="next-steps"></a>后续步骤

有关本文中的概念的更详细介绍，请参阅博客文章 [Docker Tagging:Best practices for tagging and versioning docker images](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/)（Docker 标记：有关对 Docker 映像进行标记和版本控制的最佳做法）。

若要最大程度地提高 Azure 容器注册表的性能并以经济高效的方式使用它，请参阅[有关 Azure 容器注册表的最佳做法](container-registry-best-practices.md)。

<!-- IMAGES -->

<!-- LINKS - Internal -->

<!-- Update_Description: update meta properties, wording update -->