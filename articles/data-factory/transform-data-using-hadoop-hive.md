---
title: Przekształcanie danych przy użyciu działania Gałęzi Hadoop
description: Dowiedz się, jak za pomocą działania gałęzi w fabryce danych platformy Azure do uruchamiania zapytań hive w klastrze HDInsight na żądanie/własne.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 01/15/2019
ms.openlocfilehash: 8c5c917e12b1314c40763f58a7723a4df787ffa0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418936"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu działania gałęzi usługi Hadoop w usłudze Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-hive-activity.md)
> * [Bieżąca wersja](transform-data-using-hadoop-hive.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Działanie hive hdinsight w [potoku](concepts-pipelines-activities.md) fabryki danych wykonuje zapytania hive na [własny](compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastra HDInsight. W tym artykule opiera się na [działaniach transformacji danych,](transform-data.md) który przedstawia ogólny przegląd transformacji danych i obsługiwanych działań transformacji.

Jeśli jesteś nowy w usłudze Azure Data Factory, przeczytaj [wprowadzenie do usługi Azure Data Factory](introduction.md) i wykonaj [samouczek: przekształć dane](tutorial-transform-data-spark-powershell.md) przed przeczytaniem tego artykułu. 

## <a name="syntax"></a>Składnia

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\HiveScripts\\MyHiveSript.hql",
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
| Właściwość            | Opis                                                  | Wymagany |
| ------------------- | ------------------------------------------------------------ | -------- |
| name                | Nazwa działania                                         | Tak      |
| description         | Tekst opisujący, do czego jest używane działanie                | Nie       |
| type                | W przypadku aktywności hive typ działania to HDinsightHive        | Tak      |
| linkedServiceName   | Odwołanie do klastra HDInsight zarejestrowanego jako usługa połączona w fabryce danych. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz Artykuł [dotyczący powiązanych usług obliczeń.](compute-linked-services.md) | Tak      |
| scriptLinkedService | Odwołanie do usługi Linked Service usługi Azure Storage używane do przechowywania skryptu hive do wykonania. Jeśli nie określisz tej usługi połączonej, używana jest usługa linked service usługi Azure Storage zdefiniowana w usłudze połączonej usługi HDInsight. | Nie       |
| scriptPath          | Podaj ścieżkę do pliku skryptu przechowywanego w usłudze Azure Storage, o której mowa w scriptLinkedService. W nazwie pliku rozróżniana jest wielkość liter. | Tak      |
| getDebugInfo        | Określa, kiedy pliki dziennika są kopiowane do usługi Azure Storage używane przez klaster HDInsight (lub) określone przez scriptLinkedService. Dozwolone wartości: Brak, Zawsze lub Błąd. Wartość domyślna: None. | Nie       |
| Argumenty           | Określa tablicę argumentów dla zadania Hadoop. Argumenty są przekazywane jako argumenty wiersza polecenia do każdego zadania. | Nie       |
| Definiuje             | Określ parametry jako pary klucz/wartość do odwoływania się w skrypcie gałęzi. | Nie       |
| Querytimeout        | Wartość limitu czasu kwerendy (w minutach). Ma zastosowanie, gdy klaster HDInsight jest włączony z włączonym pakietem zabezpieczeń przedsiębiorstwa. | Nie       |

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły, które wyjaśniają, jak przekształcać dane w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktywność świń](transform-data-using-hadoop-pig.md)
* [Działanie mapreduce](transform-data-using-hadoop-map-reduce.md)
* [Aktywność w serwisie Hadoop Streaming](transform-data-using-hadoop-streaming.md)
* [Aktywność iskierki](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wsadowe uczenia maszynowego](transform-data-using-machine-learning.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)
