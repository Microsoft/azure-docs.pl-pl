---
title: 'Przekształcanie danych: Przetwarzaj & Przekształć dane '
description: Dowiedz się, jak przekształcać dane lub przetwarzać dane w Azure Data Factory przy użyciu usługi Hadoop, Azure Machine Learning Studio (klasyczne) lub Azure Data Lake Analytics.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: c9818bfd2a9519cd14d34ecc810179d66aa57e52
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363988"
---
# <a name="transform-data-in-azure-data-factory-version-1"></a>Przekształć dane w Azure Data Factory wersji 1
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Połączenia strumieniowe usługi Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Azure Machine Learning Studio (klasyczny)](data-factory-azure-ml-batch-execution-activity.md) 
> * [Procedura składowana](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [Niestandardowe środowisko .NET](data-factory-use-custom-activities.md)

## <a name="overview"></a>Omówienie
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zapoznaj się z tematem [działania przekształcania danych w programie Data Factory](../transform-data.md).

W tym artykule opisano działania przekształcania danych w Azure Data Factory, które umożliwiają przekształcanie i przetwarzanie danych pierwotnych w przewidywania i szczegółowe informacje. Działanie transformacji jest wykonywane w środowisku obliczeniowym, takim jak klaster usługi Azure HDInsight lub Azure Batch. Zawiera łącza do artykułów ze szczegółowymi informacjami na temat poszczególnych działań transformacji.

Data Factory obsługuje następujące działania przekształcania danych, które można dodać do [potoków](data-factory-create-pipelines.md) pojedynczo lub łączyć z innymi działaniami.

> [!NOTE]
> Aby zapoznać się z instrukcjami krok po kroku, zobacz artykuł [Tworzenie potoku za pomocą transformacji Hive](data-factory-build-your-first-pipeline.md) .  
> 
> 

## <a name="hdinsight-hive-activity"></a>Działanie programu Hive w usłudze HDInsight
Działanie programu Hive w usłudze HDInsight w potoku Data Factory wykonuje zapytania Hive na własnym lub lokalnym klastrze usługi HDInsight opartym na systemie Windows/Linux. Aby uzyskać szczegółowe informacje o tym działaniu, zobacz artykuł dotyczący [działań Hive](data-factory-hive-activity.md) . 

## <a name="hdinsight-pig-activity"></a>Działanie dotyczące usługi HDInsight świni
Działanie świni w usłudze HDInsight w potoku Data Factory wykonuje zapytania dotyczące trzody chlewnej we własnym lub lokalnym klastrze usługi HDInsight opartym na systemie Windows/Linux. Szczegółowe informacje na temat tego działania znajdziesz w artykule dotyczącym działań związanych z [świnią](data-factory-pig-activity.md) . 

## <a name="hdinsight-mapreduce-activity"></a>Działanie MapReduce usługi HDInsight
Działanie MapReduce usługi HDInsight w potoku Data Factory wykonuje programy MapReduce na własnym lub na żądanie w klastrze HDInsight opartym na systemie Windows/Linux. Aby uzyskać szczegółowe informacje o tym działaniu, zobacz artykuł dotyczący [działań MapReduce](data-factory-map-reduce.md) .

## <a name="hdinsight-streaming-activity"></a>Działanie przesyłania strumieniowego HDInsight
Działanie przesyłania strumieniowego usługi HDInsight w potoku Data Factory wykonuje programy przesyłania strumieniowego Hadoop we własnym lub lokalnym klastrze usługi HDInsight opartym na systemie Windows/Linux. Zobacz [działanie przesyłania strumieniowego HDInsight](data-factory-hadoop-streaming-activity.md) , aby uzyskać szczegółowe informacje o tym działaniu.

## <a name="hdinsight-spark-activity"></a>Działania platformy Spark w usłudze HDInsight
Działanie usługi HDInsight Spark w potoku Data Factory wykonuje programy platformy Spark w ramach własnego klastra usługi HDInsight. Aby uzyskać szczegółowe informacje, zobacz [wywoływanie programów Spark z Azure Data Factory](data-factory-spark.md). 

## <a name="azure-machine-learning-studio-classic-activities"></a>Działania Azure Machine Learning Studio (klasyczne)
Azure Data Factory umożliwia łatwe tworzenie potoków korzystających z opublikowanej usługi sieci Web Azure Machine Learning Studio (klasycznej) na potrzeby analizy predykcyjnej. Za pomocą [działania wykonywania wsadowego](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) w potoku Azure Data Factory można wywołać usługę sieci Web programu Studio (klasyczną), aby tworzyć prognozy dotyczące danych w usłudze Batch.

W miarę upływu czasu modele predykcyjne w eksperymentach oceniania w programie Studio (klasycznej) muszą być ponownie przeszkoli przy użyciu nowych wejściowych zestawów danych. Po wykonaniu ponownych szkoleń, chcesz zaktualizować usługę oceniania przy użyciu modelu uczenia maszynowego. Możesz użyć [działania Aktualizuj zasób](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) , aby zaktualizować usługę sieci Web przy użyciu nowo nauczonego modelu.  

Aby uzyskać szczegółowe informacje o działaniach programu Studio (klasycznych), zobacz temat [Korzystanie z działań Azure Machine Learning Studio (klasycznych)](data-factory-azure-ml-batch-execution-activity.md) . 

## <a name="stored-procedure-activity"></a>Działanie procedury składowanej
Możesz użyć działania procedury składowanej SQL Server w potoku Data Factory, aby wywołać procedurę składowaną w jednym z następujących magazynów danych: Azure SQL Database, Azure Synapse Analytics, SQL Server Database w przedsiębiorstwie lub maszynie wirtualnej platformy Azure. Szczegóły można znaleźć w artykule dotyczącym [działania procedury składowanej](data-factory-stored-proc-activity.md) .  

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics działanie U-SQL
Data Lake Analytics działanie U-SQL uruchamia skrypt U-SQL w klastrze Azure Data Lake Analytics. Szczegóły można znaleźć w artykule dotyczącym [działania analiza danych U-SQL](data-factory-usql-activity.md) . 

## <a name="net-custom-activity"></a>Niestandardowe działanie platformy .NET
Jeśli zachodzi potrzeba przekształcenia danych w sposób, który nie jest obsługiwany przez Data Factory, można utworzyć niestandardowe działanie z własną logiką przetwarzania danych i użyć działania w potoku. Można skonfigurować niestandardowe działanie platformy .NET do uruchamiania przy użyciu usługi Azure Batch lub klastra usługi Azure HDInsight. Aby uzyskać szczegółowe informacje, zobacz artykuł [Używanie działań niestandardowych](data-factory-use-custom-activities.md) . 

Możesz utworzyć niestandardowe działanie, aby uruchamiać skrypty w klastrze usługi HDInsight z zainstalowanym językiem R. Zobacz [Uruchamianie skryptów języka R przy użyciu usługi Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Środowiska obliczeniowe
Należy utworzyć połączoną usługę dla środowiska obliczeniowego, a następnie użyć połączonej usługi podczas definiowania działania transformacji. Istnieją dwa typy środowisk obliczeniowych obsługiwanych przez Data Factory. 

1. **Na żądanie**: w tym przypadku środowisko komputerowe jest w pełni zarządzane przez Data Factory. Jest automatycznie tworzony przez usługę Data Factory przed przesłaniem zadania w celu przetworzenia danych i usunięciu po zakończeniu zadania. Można skonfigurować i kontrolować szczegółowe ustawienia środowiska obliczeniowego na żądanie na potrzeby wykonywania zadań, zarządzania klastrami i uruchamiania akcji. 
2. **Wprowadź swój własny**: w tym przypadku możesz zarejestrować własne środowisko obliczeniowe (na przykład klaster usługi HDInsight) jako połączoną usługę w Data Factory. Środowisko komputerowe jest zarządzane przez Ciebie, a usługa Data Factory używa jej do wykonywania działań. 

Zobacz artykuł dotyczący [połączonych usług](data-factory-compute-linked-services.md) , aby dowiedzieć się więcej o usługach obliczeniowych obsługiwanych przez Data Factory. 

## <a name="summary"></a>Podsumowanie
Azure Data Factory obsługuje następujące działania przekształcania danych i środowiska obliczeniowe dla działań. Działania przekształcania można dodawać do potoków pojedynczo lub w łańcuchu z innymi działaniami.

| Działanie przekształcania danych | Środowisko obliczeniowe |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Połączenia strumieniowe usługi Hadoop](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Działania Azure Machine Learning Studio (klasyczne): wykonywanie wsadowe i aktualizowanie zasobu](data-factory-azure-ml-batch-execution-activity.md) |Maszyna wirtualna platformy Azure |
| [Procedura składowana](data-factory-stored-proc-activity.md) |Azure SQL, Azure Synapse Analytics lub SQL Server |
| [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |Usługa HDInsight [Hadoop] lub usługa Azure Batch |

