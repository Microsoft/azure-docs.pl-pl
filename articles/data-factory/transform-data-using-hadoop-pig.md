---
title: Przekształcanie danych przy użyciu aktywności świni Hadoop
description: Dowiedz się, jak za pomocą działania świni w fabryce danych platformy Azure do uruchamiania skryptów Świnia na żądanie/własnego klastra HDInsight.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 01/16/2018
ms.openlocfilehash: 73ce8c670940a31af6a88f98bfd5880ede259e01
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418902"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu aktywności świni Hadoop w fabryce danych platformy Azure

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-pig-activity.md)
> * [Bieżąca wersja](transform-data-using-hadoop-pig.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Działania HDInsight Pig w [potoku](concepts-pipelines-activities.md) fabryki danych wykonuje zapytania Pig na [własną](compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastra HDInsight. W tym artykule opiera się na [działaniach transformacji danych,](transform-data.md) który przedstawia ogólny przegląd transformacji danych i obsługiwanych działań transformacji.

Jeśli jesteś nowy w usłudze Azure Data Factory, przeczytaj [wprowadzenie do usługi Azure Data Factory](introduction.md) i wykonaj [samouczek: przekształć dane](tutorial-transform-data-spark-powershell.md) przed przeczytaniem tego artykułu. 

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

| Właściwość            | Opis                              | Wymagany |
| ------------------- | ---------------------------------------- | -------- |
| name                | Nazwa działania                     | Tak      |
| description         | Tekst opisujący, do czego jest używane działanie | Nie       |
| type                | W przypadku aktywności hive typem działania jest HDinsightPig | Tak      |
| linkedServiceName   | Odwołanie do klastra HDInsight zarejestrowanego jako usługa połączona w fabryce danych. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz Artykuł [dotyczący powiązanych usług obliczeń.](compute-linked-services.md) | Tak      |
| scriptLinkedService | Odwołanie do usługi Azure Storage Linked Service używane do przechowywania skryptu Świcie do wykonania. Jeśli nie określisz tej usługi połączonej, używana jest usługa linked service usługi Azure Storage zdefiniowana w usłudze połączonej usługi HDInsight. | Nie       |
| scriptPath          | Podaj ścieżkę do pliku skryptu przechowywanego w usłudze Azure Storage, o której mowa w scriptLinkedService. W nazwie pliku rozróżniana jest wielkość liter. | Nie       |
| getDebugInfo        | Określa, kiedy pliki dziennika są kopiowane do usługi Azure Storage używane przez klaster HDInsight (lub) określone przez scriptLinkedService. Dozwolone wartości: Brak, Zawsze lub Błąd. Wartość domyślna: None. | Nie       |
| Argumenty           | Określa tablicę argumentów dla zadania Hadoop. Argumenty są przekazywane jako argumenty wiersza polecenia do każdego zadania. | Nie       |
| Definiuje             | Określ parametry jako pary klucz/wartość do odwoływania się w skrypcie Świni. | Nie       |

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły, które wyjaśniają, jak przekształcać dane w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktywność gałęzi](transform-data-using-hadoop-hive.md)
* [Działanie mapreduce](transform-data-using-hadoop-map-reduce.md)
* [Aktywność w serwisie Hadoop Streaming](transform-data-using-hadoop-streaming.md)
* [Aktywność iskierki](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wsadowe uczenia maszynowego](transform-data-using-machine-learning.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)
