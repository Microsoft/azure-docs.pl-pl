---
title: Typy zapytań
titleSuffix: Azure Cognitive Search
description: Dowiedz się więcej na temat typów zapytań obsługiwanych w Wyszukiwanie poznawcze, w tym swobodnego tekstu, filtru, autouzupełniania i sugestii, wyszukiwania geograficznego, zapytań systemowych i wyszukiwania dokumentów.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: d1ea2d0ba8ed5850e5d4cd9c06a0b016c4059ca7
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007861"
---
# <a name="query-types-in-azure-cognitive-search"></a>Typy zapytań w usłudze Azure Wyszukiwanie poznawcze

Na platformie Azure Wyszukiwanie poznawcze zapytanie jest pełną specyfikacją operacji rundy, z parametrami, które regulują wykonywanie zapytania, oraz parametrami, które kształtują zwrot z powrotem.

## <a name="elements-of-a-request"></a>Elementy żądania

Poniższy przykład to zapytanie reprezentatywne skonstruowane przy użyciu [interfejsu API REST dokumentów wyszukiwania](/rest/api/searchservice/search-documents). Ten przykład wskazuje na [indeks demonstracyjny hoteli](search-get-started-portal.md) i zawiera wspólne parametry, dzięki czemu można uzyskać pomysł dotyczący wyglądu zapytania.

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
{
    "queryType": "simple" 
    "search": "`New York` +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

Zapytania są zawsze kierowane do kolekcji dokumentów o pojedynczym indeksie. Nie można przyłączyć indeksów ani tworzyć niestandardowych lub tymczasowych struktur danych jako obiektów docelowych zapytań.

+ **`queryType`** Ustawia parser, który jest [domyślnym prostym analizatorem zapytań](search-query-simple-examples.md) (optymalnym dla wyszukiwania pełnotekstowego) lub [pełnym parserem zapytań Lucene](search-query-lucene-examples.md) , używanym do zaawansowanych konstrukcji zapytań, takich jak wyrażenia regularne, wyszukiwanie w sąsiedztwie, rozmyte i wieloznaczne wyszukiwanie, aby określić nazwę.

+ **`search`** zawiera kryteria dopasowywania, zwykle całe warunki lub frazy, ale często do nich dołączone są operatory logiczne. Pojedyncze warunki autonomiczne to zapytania *warunkowe* . Zapytania o wiele części zawarte w cudzysłowie są zapytaniami *fraz* . Wyszukiwanie może być niezdefiniowane, jak w **`search=*`** , ale bez kryteriów, które są zgodne, zestaw wyników składa się z arbitralnie wybranych dokumentów.

+ **`searchFields`** ogranicza wykonywanie zapytania do określonych pól. Każde pole, które jest przypisane do *wyszukiwania* w schemacie indeksu, jest kandydatem dla tego parametru.

Odpowiedzi są również dostosowane do parametrów dołączanych do zapytania:

+ **`select`** określa pola, które mają zostać zwrócone w odpowiedzi. W instrukcji SELECT można używać tylko pól oznaczonych jako możliwe do *pobierania* w indeksie.

+ **`top`** Zwraca określoną liczbę najlepiej pasujących dokumentów. W tym przykładzie zwracane są tylko 10 trafień. Aby wyświetlić wyniki, można użyć klauzuli TOP i Skip (niepokazywany).

+ **`count`** informuje o tym, ile dokumentów w całym indeksie jest zgodnych, które mogą być większe niż te, które są zwracane. 

+ **`orderby`** jest używany, jeśli chcesz sortować wyniki według wartości, takiej jak Klasyfikacja lub lokalizacja. W przeciwnym razie wartość domyślna to użycie oceny przydatności do rangi wyników.

> [!Tip]
> Przed zapisaniem dowolnego kodu można użyć narzędzi zapytania, aby poznać składnię i eksperymentować z innymi parametrami. Najszybszym podejściem jest wbudowane narzędzie portalu, [Eksplorator wyszukiwania](search-explorer.md).
>
> Jeśli wykonano ten [Przewodnik Szybki Start, aby utworzyć indeks demonstracyjny hoteli](search-get-started-portal.md), można wkleić ten ciąg zapytania do paska wyszukiwania Eksploratora, aby uruchomić pierwsze zapytanie: `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

### <a name="how-field-attributes-in-an-index-determine-query-behaviors"></a>Jak atrybuty pola w indeksie określają zachowania zapytań

Projekt indeksu i projekt zapytania są ściśle powiązane z platformą Azure Wyszukiwanie poznawcze. Podstawowym faktem, że wiadomo, że *schemat indeksu*, z atrybutami każdego pola, określa rodzaj zapytania, które można skompilować. 

Atrybuty indeksu w polu ustawiają dozwolone operacje — czy pole można *wyszukiwać* w indeksie, *pobrać* z wyników, do *sortowania*, *filtrować* i tak dalej. W przykładowym ciągu zapytania `"$orderby": "Rating"` działa tylko, ponieważ pole Rating jest oznaczone jako do *sortowania* w schemacie indeksu. 

![Definicja indeksu dla przykładu hotelu](./media/search-query-overview/hotel-sample-index-definition.png "Definicja indeksu dla przykładu hotelu")

Powyższy zrzut ekranu jest częściową listą atrybutów indeksu dla przykładu hoteli. Cały schemat indeksu można wyświetlić w portalu. Aby uzyskać więcej informacji na temat atrybutów indeksu, zobacz [create index REST API](/rest/api/searchservice/create-index).

> [!Note]
> Niektóre funkcje zapytań są włączone na poziomie indeksu, a nie na podstawie poszczególnych pól. Te możliwości obejmują: [mapy synonimów](search-synonyms.md), [Niestandardowe analizatory](index-add-custom-analyzers.md), [konstrukcje sugerujące (dla autouzupełniania i sugerowanych zapytań)](index-add-suggesters.md), [logika oceniania dla wyników rankingu](index-add-scoring-profiles.md).

## <a name="choose-a-parser-simple--full"></a>Wybierz parser: prosty | szczegółowe

Usługa Azure Wyszukiwanie poznawcze umożliwia wybór między dwoma parserami zapytań do obsługi typowych i wyspecjalizowanych zapytań. Żądania przy użyciu prostego analizatora są formułowane przy użyciu [prostej składni zapytania](query-simple-syntax.md), wybranej jako wartości domyślnej dla jego szybkości i skuteczności w bezpłatnych zapytaniach tekstowych formularza. Ta składnia obsługuje wiele typowych operatorów wyszukiwania, w tym operatory AND, OR, NOT, phrase, sufiks i pierwszeństwo.

[Pełna składnia zapytań Lucene](query-Lucene-syntax.md#bkmk_syntax), którą można włączyć po dodaniu `queryType=full` żądania, uwidacznia powszechnie przyjęty i wyraźny język zapytań opracowany w ramach oprogramowania [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Pełna składnia rozszerza prostą składnię. Wszystkie zapytania zapisane dla prostej składni są uruchamiane w ramach pełnego analizatora Lucene. 

Poniższe przykłady ilustrują punkt: te same zapytania, ale z różnymi ustawieniami querytype, dają różne wyniki. W pierwszym zapytaniu polecenie `^3` After `historic` jest traktowane jako część wyszukiwanego terminu. Górny wynik tego zapytania to "Marquis plac & Suites", który ma *Ocean* w opisie.

```http
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

To samo zapytanie używające pełnego analizatora Lucene interpretuje `^3` jako Detonator długoterminowy jako pole. Przełączenie analizatorów zmienia rangę, z wynikami zawierającymi termin *historyczny* w górnej części.

```http
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
| [Autouzupełnianie lub sugerowane wyniki](search-autocomplete-tutorial.md) | Autouzupełnianie lub parametr sugestii | Alternatywny formularz zapytania, który jest wykonywany w oparciu o częściowe ciągi w środowisku wyszukiwania w trakcie wpisywania. Możesz użyć autouzupełniania i sugestii razem lub oddzielnie. |
| [Wyszukiwanie rozmyte](query-lucene-syntax.md#bkmk_fuzzy) | Parametr wyszukiwania i pełny analizator składni | Dopasowuje się do warunków mających podobną konstrukcję lub pisownię. <br/>[Przykład wyszukiwania rozmytego](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [Wyszukiwanie w sąsiedztwie](query-lucene-syntax.md#bkmk_proximity) | Parametr wyszukiwania i pełny analizator składni | Znajduje terminy, które są blisko siebie w dokumencie. <br/>[Przykład wyszukiwania w sąsiedztwie](search-query-lucene-examples.md#example-4-proximity-search) |
| [zwiększenie warunków](query-lucene-syntax.md#bkmk_termboost) | Parametr wyszukiwania i pełny analizator składni | Określa wyższy poziom dokumentu, jeśli zawiera on podwyższony termin względem innych, które nie są. <br/>[Przykład zwiększania warunków](search-query-lucene-examples.md#example-5-term-boosting) |
| [Wyszukiwanie wyrażeń regularnych](query-lucene-syntax.md#bkmk_regex) | Parametr wyszukiwania i pełny analizator składni | Dopasowuje się w oparciu o zawartość wyrażenia regularnego. <br/>[Przykład wyrażenia regularnego](search-query-lucene-examples.md#example-6-regex) |
|  [Wyszukiwanie przy użyciu symboli wieloznacznych lub prefiksów](query-lucene-syntax.md#bkmk_wildcard) | Parametr wyszukiwania i pełny analizator składni | Dopasowuje się na podstawie prefiksu i tyldy ( `~` ) lub pojedynczego znaku ( `?` ). <br/>[Przykład wyszukiwania symboli wieloznacznych](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="next-steps"></a>Następne kroki

Skorzystaj z portalu lub innego narzędzia, takiego jak Poster lub Visual Studio Code lub jeden z zestawów SDK, aby eksplorować zapytania w bardziej szczegółowy sposób. Następujące linki umożliwią rozpoczęcie pracy.

+ [Eksplorator wyszukiwania](search-explorer.md)
+ [Jak wykonywać zapytania w programie .NET](./search-get-started-dotnet.md)
+ [Jak wykonywać zapytania w usłudze REST](./search-get-started-powershell.md)