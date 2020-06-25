---
title: Powiadomienia o nowych funkcjach
titleSuffix: Azure Cognitive Search
description: Anonse nowych i ulepszonych funkcji, w tym zmiana nazwy usługi Azure Search na platformę Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 06/23/2020
ms.openlocfilehash: 34aeda7534c15b51b9759b696caaf3fcfac88659
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85317702"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Co nowego w usłudze Azure Wyszukiwanie poznawcze

Dowiedz się, co nowego w usłudze. Oznacz Tę stronę zakładką, aby zapewnić aktualność usługi.

## <a name="feature-announcements"></a>Anonse funkcji

### <a name="june-2020"></a>Czerwiec 2020 r.

+ [Azure Machine Learning (AML) (wersja zapoznawcza)](cognitive-search-aml-skill.md) to umiejętność wykorzystywana podczas indeksowania, która otacza model Azure Machine Learning. Za pomocą umiejętności można zintegrować wcześniej przeszkolony i wdrożoną niestandardową Uczenie maszynowe w potoku wzbogacania AI. [Zarejestruj się, aby zażądać dostępu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0jK7x7HQYdDm__YfEsbtcZUMTFGTFVTOE5XMkVUMFlDVFBTTlYzSlpLTi4u). Gdy wszystko będzie gotowe do dodania umiejętności AML, użyj szablonu AML w edytorze zestawu umiejętności w Azure Portal. Aby poznać wskazówki, zacznij korzystać z [tego samouczka](cognitive-search-tutorial-aml-custom-skill.md).

### <a name="may-2020-microsoft-build"></a>Maj 2020 (Microsoft Build)

