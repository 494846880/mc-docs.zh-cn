---
title: TLS 配置 - Azure 门户 - Azure Database for MySQL
description: 了解如何使用 Azure 门户为 Azure Database for MySQL 设置 TLS 配置
author: WenJason
ms.author: v-jay
ms.service: mysql
ms.topic: conceptual
origin.date: 06/02/2020
ms.date: 06/29/2020
ms.openlocfilehash: 6b5059e01da3a8d0c7e213be025f442111629ccb
ms.sourcegitcommit: 3a8a7d65d0791cdb6695fe6c2222a1971a19f745
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2020
ms.locfileid: "85516784"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>使用 Azure 门户在 Azure Database for MySQL 中配置 TLS 设置

本文介绍如何配置 Azure Database for MySQL 服务器，以强制设置连接所允许的最低 TLS 版本并拒绝所有使用更低 TLS 版本（与已配置的最低 TLS 版本相比）的连接，从而增强网络安全性。

可以强制设置用于连接到 Azure Database for MySQL 的 TLS 版本。 现在，客户可以选择为其数据库服务器设置最低 TLS 版本。 例如，将此最低 TLS 版本设置为 1.0 意味着应允许客户端使用 TLS 1.0、1.1 和 1.2 进行连接。 也可将此选项设置为 1.2，这意味着仅允许客户端使用 TLS 1.2+ 进行连接，所有使用 TLS 1.0 和 TLS 1.1 的传入连接都将被拒绝。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

* [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>为 Azure Database for MySQL 设置 TLS 配置

请按照以下步骤设置 MySQL 服务器的最低 TLS 版本：

1. 在 [Azure 门户](https://portal.azure.cn/)中，选择现有 Azure Database for MySQL 服务器。

1. 在 MySQL 服务器页上的“设置”下，单击“连接安全性”，打开连接安全性配置页 。

1. 在“最低 TLS 版本”中，选择“1.2”，为 MySQL 服务器拒绝 TLS 版本低于 TLS 1.2 的连接 。

    ![Azure Database for MySQL 的 TLS 配置](./media/howto-tls-configurations/setting-tls-value.png)

1. 单击“保存”  以保存更改。

1. 此时将显示一则通知，确认已成功启用了连接安全性设置。

    ![Azure Database for MySQL 的 TLS 配置成功](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>后续步骤

- 了解[如何基于指标创建警报](howto-alert-on-metric.md)