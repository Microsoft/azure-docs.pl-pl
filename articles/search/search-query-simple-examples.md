---
title: Użyj prostej składni zapytań Lucene
titleSuffix: Azure Cognitive Search
description: Przykłady zapytań przedstawiające prostą składnię wyszukiwania pełnotekstowego, wyszukiwanie filtrów i wyszukiwanie geograficzne względem indeksu Wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 2abe19351c92bf9cea85c85dd55f47b5ee6d1625
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694040"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Użyj składni wyszukiwania "Simple" w usłudze Azure Wyszukiwanie poznawcze

W przypadku usługi Azure Wyszukiwanie poznawcze [prosta Składnia zapytania](query-simple-syntax.md) wywołuje domyślny Analizator zapytań w celu wyszukiwania pełnotekstowego. Analizator jest szybki i obsługuje często spotykane scenariusze, w tym wyszukiwanie pełnotekstowe, filtrowanie i wyszukiwanie aspektów oraz wyszukiwanie przy użyciu prefiksów. W tym artykule użyto przykładów do zilustrowania prostego użycia składni w żądaniu [przeszukiwania dokumentów (interfejs API REST)](/rest/api/searchservice/search-documents) .

> [!NOTE]
> Alternatywna składnia zapytania to [pełna Lucene](query-lucene-syntax.md), obsługujące bardziej złożone struktury zapytań, takie jak rozmyte i wieloznaczne wyszukiwanie. Aby uzyskać więcej informacji i przykładów, zobacz [Korzystanie z pełnej składni Lucene](search-query-lucene-examples.md).

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
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ ustawienie "Wyszukaj" `*` jest nieokreślonym zapytaniem równym null lub pustego wyszukiwania. Nie jest to szczególnie przydatne, ale jest najprostszym wyszukiwaniem, które można wykonać i zawiera wszystkie pola do pobierania w indeksie, ze wszystkimi wartościami.

+ wartość "querytype" ustawiona na wartość "Simple" jest wartością domyślną i można ją pominąć, ale jest zawarta, aby dodatkowo wzmocnić, że przykłady zapytania w tym artykule są wyrażane w prostej składni.

+ Opcja "Select" ustawiona na listę pól rozdzielana przecinkami jest używana do tworzenia struktury wyników wyszukiwania, w tym tylko tych pól, które są przydatne w kontekście wyników wyszukiwania.

+ wartość "Count" zwraca liczbę dokumentów pasujących do kryteriów wyszukiwania. W przypadku pustego ciągu wyszukiwania liczba będzie zawierać wszystkie dokumenty w indeksie (50 w przypadku hoteli-Sample-index).

## <a name="example-1-full-text-search"></a>Przykład 1: wyszukiwanie pełnotekstowe

