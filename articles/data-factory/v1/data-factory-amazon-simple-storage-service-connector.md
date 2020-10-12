---
title: Przenoszenie danych z usługi Amazon Simple Storage Service przy użyciu Data Factory
description: Dowiedz się więcej na temat przenoszenia danych z usługi Amazon Simple Storage Service (S3) za pomocą Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b23be9901df7ca435f412d9f49e1a7ad88382ade
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84711922"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Przenoszenie danych z usługi Amazon Simple Storage Service przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-amazon-simple-storage-service-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz aktualnej wersji usługi Data Factory, zobacz temat [Łącznik Amazon S3 w wersji 2](../connector-amazon-simple-storage-service.md).

W tym artykule wyjaśniono, jak za pomocą działania kopiowania w Azure Data Factory przenieść dane z usługi Amazon Simple Storage Service (S3). Jest on używany w artykule dotyczącym [przenoszenia danych](data-factory-data-movement-activities.md) , który przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania.

Dane z usługi Amazon S3 można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Data Factory obecnie obsługuje tylko przeniesienie danych z usługi Amazon S3 do innych magazynów danych, ale nie przenosi danych z innych magazynów danych do usługi Amazon S3.

## <a name="required-permissions"></a>Wymagane uprawnienia
Aby skopiować dane z usługi Amazon S3, upewnij się, że masz przyznane następujące uprawnienia:

* `s3:GetObject` i `s3:GetObjectVersion` dla operacji obiektów Amazon S3.
* `s3:ListBucket` dla operacji zasobnika Amazon S3. Jeśli używasz Kreatora kopiowania Data Factory, `s3:ListAllMyBuckets` jest również wymagany.

