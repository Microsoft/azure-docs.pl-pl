---
title: Przekaż duże ilości danych do Azure Data Lake Storage Gen1 — metody w trybie offline
description: Usługa Import/Export służy do kopiowania danych z usługi Azure Blob Storage do Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 940b7ac90f85e0254d59459b70ccc15312cd69f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98700843"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-storage-gen1"></a>Użyj usługi Azure Import/Export do kopiowania danych w trybie offline do Data Lake Storage Gen1

W tym artykule dowiesz się, jak kopiować ogromne zestawy danych (>200 GB) do Data Lake Storage Gen1 przy użyciu metod kopiowania w trybie offline, takich jak [usługa Azure Import/Export](../import-export/storage-import-export-service.md). W odniesieniu do pliku używanego jako przykład w tym artykule przedstawiono 339 420 860 416 b lub około 319 GB na dysku. Wywołajmy ten plik 319GB. tsv.

Usługa Azure Import/Export ułatwia bezpieczne przesyłanie dużych ilości danych do usługi Azure Blob Storage przez wysyłanie dysków twardych do centrum danych platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać następujące czynności:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Storage**.
* **Konto Azure Data Lake Storage Gen1**. Aby uzyskać instrukcje dotyczące sposobu tworzenia takiego elementu, zobacz Wprowadzenie [do Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

## <a name="prepare-the-data"></a>Przygotowywanie danych

Przed rozpoczęciem korzystania z usługi Import/Export należy podzielić plik danych do przeniesienia **na kopie o rozmiarze mniejszym niż 200 GB** . Narzędzie import nie działa z plikami większymi niż 200 GB. W tym artykule podzielę plik na fragmenty 100 GB każdego z nich. Można to zrobić za pomocą [Cygwin](https://cygwin.com/install.html). Cygwin obsługuje polecenia systemu Linux. W takim przypadku należy użyć następującego polecenia:

```console
split -b 100m 319GB.tsv
```

Operacja Split tworzy pliki o następujących nazwach.

* *319GB. tsv-część-AA*
* *319GB. tsv-część-AB*
* *319GB. tsv-część-AC*
* *319GB. tsv-część-AD*

## <a name="get-disks-ready-with-data"></a>Pobierz dyski gotowe do danych

Postępuj zgodnie z instrukcjami w temacie [Korzystanie z usługi Azure Import/Export](../import-export/storage-import-export-service.md) (w sekcji **przygotowanie dysków** ), aby przygotować dyski twarde. Oto ogólna sekwencja:

1. Pozyskaj dysk twardy, który spełnia wymagania, aby można było użyć usługi Azure Import/Export.
2. Określ konto usługi Azure Storage, na którym zostaną skopiowane dane po wysłaniu do centrum danych platformy Azure.
3. Użyj [narzędzia Azure Import/Export](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), narzędzia wiersza polecenia. Oto przykładowy fragment, który pokazuje, jak używać tego narzędzia.

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    Zobacz [Korzystanie z usługi Azure Import/Export,](../import-export/storage-import-export-service.md) Aby uzyskać więcej przykładowych fragmentów kodu.
4. Poprzednie polecenie tworzy plik dziennika w określonej lokalizacji. Ten plik dziennika służy do tworzenia zadania importowania z [Azure Portal](https://portal.azure.com).

## <a name="create-an-import-job"></a>Tworzenie zadania importu

Teraz można utworzyć zadanie importu, korzystając z instrukcji w sekcji [Korzystanie z usługi Azure Import/Export](../import-export/storage-import-export-service.md) (w obszarze **Tworzenie zadania importowania** ). W przypadku tego zadania importowania z innymi szczegółami Podaj również plik dziennika utworzony podczas przygotowywania stacji dysków.

## <a name="physically-ship-the-disks"></a>Fizyczne dostarczanie dysków

Teraz możesz fizycznie dostarczyć dyski do centrum danych platformy Azure. Dane zostaną skopiowane do obiektów BLOB usługi Azure Storage, które zostały podane podczas tworzenia zadania importu. Ponadto podczas tworzenia zadania, jeśli wybrano opcję późniejszego udostępnienia informacji o śledzeniu, można teraz wrócić do zadania importowania i zaktualizować numer śledzenia.

## <a name="copy-data-from-blobs-to-data-lake-storage-gen1"></a>Kopiowanie danych z obiektów BLOB do Data Lake Storage Gen1

Po wyświetleniu przez niego stanu zadania importowania można sprawdzić, czy dane są dostępne w określonych obiektach Blob usługi Azure Storage. Następnie można użyć różnych metod, aby przenieść te dane z obiektów BLOB do Azure Data Lake Storage Gen1. Aby uzyskać wszystkie dostępne opcje przekazywania danych, zobacz pozyskiwanie [danych w Data Lake Storage Gen1](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1).

W tej sekcji udostępniamy definicje JSON, których można użyć do utworzenia potoku Azure Data Factory do kopiowania danych. Te definicje JSON można użyć z [Azure Portal](../data-factory/tutorial-copy-data-portal.md) lub [programu Visual Studio](../data-factory/tutorial-copy-data-dot-net.md).

### <a name="source-linked-service-azure-storage-blob"></a>Źródłowa usługa połączona (obiekt BLOB usługi Azure Storage)

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="target-linked-service-data-lake-storage-gen1"></a>Docelowa usługa połączona (Data Lake Storage Gen1)

```JSON
{
    "name": "AzureDataLakeStorageGen1LinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Storage Gen1 account with your access rights>",
            "dataLakeStoreUri": "https://<adlsg1_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
```

### <a name="input-data-set"></a>Zestaw danych wejściowych

```JSON
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

### <a name="output-data-set"></a>Wyjściowy zestaw danych

```JSON
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStorageGen1LinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
```

### <a name="pipeline-copy-activity"></a>Potok (działanie kopiowania)

```JSON
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
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
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

Aby uzyskać więcej informacji, zobacz [przenoszenie danych z usługi Azure Storage BLOB do Azure Data Lake Storage Gen1 przy użyciu Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-data-lake-storage-gen1"></a>Rekonstrukcja plików danych w Data Lake Storage Gen1

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Rozpocząłmy pracę z plikiem o rozmiarze 319 GB i połączysz go z plikami o mniejszych rozmiarach, aby można było je przesłać za pomocą usługi Azure Import/Export. Teraz, gdy dane są w Azure Data Lake Storage Gen1, możemy odtworzyć plik do jego oryginalnego rozmiaru. Aby to zrobić, można użyć następujących poleceń cmdlet Azure PowerShell.

```PowerShell
# Login to our account
Connect-AzAccount

# List your subscriptions
Get-AzSubscription

# Switch to the subscription you want to work with
Set-AzContext -SubscriptionId
Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzDataLakeStoreItem -AccountName "<adlsg1_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv"
```

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
* [Użyj Azure Data Lake Analytics z Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
