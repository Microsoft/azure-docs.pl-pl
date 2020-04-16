---
title: Wykonywanie potoków usługi Azure Machine Learning
description: Dowiedz się, jak uruchomić potoki usługi Azure Machine Learning w potokach usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.date: 10/10/2019
ms.openlocfilehash: f033651eb7e52ba60cce9b74941a4ef0eb376d2b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419004"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Wykonywanie potoków usługi Azure Machine Learning w fabryce danych platformy Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Uruchom potoki usługi Azure Machine Learning jako krok w potokach usługi Azure Data Factory. Działanie Potok wykonywania uczenia maszynowego umożliwia przewidywanie partii scenariuszy, takich jak identyfikowanie możliwych domyślnych kredytów, określanie tonacji i analizowanie wzorców zachowania klientów.

Poniższy film zawiera sześciominutowe wprowadzenie i demonstrację tej funkcji.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

## <a name="syntax"></a>Składnia

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Dozwolone wartości | Wymagany
-------- | ----------- | -------------- | --------
name | Nazwa działania w potoku | Ciąg | Tak
type | Typ działania to "AzureMLExecutePipeline" | Ciąg | Tak
linkedServiceName | Usługa połączona z usługą Azure Machine Learning | Odwołanie do usługi połączonej | Tak
mlPipelineId (ida) | Identyfikator opublikowanego potoku usługi Azure Machine Learning | Ciąg (lub wyrażenie z wynikiemTylią ciągu) | Tak
nazwa eksperymentu | Uruchom nazwę eksperymentu historii przebiegu potoku usługi Machine Learning | Ciąg (lub wyrażenie z wynikiemTylią ciągu) | Nie
mlPipelineParameters | Klucz, pary wartości mają być przekazywane do opublikowanego punktu końcowego potoku usługi Azure Machine Learning. Klucze muszą być zgodne z nazwami parametrów potoku zdefiniowanych w opublikowanym potoku usługi Machine Learning | Obiekt z parami wartości klucza (lub wyrażenie z wynikiemType obiektu) | Nie
mlParentRunId | Identyfikator uruchomienia potoku nadrzędnego usługi Azure Machine Learning | Ciąg (lub wyrażenie z wynikiemTylią ciągu) | Nie
continueOnStepFailure | Czy kontynuować wykonywanie innych kroków w potoku uczenia maszynowego, jeśli krok zakończy się niepowodzeniem | wartość logiczna | Nie

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły, które wyjaśniają, jak przekształcać dane w inny sposób:

* [Wykonywanie działania przepływ danych](control-flow-execute-data-flow-activity.md)
* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktywność gałęzi](transform-data-using-hadoop-hive.md)
* [Aktywność świń](transform-data-using-hadoop-pig.md)
* [Działanie mapreduce](transform-data-using-hadoop-map-reduce.md)
* [Aktywność w serwisie Hadoop Streaming](transform-data-using-hadoop-streaming.md)
* [Aktywność iskierki](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)
