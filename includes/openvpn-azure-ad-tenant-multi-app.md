---
title: include 文件
description: include 文件
services: vpn-gateway
ms.service: vpn-gateway
ms.topic: include
origin.date: 10/15/2020
author: rockboyfor
ms.date: 10/26/2020
ms.testscope: no
ms.testdate: 10/26/2020
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 65fd66cad78cab44f418aae9327d4d502b1fed16
ms.sourcegitcommit: 537d52cb783892b14eb9b33cf29874ffedebbfe3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2020
ms.locfileid: "92472610"
---
<a name="tenant"></a>
## <a name="1-create-the-azure-ad-tenant"></a>1.创建 Azure AD 租户

使用[创建新租户](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md)一文中的步骤创建 Azure AD 租户：

* 组织名称
* 初始域名

    示例：

    :::image type="content" source="./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png" alt-text="新 Azure AD 租户":::

<a name="users"></a>
## <a name="2-create-tenant-users"></a>2.创建租户用户

在此步骤中，你将创建两个 Azure AD 租户用户：一个全局管理员帐户和一个主要用户帐户。 主要用户帐户用作主要嵌入帐户（服务帐户）。 创建 Azure AD 租户用户帐户时，可以根据要创建的用户类型调整目录角色。 使用[此文](../articles/active-directory/fundamentals/add-users-azure-active-directory.md)中的步骤为 Azure AD 租户创建至少两个用户。 若要创建帐户类型，请务必更改“目录角色”：

* 全局管理员
* User

<a name="register-client"></a>
## <a name="3-register-the-vpn-client"></a>3.注册 VPN 客户端

在 Azure AD 租户中注册 VPN 客户端。

1. 找到要用于身份验证的目录的目录 ID。 此 ID 在“Active Directory”页的“属性”部分中列出。

    :::image type="content" source="./media/openvpn-azure-ad-tenant-multi-app/directory-id.png" alt-text="新 Azure AD 租户":::

2. 复制“目录 ID”。

3. 以拥有“全局管理员”角色的用户身份登录到 Azure 门户。

4. 接下来，做出管理员许可。 在浏览器的地址栏中复制并粘贴与部署位置相关的 URL：

    <!--MOONCAKE: CUSTOMIZE-->
    <!--UPDATE CAREFULLY-->
    
    公共

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Azure 德国云

    ```
    https://login.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````
    
    <!--CORRECT ON https://login.microsoftonline.de/-->
    
    Azure 中国世纪互联

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```
    
    <!--MOONCAKE: CUSTOMIZE-->
    <!--UPDATE CAREFULLY-->

> [!NOTE]
> 如果你使用的全局管理员帐户不是 Azure AD 租户的本机帐户来提供许可，请在 URL 中将“common”替换为 Azure AD Directory ID。 在某些其他情况下，你可能还需要将“common”替换为你的 Directory ID。
>

5. 如果出现提示，请选择“全局管理员”帐户。

    :::image type="content" source="./media/openvpn-azure-ad-tenant-multi-app/pick.png" alt-text="新 Azure AD 租户":::

6. 出现提示时选择“接受”。

    ![屏幕截图显示了“为你的组织请求接受的权限”消息和关于请求信息的窗口。](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. 在 Azure AD 下的“企业应用程序”中，你将看到“Azure VPN”已列出 。

    :::image type="content" source="./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png" alt-text="新 Azure AD 租户":::

<a name="register-apps"></a>
## <a name="4-register-additional-applications"></a>4.注册其他应用程序

在此步骤中，你将为各个用户和组注册其他应用程序。

1. 在 Azure Active Directory 下单击“应用注册”，然后单击“+ 新建注册” 。

    :::image type="content" source="./media/openvpn-azure-ad-tenant-multi-app/app1.png" alt-text="新 Azure AD 租户":::

2. 在“注册应用程序”页上，输入名称 。 选择所需的支持的帐户类型，然后单击“注册” 

    :::image type="content" source="./media/openvpn-azure-ad-tenant-multi-app/app2.png" alt-text="新 Azure AD 租户":::

3. 注册新应用后，请单击应用边栏选项卡下的“公开 API”。

4. 单击“+ 添加范围”。

5. 保留默认的“应用程序 ID URI”。 单击“保存并继续”。

    :::image type="content" source="./media/openvpn-azure-ad-tenant-multi-app/app3.png" alt-text="新 Azure AD 租户":::

6. 填写必填字段，确保“状态”为“已启用”。 单击“添加范围”。

    :::image type="content" source="./media/openvpn-azure-ad-tenant-multi-app/app4.png" alt-text="新 Azure AD 租户":::

7. 单击“公开 API”，然后单击“+ 添加客户端应用程序” 。  对于“客户端 ID”，根据云输入以下值：

    - 对于 Azure 公有云，输入“41b23e61-6c1e-4545-b367-cd054e0ed4b4” 
    - 对于 Azure 政府，输入“51bb15d4-3a4f-4ebf-9dca-40096fe32426” 
    - 对于 Azure 德国，输入“538ee9e6-310a-468d-afef-ea97365856a9” 
    - 对于 Azure 中国世纪互联，输入“49f817b6-84ae-4cc0-928c-73f27289b3aa” 

8. 单击“添加应用程序”。

    :::image type="content" source="./media/openvpn-azure-ad-tenant-multi-app/app5.png" alt-text="新 Azure AD 租户":::

9. 复制“概述”页中的应用程序客户端 ID 。 你将需要使用此信息来配置 VPN 网关。

    :::image type="content" source="./media/openvpn-azure-ad-tenant-multi-app/app6.png" alt-text="新 Azure AD 租户":::

10. 重复此[注册其他应用程序](#register-apps)部分中的步骤，创建安全要求所需的多个应用程序。 每个应用程序都将关联到一个 VPN 网关，并且可以有不同的用户集。 只能将一个应用程序关联到一个网关。

<a name="assign-users"></a>
## <a name="5-assign-users-to-applications"></a>5.将用户分配给应用程序

将用户分配到应用程序。

1. 在“Azure AD > 企业应用程序”下，选择新注册的应用程序，然后单击“属性” 。 确保“需要进行用户分配?”设置为“是”。  单击“保存”  。

    :::image type="content" source="./media/openvpn-azure-ad-tenant-multi-app/user2.png" alt-text="新 Azure AD 租户":::

2. 在应用页上，单击“用户和组”，然后单击“+添加用户” 。

    :::image type="content" source="./media/openvpn-azure-ad-tenant-multi-app/user3.png" alt-text="新 Azure AD 租户":::

3. 在“添加分配”下，单击“用户和组” 。 选择希望能够访问此 VPN 应用程序的用户。 单击“选择”。

    :::image type="content" source="./media/openvpn-azure-ad-tenant-multi-app/user4.png" alt-text="新 Azure AD 租户":::

<!-- Update_Description: update meta properties, wording update, update link -->