---
title: Format ORC w fabryce danych platformy Azure
description: W tym temacie opisano sposób postępowania z formatem ORC w usłudze Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 9b68d3724c6390fc5d30745924451e27ef9855b3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417729"
---
# <a name="orc-format-in-azure-data-factory"></a>Format ORC w fabryce danych platformy Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule należy wykonać, aby **przeanalizować pliki ORC lub zapisać dane w formacie ORC**. 

Format ORC jest obsługiwany dla następujących łączników: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), System [plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)i [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Zestawy danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych ORC.

| Właściwość         | Opis                                                  | Wymagany |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Właściwość typu zestawu danych musi być ustawiona na **Orc**. | Tak      |
| location         | Ustawienia lokalizacji plików. Każdy łącznik oparty na plikach ma swój własny `location`typ lokalizacji i obsługiwane właściwości w obszarze . **Zobacz szczegóły w sekcji właściwości łącznika - > Dataset .** | Tak      |

Poniżej znajduje się przykład zestawu danych ORC w usłudze Azure Blob Storage:

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

* Złożone typy danych nie są obsługiwane (STRUCT, MAP, LIST, UNION).
* Biały znak w nazwie kolumny nie jest obsługiwany.
* Plik ORC ma trzy [opcje związane z kompresją](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB, SNAPPY. Usługa Data Factory obsługuje odczyt danych z pliku ORC w dowolnym z tych skompresowanych formatów. Do odczytywania danych używa kodera-dekodera kompresji z metadanych. Podczas zapisywania w pliku ORC usługa Data Factory wybiera natomiast opcję ZLIB, która jest domyślna dla formatu ORC. Obecnie nie ma możliwości zastąpienia tego zachowania.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło ORC i ujście.

### <a name="orc-as-source"></a>ORC jako źródło

Następujące właściwości są obsługiwane w sekcji *** \*\* źródła*** działania kopiowania.

| Właściwość      | Opis                                                  | Wymagany |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość typu źródła działania kopiowania musi być ustawiona na **OrcSource**. | Tak      |
| sklepyWystawy | Grupa właściwości dotyczące sposobu odczytywania danych z magazynu danych. Każdy łącznik oparty na plikach ma `storeSettings`własne obsługiwane ustawienia odczytu w obszarze . **Zobacz szczegóły w artykule łącznika -> Sekcji Właściwości działania kopiowania**. | Nie       |

### <a name="orc-as-sink"></a>ORC jako zlew

Następujące właściwości są obsługiwane w sekcji *** \*ujście działania kopiowania.\* ***

| Właściwość      | Opis                                                  | Wymagany |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość typu źródła działania kopiowania musi być ustawiona na **OrcSink**. | Tak      |
| sklepyWystawy | Grupa właściwości dotyczące sposobu zapisywania danych w magazynie danych. Każdy łącznik oparty na plikach ma `storeSettings`własne obsługiwane ustawienia zapisu w obszarze . **Zobacz szczegóły w artykule łącznika -> Sekcji Właściwości działania kopiowania**. | Nie       |

## <a name="using-self-hosted-integration-runtime"></a>Korzystanie z samodzielnego środowiska wykonawczego integracji

> [!IMPORTANT]
> W przypadku kopiowania wspieranego przez self-hosted Integration Runtime, na przykład między lokalnymi i chmurowymi magazynami danych, jeśli nie kopiujesz plików ORC **w stanie rzeczywistym,** musisz zainstalować **64-bitowy pakiet JRE 8 (Java Runtime Environment) lub OpenJDK** i **microsoft Visual C++ 2010 Redystrybucyjny pakiet na komputerze podczerwieni.** Sprawdź poniższy akapit z więcej szczegółów.

W przypadku kopiowania uruchomionego na samodzielnym podczerweniu z serializacją/deserializacji plików ORC, *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* ADF lokalizuje środowisko wykonawcze Java, *`JAVA_HOME`* najpierw sprawdzając rejestr pod kątem środowiska JRE, jeśli nie znaleziono, po drugie sprawdzając zmienną systemową dla OpenJDK.

- **Aby użyć środowiska JRE:** 64-bitowa podczerwość wymaga 64-bitowego środowiska JRE. Można go znaleźć [tutaj](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Aby użyć OpenJDK:** Jest obsługiwany od wersji IR 3.13. Pakiet jvm.dll ze wszystkimi innymi wymaganymi zestawami OpenJDK do samodzielnego hostowania komputera podczerwieni i odpowiednio ustawić zmienną środowiska systemu JAVA_HOME.
- **Aby zainstalować pakiet redystrybucyjny Visual C++ 2010:** Pakiet redystrybucyjny Visual C++ 2010 nie jest zainstalowany z instalacjami podczerwonych hostowanych samodzielnie. Można go znaleźć [tutaj](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Jeśli kopiujesz dane do/z formatu ORC przy użyciu self-hosted Integration Runtime i błąd trafienia mówiąc:"Wystąpił błąd podczas wywoływania java, komunikat: `_JAVA_OPTIONS` **java.lang.OutOfMemoryError:Java sterty miejsca**", można dodać zmienną środowiskową na komputerze, który obsługuje Self-hosted IR, aby dostosować rozmiar sterty min/max dla JVM upoważnić do takiej kopii, a następnie ponownie uruchomić potoku.

![Ustawianie rozmiaru sterty JVM na samodzielnym podczerwieni](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Przykład: ustaw `_JAVA_OPTIONS` zmienną z wartością `-Xms256m -Xmx16g`. Flaga `Xms` określa początkową pulę alokacji pamięci dla maszyny `Xmx` wirtualnej Java (JVM), określając jednocześnie maksymalną pulę alokacji pamięci. Oznacza to, że JVM `Xms` zostanie uruchomiony z ilością pamięci i `Xmx` będzie mógł użyć maksymalnej ilości pamięci. Domyślnie ADF używać min 64 MB i max 1G.

## <a name="next-steps"></a>Następne kroki

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
