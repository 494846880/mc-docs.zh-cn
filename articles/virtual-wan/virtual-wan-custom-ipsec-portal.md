---
title: 为 Azure 虚拟 WAN 配置自定义 IPsec 策略 - 门户 | Azure
description: 了解如何使用门户为 Azure 虚拟 WAN 配置自定义 IPsec 策略。
services: virtual-wan
ms.service: virtual-wan
ms.topic: how-to
origin.date: 10/14/2019
author: rockboyfor
ms.date: 11/02/2020
ms.testscope: yes
ms.testdate: 11/02/2020
ms.author: v-yeche
ms.openlocfilehash: 41803e73e291be2bad4a96e46e4db5f18d9fda4d
ms.sourcegitcommit: 93309cd649b17b3312b3b52cd9ad1de6f3542beb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93104761"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>使用门户为虚拟 WAN 配置自定义 IPsec 策略

可以在 Azure 门户中为虚拟 WAN VPN 连接配置自定义 IPsec 策略。 如果希望两端（本地和 Azure VPN 网关）对“IKE 阶段 1”和“IKE 阶段 2”使用相同的设置，则可使用自定义策略。

## <a name="working-with-custom-policies"></a>使用自定义策略

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>配置策略

1. **找到虚拟中心** 。 从浏览器导航到 [Azure 门户](https://portal.azure.cn/?feature.canmodifystamps=true&Microsoft_Azure_Network=flight16&feature.vwaner=true&feature.vwanp2s=true)并使用 Azure 帐户登录。 导航到虚拟 WAN 资源并找到 VPN 站点所连接到的虚拟中心。
2. **选择 VPN 站点** 。 在“中心概述”页中，单击“VPN (站点到站点)”，然后选择要为其设置自定义 IPsec 策略的 VPN 站点。

    :::image type="content" source="./media/virtual-wan-custom-ipsec-portal/locate.png" alt-text="select":::
3. **编辑 VPN 连接** 。 从上下文菜单“...”中，选择“编辑 VPN 连接”    。

    :::image type="content" source="./media/virtual-wan-custom-ipsec-portal/contextmenu.png" alt-text="select":::
4. **配置设置** 。 在“编辑 VPN 连接”页上，将 IPsec 设置从默认设置更改为自定义设置，然后自定义 IPsec 策略。 选择“保存”以保存设置  。

    :::image type="content" source="./media/virtual-wan-custom-ipsec-portal/edit.png" alt-text="select":::

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。

<!-- Update_Description: update meta properties, wording update, update link -->