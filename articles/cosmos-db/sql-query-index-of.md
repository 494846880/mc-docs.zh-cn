---
title: Azure Cosmos DB 查询语言中的 INDEX_OF
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 INDEX_OF。
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
origin.date: 09/13/2019
author: rockboyfor
ms.date: 12/14/2020
ms.author: v-yeche
ms.custom: query-reference
ms.openlocfilehash: a26cb736cf5226324e47ed6a9027949f14a67097
ms.sourcegitcommit: a8afac9982deafcf0652c63fe1615ba0ef1877be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96850717"
---
# <a name="index_of-azure-cosmos-db"></a>INDEX_OF (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 返回第一个指定的字符串表达式中第一次出现第二个字符串表达式的起始位置，如果未找到字符串，则返回 -1。  

## <a name="syntax"></a>语法

```sql
INDEX_OF(<str_expr1>, <str_expr2> [, <numeric_expr>])  
```  

## <a name="arguments"></a>参数

*str_expr1*  
  是要搜索的字符串表达式。  

*str_expr2*  
  是要搜索的字符串表达式。  

*numeric_expr* 可选数值表达式，用于设置搜索开始位置。 *str_expr1* 中的第一个位置为 0。 

## <a name="return-types"></a>返回类型

  返回数值表达式。  

## <a name="examples"></a>示例

  以下示例将返回 "abc" 内不同子字符串的索引。  

```sql
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  

 下面是结果集：  

```json
[{"i1": 0, "i2": 1, "i3": -1}]  
```  

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)

<!-- Update_Description: update meta properties, wording update, update link -->