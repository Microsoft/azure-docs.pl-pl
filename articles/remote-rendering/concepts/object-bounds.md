---
title: Granice obiektów
description: Wyjaśnia, w jaki sposób można zbadać granice obiektu przestrzennego
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: d9f970d08318d7dec685d3021c72b7f80de90049
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758881"
---
# <a name="object-bounds"></a>Granice obiektów

Granice obiektów reprezentują wolumin, który jest zajmowany przez [jednostkę](entities.md) i jej elementy podrzędne. W przypadku renderowania zdalnego na platformie Azure granice obiektów są zawsze przyznawane jako *wyrównane pola* (AABB) osi. Granice obiektów mogą znajdować się w *przestrzeni lokalnej* lub w *przestrzeni świata*. W obu przypadkach są zawsze wyrównane do osi, co oznacza, że rozmiary i woluminy mogą się różnić między reprezentacją przestrzeni lokalnej i światowej.

## <a name="querying-object-bounds"></a>Badanie granic obiektów

Lokalne AABB [siatki](meshes.md) można zbadać bezpośrednio z zasobu sieci siatkowej. Te granice mogą być przekształcone w miejsce lokalne lub na świecie jednostki przy użyciu transformacji jednostki.

Istnieje możliwość obliczenia granic całej hierarchii obiektów w ten sposób, ale wymaga ona przechodzenia do hierarchii, wykonywania zapytań dotyczących granic dla każdej siatki i łączenia ich ręcznie. Ta operacja jest zarówno żmudnym, jak i niewydajne.

Lepszym sposobem jest wywołanie `QueryLocalBoundsAsync` lub `QueryWorldBoundsAsync` w jednostce. Obliczenia są następnie przeciążać do serwera i zwracane z minimalnym opóźnieniem.

```cs
private BoundsQueryAsync _boundsQuery = null;

public void GetBounds(Entity entity)
{
    _boundsQuery = entity.QueryWorldBoundsAsync();
    _boundsQuery.Completed += (BoundsQueryAsync bounds) =>
    {
        if (bounds.IsRanToCompletion)
        {
            Double3 aabbMin = bounds.Result.min;
            Double3 aabbMax = bounds.Result.max;
            // ...
        }
    };
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    ApiHandle<BoundsQueryAsync> boundsQuery = *entity->QueryWorldBoundsAsync();
    boundsQuery->Completed([](ApiHandle<BoundsQueryAsync> bounds)
    {
        if (bounds->IsRanToCompletion())
        {
            Double3 aabbMin = bounds->Result()->min;
            Double3 aabbMax = bounds->Result()->max;
            // ...
        }
    });
}
```

## <a name="next-steps"></a>Następne kroki

* [Zapytania przestrzenne](../overview/features/spatial-queries.md)
