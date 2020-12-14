---
title: Użyj pełnej składni zapytań Lucene
titleSuffix: Azure Cognitive Search
description: Składnia zapytań Lucene dla wyszukiwania rozmytego, wyszukiwania w sąsiedztwie, zwiększania warunków, wyszukiwania wyrażeń regularnych i wyszukiwania symboli wieloznacznych w usłudze Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: df26cfc3b220f40a7e73ff1c750d2b2ae37e7625
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401461"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Użyj "pełnej" składni wyszukiwania Lucene (zapytania zaawansowane w usłudze Azure Wyszukiwanie poznawcze)

Podczas konstruowania zapytań dotyczących usługi Azure Wyszukiwanie poznawcze można zastąpić domyślny [prosty Analizator zapytań](query-simple-syntax.md) , używając bardziej zaawansowanego [analizatora zapytań Lucene na platformie Azure wyszukiwanie poznawcze](query-lucene-syntax.md) , aby tworzyć wyspecjalizowane i zaawansowane definicje zapytań. 

Parser Lucene obsługuje złożone konstrukcje zapytań, takie jak zapytania o zakresie pól, Wyszukiwanie rozmyte, wrostkowe i symboli wieloznacznych, wyszukiwanie w sąsiedztwie, zwiększanie terminów i wyszukiwanie wyrażeń regularnych. Dodatkowa moc jest dostarczana z dodatkowymi wymaganiami dotyczącymi przetwarzania, dlatego należy oczekiwać nieco dłuższego czasu wykonania. W tym artykule można zapoznać się z przykładami pokazującymi operacje zapytań na podstawie pełnej składni.

