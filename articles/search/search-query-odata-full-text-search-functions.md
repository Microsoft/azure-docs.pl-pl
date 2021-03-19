---
title: Dokumentacja funkcji wyszukiwania pełnotekstowego OData
titleSuffix: Azure Cognitive Search
description: Funkcje wyszukiwania pełnotekstowego OData, Search. IsMatch i Search. ismatchscoring w usłudze Azure Wyszukiwanie poznawcze zapytania.
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
ms.openlocfilehash: 78f9e4d8fa80fdf74bdb5cd79f4489d12696fcc2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88935793"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>Funkcja wyszukiwania pełnotekstowego OData w usłudze Azure Wyszukiwanie poznawcze — `search.ismatch` i `search.ismatchscoring`

Usługa Azure Wyszukiwanie poznawcze obsługuje wyszukiwanie pełnotekstowe w kontekście [wyrażeń filtru OData](query-odata-filter-orderby-syntax.md) za pośrednictwem `search.ismatch` `search.ismatchscoring` funkcji i. Te funkcje umożliwiają łączenie wyszukiwania pełnotekstowego z ścisłym filtrowaniem logicznym w sposób, który nie jest możliwy tylko przy użyciu parametru najwyższego poziomu `search` [interfejsu API wyszukiwania](/rest/api/searchservice/search-documents).

> [!NOTE]
> `search.ismatch`Funkcje i `search.ismatchscoring` są obsługiwane tylko w filtrach w [interfejsie API wyszukiwania](/rest/api/searchservice/search-documents). Nie są one obsługiwane w interfejsach API [Sugeruj](/rest/api/searchservice/suggestions) lub [Autouzupełnianie](/rest/api/searchservice/autocomplete) .

## <a name="syntax"></a>Składnia

