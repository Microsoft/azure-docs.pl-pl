---
title: Odwołanie operatora logicznego OData
titleSuffix: Azure Cognitive Search
description: Składnia i dokumentacja referencyjna dotycząca korzystania z operatorów logicznych OData, oraz, lub, a nie w przypadku zapytań usługi Azure Wyszukiwanie poznawcze.
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
ms.openlocfilehash: 27d5427d34de591f9cfeab2310d79a2fde217624
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88917877"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Operatory logiczne OData w usłudze Azure Wyszukiwanie poznawcze —, `and` `or` , `not`

[Wyrażenia filtru OData](query-odata-filter-orderby-syntax.md) na platformie Azure wyszukiwanie poznawcze są wyrażeniami logicznymi, które są szacowane do `true` lub `false` . Filtr złożony można napisać, pisząc szereg [prostszych filtrów](search-query-odata-comparison-operators.md) i tworząc je przy użyciu operatorów logicznych z [algebry logicznego](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: Operator binarny, który jest obliczany w `true` przypadku, gdy oba wyrażenia podrzędne i po prawej stronie są oceniane na `true` .
- `or`: Operator binarny, który jest obliczany w `true` przypadku, gdy jeden z jego lewy lub prawy Podwyrażenie ma wartość `true` .
- `not`: Jednoargumentowy operator, który jest obliczany w `true` przypadku, gdy jego Podwyrażenie ma wartość `false` , i na odwrót.

Te, razem z [operatorami kolekcji `any` i `all` ](search-query-odata-collection-operators.md), umożliwiają konstruowanie filtrów, które mogą wyrażać bardzo złożone kryteria wyszukiwania.

## <a name="syntax"></a>Składnia

Następujący EBNF ([formularz rozszerzony Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę wyrażenia OData, które używa operatorów logicznych.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Dostępny jest również interaktywny diagram składni:

> [!div class="nextstepaction"]
> [Diagram składni OData dla Wyszukiwanie poznawcze platformy Azure](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Zapoznaj się z informacjami o [składni wyrażenia OData dla usługi Azure wyszukiwanie poznawcze](search-query-odata-syntax-reference.md) , aby uzyskać pełną EBNF.

Istnieją dwie formy wyrażeń logicznych: binary ( `and` / `or` ), gdzie istnieją dwa wyrażenia podrzędne i jednoargumentowy ( `not` ), gdzie istnieje tylko jeden. Wyrażenia podrzędne mogą być wyrażeniami logicznymi dowolnego rodzaju:

- Pola lub zmienne zakresu typu `Edm.Boolean`
- Funkcje, które zwracają wartości typu `Edm.Boolean` , takie jak `geo.intersects` lub `search.ismatch`
- [Wyrażenia porównania](search-query-odata-comparison-operators.md), takie jak `rating gt 4`
- [Wyrażenia kolekcji](search-query-odata-collection-operators.md), takie jak `Rooms/any(room: room/Type eq 'Deluxe Room')`
- Literały logiczne `true` lub `false` .
- Inne wyrażenia logiczne konstruowane przy użyciu `and` , `or` , i `not` .

> [!IMPORTANT]
> Istnieją sytuacje, w których nie można używać wszystkich rodzajów wyrażeń podrzędnych z `and` / `or` , szczególnie w wyrażeniach lambda. Aby uzyskać szczegółowe informacje, zobacz [Operatory kolekcji OData w usłudze Azure wyszukiwanie poznawcze](search-query-odata-collection-operators.md#limitations) .

### <a name="logical-operators-and-null"></a>Operatory logiczne i `null`

Większość wyrażeń logicznych, takich jak Functions i porównania, nie może generować `null` wartości, a operatory logiczne nie mogą być stosowane do `null` literału bezpośrednio (na przykład `x and null` nie jest to dozwolone). Jednak pola logiczne mogą być `null` , dlatego należy wiedzieć, jak `and` `or` operatory, i `not` działają w obecności wartości null. Jest to podsumowanie w poniższej tabeli, gdzie `b` to pole typu `Edm.Boolean` :

| Wyrażenie | Wynik gdy `b` jest `null` |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

Gdy pole logiczne `b` jest wyświetlane przez siebie w wyrażeniu filtru, zachowuje się tak, jakby zostało zapisaniu `b eq true` , więc jeśli `b` jest `null` , wyrażenie daje `false` w wyniku. Podobnie, `not b` zachowuje się jak `not (b eq true)` , dlatego jest oceniane `true` . W ten sposób `null` pola zachowują się tak samo jak `false` . Jest to zgodne z zachowaniem ich w połączeniu z innymi wyrażeniami przy użyciu `and` i `or` , jak pokazano w powyższej tabeli. Pomimo tego, bezpośrednie porównanie z `false` ( `b eq false` ) będzie nadal oceniane `false` . Innymi słowy, `null` nie jest równe, chociaż `false` zachowuje się tak jak w wyrażeniach logicznych.

## <a name="examples"></a>Przykłady

Dopasowuje dokumenty `rating` , gdy pole jest z zakresu od 3 do 5 włącznie:

```odata-filter-expr
    rating ge 3 and rating le 5
```

Dopasowuje dokumenty, w których wszystkie elementy `ratings` pola są mniejsze niż 3 lub większe niż 5:

```odata-filter-expr
    ratings/all(r: r lt 3 or r gt 5)
```

Dopasowuje dokumenty `location` , w których pole znajduje się w danym wielokąta, a dokument nie zawiera terminu "Public".

```odata-filter-expr
    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')
```

Dopasowuj dokumenty dla hoteli w Vancouver, Kanada, gdy istnieje Pokój Deluxe z stawką bazową mniejszą niż 160:

```odata-filter-expr
    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)
```

## <a name="next-steps"></a>Następne kroki  

- [Filtry na platformie Azure Wyszukiwanie poznawcze](search-filters.md)
- [Omówienie języka wyrażeń OData dla platformy Azure Wyszukiwanie poznawcze](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Wyszukiwanie poznawcze](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;interfejs API REST usługi Azure Wyszukiwanie poznawcze&#41;](/rest/api/searchservice/Search-Documents)