---
title: Przekształcanie danych przy użyciu aktywności przesyłania strumieniowego w uodwęzo w hadoop
description: W tym artykule wyjaśniono, jak używać działania przesyłania strumieniowego Hadoop w usłudze Azure Data Factory do przekształcania danych przez uruchamianie programów przesyłania strumieniowego Hadoop w klastrze Hadoop.
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/16/2018
ms.openlocfilehash: c1bba6903fe1cb8cc5bae9a12153553594180b43
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418885"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu aktywności przesyłania strumieniowego usługi Hadoop w fabryce danych platformy Azure
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-hadoop-streaming-activity.md)
> * [Bieżąca wersja](transform-data-using-hadoop-streaming.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktywność przesyłania strumieniowego HDInsight w [potoku](concepts-pipelines-activities.md) usługi Data Factory wykonuje programy przesyłania strumieniowego Hadoop na [własny](compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastra HDInsight. W tym artykule opiera się na [działaniach transformacji danych,](transform-data.md) który przedstawia ogólny przegląd transformacji danych i obsługiwanych działań transformacji.

Jeśli jesteś nowy w usłudze Azure Data Factory, przeczytaj [wprowadzenie do usługi Azure Data Factory](introduction.md) i wykonaj [samouczek: przekształć dane](tutorial-transform-data-spark-powershell.md) przed przeczytaniem tego artykułu. 

## <a name="json-sample"></a>Próbka JSON
```json
{
    "name": "Streaming Activity",
    "description": "Description",
    "type": "HDInsightStreaming",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mapper": "MyMapper.exe",
        "reducer": "MyReducer.exe",
        "combiner": "MyCombiner.exe",
        "fileLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "filePaths": [
            "<containername>/example/apps/MyMapper.exe",
            "<containername>/example/apps/MyReducer.exe",
            "<containername>/example/apps/MyCombiner.exe"
        ],
        "input": "wasb://<containername>@<accountname>.blob.core.windows.net/example/input/MapperInput.txt",
        "output": "wasb://<containername>@<accountname>.blob.core.windows.net/example/output/ReducerOutput.txt",
        "commandEnvironment": [
            "CmdEnvVarName=CmdEnvVarValue"
        ],
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Szczegóły składni

| Właściwość          | Opis                              | Wymagany |
| ----------------- | ---------------------------------------- | -------- |
| name              | Nazwa działania                     | Tak      |
| description       | Tekst opisujący, do czego jest używane działanie | Nie       |
| type              | W przypadku aktywności przesyłania strumieniowego hadoop typem działania jest HDInsightStreaming | Tak      |
| linkedServiceName | Odwołanie do klastra HDInsight zarejestrowanego jako usługa połączona w fabryce danych. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz Artykuł [dotyczący powiązanych usług obliczeń.](compute-linked-services.md) | Tak      |
| Mapowania            | Określa nazwę pliku wykonywalnego mapera | Tak      |
| Reduktor           | Określa nazwę pliku wykonywalnego reduktora | Tak      |
| kombajna          | Określa nazwę pliku wykonywalnego kombajni | Nie       |
| plikLinkedService | Odwołanie do usługi Azure Storage Linked Service używane do przechowywania programów Mapper, Combiner i Reducer do wykonania. Jeśli nie określisz tej usługi połączonej, używana jest usługa linked service usługi Azure Storage zdefiniowana w usłudze połączonej usługi HDInsight. | Nie       |
| Filepath          | Podaj tablicę ścieżki do programów Mapper, Combiner i Reducer przechowywane w usłudze Azure Storage, o których mowa fileLinkedService. W ścieżce jest rozróżniana wielkość liter. | Tak      |
| wejście             | Określa ścieżkę WASB do pliku wejściowego dla mapera. | Tak      |
| output            | Określa ścieżkę WASB do pliku wyjściowego reduktora. | Tak      |
| getDebugInfo      | Określa, kiedy pliki dziennika są kopiowane do usługi Azure Storage używane przez klaster HDInsight (lub) określone przez scriptLinkedService. Dozwolone wartości: Brak, Zawsze lub Błąd. Wartość domyślna: None. | Nie       |
| Argumenty         | Określa tablicę argumentów dla zadania Hadoop. Argumenty są przekazywane jako argumenty wiersza polecenia do każdego zadania. | Nie       |
| Definiuje           | Określ parametry jako pary klucz/wartość do odwoływania się w skrypcie gałęzi. | Nie       | 

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły, które wyjaśniają, jak przekształcać dane w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktywność gałęzi](transform-data-using-hadoop-hive.md)
* [Aktywność świń](transform-data-using-hadoop-pig.md)
* [Działanie mapreduce](transform-data-using-hadoop-map-reduce.md)
* [Aktywność iskierki](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wsadowe uczenia maszynowego](transform-data-using-machine-learning.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)
