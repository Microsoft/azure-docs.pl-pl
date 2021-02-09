---
title: Konfigurowanie indeksatora obiektów BLOB
titleSuffix: Azure Cognitive Search
description: Skonfiguruj indeksator usługi Azure BLOB do automatyzowania indeksowania zawartości obiektów BLOB dla operacji wyszukiwania pełnotekstowego w usłudze Azure Wyszukiwanie poznawcze.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 74813fabec4d5fe43cd158bb4aa359c2a3b0188a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988726"
---
# <a name="how-to-configure-blob-indexing-in-cognitive-search"></a>Jak skonfigurować indeksowanie obiektów BLOB w Wyszukiwanie poznawcze

W tym artykule opisano sposób konfigurowania indeksatora obiektów BLOB służącego do indeksowania dokumentów tekstowych (takich jak pliki PDF, Microsoft Office dokumenty i inne) w usłudze Azure Wyszukiwanie poznawcze. Jeśli nie znasz pojęć indeksatora, Zacznij od [indeksatorów na platformie Azure wyszukiwanie poznawcze](search-indexer-overview.md) i [Utwórz indeksator wyszukiwania](search-howto-create-indexers.md) przed przekazaniem do indeksowania obiektów BLOB.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Obsługiwane formaty dokumentów

Indeksator usługi Azure Wyszukiwanie poznawcze BLOB może wyodrębnić tekst z następujących formatów dokumentu:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="data-source-definitions"></a>Definicje źródeł danych

Różnica między indeksatorem obiektów blob i dowolnym innym indeksatorem jest definicją źródła danych, która jest przypisana do indeksatora. Źródło danych hermetyzuje wszystkie właściwości, które określają typ, połączenie i lokalizację zawartości do indeksowania.

Definicja źródła danych obiektu BLOB wygląda podobnie do poniższego przykładu:

```http
{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}
```

