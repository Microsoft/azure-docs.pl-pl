---
title: Rozdzielony format tekstu w usłudze Azure Data Factory
description: W tym temacie opisano sposób postępowania z formatem tekstu rozdzielanych w usłudze Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: jingwang
ms.openlocfilehash: 696c175f55ecdb09b1b88f9766c4c678afe2c4ec
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417104"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Rozdzielony format tekstu w usłudze Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ten artykuł należy wykonać, aby **przeanalizować rozdzielone pliki tekstowe lub zapisać dane w rozdzielanym formacie tekstowym**. 

Rozdzielany format tekstu jest obsługiwany dla następujących łączników: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), System [plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)i [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Zestawy danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez rozdzielony zestaw danych tekstowych.

| Właściwość         | Opis                                                  | Wymagany |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Właściwość typu zestawu danych musi być ustawiona na **Tekst Delimited**. | Tak      |
| location         | Ustawienia lokalizacji plików. Każdy łącznik oparty na plikach ma swój własny `location`typ lokalizacji i obsługiwane właściwości w obszarze .  | Tak      |
| columnDelimiter  | Znaki używane do oddzielania kolumn w pliku. <br>Wartością domyślną jest **przecinek `,` **. Gdy ogranicznik kolumny jest zdefiniowany jako pusty ciąg, co oznacza brak ogranicznika, cała linia jest traktowana jako pojedyncza kolumna.<br>Obecnie ogranicznik kolumny jako pusty ciąg lub multi-char jest obsługiwany tylko dla mapowania przepływu danych, ale nie kopiuj działania.  | Nie       |
| rowDelimiter     | Pojedynczy znak lub "\r\n" używany do oddzielania wierszy w pliku. <br>Wartością domyślną jest dowolna z następujących wartości **odczytu: ["\r\n", "\r", "\n"]** i **"\n" lub "\r\n" przy zapisie** przez mapowanie przepływu danych i działania kopiowania. <br>Gdy ogranicznik wiersza jest ustawiony na brak ogranicznika (pusty ciąg), ogranicznik kolumny musi być ustawiony jako nie ogranicznik (pusty ciąg), co oznacza, że cała zawartość jest traktowana jako pojedyncza wartość.<br>Obecnie ogranicznik wiersza jako pusty ciąg jest obsługiwany tylko dla mapowania przepływu danych, ale nie kopiuj działania. | Nie       |
| quoteChar        | Pojedynczy znak do cytowania wartości kolumny, jeśli zawiera ogranicznik kolumny. <br>Wartością domyślną jest **podwójne cudzysłowy** `"`. <br>W przypadku mapowania przepływu danych `quoteChar` nie może być pustym ciągiem. <br>W przypadku działania `quoteChar` Kopiowanie, gdy jest zdefiniowany jako pusty ciąg, oznacza to, że nie ma znaku cudzysłowu, a wartość kolumny nie jest cytowana i `escapeChar` jest używana do ucieczki od ogranicznika kolumny i siebie. | Nie       |
| escapeChar       | Pojedynczy znak do ucieczki cudzysłowów wewnątrz wartości cytowanej.<br>Wartością domyślną jest ** `\`ukośnik odwrotny **. <br>W przypadku mapowania przepływu danych `escapeChar` nie może być pustym ciągiem. <br/>W przypadku działania `escapeChar` Kopiowania, gdy `quoteChar` jest zdefiniowany jako pusty ciąg, musi być ustawiona jako pusty ciąg, jak również, w którym to przypadku upewnij się, że wszystkie wartości kolumn nie zawierają ograniczników. | Nie       |
| firstRowAsHeader | Określa, czy pierwszy wiersz ma być traktowany/tworzyć jako wiersz nagłówka z nazwami kolumn.<br>Dozwolone wartości są **prawdziwe** i **fałszywe** (domyślnie). | Nie       |
| nullValue        | Określa reprezentację ciągu wartości null. <br>Wartością domyślną jest **pusty ciąg**. | Nie       |
| encodingName     | Typ kodowania używany do odczytu/zapisu plików testowych. <br>Allowed values are as follows: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>Przepływ danych mapowania notatki nie obsługuje kodowania UTF-7. | Nie       |
| compressionCodec (kod kodu kompresji) | Koder-dekodusz kompresji używany do odczytu/zapisu plików tekstowych. <br>Dozwolone wartości to **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **snappy**lub **lz4**. Wartość domyślna nie jest skompresowana. <br>**Uwaga** obecnie Copy activity nie obsługuje "snappy" & "lz4", a mapowanie przepływu danych nie obsługuje "ZipDeflate". <br>**Uwaga** Podczas korzystania z działania kopiowania do dekompresji plików ZipDeflate i zapisu do magazynu `<path specified in dataset>/<folder named as source zip file>/`danych ujścia opartego na plikach pliki zostaną wyodrębnione do folderu: . | Nie       |
| Compressionlevel | Stopień kompresji. <br>Dozwolone wartości są **optymalne** lub **najszybsze**.<br>- **Najszybszy:** Operacja kompresji powinna zakończyć się tak szybko, jak to możliwe, nawet jeśli wynikowy plik nie jest optymalnie skompresowany.<br>- **Optymalne**: Operacja kompresji powinna być optymalnie skompresowana, nawet jeśli operacja trwa dłużej. Aby uzyskać więcej informacji, zobacz temat [Poziom kompresji.](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) | Nie       |

Poniżej znajduje się przykład rozdzielanego tekstowego zestawu danych w usłudze Azure Blob Storage:

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez rozdzielone źródło tekstu i ujście.

### <a name="delimited-text-as-source"></a>Rozdzielony tekst jako źródło 

Następujące właściwości są obsługiwane w sekcji *** \*\* źródła*** działania kopiowania.

| Właściwość       | Opis                                                  | Wymagany |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | Właściwość typu źródła działania kopiowania musi być ustawiona na **DelimitedTextSource**. | Tak      |
| formatYStawienia | Grupa właściwości. Poniżej znajduje się **tabela ustawień odczytu tekstu rozdzielanych.** | Nie       |
| sklepyWystawy  | Grupa właściwości dotyczące sposobu odczytywania danych z magazynu danych. Każdy łącznik oparty na plikach ma `storeSettings`własne obsługiwane ustawienia odczytu w obszarze . | Nie       |

Obsługiwane **ustawienia odczytu tekstu rozdzielanych** w obszarze: `formatSettings`

| Właściwość      | Opis                                                  | Wymagany |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Typ formatuStawienia muszą być ustawione na **DelimitedTextReadSettings**. | Tak      |
| skipLineCount | Wskazuje liczbę **niepustych** wierszy do pominięcia podczas odczytywania danych z plików wejściowych. <br>Jeśli określono zarówno właściwość skipLineCount, jak i firstRowAsHeader, najpierw zostaną pominięte wiersze, a następnie zostaną odczytane informacje nagłówka z pliku wejściowego. | Nie       |

### <a name="delimited-text-as-sink"></a>Rozdzielony tekst jako zlew

Następujące właściwości są obsługiwane w sekcji *** \*ujście działania kopiowania.\* ***

| Właściwość       | Opis                                                  | Wymagany |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | Właściwość typu źródła działania kopiowania musi być ustawiona na **DelimitedTextSink**. | Tak      |
| formatYStawienia | Grupa właściwości. Poniżej znajduje się **tabela ustawień zapisu tekstu rozdzielanych.** |          |
| sklepyWystawy  | Grupa właściwości dotyczące sposobu zapisywania danych w magazynie danych. Każdy łącznik oparty na plikach ma `storeSettings`własne obsługiwane ustawienia zapisu w obszarze .  | Nie       |

Obsługiwane **ustawienia zapisu tekstu rozdzielanych** w obszarze: `formatSettings`

| Właściwość      | Opis                                                  | Wymagany                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Typ formatuStawienia muszą być ustawione na **DelimitedTextWriteSettings**. | Tak                                                   |
| plikWybudka | Rozszerzenie pliku używane do nazwania plików wyjściowych, `.csv` `.txt`np. Należy określić, `fileName` gdy nie jest określony w wyjściowym zestawie danych DelimitedText. Gdy nazwa pliku jest skonfigurowana w wyjściowym zestawie danych, będzie używana jako nazwa pliku ujścia, a ustawienie rozszerzenia pliku zostanie zignorowane.  | Tak, gdy nazwa pliku nie jest określona w wyjściowym zestawie danych |

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Dowiedz się szczegółów z [transformacji źródła](data-flow-source.md) i [transformacji ujścia](data-flow-sink.md) w przepływie danych mapowania.

## <a name="next-steps"></a>Następne kroki

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
