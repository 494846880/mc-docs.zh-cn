---
title: include 文件
description: include 文件
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 02/14/2020
ms.date: 12/07/2020
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 1bec86082f992ffffe482bae82b229454404bc82
ms.sourcegitcommit: ac1cb9a6531f2c843002914023757ab3f306dc3e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2020
ms.locfileid: "96747053"
---
如果希望在授予访问权限之前提示用户提供第二个身份验证因素，可以配置 Azure AD 多重身份验证 (MFA)。 可以针对每个用户配置 MFA，也可以通过[条件访问](../articles/active-directory/conditional-access/overview.md)利用 MFA。

* 可以为每个用户启用 MFA，而不收费额外费用。 为每个用户启用 MFA 时，将提示用户针对绑定到 Azure AD 租户的所有应用程序进行第二因素身份验证。 有关步骤，请参阅[选项 1](/vpn-gateway/openvpn-azure-ad-mfa#peruser)。
* 使用条件访问可对提升第二个因素的方式进行更细粒度的控制。 它可以允许仅将 MFA 分配给 VPN，并排除绑定到 Azure AD 租户的其他应用程序。 有关步骤，请参阅[选项 2](/vpn-gateway/openvpn-azure-ad-mfa#conditional)。