Wyszukiwanie pełnotekstowe może składać się z dowolnej liczby autonomicznych terminów lub fraz ujętych w cudzysłów, z operatorami logicznymi lub bez. 

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "pool spa +airport",
    "searchMode": any,
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
}
```

Wyszukiwanie słów kluczowych, które składa się z ważnych terminów lub fraz, najlepiej działa. Pola ciągów podlegają analizie tekstu podczas indeksowania i wykonywania zapytań, porzucając nieistotne słowa, takie jak "a", "i", "IT". Aby zobaczyć, w jaki sposób ciąg zapytania jest tokenem w indeksie, Przekaż ciąg w wywołaniu [analizy tekstu](/rest/api/searchservice/test-analyzer) do indeksu.

Parametr "searchmode" kontroluje precyzję i odwołanie. Jeśli chcesz więcej odzyskania, Użyj domyślnej wartości "any", która zwraca wynik, jeśli dowolna część ciągu zapytania zostanie dopasowana. Jeśli preferujesz precyzję, gdzie wszystkie części ciągu muszą być dopasowane, Zmień wartość searchmode na "All". Wypróbuj powyższe zapytanie na dwa sposoby, aby zobaczyć, jak opcja searchmode zmienia wynik.

Odpowiedź na zapytanie "Pool Spa + lotniska" powinna wyglądać podobnie do poniższego przykładu, który został przycięty do zwięzłości.

```json
"@odata.count": 6,
"value": [
    {
        "@search.score": 7.3617697,
        "HotelId": "21",
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown.",
        "Category": "Resort and Spa",
        "Tags": [
            "pool",
            "continental breakfast",
            "free parking"
        ]
    },
    {
        "@search.score": 2.5560288,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Description": "Newly Redesigned Rooms & airport shuttle.  Minutes from the airport, enjoy lakeside amenities, a resort-style pool & stylish new guestrooms with Internet TVs.",
        "Category": "Luxury",
        "Tags": [
            "24-hour front desk service",
            "continental breakfast",
            "free wifi"
        ]
    },
    {
        "@search.score": 2.2988036,
        "HotelId": "35",
        "HotelName": "Suites At Bellevue Square",
        "Description": "Luxury at the mall.  Located across the street from the Light Rail to downtown.  Free shuttle to the mall and airport.",
        "Category": "Resort and Spa",
        "Tags": [
            "continental breakfast",
            "air conditioning",
            "24-hour front desk service"
        ]
    }
```

Zwróć uwagę na wynik wyszukiwania w odpowiedzi. Jest to wynik istotności dopasowania. Domyślnie usługa wyszukiwania zwróci pierwsze 50 dopasowań w oparciu o ten wynik.

Jednolite Punktacja "1,0" występuje, gdy nie ma rangi, ponieważ wyszukiwanie nie było wyszukiwaniem pełnotekstowym lub nie podano żadnych kryteriów. Na przykład w przypadku pustego wyszukiwania (Search = `*` ) wiersze powracają do dowolnej kolejności. Jeśli dołączysz rzeczywiste kryteria, wyniki wyszukiwania zostaną rozdzielone na znaczące wartości.

## <a name="example-2-look-up-by-id"></a>Przykład 2: wyszukiwanie według identyfikatora

Gdy zwracasz wyniki wyszukiwania w zapytaniu, logicznym następnym krokiem jest udostępnienie strony szczegółów zawierającej więcej pól z dokumentu. W tym przykładzie pokazano, jak zwrócić pojedynczy dokument przy użyciu [dokumentu wyszukiwania](/rest/api/searchservice/lookup-document) przez przekazanie identyfikatora dokumentu.

```http
GET /indexes/hotels-sample-index/docs/41?api-version=2020-06-30
```

Wszystkie dokumenty mają unikatowy identyfikator. Jeśli używasz portalu, wybierz indeks z karty **indeksy** , a następnie sprawdź definicje pól, aby określić, które pole jest kluczem. Przy użyciu polecenia REST wywołanie [Get index](/rest/api/searchservice/get-index) zwraca definicję indeksu w treści odpowiedzi.

Odpowiedź na powyższe zapytanie składa się z dokumentu, którego kluczem jest 41. Każde pole oznaczone jako "możliwe do pobierania" w definicji indeksu może być zwracane w wynikach wyszukiwania i renderowane w aplikacji.

```json
{
    "HotelId": "41",
    "HotelName": "Ocean Air Motel",
    "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away.",
    "Description_fr": "L'hôtel front de mer surplombant la plage dispose de chambres avec balcon privé et 2 piscines intérieures et extérieures. Divers commerces et animations artistiques sont sur la promenade, à quelques pas.",
    "Category": "Budget",
    "Tags": [
        "pool",
        "air conditioning",
        "bar"
    ],
    "ParkingIncluded": true,
    "LastRenovationDate": "1951-05-10T00:00:00Z",
    "Rating": 3.5,
    "Location": {
        "type": "Point",
        "coordinates": [
            -157.846817,
            21.295841
        ],
        "crs": {
            "type": "name",
            "properties": {
                "name": "EPSG:4326"
            }
        }
    },
    "Address": {
        "StreetAddress": "1450 Ala Moana Blvd 2238 Ala Moana Ctr",
        "City": "Honolulu",
        "StateProvince": "HI",
        "PostalCode": "96814",
        "Country": "USA"
    },
```

## <a name="example-3-filter-on-text"></a>Przykład 3: filtrowanie według tekstu

[Składnia filtru](search-query-odata-filter.md) jest wyrażeniem OData, którego można użyć sama lub z opcją "Search" (wyszukiwanie). Używane razem "filtr" jest stosowany jako pierwszy do całego indeksu, a następnie wyszukiwanie jest wykonywane na wynikach filtru. Z tego względu filtrowanie może być przydatne, jeśli chcemy poprawić wydajność zapytań, ponieważ pozwala ono zawęzić zestaw dokumentów przetwarzany przez zapytanie wyszukiwania.

Filtry można definiować w dowolnym polu oznaczonym jako "z możliwością filtrowania" w definicji indeksu. W przypadku hoteli — przykładowe indeksy, pola z możliwością filtrowania obejmują kategorię, Tagi, ParkingIncluded, klasyfikację i większość pól adresu.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "art tours",
    "queryType": "simple",
    "filter": "Category eq 'Resort and Spa'",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

Odpowiedź na powyższe zapytanie jest objęta zakresem tylko hoteli sklasyfikowanych jako "raport i spa", które obejmują warunki "sztuki" lub "wycieczki". W takim przypadku istnieje tylko jedno dopasowanie.

```json
{
    "@search.score": 2.8576312,
    "HotelId": "31",
    "HotelName": "Santa Fe Stay",
    "Description": "Nestled on six beautifully landscaped acres, located 2 blocks from the Plaza. Unwind at the spa and indulge in art tours on site.",
    "Category": "Resort and Spa"
}
```

## <a name="example-4-filter-functions"></a>Przykład 4: funkcje filtrowania

Wyrażenia filtru mogą zawierać [funkcje "Search. IsMatch" i "Search. ismatchscoring"](search-query-odata-full-text-search-functions.md), co pozwala na utworzenie zapytania wyszukiwania w filtrze. To wyrażenie filtru używa symboli wieloznacznych w celu wybrania walorów *, w tym* bezpłatnych sieci Wi-Fi, bezpłatnych parków i tak dalej.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
  {
    "search": "",
    "filter": "search.ismatch('free*', 'Tags', 'full', 'any')",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
  }
```

Odpowiedź na powyższe zapytanie jest zgodna z 19 hoteli, które oferują bezpłatne usługi. Zwróć uwagę, że wynik wyszukiwania jest jednolitym "1,0" w ciągu wyników. Dzieje się tak, ponieważ wyrażenie wyszukiwania ma wartość null lub jest puste, co oznacza, że filtr Verbatim jest zgodny, ale nie ma wyszukiwania pełnotekstowego. Oceny przydatności są zwracane tylko przy wyszukiwaniu pełnotekstowym. Jeśli używasz filtrów bez "wyszukiwania", upewnij się, że masz wystarczające pola do sortowania, aby można było kontrolować rangę wyszukiwania.

```json
"@odata.count": 19,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "31",
        "HotelName": "Santa Fe Stay",
        "Tags": [
            "view",
            "restaurant",
            "free parking"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "27",
        "HotelName": "Super Deluxe Inn & Suites",
        "Tags": [
            "bar",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Tags": [
            "continental breakfast",
            "free parking",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
```

## <a name="example-5-range-filters"></a>Przykład 5: filtry zakresu

Filtrowanie zakresu jest obsługiwane za poorednictwem wyrażeń filtrów dla dowolnego typu danych. Poniższe przykłady ilustrują zakresy liczbowe i ciągi. Typy danych są ważne w filtrach zakresów i działają najlepiej, gdy dane liczbowe znajdują się w polach liczbowych oraz dane ciągu w polach ciągów. Dane liczbowe w polach ciągów nie są odpowiednie dla zakresów, ponieważ ciągi liczbowe nie są porównywalne.

Następujące zapytanie jest zakresem liczbowym. W hotelach-Sample-index, jedyne pole liczbowe do filtrowania ma klasyfikację.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating ge 2 and Rating lt 4",
    "select": "HotelId, HotelName, Rating",
    "orderby": "Rating desc",
    "count": true
}
```

Odpowiedź na to zapytanie powinna wyglądać podobnie do poniższego przykładu, który został przycięty do zwięzłości.

```json
"@odata.count": 27,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "22",
        "HotelName": "Stone Lion Inn",
        "Rating": 3.9
    },
    {
        "@search.score": 1.0,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Rating": 3.8
    },
    {
        "@search.score": 1.0,
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Rating": 3.6
    }
