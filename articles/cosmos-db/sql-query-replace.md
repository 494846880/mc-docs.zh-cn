---
title: Azure Cosmos DB 查询语言中的 REPLACE
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 REPLACE。
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
origin.date: 09/13/2019
author: rockboyfor
ms.date: 12/14/2020
ms.author: v-yeche
ms.custom: query-reference
ms.openlocfilehash: c1f07c3d2a6d2baa84b26b92175e8be17908eadb
ms.sourcegitcommit: a8afac9982deafcf0652c63fe1615ba0ef1877be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96850659"
---
# <a name="replace-azure-cosmos-db"></a>REPLACE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 将出现的所有指定字符串值替换为另一个字符串值。  

## <a name="syntax"></a>语法

```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  

## <a name="arguments"></a>参数

*str_expr1*  
  是要搜索的字符串表达式。  

*str_expr2*  
  要找到的字符串表达式。  

*str_expr3*  
  是用于替换 *str_expr1* 中的 *str_expr2* 匹配项的字符串表达式。  

## <a name="return-types"></a>返回类型

  返回一个字符串表达式。  

## <a name="examples"></a>示例

  以下示例演示如何在查询中使用 `REPLACE`。  

```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  

 下面是结果集。  

```json
[{"replace": "This is a desk"}]  
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)

<!-- Update_Description: update meta properties, wording update, update link -->