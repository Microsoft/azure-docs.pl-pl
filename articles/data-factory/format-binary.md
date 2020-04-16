---
title: Format binarny w fabryce danych platformy Azure
description: W tym temacie opisano sposób postępowania z formatem binarnym w usłudze Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 4560560b3677030a66e277e96eb552d39f5c82c1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416340"
---
# <a name="binary-format-in-azure-data-factory"></a>Format binarny w fabryce danych platformy Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Format binarny jest obsługiwany dla następujących łączników: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), System [plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)i [SFTP](connector-sftp.md).

Można użyć binarnego zestawu danych w [aktywności kopiowania,](copy-activity-overview.md) [aktywności GetMetadata](control-flow-get-metadata-activity.md)lub [aktywności Usuń](delete-activity.md). W przypadku korzystania z binarnego zestawu danych podajnik ADF nie analizuje zawartości pliku, ale traktuje ją w stanie gotowym do użycia. 

>[!NOTE]
>Korzystając z binarnego zestawu danych w aktywności kopiowania, można kopiować tylko z binarnego zestawu danych do binarnego zestawu danych.

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Zestawy danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez binarny zestaw danych.

| Właściwość         | Opis                                                  | Wymagany |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Właściwość typu zestawu danych musi być ustawiona na **Binary**. | Tak      |
| location         | Ustawienia lokalizacji plików. Każdy łącznik oparty na plikach ma swój własny `location`typ lokalizacji i obsługiwane właściwości w obszarze . **Zobacz szczegóły w sekcji właściwości łącznika - > Dataset .** | Tak      |
| kompresja | Grupa właściwości do skonfigurowania kompresji plików. Skonfiguruj tę sekcję, jeśli chcesz wykonać kompresję/dekompresję podczas wykonywania działania. | Nie |
| type | Koder-dekodusz kompresji używany do odczytu/zapisu plików binarnych. <br>Dozwolone wartości to **bzip2**, **gzip**, **deflate**, **ZipDeflate**. , aby użyć go podczas zapisywania pliku.<br>Uwaga Podczas korzystania z działania kopiowania do dekompresji plików ZipDeflate i zapisu do magazynu `<path specified in dataset>/<folder named as source zip file>/`danych ujścia opartego na plikach pliki zostaną wyodrębnione do folderu: . | Nie       |
| poziom | Stopień kompresji. Zastosuj, gdy zestaw danych jest używany w zlew aktywności kopiowania.<br>Dozwolone wartości są **optymalne** lub **najszybsze**.<br>- **Najszybszy:** Operacja kompresji powinna zakończyć się tak szybko, jak to możliwe, nawet jeśli wynikowy plik nie jest optymalnie skompresowany.<br>- **Optymalne**: Operacja kompresji powinna być optymalnie skompresowana, nawet jeśli operacja trwa dłużej. Aby uzyskać więcej informacji, zobacz temat [Poziom kompresji.](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) | Nie       |

Poniżej znajduje się przykład binarnego zestawu danych w usłudze Azure Blob Storage:

```json
{
    "name": "BinaryDataset",
    "properties": {
        "type": "Binary",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło binarne i ujście.

>[!NOTE]
>Korzystając z binarnego zestawu danych w aktywności kopiowania, można kopiować tylko z binarnego zestawu danych do binarnego zestawu danych.

### <a name="binary-as-source"></a>Binarne jako źródło

Następujące właściwości są obsługiwane w sekcji *** \*\* źródła*** działania kopiowania.

| Właściwość      | Opis                                                  | Wymagany |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość typu źródła działania kopiowania musi być ustawiona na **BinarySource**. | Tak      |
| sklepyWystawy | Grupa właściwości dotyczące sposobu odczytywania danych z magazynu danych. Każdy łącznik oparty na plikach ma `storeSettings`własne obsługiwane ustawienia odczytu w obszarze . **Zobacz szczegóły w artykule łącznika -> Sekcji Właściwości działania kopiowania**. | Nie       |

### <a name="binary-as-sink"></a>Binarny jako zlew

Następujące właściwości są obsługiwane w sekcji *** \*ujście działania kopiowania.\* ***

| Właściwość      | Opis                                                  | Wymagany |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość typu źródła działania kopiowania musi być ustawiona na **BinarySink**. | Tak      |
| sklepyWystawy | Grupa właściwości dotyczące sposobu zapisywania danych w magazynie danych. Każdy łącznik oparty na plikach ma `storeSettings`własne obsługiwane ustawienia zapisu w obszarze . **Zobacz szczegóły w artykule łącznika -> Sekcji Właściwości działania kopiowania**. | Nie       |

## <a name="next-steps"></a>Następne kroki

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuwanie działania](delete-activity.md)
