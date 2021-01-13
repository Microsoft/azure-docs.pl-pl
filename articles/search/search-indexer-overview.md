---
title: Indeksatory do przeszukiwania danych podczas importowania
titleSuffix: Azure Cognitive Search
description: Przeszukiwanie Azure SQL Database, wystąpienia zarządzanego SQL, Azure Cosmos DB lub usługi Azure Storage w celu wyodrębnienia danych z możliwością wyszukiwania i wypełnienia indeksu Wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 0405db2b68abefbfdc424def9e35e363e45043cd
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180136"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indeksatory w usłudze Azure Cognitive Search

*Indeksator* na platformie Azure wyszukiwanie poznawcze to przeszukiwarka, która wyodrębnia dane z możliwością wyszukiwania i metadane z zewnętrznego źródła danych platformy Azure i wypełnia indeks wyszukiwania przy użyciu mapowań pola do pola między danymi źródłowymi a indeksem. Takie podejście jest czasami nazywane "modelem ściągania", ponieważ usługa ściąga dane w programie bez konieczności pisania kodu, który dodaje dane do indeksu.

Indeksatory są oparte tylko na platformie Azure, z poszczególnymi indeksatorami dla usług Azure SQL, Azure Cosmos DB, Table Storage platformy Azure i Blob Storage. Podczas konfigurowania indeksatora należy określić źródło danych, a także indeks (miejsce docelowe). Niektóre źródła danych, takie jak indeksatory magazynu obiektów blob, mają dodatkowe właściwości specyficzne dla tego typu zawartości.

Indeksatory można uruchamiać na żądanie lub według cyklicznego harmonogramu odświeżania danych uruchamianego co pięć minut. Częstsze aktualizacje wymagają modelu wypychania, który jednocześnie aktualizuje dane zarówno w usłudze Azure Wyszukiwanie poznawcze, jak i w zewnętrznym źródle danych.

## <a name="usage-scenarios"></a>Scenariusze użycia

Można użyć indeksatora jako jedynego środka do pozyskiwania danych lub użyć kombinacji technik, które obejmują ładowanie tylko niektórych pól w indeksie, opcjonalnie Przekształcanie lub wzbogacanie zawartości w sposób. Poniższa tabela zawiera podsumowanie głównych scenariuszy.

| Scenariusz |Strategia |
|----------|---------|
| Pojedyncze Źródło | Ten wzorzec jest najprostszy: jedno źródło danych jest jedynym dostawcą zawartości dla indeksu wyszukiwania. Ze źródła można zidentyfikować jedno pole zawierające unikatowe wartości, które będzie stanowić klucz dokumentu w indeksie wyszukiwania. Unikatowa wartość zostanie użyta jako identyfikator. Wszystkie inne pola źródłowe są mapowane niejawnie lub jawnie do odpowiednich pól w indeksie. </br></br>Ważnym wnioskiem jest to, że wartość klucza dokumentu pochodzi z danych źródłowych. Usługa wyszukiwania nie generuje wartości kluczy. W kolejnych uruchomieniach dokumenty przychodzące z nowymi kluczami są dodawane, podczas gdy dokumenty przychodzące z istniejącymi kluczami są scalone lub zastępowane, w zależności od tego, czy pola indeksu mają wartość null czy wypełniono. |
| Wiele źródeł| Indeks może akceptować zawartość z wielu źródeł, gdzie każda z nich uruchamia nową zawartość z innego źródła. </br></br>Jeden z wyników może być indeksem, który uzyskuje dokumenty po uruchomieniu każdego indeksatora, z całymi dokumentami utworzonymi w całości z każdego źródła. Wyzwanie dla tego scenariusza polega na projektowaniu schematu indeksu, który działa dla wszystkich danych przychodzących, i klucz dokumentu, który jest jednolity w indeksie wyszukiwania. Na przykład, jeśli wartości, które jednoznacznie identyfikują dokument, są metadata_storage_path w kontenerze obiektów blob i klucz podstawowy w tabeli SQL, można przystąpić do tego, że jedno lub oba źródła muszą zostać zmienione w celu zapewnienia wartości kluczy w typowym formacie, niezależnie od źródła zawartości. W tym scenariuszu należy zastanowić się, aby przetworzyć pewien poziom przetwarzania wstępnego, aby homogenizować dane, aby można było je ściągnąć do jednego indeksu.</br></br>Alternatywny wynik może wyszukiwać dokumenty, które są częściowo wypełniane podczas pierwszego uruchomienia, a następnie uzupełniane przez kolejne uruchomienia w celu wprowadzenia wartości z innych źródeł. Wyzwaniem tego wzorca jest upewnienie się, że każdy przebieg indeksowania ma cel tego samego dokumentu. Scalanie pól w istniejącym dokumencie wymaga dopasowania klucza dokumentu. Aby zapoznać się z prezentacją tego scenariusza, zobacz [Samouczek: indeks z wielu źródeł danych](tutorial-multiple-data-sources.md). |
| Przekształcanie zawartości | Wyszukiwanie poznawcze obsługuje opcjonalne zachowania [wzbogacania AI](cognitive-search-concept-intro.md) , które dodają analizę obrazu i przetwarzanie języka naturalnego w celu utworzenia nowej, przeszukiwanej zawartości i struktury. Wzbogacanie AI jest zdefiniowane przez [zestawu umiejętności](cognitive-search-working-with-skillsets.md), dołączone do indeksatora. Aby przeprowadzić wzbogacanie AI, indeksator nadal potrzebuje indeksu i źródła danych, ale w tym scenariuszu program dodaje zestawu umiejętności do wykonywania indeksatora. |

