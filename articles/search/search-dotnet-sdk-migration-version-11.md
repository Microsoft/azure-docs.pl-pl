---
title: Uaktualnianie do zestawu .NET SDK w wersji 11
titleSuffix: Azure Cognitive Search
description: Migruj kod do platformy Azure Wyszukiwanie poznawcze .NET SDK wersja 11 ze starszych wersji. Dowiedz się, co nowego i jakie zmiany w kodzie są wymagane.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: 644184f5bb51d049d890655ada5be5ebd4c11bf7
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87909917"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>Uaktualnianie do platformy Azure Wyszukiwanie poznawcze .NET SDK wersja 11

W przypadku korzystania z programu [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search)w wersji 10,0 lub starszej, ten artykuł pomoże Ci uaktualnić program do wersji 11.

Wersja 11 to w pełni przeprojektowana Biblioteka kliencka, wydawana przez zespół programistyczny zestawu Azure SDK (poprzednie wersje zostały utworzone przez zespół deweloperów Wyszukiwanie poznawcze platformy Azure). Biblioteka została przeprojektowana w celu zapewnienia większej spójności z innymi bibliotekami klientów platformy Azure, biorąc pod uwagę zależność od [platformy Azure. Core](https://docs.microsoft.com/dotnet/api/azure.core) i [System.Text.Js](https://docs.microsoft.com/dotnet/api/system.text.json)i implementowanie znanych podejścia do typowych zadań.

Niektóre kluczowe różnice, które należy zauważyć w nowej wersji, to m.in.:

+ Jeden pakiet i Biblioteka zamiast wielu
+ Nazwa nowego pakietu: `Azure.Search.Documents` zamiast `Microsoft.Azure.Search` .
+ Trzy klienci zamiast dwóch: `SearchClient` , `SearchIndexClient` ,`SearchIndexerClient`
+ Nazewnictwo różnic między zakresami interfejsów API i małymi różnicami strukturalnymi, które upraszczają niektóre zadania

## <a name="package-and-library-consolidation"></a>Konsolidacja pakietów i bibliotek

Wersja 11 konsoliduje wiele pakietów i bibliotek w jeden. Po migracji masz mniejszą liczbę bibliotek do zarządzania.

+ [Azure.Search.Docpakiet uments](https://www.nuget.org/packages/Azure.Search.Documents/)

+ [Dokumentacja interfejsu API dla biblioteki klienta](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)

## <a name="client-differences"></a>Różnice klienta

Jeśli ma to zastosowanie, w poniższej tabeli są mapowane biblioteki klienckie między tymi dwiema wersjami.

| Zakres operacji | Microsoft. Azure. Search &nbsp; (v10) | Azure.Search.Documents &nbsp; (v11) |
|---------------------|------------------------------|------------------------------|
| Klient używany do wykonywania zapytań i do wypełniania indeksu. | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient) |
| Klient używany na potrzeby indeksów, analizatorów, mapy synonimów | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| Klient używany dla indeksatorów, źródeł danych, umiejętności | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient (**Nowy**)](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Important]
> Wersja 11 zmienia nazwę na wersję 10 `SearchIndexClient` na `SearchClient` , a następnie ponownie używa nazwy dla klienta działającego przy użyciu indeksów, analizatora i obiektów synonimów. Podczas aktualizowania odwołań klientów wykonaj sekwencję kroków krok po [kroku, aby przeprowadzić uaktualnienie](#UpgradeSteps) , aby uniknąć pomyłek podczas wyszukiwania i zamieniania.

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>Nazewnictwo i inne różnice interfejsu API

Oprócz różnic między klientami (zanotowanymi wcześniej i w ten sposób pominięto), zmieniono nazwy wielu innych interfejsów API i w niektórych przypadkach zostały one przeprojektowane. Różnice nazw klas są zestawione poniżej. Ta lista nie jest wyczerpująca, ale wykonuje zmiany w grupie interfejsu API według zadania, które mogą być przydatne w przypadku poprawek w określonych blokach kodu. Aby uzyskać listę elementów aktualizacji interfejsu API, zobacz [Dziennik zmian](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) w `Azure.Search.Documents` witrynie GitHub.

### <a name="authentication-and-encryption"></a>Uwierzytelnianie i szyfrowanie

| Wersja 10 | Odpowiednik w wersji 11 |
|------------|-----------------------|
| [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential) |
| `EncryptionKey`(istniały w [zestawie SDK wersji zapoznawczej](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) jako ogólnie dostępna funkcja) | [SearchResourceEncryptionKey](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>Indeksy, analizatory, mapy synonimów

| Wersja 10 | Odpowiednik w wersji 11 |
|------------|-----------------------|
| [Indeks](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [Pole](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field) | [SearchField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [Typu](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [Analizator](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicalanalyze) (również `AnalyzerName` do `LexicalAnalyzerName` ) |
| [AnalyzeRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (również `StandardTokenizerV2` do `LuceneStandardTokenizerV2` ) |
| [TokenInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Tokenizatora](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (również `TokenizerName` do `LexicalTokenizerName` ) |
| [SynonymMap. format](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap.format) | Brak. Usuń odwołania do `Format` . |

Definicje pól są usprawnione: [SearchableField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchablefield), [SimpleField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.simplefield), [ComplexField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.complexfield) to nowe interfejsy API do tworzenia definicji pól.

### <a name="indexers-datasources-skillsets"></a>Indeksatory, źródła danych, umiejętności

| Wersja 10 | Odpowiednik w wersji 11 |
|------------|-----------------------|
| [Indeksator](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [Czy](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [Zestawu umiejętności](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskillse) |
| [DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>Importowanie danych

| Wersja 10 | Odpowiednik w wersji 11 |
|------------|-----------------------|
| [IndexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-definitions-and-results"></a>Definicje i wyniki zapytania

| Wersja 10 | Odpowiednik w wersji 11 |
|------------|-----------------------|
| [DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresult-1) lub [SearchResults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresults-1), w zależności od tego, czy wynik jest pojedynczym dokumentem, czy wielokrotnym. |
| [DocumentSuggestResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters) |  [SearchOptions zawierają](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchoptions)  |

<a name="WhatsNew"></a>

## <a name="whats-in-version-11"></a>Wersja 11

Każda wersja biblioteki klienta Wyszukiwanie poznawcze platformy Azure jest przeznaczona dla odpowiedniej wersji interfejsu API REST. Interfejs API REST jest uznawany za fundament usługi, a poszczególne zestawy SDK zawijają wersję interfejsu API REST. Deweloperem platformy .NET może być pomocne zapoznanie się z [dokumentacją interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/) , jeśli potrzebujesz więcej informacji na temat określonych obiektów lub operacji.

Wersja 11 jest przeznaczona dla [usługi wyszukiwania 2020-06-30](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json). Ponieważ wersja 11 jest również nową biblioteką klienta utworzoną od podstaw, większość wysiłku programistycznego koncentruje się na równoważności z wersją 10, a niektóre funkcje interfejsu API REST są nadal w stanie oczekiwania.

Wersja 11 w pełni obsługuje następujące obiekty i operacje:

+ Tworzenie indeksu i zarządzanie nimi
+ Tworzenie mapy synonimów i zarządzanie nimi
+ Wszystkie typy zapytań i składnia (z wyjątkiem filtrów przestrzennych)
+ Obiekty indeksatora i operacje indeksowania źródeł danych platformy Azure, w tym źródeł danych i umiejętności

### <a name="pending-features"></a>Funkcje oczekujące

Poniższe funkcje w wersji 10 nie są jeszcze dostępne w wersji 11. W przypadku korzystania z tych funkcji należy wstrzymać proces migracji do czasu ich obsługi.

+ [typy geograficzne](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.serialization.geojsonextensions)
+ [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) (chociaż można użyć [tego obejścia](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/tests/Samples/FieldBuilder/FieldBuilder.cs)).
+ [Magazyn wiedzy](knowledge-store-concept-intro.md)

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki do uaktualnienia

Poniższe kroki ułatwiają rozpoczęcie migracji kodu przez przechodzenie przez pierwszy zestaw wymaganych zadań, szczególnie w odniesieniu do odwołań klientów.

1. Zainstaluj [Azure.Search.Docpakiet uments](https://www.nuget.org/packages/Azure.Search.Documents/) , klikając prawym przyciskiem myszy odwołanie do projektu i wybierając pozycję "Zarządzaj pakietami NuGet..." w programie Visual Studio.

1. Zastąp dyrektywy using dla Microsoft. Azure. Search:

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Zamień [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials) na [AzureKeyCredential](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential).

1. Aktualizowanie odwołań klienta dla obiektów powiązanych z indeksatorem. Jeśli używasz indeksatorów, DataSources lub umiejętności, Zmień odwołania klienta do [SearchIndexerClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient). Ten klient jest nowy w wersji 11 i nie ma poprzedzającego go.

1. Aktualizowanie odwołań klienta na potrzeby zapytań i importowania danych. Wystąpienia elementu [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) należy zmienić na [SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient). Aby uniknąć pomyłek nazw, przed przejściem do następnego kroku upewnij się, że wszystkie wystąpienia są przechwytywane.

1. Aktualizowanie odwołań klienta dla obiektów index, indeksator, Mapa synonim i Analizator. Wystąpienia elementu [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) należy zmienić na [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchindexclient). 

1. Tak dużo, jak to możliwe, zaktualizuj klasy, metody i właściwości, aby używać interfejsów API nowej biblioteki. Sekcja [różnice nazewnictwa](#naming-differences) jest miejscem do uruchomienia, ale można również przejrzeć [Dziennik zmian](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md).

   Jeśli masz problemy z znalezieniem równoważnych interfejsów API, sugerujemy rejestrowanie problemu w [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) celu poprawienia dokumentacji lub zbadania problemu.

1. Ponownie skompiluj rozwiązanie. Po naprawieniu błędów kompilacji lub ostrzeżeń możesz wprowadzić dodatkowe zmiany w aplikacji, aby korzystać z [nowych funkcji](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-11"></a>Istotne zmiany w wersji 11

Uwzględniając zmiany w bibliotekach i interfejsach API, uaktualnienie do wersji 11 nie jest proste i stanowi istotną zmianę w tym sensie, że kod nie będzie już zgodny z wersją 10 i wcześniejszą. Szczegółowe przegląd różnic można znaleźć w [dzienniku zmian](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) `Azure.Search.Documents` .

W przypadku wersji usługi przeniesienie od 10 do 11 wprowadza następujące zmiany w zachowaniu: 

+ [Algorytm klasyfikacji BM25](index-ranking-similarity.md) zastępuje poprzedni algorytm klasyfikacji nowszą technologią. Nowe usługi będą używać tego algorytmu automatycznie. W przypadku istniejących usług należy ustawić parametry, aby używały nowego algorytmu.

+ [Uporządkowane wyniki](search-query-odata-orderby.md) dla wartości null zostały zmienione w tej wersji, a wartości null są wyświetlane jako pierwsze, jeśli sortowanie jest `asc` i ostatnie, jeśli sortowanie jest `desc` . Jeśli Zapisano kod do obsługi sortowania wartości null, należy przejrzeć i potencjalnie usunąć ten kod, jeśli nie jest już potrzebny.

## <a name="next-steps"></a>Następne kroki

+ [Azure.Search.Docpakiet uments](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Przykłady w serwisie GitHub](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Dokumentacja interfejsu APIAzure.Search.Document](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)