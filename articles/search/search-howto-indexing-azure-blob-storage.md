---
title: Konfigurowanie indeksatora obiektów BLOB
titleSuffix: Azure Cognitive Search
description: Skonfiguruj indeksator usługi Azure BLOB do automatyzowania indeksowania zawartości obiektów BLOB dla operacji wyszukiwania pełnotekstowego w usłudze Azure Wyszukiwanie poznawcze.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: e3419711c9a7358914f85574f6dbd5af29def1cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91403618"
---
# <a name="how-to-configure-a-blob-indexer-in-azure-cognitive-search"></a>Jak skonfigurować indeksator obiektów BLOB na platformie Azure Wyszukiwanie poznawcze

W tym artykule pokazano, jak za pomocą usługi Azure Wyszukiwanie poznawcze indeksować dokumenty tekstowe (takie jak pliki PDF, dokumenty Microsoft Office i inne popularne formaty) przechowywane w usłudze Azure Blob Storage. Najpierw objaśnia podstawowe informacje na temat konfigurowania i konfigurowania indeksatora obiektów BLOB. Następnie oferuje dokładniejszą eksplorację zachowań i scenariuszy, które prawdopodobnie napotkasz.

<a name="SupportedFormats"></a>

## <a name="supported-formats"></a>Obsługiwane formaty

Indeksator obiektów BLOB może wyodrębnić tekst z następujących formatów dokumentu:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="set-up-blob-indexing"></a>Konfigurowanie indeksowania obiektów BLOB

Można skonfigurować usługę Azure Blob Storage indeksator przy użyciu:

* [Azure Portal](https://ms.portal.azure.com)
* [Interfejs API REST](/rest/api/searchservice/Indexer-operations) usługi Azure wyszukiwanie poznawcze
* Azure Wyszukiwanie poznawcze [.NET SDK](/dotnet/api/overview/azure/search)

> [!NOTE]
> Niektóre funkcje (na przykład mapowania pól) nie są jeszcze dostępne w portalu i muszą być używane programowo.
>

W tym miejscu zademonstrowano przepływ przy użyciu interfejsu API REST.

### <a name="step-1-create-a-data-source"></a>Krok 1. Tworzenie źródła danych

Źródło danych określa, które dane mają być indeksowane, które są potrzebne do uzyskiwania dostępu do danych, oraz zasady umożliwiające wydajne identyfikowanie zmian w danych (nowych, zmodyfikowanych lub usuniętych wierszy). Źródło danych może być używane przez wiele indeksatorów w tej samej usłudze wyszukiwania.

W przypadku indeksowania obiektów BLOB źródło danych musi mieć następujące wymagane właściwości:

* **Nazwa** jest unikatową nazwą źródła danych w ramach usługi wyszukiwania.
* **Typ** musi być `azureblob` .
* * * poświadczenia podaj parametry połączenia konta magazynu jako `credentials.connectionString` parametr. Aby uzyskać szczegółowe informacje [, zobacz Jak określić poświadczenia](#Credentials) poniżej.
* **kontener** Określa kontener na koncie magazynu. Domyślnie można pobrać wszystkie obiekty blob w kontenerze. Jeśli chcesz tylko indeksować obiekty blob w konkretnym katalogu wirtualnym, możesz określić ten katalog przy użyciu opcjonalnego parametru **zapytania** .

Aby utworzyć źródło danych:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }
```

Aby uzyskać więcej informacji na temat interfejsu API tworzenia źródła danych, zobacz [Create DataSource](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>

#### <a name="how-to-specify-credentials"></a>Jak określić poświadczenia

Poświadczenia dla kontenera obiektów BLOB można podać w jeden z następujących sposobów:

* **Parametry połączenia tożsamości zarządzanej**: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;` 

  Te parametry połączenia nie wymagają klucza konta, ale musisz postępować zgodnie z instrukcjami dotyczącymi [konfigurowania połączenia z kontem usługi Azure Storage przy użyciu tożsamości zarządzanej](search-howto-managed-identities-storage.md).

* **Parametry połączenia z pełnymi dostępem do konta magazynu**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`

  Parametry połączenia można uzyskać z Azure Portal, przechodząc do bloku konto magazynu > ustawienia > klucze (dla klasycznych kont magazynu) lub ustawienia > klucze dostępu (dla Azure Resource Manager kont magazynu).

* Parametry połączenia **sygnatury dostępu współdzielonego** (SAS) konta magazynu:`BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl`

  Sygnatura dostępu współdzielonego powinna mieć uprawnienia do listy i odczytu w kontenerach i obiektach (w tym przypadku obiektów BLOB).

* **Sygnatura dostępu współdzielonego kontenera**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl`

  Sygnatura dostępu współdzielonego powinna mieć uprawnienia do listy i odczytu kontenera.

Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego magazynu, zobacz [Używanie sygnatur dostępu współdzielonego](../storage/common/storage-sas-overview.md).

> [!NOTE]
> W przypadku używania poświadczeń SAS należy okresowo zaktualizować poświadczenia źródła danych za pomocą odnowionych podpisów, aby zapobiec ich wygaśnięciu. Jeśli poświadczenia sygnatury dostępu współdzielonego wygasną, indeksator zakończy się niepowodzeniem z komunikatem o błędzie podobnym do `Credentials provided in the connection string are invalid or have expired.` .  

### <a name="step-2-create-an-index"></a>Krok 2. Tworzenie indeksu

Indeks określa pola w dokumencie, atrybuty i inne konstrukcje, które kształtują środowisko wyszukiwania.

Oto jak utworzyć indeks z polem z możliwością wyszukiwania, `content` Aby przechowywać tekst wyodrębniony z obiektów blob:

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

Aby uzyskać więcej informacji, zobacz [Tworzenie indeksu (interfejs API REST)](/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Krok 3. Tworzenie indeksatora

Indeksator łączy źródło danych z docelowym indeksem wyszukiwania i zawiera harmonogram do automatyzowania odświeżania danych.

Po utworzeniu indeksu i źródła danych można rozpocząć tworzenie indeksatora:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Ten indeksator będzie uruchamiany co dwie godziny (interwał harmonogramu jest ustawiony na wartość "PT2H"). Aby uruchomić indeksator co 30 minut, ustaw interwał na wartość "PT30M". Najkrótszy obsługiwany interwał to 5 minut. Harmonogram jest opcjonalny — w przypadku pominięcia, indeksator jest uruchamiany tylko raz, gdy zostanie utworzony. Można jednak uruchomić indeksator na żądanie w dowolnym momencie.   

Aby uzyskać więcej informacji, zobacz [Tworzenie indeksatora (interfejs API REST)](/rest/api/searchservice/create-indexer). Więcej informacji o definiowaniu harmonogramów indeksatorów znajduje się w temacie [jak zaplanować indeksatory dla platformy Azure wyszukiwanie poznawcze](search-howto-schedule-indexers.md).

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-blobs-are-indexed"></a>Jak są indeksowane obiekty blob

W zależności od [konfiguracji indeksatora](#PartsOfBlobToIndex)indeksator obiektów BLOB może indeksować tylko metadane magazynu (przydatne w przypadku ponoszenia informacji o metadanych i braku potrzeby indeksowania zawartości obiektów BLOB), magazynu i metadanych zawartości, a także metadanych i zawartości tekstowej. Domyślnie indeksator wyodrębnia zarówno metadane, jak i zawartość.

> [!NOTE]
> Domyślnie obiekty blob o zawartości strukturalnej, takiej jak JSON lub CSV, są indeksowane jako pojedynczy fragment tekstu. Jeśli chcesz indeksować obiekty blob JSON i CSV w uporządkowany sposób, zobacz [indeksowanie obiektów BLOB JSON](search-howto-index-json-blobs.md) i [indeksowanie obiektów BLOB woluminów CSV](search-howto-index-csv-blobs.md) , aby uzyskać więcej informacji.
>
> Dokument złożony lub osadzony (na przykład archiwum ZIP, dokument programu Word z osadzoną wiadomością e-mail programu Outlook zawierającą załączniki lub. Plik MSG z załącznikami) jest również indeksowany jako pojedynczy dokument. Na przykład wszystkie obrazy wyodrębnione z załączników. Plik MSG zostanie zwrócony w polu normalized_images.

* Zawartość tekstowa dokumentu jest wyodrębniana do pola ciągu o nazwie `content` .

  > [!NOTE]
  > Usługa Azure Wyszukiwanie poznawcze ogranicza ilość tekstu wyodrębnianego w zależności od warstwy cenowej: 32 000 znaków dla warstwy Bezpłatna, 64 000 w przypadku wersji podstawowa, 4 000 000 dla Standard, 8 000 000 dla standardu S2 i 16 000 000 dla standardowego stanu S3. Ostrzeżenie jest zawarte w odpowiedzi stanu indeksatora dla obciętych dokumentów.  

* Właściwości metadanych określone przez użytkownika w obiekcie BLOB (jeśli istnieją) są wyodrębniane Verbatim. Należy zauważyć, że wymaga to pola, które ma być zdefiniowane w indeksie o takiej samej nazwie jak klucz metadanych obiektu BLOB. Na przykład jeśli obiekt BLOB zawiera klucz metadanych o `Sensitivity` wartości `High` , należy zdefiniować pole o nazwie `Sensitivity` w indeksie wyszukiwania i zostanie ono wypełnione wartością `High` .

* Standardowe właściwości metadanych obiektów BLOB są wyodrębniane do następujących pól:

  * ** \_ \_ Nazwa magazynu metadanych** (EDM. String) — nazwa pliku obiektu BLOB. Na przykład jeśli masz resume.pdf obiektu BLOB, wartość tego pola to `resume.pdf` .

  * ** \_ \_ ścieżka magazynu metadanych** (EDM. String) — pełny identyfikator URI obiektu BLOB, w tym konto magazynu. Na przykład `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

  * ** \_ \_ \_ Typ zawartości magazynu metadanych** (EDM. String) — typ zawartości określony przez kod, który został użyty do przekazania obiektu BLOB. Na przykład `application/octet-stream`.

  * ** \_ \_ ostatnio \_ modyfikowany magazyn metadanych** (EDM. DateTimeOffset) — sygnatura czasowa ostatniej modyfikacji dla obiektu BLOB. Usługa Azure Wyszukiwanie poznawcze używa tej sygnatury czasowej do identyfikowania zmienionych obiektów blob, aby uniknąć ponownego indeksowania wszystkiego po początkowej indeksowaniu.

  * ** \_ \_ Rozmiar magazynu metadanych** (EDM. Int64) — rozmiar obiektu BLOB w bajtach.

  * **wartość \_ \_ \_ MD5 zawartości magazynu metadanych** (EDM. String) — skrót MD5 zawartości obiektu BLOB, jeśli jest dostępny.

  * ** \_ \_ \_ token SAS magazynu metadanych** (EDM. String) — tymczasowy token SAS, który może być używany przez [niestandardowe umiejętności](cognitive-search-custom-skill-interface.md) w celu uzyskania dostępu do obiektu BLOB. Ten token nie powinien być przechowywany do późniejszego użycia, ponieważ mógł wygasnąć.

* Właściwości metadanych specyficzne dla każdego formatu dokumentu są wyodrębniane do pól wymienionych w [tym miejscu](#ContentSpecificMetadata).

Nie musisz definiować pól dla wszystkich powyższych właściwości w indeksie wyszukiwania — wystarczy przechwycić właściwości potrzebne dla aplikacji.

> [!NOTE]
> Często nazwy pól w istniejącym indeksie będą różnić się od nazw pól generowanych podczas wyodrębniania dokumentu. **Mapowania pól** można użyć do mapowania nazw właściwości dostarczanych przez usługę Azure wyszukiwanie poznawcze do nazw pól w indeksie wyszukiwania. Zobaczysz przykładowe mapowania pól poniżej.
>

<a name="DocumentKeys"></a>

### <a name="defining-document-keys-and-field-mappings"></a>Definiowanie kluczy dokumentu i mapowań pól

W usłudze Azure Wyszukiwanie poznawcze klucz dokumentu jednoznacznie identyfikuje dokument. Każdy indeks wyszukiwania musi mieć dokładnie jedno pole klucza typu EDM. String. Pole klucza jest wymagane dla każdego dokumentu dodawanego do indeksu (w rzeczywistości jest to jedyne pole wymagane).  

Należy uważnie rozważyć, które wyodrębnione pole powinno być mapowane na pole klucza dla indeksu. Kandydaci są:

* ** \_ \_ Nazwa magazynu metadanych** — może to być wygodny kandydat, ale należy zauważyć, że 1) nazwy mogą nie być unikatowe, ponieważ mogą istnieć obiekty blob o takiej samej nazwie w różnych folderach i 2) nazwa może zawierać znaki, które są nieprawidłowe w kluczach dokumentów, takich jak łączniki. Za pomocą funkcji mapowania pól można zajmować się nieprawidłowymi znakami `base64Encode` [field mapping function](search-indexer-field-mappings.md#base64EncodeFunction) — Jeśli to zrobisz, pamiętaj, aby zakodować klucze dokumentu podczas przekazywania ich w wywołaniach interfejsu API, takich jak odnośnik. (Na przykład w programie .NET można użyć w tym celu [metody UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode) ).

* ** \_ \_ ścieżka magazynu metadanych** — przy użyciu pełnej ścieżki zapewnia unikatowość, ale ścieżka w nieskończoność zawiera `/` znaki, które są [nieprawidłowe w kluczu dokumentu](/rest/api/searchservice/naming-rules).  Jak powyżej, można zakodować klucze przy użyciu `base64Encode` [funkcji](search-indexer-field-mappings.md#base64EncodeFunction).

* Trzecią opcją jest dodanie niestandardowej właściwości metadanych do obiektów BLOB. Ta opcja wymaga jednak, aby proces przekazywania obiektów BLOB dodał tę właściwość metadanych do wszystkich obiektów BLOB. Ponieważ klucz jest wymaganą właściwością, wszystkie obiekty blob, które nie mają tej właściwości, będą kończyć się niepowodzeniem.

> [!IMPORTANT]
> Jeśli nie istnieje jawne mapowanie pola klucza w indeksie, usługa Azure Wyszukiwanie poznawcze automatycznie używa `metadata_storage_path` jako klucza i Base-64 wartości klucza (druga opcja powyżej).
>
>

Na potrzeby tego przykładu wybierzemy `metadata_storage_name` pole jako klucz dokumentu. Załóżmy również, że indeks ma pole klucza o nazwie `key` i pole `fileSize` do przechowywania rozmiaru dokumentu. Aby odpowiednio podpracować, określ następujące mapowania pól podczas tworzenia lub aktualizowania indeksatora:

```http
    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]
```

Aby to zrobić, możesz dodać mapowania pól i włączyć kodowanie Base-64 kluczy dla istniejącego indeksatora:

```http
    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }
```

Aby uzyskać więcej informacji, zobacz [mapowania pól i przekształcenia](search-indexer-field-mappings.md).

#### <a name="what-if-you-need-to-encode-a-field-to-use-it-as-a-key-but-you-also-want-to-search-it"></a>Co zrobić, jeśli zachodzi potrzeba zakodowania pola, aby użyć go jako klucza, ale również chcesz go wyszukać?

Istnieją przypadki, gdy musisz użyć zakodowanej wersji pola, takiego jak metadata_storage_path jako klucz, ale musisz również mieć to pole, aby można je było przeszukiwać (bez kodowania). Aby rozwiązać ten problem, można zamapować go na dwa pola; jeden, który będzie używany jako klucz, i drugi, który będzie używany do celów wyszukiwania. W poniższym przykładzie pole *klucza* zawiera zakodowaną ścieżkę, podczas gdy pole *ścieżki* nie jest kodowane i zostanie użyte jako pole wyszukiwania w indeksie.

```http
    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "path" }
      ]
    }
