---
title: Przenoszenie danych z usługi Amazon RedShift przy użyciu Azure Data Factory
description: Dowiedz się, jak przenieść dane z usługi Amazon RedShift za pomocą działania kopiowania Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 290990e312a7f591539686ecce1eec1ac742dd60
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999301"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Przenoszenie danych z usługi Amazon RedShift przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-amazon-redshift-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-amazon-redshift.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz temat [łącznika Amazon RedShift w wersji 2](../connector-amazon-redshift.md).

W tym artykule wyjaśniono, jak za pomocą działania kopiowania w Azure Data Factory przenieść dane z usługi Amazon RedShift. Artykuł korzysta z artykułu dotyczącego [przenoszenia danych](data-factory-data-movement-activities.md) , który przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania.

Data Factory obecnie obsługuje tylko przeniesienie danych z usługi Amazon RedShift do [obsługiwanego magazynu danych ujścia](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Przeniesienie danych z innych magazynów danych do usługi Amazon RedShift nie jest obsługiwane.

> [!TIP]
> Aby osiągnąć najlepszą wydajność podczas kopiowania dużych ilości danych z usługi Amazon RedShift, rozważ użycie wbudowanego polecenia **Unload** RedShift w usłudze Amazon Simple Storage (Amazon S3). Aby uzyskać szczegółowe informacje, zobacz [Używanie Unload do kopiowania danych z usługi Amazon RedShift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Wymagania wstępne
* Jeśli przenosisz dane do lokalnego magazynu danych, zainstaluj [bramę zarządzanie danymi](data-factory-data-management-gateway.md) na maszynie lokalnej. Udziel dostępu bramy do klastra Amazon RedShift przy użyciu lokalnego adresu IP maszyny. Aby uzyskać instrukcje, zobacz [Autoryzuj dostęp do klastra](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Aby przenieść dane do magazynu danych platformy Azure, zobacz [adres IP obliczeń i zakresy SQL, które są używane przez Microsoft Azure centrów](https://www.microsoft.com/download/details.aspx?id=41653)zasobów.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, aby przenieść dane ze źródła Amazon RedShift przy użyciu różnych narzędzi i interfejsów API.

Najprostszym sposobem tworzenia potoku jest użycie Kreatora kopiowania Azure Data Factory. Aby uzyskać szybki Przewodnik dotyczący tworzenia potoku przy użyciu Kreatora kopiowania, zobacz [Samouczek: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

Potok można również utworzyć przy użyciu programu Visual Studio, Azure PowerShell lub innych narzędzi. Do utworzenia potoku można także użyć szablonów Azure Resource Manager, interfejsu API platformy .NET lub interfejsu API REST. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz połączone usługi, aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych.
2. Utwórz zestawy danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania.
3. Utwórz potok z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

W przypadku używania Kreatora kopiowania definicje JSON dla tych Data Factory jednostek są tworzone automatycznie. Korzystając z narzędzi lub interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować jednostki Data Factory przy użyciu formatu JSON. Przykład JSON: kopiowanie danych z Amazon RedShift do usługi Azure Blob Storage pokazuje definicje JSON dla jednostek Data Factory, które są używane do kopiowania danych z magazynu danych Amazon RedShift.

W poniższych sekcjach opisano właściwości JSON, które są używane do definiowania jednostek Data Factory dla usługi Amazon RedShift.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Poniższa tabela zawiera opisy elementów JSON, które są specyficzne dla połączonej usługi Amazon RedShift.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| **Wprowadź** |Ta właściwość musi być ustawiona na **AmazonRedshift**. |Tak |
| **Server** |Adres IP lub nazwa hosta serwera Amazon RedShift. |Tak |
| **przewożąc** |Numer portu TCP używanego przez serwer Amazon RedShift do nasłuchiwania połączeń klientów. |Nie (domyślnie 5439) |
| **Database** |Nazwa bazy danych Amazon RedShift. |Tak |
| **uż** |Nazwa użytkownika, który ma dostęp do bazy danych. |Tak |
| **hasło** |Hasło konta użytkownika. |Tak |

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby zapoznać się z listą sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych](data-factory-create-datasets.md) . Sekcje **struktury**, **dostępności** i **zasad** są podobne do wszystkich typów zestawów danych. Przykłady typów zestawów danych: Azure SQL, Azure Blob Storage i Azure Table Storage.

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje dotyczące lokalizacji danych w sklepie. Sekcja **typeProperties** dla zestawu danych typu **relacyjnego**, który zawiera zestaw danych Amazon RedShift, ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| **tableName** |Nazwa tabeli w bazie danych Amazon RedShift, do której odwołuje się połączona usługa. |Nie (Jeśli określono Właściwość **zapytania** działania Copy typu **RelationalSource** ) |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby zapoznać się z listą sekcji i właściwości, które są dostępne do definiowania działań, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . **Nazwa**, **Opis**, tabela **danych wejściowych** , tabela **wyników** i właściwości **zasad** są dostępne dla wszystkich typów działań. Właściwości, które są dostępne w sekcji **typeProperties** , różnią się w zależności od typu działania. W przypadku działania kopiowania właściwości różnią się w zależności od typów źródeł danych i ujścia.

W przypadku działania kopiowania, gdy źródło jest typu **AmazonRedshiftSource**, w sekcji **typeProperties** są dostępne następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| **dotyczących** | Użyj zapytania niestandardowego, aby odczytać dane. |Nie (Jeśli określono Właściwość **TableName** zestawu danych) |
| **redshiftUnloadSettings** | Zawiera grupę właściwości przy użyciu polecenia RedShift **Unload** . | Nie |
| **s3LinkedServiceName** | Firma Amazon S3, która będzie używana jako magazyn tymczasowy. Połączona usługa jest określona przy użyciu nazwy Azure Data Factory typu **typu awsaccesskey**. | Wymagane w przypadku użycia właściwości **redshiftUnloadSettings** |
| **zasobnikname** | Wskazuje zasobnik usługi Amazon S3, który ma być używany do przechowywania danych tymczasowych. Jeśli ta właściwość nie jest określona, działanie Copy automatycznie generuje zasobnik. | Wymagane w przypadku użycia właściwości **redshiftUnloadSettings** |

Alternatywnie można użyć typu **RelationalSource** , który obejmuje Amazon RedShift, z następującą właściwością w sekcji **typeProperties** . Uwaga Ten typ źródła nie obsługuje polecenia RedShift **Unload** .

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| **dotyczących** |Użyj zapytania niestandardowego, aby odczytać dane. | Nie (Jeśli określono Właściwość **TableName** zestawu danych) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Korzystanie z usługi UNLOAD do kopiowania danych z usługi Amazon RedShift

Polecenie Amazon RedShift [**unload Zwalnia**](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) wyniki zapytania do co najmniej jednego pliku w witrynie Amazon S3. To polecenie jest zalecane przez Amazon do kopiowania dużych zestawów danych z RedShift.

**Przykład: kopiowanie danych z Amazon RedShift do usługi Azure Synapse Analytics (dawniej SQL Data Warehouse)**

Ten przykład kopiuje dane z Amazon RedShift do usługi Azure Synapse Analytics. W przykładzie zastosowano polecenie RedShift **Unload** , przygotowane dane kopiowania i bazę danych firmy Microsoft.

W przypadku tego przykładowego przypadku użycia działanie Copy najpierw zwalnia dane z usługi Amazon RedShift do usługi Amazon S3 zgodnie z konfiguracją w opcji  **redshiftUnloadSettings** . Następnie dane są kopiowane z usługi Amazon S3 do magazynu Azure Blob Storage, jak określono w opcji **stagingSettings** . Na koniec baza danych ładuje dane do usługi Azure Synapse Analytics. Wszystkie formaty pośrednie są obsługiwane przez działanie kopiowania.

![Kopiowanie przepływu pracy z programu Amazon RedShift do usługi Azure Synapse Analytics](media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>Przykład JSON: kopiowanie danych z Amazon RedShift do magazynu obiektów blob platformy Azure
Ten przykład pokazuje, jak skopiować dane z bazy danych Amazon RedShift do usługi Azure Blob Storage. Dane można kopiować bezpośrednio do dowolnego [obsługiwanego ujścia](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania.

Przykład zawiera następujące jednostki fabryki danych:

* Połączona usługa typu [AmazonRedshift](#linked-service-properties)
* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [relacyjnego](#dataset-properties)
* Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa właściwości [RelationalSource](#copy-activity-properties) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)

Przykład kopiuje dane z wyniku zapytania w usłudze Amazon RedShift do obiektu blob platformy Azure co godzinę. Właściwości JSON, które są używane w przykładzie, są opisane w sekcjach, które są zgodne z definicjami jednostek.

**Połączona usługa Amazon RedShift**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": "<The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>",
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Połączona usługa Azure Blob Storage**

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
**Zestaw danych wejściowych RedShift Amazon**

Właściwość **zewnętrzna** ma wartość "true", aby poinformować usługę Data Factory, że zestaw danych jest zewnętrzny dla fabryki danych. To ustawienie właściwości wskazuje, że zestaw danych nie jest generowany przez działanie w fabryce danych. Ustaw właściwość na wartość true dla wejściowego zestawu danych, który nie jest tworzony przez działanie w potoku.

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Wyjściowy zestaw danych obiektów blob platformy Azure**

Dane są zapisywane w nowym obiekcie blob co godzinę przez ustawienie właściwości **częstotliwość** na wartość "godzina" i Właściwość **Interwał** na 1. Właściwość **folderPath** obiektu BLOB jest obliczana dynamicznie. Wartość właściwości jest oparta na godzinie rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części roku, miesiąca, dnia i godziny rozpoczęcia.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Działanie kopiowania w potoku za pomocą źródła RedShift platformy Azure (typu RelationalSource) i ujścia obiektów blob platformy Azure**

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych. Zaplanowano uruchomienie potoku co godzinę. W definicji JSON dla potoku, typ **źródła** ma wartość **RelationalSource** , a typ **ujścia** to **wartość blobsink**. Zapytanie SQL określone dla właściwości **zapytania** wybiera dane do skopiowania z ostatniej godziny.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
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
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Mapowanie typu dla Amazon RedShift
Jak wspomniano w artykule [działania związane z przenoszeniem danych](data-factory-data-movement-activities.md) , działanie kopiowania wykonuje konwersje typów automatycznych z typu źródłowego na typ ujścia. Typy są konwertowane przy użyciu podejścia dwuetapowego:

1. Konwertuj z natywnego typu źródła na typ .NET
2. Konwertuj z typu .NET na natywny typ ujścia

Następujące mapowania są używane, gdy działanie kopiowania konwertuje dane z typu Amazon RedShift na typ .NET:

| Typ RedShift Amazon | Typ .NET |
| --- | --- |
| SMALLINT |Int16 |
| LICZBA CAŁKOWITA |Int32 |
| BIGINT |Int64 |
| DOKŁADNOŚCI |Liczba dziesiętna |
| LICZBA RZECZYWISTA |Pojedyncze |
| PODWÓJNA PRECYZJA |Double |
| TYPU |Ciąg |
| DELIKATN |Ciąg |
| VARCHAR |Ciąg |
| DATE |DateTime |
| ZNACZNIK czasu |DateTime |
| TEKST |Ciąg |

## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby dowiedzieć się, jak mapować kolumny w źródłowym zestawie danych na kolumny w zestawie danych ujścia, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Powtarzalne odczyty ze źródeł relacyjnych
Podczas kopiowania danych z magazynu danych relacyjnych należy pamiętać o powtarzaniu, aby uniknąć niezamierzonych wyników. W Azure Data Factory można ręcznie uruchomić ponownie wycinka. Możesz również skonfigurować **zasady** ponawiania dla zestawu danych, aby ponownie uruchomić wycinek w przypadku wystąpienia błędu. Upewnij się, że odczytane są te same dane, niezależnie od tego, ile razy zostanie uruchomiony wycink. Upewnij się również, że te same dane są odczytywane niezależnie od sposobu ponownego uruchomienia wycinka. Aby uzyskać więcej informacji, zobacz [powtarzalne odczyty ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Poznaj kluczowe czynniki wpływające na wydajność działania kopiowania i sposoby optymalizacji wydajności w przewodniku dotyczącym [wydajności i dostrajania działania kopiowania](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
