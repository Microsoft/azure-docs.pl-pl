---
title: Format Avro w Azure Data Factory
description: W tym temacie opisano sposób postępowania z formatem Avro w Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: 7d61121b4c80b7b89ec29ade4ab1bfab91a660d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334348"
---
# <a name="avro-format-in-azure-data-factory"></a>Format Avro w Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Postępuj zgodnie z tym artykułem, jeśli chcesz **analizować pliki Avro lub zapisywać dane w formacie Avro**. 

Format Avro jest obsługiwany dla następujących łączników: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [system plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)i [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Avro.

| Właściwość         | Opis                                                  | Wymagane |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Właściwość Type zestawu danych musi być ustawiona na wartość **Avro**. | Tak      |
| location         | Ustawienia lokalizacji plików. Każdy Łącznik oparty na plikach ma własny typ lokalizacji i obsługiwane właściwości w sekcji `location` . **Zobacz szczegóły w sekcji łącznik — > właściwości zestawu danych**. | Tak      |
| avroCompressionCodec | Koder-dekoder kompresji do użycia podczas zapisywania w plikach Avro. Podczas odczytywania z plików Avro Data Factory automatycznie określa koder-dekoder kompresji na podstawie metadanych pliku.<br>Obsługiwane typy to "**none**" (wartość domyślna), "**Wklęśnięcie**", "**przyciąganie**". Działanie kopiowania obecnie nie obsługuje przyciągania w przypadku plików Avro odczytu/zapisu. | Nie       |

> [!NOTE]
> Biały znak w nazwie kolumny nie jest obsługiwany w przypadku plików Avro.

Poniżej znajduje się przykład zestawu danych Avro na platformie Azure Blob Storage:

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
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
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujścia Avro.

### <a name="avro-as-source"></a>Avro jako źródło

W sekcji *** \* Źródło \* *** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **AvroSource**. | Tak      |
| storeSettings | Grupa właściwości do odczytywania danych z magazynu danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia odczytu w obszarze `storeSettings` . **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

### <a name="avro-as-sink"></a>Avro jako ujścia

W sekcji *** \* ujścia \* *** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **AvroSink**. | Tak      |
| formatSettings          | Grupa właściwości. Zapoznaj się z tabelą **ustawień zapisu Avro** poniżej.| Nie      |
| storeSettings | Grupa właściwości do zapisywania danych w magazynie danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia zapisu w obszarze `storeSettings` . **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

Obsługiwane **Ustawienia zapisu Avro** w obszarze `formatSettings` :

| Właściwość      | Opis                                                  | Wymagane                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| typ          | Typ formatSettings musi być ustawiony na **AvroWriteSettings**. | Tak                                                   |
| maxRowsPerFile | Podczas zapisywania danych w folderze można wybrać opcję zapisu w wielu plikach i określić maksymalną liczbę wierszy na plik.  | Nie |
| fileNamePrefix | Ma zastosowanie, gdy `maxRowsPerFile` jest skonfigurowany.<br> Określ prefiks nazwy pliku podczas zapisywania danych do wielu plików, co spowodowało następujący wzorzec: `<fileNamePrefix>_00000.<fileExtension>` . Jeśli nie zostanie określony, prefiks nazwy pliku zostanie wygenerowany automatycznie. Ta właściwość nie ma zastosowania, gdy źródło jest magazynem opartym na plikach lub [z magazynem danych z włączoną opcją partycji](copy-activity-performance-features.md).  | Nie |

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

W mapowaniu przepływów danych można odczytywać i zapisywać w formacie Avro w następujących magazynach danych: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)i [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Właściwości źródła

Poniższa tabela zawiera listę właściwości obsługiwanych przez źródło Avro. Można edytować te właściwości na karcie **Opcje źródła** .

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Ścieżki symboli wieloznacznych | Wszystkie pliki zgodne ze ścieżką wieloznaczną zostaną przetworzone. Zastępuje folder i ścieżkę pliku ustawioną w zestawie danych. | nie | Ciąg [] | wildcardPaths |
| Ścieżka katalogu głównego partycji | W przypadku danych plików podzielonych na partycje można wprowadzić ścieżkę katalogu głównego partycji, aby odczytywać foldery partycjonowane jako kolumny | nie | Ciąg | partitionRootPath |
| Lista plików | Czy źródło wskazuje plik tekstowy, który zawiera listę plików do przetworzenia | nie | `true` lub `false` | fileList |
| Kolumna do przechowywania nazwy pliku | Utwórz nową kolumnę o nazwie i ścieżce pliku źródłowego | nie | Ciąg | rowUrlColumn |
| Po zakończeniu | Usuń lub Przenieś pliki po przetworzeniu. Ścieżka pliku zaczyna się od katalogu głównego kontenera | nie | Usuń: `true` lub `false` <br> Przenieś `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Filtruj według ostatniej modyfikacji | Wybierz filtrowanie plików na podstawie czasu ich ostatniej modyfikacji | nie | Timestamp | modifiedAfter <br> modifiedBefore |
| Nie znaleziono plików | W przypadku wartości true błąd nie jest zgłaszany, jeśli nie znaleziono plików | nie | `true` lub `false` | ignoreNoFilesFound |

### <a name="sink-properties"></a>Właściwości ujścia

Poniższa tabela zawiera listę właściwości obsługiwanych przez ujścia Avro. Te właściwości można edytować na karcie **Ustawienia** .

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Wyczyść folder | Jeśli folder docelowy został wyczyszczony przed zapisem | nie | `true` lub `false` | obciąć |
| Opcja nazwy pliku | Format nazewnictwa zapisanych danych. Domyślnie jeden plik na partycję w formacie `part-#####-tid-<guid>` | nie | Wzorzec: ciąg <br> Na partycję: String [] <br> Jako dane w kolumnie: ciąg <br> Dane wyjściowe do pojedynczego pliku: `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |
| Wszystkie oferty | Ujmij wszystkie wartości w cudzysłowy | nie | `true` lub `false` | quoteAll |

## <a name="data-type-support"></a>Obsługa typu danych

### <a name="copy-activity"></a>Działanie kopiowania
Avro [złożone typy danych](https://avro.apache.org/docs/current/spec.html#schema_complex) nie są obsługiwane (rekordy, wyliczenia, tablice, mapy, Unii i naprawione) w działaniu kopiowania.

### <a name="data-flows"></a>Przepływy danych
Podczas pracy z plikami Avro w przepływach danych można odczytywać i zapisywać złożone typy danych, ale najpierw należy wyczyścić schemat fizyczny z zestawu danych. W obszarze przepływy danych można ustawić logiczne projekcje i kolumny pochodne, które są złożonymi strukturami, a następnie mapowanie tych pól do pliku Avro.

## <a name="next-steps"></a>Następne kroki

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
