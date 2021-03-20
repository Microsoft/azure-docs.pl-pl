---
title: Rozwiązywanie problemów z filtrami kolekcji OData
titleSuffix: Azure Cognitive Search
description: Zapoznaj się z podejściem do rozpoznawania błędów filtru kolekcji OData w zapytaniach usługi Azure Wyszukiwanie poznawcze.
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
ms.openlocfilehash: 3050f701c11773207aa6054d4d08d908d87b2ce7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88932070"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>Rozwiązywanie problemów z filtrami kolekcji OData na platformie Azure Wyszukiwanie poznawcze

Aby [odfiltrować](query-odata-filter-orderby-syntax.md) pola kolekcji na platformie Azure wyszukiwanie poznawcze, można użyć [ `any` `all` operatorów i](search-query-odata-collection-operators.md) razem z **wyrażeniami lambda**. Wyrażenie lambda jest filtrem podrzędnym, który jest stosowany do każdego elementu kolekcji.

W wyrażeniu lambda nie każda funkcja wyrażeń filtru jest dostępna. Dostępne funkcje różnią się w zależności od typu danych pola kolekcji, które chcesz filtrować. Może to spowodować wystąpienie błędu, jeśli spróbujesz użyć funkcji w wyrażeniu lambda, która nie jest obsługiwana w tym kontekście. Jeśli napotykasz takie błędy podczas próby zapisania złożonego filtru nad polami kolekcji, ten artykuł pomoże rozwiązać problem.

## <a name="common-collection-filter-errors"></a>Typowe błędy filtru kolekcji

Poniższa tabela zawiera listę błędów, które mogą wystąpić podczas próby wykonania filtru kolekcji. Te błędy występują w przypadku używania funkcji wyrażeń filtru, które nie są obsługiwane w wyrażeniu lambda. Każdy błąd zawiera pewne wskazówki dotyczące ponownego zapisywania filtru w celu uniknięcia błędu. Tabela zawiera również link do odpowiedniej sekcji tego artykułu, która zawiera więcej informacji na temat tego, jak uniknąć tego błędu.

