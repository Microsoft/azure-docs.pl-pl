---
title: Omówienie indeksatora
titleSuffix: Azure Cognitive Search
description: Przeszukiwanie Azure SQL Database, wystąpienia zarządzanego SQL, Azure Cosmos DB lub usługi Azure Storage w celu wyodrębnienia danych z możliwością wyszukiwania i wypełnienia indeksu Wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: a274e96defa8b6b74c046923d87f198029399dd4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100098099"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indeksatory w usłudze Azure Cognitive Search

*Indeksator* na platformie Azure wyszukiwanie poznawcze to przeszukiwarka, która wyodrębnia tekst z możliwością wyszukiwania i metadane z zewnętrznego źródła danych platformy Azure i wypełnia indeks wyszukiwania przy użyciu mapowań pól między danymi źródłowymi a indeksem. Takie podejście jest czasami nazywane "modelem ściągania", ponieważ usługa ściąga dane w programie bez konieczności pisania kodu, który dodaje dane do indeksu. Indeksatory mogą również dotyczyć możliwości [wzbogacania AI](cognitive-search-concept-intro.md) wyszukiwanie poznawcze, integrując zewnętrzne przetwarzanie zawartości w trasie do indeksu.

Indeksatory są oparte tylko na platformie Azure, z poszczególnymi indeksatorami dla usług [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md), [Table Storage platformy Azure](search-howto-indexing-azure-tables.md) i [BLOB Storage](search-howto-indexing-azure-blob-storage.md). Podczas konfigurowania indeksatora należy określić źródło danych, a także indeks (miejsce docelowe). Kilka źródeł, takich jak BLOB Storage, ma dodatkowe właściwości konfiguracji specyficzne dla tego typu zawartości.

Indeksatory można uruchamiać na żądanie lub według cyklicznego harmonogramu odświeżania danych uruchamianego co pięć minut. Częstsze aktualizacje wymagają ["wypychania modelu"](search-what-is-data-import.md) , który jednocześnie aktualizuje dane zarówno w usłudze Azure wyszukiwanie poznawcze, jak i w zewnętrznym źródle danych.

## <a name="usage-scenarios"></a>Scenariusze użycia

Można użyć indeksatora jako jedynego środka do pozyskiwania danych lub jako część kombinacji technik, które ładują i opcjonalnie przekształcają lub wzbogacają zawartość w sposób. Poniższa tabela zawiera podsumowanie głównych scenariuszy.

