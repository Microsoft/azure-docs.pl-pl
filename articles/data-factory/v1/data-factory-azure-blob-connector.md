---
title: Kopiowanie danych do/z usługi Azure Blob Storage
description: 'Dowiedz się, jak kopiować dane obiektów BLOB w Azure Data Factory. Skorzystaj z naszego przykładu: Jak kopiować dane do i z usługi Azure Blob Storage i Azure SQL Database.'
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f1343f900e12bff09c0436ca52d8b091fe48a181
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393551"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Kopiowanie danych do lub z usługi Azure Blob Storage przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-azure-blob-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-azure-blob-storage.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Azure Blob Storage Connector w wersji 2](../connector-azure-blob-storage.md).


W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych do i z usługi Azure Blob Storage. Jest on używany w artykule dotyczącym [przenoszenia danych](data-factory-data-movement-activities.md) , który przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania.

## <a name="overview"></a>Omówienie
Dane można kopiować z dowolnego obsługiwanego źródłowego magazynu danych na platformę Azure Blob Storage lub z usługi Azure Blob Storage do dowolnego obsługiwanego magazynu danych ujścia. Poniższa tabela zawiera listę magazynów danych obsługiwanych jako źródła lub ujścia przez działanie kopiowania. Można na przykład przenieść dane **z** bazy danych SQL Server lub bazy danych Azure SQL Database **do** magazynu obiektów blob platformy Azure. I można skopiować dane **z** usługi Azure Blob Storage **do** usługi Azure Synapse Analytics lub kolekcji Azure Cosmos DB.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane **z usługi Azure Blob Storage** można kopiować do następujących magazynów danych:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Dane z następujących magazynów danych można kopiować **do platformy Azure Blob Storage**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> Działanie kopiowania obsługuje kopiowanie danych z/do kont usługi Azure Storage ogólnego przeznaczenia i gorącą/chłodną usługę BLOB Storage. Działanie obsługuje **odczytywanie z bloków, dołączania lub stronicowych obiektów BLOB**, ale obsługuje **Zapisywanie tylko w celu blokowania obiektów BLOB**. Usługa Azure Premium Storage nie jest obsługiwana jako ujścia, ponieważ jest ona wykonywana przez stronicowe obiekty blob.
>
> Działanie Copy nie usuwa danych ze źródła, gdy dane zostaną pomyślnie skopiowane do lokalizacji docelowej. Jeśli musisz usunąć dane źródłowe po pomyślnej kopii, Utwórz [niestandardowe działanie](data-factory-use-custom-activities.md) , aby usunąć dane i użyć działania w potoku. Aby zapoznać się z przykładem, zobacz sekcję [usuwanie obiektu BLOB lub folderu w witrynie GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity).

