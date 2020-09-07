---
title: 记录 ML 试验和指标
titleSuffix: Azure Machine Learning
description: 监视 Azure ML 试验和运行指标，以便改进模型创建过程。 使用 run.log、Run.start_logging 或 ScriptRunConfig 向训练脚本添加日志记录功能。
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 02a18a185a2f711833351578b07dfa4def4ff57a
ms.sourcegitcommit: b5ea35dcd86ff81a003ac9a7a2c6f373204d111d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88946745"
---
# <a name="enable-logging-in-azure-ml-training-runs"></a>在 Azure 机器学习训练运行中启用日志记录功能
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 机器学习 Python SDK 允许使用默认的 Python 日志记录包和特定于 SDK 的功能记录实时信息。 你可以在本地进行记录，并将日志发送到门户中的工作区。

日志可帮助你诊断错误和警告，或跟踪参数和模型性能等性能指标。 本文介绍如何在以下场景中启用日志记录功能：

> [!div class="checklist"]
> * 交互式训练会话
> * 使用 ScriptRunConfig 提交训练作业
> * Python 的原生 `logging` 设置
> * 来自其他源的日志记录


> [!TIP]
> 本文说明如何监视模型训练过程。 如果你希望监视 Azure 机器学习的资源使用情况和事件，例如配额、已完成的训练运行或已完成的模型部署，请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。

## <a name="data-types"></a>数据类型

可以记录多个数据类型，包括标量值、列表、表、图像、目录等。 有关不同数据类型的详细信息和 Python 代码示例，请查看 [Run 类参考页](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)。

## <a name="interactive-logging-session"></a>交互式日志记录会话

交互式日志记录会话通常用在笔记本环境中。 方法 [Experiment.start_logging()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) 启动交互式日志记录会话。 试验中会话期间记录的任何指标都会添加到运行记录中。 方法 [run.complete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) 结束会话并将运行标记为已完成。

以下代码片段使用交互式日志记录会话通过 [run.log()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#log-name--value--description----) 方法记录训练参数和性能指标。 它还将训练的模型上传到指定的输出位置。

```Python
# Get an experiment object from Azure Machine Learning
experiment = Experiment(workspace=ws, name="train-within-notebook")

# Create a run object in the experiment
run =  experiment.start_logging()
# Log the algorithm parameter alpha to the run
run.log('alpha', 0.03)

# Create, fit, and test the scikit-learn Ridge regression model
regression_model = Ridge(alpha=0.03)
regression_model.fit(data['train']['X'], data['train']['y'])
preds = regression_model.predict(data['test']['X'])

# Output the Mean Squared Error to the notebook and to the run
print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
run.log('mse', mean_squared_error(data['test']['y'], preds))

# Save the model to the outputs directory for capture
model_file_name = 'outputs/model.pkl'

joblib.dump(value = regression_model, filename = model_file_name)

# upload the model file explicitly into artifacts 
run.upload_file(name = model_file_name, path_or_stream = model_file_name)

# Complete the run
run.complete()
```

如需一个使用交互式日志记录的完整示例笔记本，请参阅[在笔记本中训练模型](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)。

## <a name="scriptrunconfig-logs"></a>ScriptRunConfig 日志

此部分介绍如何在 ScriptConfig 运行中添加日志记录代码。 可以使用 [**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) 类来封装用于可重复运行的脚本和环境。 还可以使用此选项来显示一个用于监视的 Jupyter Notebooks 视觉小组件。

此示例使用 [run.log()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#log-name--value--description----) 方法对 alpha 值执行参数扫描并捕获结果。

1. 创建包含日志记录逻辑的训练脚本 `train.py`。

   ```Python
   # Copyright (c) Microsoft. All rights reserved.
   # Licensed under the MIT license.

   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from azureml.core.run import Run
   import os
   import numpy as np
   import mylib
   # sklearn.externals.joblib is removed in 0.23
   try:
      from sklearn.externals import joblib
   except ImportError:
      import joblib

   os.makedirs('./outputs', exist_ok=True)

   X, y = load_diabetes(return_X_y=True)

   run = Run.get_context()

   X_train, X_test, y_train, y_test = train_test_split(X, y,
                                                      test_size=0.2,
                                                      random_state=0)
   data = {"train": {"X": X_train, "y": y_train},
         "test": {"X": X_test, "y": y_test}}

   # list of numbers from 0.0 to 1.0 with a 0.05 interval
   alphas = mylib.get_alphas()

   for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha=alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
         joblib.dump(value=reg, filename=os.path.join('./outputs/',
                                                      model_file_name))

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
    ```


1. 提交要在用户管理的环境中运行的 ```train.py``` 脚本。 整个脚本文件夹都要提交，以便进行训练。

   ```Python
   from azureml.core import ScriptRunConfig

   src = ScriptRunConfig(source_directory='./', script='train.py')
   src.run_config.environment = user_managed_env
   ```

   ```Python
   run = exp.submit(src)
   ```

    `show_output` 参数会启用详细日志记录，让你可以查看训练过程的详细信息，以及有关任何远程资源或计算目标的信息。 请使用以下代码在提交试验时启用详细日志记录。

```python
run = exp.submit(src, show_output=True)
```

还可以在生成的运行上的 `wait_for_completion` 函数中使用相同的参数。

```python
run.wait_for_completion(show_output=True)
```

如需一个使用 ScriptRunConfigs 日志的完整示例笔记本，请参阅[在本地训练模型](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local/train-on-local.ipynb)。

## <a name="native-python-logging"></a>原生 Python 日志记录

SDK 中的某些日志可能包含一个错误，指示你将日志记录级别设置为“调试”。 若要设置日志记录级别，请在脚本中添加以下代码。

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>其他日志记录源

Azure 机器学习还可以在训练期间记录其他来源的信息，例如自动化机器学习运行或运行作业的 Docker 容器。 这些日志未进行记录，但如果你遇到问题并联系了 Microsoft 支持部门，他们可以在排除故障时使用这些日志。

有关 Azure 机器学习设计器（预览版）中的日志记录指标的信息，请参阅[如何在设计器（预览版）中记录指标](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>示例笔记本
下面的笔记本展示了本文中的概念：
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

请参阅以下文章，详细了解如何使用 Azure 机器学习：

* 了解如何[在 Azure 机器学习设计器（预览版）中记录指标](how-to-track-designer-experiments.md)。

* 查看教程[使用 Azure 机器学习训练图像分类模型](tutorial-train-models-with-aml.md)中的示例，了解如何注册和部署最佳模型。
