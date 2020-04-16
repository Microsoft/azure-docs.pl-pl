---
title: Pobierz aktywność metadanych w fabryce danych platformy Azure
description: Dowiedz się, jak używać działania Pobierz metadane w potoku fabryki danych.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jingwang
ms.openlocfilehash: 344ad8e106c119c1de59570d1ec4e3df5e1cc8af
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417121"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Pobierz aktywność metadanych w fabryce danych platformy Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Za pomocą działania Pobierz metadane można pobrać metadane dowolnych danych w usłudze Azure Data Factory. Tego działania można użyć w następujących scenariuszach:

- Sprawdź poprawność metadanych wszelkich danych.
- Wyzwalanie potoku, gdy dane są gotowe/dostępne.

W przepływie sterowania dostępna jest następująca funkcja:

- Można użyć danych wyjściowych z Get Metadata działania w wyrażeniach warunkowych do wykonywania sprawdzania poprawności.
- Można wyzwolić potoku, gdy warunek jest spełniony za pośrednictwem Do Until pętli.

## <a name="capabilities"></a>Możliwości

Działanie Pobierz metadane przyjmuje zestaw danych jako dane wejściowe i zwraca informacje o metadanych jako dane wyjściowe. Obecnie obsługiwane są następujące łączniki i odpowiadające im metadane można pobrać. Maksymalny rozmiar zwróconych metadanych wynosi 2 MB.

>[!NOTE]
>Po uruchomieniu get metadanych działania w czasie wykonywania integracji hostowanego samodzielnie, najnowsze możliwości są obsługiwane w wersji 3.6 lub nowszej.

### <a name="supported-connectors"></a>Obsługiwane złącza

**Przechowywanie plików**

| Łącznik/metadane | Itemname<br>(plik/folder) | Itemtype<br>(plik/folder) | size<br>(plik) | Utworzone<br>(plik/folder) | Lastmodified<br>(plik/folder) |Childitems<br>(folder) |contentMD5<br>(plik) | — struktura<br/>(plik) | Columncount<br>(plik) | Istnieje<br>(plik/folder) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Azure Blob Storage](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Usługa Azure Data Lake Storage 1. generacji](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Usługa Azure Data Lake Storage 2. generacji](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Pliki platformy Azure](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [System plików](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

- Korzystając z działania Pobierz metadane względem folderu, upewnij się, że masz uprawnienie LIST/EXECUTE do danego folderu.
- W przypadku Amazon S3 `lastModified` i Google Cloud Storage dotyczy zasobnika i klucza, ale nie do folderu wirtualnego i `exists` dotyczy zasobnika i klucza, ale nie do prefiksu lub folderu wirtualnego.
- W przypadku magazynu `lastModified` obiektów Blob platformy Azure dotyczy kontenera i obiektu blob, ale nie do folderu wirtualnego.
- `lastModified`filtr ma obecnie zastosowanie do filtrowania elementów podrzędnych, ale nie samego określonego folderu/pliku.
- Filtr symboli wieloznacznych w folderach/plikach nie jest obsługiwany w przypadku działania Pobierz metadane.

**Relacyjna baza danych**

| Łącznik/metadane | — struktura | Columncount | Istnieje |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Wystąpienie zarządzanego usługi Azure SQL Database](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Magazyn danych SQL platformy Azure](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Opcje metadanych

Można określić następujące typy metadanych na liście pól Uzyskaj działanie metadanych, aby pobrać odpowiednie informacje:

| Typ metadanych | Opis |
|:--- |:--- |
| Itemname | Nazwa pliku lub folderu. |
| Itemtype | Typ pliku lub folderu. Zwrócona `File` wartość `Folder`jest lub . |
| size | Rozmiar pliku w bajtach. Dotyczy tylko plików. |
| Utworzone | Utworzono datetime pliku lub folderu. |
| Lastmodified | Ostatnia modyfikacja datetime pliku lub folderu. |
| Childitems | Lista podfolderów i plików w danym folderze. Dotyczy tylko folderów. Zwrócona wartość to lista nazwy i typu każdego elementu podrzędnego. |
| contentMD5 | MD5 pliku. Dotyczy tylko plików. |
| — struktura | Struktura danych w tabeli bazy danych lub relacyjnych. Zwracana wartość to lista nazw kolumn i typów kolumn. |
| Columncount | Liczba kolumn w pliku lub tabeli relacyjnej. |
| Istnieje| Niezależnie od tego, czy istnieje plik, folder czy tabela. Należy zauważyć, że jeśli `exists` jest określony na liście get metadanych pole, działanie nie zakończy się niepowodzeniem, nawet jeśli plik, folder lub tabela nie istnieje. Zamiast tego `exists: false` jest zwracany w danych wyjściowych. |

>[!TIP]
>Aby sprawdzić, czy plik, folder lub tabela istnieje, określ `exists` to na liście pól Uzyskaj działanie metadanych. Następnie można sprawdzić `exists: true/false` wynik w danych wyjściowych działania. Jeśli `exists` nie jest określony na liście pól, Get Metadata działania zakończy się niepowodzeniem, jeśli obiekt nie zostanie znaleziony.

>[!NOTE]
>Po otrzymaniu metadanych z magazynów `modifiedDatetimeEnd`plików `childItems` i skonfigurować `modifiedDatetimeStart` lub , w danych wyjściowych będzie zawierać tylko pliki w danej ścieżce, które mają ostatni zmodyfikowany czas w określonym zakresie. W nie będzie zawierać elementów w podfolderach.

## <a name="syntax"></a>Składnia

**Uzyskaj aktywność metadanych**

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```

**Dataset**

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>Właściwości typu

Obecnie działanie Pobierz metadane może zwracać następujące typy informacji o metadanych:

Właściwość | Opis | Wymagany
-------- | ----------- | --------
lista pól | Typy wymaganych informacji o metadanych. Aby uzyskać szczegółowe informacje na temat obsługiwanych metadanych, zobacz sekcję [Opcje metadanych](#metadata-options) w tym artykule. | Tak 
Dataset | Zestaw danych referencyjnych, którego metadane mają być pobierane przez działanie Pobierz metadane. Zobacz [możliwości](#capabilities) sekcji informacji na temat obsługiwanych łączników. Szczegółowe informacje o składni zestawu danych można znaleźć w określonych tematach łączników. | Tak
formatYStawienia | Stosowanie podczas korzystania z zestawu danych typu formatu. | Nie
sklepyWystawy | Stosowanie podczas korzystania z zestawu danych typu formatu. | Nie

## <a name="sample-output"></a>Przykładowe dane wyjściowe

Wyniki Get Metadata są wyświetlane w danych wyjściowych działania. Poniżej przedstawiono dwa przykłady przedstawiające opcje obszernych metadanych. Aby użyć wyników w kolejnym działaniu, `@{activity('MyGetMetadataActivity').output.itemName}`użyj tego wzorca: .

### <a name="get-a-files-metadata"></a>Pobierz metadane pliku

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Pobierz metadane folderu

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o innych działaniach związanych z przepływem sterowania obsługiwanych przez fabrykę danych:

- [Wykonywanie działania potoku](control-flow-execute-pipeline-activity.md)
- [Działanie ForEach](control-flow-for-each-activity.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)
- [Aktywność w sieci Web](control-flow-web-activity.md)
