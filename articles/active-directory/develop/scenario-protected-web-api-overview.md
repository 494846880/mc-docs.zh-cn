---
title: 受保护的 Web API - 概述
titleSuffix: Microsoft identity platform
description: 了解如何构建受保护的 Web API（概述）。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/19/2020
ms.author: v-junlch
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 7654a52312a2dc427bf9bb069c1cbea774a4e2ae
ms.sourcegitcommit: 7646936d018c4392e1c138d7e541681c4dfd9041
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88647718"
---
# <a name="scenario-protected-web-api"></a>方案：受保护的 Web API

通过此方案了解如何公开 Web API。 还了解如何保护 Web API，以便只有经过身份验证的用户才能访问它。

若要使用 Web API，需要启用使用工作和学校帐户进行了身份验证的用户。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>详情

下面是保护 Web API 时需要了解的具体信息：

- 应用注册必须至少公开一个范围或一个应用程序角色 。
  - 范围由代表用户调用的 Web API 公开。
  - 应用程序角色由守护程序应用程序调用的 Web API（应用程序代表它们自己调用你的 Web API）公开。
- 如果你创建新的 Web API 应用注册，请选择 `2` 作为 Web API 接受的[访问令牌版本](reference-app-manifest.md#accesstokenacceptedversion-attribute)。 对于旧版 Web API，接受的令牌版本可以是 `null`，但此值将登录受众限制为仅组织。
- Web API 的代码配置必须验证调用 Web API 时使用的令牌。
- 控制器操作中的代码必须验证令牌中的角色或范围。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用注册](scenario-protected-web-api-app-registration.md)

