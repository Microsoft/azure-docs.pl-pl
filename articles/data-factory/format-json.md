---
title: Format JSON w Azure Data Factory
description: W tym temacie opisano sposób postępowania z formatem JSON w Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: e0d8d5c833460838c28c7bc20b3ab764ed3ae659
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011629"
---
# <a name="json-format-in-azure-data-factory"></a>Format JSON w Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Postępuj zgodnie z tym artykułem, jeśli chcesz **przeanalizować pliki JSON lub zapisać dane w formacie JSON**. 

Format JSON jest obsługiwany dla następujących łączników: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [system plików](connector-file-system.md), [FTP](connector-ftp.md), [Magazyn w chmurze Google](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)i [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych JSON.

| Właściwość         | Opis                                                  | Wymagane |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Właściwość Type zestawu danych musi być ustawiona na wartość **JSON**. | Tak      |
| location         | Ustawienia lokalizacji plików. Każdy Łącznik oparty na plikach ma własny typ lokalizacji i obsługiwane właściwości w sekcji `location` . **Zobacz szczegóły w sekcji łącznik — > właściwości zestawu danych**. | Tak      |
| encodingName     | Typ kodowania używany do odczytu/zapisu plików testowych. <br>Dozwolone wartości są następujące: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| Nie       |
| kompresja | Grupa właściwości do konfigurowania kompresji plików. Skonfiguruj tę sekcję, jeśli chcesz przeprowadzić kompresję/dekompresowanie podczas wykonywania działania. | Nie |
| typ<br/>(*w `compression` obszarze*) | Koder-dekoder kompresji używany do odczytu/zapisu plików JSON. <br>Dozwolone wartości to **bzip2**, **gzip**, **Wklęśnięcie**, **ZipDeflate**, **TarGzip**, **tar**, **przyciągania** lub **lz4**. Wartość domyślna nie jest skompresowana.<br>Działanie **kopiowania obecnie nie** obsługuje "przyciągania" & "lz4", a przepływ danych mapowania nie obsługuje "ZipDeflate" "," TarGzip "i" tar ".<br>**Uwaga** w przypadku używania działania kopiowania w celu dekompresowania **ZipDeflate** / **TarGzip** / plików **tar** i zapisu w magazynie danych obiektów ujścia opartych na plikach, domyślnie pliki są wyodrębniane do folderu: `<path specified in dataset>/<folder named as source compressed file>/` , użyj `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [źródła działania Copy](#json-as-source) , aby określić, czy należy zachować nazwę skompresowanych plików jako strukturę folderów.| Nie.  |
| poziom<br/>(*w `compression` obszarze*) | Współczynnik kompresji. <br>Dozwolone wartości to **optymalne** lub **najszybszy**.<br>- **Najszybsze:** Operacja kompresji powinna zostać ukończona tak szybko, jak to możliwe, nawet jeśli plik nie jest optymalnie kompresowany.<br>- **Optymalnie**: operacja kompresji powinna być optymalnie skompresowana, nawet jeśli operacja trwa dłużej. Aby uzyskać więcej informacji, zobacz temat [poziom kompresji](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Nie       |

Poniżej znajduje się przykład zestawu danych JSON w usłudze Azure Blob Storage:

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "gzip"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujścia danych JSON.

Dowiedz się więcej na temat sposobu wyodrębniania danych z plików JSON i mapowania na ujścia/format danych lub odwrotnie z [mapowania schematu](copy-activity-schema-and-type-mapping.md).

### <a name="json-as-source"></a>KOD JSON jako źródło

W sekcji działanie copy **_ \_ Source \**** są obsługiwane następujące właściwości.

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **JSONSource**. | Tak      |
| formatSettings | Grupa właściwości. Zapoznaj się z tabelą **ustawień odczytu JSON** poniżej. | Nie       |
| storeSettings | Grupa właściwości do odczytywania danych z magazynu danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia odczytu w obszarze `storeSettings` . **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

Obsługiwane **Ustawienia odczytu JSON** w obszarze `formatSettings` :

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ formatSettings musi być ustawiony na **JsonReadSettings**. | Tak      |
| compressionProperties | Grupa właściwości na temat sposobu dekompresowania danych dla danego kodera kompresji. | Nie       |
| preserveZipFileNameAsFolder<br>(*w obszarze `compressionProperties` -> `type` jako `ZipDeflateReadSettings`*)  | Stosuje się, gdy zestaw danych wejściowych jest skonfigurowany z kompresją **ZipDeflate** . Wskazuje, czy podczas kopiowania zachować nazwę źródłowego pliku zip jako strukturę folderów.<br>-Po ustawieniu na **wartość true (domyślnie)** Data Factory zapisuje niespakowane pliki do `<path specified in dataset>/<folder named as source zip file>/` .<br>-Po ustawieniu na **wartość false** Data Factory zapisuje niespakowane pliki bezpośrednio do `<path specified in dataset>` . Upewnij się, że nie masz zduplikowanych nazw plików w różnych źródłowych plikach ZIP, aby uniknąć wyścigów lub nieoczekiwanych zachowań.  | Nie |
| preserveCompressionFileNameAsFolder<br>(*w obszarze `compressionProperties` -> `type` jako `TarGZipReadSettings` lub `TarReadSettings`*) | Stosuje się, gdy zestaw danych wejściowych **TarGzip** jest skonfigurowany z / kompresją **pułapki** TarGzip. Wskazuje, czy podczas kopiowania zachować źródłową nazwę pliku skompresowanego jako strukturę folderów.<br>-Po ustawieniu na **wartość true (domyślnie)** Data Factory zapisuje zdekompresować pliki do `<path specified in dataset>/<folder named as source compressed file>/` . <br>-Po ustawieniu na **wartość false** Data Factory zapisuje dekompresowane pliki bezpośrednio do `<path specified in dataset>` . Upewnij się, że nie masz zduplikowanych nazw plików w różnych plikach źródłowych, aby uniknąć wyścigów lub nieoczekiwanych zachowań. | Nie |

### <a name="json-as-sink"></a>JSON jako ujścia

W sekcji działanie kopiowania **_ \_ ujścia \**** są obsługiwane następujące właściwości.

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **JSONSink**. | Tak      |
| formatSettings | Grupa właściwości. Zapoznaj się z tabelą **ustawień zapisu JSON** poniżej. | Nie       |
| storeSettings | Grupa właściwości do zapisywania danych w magazynie danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia zapisu w obszarze `storeSettings` . **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

Obsługiwane **Ustawienia zapisu** w formacie JSON w obszarze `formatSettings` :

| Właściwość      | Opis                                                  | Wymagane                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| typ          | Typ formatSettings musi być ustawiony na **JsonWriteSettings**. | Tak                                                   |
| filePattern |Wskazuje wzorzec danych przechowywanych w każdym pliku JSON. Dozwolone wartości to: **setOfObjects** (linie JSON) i **arrayOfObjects**. Wartością **domyślną** jest **setOfObjects**. Aby uzyskać szczegółowe informacje o tych wzorcach, zobacz sekcję [Wzorce plików JSON](#json-file-patterns). |Nie |

### <a name="json-file-patterns"></a>Wzorce plików JSON

Podczas kopiowania danych z plików JSON działania kopiowania mogą automatycznie wykrywać i analizować następujące wzorce plików JSON. Podczas zapisywania danych w plikach JSON można skonfigurować wzorzec pliku dla ujścia działania kopiowania.

- **Typ I: setOfObjects**

    Każdy plik zawiera pojedynczy obiekt, linie JSON lub połączone obiekty.

    * **przykład kodu JSON z pojedynczym obiektem**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **Linie JSON (domyślnie dla ujścia)**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **przykład kodu JSON z obiektami połączonymi**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Typ II: arrayOfObjects**

    Każdy plik zawiera tablicę obiektów.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

W mapowaniu przepływów danych można odczytywać i zapisywać w formacie JSON w następujących magazynach danych: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)i [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Właściwości źródła

Poniższa tabela zawiera listę właściwości obsługiwanych przez źródło danych JSON. Można edytować te właściwości na karcie **Opcje źródła** .

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Ścieżki symboli wieloznacznych | Wszystkie pliki zgodne ze ścieżką wieloznaczną zostaną przetworzone. Zastępuje folder i ścieżkę pliku ustawioną w zestawie danych. | nie | Ciąg [] | wildcardPaths |
| Ścieżka katalogu głównego partycji | W przypadku danych plików podzielonych na partycje można wprowadzić ścieżkę katalogu głównego partycji, aby odczytywać foldery partycjonowane jako kolumny | nie | Ciąg | partitionRootPath |
| Lista plików | Czy źródło wskazuje plik tekstowy, który zawiera listę plików do przetworzenia | nie | `true` lub `false` | fileList |
| Kolumna do przechowywania nazwy pliku | Utwórz nową kolumnę o nazwie i ścieżce pliku źródłowego | nie | Ciąg | rowUrlColumn |
| Po zakończeniu | Usuń lub Przenieś pliki po przetworzeniu. Ścieżka pliku zaczyna się od katalogu głównego kontenera | nie | Usuń: `true` lub `false` <br> Przenieś `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Filtruj według ostatniej modyfikacji | Wybierz filtrowanie plików na podstawie czasu ich ostatniej modyfikacji | nie | Timestamp | modifiedAfter <br> modifiedBefore |
| Pojedynczy dokument | Mapowanie przepływów danych odczytywanie jednego dokumentu JSON z każdego pliku | nie | `true` lub `false` | singleDocument |
| Nazwy kolumn bez cytowania | Jeśli wybrano **nazwy kolumn bez cudzysłowów** , mapowanie przepływów danych odczytuje kolumny JSON, które nie są ujęte w cudzysłowy. | nie | `true` lub `false` |  unquotedColumnNames |
| Ma Komentarze | Zaznacz pole **ma Komentarze** , jeśli dane JSON mają komentarz w stylu C lub C++ | nie | `true` lub `false` | asComments |
| Pojedyncze cudzysłowy | Odczytuje kolumny JSON, które nie są ujęte w cudzysłowy | nie | `true` lub `false` | singleQuoted |
| Odwrócony ukośnik odwrotny | Wybierz **ukośnik odwrotny** , jeśli ukośniki odwrotne są używane do ucieczki znaków w danych JSON | nie | `true` lub `false` | backslashEscape |
| Nie znaleziono plików | W przypadku wartości true błąd nie jest zgłaszany, jeśli nie znaleziono plików | nie | `true` lub `false` | ignoreNoFilesFound |

### <a name="source-format-options"></a>Opcje formatu źródła

Używanie zestawu danych JSON jako źródła w przepływie danych pozwala na ustawienie pięciu dodatkowych ustawień. Te ustawienia można znaleźć w obszarze **Ustawienia JSON** zgodnie z opisem na karcie **Opcje źródła** .  

![Ustawienia JSON](media/data-flow/json-settings.png "Ustawienia JSON")

#### <a name="default"></a>Domyślne

Domyślnie dane JSON są odczytywane w następującym formacie.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>Pojedynczy dokument

W przypadku wybrania **jednego dokumentu** mapowanie przepływów danych odczytuje jeden dokument JSON z każdego pliku. 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```
> [!NOTE]
> Jeśli przepływy danych zgłaszają błąd informujący o błędzie "corrupt_record" podczas wyświetlania podglądu danych JSON, prawdopodobnie dane zawierają pojedynczy dokument w pliku JSON. Ustawienie "pojedynczego dokumentu" powinno wyczyścić Ten błąd.

#### <a name="unquoted-column-names"></a>Nazwy kolumn bez cytowania

Jeśli wybrano **nazwy kolumn bez cudzysłowów** , mapowanie przepływów danych odczytuje kolumny JSON, które nie są ujęte w cudzysłowy. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>Ma Komentarze

Zaznacz pole **ma Komentarze** , jeśli dane JSON mają komentarz w stylu C lub C++.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>Pojedyncze cudzysłowy

Wybierz opcję **pojedyncze cudzysłowy** , jeśli pola i wartości JSON używają apostrofów zamiast podwójnych cudzysłowów.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>Odwrócony ukośnik odwrotny

Wybierz **ukośnik odwrotny** , jeśli ukośniki odwrotne są używane do ucieczki znaków w danych JSON.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

### <a name="sink-properties"></a>Właściwości ujścia

Poniższa tabela zawiera listę właściwości obsługiwanych przez ujścia JSON. Te właściwości można edytować na karcie **Ustawienia** .

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Wyczyść folder | Jeśli folder docelowy został wyczyszczony przed zapisem | nie | `true` lub `false` | obciąć |
| Opcja nazwy pliku | Format nazewnictwa zapisanych danych. Domyślnie jeden plik na partycję w formacie `part-#####-tid-<guid>` | nie | Wzorzec: ciąg <br> Na partycję: String [] <br> Jako dane w kolumnie: ciąg <br> Dane wyjściowe do pojedynczego pliku: `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="creating-json-structures-in-a-derived-column"></a>Tworzenie struktur JSON w kolumnie pochodnej

Do przepływu danych można dodać kolumnę złożoną za pośrednictwem konstruktora wyrażeń kolumn pochodnych. W transformację kolumn pochodnych Dodaj nową kolumnę, a następnie otwórz konstruktora wyrażeń, klikając niebieską ramkę. Aby utworzyć kolumnę złożoną, można wprowadzić strukturę JSON ręcznie lub użyć środowiska użytkownika do interaktywnego dodawania podkolumn.

#### <a name="using-the-expression-builder-ux"></a>Korzystanie z środowiska programu Expression Builder

W okienku po stronie schematu danych wyjściowych Umieść kursor nad kolumną i kliknij ikonę znaku plus. Wybierz pozycję **Dodaj podkolumnę** , aby utworzyć kolumnę typu złożonego.

![Dodaj podkolumnę](media/data-flow/derive-add-subcolumn.png "Dodaj podkolumnę")

W ten sam sposób można dodać dodatkowe kolumny i podkolumny. W przypadku każdego niezłożonej pola wyrażenie może być dodane w edytorze wyrażeń z prawej strony.

![Dodawanie złożonej kolumny](media/data-flow/derive-complex-column.png "Dodawanie kolumn")

#### <a name="entering-the-json-structure-manually"></a>Ręczne wprowadzanie struktury JSON

Aby ręcznie dodać strukturę JSON, Dodaj nową kolumnę i wprowadź wyrażenie w edytorze. Wyrażenie jest zgodne z następującym formatem ogólnym:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Jeśli to wyrażenie zostało wprowadzone dla kolumny o nazwie "complexColumn", zostanie ona zapisywana w ujścia jako następujący kod JSON:

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Przykładowy skrypt ręczny dla pełnej definicji hierarchicznej
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="next-steps"></a>Następne kroki

- [Przegląd działania kopiowania](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)