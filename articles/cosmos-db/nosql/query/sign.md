---
title: SIGN
titleSuffix: Azure Cosmos DB for NoSQL
description: An Azure Cosmos DB for NoSQL system function that returns the sign of the specified number.
author: jcodella
ms.author: jacodel
ms.reviewer: sidandrews
ms.service: cosmos-db
ms.subservice: nosql
ms.topic: reference
ms.devlang: nosql
ms.date: 02/27/2024
ms.custom: query-reference
---

# SIGN (NoSQL query)

[!INCLUDE[NoSQL](../../includes/appliesto-nosql.md)]

Returns the positive (+1), zero (0), or negative (-1) sign of the specified numeric expression.  

## Syntax

```nosql
SIGN(<numeric_expr>)  
```

## Arguments

| | Description |
| --- | --- |
| **`numeric_expr`** | A numeric expression. |

## Return types

Returns a numeric expression.  

## Examples

The following example returns the sign of various numbers from -2 to 2.  

:::code language="nosql" source="~/cosmos-db-nosql-query-samples/scripts/sign/query.sql" highlight="2-6":::

:::code language="json" source="~/cosmos-db-nosql-query-samples/scripts/sign/result.json":::

## Remarks

- This function doesn't use the index.

## Related content

- [System functions](system-functions.yml)
- [`ABS`](abs.md)