## <a name="get-started"></a>Rozpoczęcie pracy
Można utworzyć potok za pomocą działania kopiowania, które przenosi dane do/z Blob Storage platformy Azure przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. [W tym](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) artykule opisano tworzenie potoku w celu skopiowania danych z lokalizacji BLOB Storage platformy Azure do innej lokalizacji usługi Azure Blob Storage. Aby zapoznać się z samouczkiem dotyczącym tworzenia potoku w celu skopiowania danych z usługi Azure Blob Storage do Azure SQL Database, zobacz [Samouczek: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

Do utworzenia potoku można także użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon Azure Resource Manager**, interfejs API **platformy .NET** i **interfejs API REST**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz **fabrykę danych**. Fabryka danych może zawierać jeden lub więcej potoków.
2. Utwórz **połączone usługi** , aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych. Na przykład jeśli kopiujesz dane z usługi Azure Blob Storage do Azure SQL Database, utworzysz dwie połączone usługi, aby połączyć konto usługi Azure Storage i Azure SQL Database z fabryką danych. Aby uzyskać właściwości połączonej usługi specyficzne dla platformy Azure Blob Storage, zobacz sekcję [Właściwości połączonej usługi](#linked-service-properties) .
2. Utwórz **zestawy** danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania. W przykładzie opisanym w ostatnim kroku utworzysz zestaw danych, aby określić kontener obiektów blob i folder zawierający dane wejściowe. Ponadto utworzysz kolejny zestaw danych, aby określić tabelę SQL w Azure SQL Database, która zawiera dane skopiowane z magazynu obiektów BLOB. Dla właściwości zestawu danych, które są specyficzne dla Blob Storage platformy Azure, zobacz sekcję [Właściwości zestawu danych](#dataset-properties) .
3. Utwórz **potok** z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. W powyższym przykładzie użyto BlobSource jako źródła i obiektu sqlsink jako ujścia dla działania kopiowania. Podobnie, jeśli kopiujesz z Azure SQL Database do Blob Storage platformy Azure, użyjesz sqlsource i wartość blobsink w działaniu kopiowania. Aby uzyskać właściwości działania kopiowania specyficzne dla platformy Azure Blob Storage, zobacz sekcję [właściwości działania kopiowania](#copy-activity-properties) . Aby uzyskać szczegółowe informacje na temat używania magazynu danych jako źródła lub ujścia, kliknij link w poprzedniej sekcji dla magazynu danych.

Gdy używasz Kreatora, definicje JSON dla tych Data Factory jednostek (połączone usługi, zestawy danych i potok) są automatycznie tworzone. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować te Data Factory jednostki przy użyciu formatu JSON.  Przykłady zawierające definicje JSON dla Data Factory jednostek, które są używane do kopiowania danych do/z Blob Storage platformy Azure, zobacz sekcję [przykłady JSON](#json-examples-for-copying-data-to-and-from-blob-storage  ) w tym artykule.

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON, które są używane do definiowania Data Factory jednostek specyficznych dla Blob Storage platformy Azure.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi
Istnieją dwa typy połączonych usług, za pomocą których można połączyć usługę Azure Storage z fabryką danych Azure. Są to: **AzureStorage** połączonej usługi i **AzureStorageSas** połączonej usługi. Połączona usługa Azure Storage udostępnia fabrykę danych z globalnym dostępem do usługi Azure Storage. W związku z tym połączona usługa Azure Storage SAS (Sygnatura dostępu współdzielonego) udostępnia fabrykę danych z ograniczonym dostępem do usługi Azure Storage. Nie istnieją inne różnice między tymi dwoma połączonymi usługami. Wybierz połączoną usługę, która odpowiada Twoim potrzebom. Poniższe sekcje zawierają więcej informacji na temat tych dwóch połączonych usług.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby określić zestaw danych do reprezentowania danych wejściowych lub wyjściowych w usłudze Azure Blob Storage, należy ustawić Właściwość Type zestawu danych na: **AzureBlob**. Ustaw właściwość **linkedServiceName** zestawu danych na nazwę połączonej usługi Azure Storage lub Azure Storage SAS.  Właściwości typu zestawu danych określają **kontener obiektów BLOB** i **folder** w magazynie obiektów BLOB.

Aby uzyskać pełną listę sekcji JSON & właściwości dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych](data-factory-create-datasets.md) . Sekcje, takie jak struktura, dostępność i zasady JSON zestawu danych są podobne dla wszystkich typów zestawu danych (Azure SQL, Azure Blob, Azure Table itp.).

Usługa Data Factory obsługuje następujące wartości typu opartego na technologii .NET zgodne ze specyfikacją CLS, które zapewniają informacje o typie w "strukturze" dla źródeł danych odczytywanych ze schematu, takich jak Azure BLOB: Int16, Int32, Int64, Single, Double, decimal, Byte [], bool, String, GUID, DateTime, DateTimeOffset, TimeSpan. Data Factory automatycznie wykonuje konwersje typów podczas przesuwania danych ze źródłowego magazynu danych do magazynu danych ujścia.

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji, formacie itp., danych w magazynie danych. Sekcja typeProperties zestawu danych typu **AzureBlob** z zestawem danych ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Ścieżka do kontenera i folderu w magazynie obiektów BLOB. Przykład: myblobcontainer\myblobfolder\ |Tak |
| fileName |Nazwa obiektu BLOB. Nazwa pliku jest opcjonalna i uwzględnia wielkość liter.<br/><br/>W przypadku określenia nazwy pliku działanie (łącznie z kopią) działa w określonym obiekcie blob.<br/><br/>Jeśli nazwa pliku nie jest określona, Copy zawiera wszystkie obiekty blob w folderPath dla wejściowego zestawu danych.<br/><br/>Jeśli **Nazwa pliku** nie została określona dla wyjściowego zestawu danych, a **preserveHierarchy** nie jest określona w ujścia aktywności, nazwa wygenerowanego pliku będzie w następującym formacie: `Data.<Guid>.txt` (na przykład:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| partitionedBy |partitionedBy jest właściwością opcjonalną. Można jej użyć do określenia dynamicznego folderPath i nazwy pliku dla danych szeregów czasowych. Na przykład folderPath może być sparametryzowany dla każdej godziny danych. Zobacz [sekcję using partitionedBy](#using-partitionedby-property) , aby uzyskać szczegółowe informacje i przykłady. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **Type** w polu Format na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstowy](data-factory-supported-file-and-compression-formats.md#text-format), [Format JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Format Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Format Orc](data-factory-supported-file-and-compression-formats.md#orc-format)i sekcje [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Jeśli chcesz **skopiować pliki** między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **gzip**, **Wklęśnięcie**, **BZip2** i **ZipDeflate**. Obsługiwane poziomy to: **optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

### <a name="using-partitionedby-property"></a>Używanie właściwości partitionedBy
Jak wspomniano w poprzedniej sekcji, można określić dynamiczne folderPath i nazwa pliku dla danych szeregów czasowych z właściwością **partitionedBy** , [funkcjami Data Factory i zmiennymi systemowymi](data-factory-functions-variables.md).

Aby uzyskać więcej informacji na temat zestawów danych, planowania i wycinków szeregów czasowych, zobacz [Tworzenie zestawów danych](data-factory-create-datasets.md) i [Planowanie & artykułów wykonawczych](data-factory-scheduling-and-execution.md) .

#### <a name="sample-1"></a>Przykład 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

W tym przykładzie {Slice} jest zastępowana wartością zmiennej systemowej Data Factory parametru slicestart w formacie (YYYYMMDDHH). Parametru slicestart odnosi się do czasu rozpoczęcia wycinka. FolderPath różni się dla każdego wycinka. Na przykład: wikidatagateway/wikisampledataout/2014100103 lub wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>Przykład 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

W tym przykładzie rok, miesiąc, dzień i czas parametru slicestart są wyodrębniane do oddzielnych zmiennych, które są używane przez właściwości folderPath i fileName.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . Właściwości, takie jak nazwa, opis, wejściowe i wyjściowe zestawy danych, oraz zasady są dostępne dla wszystkich typów działań. Natomiast właściwości dostępne w sekcji **typeProperties** działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się w zależności od typów źródeł i ujścia. Jeśli przenosisz dane z usługi Azure Blob Storage, musisz ustawić typ źródła w działaniu kopiowania na **BlobSource**. Podobnie, Jeśli przenosisz dane do Blob Storage platformy Azure, możesz ustawić typ ujścia w działaniu kopiowania na **wartość blobsink**. Ta sekcja zawiera listę właściwości obsługiwanych przez BlobSource i wartość blobsink.

**BlobSource** obsługuje następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| rozpoznawania |Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. |True (wartość domyślna), FAŁSZ |Nie |

**Wartość blobsink** obsługuje następujące właściwości **typeProperties** sekcja:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| copyBehavior |Definiuje zachowanie kopiowania, gdy źródłem jest BlobSource lub system plików. |<b>PreserveHierarchy</b>: zachowuje hierarchię plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego do folderu docelowego.<br/><br/><b>FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie wygenerowaną nazwę. <br/><br/><b>MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku/obiektu BLOB jest określona, scaloną nazwą pliku będzie określona nazwa; w przeciwnym razie zostanie automatycznie wygenerowana nazwa pliku. |Nie |

**BlobSource** obsługuje również te dwie właściwości w celu zapewnienia zgodności z poprzednimi wersjami.

* **treatEmptyAsNull**: określa, czy ciąg zerowy lub pusty ma być traktowany jako wartość null.
* **skipHeaderLineCount** — określa liczbę wierszy, które muszą być pominięte. Ma zastosowanie tylko wtedy, gdy wejściowy zestaw danych używa formatu TextFormat.

Podobnie **wartość blobsink** obsługuje następującą właściwość w celu zapewnienia zgodności z poprzednimi wersjami.

* **blobWriterAddHeader**: określa, czy dodać nagłówek definicji kolumn podczas zapisywania do wyjściowego zestawu danych.

Zestawy danych obsługują teraz następujące właściwości, które implementują te same funkcje: **treatEmptyAsNull**, **skipLineCount**, **użycia**.

W poniższej tabeli przedstawiono wskazówki dotyczące używania nowych właściwości zestawu danych zamiast tych właściwości źródła/ujścia obiektu BLOB.

| Właściwość działania kopiowania | DataSet — Właściwość |
|:--- |:--- |
| skipHeaderLineCount na BlobSource |skipLineCount i użycia. Wiersze są najpierw pomijane, a następnie pierwszy wiersz jest odczytywany jako nagłówek. |
| treatEmptyAsNull na BlobSource |treatEmptyAsNull dla wejściowego zestawu danych |
| blobWriterAddHeader na wartość blobsink |Użycia dla wyjściowego zestawu danych |

Aby uzyskać szczegółowe informacje o tych właściwościach, zobacz sekcję [Określanie formatu TextFormat](data-factory-supported-file-and-compression-formats.md#text-format) .

### <a name="recursive-and-copybehavior-examples"></a>Przykłady cykliczne i copyBehavior
W tej sekcji opisano zachowanie operacji kopiowania dla różnych kombinacji wartości cyklicznych i copyBehavior.

| rozpoznawania | copyBehavior | Wynikowe działanie |
| --- | --- | --- |
| true |preserveHierarchy |Dla folderu źródłowego Folder1 z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony z tą samą strukturą co Źródło<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |Dla folderu źródłowego Folder1 z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>docelowy Folder1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla File5 |
| true |mergeFiles |Dla folderu źródłowego Folder1 z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>docelowy Folder1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 + file3 + File4 + zawartość pliku 5 są scalane w jeden plik z automatycznie wygenerowaną nazwą pliku |
| fałsz |preserveHierarchy |Dla folderu źródłowego Folder1 z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony z następującą strukturą<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/><br/>Subfolder1 z file3, File4 i File5 nie są odbierane. |
| fałsz |flattenHierarchy |Dla folderu źródłowego Folder1 z następującą strukturą:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony z następującą strukturą<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik2<br/><br/><br/>Subfolder1 z file3, File4 i File5 nie są odbierane. |
| fałsz |mergeFiles |Dla folderu źródłowego Folder1 z następującą strukturą:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony z następującą strukturą<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Zawartość plik1 + plik2 są scalane w jeden plik z automatycznie wygenerowaną nazwą pliku. automatycznie wygenerowana nazwa dla plik1<br/><br/>Subfolder1 z file3, File4 i File5 nie są odbierane. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Przewodnik: kopiowanie danych do/z Blob Storage za pomocą Kreatora kopiowania
Przyjrzyjmy się sposobom szybkiego kopiowania danych do/z usługi Azure Blob Storage. W tym instruktażu źródłowe i docelowe magazyny danych typu: Azure Blob Storage. Potok w tym instruktażu kopiuje dane z folderu do innego folderu w tym samym kontenerze obiektów BLOB. Ten Instruktaż jest celowo prosty, aby pokazać ustawienia lub właściwości przy użyciu Blob Storage jako źródła lub ujścia.

### <a name="prerequisites"></a>Wymagania wstępne
1. Utwórz **konto usługi Azure Storage** ogólnego przeznaczenia, jeśli jeszcze go nie masz. Magazyn obiektów BLOB jest używany jako **źródłowy** i **docelowy** magazyn danych w tym instruktażu. Jeśli nie masz konta usługi Azure Storage, zapoznaj się z artykułem [Tworzenie konta magazynu](../../storage/common/storage-account-create.md) , aby dowiedzieć się, jak go utworzyć.
2. Utwórz kontener obiektów BLOB o nazwie **adfblobconnector** na koncie magazynu.
4. Utwórz folder o nazwie **Input** w kontenerze **adfblobconnector** .
5. Utwórz plik o nazwie **emp.txt** z następującą zawartością i przekaż go do folderu **Input** przy użyciu narzędzi takich jak [Eksplorator usługi Azure Storage](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>Tworzenie fabryki danych
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij pozycję **Utwórz zasób** w lewym górnym rogu, kliknij pozycję **Analiza i analiza**, a następnie kliknij pozycję **Data Factory**.
3. W okienku **Nowa fabryka danych** :  
    1. Wprowadź **ADFBlobConnectorDF** dla **nazwy**. Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli wystąpi błąd: `*Data factory name "ADFBlobConnectorDF" is not available` , Zmień nazwę fabryki danych (na przykład yournameADFBlobConnectorDF) i spróbuj utworzyć ją ponownie. Artykuł [Data Factory — Naming Rules](data-factory-naming-rules.md) (Fabryka danych — zasady nazewnictwa) zawiera zasady nazewnictwa artefaktów usługi Fabryka danych.
    2. Wybierz swoją **subskrypcję** platformy Azure.
    3. W obszarze Grupa zasobów wybierz pozycję **Użyj istniejącej** , aby wybrać istniejącą grupę zasobów (lub) wybierz pozycję **Utwórz nową** , aby wprowadzić nazwę grupy zasobów.
    4. Wybierz **lokalizację** fabryki danych.
    5. Zaznacz pole wyboru **Przypnij do pulpitu nawigacyjnego** u dołu bloku.
    6. Kliknij pozycję **Utwórz**.
3. Po zakończeniu tworzenia zostanie wyświetlony blok **Data Factory** , jak pokazano na poniższej ilustracji:  ![ Strona główna fabryki danych](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Kreator kopiowania
1. Na stronie głównej Data Factory kliknij przycisk **Kopiuj dane** , aby uruchomić **Kreatora kopiowanie danych** na oddzielnej karcie.  

    > [!NOTE]
    > Jeśli zobaczysz, że przeglądarka sieci Web jest zablokowana w "autoryzuje...", Wyłącz/Usuń zaznaczenie pola **pliki cookie innych firm i ustawienie danych lokacji** (lub) pozostaw i Utwórz wyjątek dla **login.microsoftonline.com** , a następnie spróbuj ponownie uruchomić kreatora.
2. Na stronie **Właściwości**:
    1. Wprowadź **CopyPipeline** dla **nazwy zadania**. Nazwa zadania to nazwa potoku w fabryce danych.
    2. Wprowadź **Opis** zadania (opcjonalnie).
    3. W przypadku **zadania erze lub harmonogramu zadań** należy zachować **regularne uruchamianie przy** użyciu opcji harmonogram. Jeśli chcesz uruchomić to zadanie tylko raz, a nie uruchamiaj wielokrotnie zgodnie z harmonogramem, wybierz pozycję **Uruchom raz**. W przypadku wybrania opcji **Uruchom raz** , zostanie utworzony [potok jednorazowy](data-factory-create-pipelines.md#onetime-pipeline) .
    4. Zachowaj ustawienia **wzorca cyklicznego**. To zadanie jest uruchamiane codziennie między godzinami rozpoczęcia i zakończenia określonymi w następnym kroku.
    5. Zmień **datę i godzinę rozpoczęcia** na **04/21/2017**.
    6. Zmień **datę i godzinę zakończenia** na **04/25/2017**. Możesz chcieć wpisać datę zamiast przeglądać kalendarz.
    8. Kliknij przycisk **Dalej**.
        ![Narzędzie kopiowania — strona Właściwości](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. Na stronie **Magazyn danych źródłowych** kliknij kafelek **Azure Blob Storage**. Ta strona służy do określania magazynu danych źródłowych do zadania kopiowania. Możesz użyć istniejącej połączonej usługi magazynu danych lub określić nowy magazyn danych. Aby użyć istniejącej połączonej usługi, należy wybrać **istniejące** połączone usługi i wybrać odpowiednią połączoną usługę.
    ![Narzędzie kopiowania — strona magazynu danych źródłowych](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. Na stronie **Określanie konta usługi Azure Blob Storage**:
    1. Zachowaj automatycznie wygenerowaną nazwę dla **nazwy połączenia**. Nazwa połączenia jest nazwą połączonej usługi typu: Azure Storage.
    2. Upewnij się, że wybrano opcję **Z subskrypcji Azure** dla ustawienia **Metoda wyboru konta**.
    3. Wybierz swoją subskrypcję platformy Azure lub Zachowaj **pozycję Wszystkie** dla **subskrypcji platformy Azure**.
    4. Wybierz **konto usługi Azure Storage** z listy kont usługi Azure Storage dostępnych w ramach wybranej subskrypcji. Ustawienia konta magazynu można również wprowadzić ręcznie, wybierając opcję **wprowadź ręcznie** dla **metody wyboru konta**.
    5. Kliknij przycisk **Dalej**.  
        ![Narzędzie kopiowania — określanie konta usługi Azure Blob Storage](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. Na stronie **Wybieranie pliku lub folderu wejściowego**:
    1. Kliknij dwukrotnie pozycję **adfblobcontainer**.
    2. Wybierz pozycję **dane wejściowe**, a następnie kliknij pozycję **Wybierz**. W tym instruktażu należy wybrać folder wejściowy. Zamiast tego można również wybrać plik emp.txt w folderze.
        ![Narzędzie kopiowania — Wybieranie pliku wejściowego lub folderu 1](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. Na stronie **Wybieranie pliku lub folderu wejściowego** :
    1. Upewnij się, że **plik lub folder** jest ustawiony na **adfblobconnector/Input**. Jeśli pliki znajdują się w podfolderach, na przykład 2017/04/01, 2017/04/02 i tak dalej, wprowadź adfblobconnector/Input/{Year}/{Month}/{Day} dla pliku lub folderu. Po naciśnięciu klawisza TAB z pola tekstowego zobaczysz trzy listy rozwijane, aby wybrać formaty rok (RRRR), miesiąc (MM) i dzień (DD).
    2. Nie ustawiaj **cyklicznie pliku kopiowania**. Wybierz tę opcję, aby przeprowadzić cykliczne przechodzenie przez foldery w poszukiwaniu plików do skopiowania do miejsca docelowego.
    3. Nie można **skopiować plików binarnych** . Wybierz tę opcję, aby wykonać kopię binarną pliku źródłowego do miejsca docelowego. Nie zaznaczaj tego przewodnika, aby zobaczyć więcej opcji na następnych stronach.
    4. Upewnij się, że **Typ kompresji** ma wartość **none**. Wybierz wartość tej opcji, jeśli pliki źródłowe są skompresowane w jednym z obsługiwanych formatów.
    5. Kliknij przycisk **Dalej**.
    ![Narzędzie kopiowania — Wybieranie pliku wejściowego lub folderu 2](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. Na stronie **Ustawienia formatu pliku** są wyświetlane ograniczniki i schemat wykrywany automatycznie przez kreatora w ramach analizy pliku.
    1. Potwierdź następujące opcje:  
        a. **Format pliku** jest ustawiony na **Format tekstu**. Wszystkie obsługiwane formaty są widoczne na liście rozwijanej. Na przykład: JSON, Avro, ORC, Parquet.
       b. **Ogranicznik kolumny** jest ustawiony na `Comma (,)` . Na liście rozwijanej można zobaczyć inne ograniczniki kolumn obsługiwane przez Data Factory. Możesz również określić niestandardowy ogranicznik.
       c. **Ogranicznik wiersza** jest ustawiony na `Carriage Return + Line feed (\r\n)` . Pozostałe ograniczniki wierszy obsługiwane przez Data Factory można zobaczyć na liście rozwijanej. Możesz również określić niestandardowy ogranicznik.
       d. **Liczba wierszy Pomiń** jest ustawiona na **0**. Jeśli chcesz, aby kilka wierszy zostało pominięte w górnej części pliku, wprowadź tutaj liczbę.
       e. **Pierwszy wiersz danych zawiera nazwy kolumn** nie są ustawione. Jeśli pliki źródłowe zawierają nazwy kolumn w pierwszym wierszu, wybierz tę opcję.
       f. Ustawiona jest opcja **Traktuj pustą kolumnę jako wartość null** .
    2. Rozwiń pozycję **Ustawienia zaawansowane** , aby wyświetlić zaawansowaną opcję.
    3. W dolnej części strony zapoznaj się z **podglądem** danych w pliku emp.txt.
    4. Kliknij przycisk **schemat** na dole, aby zobaczyć schemat, który został wywnioskowany przez Kreatora kopiowania, przeglądając dane w pliku źródłowym.
    5. Po przejrzeniu ograniczników i wyświetleniu podglądu danych kliknij przycisk **Dalej**.
    ![Narzędzie kopiowania — ustawienia formatu pliku](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. Na **stronie docelowy magazyn danych** wybierz pozycję **Azure Blob Storage** i kliknij przycisk **dalej**. Używasz usługi Azure Blob Storage jako źródłowego i docelowego magazynu danych w tym instruktażu.  
    ![Narzędzie kopiowania — Wybieranie docelowego magazynu danych](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. Na stronie **Określanie konta usługi Azure Blob Storage** :  
    1. Wprowadź **AzureStorageLinkedService** dla pola **Nazwa połączenia** .
    2. Upewnij się, że wybrano opcję **Z subskrypcji Azure** dla ustawienia **Metoda wyboru konta**.
    3. Wybierz swoją **subskrypcję** platformy Azure.
    4. Wybierz konto usługi Azure Storage.
    5. Kliknij przycisk **Dalej**.
10. Na stronie **Wybieranie pliku lub folderu wyjściowego** :  
    1. Określ **ścieżkę folderu** jako **adfblobconnector/Output/{Year}/{Month}/{Day}**. Klawisz **Enter.**
    1. W **roku** wybierz pozycję **rrrr**.
    1. W **miesiącu** upewnij się, że jest ustawiona na **mm**.
    1. Na **dzień** upewnij się, że ustawiono wartość **DD**.
    1. Upewnij się, że **Typ kompresji** ma wartość **none**.
    1. Upewnij się, że **zachowanie kopiowania** ma ustawioną wartość **Scal pliki**. Jeśli plik wyjściowy o tej samej nazwie już istnieje, Nowa zawartość zostanie dodana do tego samego pliku na końcu.
    1. Kliknij przycisk **Dalej**.
       ![Narzędzie kopiowania — Wybieranie pliku lub folderu wyjściowego](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. Na stronie **Ustawienia formatu pliku** przejrzyj ustawienia, a następnie kliknij przycisk **dalej**. Jedną z dodatkowych opcji jest dodanie nagłówka do pliku wyjściowego. W przypadku wybrania tej opcji wiersz nagłówka zostanie dodany z nazwami kolumn ze schematu źródła. Podczas wyświetlania schematu dla źródła można zmienić nazwy domyślnych kolumn. Na przykład, można zmienić pierwszą kolumnę na imię i drugą kolumnę na nazwisko. Następnie plik wyjściowy jest generowany z nagłówkiem z tymi nazwami jako nazwami kolumn.
    ![Narzędzie kopiowania — ustawienia formatu pliku dla miejsca docelowego](media/data-factory-azure-blob-connector/file-format-destination.png)
12. Na stronie **Ustawienia wydajności** upewnij się, że **jednostki chmury** i **kopie równoległe** są ustawione na wartość **automatycznie**, a następnie kliknij przycisk Dalej. Aby uzyskać szczegółowe informacje na temat tych ustawień, zobacz [Przewodnik dotyczący wydajności i dostrajania działania kopiowania](data-factory-copy-activity-performance.md#parallel-copy).
    ![Narzędzie kopiowania — ustawienia wydajności](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. Na stronie **Podsumowanie** Przejrzyj wszystkie ustawienia (właściwości zadania, ustawienia źródłowe i docelowe oraz ustawienia kopiowania), a następnie kliknij przycisk **dalej**.
    ![Narzędzie kopiowania — strona podsumowania](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Przejrzyj informacje na stronie **Podsumowanie** i kliknij przycisk **Zakończ**. Kreator utworzy dwie połączone usługi, dwa zestawy danych (wejściowy i wyjściowy) i jeden potok w fabryce danych (z której został uruchomiony Kreator kopiowania).
    ![Narzędzie kopiowania — Strona wdrożenia](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>Monitorowanie potoku (Kopiuj zadanie)

1. Kliknij link `Click here to monitor copy pipeline` na stronie **wdrażanie** .
2. **Aplikacja monitor i zarządzanie** powinna zostać wyświetlona na osobnej karcie.  ![Monitorowanie aplikacji i zarządzanie nią](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Zmień wartość w polu godzina **rozpoczęcia** na górną `04/19/2017` i **końcową** na wartość `04/27/2017` , a następnie kliknij pozycję **Zastosuj**.
4. Na liście **okna działania** powinny być widoczne pięć okien aktywności. Czasy **WindowStart** powinny obejmować wszystkie dni od rozpoczęcia potoku do czasu zakończenia potoku.
5. Kliknij przycisk **Odśwież** , aby wyświetlić listę **okien działania** kilka razy, aż zobaczysz stan wszystkie okna działania ustawione na gotowe.
6. Teraz sprawdź, czy pliki wyjściowe są generowane w folderze wyjściowym kontenera adfblobconnector. W folderze wyjściowym powinna zostać wyświetlona następująca struktura folderów:
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
   Aby uzyskać szczegółowe informacje na temat monitorowania fabryk danych i zarządzania nimi, zobacz artykuł [monitorowanie i zarządzanie Data Factory potoku](data-factory-monitor-manage-app.md) .

### <a name="data-factory-entities"></a>Jednostki Data Factory
Teraz przejdź z powrotem do karty przy użyciu strony głównej Data Factory. Należy zauważyć, że teraz istnieją dwie połączone usługi, dwa zestawy danych i jeden potok w fabryce danych.

![Strona główna Data Factory z jednostkami](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Kliknij przycisk **Utwórz i Wdróż,** aby uruchomić Edytor Data Factory.

![Edytor fabryki danych](media/data-factory-azure-blob-connector/data-factory-editor.png)

W fabryce danych powinny zostać wyświetlone następujące jednostki Data Factory:

- Dwie połączone usługi. Jeden dla źródła i drugi dla miejsca docelowego. Połączone usługi odnoszą się do tego samego konta usługi Azure Storage w tym instruktażu.
- Dwa zestawy danych. Wejściowy zestaw danych i wyjściowy zestaw danych. W tym instruktażu oba te elementy używają tego samego kontenera obiektów blob, ale odwołują się do różnych folderów (wejściowych i wyjściowych).
- Potok. Potok zawiera działanie kopiowania, które używa źródła obiektów blob i ujścia obiektów BLOB do kopiowania danych z lokalizacji obiektu blob platformy Azure do innej lokalizacji obiektów blob platformy Azure.

Poniższe sekcje zawierają więcej informacji na temat tych jednostek.

#### <a name="linked-services"></a>Połączone usługi
Powinny być widoczne dwie połączone usługi. Jeden dla źródła i drugi dla miejsca docelowego. W tym instruktażu obie definicje wyglądają tak samo, z wyjątkiem nazw. **Typ** połączonej usługi jest ustawiony na **AzureStorage**. Najważniejszym właściwością definicji połączonej usługi jest parametr **ConnectionString**, który jest używany przez Data Factory do nawiązywania połączenia z kontem usługi Azure Storage w czasie wykonywania. Zignoruj Właściwość hubName w definicji.

##### <a name="source-blob-storage-linked-service"></a>Połączona usługa magazynu obiektów BLOB
```json
{
    "name": "Source-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

##### <a name="destination-blob-storage-linked-service"></a>Połączona usługa docelowego magazynu obiektów BLOB

```json
{
    "name": "Destination-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

Aby uzyskać więcej informacji na temat połączonej usługi Azure Storage, zobacz sekcję [Właściwości połączonej usługi](#linked-service-properties) .

#### <a name="datasets"></a>Zestawy danych
Istnieją dwa zestawy danych wejściowych i wyjściowy zestaw danych. Typ zestawu danych jest ustawiony na **AzureBlob** dla obu.

Wejściowy zestaw danych wskazuje folder **wejściowy** kontenera obiektów BLOB **adfblobconnector** . Właściwość **zewnętrzna** ma ustawioną **wartość true** dla tego zestawu danych, ponieważ dane nie są tworzone przez potok z działaniem kopiowania, które pobiera ten zestaw danych jako dane wejściowe.

Wyjściowy zestaw danych wskazuje folder **wyjściowy** tego samego kontenera obiektów BLOB. Wyjściowy zestaw danych używa również roku, miesiąca i dnia zmiennej systemowej **parametru slicestart** do dynamicznego szacowania ścieżki pliku wyjściowego. Aby uzyskać listę funkcji i zmiennych systemowych obsługiwanych przez Data Factory, zobacz [funkcje Data Factory i zmienne systemowe](data-factory-functions-variables.md). Właściwość **zewnętrzna** ma wartość **false** (wartość domyślna), ponieważ ten zestaw danych jest generowany przez potok.

Aby uzyskać więcej informacji na temat właściwości obsługiwanych przez zestaw danych obiektów blob platformy Azure, zobacz sekcję [Właściwości zestawu danych](#dataset-properties) .

##### <a name="input-dataset"></a>Wejściowy zestaw danych

```json
{
    "name": "InputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Source-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/input/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

##### <a name="output-dataset"></a>Wyjściowy zestaw danych

```json
{
    "name": "OutputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Destination-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/output/{year}/{month}/{day}",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
            "partitionedBy": [
                { "name": "year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }
            ]
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

#### <a name="pipeline"></a>Potok
Potok ma tylko jedno działanie. **Typ** działania jest ustawiony na wartość **Kopiuj**. We właściwościach typu działania znajdują się dwie sekcje — jeden dla źródła i drugi dla ujścia. Typ źródła jest ustawiany na **BlobSource** , ponieważ działanie kopiuje dane z magazynu obiektów BLOB. Typ ujścia jest ustawiany na **wartość blobsink** jako działanie służące do kopiowania danych do magazynu obiektów BLOB. Działanie Copy przyjmuje wartość InputDataset-z4y jako dane wyjściowe i OutputDataset-z4y.

Aby uzyskać więcej informacji na temat właściwości obsługiwanych przez BlobSource i wartość blobsink, zobacz sekcję [copy Activity Properties](#copy-activity-properties) .

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "MergeFiles",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-z4y"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-z4y"
                    }
                ],
                "policy": {
                    "timeout": "1.00:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 3,
                    "longRetry": 0,
                    "longRetryInterval": "00:00:00"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "Activity-0-Blob path_ adfblobconnector_input_->OutputDataset-z4y"
            }
        ],
        "start": "2017-04-21T22:34:00Z",
        "end": "2017-04-25T05:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>Przykłady JSON kopiowania danych do i z Blob Storage
W poniższych przykładach przedstawiono przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazują one sposób kopiowania danych do i z usługi Azure Blob Storage i Azure SQL Database. Można jednak skopiować dane **bezpośrednio** z dowolnego źródła do dowolnego ujścia w [tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w Azure Data Factory.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>Przykład JSON: kopiowanie danych z Blob Storage do SQL Database
Poniższy przykład pokazuje:

1. Połączona usługa typu [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Połączona usługa typu [AzureStorage](#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [wartość azuresqltable](data-factory-azure-sql-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [BlobSource](#copy-activity-properties) i [sqlsink](data-factory-azure-sql-connector.md#copy-activity-properties).

Przykład kopiuje dane szeregów czasowych z obiektu blob platformy Azure do tabeli SQL Azure co godzinę. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

**Połączona usługa Azure SQL:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Połączona usługa Azure Storage:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory obsługuje dwa typy połączonych usług Azure Storage: **AzureStorage** i **AzureStorageSas**. W pierwszej kolejności należy określić parametry połączenia, które zawierają klucz konta i dla późniejszej, należy określić identyfikator URI sygnatury dostępu współdzielonego (SAS). Szczegóły można znaleźć w sekcji [połączone usługi](#linked-service-properties) .

**Zestaw danych wejściowych obiektów blob platformy Azure:**

Dane są pobierane z nowego obiektu BLOB co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu i nazwa pliku obiektu BLOB są dynamicznie oceniane na podstawie czasu rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części Year, month i Day czasu rozpoczęcia i nazwy pliku używa części godziny godziny rozpoczęcia. ustawienie "zewnętrzne": wartość "true" informuje Data Factory, że tabela jest zewnętrzna z fabryką danych i nie jest generowana przez działanie w fabryce danych.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Wyjściowy zestaw danych usługi Azure SQL:**

Przykład kopiuje dane do tabeli o nazwie "MyTable" w Azure SQL Database. Utwórz tabelę w bazie danych SQL o tej samej liczbie kolumn, która będzie zawierała plik CSV. Nowe wiersze są dodawane do tabeli co godzinę.

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Działanie kopiowania w potoku ze źródłem obiektów blob i obiektem SQL:**

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych i zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **BlobSource** , a typ **ujścia** to **sqlsink**.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>Przykład JSON: kopiowanie danych z usługi Azure SQL do obiektu blob platformy Azure
Poniższy przykład pokazuje:

1. Połączona usługa typu [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Połączona usługa typu [AzureStorage](#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [wartość azuresqltable](data-factory-azure-sql-connector.md#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa elementu [sqlsource](data-factory-azure-sql-connector.md#copy-activity-properties) i [wartość blobsink](#copy-activity-properties).

Przykład kopiuje dane szeregów czasowych z tabeli SQL Azure do obiektu blob platformy Azure co godzinę. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

**Połączona usługa Azure SQL:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Połączona usługa Azure Storage:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory obsługuje dwa typy połączonych usług Azure Storage: **AzureStorage** i **AzureStorageSas**. W pierwszej kolejności należy określić parametry połączenia, które zawierają klucz konta i dla późniejszej, należy określić identyfikator URI sygnatury dostępu współdzielonego (SAS). Szczegóły można znaleźć w sekcji [połączone usługi](#linked-service-properties) .

**Zestaw danych wejściowych usługi Azure SQL:**

W przykładzie przyjęto założenie, że utworzono tabelę "MyTable" w usłudze Azure SQL i zawiera kolumnę o nazwie "timestampcolumn" dla danych szeregów czasowych.

Ustawienie "External": "true" informuje Data Factory usługę, że tabela jest zewnętrzna z fabryką danych i nie jest generowana przez działanie w fabryce danych.

```json
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Wyjściowy zestaw danych obiektów blob platformy Azure:**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu dla obiektu BLOB jest obliczana dynamicznie na podstawie czasu rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części roku, miesiąca, dnia i godziny rozpoczęcia.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Działanie kopiowania w potoku ze źródłem SQL i obiektem BLOB:**

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych i zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma ustawioną wartość **sqlsource** , a typ **ujścia** to **wartość blobsink**. Zapytanie SQL określone dla właściwości **SqlReaderQuery** wybiera dane w ciągu ostatniej godziny do skopiowania.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

> [!NOTE]
> Aby zmapować kolumny ze źródłowego zestawu danych do kolumn z obiektu ujścia danych, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [Przewodnik dostrajania wydajności & działania kopiowania](data-factory-copy-activity-performance.md) , aby poznać kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) w Azure Data Factory i różne sposoby jego optymalizacji.
