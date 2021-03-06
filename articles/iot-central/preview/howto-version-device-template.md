---
title: 了解 Azure IoT Central 应用的设备模板版本控制 | Microsoft Docs
description: 通过创建新版本循环访问设备模板，不影响实时连接设备
author: sarahhubbard
ms.author: v-yiso
origin.date: 07/17/2019
ms.date: 12/16/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 9a39f5dc519b92b0593b1431d0cf95c741bfd676
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "74882976"
---
# <a name="create-a-new-device-template-version-preview-features"></a>创建新设备模板版本（预览版功能）

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Azure IoT Central 用于快速开发 IoT 应用程序。 可以通过添加、编辑或删除设备功能、视图和自定义项来快速循环访问设备模板设计。 发布设备模板后，设备功能模型将显示为“已发布”，其旁边会显示锁形图标。  若要对设备功能模型进行更改，需要创建设备模板的新版本。 同时，云属性、自定义项和视图均可随时编辑，而无需对设备模板进行版本控制。 保存其中的任何更改后，可以发布设备模板，使操作员能够在设备资源管理器中看到最新的更改。

> [!NOTE]
> 若要详细了解如何创建设备模板，请参阅[设置和管理设备模板](howto-set-up-template.md)

## <a name="add-customizations-to-the-device-template-without-versioning"></a>在不进行版本控制的情况下将自定义项添加到设备模板

无需对设备模板和接口进行版本控制，就能编辑设备功能的某些元素。 例如，其中的一些字段包括显示名称、语义类型、最小值、最大值、小数位数、颜色、单位、显示单位、注释和说明。 若要添加其中的某个自定义项：

1. 转到“设备模板”页。 
1. 选择要自定义的设备模板。
1. 选择“自定义”选项卡。 
1. 在设备功能模型中定义的所有功能将在此处列出。 可以保存此处可编辑的所有字段并在整个应用程序中使用，而无需对设备模板进行版本控制。 如果要编辑的字段是只读的，则需要对设备模板进行版本控制才能更改这些字段。 选择要编辑的字段，然后输入任何新值。
1. 单击“保存”  。 现在，这些值将替代设备模板中最初保存的所有内容，并在整个应用程序中使用。

## <a name="versioning-a-device-template"></a>对设备模板进行版本控制

创建设备模板的新版本会创建该模板的草稿版本，可在其中编辑设备功能模型。 任何已发布的接口将保持已发布状态，直到单独对其进行版本控制。 若要修改已发布的接口，必须先创建一个新的设备模板版本。

仅当你要尝试编辑无法在设备模板的 customizations 节中编辑的一部分设备功能模型时，才应该对设备模板进行版本控制。 

若要对设备模板进行版本控制：

1. 转到“设备模板”页。 
1. 选择尝试进行版本控制的设备模板。
1. 单击页面顶部的“版本”按钮，并为模板指定新名称。  我们已自动建议一个新名称，但可以编辑该名称。
1. 单击“创建”。 
1. 该设备模板现在处于草稿模式。 将会看到，接口仍处于锁定状态，必须单独对其进行版本控制才能编辑。 

### <a name="versioning-an-interface"></a>对接口进行版本控制

对接口进行版本控制可以在已创建的接口中添加、更新和删除功能。 

若要对接口进行版本控制：

1. 转到“设备模板”页。 
1. 选择处于草稿模式的设备模板。
1. 选择处于已发布模式的、要进行版本控制和编辑的接口。
1. 单击接口页面顶部的“版本”按钮。  
1. 单击“创建”。 
1. 该接口现在处于草稿模式。 可以在接口中添加或编辑功能，而不会破坏现有的自定义项和视图。 

> [!NOTE]
> 无法对 Azure IoT 发布的标准接口进行版本控制或编辑。 这些标准接口用于设备认证。

> [!NOTE]
> 发布接口后，即使是在草稿模式下，也无法删除其任何功能。 只能编辑功能，或将其添加到处于草稿模式的接口。


## <a name="migrate-a-device-across-device-template-versions"></a>跨设备模板版本迁移设备

可以创建多个版本的设备模板。 一段时间过后，会有多个使用这些设备模板的连接设备。 可以将设备从设备模板的一个版本迁移到另一个版本。 以下步骤介绍如何迁移设备：

1. 转到“设备资源管理器”页。 
1. 选择需迁移到另一版本的设备。
1. 选择“迁移”。 
1. 选择具有要将设备迁移到的版本号的设备模板，然后选择“迁移”。 

![如何迁移设备](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>后续步骤

了解如何在 Azure IoT Central 应用程序中使用设备模板版本后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [如何创建遥测规则](tutorial-create-telemetry-rules.md)
