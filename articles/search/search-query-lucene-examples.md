---
title: Użyj pełnej składni zapytań Lucene
titleSuffix: Azure Cognitive Search
description: Przykłady zapytania przedstawiające składnię zapytań Lucene dla wyszukiwania rozmytego, wyszukiwania w sąsiedztwie, zwiększania terminów, wyszukiwania wyrażeń regularnych i wyszukiwania symboli wieloznacznych w indeksie Wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 6213efb6ba14052c6f957a6d999f48f55f65186c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101693564"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Użyj "pełnej" składni wyszukiwania Lucene (zapytania zaawansowane w usłudze Azure Wyszukiwanie poznawcze)

Podczas konstruowania zapytań dotyczących usługi Azure Wyszukiwanie poznawcze można zastąpić domyślny [prosty Analizator zapytań](query-simple-syntax.md) z bardziej zaawansowanym [analizatorem zapytań Lucene](query-lucene-syntax.md) , aby sformułować wyspecjalizowane i zaawansowane wyrażenia zapytań.

Parser Lucene obsługuje złożone formaty zapytań, takie jak zapytania o zakresie pól, Wyszukiwanie rozmyte, wrostkowe i symboli wieloznacznych, wyszukiwanie w sąsiedztwie, zwiększanie terminów i wyszukiwanie wyrażeń regularnych. Dodatkowa moc jest dostarczana z dodatkowymi wymaganiami dotyczącymi przetwarzania, dlatego należy oczekiwać nieco dłuższego czasu wykonania. W tym artykule można zapoznać się z przykładami pokazującymi operacje zapytań na podstawie pełnej składni.