```

Następne zapytanie jest filtrem zakresu względem pola ciągu (Address/StateProvince):

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Address/StateProvince ge 'A*' and Address/StateProvince lt 'D*'",
    "select": "HotelId, HotelName, Address/StateProvince",
    "count": true
}
```

Odpowiedź na to zapytanie powinna wyglądać podobnie do poniższego przykładu, który został przycięty do zwięzłości. W tym przykładzie nie można sortować według StateProvince, ponieważ pole nie ma atrybutu "sortowanie" w definicji indeksu.

```json
"@odata.count": 9,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "9",
        "HotelName": "Smile Hotel",
        "Address": {
            "StateProvince": "CA "
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Address": {
            "StateProvince": "CO"
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "7",
        "HotelName": "Countryside Resort",
        "Address": {
            "StateProvince": "CA "
        }
    },
```

## <a name="example-6-geo-search"></a>Przykład 6: wyszukiwanie geograficzne

Indeks przykładowych hoteli zawiera pole geo_location ze współrzędnymi szerokości i długości geograficznej. W tym przykładzie jest stosowana [Funkcja Geo. Distance](search-query-odata-geo-spatial-functions.md#examples) , która filtruje dokumenty w obrębie obwodu punktu początkowego, do dowolnej odległości (w kilometrach), którą zapewniasz. Możesz dostosować ostatnią wartość zapytania (10), aby zmniejszyć lub powiększyć obszar powierzchni zapytania.

```http
POST /indexes/v/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "geo.distance(Location, geography'POINT(-122.335114 47.612839)') le 10",
    "select": "HotelId, HotelName, Address/City, Address/StateProvince",
    "count": true
}
```

Odpowiedź na to zapytanie zwraca wszystkie hotele w ramach 10 Kilometer odległość współrzędnych:

```json
{
    "@odata.count": 3,
    "value": [
        {
            "@search.score": 1.0,
            "HotelId": "45",
            "HotelName": "Arcadia Resort & Restaurant",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "24",
            "HotelName": "Gacc Capital",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "16",
            "HotelName": "Double Sanctuary Resort",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        }
    ]
}
```

## <a name="example-7-booleans-with-searchmode"></a>Przykład 7: wartości logiczne z opcją searchmode

Prosta składnia obsługuje operatory logiczne w postaci znaków ( `+, -, |` ) do obsługi logiki zapytań i, or, i nie. Wyszukiwanie logiczne działa tak, jak to możliwe, z kilkoma niewielkimi wyjątkami. 

W poprzednich przykładach parametr "searchmode" został wprowadzony jako mechanizm do wpływania precyzji i odwoływania, a funkcja "searchmode = any" preferuje odwoływanie (dokument, który spełnia kryteria, jest uznawana za dopasowanie), a wartość "searchmode = All" preferuje precyzję (wszystkie kryteria muszą być dopasowane do dokumentu). 

W kontekście wyszukiwania logicznego, domyślnie wartość "searchmode = any" może być myląca, jeśli tworzysz zapytanie z wieloma operatorami i uzyskujesz szersze wyniki, a nie węższe. Jest to szczególnie prawdziwe, gdzie wyniki obejmują wszystkie dokumenty "niezawierające" określonego terminu lub frazy.

Poniższy przykład stanowi ilustrację. Uruchamianie następującego zapytania z opcją searchmode (any), 42 dokumenty są zwracane: te zawierające termin "restauracji" oraz wszystkie dokumenty, które nie mają frazy "warunki powietrza". 

Należy zauważyć, że nie ma spacji między operatorem logicznym ( `-` ) i frazą "warunki powietrza".

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "restaurant -\"air conditioning\"",
    "searchMode": "any",
    "searchFields": "Tags",
    "select": "HotelId, HotelName, Tags",
    "count": true
}
```

Zmiana na "searchmode = All" wymusza skumulowany wpływ na kryteria i zwraca mniejszy zestaw wyników (7 dopasowań) składający się z dokumentów zawierających termin "restauracji", minus te zawierające frazę "warunki powietrza".

Odpowiedź na to zapytanie będzie teraz wyglądać podobnie do poniższego przykładu, który został przycięty do zwięzłości.

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.5460577,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
    {
        "@search.score": 2.166792,
        "HotelId": "10",
        "HotelName": "Countryside Hotel",
        "Tags": [
            "24-hour front desk service",
            "coffee in lobby",
            "restaurant"
        ]
    },
```

