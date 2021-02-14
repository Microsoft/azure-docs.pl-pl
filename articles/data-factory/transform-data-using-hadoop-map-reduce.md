---
title: Przekształcanie danych przy użyciu działania MapReduce usługi Hadoop
description: Dowiedz się, jak przetwarzać dane przez uruchamianie programów MapReduce Hadoop w klastrze usługi Azure HDInsight z fabryki danych Azure.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: f03906586d6226c92cfa69e1a139d4c876cbf723
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375888"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu działania MapReduce usługi Hadoop w Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-map-reduce.md)
> * [Bieżąca wersja](transform-data-using-hadoop-map-reduce.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Działanie usługi HDInsight MapReduce w [potoku](concepts-pipelines-activities.md) Data Factory wywołuje program MapReduce na swoim klastrze [usługi](compute-linked-services.md#azure-hdinsight-linked-service) HDInsight lub [na żądanie](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . W tym artykule przedstawiono artykuł [działania przekształcania danych](transform-data.md) , który zawiera ogólne omówienie transformacji danych i obsługiwanych działań transformacji.

Jeśli jesteś nowym do Azure Data Factory, przeczytaj artykuł [wprowadzenie do Azure Data Factory](introduction.md) i wykonaj samouczek: [Samouczek: Przekształcanie danych](tutorial-transform-data-spark-powershell.md) przed przeczytaniem tego artykułu.

Aby uzyskać szczegółowe informacje na temat uruchamiania skryptów świń/Hive w klastrze usługi HDInsight z poziomu potoku przy użyciu działań dotyczących trzody chlewnej i Hive, zobacz [świnie](transform-data-using-hadoop-pig.md) i [Hive](transform-data-using-hadoop-hive.md) .

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

| Właściwość          | Opis                              | Wymagane |
| ----------------- | ---------------------------------------- | -------- |
| name              | Nazwa działania                     | Tak      |
| description (opis)       | Tekst opisujący działanie używanego działania | Nie       |
| typ              | Dla działania MapReduce typem działania jest HDinsightMapReduce | Tak      |
| linkedServiceName | Odwołanie do klastra usługi HDInsight zarejestrowanego jako połączona usługa w Data Factory. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz artykuł dotyczący [połączonych usług obliczeniowych](compute-linked-services.md) . | Tak      |
| Nazwą         | Nazwa klasy, która ma zostać wykonana         | Tak      |
| jarLinkedService  | Odwołanie do połączonej usługi Azure Storage użytej do przechowywania plików jar. W tym miejscu są obsługiwane tylko połączone usługi **[BLOB Storage platformy Azure](./connector-azure-blob-storage.md)** i **[ADLS Gen2](./connector-azure-data-lake-storage.md)** . Jeśli nie określisz tej połączonej usługi, zostanie użyta połączona usługa Azure Storage zdefiniowana w połączonej usłudze HDInsight. | Nie       |
| jarFilePath       | Podaj ścieżkę do plików jar przechowywanych w magazynie platformy Azure, do których odwołuje się jarLinkedService. W nazwie pliku rozróżniana jest wielkość liter. | Tak      |
| jarlibs           | Tablica ciągów ścieżek do plików z biblioteką jar, do których odwołuje się zadanie przechowywane w magazynie platformy Azure zdefiniowanym w jarLinkedService. W nazwie pliku rozróżniana jest wielkość liter. | Nie       |
| GetDebugInfo —      | Określa, kiedy pliki dziennika są kopiowane do usługi Azure Storage używanej przez klaster HDInsight (lub) określonej przez jarLinkedService. Dozwolone wartości: brak, zawsze lub niepowodzenie. Wartość domyślna: None. | Nie       |
| argumentu         | Określa tablicę argumentów zadania usługi Hadoop. Argumenty są przesyłane jako argumenty wiersza polecenia do każdego zadania. | Nie       |
| definiuje           | Określ parametry jako pary klucz/wartość dla odwołania w skrypcie Hive. | Nie       |



## <a name="example"></a>Przykład
Możesz użyć działania MapReduce usługi HDInsight, aby uruchomić dowolny plik JAR MapReduce w klastrze usługi HDInsight. W poniższej przykładowej definicji JSON potoku, działanie usługi HDInsight jest skonfigurowane do uruchamiania pliku JAR Mahout.

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
Można określić dowolne argumenty dla programu MapReduce w sekcji **argumenty** . W czasie wykonywania zobaczysz kilka dodatkowych argumentów (na przykład: MapReduce. job. Tags) ze środowiska MapReduce Framework. Aby odróżnić argumenty od argumentów MapReduce, należy rozważyć użycie zarówno opcji, jak i wartości jako argumentów, jak pokazano w poniższym przykładzie (-s,--dane wejściowe,--danych wyjściowych itp., są opcjami natychmiast po ich wartości).

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami, które wyjaśniają sposób przekształcania danych w inny sposób:

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie Hive](transform-data-using-hadoop-hive.md)
* [Aktywność trzody chlewnej](transform-data-using-hadoop-pig.md)
* [Działanie przesyłania strumieniowego Hadoop](transform-data-using-hadoop-streaming.md)
* [Działanie platformy Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wykonywania wsadowego w Azure Machine Learning Studio (klasycznej)](transform-data-using-machine-learning.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)