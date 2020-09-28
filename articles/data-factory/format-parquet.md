---
title: Format Parquet w Azure Data Factory
description: W tym temacie opisano sposób postępowania z formatem Parquet w Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/27/2020
ms.author: jingwang
ms.openlocfilehash: 13a0966413519e56199cfce150d9dd7973d634ec
ms.sourcegitcommit: ada9a4a0f9d5dbb71fc397b60dc66c22cf94a08d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91404731"
---
# <a name="parquet-format-in-azure-data-factory"></a>Format Parquet w Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Postępuj zgodnie z tym artykułem, jeśli chcesz **analizować pliki Parquet lub zapisywać dane w formacie Parquet**. 

Format Parquet jest obsługiwany dla następujących łączników: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [system plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)i [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Parquet.

| Właściwość         | Opis                                                  | Wymagane |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Właściwość Type zestawu danych musi być ustawiona na wartość **Parquet**. | Tak      |
| location         | Ustawienia lokalizacji plików. Każdy Łącznik oparty na plikach ma własny typ lokalizacji i obsługiwane właściwości w sekcji `location` . **Zobacz szczegóły w sekcji łącznik — > właściwości zestawu danych**. | Tak      |
| compressionCodec | Koder-dekoder kompresji do użycia podczas zapisywania w plikach Parquet. Podczas odczytywania z plików Parquet fabryki danych automatycznie określają koder-dekoder kompresji na podstawie metadanych pliku.<br>Obsługiwane typy to "**none**", "**gzip**", "**przyciąganie**" (domyślnie) i "**LZO**". Uwaga Ta sama czynność kopiowania nie obsługuje LZO w przypadku plików Parquet odczytu/zapisu. | Nie       |

> [!NOTE]
> Biały znak w nazwie kolumny nie jest obsługiwany w przypadku plików Parquet.

Poniżej znajduje się przykład zestawu danych Parquet na platformie Azure Blob Storage:

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
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
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujścia Parquet.

### <a name="parquet-as-source"></a>Parquet jako źródło

W sekcji *** \* Źródło \* *** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **ParquetSource**. | Tak      |
| storeSettings | Grupa właściwości do odczytywania danych z magazynu danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia odczytu w obszarze `storeSettings` . **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

### <a name="parquet-as-sink"></a>Parquet jako ujścia

W sekcji *** \* ujścia \* *** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Właściwość Type ujścia działania Copy musi być ustawiona na wartość **ParquetSink**. | Tak      |
| formatSettings | Grupa właściwości. Zapoznaj się z tabelą **ustawień zapisu Parquet** poniżej. |    Nie      |
| storeSettings | Grupa właściwości do zapisywania danych w magazynie danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia zapisu w obszarze `storeSettings` . **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

Obsługiwane **Ustawienia zapisu Parquet** w obszarze `formatSettings` :

| Właściwość      | Opis                                                  | Wymagane                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| typ          | Typ formatSettings musi być ustawiony na **ParquetWriteSettings**. | Tak                                                   |
| maxRowsPerFile | Podczas zapisywania danych w folderze można wybrać opcję zapisu w wielu plikach i określić maksymalną liczbę wierszy na plik.  | Nie |
| fileNamePrefix | Ma zastosowanie, gdy `maxRowsPerFile` jest skonfigurowany.<br> Określ prefiks nazwy pliku podczas zapisywania danych do wielu plików, co spowodowało następujący wzorzec: `<fileNamePrefix>_00000.<fileExtension>` . Jeśli nie zostanie określony, prefiks nazwy pliku zostanie wygenerowany automatycznie. Ta właściwość nie ma zastosowania, gdy źródło jest magazynem opartym na plikach lub [z magazynem danych z włączoną opcją partycji](copy-activity-performance-features.md).  | Nie |

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

W mapowaniu przepływów danych można odczytywać i zapisywać w formacie Parquet w następujących magazynach danych: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)i [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Właściwości źródła

Poniższa tabela zawiera listę właściwości obsługiwanych przez źródło Parquet. Można edytować te właściwości na karcie **Opcje źródła** .

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Format musi być `parquet` | tak | `parquet` | format |
| Ścieżki symboli wieloznacznych | Wszystkie pliki zgodne ze ścieżką wieloznaczną zostaną przetworzone. Zastępuje folder i ścieżkę pliku ustawioną w zestawie danych. | nie | Ciąg [] | wildcardPaths |
| Ścieżka katalogu głównego partycji | W przypadku danych plików podzielonych na partycje można wprowadzić ścieżkę katalogu głównego partycji, aby odczytywać foldery partycjonowane jako kolumny | nie | Ciąg | partitionRootPath |
| Lista plików | Czy źródło wskazuje plik tekstowy, który zawiera listę plików do przetworzenia | nie | `true` lub `false` | fileList |
| Kolumna do przechowywania nazwy pliku | Utwórz nową kolumnę o nazwie i ścieżce pliku źródłowego | nie | Ciąg | rowUrlColumn |
| Po zakończeniu | Usuń lub Przenieś pliki po przetworzeniu. Ścieżka pliku zaczyna się od katalogu głównego kontenera | nie | Usuń: `true` lub `false` <br> Przenieś `[<from>, <to>]` | purgeFiles <br> moveFiles |
| Filtruj według ostatniej modyfikacji | Wybierz filtrowanie plików na podstawie czasu ich ostatniej modyfikacji | nie | Timestamp | modifiedAfter <br> modifiedBefore |
| Nie znaleziono plików | W przypadku wartości true błąd nie jest zgłaszany, jeśli nie znaleziono plików | nie | `true` lub `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Przykład źródła

Poniższy obraz stanowi przykład konfiguracji źródła Parquet w mapowaniu przepływów danych.

![Źródło Parquet](media/data-flow/parquet-source.png)

Skojarzony skrypt przepływu danych:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    rowUrlColumn: 'fileName',
    format: 'parquet') ~> ParquetSource
```

### <a name="sink-properties"></a>Właściwości ujścia

Poniższa tabela zawiera listę właściwości obsługiwanych przez ujścia Parquet. Te właściwości można edytować na karcie **Ustawienia** .

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Format musi być `parquet` | tak | `parquet` | format |
| Wyczyść folder | Jeśli folder docelowy został wyczyszczony przed zapisem | nie | `true` lub `false` | obciąć |
| Opcja nazwy pliku | Format nazewnictwa zapisanych danych. Domyślnie jeden plik na partycję w formacie `part-#####-tid-<guid>` | nie | Wzorzec: ciąg <br> Na partycję: String [] <br> Jako dane w kolumnie: ciąg <br> Dane wyjściowe do pojedynczego pliku: `['<fileName>']` | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="sink-example"></a>Przykład ujścia

Poniżej przedstawiono przykład konfiguracji ujścia Parquet w mapowaniu przepływów danych.

![Ujścia Parquet](media/data-flow/parquet-sink.png)

Skojarzony skrypt przepływu danych:

```
ParquetSource sink(
    format: 'parquet',
    filePattern:'output[n].parquet',
    truncate: true,
    allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> ParquetSink
```

## <a name="data-type-support"></a>Obsługa typu danych

Parquet złożone typy danych nie są obecnie obsługiwane (na przykład mapy, listy, struktury).

## <a name="using-self-hosted-integration-runtime"></a>Korzystanie z Integration Runtime samoobsługowego

> [!IMPORTANT]
> W przypadku kopii obsługiwanej przez samoobsługowe Integration Runtime, np. między lokalnym i magazynem danych w chmurze, jeśli nie kopiujesz plików **Parquet, należy**zainstalować **pakiet redystrybucyjny** **64-bitowy z programem JRE 8 (Java Runtime Environment) lub OpenJDK** i Microsoft Visual C++ 2010 na maszynie podczerwieni. Aby uzyskać więcej informacji, zapoznaj się z poniższym akapitem.

W przypadku kopiowania uruchomionego na samoobsługowym środowisku IR przy użyciu serializacji/deserializacji pliku Parquet można zlokalizować środowisko uruchomieniowe języka Java, sprawdzając najpierw rejestr *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* dla środowiska JRE, jeśli nie zostanie on znaleziony, a następnie w celu sprawdzenia zmiennej systemowej *`JAVA_HOME`* dla OpenJDK.

- **Aby użyć środowiska JRE**: 64-bitowy IR wymaga 64-bitowego środowiska JRE. Można je znaleźć w [tym miejscu](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Aby użyć OpenJDK**: jest obsługiwana od czasu IR w wersji 3,13. Zapakuj jvm.dll ze wszystkimi innymi wymaganymi zestawami OpenJDK na samodzielną maszynę podczerwieni i ustaw dla zmiennej środowiskowej system JAVA_HOME odpowiednie.
- **Aby zainstalować pakiet redystrybucyjny Visual C++ 2010**: pakiet redystrybucyjny Visual C++ 2010 nie jest instalowany z własnymi obsługiwanymi instalacjami środowiska IR. Można je znaleźć w [tym miejscu](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Jeśli skopiujesz dane do/z formatu Parquet przy użyciu samodzielnego Integration Runtime i błędu trafień mówiąc "Wystąpił błąd podczas wywoływania języka Java, komunikat: **Java. lang. OutOfMemoryError: przestrzeń sterty Java**", można dodać zmienną środowiskową `_JAVA_OPTIONS` na maszynie, która hostuje własne środowisko IR, aby dostosować minimalny/maksymalny rozmiar sterty dla JVM w celu uzyskania takiego kopiowania, a następnie ponownie uruchomić potok

![Ustawianie rozmiaru sterty JVM na samoobsługowym środowisku IR](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Przykład: Ustaw zmienną `_JAVA_OPTIONS` o wartości `-Xms256m -Xmx16g` . Flaga `Xms` określa początkową pulę alokacji pamięci dla wirtualna maszyna Java (JVM), podczas gdy `Xmx` określa maksymalną pulę alokacji pamięci. Oznacza to, że JVM zostanie uruchomione z `Xms` ilością pamięci i będzie można użyć maksymalnej `Xmx` ilości pamięci. Domyślnie funkcja ADF używa minimalnej 64 MB i maksymalnej wartości 1G.

## <a name="next-steps"></a>Następne kroki

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
