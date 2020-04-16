---
title: Przekształcanie danych przy użyciu działania Hadoop MapReduce
description: Dowiedz się, jak przetwarzać dane, uruchamiając programy Programu MapReduce usługi Hadoop w klastrze usługi Azure HDInsight z fabryki danych platformy Azure.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 01/16/2018
ms.openlocfilehash: e3060f7e36f9e2696194da12c3c800555103d271
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418919"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu działania Programu Hadoop MapReduce w usłudze Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-map-reduce.md)
> * [Bieżąca wersja](transform-data-using-hadoop-map-reduce.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Działanie HDInsight MapReduce w [potoku](concepts-pipelines-activities.md) fabryki danych wywołuje program MapReduce na [własny](compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastra HDInsight. W tym artykule opiera się na [działaniach transformacji danych,](transform-data.md) który przedstawia ogólny przegląd transformacji danych i obsługiwanych działań transformacji.

Jeśli jesteś nowy w usłudze Azure Data Factory, przeczytaj [wprowadzenie do usługi Azure Data Factory](introduction.md) i wykonaj samouczek: [Samouczek: przekształć dane](tutorial-transform-data-spark-powershell.md) przed przeczytaniem tego artykułu.

Zobacz [Świnia](transform-data-using-hadoop-pig.md) i [gałąź,](transform-data-using-hadoop-hive.md) aby uzyskać szczegółowe informacje na temat uruchamiania skryptów Pig/Hive w klastrze HDInsight z potoku przy użyciu działań HIInight Pig i Hive.

## <a name="syntax"></a>Składnia

```json
{
    "name": "Map Reduce Activity",
    "description": "Description",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.myorg.SampleClass",
        "jarLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "MyAzureStorage/jars/sample.jar",
        "getDebugInfo": "Failure",
        "arguments": [
            "-SampleHadoopJobArgument1"
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
| type              | W przypadku działania MapReduce typem działania jest HDinsightMapReduce | Tak      |
| linkedServiceName | Odwołanie do klastra HDInsight zarejestrowanego jako usługa połączona w fabryce danych. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz Artykuł [dotyczący powiązanych usług obliczeń.](compute-linked-services.md) | Tak      |
| Classname         | Nazwa klasy, która ma zostać wykonana         | Tak      |
| jarLinkedSługa  | Odwołanie do usługi Linked Service usługi Azure Storage używanej do przechowywania plików Jar. Jeśli nie określisz tej usługi połączonej, używana jest usługa linked service usługi Azure Storage zdefiniowana w usłudze połączonej usługi HDInsight. | Nie       |
| jarFilePath       | Podaj ścieżkę do plików Jar przechowywanych w usłudze Azure Storage, o których mowa w jarLinkedService. W nazwie pliku rozróżniana jest wielkość liter. | Tak      |
| jarlibs ( jarlibs )           | Tablica ciągów ścieżki do plików biblioteki Jar, do których odwołuje się zadanie przechowywane w usłudze Azure Storage zdefiniowanej w jarLinkedService. W nazwie pliku rozróżniana jest wielkość liter. | Nie       |
| getDebugInfo      | Określa, kiedy pliki dziennika są kopiowane do usługi Azure Storage używane przez klaster HDInsight (lub) określone przez jarLinkedService. Dozwolone wartości: Brak, Zawsze lub Błąd. Wartość domyślna: None. | Nie       |
| Argumenty         | Określa tablicę argumentów dla zadania Hadoop. Argumenty są przekazywane jako argumenty wiersza polecenia do każdego zadania. | Nie       |
| Definiuje           | Określ parametry jako pary klucz/wartość do odwoływania się w skrypcie gałęzi. | Nie       |



## <a name="example"></a>Przykład
Za pomocą programu HDInsight MapReduce Activity można uruchomić dowolny plik jar MapReduce w klastrze HDInsight. W poniższej przykładowej definicji JSON potoku działania HDInsight jest skonfigurowany do uruchamiania pliku Mahout JAR.

```json
{
    "name": "MapReduce Activity for Mahout",
    "description": "Custom MapReduce to generate Mahout result",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
        "jarLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
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
    }
}
```
W sekcji **argumenty** można określić dowolne argumenty programu MapReduce. W czasie wykonywania zostanie wyświetlonych kilka dodatkowych argumentów (na przykład: mapreduce.job.tags) z platformy MapReduce. Aby odróżnić argumenty z MapReduce argumenty, należy rozważyć użycie zarówno opcji i wartości jako argumenty, jak pokazano w poniższym przykładzie (-s, --input, --output itp., są opcje natychmiast po ich wartości).

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły, które wyjaśniają, jak przekształcać dane w inny sposób:

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktywność gałęzi](transform-data-using-hadoop-hive.md)
* [Aktywność świń](transform-data-using-hadoop-pig.md)
* [Aktywność w serwisie Hadoop Streaming](transform-data-using-hadoop-streaming.md)
* [Aktywność iskierki](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wsadowe uczenia maszynowego](transform-data-using-machine-learning.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)
