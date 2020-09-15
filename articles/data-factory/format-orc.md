---
title: Format ORC w Azure Data Factory
description: W tym temacie opisano sposób postępowania z formatem ORC w Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: jingwang
ms.openlocfilehash: f71b739242cf4f6a3549927a2a7e61400b2f987e
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2020
ms.locfileid: "90061091"
---
# <a name="orc-format-in-azure-data-factory"></a>Format ORC w Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Postępuj zgodnie z tym artykułem, jeśli chcesz **analizować pliki ORC lub zapisywać dane w formacie Orc**. 

Format ORC jest obsługiwany dla następujących łączników: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [system plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)i [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych ORC.

| Właściwość         | Opis                                                  | Wymagane |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Właściwość Type zestawu danych musi być ustawiona na wartość **Orc**. | Tak      |
| location         | Ustawienia lokalizacji plików. Każdy Łącznik oparty na plikach ma własny typ lokalizacji i obsługiwane właściwości w sekcji `location` . **Zobacz szczegóły w sekcji łącznik — > właściwości zestawu danych**. | Tak      |

Poniżej znajduje się przykład zestawu danych ORC na platformie Azure Blob Storage:

```json
{
    "name": "ORCDataset",
    "properties": {
        "type": "Orc",
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
            }
        }
    }
}
```

Pamiętaj o następujących kwestiach:

* Złożone typy danych nie są obsługiwane (struktura, mapa, lista, Unia).
* Biały znak w nazwie kolumny nie jest obsługiwany.
* Plik ORC ma trzy [opcje związane z kompresją](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB, SNAPPY. Usługa Data Factory obsługuje odczyt danych z pliku ORC w dowolnym z tych skompresowanych formatów. Do odczytywania danych używa kodera-dekodera kompresji z metadanych. Podczas zapisywania w pliku ORC usługa Data Factory wybiera natomiast opcję ZLIB, która jest domyślna dla formatu ORC. Obecnie nie ma możliwości zastąpienia tego zachowania.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujścia ORC.

### <a name="orc-as-source"></a>ORC jako źródło

W sekcji *** \* Źródło \* *** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **OrcSource**. | Tak      |
| storeSettings | Grupa właściwości do odczytywania danych z magazynu danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia odczytu w obszarze `storeSettings` . **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

### <a name="orc-as-sink"></a>ORC jako ujścia

W sekcji *** \* ujścia \* *** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **OrcSink**. | Tak      |
| formatSettings | Grupa właściwości. Zapoznaj się z tabelą **ustawień zapisu Orc** poniżej. |    Nie      |
| storeSettings | Grupa właściwości do zapisywania danych w magazynie danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia zapisu w obszarze `storeSettings` . **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

Obsługiwane **Ustawienia zapisu Orc** w obszarze `formatSettings` :

| Właściwość      | Opis                                                  | Wymagane                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| typ          | Typ formatSettings musi być ustawiony na **OrcWriteSettings**. | Tak                                                   |
| maxRowsPerFile | Podczas zapisywania danych w folderze można wybrać opcję zapisu w wielu plikach i określić maksymalną liczbę wierszy na plik.  | Nie |
| fileNamePrefix | Określ prefiks nazwy pliku podczas zapisywania danych do wielu plików, co spowodowało następujący wzorzec: `<fileNamePrefix>_00000.<fileExtension>` . Jeśli nie zostanie określony, prefiks nazwy pliku zostanie wygenerowany automatycznie. Ta właściwość nie ma zastosowania, gdy źródło jest magazynem opartym na plikach lub [z magazynem danych z włączoną opcją partycji](copy-activity-performance-features.md).  | Nie |

## <a name="using-self-hosted-integration-runtime"></a>Korzystanie z Integration Runtime samoobsługowego

> [!IMPORTANT]
> W przypadku kopii obsługiwanej przez samoobsługowe Integration Runtime, np. między lokalnym i magazynem danych w chmurze, jeśli nie kopiujesz plików **Orc, należy**zainstalować **pakiet redystrybucyjny** **64-bitowy z programem JRE 8 (Java Runtime Environment) lub OpenJDK** i Microsoft Visual C++ 2010 na maszynie podczerwieni. Aby uzyskać więcej informacji, zapoznaj się z poniższym akapitem.

W przypadku kopiowania uruchomionego na samoobsługowym środowisku IR przy użyciu serializacji/deserializacji pliku ORC można zlokalizować środowisko uruchomieniowe języka Java, sprawdzając najpierw rejestr *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* dla środowiska JRE, jeśli nie zostanie on znaleziony, a następnie w celu sprawdzenia zmiennej systemowej *`JAVA_HOME`* dla OpenJDK.

- **Aby użyć środowiska JRE**: 64-bitowy IR wymaga 64-bitowego środowiska JRE. Można je znaleźć w [tym miejscu](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Aby użyć OpenJDK**: jest obsługiwana od czasu IR w wersji 3,13. Zapakuj jvm.dll ze wszystkimi innymi wymaganymi zestawami OpenJDK na samodzielną maszynę podczerwieni i ustaw dla zmiennej środowiskowej system JAVA_HOME odpowiednie.
- **Aby zainstalować pakiet redystrybucyjny Visual C++ 2010**: pakiet redystrybucyjny Visual C++ 2010 nie jest instalowany z własnymi obsługiwanymi instalacjami środowiska IR. Można je znaleźć w [tym miejscu](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Jeśli skopiujesz dane do/z formatu ORC przy użyciu samodzielnego Integration Runtime i błędu trafień mówiąc "Wystąpił błąd podczas wywoływania języka Java, komunikat: **Java. lang. OutOfMemoryError: przestrzeń sterty Java**", można dodać zmienną środowiskową `_JAVA_OPTIONS` na maszynie, która hostuje własne środowisko IR, aby dostosować minimalny/maksymalny rozmiar sterty dla JVM w celu uzyskania takiego kopiowania, a następnie ponownie uruchomić potok

![Ustawianie rozmiaru sterty JVM na samoobsługowym środowisku IR](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Przykład: Ustaw zmienną `_JAVA_OPTIONS` o wartości `-Xms256m -Xmx16g` . Flaga `Xms` określa początkową pulę alokacji pamięci dla wirtualna maszyna Java (JVM), podczas gdy `Xmx` określa maksymalną pulę alokacji pamięci. Oznacza to, że JVM zostanie uruchomione z `Xms` ilością pamięci i będzie można użyć maksymalnej `Xmx` ilości pamięci. Domyślnie funkcja ADF używa minimalnej 64 MB i maksymalnej wartości 1G.

## <a name="next-steps"></a>Następne kroki

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
