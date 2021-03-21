---
title: Przekształcanie danych przy użyciu działania programu Hive — Azure
description: Dowiedz się, jak używać działania programu Hive w Azure Data Factory V1, aby uruchamiać zapytania Hive na żądanie/własny klaster usługi HDInsight.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9d14ddb172546e062b62a5a8dd98b49a0a6e1c6f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383028"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu działania programu Hive w Azure Data Factory 
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
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Przekształć dane przy użyciu działania programu Hive w Data Factory](../transform-data-using-hadoop-hive.md).

Działanie programu Hive w [usłudze](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight w [potoku](data-factory-create-pipelines.md) Data Factory wykonuje zapytania Hive na [własnym lub lokalnym klastrze usługi](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) HDInsight opartym na systemie Windows/Linux. W tym artykule przedstawiono artykuł [działania przekształcania danych](data-factory-data-transformation-activities.md) , który zawiera ogólne omówienie transformacji danych i obsługiwanych działań transformacji.

> [!NOTE] 
> Jeśli dopiero zaczynasz Azure Data Factory, przeczytaj artykuł [wprowadzenie do Azure Data Factory](data-factory-introduction.md) i wykonaj samouczek: [Kompiluj pierwszy potok danych](data-factory-build-your-first-pipeline.md) przed przeczytaniem tego artykułu. 

## <a name="syntax"></a>Składnia

```JSON
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```
## <a name="syntax-details"></a>Szczegóły składni
| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| name |Nazwa działania |Tak |
| description (opis) |Tekst opisujący działanie używanego działania |Nie |
| typ |HDinsightHive |Tak |
| danych wejściowych |Dane wejściowe używane przez działanie Hive |Nie |
| wydajności |Dane wyjściowe generowane przez działanie Hive |Tak |
| linkedServiceName |Odwołanie do klastra usługi HDInsight zarejestrowanego jako połączona usługa w Data Factory |Tak |
| skrypt |Określ skrypt Hive w tekście |Nie |
| scriptPath |Zapisz skrypt Hive w magazynie obiektów blob platformy Azure i podaj ścieżkę do pliku. Użyj właściwości "Script" lub "scriptPath". Nie można jednocześnie używać obu tych metod. W nazwie pliku rozróżniana jest wielkość liter. |Nie |
| definiuje |Określ parametry jako pary klucz/wartość dla odwołania w skrypcie Hive przy użyciu elementu "hiveconf" |Nie |

## <a name="example"></a>Przykład
Rozważmy przykładową analizę dzienników gier, w której chcesz zidentyfikować czas spędzony przez użytkowników, którzy odgrywają gry uruchomione przez firmę. 

Następujący dziennik to Przykładowy dziennik gier, który jest rozdzielony przecinkami ( `,` ) i zawiera następujące pola — ProfileID, SessionStart, Duration, SrcIPAddress i GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

**Skrypt Hive** służący do przetwarzania tych danych:

```
DROP TABLE IF EXISTS HiveSampleIn; 
CREATE EXTERNAL TABLE HiveSampleIn 
(
    ProfileID        string, 
    SessionStart     string, 
    Duration         int, 
    SrcIPAddress     string, 
    GameType         string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 

DROP TABLE IF EXISTS HiveSampleOut; 
CREATE EXTERNAL TABLE HiveSampleOut 
(    
    ProfileID     string, 
    Duration     int
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';

INSERT OVERWRITE TABLE HiveSampleOut
Select 
    ProfileID,
    SUM(Duration)
FROM HiveSampleIn Group by ProfileID
```

Aby wykonać ten skrypt Hive w potoku Data Factory, należy wykonać następujące czynności

1. Utwórz połączoną usługę, aby zarejestrować [własny klaster obliczeniowy usługi HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) lub skonfigurować [klaster obliczeniowy usługi HDInsight na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Zadzwońmy do tej połączonej usługi "HDInsightLinkedService".
2. Utwórz [połączoną usługę](data-factory-azure-blob-connector.md) , aby skonfigurować połączenie z magazynem obiektów blob platformy Azure hostującym dane. Wywoływanie tej połączonej usługi "StorageLinkedService"
3. Utwórz [zestawy](data-factory-create-datasets.md) danych wskazujące dane wejściowe i wyjściowe. Wywoływanie wejściowego zestawu danych "HiveSampleIn" i wyjściowego zestawu danych "HiveSampleOut"
4. Skopiuj zapytanie programu Hive jako plik do usługi Azure Blob Storage skonfigurowany w kroku #2. Jeśli magazyn służący do hostowania danych różni się od jednego z tych, które obsługują ten plik zapytania, należy utworzyć oddzielną połączoną usługę Azure Storage i odwołać się do niej w działaniu. Użyj **scriptPath** , aby określić ścieżkę do pliku zapytania Hive i **elementu scriptlinkedservice** , aby określić magazyn platformy Azure, który zawiera plik skryptu. 
   
   > [!NOTE]
   > Możesz również dostarczyć skrypt Hive w tekście definicji działania przy użyciu właściwości **skryptu** . Nie zalecamy tego podejścia, ponieważ wszystkie znaki specjalne w skrypcie w dokumencie JSON muszą zostać zmienione i mogą powodować problemy z debugowaniem. Najlepszym rozwiązaniem jest wykonanie kroku #4.
   > 
   > 
5. Utwórz potok z działaniem HDInsightHive. Działanie przetwarza/przekształca dane.

  ```json
  {
    "name": "HiveActivitySamplePipeline",
       "properties": {
    "activities": [
      {
        "name": "HiveActivitySample",
        "type": "HDInsightHive",
        "inputs": [
        {
          "name": "HiveSampleIn"
        }
        ],
             "outputs": [
               {
                "name": "HiveSampleOut"
               }
             ],
             "linkedServiceName": "HDInsightLinkedService",
             "typeproperties": {
                 "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                 "scriptLinkedService": "StorageLinkedService"
             },
              "scheduler": {
          "frequency": "Hour",
                   "interval": 1
             }
           }
      ]
    }
  }
  ```

6. Wdróż potok. Aby uzyskać szczegółowe informacje, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . 
7. Monitoruj potok za pomocą widoków monitorowanie i zarządzanie fabryki danych. Aby uzyskać szczegółowe informacje, zobacz artykuł [monitorowanie i zarządzanie potokami Data Factory](data-factory-monitor-manage-pipelines.md) . 

## <a name="specifying-parameters-for-a-hive-script"></a>Określanie parametrów dla skryptu Hive
W tym przykładzie dzienniki gier są wprowadzane codziennie do usługi Azure Blob Storage i są przechowywane w folderze partycjonowanym z datą i godziną. Chcesz Sparametryzuj skrypt Hive i przekazać lokalizację folderu wejściowego dynamicznie podczas wykonywania, a także generować dane wyjściowe partycjonowane z datą i godziną.

Aby użyć sparametryzowanego skryptu Hive, wykonaj następujące czynności:

* Zdefiniuj parametry w **definicjach**.

  ```JSON  
    {
        "name": "HiveActivitySamplePipeline",
          "properties": {
        "activities": [
             {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                      {
                        "name": "HiveSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "HiveSampleOut"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      },
                       "scheduler": {
                          "frequency": "Hour",
                          "interval": 1
                    }
                }
              }
        ]
      }
    }
    ```
* W skrypcie Hive zapoznaj się z parametrem za pomocą **$ {hiveconf: ParameterName}**. 
  
    ```
    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID     string, 
        SessionStart     string, 
        Duration     int, 
        SrcIPAddress     string, 
        GameType     string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 

    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (
        ProfileID     string, 
        Duration     int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID
    ```
  ## <a name="see-also"></a>Zobacz też
* [Aktywność trzody chlewnej](data-factory-pig-activity.md)
* [Działanie MapReduce](data-factory-map-reduce.md)
* [Działanie przesyłania strumieniowego Hadoop](data-factory-hadoop-streaming-activity.md)
* [Wywoływanie programów platformy Spark](data-factory-spark.md)
* [Wywoływanie skryptów języka R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

