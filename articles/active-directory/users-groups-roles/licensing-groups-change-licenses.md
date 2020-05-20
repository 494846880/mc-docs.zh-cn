---
title: 如何更改用户和组的许可证分配 - Azure Active Directory | Microsoft Docs
description: 介绍使用组许可将组中的用户迁移到不同服务许可证（Office 365 企业版 E1 和 E3）的建议过程
services: active-directory
keywords: Azure AD 许可
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
origin.date: 09/27/2019
ms.date: 11/14/2019
ms.author: v-junlch
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a15f8a1a09efc51cdcd842c81ecb0288dcc529f1
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "74084789"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>在 Azure Active Directory 中更改用户或组的许可证分配

本文介绍如何在 Azure Active Directory (Azure AD) 中的服务许可计划之间移动用户和组。 目标 Azure AD 的方法是确保在许可证更改期间不会丢失任何服务或数据。 用户应该可以在服务之间无缝切换。 本文中的许可计划分配步骤描述了如何将 Office 365 E1 上的用户或组更改为 Office 365 E3 用户或组，但这些步骤适用于所有许可计划。 更新某个用户或组的许可证分配时，删除许可证分配和添加新分配的操作会同时进行，因此，在许可证更改期间用户不会失去服务的访问权限，也不会看到计划之间发生许可证冲突。

## <a name="before-you-begin"></a>开始之前

在更新许可证分配之前，必须验证某些假设条件是否符合要更新的所有用户或组。 如果假设条件并不符合组中的所有用户，则某些用户的迁移可能失败。 因此，某些用户无法访问服务或数据。 请确保：

- 用户的当前许可计划（在本例中为 Office 365 E1）已分配到某个组并由用户继承，而不是直接分配的。

- 有足够的可用许可证用于所要分配的许可计划。 如果没有足够的许可证，可能不会为某些用户分配新的许可计划。 可以检查可用的许可证数目。

- 没有为用户分配可能与所需许可证冲突的，或者可能阻止删除当前许可证的其他服务许可证。 例如，依赖于其他服务的 Workplace Analytics 或 Project Online 等服务中的许可证。

- 如果通过 Azure AD Connect 在本地管理组并将其同步到 Azure AD 中，则需要使用本地系统添加或删除用户。 将更改同步到 Azure AD 并在组许可中拾取更改可能会花费一段时间。

## <a name="change-user-license-assignments"></a>更改用户许可证分配

在“更新许可证分配”页上，如果看到某些复选框不可用，则表示相应的服务不可更改，因为它们继承自组许可证。 

1. 使用 Azure AD 组织中的许可证管理员帐户登录到 [Azure 门户](https://portal.azure.cn/)。
1. 选择“Azure Active Directory” > “用户”，然后打开该用户对应的“配置文件”页。
1. 选择“许可证”。 
1. 选择“分配”以编辑用户或组的许可证分配。  可以在“分配”页中解决许可证分配冲突。 
1. 选中 Office 366 E3 对应的复选框，并确保至少选择了分配给用户的所有 E1 服务。
1. 清除 Office 365 E1 对应的复选框。

    ![用户的许可证分配页，其中显示已清除 Office 365 E1，并已选中 Office 365 E3](./media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. 选择“保存”。 

Azure AD 将应用新许可证，同时删除旧许可证以提供服务连续性。

## <a name="change-group-license-assignments"></a>更改组许可证分配

1. 使用 Azure AD 组织中的许可证管理员帐户登录到 [Azure 门户](https://portal.azure.cn/)。
1. 选择“Azure Active Directory” > “组”，然后打开组的“概述”页。
1. 选择“许可证”。 
1. 选择“分配”命令以编辑用户或组的许可证分配。 
1. 选中 Office 366 E3 对应的复选框。 为了保持服务连续性，请确保选择已分配给用户的所有 E1 服务。
1. 清除 Office 365 E1 对应的复选框。

    ![在用户或组的“许可证”页上选择“分配”命令](./media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. 选择“保存”。 

为了提供服务连续性，Azure AD 将应用新许可证，同时删除组中所有用户的旧许可证。

## <a name="next-steps"></a>后续步骤

参阅以下文章，了解通过组进行许可证管理的其他方案：

- [将许可证分配到 Azure Active Directory 中的组](../users-groups-roles/licensing-groups-assign.md)
- [识别和解决 Azure Active Directory 中组的许可问题](../users-groups-roles/licensing-groups-resolve-problems.md)
- [如何在 Azure Active Directory 中将单个许可用户迁移到组许可](../users-groups-roles/licensing-groups-migrate-users.md)
- [Azure Active Directory 中的组许可的 PowerShell 示例](../users-groups-roles/licensing-ps-examples.md)

<!-- Update_Description: wording update -->