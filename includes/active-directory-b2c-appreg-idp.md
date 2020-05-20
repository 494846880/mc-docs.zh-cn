---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
origin.date: 10/16/2019
ms.date: 11/25/2019
ms.author: v-junlch
ms.openlocfilehash: 2908f5a860545cc6c9e1daab15d7e14772d1f099
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "74461741"
---
若要在 Azure AD B2C 租户中注册应用程序，可以使用当前“应用程序”体验  。 

#### <a name="applications"></a>[应用程序](#tab/applications/)

1. 登录 [Azure 门户](https://portal.azure.cn)。
1. 在顶部菜单中选择“目录 + 订阅”  筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”  。 或者，选择“所有服务”  并搜索并选择“Azure AD B2C”  。
1. 选择“应用程序”，然后选择“添加”   。
1. 输入应用程序的名称。 例如，*testapp1*。
1. 对于“Web 应用/Web API”，请选择“是”。  
1. 对于“回复 URL”，请输入 `https://jwt.ms`
1. 选择“创建”  。

