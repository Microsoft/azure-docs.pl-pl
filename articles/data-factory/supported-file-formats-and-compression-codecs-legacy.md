---
title: Obsługiwane formaty plików w usłudze Azure Data Factory (starsze wersje)
description: W tym temacie opisano formaty plików i kody kompresji, które są obsługiwane przez łączniki oparte na plikach w usłudze Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: b1f11a1ff25117c07e61475e7e83fc0c170cd552
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414650"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory-legacy"></a>Obsługiwane formaty plików i kodeki kompresji w usłudze Azure Data Factory (starsze wersje)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Ten artykuł dotyczy następujących łączników: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), System [plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)i [SFTP](connector-sftp.md).*

>[!IMPORTANT]
>Data Factory wprowadzono nowy model zestawu danych oparty na formacie, zobacz odpowiedni artykuł format ze szczegółami: <br>- [Format Avro](format-avro.md)<br>- [Format binarny](format-binary.md)<br>- [Rozdzielony format tekstu](format-delimited-text.md)<br>- [Format JSON](format-json.md)<br>- [Format ORC](format-orc.md)<br>- [Format parkietu](format-parquet.md)<br>Pozostałe konfiguracje wymienione w tym artykule są nadal obsługiwane jako — jest dla kompabitilności wstecznej. Zaleca się, aby użyć nowego modelu w przyszłości. 

## <a name="text-format-legacy"></a><a name="text-format"></a>Format tekstu (starsza wersja)

>[!NOTE]
>Poznaj nowy model z artykułu [o formacie tekstowym rozdzielanym.](format-delimited-text.md) Następujące konfiguracje zestawu danych magazynu danych opartego na plikach są nadal obsługiwane w stanie— jest to zgodne z wstecznym rozmiarem. Zaleca się, aby użyć nowego modelu w przyszłości.

