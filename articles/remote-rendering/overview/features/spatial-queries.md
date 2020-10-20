---
title: Zapytania przestrzenne
description: Jak wykonywać zapytania przestrzenne w scenie
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: d1a7baa25497cf1ba697725ac8530bc04c458aa5
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207447"
---
# <a name="spatial-queries"></a>Zapytania przestrzenne

Zapytania przestrzenne to operacje, za pomocą których można zażądać usługi zdalnego renderowania, które obiekty znajdują się w obszarze. Zapytania przestrzenne są często używane do implementowania interakcji, takich jak ustalenie, który obiekt jest wskazywany przez użytkownika.

Wszystkie zapytania przestrzenne są oceniane na serwerze. W związku z tym są to operacje asynchroniczne, a wyniki pojawią się z opóźnieniem, który zależy od opóźnienia sieci. Ponieważ każde zapytanie przestrzenne generuje ruch sieciowy, należy uważać, aby nie było zbyt wiele jednocześnie.

## <a name="collision-meshes"></a>Siatki kolizji

Zapytania przestrzenne są obsługiwane przez aparat [fizyki Havok](https://www.havok.com/products/havok-physics) i wymagają obecności dedykowanej siatki kolizji. Domyślnie [Konwersja modelu](../../how-tos/conversion/model-conversion.md) generuje siatki kolizji. Jeśli nie są wymagane zapytania przestrzenne w modelu złożonym, rozważ wyłączenie generowania siatki kolizji w [opcjach konwersji](../../how-tos/conversion/configure-model-conversion.md), ponieważ ma to wpływ na wiele sposobów:

* [Konwersja modelu](../../how-tos/conversion/model-conversion.md) będzie trwać znacznie dłużej.
* Rozmiary plików konwertowanego modelu są znacznie większe, co wpływa na szybkość pobierania.
* Czasy ładowania środowiska uruchomieniowego są dłuższe.
* Użycie pamięci procesora CPU przez środowisko uruchomieniowe jest wyższe.
* Dla każdego wystąpienia modelu istnieje niewielkie obciążenie wydajności środowiska uruchomieniowego.

## <a name="ray-casts"></a>Rzutowania promienia

*Rzutowanie promienia* to zapytanie przestrzenne, w którym środowisko uruchomieniowe sprawdza, które obiekty są przecinane przez promień, rozpoczynając od danego położenia i wskazując określony kierunek. W ramach optymalizacji zapewniona jest również Maksymalna odległość usługi ray, która nie wyszukuje obiektów, które są zbyt daleko.

```cs
async void CastRay(AzureSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastHit[] hits = await session.Actions.RayCastQueryAsync(rayCast).AsTask();

    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitObject;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
```

```cpp
void CastRay(ApiHandle<AzureSession> session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast;
    rayCast.StartPos = { 0, 0, 0 };
    rayCast.EndPos = { 0, 0, 1 };
    rayCast.MaxHits = 10;

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy::ClosestHit;

    ApiHandle<RaycastQueryAsync> castQuery = *session->Actions()->RayCastQueryAsync(rayCast);

    castQuery->Completed([](const ApiHandle<RaycastQueryAsync>& async)
        {
            std::vector<RayCastHit> hits;
            async->GetResult(hits);

            if (hits.size() > 0)
            {
                auto hitObject = hits[0].HitObject;
                auto hitPosition = hits[0].HitPosition;
                auto hitNormal = hits[0].HitNormal;

                // do something with the hit information
            }
        });
}
```


Istnieją trzy tryby kolekcji trafień:

* ** `Closest` :** W tym trybie będzie raportowany tylko najbliższy trafi.
* ** `Any` :** Preferuj ten tryb, gdy wszystko, co chcesz wiedzieć, jest to, *czy* promień uderzy w sobie, ale nie Zadbaj o to, co zostało natrafione dokładnie. To zapytanie może być znacznie tańsze do obliczenia, ale również ma tylko kilka aplikacji.
* ** `All` :** W tym trybie wszystkie trafienia na promieniu są raportowane według odległości. Nie używaj tego trybu, chyba że naprawdę potrzebujesz więcej niż pierwsze trafienie. Ogranicz liczbę raportowanych trafień za pomocą `MaxHits` opcji.

Aby wykluczyć obiekty selektywnie z uwzględnienia w przypadku rzutowania promieniowego, można użyć składnika [HierarchicalStateOverrideComponent](override-hierarchical-state.md) .

<!--
The CollisionMask allows the query to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>Wynik trafień

Wynik zapytania Cast Ray jest tablicą trafień. Tablica jest pusta, jeśli żaden obiekt nie został trafiony.

Trafienie ma następujące właściwości:

* ** `HitEntity` :** Który [obiekt](../../concepts/entities.md) został trafiony.
* ** `SubPartId` :** Która *Podsiatka* została trafiona w [MeshComponent](../../concepts/meshes.md). Może służyć do indeksowania `MeshComponent.UsedMaterials` i wyszukiwania [materiału](../../concepts/materials.md) w tym momencie.
* ** `HitPosition` :** Miejsce na świecie, w którym Ray przecina obiekt.
* ** `HitNormal` :** Powierzchnia świata jest normalna dla siatki w pozycji przecięcia.
* ** `DistanceToHit` :** Odległość od promień pozycji początkowej do trafienia.

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [Zdalnymanager. RayCastQueryAsync ()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.raycastqueryasync)
* [Zdalnymanager:: RayCastQueryAsync ()](/cpp/api/remote-rendering/remotemanager#raycastqueryasync)

## <a name="next-steps"></a>Następne kroki

* [Granice obiektów](../../concepts/object-bounds.md)
* [Zastępowanie Stanów hierarchicznych](override-hierarchical-state.md)