> [!Note]
> Wiele wyspecjalizowanych konstrukcji zapytań włączonych za pomocą pełnej składni zapytań Lucene nie jest w trakcie [analizowania tekstu](search-lucene-query-architecture.md#stage-2-lexical-analysis), co może być zaskakującee, jeśli oczekujesz rdzeni lub Lematyzacja. Analiza leksykalna jest wykonywana tylko w przypadku pełnych warunków (zapytania warunkowego zapytania lub frazy). Typy zapytań z niekompletnymi postanowieniami (zapytanie o prefiks, zapytanie symboli wieloznacznych, zapytanie rozmyte) są dodawane bezpośrednio do drzewa zapytań, pomijając etap analizy. Jedyne przekształcenie wykonywane na częściowych terminach zapytania to lowercasing. 
>

## <a name="nyc-jobs-examples"></a>Przykłady zadań NYC

Poniższe przykłady wykorzystują [indeks wyszukiwania zadań NYC](https://azjobsdemo.azurewebsites.net/)  składający się z zadań dostępnych na podstawie zestawu danych dostarczonego przez [miasto z inicjatywy New York OpenData Initiative](https://nycopendata.socrata.com/). Te dane nie powinny być uważane za bieżące ani ukończone. Indeks znajduje się w usłudze piaskownicy dostarczonej przez firmę Microsoft, co oznacza, że nie potrzebujesz subskrypcji platformy Azure ani usługi Azure Wyszukiwanie poznawcze do wypróbowania tych zapytań.

To, czego potrzebujesz, jest Poster lub równoważne narzędzie do wystawiania żądania HTTP przy POBIERAniu lub WYSYŁAniu. Jeśli nie znasz tych narzędzi, zobacz [Szybki Start: Eksplorowanie interfejsu API REST platformy Azure wyszukiwanie poznawcze](search-get-started-rest.md).

## <a name="set-up-the-request"></a>Skonfiguruj żądanie

1. Nagłówki żądań muszą mieć następujące wartości:

   | Klucz | Wartość |
   |-----|-------|
   | Content-Type | `application/json`|
   | klucz interfejsu API  | `252044BE3886FE4A8E3BAA4F595114BB` </br> (jest to rzeczywisty klucz interfejsu API zapytania dla usługi wyszukiwania piaskownicy hostującym indeks zadań NYC) |

1. Ustaw zlecenie na **`GET`** .

1. Ustaw adres URL na **`https://azs-playground.search.windows.net/indexes/nycjobs/docs/search=*&api-version=2020-06-30&queryType=full`**

   + Kolekcja Documents na indeksie zawiera całą zawartość do przeszukiwania. Klucz interfejsu API zapytania podany w nagłówku żądania działa tylko w przypadku operacji odczytu przeznaczonych dla kolekcji dokumentów.

   + **`$count=true`** Zwraca liczbę dokumentów pasujących do kryteriów wyszukiwania. W przypadku pustego ciągu wyszukiwania liczba będzie zawierać wszystkie dokumenty w indeksie (około 2558 w przypadku zadań NYC).

   + **`search=*`** jest nieokreślonym zapytaniem, równoważne wartości null lub pustego wyszukiwania. Nie jest to szczególnie przydatne, ale jest najprostszym wyszukiwaniem, które można wykonać i zawiera wszystkie pola do pobierania w indeksie, ze wszystkimi wartościami.

   + **`queryType=full`** wywołuje pełny Analizator Lucene.

1. W ramach kroku weryfikacji Wklej następujące żądanie do GET i kliknij pozycję **Wyślij**. Wyniki są zwracane w postaci pełnych dokumentów JSON.

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-full-lucene-parsing"></a>Jak wywoływać pełne analizowanie Lucene

Dodaj **`queryType=full`** , aby wywołać pełną składnię zapytania, zastępując domyślną prostą składnię zapytania. We wszystkich przykładach w tym artykule określono **`queryType=full`** parametr Search wskazujący, że Pełna składnia jest obsługiwana przez Analizator zapytań Lucene. 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "queryType": "full"
}
```

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Przykład 1: zapytanie zakresu zapytania do listy pól

Pierwszy przykład nie jest specyficzny dla parsera, ale prowadzimy do tego, aby wprowadzić pierwszą podstawową koncepcję zapytania: zawieranie. W tym przykładzie ograniczenie wykonywania zapytania i odpowiedzi do zaledwie kilku określonych pól. Znajomość sposobu tworzenia struktury odpowiedzi w formacie JSON jest ważna, gdy narzędzie jest dostępne po opublikowaniu lub w Eksploratorze wyszukiwania. 

To zapytanie dotyczy tylko *business_title* w **`searchFields`** , określające za pośrednictwem **`select`** parametru tego samego pola w odpowiedzi.

```http
POST https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Odpowiedź na to zapytanie powinna wyglądać podobnie do poniższego zrzutu ekranu.

  ![Przykładowe odpowiedzi Poster z wynikami](media/search-query-lucene-examples/postman-sample-results.png)

Być może zauważono wynik wyszukiwania w odpowiedzi. Jednolite Punktacja **1** występuje, gdy nie ma żadnej rangi, ponieważ wyszukiwanie nie było wyszukiwaniem pełnotekstowym lub nie podano żadnych kryteriów. W przypadku pustego wyszukiwania wiersze są wycofywane w dowolnej kolejności. Jeśli dołączysz rzeczywiste kryteria, wyniki wyszukiwania zostaną rozdzielone na znaczące wartości.

## <a name="example-2-fielded-search"></a>Przykład 2: wyszukiwanie polowe

Pełna składnia Lucene obsługuje określanie zakresu poszczególnych wyrażeń wyszukiwania do określonego pola. Ten przykład wyszukuje tytuły biznesowe z terminem starszym w nich, ale nie na żadnym z nich. Można określić wiele pól przy użyciu i.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:(senior NOT junior) AND posting_type:external",
    "searchFields": "business_title, posting_type",
    "select": "business_title, posting_type"
}
```

Odpowiedź na to zapytanie powinna wyglądać podobnie do poniższego zrzutu ekranu (posting_type nie jest wyświetlany).

  :::image type="content" source="media/search-query-lucene-examples/intrafieldfilter.png" alt-text="Wyrażenie wyszukiwania przykładowego odpowiedzi Poster" border="false":::

Wyrażenie wyszukiwania może być pojedynczym słowem lub frazą lub bardziej skomplikowanym wyrażeniem w nawiasach, opcjonalnie z operatorami logicznymi. Oto kilka przykładów:

+ `business_title:(senior NOT junior)`
+ `state:("New York" OR "New Jersey")`
+ `business_title:(senior NOT junior) AND posting_type:external`

Pamiętaj, aby umieścić wiele ciągów w cudzysłowie, jeśli chcesz, aby oba ciągi były oceniane jako pojedyncze jednostki, tak jak w tym przypadku wyszukiwanie dwóch odrębnych lokalizacji w `state` polu. W zależności od narzędzia może zajść potrzeba ucieczki ( `\` ) cudzysłowu. 

Pole określone w elemencie **FieldName: searchExpression** musi być polem z możliwością wyszukiwania. Aby uzyskać szczegółowe informacje na temat sposobu używania atrybutów indeksu w definicjach pól, zobacz [create index (interfejs API REST platformy Azure wyszukiwanie poznawcze)](/rest/api/searchservice/create-index) .

> [!NOTE]
> W powyższym przykładzie **`searchFields`** parametr zostanie pominięty, ponieważ każda część zapytania ma jawnie określoną nazwę pola. Można jednak nadal używać, **`searchFields`** jeśli zapytanie ma wiele części (przy użyciu instrukcji i). Na przykład zapytanie `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` byłoby zgodne tylko z `senior NOT junior` `business_title` polem, a w tym polu będzie pasować wartość "External" `posting_type` . Nazwa pola podana w `fieldName:searchExpression` zawsze ma pierwszeństwo przed **`searchFields`** , co oznacza, że w tym przykładzie można pominąć `business_title` z **`searchFields`** .

## <a name="example-3-fuzzy-search"></a>Przykład 3: Wyszukiwanie rozmyte

Pełna składnia Lucene obsługuje również Wyszukiwanie rozmyte, które dopasowuje się do warunków, które mają podobną konstrukcję. Aby wykonać Wyszukiwanie rozmyte, Dołącz symbol tyldy `~` na końcu pojedynczego słowa z opcjonalnym parametrem, wartością z przedziału od 0 do 2, która określa odległość edycji. Na przykład, `blue~` lub `blue~1` zwróci niebieskie, blues i przyklej.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:asosiate~",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Wyrażenia nie są obsługiwane bezpośrednio, ale można określić dopasowanie rozmyte dla każdego warunku jednoczęściowej frazy, na przykład `search=business_title:asosiate~ AND comm~` .  Na poniższym zrzucie ekranu odpowiedź zawiera dopasowanie do *skojarzenia ze społecznością*.

  :::image type="content" source="media/search-query-lucene-examples/fuzzysearch.png" alt-text="Odpowiedź na Wyszukiwanie rozmyte" border="false":::

> [!Note]
> Zapytania rozmyte nie są [analizowane](search-lucene-query-architecture.md#stage-2-lexical-analysis). Typy zapytań z niekompletnymi postanowieniami (zapytanie o prefiks, zapytanie symboli wieloznacznych, zapytanie rozmyte) są dodawane bezpośrednio do drzewa zapytań, pomijając etap analizy. Jedyne przekształcenie wykonywane na częściowych terminach zapytania to lowercasing.
>

## <a name="example-4-proximity-search"></a>Przykład 4: wyszukiwanie w sąsiedztwie

Wyszukiwania w sąsiedztwie są używane do znajdowania terminów blisko siebie w dokumencie. Wstaw symbol tyldy "~" na końcu frazy, a po niej liczbę słów, które tworzą granicę bliskości. Na przykład "Port lotniczy" w hotelu "~ 5 zawiera warunki hotelowe i lotnisko w 5 wyrazach innych elementów w dokumencie.

To zapytanie wyszukuje terminy "starszy" i "analityk", gdzie każdy termin jest oddzielony nie więcej niż jednym słowem, a cudzysłowy są oznaczone znakiem ucieczki ( `\"` ) w celu zachowania frazy:

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~1",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Odpowiedź na to zapytanie powinna wyglądać podobnie do poniższego zrzutu ekranu 

  :::image type="content" source="media/search-query-lucene-examples/proximity-before.png" alt-text="Zapytanie o bliskość" border="false":::