Jeśli chcesz odczytać z pliku tekstowego lub zapisać do `type` pliku `format` tekstowego, ustaw właściwość w sekcji zestawu danych na **TextFormat**. Ponadto możesz określić następujące **opcjonalne** właściwości w sekcji `format`. Aby uzyskać informacje na temat sposobu konfigurowania, zobacz sekcję [Przykład formatu TextFormat](#textformat-example).

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| columnDelimiter |Znak używany do rozdzielania kolumn w pliku. Można rozważyć użycie rzadkiego znaku, który może nie mieć druku w danych. Na przykład określ "\u0001", który reprezentuje początek nagłówka (SOH). |Dozwolony jest tylko jeden znak. Wartość **domyślna** to **przecinek (,)**. <br/><br/>Aby użyć znaku Unicode, należy zapoznać się ze [znakami Unicode,](https://en.wikipedia.org/wiki/List_of_Unicode_characters) aby uzyskać odpowiedni kod dla niego. |Nie |
| rowDelimiter |Znak używany do rozdzielania wierszy w pliku. |Dozwolony jest tylko jeden znak. Wartością **domyślną** jest dowolna z następujących wartości przy odczycie: **[„\r\n”, „\r”, „\n”]** oraz wartość **„\r\n”** przy zapisie. |Nie |
| escapeChar |Znak specjalny służący do zmiany interpretacji ogranicznika kolumny w zawartości pliku wejściowego. <br/><br/>W przypadku tabeli nie można określić zarówno właściwości escapeChar, jak i quoteChar. |Dozwolony jest tylko jeden znak. Brak wartości domyślnej. <br/><br/>Przykład: jeśli masz przecinek (',') jako ogranicznik kolumny, ale chcesz mieć znak przecinka w tekście (przykład: "Hello, world"), możesz zdefiniować '$' jako znak ucieczki i użyć ciągu "Hello$, world" w źródle. |Nie |
| quoteChar |Znak używany do umieszczania wartości ciągu w cudzysłowie. Ograniczniki kolumny i wiersza umieszczone w cudzysłowie są traktowane jako część wartości ciągu. Ta właściwość ma zastosowanie zarówno do wejściowych, jak i wyjściowych zestawów danych.<br/><br/>W przypadku tabeli nie można określić zarówno właściwości escapeChar, jak i quoteChar. |Dozwolony jest tylko jeden znak. Brak wartości domyślnej. <br/><br/>Na przykład jeśli ogranicznikiem kolumny jest przecinek (,), ale chcesz, aby znak przecinka występował w tekście (przykład: <Witaj, świecie>), możesz zdefiniować cudzysłów (") jako znak cudzysłowu i użyć ciągu "Witaj, świecie" w źródle. |Nie |
| nullValue |Co najmniej jeden znak służący do reprezentowania wartości null. |Co najmniej jeden znak. Wartości **domyślne** to **„\N” i „NULL”** przy odczycie oraz **„\N”** przy zapisie. |Nie |
| encodingName |Określa nazwę kodowania. |Prawidłowa nazwa kodowania. Zobacz [właściwość Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Przykład: windows-1250 lub shift_jis. Wartość **domyślna** to **UTF-8**. |Nie |
| firstRowAsHeader |Określa, czy pierwszy wiersz ma być traktowany jako nagłówek. W przypadku zestawu danych wejściowych usługa Data Factory odczytuje pierwszy wiersz jako nagłówek. W przypadku zestawu danych wyjściowych usługa Data Factory zapisuje pierwszy wiersz jako nagłówek. <br/><br/>Aby uzyskać przykładowe scenariusze, zobacz sekcję [Scenariusze użycia właściwości `firstRowAsHeader` oraz `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |True<br/><b>False (domyślnie)</b> |Nie |
| skipLineCount |Wskazuje liczbę **niepustych** wierszy do pominięcia podczas odczytywania danych z plików wejściowych. Jeśli określono zarówno właściwość skipLineCount, jak i firstRowAsHeader, najpierw zostaną pominięte wiersze, a następnie zostaną odczytane informacje nagłówka z pliku wejściowego. <br/><br/>Aby uzyskać przykładowe scenariusze, zobacz sekcję [Scenariusze użycia właściwości `firstRowAsHeader` oraz `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Liczba całkowita |Nie |
| treatEmptyAsNull |Określa, czy ciąg pusty lub o wartości null ma być traktowany jako wartość null podczas odczytu danych z pliku wejściowego. |**True (domyślnie)**<br/>False |Nie |

### <a name="textformat-example"></a>Przykład formatu TextFormat

W poniższej definicji JSON dla zestawu danych niektóre właściwości opcjonalne są określone.

```json
"typeProperties":
{
    "folderPath": "mycontainer/myfolder",
    "fileName": "myblobname",
    "format":
    {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";",
        "quoteChar": "\"",
        "NullValue": "NaN",
        "firstRowAsHeader": true,
        "skipLineCount": 0,
        "treatEmptyAsNull": true
    }
},
```

Aby użyć właściwości `escapeChar` zamiast `quoteChar`, zastąp wiersz z właściwością `quoteChar` następującą właściwością escapeChar:

```json
"escapeChar": "$",
```

### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Scenariusze użycia właściwości firstRowAsHeader oraz skipLineCount

* Kopiujesz dane ze źródła innego niż plik do pliku tekstowego i chcesz dodać wiersz nagłówka zawierający metadane schematu (na przykład: schemat SQL). Ustaw właściwość `firstRowAsHeader` na wartość true w zestawie danych wyjściowych dla tego scenariusza.
* Kopiujesz dane z pliku tekstowego zawierającego wiersz nagłówka do ujścia innego niż plik i chcesz pominąć ten wiersz. Ustaw właściwość `firstRowAsHeader` na wartość true w zestawie danych wejściowych.
* Kopiujesz dane z pliku tekstowego i chcesz pominąć kilka początkowych wierszy, które nie zawierają żadnych danych bądź informacji nagłówka. Określ właściwość `skipLineCount`, aby wskazać liczbę wierszy do pominięcia. Jeśli pozostała część pliku zawiera wiersz nagłówka, możesz również określić właściwość `firstRowAsHeader`. Jeśli określono zarówno właściwość `skipLineCount`, jak i `firstRowAsHeader`, najpierw zostaną pominięte wiersze, a następnie zostaną odczytane informacje nagłówka z pliku wejściowego

## <a name="json-format-legacy"></a><a name="json-format"></a>Format JSON (starsza wersja)

>[!NOTE]
>Poznaj nowy model z artykułu [w formacie JSON.](format-json.md) Następujące konfiguracje zestawu danych magazynu danych opartego na plikach są nadal obsługiwane w stanie— jest to zgodne z wstecznym rozmiarem. Zaleca się, aby użyć nowego modelu w przyszłości.

Aby **zaimportować/wyeksportować plik JSON jako —do/z usługi Azure Cosmos DB**, zobacz Importowanie/eksportowanie dokumentów JSON sekcji w [Przenieś dane do/z usługi Azure Cosmos DB](connector-azure-cosmos-db.md) artykułu.

Jeśli chcesz przeanalizować pliki JSON lub zapisać dane w formacie `type` JSON, `format` ustaw właściwość w sekcji na **JsonFormat**. Ponadto możesz określić następujące **opcjonalne** właściwości w sekcji `format`. Aby uzyskać informacje na temat sposobu konfigurowania, zobacz sekcję [Przykład formatu JsonFormat](#jsonformat-example).

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| filePattern |Wskazuje wzorzec danych przechowywanych w każdym pliku JSON. Dozwolone wartości to: **setOfObjects** i **arrayOfObjects**. Wartością **domyślną** jest **setOfObjects**. Aby uzyskać szczegółowe informacje o tych wzorcach, zobacz sekcję [Wzorce plików JSON](#json-file-patterns). |Nie |
| jsonNodeReference | Jeśli chcesz wykonać iterację i ekstrakcję danych z obiektów wewnątrz pola tablicy o tym samym wzorcu, określ ścieżkę JSON tej tablicy. Ta właściwość jest obsługiwana tylko podczas kopiowania danych **z** plików JSON. | Nie |
| jsonPathDefinition | Określa wyrażenie ścieżki JSON dla każdego mapowania kolumny z niestandardową nazwą kolumny (musi zaczynać się małą literą). Ta właściwość jest obsługiwana tylko podczas kopiowania danych **z** plików JSON i można wyodrębnić dane z obiektu lub tablicy. <br/><br/> W przypadku pól obiektu głównego na początku użyj elementu głównego $. W przypadku pól wewnątrz tablicy wybranej przez właściwość `jsonNodeReference` najpierw podaj element tablicy. Aby uzyskać informacje na temat sposobu konfigurowania, zobacz sekcję [Przykład formatu JsonFormat](#jsonformat-example). | Nie |
| encodingName |Określa nazwę kodowania. Aby uzyskać listę prawidłowych nazw kodowania, zobacz właściwość [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Na przykład: windows-1250 lub shift_jis. Wartość **domyślna** to: **UTF-8**. |Nie |
| nestingSeparator |Znak używany do rozdzielania poziomów zagnieżdżenia. Wartość domyślna to „.” (kropka). |Nie |

>[!NOTE]
>W przypadku danych krzyżowych w tablicy do wielu wierszy (przypadek 1 -> próbki 2 w [przykładach JsonFormat),](#jsonformat-example)można rozwinąć tylko pojedynczą tablicę za pomocą właściwości `jsonNodeReference`.

### <a name="json-file-patterns"></a>Wzorce plików JSON

Działanie kopiowania może analizować następujące wzorce plików JSON:

- **Typ I: setOfObjects**

    Każdy plik zawiera pojedynczy obiekt lub wiele obiektów rozdzielonych wierszami bądź połączonych. W przypadku wybrania tej opcji w zestawie danych wyjściowych działanie kopiowania tworzy pojedynczy plik JSON z każdym obiektem w osobnym wierszu (rozdzielanie wierszami).

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

### <a name="jsonformat-example"></a>Przykład formatu JsonFormat

**Przypadek 1. Kopiowanie danych z plików JSON**

**Przykład 1. Wyodrębnianie danych z obiektu i tablicy**

W tym przykładzie oczekiwany jest jeden główny obiekt JSON mapowany na pojedynczy rekord w wyniku tabelarycznym. Jeśli masz plik JSON z następującą zawartością:

```json
{
    "id": "ed0e4960-d9c5-11e6-85dc-d7996816aad3",
    "context": {
        "device": {
            "type": "PC"
        },
        "custom": {
            "dimensions": [
                {
                    "TargetResourceType": "Microsoft.Compute/virtualMachines"
                },
                {
                    "ResourceManagementProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
                },
                {
                    "OccurrenceTime": "1/13/2017 11:24:37 AM"
                }
            ]
        }
    }
}
```

i chcesz skopiować ją do tabeli usługi Azure SQL w następującym formacie przez wyodrębnienie danych z obiektu i tabeli:

| ID | deviceType | targetResourceType | identyfikator y gospodarowania zasobamiProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

Zestaw danych wejściowych typu **JsonFormat** jest zdefiniowany następująco: (częściowa definicja zawierająca tylko stosowne fragmenty). Więcej szczegółów:

- Sekcja `structure` definiuje niestandardowe nazwy kolumn i odpowiedni typ danych podczas konwersji na dane tabelaryczne. Ta sekcja jest **opcjonalna**, o ile nie trzeba wykonać mapowania kolumn. Aby uzyskać więcej informacji, zobacz [Mapowanie kolumn źródłowego zestawu danych do docelowych kolumn zestawu danych](copy-activity-schema-and-type-mapping.md).
- Właściwość `jsonPathDefinition` określa ścieżkę JSON dla każdej kolumny, wskazując, skąd mają zostać wyodrębnione dane. Aby skopiować dane z `array[x].property` tablicy, można użyć wyodrębnić wartość danej właściwości z `xth` obiektu lub można użyć, `array[*].property` aby znaleźć wartość z dowolnego obiektu zawierającego taką właściwość.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "deviceType",
            "type": "String"
        },
        {
            "name": "targetResourceType",
            "type": "String"
        },
        {
            "name": "resourceManagementProcessRunId",
            "type": "String"
        },
        {
            "name": "occurrenceTime",
            "type": "DateTime"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagementProcessRunId": "$.context.custom.dimensions[1].ResourceManagementProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}
        }
    }
}
```

**Przykład 2. Krzyżowe stosowanie tego samego wzorca z tabeli do wielu obiektów**

W tym przykładzie oczekiwane jest przetransformowanie jednego głównego obiektu JSON na wiele rekordów w wyniku tabelarycznym. Jeśli masz plik JSON z następującą zawartością:

```json
{
    "ordernumber": "01",
    "orderdate": "20170122",
    "orderlines": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "sanmateo": "No 1" } ]
}
```

i chcesz ją skopiować do tabeli Azure SQL w następującym formacie, spłaszczając dane wewnątrz tablicy i łącząc je krzyżowo ze wspólnymi informacjami głównymi:

| `ordernumber` | `orderdate` | `order_pd` | `order_price` | `city` |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P2 | 13 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P3 | 231 | `[{"sanmateo":"No 1"}]` |


Zestaw danych wejściowych typu **JsonFormat** jest zdefiniowany następująco: (częściowa definicja zawierająca tylko stosowne fragmenty). Więcej szczegółów:

- Sekcja `structure` definiuje niestandardowe nazwy kolumn i odpowiedni typ danych podczas konwersji na dane tabelaryczne. Ta sekcja jest **opcjonalna**, o ile nie trzeba wykonać mapowania kolumn. Aby uzyskać więcej informacji, zobacz [Mapowanie kolumn źródłowego zestawu danych do docelowych kolumn zestawu danych](copy-activity-schema-and-type-mapping.md).
- `jsonNodeReference`wskazuje, aby iterować i wyodrębnić dane z obiektów o tym samym wzorcu w **tablicy** `orderlines`.
- Właściwość `jsonPathDefinition` określa ścieżkę JSON dla każdej kolumny, wskazując, skąd mają zostać wyodrębnione dane. W tym `ordernumber`przykładzie , i `orderdate` `city` znajdują się pod `$.`obiektem `order_pd` `order_price` głównym ze ścieżką JSON począwszy od , while i są zdefiniowane ze ścieżką pochodzącą z elementu tablicy bez `$.`.

```json
"properties": {
    "structure": [
        {
            "name": "ordernumber",
            "type": "String"
        },
        {
            "name": "orderdate",
            "type": "String"
        },
        {
            "name": "order_pd",
            "type": "String"
        },
        {
            "name": "order_price",
            "type": "Int64"
        },
        {
            "name": "city",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonNodeReference": "$.orderlines",
            "jsonPathDefinition": {"ordernumber": "$.ordernumber", "orderdate": "$.orderdate", "order_pd": "prod", "order_price": "price", "city": " $.city"}
        }
    }
}
```

**Pamiętaj o następujących kwestiach:**

* Jeśli sekcja `structure` i właściwość `jsonPathDefinition` nie są zdefiniowane w zestawie danych usługi Data Factory, działanie kopiowania wykrywa schemat z pierwszego obiektu i spłaszcza cały obiekt.
* Jeśli dane wejściowe JSON zawierają tablicę, działanie kopiowania domyślnie konwertuje całą wartość tablicy na ciąg. Możesz wyodrębnić z niej dane przy użyciu właściwości `jsonNodeReference` i/lub `jsonPathDefinition` bądź ją pominąć, nie określając jej we właściwości `jsonPathDefinition`.
* Jeśli na tym samym poziomie występują zduplikowane nazwy, działanie kopiowania wybierze ostatnią z nich.
* W przypadku nazw właściwości wielkość liter ma znaczenie. Dwie właściwości o takiej samej nazwie, ale zapisanej przy użyciu różnej wielkości liter, są traktowane jako dwie osobne właściwości.

**Przypadek 2. Zapisywanie danych do pliku JSON**

Jeśli masz następującą tabelę w bazie danych SQL:

| ID | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

i dla każdego rekordu, można oczekiwać, aby zapisać do obiektu JSON w następującym formacie:

```json
{
    "id": "1",
    "order": {
        "date": "20170119",
        "price": 2000,
        "customer": "David"
    }
}
```

Zestaw danych wyjściowych typu **JsonFormat** jest zdefiniowany następująco: (częściowa definicja zawierająca tylko stosowne fragmenty). W szczególności `structure` sekcja definiuje niestandardowe nazwy właściwości w `nestingSeparator` pliku docelowym (domyślnie jest to ".") są używane do identyfikowania warstwy gniazda z nazwy. Ta sekcja jest **opcjonalna**, o ile nie chcesz zmieniać nazwy właściwości na podstawie porównania z nazwą kolumny źródłowej ani zagnieżdżać właściwości.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "order.date",
            "type": "String"
        },
        {
            "name": "order.price",
            "type": "Int64"
        },
        {
            "name": "order.customer",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat"
        }
    }
}
```

## <a name="parquet-format-legacy"></a><a name="parquet-format"></a>Format parkietu (legacy)

>[!NOTE]
>Poznaj nowy model z artykułu [w formacie Parkietu.](format-parquet.md) Następujące konfiguracje zestawu danych magazynu danych opartego na plikach są nadal obsługiwane w stanie— jest to zgodne z wstecznym rozmiarem. Zaleca się, aby użyć nowego modelu w przyszłości.

Jeśli chcesz analizować pliki Parquet lub zapisywać dane w formacie Parquet, ustaw właściwość `format` `type` na wartość **ParquetFormat**. Nie musisz określać żadnych właściwości w sekcji Format należącej do sekcji typeProperties. Przykład:

```json
"format":
{
    "type": "ParquetFormat"
}
```

Pamiętaj o następujących kwestiach:

* Złożone typy danych nie są obsługiwane (MAP, LIST).
* Biały znak w nazwie kolumny nie jest obsługiwany.
* Plik Parquet ma następujące opcje związane z kompresją: NONE, SNAPPY, GZIP oraz LZO. Data Factory obsługuje odczytywanie danych z pliku Parkietu w każdym z tych skompresowanych formatów z wyjątkiem LZO - używa kodeka kompresji w metadanych do odczytu danych. Podczas zapisywania w pliku Parquet usługa Data Factory wybiera natomiast opcję SNAPPY, która jest domyślna dla formatu Parquet. Obecnie nie ma możliwości zastąpienia tego zachowania.

> [!IMPORTANT]
> W przypadku kopiowania wspieranego przez self-hosted Integration Runtime, na przykład między lokalnymi i chmurowymi magazynami danych, jeśli nie kopiujesz plików **Parkietu w stanie rzeczywistym,** musisz zainstalować **64-bitowy środowiska JRE 8 (Java Runtime Environment) lub OpenJDK** na komputerze podczerwieni. Zobacz poniższy akapit z więcej szczegółów.

W przypadku kopiowania uruchomionego na samodzielnym podczerweniu z serializacją/deserializacji plików Parkietu, *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* ADF lokalizuje środowisko wykonawcze Java, *`JAVA_HOME`* najpierw sprawdzając rejestr dla środowiska JRE, jeśli nie znaleziono, po drugie sprawdzając zmienną systemową dla OpenJDK.

- **Aby użyć środowiska JRE:** 64-bitowa podczerwość wymaga 64-bitowego środowiska JRE. Można go znaleźć [tutaj](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Aby użyć OpenJDK**: jest obsługiwany od wersji IR 3.13. Pakiet jvm.dll ze wszystkimi innymi wymaganymi zestawami OpenJDK do samodzielnego hostowania komputera podczerwieni i odpowiednio ustawić zmienną środowiska systemu JAVA_HOME.

>[!TIP]
>Jeśli kopiujesz dane do/z formatu Parkietu przy użyciu self-hosted Integration Runtime i błąd trafienia mówiąc: "Wystąpił błąd podczas wywoływania java, komunikat: `_JAVA_OPTIONS` **java.lang.OutOfMemoryError:Java sterty miejsca**", można dodać zmienną środowiskową na komputerze, który obsługuje Self-hosted IR, aby dostosować rozmiar sterty min/max dla JVM upoważnić do takiej kopii, a następnie ponownie uruchomić potoku.

![Ustawianie rozmiaru sterty JVM na samodzielnym podczerwieni](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Przykład: ustaw `_JAVA_OPTIONS` zmienną z wartością `-Xms256m -Xmx16g`. Flaga `Xms` określa początkową pulę alokacji pamięci dla maszyny `Xmx` wirtualnej Java (JVM), określając jednocześnie maksymalną pulę alokacji pamięci. Oznacza to, że JVM `Xms` zostanie uruchomiony z ilością pamięci i `Xmx` będzie mógł użyć maksymalnej ilości pamięci. Domyślnie ADF używać min 64MB i max 1G.

### <a name="data-type-mapping-for-parquet-files"></a>Mapowanie typu danych dla plików parkietu

| Tymczasowy typ danych fabryki danych | Typ prymitywny parkietu | Parquet Oryginalny typ (Deserialize) | Typ oryginalny parkietu (serializacja) |
|:--- |:--- |:--- |:--- |
| Wartość logiczna | Wartość logiczna | Nie dotyczy | Nie dotyczy |
| SByte | Int32 | Int8 (włas ie) | Int8 (włas ie) |
| Byte | Int32 | UInt8 (polski) | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| UInt16 | Int32 | UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64/Binarny | UInt64 | Wartość dziesiętna |
| Single | Liczba zmiennoprzecinkowa | Nie dotyczy | Nie dotyczy |
| Double | Double | Nie dotyczy | Nie dotyczy |
| Wartość dziesiętna | plików binarnych | Wartość dziesiętna | Wartość dziesiętna |
| Ciąg | plików binarnych | Utf8 (polski) | Utf8 (polski) |
| DateTime | Int96 ( int96 ) | Nie dotyczy | Nie dotyczy |
| przedział_czasu | Int96 ( int96 ) | Nie dotyczy | Nie dotyczy |
| Datetimeoffset | Int96 ( int96 ) | Nie dotyczy | Nie dotyczy |
| Bytearray | plików binarnych | Nie dotyczy | Nie dotyczy |
| Guid (identyfikator GUID) | plików binarnych | Utf8 (polski) | Utf8 (polski) |
| Char | plików binarnych | Utf8 (polski) | Utf8 (polski) |
| CharArray (CharArray) | Nieobsługiwane | Nie dotyczy | Nie dotyczy |

## <a name="orc-format-legacy"></a><a name="orc-format"></a>Format ORC (starsza wersja)

>[!NOTE]
>Poznaj nowy model z artykułu [w formacie ORC.](format-orc.md) Następujące konfiguracje zestawu danych magazynu danych opartego na plikach są nadal obsługiwane w stanie— jest to zgodne z wstecznym rozmiarem. Zaleca się, aby użyć nowego modelu w przyszłości.

Jeśli chcesz analizować pliki ORC lub zapisywać dane w formacie ORC, ustaw właściwość `format` `type` na wartość **OrcFormat**. Nie musisz określać żadnych właściwości w sekcji Format należącej do sekcji typeProperties. Przykład:

```json
"format":
{
    "type": "OrcFormat"
}
```

Pamiętaj o następujących kwestiach:

* Złożone typy danych nie są obsługiwane (STRUCT, MAP, LIST, UNION).
* Biały znak w nazwie kolumny nie jest obsługiwany.
* Plik ORC ma trzy [opcje związane z kompresją](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB, SNAPPY. Usługa Data Factory obsługuje odczyt danych z pliku ORC w dowolnym z tych skompresowanych formatów. Do odczytywania danych używa kodera-dekodera kompresji z metadanych. Podczas zapisywania w pliku ORC usługa Data Factory wybiera natomiast opcję ZLIB, która jest domyślna dla formatu ORC. Obecnie nie ma możliwości zastąpienia tego zachowania.

> [!IMPORTANT]
> W przypadku kopiowania wspieranego przez self-hosted Integration Runtime, na przykład między lokalnymi i chmurowymi magazynami danych, jeśli nie kopiujesz plików ORC **w stanie rzeczywistym,** musisz zainstalować **64-bitowy środowiska JRE 8 (Java Runtime Environment) lub OpenJDK** na komputerze podczerwieni. Zobacz poniższy akapit z więcej szczegółów.

W przypadku kopiowania uruchomionego na samodzielnym podczerweniu z serializacją/deserializacji plików ORC, *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* ADF lokalizuje środowisko wykonawcze Java, *`JAVA_HOME`* najpierw sprawdzając rejestr pod kątem środowiska JRE, jeśli nie znaleziono, po drugie sprawdzając zmienną systemową dla OpenJDK.

- **Aby użyć środowiska JRE:** 64-bitowa podczerwość wymaga 64-bitowego środowiska JRE. Można go znaleźć [tutaj](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Aby użyć OpenJDK**: jest obsługiwany od wersji IR 3.13. Pakiet jvm.dll ze wszystkimi innymi wymaganymi zestawami OpenJDK do samodzielnego hostowania komputera podczerwieni i odpowiednio ustawić zmienną środowiska systemu JAVA_HOME.

### <a name="data-type-mapping-for-orc-files"></a>Mapowanie typów danych dla plików ORC

| Tymczasowy typ danych fabryki danych | Typy ORC |
|:--- |:--- |
| Wartość logiczna | Wartość logiczna |
| SByte | Byte |
| Byte | Krótki |
| Int16 | Krótki |
| UInt16 | int |
| Int32 | int |
| UInt32 | Długi |
| Int64 | Długi |
| UInt64 | Ciąg |
| Single | Liczba zmiennoprzecinkowa |
| Double | Double |
| Wartość dziesiętna | Wartość dziesiętna |
| Ciąg | Ciąg |
| DateTime | Znacznik czasu |
| Datetimeoffset | Znacznik czasu |
| przedział_czasu | Znacznik czasu |
| Bytearray | plików binarnych |
| Guid (identyfikator GUID) | Ciąg |
| Char | Char(1) |

## <a name="avro-format-legacy"></a><a name="avro-format"></a>Format AVRO (starsza wersja)

>[!NOTE]
>Poznaj nowy model z artykułu [w formacie Avro.](format-avro.md) Następujące konfiguracje zestawu danych magazynu danych opartego na plikach są nadal obsługiwane w stanie— jest to zgodne z wstecznym rozmiarem. Zaleca się, aby użyć nowego modelu w przyszłości.

Jeśli chcesz analizować pliki Avro lub zapisywać dane w formacie Avro, ustaw właściwość `format` `type` na wartość **AvroFormat**. Nie musisz określać żadnych właściwości w sekcji Format należącej do sekcji typeProperties. Przykład:

```json
"format":
{
    "type": "AvroFormat",
}
```

Aby użyć formatu Avro w tabeli Hive, można zapoznać się z [samouczkiem Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Pamiętaj o następujących kwestiach:

* [Złożone typy danych](https://avro.apache.org/docs/current/spec.html#schema_complex) nie są obsługiwane (rekordy, wyliczenia, tablice, mapy, związki i stałe).

## <a name="compression-support-legacy"></a><a name="compression-support"></a>Obsługa kompresji (starsza wersja)

Usługa Azure Data Factory obsługuje dane kompresyjne/dekompresyjne podczas kopiowania. Po określeniu `compression` właściwości w wejściowym zestawie danych działanie kopiowania odczytuje skompresowane dane ze źródła i dekompresuje je; i po określeniu właściwości w wyjściowym zestawie danych, skompresować działanie kopiowania następnie zapisać dane do ujścia. Oto kilka przykładowych scenariuszy:

* Odczyt gzip skompresowane dane z obiektu blob platformy Azure, dekompresować go i zapisać dane wyników do bazy danych SQL platformy Azure. Definiujesz wejściowy zestaw danych obiektów `compression` `type` blob platformy Azure z właściwością jako GZIP.
* Odczytuj dane z pliku zwykłego tekstu z lokalnego systemu plików, skompresuj je przy użyciu formatu GZip i zapisz skompresowane dane w obiekcie blob platformy Azure. Definiujesz wyjściowy zestaw danych obiektów `compression` `type` blob platformy Azure z właściwością jako GZip.
* Odczyt pliku zip z serwera FTP, rozpakować go, aby uzyskać pliki wewnątrz i wylądować te pliki w usłudze Azure Data Lake Store. Definiujesz wejściowy zestaw danych `compression` `type` FTP z właściwością jako ZipDeflate.
* Odczytu danych skompresowanych przez GZIP z obiektu blob platformy Azure, dekompresuj go, skompresuj przy użyciu BZIP2 i zapisuj dane wyników w obiekcie azure. Definiujesz wejściowy zestaw danych `compression` `type` obiektów blob platformy Azure z `compression` `type` zestawem GZIP i wyjściowym zestawem danych z zestawem BZIP2.

Aby określić kompresję dla zestawu danych, należy użyć właściwości **kompresji** w zestawie danych JSON, jak w poniższym przykładzie:

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "fileName": "pagecounts.csv.gz",
            "folderPath": "compression/file/",
            "format": {
                "type": "TextFormat"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

Sekcja **kompresji** ma dwie właściwości:

* **Typ:** kodek kompresji, który może być **GZIP**, **Deflate**, **BZIP2**lub **ZipDeflate**. Uwaga Podczas korzystania z działania kopiowania do dekompresji plików ZipDeflate i zapisu do magazynu `<path specified in dataset>/<folder named as source zip file>/`danych ujścia opartego na plikach pliki zostaną wyodrębnione do folderu: .
* **Poziom:** stopień kompresji, który może być **optymalny** lub **najszybszy**.

  * **Najszybszy:** Operacja kompresji powinna zakończyć się tak szybko, jak to możliwe, nawet jeśli wynikowy plik nie jest optymalnie skompresowany.
  * **Optymalne**: Operacja kompresji powinna być optymalnie skompresowana, nawet jeśli operacja trwa dłużej.

    Aby uzyskać więcej informacji, zobacz temat [Poziom kompresji.](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx)

> [!NOTE]
> Ustawienia kompresji nie są obsługiwane dla danych w **avroformat**, **OrcFormat**lub **ParquetFormat**. Podczas odczytywania plików w tych formatach usługa Data Factory wykrywa i używa kodeka kompresji w metadanych. Podczas zapisywania do plików w tych formatach usługa Data Factory wybiera domyślny kodek kompresji dla tego formatu. Na przykład ZLIB dla OrcFormat i SNAPPY dla ParkietFormat.

## <a name="unsupported-file-types-and-compression-formats"></a>Nieobsługiwały się typy plików i formaty kompresji

Można użyć funkcji rozszerzalności usługi Azure Data Factory do przekształcania plików, które nie są obsługiwane.
Dwie opcje obejmują usługi Azure Functions i zadania niestandardowe przy użyciu usługi Azure Batch.

Możesz zobaczyć przykład, który używa funkcji platformy Azure, aby [wyodrębnić zawartość pliku tar](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction). Aby uzyskać więcej informacji, zobacz [Działanie usługi Azure Functions](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity).

Tę funkcję można również utworzyć przy użyciu niestandardowego działania dotnet. Więcej informacji można znaleźć [tutaj](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity)

## <a name="next-steps"></a>Następne kroki

Poznaj najnowsze obsługiwane formaty plików i kompresje z [obsługiwanych formatów plików i kompresji](supported-file-formats-and-compression-codecs.md).
