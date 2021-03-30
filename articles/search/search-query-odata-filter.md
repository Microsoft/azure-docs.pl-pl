---
title: Odwołanie filtru OData
titleSuffix: Azure Cognitive Search
description: Dokumentacja języka OData i pełna Składnia służąca do tworzenia wyrażeń filtrów w usłudze Azure Wyszukiwanie poznawcze zapytania.
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
ms.openlocfilehash: 0f33b5a28d7c83be7e546c3f61bc517047c51312
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88934858"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>Składnia $filter OData na platformie Azure Wyszukiwanie poznawcze

Usługa Azure Wyszukiwanie poznawcze używa [wyrażeń filtru OData](query-odata-filter-orderby-syntax.md) , aby zastosować dodatkowe kryteria do zapytania wyszukiwania oprócz terminów wyszukiwania pełnotekstowego. W tym artykule opisano szczegółowo składnię filtrów. Aby uzyskać ogólne informacje o filtrach i sposobach ich użycia do realizacji konkretnych scenariuszy zapytań, zobacz [filtry na platformie Azure wyszukiwanie poznawcze](search-filters.md).

## <a name="syntax"></a>Składnia

Filtr w języku OData jest wyrażeniem logicznym, które z kolei może być jednym z kilku typów wyrażeń, jak pokazano w poniższym EBNF ([formularz rozszerzony Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

Dostępny jest również interaktywny diagram składni:

> [!div class="nextstepaction"]
> [Diagram składni OData dla Wyszukiwanie poznawcze platformy Azure](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Zapoznaj się z informacjami o [składni wyrażenia OData dla usługi Azure wyszukiwanie poznawcze](search-query-odata-syntax-reference.md) , aby uzyskać pełną EBNF.

Typy wyrażeń logicznych obejmują:

- Wyrażenia filtru kolekcji używające `any` lub `all` . Zastosuj kryteria filtrowania do pól kolekcji. Aby uzyskać więcej informacji, zobacz [Operatory kolekcji OData na platformie Azure wyszukiwanie poznawcze](search-query-odata-collection-operators.md).
- Wyrażenia logiczne łączące inne wyrażenia logiczne przy użyciu operatorów `and` , `or` i `not` . Aby uzyskać więcej informacji, zobacz [logiczne operatory OData na platformie Azure wyszukiwanie poznawcze](search-query-odata-logical-operators.md).
- Wyrażenia porównania, które porównują pola lub zmienne zakresów do wartości stałych przy użyciu operatorów `eq` , `ne` ,,, `gt` `lt` `ge` i `le` . Aby uzyskać więcej informacji, zobacz [Operatory porównania OData na platformie Azure wyszukiwanie poznawcze](search-query-odata-comparison-operators.md). Wyrażenia porównania są również używane do porównywania odległości między współrzędnymi geograficznie przy użyciu `geo.distance` funkcji. Aby uzyskać więcej informacji, zobacz [funkcje geograficzne OData w usłudze Azure wyszukiwanie poznawcze](search-query-odata-geo-spatial-functions.md).
- Literały logiczne `true` i `false` . Te stałe mogą być przydatne czasami podczas programowego generowania filtrów, ale w przeciwnym razie nie są one używane w ćwiczeniach.
- Wywołania funkcji logicznych, w tym:
  - `geo.intersects`, który testuje, czy dany punkt znajduje się w obrębie danego wielokąta. Aby uzyskać więcej informacji, zobacz [funkcje geograficzne OData w usłudze Azure wyszukiwanie poznawcze](search-query-odata-geo-spatial-functions.md).
  - `search.in`, która porównuje zmienną pola lub zakresu z każdą wartością na liście wartości. Aby uzyskać więcej informacji, [Zobacz `search.in` Funkcja OData w usłudze Azure wyszukiwanie poznawcze](search-query-odata-search-in-function.md).
  - `search.ismatch` i `search.ismatchscoring` , które wykonują operacje wyszukiwania pełnotekstowego w kontekście filtru. Aby uzyskać więcej informacji, zobacz [funkcje wyszukiwania pełnotekstowego protokołu OData w usłudze Azure wyszukiwanie poznawcze](search-query-odata-full-text-search-functions.md).
- Ścieżki pól lub zmienne zakresowe typu `Edm.Boolean` . Na przykład jeśli indeks ma pole logiczne o nazwie `IsEnabled` i chcesz zwrócić wszystkie dokumenty, w których to pole jest `true` , wyrażenie filtru może być po prostu nazwą `IsEnabled` .
- Wyrażenia logiczne w nawiasach. Używanie nawiasów może pomóc jawnie określić kolejność operacji w filtrze. Aby uzyskać więcej informacji na temat domyślnego pierwszeństwa operatorów OData, zobacz następną sekcję.

### <a name="operator-precedence-in-filters"></a>Pierwszeństwo operatorów w filtrach

Jeśli piszesz wyrażenie filtru bez nawiasów wokół jego wyrażeń podrzędnych, usługa Azure Wyszukiwanie poznawcze obliczy go zgodnie z zestawem reguł pierwszeństwa operatora. Reguły te są oparte na tym, które operatory są używane do łączenia wyrażeń podrzędnych. Poniższa tabela zawiera listę grup operatorów w kolejności od najwyższego do najniższego pierwszeństwa:

| Group (Grupa) | Operatorzy: |
| --- | --- |
| Operatory logiczne | `not` |
| Operatory porównania | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Operatory logiczne | `and` |
| Operatory logiczne | `or` |

Operator, który jest wyższy w powyższej tabeli, zostanie "poddany bardziej ścisłie" do jego operandów niż inne operatory. Na przykład `and` ma wyższy priorytet niż `or` , a operatory porównania mają wyższy priorytet niż jeden z nich, dlatego następujące dwa wyrażenia są równoważne:

```odata-filter-expr
    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))
```

`not`Operator ma najwyższy priorytet wszystkich elementów — nawet wyższych niż operatory porównania. Dlatego jeśli próbujesz napisać filtr podobny do tego:

```odata-filter-expr
    not Rating gt 5
```

Zostanie wyświetlony następujący komunikat o błędzie:

```text
    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.
```

Ten błąd występuje, ponieważ operator jest skojarzony tylko z `Rating` polem, który jest typu `Edm.Int32` , a nie z całym wyrażeniem porównania. Poprawka polega na umieszczeniu operandu `not` w nawiasach:

```odata-filter-expr
    not (Rating gt 5)
```

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Ograniczenia rozmiaru filtru

Istnieją limity rozmiaru i złożoności wyrażeń filtru, które można wysłać do Wyszukiwanie poznawcze platformy Azure. Limity są oparte na liczbie klauzul w wyrażeniu filtru. Dobrym warunkiem jest to, że jeśli masz setki klauzul, masz ryzyko przekroczenia limitu. Zalecamy projektowanie aplikacji w taki sposób, aby nie generować filtrów o nieograniczonej wielkości.

> [!TIP]
> Użycie [ `search.in` funkcji](search-query-odata-search-in-function.md) zamiast długich rozgałęzień porównania równości może pomóc w uniknięciu ograniczenia klauzuli filtru, ponieważ wywołanie funkcji jest traktowane jako pojedyncza klauzula.

## <a name="examples"></a>Przykłady

Znajdź wszystkie hotele o co najmniej jednym pokoju z stawką bazową mniejszą niż $200, które są klasyfikowane na poziomie lub powyżej 4:

```odata-filter-expr
    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4
```

Znajdź wszystkie hotele inne niż "Sea View Motel", które zostały Renovated od 2010:

```odata-filter-expr
    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z
```

Znajdź wszystkie hotele, które zostały Renovated w 2010 lub nowszej. Literał DateTime zawiera informacje o strefie czasowej dla Pacyficzny czas standardowy:  

```odata-filter-expr
    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00
```

Znajdź wszystkie hotele, w których znajduje się parking i gdzie nie są naliczane wszystkie pokoje:

```odata-filter-expr
    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)
```

 \- Oraz  

```odata-filter-expr
    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)
```

Znajdź wszystkie hotele, które są możliwość zaprojektowaniae lub zawierają informacje o obparkowanie oraz o klasyfikacji 5:  

```odata-filter-expr
    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5
```

Znajdź wszystkie hotele ze znacznikiem "Wi-Fi" w co najmniej jednym pokoju (gdzie każde pomieszczenie ma Tagi przechowywane w `Collection(Edm.String)` polu):  

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))
```

Znajdź wszystkie hotele z dowolnymi pomieszczeń:  

```odata-filter-expr
    $filter=Rooms/any()
```

Znajdź wszystkie hotele, które nie mają pokojów:

```odata-filter-expr
    $filter=not Rooms/any()
```

Znajdź wszystkie hotele w ciągu 10 kilometrów danego punktu odwołania (gdzie `Location` jest polem typu `Edm.GeographyPoint` ):

```odata-filter-expr
    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10
```

Znajdź wszystkie hotele w ramach danego okienka ekranu opisane jako wielokąt (gdzie `Location` jest polem typu EDM. geographyPoint względem). Wielokąt musi być zamknięty, co oznacza, że pierwszy i ostatni zestaw punktów muszą być takie same. Ponadto [punkty muszą być wymienione w porządku w lewo](/rest/api/searchservice/supported-data-types#Anchor_1).

```odata-filter-expr
    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

Znajdź wszystkie hotele, w których pole "Description" ma wartość null. Pole będzie miało wartość null, jeśli nigdy nie zostało ustawione, lub jeśli zostało jawnie ustawione na wartość null:  

```odata-filter-expr
    $filter=Description eq null
```

Znajdź wszystkie hotele z nazwą równą "Sea View Motel" lub "hotelem". Te frazy zawierają spacje, a spacja jest domyślnym ogranicznikiem. Można określić alternatywny ogranicznik w pojedynczym cudzysłowie jako trzeci parametr ciągu:  

```odata-filter-expr
    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')
```

Znajdź wszystkie hotele z nazwą równą "Sea View Motel" lub "hotelem" oddzielonym przez "|"):  

