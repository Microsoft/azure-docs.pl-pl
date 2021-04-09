---
title: Tworzenie indeksatora
titleSuffix: Azure Cognitive Search
description: Ustaw właściwości indeksatora, aby określić źródła danych i miejsca docelowe. Można ustawić parametry w celu zmodyfikowania zachowań środowiska uruchomieniowego.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 596eca0d73ffc4a590fae9b346658a2c31a1d68c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676482"
---
# <a name="creating-indexers-in-azure-cognitive-search"></a>Tworzenie indeksatorów na platformie Azure Wyszukiwanie poznawcze

Indeksator wyszukiwania zapewnia zautomatyzowany przepływ pracy do przenoszenia dokumentów i zawartości z zewnętrznego źródła danych do indeksu wyszukiwania w usłudze wyszukiwania. Jak pierwotnie zaprojektowany, wyodrębnia tekst i metadane ze źródła danych platformy Azure, deserializacji dokumenty w formacie JSON i przekazuje je do aparatu wyszukiwania do indeksowania. Jest to, że została rozszerzona o obsługę [wzbogacania zawartości AI](cognitive-search-concept-intro.md) do przetwarzania głębokiego. 

Użycie indeksatorów znacznie zmniejsza liczbę i złożoność kodu, który należy napisać. Ten artykuł koncentruje się na Mechanics tworzenia indeksatora jako przygotowania do bardziej zaawansowanej pracy z indeksatorami i [umiejętności](cognitive-search-working-with-skillsets.md).

## <a name="whats-an-indexer-definition"></a>Co to jest definicja indeksatora?

Indeksatory są używane w przypadku indeksowania tekstowego, który pobiera zawartość alfanumeryczną z pól źródłowych do pól indeksu lub przetwarzanie oparte na AI, które analizuje nierozróżniany tekst dla struktury, lub analizuje obrazy dla tekstu i informacji, a także dodaje tę zawartość do indeksu. Następujące definicje indeksów są typowe dla każdego z tych scenariuszy.

### <a name="indexers-for-text-content"></a>Indeksatory dla zawartości tekstowej

Pierwotny cel indeksatora był uproszczenie złożonego procesu ładowania indeksu przez udostępnienie mechanizmu łączenia się i odczytywania zawartości tekstowej i liczbowej z pól w źródle danych, serializacji tej zawartości jako dokumentów JSON i przekazanie tych dokumentów do aparatu wyszukiwania w celu indeksowania. Jest to nadal podstawowy przypadek użycia i dla tej operacji trzeba utworzyć indeksator z właściwościami zdefiniowanymi w poniższym przykładzie.

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String indicated which existing data source to use,
  "targetIndexName": (required) String,
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [ optional unless there are field discrepancies that need resolution]
}
```

**`name`**, **`dataSourceName`** , I **`targetIndexName`** właściwości są wymagane i w zależności od sposobu tworzenia indeksatora, zarówno źródło danych, jak i indeks muszą już istnieć w usłudze, zanim będzie można uruchomić indeksator. 

**`parameters`** Właściwość modyfikuje zachowania w czasie wykonywania, takie jak liczba błędów, które mają być akceptowane przed błędem całego zadania. Parametry są również sposobem określania zachowań specyficznych dla źródła. Na przykład jeśli źródłem jest magazyn obiektów blob, można ustawić parametr, który filtruje rozszerzenia plików: `"parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }` .

**`field mappings`** Właściwość służy do jawnego mapowania pól źródła do miejsca docelowego, jeśli te pola różnią się nazwami lub typem. Inne właściwości (niepokazywany) są używane do [określania harmonogramu](search-howto-schedule-indexers.md), tworzenia indeksatora w stanie wyłączonym lub określania [klucza szyfrowania](search-security-manage-encryption-keys.md) dodatkowego szyfrowania danych przechowywanych w spoczynku.

### <a name="indexers-for-ai-indexing"></a>Indeksatory dla indeksowania AI

Ponieważ Indeksatory są mechanizmem, za pomocą którego usługa wyszukiwania wykonuje żądania wychodzące, indeksatory zostały rozszerzone pod kątem obsługi wzbogacania AI, Dodawanie infrastruktury i obiektów w celu zaimplementowania tego przypadku użycia.

