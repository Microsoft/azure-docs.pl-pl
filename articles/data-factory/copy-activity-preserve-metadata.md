---
title: Zachowywanie metadanych i list ACL przy użyciu działania kopiowania w usłudze Azure Data Factory
description: Dowiedz się, jak zachować metadane i listy ACL podczas kopiowania przy użyciu działania kopiowania w usłudze Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: jingwang
ms.openlocfilehash: 5ce1b85394a7bb604841f7fb941bdebf12c0bca2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414149"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Zachowywanie metadanych i list ACL przy użyciu działania kopiowania w usłudze Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Korzystając z działania kopiowania usługi Azure Data Factory do kopiowania danych ze źródła do ujścia, w następujących scenariuszach można również zachować metadane i listy ACL wzdłuż.

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a>Zachowywanie metadanych dla migracji jezior

Podczas migracji danych z jednego źródła danych do innego, w tym [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob](connector-azure-blob-storage.md)i [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md)można zachować metadane pliku wraz z danymi.

Działanie kopiowania obsługuje zachowywanie następujących atrybutów podczas kopiowania danych:

- **Wszystkie metadane określone przez klienta** 
- I następujące **pięć danych przechowywania wbudowanych właściwości systemu:** `contentType`, `contentLanguage` (z wyjątkiem Amazon S3), `contentEncoding`, , `contentDisposition` `cacheControl`.

Podczas kopiowania plików z amazon S3/Azure Data Lake Storage Gen2/Azure Blob do usługi Azure Data Lake Storage Gen2/Azure Blob w formacie binarnym, można znaleźć **opcję Zachowaj** na karcie**Ustawienia** **działania** > kopiowania dla tworzenia działań lub na stronie **Ustawienia** w narzędziu Kopiowanie danych.

![Kopiowanie aktywności zachowywać metadane](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

Oto przykład konfiguracji JSON działania kopiowania `preserve`(patrz): 

```json
"activities":[
    {
        "name": "CopyAndPreserveMetadata",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a>Zachowywanie list ACL z pamięci masowej Data Lake Gen1/Gen2 do Gen2

Podczas uaktualniania z usługi Azure Data Lake Storage Gen1 do Gen2 lub kopiowania danych między ADLS Gen2, można zachować listy kontroli dostępu POSIX (Listy KONTROLI DOSTĘPU) wraz z plikami danych. Aby uzyskać więcej informacji na temat kontroli dostępu, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) i [kontrola dostępu w usłudze Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-access-control.md)

Działanie kopiowania obsługuje zachowanie następujących typów list ACL podczas kopiowania danych. Można wybrać jeden lub więcej typów:

- **ACL**: Kopiowanie i zachowywanie list kontroli dostępu POSIX w plikach i katalogach. Kopiuje pełne istniejące listy ACL ze źródła do ujścia. 
- **Właściciel**: Kopiowanie i zachowywanie użytkownika posiadania plików i katalogów. Wymagany jest dostęp superużytnik do ujścia Data Lake Storage Gen2.
- **Grupa**: Kopiowanie i zachowywanie grupy zawierającej pliki i katalogi. Wymagany jest dostęp superużytnik do ujścia Data Lake Storage Gen2 lub użytkownika będącego właścicielem (jeśli użytkownik będący właścicielem jest również członkiem grupy docelowej).

Jeśli określisz, aby skopiować z folderu, data factory replikuje listy ACL dla `recursive` danego folderu i pliki i katalogi pod nim, jeśli jest ustawiona na true. Jeśli określisz, aby skopiować z jednego pliku, listy ACL w tym pliku są kopiowane.

>[!NOTE]
>W przypadku używania adf do zachowania list ACL z usługi Data Lake Storage Gen1/Gen2 do Gen2, istniejące listy ACL w odpowiednim folderze/plikach ujścia Gen2 zostaną zastąpione.

>[!IMPORTANT]
>Po wybraniu zachowania list ACL upewnij się, że udzielasz wystarczająco wysokie uprawnienia dla usługi Data Factory do działania na koncie usługi Data Lake Gen2 ujścia. Na przykład użyj uwierzytelniania klucza konta lub przypisz rolę Właściciel danych obiektu Blob magazynu do jednostki usługi lub tożsamości zarządzanej.

Po skonfigurowaniu źródła jako data lake storage Gen1/Gen2 z formatem binarnym lub opcją kopiowania binarnego i pogrążeniem jako Data Lake Storage Gen2 w formacie binarnym lub opcji kopiowania binarnego, można znaleźć opcję **Zachowaj** na stronie **Ustawienia** w narzędziu Kopiowanie danych lub na karcie > **Ustawienia** **działania kopiowania**do tworzenia aktywności.

![Pamięć masowa Data Lake Gen1/Gen2 do Gen2 Preserve ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Oto przykład konfiguracji JSON działania kopiowania `preserve`(patrz): 

```json
"activities":[
    {
        "name": "CopyAndPreserveACLs",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1/Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>Następne kroki

Zobacz inne artykuły dotyczące działania kopiowania:

- [Omówienie działania kopiowania](copy-activity-overview.md)
- [Wydajność działania kopiowania](copy-activity-performance.md)