Spróbuj ponownie, eliminując każdą odległość ( `~0` ) między warunkami "wyższy analityk". Zwróć uwagę, że 8 dokumentów jest zwracanych dla tego zapytania, a nie do 10 dla poprzedniego zapytania.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~0",
    "searchFields": "business_title",
    "select": "business_title"
}
```

## <a name="example-5-term-boosting"></a>Przykład 5: zwiększenie warunków

Zwiększenie warunków dotyczy klasyfikacji dokumentu, jeśli zawiera on podwyższony termin względem dokumentów, które nie zawierają warunków. Aby zwiększyć okres obowiązywania, użyj karetki, `^` symbolu ze współczynnikiem zwiększania (liczba) na końcu wyszukiwanego okresu.

W tym zapytaniu "Before" Wyszukaj zadania z takim *analitykiem komputerowym* i zwróć uwagę na to, że nie ma żadnych wyników zarówno dla *komputera* , jak i *analityka*, ale zadania *komputera* znajdują się w górnej części wyników.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst",
    "searchFields": "business_title",
    "select": "business_title"
}
```

W zapytaniu "After" Powtórz wyszukiwanie, a tym samym czasie zwiększy wyniki za pomocą warunkowego *analityka* na *komputerze* , jeśli oba słowa nie istnieją. Czytelna dla człowieka wersja zapytania to `search=business_title:computer analyst^2` . W przypadku zapytania wykonywanego w programie Poster `^2` jest zakodowany jako `%5E2` .

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst%5e2",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Odpowiedź na to zapytanie powinna wyglądać podobnie do poniższego zrzutu ekranu.

  :::image type="content" source="media/search-query-lucene-examples/termboostingafter.png" alt-text="Zwiększenie warunków" border="false":::