```
<a name="WhichBlobsAreIndexed"></a>

## <a name="index-by-file-type"></a>Indeksuj według typu pliku

Można kontrolować, które obiekty blob są indeksowane i które są pomijane.

### <a name="include-blobs-having-specific-file-extensions"></a>Uwzględnij obiekty blob z określonymi rozszerzeniami plików

Można indeksować tylko obiekty blob z rozszerzeniami nazw plików, które można określić za pomocą `indexedFileNameExtensions` parametru konfiguracji indeksatora. Wartość jest ciągiem zawierającym listę rozszerzeń plików rozdzielanych przecinkami (z kropką wiodącą). Na przykład, aby zindeksować tylko. PDF i. Pliki BLOB DOCX, wykonaj następujące czynności:

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }
```

### <a name="exclude-blobs-having-specific-file-extensions"></a>Wyklucz obiekty blob z określonymi rozszerzeniami plików

Można wykluczyć obiekty blob z określonymi rozszerzeniami nazw plików z indeksowania przy użyciu `excludedFileNameExtensions` parametru konfiguracji. Wartość jest ciągiem zawierającym listę rozszerzeń plików rozdzielanych przecinkami (z kropką wiodącą). Na przykład, aby indeksować wszystkie obiekty blob z wyjątkiem. PNG i. Rozszerzenia JPEG, wykonaj następujące czynności:

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }
```

Jeśli oba `indexedFileNameExtensions` `excludedFileNameExtensions` Parametry i są obecne, platforma Azure wyszukiwanie poznawcze najpierw wyszukać `indexedFileNameExtensions` , a następnie na `excludedFileNameExtensions` . Oznacza to, że jeśli to samo rozszerzenie pliku znajduje się na obu listach, zostanie wyłączone z indeksowania.

<a name="PartsOfBlobToIndex"></a>

## <a name="index-parts-of-a-blob"></a>Indeksowanie części obiektu BLOB

Można kontrolować, które fragmenty obiektów BLOB są indeksowane przy użyciu `dataToExtract` parametru konfiguracji. Może przyjmować następujące wartości:

* `storageMetadata` -Określa, że indeksowane są tylko [standardowe właściwości obiektów blob i metadane określone przez użytkownika](../storage/blobs/storage-blob-container-properties-metadata.md) .
* `allMetadata` -Określa, że metadane magazynu i [metadane specyficzne dla typu zawartości](#ContentSpecificMetadata) wyodrębnione z zawartości obiektu BLOB są indeksowane.
* `contentAndMetadata` -Określa, że wszystkie metadane i zawartość tekstowa wyodrębnione z obiektu BLOB są indeksowane. Jest to wartość domyślna.

Na przykład aby zindeksować tylko metadane magazynu, użyj:

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }
```

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Używanie metadanych obiektów BLOB do kontrolowania sposobu indeksowania obiektów BLOB