+ Funkcja [debugowania sesji](cognitive-search-debug-session.md) jest teraz dostępna w wersji zapoznawczej. [Zarejestruj się, aby zażądać dostępu](https://aka.ms/DebugSessions). Sesje debugowania zapewniają interfejs oparty na portalu do badania i rozwiązywania problemów z zestawu umiejętności. Poprawki utworzone w sesji debugowania można zapisać w środowisku produkcyjnym umiejętności. Rozpocznij pracę z [tym samouczkiem](cognitive-search-tutorial-debug-sessions.md).

+ Ulepszenia zabezpieczeń umożliwiają [skonfigurowanie prywatnego punktu końcowego wyszukiwania (wersja zapoznawcza)](service-create-private-endpoint.md) , który jest niedostępny w publicznej sieci Internet. Istnieje również możliwość [skonfigurowania reguł IP dla obsługi zapory w powiązaniu (wersja zapoznawcza)](service-configure-firewall.md).

+ Użyj [tożsamości zarządzanej przez system (wersja zapoznawcza)](search-howto-managed-identities-data-sources.md) , aby skonfigurować połączenie ze źródłem danych platformy Azure do indeksowania. Dotyczy [indeksatorów](search-indexer-overview.md) , które pobierają zawartość ze źródeł danych platformy Azure, takich jak Azure SQL Database, Azure Cosmos DB i Azure Storage.

+ Zmień podstawę sposobu, w jaki wyniki wyszukiwania są obliczane, z fragmentu na wszystkie-fragmentów, przy użyciu parametrów zapytania [scoringStatistics = Global](index-similarity-and-scoring.md#scoring-statistics) i SessionID.

### <a name="march-2020"></a>Marzec 2020 r.

+ [Natywne usuwanie nietrwałego obiektu BLOB (wersja zapoznawcza)](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) oznacza, że usługa Azure Blob Storage Indexer w systemie Azure wyszukiwanie poznawcze rozpoznaje obiekty blob w stanie nietrwałego usunięcia, a następnie usuwa odpowiedni dokument wyszukiwania podczas indeksowania.

+ Dostępny jest nowy, stabilny [interfejs API REST zarządzania (2020-03-13)](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) . 

### <a name="february-2020"></a>Luty 2020 r.

+ [Wykrywanie danych osobowych (wersja zapoznawcza)](cognitive-search-skill-pii-detection.md) to umiejętność wykorzystywana podczas indeksowania, która wyodrębnia dane osobowe z tekstu wejściowego i umożliwia zamaskowanie tego tekstu na różne sposoby.

+ [Wyszukiwanie jednostek niestandardowych (wersja zapoznawcza)](cognitive-search-skill-custom-entity-lookup.md ) wyszukuje tekst na podstawie niestandardowej, zdefiniowanej przez użytkownika listy słów i fraz. Korzystając z tej listy, etykieta wszystkie dokumenty z dowolnymi zgodnymi jednostkami. Umiejętność obsługuje również stopień dopasowywania rozmytego, który można zastosować, aby znaleźć dopasowania, które są podobne, ale nie są bardzo dokładne. 

### <a name="january-2020"></a>Styczeń 2020 r.

+ [Klucze szyfrowania zarządzane przez klienta](search-security-manage-encryption-keys.md) są teraz ogólnie dostępne. Jeśli używasz usługi REST, możesz uzyskać dostęp do tej funkcji przy użyciu polecenia `api-version=2019-05-06` . W przypadku kodu zarządzanego prawidłowy pakiet nadal jest [zestawem SDK .NET w wersji 8,0-Preview](search-dotnet-sdk-migration-version-9.md) , nawet jeśli ta funkcja nie jest dostępna w wersji zapoznawczej. 

+ Prywatny dostęp do usługi wyszukiwania jest dostępny za pomocą dwóch mechanizmów, które są obecnie dostępne w wersji zapoznawczej:

  + Aby utworzyć usługę, można ograniczyć dostęp do określonych adresów IP za pomocą interfejsu API REST zarządzania `api-version=2019-10-01-Preview` . Interfejs API w wersji zapoznawczej zawiera nowe właściwości **IpRule** i **NetworkRuleSet** w [interfejsie API metodę createorupdate](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Ta funkcja w wersji zapoznawczej jest dostępna w wybranych regionach. Aby uzyskać więcej informacji, zobacz [jak używać interfejsu API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

  + Obecnie dostępne w wersji zapoznawczej o ograniczonym dostępie, można zainicjować obsługę Azure Search usługi, która obsługuje prywatny punkt końcowy platformy Azure dla połączeń klientów w tej samej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Tworzenie prywatnego punktu końcowego dla bezpiecznego połączenia](service-create-private-endpoint.md).

### <a name="december-2019"></a>Grudzień 2019 r.

+ [Tworzenie aplikacji (wersja zapoznawcza)](search-create-app-portal.md) to nowy Kreator w portalu, który GENERUJE plik HTML do pobrania. Plik zawiera osadzony skrypt, który renderuje operacyjną aplikację sieci Web w stylu "localhost", powiązaną z indeksem w usłudze wyszukiwania. Strony można konfigurować w Kreatorze i zawierać pasek wyszukiwania, obszar wyników, nawigację paska bocznego i obsługę zapytań typeahead. Możesz zmodyfikować kod HTML w trybie offline, aby zwiększyć lub dostosować przepływ pracy lub wygląd.

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

+ Ogólnie dostępna w [chmurze Azure Government](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nowa nazwa usługi

Azure Search teraz zmieniono nazwę na **platformę Azure wyszukiwanie poznawcze** w celu odzwierciedlenia rozszerzonego (nieopcjonalnego) korzystania z umiejętności poznawczych i przetwarzania AI w operacjach podstawowych. Wersje interfejsu API, pakiety NuGet, przestrzenie nazw i punkty końcowe nie są zmieniane. Zmiany nazwy usługi nie wpływają na nowe i istniejące rozwiązania wyszukiwania.

## <a name="service-updates"></a>Aktualizacje usług

[Anonse aktualizacji usługi](https://azure.microsoft.com/updates/?product=search&status=all) dla systemu Azure wyszukiwanie poznawcze można znaleźć w witrynie sieci Web systemu Azure.