Wszystkie powyższe właściwości i parametry mają zastosowanie do indeksatorów, które wykonują wzbogacanie AI. Następujące właściwości są specyficzne dla wzbogacania AI: **`skillSets`** , **`outputFieldMappings`** , **`cache`** (tylko wersja zapoznawcza i REST). 

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String, name of an existing data source,
  "targetIndexName": (required) String, name of an existing index,
  "skillsetName" : (required for AI enrichment) String, name of an existing skillset,
  "cache":  {
    "storageConnectionString" : (required for caching AI enriched content) Connection string to a blob container,
    "enableReprocessing": true
    },
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [],
  "outputFieldMappings" : (required for AI enrichment) { ... },
}
```

Wzbogacanie AI wykracza poza zakres tego artykułu. Aby uzyskać więcej informacji, Zacznij od następujących artykułów: [wzbogacanie AI](cognitive-search-concept-intro.md), [umiejętności na platformie Azure wyszukiwanie poznawcze](cognitive-search-working-with-skillsets.md)i [Tworzenie zestawu umiejętności (REST)](/rest/api/searchservice/create-skillset).

## <a name="choose-an-indexer-client-and-create-the-indexer"></a>Wybierz klienta indeksatora i Utwórz indeksator

Gdy wszystko jest gotowe do utworzenia indeksatora w usłudze zdalnego wyszukiwania, będzie potrzebny klient wyszukiwania w postaci narzędzia, takiego jak Azure Portal lub Poster lub kod, który tworzy wystąpienie dla klienta indeksatora. Zalecamy używanie Azure Portal lub interfejsów API REST na potrzeby wczesnego tworzenia i testowania koncepcji.

### <a name="permissions"></a>Uprawnienia

Wszystkie operacje związane z indeksatorami, w tym żądania pobrania stanu lub definicji, wymagają [klucza API-Key administratora](search-security-api-keys.md) w żądaniu.

### <a name="limits"></a>Limity

Wszystkie [warstwy usług ograniczają](search-limits-quotas-capacity.md#indexer-limits) liczbę obiektów, które można utworzyć. Jeśli przeprowadzasz eksperymentowanie w warstwie Bezpłatna, możesz mieć tylko 3 obiekty dla każdego typu i 2 minuty przetwarzania indeksatora (nie obejmuje przetwarzania zestawu umiejętności).

### <a name="use-azure-portal-to-create-an-indexer"></a>Używanie Azure Portal do tworzenia indeksatora

Portal udostępnia dwie opcje tworzenia indeksatora: [**Kreator importowania danych**](search-import-data-portal.md) i **Nowy indeksator** , który udostępnia pola do określania definicji indeksatora. Kreator jest unikatowy w tym, że tworzy wszystkie wymagane elementy. Inne podejścia wymagają wstępnie zdefiniowanego źródła danych i indeksu.

Poniższy zrzut ekranu pokazuje, gdzie można znaleźć te funkcje w portalu. 

  :::image type="content" source="media/search-howto-create-indexers/portal-indexer-client.png" alt-text="Indeksator hoteli" border="true":::

### <a name="use-a-rest-client"></a>Korzystanie z klienta REST

Zarówno program Poster, jak i Visual Studio Code (z rozszerzeniem dla systemu Azure Wyszukiwanie poznawcze) mogą działać jako klient indeksatora. Za pomocą dowolnego z tych narzędzi można nawiązać połączenie z usługą wyszukiwania i wysyłać żądania [utworzenia indeksatora (REST)](/rest/api/searchservice/create-indexer) . Istnieje wiele samouczków i przykładów demonstrujących klientów REST do tworzenia obiektów. 

Aby dowiedzieć się więcej na temat każdego klienta, Zacznij od jednego z poniższych artykułów:

+ [Tworzenie indeksu wyszukiwania przy użyciu REST i programu Poster](search-get-started-rest.md)
+ [Wprowadzenie do Visual Studio Code i platformy Azure Wyszukiwanie poznawcze](search-get-started-vs-code.md)

Zapoznaj się z [operacjami indeksatora (REST)](/rest/api/searchservice/Indexer-operations) , aby uzyskać pomoc dotyczącą formułowanych żądań indeksatora.

### <a name="use-an-sdk"></a>Korzystanie z zestawu SDK

W przypadku Wyszukiwanie poznawcze zestawy SDK platformy Azure implementują ogólnie dostępne funkcje. W związku z tym można użyć dowolnego z zestawów SDK do tworzenia obiektów powiązanych indeksatorem. Wszystkie z nich zapewniają **SearchIndexerClient** , który ma metody tworzenia indeksatorów i powiązanych obiektów, w tym umiejętności.

| Zestaw Azure SDK | Klient | Przykłady |
|-----------|--------|----------|
| .NET | [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) | [DotNetHowToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) |
| Java | [SearchIndexerClient](/java/api/com.azure.search.documents.indexes.searchindexerclient) | [CreateIndexerExample. Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) |
| JavaScript | [SearchIndexerClient](/javascript/api/@azure/search-documents/searchindexerclient) | [Indexers (Indeksatory)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |
| Python | [SearchIndexerClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexerclient) | [sample_indexers_operations. PR](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) |

## <a name="run-the-indexer"></a>Uruchamianie indeksatora

Indeksator jest uruchamiany automatycznie podczas tworzenia indeksatora w usłudze. Jest to moment, w którym można się dowiedzieć, czy występują błędy połączenia ze źródłem danych, problemy związane z mapowaniem pól lub problemy z zestawu umiejętności. 

Istnieje kilka sposobów uruchamiania indeksatora:

+ Wyślij żądanie HTTP dotyczące [utworzenia indeksatora](/rest/api/searchservice/create-indexer) lub [Update indeksatora](/rest/api/searchservice/update-indexer) w celu dodania lub zmiany definicji i uruchomienia indeksatora.

+ Wyślij żądanie HTTP dla [indeksatora uruchamiania](/rest/api/searchservice/run-indexer) , aby wykonać indeksator bez zmian w definicji.

+ Uruchom program, który wywołuje metody SearchIndexerClient w celu utworzenia, aktualizacji lub uruchomienia.

> [!NOTE]
> Aby uniknąć natychmiastowego uruchamiania indeksatora przy tworzeniu, należy uwzględnić **`disabled=true`** w definicji indeksatora.

Alternatywnie można umieścić indeksator zgodnie z [harmonogramem](search-howto-schedule-indexers.md) , aby wywołać przetwarzanie w regularnych odstępach czasu. 

Zaplanowane przetwarzanie zwykle pokrywa się z potrzebą przyrostowego indeksowania zmienionej zawartości. Logika wykrywania zmian jest funkcją wbudowaną w platformę źródłową. Zmiany w kontenerze obiektów BLOB są automatycznie wykrywane przez indeksator. Aby uzyskać wskazówki dotyczące korzystania z wykrywania zmian w innych źródłach danych, zapoznaj się z dokumentacją indeksatora dla określonych źródeł danych:

+ [Azure SQL database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
+ [Table Storage platformy Azure](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="change-detection-and-indexer-state"></a>Stan wykrywania zmian i indeksatora

Indeksatory mogą wykrywać zmiany w danych źródłowych i przetwarzać nowe lub zaktualizowane dokumenty na każdym uruchomieniu indeksatora. Na przykład jeśli stan indeksatora wskazuje, że uruchomienie zakończyło się pomyślnie z `0/0` przetworzonymi dokumentami, oznacza to, że indeksator nie znalazł żadnych nowych lub zmienionych wierszy ani obiektów BLOB w źródłowym źródle danych.

Jak indeksator obsługuje wykrywanie zmian w zależności od źródła danych:

+ Na platformie Azure Blob Storage, na platformie Azure Table Storage i Azure Data Lake Storage Gen2 sygnatury każdej aktualizacji obiektów blob lub wierszy wraz z datą i godziną. Różne indeksatory używają tych informacji do ustalenia, które dokumenty należy zaktualizować w indeksie. Wbudowane wykrywanie zmian oznacza, że indeksator może rozpoznawać nowe i zaktualizowane dokumenty, bez konieczności dodatkowej konfiguracji.

+ Usługi Azure SQL i Cosmos DB udostępniają funkcje wykrywania zmian na swoich platformach. W definicji źródła danych można określić zasady wykrywania zmian.

W przypadku dużych obciążeń indeksowania indeksator śledzi również informacje o ostatnim dokumencie przetworzonym przez wewnętrzny "górny znak wodny". Znacznik nigdy nie jest ujawniany w interfejsie API, ale wewnętrznie indeksator śledzi miejsce zatrzymania. Gdy indeksowanie zostanie wznowione, za pomocą zaplanowanego uruchomienia lub wywołania na żądanie, indeksator odwołuje się do znacznika limitu górnego, aby można było go wybrać w miejscu, w którym został pozostawiony.

Jeśli konieczne jest wyczyszczenie znacznika limitu górnego, aby ponownie indeksować pełne, można użyć [Zresetuj indeksator](/rest/api/searchservice/reset-indexer). Aby uzyskać bardziej wybiórcze ponowne indeksowanie, użyj [Ustawienia umiejętności](/rest/api/searchservice/preview-api/reset-skills) lub [Zresetuj dokumenty](/rest/api/searchservice/preview-api/reset-documents). Resetowanie interfejsów API umożliwia wyczyszczenie stanu wewnętrznego, a także opróżnianie pamięci podręcznej, jeśli włączono [wzbogacanie przyrostowe](search-howto-incremental-index.md). Aby uzyskać więcej informacji w tle i porównaniu z każdą opcją resetowania, zobacz [Uruchamianie lub resetowanie indeksatorów, umiejętności i dokumentów](search-howto-run-reset-indexers.md).

## <a name="know-your-data"></a>Poznaj swoje dane

Indeksatory oczekują zestawu wierszy tabelarycznych, gdzie każdy wiersz zmienia się w pełnym lub częściowym dokumencie wyszukiwania w indeksie. Często istnieje zgodność jeden do jednego między wierszem a wyszukiwanym dokumentem wyszukiwania, gdzie wszystkie pola w wierszu w pełni wypełniają każdy dokument. Można jednak użyć indeksatorów do wygenerowania tylko części dokumentu, na przykład jeśli używasz wielu indeksatorów lub podejścia do tworzenia indeksu. 

Aby spłaszczyć relacyjne dane do zestawu wierszy, należy utworzyć widok SQL lub skompilować zapytanie, które zwraca rekordy nadrzędne i podrzędne w tym samym wierszu. Przykładowo Wbudowany zestaw danych hoteli to baza danych SQL, która ma 50 rekordów (po jednym dla każdego hotelu), połączonych z rekordami pokoju w powiązanej tabeli. Zapytanie, które spłaszcza dane zbiorcze do zestawu wierszy, osadza wszystkie informacje o pokoju w dokumentach JSON w każdym rekordzie hotelu. Osadzone informacje o pomieszczeniu są generowane przez zapytanie, które używa klauzuli **for JSON** . Więcej informacji o tej metodzie można znaleźć w temacie [Definiowanie zapytania, które zwraca osadzony kod JSON](index-sql-relational-data.md#define-a-query-that-returns-embedded-json). Jest to tylko jeden przykład: można znaleźć inne podejścia, które spowodują powstanie tego samego efektu.

Oprócz spłaszczonych danych ważne jest, aby pobierać tylko dane z możliwością wyszukiwania. Dane z możliwością wyszukiwania są alfanumeryczne. Wyszukiwanie poznawcze nie może przeszukiwać danych binarnych w dowolnym formacie, chociaż mogą wyodrębnić i wywnioskować opisy tekstowe plików obrazów (zobacz [wzbogacanie AI](cognitive-search-concept-intro.md)), aby utworzyć zawartość do przeszukiwania. Podobnie, przy użyciu wzbogacania AI, duży tekst może być analizowany przez modele języka naturalnego, aby znaleźć strukturę lub istotne informacje, generując nową zawartość, którą można dodać do dokumentu wyszukiwania.

W przypadku, gdy indeksatory nie rozwiązują problemów z danymi, może być konieczne wykonanie innych form czyszczenia lub manipulowania danymi. Aby uzyskać więcej informacji, należy zapoznać się z dokumentacją produktu [usługi Azure Database](../index.yml?product=databases).

## <a name="know-your-index"></a>Poznaj swój indeks

Wycofaj te indeksatory, które przechodzą z przeszukiwanych dokumentów do aparatu wyszukiwania w celu indeksowania. Tak samo jak indeksatory mają właściwości, które określają zachowanie wykonywania, schemat indeksu ma właściwości, które mają wpływ na sposób indeksowania ciągów (tylko ciągi są analizowane i podlegają tokenom). W zależności od przypisań analizatora indeksowane ciągi mogą się różnić od tego, co zostało przesłane. Efekty analizatorów można oszacować przy użyciu [analizy tekstu (REST)](/rest/api/searchservice/test-analyzer). Aby uzyskać więcej informacji na temat analizatorów, zobacz [analizatory do przetwarzania tekstu](search-analyzers.md).

Pod względem tego, jak indeksatory współdziałają z indeksem, indeksator sprawdza tylko nazwy pól i typy. Nie ma kroku sprawdzania poprawności, który gwarantuje, że zawartość przychodząca jest poprawna dla odpowiedniego pola wyszukiwania w indeksie. W ramach kroku weryfikacji można uruchamiać zapytania na wypełnionym indeksie, które zwracają całe dokumenty lub wybrane pola. Aby uzyskać więcej informacji na temat wykonywania zapytań dotyczących zawartości indeksu, zobacz [Tworzenie podstawowego zapytania](search-query-create.md).

## <a name="next-steps"></a>Następne kroki

+ [Planowanie indeksatorów](search-howto-schedule-indexers.md)
+ [Definiuj mapowania pól](search-indexer-field-mappings.md)
+ [Monitorowanie stanu indeksatora](search-howto-monitor-indexers.md)
+ [Nawiązywanie połączenia przy użyciu tożsamości zarządzanych](search-howto-managed-identities-data-sources.md)