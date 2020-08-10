---
title: include 文件
description: include 文件
services: cognitive-services
author: Johnnytechn
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 08/07/2020
ms.topic: include
ms.custom: include file
ms.author: v-johya
origin.date: 02/14/2020
ms.openlocfilehash: 51007fc2fee2b3eb095e855376c4a8a99891ade4
ms.sourcegitcommit: caa18677adb51b5321ad32ae62afcf92ac00b40b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88024223"
---
使用适用于 .NET 的语言理解 (LUIS) 预测客户端库可以：

* 按槽获取预测
* 按版本进行预测

[参考文档](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | [预测运行时包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [C# 示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## <a name="prerequisites"></a>先决条件

* 语言理解 (LUIS) 门户帐户 - [免费创建](https://luis.azure.cn)
* [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) 的当前版本。
* LUIS 应用 ID - 使用 `df67dcdb-c37d-46af-88e1-8b97951ca1c2` 的公共 IoT 应用 ID。 快速入门代码中使用的用户查询特定于该应用。

## <a name="setting-up"></a>设置

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

在首选编辑器或 IDE 中创建新的 .NET Core 应用程序。

1. 在控制台窗口（例如 CMD、PowerShell 或 Bash）中，使用 dotnet `new` 命令创建名为 `language-understanding-quickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：`Program.cs`。

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. 将目录更改为新创建的应用文件夹。

1. 可使用以下代码生成应用程序：

    ```dotnetcli
    dotnet build
    ```

    生成输出不应包含警告或错误。

    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```

### <a name="install-the-sdk"></a>安装 SDK

在应用程序目录中，使用以下命令安装适用于 .NET 的语言理解 (LUIS) 预测运行时客户端库：

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

如果你使用的是 Visual Studio IDE，客户端库可用作可下载的 NuGet 包。

## <a name="object-model"></a>对象模型

语言理解 (LUIS) 预测运行时客户端是对 Azure 进行身份验证的 [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) 对象，其中包含资源密钥。

创建客户端后，可以使用此客户端访问如下所述的功能：

* 按[过渡或产品槽](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)进行预测
* 按[版本](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)进行预测


## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 .NET 的语言理解 (LUIS) 预测运行时客户端库来执行以下操作：

* [按槽进行预测](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>添加依赖项

在首选的编辑器或 IDE 中，从项目目录打开 *Program.cs* 文件。 将现有 `using` 代码替换为以下 `using` 指令：

```csharp
// <snippet_using>
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
// </snippet_using>

namespace UseRuntime
{
    class Program
    {
        // <snippet_variables>
        // Use Language Understanding (LUIS) prediction endpoint key
        // to create authentication credentials
        // Prediction key must be assigned in LUIS portal
        private static string predictionKey = "REPLACE-WITH-YOUR-ASSIGNED-PREDICTION-KEY";

        // Endpoint URL example value = "https://YOUR-RESOURCE-NAME.cognitiveservices.azure.cn"
        private static string predictionEndpoint = "https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.cn";

        // App Id example value = "df67dcdb-c37d-46af-88e1-8b97951ca1c2"
        private static string appId = "REPLACE-WITH-YOUR-LUIS_APP_ID";
        // </snippet_variables>

        // <snippet_main>
        static void Main(string[] args)
        {

            // Get prediction
            var predictionResult = GetPredictionAsync().Result;

            var prediction = predictionResult.Prediction;

            // Display query
            Console.WriteLine("Query:'{0}'", predictionResult.Query);
            Console.WriteLine("TopIntent :'{0}' ", prediction.TopIntent);

            foreach (var i in prediction.Intents)
            {
                Console.WriteLine(string.Format("{0}:{1}", i.Key, i.Value.Score));
            }

            foreach (var e in prediction.Entities)
            {
                Console.WriteLine(string.Format("{0}:{1}", e.Key, e.Value));
            }

            Console.Write("done");

        }
        // </snippet_main>

        // <snippet_create_client>
        static LUISRuntimeClient CreateClient()
        {
            var credentials = new ApiKeyServiceClientCredentials(predictionKey);
            var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { })
            {
                Endpoint = predictionEndpoint
            };

            return luisClient;

        }
        // </snippet_create_client>

        // <snippet_maintask>
        static async Task<PredictionResponse> GetPredictionAsync()
        {

            // Get client 
            using (var luisClient = CreateClient())
            {

                var requestOptions = new PredictionRequestOptions
                {
                    DatetimeReference = DateTime.Parse("2019-01-01"),
                    PreferExternalEntities = true
                };

                var predictionRequest = new PredictionRequest
                {
                    Query = "turn on the bedroom light",
                    Options = requestOptions
                };

                // get prediction
                return await luisClient.Prediction.GetSlotPredictionAsync(
                    Guid.Parse(appId),
                    slotName: "production",
                    predictionRequest,
                    verbose: true,
                    showAllIntents: true,
                    log: true);
            }
        }
        // </snippet_maintask>
    }
}
```

## <a name="authenticate-the-client"></a>验证客户端

1. 为密钥、资源名称、应用 ID 和发布槽创建变量。 将应用 ID 设置为公共 IoT 应用：

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

```csharp
// <snippet_using>
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
// </snippet_using>

namespace UseRuntime
{
    class Program
    {
        // <snippet_variables>
        // Use Language Understanding (LUIS) prediction endpoint key
        // to create authentication credentials
        // Prediction key must be assigned in LUIS portal
        private static string predictionKey = "REPLACE-WITH-YOUR-ASSIGNED-PREDICTION-KEY";

        // Endpoint URL example value = "https://YOUR-RESOURCE-NAME.cognitiveservices.azure.cn"
        private static string predictionEndpoint = "https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.cn";

        // App Id example value = "df67dcdb-c37d-46af-88e1-8b97951ca1c2"
        private static string appId = "REPLACE-WITH-YOUR-LUIS_APP_ID";
        // </snippet_variables>

        // <snippet_main>
        static void Main(string[] args)
        {

            // Get prediction
            var predictionResult = GetPredictionAsync().Result;

            var prediction = predictionResult.Prediction;

            // Display query
            Console.WriteLine("Query:'{0}'", predictionResult.Query);
            Console.WriteLine("TopIntent :'{0}' ", prediction.TopIntent);

            foreach (var i in prediction.Intents)
            {
                Console.WriteLine(string.Format("{0}:{1}", i.Key, i.Value.Score));
            }

            foreach (var e in prediction.Entities)
            {
                Console.WriteLine(string.Format("{0}:{1}", e.Key, e.Value));
            }

            Console.Write("done");

        }
        // </snippet_main>

        // <snippet_create_client>
        static LUISRuntimeClient CreateClient()
        {
            var credentials = new ApiKeyServiceClientCredentials(predictionKey);
            var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { })
            {
                Endpoint = predictionEndpoint
            };

            return luisClient;

        }
        // </snippet_create_client>

        // <snippet_maintask>
        static async Task<PredictionResponse> GetPredictionAsync()
        {

            // Get client 
            using (var luisClient = CreateClient())
            {

                var requestOptions = new PredictionRequestOptions
                {
                    DatetimeReference = DateTime.Parse("2019-01-01"),
                    PreferExternalEntities = true
                };

                var predictionRequest = new PredictionRequest
                {
                    Query = "turn on the bedroom light",
                    Options = requestOptions
                };

                // get prediction
                return await luisClient.Prediction.GetSlotPredictionAsync(
                    Guid.Parse(appId),
                    slotName: "production",
                    predictionRequest,
                    verbose: true,
                    showAllIntents: true,
                    log: true);
            }
        }
        // </snippet_maintask>
    }
}
```

1. 使用密钥创建 [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) 对象，并在终结点中使用该对象创建一个 [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) 对象。

```csharp
// <snippet_using>
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
// </snippet_using>

namespace UseRuntime
{
    class Program
    {
        // <snippet_variables>
        // Use Language Understanding (LUIS) prediction endpoint key
        // to create authentication credentials
        // Prediction key must be assigned in LUIS portal
        private static string predictionKey = "REPLACE-WITH-YOUR-ASSIGNED-PREDICTION-KEY";

        // Endpoint URL example value = "https://YOUR-RESOURCE-NAME.cognitiveservices.azure.cn"
        private static string predictionEndpoint = "https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.cn";

        // App Id example value = "df67dcdb-c37d-46af-88e1-8b97951ca1c2"
        private static string appId = "REPLACE-WITH-YOUR-LUIS_APP_ID";
        // </snippet_variables>

        // <snippet_main>
        static void Main(string[] args)
        {

            // Get prediction
            var predictionResult = GetPredictionAsync().Result;

            var prediction = predictionResult.Prediction;

            // Display query
            Console.WriteLine("Query:'{0}'", predictionResult.Query);
            Console.WriteLine("TopIntent :'{0}' ", prediction.TopIntent);

            foreach (var i in prediction.Intents)
            {
                Console.WriteLine(string.Format("{0}:{1}", i.Key, i.Value.Score));
            }

            foreach (var e in prediction.Entities)
            {
                Console.WriteLine(string.Format("{0}:{1}", e.Key, e.Value));
            }

            Console.Write("done");

        }
        // </snippet_main>

        // <snippet_create_client>
        static LUISRuntimeClient CreateClient()
        {
            var credentials = new ApiKeyServiceClientCredentials(predictionKey);
            var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { })
            {
                Endpoint = predictionEndpoint
            };

            return luisClient;

        }
        // </snippet_create_client>

        // <snippet_maintask>
        static async Task<PredictionResponse> GetPredictionAsync()
        {

            // Get client 
            using (var luisClient = CreateClient())
            {

                var requestOptions = new PredictionRequestOptions
                {
                    DatetimeReference = DateTime.Parse("2019-01-01"),
                    PreferExternalEntities = true
                };

                var predictionRequest = new PredictionRequest
                {
                    Query = "turn on the bedroom light",
                    Options = requestOptions
                };

                // get prediction
                return await luisClient.Prediction.GetSlotPredictionAsync(
                    Guid.Parse(appId),
                    slotName: "production",
                    predictionRequest,
                    verbose: true,
                    showAllIntents: true,
                    log: true);
            }
        }
        // </snippet_maintask>
    }
}
```

## <a name="get-prediction-from-runtime"></a>从运行时获取预测

添加以下方法以创建对预测运行时的请求。

用户言语是 [PredictionRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet) 对象的一部分。

GetSlotPredictionAsync 方法需要多个参数，如应用 ID、槽名称、用于满足请求的预测请求对象。 其他选项（如详细、显示所有意向和日志）都是可选的。

```csharp
// <snippet_using>
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
// </snippet_using>

namespace UseRuntime
{
    class Program
    {
        // <snippet_variables>
        // Use Language Understanding (LUIS) prediction endpoint key
        // to create authentication credentials
        // Prediction key must be assigned in LUIS portal
        private static string predictionKey = "REPLACE-WITH-YOUR-ASSIGNED-PREDICTION-KEY";

        // Endpoint URL example value = "https://YOUR-RESOURCE-NAME.cognitiveservices.azure.cn"
        private static string predictionEndpoint = "https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.cn";

        // App Id example value = "df67dcdb-c37d-46af-88e1-8b97951ca1c2"
        private static string appId = "REPLACE-WITH-YOUR-LUIS_APP_ID";
        // </snippet_variables>

        // <snippet_main>
        static void Main(string[] args)
        {

            // Get prediction
            var predictionResult = GetPredictionAsync().Result;

            var prediction = predictionResult.Prediction;

            // Display query
            Console.WriteLine("Query:'{0}'", predictionResult.Query);
            Console.WriteLine("TopIntent :'{0}' ", prediction.TopIntent);

            foreach (var i in prediction.Intents)
            {
                Console.WriteLine(string.Format("{0}:{1}", i.Key, i.Value.Score));
            }

            foreach (var e in prediction.Entities)
            {
                Console.WriteLine(string.Format("{0}:{1}", e.Key, e.Value));
            }

            Console.Write("done");

        }
        // </snippet_main>

        // <snippet_create_client>
        static LUISRuntimeClient CreateClient()
        {
            var credentials = new ApiKeyServiceClientCredentials(predictionKey);
            var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { })
            {
                Endpoint = predictionEndpoint
            };

            return luisClient;

        }
        // </snippet_create_client>

        // <snippet_maintask>
        static async Task<PredictionResponse> GetPredictionAsync()
        {

            // Get client 
            using (var luisClient = CreateClient())
            {

                var requestOptions = new PredictionRequestOptions
                {
                    DatetimeReference = DateTime.Parse("2019-01-01"),
                    PreferExternalEntities = true
                };

                var predictionRequest = new PredictionRequest
                {
                    Query = "turn on the bedroom light",
                    Options = requestOptions
                };

                // get prediction
                return await luisClient.Prediction.GetSlotPredictionAsync(
                    Guid.Parse(appId),
                    slotName: "production",
                    predictionRequest,
                    verbose: true,
                    showAllIntents: true,
                    log: true);
            }
        }
        // </snippet_maintask>
    }
}
```

## <a name="main-code-for-the-prediction"></a>预测的主代码

使用以下主方法将变量和方法结合在一起，以获取预测。

```csharp
// <snippet_using>
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
// </snippet_using>

namespace UseRuntime
{
    class Program
    {
        // <snippet_variables>
        // Use Language Understanding (LUIS) prediction endpoint key
        // to create authentication credentials
        // Prediction key must be assigned in LUIS portal
        private static string predictionKey = "REPLACE-WITH-YOUR-ASSIGNED-PREDICTION-KEY";

        // Endpoint URL example value = "https://YOUR-RESOURCE-NAME.cognitiveservices.azure.cn"
        private static string predictionEndpoint = "https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.cn";

        // App Id example value = "df67dcdb-c37d-46af-88e1-8b97951ca1c2"
        private static string appId = "REPLACE-WITH-YOUR-LUIS_APP_ID";
        // </snippet_variables>

        // <snippet_main>
        static void Main(string[] args)
        {

            // Get prediction
            var predictionResult = GetPredictionAsync().Result;

            var prediction = predictionResult.Prediction;

            // Display query
            Console.WriteLine("Query:'{0}'", predictionResult.Query);
            Console.WriteLine("TopIntent :'{0}' ", prediction.TopIntent);

            foreach (var i in prediction.Intents)
            {
                Console.WriteLine(string.Format("{0}:{1}", i.Key, i.Value.Score));
            }

            foreach (var e in prediction.Entities)
            {
                Console.WriteLine(string.Format("{0}:{1}", e.Key, e.Value));
            }

            Console.Write("done");

        }
        // </snippet_main>

        // <snippet_create_client>
        static LUISRuntimeClient CreateClient()
        {
            var credentials = new ApiKeyServiceClientCredentials(predictionKey);
            var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { })
            {
                Endpoint = predictionEndpoint
            };

            return luisClient;

        }
        // </snippet_create_client>

        // <snippet_maintask>
        static async Task<PredictionResponse> GetPredictionAsync()
        {

            // Get client 
            using (var luisClient = CreateClient())
            {

                var requestOptions = new PredictionRequestOptions
                {
                    DatetimeReference = DateTime.Parse("2019-01-01"),
                    PreferExternalEntities = true
                };

                var predictionRequest = new PredictionRequest
                {
                    Query = "turn on the bedroom light",
                    Options = requestOptions
                };

                // get prediction
                return await luisClient.Prediction.GetSlotPredictionAsync(
                    Guid.Parse(appId),
                    slotName: "production",
                    predictionRequest,
                    verbose: true,
                    showAllIntents: true,
                    log: true);
            }
        }
        // </snippet_maintask>
    }
}
```

## <a name="run-the-application"></a>运行应用程序

从应用程序目录使用 `dotnet run` 命令运行应用程序。

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>清理资源

完成预测后，通过删除 program.cs 文件及其子目录来清理此快速入门中的工作。

