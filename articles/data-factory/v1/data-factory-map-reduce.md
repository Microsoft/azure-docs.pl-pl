---
title: Wywołaj program MapReduce z Azure Data Factory
description: Dowiedz się, jak przetwarzać dane przez uruchamianie programów MapReduce w klastrze usługi Azure HDInsight z fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.assetid: c34db93f-570a-44f1-a7d6-00390f4dc0fa
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 8bdcaf20330a3700681fd96f858370dd7dcdf4c7
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495433"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Wywoływanie programów MapReduce z Data Factory
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
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Przekształć dane przy użyciu działania MapReduce w Data Factory](../transform-data-using-hadoop-map-reduce.md).


Działanie MapReduce usługi HDInsight w [potoku](data-factory-create-pipelines.md) Data Factory wykonuje programy MapReduce na [własnym](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) w klastrze HDInsight opartym na systemie Windows/Linux. W tym artykule przedstawiono artykuł [działania przekształcania danych](data-factory-data-transformation-activities.md) , który zawiera ogólne omówienie transformacji danych i obsługiwanych działań transformacji.

> [!NOTE] 
> Jeśli dopiero zaczynasz Azure Data Factory, przeczytaj artykuł [wprowadzenie do Azure Data Factory](data-factory-introduction.md) i wykonaj samouczek: [Kompiluj pierwszy potok danych](data-factory-build-your-first-pipeline.md) przed przeczytaniem tego artykułu.  

## <a name="introduction"></a>Wprowadzenie
Potok w usłudze Azure Data Factory przetwarza dane w połączonych usługach magazynu za pomocą połączonych usług obliczeniowych. Zawiera sekwencję działań, w których każde działanie wykonuje określoną operację przetwarzania. W tym artykule opisano użycie działania HDInsight MapReduce.

Zobacz [świnie](data-factory-pig-activity.md) i [Hive](data-factory-hive-activity.md) , aby uzyskać szczegółowe informacje na temat uruchamiania skryptów świń/Hive w klastrze usługi HDInsight opartym na systemie Windows/Linux z potoku przy użyciu działań dotyczących trzody chlewnej i usługi HDInsight. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON dla działania MapReduce usługi HDInsight
W definicji JSON dla działania usługi HDInsight: 

1. Ustaw **Typ** **działania** na **HDInsight**.
2. Określ nazwę klasy dla właściwości **ClassName** .
3. Określ ścieżkę do pliku JAR, łącznie z nazwą pliku dla właściwości **jarFilePath** .
4. Określ połączoną usługę odwołującą się do Blob Storage platformy Azure, która zawiera plik JAR dla właściwości **jarLinkedService** .   
5. Określ wszelkie argumenty dla programu MapReduce w sekcji **argumenty** . W czasie wykonywania zobaczysz kilka dodatkowych argumentów (na przykład: MapReduce. job. Tags) ze środowiska MapReduce Framework. Aby odróżnić argumenty od argumentów MapReduce, należy rozważyć użycie obu opcji i wartości jako argumentów, jak pokazano w poniższym przykładzie (-s,--Input,--Output itp., są opcjami natychmiast po ich wartości).

    ```JSON   
    {
        "name": "MahoutMapReduceSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "-s",
                            "SIMILARITY_LOGLIKELIHOOD",
                            "--input",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                            "--output",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                            "--maxSimilaritiesPerItem",
                            "500",
                            "--tempDir",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                        ]
                    },
                    "inputs": [
                        {
                            "name": "MahoutInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "MahoutOutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MahoutActivity",
                    "description": "Custom Map Reduce to generate Mahout result",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-01-03T00:00:00Z",
            "end": "2017-01-04T00:00:00Z"
        }
    }
    ```
   Możesz użyć działania MapReduce usługi HDInsight, aby uruchomić dowolny plik JAR MapReduce w klastrze usługi HDInsight. W poniższej przykładowej definicji JSON potoku, działanie usługi HDInsight jest skonfigurowane do uruchamiania pliku JAR Mahout.

