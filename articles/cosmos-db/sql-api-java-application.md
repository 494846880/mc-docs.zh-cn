---
title: 教程 - 使用 Azure Cosmos DB 和 SQL API 构建 Java Web 应用
description: 教程 - 此 Java Web 应用程序教程介绍了如何使用 Azure Cosmos DB 和 SQL API 通过 Azure 网站上托管的 Java 应用程序存储和访问数据。
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
origin.date: 05/12/2020
author: rockboyfor
ms.date: 11/16/2020
ms.testscope: yes
ms.testdate: 09/28/2020
ms.author: v-yeche
ms.custom: devx-track-java
ms.openlocfilehash: 2f6c9869110f46713d5f3a486e27006368c13660
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96432392"
---
# <a name="tutorial-build-a-java-web-application-using-azure-cosmos-db-and-the-sql-api"></a>教程：使用 Azure Cosmos DB 和 SQL API 构建 Java Web 应用程序
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

本 Java Web 应用程序教程介绍了如何使用 [Azure Cosmos DB](https://www.azure.cn/home/features/cosmos-db/) 服务通过 Azure 应用服务 Web 应用上托管的 Java 应用程序来存储和访问数据。 在本文中，将学习以下内容：

* 如何在 Eclipse 中构建基本 JavaServer Pages (JSP) 程序。
* 如何使用 [Azure Cosmos DB Java SDK](https://github.com/Azure/azure-documentdb-java) 来处理 Azure Cosmos DB 服务。

此 Java 应用程序教程演示了如何创建一个基于 web 的任务管理应用程序，可以使用此应用程序创建、检索任务，以及将任务标记为已完成，如下图所示。 ToDo 列表中的每个任务都存储为 Azure Cosmos DB 中的 JSON 文档。

:::image type="content" source="./media/sql-api-java-application/image1.png" alt-text="我的待办事项列表 Java 应用程序":::

> [!TIP]
> 此应用程序开发教程假定你之前有使用 Java 的经验。 如果不熟悉 Java 或[必备工具](#Prerequisites)，我们建议从 GitHub 下载完整的 [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) 项目，并按照[本文末尾的说明](#GetProject)生成该项目。 构建之后，可以回顾本文以深入了解项目上下文中的代码。  
>

<a name="Prerequisites"></a>
## <a name="prerequisites-for-this-java-web-application-tutorial"></a>此 Java Web 应用程序教程的先决条件

在开始本应用程序开发教程前，必须具有：

* 如果没有 Azure 订阅，请在开始前创建一个[试用订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Java 开发工具包 (JDK) 7+](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true)。
* [Eclipse IDE for Java EE Developers。](https://www.eclipse.org/downloads/packages/release/luna/sr1/eclipse-ide-java-ee-developers)
* [已启用 Java 运行时环境（例如 Tomcat 或 Jetty）的 Azure 网站。](../app-service/quickstart-java.md)

如果是首次安装这些工具，那么可以参考 coreservlets.com 网站的[教程：安装 TomCat7 并将其与 Eclipse 一起使用](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html)一文的“快速入门”部分提供的安装过程进行演练。

<a name="CreateDB"></a>
## <a name="create-an-azure-cosmos-db-account"></a>创建 Azure Cosmos DB 帐户

让我们首先创建一个 Azure Cosmos DB 帐户。 如果已有一个帐户，或者要在本教程中使用 Azure Cosmos DB 模拟器，可以跳到[步骤 2：创建 Java JSP 应用程序](#CreateJSP)。

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

<a name="CreateJSP"></a>
## <a name="create-the-java-jsp-application"></a>创建 Java JSP 应用程序

若要创建 JSP 应用程序，请执行以下步骤：

1. 首先，我们将从创建 Java 项目开始。 启动 Eclipse，并依次单击“文件”、“新建”和“动态 Web 项目”。   如果未看到“动态 Web 项目”作为可用项目列出，请执行下列操作：依次单击“文件”、“新建”和“项目”，展开“Web”，单击“动态 Web 项目”，并单击“下一步”。      

    :::image type="content" source="./media/sql-api-java-application/image10.png" alt-text="JSP Java 应用程序开发":::

1. 在“项目名称”框中输入项目名称，在“目标运行时”下拉菜单中随意选择一个值（例如 Apache Tomcat v7.0），并单击“完成”。   选择目标运行可通过 Eclipse 在本地运行项目。

1. 在 Eclipse 的项目资源管理器视图中，展开项目。 右键单击“WebContent”，单击“新建”，并单击“JSP 文件”。  

1. 在“新建 JSP 文件”对话框中，将文件命名为 **index.jsp**。 将父文件夹保留为 **WebContent**，如下图所示，然后单击“下一步”。

    :::image type="content" source="./media/sql-api-java-application/image11.png" alt-text="创建新的 JSP 文件 - Java Web 应用程序教程":::

1. 对于本教程，请在“选择 JSP 模板”对话框中选择“新建 JSP 文件(html)”，并单击“完成”。  

1. 在 Eclipse 中打开 index.jsp 文件后，添加文本以显示 Hello World! 在现有 `<body>` 元素中。 更新后的 `<body>` 内容应类似于以下代码：

    ```html
    <body>
     <% out.println("Hello World!"); %>
    </body>
    ```

1. 保存 index.jsp 文件。

1. 如果在步骤 2 中设置了目标运行时，则可以单击“项目”，并单击“运行”，在本地运行 JSP 应用程序： 

    :::image type="content" source="./media/sql-api-java-application/image12.png" alt-text="Hello World — Java 应用程序教程":::

<a name="InstallSDK"></a>
## <a name="install-the-sql-java-sdk"></a>安装 SQL Java SDK

提取 SQL Java SDK 及其依赖项的最简单方法是使用 [Apache Maven](https://maven.apache.org/)。 要执行此操作，需要按照以下步骤将项目转换为 Maven 项目：

1. 在项目资源管理器中右键单击项目，单击“配置”，并单击“转换为 Maven 项目”。 

1. 在“创建新 POM”窗口中，接受默认值，并单击“完成”。 

1. 在“项目资源管理器” 中，打开 pom.xml 文件。

1. 在“依赖项”选项卡上，在“依赖项”窗格中单击“添加”。  

1. 在“选择依赖项”  窗口中，执行以下操作：

    * 在“组 ID”框中，输入 `com.azure`。
    * 在“项目 ID”框中，输入 `azure-cosmos`。
    * 在“版本”框中输入 `4.0.1-beta.1`。

    或者，可以直接将组 ID 和项目 ID 的依赖项 XML 添加到 pom.xml 文件：

    ```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-cosmos</artifactId>
      <version>4.0.1-beta.1</version>
    </dependency>
    ```

1. 单击“确定”，Maven 将安装 SQL Java SDK 或保存 pom.xml 文件。

<a name="UseService"></a>
## <a name="use-the-azure-cosmos-db-service-in-your-java-application"></a>在 Java 应用程序中使用 Azure Cosmos DB 服务

现在，让我们向 Web 应用程序添加模型、视图和控制器。

### <a name="add-a-model"></a>添加模型

首先，让我们在新文件 TodoItem.java 中定义模型。 `TodoItem` 类定义项的架构以及 getter 和 setter 方法：

```java
package com.microsoft.azure.documentdb.sample.model;

//@Data
//@Builder
public class TodoItem {
    private String entityType;
    private String category;
    private boolean complete;
    private String id;
    private String name;

    public String getCategory() {
        return category;
    }

    public void setCategory(String category) {
        this.category = category;
    }

    public String getEntityType() {
        return entityType;
    }

    public void setEntityType(String entityType) {
        this.entityType = entityType;
    }

    public boolean isComplete() {
        return complete;
    }

    public void setComplete(boolean complete) {
        this.complete = complete;
    }

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

}
```

### <a name="add-the-data-access-objectdao-classes"></a>添加数据访问对象 (DAO) 类

创建数据访问对象 (DAO)，将 ToDo 项保存到 Azure Cosmos DB 的过程进行抽象。 要将 ToDo 项保存到集合中，客户端需要知道保存到哪个数据库和集合（通过自链接引用） 通常，如果可能的话最好缓存数据库和集合，以避免额外的往返访问数据库。

1. 若要调用 Azure Cosmos DB 服务，必须实例化一个新的 `cosmosClient` 对象。 一般情况下，最好是重用 `cosmosClient`，而不是为每个后续请求构造新的客户端。 可以通过在 `cosmosClientFactory` 类中定义客户端来重用它。 更新在[步骤 1](#CreateDB) 中保存的 HOST 和 MASTER_KEY 值。 将 HOST 替换为 URI，将 MASTER_KEY 替换为主密钥。 使用以下代码在 CosmosClientFactory.java 文件中创建 `CosmosClientFactory` 类：

    ```java
    package com.microsoft.azure.documentdb.sample.dao;

    import com.azure.cosmos.ConnectionPolicy;
    import com.azure.cosmos.ConsistencyLevel;
    import com.azure.cosmos.CosmosClient;
    import com.azure.cosmos.CosmosClientBuilder;

    public class CosmosClientFactory {
       private static final String HOST = "https://docdb-java-sample.documents.azure.cn:443/";
       private static final String MASTER_KEY = "[YOUR_KEY_HERE]";

       private static CosmosClient cosmosClient = new CosmosClientBuilder()
               .setEndpoint(HOST)
               .setKey(MASTER_KEY)
               .setConnectionPolicy(ConnectionPolicy.getDefaultPolicy())
               .setConsistencyLevel(ConsistencyLevel.EVENTUAL)
               .buildClient();

       public static CosmosClient getCosmosClient() {
           return cosmosClient;
       }

    }
    ```

1. 创建新 TodoDao.java 文件，并添加 `TodoDao` 类以创建、更新、读取和删除 ToDo 项：

    ```java
    package com.microsoft.azure.documentdb.sample.dao;

    import java.util.List;

    import com.microsoft.azure.documentdb.sample.model.TodoItem;

    public interface TodoDao {
       /**
        * @return A list of TodoItems
        */
       public List<TodoItem> readTodoItems();

       /**
        * @param todoItem
        * @return whether the todoItem was persisted.
        */
       public TodoItem createTodoItem(TodoItem todoItem);

       /**
        * @param id
        * @return the TodoItem
        */
       public TodoItem readTodoItem(String id);

       /**
        * @param id
        * @return the TodoItem
        */
       public TodoItem updateTodoItem(String id, boolean isComplete);

       /**
        *
        * @param id
        * @return whether the delete was successful.
        */
       public boolean deleteTodoItem(String id);
    }
    ```

1. 创建新 MockDao.java 文件，并添加 `MockDao` 类，此类实现 `TodoDao` 类以对项执行 CRUD 操作：

    ```java
    package com.microsoft.azure.documentdb.sample.dao;

    import java.util.ArrayList;
    import java.util.HashMap;
    import java.util.List;
    import java.util.Map;

    import lombok.NonNull;

    import org.apache.commons.lang3.StringUtils;

    import com.microsoft.azure.documentdb.sample.model.TodoItem;

    public class MockDao implements TodoDao {
       private final Map<String, TodoItem> todoItemMap;

       public MockDao() {
           todoItemMap = new HashMap<String, TodoItem>();
       }

       @Override
       public TodoItem createTodoItem(@NonNull TodoItem todoItem) {
           if (StringUtils.isEmpty(todoItem.getId())) {
               todoItem.setId(generateId());
           }
           todoItemMap.put(todoItem.getId(), todoItem);
           return todoItem;
       }

       @Override
       public TodoItem readTodoItem(@NonNull String id) {
           return todoItemMap.get(id);
       }

       @Override
       public List<TodoItem> readTodoItems() {
           return new ArrayList<TodoItem>(todoItemMap.values());
       }

       @Override
       public TodoItem updateTodoItem(String id, boolean isComplete) {
           todoItemMap.get(id).setComplete(isComplete);
           return todoItemMap.get(id);
       }

       @Override
       public boolean deleteTodoItem(@NonNull String id) {
           todoItemMap.remove(id);
           return true;
       }

       private String generateId() {
           return new Integer(todoItemMap.size()).toString();
       }
    }
    ```

1. 创建新 DocDbDao.java 文件，并添加 `DocDbDao` 类。 此类定义的代码用于将 TodoItem 保存到容器中，在存在数据库和集合时检索数据库和集合或是在不存在时创建新的数据库和集合。 此示例使用 [Gson](https://code.google.com/p/google-gson/) 将 TodoItem 普通旧 Java 对象 (POJO) 序列化到 JSON 文档和从中反序列化 POJO。 要将 ToDo 项保存到集合中，客户端需要知道保存到哪个数据库和集合（通过自链接引用） 此类还定义帮助程序函数以便按另一个属性（例如“ID”）而不是自链接来检索文档。 可使用帮助程序方法按 ID 检索 TodoItem JSON 文档，然后将其反序列化到 POJO。

    我们还可以通过 `cosmosClient` 客户端对象使用 SQL 查询获取 TodoItem 的集合或列表。 最后，定义删除方法以从列表中删除 TodoItem。 以下代码演示 `DocDbDao` 类的内容：

    ```java
    package com.microsoft.azure.documentdb.sample.dao;

    import java.util.ArrayList;
    import java.util.List;

    import com.google.gson.Gson;
    import com.azure.cosmos.ConnectionPolicy;
    import com.azure.cosmos.ConsistencyLevel;
    import com.azure.cosmos.CosmosClient;
    import com.azure.cosmos.CosmosClientBuilder;
    import com.azure.cosmos.CosmosClientException;
    import com.azure.cosmos.CosmosContainer;
    import com.azure.cosmos.CosmosDatabase;
    import com.azure.cosmos.implementation.Utils;
    import com.azure.cosmos.models.CosmosContainerProperties;
    import com.azure.cosmos.models.CosmosItemRequestOptions;
    import com.azure.cosmos.models.FeedOptions;
    import com.azure.cosmos.models.FeedResponse;
    import com.azure.cosmos.models.PartitionKey;
    import com.fasterxml.jackson.core.JsonProcessingException;
    import com.fasterxml.jackson.databind.JsonNode;
    import com.fasterxml.jackson.databind.ObjectMapper;
    import com.fasterxml.jackson.databind.node.ObjectNode;
    import com.microsoft.azure.documentdb.sample.model.TodoItem;

    public class DocDbDao implements TodoDao {
       // The name of our database.
       private static final String DATABASE_ID = "TestDB";

       // The name of our collection.
       private static final String CONTAINER_ID = "TestCollection";

       // We'll use Gson for POJO <=> JSON serialization for this example.
       private static Gson gson = new Gson();

       // The Cosmos DB Client
       private static CosmosClient cosmosClient = CosmosClientFactory
               .getCosmosClient();

       // The Cosmos DB database
       private static CosmosDatabase cosmosDatabase = null;

       // The Cosmos DB container
       private static CosmosContainer cosmosContainer = null;

       // For POJO/JsonNode interconversion
       private static final ObjectMapper OBJECT_MAPPER = Utils.getSimpleObjectMapper();

       @Override
       public TodoItem createTodoItem(TodoItem todoItem) {
           // Serialize the TodoItem as a JSON Document.

           JsonNode todoItemJson = OBJECT_MAPPER.valueToTree(todoItem);

           ((ObjectNode)todoItemJson).put("entityType", "todoItem");    

           try {
               // Persist the document using the DocumentClient.
               todoItemJson = 
                       getContainerCreateResourcesIfNotExist()
                       .createItem(todoItemJson)
                       .getResource();
           } catch (CosmosClientException e) {
               System.out.println("Error creating TODO item.\n");
               e.printStackTrace();
               return null;
           }

           try {

               return OBJECT_MAPPER.treeToValue(todoItemJson, TodoItem.class);
               //return todoItem;
           } catch (Exception e) {
               System.out.println("Error deserializing created TODO item.\n");
               e.printStackTrace();

               return null;
           }

       }

       @Override
       public TodoItem readTodoItem(String id) {
           // Retrieve the document by id using our helper method.
           JsonNode todoItemJson = getDocumentById(id);

           if (todoItemJson != null) {
               // De-serialize the document in to a TodoItem.
               try {
               return OBJECT_MAPPER.treeToValue(todoItemJson, TodoItem.class);
               } catch (JsonProcessingException e) {
                   System.out.println("Error deserializing read TODO item.\n");
                   e.printStackTrace();

                   return null;             
               }
           } else {
               return null;
           }
       }

       @Override
       public List<TodoItem> readTodoItems() {

           List<TodoItem> todoItems = new ArrayList<TodoItem>();        

           String sql = "SELECT * FROM root r WHERE r.entityType = 'todoItem'";                    
           int maxItemCount = 1000;
           int maxDegreeOfParallelism = 1000;
           int maxBufferedItemCount = 100;

           FeedOptions options = new FeedOptions();
           options.setMaxItemCount(maxItemCount);
           options.setMaxBufferedItemCount(maxBufferedItemCount);
           options.setMaxDegreeOfParallelism(maxDegreeOfParallelism);
           options.setRequestContinuation(null);
           options.setPopulateQueryMetrics(false);

           int error_count = 0;
           int error_limit = 10;

           do {

               for (FeedResponse<JsonNode> pageResponse: 
                   getContainerCreateResourcesIfNotExist()
                       .queryItems(sql, options, JsonNode.class)
                       .iterableByPage()) {

                   options.setRequestContinuation(pageResponse.getContinuationToken());

                   for (JsonNode item : pageResponse.getElements()) {

                       try {
                           todoItems.add(OBJECT_MAPPER.treeToValue(item, TodoItem.class));
                       } catch (JsonProcessingException e) {
                           if (error_count < error_limit) {
                               error_count++;
                               if (error_count >= error_limit) {
                                   System.out.println("\n...reached max error count.\n");
                               } else {
                                   System.out.println("Error deserializing TODO item JsonNode. " + 
                                           "This item will not be returned.");
                                   e.printStackTrace();
                               }
                           }
                       }

                   }
               }

           } while(options.getRequestContinuation() != null);        

           return todoItems;
       }

       @Override
       public TodoItem updateTodoItem(String id, boolean isComplete) {
           // Retrieve the document from the database
           JsonNode todoItemJson = getDocumentById(id);

           // You can update the document as a JSON document directly.
           // For more complex operations - you could de-serialize the document in
           // to a POJO, update the POJO, and then re-serialize the POJO back in to
           // a document.
           ((ObjectNode)todoItemJson).put("complete", isComplete);

           try {
               // Persist/replace the updated document.
               todoItemJson = 
                       getContainerCreateResourcesIfNotExist()
                       .replaceItem(todoItemJson, id, new PartitionKey(id), new CosmosItemRequestOptions())
                       .getResource();
           } catch (CosmosClientException e) {
               System.out.println("Error updating TODO item.\n");
               e.printStackTrace();
               return null;
           }

           // De-serialize the document in to a TodoItem.
           try {
               return OBJECT_MAPPER.treeToValue(todoItemJson, TodoItem.class);
           } catch (JsonProcessingException e) {
               System.out.println("Error deserializing updated item.\n");
               e.printStackTrace();

               return null;             
           }        
       }

       @Override
       public boolean deleteTodoItem(String id) {
           // CosmosDB refers to documents by self link rather than id.

           // Query for the document to retrieve the self link.
           JsonNode todoItemJson = getDocumentById(id);

           try {
               // Delete the document by self link.
               getContainerCreateResourcesIfNotExist()
                   .deleteItem(id, new PartitionKey(id), new CosmosItemRequestOptions());
           } catch (CosmosClientException e) {
               System.out.println("Error deleting TODO item.\n");
               e.printStackTrace();
               return false;
           }

           return true;
       }

       /*

       private CosmosDatabase getTodoDatabase() {
           if (databaseCache == null) {
               // Get the database if it exists
               List<CosmosDatabase> databaseList = cosmosClient
                       .queryDatabases(
                               "SELECT * FROM root r WHERE r.id='" + DATABASE_ID
                                       + "'", null).getQueryIterable().toList();

               if (databaseList.size() > 0) {
                   // Cache the database object so we won't have to query for it
                   // later to retrieve the selfLink.
                   databaseCache = databaseList.get(0);
               } else {
                   // Create the database if it doesn't exist.
                   try {
                       CosmosDatabase databaseDefinition = new CosmosDatabase();
                       databaseDefinition.setId(DATABASE_ID);

                       databaseCache = cosmosClient.createDatabase(
                               databaseDefinition, null).getResource();
                   } catch (CosmosClientException e) {
                       // TODO: Something has gone terribly wrong - the app wasn't
                       // able to query or create the collection.
                       // Verify your connection, endpoint, and key.
                       e.printStackTrace();
                   }
               }
           }

           return databaseCache;
       }

       */

       private CosmosContainer getContainerCreateResourcesIfNotExist() {

           try {

               if (cosmosDatabase == null) {
                   cosmosDatabase = cosmosClient.createDatabaseIfNotExists(DATABASE_ID).getDatabase();
               }

           } catch(CosmosClientException e) {
               // TODO: Something has gone terribly wrong - the app wasn't
               // able to query or create the collection.
               // Verify your connection, endpoint, and key.
               System.out.println("Something has gone terribly wrong - " +
                                  "the app wasn't able to create the Database.\n");
               e.printStackTrace();
           }        

           try {

               if (cosmosContainer == null) {
                   CosmosContainerProperties properties = new CosmosContainerProperties(CONTAINER_ID,"/id");
                   cosmosContainer = cosmosDatabase.createContainerIfNotExists(properties).getContainer();
               }

           } catch(CosmosClientException e) {
               // TODO: Something has gone terribly wrong - the app wasn't
               // able to query or create the collection.
               // Verify your connection, endpoint, and key.
               System.out.println("Something has gone terribly wrong - " +
                       "the app wasn't able to create the Container.\n");
               e.printStackTrace();
           }    

           return cosmosContainer;
       }

       private JsonNode getDocumentById(String id) {

           String sql = "SELECT * FROM root r WHERE r.id='" + id + "'";                    
           int maxItemCount = 1000;
           int maxDegreeOfParallelism = 1000;
           int maxBufferedItemCount = 100;

           FeedOptions options = new FeedOptions();
           options.setMaxItemCount(maxItemCount);
           options.setMaxBufferedItemCount(maxBufferedItemCount);
           options.setMaxDegreeOfParallelism(maxDegreeOfParallelism);
           options.setRequestContinuation(null);
           options.setPopulateQueryMetrics(false);

           List<JsonNode> itemList = new ArrayList();

           do {

               for (FeedResponse<JsonNode> pageResponse: 
                   getContainerCreateResourcesIfNotExist()
                       .queryItems(sql, options, JsonNode.class)
                       .iterableByPage()) {

                   options.setRequestContinuation(pageResponse.getContinuationToken());

                   for (JsonNode item : pageResponse.getElements()) {
                       itemList.add(item);
                   }
               }

           } while(options.getRequestContinuation() != null);

           if (itemList.size() > 0) {
               return itemList.get(0);
           } else {
               return null;
           }
       }

    }
    ```

1. 接下来，创建新 TodoDaoFactory.java 文件，并添加创建新 DocDbDao 对象的 `TodoDaoFactory` 类：

    ```java
    package com.microsoft.azure.documentdb.sample.dao;

    public class TodoDaoFactory {
       private static TodoDao myTodoDao = new DocDbDao();

       public static TodoDao getDao() {
           return myTodoDao;
       }
    }
    ```

### <a name="add-a-controller"></a>添加控制器

将 TodoItemController 控制器添加到应用程序。 在此项目中，将使用[项目 Lombok](https://projectlombok.org/) 生成构造函数、getter、setter 和一个生成器。 或者，可以手动编写此代码，或使用 IDE 生成此代码：

```java
package com.microsoft.azure.documentdb.sample.controller;

import java.util.List;
import java.util.UUID;

import lombok.NonNull;

import com.microsoft.azure.documentdb.sample.dao.TodoDao;
import com.microsoft.azure.documentdb.sample.dao.TodoDaoFactory;
import com.microsoft.azure.documentdb.sample.model.TodoItem;

public class TodoItemController {
    public static TodoItemController getInstance() {
        if (todoItemController == null) {
            todoItemController = new TodoItemController(TodoDaoFactory.getDao());
        }
        return todoItemController;
    }

    private static TodoItemController todoItemController;

    private final TodoDao todoDao;

    TodoItemController(TodoDao todoDao) {
        this.todoDao = todoDao;
    }

    public TodoItem createTodoItem(@NonNull String name,
            @NonNull String category, boolean isComplete) {
        TodoItem todoItem = new TodoItem();

        todoItem.setName(name);
        todoItem.setCategory(category);
        todoItem.setComplete(isComplete);
        todoItem.setId(UUID.randomUUID().toString());

        return todoDao.createTodoItem(todoItem);
    }

    public boolean deleteTodoItem(@NonNull String id) {
        return todoDao.deleteTodoItem(id);
    }

    public TodoItem getTodoItemById(@NonNull String id) {
        return todoDao.readTodoItem(id);
    }

    public List<TodoItem> getTodoItems() {
        return todoDao.readTodoItems();
    }

    public TodoItem updateTodoItem(@NonNull String id, boolean isComplete) {
        return todoDao.updateTodoItem(id, isComplete);
    }
}
```

### <a name="create-a-servlet"></a>创建 servlet

接下来，创建 servlet 以将 HTTP 请求路由到控制器。 创建 ApiServlet.java 文件，并在其下定义以下代码：

```java
package com.microsoft.azure.documentdb.sample;

import java.io.IOException;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.apache.commons.lang3.StringUtils;

import com.google.gson.Gson;
import com.microsoft.azure.documentdb.sample.controller.TodoItemController;

/**
 * API Frontend Servlet
 */
@WebServlet("/api")
public class ApiServlet extends HttpServlet {
    // API Keys
    public static final String API_METHOD = "method";

    // API Methods
    public static final String CREATE_TODO_ITEM = "createTodoItem";
    public static final String GET_TODO_ITEMS = "getTodoItems";
    public static final String UPDATE_TODO_ITEM = "updateTodoItem";

    // API Parameters
    public static final String TODO_ITEM_ID = "todoItemId";
    public static final String TODO_ITEM_NAME = "todoItemName";
    public static final String TODO_ITEM_CATEGORY = "todoItemCategory";
    public static final String TODO_ITEM_COMPLETE = "todoItemComplete";

    public static final String MESSAGE_ERROR_INVALID_METHOD = "{'error': 'Invalid method'}";

    private static final long serialVersionUID = 1L;
    private static final Gson gson = new Gson();

    @Override
    protected void doGet(HttpServletRequest request,
            HttpServletResponse response) throws ServletException, IOException {

        String apiResponse = MESSAGE_ERROR_INVALID_METHOD;

        TodoItemController todoItemController = TodoItemController
                .getInstance();

        String id = request.getParameter(TODO_ITEM_ID);
        String name = request.getParameter(TODO_ITEM_NAME);
        String category = request.getParameter(TODO_ITEM_CATEGORY);
        boolean isComplete = StringUtils.equalsIgnoreCase("true",
                request.getParameter(TODO_ITEM_COMPLETE)) ? true : false;

        switch (request.getParameter(API_METHOD)) {
        case CREATE_TODO_ITEM:
            apiResponse = gson.toJson(todoItemController.createTodoItem(name,
                    category, isComplete));
            break;
        case GET_TODO_ITEMS:
            apiResponse = gson.toJson(todoItemController.getTodoItems());
            break;
        case UPDATE_TODO_ITEM:
            apiResponse = gson.toJson(todoItemController.updateTodoItem(id,
                    isComplete));
            break;
        default:
            break;
        }

        response.setCharacterEncoding("UTF-8");
        response.getWriter().println(apiResponse);
    }

    @Override
    protected void doPost(HttpServletRequest request,
            HttpServletResponse response) throws ServletException, IOException {
        doGet(request, response);
    }
}
```

<a name="Wire"></a>
## <a name="wire-the-rest-of-the-of-java-app-together"></a>将 Java 应用的其余内容绑定在一起

现在，我们完成了有趣的部分，剩下所有要做的是构建一个快速的用户接口，并将其与我们的 DAO 进行绑定。

1. 需要一个 Web 用户界面来向用户显示。 让我们使用以下代码重新编写之前创建的 index.jsp：

    ```java
    <%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
     <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
     <meta http-equiv="X-UA-Compatible" content="IE=edge;" />
     <title>Azure DocumentDB Java Sample</title>

     <!-- Bootstrap -->

     <link href="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet">

     <style>
       /* Add padding to body for fixed nav bar */
       body {
         padding-top: 50px;
       }
     </style>
    </head>
    <body>
     <!-- Nav Bar -->

     <div class="navbar navbar-inverse navbar-fixed-top" role="navigation">
       <div class="container">
         <div class="navbar-header">
           <a class="navbar-brand" href="#">My Tasks</a>
         </div>
       </div>
     </div>

     <!-- Body -->

     <div class="container">
       <h1>My ToDo List</h1>

       <hr/>

       <!-- The ToDo List -->

       <div class = "todoList">
         <table class="table table-bordered table-striped" id="todoItems">
           <thead>
             <tr>
               <th>Name</th>
               <th>Category</th>
               <th>Complete</th>
             </tr>
           </thead>
           <tbody>
           </tbody>
         </table>

         <!-- Update Button -->

         <div class="todoUpdatePanel">
           <form class="form-horizontal" role="form">
             <button type="button" class="btn btn-primary">Update Tasks</button>
           </form>
         </div>

       </div>

       <hr/>

       <!-- Item Input Form -->

       <div class="todoForm">
         <form class="form-horizontal" role="form">
           <div class="form-group">
             <label for="inputItemName" class="col-sm-2">Task Name</label>
             <div class="col-sm-10">
               <input type="text" class="form-control" id="inputItemName" placeholder="Enter name">
             </div>
           </div>

           <div class="form-group">
             <label for="inputItemCategory" class="col-sm-2">Task Category</label>
             <div class="col-sm-10">
               <input type="text" class="form-control" id="inputItemCategory" placeholder="Enter category">
             </div>
           </div>

           <button type="button" class="btn btn-primary">Add Task</button>
         </form>
       </div>

     </div>

     <!-- Placed at the end of the document so the pages load faster -->

     <script src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.1.min.js"></script>
     <script src="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/bootstrap.min.js"></script>
     <script src="assets/todo.js"></script>
    </body>
    </html>
    ```

1. 最后，编写一些客户端 JavaScript 将 Web 用户界面和 servlet 绑定在一起：

    ```java
    /**
    * ToDo App
    */

    var todoApp = {
     /*
      * API methods to call Java backend.
      */
     apiEndpoint: "api",

     createTodoItem: function(name, category, isComplete) {
       $.post(todoApp.apiEndpoint, {
           "method": "createTodoItem",
           "todoItemName": name,
           "todoItemCategory": category,
           "todoItemComplete": isComplete
         },
         function(data) {
           var todoItem = data;
           todoApp.addTodoItemToTable(todoItem.id, todoItem.name, todoItem.category, todoItem.complete);
         },
         "json");
     },

     getTodoItems: function() {
       $.post(todoApp.apiEndpoint, {
           "method": "getTodoItems"
         },
         function(data) {
           var todoItemArr = data;
           $.each(todoItemArr, function(index, value) {
             todoApp.addTodoItemToTable(value.id, value.name, value.category, value.complete);
           });
         },
         "json");
     },

     updateTodoItem: function(id, isComplete) {
       $.post(todoApp.apiEndpoint, {
           "method": "updateTodoItem",
           "todoItemId": id,
           "todoItemComplete": isComplete
         },
         function(data) {},
         "json");
     },

     /*
      * UI Methods
      */
     addTodoItemToTable: function(id, name, category, isComplete) {
       var rowColor = isComplete ? "active" : "warning";

       todoApp.ui_table().append($("<tr>")
         .append($("<td>").text(name))
         .append($("<td>").text(category))
         .append($("<td>")
           .append($("<input>")
             .attr("type", "checkbox")
             .attr("id", id)
             .attr("checked", isComplete)
             .attr("class", "isComplete")
           ))
         .addClass(rowColor)
       );
     },

     /*
      * UI Bindings
      */
     bindCreateButton: function() {
       todoApp.ui_createButton().click(function() {
         todoApp.createTodoItem(todoApp.ui_createNameInput().val(), todoApp.ui_createCategoryInput().val(), false);
         todoApp.ui_createNameInput().val("");
         todoApp.ui_createCategoryInput().val("");
       });
     },

     bindUpdateButton: function() {
       todoApp.ui_updateButton().click(function() {
         // Disable button temporarily.
         var myButton = $(this);
         var originalText = myButton.text();
         $(this).text("Updating...");
         $(this).prop("disabled", true);

         // Call api to update todo items.
         $.each(todoApp.ui_updateId(), function(index, value) {
           todoApp.updateTodoItem(value.name, value.value);
           $(value).remove();
         });

         // Re-enable button.
         setTimeout(function() {
           myButton.prop("disabled", false);
           myButton.text(originalText);
         }, 500);
       });
     },

     bindUpdateCheckboxes: function() {
       todoApp.ui_table().on("click", ".isComplete", function(event) {
         var checkboxElement = $(event.currentTarget);
         var rowElement = $(event.currentTarget).parents('tr');
         var id = checkboxElement.attr('id');
         var isComplete = checkboxElement.is(':checked');

         // Togle table row color
         if (isComplete) {
           rowElement.addClass("active");
           rowElement.removeClass("warning");
         } else {
           rowElement.removeClass("active");
           rowElement.addClass("warning");
         }

         // Update hidden inputs for update panel.
         todoApp.ui_updateForm().children("input[name='" + id + "']").remove();

         todoApp.ui_updateForm().append($("<input>")
           .attr("type", "hidden")
           .attr("class", "updateComplete")
           .attr("name", id)
           .attr("value", isComplete));

       });
     },

     /*
      * UI Elements
      */
     ui_createNameInput: function() {
       return $(".todoForm #inputItemName");
     },

     ui_createCategoryInput: function() {
       return $(".todoForm #inputItemCategory");
     },

     ui_createButton: function() {
       return $(".todoForm button");
     },

     ui_table: function() {
       return $(".todoList table tbody");
     },

     ui_updateButton: function() {
       return $(".todoUpdatePanel button");
     },

     ui_updateForm: function() {
       return $(".todoUpdatePanel form");
     },

     ui_updateId: function() {
       return $(".todoUpdatePanel .updateComplete");
     },

     /*
      * Install the TodoApp
      */
     install: function() {
       todoApp.bindCreateButton();
       todoApp.bindUpdateButton();
       todoApp.bindUpdateCheckboxes();

       todoApp.getTodoItems();
     }
    };

    $(document).ready(function() {
     todoApp.install();
    });
    ```

1. 现在剩下的就是测试此应用程序。 在本地运行此应用程序，并添加一些 Todo 项，方法是填充项名称和类别，并单击“添加任务” 。 显示项之后，可以通过切换复选框，然后单击“更新任务”来更新项是否已完成。

<a name="Deploy"></a>
## <a name="deploy-your-java-application-to-azure-web-sites"></a>将 Java 应用程序部署到 Azure 网站

要在 Azure 网站上部署 Java 应用程序，只需将应用程序导出为 WAR 文件，然后通过源控件（例如 Git）或 FTP 上传此文件。

1. 要将应用程序导出为 WAR 文件，请在“项目资源管理器”中右键单击项目，然后依次单击“导出”和“WAR 文件”。  

1. 在“WAR 导出”  窗口中，执行以下操作：

    * 在“Web 项目”框中，输入 azure-documentdb-java-sample。
    * 在“目标”框中，选择一个目标以保存 WAR 文件。
    * 单击“完成”。

1. 现在已经具有 WAR 文件，只需将它上传到 Azure 网站的 **webapps** 目录。 有关上传此文件的说明，请参阅[将 Java 应用程序添加到 Azure 应用服务 Web 应用](../app-service/quickstart-java.md)。 将 WAR 文件上传到 webapps 目录之后，运行时环境将检测到已经添加了此文件，并会自动加载它。

1. 若要查看已完成的产品，请导航到 `http://YOUR\_SITE\_NAME.chinacloudsites.cn/azure-java-sample/` 并开始添加任务！

<a name="GetProject"></a>
## <a name="get-the-project-from-github"></a>从 GitHub 获取项目

GitHub 上的 [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) 项目包含本教程中的所有示例。 要将 todo 项目导入 Eclipse，请确保具有 [先决条件](#Prerequisites) 部分中所列的软件和资源，并执行以下操作：

1. 安装 [项目 Lombok](https://projectlombok.org/)。 Lombok 用于生成项目中的构造函数、getter 和 setter。 下载 lombok.jar 文件之后，双击此文件进行安装，或者从命令行安装。

1. 如果 Eclipse 处于打开状态，请将其关闭并重新启动以加载 Lombok。

1. 在 Eclipse 中，在“文件”菜单上单击“导入”。 

1. 在“导入”窗口中，依次单击“Git”、“来自 Git 的项目”和“下一步”。   

1. 在“选择存储库源”屏幕上，单击“克隆 URI”。 

1. 在“源 Git 存储库”屏幕上的“URI”框中，输入 https://github.com/Azure-Samples/documentdb-java-todo-app.git ，然后单击“下一步”  。

1. 在“分支选择”屏幕上，确保已选择“master”，并单击“下一步”。  

1. 在“本地目标”屏幕上，单击“浏览”选择要将存储库复制到的文件夹，然后单击“下一步”。  

1. 在“选择要用于导入项目的向导”屏幕上，确保已选择“导入现有项目”，并单击“下一步”。  

1. 在“导入项目”屏幕上，取消选择“DocumentDB”项目，并单击“完成”。   DocumentDB 项目包含 Azure Cosmos DB Java SDK，我们会将其添加为依赖项。

1. 在“项目资源管理器”中，导航到 azure-documentdb-java-sample\src\com.microsoft.azure.documentdb.sample.dao\DocumentClientFactory.java，并将 HOST 和 MASTER_KEY 值替换为 Azure Cosmos DB 帐户的 URI 和主密钥，然后保存该文件。 有关更多信息，请参阅[步骤 1.创建 Azure Cosmos 数据库帐户](#CreateDB)。

1. 在“项目资源管理器”中，右键单击“azure-documentdb-java-sample”，单击“生成路径”，并单击“配置生成路径”。   

1. 在“Java 生成路径”屏幕上，在右侧窗格中，选择“库”选项卡，并单击“添加外部 JAR”。   导航至 lombok.jar 文件的位置，单击“打开”，并单击“确定”。 

1. 使用步骤 12 再次打开“属性”窗口，并在左窗格中单击“目标运行时”。 

1. 在“目标运行时”屏幕上，单击“新建”，选择“Apache Tomcat v7.0”，并单击“确定”。   

1. 使用步骤 12 再次打开“属性”窗口，并在左窗格中单击“项目方面”。 

1. 在“项目方面”屏幕上，选择“动态 Web 模块”和“Java”，并单击“确定”。   

1. 在此屏幕下面的“服务器”选项卡上，右键单击“localhost 上的 Tomcat v7.0 服务器”，并单击“添加和删除”。  

1. 在“添加和删除”窗口中，将 **azure-documentdb-java-sample** 移到“配置”框，然后单击“完成”。  

1. 在“服务器”选项卡上，右键单击“localhost 上的 Tomcat v7.0 服务器”，并单击“重新启动”。  

1. 在浏览器中，导航到 `http://localhost:8080/azure-documentdb-java-sample/` 并开始向任务列表添加内容。 请注意，如果更改了默认端口值，请将 8080 更改成选择的值。

1. 要将项目部署到 Azure 网站，请参阅[步骤 6. 将应用程序部署到 Azure 网站](#Deploy)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure Cosmos DB 生成 node.js 应用程序](sql-api-nodejs-application.md)

<!-- Update_Description: update meta properties, wording update, update link -->