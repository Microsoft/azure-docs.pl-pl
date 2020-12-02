---
title: Przekształcanie danych za pomocą działania Hadoop Streaming — Azure
description: Dowiedz się, jak używać działania przesyłania strumieniowego Hadoop w usłudze Azure Data Factory w celu przekształcania danych przez uruchamianie programów przesyłania strumieniowego Hadoop na żądanie/własny klaster usługi HDInsight.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 80f78e3d2e7fdcd8fef53ca0412676a37c6486c2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495603"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu działania usługi Hadoop streaming w Azure Data Factory
> [!div class="op_single_selector" title1="Działania transformacji"]
> * [Działanie Hive](data-factory-hive-activity.md) 
> * [Aktywność trzody chlewnej](data-factory-pig-activity.md)
> * [Działanie MapReduce](data-factory-map-reduce.md)
> * [Działanie przesyłania strumieniowego Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Działanie platformy Spark](data-factory-spark.md)
> * [Działanie wykonywania wsadowego w usłudze Azure Machine Learning Studio (wersja klasyczna)](data-factory-azure-ml-batch-execution-activity.md)
> * [Działanie aktualizacji zasobów w usłudze Azure Machine Learning Studio (wersja klasyczna)](data-factory-azure-ml-update-resource-activity.md)
> * [Działania procedur składowanych](data-factory-stored-proc-activity.md)
> * [Działania języka U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md)
> * [Niestandardowe działanie platformy .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Przekształć dane przy użyciu działania Hadoop streaming w Data Factory](../transform-data-using-hadoop-streaming.md).


Za pomocą działania HDInsightStreamingActivity można wywołać zadanie przesyłania strumieniowego Hadoop z potoku Azure Data Factory. Poniższy fragment kodu JSON przedstawia składnię używania HDInsightStreamingActivity w pliku JSON potoku. 

Działanie przesyłania strumieniowego usługi HDInsight w [potoku](data-factory-create-pipelines.md) Data Factory wykonuje programy przesyłania strumieniowego Hadoop we [własnym lub lokalnym klastrze usługi](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) HDInsight opartym na systemie Windows/Linux. [on-demand](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) W tym artykule przedstawiono artykuł [działania przekształcania danych](data-factory-data-transformation-activities.md) , który zawiera ogólne omówienie transformacji danych i obsługiwanych działań transformacji.

> [!NOTE] 
> Jeśli dopiero zaczynasz Azure Data Factory, przeczytaj artykuł [wprowadzenie do Azure Data Factory](data-factory-introduction.md) i wykonaj samouczek: [Kompiluj pierwszy potok danych](data-factory-build-your-first-pipeline.md) przed przeczytaniem tego artykułu. 