## <a name="sample-on-github"></a>Przykład w witrynie GitHub
Możesz pobrać przykład użycia działania HDInsight MapReduce z: [Data Factory Samples w witrynie GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Uruchamianie programu liczenia wyrazów
Potok w tym przykładzie powoduje uruchomienie programu Word Count map/Zmniejsz program w klastrze usługi Azure HDInsight.   

### <a name="linked-services"></a>Połączone usługi
Najpierw Utwórz połączoną usługę, aby połączyć magazyn platformy Azure używany przez klaster usługi Azure HDInsight z fabryką danych Azure. Jeśli skopiujesz/wkleisz Poniższy kod, nie zapomnij zastąpić **nazwy konta** i **klucza konta** nazwą i kluczem usługi Azure Storage. 

#### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage

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
Następnie utworzysz połączoną usługę, aby połączyć klaster usługi Azure HDInsight z fabryką danych Azure. Jeśli skopiujesz/wkleisz Poniższy kod, Zastąp **nazwę klastra usługi HDInsight** nazwą klastra HDInsight i zmień wartości nazwy użytkownika i hasła.   

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
Potok w tym przykładzie nie przyjmuje żadnych danych wejściowych. Należy określić wyjściowy zestaw danych dla działania HDInsight MapReduce. Ten zestaw danych jest tylko fikcyjnym zestawem danych, który jest wymagany do przekierowania harmonogramu potoku.  

```JSON
{
    "name": "MROutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "WordCountOutput1.txt",
            "folderPath": "example/data/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Potok
Potok w tym przykładzie ma tylko jedno działanie, którego typem jest: HDInsightMapReduce. Niektóre z ważnych właściwości w formacie JSON są następujące: 

| Właściwość | Uwagi |
|:--- |:--- |
| typ |Typ musi być ustawiony na **HDInsightMapReduce**. |
| Nazwą |Nazwa klasy to: **WORDCOUNT** |
| jarFilePath |Ścieżka do pliku JAR zawierającego klasę. Jeśli skopiujesz/wkleisz Poniższy kod, nie zapomnij zmienić nazwy klastra. |
| jarLinkedService |Połączona usługa Azure Storage, która zawiera plik JAR. Ta połączona usługa odwołuje się do magazynu skojarzonego z klastrem usługi HDInsight. |
| argumentu |Program WORDCOUNT przyjmuje dwa argumenty, dane wejściowe i wyjściowe. Plik wejściowy jest plikiem davinci.txt. |
| frequency/interval |Wartości tych właściwości pasują do wyjściowego zestawu danych. |
| linkedServiceName |odwołuje się do utworzonej wcześniej usługi HDInsight. |

```JSON
{
    "name": "MRSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run the Word Count Program",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "wordcount",
                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": [
                        "/example/data/gutenberg/davinci.txt",
                        "/example/data/WordCountOutput1"
                    ]
                },
                "outputs": [
                    {
                        "name": "MROutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "MRActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-03T00:00:00Z",
        "end": "2014-01-04T00:00:00Z"
    }
}
```

## <a name="run-spark-programs"></a>Uruchamianie programów Spark
Możesz użyć działania MapReduce, aby uruchomić programy platformy Spark w klastrze usługi HDInsight Spark. Zobacz [Wywoływanie programów platformy Spark z usługi Azure Data Factory](data-factory-spark.md).  

[developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: /previous-versions/azure/dn834987(v=azure.100)
[Azure Portal]: https://portal.azure.com

## <a name="see-also"></a>Zobacz też
* [Działanie Hive](data-factory-hive-activity.md)
* [Aktywność trzody chlewnej](data-factory-pig-activity.md)
* [Działanie przesyłania strumieniowego Hadoop](data-factory-hadoop-streaming-activity.md)
* [Wywoływanie programów platformy Spark](data-factory-spark.md)
* [Wywoływanie skryptów języka R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)