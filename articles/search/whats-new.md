---
title: Co nowego w usłudze Azure Wyszukiwanie poznawcze
description: Anonse nowych i ulepszonych funkcji, w tym zmiana nazwy usługi Azure Search na platformę Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 0df3c6b1851767616111308ad0bacce227ba1a83
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91948905"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Co nowego w usłudze Azure Wyszukiwanie poznawcze

Dowiedz się, co nowego w usłudze. Oznacz Tę stronę zakładką, aby zapewnić aktualność usługi.

## <a name="september-2020"></a>Wrzesień 2020

Utwórz tożsamość usługi wyszukiwania w Azure Active Directory, a następnie użyj uprawnień RBAC, aby przyznać tożsamości tylko do odczytu dla źródeł danych platformy Azure. Opcjonalnie można wybrać opcję [zaufanego wyjątku usługi](search-indexer-howto-access-trusted-service-exception.md) , jeśli nie są dostępne reguły adresów IP.


|Ona&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategoria | Opis | Dostępność  |
|------------------------------|----------|-------------|---------------|
| [Tożsamość usługi zarządzanej](search-howto-managed-identities-data-sources.md) | Indeksatory, zabezpieczenia | Utwórz tożsamość usługi wyszukiwania w Azure Active Directory, a następnie użyj uprawnień RBAC, aby udzielić dostępu do źródeł danych platformy Azure. Takie podejście eliminuje konieczność poświadczeń w parametrach połączenia. <br><br>Dodatkowy sposób używania tożsamości usługi zarządzanej odbywa się za pomocą [zaufanego wyjątku usługi](search-indexer-howto-access-trusted-service-exception.md) , jeśli reguły adresów IP nie są opcją. | Ogólnie dostępna. Uzyskaj dostęp do tej funkcji w przypadku korzystania z portalu lub [tworzenia źródła danych (REST)](/rest/api/searchservice/create-data-source) z interfejsem API-Version = 2020-06-30. |
| [Żądania wychodzące przy użyciu linku prywatnego](search-indexer-howto-access-private.md) | Indeksatory, zabezpieczenia | Utwórz zasób udostępnionego linku, którego indeksatory mogą używać podczas uzyskiwania dostępu do zasobów platformy Azure zabezpieczonych za pomocą prywatnego linku platformy Azure. Aby uzyskać więcej informacji na temat wszystkich sposobów zabezpieczania połączeń indeksatora, zobacz [Zabezpieczanie zasobów indeksatora przy użyciu funkcji zabezpieczeń sieci platformy Azure](search-indexer-securing-resources.md). | Ogólnie dostępna. Uzyskaj dostęp do tej funkcji, gdy korzystasz z portalu lub [udostępnionego zasobu linku prywatnego](/rest/api/searchmanagement/sharedprivatelinkresources) z interfejsem API-Version = 2020-08-01. |
| [Interfejs API REST zarządzania (2020-08-01)](/rest/api/searchmanagement/management-api-versions) | REST | Nowy stabilny interfejs API REST dodaje obsługę tworzenia udostępnionych zasobów linków prywatnych. | Ogólnie dostępna. |
| [Interfejs API REST zarządzania (2020-08-01 — wersja zapoznawcza)](/rest/api/searchmanagement/management-api-versions) | REST | Dodaje udostępnione zasoby linku prywatnego dla baz danych Azure Functions i Azure SQL dla programu MySQL. | Publiczna wersja zapoznawcza. |
| [Zestaw .NET SDK 4,0](/dotnet/api/overview/azure/search/management) | Zestaw SDK .NET | Aktualizacja zestawu Azure SDK dla zestawu SDK zarządzania, interfejs API REST w wersji 2020-08-01. | Ogólnie dostępna. |

## <a name="august-2020"></a>Sierpień 2020 r.

