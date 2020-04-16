---
title: Format parkietu w usłudze Azure Data Factory
description: W tym temacie opisano sposób postępowania z formatem parkietu w usłudze Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 223b1b996b82acaa753eb55723e251dc5901bbec
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417712"
---
# <a name="parquet-format-in-azure-data-factory"></a>Format parkietu w usłudze Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule należy wykonać następujące informacje, aby **przeanalizować pliki parkietu lub zapisać dane w formacie Parkiet**. 

Format parkietu jest obsługiwany dla następujących łączników: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), System [plików](connector-file-system.md) [, FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)i [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Zestawy danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Parkiet.

| Właściwość         | Opis                                                  | Wymagany |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Właściwość typu zestawu danych musi być ustawiona na **Parkiet**. | Tak      |
| location         | Ustawienia lokalizacji plików. Każdy łącznik oparty na plikach ma swój własny `location`typ lokalizacji i obsługiwane właściwości w obszarze . **Zobacz szczegóły w sekcji właściwości łącznika - > Dataset .** | Tak      |
| compressionCodec (kod kodu kompresji) | Koder-dekodator kompresji używany podczas zapisywania do plików parkietu. Podczas odczytywania z plików parkietu fabryki danych automatycznie określają kodek kompresji na podstawie metadanych pliku.<br>Obsługiwane typy to "**none**", "**gzip**", "**snappy**" (domyślnie) i "**lzo**". Uwaga aktualnie Skopiuj działanie nie obsługuje LZO podczas odczytu/zapisu plików parkietu. | Nie       |

> [!NOTE]
> Biały znak w nazwie kolumny nie jest obsługiwany dla plików parkietu.

Poniżej znajduje się przykład parquet zestaw danych w usłudze Azure Blob Storage:

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło parkietu i ujście.

### <a name="parquet-as-source"></a>Parkiet jako źródło

Następujące właściwości są obsługiwane w sekcji *** \*\* źródła*** działania kopiowania.

| Właściwość      | Opis                                                  | Wymagany |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość typu źródła działania kopiowania musi być ustawiona na **ParquetSource**. | Tak      |
| sklepyWystawy | Grupa właściwości dotyczące sposobu odczytywania danych z magazynu danych. Każdy łącznik oparty na plikach ma `storeSettings`własne obsługiwane ustawienia odczytu w obszarze . **Zobacz szczegóły w artykule łącznika -> Sekcji Właściwości działania kopiowania**. | Nie       |

### <a name="parquet-as-sink"></a>Parkiet jako zlew

Następujące właściwości są obsługiwane w sekcji *** \*ujście działania kopiowania.\* ***

| Właściwość      | Opis                                                  | Wymagany |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość typu źródła działania kopiowania musi być ustawiona na **Parkiet**. | Tak      |
| sklepyWystawy | Grupa właściwości dotyczące sposobu zapisywania danych w magazynie danych. Każdy łącznik oparty na plikach ma `storeSettings`własne obsługiwane ustawienia zapisu w obszarze . **Zobacz szczegóły w artykule łącznika -> Sekcji Właściwości działania kopiowania**. | Nie       |

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Dowiedz się szczegółów z [transformacji źródła](data-flow-source.md) i [transformacji ujścia](data-flow-sink.md) w przepływie danych mapowania.

## <a name="data-type-support"></a>Obsługa typów danych

Typy danych złożonych parkietu nie są obecnie obsługiwane (np.

## <a name="using-self-hosted-integration-runtime"></a>Korzystanie z samodzielnego środowiska wykonawczego integracji

> [!IMPORTANT]
> W przypadku kopiowania wspieranego przez self-hosted Integration Runtime, na przykład między lokalnymi i chmurowymi magazynami danych, jeśli nie kopiujesz plików Parquet **w stanie rzeczywistym,** musisz zainstalować **64-bitowy pakiet JRE 8 (Java Runtime Environment) lub OpenJDK** i **microsoft Visual C++ 2010 Redystrybucyjny pakiet na komputerze podczerwieni.** Sprawdź poniższy akapit z więcej szczegółów.

W przypadku kopiowania uruchomionego na samodzielnym podczerweniu z serializacją/deserializacji plików Parkietu, *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* ADF lokalizuje środowisko wykonawcze Java, *`JAVA_HOME`* najpierw sprawdzając rejestr dla środowiska JRE, jeśli nie znaleziono, po drugie sprawdzając zmienną systemową dla OpenJDK.

- **Aby użyć środowiska JRE:** 64-bitowa podczerwość wymaga 64-bitowego środowiska JRE. Można go znaleźć [tutaj](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Aby użyć OpenJDK:** Jest obsługiwany od wersji IR 3.13. Pakiet jvm.dll ze wszystkimi innymi wymaganymi zestawami OpenJDK do samodzielnego hostowania komputera podczerwieni i odpowiednio ustawić zmienną środowiska systemu JAVA_HOME.
- **Aby zainstalować pakiet redystrybucyjny Visual C++ 2010:** Pakiet redystrybucyjny Visual C++ 2010 nie jest zainstalowany z instalacjami podczerwonych hostowanych samodzielnie. Można go znaleźć [tutaj](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Jeśli kopiujesz dane do/z formatu Parkietu przy użyciu self-hosted Integration Runtime i błąd trafienia mówiąc: "Wystąpił błąd podczas wywoływania java, komunikat: `_JAVA_OPTIONS` **java.lang.OutOfMemoryError:Java sterty miejsca**", można dodać zmienną środowiskową na komputerze, który obsługuje Self-hosted IR, aby dostosować rozmiar sterty min/max dla JVM upoważnić do takiej kopii, a następnie ponownie uruchomić potoku.

![Ustawianie rozmiaru sterty JVM na samodzielnym podczerwieni](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Przykład: ustaw `_JAVA_OPTIONS` zmienną z wartością `-Xms256m -Xmx16g`. Flaga `Xms` określa początkową pulę alokacji pamięci dla maszyny `Xmx` wirtualnej Java (JVM), określając jednocześnie maksymalną pulę alokacji pamięci. Oznacza to, że JVM `Xms` zostanie uruchomiony z ilością pamięci i `Xmx` będzie mógł użyć maksymalnej ilości pamięci. Domyślnie ADF używać min 64 MB i max 1G.

## <a name="next-steps"></a>Następne kroki

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
