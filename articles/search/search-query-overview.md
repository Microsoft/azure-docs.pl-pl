---
title: Typy i kompozycja zapytań
titleSuffix: Azure Cognitive Search
description: Podstawowe informacje na temat tworzenia zapytania wyszukiwania w usłudze Azure Wyszukiwanie poznawcze przy użyciu parametrów do filtrowania, wybierania i sortowania wyników.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 0c05db39e02a6bc2a7fa5d62b8b891626eb0d241
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675809"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Typy i kompozycje zapytań w usłudze Azure Wyszukiwanie poznawcze

Na platformie Azure Wyszukiwanie poznawcze zapytanie jest pełną specyfikacją operacji rundy. Na żądanie istnieją parametry, które dostarczają instrukcje wykonania dla aparatu, a także parametry, które kształtują zwrot z powrotem. Nieokreślony ( `search=*` ), bez kryteriów dopasowania i przy użyciu parametrów wartości null lub domyślnych, zapytanie jest wykonywane dla wszystkich pól, które można przeszukiwać jako operacje wyszukiwania pełnotekstowego, zwracając nieoceniony zestaw wyników w dowolnej kolejności.

Poniższy przykład to zapytanie reprezentatywne skonstruowane w [interfejsie API REST](/rest/api/searchservice/search-documents). Ten przykład wskazuje na [indeks demonstracyjny hoteli](search-get-started-portal.md) i zawiera wspólne parametry, dzięki czemu można uzyskać pomysł dotyczący wyglądu zapytania.

```
{
    "queryType": "simple" 
    "search": "+New York +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

+ **`queryType`** Ustawia parser, który jest [domyślnym prostym analizatorem zapytań](search-query-simple-examples.md) (optymalnym dla wyszukiwania pełnotekstowego) lub [pełnym parserem zapytań Lucene](search-query-lucene-examples.md) , używanym do zaawansowanych konstrukcji zapytań, takich jak wyrażenia regularne, wyszukiwanie w sąsiedztwie, rozmyte i wieloznaczne wyszukiwanie, aby określić nazwę.

+ **`search`** zawiera kryteria dopasowywania, zwykle całe warunki lub frazy, ale często do nich dołączone są operatory logiczne. Pojedyncze warunki autonomiczne to zapytania *warunkowe* . Zapytania o wiele części zawarte w cudzysłowie są zapytaniami *fraz* . Wyszukiwanie może być niezdefiniowane, jak w **`search=*`** , ale bez kryteriów, które są zgodne, zestaw wyników składa się z arbitralnie wybranych dokumentów.

+ **`searchFields`** ogranicza wykonywanie zapytania do określonych pól. Każde pole, które jest przypisane do *wyszukiwania* w schemacie indeksu, jest kandydatem dla tego parametru.

Odpowiedzi są również dostosowane do parametrów dołączanych do zapytania:

+ **`select`** określa pola, które mają zostać zwrócone w odpowiedzi. W instrukcji SELECT można używać tylko pól oznaczonych jako możliwe do *pobierania* w indeksie.

+ **`top`** Zwraca określoną liczbę najlepiej pasujących dokumentów. W tym przykładzie zwracane są tylko 10 trafień. Aby wyświetlić wyniki, można użyć klauzuli TOP i Skip (niepokazywany).

+ **`count`** informuje o tym, ile dokumentów w całym indeksie jest zgodnych, które mogą być większe niż te, które są zwracane. 

+ **`orderby`** jest używany, jeśli chcesz sortować wyniki według wartości, takiej jak Klasyfikacja lub lokalizacja. W przeciwnym razie wartość domyślna to użycie oceny przydatności do rangi wyników.

Na platformie Azure Wyszukiwanie poznawcze wykonywanie zapytania jest zawsze w odniesieniu do jednego indeksu, uwierzytelniane przy użyciu klucza API-Key dostarczonego w żądaniu. W pozostałych, oba są dostępne w nagłówkach żądania.

### <a name="how-to-run-this-query"></a>Jak uruchomić to zapytanie

Przed zapisaniem dowolnego kodu można użyć narzędzi zapytania, aby poznać składnię i eksperymentować z innymi parametrami. Najszybszym podejściem jest wbudowane narzędzie portalu, [Eksplorator wyszukiwania](search-explorer.md).

Jeśli wykonano ten [Przewodnik Szybki Start, aby utworzyć indeks demonstracyjny hoteli](search-get-started-portal.md), można wkleić ten ciąg zapytania do paska wyszukiwania Eksploratora, aby uruchomić pierwsze zapytanie: `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Jak operacje zapytań są włączane przez indeks

