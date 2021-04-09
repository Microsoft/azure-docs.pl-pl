---
title: Granice obiektów
description: Wyjaśnia, w jaki sposób można zbadać granice obiektu przestrzennego
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: df04b767035dffb62fde89d1e74b808d62fcc943
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594488"
---
# <a name="object-bounds"></a>Granice obiektów

Granice obiektów reprezentują wolumin, który jest zajmowany przez [jednostkę](entities.md) i jej elementy podrzędne. W przypadku renderowania zdalnego na platformie Azure granice obiektów są zawsze przyznawane jako *wyrównane pola* (AABB) osi. Granice obiektów mogą znajdować się w *przestrzeni lokalnej* lub w *przestrzeni świata*. W obu przypadkach są zawsze wyrównane do osi, co oznacza, że rozmiary i woluminy mogą się różnić między reprezentacją przestrzeni lokalnej i światowej.

## <a name="querying-object-bounds"></a>Badanie granic obiektów

Pole ograniczenia wyrównanej osi lokalnej [siatki](meshes.md) może być badane bezpośrednio z zasobu siatki. Te granice mogą być przekształcone w miejsce lokalne lub na świecie jednostki przy użyciu transformacji jednostki.

Istnieje możliwość obliczenia granic całej hierarchii obiektów w ten sposób, ale wymaga ona przechodzenia do hierarchii, wykonywania zapytań dotyczących granic dla każdej siatki i łączenia ich ręcznie. Ta operacja jest zarówno żmudnym, jak i niewydajne.

Lepszym sposobem jest wywołanie `QueryLocalBoundsAsync` lub `QueryWorldBoundsAsync` w jednostce. Obliczenia są następnie przeciążać do serwera i zwracane z minimalnym opóźnieniem.

```cs
public async void GetBounds(Entity entity)
{
    try
    {
        Task<Bounds> boundsQuery = entity.QueryWorldBoundsAsync();
        Bounds result = await boundsQuery;
    
        Double3 aabbMin = result.Min;
        Double3 aabbMax = result.Max;
        // ...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    entity->QueryWorldBoundsAsync(
        // completion callback:
        [](Status status, Bounds bounds)
        {
           if (status == Status::OK)
            {
                Double3 aabbMin = bounds.Min;
                Double3 aabbMax = bounds.Max;
                // ...
            }
        }
    );
}
```

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [Obiekt C# Entity. QueryLocalBoundsAsync](/dotnet/api/microsoft.azure.remoterendering.entity.querylocalboundsasync)
* [Jednostka C++:: QueryLocalBoundsAsync](/cpp/api/remote-rendering/entity#querylocalboundsasync)

## <a name="next-steps"></a>Następne kroki

* [Zapytania przestrzenne](../overview/features/spatial-queries.md)