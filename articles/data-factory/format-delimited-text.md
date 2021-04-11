---
title: Format tekstu rozdzielanego w Azure Data Factory
description: W tym temacie opisano sposób postępowania z rozdzielonym formatem tekstu w Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: jingwang
ms.openlocfilehash: 1cff49e4ddb7423e1e5956e1436cefd18abe6dfe
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107078"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Format tekstu rozdzielanego w Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Postępuj zgodnie z tym artykułem, jeśli chcesz **przeanalizować rozdzielane pliki tekstowe lub zapisać dane w formacie tekstu rozdzielanego**. 

Format tekstu rozdzielanego jest obsługiwany dla następujących łączników: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [system plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)i [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez rozdzielany zestaw danych tekstowych.

| Właściwość         | Opis                                                  | Wymagane |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Właściwość Type zestawu danych musi być ustawiona na wartość **DelimitedText**. | Tak      |
| location         | Ustawienia lokalizacji plików. Każdy Łącznik oparty na plikach ma własny typ lokalizacji i obsługiwane właściwości w sekcji `location` .  | Tak      |
| columnDelimiter  | Znaki używane do oddzielania kolumn w pliku. <br>Wartość domyślna to **przecinek `,`**. Gdy ogranicznik kolumny jest zdefiniowany jako pusty ciąg, co oznacza brak ogranicznika, cały wiersz jest traktowany jako jedna kolumna.<br>Obecnie ogranicznik kolumny jako pusty ciąg lub wiele znaków jest obsługiwany tylko na potrzeby mapowania przepływu danych, ale nie jest to działanie kopiowania.  | Nie       |
| rowDelimiter     | Pojedynczy znak lub "\r\n" używany do oddzielania wierszy w pliku. <br>Wartość domyślna to dowolna z następujących wartości **podczas odczytu: ["\r\n", "\r", "\n"]**, **"\n" lub "\r\n" przy zapisie** odpowiednio mapując przepływ danych i działanie kopiowania. <br>Gdy ogranicznik wiersza jest ustawiony na brak ogranicznika (pusty ciąg), ogranicznik kolumny musi być ustawiony jako brak ogranicznika (pusty ciąg), co oznacza, że cała zawartość jest traktowana jako pojedyncza wartość.<br>Obecnie ogranicznik wiersza jako pusty ciąg jest obsługiwany tylko w przypadku mapowania przepływu danych, ale bez działania kopiowania. | Nie       |
| quoteChar        | Pojedynczy znak do wartości kolumny QUOTE, jeśli zawiera ogranicznik kolumny. <br>Wartość domyślna to **podwójne cudzysłowy** `"` . <br>Gdy `quoteChar` jest zdefiniowany jako pusty ciąg, oznacza to, że nie istnieje znak cudzysłowu, a wartość kolumny nie jest ujęta w cudzysłów i `escapeChar` jest używana do ucieczki ogranicznika kolumny i samego siebie. | Nie       |
| escapeChar       | Pojedynczy znak do cudzysłowu ucieczki wewnątrz wartości w cudzysłowie.<br>Wartość domyślna to **ukośnik odwrotny `\`**. <br>Gdy `escapeChar` jest zdefiniowany jako pusty ciąg, `quoteChar` należy ustawić jako pusty ciąg, a w takim przypadku upewnij się, że wszystkie wartości kolumn nie zawierają ograniczników. | Nie       |
| firstRowAsHeader | Określa, czy pierwszy wiersz ma być traktowany jak wiersz nagłówka z nazwami kolumn.<br>Dozwolone wartości to **true** i **false** (wartość domyślna).<br>Gdy pierwszy wiersz jako nagłówek ma wartość false, Uwaga: Podgląd danych interfejsu użytkownika i wyszukiwanie działania wyszukiwania automatyczne generowanie nazw kolumn jako Prop_ {n} (począwszy od 0), działanie kopiowania wymaga [jawnego mapowania](copy-activity-schema-and-type-mapping.md#explicit-mapping) ze źródła do ujścia i lokalizuje kolumny według liczby porządkowej (rozpoczynając od 1), a następnie mapuje kolumny o nazwie jako Column_ {n} (rozpoczynając od 1).  | Nie       |
| nullValue        | Określa reprezentację ciągu wartości null. <br>Wartość domyślna to **pusty ciąg**. | Nie       |
| encodingName     | Typ kodowania używany do odczytu/zapisu plików testowych. <br>Dozwolone wartości są następujące: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>Przepływ danych mapowania notatek nie obsługuje kodowania UTF-7. | Nie       |
| compressionCodec | Koder-dekoder kompresji używany do odczytu/zapisu plików tekstowych. <br>Dozwolone wartości to **bzip2**, **gzip**, **Wklęśnięcie**, **ZipDeflate**, **TarGzip**, **tar**, **przyciągania** lub **lz4**. Wartość domyślna nie jest skompresowana. <br>Działanie **kopiowania obecnie nie** obsługuje "przyciągania" & "lz4", a przepływ danych mapowania nie obsługuje "ZipDeflate", "TarGzip" i "tar". <br>**Uwaga** w przypadku używania działania kopiowania w celu dekompresowania **ZipDeflate** / **TarGzip** / plików **tar** i zapisu w magazynie danych obiektów ujścia opartych na plikach, domyślnie pliki są wyodrębniane do folderu: `<path specified in dataset>/<folder named as source compressed file>/` , użyj `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [źródła działania Copy](#delimited-text-as-source) , aby określić, czy należy zachować nazwę skompresowanych plików jako strukturę folderów. | Nie       |
| compressionLevel | Współczynnik kompresji. <br>Dozwolone wartości to **optymalne** lub **najszybszy**.<br>- **Najszybsze:** Operacja kompresji powinna zostać ukończona tak szybko, jak to możliwe, nawet jeśli plik nie jest optymalnie kompresowany.<br>- **Optymalnie**: operacja kompresji powinna być optymalnie skompresowana, nawet jeśli operacja trwa dłużej. Aby uzyskać więcej informacji, zobacz temat [poziom kompresji](/dotnet/api/system.io.compression.compressionlevel) . | Nie       |

Poniżej znajduje się przykład rozdzielanego zestawu danych tekstowych w usłudze Azure Blob Storage:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "escapeChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości, które są obsługiwane przez rozdzielane Źródło tekstu i ujścia.

### <a name="delimited-text-as-source"></a>Rozdzielany tekst jako źródło 

W sekcji ***\* Źródło \**** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość       | Opis                                                  | Wymagane |
| -------------- | ------------------------------------------------------------ | -------- |
| typ           | Właściwość Type źródła działania Copy musi być ustawiona na wartość **DelimitedTextSource**. | Tak      |
| formatSettings | Grupa właściwości. Zapoznaj się z rozdzieloną tabelą **Ustawienia odczytu tekstu** poniżej. |  Nie       |
| storeSettings  | Grupa właściwości do odczytywania danych z magazynu danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia odczytu w obszarze `storeSettings` . | Nie       |

Obsługiwane **rozdzielane tekstem ustawienia odczytu** w obszarze `formatSettings` :

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ formatSettings musi być ustawiony na **DelimitedTextReadSettings**. | Tak      |
| skipLineCount | Wskazuje liczbę **niepustych** wierszy do pominięcia podczas odczytywania danych z plików wejściowych. <br>Jeśli określono zarówno właściwość skipLineCount, jak i firstRowAsHeader, najpierw zostaną pominięte wiersze, a następnie zostaną odczytane informacje nagłówka z pliku wejściowego. | Nie       |
| compressionProperties | Grupa właściwości na temat sposobu dekompresowania danych dla danego kodera kompresji. | Nie       |
| preserveZipFileNameAsFolder<br>(*w obszarze `compressionProperties` -> `type` jako `ZipDeflateReadSettings`*) |  Stosuje się, gdy zestaw danych wejściowych jest skonfigurowany z kompresją **ZipDeflate** . Wskazuje, czy podczas kopiowania zachować nazwę źródłowego pliku zip jako strukturę folderów.<br>-Po ustawieniu na **wartość true (domyślnie)** Data Factory zapisuje niespakowane pliki do `<path specified in dataset>/<folder named as source zip file>/` .<br>-Po ustawieniu na **wartość false** Data Factory zapisuje niespakowane pliki bezpośrednio do `<path specified in dataset>` . Upewnij się, że nie masz zduplikowanych nazw plików w różnych źródłowych plikach ZIP, aby uniknąć wyścigów lub nieoczekiwanych zachowań.  | Nie |
| preserveCompressionFileNameAsFolder<br>(*w obszarze `compressionProperties` -> `type` jako `TarGZipReadSettings` lub `TarReadSettings`*)  | Stosuje się, gdy zestaw danych wejściowych jest skonfigurowany z / kompresją **pułapki** TarGzip. Wskazuje, czy podczas kopiowania zachować źródłową nazwę pliku skompresowanego jako strukturę folderów.<br>-Po ustawieniu na **wartość true (domyślnie)** Data Factory zapisuje zdekompresować pliki do `<path specified in dataset>/<folder named as source compressed file>/` . <br>-Po ustawieniu na **wartość false** Data Factory zapisuje dekompresowane pliki bezpośrednio do `<path specified in dataset>` . Upewnij się, że nie masz zduplikowanych nazw plików w różnych plikach źródłowych, aby uniknąć wyścigów lub nieoczekiwanych zachowań. | Nie |

```json
"activities": [
    {
        "name": "CopyFromDelimitedText",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                },
                "formatSettings": {
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 3,
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="delimited-text-as-sink"></a>Tekst rozdzielony jako ujścia

W sekcji ***\* ujścia \**** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość       | Opis                                                  | Wymagane |
| -------------- | ------------------------------------------------------------ | -------- |
| typ           | Właściwość Type źródła działania Copy musi być ustawiona na wartość **DelimitedTextSink**. | Tak      |
| formatSettings | Grupa właściwości. Zapoznaj się z rozdzieloną tabelą **ustawień zapisu tekstu** poniżej. |    Nie      |
| storeSettings  | Grupa właściwości do zapisywania danych w magazynie danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia zapisu w obszarze `storeSettings` .  | Nie       |

Obsługiwane **rozdzielone ustawienia zapisu tekstu** w obszarze `formatSettings` :

| Właściwość      | Opis                                                  | Wymagane                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| typ          | Typ formatSettings musi być ustawiony na **DelimitedTextWriteSettings**. | Tak                                                   |
| fileExtension | Rozszerzenie pliku używane do nazwy plików wyjściowych, na przykład `.csv` , `.txt` . Musi być określony, jeśli `fileName` nie jest określony w wyjściowym zestawie danych DelimitedText. Jeśli nazwa pliku jest skonfigurowana w wyjściowym zestawie danych, zostanie użyta jako nazwa pliku ujścia i ustawienie rozszerzenia pliku zostanie zignorowane.  | Tak, jeśli nazwa pliku nie jest określona w wyjściowym zestawie danych |
| maxRowsPerFile | Podczas zapisywania danych w folderze można wybrać opcję zapisu w wielu plikach i określić maksymalną liczbę wierszy na plik.  | Nie |
| fileNamePrefix | Ma zastosowanie, gdy `maxRowsPerFile` jest skonfigurowany.<br> Określ prefiks nazwy pliku podczas zapisywania danych do wielu plików, co spowodowało następujący wzorzec: `<fileNamePrefix>_00000.<fileExtension>` . Jeśli nie zostanie określony, prefiks nazwy pliku zostanie wygenerowany automatycznie. Ta właściwość nie ma zastosowania, gdy źródło jest magazynem opartym na plikach lub [z magazynem danych z włączoną opcją partycji](copy-activity-performance-features.md).  | Nie |

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

W mapowaniu przepływów danych można odczytywać i zapisywać w rozdzielonym formacie tekstowym w następujących magazynach danych: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)i [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Właściwości źródła

Poniższa tabela zawiera listę właściwości obsługiwanych przez rozdzielane Źródło tekstu. Można edytować te właściwości na karcie **Opcje źródła** .

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Ścieżki symboli wieloznacznych | Wszystkie pliki zgodne ze ścieżką wieloznaczną zostaną przetworzone. Zastępuje folder i ścieżkę pliku ustawioną w zestawie danych. | nie | Ciąg [] | wildcardPaths |
| Ścieżka katalogu głównego partycji | W przypadku danych plików podzielonych na partycje można wprowadzić ścieżkę katalogu głównego partycji, aby odczytywać foldery partycjonowane jako kolumny | nie | Ciąg | partitionRootPath |
| Lista plików | Czy źródło wskazuje plik tekstowy, który zawiera listę plików do przetworzenia | nie | `true` lub `false` | fileList |
| Wiersze wielowierszowe | Czy plik źródłowy zawiera wiersze, które rozciągają się na wiele wierszy. Wartości wielowierszowe muszą być w cudzysłowach. | nie `true` lub `false` | multiLineRow |
| Kolumna do przechowywania nazwy pliku | Utwórz nową kolumnę o nazwie i ścieżce pliku źródłowego | nie | Ciąg | rowUrlColumn |
| Po zakończeniu | Usuń lub Przenieś pliki po przetworzeniu. Ścieżka pliku zaczyna się od katalogu głównego kontenera | nie | Usuń: `true` lub `false` <br> Przenieś `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Filtruj według ostatniej modyfikacji | Wybierz filtrowanie plików na podstawie czasu ich ostatniej modyfikacji | nie | Znacznik czasu | modifiedAfter <br> modifiedBefore |
| Nie znaleziono plików | W przypadku wartości true błąd nie jest zgłaszany, jeśli nie znaleziono plików | nie | `true` lub `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Przykład źródła

Poniżej przedstawiono przykład konfiguracji rozdzielonego źródła tekstu w mapowaniu przepływów danych.

![Źródło DelimitedText](media/data-flow/delimited-text-source.png)

Skojarzony skrypt przepływu danych:

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    multiLineRow: true,
    wildcardPaths:['*.csv']) ~> CSVSource
```

> [!NOTE]
> Źródła przepływu danych obsługują ograniczoną liczbę obsługi symboli wieloznacznych systemu Linux, które są obsługiwane przez systemy plików Hadoop

### <a name="sink-properties"></a>Właściwości ujścia

Poniższa tabela zawiera listę właściwości obsługiwanych przez rozdzieloną zlewkę tekstu. Te właściwości można edytować na karcie **Ustawienia** .

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Wyczyść folder | Jeśli folder docelowy został wyczyszczony przed zapisem | nie | `true` lub `false` | obciąć |
| Opcja nazwy pliku | Format nazewnictwa zapisanych danych. Domyślnie jeden plik na partycję w formacie `part-#####-tid-<guid>` | nie | Wzorzec: ciąg <br> Na partycję: String [] <br> Nazwij plik jako dane kolumny: ciąg <br> Dane wyjściowe do pojedynczego pliku: `['<fileName>']` <br> Nazwa folderu jako dane kolumny: ciąg | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames <br> rowFolderUrlColumn |
| Wszystkie oferty | Ujmij wszystkie wartości w cudzysłowy | nie | `true` lub `false` | quoteAll |
| Nagłówek | Dodawanie nagłówków klientów do plików wyjściowych | nie | `[<string array>]` | header |

### <a name="sink-example"></a>Przykład ujścia

Poniższy obraz to przykład konfiguracji rozdzielonego ujścia tekstu w mapowaniu przepływów danych.

![Ujścia DelimitedText](media/data-flow/delimited-text-sink.png)

Skojarzony skrypt przepływu danych:

```
CSVSource sink(allowSchemaDrift: true,
    validateSchema: false,
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CSVSink
```

## <a name="next-steps"></a>Następne kroki

- [Przegląd działania kopiowania](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
