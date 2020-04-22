---
title: Format JSON w fabryce danych platformy Azure
description: W tym temacie opisano sposób postępowania z formatem JSON w usłudze Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: jingwang
ms.openlocfilehash: 7b554ea5c2868559574979c58697fd31f8d2a2c4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686277"
---
# <a name="json-format-in-azure-data-factory"></a>Format JSON w fabryce danych platformy Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ten artykuł należy wykonać, aby **przeanalizować pliki JSON lub zapisać je w formacie JSON**. 

Format JSON jest obsługiwany dla następujących łączników: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), System [plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)i [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Zestawy danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych JSON.

| Właściwość         | Opis                                                  | Wymagany |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Właściwość typu zestawu danych musi być ustawiona na **Json**. | Tak      |
| location         | Ustawienia lokalizacji plików. Każdy łącznik oparty na plikach ma swój własny `location`typ lokalizacji i obsługiwane właściwości w obszarze . **Zobacz szczegóły w sekcji właściwości łącznika - > Dataset .** | Tak      |
| encodingName     | Typ kodowania używany do odczytu/zapisu plików testowych. <br>Allowed values are as follows: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| Nie       |
| kompresja | Grupa właściwości do skonfigurowania kompresji plików. Skonfiguruj tę sekcję, jeśli chcesz wykonać kompresję/dekompresję podczas wykonywania działania. | Nie |
| type | Koder-dekodusz kompresji używany do odczytu/zapisu plików JSON. <br>Dozwolone wartości to **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **snappy**lub **lz4**. , aby użyć go podczas zapisywania pliku. Wartość domyślna nie jest skompresowana.<br>**Uwaga** obecnie Copy activity nie obsługuje "snappy" & "lz4", a mapowanie przepływu danych nie obsługuje "ZipDeflate".<br>**Uwaga** Podczas korzystania z działania kopiowania do dekompresji plików ZipDeflate i zapisu do magazynu `<path specified in dataset>/<folder named as source zip file>/`danych ujścia opartego na plikach pliki zostaną wyodrębnione do folderu: . | Nie.  |
| poziom | Stopień kompresji. <br>Dozwolone wartości są **optymalne** lub **najszybsze**.<br>- **Najszybszy:** Operacja kompresji powinna zakończyć się tak szybko, jak to możliwe, nawet jeśli wynikowy plik nie jest optymalnie skompresowany.<br>- **Optymalne**: Operacja kompresji powinna być optymalnie skompresowana, nawet jeśli operacja trwa dłużej. Aby uzyskać więcej informacji, zobacz temat [Poziom kompresji.](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) | Nie       |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło JSON i ujście.

### <a name="json-as-source"></a>JSON jako źródło

Następujące właściwości są obsługiwane w sekcji *** \*\* źródła*** działania kopiowania.

| Właściwość      | Opis                                                  | Wymagany |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość typu źródła działania kopiowania musi być ustawiona na **JSONSource**. | Tak      |
| sklepyWystawy | Grupa właściwości dotyczące sposobu odczytywania danych z magazynu danych. Każdy łącznik oparty na plikach ma `storeSettings`własne obsługiwane ustawienia odczytu w obszarze . **Zobacz szczegóły w artykule łącznika -> Sekcji Właściwości działania kopiowania**. | Nie       |

### <a name="json-as-sink"></a>JSON jako zlew

Następujące właściwości są obsługiwane w sekcji *** \*ujście działania kopiowania.\* ***

| Właściwość      | Opis                                                  | Wymagany |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość typu źródła działania kopiowania musi być ustawiona na **JSONSink**. | Tak      |
| formatYStawienia | Grupa właściwości. Zapoznaj się z poniższą tabelą **ustawień zapisu JSON.** | Nie       |
| sklepyWystawy | Grupa właściwości dotyczące sposobu zapisywania danych w magazynie danych. Każdy łącznik oparty na plikach ma `storeSettings`własne obsługiwane ustawienia zapisu w obszarze . **Zobacz szczegóły w artykule łącznika -> Sekcji Właściwości działania kopiowania**. | Nie       |

Obsługiwane **ustawienia zapisu JSON** w obszarze: `formatSettings`

| Właściwość      | Opis                                                  | Wymagany                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Typ formatuStawienia muszą być ustawione na **JsonWriteSettings**. | Tak                                                   |
| filePattern |Wskazuje wzorzec danych przechowywanych w każdym pliku JSON. Dozwolone wartości to: **setOfObjects** i **arrayOfObjects**. Wartością **domyślną** jest **setOfObjects**. Aby uzyskać szczegółowe informacje o tych wzorcach, zobacz sekcję [Wzorce plików JSON](#json-file-patterns). |Nie |

### <a name="json-file-patterns"></a>Wzorce plików JSON

Działanie kopiowania może automatycznie wykrywać i analizować następujące wzorce plików JSON. 

- **Typ I: setOfObjects**

    Każdy plik zawiera pojedynczy obiekt lub wiele obiektów rozdzielonych wierszami bądź połączonych. 
    Gdy ta opcja jest wybrana w ujściu działania kopiowania, działanie kopiowania tworzy pojedynczy plik JSON z każdym obiektem w wierszu (rozdzielane linią).

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

    * **przykład kodu JSON z obiektami rozdzielonymi wierszami**

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

Typy plików JSON mogą być używane zarówno jako ujście, jak i źródło w przepływie danych mapowania.

### <a name="creating-json-structures-in-a-derived-column"></a>Tworzenie struktur JSON w kolumnie pochodnej

Do przepływu danych można dodać złożoną kolumnę za pośrednictwem konstruktora wyrażeń kolumn wyprowadzonych. W transformacji kolumny pochodnej dodaj nową kolumnę i otwórz konstruktora wyrażeń, klikając niebieskie pole. Aby uczynić kolumnę złożoną, można wprowadzić strukturę JSON ręcznie lub użyć środowiska użytkownika, aby interaktywnie dodać podkolumny.

#### <a name="using-the-expression-builder-ux"></a>Korzystanie z ux konstruktora wyrażeń

W okienku bocznym schematu wyjściowego umieść wskaźnik myszy na kolumnie i kliknij ikonę plus. Wybierz **dodaj podkolumnę,** aby kolumna była typem złożonym.

![Dodaj podkolumny](media/data-flow/addsubcolumn.png "Dodaj podkolumnę")

W ten sam sposób można dodać dodatkowe kolumny i podkolumny. Dla każdego pola niezłowieczne wyrażenie można dodać w edytorze wyrażeń po prawej stronie.

![Kolumna złożona](media/data-flow/complexcolumn.png "Kolumna złożona")

#### <a name="entering-the-json-structure-manually"></a>Ręczne wprowadzanie konstrukcji JSON

Aby ręcznie dodać strukturę JSON, dodaj nową kolumnę i wprowadź wyrażenie w edytorze. Wyrażenie jest zgodne z następującym ogólnym formatem:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Jeśli to wyrażenie zostało wprowadzone dla kolumny o nazwie "complexColumn", a następnie zostanie zapisany do ujścia jako następujące JSON:

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

### <a name="source-format-options"></a>Opcje formatu źródła

Użycie zestawu danych JSON jako źródła w przepływie danych umożliwia ustawienie pięciu dodatkowych ustawień. Te ustawienia można znaleźć w obszarze **ustawienia JSON** na karcie **Opcje źródłowe.**  

![Ustawienia JSON](media/data-flow/json-settings.png "Ustawienia JSON")

#### <a name="default"></a>Domyślne

Domyślnie dane JSON są odczytywane w następującym formacie.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>Pojedynczy dokument

Jeśli **wybrano pojedynczy dokument,** przepływy danych mapowania odczytują jeden dokument JSON z każdego pliku. 

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
> Jeśli przepływy danych zgłosić błąd informujący "corrupt_record" podczas wyświetlania podglądu danych JSON, jest prawdopodobne, że dane zawiera jeden dokument w pliku JSON. Ustawienie "pojedynczego dokumentu" powinno usunąć ten błąd.

#### <a name="unquoted-column-names"></a>Niecytowane nazwy kolumn

Jeśli zaznaczone są **nazwy kolumn niecytowanych,** przepływ danych mapowania odczytuje kolumny JSON, które nie są otoczone cudzysłowami. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>Ma komentarze

Wybierz pozycję **Ma komentarze,** jeśli dane JSON mają komentowanie w stylu C lub C++.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>Cytowana pojedyncza

Wybierz **opcję Pojedyncze cytowane,** jeśli pola json i wartości używają cudzysłowów pojedynczych zamiast cudzysłowów podwójnych.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>Ukośnik odwrotny uciekł

Wybierz **Pojedynczy cudzysłow,** jeśli ukośniki odwrotne są używane do usuwania znaków w danych JSON.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="next-steps"></a>Następne kroki

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