Aby uzyskać szczegółowe informacje na temat pełnej listy uprawnień usługi Amazon S3, zobacz [Określanie uprawnień w zasadach](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, które przenosi dane ze źródła Amazon S3 przy użyciu różnych narzędzi lub interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Aby uzyskać krótki przewodnik, zobacz [Samouczek: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

Do utworzenia potoku można także użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon Azure Resource Manager**, interfejs API **platformy .NET**i **interfejs API REST**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz **połączone usługi** , aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych.
2. Utwórz **zestawy** danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania.
3. Utwórz **potok** z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Gdy używasz Kreatora, definicje JSON dla tych Data Factory jednostek (połączone usługi, zestawy danych i potok) są automatycznie tworzone. Korzystając z narzędzi lub interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować te Data Factory jednostki przy użyciu formatu JSON. Aby uzyskać przykład z definicjami JSON dla Data Factory jednostek, które są używane do kopiowania danych z magazynu danych Amazon S3, zobacz [przykład JSON: Kopiuj dane z sekcji Amazon S3 do platformy Azure BLOB w](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) tym artykule.

> [!NOTE]
> Aby uzyskać szczegółowe informacje na temat obsługiwanych formatów plików i kompresji dla działania kopiowania, zobacz [formaty plików i kompresji w Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON, które są używane do definiowania jednostek Data Factory specyficznych dla usługi Amazon S3.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi
Połączona usługa łączy magazyn danych z fabryką danych. Utworzysz połączoną usługę typu **typu awsaccesskey** , aby połączyć magazyn danych Amazon S3 z fabryką danych. Poniższa tabela zawiera opis elementów JSON specyficznych dla połączonej usługi Amazon S3 (typu awsaccesskey).

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| accessKeyID |Identyfikator klucza dostępu tajnego. |ciąg |Tak |
| secretAccessKey |Sam klucz dostępu tajnego. |Zaszyfrowany ciąg tajny |Tak |

>[!NOTE]
>Ten łącznik wymaga kluczy dostępu dla konta usługi IAM do kopiowania danych z usługi Amazon S3. [Tymczasowe poświadczenia zabezpieczeń](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) nie są obsługiwane.
>

Oto przykład:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby określić zestaw danych do reprezentowania danych wejściowych w usłudze Azure Blob Storage, ustaw właściwość Type zestawu danych na **AmazonS3**. Ustaw właściwość **linkedServiceName** zestawu danych na nazwę połączonej usługi Amazon S3. Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz [Tworzenie zestawów danych](data-factory-create-datasets.md). 

Sekcje takie jak struktura, dostępność i zasady są podobne do wszystkich typów zestawów danych (takich jak SQL Database, Azure BLOB i Azure Table). Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje dotyczące lokalizacji danych w magazynie danych. Sekcja **typeProperties** zestawu danych typu **AmazonS3** (który zawiera zestaw danych Amazon S3) ma następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| zasobnikname |Nazwa zasobnika S3. |Ciąg |Tak |
| key |Klucz obiektu S3. |Ciąg |Nie |
| prefiks |Prefiks klucza obiektu S3. Zaznaczone obiekty, których klucze zaczynają się od tego prefiksu. Stosuje się tylko wtedy, gdy klucz jest pusty. |Ciąg |Nie |
| Wersja |Wersja obiektu S3, jeśli włączono obsługę wersji S3. |Ciąg |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **Type** w polu Format na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz sekcję [Format tekstu](data-factory-supported-file-and-compression-formats.md#text-format), [Format JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Format Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Format Orc](data-factory-supported-file-and-compression-formats.md#orc-format)i [Parquet format](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Jeśli chcesz skopiować pliki między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych. | |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **gzip**, **Wklęśnięcie**, **BZip2**i **ZipDeflate**. Obsługiwane poziomy to: **optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). | |Nie |


> [!NOTE]
> **zasobnikname + Key** określa lokalizację obiektu S3, gdzie zasobnik jest kontenerem głównym dla obiektów S3, a klucz jest pełną ścieżką do obiektu S3.

### <a name="sample-dataset-with-prefix"></a>Przykładowy zestaw danych z prefiksem

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "testbucket",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
### <a name="sample-dataset-with-version"></a>Przykładowy zestaw danych (z wersją)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "testbucket",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="dynamic-paths-for-s3"></a>Ścieżki dynamiczne dla S3
Poprzedni przykład korzysta z ustalonych wartości właściwości **Key** i **bucketName** w zestawie danych Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Można Data Factory obliczać te właściwości dynamicznie w czasie wykonywania przy użyciu zmiennych systemowych, takich jak parametru slicestart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Można to zrobić w przypadku właściwości **prefix** zestawu danych Amazon S3. Aby uzyskać listę obsługiwanych funkcji i zmiennych, zobacz [Data Factory funkcje i zmienne systemowe](data-factory-functions-variables.md).

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Tworzenie potoków](data-factory-create-pipelines.md). Właściwości, takie jak nazwa, opis, tabele wejściowe i wyjściowe, oraz zasady są dostępne dla wszystkich typów działań. Właściwości dostępne w sekcji **typeProperties** działania różnią się w zależności od typu działania. W przypadku działania kopiowania właściwości różnią się w zależności od typów źródeł i ujścia. Gdy źródło w działaniu kopiowania jest typu **FileSystemSource** (w tym Amazon S3), w sekcji **typeProperties** jest dostępna następująca Właściwość:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| rozpoznawania |Określa, czy w katalogu mają być cykliczne listy obiektów S3. |PRAWDA/FAŁSZ |Nie |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>Przykład JSON: kopiowanie danych z usługi Amazon S3 do magazynu Azure Blob Storage
Ten przykład pokazuje, jak skopiować dane z usługi Amazon S3 do magazynu obiektów blob platformy Azure. Dane można jednak skopiować bezpośrednio do [dowolnego ujścia, które są obsługiwane](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w Data Factory.

Przykład zawiera definicje JSON dla następujących jednostek Data Factory. Za pomocą tych definicji można utworzyć potok służący do kopiowania danych z usługi Amazon S3 do usługi BLOB Storage przy użyciu programu [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).   

* Połączona usługa typu [typu awsaccesskey](#linked-service-properties).
* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AmazonS3](#dataset-properties).
* Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [FileSystemSource](#copy-activity-properties) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowo kopiuje dane z usługi Amazon S3 do obiektu blob platformy Azure co godzinę. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

### <a name="amazon-s3-linked-service"></a>Połączona usługa Amazon S3

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="amazon-s3-input-dataset"></a>Zestaw danych wejściowych usługi Amazon S3

Ustawienie **"External": true** informuje usługę Data Factory, że zestaw danych jest zewnętrzny względem fabryki danych. Ustaw dla tej właściwości wartość true dla wejściowego zestawu danych, który nie jest tworzony przez działanie w potoku.

```json
    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }
```


### <a name="azure-blob-output-dataset"></a>Wyjściowy zestaw danych obiektów blob platformy Azure

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu dla obiektu BLOB jest obliczana dynamicznie na podstawie czasu rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części roku, miesiąca, dnia i godziny rozpoczęcia.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Działanie kopiowania w potoku za pomocą źródła Amazon S3 i ujścia obiektów BLOB

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **FileSystemSource**, a typ **ujścia** to **wartość blobsink**.

```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource",
                        "recursive": true
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AmazonS3InputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AmazonS3ToBlob"
            }
        ],
        "start": "2014-08-08T18:00:00Z",
        "end": "2014-08-08T19:00:00Z"
    }
}
```
> [!NOTE]
> Aby zmapować kolumny ze źródłowego zestawu danych do kolumn z zestawu danych ujścia, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

* Aby dowiedzieć się więcej na temat kluczowych czynników wpływających na wydajność przenoszenia danych (działanie kopiowania) w Data Factory i różne sposoby jego optymalizacji, zobacz [Przewodnik dotyczący wydajności i dostrajania działania kopiowania](data-factory-copy-activity-performance.md).

* Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
