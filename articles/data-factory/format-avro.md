---
title: Format Avro w fabryce danych platformy Azure
description: W tym temacie opisano sposób postępowania z formatem Avro w usłudze Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jingwang
ms.openlocfilehash: 931287fa2a4104069b101236bec9f76bb7193e8d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416350"
---
# <a name="avro-format-in-azure-data-factory"></a>Format Avro w fabryce danych platformy Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Postępuj zgodnie z tym artykułem, jeśli chcesz **przeanalizować pliki Avro lub zapisać dane w formacie Avro**. 

Format Avro jest obsługiwany dla następujących łączników: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), System [plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)i [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Zestawy danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Avro.

| Właściwość         | Opis                                                  | Wymagany |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Właściwość typu zestawu danych musi być ustawiona na **Avro**. | Tak      |
| location         | Ustawienia lokalizacji plików. Każdy łącznik oparty na plikach ma swój własny `location`typ lokalizacji i obsługiwane właściwości w obszarze . **Zobacz szczegóły w sekcji właściwości łącznika - > Dataset .** | Tak      |
| kod avroCompressionCodec | Kodek kompresji używany podczas pisania do plików Avro. Podczas odczytywania z plików Avro, Data Factory automatycznie określa kodek kompresji na podstawie metadanych pliku.<br>Obsługiwane typy to "**none**" (default), "**deflate**", "**snappy**". Uwaga obecnie Skopaj działanie nie obsługuje Snappy podczas odczytu / zapisu plików Avro. | Nie       |

> [!NOTE]
> Biały znak w nazwie kolumny nie jest obsługiwany dla plików Avro.

Poniżej znajduje się przykład zestawu danych Avro w usłudze Azure Blob Storage:

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Avro i ujście.

### <a name="avro-as-source"></a>Avro jako źródło

Następujące właściwości są obsługiwane w sekcji *** \*\* źródła*** działania kopiowania.

| Właściwość      | Opis                                                  | Wymagany |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość typu źródła działania kopiowania musi być ustawiona na **AvroSource**. | Tak      |
| sklepyWystawy | Grupa właściwości dotyczące sposobu odczytywania danych z magazynu danych. Każdy łącznik oparty na plikach ma `storeSettings`własne obsługiwane ustawienia odczytu w obszarze . **Zobacz szczegóły w artykule łącznika -> Sekcji Właściwości działania kopiowania**. | Nie       |

### <a name="avro-as-sink"></a>Avro jako zlew

Następujące właściwości są obsługiwane w sekcji *** \*ujście działania kopiowania.\* ***

| Właściwość      | Opis                                                  | Wymagany |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość typu źródła działania kopiowania musi być ustawiona na **AvroSink**. | Tak      |
| sklepyWystawy | Grupa właściwości dotyczące sposobu zapisywania danych w magazynie danych. Każdy łącznik oparty na plikach ma `storeSettings`własne obsługiwane ustawienia zapisu w obszarze . **Zobacz szczegóły w artykule łącznika -> Sekcji Właściwości działania kopiowania**. | Nie       |

## <a name="data-type-support"></a>Obsługa typów danych

### <a name="copy-activity"></a>Działanie kopiowania
[Avro złożone typy danych](https://avro.apache.org/docs/current/spec.html#schema_complex) nie są obsługiwane (rekordy, wyliczenia, tablice, mapy, związki i stałe) w copy activity.

### <a name="data-flows"></a>Przepływy danych
Podczas pracy z plikami Avro w przepływach danych można odczytywać i zapisywać złożone typy danych, ale najpierw należy wyczyścić schemat fizyczny z zestawu danych. W przepływach danych można ustawić rzutowanie logiczne i wyprowadzić kolumny, które są złożonymi strukturami, a następnie automatycznie mapować te pola na plik Avro.

## <a name="next-steps"></a>Następne kroki

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
