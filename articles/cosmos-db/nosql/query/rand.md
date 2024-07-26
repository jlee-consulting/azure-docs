---
title: RAND
titleSuffix: Azure Cosmos DB for NoSQL
description: An Azure Cosmos DB for NoSQL system function that returns a randomly generated numeric value from zero to one.
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

# RAND (NoSQL query)

[!INCLUDE[NoSQL](../../includes/appliesto-nosql.md)]

Returns a randomly generated numeric value from zero to one.

## Syntax

```nosql
RAND()
```

## Return types

Returns a numeric expression.

## Examples

The following example returns randomly generated numeric values.

:::code language="nosql" source="~/cosmos-db-nosql-query-samples/scripts/rand/query.novalidate.sql" highlight="2-3":::

:::code language="json" source="~/cosmos-db-nosql-query-samples/scripts/rand/result.novalidate.json":::

## Remarks

- This function doesn't use the index.
- This function is nondeterministic. Repetitive calls of this function don't return the same results.

## Related content

- [System functions](system-functions.yml)
- [`IS_NUMBER`](is-number.md)
