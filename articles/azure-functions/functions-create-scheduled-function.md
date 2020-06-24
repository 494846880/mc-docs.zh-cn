---
title: 在 Azure 中创建按计划运行的函数
description: 了解如何在 Azure 中创建根据所定义的计划运行的函数。
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 1b9ebd6c03c5753f4150e880334d7cd5df963274
ms.sourcegitcommit: f1a76ee3242698123a3d77f44c860db040b48f70
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "84563777"
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>在 Azure 中创建由计时器触发的函数

了解如何使用 Azure Functions 创建根据所定义的计划运行的[无服务器](https://azure.microsoft.com/solutions/serverless/)函数。

## <a name="prerequisites"></a>先决条件

完成本教程：

+ 如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

## <a name="create-an-azure-function-app"></a>创建 Azure Function App

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

新函数应用已可以使用了。 接下来，将在新的 Function App 中创建一个函数。

:::image type="content" source="./media/functions-create-scheduled-function/function-app-create-success.png" alt-text="函数应用已成功创建。" border="true":::

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>创建计时器触发的函数

1. 在函数应用中，选择“函数”，然后选择“+ 添加”**** **** 

   :::image type="content" source="./media/functions-create-scheduled-function/function-add-function.png" alt-text="在 Azure 门户中添加函数。" border="true":::

1. 选择“计时器触发器”模板****。 

    :::image type="content" source="./media/functions-create-scheduled-function/function-select-timer-trigger.png" alt-text="选择 Azure 门户中的计时器触发器。" border="true":::

1. 使用图像下表中指定的设置配置新触发器，然后选择“创建函数”****。

    :::image type="content" source="./media/functions-create-scheduled-function/function-configure-timer-trigger.png" alt-text="选择 Azure 门户中的计时器触发器。" border="true":::
    
    | 设置 | 建议的值 | 说明 |
    |---|---|---|
    | **名称** | 默认 | 定义计时器触发的函数的名称。 |
    | **计划** | 0 \*/1 \* \* \* \* | 六字段 [CRON 表达式](functions-bindings-timer.md#ncrontab-expressions)，计划函数每分钟运行一次。 |

## <a name="test-the-function"></a>测试函数

1. 在函数中，选择“代码 + 测试”并展开日志****。

    :::image type="content" source="./media/functions-create-scheduled-function/function-test-timer-trigger.png" alt-text="测试 Azure 门户中的计时器触发器。" border="true":::

1. 通过查看写入到日志的信息来验证执行情况。

    :::image type="content" source="./media/functions-create-scheduled-function/function-view-timer-logs.png" alt-text="查看 Azure 门户中的计时器触发器。" border="true":::

现在可以更改函数的计划，使之每小时运行一次，而不是每分钟运行一次。

## <a name="update-the-timer-schedule"></a>更新计时器计划

1. 在函数中，选择“集成”****。 可在此处定义函数的输入和输出绑定，还可以设置计划。 

1. 选择“计时器 (myTimer)”****。

    :::image type="content" source="./media/functions-create-scheduled-function/function-update-timer-schedule.png" alt-text="更新 Azure 门户中的计时器计划。" border="true":::

1. 将“计划”值更新为 `0 0 */1 * * *`，然后选择“保存”**** ****。  

    :::image type="content" source="./media/functions-create-scheduled-function/function-edit-timer-schedule.png" alt-text="Azure 门户中的函数更新计时器计划。" border="true":::

现在已获得一个每小时运行一次的函数。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>后续步骤

已创建一个按计划运行的函数。 有关计时器触发器的详细信息，请参阅[使用 Azure Functions 计划代码执行](functions-bindings-timer.md)。

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