Zwiększenie okresu różni się od profilów oceniania w tym profilu oceniania, a nie na określonych warunkach. Poniższy przykład pomaga zilustrować różnice.

Rozważmy profil oceniania, który zwiększa dopasowań w określonym polu, takich jak **gatunek** w przykładowym musicstoreindex. Zwiększenie okresu może służyć do dalszej promocji niektórych wyszukiwanych terminów wyższych niż inne. Na przykład "skała ^ 2 elektroniczna" spowoduje zwiększenie poziomu dokumentów zawierających terminy wyszukiwania w polu **gatunek** powyżej innych pól, które można wyszukiwać w indeksie. Ponadto dokumenty zawierające termin wyszukiwania "skały" będą wyższe niż w przypadku innych wyszukiwanych terminów "elektroniczny" w wyniku okresu zwiększenia wartości (2).

W przypadku ustawienia poziomu współczynnika wyższa wartość współczynnika zwiększania istotny termin będzie odnosić się do innych wyszukiwanych terminów. Domyślnie współczynnik zwiększania wynosi 1. Chociaż współczynnik zwiększania wartości musi być dodatni, może być mniejszy niż 1 (na przykład 0,2).

## <a name="example-6-regex"></a>Przykład 6: wyrażenie regularne

Wyszukiwanie w wyrażeniu regularnym wyszukuje dopasowanie na podstawie zawartości między ukośnikami "/", zgodnie z opisem w [klasie RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:/(Sen|Jun)ior/",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Odpowiedź na to zapytanie powinna wyglądać podobnie do poniższego zrzutu ekranu.

  :::image type="content" source="media/search-query-lucene-examples/regex.png" alt-text="Zapytanie wyrażenia regularnego" border="false":::

> [!Note]
> Zapytania wyrażenia regularnego nie są [analizowane](./search-lucene-query-architecture.md#stage-2-lexical-analysis). Jedyne przekształcenie wykonywane na częściowych terminach zapytania to lowercasing.
>

## <a name="example-7-wildcard-search"></a>Przykład 7: wyszukiwanie przy użyciu symboli wieloznacznych

Można użyć ogólnie rozpoznanej składni dla wielu \* symboli wieloznacznych () lub pojedynczych znaków (?). Zwróć uwagę, że Analizator zapytań Lucene obsługuje używanie tych symboli z pojedynczym terminem, a nie frazą.

W tym zapytaniu Wyszukaj zadania, które zawierają prefiks "program", który zawiera tytuły biznesowe z programowaniem terminów i programistą. Nie można użyć `*` symbolu or `?` jako pierwszego znaku wyszukiwania.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:prog*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Odpowiedź na to zapytanie powinna wyglądać podobnie do poniższego zrzutu ekranu.

  :::image type="content" source="media/search-query-lucene-examples/wildcard.png" alt-text="Zapytanie symboli wieloznacznych" border="false":::

> [!Note]
> Zapytania z symbolami wieloznacznymi nie są [analizowane](./search-lucene-query-architecture.md#stage-2-lexical-analysis). Jedyne przekształcenie wykonywane na częściowych terminach zapytania to lowercasing.
>

## <a name="next-steps"></a>Następne kroki

Spróbuj określić zapytania w kodzie. Poniższe linki wyjaśniają, jak skonfigurować zapytania wyszukiwania przy użyciu zestawów SDK platformy Azure.

+ [Tworzenie zapytań względem indeksu przy użyciu zestawu .NET SDK](search-get-started-dotnet.md)
+ [Tworzenie zapytań względem indeksu przy użyciu zestawu SDK języka Python](search-get-started-python.md)
+ [Tworzenie zapytań względem indeksu przy użyciu zestawu JavaScript SDK](search-get-started-javascript.md)

Dodatkowe informacje na temat składni, architekturę zapytań i przykłady można znaleźć w następujących linkach:

+ [Przykłady zapytań składni Lucene w celu tworzenia zaawansowanych zapytań](search-query-lucene-examples.md)
+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Prosta składnia zapytań](query-simple-syntax.md)
+ [Pełna składnia zapytań Lucene](query-lucene-syntax.md)
+ [Składnia filtru](search-query-odata-filter.md)