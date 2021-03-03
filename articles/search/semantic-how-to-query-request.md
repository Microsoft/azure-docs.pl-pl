---
title: Tworzenie zapytania semantycznego
titleSuffix: Azure Cognitive Search
description: Ustaw typ zapytania semantycznego, aby dołączyć modele uczenia głębokiego do przetwarzania zapytań, wnioskowanie o zamiar i kontekście w ramach rangi wyszukiwania i jej przydatności.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 7551ef88c2251b64cf6f6db1de4fed22db2c69e2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693649"
---
# <a name="create-a-semantic-query-in-cognitive-search"></a>Utwórz zapytanie semantyczne w Wyszukiwanie poznawcze

> [!IMPORTANT]
> Typ zapytania semantycznego jest w publicznej wersji zapoznawczej, dostępny za pomocą interfejsu API REST i Azure Portal. Funkcje w wersji zapoznawczej są oferowane w postaci, w której znajdują się [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Podczas początkowego uruchamiania podglądu nie jest naliczana opłata za wyszukiwanie semantyczne. Aby uzyskać więcej informacji, zobacz [dostępność i Cennik](semantic-search-overview.md#availability-and-pricing).

W tym artykule dowiesz się, jak sformułować żądanie wyszukiwania, które korzysta z klasyfikacji semantycznej i tworzy podpisy semantyczne i odpowiedzi.

## <a name="prerequisites"></a>Wymagania wstępne

+ Usługa wyszukiwania w warstwie Standardowa (S1, S2, S3), która znajduje się w jednym z następujących regionów: Północno-środkowe stany USA, zachodnie stany USA, zachodnie stany USA 2, Wschodnie stany USA 2, Europa Północna, Europa Zachodnia. Jeśli masz istniejącą usługę S1 lub większą w jednym z tych regionów, możesz poprosić o dostęp bez konieczności tworzenia nowej usługi.

+ Dostęp do wersji zapoznawczej wyszukiwania semantycznego: [rejestracja](https://aka.ms/SemanticSearchPreviewSignup)

+ Istniejący indeks wyszukiwania zawierający zawartość w języku angielskim

+ Klient wyszukiwania do wysyłania zapytań

  Klient wyszukiwania musi obsługiwać interfejsy API REST w wersji zapoznawczej na żądanie zapytania. Możesz użyć programu [Poster](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)lub kodu, który został zmodyfikowany, aby zapewnić wywołania REST do interfejsów API w wersji zapoznawczej. Możesz również użyć [Eksploratora wyszukiwania](search-explorer.md) w Azure Portal, aby przesłać zapytanie semantyczne.

+ Żądanie [wyszukiwania dokumentów](/rest/api/searchservice/preview-api/search-documents) z opcją semantyczną i innymi parametrami opisanymi w tym artykule.

## <a name="whats-a-semantic-query"></a>Co to jest zapytanie semantyczne?

W Wyszukiwanie poznawcze zapytanie jest sparametryzowanym żądaniem, które określa przetwarzanie zapytań i kształt odpowiedzi. *Zapytanie semantyczne* dodaje parametry, które wywołują algorytm przeklasyfikowania semantycznego, który może ocenić kontekst i znaczenie pasujących wyników, i podwyższyć poziom dopasowania do góry.

Poniższe żądanie jest reprezentatywne dla podstawowej kwerendy semantycznej (bez odpowiedzi).

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us",  
}
```

Podobnie jak w przypadku wszystkich zapytań w Wyszukiwanie poznawcze, żądanie odwołuje się do kolekcji dokumentów jednego indeksu. Co więcej, zapytanie semantyczne przeprowadzi tę samą sekwencję analizy, analizy i skanowania jako zapytanie niesemantyczne. Różnica polega na tym, jak istotność jest obliczana. Zgodnie z definicją w tej wersji zapoznawczej, zapytanie semantyczne jest jednym, którego *wyniki* są ponownie przetwarzane przy użyciu zaawansowanych algorytmów, co pozwala na wykorzystanie dopasowań uważanych za najbardziej odpowiednie przez rangę semantyczną, a nie wyniki przypisane przez domyślny algorytm klasyfikacji podobieństwa. 

Tylko górne dopasowania 50 z wyników początkowych mogą być semantycznie klasyfikowane i wszystkie zawierają podpisy w odpowiedzi. Opcjonalnie można określić **`answer`** parametr w żądaniu w celu wyodrębnienia potencjalnej odpowiedzi. Ten model przedstawia maksymalnie pięć potencjalnych odpowiedzi do zapytania, które można wybrać w górnej części strony wyszukiwania.

## <a name="query-using-rest-apis"></a>Zapytanie przy użyciu interfejsów API REST

Pełną specyfikację interfejsu API REST można znaleźć w [dokumencie wyszukiwania (wersja zapoznawcza)](/rest/api/searchservice/preview-api/search-documents).

Zapytania semantyczne są przeznaczone do pytań zakończonych nieprzerwanie, takich jak "Jakie są najlepsze zakłady dla zapylania" lub "jak narybka jaja". Jeśli chcesz, aby odpowiedź zawierała odpowiedzi, możesz dodać opcjonalny **`answer`** parametr do żądania.

Poniższy przykład używa hoteli-Sample-index do tworzenia żądania zapytania semantycznego z odpowiedziami semantycznymi i napisami:

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

### <a name="formulate-the-request"></a>Formułowanie żądania

1. Ustaw wartość **`"queryType"`** "semantyka" i **`"queryLanguage"`** "pl-US". Oba parametry są wymagane.

   QueryLanguage musi być zgodna z wszystkimi [analizatorami języka](index-add-language-analyzers.md) przypisanymi do definicji pól w schemacie indeksu. Jeśli queryLanguage ma wartość "en-us", wszystkie analizatory języka muszą być również w języku angielskim ("en. Microsoft" lub "en. Lucene"). Wszystkie analizatory języka niezależny od, takie jak słowo kluczowe lub proste, nie mają konfliktu z wartościami queryLanguage.

   W żądaniu zapytania, jeśli używasz również [korekcji pisowni](speller-how-to-add.md), ustawiana queryLanguage jest stosowana równomiernie z pisownią, odpowiedziami i napisami. Nie istnieje przesłonięcie poszczególnych części. 

   Gdy zawartość w indeksie wyszukiwania może być złożona w wielu językach, dane wejściowe zapytania najprawdopodobniej są w jednym. Aparat wyszukiwania nie sprawdza zgodności queryLanguage, analizatora języka i języka, w którym składa się zawartość, dlatego należy odpowiednio wprowadzić zakres zapytań, aby uniknąć tworzenia nieprawidłowych wyników.

<a name="searchfields"></a>

1. Ustaw wartość **`"searchFields"`** (opcjonalnie, ale zalecana).

   W kwerendzie semantycznej kolejność pól w "searchFields" odzwierciedla priorytet lub względną ważność pola w klasyfikacjach semantycznych. Będą używane tylko pola ciągu najwyższego poziomu (autonomiczne lub w kolekcji). Ponieważ searchFields ma inne zachowania w prostych i pełnych zapytaniach Luce (w przypadku których nie ma domniemanej kolejności priorytetów), wszystkie pola niebędące ciągami i podpolami nie spowodują błędu, ale nie będą również używane w klasyfikacji semantycznej.

   Określając searchFields, postępuj zgodnie z następującymi wskazówkami:

   + Zwięzłe pola, takie jak Hotelname lub title, powinny poprzedzać pełne pola, takie jak Description.

   + Jeśli indeks zawiera pole adresu URL, które jest typu tekstowego (na przykład przez człowieka, np `www.domain.com/name-of-the-document-and-other-details` `www.domain.com/?id=23463&param=eis` .), umieść je na liście (najpierw Jeśli nie ma zwięzłego pola title).

   + Jeśli określono tylko jedno pole, będzie ono traktowane jako opisowe pole dla semantycznej klasyfikacji dokumentów.  

   + Jeśli nie określono żadnych pól, wszystkie pola z możliwością wyszukiwania będą brane pod uwagę w przypadku semantycznej klasyfikacji dokumentów. Nie jest to jednak zalecane, ponieważ może nie dać optymalnych wyników z indeksu wyszukiwania.

1. Usuń **`"orderBy"`** klauzule, jeśli istnieją w istniejącym żądaniu. Wynik semantyczny jest używany do porządkowania wyników, a jeśli dołączysz jawną logikę sortowania, zwracany jest błąd HTTP 400.

1. Opcjonalnie można dodać **`"answers"`** zestaw do "Extract" i określić liczbę odpowiedzi, jeśli potrzebujesz więcej niż 1.

1. Opcjonalnie Dostosuj styl podświetlania zastosowany do napisów. Podpisy mają wyróżnione formatowanie dla fragmentów klucza w dokumencie, który podsumowuje odpowiedź. Wartość domyślna to `<em>`. Jeśli chcesz określić typ formatowania (na przykład żółte tło), możesz ustawić highlightPreTag i highlightPostTag.

1. Określ wszelkie inne parametry, które mają być używane w żądaniu. Parametry, takie jak [pisownia](speller-how-to-add.md), [wybór](search-query-odata-select.md)i liczba zwiększają jakość żądania i czytelności odpowiedzi.

### <a name="review-the-response"></a>Przejrzyj odpowiedź

Odpowiedź na powyższe zapytanie zwraca następujące dopasowanie jako pierwsze pobranie. Podpisy są zwracane automatycznie, z zwykłym tekstem i wyróżnionymi wersjami. Aby uzyskać więcej informacji na temat odpowiedzi semantycznych, zobacz [Klasyfikacja i odpowiedzi semantyczne](semantic-how-to-query-response.md).

```json
"@odata.count": 29,
"value": [
    {
        "@search.score": 1.8920634,
        "@search.rerankerScore": 1.1091284966096282,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Budget. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Budget. New Luxury Hotel. Be the first to stay.<strong> Bay views</strong> from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelId": "18",
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Budget"
    },
```

### <a name="parameters-used-in-a-semantic-query"></a>Parametry używane w zapytaniach semantycznych

Poniższa tabela zawiera podsumowanie parametrów zapytania używanych w kwerendzie semantycznej, dzięki czemu można je zobaczyć całościowo. Aby uzyskać listę wszystkich parametrów, zobacz [Wyszukiwanie dokumentów (wersja zapoznawcza REST)](/rest/api/searchservice/preview-api/search-documents)

| Parametr | Typ | Opis |
|-----------|-------|-------------|
| Znak | Ciąg | Prawidłowe wartości to proste, pełne i semantyczne. Dla zapytań semantycznych jest wymagana wartość "semantyka". |
| queryLanguage | Ciąg | Wymagane dla zapytań semantycznych. Obecnie jest zaimplementowana tylko wartość "en-us". |
| searchFields | Ciąg | Rozdzielana przecinkami lista pól do przeszukiwania. Opcjonalne, ale zalecane. Określa pola, w których występuje Klasyfikacja semantyczna. </br></br>W przeciwieństwie do prostych i pełnych typów zapytań, kolejność, w której pola są wyświetlane, określa pierwszeństwo.|
| uzyskiwan |Ciąg | Pole opcjonalne, aby określić, czy w wyniku mają być uwzględniane semantyki odpowiedzi. Obecnie tylko "Wyodrębnianie" jest implementowane. Odpowiedzi można skonfigurować tak, aby zwracały maksymalnie pięć. Ten przykład "Wyodrębnianie|count3 "" pokazuje liczbę trzech odpowiedzi. Wartość domyślna to 1.|

## <a name="query-with-search-explorer"></a>Wykonywanie zapytań przy użyciu Eksploratora wyszukiwania

Następujące zapytanie odwołuje się do wbudowanego przykładu hoteli w hotelach, przy użyciu interfejsu API w wersji 2020-06-30-Preview i działa w Eksploratorze wyszukiwania. `$select`Klauzula ogranicza wyniki do zaledwie kilku pól, ułatwiając skanowanie w pełnym formacie JSON w Eksploratorze wyszukiwania.

### <a name="with-querytypesemantic"></a>Z semantyką querytype =

```json
search=I want a nice hotel on the water with a great restaurant&$select=HotelId,HotelName,Description,Tags&queryType=semantic&queryLanguage=english&searchFields=Description,Tags
```

Poniżej przedstawiono kilka pierwszych wyników.

```json
{
    "@search.score": 0.38330218,
    "@search.rerankerScore": 0.9754053303040564,
    "HotelId": "18",
    "HotelName": "Oceanside Resort",
    "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
    "Tags": [
        "view",
        "laundry service",
        "air conditioning"
    ]
},
{
    "@search.score": 1.8920634,
    "@search.rerankerScore": 0.8829904259182513,
    "HotelId": "36",
    "HotelName": "Pelham Hotel",
    "Description": "Stunning Downtown Hotel with indoor Pool. Ideally located close to theatres, museums and the convention center. Indoor Pool and Sauna and fitness centre. Popular Bar & Restaurant",
    "Tags": [
        "view",
        "pool",
        "24-hour front desk service"
    ]
},
{
    "@search.score": 0.95706713,
    "@search.rerankerScore": 0.8538530203513801,
    "HotelId": "22",
    "HotelName": "Stone Lion Inn",
    "Description": "Full breakfast buffet for 2 for only $1.  Excited to show off our room upgrades, faster high speed WiFi, updated corridors & meeting space. Come relax and enjoy your stay.",
    "Tags": [
        "laundry service",
        "air conditioning",
        "restaurant"
    ]
},
```

### <a name="with-querytype-default"></a>Z querytype (wartość domyślna)

W celu porównania Uruchom takie samo zapytanie jak powyżej, usuwając `&queryType=semantic&queryLanguage=english&searchFields=Description,Tags` . Zwróć uwagę, że nie ma żadnych `"@search.rerankerScore"` w tych wynikach i że różne Hotele pojawiają się w trzech pierwszych pozycjach.

```json
{
    "@search.score": 8.633856,
    "HotelId": "3",
    "HotelName": "Triple Landscape Hotel",
    "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
    "Tags": [
        "air conditioning",
        "bar",
        "continental breakfast"
    ]
},
{
    "@search.score": 6.407289,
    "HotelId": "40",
    "HotelName": "Trails End Motel",
    "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
    "Tags": [
        "continental breakfast",
        "view",
        "view"
    ]
},
{
    "@search.score": 5.843788,
    "HotelId": "14",
    "HotelName": "Twin Vertex Hotel",
    "Description": "New experience in the Making.  Be the first to experience the luxury of the Twin Vertex. Reserve one of our newly-renovated guest rooms today.",
    "Tags": [
        "bar",
        "restaurant",
        "air conditioning"
    ]
},
```

## <a name="next-steps"></a>Następne kroki

Odwołaj tę klasyfikację semantyczną i odpowiedzi są kompilowane nad początkowym zestawem wyników. Każda logika, która poprawia jakość początkowych wyników, będzie przekazywać do wyszukiwania semantycznego. W następnym kroku zapoznaj się z funkcjami, które przyczyniają się do wyników początkowych, w tym analizatorów, które wpływają na sposób tworzenia tokenów ciągów, profile oceniania, które mogą dostosowywać wyniki, oraz domyślny algorytm istotności.

+ [Analizatory do przetwarzania tekstu](search-analyzers.md)
+ [Podobieństwo i ocenianie w Wyszukiwanie poznawcze](index-similarity-and-scoring.md)
+ [Dodawanie profili oceniania](index-add-scoring-profiles.md)
+ [Omówienie wyszukiwania semantycznego](semantic-search-overview.md)
+ [Dodaj sprawdzanie pisowni do terminów zapytania](speller-how-to-add.md)
