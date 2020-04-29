---
title: Format tekstu rozdzielanego w Azure Data Factory
description: W tym temacie opisano sposób postępowania z rozdzielonym formatem tekstu w Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: jingwang
ms.openlocfilehash: 696c175f55ecdb09b1b88f9766c4c678afe2c4ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417104"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Format tekstu rozdzielanego w Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Postępuj zgodnie z tym artykułem, jeśli chcesz **przeanalizować rozdzielane pliki tekstowe lub zapisać dane w formacie tekstu rozdzielanego**. 

Format tekstu rozdzielanego jest obsługiwany dla następujących łączników: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [system plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)i [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez rozdzielany zestaw danych tekstowych.

| Właściwość         | Opis                                                  | Wymagany |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Właściwość Type zestawu danych musi być ustawiona na wartość **DelimitedText**. | Tak      |
| location         | Ustawienia lokalizacji plików. Każdy Łącznik oparty na plikach ma własny typ lokalizacji i obsługiwane właściwości w sekcji `location`.  | Tak      |
| columnDelimiter  | Znaki używane do oddzielania kolumn w pliku. <br>Wartość domyślna to **przecinek `,` **. Gdy ogranicznik kolumny jest zdefiniowany jako pusty ciąg, co oznacza brak ogranicznika, cały wiersz jest traktowany jako jedna kolumna.<br>Obecnie ogranicznik kolumny jako pusty ciąg lub wiele znaków jest obsługiwany tylko na potrzeby mapowania przepływu danych, ale nie jest to działanie kopiowania.  | Nie       |
| rowDelimiter     | Pojedynczy znak lub "\r\n" używany do oddzielania wierszy w pliku. <br>Wartość domyślna to dowolna z następujących wartości **podczas odczytu: ["\r\n", "\r", "\n"]**, **"\n" lub "\r\n" przy zapisie** odpowiednio mapując przepływ danych i działanie kopiowania. <br>Gdy ogranicznik wiersza jest ustawiony na brak ogranicznika (pusty ciąg), ogranicznik kolumny musi być ustawiony jako brak ogranicznika (pusty ciąg), co oznacza, że cała zawartość jest traktowana jako pojedyncza wartość.<br>Obecnie ogranicznik wiersza jako pusty ciąg jest obsługiwany tylko w przypadku mapowania przepływu danych, ale bez działania kopiowania. | Nie       |
| quoteChar        | Pojedynczy znak do wartości kolumny QUOTE, jeśli zawiera ogranicznik kolumny. <br>Wartość domyślna to **podwójne cudzysłowy** `"`. <br>W przypadku mapowania przepływu danych `quoteChar` nie może być pustym ciągiem. <br>W przypadku działania kopiowania, `quoteChar` gdy jest zdefiniowany jako pusty ciąg, oznacza to, że nie istnieje znak cudzysłowu i wartość kolumny nie jest `escapeChar` ujęta w cudzysłów i jest używana do ucieczki ogranicznika kolumny i samego siebie. | Nie       |
| escapeChar       | Pojedynczy znak do cudzysłowu ucieczki wewnątrz wartości w cudzysłowie.<br>Wartość domyślna to **ukośnik odwrotny `\` **. <br>W przypadku mapowania przepływu danych `escapeChar` nie może być pustym ciągiem. <br/>W przypadku działania kopiowania, `escapeChar` gdy jest zdefiniowany jako pusty ciąg, `quoteChar` należy ustawić jako pusty ciąg, a w takim przypadku upewnij się, że wszystkie wartości kolumn nie zawierają ograniczników. | Nie       |
| firstRowAsHeader | Określa, czy pierwszy wiersz ma być traktowany jak wiersz nagłówka z nazwami kolumn.<br>Dozwolone wartości to **true** i **false** (wartość domyślna). | Nie       |
| nullValue        | Określa reprezentację ciągu wartości null. <br>Wartość domyślna to **pusty ciąg**. | Nie       |
| encodingName     | Typ kodowania używany do odczytu/zapisu plików testowych. <br>Dozwolone wartości są następujące: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>Przepływ danych mapowania notatek nie obsługuje kodowania UTF-7. | Nie       |
| compressionCodec | Koder-dekoder kompresji używany do odczytu/zapisu plików tekstowych. <br>Dozwolone wartości to **bzip2**, **gzip**, **Wklęśnięcie**, **ZipDeflate**, **przyciąganie**lub **lz4**. Wartość domyślna nie jest skompresowana. <br>Działanie kopiowania w **tej chwili nie** obsługuje "przyciągania" & "lz4", a przepływ danych mapowania nie obsługuje "ZipDeflate". <br>**Uwaga** w przypadku używania działania kopiowania do dekompresowania plików ZipDeflate i zapisywania w magazynie danych ujścia opartych na plikach pliki zostaną wyodrębnione do folderu: `<path specified in dataset>/<folder named as source zip file>/`. | Nie       |
| compressionLevel | Współczynnik kompresji. <br>Dozwolone wartości to **optymalne** lub **najszybszy**.<br>- **Najszybsze:** Operacja kompresji powinna zostać ukończona tak szybko, jak to możliwe, nawet jeśli plik nie jest optymalnie kompresowany.<br>- **Optymalnie**: operacja kompresji powinna być optymalnie skompresowana, nawet jeśli operacja trwa dłużej. Aby uzyskać więcej informacji, zobacz temat [poziom kompresji](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Nie       |

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

W sekcji *** \*źródło\* *** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość       | Opis                                                  | Wymagany |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | Właściwość Type źródła działania Copy musi być ustawiona na wartość **DelimitedTextSource**. | Tak      |
| formatSettings | Grupa właściwości. Zapoznaj się z rozdzieloną tabelą **Ustawienia odczytu tekstu** poniżej. | Nie       |
| storeSettings  | Grupa właściwości do odczytywania danych z magazynu danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia odczytu w obszarze `storeSettings`. | Nie       |

Obsługiwane **rozdzielane tekstem ustawienia odczytu** w `formatSettings`obszarze:

| Właściwość      | Opis                                                  | Wymagany |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Typ formatSettings musi być ustawiony na **DelimitedTextReadSettings**. | Tak      |
| skipLineCount | Wskazuje liczbę **niepustych** wierszy do pominięcia podczas odczytywania danych z plików wejściowych. <br>Jeśli określono zarówno właściwość skipLineCount, jak i firstRowAsHeader, najpierw zostaną pominięte wiersze, a następnie zostaną odczytane informacje nagłówka z pliku wejściowego. | Nie       |

### <a name="delimited-text-as-sink"></a>Tekst rozdzielony jako ujścia

W sekcji *** \*ujścia\* *** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość       | Opis                                                  | Wymagany |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | Właściwość Type źródła działania Copy musi być ustawiona na wartość **DelimitedTextSink**. | Tak      |
| formatSettings | Grupa właściwości. Zapoznaj się z rozdzieloną tabelą **ustawień zapisu tekstu** poniżej. |          |
| storeSettings  | Grupa właściwości do zapisywania danych w magazynie danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia zapisu w obszarze `storeSettings`.  | Nie       |

Obsługiwane **rozdzielone ustawienia zapisu tekstu** w `formatSettings`obszarze:

| Właściwość      | Opis                                                  | Wymagany                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Typ formatSettings musi być ustawiony na **DelimitedTextWriteSettings**. | Tak                                                   |
| fileExtension | Rozszerzenie pliku używane do nazwy plików wyjściowych, np `.csv` `.txt`.. Musi być określony, `fileName` Jeśli nie jest określony w wyjściowym zestawie danych DelimitedText. Jeśli nazwa pliku jest skonfigurowana w wyjściowym zestawie danych, zostanie użyta jako nazwa pliku ujścia i ustawienie rozszerzenia pliku zostanie zignorowane.  | Tak, jeśli nazwa pliku nie jest określona w wyjściowym zestawie danych |

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Dowiedz się więcej o [przekształceniu źródłowym](data-flow-source.md) i [transformacji ujścia](data-flow-sink.md) w mapowaniu przepływu danych.

## <a name="next-steps"></a>Następne kroki

- [Przegląd działania kopiowania](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