`"credentials"`Właściwość może być parametrami połączenia, jak pokazano w powyższym przykładzie lub jedną z alternatywnych metod opisanych w następnej sekcji. `"container"`Właściwość zawiera lokalizację zawartości w usłudze Azure Storage i `"query"` służy do określania podfolderu w kontenerze. Aby uzyskać więcej informacji na temat definicji źródeł danych, zobacz [Create Data Source (REST)](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>

## <a name="credentials"></a>Poświadczenia

Poświadczenia dla kontenera obiektów BLOB można podać w jeden z następujących sposobów:

**Parametry połączenia tożsamości zarządzanej**: `{ "connectionString" : "ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;" }`

Te parametry połączenia nie wymagają klucza konta, ale musisz postępować zgodnie z instrukcjami dotyczącymi [konfigurowania połączenia z kontem usługi Azure Storage przy użyciu tożsamości zarządzanej](search-howto-managed-identities-storage.md).

**Parametry połączenia z pełnymi dostępem do konta magazynu**: `{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }`

Parametry połączenia można uzyskać z Azure Portal, przechodząc do bloku konto magazynu > ustawienia > klucze (dla klasycznych kont magazynu) lub ustawienia > klucze dostępu (dla Azure Resource Manager kont magazynu).

Parametry połączenia **sygnatury dostępu współdzielonego** (SAS) konta magazynu:`{ "connectionString" : "BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl;" }`

Sygnatura dostępu współdzielonego powinna mieć uprawnienia do listy i odczytu w kontenerach i obiektach (w tym przypadku obiektów BLOB).

**Sygnatura dostępu współdzielonego kontenera**: `{ "connectionString" : "ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl;" }`

Sygnatura dostępu współdzielonego powinna mieć uprawnienia do listy i odczytu kontenera. Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego magazynu, zobacz [Używanie sygnatur dostępu współdzielonego](../storage/common/storage-sas-overview.md).

> [!NOTE]
> W przypadku używania poświadczeń SAS należy okresowo zaktualizować poświadczenia źródła danych za pomocą odnowionych podpisów, aby zapobiec ich wygaśnięciu. Jeśli poświadczenia sygnatury dostępu współdzielonego wygasną, indeksator zakończy się niepowodzeniem z komunikatem o błędzie podobnym do "poświadczenia podane w parametrach połączenia są nieprawidłowe lub wygasły".  

## <a name="index-definitions"></a>Definicje indeksu

Indeks określa pola w dokumencie, atrybuty i inne konstrukcje, które kształtują środowisko wyszukiwania. Poniższy przykład tworzy prosty indeks przy użyciu [create index (interfejs API REST)](/rest/api/searchservice/create-index). 

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

Definicje indeksów wymagają jednego pola w `"fields"` kolekcji, aby działały jako klucz dokumentu. Definicje indeksów powinny również zawierać pola zawartości i metadanych.

**`content`** Pole jest używane do przechowywania tekstu wyodrębnionego z obiektów BLOB. Definicja tego pola może wyglądać podobnie do powyższego. Nie musisz używać tej nazwy, ale dzięki temu można korzystać z niejawnych mapowań pól. Indeksator obiektów BLOB może wysłać zawartość obiektu BLOB do pola Content EDM. String w indeksie, nie są wymagane żadne mapowania pól.

Można również dodać pola dla wszystkich metadanych obiektów blob, które mają być używane w indeksie. Indeksator może odczytywać niestandardowe właściwości metadanych, [standardowe właściwości metadanych](#indexing-blob-metadata) i właściwości [metadanych właściwych dla zawartości](search-blob-metadata-properties.md) . Aby uzyskać więcej informacji na temat indeksów, zobacz [Tworzenie indeksu](search-what-is-an-index.md).

<a name="DocumentKeys"></a>

### <a name="defining-document-keys-and-field-mappings"></a>Definiowanie kluczy dokumentu i mapowań pól

W indeksie wyszukiwania klucz dokumentu jednoznacznie identyfikuje każdy dokument. Wybrane pole musi być typu `Edm.String` . W przypadku zawartości obiektu BLOB najlepszym kandydatem do klucza dokumentu są właściwości metadanych w obiekcie blob.

+ **`metadata_storage_name`** -Ta właściwość jest kandydatem, ale tylko wtedy, gdy nazwy są unikatowe dla wszystkich kontenerów i folderów, które są indeksowane. Niezależnie od lokalizacji obiektu BLOB wynik końcowy polega na tym, że klucz dokumentu (nazwa) musi być unikatowy w indeksie wyszukiwania po indeksowaniu całej zawartości. 

  Innym potencjalnym problemem związanym z nazwą magazynu może być znak, który jest nieprawidłowy dla kluczy dokumentów, takich jak łączniki. Za pomocą `base64Encode` [funkcji mapowania pól](search-indexer-field-mappings.md#base64EncodeFunction)można obsługiwać nieprawidłowe znaki. Jeśli to zrobisz, pamiętaj, aby zakodować klucze dokumentu podczas przekazywania ich w wywołaniach interfejsu API, takich jak [dokument wyszukiwania (REST)](/rest/api/searchservice/lookup-document). W programie .NET można użyć [metody UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode) do kodowania znaków.

+ **`metadata_storage_path`** — Użycie pełnej ścieżki zapewnia unikatowość, ale ścieżka w nieskończoność zawiera `/` znaki, które są [nieprawidłowe w kluczu dokumentu](/rest/api/searchservice/naming-rules). Jak powyżej, można użyć `base64Encode` [funkcji](search-indexer-field-mappings.md#base64EncodeFunction) do kodowania znaków.

+ Trzecią opcją jest dodanie niestandardowej właściwości metadanych do obiektów BLOB. Ta opcja wymaga, aby proces przekazywania obiektów BLOB dodał tę właściwość metadanych do wszystkich obiektów BLOB. Ponieważ klucz jest wymaganą właściwością, nie będzie można indeksować żadnych obiektów blob, w których brakuje wartości.

> [!IMPORTANT]
> Jeśli nie istnieje jawne mapowanie pola klucza w indeksie, usługa Azure Wyszukiwanie poznawcze automatycznie używa `metadata_storage_path` jako klucza i Base-64 wartości klucza (druga opcja powyżej).
>

#### <a name="example"></a>Przykład

Poniższy przykład ilustruje `metadata_storage_name` jako klucz dokumentu. Załóżmy, że indeks ma pole klucza o nazwie `key` i inne pole o nazwie `fileSize` do przechowywania rozmiaru dokumentu. [Mapowania pól](search-indexer-field-mappings.md) w definicji indeksatora ustanawiają skojarzenia pól i `metadata_storage_name` mają [ `base64Encode` funkcję mapowania pól](search-indexer-field-mappings.md#base64EncodeFunction) do obsługi nieobsługiwanych znaków.

```http
PUT https://[service name].search.windows.net/indexers/my-blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : "my-blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
  ]
}
```

#### <a name="how-to-make-an-encoded-field-searchable"></a>Jak utworzyć zakodowane pole "z możliwością wyszukiwania"

Istnieją przypadki, w których należy użyć zakodowanej wersji pola `metadata_storage_path` , takiego jak klucz, ale konieczne jest również, aby pole było przeszukiwane (bez kodowania) w indeksie wyszukiwania. Aby obsługiwać oba przypadki użycia, można mapować `metadata_storage_path` na dwa pola; jeden dla klucza (zakodowany), a drugi dla pola ścieżki, które możemy założyć, jest przypisany jako "z możliwością wyszukiwania" w schemacie indeksu. W poniższym przykładzie pokazano dwa mapowania pól dla `metadata_storage_path` .

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

<a name="PartsOfBlobToIndex"></a>

## <a name="index-content-and-metadata"></a>Indeksuj zawartość i metadane

Obiekty blob zawierają zawartość i metadane. Można kontrolować, które fragmenty obiektów BLOB są indeksowane przy użyciu `dataToExtract` parametru konfiguracji. Może przyjmować następujące wartości:

+ `contentAndMetadata` -Określa, że wszystkie metadane i zawartość tekstowa wyodrębnione z obiektu BLOB są indeksowane. Jest to wartość domyślna.

+ `storageMetadata` -Określa, że indeksowane są tylko [standardowe właściwości obiektów blob i metadane określone przez użytkownika](../storage/blobs/storage-blob-container-properties-metadata.md) .

+ `allMetadata` -Określa, że standardowe właściwości obiektów blob i wszystkie [metadane dla odnalezionych typów zawartości](search-blob-metadata-properties.md) są wyodrębniane z zawartości obiektu BLOB i indeksowane.

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

<a name="how-azure-search-indexes-blobs"></a>

### <a name="indexing-blob-content"></a>Indeksowanie zawartości obiektu BLOB

Domyślnie obiekty blob o zawartości strukturalnej, takie jak JSON lub CSV, są indeksowane jako pojedynczy fragment tekstu. Ale jeśli dokumenty JSON lub CSV mają wewnętrzną strukturę (ograniczniki), można przypisać tryby analizy do generowania poszczególnych dokumentów wyszukiwania dla każdego wiersza lub elementu. Aby uzyskać więcej informacji, zobacz [indeksowanie obiektów BLOB JSON](search-howto-index-json-blobs.md) i [indeksowanie obiektów BLOB w formacie CSV](search-howto-index-csv-blobs.md).

Dokument złożony lub osadzony (na przykład archiwum ZIP, dokument programu Word z osadzoną wiadomością e-mail programu Outlook zawierającą załączniki lub. Plik MSG z załącznikami) jest również indeksowany jako pojedynczy dokument. Na przykład wszystkie obrazy wyodrębnione z załączników. Plik MSG zostanie zwrócony w polu normalized_images.

Zawartość tekstowa dokumentu jest wyodrębniana do pola ciągu o nazwie `content` .

  > [!NOTE]
  > Usługa Azure Wyszukiwanie poznawcze ogranicza ilość tekstu wyodrębnianego w zależności od warstwy cenowej. Bieżące [limity usługi](search-limits-quotas-capacity.md#indexer-limits) są 32 000 znaków dla warstwy bezpłatna, 64 000 dla Basic, 4 000 000 dla standard, 8 000 000 dla standardu S2 i 16 000 000 dla standardowego S3. Ostrzeżenie jest zawarte w odpowiedzi stanu indeksatora dla obciętych dokumentów.  

<a name="indexing-blob-metadata"></a>

### <a name="indexing-blob-metadata"></a>Indeksowanie metadanych obiektu BLOB

Indeksatory mogą również indeksować metadane obiektów BLOB. Najpierw można wyodrębnić wszystkie właściwości metadanych określone przez użytkownika Verbatim. Aby otrzymać wartości, należy zdefiniować pole w indeksie wyszukiwania typu `Edm.String` o takiej samej nazwie jak klucz metadanych obiektu BLOB. Na przykład jeśli obiekt BLOB ma klucz metadanych o `Sensitivity` wartości `High` , należy zdefiniować pole o nazwie `Sensitivity` w indeksie wyszukiwania i zostanie ono wypełnione wartością `High` .

Po drugie, właściwości standardowego metadanych obiektu BLOB można wyodrębnić do pól wymienionych poniżej. Indeksator obiektów BLOB automatycznie tworzy wewnętrzne mapowania pól dla tych właściwości metadanych obiektu BLOB. Nadal trzeba dodać pola, które mają być używane w definicji indeksu, ale można pominąć tworzenie mapowań pól w indeksatorze.

  + **metadata_storage_name** ( `Edm.String` ) — nazwa pliku obiektu BLOB. Na przykład jeśli masz resume.pdf obiektu BLOB, wartość tego pola to `resume.pdf` .

  + **metadata_storage_path** ( `Edm.String` ) — pełny identyfikator URI obiektu BLOB, w tym konto magazynu. Na przykład `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

  + **metadata_storage_content_type** ( `Edm.String` ) — typ zawartości określony przez kod, który został użyty do przekazania obiektu BLOB. Na przykład `application/octet-stream`.

  + **metadata_storage_last_modified** ( `Edm.DateTimeOffset` ) — sygnatura czasowa ostatniej modyfikacji dla obiektu BLOB. Usługa Azure Wyszukiwanie poznawcze używa tej sygnatury czasowej do identyfikowania zmienionych obiektów blob, aby uniknąć ponownego indeksowania wszystkiego po początkowej indeksowaniu.

  + **metadata_storage_size** ( `Edm.Int64` ) — rozmiar obiektu BLOB w bajtach.

  + **metadata_storage_content_md5** ( `Edm.String` ) — skrót MD5 zawartości obiektu BLOB, jeśli jest dostępny.

  + **metadata_storage_sas_token** ( `Edm.String` ) — tymczasowy token SAS, który może być używany przez [umiejętności niestandardowe](cognitive-search-custom-skill-interface.md) w celu uzyskania dostępu do obiektu BLOB. Ten token nie powinien być przechowywany do późniejszego użycia, ponieważ mógł wygasnąć.

Na koniec wszystkie właściwości metadanych specyficzne dla formatu dokumentu indeksowania obiektów BLOB mogą być również reprezentowane w schemacie indeksu. Aby uzyskać więcej informacji na temat metadanych specyficznych dla zawartości, zobacz [właściwości metadanych zawartości](search-blob-metadata-properties.md).

Ważne jest, aby wskazać, że nie musisz definiować pól dla wszystkich powyższych właściwości w indeksie wyszukiwania — wystarczy przechwycić właściwości potrzebne dla aplikacji.

<a name="WhichBlobsAreIndexed"></a>

## <a name="how-to-control-which-blobs-are-indexed"></a>Jak kontrolować, które obiekty blob są indeksowane

Można kontrolować, które obiekty blob są indeksowane i które są pomijane przez typ pliku obiektu BLOB lub przez ustawienie właściwości w obiekcie blob, co spowoduje, że indeksator przejdzie nad nimi.

### <a name="include-specific-file-extensions"></a>Uwzględnij określone rozszerzenia plików

Użyj `indexedFileNameExtensions` , aby podać rozdzieloną przecinkami listę rozszerzeń plików do indeksowania (z kropką wiodącą). Na przykład, aby zindeksować tylko. PDF i. Pliki BLOB DOCX, wykonaj następujące czynności:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-specific-file-extensions"></a>Wyklucz określone rozszerzenia plików

Użyj `excludedFileNameExtensions` , aby podać rozdzieloną przecinkami listę rozszerzeń plików do pominięcia (ponownie z kropką). Na przykład, aby indeksować wszystkie obiekty blob z wyjątkiem. PNG i. Rozszerzenia JPEG, wykonaj następujące czynności:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Jeśli oba `indexedFileNameExtensions` `excludedFileNameExtensions` Parametry i są obecne, indeksator sprawdza najpierw `indexedFileNameExtensions` , a następnie na `excludedFileNameExtensions` . Jeśli to samo rozszerzenie pliku znajduje się na obu listach, zostanie wyłączone z indeksowania.

### <a name="add-skip-metadata-the-blob"></a>Dodawanie metadanych "Skip" dla obiektu BLOB

Parametry konfiguracji indeksatora stosują się do wszystkich obiektów BLOB w kontenerze lub folderze. Czasami chcesz kontrolować sposób indeksowania *poszczególnych obiektów BLOB* . Można to zrobić, dodając następujące właściwości i wartości metadanych do obiektów BLOB w magazynie obiektów BLOB. Gdy indeksator napotka te właściwości, spowoduje to pominięcie obiektu BLOB lub jego zawartości w przebiegu indeksowania.

| Nazwa właściwości | Wartość właściwości | Wyjaśnienie |
| ------------- | -------------- | ----------- |
| `AzureSearch_Skip` |`"true"` |Instruuje indeksator obiektu BLOB, aby całkowicie pominąć obiekt BLOB. Nie podjęto próby przeprowadzenia żadnej metadanych ani wyodrębniania zawartości. Jest to przydatne, gdy konkretny obiekt BLOB powtarza się wielokrotnie i przerywa proces indeksowania. |
| `AzureSearch_SkipContent` |`"true"` |Jest to odpowiednik `"dataToExtract" : "allMetadata"` Ustawienia opisanego [powyżej](#PartsOfBlobToIndex) w zakresie określonego obiektu BLOB. |

## <a name="index-large-datasets"></a>Indeksuj duże zestawy danych

Indeksowanie obiektów BLOB może być czasochłonnym procesem. W przypadkach, gdy masz miliony obiektów BLOB do indeksowania, możesz przyspieszyć indeksowanie, partycjonowanie danych i używanie wielu indeksatorów do [równoległego przetwarzania danych](search-howto-large-index.md#parallel-indexing). Oto, jak można je skonfigurować:

+ Partycjonowanie danych w wielu kontenerach obiektów blob lub w folderach wirtualnych

+ Skonfiguruj kilka źródeł danych, jeden dla każdego kontenera lub folderu. Aby wskazać folder obiektu BLOB, użyj `query` parametru:

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

+ Utwórz odpowiedni indeksator dla każdego źródła danych. Wszystkie indeksatory powinny wskazywać na ten sam docelowy indeks wyszukiwania.  

+ Jedna jednostka wyszukiwania w usłudze może uruchamiać jeden indeksator w danym momencie. Tworzenie wielu indeksatorów, jak opisano powyżej, jest przydatne tylko wtedy, gdy rzeczywiście są uruchamiane równolegle.

  Aby uruchamiać wiele indeksatorów równolegle, Skaluj w poziomie usługę wyszukiwania, tworząc odpowiednią liczbę partycji i replik. Jeśli na przykład usługa wyszukiwania ma 6 jednostek wyszukiwania (na przykład 2 repliki x 3), to 6 indeksatorów może być uruchomionych jednocześnie, co powoduje sześć przyrostów przepływności indeksowania. Aby dowiedzieć się więcej na temat skalowania i planowania pojemności, zobacz [Dostosowywanie pojemności usługi Wyszukiwanie poznawcze platformy Azure](search-capacity-planning.md).

<a name="DealingWithErrors"></a>

## <a name="handling-errors"></a>Obsługa błędów

Błędy, które często występują podczas indeksowania obejmują nieobsługiwane typy zawartości, brakującą zawartość lub zbyt duże obiekty blob.

Domyślnie indeksator obiektu BLOB jest zatrzymywany zaraz po napotkaniu obiektu BLOB z nieobsługiwanym typem zawartości (na przykład obrazem). Można użyć parametru, `excludedFileNameExtensions` Aby pominąć niektóre typy zawartości. Można jednak chcieć indeksować, aby przeprowadzić operację nawet w przypadku wystąpienia błędów, a następnie później debugować poszczególne dokumenty. Aby uzyskać więcej informacji o błędach indeksatora, zobacz [Rozwiązywanie typowych problemów indeksatora](search-indexer-troubleshooting.md) oraz [błędy i ostrzeżenia indeksatora](cognitive-search-common-errors-warnings.md).

### <a name="respond-to-errors"></a>Odpowiedz na błędy

Istnieją cztery właściwości indeksatora kontrolujące odpowiedzi indeksatora w przypadku wystąpienia błędów. W poniższych przykładach pokazano, jak ustawić te właściwości w definicji indeksatora. Jeśli indeksator już istnieje, możesz dodać te właściwości, edytując definicję w portalu.

#### <a name="maxfaileditems-and-maxfaileditemsperbatch"></a>`"maxFailedItems"` i `"maxFailedItemsPerBatch"`

Kontynuuj indeksowanie w przypadku wystąpienia błędów w dowolnym momencie przetwarzania podczas analizowania obiektów blob lub dodawania dokumentów do indeksu. Ustaw te właściwości na liczbę dopuszczalnych niepowodzeń. Wartość zezwala na `-1` Przetwarzanie niezależnie od liczby błędów. W przeciwnym razie wartość jest dodatnią liczbą całkowitą.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

#### <a name="failonunsupportedcontenttype-and-failonunprocessabledocument"></a>`"failOnUnsupportedContentType"` i `"failOnUnprocessableDocument"` 

W przypadku niektórych obiektów BLOB usługa Azure Wyszukiwanie poznawcze nie może określić typu zawartości lub nie może przetworzyć dokumentu z nieobsługiwanym typem zawartości. Aby zignorować te warunki awarii, ustaw parametry konfiguracji na `false` :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="relax-indexer-constraints"></a>Złagodzenie ograniczeń indeksatora

Możesz również ustawić [Właściwości konfiguracji obiektów BLOB](/rest/api/searchservice/create-indexer#blob-configuration-parameters) , które skutecznie określają, czy istnieje warunek błędu. Następująca właściwość może osłabiać ograniczenia, pomijając błędy, które mogłyby wystąpić w przeciwnym razie.

+ `"indexStorageMetadataOnlyForOversizedDocuments"` indeksowanie metadanych magazynu dla zawartości obiektu BLOB, która jest zbyt duża do przetworzenia. Zbyt duże obiekty blob są domyślnie traktowane jako błędy. W przypadku limitów rozmiaru obiektu BLOB zobacz [limity usługi](search-limits-quotas-capacity.md).

## <a name="see-also"></a>Zobacz też

+ [Indeksatory w usłudze Azure Cognitive Search](search-indexer-overview.md)
+ [Tworzenie indeksatora](search-howto-create-indexers.md)
+ [Informacje o wzbogacaniu AI o obiekty blob](search-blob-ai-integration.md)
+ [Omówienie wyszukiwania obiektów BLOB](search-blob-storage-integration.md)