## <a name="approaches-for-creating-and-managing-indexers"></a>Podejścia do tworzenia indeksatorów i zarządzania nimi

Możesz tworzyć indeksatory i zarządzać nimi przy użyciu tych metod:

+ [Kreator importu danych > portalu](search-import-data-portal.md)
+ [Interfejs API REST usługi](/rest/api/searchservice/Indexer-operations)
+ [Zestaw SDK platformy .NET](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

Jeśli używasz zestawu SDK, Utwórz [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) do pracy z indeksatorami, źródłami danych i umiejętności. Powyższy link dotyczy zestawu .NET SDK, ale wszystkie zestawy SDK udostępniają SearchIndexerClient i podobne interfejsy API.

Początkowo nowe źródła danych są anonsowane jako funkcje w wersji zapoznawczej i są tylko w trybie spoczynku. Po zakończeniu skalowania do ogólnej dostępności pełna obsługa jest wbudowana w portal i w różnych zestawach SDK, z których każdy ma własne harmonogramy wydań.

## <a name="permissions"></a>Uprawnienia

Wszystkie operacje związane z indeksatorami, w tym żądania pobrania dla stanu lub definicji, wymagają [klucza API-Key administratora](search-security-api-keys.md).

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Obsługiwane źródła danych

Indeksatory przeszukują magazyny danych na platformie Azure.

+ [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (w wersji zapoznawczej)
+ [Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [SQL Managed Instance](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [Program SQL Server na maszynach wirtualnych platformy Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

## <a name="stages-of-indexing"></a>Etapy indeksowania

W początkowym uruchomieniu, gdy indeks jest pusty, indeksator odczytuje wszystkie dane podane w tabeli lub kontenerze. W kolejnych uruchomieniach indeksator może zazwyczaj wykryć i pobrać tylko te dane, które uległy zmianie. W przypadku danych obiektów BLOB wykrywanie zmian jest automatyczne. W przypadku innych źródeł danych, takich jak Azure SQL lub Cosmos DB, wykrywanie zmian musi być włączone.

Dla każdego dokumentu, który pozyskuje, indeksator implementuje lub koordynuje wiele kroków, od pobrania dokumentu do końcowego aparatu wyszukiwania "oddania" do indeksowania. Opcjonalnie indeksator jest również kierownicą w celu wykonywania zestawu umiejętności i wyjść, przy założeniu, że zestawu umiejętności jest zdefiniowany.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="Etapy indeksatora" border="false":::

### <a name="stage-1-document-cracking"></a>Etap 1: łamanie dokumentów

Łamanie dokumentów to proces otwierania plików i wyodrębniania zawartości. W zależności od typu źródła danych indeksator spróbuje wykonać różne operacje, aby wyodrębnić potencjalnie indeksowaną zawartość.  

Przykłady:  

+ Gdy dokument jest rekordem w [źródle danych usługi Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), indeksator wyodrębni wszystkie pola dla tego rekordu.
+ Gdy dokument jest plikiem PDF w [źródle danych BLOB Storage Azure](search-howto-indexing-azure-blob-storage.md), indeksator będzie wyodrębniał tekst, obrazy i metadane.
+ Gdy dokument jest rekordem w [Cosmos DB źródle danych](search-howto-index-cosmosdb.md), indeksator wyodrębni pola i pola podrzędne z dokumentu Cosmos DB.

### <a name="stage-2-field-mappings"></a>Etap 2: mapowania pól 

Indeksator wyodrębnia tekst z pola źródłowego i wysyła je do pola docelowego w indeksie lub w magazynie wiedzy. Gdy nazwy pól i typy są zbieżne, ścieżka jest wyczyszczona. Można jednak użyć różnych nazw lub typów w danych wyjściowych. w takim przypadku należy określić indeksator, w jaki sposób ma być mapowany na pole. Ten krok występuje po popękaniu dokumentu, ale przed przekształceniami, gdy indeksator odczytuje z dokumentów źródłowych. Podczas definiowania [mapowania pól](search-indexer-field-mappings.md)wartość pola źródłowego jest wysyłana jako-do pola docelowego bez żadnych modyfikacji. Mapowania pól są opcjonalne.

### <a name="stage-3-skillset-execution"></a>Etap 3: wykonywanie zestawu umiejętności

Wykonywanie zestawu umiejętności jest opcjonalnym krokiem, który wywołuje wbudowane lub niestandardowe przetwarzanie AI. Może być konieczne w celu rozpoznania optycznego rozpoznawania znaków (OCR) w postaci analizy obrazu lub konieczności tłumaczenia języka. Niezależnie od transformacji zestawu umiejętności wykonywanie jest miejsce, w którym występuje wzbogacanie. Jeśli indeksator jest potokiem, można myśleć o [zestawu umiejętności](cognitive-search-defining-skillset.md) jako "potok w obrębie potoku". Zestawu umiejętności ma własną sekwencję kroków o nazwie umiejętności.

### <a name="stage-4-output-field-mappings"></a>Etap 4: mapowania pól wyjściowych

Dane wyjściowe zestawu umiejętności to w rzeczywistości drzewo informacji o nazwie wzbogacony dokument. Mapowania pól wyjściowych umożliwiają wybranie, które części tego drzewa mają być mapowane na pola w indeksie. Dowiedz się, jak [definiować mapowania pól wyjściowych](cognitive-search-output-field-mapping.md).

Podobnie jak mapowania pól, które kojarzą wartości Verbatim z pól źródłowych do docelowych, mapowania pól wyjściowych wskazują indeksator, w jaki sposób należy skojarzyć przekształcone wartości z polami docelowymi w indeksie. W przeciwieństwie do mapowań pól, które są uważane za opcjonalne, zawsze trzeba zdefiniować mapowanie pola danych wyjściowych dla dowolnej przekształconej zawartości, która musi znajdować się w indeksie.

Na następnym obrazie przedstawiono reprezentację etapów [debugowania](cognitive-search-debug-session.md) z przykładowym indeksatorem: łamanie dokumentów, mapowania pól, wykonywanie zestawu umiejętności oraz mapowania pól danych wyjściowych.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="przykładowa sesja debugowania" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-configuration-steps"></a>Podstawowe kroki konfiguracji

Indeksatory oferują funkcje, które są unikatowe dla źródła danych. W związku z tym niektóre aspekty konfiguracji indeksatora lub źródła danych różnią się w zależności od typu indeksatora. Wszystkie indeksatory korzystają jednak z takich samych kompozycji i wymagań. Kroki, które są wspólne dla wszystkich indeksatorów, znajdują się poniżej.

### <a name="step-1-create-a-data-source"></a>Krok 1. Tworzenie źródła danych

Indeksator uzyskuje połączenie ze źródłem danych z obiektu *źródła danych* . Definicja źródła danych zawiera parametry połączenia i prawdopodobnie poświadczenia. Wywołaj interfejs API REST [Create DataSource](/rest/api/searchservice/create-data-source) lub [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) , aby utworzyć zasób.

Źródła danych są konfigurowane i zarządzane niezależnie od indeksatorów, które z nich korzystają. Oznacza to, że jedno źródło może być używane przez wiele indeksatorów w celu jednoczesnego ładowania kilku indeksów.

### <a name="step-2-create-an-index"></a>Krok 2. Tworzenie indeksu

Indeksator automatyzuje niektóre zadania związane z pozyskiwaniem danych, ale tworzenie indeksu na ogół nie należy do tych zadań. Jako warunek wstępny należy posiadać wstępnie zdefiniowany indeks z polami, które odpowiadają polom w zewnętrznym źródle danych. Pola muszą być zgodne według nazwy i typu danych. Aby uzyskać więcej informacji na temat tworzenia struktury indeksu, zobacz [Tworzenie indeksu (interfejs API REST platformy Azure wyszukiwanie poznawcze)](/rest/api/searchservice/Create-Index) lub [klasy SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex). Aby uzyskać pomoc dotyczącą skojarzeń pól, zobacz [mapowania pól w usłudze Azure wyszukiwanie poznawcze indeksatory](search-indexer-field-mappings.md).

> [!Tip]
> Chociaż indeksatory nie generują automatycznie indeksu, kreator **Importowanie danych** w portalu może pomóc w wykonaniu tego zadania. W większości przypadków kreator może rozpoznać schemat indeksu na podstawie istniejących metadanych w źródle i przedstawić wstępny schemat indeksu, który można edytować przy użyciu funkcji wbudowanych w tym kreatorze. Po utworzeniu indeksu w usłudze możliwość dalszej edycji w portalu jest w większości ograniczona do dodawania nowych pól. Należy rozważyć użycie kreatora do tworzenia indeksu, ale nie do jego poprawiania. Aby nauczyć się wykonywania tych zadań w praktyce, skorzystaj z [przewodnika po portalu](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Krok 3. Tworzenie indeksatora i ustawianie jego harmonogramu

Definicja indeksatora to konstrukcja, która łączy wszystkie elementy związane z pozyskiwaniem danych. Wymagane elementy obejmują źródło i indeks danych. Opcjonalne elementy obejmują mapowania harmonogramu i pola. Mapowania pól są opcjonalne tylko wtedy, gdy pola źródłowe i pola indeksu są wyraźnie zgodne. Aby uzyskać więcej informacji o tworzeniu struktury indeksatora, zobacz [Create indeksatorer (Azure wyszukiwanie POZNAWCZE REST API)](/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Uruchamianie indeksatorów na żądanie

Chociaż często jest planowane planowanie indeksowania, indeksator może być również wywoływany na żądanie przy użyciu [polecenia Run](/rest/api/searchservice/run-indexer):

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2020-06-30
api-key: [Search service admin key]
```

> [!NOTE]
> Gdy funkcja Run API zwraca kod sukcesu, zaplanowano wywołanie indeksatora, ale rzeczywiste przetwarzanie odbywa się asynchronicznie. 

Stan indeksatora można monitorować w portalu lub za pomocą [interfejsu API pobierania stanu indeksatora](/rest/api/searchservice/get-indexer-status). 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Pobierz stan indeksatora

Można pobrać stan i historię wykonywania indeksatora za pomocą [polecenia Pobierz indeksator stanu](/rest/api/searchservice/get-indexer-status):

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

Odpowiedź zawiera ogólny stan indeksatora, ostatnie (lub w toku) wywołanie indeksatora i historię najnowszych wywołań indeksatora.

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

Historia wykonywania zawiera do 50 najnowszych zakończonych wykonań, które są sortowane w odwrotnej kolejności chronologicznej (w związku z czym ostatnie wykonanie jest najpierw w odpowiedzi).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz już podstawowe informacje, następnym krokiem jest przegląd wymagań i zadań specyficznych dla poszczególnych typów źródeł danych.

+ [Azure SQL Database, wystąpienie zarządzane SQL lub SQL Server na maszynie wirtualnej platformy Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Indeksowanie obiektów BLOB woluminów CSV za pomocą indeksatora usługi Azure Wyszukiwanie poznawcze BLOB](search-howto-index-csv-blobs.md)
+ [Indeksowanie obiektów BLOB JSON za pomocą indeksatora usługi Azure Wyszukiwanie poznawcze BLOB](search-howto-index-json-blobs.md)