Projekt indeksu i projekt zapytania są ściśle powiązane z platformą Azure Wyszukiwanie poznawcze. Podstawowym faktem, że wiadomo, że *schemat indeksu* , z atrybutami każdego pola, określa rodzaj zapytania, które można skompilować. 

Atrybuty indeksu w polu ustawiają dozwolone operacje — czy pole można *wyszukiwać* w indeksie, *pobrać* z wyników, do *sortowania* , *filtrować* i tak dalej. W przykładowym ciągu zapytania `"$orderby": "Rating"` działa tylko, ponieważ pole Rating jest oznaczone jako do *sortowania* w schemacie indeksu. 

![Definicja indeksu dla przykładu hotelu](./media/search-query-overview/hotel-sample-index-definition.png "Definicja indeksu dla przykładu hotelu")

Powyższy zrzut ekranu jest częściową listą atrybutów indeksu dla przykładu hoteli. Cały schemat indeksu można wyświetlić w portalu. Aby uzyskać więcej informacji na temat atrybutów indeksu, zobacz [create index REST API](/rest/api/searchservice/create-index).

> [!Note]
> Niektóre funkcje zapytań są włączone na poziomie indeksu, a nie na podstawie poszczególnych pól. Te możliwości obejmują: [mapy synonimów](search-synonyms.md), [Niestandardowe analizatory](index-add-custom-analyzers.md), [konstrukcje sugerujące (dla autouzupełniania i sugerowanych zapytań)](index-add-suggesters.md), [logika oceniania dla wyników rankingu](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Elementy żądania zapytania

Zapytania są zawsze kierowane w pojedynczym indeksie. Nie można przyłączyć indeksów ani tworzyć niestandardowych lub tymczasowych struktur danych jako obiektów docelowych zapytań. 

Wymagane elementy w żądaniu zapytania obejmują następujące składniki:

+ Punkt końcowy usługi i kolekcja dokumentów indeksu, wyrażone jako adres URL zawierający stałe i zdefiniowane przez użytkownika składniki: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (Tylko REST) jest konieczne, ponieważ wiele wersji interfejsu API jest dostępnych przez cały czas. 
+ **`api-key`** , zapytanie lub klucz API-Key, uwierzytelnia żądanie do usługi.
+ **`queryType`** — prosta lub pełna, która może zostać pominięta, jeśli używasz wbudowanej domyślnej składni prostej.
+ **`search`** lub **`filter`** zawiera kryteria dopasowania, które można określić, jeśli chcesz przeprowadzić puste wyszukiwanie. Oba typy zapytań są omówione w sekcji prostego analizatora, ale nawet zapytania zaawansowane wymagają parametru wyszukiwania do przekazywania złożonych wyrażeń zapytania.

Wszystkie inne parametry wyszukiwania są opcjonalne. Aby zapoznać się z pełną listą atrybutów, zobacz [create index (REST)](/rest/api/searchservice/create-index). Aby lepiej poznać, jak parametry są używane podczas przetwarzania, zobacz [jak wyszukiwanie pełnotekstowe działa na platformie Azure wyszukiwanie poznawcze](search-lucene-query-architecture.md).

## <a name="choose-apis-and-tools"></a>Wybierz interfejsy API i narzędzia

W poniższej tabeli przedstawiono interfejsy API i oparte na narzędziach podejścia do przesyłania zapytań.

| Metodologia | Opis |
|-------------|-------------|
| [Eksplorator wyszukiwania (portal)](search-explorer.md) | Udostępnia pasek wyszukiwania i opcje dotyczące opcji indeks i wersja interfejsu API. Wyniki są zwracane jako dokumenty JSON. Zalecane do eksploracji, testowania i weryfikacji. <br/>[Dowiedz się więcej.](search-get-started-portal.md#query-index) | 
| [Ogłoś lub inne narzędzia REST](search-get-started-postman.md) | Narzędzia do testowania sieci Web to doskonały wybór w zakresie opracowywania wywołań REST. Interfejs API REST obsługuje wszystkie możliwe operacje na platformie Azure Wyszukiwanie poznawcze. W tym artykule dowiesz się, jak skonfigurować nagłówek i treść żądania HTTP w celu wysyłania żądań do usługi Azure Wyszukiwanie poznawcze.  |
| [SearchClient (.NET)](/dotnet/api/azure.search.documents.searchclient) | Klient, który może służyć do wykonywania zapytań względem indeksu Wyszukiwanie poznawcze platformy Azure.  <br/>[Dowiedz się więcej.](search-howto-dotnet-sdk.md)  |
| [Wyszukaj dokumenty (interfejs API REST)](/rest/api/searchservice/search-documents) | Pobieranie lub OGŁASZAnie metod na indeksie przy użyciu parametrów zapytania dla dodatkowych danych wejściowych.  |

## <a name="choose-a-parser-simple--full"></a>Wybierz parser: prosty | szczegółowe

Usługa Azure Wyszukiwanie poznawcze znajduje się na platformie Apache Lucene i umożliwia wybór między dwoma analizatorami zapytań służącymi do obsługi typowych i wyspecjalizowanych zapytań. Żądania przy użyciu prostego analizatora są formułowane przy użyciu [prostej składni zapytania](query-simple-syntax.md), wybranej jako wartości domyślnej dla jego szybkości i skuteczności w bezpłatnych zapytaniach tekstowych formularza. Ta składnia obsługuje wiele typowych operatorów wyszukiwania, w tym operatory AND, OR, NOT, phrase, sufiks i pierwszeństwo.

[Pełna składnia zapytań Lucene](query-Lucene-syntax.md#bkmk_syntax), którą można włączyć po dodaniu `queryType=full` żądania, uwidacznia powszechnie przyjęty i wyraźny język zapytań opracowany w ramach oprogramowania [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Pełna składnia rozszerza prostą składnię. Wszystkie zapytania zapisane dla prostej składni są uruchamiane w ramach pełnego analizatora Lucene. 

Poniższe przykłady ilustrują punkt: te same zapytania, ale z różnymi ustawieniami querytype, dają różne wyniki. W pierwszym zapytaniu polecenie `^3` After `historic` jest traktowane jako część wyszukiwanego terminu. Górny wynik tego zapytania to "Marquis plac & Suites", który ma *Ocean* w opisie

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

To samo zapytanie używające pełnego analizatora Lucene interpretuje `^3` jako Detonator długoterminowy jako pole. Przełączenie analizatorów zmienia rangę, z wynikami zawierającymi termin *historyczny* w górnej części.

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Typy zapytań

Usługa Azure Wyszukiwanie poznawcze obsługuje szeroką gamę typów zapytań. 

| Typ zapytania | Użycie | Przykłady i więcej informacji |
|------------|--------|-------------------------------|
| Wyszukiwanie tekstu w dowolnym formacie | Parametry wyszukiwania i parser| Wyszukiwanie pełnotekstowe skanuje jeden lub więcej terminów we wszystkich polach z *możliwością wyszukiwania* w indeksie i działa w taki sposób, aby aparat wyszukiwania, taki jak Google lub Bing, mógł działać. Przykład we wprowadzeniu jest wyszukiwaniem pełnotekstowym.<br/><br/>Wyszukiwanie pełnotekstowe jest poddawana analizie leksykalnej przy użyciu standardowego analizatora Lucene (domyślnie) w przypadku małych i średnich wyrazów, Usuń słowa Stop podobne do "". Wartość domyślną można zastąpić [analizatorami w językach innych niż angielski](index-add-language-analyzers.md#language-analyzer-list) lub [wyspecjalizowanymi analizatorami niezależny od](index-add-custom-analyzers.md#AnalyzerTable) , które modyfikują analizę leksykalną. Przykładem jest [słowo kluczowe](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) , które traktuje całą zawartość pola jako pojedynczy token. Jest to przydatne w przypadku danych, takich jak kody ZIP, identyfikatory i nazwy produktów. | 
| Wyszukiwanie filtrowane | [Wyrażenie filtru OData](query-odata-filter-orderby-syntax.md) i każdy parser | Zapytania filtrujące obliczają wyrażenie logiczne dla wszystkich pól z możliwością *filtrowania* w indeksie. W przeciwieństwie do wyszukiwania, zapytanie filtru dopasowuje dokładną zawartość pola, w tym uwzględnianie wielkości liter w polach ciągów. Inną różnicą jest to, że zapytania filtru są wyrażane w składni protokołu OData. <br/>[Przykład wyrażenia filtru](search-query-simple-examples.md#example-3-filter-queries) |
| Wyszukiwanie geograficzne | [Typ EDM. geographyPoint względem](/rest/api/searchservice/supported-data-types) w polu, wyrażenie filtru i parser | Współrzędne przechowywane w polu z obiektem EDM. geographyPoint względem są używane dla kontrolek wyszukiwania "Znajdź w pobliżu" lub "mapowania". <br/>[Przykład wyszukiwania geograficznego](search-query-simple-examples.md#example-5-geo-search)|
| Wyszukiwanie zakresu | wyrażenie filtru i prosty parser | Na platformie Azure Wyszukiwanie poznawcze zapytania zakresu są kompilowane przy użyciu parametru filtru. <br/>[Przykład filtru zakresu](search-query-simple-examples.md#example-4-range-filters) | 
| [Wyszukiwanie polowe](query-lucene-syntax.md#bkmk_fields) | Parametr wyszukiwania i pełny analizator składni | Utwórz złożone wyrażenie zapytania określające jedno pole. <br/>[Przykład wyszukiwania w polu](search-query-lucene-examples.md#example-2-fielded-search) |
| [Wyszukiwanie rozmyte](query-lucene-syntax.md#bkmk_fuzzy) | Parametr wyszukiwania i pełny analizator składni | Dopasowuje się do warunków mających podobną konstrukcję lub pisownię. <br/>[Przykład wyszukiwania rozmytego](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [Wyszukiwanie w sąsiedztwie](query-lucene-syntax.md#bkmk_proximity) | Parametr wyszukiwania i pełny analizator składni | Znajduje terminy, które są blisko siebie w dokumencie. <br/>[Przykład wyszukiwania w sąsiedztwie](search-query-lucene-examples.md#example-4-proximity-search) |
| [zwiększenie warunków](query-lucene-syntax.md#bkmk_termboost) | Parametr wyszukiwania i pełny analizator składni | Określa wyższy poziom dokumentu, jeśli zawiera on podwyższony termin względem innych, które nie są. <br/>[Przykład zwiększania warunków](search-query-lucene-examples.md#example-5-term-boosting) |
| [Wyszukiwanie wyrażeń regularnych](query-lucene-syntax.md#bkmk_regex) | Parametr wyszukiwania i pełny analizator składni | Dopasowuje się w oparciu o zawartość wyrażenia regularnego. <br/>[Przykład wyrażenia regularnego](search-query-lucene-examples.md#example-6-regex) |
|  [Wyszukiwanie przy użyciu symboli wieloznacznych lub prefiksów](query-lucene-syntax.md#bkmk_wildcard) | Parametr wyszukiwania i pełny analizator składni | Dopasowuje się na podstawie prefiksu i tyldy ( `~` ) lub pojedynczego znaku ( `?` ). <br/>[Przykład wyszukiwania symboli wieloznacznych](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Zarządzanie wynikami wyszukiwania 

Wyniki zapytania są przesyłane strumieniowo jako dokumenty JSON w interfejsie API REST, ale jeśli korzystasz z interfejsów API platformy .NET, Serializacja jest wbudowana. Wyniki można kształtować przez ustawienie parametrów zapytania, wybierając określone pola dla odpowiedzi.

Parametry zapytania mogą służyć do tworzenia struktury zestawu wyników w następujący sposób:

+ Ograniczanie lub przetwarzanie wsadowe liczby dokumentów w wynikach (domyślnie 50)
+ Wybieranie pól do uwzględnienia w wynikach
+ Ustawianie kolejności sortowania
+ Dodawanie świateł trafień, aby zwrócić uwagę na pasujące warunki w treści wyników wyszukiwania

### <a name="tips-for-unexpected-results"></a>Porady dotyczące nieoczekiwanych wyników

Czasami substancja, a nie struktura wyników, nie są oczekiwane. Gdy wyniki zapytania nie są oczekiwane, możesz spróbować wykonać te modyfikacje kwerendy, aby zobaczyć, czy rezultaty rosną:

+ Zmień **`searchMode=any`** (domyślnie), aby **`searchMode=all`** wymagać dopasowania wszystkich kryteriów zamiast kryteriów. Jest to szczególnie prawdziwe, gdy operatory logiczne są uwzględnione w zapytaniu.

+ Zmień technikę zapytania, jeśli konieczne jest przeprowadzenie analizy tekstu lub leksykalnia, ale typ zapytania wyklucza przetwarzanie lingwistyczne. W wyszukiwaniu pełnotekstowym tekst lub analiza leksykalna są Autokorekty w przypadku błędów pisowni, formularzy wyrazów z pojedynczą plural, a nawet nieregularnych czasowników lub rzeczowników. W przypadku niektórych zapytań, takich jak rozmyte lub wyszukiwanie przy użyciu symboli wieloznacznych, analiza leksykalna nie jest częścią potoku analizy zapytania. W przypadku niektórych scenariuszy wyrażenia regularne zostały użyte jako obejście. 

### <a name="paging-results"></a>Stronicowanie wyników
Usługa Azure Wyszukiwanie poznawcze ułatwia implementowanie stronicowania wyników wyszukiwania. Korzystając z **`top`** parametrów i **`skip`** , można bezproblemowo wystawiać żądania wyszukiwania, które umożliwiają otrzymanie całkowitego zestawu wyników wyszukiwania w sposób umożliwiający zarządzanie, uporządkowane podzestawy, które łatwo umożliwiają dobre metody interfejsu użytkownika wyszukiwania. Razem z mniejszymi podzbiorami wyników można również odbierać liczbę dokumentów w całym zbiorze wyników wyszukiwania.

Więcej informacji na temat wyników wyszukiwania stronicowania można znaleźć w artykule [jak wyszukiwać wyniki wyszukiwania w usłudze Azure wyszukiwanie poznawcze](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Porządkowanie wyników
Podczas otrzymywania wyników dla zapytania wyszukiwania można zażądać, aby usługa Azure Wyszukiwanie poznawcze służyła wyniki uporządkowane według wartości w określonym polu. Domyślnie usługa Azure Wyszukiwanie poznawcze porządkuje wyniki wyszukiwania na podstawie rangi wyniku wyszukiwania każdego dokumentu, który pochodzi od [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Jeśli chcesz, aby usługa Azure Wyszukiwanie poznawcze zwracała wyniki uporządkowane według wartości innej niż wynik wyszukiwania, możesz użyć **`orderby`** parametru wyszukiwania. Można określić wartość **`orderby`** parametru, aby uwzględnić nazwy pól i wywołania [**`geo.distance()` funkcji**](query-odata-filter-orderby-syntax.md) dla wartości geoprzestrzennych. Po każdym wyrażeniu można następować, `asc` Aby wskazać, że wyniki są żądane w kolejności rosnącej, i **`desc`** wskazywać, że wyniki są żądane w kolejności malejącej. Domyślnie jest stosowana kolejność rosnąca.


### <a name="hit-highlighting"></a>Wyróżnianie trafień
Na platformie Azure Wyszukiwanie poznawcze naciskanie dokładnej części wyników wyszukiwania, które pasują do zapytania wyszukiwania, jest łatwe przy użyciu **`highlight`** **`highlightPreTag`** parametrów, i **`highlightPostTag`** . Możesz określić, które pola z *możliwością wyszukiwania* mają być wyróżnione dopasowanym tekstem, a także określić dokładne znaczniki ciągu do dołączenia do początku i końca dopasowanego tekstu zwracanego przez usługę Azure wyszukiwanie poznawcze.

## <a name="see-also"></a>Zobacz także

+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Wyszukiwanie poznawcze (architektura analizy zapytań)](search-lucene-query-architecture.md)
+ [Eksplorator wyszukiwania](search-explorer.md)
+ [Jak wykonywać zapytania w programie .NET](./search-get-started-dotnet.md)
+ [Jak wykonywać zapytania w usłudze REST](./search-get-started-powershell.md)