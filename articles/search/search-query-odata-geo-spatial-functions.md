---
title: Odwołanie do funkcji przestrzennej w postaci geograficznej OData
titleSuffix: Azure Cognitive Search
description: Dokumentacja składni i referencyjna dotycząca korzystania z funkcji geoprzestrzennych OData, lokalizacji geograficznej, odległości i lokalizacji geograficznej w usłudze Azure Wyszukiwanie poznawcze.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 902996c1813931638012c78f81bd65c400bee7a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74113164"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Funkcje geoprzestrzenne OData w systemie Azure Wyszukiwanie poznawcze `geo.distance` — i`geo.intersects`

Usługa Azure Wyszukiwanie poznawcze obsługuje zapytania przestrzenne geograficzne w [wyrażeniach filtru OData](query-odata-filter-orderby-syntax.md) za pośrednictwem funkcji `geo.distance` i `geo.intersects` . `geo.distance` Funkcja zwraca odległość w kilometrach między dwoma punktami, jedną jako zmienną pola lub zakres i jedną z nich jest stałą przekazaną w ramach filtra. `geo.intersects` Funkcja zwraca `true` , jeśli dany punkt znajduje się w obrębie danego wielokąta, gdzie punkt jest zmienną pola lub zakresu, a Wielokąt jest określony jako element stały przekazany jako część filtru.

`geo.distance` Funkcji można także użyć w [parametrze **$OrderBy** ](search-query-odata-orderby.md) , aby posortować wyniki wyszukiwania według odległości od danego punktu. Składnia dla `geo.distance` w **$OrderBy** jest taka sama jak w **$Filter**. W przypadku `geo.distance` korzystania z programu w **$OrderBy**pole, do którego ma zastosowanie, musi `Edm.GeographyPoint` być typu, a także do **sortowania**.

> [!NOTE]
> W przypadku `geo.distance` używania parametrów **$OrderBy** pole przekazywane do funkcji musi zawierać tylko jeden punkt geograficzny. Innymi słowy, musi być typu `Edm.GeographyPoint` i nie. `Collection(Edm.GeographyPoint)` Nie można sortować według pól kolekcji na platformie Azure Wyszukiwanie poznawcze.

## <a name="syntax"></a>Składnia

