---
title: Przekształcanie danych przy użyciu działania trzody chlewnej w Azure Data Factory
description: Dowiedz się, jak za pomocą działania wieprzowiny w Azure Data Factory V1 uruchomić skrypty wieprzowe na żądanie/własny klaster usługi HDInsight.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 0ad84a0e848abda1b786958947b4081b11b139a7
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495349"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu działania trzody chlewnej w Azure Data Factory
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
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Przekształć dane przy użyciu działania świni w Data Factory](../transform-data-using-hadoop-pig.md).


Działanie świni w [usłudze](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight w [potoku](data-factory-create-pipelines.md) Data Factory wykonuje zapytania dotyczące trzody chlewnej we [własnym lub lokalnym klastrze usługi](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) HDInsight opartym na systemie Windows/Linux. W tym artykule przedstawiono artykuł [działania przekształcania danych](data-factory-data-transformation-activities.md) , który zawiera ogólne omówienie transformacji danych i obsługiwanych działań transformacji.

> [!NOTE] 
> Jeśli dopiero zaczynasz Azure Data Factory, przeczytaj artykuł [wprowadzenie do Azure Data Factory](data-factory-introduction.md) i wykonaj samouczek: [Kompiluj pierwszy potok danych](data-factory-build-your-first-pipeline.md) przed przeczytaniem tego artykułu. 

## <a name="syntax"></a>Składnia

```JSON
{
  "name": "HiveActivitySamplePipeline",
  "properties": {
    "activities": [
      {
        "name": "Pig Activity",
        "description": "description",
        "type": "HDInsightPig",
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
          "script": "Pig script",
          "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
          "defines": {
            "param1": "param1Value"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
      }
    ]
  }
}
```

## <a name="syntax-details"></a>Szczegóły składni

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| name |Nazwa działania |Tak |
| description (opis) |Tekst opisujący działanie używanego działania |Nie |
| typ |HDinsightPig |Tak |
| danych wejściowych |Co najmniej jedno wejście używane przez działanie świni |Nie |
| wydajności |Jedno lub więcej danych wyjściowych wytwarzanych przez działanie trzody chlewnej |Tak |
| linkedServiceName |Odwołanie do klastra usługi HDInsight zarejestrowanego jako połączona usługa w Data Factory |Tak |
| skrypt |Określ skrypt dla trzody chlewnej |Nie |
| scriptPath |Zapisz skrypt trzody chlewnej w usłudze Azure Blob Storage i podaj ścieżkę do pliku. Użyj właściwości "Script" lub "scriptPath". Nie można jednocześnie używać obu tych metod. W nazwie pliku rozróżniana jest wielkość liter. |Nie |
| definiuje |Określ parametry jako pary klucz/wartość do odwołania w skrypcie świni |Nie |

## <a name="example"></a>Przykład
Rozważmy przykładową analizę dzienników gier, w której chcesz określić czas spędzony przez graczy, którzy odgrywają gry uruchomione przez firmę.

Następujący przykładowy dziennik gier to plik rozdzielony przecinkami (,). Zawiera następujące pola — ProfileID, SessionStart, Duration, SrcIPAddress i GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

**Skrypt trzody chlewnej** służący do przetworzenia tych danych:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Aby wykonać ten skrypt trzody chlewnej w potoku Data Factory, wykonaj następujące czynności:

1. Utwórz połączoną usługę, aby zarejestrować [własny klaster obliczeniowy usługi HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) lub skonfigurować [klaster obliczeniowy usługi HDInsight na żądanie](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Przydzwońmy do **HDInsightLinkedService** połączonej usługi.
2. Utwórz [połączoną usługę](data-factory-azure-blob-connector.md) , aby skonfigurować połączenie z magazynem obiektów blob platformy Azure hostującym dane. Przydzwońmy do **StorageLinkedService** połączonej usługi.
3. Utwórz [zestawy](data-factory-create-datasets.md) danych wskazujące dane wejściowe i wyjściowe. Wywołajmy wejściowy zestaw danych **PigSampleIn** i wyjściowy zestaw danych **PigSampleOut**.
4. Skopiuj zapytanie świni w pliku, który Blob Storage platformy Azure skonfigurowany w kroku #2. Jeśli magazyn platformy Azure, który obsługuje dane, różni się od tego, który hostuje plik zapytania, należy utworzyć oddzielną połączoną usługę Azure Storage. Zapoznaj się z połączoną usługą w konfiguracji działania. Użyj **scriptPath** , aby określić ścieżkę do pliku skryptu i **elementu scriptlinkedservice**. 
   
   > [!NOTE]
   > Możesz również dostarczyć skrypt świni w definicji działania przy użyciu właściwości **skryptu** . Nie zaleca się jednak, aby te podejście, ponieważ wszystkie znaki specjalne w skrypcie muszą zostać zmienione i mogą powodować problemy z debugowaniem. Najlepszym rozwiązaniem jest wykonanie kroku #4.
   >
   >
5. Utwórz potok za pomocą działania HDInsightPig. To działanie przetwarza dane wejściowe przez uruchomienie skryptu wieprzowiny w klastrze usługi HDInsight.

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
              "scriptLinkedService": "StorageLinkedService"
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
6. Wdróż potok. Aby uzyskać szczegółowe informacje, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . 
7. Monitoruj potok za pomocą widoków monitorowanie i zarządzanie fabryki danych. Aby uzyskać szczegółowe informacje, zobacz artykuł [monitorowanie i zarządzanie potokami Data Factory](data-factory-monitor-manage-pipelines.md) .

## <a name="specifying-parameters-for-a-pig-script"></a>Określanie parametrów dla skryptu wieprzowego
Rozważmy następujący przykład: dzienniki gier są odbierane codziennie na platformie Azure Blob Storage i przechowywane w folderze partycjonowanym na podstawie daty i godziny. Chcesz Sparametryzuj skrypt trzody chlewnej i przekazać lokalizację folderu wejściowego dynamicznie podczas wykonywania, a także generować dane wyjściowe partycjonowane z datą i godziną.

Aby użyć sparametryzowanego skryptu wieprzowego, wykonaj następujące czynności:

* Zdefiniuj parametry w **definicjach**.

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
              "scriptLinkedService": "StorageLinkedService",
              "defines": {
                "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0:MM}/dayno={0: dd}/',SliceStart)",
                "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
              }
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
* W skrypcie trzody chlewnej zapoznaj się z parametrami przy użyciu "**$ParameterName**", jak pokazano w następującym przykładzie:

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>Zobacz też
* [Działanie Hive](data-factory-hive-activity.md)
* [Działanie MapReduce](data-factory-map-reduce.md)
* [Działanie przesyłania strumieniowego Hadoop](data-factory-hadoop-streaming-activity.md)
* [Wywoływanie programów platformy Spark](data-factory-spark.md)
* [Wywoływanie skryptów języka R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)