> [!Note]
> Wiele wyspecjalizowanych konstrukcji zapytań włączonych za pomocą pełnej składni zapytań Lucene nie jest w trakcie [analizowania tekstu](search-lucene-query-architecture.md#stage-2-lexical-analysis), co może być zaskakującee, jeśli oczekujesz rdzeni lub Lematyzacja. Analiza leksykalna jest wykonywana tylko w przypadku pełnych warunków (zapytania warunkowego zapytania lub frazy). Typy zapytań z niekompletnymi postanowieniami (zapytanie o prefiks, zapytanie symboli wieloznacznych, zapytanie rozmyte) są dodawane bezpośrednio do drzewa zapytań, pomijając etap analizy. Jedyne przekształcenie wykonywane na częściowych terminach zapytania to lowercasing. 
>

## <a name="hotels-sample-index"></a>Przykładowy indeks hoteli

Poniższe zapytania opierają się na indeksie z przykładem hoteli, który można utworzyć, postępując zgodnie z instrukcjami w tym [przewodniku szybki start](search-get-started-portal.md).

Przykładowe zapytania są łączone za pomocą interfejsu API REST i żądań POST. Można je wkleić i uruchomić w programie [Poster](search-get-started-rest.md) lub w [Visual Studio Code z rozszerzeniem wyszukiwanie poznawcze](search-get-started-vs-code.md).

Nagłówki żądań muszą mieć następujące wartości:

| Klucz | Wartość |
|-----|-------|
| Content-Type | application/json|
| klucz interfejsu API  | `<your-search-service-api-key>`, albo klucz zapytania lub administratora |

Parametry identyfikatora URI muszą zawierać punkt końcowy usługi Search z nazwą indeksu, kolekcjami dokumentów, poleceniem Search i wersją interfejsu API, podobnym do poniższego przykładu:

```http
https://{{service-name}}.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
```

Treść żądania powinna być sformułowana jako prawidłowy kod JSON:

```json
{
    "search": "*",
    "queryType": "full",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ ustawienie "Wyszukaj" `*` jest nieokreślonym zapytaniem równym null lub pustego wyszukiwania. Nie jest to szczególnie przydatne, ale jest najprostszym wyszukiwaniem, które można wykonać i zawiera wszystkie pola do pobierania w indeksie, ze wszystkimi wartościami.

+ "querytype" ma wartość "Full" wywołuje pełny Analizator zapytań Lucene i jest wymagany dla tej składni.

+ Opcja "Select" ustawiona na listę pól rozdzielana przecinkami jest używana do tworzenia struktury wyników wyszukiwania, w tym tylko tych pól, które są przydatne w kontekście wyników wyszukiwania.

+ wartość "Count" zwraca liczbę dokumentów pasujących do kryteriów wyszukiwania. W przypadku pustego ciągu wyszukiwania liczba będzie zawierać wszystkie dokumenty w indeksie (50 w przypadku hoteli-Sample-index).

## <a name="example-1-fielded-search"></a>Przykład 1: wyszukiwanie polowe

Zakres wyszukiwania w polu indywidualny, osadzone wyrażenia wyszukiwania do określonego pola. Ten przykład wyszukuje nazwy hotelu z terminem "Hotel", ale nie "Motel". Można określić wiele pól przy użyciu i. 

Korzystając z tej składni zapytań, można pominąć parametr "searchFields", gdy pola, które mają być zapytania, znajdują się w samym wyrażeniu wyszukiwania. Jeśli dołączysz "searchFields" z wyszukiwaniem w polu, `fieldName:searchExpression` zawsze ma pierwszeństwo przed "searchFields".

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:(hotel NOT motel) AND Category:'Resort and Spa'",
    "queryType": "full",
    "select": "HotelName, Category",
    "count": true
}
```

Odpowiedź na to zapytanie powinna wyglądać podobnie do poniższego przykładu, filtrowanym według wartości "kurort i spa", zwracająca Hotele, które zawierają "Hotel" lub "Motel" w nazwie.

```json
"@odata.count": 4,
"value": [
    {
        "@search.score": 4.481559,
        "HotelName": "Nova Hotel & Spa",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.4524608,
        "HotelName": "King's Palace Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.3970203,
        "HotelName": "Triple Landscape Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.2953436,
        "HotelName": "Peaceful Market Hotel & Spa",
        "Category": "Resort and Spa"
    }
]
```

Wyrażenie wyszukiwania może być pojedynczym terminem lub frazą lub bardziej skomplikowanym wyrażeniem w nawiasach, opcjonalnie z operatorami logicznymi. Oto kilka przykładów:

+ `HotelName:(hotel NOT motel)`
+ `Address/StateProvince:("WA" OR "CA")`
+ `Tags:("free wifi" NOT "free parking") AND "coffee in lobby"`

Pamiętaj, aby umieścić frazę w cudzysłowie, jeśli chcesz, aby oba ciągi były oceniane jako pojedyncze jednostki, jak w tym przypadku wyszukiwanie dwóch odrębnych lokalizacji w polu Address/StateProvince. W zależności od klienta może zajść potrzeba ucieczki ( `\` ) cudzysłowu.

Pole określone w `fieldName:searchExpression` musi być polem z możliwością wyszukiwania. Zobacz [Tworzenie indeksu (interfejs API REST)](/rest/api/searchservice/create-index) , aby uzyskać szczegółowe informacje na temat sposobu, w jaki definicje pól są przypisane do atrybutu.

## <a name="example-2-fuzzy-search"></a>Przykład 2: Wyszukiwanie rozmyte

Wyniki wyszukiwania rozmytego są zgodne z podobnymi terminami, w tym błędnie napisanymi wyrazami. Aby wykonać Wyszukiwanie rozmyte, Dołącz symbol tyldy `~` na końcu pojedynczego słowa z opcjonalnym parametrem, wartością z przedziału od 0 do 2, która określa odległość edycji. Na przykład, `blue~` lub `blue~1` zwróci niebieskie, blues i przyklej.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Tags:conserge~",
    "queryType": "full",
    "select": "HotelName, Category, Tags",
    "searchFields": "HotelName, Category, Tags",
    "count": true
}
```

Odpowiedź na to zapytanie jest rozpoznawana jako "Concierge" w pasujących dokumentach, przycięta dla zwięzłości:

```json
"@odata.count": 12,
"value": [
    {
        "@search.score": 1.1832147,
        "HotelName": "Secret Point Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "air conditioning",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1819803,
        "HotelName": "Twin Dome Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "free wifi",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1773309,
        "HotelName": "Smile Hotel",
        "Category": "Suite",
        "Tags": [
            "view",
            "concierge",
            "laundry service"
        ]
    },
```

Wyrażenia nie są obsługiwane bezpośrednio, ale można określić dopasowanie rozmyte dla każdego warunku jednoczęściowej frazy, na przykład `search=Tags:landy~ AND sevic~` .  To wyrażenie zapytania umożliwia znalezienie 15 dopasowań w "usłudze prania".

> [!Note]
> Zapytania rozmyte nie są [analizowane](search-lucene-query-architecture.md#stage-2-lexical-analysis). Typy zapytań z niekompletnymi postanowieniami (zapytanie o prefiks, zapytanie symboli wieloznacznych, zapytanie rozmyte) są dodawane bezpośrednio do drzewa zapytań, pomijając etap analizy. Jedynym przekształceniem wykonywanym na częściowych terminach zapytania jest mała wielkość liter.
>

## <a name="example-3-proximity-search"></a>Przykład 3: wyszukiwanie zbliżeniowe

Wyszukiwanie w sąsiedztwie znajduje się w dokumencie warunki, które są blisko siebie. Wstaw symbol tyldy "~" na końcu frazy, a po niej liczbę słów, które tworzą granicę bliskości.

To zapytanie wyszukuje terminy "Hotel" i "Lotnisko" w 5 wyrazach innych elementów w dokumencie. Znaki cudzysłowu są oznaczone znakiem ucieczki ( `\"` ), aby zachować frazę:

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Description: \"hotel airport\"~5",
    "queryType": "full",
    "select": "HotelName, Description",
    "searchFields": "HotelName, Description",
    "count": true
}
```

Odpowiedź na to zapytanie powinna wyglądać podobnie do poniższego przykładu:

```json
"@odata.count": 2,
"value": [
    {
        "@search.score": 0.6331726,
        "HotelName": "Trails End Motel",
        "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
    },
    {
        "@search.score": 0.43032226,
        "HotelName": "Catfish Creek Fishing Cabins",
        "Description": "Brand new mattresses and pillows.  Free airport shuttle. Great hotel for your business needs. Comp WIFI, atrium lounge & restaurant, 1 mile from light rail."
    }
]
```

## <a name="example-4-term-boosting"></a>Przykład 4: zwiększenie warunków

Zwiększenie warunków dotyczy klasyfikacji dokumentu, jeśli zawiera on podwyższony termin względem dokumentów, które nie zawierają warunków. Aby zwiększyć okres obowiązywania, użyj karetki, `^` symbolu ze współczynnikiem zwiększania (liczba) na końcu wyszukiwanego okresu. Współczynnik zwiększania wydajności jest wartością domyślną 1, a chociaż musi być dodatnia, może być mniejsza niż 1 (na przykład 0,2). Zwiększenie okresu różni się od profilów oceniania w tym profilu oceniania, a nie na określonych warunkach.

W tym zapytaniu "Before" Wyszukaj ciąg "sekwencje Access" i zwróć uwagę na to, że istnieje siedem dokumentów pasujących do jednego lub obu warunków.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "beach access",
    "queryType": "full",
    "select": "HotelName, Description, Tags",
    "searchFields": "HotelName, Description, Tags",
    "count": true
}
```

W rzeczywistości istnieje tylko jeden dokument, który pasuje do "Access" i ponieważ jest to jedyne dopasowanie, jego położenie jest wysokie (Druga pozycja), nawet jeśli dokument nie ma terminu "sekwencje".

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.2723424,
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown."
    },
    {
        "@search.score": 1.5507699,
        "HotelName": "Old Carrabelle Hotel",
        "Description": "Spacious rooms, glamorous suites and residences, rooftop pool, walking access to shopping, dining, entertainment and the city center."
    },
    {
        "@search.score": 1.5358944,
        "HotelName": "Whitefish Lodge & Suites",
        "Description": "Located on in the heart of the forest. Enjoy Warm Weather, Beach Club Services, Natural Hot Springs, Airport Shuttle."
    },
    {
        "@search.score": 1.3433652,
        "HotelName": "Ocean Air Motel",
        "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away."
    },
```

W zapytaniu "After" Powtórz wyszukiwanie, tym samym czasie, które zwiększają wyniki z terminem "sekwencje" w ciągu terminu "dostęp". Czytelna dla człowieka wersja zapytania to `search=Description:beach^2 access` . W zależności od klienta może być konieczne wyraźne wyrażenie `^2` `%5E2` .

Po zwiększeniu okresu "sekwencje" dopasowanie w starym hotelu Carrabelle przenosi się w dół do szóstego miejsca.

<!-- Consider a scoring profile that boosts matches in a certain field, such as "genre" in a music app. Term boosting could be used to further boost certain search terms higher than others. For example, "rock^2 electronic" will boost documents that contain the search terms in the "genre" field higher than other searchable fields in the index. Furthermore, documents that contain the search term "rock" will be ranked higher than the other search term "electronic" as a result of the term boost value (2). -->

## <a name="example-5-regex"></a>Przykład 5: wyrażenie regularne

Wyszukiwanie w wyrażeniu regularnym wyszukuje dopasowanie na podstawie zawartości między ukośnikami "/", zgodnie z opisem w [klasie RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:/(Mo|Ho)tel/",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

Odpowiedź na to zapytanie powinna wyglądać podobnie do poniższego przykładu:

```json
    "@odata.count": 22,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Days Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Triple Landscape Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Smile Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Pelham Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Sublime Cliff Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Twin Dome Motel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Nova Hotel & Spa"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
```

> [!Note]
> Zapytania wyrażenia regularnego nie są [analizowane](./search-lucene-query-architecture.md#stage-2-lexical-analysis). Jedynym przekształceniem wykonywanym na częściowych terminach zapytania jest mała wielkość liter.
>

## <a name="example-6-wildcard-search"></a>Przykład 6: wyszukiwanie przy użyciu symboli wieloznacznych

Można użyć ogólnie rozpoznanej składni dla wielu `*` symboli wieloznacznych () lub pojedynczych ( `?` ). Zwróć uwagę, że Analizator zapytań Lucene obsługuje używanie tych symboli z pojedynczym terminem, a nie frazą.

W tej kwerendzie Wyszukaj nazwy hotelu zawierające prefiks "SC". Nie można użyć `*` symbolu or `?` jako pierwszego znaku wyszukiwania.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:sc*",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

Odpowiedź na to zapytanie powinna wyglądać podobnie do poniższego przykładu:

```json
    "@odata.count": 2,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scottish Inn"
        }
    ]
```

> [!Note]
> Zapytania z symbolami wieloznacznymi nie są [analizowane](./search-lucene-query-architecture.md#stage-2-lexical-analysis). Jedynym przekształceniem wykonywanym na częściowych terminach zapytania jest mała wielkość liter.
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