| Komunikat o błędzie | Istniał | Aby uzyskać więcej informacji, zobacz |
| --- | --- | --- |
| Funkcja "IsMatch" nie ma parametrów powiązanych z zmienną zakresu ". W wyrażeniach lambda są obsługiwane tylko odwołania pola powiązanego ("any" lub "All"). Zmień filtr tak, aby funkcja "IsMatch" była spoza wyrażenia lambda, i spróbuj ponownie. | Używanie `search.ismatch` lub `search.ismatchscoring` wewnątrz wyrażenia lambda | [Reguły filtrowania złożonych kolekcji](#bkmk_complex) |
| Nieprawidłowe wyrażenie lambda. Znaleziono test pod kątem równości lub nierówności w przypadku, gdy w wyrażeniu lambda oczekiwano wartości przeciwnej, która wykonuje iterację na polu typu kolekcja (EDM. String). Dla elementu "any" Użyj wyrażeń w postaci "x EQ y" lub "search.in (...)". Dla elementu "All" Użyj wyrażeń w postaci "x ne y", "not (x EQ y)" lub "not search.in (...)". | Filtrowanie według pola typu `Collection(Edm.String)` | [Reguły filtrowania kolekcji ciągów](#bkmk_strings) |
| Nieprawidłowe wyrażenie lambda. Znaleziono nieobsługiwaną formę złożonego wyrażenia logicznego. Dla elementu "any" Użyj wyrażeń, które są "ORs of ANDs", znanego również jako disjunctive Normal form. Na przykład: "(a i b) lub (c i d)" gdzie a, b, c i d są porównywane lub podwyrażeniem równości. Dla elementu "All" należy użyć wyrażeń, które są "ANDs of ORs", znane także jako conjunctive Normal form. Na przykład: "(a lub b) i (c lub d)", gdzie a, b, c i d są porównaniem lub nierównym wyrażeniami podrzędnymi. Przykłady wyrażeń porównania: "x gt 5", "x Le 2". Przykład wyrażenia równości: "x EQ 5". Przykład wyrażenia nierówności: "x ne 5". | Filtrowanie pól typu `Collection(Edm.DateTimeOffset)` , `Collection(Edm.Double)` , `Collection(Edm.Int32)` , lub `Collection(Edm.Int64)` | [Reguły filtrowania porównywalnych kolekcji](#bkmk_comparables) |
| Nieprawidłowe wyrażenie lambda. Znaleziono nieobsługiwane użycie lokalizacji geograficznej. Distance () lub lokalizacji geograficznej () w wyrażeniu lambda, które wykonuje iterację na polu typu kolekcja (EDM. geographyPoint względem). W przypadku elementu "any" Upewnij się, że porównujesz lokalizację Geo. Distance () przy użyciu operatorów "lt" lub "Le" i upewnij się, że żadne użycie elementu Geo. intersects () nie jest negacją. W przypadku elementu "All" Upewnij się, że porównujesz lokalizację geograficzną. Distance () przy użyciu operatorów "gt" lub "GE" i upewnij się, że dowolne użycie elementu Geo. intersects () jest negacją. | Filtrowanie według pola typu `Collection(Edm.GeographyPoint)` | [Reguły filtrowania kolekcji geographyPoint względem](#bkmk_geopoints) |
| Nieprawidłowe wyrażenie lambda. Złożone wyrażenia logiczne nie są obsługiwane w wyrażeniach lambda, które przechodzą na pola typu kolekcja (EDM. geographyPoint względem). W przypadku elementu "any" Połącz wyrażenia podrzędne z elementem "or"; "i" nie są obsługiwane. Dla elementu "All" Dołącz Podwyrażenie do wyrażenia "i"; element "or" nie jest obsługiwany. | Filtrowanie pól typu `Collection(Edm.String)` lub `Collection(Edm.GeographyPoint)` | [Reguły filtrowania kolekcji ciągów](#bkmk_strings) <br/><br/> [Reguły filtrowania kolekcji geographyPoint względem](#bkmk_geopoints) |
| Nieprawidłowe wyrażenie lambda. Znaleziono operator porównania (jeden z "lt", "Le", "gt" lub "GE"). Tylko operatory równości są dozwolone w wyrażeniach lambda, które przechodzą na pola typu kolekcja (EDM. String). Dla elementu "any" Użyj wyrażeń w postaci "x EQ y". Dla elementu "All" Użyj wyrażeń w postaci "x ne y" lub "not (x EQ y)". | Filtrowanie według pola typu `Collection(Edm.String)` | [Reguły filtrowania kolekcji ciągów](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Pisanie prawidłowych filtrów kolekcji

Reguły zapisywania prawidłowych filtrów kolekcji różnią się w zależności od typu danych. W poniższych sekcjach opisano reguły, pokazując Przykłady obsługiwanych funkcji filtru, które nie są:

- [Reguły filtrowania kolekcji ciągów](#bkmk_strings)
- [Reguły filtrowania kolekcji logicznych](#bkmk_bools)
- [Reguły filtrowania kolekcji geographyPoint względem](#bkmk_geopoints)
- [Reguły filtrowania porównywalnych kolekcji](#bkmk_comparables)
- [Reguły filtrowania złożonych kolekcji](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Reguły filtrowania kolekcji ciągów

Wewnątrz wyrażeń lambda dla kolekcji ciągów jedynymi operatorami porównania, których można użyć, są `eq` i `ne` .

> [!NOTE]
> Usługa Azure wyszukiwanie poznawcze nie obsługuje `lt` / `le` / `gt` / `ge` operatorów dla ciągów, niezależnie od tego, czy znajduje się w wyrażeniu lambda, czy poza nim.

Treść elementu `any` może przetestować pod kątem równości, podczas gdy treść elementu `all` może zostać przetestowana tylko pod kątem nierówności.

Istnieje również możliwość łączenia wielu wyrażeń za pośrednictwem `or` treści i w treści `any` `and` `all` . Ponieważ `search.in` Funkcja jest równoważna łączeniu kontroli równości z `or` , jest również dozwolona w treści `any` . Z drugiej `not search.in` strony, jest dozwolony w treści `all` .

Na przykład te wyrażenia są dozwolone:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

Chociaż te wyrażenia nie są dozwolone:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>Reguły filtrowania kolekcji logicznych

Typ `Edm.Boolean` obsługuje tylko `eq` `ne` Operatory i. W związku z tym nie ma sensu, aby można było łączyć takie klauzule, które sprawdzają tę samą zmienną zakresu, `and` / `or` ponieważ zawsze doprowadziłoby do tautologies lub sprzeczności.

Poniżej przedstawiono kilka przykładów filtrów na kolekcjach logicznych, które są dozwolone:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

W przeciwieństwie do kolekcji ciągów, kolekcje logiczne nie mają limitów, dla których operatora można używać w typie wyrażenia lambda. Oba `eq` i `ne` mogą być używane w treści `any` lub `all` .

Wyrażenia takie jak następujące są niedozwolone w przypadku kolekcji logicznych:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>Reguły filtrowania kolekcji geographyPoint względem

Wartości typu `Edm.GeographyPoint` w kolekcji nie mogą być porównywane bezpośrednio ze sobą. Zamiast tego należy je stosować jako parametry do `geo.distance` `geo.intersects` funkcji i. `geo.distance`Funkcja z kolei musi być porównywana z wartością odległości przy użyciu jednego z operatorów porównania `lt` , `le` , `gt` , lub `ge` . Te reguły mają zastosowanie również do pól nie będących kolekcją EDM. geographyPoint względem.

Podobnie jak kolekcje ciągów, `Edm.GeographyPoint` kolekcje mają pewne reguły umożliwiające używanie funkcji geoprzestrzennych i łączenie ich w różnych typach wyrażeń lambda:

- Które operatory porównania, których można używać z `geo.distance` funkcją, są zależne od typu wyrażenia lambda. W przypadku programu `any` można używać tylko `lt` lub `le` . W przypadku programu `all` można używać tylko `gt` lub `ge` . Można Negate wyrażenia obejmujące `geo.distance` , ale trzeba zmienić operator porównania ( `geo.distance(...) lt x` stanie się `not (geo.distance(...) ge x)` i `geo.distance(...) le x` stanie się `not (geo.distance(...) gt x)` ).
- W treści elementu `all` , `geo.intersects` Funkcja musi być Negacja. Z drugiej strony, w treści elementu `any` , `geo.intersects` Funkcja nie może być negacją.
- W treści `any` wyrażeń geoprzestrzennych można łączyć za pomocą `or` . W treści tego `all` wyrażenia można łączyć za pomocą `and` .

Powyższe ograniczenia istnieją z przyczyn podobnych, jak ograniczenie równości/nierówności dla kolekcji ciągów. Zobacz [Omówienie filtrów kolekcji OData na platformie Azure wyszukiwanie poznawcze](search-query-understand-collection-filters.md) , aby uzyskać bardziej szczegółowy wgląd w te przyczyny.

Poniżej przedstawiono kilka przykładów filtrów dla `Edm.GeographyPoint` kolekcji, które są dozwolone:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

Wyrażenia takie jak następujące są niedozwolone w przypadku `Edm.GeographyPoint` kolekcji:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Reguły filtrowania porównywalnych kolekcji

Ta sekcja ma zastosowanie do wszystkich następujących typów danych:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

Typy takie jak `Edm.Int32` i `Edm.DateTimeOffset` obsługują wszystkie sześć operatorów porównania: `eq` ,,, `ne` , `lt` `le` `gt` i `ge` . Wyrażenia lambda w kolekcjach tych typów mogą zawierać proste wyrażenia używające któregokolwiek z tych operatorów. Dotyczy to zarówno programu `any` , jak i `all` . Na przykład następujące filtry są dozwolone:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

Istnieją jednak ograniczenia dotyczące sposobu łączenia takich wyrażeń porównania z bardziej złożonymi wyrażeniami wewnątrz wyrażenia lambda:

- Reguły dla `any` :
  - Proste wyrażenia nierówności nie mogą być przydatne w połączeniu z innymi wyrażeniami. Na przykład to wyrażenie jest dozwolone:
    - `ratings/any(r: r ne 5)`

    ale to wyrażenie nie jest:
    - `ratings/any(r: r ne 5 and r gt 2)`

    Chociaż to wyrażenie jest dozwolone, nie jest przydatne, ponieważ warunki nakładają się na siebie:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Proste wyrażenia porównania obejmujące `eq` , `lt` , `le` , `gt` lub `ge` mogą być łączone z `and` / `or` . Na przykład:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - Wyrażenia porównania połączone z `and` (połączeniami) można również łączyć za pomocą polecenia `or` . Ten formularz jest znany w logice logicznej jako "[disjunctive Normal](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF). Na przykład:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- Reguły dla `all` :
  - Proste wyrażenia równości nie mogą być przydatne w połączeniu z innymi wyrażeniami. Na przykład to wyrażenie jest dozwolone:
    - `ratings/all(r: r eq 5)`

    ale to wyrażenie nie jest:
    - `ratings/all(r: r eq 5 or r le 2)`

    Chociaż to wyrażenie jest dozwolone, nie jest przydatne, ponieważ warunki nakładają się na siebie:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Proste wyrażenia porównania obejmujące `ne` , `lt` , `le` , `gt` lub `ge` mogą być łączone z `and` / `or` . Na przykład:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - Wyrażenia porównania połączone z (rozłączenia `or` ) można również łączyć za pomocą polecenia `and` . Ten formularz jest znany w logice logicznej jako "[conjunctive Normal](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (CNF). Na przykład:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Reguły filtrowania złożonych kolekcji

Wyrażenia lambda w złożonej kolekcji obsługują znacznie bardziej elastyczną składnię niż wyrażenia lambda na kolekcjach typów pierwotnych. Można użyć dowolnej konstrukcji filtru w tym wyrażeniu lambda, która może być używana poza jednym, z tylko dwoma wyjątkami.

Najpierw funkcje `search.ismatch` i `search.ismatchscoring` nie są obsługiwane w wyrażeniach lambda. Aby uzyskać więcej informacji, zobacz [Omówienie filtrów kolekcji OData w usłudze Azure wyszukiwanie poznawcze](search-query-understand-collection-filters.md).

Drugie, odwołujące się do pól, które nie są *powiązane* z zmienną zakresu (tak jak *zmienne wolne*), nie są dozwolone. Rozważmy na przykład następujące dwa równoważne wyrażenia filtru OData:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

Pierwsze wyrażenie będzie dozwolone, podczas gdy drugi formularz zostanie odrzucony, ponieważ `details/margin` nie jest powiązany z zmienną zakresu `s` .

Ta reguła rozszerza również do wyrażeń, które mają zmienne powiązane w zewnętrznym zakresie. Takie zmienne są wolne w odniesieniu do zakresu, w którym się znajdują. Na przykład pierwsze wyrażenie jest dozwolone, podczas gdy drugie równoważne wyrażenie nie jest dozwolone, ponieważ `s/name` jest wolne w odniesieniu do zakresu zmiennej zakresu `a` :

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

To ograniczenie nie powinno stanowić problemu w ćwiczeń, ponieważ zawsze jest możliwe konstruowanie filtrów, takich jak wyrażenia lambda, które zawierają tylko zmienne powiązane.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Arkusz Ściągawka dla reguł filtru kolekcji

Poniższa tabela zawiera podsumowanie reguł tworzenia prawidłowych filtrów dla każdego typu danych kolekcji.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Aby zapoznać się z przykładami tworzenia prawidłowych filtrów dla każdego przypadku, zobacz [jak napisać prawidłowe filtry kolekcji](#bkmk_examples).

Jeśli często zapisujesz filtry, a zrozumienie zasad z pierwszych zasad pomoże Ci więcej niż memorizing je, zobacz temat [Omówienie filtrów kolekcji OData na platformie Azure wyszukiwanie poznawcze](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Następne kroki  

- [Informacje o filtrach kolekcji OData na platformie Azure Wyszukiwanie poznawcze](search-query-understand-collection-filters.md)
- [Filtry na platformie Azure Wyszukiwanie poznawcze](search-filters.md)
- [Omówienie języka wyrażeń OData dla platformy Azure Wyszukiwanie poznawcze](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Wyszukiwanie poznawcze](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;interfejs API REST usługi Azure Wyszukiwanie poznawcze&#41;](/rest/api/searchservice/Search-Documents)