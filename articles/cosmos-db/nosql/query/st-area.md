---
title: ST_AREA
titleSuffix: Azure Cosmos DB for NoSQL
description: An Azure Cosmos DB for NoSQL system function that returns the total area of a GeoJSON polygon or multi-polygon.
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

# ST_AREA (NoSQL query)

[!INCLUDE[NoSQL](../../includes/appliesto-nosql.md)]

Returns the total area of a GeoJSON **Polygon** or **MultiPolygon** expression.

> [!NOTE]
> For more information, see [Geospatial and GeoJSON location data](geospatial-intro.md).

## Syntax

```nosql
ST_AREA(<spatial_expr>)
```

## Arguments

| | Description |
| --- | --- |
| **`spatial_expr`** | Any valid GeoJSON **Polygon** or **MultiPolygon** expression. |

## Return types

Returns a numeric expression that enumerates the total area of a set of points.

## Examples

The following example shows how to return the area of a polygon.

:::code language="nosql" source="~/cosmos-db-nosql-query-samples/scripts/st-area/query.sql" highlight="2-11":::

:::code language="json" source="~/cosmos-db-nosql-query-samples/scripts/st-area/result.json":::

## Remarks

- The result is expressed in square meters for the default reference system.
- Using this function to calculate the area of zero or one-dimensional figures like GeoJSON **Points** and **LineStrings** results in an area of `0`.
- The GeoJSON specification requires that points within a Polygon be specified in counter-clockwise order. A Polygon specified in clockwise order represents the inverse of the region within it.

## Related content

- [System functions](system-functions.yml)
- [`ST_WITHIN`](st-within.md)
