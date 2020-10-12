---
title: Format programu Excel w Azure Data Factory
description: W tym temacie opisano sposób postępowania z formatem programu Excel w Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: jingwang
ms.openlocfilehash: dad1f9f232cb9d713af81f6aea57a4ffe651da19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331968"
---
# <a name="excel-format-in-azure-data-factory"></a>Format programu Excel w Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Postępuj zgodnie z tym artykułem, jeśli chcesz **przeanalizować pliki programu Excel**. Azure Data Factory obsługuje zarówno "xls", jak i "xlsx".

Format programu Excel jest obsługiwany dla następujących łączników [: Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [system plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)i [SFTP](connector-sftp.md). Jest ona obsługiwana jako źródło, ale nie jako ujścia.

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych programu Excel.

| Właściwość         | Opis                                                  | Wymagane |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Właściwość Type zestawu danych musi być ustawiona na wartość **Excel**.   | Tak      |
| location         | Ustawienia lokalizacji plików. Każdy Łącznik oparty na plikach ma własny typ lokalizacji i obsługiwane właściwości w sekcji `location` . | Tak      |
| sheetName        | Nazwa arkusza programu Excel, w którym mają zostać odczytane dane.                       | Tak      |
| range            | Zakres komórek w danym arkuszu do lokalizowania danych selektywnych, na przykład:<br>-Nie określono: odczytuje cały arkusz jako tabelę z pierwszego niepustego wiersza i kolumny<br>- `A3`: odczytuje tabelę rozpoczynającą się od danej komórki, dynamicznie wykrywa wszystkie wiersze poniżej i wszystkie kolumny w prawo<br>- `A3:H5`: odczytuje ten stały zakres jako tabelę<br>- `A3:A3`: odczytuje tę pojedynczą komórkę | Nie       |
| firstRowAsHeader | Określa, czy pierwszy wiersz danego arkusza/zakresu ma być traktowany jak wiersz nagłówka z nazwami kolumn.<br>Dozwolone wartości to **true** i **false** (wartość domyślna). | Nie       |
| nullValue        | Określa reprezentację ciągu wartości null. <br>Wartość domyślna to **pusty ciąg**. | Nie       |
| kompresja | Grupa właściwości do konfigurowania kompresji plików. Skonfiguruj tę sekcję, jeśli chcesz przeprowadzić kompresję/dekompresowanie podczas wykonywania działania. | Nie |
| typ<br/>(*w `compression` obszarze *) | Koder-dekoder kompresji używany do odczytu/zapisu plików JSON. <br>Dozwolone wartości to **bzip2**, **gzip**, **Wklęśnięcie**, **ZipDeflate**, **TarGzip**, **przyciąganie**lub **lz4**. Wartość domyślna nie jest skompresowana.<br>Działanie kopiowania w **tej chwili nie** obsługuje "przyciągania" & "lz4", a przepływ danych mapowania nie obsługuje "ZipDeflate".<br>**Uwaga** w przypadku używania działania kopiowania do dekompresowania plików **ZipDeflate** i zapisywania w magazynie danych ujścia opartych na plikach pliki są wyodrębniane do folderu: `<path specified in dataset>/<folder named as source zip file>/` . | Nie.  |
| poziom<br/>(*w `compression` obszarze *) | Współczynnik kompresji. <br>Dozwolone wartości to **optymalne** lub **najszybszy**.<br>- **Najszybsze:** Operacja kompresji powinna zostać ukończona tak szybko, jak to możliwe, nawet jeśli plik nie jest optymalnie kompresowany.<br>- **Optymalnie**: operacja kompresji powinna być optymalnie skompresowana, nawet jeśli operacja trwa dłużej. Aby uzyskać więcej informacji, zobacz temat [poziom kompresji](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Nie       |

Poniżej znajduje się przykład zestawu danych programu Excel na platformie Azure Blob Storage:

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
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
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło programu Excel.

### <a name="excel-as-source"></a>Excel jako źródło 

W sekcji *** \* Źródło \* *** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **ExcelSource**. | Tak      |
| storeSettings | Grupa właściwości do odczytywania danych z magazynu danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia odczytu w obszarze `storeSettings` . | Nie       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

W mapowaniu przepływów danych można odczytać format programu Excel w następujących magazynach danych: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)i [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties). Możesz wskazać pliki programu Excel przy użyciu zestawu danych programu Excel lub z użyciem [wbudowanego zestawu danych](data-flow-source.md#inline-datasets).

### <a name="source-properties"></a>Właściwości źródła

Poniższa tabela zawiera listę właściwości obsługiwanych przez źródło programu Excel. Można edytować te właściwości na karcie **Opcje źródła** . W przypadku korzystania z wbudowanego zestawu danych zostaną wyświetlone dodatkowe ustawienia plików, które są takie same jak właściwości opisane w sekcji [Właściwości zestawu danych](#dataset-properties) .

| Nazwa                      | Opis                                                  | Wymagane | Dozwolone wartości                                            | Właściwość skryptu przepływu danych         |
| ------------------------- | ------------------------------------------------------------ | -------- | --------------------------------------------------------- | --------------------------------- |
| Ścieżki symboli wieloznacznych           | Wszystkie pliki zgodne ze ścieżką wieloznaczną zostaną przetworzone. Zastępuje folder i ścieżkę pliku ustawioną w zestawie danych. | nie       | Ciąg []                                                  | wildcardPaths                     |
| Ścieżka katalogu głównego partycji       | W przypadku danych plików podzielonych na partycje można wprowadzić ścieżkę katalogu głównego partycji, aby odczytywać foldery partycjonowane jako kolumny | nie       | Ciąg                                                    | partitionRootPath                 |
| Lista plików             | Czy źródło wskazuje plik tekstowy, który zawiera listę plików do przetworzenia | nie       | `true` lub `false`                                         | fileList                          |
| Kolumna do przechowywania nazwy pliku | Utwórz nową kolumnę o nazwie i ścieżce pliku źródłowego       | nie       | Ciąg                                                    | rowUrlColumn                      |
| Po zakończeniu          | Usuń lub Przenieś pliki po przetworzeniu. Ścieżka pliku zaczyna się od katalogu głównego kontenera | nie       | Usuń: `true` lub `false` <br> Przenieś `['<from>', '<to>']` | purgeFiles <br> moveFiles         |
| Filtruj według ostatniej modyfikacji   | Wybierz filtrowanie plików na podstawie czasu ich ostatniej modyfikacji | nie       | Timestamp                                                 | modifiedAfter <br> modifiedBefore |
| Nie znaleziono plików | W przypadku wartości true błąd nie jest zgłaszany, jeśli nie znaleziono plików | nie | `true` lub `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Przykład źródła

Poniżej znajduje się przykład konfiguracji źródła programu Excel w mapowaniu przepływów danych przy użyciu trybu zestawu danych.

![Źródło programu Excel](media/data-flow/excel-source.png)

Skojarzony skrypt przepływu danych:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    wildcardPaths:['*.xls']) ~> ExcelSource
```

Jeśli używasz wbudowanego zestawu danych, zobaczysz następujące opcje źródła w mapowaniu przepływu danych.

![Wbudowany zestaw danych programu Excel](media/data-flow/excel-source-inline-dataset.png)

Skojarzony skrypt przepływu danych:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'excel',
    fileSystem: 'container',
    folderPath: 'path',
    fileName: 'sample.xls',
    sheetName: 'worksheet',
    firstRowAsHeader: true) ~> ExcelSourceInlineDataset
```

## <a name="next-steps"></a>Następne kroki

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
