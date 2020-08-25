---
title: B2B 协作的自助注册门户 - Azure AD
description: Azure Active Directory B2B 协作可让业务合作伙伴有选择性地访问本方的企业应用程序，为跨公司合作关系提供支持
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 08/17/2020
ms.author: v-junlch
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5187e5668ffe458c8c0c9e519a7383d4f9f9b5d1
ms.sourcegitcommit: 7646936d018c4392e1c138d7e541681c4dfd9041
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88648119"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>用于 Azure AD B2B 协作注册的自助服务

客户可借助通过 [Azure 门户](https://portal.azure.cn)和面向最终用户的[应用程序访问面板](https://account.activedirectory.windowsazure.cn/r#/applications)公开的内置功能执行许多操作。 但你可能需要为 B2B 用户自定义载入工作流，以适应你组织的需求。

## <a name="azure-active-directory-b2b-invitation-api"></a>Azure Active Directory B2B 邀请 API

组织可以使用 [Microsoft Graph 邀请管理器 API](https://docs.microsoft.com/graph/api/resources/invitation?view=graph-rest-1.0) 为 B2B 来宾用户构建自己的加入体验。 例如，如果想要建立自己的体验，可以使用[创建邀请 API](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0&tabs=http) 自动将你的自定义邀请电子邮件直接发送给 B2B 用户。 或者，应用可以使用创建响应中返回的 inviteRedeemUrl，将你自己的邀请（通过所选的通信机制）发送给受邀用户。

## <a name="next-steps"></a>后续步骤

* [什么是 Azure AD B2B 协作？](what-is-b2b.md)
* [Azure AD B2B 协作授权](licensing-guidance.md)
* [Azure Active Directory B2B 协作常见问题 (FAQ)](faq.md)