| Scenariusz |Strategia |
|----------|---------|
| Pojedyncze źródło danych | Ten wzorzec jest najprostszy: jedno źródło danych jest jedynym dostawcą zawartości dla indeksu wyszukiwania. Ze źródła można zidentyfikować jedno pole zawierające unikatowe wartości, które będzie stanowić klucz dokumentu w indeksie wyszukiwania. Unikatowa wartość zostanie użyta jako identyfikator. Wszystkie inne pola źródłowe są mapowane niejawnie lub jawnie do odpowiednich pól w indeksie. </br></br>Ważnym wnioskiem jest to, że wartość klucza dokumentu pochodzi z danych źródłowych. Usługa wyszukiwania nie generuje wartości kluczy. W kolejnych uruchomieniach dokumenty przychodzące z nowymi kluczami są dodawane, podczas gdy dokumenty przychodzące z istniejącymi kluczami są scalone lub zastępowane, w zależności od tego, czy pola indeksu mają wartość null czy wypełniono. |
| Wiele źródeł danych | Indeks może akceptować zawartość z wielu źródeł, gdzie każda z nich uruchamia nową zawartość z innego źródła. </br></br>Jeden z wyników może być indeksem, który uzyskuje dokumenty po uruchomieniu każdego indeksatora, z całymi dokumentami utworzonymi w całości z każdego źródła. Przykładowo dokumenty 1-100 pochodzą z magazynu obiektów blob, dokumenty 101-200 pochodzą z usługi Azure SQL i tak dalej. Wyzwanie dla tego scenariusza polega na projektowaniu schematu indeksu, który działa dla wszystkich danych przychodzących, oraz struktury klucza dokumentu, która jest jednolita w indeksie wyszukiwania. Natywnie wartości, które jednoznacznie identyfikują dokument, są metadata_storage_path w kontenerze obiektów blob i klucz podstawowy w tabeli SQL. Można wyobrazić, że jedno lub oba źródła należy zmienić w celu zapewnienia wartości kluczy w typowym formacie, niezależnie od źródła zawartości. W tym scenariuszu należy zastanowić się, aby przetworzyć pewien poziom przetwarzania wstępnego, aby homogenizować dane, aby można było je ściągnąć do jednego indeksu. </br></br>Alternatywny wynik może wyszukiwać dokumenty, które są częściowo wypełniane podczas pierwszego uruchomienia, a następnie uzupełniane przez kolejne uruchomienia w celu wprowadzenia wartości z innych źródeł. Na przykład pola 1-10 pochodzą z magazynu obiektów blob, 11-20 z usługi Azure SQL i tak dalej. Wyzwaniem tego wzorca jest upewnienie się, że każdy przebieg indeksowania ma cel tego samego dokumentu. Scalanie pól w istniejącym dokumencie wymaga dopasowania klucza dokumentu. Aby zapoznać się z prezentacją tego scenariusza, zobacz [Samouczek: indeks z wielu źródeł danych](tutorial-multiple-data-sources.md). |
| Wiele indeksatorów | Jeśli używasz wielu źródeł danych, może być również konieczne użycie wielu indeksatorów, jeśli trzeba zmienić parametry czasu wykonywania, harmonogram lub mapowania pól. Chociaż wiele zestawów indeksatora-źródła danych może mieć ten sam indeks, należy ostrożnie uruchamiać indeksatory, które mogą zastąpić istniejące wartości w indeksie. Jeśli drugie źródło danych indeksatora wskazuje te same dokumenty i pola, wszystkie wartości z pierwszego uruchomienia zostaną nadpisywane. Wartości pól są zamienione w całości; indeksator nie może scalić wartości z wielu przebiegów w tym samym polu.</br></br>Innym przypadkiem użycia wieloskładnikowego jest [skalowanie między regionami z wyszukiwanie poznawcze](search-performance-optimization.md#use-indexers-for-updating-content-on-multiple-services). Mogą istnieć kopie tego samego indeksu wyszukiwania w różnych regionach. Aby synchronizować zawartość indeksu wyszukiwania, może istnieć wiele indeksatorów pobierających z tego samego źródła danych, gdzie każdy indeksator odwołuje się do innego indeksu wyszukiwania.</br></br>[Równoległe indeksowanie](search-howto-large-index.md#parallel-indexing) bardzo dużych zestawów danych wymaga również strategii dotyczącej wielu indeksów. Każdy indeksator odwołuje się do podzestawu danych. |
| Przekształcanie zawartości | Wyszukiwanie poznawcze obsługuje opcjonalne zachowania [wzbogacania AI](cognitive-search-concept-intro.md) , które dodają analizę obrazu i przetwarzanie języka naturalnego w celu utworzenia nowej, przeszukiwanej zawartości i struktury. Wzbogacanie AI jest oparte na indeksatorze przez dołączoną [zestawu umiejętności](cognitive-search-working-with-skillsets.md). Aby przeprowadzić wzbogacanie AI, indeksator nadal potrzebuje indeksu i źródła danych platformy Azure, ale w tym scenariuszu program dodaje zestawu umiejętności do wykonywania indeksatora. |

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Obsługiwane źródła danych

Indeksatory przeszukują magazyny danych na platformie Azure.

+ [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (w wersji zapoznawczej)
+ [Table Storage platformy Azure](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Wystąpienie zarządzane SQL](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [Program SQL Server na maszynach wirtualnych platformy Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

Połączenia indeksatora ze zdalnymi źródłami danych mogą być nawiązywane przy użyciu standardowych połączeń internetowych (publicznych) lub szyfrowanych prywatnych połączeń, gdy używane są usługi Azure Virtual Networks dla aplikacji klienckich. Możesz również skonfigurować połączenia do uwierzytelniania za pomocą zaufanej tożsamości usługi. Aby uzyskać więcej informacji na temat bezpiecznych połączeń, zobacz [udzielanie dostępu za pośrednictwem prywatnych punktów końcowych](search-indexer-securing-resources.md#granting-access-via-private-endpoints) i [nawiązywanie połączenia ze źródłem danych przy użyciu tożsamości zarządzanej](search-howto-managed-identities-data-sources.md).

## <a name="stages-of-indexing"></a>Etapy indeksowania

W początkowym uruchomieniu, gdy indeks jest pusty, indeksator odczytuje wszystkie dane podane w tabeli lub kontenerze. W kolejnych uruchomieniach indeksator może zazwyczaj wykryć i pobrać tylko te dane, które uległy zmianie. W przypadku danych obiektów BLOB wykrywanie zmian jest automatyczne. W przypadku innych źródeł danych, takich jak Azure SQL lub Cosmos DB, wykrywanie zmian musi być włączone.

Dla każdego otrzymanego dokumentu indeksator implementuje lub koordynuje wiele kroków, od pobrania dokumentu do końcowego aparatu wyszukiwania "oddania" do indeksowania. Opcjonalnie indeksator jest również kierownicą w celu wykonywania zestawu umiejętności i wyjść, przy założeniu, że zestawu umiejętności jest zdefiniowany.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="Etapy indeksatora" border="false":::

### <a name="stage-1-document-cracking"></a>Etap 1: łamanie dokumentów

Łamanie dokumentów to proces otwierania plików i wyodrębniania zawartości. W zależności od typu źródła danych indeksator spróbuje wykonać różne operacje, aby wyodrębnić potencjalnie indeksowaną zawartość.  

Przykłady:  

+ Gdy dokument jest rekordem w [źródle danych usługi Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), indeksator wyodrębni wszystkie pola dla tego rekordu.
+ Gdy dokument jest plikiem PDF w [źródle danych BLOB Storage Azure](search-howto-indexing-azure-blob-storage.md), indeksator będzie wyodrębniał tekst, obrazy i metadane.
+ Gdy dokument jest rekordem w [Cosmos DB źródle danych](search-howto-index-cosmosdb.md), indeksator wyodrębni pola i pola podrzędne z dokumentu Cosmos DB.

### <a name="stage-2-field-mappings"></a>Etap 2: mapowania pól 

Indeksator wyodrębnia tekst z pola źródłowego i wysyła je do pola docelowego w indeksie lub w magazynie wiedzy. Gdy nazwy pól i typy są zbieżne, ścieżka jest wyczyszczona. Można jednak użyć różnych nazw lub typów w danych wyjściowych. w takim przypadku należy określić indeksator, w jaki sposób ma być mapowany na pole. 

Ten krok występuje po popękaniu dokumentu, ale przed przekształceniami, gdy indeksator odczytuje z dokumentów źródłowych. Podczas definiowania [mapowania pól](search-indexer-field-mappings.md)wartość pola źródłowego jest wysyłana jako-do pola docelowego bez żadnych modyfikacji. 

### <a name="stage-3-skillset-execution"></a>Etap 3: wykonywanie zestawu umiejętności

Wykonywanie zestawu umiejętności jest opcjonalnym krokiem, który wywołuje wbudowane lub niestandardowe przetwarzanie AI. Może być potrzebny do rozpoznawania znaków optycznych (OCR) w formie analizy obrazów, jeśli dane źródłowe są obrazami binarnymi lub jeśli zawartość znajduje się w różnych językach, może być konieczne tłumaczenie języka. 

Niezależnie od transformacji zestawu umiejętności wykonywanie jest miejsce, w którym występuje wzbogacanie. Jeśli indeksator jest potokiem, można myśleć o [zestawu umiejętności](cognitive-search-defining-skillset.md) jako "potok w obrębie potoku".

### <a name="stage-4-output-field-mappings"></a>Etap 4: mapowania pól wyjściowych

Jeśli dołączysz zestawu umiejętności, najprawdopodobniej będzie trzeba uwzględnić mapowania pól wyjściowych. Dane wyjściowe zestawu umiejętności to w rzeczywistości drzewo informacji o nazwie *wzbogacony dokument*. Mapowania pól wyjściowych umożliwiają wybranie, które części tego drzewa mają być mapowane na pola w indeksie. Dowiedz się, jak [definiować mapowania pól wyjściowych](cognitive-search-output-field-mapping.md).

Mapowania pól kojarzą wartości Verbatim ze źródła danych z polami docelowymi, natomiast mapowania pól wyjściowych wskazują indeksator, w jaki sposób należy skojarzyć przekształcone wartości w dodanym wzbogaconym dokumencie do pól docelowych w indeksie. W przeciwieństwie do mapowań pól, które są uważane za opcjonalne, zawsze trzeba zdefiniować mapowanie pola danych wyjściowych dla dowolnej przekształconej zawartości, która musi znajdować się w indeksie.

Na następnym obrazie przedstawiono reprezentację etapów [debugowania](cognitive-search-debug-session.md) z przykładowym indeksatorem: łamanie dokumentów, mapowania pól, wykonywanie zestawu umiejętności oraz mapowania pól danych wyjściowych.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="przykładowa sesja debugowania" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-workflow"></a>Podstawowy przepływ pracy

Indeksatory oferują funkcje, które są unikatowe dla źródła danych. W związku z tym niektóre aspekty konfiguracji indeksatora lub źródła danych różnią się w zależności od typu indeksatora. Wszystkie indeksatory korzystają jednak z takich samych kompozycji i wymagań. Kroki, które są wspólne dla wszystkich indeksatorów, znajdują się poniżej.

### <a name="step-1-create-a-data-source"></a>Krok 1. Tworzenie źródła danych

Indeksatory wymagają obiektu *źródła danych* , który dostarcza parametry połączenia i prawdopodobnie poświadczenia. Wywołaj klasę [Create Data Source (REST)](/rest/api/searchservice/create-data-source) lub [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) , aby utworzyć zasób.

Źródła danych są konfigurowane i zarządzane niezależnie od indeksatorów, które z nich korzystają. Oznacza to, że jedno źródło może być używane przez wiele indeksatorów w celu jednoczesnego ładowania kilku indeksów.

### <a name="step-2-create-an-index"></a>Krok 2. Tworzenie indeksu

Indeksator automatyzuje niektóre zadania związane z pozyskiwaniem danych, ale tworzenie indeksu na ogół nie należy do tych zadań. Jako warunek wstępny należy posiadać wstępnie zdefiniowany indeks z polami, które odpowiadają polom w zewnętrznym źródle danych. Pola muszą być zgodne według nazwy i typu danych. Jeśli nie, możesz [zdefiniować mapowania pól](search-indexer-field-mappings.md) , aby ustanowić skojarzenie. Aby uzyskać więcej informacji o tworzeniu struktury indeksu, zobacz [Tworzenie indeksu (REST)](/rest/api/searchservice/Create-Index) lub [klasy SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex).

> [!Tip]
> Chociaż indeksatory nie generują automatycznie indeksu, kreator **Importowanie danych** w portalu może pomóc w wykonaniu tego zadania. W większości przypadków kreator może rozpoznać schemat indeksu na podstawie istniejących metadanych w źródle i przedstawić wstępny schemat indeksu, który można edytować przy użyciu funkcji wbudowanych w tym kreatorze. Po utworzeniu indeksu w usłudze możliwość dalszej edycji w portalu jest w większości ograniczona do dodawania nowych pól. Należy rozważyć użycie kreatora do tworzenia indeksu, ale nie do jego poprawiania. Aby nauczyć się wykonywania tych zadań w praktyce, skorzystaj z [przewodnika po portalu](search-get-started-portal.md).

### <a name="step-3-create-and-run-or-schedule-the-indexer"></a>Krok 3. Tworzenie i uruchamianie indeksatora (lub harmonogramu)

Indeksator jest uruchamiany podczas pierwszego [utworzenia indeksatora](/rest/api/searchservice/Create-Indexer) usługi wyszukiwania. Jest to możliwe tylko w przypadku tworzenia lub uruchamiania indeksatora, który można sprawdzić, jeśli źródło danych jest dostępne lub zestawu umiejętności jest prawidłowy. Po pierwszym uruchomieniu można uruchomić go ponownie na żądanie przy użyciu [indeksatora uruchamiania](/rest/api/searchservice/run-indexer)lub [zdefiniować harmonogram cykliczny](search-howto-schedule-indexers.md). 

Stan indeksatora można monitorować w portalu lub za pomocą [interfejsu API pobierania stanu indeksatora](/rest/api/searchservice/get-indexer-status). Należy również [uruchomić zapytania na indeksie](search-query-create.md) , aby sprawdzić, czy wynik jest oczekiwany.

## <a name="next-steps"></a>Następne kroki

Po wprowadzeniu, następnym krokiem jest zapoznanie się z właściwościami indeksatora, parametrami, planowaniem i monitorowaniem indeksatora. Alternatywnie można wrócić do listy [obsługiwanych źródeł danych](#supported-data-sources) , aby uzyskać więcej informacji na temat konkretnego źródła.

+ [Tworzenie indeksatorów](search-howto-create-indexers.md)
+ [Zresetuj i uruchom indeksatory](search-howto-run-reset-indexers.md)
+ [Planowanie indeksatorów](search-howto-schedule-indexers.md)
+ [Definiuj mapowania pól](search-indexer-field-mappings.md)
+ [Monitorowanie stanu indeksatora](search-howto-monitor-indexers.md)