---
title: 使用 Azure Site Recovery 从 Azure 故障回复 Azure VMware 解决方案 VM
description: 了解在灾难恢复期间如何在故障转移到 Azure 后故障回复到 Azure VMware 解决方案私有云。
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
origin.date: 09/30/2020
author: rockboyfor
ms.date: 11/09/2020
ms.testscope: yes
ms.testdate: 11/09/2020
ms.author: v-yeche
ms.custom: MVC
ms.openlocfilehash: 125e6161540ea0c2c4538a3750328e570f930911
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329143"
---
<!--Verified Successfully-->
# <a name="fail-back-vms-to-azure-vmware-solution-private-cloud"></a>将 VM 故障回复到 Azure VMware 解决方案私有云

本文介绍在使用 [Azure Site Recovery](site-recovery-overview.md) 将 Azure VMware 解决方案 VM [故障转移](avs-tutorial-failover.md)到 Azure 之后，如何将 Azure VM 故障回复到 Azure VMware 解决方案私有云。 故障回复后，启用复制，以便 Azure VMware 解决方案 VM 开始复制到 Azure。

## <a name="before-you-start"></a>开始之前

1. 了解 [VMware 故障回复](failover-failback-overview.md#vmwarephysical-reprotectionfailback)。 
2. 确保已查看并完成[准备故障回复](vmware-azure-prepare-failback.md)的步骤，并已部署全部所需的组件。 组件包括 Azure 中的进程服务器、主目标服务器和用于故障回复的 VPN 站点到站点连接（或 ExpressRoute 专用对等互连）。
3. 请确保已完成重新保护和故障回复的[要求](avs-tutorial-reprotect.md#before-you-begin)，并且已[启用 Azure VM 的重新保护](avs-tutorial-reprotect.md#enable-reprotection)，以便将其从 Azure 复制到 Azure VMware 解决方案私有云。 VM 必须处于已复制状态才能故障回复。

## <a name="run-a-failover-to-fail-back"></a>运行故障转移以进行故障回复

1. 请确保已重新保护 Azure VM 并将其复制到 Azure VMware 解决方案私有云。
    - VM 至少需有一个恢复点才能故障回复。
    - 如果对恢复计划进行故障回复，该计划中的所有计算机应至少有一个恢复点。
2. 在保管库中 >“复制的项”，选择 VM  。 右键单击该 VM 并选择“计划内故障转移”。
3. 在“确认故障转移”中，确认故障转移方向为从 Azure 转移。
4. 选择要用于此故障转移的恢复点。
    - 建议使用“最新”恢复点。 应用一致性点会在最新的时间点之后，并会导致丢失部分数据。
    - “最新”是崩溃一致性恢复点。
    - 使用“最新”时，VM 将故障转移到其最新可用时间点。 如果恢复计划中存在多 VM 一致性的复制组，该组中的每个 VM 将故障转移到其独立的最新时间点。
    - 如果使用应用一致性恢复点，每个 VM 将故障回复到其最新可用时间点。 如果恢复计划包含复制组，每个组将恢复到其公共的可用恢复点。
5. 故障转移开始。 Site Recovery 关闭 Azure VM。
6. 故障转移完成后，检查是否一切都符合预期。 检查 Azure VM 是否已关闭。 
7. 进行全面的验证后，右键单击 VM 并选择“提交”，以完成故障转移过程。 “提交”操作会删除已故障转移的 Azure VM。 

> [!NOTE]
> 对于 Windows VM，Site Recovery 在故障转移期间会禁用 VMware 工具。 在故障回复 Windows VM 期间，会再次启用 VMware 工具。 

## <a name="reprotect-from-azure-vmware-solution-to-azure"></a>从 Azure VMware 解决方案重新保护到 Azure

提交故障回复后，将删除 Azure VM。 VM 已故障回复到 Azure VMware 解决方案私有云，但未受到保护。 可按如下所述开始将 VM 复制到 Azure：

1. 在保管库中选择“复制的项”，选择已故障回复的 VM，然后选择“重新保护”。
2. 指定用于将数据发回到 Azure 的进程服务器。
3. 选择“确定”启动重新保护作业。

> [!NOTE]
> Azure VMware 解决方案 VM 启动后，代理最多需要 15 分钟的时间来重新注册到配置服务器。 在此期间，重新保护会失败并返回一条错误消息，指出未安装代理。 如果发生这种情况，请等待几分钟，然后再重新保护。

## <a name="next-steps"></a>后续步骤

重新保护作业完成后，Azure VMware 解决方案 VM 将复制到 Azure。 可根据需要[再次运行到 Azure 的故障转移](avs-tutorial-failover.md)。

<!-- Update_Description: new article about avs tutorial failback -->
<!--NEW.date: 11/09/2020-->