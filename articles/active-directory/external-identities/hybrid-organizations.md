---
title: 适用于混合组织的 B2B 协作 - Azure AD
description: 使用 Azure AD B2B 协作授予合作伙伴对本地资源和云资源的访问权限。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: v-junlch
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 187f1e36ebd59ddad1f65a8563b6564b4efc9e1a
ms.sourcegitcommit: 7646936d018c4392e1c138d7e541681c4dfd9041
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88648078"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>适用于混合组织的 Azure Active Directory B2B 协作

使用 Azure Active Directory (Azure AD) B2B 协作，可以轻松向外部合作伙伴授予对组织中应用和资源的访问权限。 即使是在同时包含本地资源和基于云的资源的混合配置中，也能做到这一点。 当前是在本地标识系统中管理外部合作伙伴帐户，还是在云中以 Azure AD B2B 用户的形式管理外部帐户，都不重要。 现在，可以对这两种环境使用相同的登录凭据，向这些用户授予对任一位置中的资源的访问权限。

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>向本地托管的合作伙伴帐户授予对云资源的访问权限

在 Azure AD 推出之前，使用本地标识系统的组织一贯是在其本地目录中管理合作伙伴帐户。 如果你就在这样的一家组织，则需要确保在将应用和其他资源转移到云时，合作伙伴仍旧拥有访问权限。 最好是让这些用户使用相同的凭据集来访问云资源和本地资源。 

我们现在提供了相应的方法，让你使用 Azure AD Connect 将这些本地帐户作为“来宾用户”同步到云，在云中，这些帐户的行为与 Azure AD B2B 用户相同。

为帮助保护公司数据，可将访问范围控制为适当的资源，并配置授权策略，以区分对待这些来宾用户和本公司的员工。

有关实施详细信息，请参阅[使用 Azure AD B2B 协作向本地托管的合作伙伴帐户授予对云资源的访问权限](hybrid-on-premises-to-cloud.md)。
 
## <a name="next-steps"></a>后续步骤

- [使用 Azure AD B2B 协作向本地托管的合作伙伴帐户授予对云资源的访问权限](hybrid-on-premises-to-cloud.md)



