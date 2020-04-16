---
title: Przekształcanie danych
description: Dowiedz się, jak przekształcać dane lub przetwarzać dane w usłudze Azure Data Factory przy użyciu usługi Hadoop, Machine Learning lub Usługi Azure Data Lake Analytics.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 07/31/2018
ms.openlocfilehash: 024d61c5bcc38c78dc5a2bd5e01cba43d7b5267e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418834"
---
# <a name="transform-data-in-azure-data-factory"></a>Przekształcanie danych w usłudze Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

> [!div class="op_single_selector"]
> * [Mapowanie przepływu danych](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce (Mapa)](transform-data-using-hadoop-map-reduce.md)  
> * [Transmisja hdinsight](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Uczenie maszynowe](transform-data-using-machine-learning.md) 
> * [Procedura składowana](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Notes Databricks](transform-data-databricks-notebook.md)
> * [Słoik Databricks](transform-data-databricks-jar.md)
> * [Pyton databricks](transform-data-databricks-python.md)
> * [Niestandardowe usługi .NET](transform-data-using-dotnet-custom-activity.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
## <a name="overview"></a>Omówienie
W tym artykule opisano działania przekształcania danych w usłudze Azure Data Factory, których można użyć do przekształcania i przetwarzania nieprzetworzonych danych w prognozy i szczegółowe informacje na dużą skalę. Działanie transformacji jest wykonywane w środowisku obliczeniowym, takim jak Azure Databricks lub Azure HDInsight. Zawiera łącza do artykułów ze szczegółowymi informacjami na temat każdego działania transformacji.

Usługa Fabryka danych obsługuje następujące działania przekształcania danych, które można dodać do [potoków](concepts-pipelines-activities.md) indywidualnie lub w łańcuchu z innym działaniem.

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>Przekształcanie natywnie w usługę Azure Data Factory za pomocą przepływów danych

### <a name="mapping-data-flows"></a>Przepływy danych mapowania

Przepływy danych mapowania są wizualnie zaprojektowane przekształcenia danych w usłudze Azure Data Factory. Przepływy danych umożliwiają inżynierom danych opracowanie logiki przekształcania danych graficznych bez pisania kodu. Wynikowe przepływy danych są wykonywane jako działania w potokach usługi Azure Data Factory, które używają skalowane w poziomie klastrów platformy Spark. Działania przepływu danych można zudralizować za pomocą istniejących funkcji planowania, sterowania, przepływu i monitorowania. Aby uzyskać więcej informacji, zobacz [mapowanie przepływów danych](concepts-data-flow-overview.md).

### <a name="wrangling-data-flows"></a>Rozłączanie przepływów danych

Wrangling przepływów danych w usłudze Azure Data Factory umożliwiają przygotowanie danych bez kodu w skali chmury iteratively. Wrangling przepływów danych zintegrować z [power query online](https://docs.microsoft.com/power-query/) i sprawia, że funkcje Power Query M dostępne dla danych wrangling w skali chmury poprzez wykonanie iskrowe. Aby uzyskać więcej informacji, zobacz [wrangling przepływów danych](wrangling-data-flow-overview.md).

## <a name="external-transformations"></a>Przekształcenia zewnętrzne

Opcjonalnie można ręcznie kodować przekształcenia i samodzielnie zarządzać zewnętrznym środowiskiem obliczeniowym.

### <a name="hdinsight-hive-activity"></a>Aktywność gałęzi HDInsight
Działanie hive hdinsight w potoku fabryki danych wykonuje zapytania hive na własny lub na żądanie Windows/Linux oparte na klastrze HDInsight. Zobacz artykuł [aktywności hive,](transform-data-using-hadoop-hive.md) aby uzyskać szczegółowe informacje na temat tego działania. 

### <a name="hdinsight-pig-activity"></a>Aktywność HDInsight Pig
Działanie HIInsight Pig w potoku usługi Data Factory wykonuje zapytania Pig na własny lub na żądanie Windows/ Linux oparte na klastrze HDInsight. Zobacz artykuł [o aktywności świni,](transform-data-using-hadoop-pig.md) aby uzyskać szczegółowe informacje na temat tej działalności. 

### <a name="hdinsight-mapreduce-activity"></a>Mapa HDInsightZrozuchomić aktywność
Działanie HDInsight MapReduce w potoku data factory wykonuje programy MapReduce na własną lub na żądanie klaster HDInsight oparty na systemie Windows/Linux. Zobacz [MapReduce artykuł aktywności,](transform-data-using-hadoop-map-reduce.md) aby uzyskać szczegółowe informacje na temat tego działania.

### <a name="hdinsight-streaming-activity"></a>Aktywność przesyłania strumieniowego hdinsight
Działanie hdinsight przesyłania strumieniowego w potoku usługi Data Factory wykonuje programy przesyłania strumieniowego Hadoop na własny lub na żądanie klaster HDInsight oparty na systemie Windows/Linux. Zobacz [HDInsight streaming działania,](transform-data-using-hadoop-streaming.md) aby uzyskać szczegółowe informacje na temat tego działania.

### <a name="hdinsight-spark-activity"></a>Działanie platformy SPARK aplikacji HDInsight
Działanie programu HDInsight Spark w potoku usługi Data Factory wykonuje programy Platformy Spark we własnym klastrze HDInsight. Aby uzyskać szczegółowe informacje, zobacz [Wywoływanie programów Spark z usługi Azure Data Factory](transform-data-using-spark.md). 

### <a name="machine-learning-activities"></a>Działania związane z uczeniem maszynowym
Usługa Azure Data Factory umożliwia łatwe tworzenie potoków korzystających z opublikowanej usługi sieci Web usługi Azure Machine Learning do analizy predykcyjnej. Za pomocą [działania wykonywania wsadowego](transform-data-using-machine-learning.md) w potoku usługi Azure Data Factory, można wywołać usługę sieci web uczenia maszynowego, aby prognoz na danych w partii.

Z biegiem czasu modele predykcyjne w eksperymentach oceniania uczenia maszynowego muszą zostać przeszkolone przy użyciu nowych wejściowych zestawów danych. Po zakończeniu przekwalifikowania chcesz zaktualizować usługę sieci web oceniania za pomocą ponownie przeszkolonego modelu uczenia maszynowego. Za pomocą [działania Aktualizuj zasób](update-machine-learning-models.md) można zaktualizować usługę sieci web za pomocą nowo przeszkolonego modelu.  

Zobacz [Korzystanie z działań uczenia maszynowego,](transform-data-using-machine-learning.md) aby uzyskać szczegółowe informacje na temat tych działań uczenia maszynowego. 

### <a name="stored-procedure-activity"></a>Działanie procedury składowanej
Za pomocą działania procedury przechowywanej programu SQL Server w potoku usługi Data Factory można wywołać procedurę składowaną w jednym z następujących magazynów danych: Azure SQL Database, Azure SQL Data Warehouse, BAZA DANYCH PROGRAMU SQL Server w przedsiębiorstwie lub maszyna wirtualna platformy Azure. Zobacz [artykule działania procedury składowanej,](transform-data-using-stored-procedure.md) aby uzyskać szczegółowe informacje.  

### <a name="data-lake-analytics-u-sql-activity"></a>Działanie U-SQL usługi Data Lake Analytics
Działanie U-SQL usługi Data Lake Analytics uruchamia skrypt U-SQL w klastrze usługi Azure Data Lake Analytics. Szczegółowe informacje można znaleźć w artykule [o aktywności U-SQL analizy danych.](transform-data-using-data-lake-analytics.md) 

### <a name="databricks-notebook-activity"></a>Działanie notesu databricks

Aktywność notesu usługi Azure Databricks w potoku usługi Data Factory uruchamia notes Databricks w obszarze roboczym usługi Azure Databricks. Usługa Azure Databricks to zarządzana platforma do uruchamiania platformy Apache Spark. Zobacz [Przekształcanie danych przez uruchomienie notesu Databricks](transform-data-databricks-notebook.md).

### <a name="databricks-jar-activity"></a>Aktywność Databricks Jar

Działanie usługi Azure Databricks Jar w potoku fabryki danych uruchamia jar platformy Spark w klastrze usługi Azure Databricks. Usługa Azure Databricks to zarządzana platforma do uruchamiania platformy Apache Spark. Zobacz [Przekształcanie danych przez uruchamianie działania jar w usłudze Azure Databricks](transform-data-databricks-jar.md).

### <a name="databricks-python-activity"></a>Działanie języka Databricks Python

Działanie usługi Azure Databricks Python w potoku fabryki danych uruchamia plik Języka Python w klastrze usługi Azure Databricks. Usługa Azure Databricks to zarządzana platforma do uruchamiania platformy Apache Spark. Zobacz [Przekształcanie danych przez uruchamianie działania języka Python w usłudze Azure Databricks](transform-data-databricks-python.md).

### <a name="custom-activity"></a>Działanie niestandardowe
Jeśli trzeba przekształcić dane w sposób, który nie jest obsługiwany przez fabrykę danych, można utworzyć działanie niestandardowe z własną logiką przetwarzania danych i użyć działania w potoku. Niestandardowe działanie platformy .NET można skonfigurować do uruchamiania przy użyciu usługi Azure Batch lub klastra usługi Azure HDInsight. Zobacz Użyj artykułu [działania niestandardowe,](transform-data-using-dotnet-custom-activity.md) aby uzyskać szczegółowe informacje. 

Możesz utworzyć niestandardowe działanie, aby uruchamiać skrypty w klastrze usługi HDInsight z zainstalowanym językiem R. Zobacz [Uruchamianie skryptów języka R przy użyciu usługi Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

### <a name="compute-environments"></a>Środowiska obliczeniowe
Utwórz usługę połączony dla środowiska obliczeniowego, a następnie użyj połączonej usługi podczas definiowania działania transformacji. Istnieją dwa typy środowisk obliczeniowych obsługiwanych przez fabrykę danych. 

- **Na żądanie:** W tym przypadku środowisko obliczeniowe jest w pełni zarządzane przez fabrykę danych. Jest on automatycznie tworzony przez usługę Data Factory przed przesłaniem zadania do przetwarzania danych i usunięciem po zakończeniu zadania. Można skonfigurować i kontrolować szczegółowe ustawienia środowiska obliczeniowego na żądanie do wykonywania zadań, zarządzania klastrami i akcji boottrappingu. 
- **Bring Your Own:** W takim przypadku można zarejestrować własne środowisko obliczeniowe (na przykład klaster HDInsight) jako połączona usługa w fabryce danych. Środowisko obliczeniowe jest zarządzane przez Ciebie i usługa Data Factory używa go do wykonywania działań. 

Zobacz artykuł [Compute Linked Services,](compute-linked-services.md) aby dowiedzieć się więcej o usługach obliczeniowych obsługiwanych przez fabrykę danych. 

## <a name="next-steps"></a>Następne kroki
Zobacz następujący samouczek na przykład przy użyciu działania transformacji: [Samouczek: przekształcanie danych przy użyciu platformy Spark](tutorial-transform-data-spark-powershell.md)
