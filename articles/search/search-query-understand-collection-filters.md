---
title: Informacje o filtrach kolekcji OData
titleSuffix: Azure Cognitive Search
description: Dowiedz się, w jaki sposób filtry kolekcji OData działają w ramach zapytań Wyszukiwanie poznawcze platformy Azure, w tym ograniczeń i zachowań unikatowych dla kolekcji.
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
ms.openlocfilehash: f6e8ed5baef9b8594bb1fe03942e831fd8264a56
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74113073"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Informacje o filtrach kolekcji OData na platformie Azure Wyszukiwanie poznawcze

Aby [odfiltrować](query-odata-filter-orderby-syntax.md) pola kolekcji na platformie Azure wyszukiwanie poznawcze, można użyć [ `any` operatorów `all` i](search-query-odata-collection-operators.md) razem z **wyrażeniami lambda**. Wyrażenia lambda są wyrażeniami logicznymi odwołującymi się do **zmiennej zakresu**. Operatory `any` i `all` są analogiczne do `for` pętli w większości języków programowania, przy czym zmienna zakresu przyjmuje rolę zmiennej pętli i wyrażenie lambda jako treść pętli. Zmienna zakresu przyjmuje wartość "Current" kolekcji podczas iteracji pętli.

Co najmniej na to, jak działa koncepcyjnie. W rzeczywistości platforma Azure Wyszukiwanie poznawcze implementuje filtry w bardzo różny sposób, w jaki `for` działają pętle. W idealnym przypadku ta różnica byłaby niewidoczna dla Ciebie, ale w niektórych sytuacjach nie jest. Wynik końcowy polega na tym, że istnieją reguły, które należy wykonać podczas pisania wyrażeń lambda.

W tym artykule wyjaśniono, dlaczego istnieją reguły dla filtrów kolekcji, dzięki czemu usługa Azure Wyszukiwanie poznawcze wykonuje te filtry. Jeśli piszesz Zaawansowane filtry z złożonymi wyrażeniami lambda, ten artykuł może być przydatny podczas tworzenia informacji o tym, co można zrobić w filtrach i dlaczego.