## <a name="json-sample"></a>Przykład JSON
Klaster usługi HDInsight jest automatycznie wypełniany przy użyciu przykładowych programów (wc.exe i cat.exe) i danych (davinci.txt). Domyślnie nazwa kontenera, który jest używany przez klaster usługi HDInsight, jest nazwą klastra. Na przykład jeśli nazwa klastra to myhdicluster, nazwa skojarzonego kontenera obiektów BLOB byłaby myhdicluster. 

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<nameofthecluster>/example/apps/wc.exe",
                        "<nameofthecluster>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "AzureStorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00Z",
        "end": "2014-01-05T00:00:00Z"
    }
}
```

Pamiętaj o następujących kwestiach:

1. Ustaw **linkedServiceName** na nazwę połączonej usługi, która wskazuje na klaster HDInsight, na którym uruchamiane jest zadanie przesyłania strumieniowego MapReduce.
2. Ustaw typ działania na **HDInsightStreaming**.
3. Dla właściwości **Mapowanie** Określ nazwę pliku wykonywalnego mapowania. W przykładzie cat.exe jest plikiem wykonywalnym mapowania.
4. Dla właściwości **Redukuj** Określ nazwę pliku wykonywalnego. W przykładzie wc.exe jest plikiem wykonywalnym.
5. Dla właściwości Typ **danych wejściowych** Określ plik wejściowy (łącznie z lokalizacją) dla mapowania. W przykładzie: `wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt` : adfsample jest kontenerem obiektów blob, przykładem/danymi/Gutenberg jest folder, a davinci.txt jest obiektem BLOB.
6. Dla właściwości Typ **danych wyjściowych** Określ plik wyjściowy (łącznie z lokalizacją) dla elementu zmniejszającego liczbę. Dane wyjściowe zadania przesyłania strumieniowego Hadoop są zapisywane w lokalizacji określonej dla tej właściwości.
7. W sekcji **filePaths** Określ ścieżki dla plików wykonywalnych mapowania i redukcji. W przykładzie: "adfsample/example/Apps/wc.exe", adfsample jest kontenerem obiektów blob, przykładem/aplikacjami jest folder, a wc.exe jest plikiem wykonywalnym.
8. Dla właściwości **fileLinkedService** Określ połączoną usługę Azure Storage, która reprezentuje magazyn platformy Azure, który zawiera pliki określone w sekcji filePaths.
9. Dla właściwości **argumenty** określ argumenty zadania przesyłania strumieniowego.
10. Właściwość **GetDebugInfo —** jest elementem opcjonalnym. Jeśli jest ustawiona na niepowodzenie, dzienniki są pobierane tylko w przypadku niepowodzenia. Gdy ustawienie ma wartość zawsze, dzienniki są zawsze pobierane niezależnie od stanu wykonania.

> [!NOTE]
> Jak pokazano w przykładzie, należy określić wyjściowy zestaw danych dla działania Hadoop przesyłania strumieniowego **dla właściwości** Outputs. Ten zestaw danych jest tylko fikcyjnym zestawem danych, który jest wymagany do przekierowania harmonogramu potoku. Nie trzeba określać żadnego wejściowego zestawu danych dla działania dla właściwości **dane wejściowe** .  
> 
> 

## <a name="example"></a>Przykład
Potok w tym instruktażu uruchamia mapę przesyłania strumieniowego programu Word Count/Zmniejsz program w klastrze usługi Azure HDInsight. 

### <a name="linked-services"></a>Połączone usługi
#### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
Najpierw Utwórz połączoną usługę, aby połączyć magazyn platformy Azure używany przez klaster usługi Azure HDInsight z fabryką danych Azure. Jeśli skopiujesz/wkleisz Poniższy kod, nie zapomnij zastąpić nazwy konta i klucza konta nazwą i kluczem usługi Azure Storage. 

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Połączona usługa Azure HDInsight
Następnie utworzysz połączoną usługę, aby połączyć klaster usługi Azure HDInsight z fabryką danych Azure. Jeśli skopiujesz/wkleisz Poniższy kod, zastąp nazwę klastra usługi HDInsight nazwą klastra HDInsight i zmień wartości nazwy użytkownika i hasła. 

```JSON
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
            "userName": "admin",
            "password": "**********",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

### <a name="datasets"></a>Zestawy danych
#### <a name="output-dataset"></a>Wyjściowy zestaw danych
Potok w tym przykładzie nie przyjmuje żadnych danych wejściowych. Należy określić wyjściowy zestaw danych dla działania przesyłania strumieniowego usługi HDInsight. Ten zestaw danych jest tylko fikcyjnym zestawem danych, który jest wymagany do przekierowania harmonogramu potoku. 

```JSON
{
    "name": "StreamingOutputDataset",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "adftutorial/streamingdata/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Potok
Potok w tym przykładzie ma tylko jedno działanie, którego typem jest: **HDInsightStreaming**. 

Klaster usługi HDInsight jest automatycznie wypełniany przy użyciu przykładowych programów (wc.exe i cat.exe) i danych (davinci.txt). Domyślnie nazwa kontenera, który jest używany przez klaster usługi HDInsight, jest nazwą klastra. Na przykład jeśli nazwa klastra to myhdicluster, nazwa skojarzonego kontenera obiektów BLOB byłaby myhdicluster.  

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<blobcontainer>/example/apps/wc.exe",
                        "<blobcontainer>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "StorageLinkedService"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00Z",
        "end": "2017-01-04T00:00:00Z"
    }
}
```
## <a name="see-also"></a>Zobacz też
* [Działanie Hive](data-factory-hive-activity.md)
* [Aktywność trzody chlewnej](data-factory-pig-activity.md)
* [Działanie MapReduce](data-factory-map-reduce.md)
* [Wywoływanie programów platformy Spark](data-factory-spark.md)
* [Wywoływanie skryptów języka R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