Następujący EBNF ([formularz rozszerzony Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę `search.ismatch` `search.ismatchscoring` funkcji and:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Dostępny jest również interaktywny diagram składni:

> [!div class="nextstepaction"]
> [Diagram składni OData dla Wyszukiwanie poznawcze platformy Azure](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Zapoznaj się z informacjami o [składni wyrażenia OData dla usługi Azure wyszukiwanie poznawcze](search-query-odata-syntax-reference.md) , aby uzyskać pełną EBNF.

### <a name="searchismatch"></a>Wyszukaj. IsMatch

`search.ismatch`Funkcja oblicza zapytanie wyszukiwania pełnotekstowego jako część wyrażenia filtru. Dokumenty, które pasują do zapytania wyszukiwania, zostaną zwrócone w zestawie wyników. Dostępne są następujące przeciążenia tej funkcji:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Parametry są zdefiniowane w poniższej tabeli:

| Nazwa parametru | Typ | Opis |
| --- | --- | --- |
| `search` | `Edm.String` | Zapytanie wyszukiwania (w [prostej](query-simple-syntax.md) lub [pełnej](query-lucene-syntax.md) składni zapytań Lucene). |
| `searchFields` | `Edm.String` | Rozdzielana przecinkami lista pól wyszukiwania do wyszukania; Domyślnie wszystkie pola z możliwością wyszukiwania w indeksie. W przypadku używania [wyszukiwania z polami](query-lucene-syntax.md#bkmk_fields) w `search` parametrze specyfikatory pola w zapytaniu Lucene zastępują wszystkie pola określone w tym parametrze. |
| `queryType` | `Edm.String` | `'simple'` lub `'full'` ; wartość domyślna to `'simple'` . Określa, jaki język zapytań był używany w `search` parametrze. |
| `searchMode` | `Edm.String` | `'any'` lub `'all'` , wartość domyślna to `'any'` . Wskazuje, czy wszystkie lub wszystkie terminy wyszukiwania w `search` parametrze muszą być dopasowane w celu zliczenia dokumentu jako zgodnego. W przypadku korzystania z [operatorów logicznych "Lucene](query-lucene-syntax.md#bkmk_boolean) " w `search` parametrze będą one miały pierwszeństwo przed tym parametrem. |

Wszystkie powyższe parametry są równoważne z odpowiednimi [parametrami żądania wyszukiwania w interfejsie API wyszukiwania](/rest/api/searchservice/search-documents).

`search.ismatch`Funkcja zwraca wartość typu `Edm.Boolean` , która pozwala na redagowanie jej z innymi wyrażeniami podrzędnymi filtru przy użyciu logicznych [operatorów logicznych](search-query-odata-logical-operators.md).

> [!NOTE]
> Usługa Azure Wyszukiwanie poznawcze nie obsługuje używania `search.ismatch` `search.ismatchscoring` wyrażeń lambda ani wewnątrz nich. Oznacza to, że nie jest możliwe zapisanie filtrów za pomocą kolekcji obiektów, które mogą skorelować wyniki wyszukiwania pełnotekstowego przy użyciu ścisłych dopasowania filtru dla tego samego obiektu. Aby uzyskać więcej informacji na temat tego ograniczenia, jak również przykładów, zobacz [Rozwiązywanie problemów z filtrami kolekcji na platformie Azure wyszukiwanie poznawcze](search-query-troubleshoot-collection-filters.md). Aby uzyskać bardziej szczegółowe informacje na temat tego ograniczenia, zobacz [Omówienie filtrów kolekcji na platformie Azure wyszukiwanie poznawcze](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>Search. ismatchscoring

`search.ismatchscoring`Funkcja, podobnie jak `search.ismatch` Funkcja, zwraca `true` dla dokumentów, które pasują do zapytania wyszukiwania pełnotekstowego przesłanego jako parametr. Różnica między nimi polega na tym, że Ocena istotności dokumentów pasujących do `search.ismatchscoring` zapytania będzie współtworzyć ogólny wynik dokumentu, a w przypadku `search.ismatch` nie zostanie zmieniony wynik oceny dokumentu. Następujące przeciążenia tej funkcji są dostępne z parametrami takimi jak `search.ismatch` :

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Obie `search.ismatch` funkcje i `search.ismatchscoring` mogą być używane w tym samym wyrażeniu filtru.

## <a name="examples"></a>Przykłady

Znajdź dokumenty z wyrazem "Waterfront". To zapytanie filtru jest identyczne z [żądaniem wyszukiwania](/rest/api/searchservice/search-documents) w `search=waterfront` .

```odata-filter-expr
    search.ismatchscoring('waterfront')
```

Znajdź dokumenty z wyrazem "Hostel" i klasyfikacją wyższą lub równą 4 lub dokumenty z wyrazem "Motel" i klasyfikacją równą 5. Należy zauważyć, że to żądanie nie mogło być wyrażone bez `search.ismatchscoring` funkcji.

```odata-filter-expr
    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5
```

Znajdź dokumenty bez słowa "możliwość zaprojektowania".

```odata-filter-expr
    not search.ismatch('luxury')
```

Znajdź dokumenty z frazą "widok oceanu" lub klasyfikacją równą 5. `search.ismatchscoring`Zapytanie zostanie wykonane tylko względem pól `HotelName` i `Rooms/Description` .

Dokumenty, które pasują tylko do drugiej klauzuli rozłączenia, zostaną zwrócone zbyt--Hotele z `Rating` równymi 5. Aby jasno wymusić, że te dokumenty nie pasują do żadnej z ocenionych części wyrażenia, zostaną zwrócone z wynikiem równym zero.

```odata-filter-expr
    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5
```

Znajdź dokumenty, w których terminy "Hotel" i "Lotnisko" znajdują się w 5 wyrazach od siebie w opisie hotelu i gdzie palenie nie jest dozwolone w co najmniej niektórych pokojach. To zapytanie używa [pełnego języka zapytań Lucene](query-lucene-syntax.md).

```odata-filter-expr
    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)
```

## <a name="next-steps"></a>Następne kroki  

- [Filtry na platformie Azure Wyszukiwanie poznawcze](search-filters.md)
- [Omówienie języka wyrażeń OData dla platformy Azure Wyszukiwanie poznawcze](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Wyszukiwanie poznawcze](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;interfejs API REST usługi Azure Wyszukiwanie poznawcze&#41;](/rest/api/searchservice/Search-Documents)