Opisane powyżej parametry konfiguracji dotyczą wszystkich obiektów BLOB. Czasami może być konieczne sterowanie sposobem indeksowania *poszczególnych obiektów BLOB* . Można to zrobić, dodając następujące właściwości i wartości metadanych obiektu BLOB:

| Nazwa właściwości | Wartość właściwości | Objaśnienie |
| --- | --- | --- |
| AzureSearch_Skip |oznacza |Instruuje indeksator obiektu BLOB, aby całkowicie pominąć obiekt BLOB. Nie podjęto próby przeprowadzenia żadnej metadanych ani wyodrębniania zawartości. Jest to przydatne, gdy konkretny obiekt BLOB powtarza się wielokrotnie i przerywa proces indeksowania. |
| AzureSearch_SkipContent |oznacza |Jest to odpowiednik `"dataToExtract" : "allMetadata"` Ustawienia opisanego [powyżej](#PartsOfBlobToIndex) w zakresie określonego obiektu BLOB. |

## <a name="index-from-multiple-sources"></a>Indeks z wielu źródeł

Możesz chcieć "złożyć" dokumenty z wielu źródeł w indeksie. Na przykład możesz chcieć scalić tekst z obiektów blob z innymi metadanymi przechowywanymi w Cosmos DB. Można nawet użyć interfejsu API indeksowania wypychania razem z różnymi indeksatorami w celu utworzenia dokumentów wyszukiwania z wielu części.

Aby to działało, wszystkie indeksatory i inne składniki muszą wyrazić zgodę na klucz dokumentu. Aby uzyskać dodatkowe informacje na temat tego tematu, zobacz [indeksowanie wielu źródeł danych platformy Azure](./tutorial-multiple-data-sources.md) lub tego wpisu w blogu, [łączenie dokumentów z innymi danymi na platformie Azure wyszukiwanie poznawcze](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

## <a name="index-large-datasets"></a>Indeksuj duże zestawy danych

Indeksowanie obiektów BLOB może być czasochłonnym procesem. W przypadkach, gdy masz miliony obiektów BLOB do indeksowania, możesz przyspieszyć indeksowanie, partycjonowanie danych i używanie wielu indeksatorów do równoległego przetwarzania danych. Oto, jak można je skonfigurować:

* Partycjonowanie danych w wielu kontenerach obiektów blob lub w folderach wirtualnych

* Skonfiguruj kilka źródeł danych usługi Azure Wyszukiwanie poznawcze, jeden dla każdego kontenera lub folderu. Aby wskazać folder obiektu BLOB, użyj `query` parametru:

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

* Utwórz odpowiedni indeksator dla każdego źródła danych. Wszystkie indeksatory mogą wskazywać na ten sam docelowy indeks wyszukiwania.  

* Jedna jednostka wyszukiwania w usłudze może uruchamiać jeden indeksator w danym momencie. Tworzenie wielu indeksatorów, jak opisano powyżej, jest przydatne tylko wtedy, gdy rzeczywiście są uruchamiane równolegle. Aby uruchamiać wiele indeksatorów równolegle, Skaluj w poziomie usługę wyszukiwania, tworząc odpowiednią liczbę partycji i replik. Jeśli na przykład usługa wyszukiwania ma 6 jednostek wyszukiwania (na przykład 2 repliki x 3), to 6 indeksatorów może być uruchomionych jednocześnie, co powoduje sześć przyrostów przepływności indeksowania. Aby dowiedzieć się więcej na temat skalowania i planowania pojemności, zobacz [Dostosowywanie pojemności usługi Wyszukiwanie poznawcze platformy Azure](search-capacity-planning.md).

<a name="DealingWithErrors"></a>

## <a name="handle-errors"></a>Obsługa błędów

Domyślnie indeksator obiektu BLOB jest zatrzymywany zaraz po napotkaniu obiektu BLOB z nieobsługiwanym typem zawartości (na przykład obrazem). Możesz użyć `excludedFileNameExtensions` parametru, aby pominąć niektóre typy zawartości. Jednak może być konieczne indeksowanie obiektów Blob bez znajomości wszystkich możliwych typów zawartości z wyprzedzeniem. Aby kontynuować indeksowanie, gdy zostanie napotkany nieobsługiwany typ zawartości, ustaw `failOnUnsupportedContentType` parametr konfiguracji na `false` :

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }
```

W przypadku niektórych obiektów BLOB usługa Azure Wyszukiwanie poznawcze nie może określić typu zawartości lub nie może przetworzyć dokumentu z nieobsługiwanym typem zawartości. Aby zignorować ten tryb niepowodzenia, ustaw `failOnUnprocessableDocument` dla parametru konfiguracji wartość false:

```http
      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Usługa Azure Wyszukiwanie poznawcze ogranicza rozmiar indeksowanych obiektów BLOB. Limity te są udokumentowane w [limitach usługi w usłudze Azure wyszukiwanie poznawcze](./search-limits-quotas-capacity.md). Zbyt duże obiekty blob są domyślnie traktowane jako błędy. Można jednak nadal indeksować metadane magazynu o zwiększonym rozmiarze obiektów BLOB w przypadku ustawienia `indexStorageMetadataOnlyForOversizedDocuments` dla parametru konfiguracji wartości true:

```http
    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

Możesz również kontynuować indeksowanie w przypadku wystąpienia błędów w dowolnym momencie przetwarzania, podczas analizowania obiektów blob lub dodawania dokumentów do indeksu. Aby zignorować określoną liczbę błędów, należy ustawić `maxFailedItems` `maxFailedItemsPerBatch` wymagane wartości parametrów i konfiguracji. Na przykład:

```http
    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }
```

<a name="ContentSpecificMetadata"></a>

## <a name="content-type-specific-metadata-properties"></a>Właściwości metadanych specyficznych dla typu zawartości

Poniższa tabela zawiera podsumowanie przetwarzania dla każdego formatu dokumentu oraz opis właściwości metadanych wyodrębnionych przez usługę Azure Wyszukiwanie poznawcze.

| Format dokumentu/typ zawartości | Wyodrębnione metadane | Szczegóły przetwarzania |
| --- | --- | --- |
| HTML (tekst/HTML) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Rozpakowywanie znacznika HTML i wyodrębnienie tekstu |
| PDF (aplikacja/PDF) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Wyodrębnij tekst, w tym osadzone dokumenty (z wyjątkiem obrazów) |
| DOCX (Application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnij tekst, w tym osadzone dokumenty |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnij tekst, w tym osadzone dokumenty |
| DOCM (Application/vnd.ms-word.document. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnij tekst, w tym osadzone dokumenty |
| WORD XML (application/vnd. MS-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Przepakowywanie znacznika XML i wyodrębnienie tekstu |
| WORD 2003 XML (application/vnd. MS-WordML) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Przepakowywanie znacznika XML i wyodrębnienie tekstu |
| XLSX (application/vnd. openxmlformats-officedocument. SpreadsheetML. Sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnij tekst, w tym osadzone dokumenty |
| XLS (application/vnd. MS-Excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnij tekst, w tym osadzone dokumenty |
| XLSM (application/vnd. MS-Excel. Sheet. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnij tekst, w tym osadzone dokumenty |
| PPTX (application/vnd. openxmlformats-officedocument. presentationml. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Wyodrębnij tekst, w tym osadzone dokumenty |
| PPT (application/vnd. MS-PowerPoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Wyodrębnij tekst, w tym osadzone dokumenty |
| PPTM (application/vnd. MS-PowerPoint. Presentation. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Wyodrębnij tekst, w tym osadzone dokumenty |
| MSG (application/vnd. MS-Outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Wyodrębnij tekst, w tym tekst wyodrębniony z załączników. `metadata_message_to_email``metadata_message_cc_email`i `metadata_message_bcc_email` są kolekcjami ciągów, pozostałe pola są ciągami.|
| ODT (application/vnd. języka Oasis. OpenDocument. Text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnij tekst, w tym osadzone dokumenty |
| ODS (application/vnd. języka Oasis. OpenDocument. arkusza kalkulacyjnego) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnij tekst, w tym osadzone dokumenty |
| ODP (application/vnd. języka Oasis. OpenDocument. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Wyodrębnij tekst, w tym osadzone dokumenty |
| Plik ZIP (Application/zip) |`metadata_content_type` |Wyodrębnij tekst ze wszystkich dokumentów w archiwum |
| GZ (aplikacja/gzip) |`metadata_content_type` |Wyodrębnij tekst ze wszystkich dokumentów w archiwum |
| EPUB (Application/EPUB + zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Wyodrębnij tekst ze wszystkich dokumentów w archiwum |
| XML (aplikacja/XML) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Przepakowywanie znacznika XML i wyodrębnienie tekstu |
| JSON (Application/JSON) |`metadata_content_type`<br/>`metadata_content_encoding` |Wyodrębnij tekst<br/>Uwaga: Jeśli chcesz wyodrębnić wiele pól dokumentu z obiektu BLOB JSON, zobacz [indeksowanie obiektów BLOB JSON](search-howto-index-json-blobs.md) w celu uzyskania szczegółów |
| EML (Message/RFC822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Wyodrębnij tekst, w tym załączniki |
| RTF (aplikacja/RTF) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Wyodrębnij tekst|
| Zwykły tekst (tekst/zwykły) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Wyodrębnij tekst|

## <a name="see-also"></a>Zobacz też

* [Indeksatory w usłudze Azure Cognitive Search](search-indexer-overview.md)
* [Zrozumienie obiektów BLOB przy użyciu AI](search-blob-ai-integration.md)
* [Omówienie indeksowania obiektów BLOB](search-blob-storage-integration.md)