```odata-filter-expr
    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')
```

Znajdź wszystkie hotele, w których wszystkie pokoje mają tag "Wi-Fi" lub "wanny":

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))
```

Znajdź dopasowanie do fraz w kolekcji, takich jak "ogrzewane Stojaki ręczników" lub "hairdryer dołączone" w tagach.

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))
```

Znajdź dokumenty z wyrazem "Waterfront". To zapytanie filtru jest identyczne z [żądaniem wyszukiwania](/rest/api/searchservice/search-documents) w `search=waterfront` .

```odata-filter-expr
    $filter=search.ismatchscoring('waterfront')
```

Znajdź dokumenty z wyrazem "Hostel" i klasyfikacją wyższą lub równą 4 lub dokumenty z wyrazem "Motel" i klasyfikacją równą 5. Nie można wyrazić tego żądania bez `search.ismatchscoring` funkcji, ponieważ łączy ono wyszukiwanie pełnotekstowe z operacjami filtrowania przy użyciu `or` .

```odata-filter-expr
    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5
```

Znajdź dokumenty bez słowa "możliwość zaprojektowania".

```odata-filter-expr
    $filter=not search.ismatch('luxury')
```

Znajdź dokumenty z frazą "widok oceanu" lub klasyfikacją równą 5. `search.ismatchscoring`Zapytanie zostanie wykonane tylko względem pól `HotelName` i `Description` . Dokumenty, które pasują tylko do drugiej klauzuli rozłączenia, zostaną zwrócone zbyt--Hotele z `Rating` równymi 5. Te dokumenty zostaną zwrócone z wynikiem równym zero, aby było jasne, że nie pasują do żadnej z ocenionych części wyrażenia.

```odata-filter-expr
    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5
```

Znajdź Hotele, w których terminy "Hotel" i "Lotnisko" nie są więcej niż pięć wyrazów w opisie i gdzie nie są naliczane wszystkie pokoje. To zapytanie używa [pełnego języka zapytań Lucene](query-lucene-syntax.md).

```odata-filter-expr
    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)
```

## <a name="next-steps"></a>Następne kroki  

- [Filtry na platformie Azure Wyszukiwanie poznawcze](search-filters.md)
- [Omówienie języka wyrażeń OData dla platformy Azure Wyszukiwanie poznawcze](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Wyszukiwanie poznawcze](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;interfejs API REST usługi Azure Wyszukiwanie poznawcze&#41;](/rest/api/searchservice/Search-Documents)