## <a name="example-8-paging-results"></a>Przykład 8: wyniki stronicowania

W poprzednich przykładach przedstawiono informacje o parametrach, które mają wpływ na kompozycję wyników wyszukiwania, w tym "Select", które określają, które pola są w wyniku, sortowania i sposobu uwzględniania liczby wszystkich dopasowań. Ten przykład to kontynuacja tworzenia wyników wyszukiwania w formie parametrów stronicowania, która pozwala na przetwarzanie wsadowe liczby wyników, które są wyświetlane na danej stronie. 

Domyślnie usługa wyszukiwania zwraca górne dopasowania do 50. Aby kontrolować liczbę dopasowań na każdej stronie, użyj "Top", aby zdefiniować rozmiar partii, a następnie użyj "Skip", aby pobrać kolejne partie.

W poniższym przykładzie zastosowano filtr i porządek sortowania w polu Rating (Klasyfikacja jest możliwe do filtrowania i sortowania), ponieważ łatwiej jest zobaczyć efekty stronicowania w posortowanych wynikach. W regularnym zapytaniu pełnego wyszukiwania najważniejsze dopasowania są klasyfikowane i stronicowane według " @search.score ".

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "count": true
}
```

Zapytanie znajduje 21 pasujących dokumentów, ale ponieważ określono "Top", odpowiedź zwraca tylko pięć najlepszych dopasowań, z klasyfikacją rozpoczynającą się od 4,9 i kończącą się na 4,7 z "Lady Lake B & B". 

Aby uzyskać następny 5, Pomiń pierwszą partię:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "skip": "5",
    "count": true
}
```

