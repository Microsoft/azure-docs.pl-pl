---
title: Przekształcanie danych za pomocą działania usługi Hadoop
description: Dowiedz się, jak za pomocą działania świni w usłudze Azure Data Factory uruchamiać skrypty wieprzowe na żądanie/własny klaster usługi HDInsight.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: 8b6f75204f64f892f80abb4890796551bffd22a7
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368965"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu działania w ramach programu Hadoop w Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-pig-activity.md)
> * [Bieżąca wersja](transform-data-using-hadoop-pig.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Działanie świni w [usłudze](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight w [potoku](concepts-pipelines-activities.md) Data Factory wykonuje zapytania dotyczące trzody chlewnej we [własnym lub lokalnym klastrze usługi](compute-linked-services.md#azure-hdinsight-linked-service) HDInsight. W tym artykule przedstawiono artykuł [działania przekształcania danych](transform-data.md) , który zawiera ogólne omówienie transformacji danych i obsługiwanych działań transformacji.

Jeśli jesteś nowym do Azure Data Factory, przeczytaj artykuł [wprowadzenie do Azure Data Factory](introduction.md) i wykonaj [Samouczek: Przekształcanie danych](tutorial-transform-data-spark-powershell.md) przed przeczytaniem tego artykułu. 

## <a name="syntax"></a>Składnia

```json
{
    "name": "Pig Activity",
    "description": "description",
    "type": "HDInsightPig",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\PigScripts\\MyPigSript.pig",
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

| Właściwość            | Opis                              | Wymagane |
| ------------------- | ---------------------------------------- | -------- |
| name                | Nazwa działania                     | Tak      |
| description         | Tekst opisujący działanie używanego działania | Nie       |
| typ                | W przypadku działania programu Hive typem działania jest HDinsightPig | Tak      |
| linkedServiceName   | Odwołanie do klastra usługi HDInsight zarejestrowanego jako połączona usługa w Data Factory. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz artykuł dotyczący [połączonych usług obliczeniowych](compute-linked-services.md) . | Tak      |
| Elementu scriptlinkedservice | Odwołanie do połączonej usługi Azure Storage użytej do przechowania skryptu wieprzowego do wykonania. W tym miejscu są obsługiwane tylko połączone usługi **[BLOB Storage platformy Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)** i **[ADLS Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)** . Jeśli nie określisz tej połączonej usługi, zostanie użyta połączona usługa Azure Storage zdefiniowana w połączonej usłudze HDInsight. | Nie       |
| scriptPath          | Podaj ścieżkę do pliku skryptu przechowywanego w usłudze Azure Storage, która jest określona przez elementu scriptlinkedservice. W nazwie pliku rozróżniana jest wielkość liter. | Nie       |
| GetDebugInfo —        | Określa, kiedy pliki dziennika są kopiowane do usługi Azure Storage używanej przez klaster HDInsight (lub) określonej przez elementu scriptlinkedservice. Dozwolone wartości: brak, zawsze lub niepowodzenie. Wartość domyślna: None. | Nie       |
| argumentu           | Określa tablicę argumentów zadania usługi Hadoop. Argumenty są przesyłane jako argumenty wiersza polecenia do każdego zadania. | Nie       |
| definiuje             | Określ parametry jako pary klucz/wartość do odwołania w skrypcie trzody chlewnej. | Nie       |

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami, które wyjaśniają sposób przekształcania danych w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie Hive](transform-data-using-hadoop-hive.md)
* [Działanie MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działanie przesyłania strumieniowego Hadoop](transform-data-using-hadoop-streaming.md)
* [Działanie platformy Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wykonywania wsadowego w Azure Machine Learning Studio (klasycznej)](transform-data-using-machine-learning.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)
