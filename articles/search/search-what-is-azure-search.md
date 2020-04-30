---
title: Wprowadzenie do wyszukiwania poznawczego na platformie Azure
titleSuffix: Azure Cognitive Search
description: Azure Wyszukiwanie poznawcze to w pełni zarządzana usługa wyszukiwania w chmurze firmy Microsoft. Odczytuj opisy funkcji, przepływ pracy deweloperskiej, porównania z innymi produktami wyszukiwania firmy Microsoft i jak zacząć pracę.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 01/06/2020
ms.openlocfilehash: fee7c8eb73fe0bb7c9fd0bd9de9aa57bd8c40215
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77580655"
---
# <a name="what-is-azure-cognitive-search"></a>Co to jest wyszukiwanie poznawcze na platformie Azure?

Usługa Azure Wyszukiwanie poznawcze ([znana wcześniej jako "Azure Search"](whats-new.md#new-service-name)) to rozwiązanie w chmurze typu "wyszukiwanie jako usługa", które udostępnia deweloperom interfejsy API i narzędzia umożliwiające dodawanie bogatego środowiska wyszukiwania za pośrednictwem prywatnej, niejednorodnej zawartości w aplikacjach sieci Web, mobilnych i firmowych. Twój kod lub narzędzie wywołuje pozyskiwanie danych (indeksowanie) w celu utworzenia i załadowania indeksu. Opcjonalnie możesz dodać umiejętności poznawcze, aby zastosować procesy AI podczas indeksowania. Takie działanie może dodawać nowe informacje i struktury przydatne w przypadku wyszukiwania i innych scenariuszy.

Po drugiej stronie usługi kod aplikacji wystawia żądania zapytań i obsługuje odpowiedzi. Środowisko wyszukiwania jest zdefiniowane w kliencie przy użyciu funkcji z usługi Azure Wyszukiwanie poznawcze, z wykonywaniem zapytań w odniesieniu do utrwalonego indeksu tworzonego, własnego i przechowywanego w usłudze.

![Architektura usługi Azure Wyszukiwanie poznawcze](media/search-what-is-azure-search/azure-search-diagram.svg "Architektura usługi Azure Wyszukiwanie poznawcze")

Funkcje są uwidaczniane za pośrednictwem prostego [interfejsu API REST](/rest/api/searchservice/) lub [zestawu SDK platformy .NET](search-howto-dotnet-sdk.md), które pozwalają zamaskować złożoność związaną z używaniem pobierania informacji. Oprócz interfejsów API witryna Azure Portal udostępnia funkcje administrowania i zarządzania zawartością oraz narzędzia służące do tworzenia prototypów i wykonywania zapytań względem indeksów. Ponieważ usługa ta działa w chmurze, zarządzaniem infrastrukturą i dostępnością zajmuje się firma Microsoft.

## <a name="when-to-use-azure-cognitive-search"></a>Kiedy używać usługi Azure Wyszukiwanie poznawcze

Wyszukiwanie poznawcze platformy Azure są odpowiednie dla następujących scenariuszy aplikacji:

+ Konsolidowanie heterogenicznych typów zawartości do prywatnego, pojedynczego, wyszukiwania w indeksie. Zapytania są zawsze nad indeksem utworzonym i ładowanym do dokumentów, a indeks zawsze znajduje się w chmurze w usłudze Azure Wyszukiwanie poznawcze. Można wypełnić indeks strumieniami dokumentów JSON z dowolnego źródła lub platformy. Alternatywnie, w przypadku zawartości źródłowej na platformie Azure, można użyć *indeksatora* do ściągania danych do indeksu. Definicja indeksu i zarządzanie/własność są kluczowym powodem korzystania z usługi Azure Wyszukiwanie poznawcze.

+ Nieprzetworzona zawartość to duże, nierozróżniane tekst, pliki obrazów lub pliki aplikacji, takie jak typy zawartości pakietu Office w źródle danych platformy Azure, takie jak Azure Blob Storage lub Cosmos DB. Podczas indeksowania można stosować umiejętności poznawcze w celu dodania struktury lub wyodrębnienia znaczenia z plików obrazów i aplikacji.

+ Łatwa implementacja funkcji związanych z wyszukiwaniem. Interfejsy API usługi Azure Wyszukiwanie poznawcze upraszczają konstruowanie zapytań, nawigację aspektową, filtry (w tym wyszukiwanie geoprzestrzenne), mapowanie synonimów, zapytania typeahead i dostrajanie istotności. Korzystając z wbudowanych funkcji, można zaspokoić oczekiwania użytkowników końcowych w przypadku wyszukiwania podobnego do komercyjnych aparatów wyszukiwania w sieci Web.

+ Indeksowanie tekstu bez struktury lub Wyodrębnianie tekstu i informacji z plików obrazów. Funkcja [wzbogacania AI](cognitive-search-concept-intro.md) systemu Azure wyszukiwanie poznawcze dodaje przetwarzanie AI do potoku indeksowania. Niektóre typowe przypadki użycia obejmują OCR przez zeskanowany dokument, rozpoznawanie jednostek i wyodrębnianie kluczowych fraz w przypadku dużych dokumentów, wykrywania języka i tłumaczenia tekstu oraz analizy tonacji.

+ Wymagania lingwistyczne spełnione przy użyciu niestandardowych i analizatorów języka platformy Azure Wyszukiwanie poznawcze. Jeśli masz zawartość w wersji innej niż angielska, platforma Azure Wyszukiwanie poznawcze obsługuje zarówno analizatory Lucene, jak i Microsoft Natural Language. Można również skonfigurować analizatory do osiągnięcia wyspecjalizowanego przetwarzania nieprzetworzonych zawartości, takich jak filtrowanie znaków diakrytycznych.

<a name="feature-drilldown"></a>

## <a name="feature-descriptions"></a>Opisy funkcji

| Podstawowe&nbsp;wyszukiwanie&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Funkcje |
|-------------------|----------|
|Wyszukiwanie tekstowe w dowolnej postaci | [**Wyszukiwanie pełnotekstowe**](search-lucene-query-architecture.md) jest podstawowym przypadkiem użycia dla większości aplikacji opartych na wyszukiwaniu. Zapytania można formułować za pomocą obsługiwanej składni. <br/><br/>[**Prosta składnia zapytań**](query-simple-syntax.md) zawiera operatory logiczne, operatory wyszukiwania fraz, operatory sufiksów oraz operatory pierwszeństwa.<br/><br/>[**Składnia zapytań Lucene**](query-lucene-syntax.md) obejmuje wszystkie operacje prostej składni rozszerzone o wyszukiwanie rozmyte, wyszukiwanie w sąsiedztwie, promowanie terminów i wyrażenia regularne.|
| Trafność | [**Proste ocenianie**](index-add-scoring-profiles.md) to kluczowa korzyść dla wyszukiwanie poznawcze platformy Azure. Profile oceniania służą do modelowania trafności jako funkcji wartości w samych dokumentach. Na przykład nowsze produkty lub produkty o obniżonej cenie mogą być wyświetlane na początku wyników wyszukiwania. Do tworzenia profilów oceniania można również używać tagów spersonalizowanej oceny opartych na preferencjach klientów, śledzonych i przechowywanych oddzielnie. |
| Wyszukiwanie geograficzne | Usługa Azure Wyszukiwanie poznawcze przetwarza, filtruje i wyświetla lokalizacje geograficzne. Pozwala ona użytkownikom eksplorować dane na podstawie zbliżenia wyniku wyszukiwania do lokalizacji fizycznej. Aby dowiedzieć się więcej, [obejrzyj ten film](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) lub [zapoznaj się z przykładem](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). |
| Filtry i aspekty | [**Nawigacja aspektowa**](search-faceted-navigation.md) jest włączana za pomocą jednego parametru zapytania. Usługa Azure Wyszukiwanie poznawcze zwraca strukturę nawigacji aspektowej, której można użyć jako kodu listy kategorii dla samodzielnego filtrowania (na przykład w celu filtrowania elementów katalogu według ceny lub zakresu lub marki). <br/><br/> [**Filtry**](query-odata-filter-orderby-syntax.md) umożliwiają integrowanie nawigacji aspektowej z interfejsem użytkownika aplikacji, rozbudowywanie zapytań oraz filtrowanie na podstawie kryteriów określonych przez użytkownika lub dewelopera. Do tworzenie filtrów służy składnia OData. |
| Funkcje środowiska użytkownika | [**Funkcja autouzupełniania**](search-autocomplete-tutorial.md) może być włączona dla zapytań typu "w przód" na pasku wyszukiwania. <br/><br/>[**Sugestie dotyczące wyszukiwania**](https://docs.microsoft.com/rest/api/searchservice/suggesters) działają również na podstawie tekstu częściowego wpisanego w pasku wyszukiwania, ale wyniki są faktycznymi dokumentami w indeksie, a nie terminami zapytania. <br/><br/>[**Synonimy**](search-synonyms.md) umożliwiają kojarzenie równoważnych terminów, co niejawnie rozszerza zakres zapytania — bez potrzeby podawania terminów alternatywnych przez użytkownika. <br/><br/>[**Wyróżnianie trafień**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) umożliwia zastosowanie formatowania tekstu do pasującego słowa kluczowego w wynikach wyszukiwania. Można wybrać pola, które zwracają wyróżnione fragmenty.<br/><br/>[**Sortowanie**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) jest dostępne dla wielu pól za pośrednictwem schematu indeksu. Można je przełączać w czasie wykonywania zapytania za pomocą pojedynczego parametru wyszukiwania.<br/><br/> [**Stronicowanie**](search-pagination-page-layout.md) i ograniczanie wyników wyszukiwania jest proste dzięki precyzyjnej kontroli oferowanej przez usługę Azure wyszukiwanie poznawcze w wyniku wyszukiwania.  <br/><br/>|

| Wzbogacanie AI&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;       | Funkcje |
|-------------------|----------|
|Przetwarzanie AI podczas indeksowania | [**Wzbogacenie AI**](cognitive-search-concept-intro.md) do analizy obrazów i tekstu można zastosować do potoku indeksowania, aby wyodrębnić informacje tekstowe z nieprzetworzonej zawartości. Kilka przykładów [wbudowanych możliwości](cognitive-search-predefined-skills.md) to optyczne rozpoznawanie znaków (które pozwala na przeszukiwanie plików JPEG), rozpoznawanie jednostek (identyfikowanie organizacji, nazwy lub lokalizacji) oraz rozpoznawanie kluczowych fraz. W celu dołączenia do potoku można również użyć [możliwości kodu niestandardowego](cognitive-search-create-custom-skill-example.md). |
| Przechowywanie wzbogaconej zawartości do analizy i zużycia w scenariuszach niezwiązanych z wyszukiwaniem | [**Magazyn wiedzy (wersja zapoznawcza)**](knowledge-store-concept-intro.md) to rozszerzenie indeksowania opartego na formacie AI. Za pomocą usługi Azure Storage jako zaplecza można zapisywać wzbogacania utworzone podczas indeksowania. Te artefakty mogą służyć do projektowania lepszych umiejętności lub tworzenia kształtu i struktury z amorphous lub niejednoznacznych danych. Można tworzyć projekcje tych struktur, które są przeznaczone dla konkretnych obciążeń lub użytkowników. Można również bezpośrednio analizować wyodrębnione dane lub ładować je do innych aplikacji.<br/><br/> |
| Zawartość buforowana | [**Przyrostowe wzbogacanie (wersja zapoznawcza)**](cognitive-search-incremental-indexing-conceptual.md) ogranicza przetwarzanie do tylko dokumentów, które są zmieniane przez specyficzną edycję potoku, przy użyciu zawartości z pamięci podręcznej dla części potoku, które nie ulegają zmianie. |

| Importowanie&nbsp;/indeksowanie danych | Funkcje |
|----------------------------------|----------|
| Źródła danych | Usługa Azure Wyszukiwanie poznawcze Indexes akceptuje dane ze wszystkich źródeł, pod warunkiem, że jest ona przesyłana jako struktura danych JSON. <br/><br/> [**Indeksatory**](search-indexer-overview.md) automatyzują pozyskiwanie danych dla obsługiwanych źródeł danych platformy Azure i obsługują serializację JSON. Łączenie się z usługą [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md)lub [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) w celu wyodrębnienia zawartości z możliwością wyszukiwania w podstawowych magazynach danych. Indeksatory obiektów blob platformy Azure mogą przeprowadzać *„łamanie zabezpieczeń dokumentów”* w celu [wyodrębniania tekstu z głównych formatów plików](search-howto-indexing-azure-blob-storage.md), na przykład plików PDF i HTML oraz dokumentów pakietu Microsoft Office. |
| Hierarchiczne i zagnieżdżone struktury danych | [**Złożone typy**](search-howto-complex-data-types.md) i Kolekcje umożliwiają modelowanie praktycznie dowolnego typu struktury JSON jako indeksu wyszukiwanie poznawcze platformy Azure. Kardynalność "jeden do wielu" i wiele-do-wielu może być wyrażona natywnie za poorednictwem kolekcji, typów złożonych i kolekcji typów złożonych.|
| Analiza lingwistyczna | Analizatory to składniki używane do przetwarzania tekstu podczas operacji indeksowania i wyszukiwania. Są dwa typy analizatorów. <br/><br/>[**Niestandardowe analizatory leksykalne**](index-add-custom-analyzers.md) są używane w złożonych zapytaniach wyszukiwania korzystających z dopasowania fonetycznego i wyrażeń regularnych. <br/><br/>[**Analizatory języków**](index-add-language-analyzers.md), opracowane przez firmę Lucene lub Microsoft, są używane do inteligentnej obsługi struktur lingwistycznych, m.in. czasów gramatycznych, rodzajów i rzeczowników z nieregularną liczbą mnogą (na przykład „mouse” i „mice” w języku angielskim), a także rozkładania i dzielenia wyrazów (w przypadku języków, w których nie używa się odstępów). <br/><br/>|


| Poziom&nbsp;platformy&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Funkcje |
|-------------------|----------|
| Narzędzia służące do tworzenia prototypów i przeprowadzania inspekcji | Portal udostępnia [**kreatora importowania danych**](search-import-data-portal.md) umożliwiającego konfigurowanie indeksatorów, projektanta indeksów służącego do wdrożenia indeksu oraz [**eksploratora wyszukiwania**](search-explorer.md), który pozwala testować zapytania i dostosowywać profile oceniania. Można również otworzyć dowolny indeks, aby wyświetlić jego schemat. |
| Monitorowanie i diagnostyka | [**Włącz funkcje monitorowania**](search-monitor-usage.md) , aby przekroczyć metryki na pierwszy rzut oka, które są zawsze widoczne w portalu. Metryki dotyczące liczby zapytań na sekundę, opóźnienia i ograniczania są przechwytywane i udostępniane na stronach portalu bez konieczności konfigurowania dodatkowych ustawień.|
| Szyfrowanie po stronie serwera | [**Szyfrowanie zarządzane przez firmę Microsoft — w spoczynku**](search-security-overview.md#encrypted-transmission-and-storage) jest wbudowane w wewnętrzną warstwę magazynu i jest nieodwołalne. Opcjonalnie można uzupełnić domyślne szyfrowanie przy użyciu [**kluczy szyfrowania zarządzanych przez klienta**](search-security-manage-encryption-keys.md). Klucze tworzone i zarządzane w Azure Key Vault są używane do szyfrowania indeksów i map synonimów w usłudze Azure Wyszukiwanie poznawcze. |
| Infrastruktura | **Platforma o wysokiej dostępności** zapewnia niezawodne działanie usługi wyszukiwania. Po poprawnym skalowaniu [usługa Azure wyszukiwanie poznawcze oferuje umowę SLA na 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).<br/><br/> W **pełni zarządzane i skalowalne** jako kompleksowe rozwiązanie usługa Azure wyszukiwanie poznawcze wymaga bezwzględnego zarządzania infrastrukturą. Skalowanie w dwóch wymiarach pozwala dostosować usługę do swoich potrzeb, na przykład zwiększyć miejsce do magazynowania dokumentów czy zapewnić obsługę większych obciążeń zapytaniami.<br/><br/>|

## <a name="how-to-use-azure-cognitive-search"></a>Jak korzystać z usługi Azure Wyszukiwanie poznawcze
### <a name="step-1-provision-service"></a>Krok 1. Aprowizowanie usługi
Usługę Azure Wyszukiwanie poznawcze można zainicjować w [Azure Portal](https://portal.azure.com/) lub za pomocą [interfejsu API zarządzania zasobami platformy Azure](/rest/api/searchmanagement/). Możesz wybrać wersję bezpłatną, współużytkowaną z innymi subskrybentami, lub [warstwę płatną](https://azure.microsoft.com/pricing/details/search/), która pozwala rezerwować zasoby używane przez konkretną usługę. W warstwach płatnych dostępne są dwa wymiary skalowania usługi: 

- Dodawanie replik w celu zwiększenia pojemności umożliwiającej obsługę dużych obciążeń zapytaniami.   
- Dodawanie partycji w celu zwiększenia magazynu dla dokumentów. 

Dzięki oddzielnej obsłudze magazynu dokumentów i przepływności zapytań możesz kalibrować zasoby na podstawie wymagań środowiska produkcyjnego.

### <a name="step-2-create-index"></a>Krok 2. Tworzenie indeksu
Aby można było przekazać zawartość do przeszukiwania, należy najpierw zdefiniować indeks Wyszukiwanie poznawcze platformy Azure. Indeks przypomina tabelę bazy danych, która przechowuje dane i może akceptować zapytania wyszukiwania. Musisz zdefiniować schemat indeksu odzwierciedlający strukturę dokumentów, które chcesz przeszukiwać, podobnie jak w przypadku pól w bazie danych.

Schemat możesz utworzyć w witrynie Azure Portal lub programowo przy użyciu [zestawu SDK platformy .NET](search-howto-dotnet-sdk.md) lub [interfejsu API REST](/rest/api/searchservice/).

### <a name="step-3-load-data"></a>Krok 3. Ładowanie danych
Po zdefiniowaniu indeksu możesz zacząć przekazywać zawartość. Możesz użyć modelu wypychania lub modelu ściągania.

W modelu ściągania dane są pobierane z zewnętrznych źródeł danych. Jest on obsługiwany przez *indeksatory*, które usprawniają i automatyzują aspekty pozyskiwania danych, takie jak łączenie się z danymi oraz ich odczytywanie i serializowanie. [Indeksatory](/rest/api/searchservice/Indexer-operations) są dostępne dla usług Azure Cosmos DB, Azure SQL Database, Azure Blob Storage oraz programu SQL Server hostowanego na maszynie wirtualnej platformy Azure. Indeksator możesz skonfigurować na potrzeby zaplanowanego odświeżania danych lub odświeżania danych na żądanie.

Model wypychania jest oparty na zestawie SDK lub interfejsach API REST i umożliwia wysyłanie zaktualizowanych dokumentów do indeksu. Dane możesz wypychać praktycznie z każdego zestawu danych w formacie JSON. Aby uzyskać wskazówki dotyczące ładowania danych, zobacz [Dodawanie, aktualizowanie lub usuwanie dokumentów](/rest/api/searchservice/addupdate-or-delete-documents) lub [Jak używać zestawu SDK dla platformy .NET](search-howto-dotnet-sdk.md).

### <a name="step-4-search"></a>Krok 4. Wyszukiwanie
Po wypełnieniu indeksu możesz [wydać zapytania wyszukiwania](search-query-overview.md) do punktu końcowego usługi przy użyciu prostych żądań HTTP z [interfejsem API REST](/rest/api/searchservice/Search-Documents) lub [zestawem SDK platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations).

Przejdź do sekcji [Tworzenie pierwszej aplikacji wyszukiwania](tutorial-csharp-create-first-app.md) , aby skompilować, a następnie rozwiń stronę sieci Web, która zbiera dane wejściowe użytkownika i obsługuje wyniki. Można również użyć [programu Poster do interaktywnego wywołania REST](search-get-started-postman.md) lub wbudowanego [eksploratora wyszukiwania](search-explorer.md) w Azure Portal, aby zbadać istniejący indeks.

## <a name="how-it-compares"></a>Porównanie z innymi rozwiązaniami

Klienci często pytają, jak platforma Azure Wyszukiwanie poznawcze porównuje z innymi rozwiązaniami związanymi z wyszukiwaniem. Poniższa tabela zawiera podsumowanie podstawowych różnic.

| W porównaniu do | Podstawowe różnice |
|-------------|-----------------|
|Bing | [Interfejs API wyszukiwania w sieci Web Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) wyszukuje przesłane pasujące terminy w indeksach witryny Bing.com. Indeksy są tworzone na podstawie plików HTML i XML oraz innej zawartości internetowej pochodzącej z publicznych witryn. Na takiej samej zasadzie [Wyszukiwanie niestandardowe Bing](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) udostępnia tę samą przeszukiwarkę dla typów zawartości internetowej, ograniczoną do poszczególnych witryn.<br/><br/>Usługa Azure Wyszukiwanie poznawcze przeszukuje zdefiniowany przez siebie indeks, wypełniając posiadane dane i dokumenty, często z różnych źródeł. Usługa Azure Wyszukiwanie poznawcze ma możliwości przeszukiwarki dla niektórych źródeł danych za pomocą [indeksatorów](search-indexer-overview.md), ale można wypchnąć dowolny dokument JSON, który jest zgodny ze schematem indeksu, w pojedynczy, skonsolidowany zasób do przeszukiwania. |
|Wyszukiwanie w bazie danych | Wiele platform baz danych udostępnia wbudowane środowisko wyszukiwania. Platforma SQL Server oferuje [wyszukiwanie pełnotekstowe](https://docs.microsoft.com/sql/relational-databases/search/full-text-search). Usługa Cosmos DB i podobne technologie mają indeksy odpytywalne. W przypadku oceny produktów, które łączą funkcje wyszukiwania i magazynu, może być trudno wybrać najlepsze rozwiązanie. Wiele rozwiązań używa zarówno systemu DBMS dla magazynu, jak i usługi Azure Wyszukiwanie poznawcze na potrzeby wyspecjalizowanych funkcji wyszukiwania.<br/><br/>W porównaniu z wyszukiwaniem w systemie DBMS usługa Azure Wyszukiwanie poznawcze przechowuje zawartość z niejednorodnych źródeł i oferuje wyspecjalizowane funkcje przetwarzania tekstu, takie jak przetwarzanie tekstu z uwzględnieniem języka (Lematyzacja, formularze programu Word) w [językach 56](https://docs.microsoft.com/rest/api/searchservice/language-support). Obsługuje ona również autokorektę błędnie napisanych wyrazów, [synonimy](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations), [sugestie](https://docs.microsoft.com/rest/api/searchservice/suggestions), [formanty oceniania](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [aspekty](https://docs.microsoft.com/azure/search/search-filters-facets)i [tokenizację niestandardową](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search). [Aparat wyszukiwania pełnotekstowego](search-lucene-query-architecture.md) w usłudze Azure wyszukiwanie poznawcze jest oparty na usłudze Apache Lucene, w standardzie do pobrania informacji. Mimo że platforma Azure Wyszukiwanie poznawcze utrzymuje dane w postaci odwróconego indeksu, jest to rzadko zastępowane dla rzeczywistego magazynu danych. Więcej informacji znajduje się we [wpisie na forum](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>Użycie zasobów to kolejny etap w tej kategorii. Indeksowanie i niektóre operacje zapytań są często intensywne z obliczeniowego punktu widzenia. Przeniesienie wyszukiwania z DBMS do dedykowanego rozwiązania w chmurze pozwala zachować zasoby systemowe dla przetwarzania transakcyjnego. Dzięki temu można łatwo dostosować skalę do liczby zapytań.|
|Dedykowane rozwiązanie wyszukiwania | Jeśli przyjmiemy, że decydujesz się na dedykowane wyszukiwanie z pełnego zakresu funkcjonalności, ostateczne porównanie w oparciu o kategorie odbywa się miedzy rozwiązaniami lokalnymi a usługą w chmurze. Wiele technologii wyszukiwania umożliwia sterowanie indeksowaniem i potokami zapytań oraz dostęp do zaawansowanej składni zapytań i filtrów. Pozwalają one sterować rangą i trafnością, a także udostępniają funkcje samodzielnego i inteligentnego wyszukiwania. <br/><br/>Usługę w chmurze warto wybrać, jeśli potrzebne jest gotowe rozwiązanie z regulowaną skalą, które prawie nie wymaga konserwacji i nakładu pracy. <br/><br/>W ramach modelu chmury kilku dostawców oferuje porównywalne funkcje bazowe, obejmujące wyszukiwanie pełnotekstowe, wyszukiwanie geograficzne oraz obsługę określonego poziomu niejednoznaczności w danych wejściowych wyszukiwania. Zwykle korzysta się z [wyspecjalizowanej funkcji](#feature-drilldown) lub wybiera się łatwość użytkowania i ogólną prostotę interfejsów API, narzędzi i funkcji zarządzania. |

Dzięki dostawcom chmury usługa Azure Wyszukiwanie poznawcze jest najmocna w przypadku obciążeń wyszukiwania pełnotekstowego w magazynach zawartości i bazach danych na platformie Azure, w przypadku aplikacji, które korzystają głównie z wyszukiwania zarówno do pobierania informacji, jak i nawigowania po zawartości. 

Oto najważniejsze zalety:

+ Integracja danych platformy Azure (przeszukiwarki) w warstwie indeksowania
+ Witryna Azure Portal umożliwiająca centralne zarządzanie
+ Skalowalność i niezawodność platformy Azure oraz dostępność światowej klasy
+ Przetwarzanie danych pierwotnych w celu łatwiejszego wyszukiwania, w tym tekstu z obrazów lub znalezienie wzorców w zawartości bez struktury.
+ Analiza językowa i niestandardowa z analizatorami umożliwiającymi spójne wyszukiwanie pełnotekstowe w 56 językach
+ [Podstawowe funkcje, często używane w aplikacjach służących do wyszukiwania](#feature-drilldown): ocenianie, kategoryzowanie, sugestie, synonimy, wyszukiwanie geograficzne i inne.

> [!Note]
> Źródła danych spoza platformy Azure są w pełni obsługiwane, ale nie korzystają z indeksatorów, tylko metodologii wypychania wymagającej użycia większej ilości kodu. Korzystając z interfejsów API, można potokować dowolną kolekcję dokumentów JSON do indeksu Wyszukiwanie poznawcze platformy Azure.

Wśród naszych klientów mogą korzystać z szerokiego zakresu funkcji platformy Azure Wyszukiwanie poznawcze obejmują wykazy online, programy biznesowe i aplikacje do odnajdywania dokumentów.

## <a name="rest-api--net-sdk"></a>Interfejs API REST | Zestaw .NET SDK

Chociaż wiele zadań można wykonać w portalu, usługa Azure Wyszukiwanie poznawcze jest przeznaczona dla deweloperów, którzy chcą zintegrować funkcje wyszukiwania z istniejącymi aplikacjami. Dostępne są następujące interfejsy programowania.

|Platforma |Opis |
|-----|------------|
|[REST](/rest/api/searchservice/) | Polecenia HTTP obsługiwane przez dowolną platformę i język programowania, w tym Xamarin, Java i JavaScript|
|[Zestaw SDK .NET](search-howto-dotnet-sdk.md) | Otoka .NET dla interfejsu API REST umożliwia efektywne kodowanie w języku C# oraz w innych językach z kodem zarządzanym, przeznaczonych dla platformy .NET |

## <a name="free-trial"></a>Bezpłatna wersja próbna
Subskrybenci platformy Azure mogą [aprowizować usługę w warstwie Bezpłatna](search-create-service-portal.md).

Jeśli nie masz subskrypcji, możesz [otworzyć bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Otrzymasz środki umożliwiające wypróbowanie płatnych usług platformy Azure. Nawet po ich wyczerpaniu możesz zachować konto i korzystać z [bezpłatnych usług platformy Azure](https://azure.microsoft.com/free/). Karta kredytowa nie zostanie obciążona, chyba że jawnie zmienisz ustawienia i poprosisz o jej obciążenie.

Możesz też [aktywować korzyści subskrybenta MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) — w ramach subskrypcji MSDN co miesiąc otrzymasz kredyt, który można przeznaczyć na płatne usługi platformy Azure. 

## <a name="how-to-get-started"></a>Jak zacząć

1. Utwórz [bezpłatną usługę](search-create-service-portal.md). Wszystkie samouczki i przewodniki Szybki Start można ukończyć, korzystając z bezpłatnej usługi.

2. Wykonaj kroki opisane w [samouczku dotyczącym korzystania z wbudowanych narzędzi do indeksowania i tworzenia zapytań](search-get-started-portal.md). Pozwoli ci to zapoznać się z ważnymi pojęciami oraz informacjami udostępnianymi w portalu.

3. Rozpocznij pracę z kodem, korzystając z platformy .NET lub interfejsu API REST:

   + [Jak używać zestawu SDK dla platformy .NET](search-howto-dotnet-sdk.md) — przedstawia podstawowy przepływ pracy w kodzie zarządzanym.  
   + [Wprowadzenie do interfejsu API REST](https://github.com/Azure-Samples/search-rest-api-getting-started) — zawiera te same kroki, wykonywane za pomocą interfejsu API REST. Możesz również użyć tego przewodnika Szybki Start, aby wywołać interfejsy API REST z programu Poster lub programu Fiddler: [Poznaj interfejsy API REST platformy Azure wyszukiwanie poznawcze](search-get-started-postman.md).

## <a name="watch-this-video"></a>Obejrzyj ten film wideo

Wyszukiwarki to podstawowe instrumenty do pobierania informacji w internecie, aplikacjach mobilnych i firmowych magazynach danych. Usługa Azure Wyszukiwanie poznawcze udostępnia narzędzia do tworzenia środowiska wyszukiwania podobnego do tych w dużych komercyjnych witrynach sieci Web.

W tym 9-minutowym filmie wideo menedżer programu Liam Cavanagh przedstawia korzyści związane z integracją wyszukiwarki z aplikacją. Krótkie pokazy obejmują kluczowe funkcje platformy Azure Wyszukiwanie poznawcze i wygląd typowego przepływu pracy. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0–3 minuta — omówienie kluczowych funkcji i przypadków użycia.
+ 3–4 minuta — omówienie aprowizowania usługi. 
+ 4–6 minuta — omówienie kreatora importowania danych używanego do utworzenia indeksu na podstawie wbudowanego zestawu danych dotyczących nieruchomości.
+ 6–9 minuta — omówienie eksploratora wyszukiwania i różnych zapytań.