|Ona&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategoria | Opis | Dostępność  |
|---------|------------------|-------------|---------------|
| [podwójne szyfrowanie](search-security-overview.md#encryption) | Zabezpieczenia | Włącz podwójne szyfrowanie w warstwie magazynowania, konfigurując szyfrowanie klucza zarządzanego przez klienta (CMK) w nowych usługach wyszukiwania. Utwórz nową usługę, [Skonfiguruj i Zastosuj klucze zarządzane przez klienta](search-security-manage-encryption-keys.md) do indeksów lub map synonimów, a następnie skorzystaj z podwójnego szyfrowania nad tą zawartością. | Ogólnie dostępna we wszystkich usługach wyszukiwania utworzonych po 1 sierpnia 2020 w następujących regionach: zachodnie stany USA 2, Wschodnie stany USA, Południowo-środkowe stany USA, US Gov Wirginia, US Gov Arizona. Tworzenie usługi przy użyciu portalu, interfejsów API REST zarządzania lub zestawów SDK. |

## <a name="july-2020"></a>Lipiec 2020 r.

|Ona&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategoria | Opis | Dostępność  |
|---------|------------------|-------------|---------------|
| [Azure.Search.DocBiblioteka kliencka uments](/dotnet/api/overview/azure/search.documents-readme) | Zestaw Azure SDK dla platformy .NET | Biblioteka kliencka platformy .NET wydana przez zespół zestawu Azure SDK, zaprojektowany pod kątem spójności z innymi bibliotekami klienta platformy .NET. <br/><br/>Wersja 11 wskazuje interfejs API REST wyszukiwania-Version = 2020-06-30, ale nie obsługuje jeszcze sklepu z bazami danych, typów geoprzestrzennych ani [FieldBuilder](/dotnet/api/microsoft.azure.search.fieldbuilder). <br/><br/>Aby uzyskać więcej informacji, zobacz  [Szybki Start: Tworzenie indeksu](search-get-started-dotnet.md) i [uaktualnianie do Azure.Search.Documents (v11)](search-dotnet-sdk-migration-version-11.md). | Ogólnie dostępna. </br> Zainstaluj [ pakietAzure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) z narzędzia NuGet. |
| [azure.search.docBiblioteka kliencka uments](/python/api/overview/azure/search-documents-readme)  | Zestaw Azure SDK dla środowiska Python| Biblioteka klienta języka Python wydana przez zespół zestawu Azure SDK, zaprojektowany pod kątem spójności z innymi bibliotekami klienta języka Python. <br/><br/>Wersja 11 wskazuje interfejs API REST wyszukiwania — wersja = 2020-06-30. | Ogólnie dostępna. </br> Zainstaluj [pakiet Azure-Search-Documents](https://pypi.org/project/azure-search-documents/) z PyPI. |
| [@azure/search-documents Biblioteka kliencka](/javascript/api/overview/azure/search-documents-readme)  | Zestaw Azure SDK dla języka JavaScript | Biblioteka kliencka języka JavaScript wydana przez zespół zestawu Azure SDK, zaprojektowana pod kątem spójności z innymi bibliotekami klientów JavaScript. <br/><br/>Wersja 11 wskazuje interfejs API REST wyszukiwania — wersja = 2020-06-30. | Ogólnie dostępna. </br> Zainstaluj [ @azure/search-documents pakiet](https://www.npmjs.com/package/@azure/search-documents) z npm. |

## <a name="june-2020"></a>Czerwiec 2020 r.

|Ona&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategoria | Opis | Dostępność  |
|---------|------------------|-------------|---------------|
[**Magazyn wiedzy**](knowledge-store-concept-intro.md) | Wzbogacanie sztucznej inteligencji | Dane wyjściowe indeksatora wzbogaconego AI, przechowujące zawartość w usłudze Azure Storage do użycia w innych aplikacjach i procesach. | Ogólnie dostępna. </br> Użyj [interfejsu API REST usługi Search 2020-06-30](/rest/api/searchservice/) lub nowszego lub portalu. |
| [**Interfejs API REST usługi Search 2020-06-30**](/rest/api/searchservice/) | REST | Nowa stabilna wersja interfejsów API REST. Oprócz sklepu z bazami danych ta wersja zawiera ulepszenia dotyczące przydatności i oceny wyników wyszukiwania. | Ogólnie dostępna. |
| [**Algorytm zgodności Okapi BM25**](https://en.wikipedia.org/wiki/Okapi_BM25) | Zapytanie | Nowy algorytm klasyfikacji istotności jest automatycznie używany dla wszystkich nowych usług wyszukiwania utworzonych po 15 lipca. W przypadku utworzonych wcześniej usług możesz wybrać wartość `similarity` właściwości dla pól indeksu. | Ogólnie dostępna. </br> Użyj [interfejsu API REST usługi Search 2020-06-30](/rest/api/searchservice/) lub nowszego lub interfejsu api REST 2019-05-06. |
| **executionEnvironment** | Zabezpieczenia (indeksatory) | Jawnie ustaw tę właściwość konfiguracji indeksatora na `private` , aby wymusić wszystkie połączenia z zewnętrznymi źródłami danych za pośrednictwem prywatnego punktu końcowego. Dotyczy tylko usług wyszukiwania, które korzystają z prywatnego linku platformy Azure. | Ogólnie dostępna. </br> Użyj [interfejsu API REST usługi Search 2020-06-30](/rest/api/searchservice/) , aby ustawić ten ogólny parametr konfiguracji. |

## <a name="may-2020-microsoft-build"></a>Maj 2020 (Microsoft Build)

|Ona&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategoria | Opis | Dostępność  |
|---------|------------------|-------------|---------------|
| [**Sesje debugowania**](cognitive-search-debug-session.md) | Wzbogacanie sztucznej inteligencji | Sesje debugowania zapewniają interfejs oparty na portalu do badania i rozwiązywania problemów z istniejącym zestawu umiejętności. Poprawki utworzone w sesji debugowania można zapisać w środowisku produkcyjnym umiejętności. Rozpocznij pracę z [tym samouczkiem](cognitive-search-tutorial-debug-sessions.md). | Publiczna wersja zapoznawcza w portalu. |
| [**Reguły adresów IP dla obsługi zapory w powiązaniu**](service-configure-firewall.md) | Zabezpieczenia | Ogranicz dostęp do punktu końcowego usługi wyszukiwania do określonych adresów IP. | Ogólnie dostępna. </br> Użyj [interfejsu API REST zarządzania 2020-03-13](/rest/api/searchmanagement/) lub nowszego lub portalu. |
| [**Prywatny link do prywatnego punktu końcowego wyszukiwania**](service-create-private-endpoint.md) | Zabezpieczenia| Włącz ochronę usługi wyszukiwania z publicznej sieci Internet, uruchamiając ją jako zasób linku prywatnego, dostępny tylko dla aplikacji klienckich i innych usług platformy Azure w tej samej sieci wirtualnej. | Ogólnie dostępna. </br> Użyj [interfejsu API REST zarządzania 2020-03-13](/rest/api/searchmanagement/) lub nowszego lub portalu. |
| [**tożsamość zarządzana przez system (wersja zapoznawcza)**](search-howto-managed-identities-data-sources.md) | Zabezpieczenia (indeksatory) | Zarejestruj usługę wyszukiwania jako zaufaną usługę z Azure Active Directory, aby skonfigurować połączenia z obsługiwanym źródłem danych platformy Azure na potrzeby indeksowania. Dotyczy [indeksatorów](search-indexer-overview.md) , które pobierają zawartość ze źródeł danych platformy Azure, takich jak Azure SQL Database, Azure Cosmos DB i Azure Storage. | Publiczna wersja zapoznawcza. </br> Zarejestrowanie usługi wyszukiwania przy użyciu portalu. |
| [**parametr zapytania SessionID**](index-similarity-and-scoring.md), [scoringStatistics = Global](index-similarity-and-scoring.md#scoring-statistics) | Zapytanie (istotność) | Dodaj identyfikator sesji do zapytania, aby ustanowić sesję do obliczania wyników wyszukiwania, z scoringStatistics = Global, aby zebrać wyniki ze wszystkich fragmentów, aby uzyskać bardziej spójne obliczenia wyników wyszukiwania. | Ogólnie dostępna. </br> Użyj [interfejsu API REST usługi Search 2020-06-30](/rest/api/searchservice/) lub nowszego lub interfejsu api REST 2019-05-06. |
| [**Features (wersja zapoznawcza)**](index-similarity-and-scoring.md#featuresMode-param) | Zapytanie | Dodaj ten parametr zapytania, aby rozwinąć ocenę istotności, aby pokazać więcej szczegółów: wynik podobieństwa pola, częstotliwość poszczególnych pól i dla każdego pola numer unikatowych tokenów. Te punkty danych można wykorzystać w niestandardowych algorytmach oceniania. Aby uzyskać przykład demonstrujący tę możliwość, zobacz [Dodawanie uczenia maszynowego (LearnToRank) w celu wyszukania istotności](https://github.com/Azure-Samples/search-ranking-tutorial). | Publiczna wersja zapoznawcza. </br> Użyj [interfejsu API REST usługi Search 2020-06-30 — wersja zapoznawcza](/rest/api/searchservice/index-preview) lub interfejs api REST 2019-05-06 — wersja zapoznawcza. |

## <a name="march-2020"></a>Marzec 2020 r.

|Ona&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategoria | Opis | Dostępność  |
|---------|------------------|-------------|---------------|
| [**Natywne usuwanie nietrwałego obiektu BLOB (wersja zapoznawcza)**](search-howto-index-changed-deleted-blobs.md) | Indexers (Indeksatory) | Indeksator usługi Azure Blob Storage na platformie Azure Wyszukiwanie poznawcze będzie rozpoznawał obiekty blob w stanie nietrwałego usunięcia, a następnie usuwa odpowiedni dokument wyszukiwania podczas indeksowania. | Publiczna wersja zapoznawcza. </br> Użyj [interfejsu API REST usługi Search 2020-06-30 — wersja zapoznawcza](/rest/api/searchservice/index-preview) i interfejs api REST 2019-05-06 — wersja zapoznawcza, za pomocą indeksatora uruchamiania dla źródła danych obiektów blob platformy Azure, które ma włączone natywnie "usuwanie nietrwałe". |
| [**Interfejs API REST zarządzania (2020-03-13)**](/rest/api/searchmanagement/management-api-versions) | REST | Nowy stabilny interfejs API REST do tworzenia usługi wyszukiwania i zarządzania nią. Dodaje zaporę IP i obsługę linków prywatnych | Ogólnie dostępna. |

## <a name="february-2020"></a>Luty 2020 r.

|Ona&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategoria | Opis | Dostępność  |
|---------|------------------|-------------|---------------|
| [**Wykrywanie przez dane OSOBowe (wersja zapoznawcza)**](cognitive-search-skill-pii-detection.md) | Wzbogacanie sztucznej inteligencji | Nowa umiejętność, która jest używana podczas indeksowania, która wyodrębnia informacje osobiste z tekstu wejściowego i umożliwia maskowanie tego tekstu na różne sposoby. | Publiczna wersja zapoznawcza. </br> Użyj portalu lub [interfejsu API REST usługi Search 2020-06-30 — wersja zapoznawcza](/rest/api/searchservice/index-preview) lub interfejs api REST 2019-05-06 — wersja zapoznawcza. |
| [**Wyszukiwanie jednostek niestandardowych (wersja zapoznawcza)**](cognitive-search-skill-custom-entity-lookup.md )| Wzbogacanie sztucznej inteligencji | Nowa umiejętność, która szuka tekstu na podstawie niestandardowej, zdefiniowanej przez użytkownika listy słów i fraz. Korzystając z tej listy, etykieta wszystkie dokumenty z dowolnymi zgodnymi jednostkami. Umiejętność obsługuje również stopień dopasowywania rozmytego, który można zastosować, aby znaleźć dopasowania, które są podobne, ale nie dokładnie. | Publiczna wersja zapoznawcza. </br> Użyj portalu lub [interfejsu API REST usługi Search 2020-06-30 — wersja zapoznawcza](/rest/api/searchservice/index-preview) lub interfejs api REST 2019-05-06 — wersja zapoznawcza. |

## <a name="january-2020"></a>Styczeń 2020 r.

|Ona&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategoria | Opis | Dostępność  |
|---------|------------------|-------------|---------------|
| [**Klucze szyfrowania zarządzane przez klienta**](search-security-manage-encryption-keys.md) |Zabezpieczenia | Dodaje dodatkową warstwę szyfrowania oprócz wbudowanego szyfrowania platformy. Przy użyciu utworzonego klucza szyfrowania i zarządzania można szyfrować zawartość indeksu i mapy synonimów, zanim ładunek osiągnie usługę wyszukiwania. | Ogólnie dostępna. </br> Użyj interfejsu API REST usługi Search 2019-05-06 lub nowszego. W przypadku kodu zarządzanego prawidłowy pakiet nadal jest [zestawem SDK .NET w wersji 8,0-Preview](search-dotnet-sdk-migration-version-9.md) , nawet jeśli ta funkcja nie jest dostępna w wersji zapoznawczej. |
| [**Reguły adresów IP dla obsługiwanej zapory (wersja zapoznawcza)**](service-configure-firewall.md) | Zabezpieczenia | Ogranicz dostęp do punktu końcowego usługi wyszukiwania do określonych adresów IP. Interfejs API w wersji zapoznawczej zawiera nowe właściwości **IpRule** i **NetworkRuleSet** w [interfejsie API metodę createorupdate](/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Ta funkcja w wersji zapoznawczej jest dostępna w wybranych regionach. |  Publiczna wersja zapoznawcza przy użyciu interfejsu API-Version = 2019-10 -01 — wersja zapoznawcza.  |
| [**Prywatny link do prywatnego punktu końcowego wyszukiwania (wersja zapoznawcza)**](service-create-private-endpoint.md) | Zabezpieczenia| Włącz ochronę usługi wyszukiwania z publicznej sieci Internet, uruchamiając ją jako zasób linku prywatnego, dostępny tylko dla aplikacji klienckich i innych usług platformy Azure w tej samej sieci wirtualnej. | Publiczna wersja zapoznawcza przy użyciu interfejsu API-Version = 2019-10 -01 — wersja zapoznawcza.  |

## <a name="features-in-2019"></a>Funkcje w 2019

### <a name="december-2019"></a>Grudzień 2019 r.

+ [Tworzenie aplikacji demonstracyjnej (wersja zapoznawcza)](search-create-app-portal.md) to nowy Kreator w portalu, który GENERUJE plik HTML do pobrania z użyciem zapytania (tylko do odczytu) do indeksu. Plik zawiera osadzony skrypt, który renderuje operacyjną aplikację sieci Web w stylu "localhost", powiązaną z indeksem w usłudze wyszukiwania. Strony można konfigurować w Kreatorze i zawierać pasek wyszukiwania, obszar wyników, nawigację paska bocznego i obsługę zapytań typeahead. Możesz zmodyfikować kod HTML w trybie offline, aby zwiększyć lub dostosować przepływ pracy lub wygląd. Aplikacja demonstracyjna nie została łatwo rozszerzona w celu uwzględnienia warstw zabezpieczeń i hostingu, które zwykle są konieczne w scenariuszach produkcyjnych. Należy rozważyć ją jako narzędzie do sprawdzania poprawności i testowania, a nie krótkie wycinanie do pełnej aplikacji klienckiej.

+ [Utwórz prywatny punkt końcowy dla bezpiecznych połączeń (wersja zapoznawcza)](service-create-private-endpoint.md) zawiera opis sposobu konfigurowania prywatnego linku dla bezpiecznych połączeń do usługi wyszukiwania. Ta funkcja w wersji zapoznawczej jest dostępna na żądanie i używa [prywatnego linku platformy Azure](../private-link/private-link-overview.md) i [usługi Azure Virtual Network](../virtual-network/virtual-networks-overview.md) w ramach rozwiązania.

### <a name="november-2019---ignite-conference"></a>Listopad 2019 — Konferencja zapłonowa

+ [Wzbogacanie przyrostowe (wersja zapoznawcza)](cognitive-search-incremental-indexing-conceptual.md) dodaje buforowanie i statefullness do potoku wzbogacania, dzięki czemu można korzystać z określonych etapów lub faz bez utraty zawartości, która została już przetworzona. Wcześniej każda zmiana potoku wzbogacania wymagała pełnej kompilacji. W przypadku przyrostowego wzbogacania dane wyjściowe analizy kosztów, w szczególności analizy obrazów, są zachowywane.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Wyodrębnianie dokumentu (wersja zapoznawcza)](cognitive-search-skill-document-extraction.md) to umiejętność wykorzystywana podczas indeksowania, która pozwala wyodrębnić zawartość pliku z zestawu umiejętności. Wcześniej przed wykonaniem zestawu umiejętności tylko złamanie dokumentu. Po dodaniu tej umiejętności można także wykonać tę operację w ramach wykonywania zestawu umiejętności.

+ [Tłumaczenie tekstu](cognitive-search-skill-text-translation.md) jest umiejętnością poznawczyą używaną podczas indeksowania, która oblicza tekst i dla każdego rekordu zwraca tekst przetłumaczony na określony język docelowy.

+ [Szablony Power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) mogą szybko Rozpocznij pracę wizualizacje i analizę wzbogaconej zawartości w sklepie z Power BI Desktop. Ten szablon jest przeznaczony dla projekcji tabel platformy Azure utworzonych za pomocą [Kreatora importu danych](knowledge-store-create-portal.md).

+ W indeksatorach są teraz obsługiwane [Azure Data Lake Storage Gen2 (wersja zapoznawcza)](search-howto-index-azure-data-lake-storage.md) [Cosmos DB, interfejs API Gremlin (wersja](search-howto-index-cosmosdb.md)zapoznawcza) i [Cosmos DB interfejs API Cassandra (wersja zapoznawcza)](search-howto-index-cosmosdb.md) . Możesz zarejestrować się, korzystając z [tego formularza](https://aka.ms/azure-cognitive-search/indexer-preview). Po zaakceptowaniu w programie w wersji zapoznawczej otrzymasz wiadomość e-mail z potwierdzeniem.

### <a name="july-2019"></a>Lipiec 2019 r.

+ Ogólnie dostępna w [chmurze Azure Government](../azure-government/compare-azure-government-global-azure.md#azure-cognitive-search).

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nowa nazwa usługi

Azure Search teraz zmieniono nazwę na **platformę Azure wyszukiwanie poznawcze** w celu odzwierciedlenia rozszerzonego (nieopcjonalnego) korzystania z umiejętności poznawczych i przetwarzania AI w operacjach podstawowych. Wersje interfejsu API, pakiety NuGet, przestrzenie nazw i punkty końcowe nie są zmieniane. Zmiany nazwy usługi nie wpływają na nowe i istniejące rozwiązania wyszukiwania.

## <a name="service-updates"></a>Aktualizacje usług

[Anonse aktualizacji usługi](https://azure.microsoft.com/updates/?product=search&status=all) dla systemu Azure wyszukiwanie poznawcze można znaleźć w witrynie sieci Web systemu Azure.