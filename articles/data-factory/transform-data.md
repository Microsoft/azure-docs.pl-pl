---
title: Przekształcanie danych
description: Przekształć dane lub Przetwarzaj dane w Azure Data Factory przy użyciu usługi Hadoop, Azure Machine Learning Studio (klasyczne) lub Azure Data Lake Analytics.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 07/31/2018
ms.openlocfilehash: 43a035662cc76dc6de1de3fa990e06f2e00cfd66
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632329"
---
# <a name="transform-data-in-azure-data-factory"></a>Przekształcanie danych w usłudze Azure Data Factory

> [!div class="op_single_selector"]
> * [Mapowanie przepływu danych](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [Przesyłanie strumieniowe w usłudze HDInsight](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Azure Machine Learning Studio (klasyczny)](transform-data-using-machine-learning.md) 
> * [Procedura składowana](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Notes datakostki](transform-data-databricks-notebook.md)
> * [W jar](transform-data-databricks-jar.md)
> * [Środowiska Python](transform-data-databricks-python.md)
> * [Niestandardowe środowisko .NET](transform-data-using-dotnet-custom-activity.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Omówienie
W tym artykule opisano działania przekształcania danych w Azure Data Factory, które umożliwiają przekształcanie i przetwarzanie danych pierwotnych w przewidywania i szczegółowe informacje na dużą skalę. Działanie transformacji jest wykonywane w środowisku obliczeniowym, takim jak Azure Databricks lub Azure HDInsight. Zawiera łącza do artykułów ze szczegółowymi informacjami na temat poszczególnych działań transformacji.

Data Factory obsługuje następujące działania przekształcania danych, które można dodać do [potoków](concepts-pipelines-activities.md) pojedynczo lub łączyć z innymi działaniami.

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>Przekształć natywnie w Azure Data Factory z przepływami danych

### <a name="mapping-data-flows"></a>Przepływy danych mapowania

Mapowanie przepływów danych to wizualnie zaprojektowane przekształcenia danych w Azure Data Factory. Przepływy danych umożliwiają inżynierom danych Tworzenie logiki transformacji danych graficznych bez pisania kodu. Wyniki przepływów danych są wykonywane jako działania w ramach potoków Azure Data Factory, które używają skalowanych klastrów Spark. Działania związane z przepływem danych mogą być operacyjne za pośrednictwem istniejących Data Factory planowania, kontroli, przepływu i monitorowania. Aby uzyskać więcej informacji, zobacz [Mapowanie przepływów danych](concepts-data-flow-overview.md).

### <a name="data-wrangling"></a>Przetwarzanie danych

Power Query w Azure Data Factory umożliwia przetwarzanie danych w skali chmury, co umożliwia iteracyjne Przygotowywanie danych w skali chmury. Usługa Data przetwarzanie integruje się z usługą [Power Query online](/power-query/) i udostępnia funkcje Power Query M dla danych przetwarzanie w skali chmury za pośrednictwem wykonywania platformy Spark. Aby uzyskać więcej informacji, zobacz [przetwarzanie danych w ADF](wrangling-overview.md).

## <a name="external-transformations"></a>Przekształcenia zewnętrzne

Opcjonalnie możesz ręcznie przekształceń kodu i zarządzać zewnętrznym środowiskiem obliczeniowym.

### <a name="hdinsight-hive-activity"></a>Działanie programu Hive w usłudze HDInsight
Działanie programu Hive w usłudze HDInsight w potoku Data Factory wykonuje zapytania Hive na własnym lub lokalnym klastrze usługi HDInsight opartym na systemie Windows/Linux. Aby uzyskać szczegółowe informacje o tym działaniu, zobacz artykuł dotyczący [działań Hive](transform-data-using-hadoop-hive.md) . 

### <a name="hdinsight-pig-activity"></a>Działanie dotyczące usługi HDInsight świni
Działanie świni w usłudze HDInsight w potoku Data Factory wykonuje zapytania dotyczące trzody chlewnej we własnym lub lokalnym klastrze usługi HDInsight opartym na systemie Windows/Linux. Szczegółowe informacje na temat tego działania znajdziesz w artykule dotyczącym działań związanych z [świnią](transform-data-using-hadoop-pig.md) . 

### <a name="hdinsight-mapreduce-activity"></a>Działanie MapReduce usługi HDInsight
Działanie MapReduce usługi HDInsight w potoku Data Factory wykonuje programy MapReduce na własnym lub na żądanie w klastrze HDInsight opartym na systemie Windows/Linux. Aby uzyskać szczegółowe informacje o tym działaniu, zobacz artykuł dotyczący [działań MapReduce](transform-data-using-hadoop-map-reduce.md) .

### <a name="hdinsight-streaming-activity"></a>Działanie przesyłania strumieniowego HDInsight
Działanie przesyłania strumieniowego usługi HDInsight w potoku Data Factory wykonuje programy przesyłania strumieniowego Hadoop we własnym lub lokalnym klastrze usługi HDInsight opartym na systemie Windows/Linux. Zobacz [działanie przesyłania strumieniowego HDInsight](transform-data-using-hadoop-streaming.md) , aby uzyskać szczegółowe informacje o tym działaniu.

### <a name="hdinsight-spark-activity"></a>Działanie usługi HDInsight Spark
Działanie usługi HDInsight Spark w potoku Data Factory wykonuje programy platformy Spark w ramach własnego klastra usługi HDInsight. Aby uzyskać szczegółowe informacje, zobacz [wywoływanie programów Spark z Azure Data Factory](transform-data-using-spark.md). 

### <a name="azure-machine-learning-studio-classic-activities"></a>Działania Azure Machine Learning Studio (klasyczne)
Azure Data Factory umożliwia łatwe tworzenie potoków korzystających z opublikowanej usługi sieci Web Azure Machine Learning Studio (klasycznej) na potrzeby analizy predykcyjnej. Za pomocą [działania wykonywania wsadowego](transform-data-using-machine-learning.md) w potoku Azure Data Factory można wywołać usługę sieci Web programu Studio (klasyczną), aby tworzyć prognozy dotyczące danych w usłudze Batch.

W miarę upływu czasu modele predykcyjne w eksperymentach oceniania w programie Studio (klasycznej) muszą być ponownie przeszkoli przy użyciu nowych wejściowych zestawów danych. Po wykonaniu ponownych szkoleń, chcesz zaktualizować usługę oceniania przy użyciu modelu uczenia maszynowego. Możesz użyć [działania Aktualizuj zasób](update-machine-learning-models.md) , aby zaktualizować usługę sieci Web przy użyciu nowo nauczonego modelu.  

Aby uzyskać szczegółowe informacje o działaniach programu Studio (klasycznych), zobacz temat [Korzystanie z działań Azure Machine Learning Studio (klasycznych)](transform-data-using-machine-learning.md) . 

### <a name="stored-procedure-activity"></a>Działanie procedury składowanej
Możesz użyć działania procedury składowanej SQL Server w potoku Data Factory, aby wywołać procedurę składowaną w jednym z następujących magazynów danych: Azure SQL Database, Azure Synapse Analytics, SQL Server Database w przedsiębiorstwie lub maszynie wirtualnej platformy Azure. Szczegóły można znaleźć w artykule dotyczącym [działania procedury składowanej](transform-data-using-stored-procedure.md) .  

### <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics działanie U-SQL
Data Lake Analytics działanie U-SQL uruchamia skrypt U-SQL w klastrze Azure Data Lake Analytics. Szczegóły można znaleźć w artykule dotyczącym [działania analiza danych U-SQL](transform-data-using-data-lake-analytics.md) . 

### <a name="databricks-notebook-activity"></a>Aktywność notesu datakostki

Działanie notesu Azure Databricks w potoku Data Factory uruchamia Notes datacegły w obszarze roboczym Azure Databricks. Azure Databricks to zarządzana platforma do uruchamiania Apache Spark. Zobacz [Przekształć dane, uruchamiając Notes datakostki](transform-data-databricks-notebook.md).

### <a name="databricks-jar-activity"></a>Aktywność jar

Działanie Azure Databricks jar w potoku Data Factory uruchamia plik JAR platformy Spark w klastrze Azure Databricks. Azure Databricks to zarządzana platforma do uruchamiania Apache Spark. Zobacz [Przekształć dane, uruchamiając działanie jar w Azure Databricks](transform-data-databricks-jar.md).

### <a name="databricks-python-activity"></a>Aktywność języka Python dla elementów datakostek

Działanie języka Python Azure Databricks w potoku Data Factory uruchamia plik w języku Python w klastrze Azure Databricks. Azure Databricks to zarządzana platforma do uruchamiania Apache Spark. Zobacz [Przekształć dane, uruchamiając działanie języka Python w Azure Databricks](transform-data-databricks-python.md).

### <a name="custom-activity"></a>Działanie niestandardowe
Jeśli zachodzi potrzeba przekształcenia danych w sposób, który nie jest obsługiwany przez Data Factory, można utworzyć niestandardowe działanie z własną logiką przetwarzania danych i użyć działania w potoku. Można skonfigurować niestandardowe działanie platformy .NET do uruchamiania przy użyciu usługi Azure Batch lub klastra usługi Azure HDInsight. Aby uzyskać szczegółowe informacje, zobacz artykuł [Używanie działań niestandardowych](transform-data-using-dotnet-custom-activity.md) . 

Możesz utworzyć niestandardowe działanie, aby uruchamiać skrypty w klastrze usługi HDInsight z zainstalowanym językiem R. Zobacz [Uruchamianie skryptów języka R przy użyciu usługi Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

### <a name="compute-environments"></a>Środowiska obliczeniowe
Należy utworzyć połączoną usługę dla środowiska obliczeniowego, a następnie użyć połączonej usługi podczas definiowania działania transformacji. Istnieją dwa typy środowisk obliczeniowych obsługiwanych przez Data Factory. 

- **Na żądanie**: w tym przypadku środowisko komputerowe jest w pełni zarządzane przez Data Factory. Jest automatycznie tworzony przez usługę Data Factory przed przesłaniem zadania w celu przetworzenia danych i usunięciu po zakończeniu zadania. Można skonfigurować i kontrolować szczegółowe ustawienia środowiska obliczeniowego na żądanie na potrzeby wykonywania zadań, zarządzania klastrami i uruchamiania akcji. 
- **Wprowadź swój własny**: w tym przypadku możesz zarejestrować własne środowisko obliczeniowe (na przykład klaster usługi HDInsight) jako połączoną usługę w Data Factory. Środowisko komputerowe jest zarządzane przez Ciebie, a usługa Data Factory używa jej do wykonywania działań. 

Zobacz artykuł dotyczący [połączonych usług](compute-linked-services.md) , aby dowiedzieć się więcej o usługach obliczeniowych obsługiwanych przez Data Factory. 

## <a name="next-steps"></a>Następne kroki
Zobacz poniższy samouczek, aby zapoznać się z przykładem działania przekształcania: [Samouczek: Przekształcanie danych przy użyciu platformy Spark](tutorial-transform-data-spark-powershell.md)
