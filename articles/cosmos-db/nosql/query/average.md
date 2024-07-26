---
title: AVG
titleSuffix: Azure Cosmos DB for NoSQL
description: An Azure Cosmos DB for NoSQL system function that returns the average of multiple numeric values.
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

# AVG (NoSQL query)

[!INCLUDE[NoSQL](../../includes/appliesto-nosql.md)]

Returns the average of the values in the expression.
  
## Syntax
  
```nosql
AVG(<numeric_expr>)  
```  
  
## Arguments

| | Description |
| --- | --- |
| **`numeric_expr`** | A numeric expression. |  
  
## Return types

Returns a numeric expression.
  
## Examples

For this example, consider a container with multiple items that each contain a `price` field.

:::code language="json" source="~/cosmos-db-nosql-query-samples/scripts/average/seed.json" range="1-2,4-8,10-14,16-20" highlight="5,10,15":::

In this example, the function is used to average the values of a specific field into a single aggregated value.

:::code language="nosql" source="~/cosmos-db-nosql-query-samples/scripts/average/query.sql" highlight="2":::

:::code language="json" source="~/cosmos-db-nosql-query-samples/scripts/average/result.json":::

## Remarks

- This function benefits from a [range index](../../index-policy.md#includeexclude-strategy).
- If any arguments in `AVG` are string, boolean, or null; the entire aggregate system function returns `undefined`.
- If any individual argument has an `undefined` value that value isn't included in the `AVG` calculation.

## Related content

- [System functions](system-functions.yml)
- [`SUM`](sum.md)