Następujący EBNF ([Extended back-Naura form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę `geo.distance` i funkcje oraz `geo.intersects` wartości przestrzenne, na których działają:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

Dostępny jest również interaktywny diagram składni:

> [!div class="nextstepaction"]
> [Diagram składni OData dla Wyszukiwanie poznawcze platformy Azure](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Zapoznaj się z informacjami o [składni wyrażenia OData dla usługi Azure wyszukiwanie poznawcze](search-query-odata-syntax-reference.md) , aby uzyskać pełną EBNF.

### <a name="geodistance"></a>Lokalizacja geograficzna

`geo.distance` Funkcja przyjmuje dwa parametry typu `Edm.GeographyPoint` i zwraca `Edm.Double` wartość, która jest odległości między nimi w kilometrach. Różni się to od innych usług, które obsługują operacje przestrzenne w geograficznie, które zwykle zwracają odległości w licznikach.

Jeden z parametrów `geo.distance` musi być stałą punktu geograficznego, a drugi musi być ścieżką pola (lub zmienną zakresu w przypadku filtrowania filtru dla pola typu `Collection(Edm.GeographyPoint)`). Kolejność tych parametrów nie ma znaczenia.

Stała punktu geograficznego ma postać `geography'POINT(<longitude> <latitude>)'`, gdzie długość geograficzna i Szerokość geograficzna są stałymi liczbowymi.

> [!NOTE]
> W przypadku `geo.distance` użycia w filtrze należy porównać odległość zwracaną przez funkcję ze stałą przy `lt`użyciu, `le`, `gt`, lub. `ge` Operatory `eq` i `ne` nie są obsługiwane w przypadku porównywania odległości. Na przykład jest to poprawne użycie programu `geo.distance`:. `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`

### <a name="geointersects"></a>geograficznie. intersects

`geo.intersects` `Edm.GeographyPoint` Funkcja przyjmuje zmienną typu i stałą `Edm.GeographyPolygon` i zwraca wartość `Edm.Boolean`  --  `true` , jeśli punkt znajduje się w granicach wielokąta, `false` w przeciwnym razie.

Wielokąt jest powierzchnią dwuwymiarową przechowywaną jako sekwencja punktów definiujących pierścień ograniczenia (Zobacz poniższe [przykłady](#examples) ). Wielokąt musi być zamknięty, co oznacza, że pierwszy i ostatni zestaw punktów muszą być takie same. [Punkty w wielokąta muszą znajdować się w porządku w lewo](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Zapytania przestrzenne geograficznie i wielokąty obejmujące 180th południka

W przypadku wielu geograficznie dowolnych bibliotek zapytań, które zawierają 180th południa (blisko linii zmiany), jest limitów lub wymaga obejścia, takiego jak dzielenie wielokąta na dwie, jeden po obu stronach południka.

W usłudze Azure Wyszukiwanie poznawcze zapytania przestrzenne, które zawierają 180-stopniowy rozmiar geograficzna, będą działały zgodnie z oczekiwaniami, jeśli kształt zapytania jest prostokątny, a współrzędne są wyrównane do układu siatki w `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`postaci długości i szerokości geograficznej (na przykład). W przeciwnym razie dla kształtów nieprostokątnych lub niewyrównanych Rozważmy podejście Split wielokąt.  

### <a name="geo-spatial-functions-and-null"></a>Funkcje przestrzenne i`null`

Podobnie jak wszystkie inne pola niebędące kolekcjami w usłudze Azure Wyszukiwanie poznawcze `Edm.GeographyPoint` , pola `null` typu mogą zawierać wartości. Gdy usługa Azure Wyszukiwanie poznawcze oblicza `geo.intersects` dla pola, które jest `null`, wynikiem będzie zawsze `false`. Zachowanie `geo.distance` w tym przypadku zależy od kontekstu:

- W filtrach `geo.distance` , `null` pole powoduje `null`. Oznacza to, że dokument będzie niezgodny `null` , ponieważ w porównaniu do dowolnej wartości innej niż null `false`jest obliczany.
- Podczas sortowania wyników przy **$orderby**użyciu $OrderBy `geo.distance` , `null` pole powoduje maksymalną możliwą odległość. Dokumenty z takimi polami będą sortowane mniej niż wszystkie inne osoby, gdy `asc` jest używany kierunek sortowania (wartość domyślna) i większe niż wszystkie inne, gdy kierunek `desc`jest.

## <a name="examples"></a>Przykłady

### <a name="filter-examples"></a>Przykłady filtrów

Znajdź wszystkie hotele w promieniu 10 kilometrów danego punktu odwołania (lokalizacja jest polem typu `Edm.GeographyPoint`):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Znajdź wszystkie hotele w obrębie danego okienka ekranu opisane jako wielokąt (lokalizacja jest polem typu `Edm.GeographyPoint`). Należy zauważyć, że Wielokąt jest zamknięty (pierwszy i ostatni zestaw punktów musi być taki sam), a [punkty muszą być wymienione w porządku w lewo](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Order — przykłady

Sortuj Hotele malejąco `rating`według, a następnie rosnąco według odległości od danego współrzędnych:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Sortuj Hotele w kolejności malejącej `search.score` według `rating`i, a następnie w kolejności rosnącej według odległości od podanych współrzędnych, tak aby między dwiema hoteli z identycznymi klasyfikacjami była wyświetlana pierwsza z nich:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Następne kroki  

- [Filtry na platformie Azure Wyszukiwanie poznawcze](search-filters.md)
- [Omówienie języka wyrażeń OData dla platformy Azure Wyszukiwanie poznawcze](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Wyszukiwanie poznawcze](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;interfejs API REST usługi Azure Wyszukiwanie poznawcze&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
