---
title: Granice obiektów
description: Wyjaśnia, w jaki sposób można zbadać granice obiektu przestrzennego
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: bd2b1026f69b5ec5814124a92d6a78d7f0225143
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613827"
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
        if (bounds->GetIsRanToCompletion())
        {
            Double3 aabbMin = bounds->GetResult().min;
            Double3 aabbMax = bounds->GetResult().max;
            // ...
        }
    });
}
```

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [Obiekt C# Entity. QueryLocalBoundsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.entity.querylocalboundsasync)
* [Jednostka C++:: QueryLocalBoundsAsync](https://docs.microsoft.com/cpp/api/remote-rendering/entity#querylocalboundsasync)

## <a name="next-steps"></a>Następne kroki

* [Zapytania przestrzenne](../overview/features/spatial-queries.md)