Aby uzyskać informacje na temat tego, jakie są reguły dla filtrów kolekcji, łącznie z przykładami, zobacz [Rozwiązywanie problemów z filtrami kolekcji OData na platformie Azure wyszukiwanie poznawcze](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Dlaczego filtry kolekcji są ograniczone

Istnieją trzy podstawowe powody, dla których nie wszystkie funkcje filtru są obsługiwane dla wszystkich typów kolekcji:

1. Tylko niektóre operatory są obsługiwane dla określonych typów danych. Na przykład nie `true` ma sensu porównywania wartości logicznych i `false` używania `lt`, `gt`, i tak dalej.
1. Usługa Azure Wyszukiwanie poznawcze nie obsługuje **skorelowanego wyszukiwania** dla pól typu `Collection(Edm.ComplexType)`.
1. Usługa Azure Wyszukiwanie poznawcze używa odwróconych indeksów do wykonywania filtrów dla wszystkich typów danych, w tym kolekcji.

Pierwszym powodem jest to, że zdefiniowano język OData i system typów modelu EDM. Ostatnie dwa zostały omówione bardziej szczegółowo w dalszej części tego artykułu.

## <a name="correlated-versus-uncorrelated-search"></a>Skorelowane i nieskorelowane wyszukiwanie

W przypadku stosowania wielu kryteriów filtrowania względem kolekcji obiektów złożonych, kryteria są **skorelowane** , ponieważ dotyczą *poszczególnych obiektów w kolekcji*. Na przykład poniższy filtr zwróci Hotele, które mają co najmniej jeden pokój Deluxe o współczynniku mniejszym niż 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Jeśli filtrowanie zostało *nieskorelowane*, powyższy filtr może zwrócić Hotele, w których jedno pomieszczenie jest w trakcie Deluxe, a różne pomieszczenie ma stawkę bazową mniejszą niż 100. Nie byłoby to sensie, ponieważ obie klauzule wyrażenia lambda stosują się do tej samej zmiennej zakresu `room`, czyli. Dlatego takie filtry są skorelowane.

Jednak w przypadku wyszukiwania pełnotekstowego nie ma sposobu odwoływania się do określonej zmiennej zakresu. Jeśli używasz wyszukiwania z polami, aby wystawić [pełne zapytanie Lucene](query-lucene-syntax.md) podobne do tego:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

Możesz uzyskać Hotele w przypadku, gdy w jednym pokoju jest Deluxe, a w opisie znajduje się w innym pokoju. Na przykład dokument poniżej z `Id` `1` jest zgodny z kwerendą:

```json
{
  "value": [
    {
      "Id": "1",
      "Rooms": [
        { "Type": "deluxe", "Description": "Large garden view suite" },
        { "Type": "standard", "Description": "Standard city view room" }
      ]
    },
    {
      "Id": "2",
      "Rooms": [
        { "Type": "deluxe", "Description": "Courtyard motel room" }
      ]
    }
  ]
}
```

Przyczyną jest to, `Rooms/Type` że odnosi się do wszystkich przeanalizowanych `Rooms/Type` warunków pola w całym dokumencie i podobnie dla `Rooms/Description`, jak pokazano w poniższej tabeli.

Jak `Rooms/Type` są przechowywane na potrzeby wyszukiwania pełnotekstowego:

| Termin w`Rooms/Type` | Identyfikatory dokumentów |
| --- | --- |
| Deluxe | 1, 2 |
| Standardowa | 1 |

Jak `Rooms/Description` są przechowywane na potrzeby wyszukiwania pełnotekstowego:

| Termin w`Rooms/Description` | Identyfikatory dokumentów |
| --- | --- |
| courtyard | 2 |
| city | 1 |
| elementy | 1 |
| znacznie | 1 |
| Motel | 2 |
| pokój | 1, 2 |
| Standardowa | 1 |
| protokołów | 1 |
| widok | 1 |

W przeciwieństwie do powyższego filtru, który zasadniczo wskazuje na "dopasowanie dokumentów, `Type` gdy pomieszczenie jest równe" pokoju Deluxe "i **że to samo pomieszczenie** ma `BaseRate` mniej niż 100", zapytanie wyszukiwania ma postać " `Rooms/Type` dopasowuje dokumenty, w których termin `Rooms/Description` " Deluxe "i zawiera frazę" widok miejscowości ". Nie ma koncepcji pojedynczych pokojów, których pola mogą być skorelowane w tym ostatnim przypadku.

> [!NOTE]
> Jeśli chcesz uzyskać pomoc techniczną dla wyszukiwania skorelowanego dodanego do usługi Azure Wyszukiwanie poznawcze, zagłosuj na [ten element głosowy użytkownika](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Odwrócone indeksy i kolekcje

Być może zauważono, że istnieją znacznie mniej ograniczeń w wyrażeniach lambda w złożonych kolekcjach niż w przypadku prostych kolekcji `Collection(Edm.Int32)`, `Collection(Edm.GeographyPoint)`takich jak, i tak dalej. Wynika to z faktu, że platforma Azure Wyszukiwanie poznawcze przechowuje złożone kolekcje jako rzeczywiste kolekcje dokumentów podrzędnych, podczas gdy proste kolekcje nie są przechowywane jako kolekcje.

Rozważmy na przykład pole Kolekcja ciągów z możliwością filtrowania `seasons` , takie jak indeks dla sprzedawcy detalicznego w trybie online. Niektóre dokumenty przekazane do tego indeksu mogą wyglądać następująco:

```json
{
  "value": [
    {
      "id": "1",
      "name": "Hiking boots",
      "seasons": ["spring", "summer", "fall"]
    },
    {
      "id": "2",
      "name": "Rain jacket",
      "seasons": ["spring", "fall", "winter"]
    },
    {
      "id": "3",
      "name": "Parka",
      "seasons": ["winter"]
    }
  ]
}
```

Wartości `seasons` pola są przechowywane w strukturze o nazwie **odwrócony indeks**, który wygląda następująco:

| Termin | Identyfikatory dokumentów |
| --- | --- |
| Spring | 1, 2 |
| wakacj | 1 |
| znajduj | 1, 2 |
| zimow | 2, 3 |

Ta struktura danych została zaprojektowana tak, aby odpowiedzieć na jedno pytanie z dużą szybkością: w których dokumentach występuje dany termin? Odpowiedź na to pytanie działa podobnie jak zwykłe sprawdzanie równości niż pętla w kolekcji. W rzeczywistości jest to dlaczego dla kolekcji ciągów, platforma Azure Wyszukiwanie poznawcze zezwala `eq` tylko na operator porównania wewnątrz wyrażenia lambda dla. `any`

Po powyższym czasie kompilowania z poziomu równości zobaczymy, jak można połączyć wiele kontroli równości dla tej samej zmiennej zakresu z `or`. Działa to z algebry i rozdzielną [właściwością kwantyfikatorów](https://en.wikipedia.org/wiki/Existential_quantification#Negation). To wyrażenie:

    seasons/any(s: s eq 'winter' or s eq 'fall')

jest równoważne:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

i każde z dwóch `any` wyrażeń podrzędnych może być efektywnie wykonywane przy użyciu odwróconego indeksu. Ponadto, z podziękowaniami dla [kwantyfikatorów](https://en.wikipedia.org/wiki/Existential_quantification#Negation), to wyrażenie:

    seasons/all(s: s ne 'winter' and s ne 'fall')

jest równoważne:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

Dlatego jest możliwe korzystanie `all` z `ne` i. `and`

> [!NOTE]
> Chociaż szczegóły wykraczają poza zakres tego dokumentu, te same zasady obejmują również [testy odległości i przecięć dla kolekcji punktów geoprzestrzennych](search-query-odata-geo-spatial-functions.md) . To dlatego, w `any`:
>
> - `geo.intersects`nie może być Negacja
> - `geo.distance`należy porównać przy użyciu `lt` lub`le`
> - wyrażenia muszą być połączone z `or`, nie`and`
>
> Reguły reguł stosują się do `all`.

W przypadku filtrowania kolekcji typów danych, które `lt`obsługują operatory, `gt`, `le`i `ge` , takie jak `Collection(Edm.Int32)` na przykład, można używać szerszej różnorodności wyrażeń. `or` W odniesieniu do `and` programu można użyć, `any`jak również w programie, pod warunkiem, że bazowe wyrażenia porównania są `and`łączone do **porównywania zakresów** przy użyciu `or`, które następnie są później połączone przy użyciu. Ta struktura wyrażeń logicznych jest nazywana [disjunctive Normal form (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form), w przeciwnym razie znanej jako "ORs of ANDs". Odwrotnie, wyrażenia lambda `all` dla tych typów danych muszą być w [formacie CONJUNCTIVE Normal (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form), w przeciwnym razie "ANDs of ORs". Platforma Azure Wyszukiwanie poznawcze umożliwia porównywanie zakresów, ponieważ może je wykonywać przy użyciu odwróconych indeksów wydajnie, podobnie jak w przypadku szybkiego wyszukiwania ciągów.

Podsumowując, poniżej przedstawiono reguły przycisku przewijania dla elementów, które są dozwolone w wyrażeniu lambda:

- W `any`programie *testy pozytywne* są zawsze dozwolone, takie jak równość, porównywanie `geo.intersects`zakresów, `geo.distance` lub `lt` porównywane `le` z lub (np. "zamknięcia", jak równość, gdy chodzi o sprawdzanie odległości).
- Wewnątrz `any`, `or` jest zawsze dozwolone. Można używać `and` tylko dla typów danych, które mogą ekspresowych kontroli zakresu i tylko wtedy, gdy używasz ORs of ands (DNF).
- W programie reguły są odwrócone — dozwolone są tylko *kontrole ujemne* , których można używać `and` zawsze i można używać `or` tylko do sprawdzania zakresu, wyrażone jako ANDs of ORs (CNF). `all`

W rzeczywistości są to typy filtrów, których najprawdopodobniej będziesz używać. Nadal pomocne jest zrozumienie granic tego, co jest możliwe.

Aby zapoznać się z konkretnymi przykładami, które rodzaje filtrów są dozwolone i które nie są, zobacz [jak napisać prawidłowe filtry kolekcji](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>Następne kroki  

- [Rozwiązywanie problemów z filtrami kolekcji OData na platformie Azure Wyszukiwanie poznawcze](search-query-troubleshoot-collection-filters.md)
- [Filtry na platformie Azure Wyszukiwanie poznawcze](search-filters.md)
- [Omówienie języka wyrażeń OData dla platformy Azure Wyszukiwanie poznawcze](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Wyszukiwanie poznawcze](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;interfejs API REST usługi Azure Wyszukiwanie poznawcze&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