Odpowiedź drugiej partii pomija pierwsze pięć dopasowań, zwracając kolejne pięć, zaczynając od "Pull'r Inn Motel". Aby kontynuować z dodatkowymi partiami, Zachowaj wartość "Top" w 5, a następnie zwiększ wartość "Pomiń" o 5 dla każdego nowego żądania (Pomiń = 5, Pomiń = 10, Pomiń = 15 itd.).

```json
"value": [
    {
        "@search.score": 1.0,
        "HotelName": "Pull'r Inn Motel",
        "Rating": 4.7
    },
    {
        "@search.score": 1.0,
        "HotelName": "Sublime Cliff Hotel",
        "Rating": 4.6
    },
    {
        "@search.score": 1.0,
        "HotelName": "Antiquity Hotel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Nordick's Motel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Winter Panorama Resort",
        "Rating": 4.5
    }
]
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz pewną praktyczną składnię zapytania, spróbuj określić zapytania w kodzie. Poniższe linki wyjaśniają, jak skonfigurować zapytania wyszukiwania przy użyciu zestawów SDK platformy Azure.

+ [Tworzenie zapytań względem indeksu przy użyciu zestawu .NET SDK](search-get-started-dotnet.md)
+ [Tworzenie zapytań względem indeksu przy użyciu zestawu SDK języka Python](search-get-started-python.md)
+ [Tworzenie zapytań względem indeksu przy użyciu zestawu JavaScript SDK](search-get-started-javascript.md)

Dodatkowe informacje na temat składni, architekturę zapytań i przykłady można znaleźć w następujących linkach:

+ [Przykłady zapytań składni Lucene w celu tworzenia zaawansowanych zapytań](search-query-lucene-examples.md)
+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Prosta składnia zapytań](query-simple-syntax.md)
+ [Pełna składnia zapytań Lucene](query-lucene-syntax.md)
+ [Składnia filtru](search-query-odata-filter.md)