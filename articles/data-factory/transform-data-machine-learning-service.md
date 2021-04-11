---
title: Wykonaj potoki Azure Machine Learning
description: Dowiedz się, jak uruchamiać potoki Azure Machine Learning w potokach Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.author: weetok
author: dcstwh
ms.date: 07/16/2020
ms.openlocfilehash: d0b941325d6bbabcb083b5f71a40c145d3ef48d0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103474"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Wykonaj potoki Azure Machine Learning w Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Uruchom potoki Azure Machine Learning jako krok w potokach Azure Data Factory. Działanie Machine Learning wykonywania potoku włącza scenariusze prognozowania partii, takie jak identyfikowanie możliwych wartości domyślnych pożyczek, Określanie tonacji oraz analizowanie wzorców zachowania klientów.

Poniższe wideo oferuje sześć minut wprowadzenie tej funkcji.

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

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
name | Nazwa działania w potoku | Ciąg | Tak
typ | Typ działania to "AzureMLExecutePipeline" | Ciąg | Tak
linkedServiceName | Połączona usługa do Azure Machine Learning | Odwołanie do połączonej usługi | Tak
mlPipelineId | Identyfikator opublikowanego potoku Azure Machine Learning | Ciąg (lub wyrażenie z typem ResultType ciągu) | Tak
eksperymentname | Nazwa eksperymentu uruchomienia dla Machine Learning uruchomienia potoku | Ciąg (lub wyrażenie z typem ResultType ciągu) | Nie
mlPipelineParameters | Klucz, pary wartości do przesłania do opublikowanego punktu końcowego potoku Azure Machine Learning. Klucze muszą być zgodne z nazwami parametrów potoku zdefiniowanych w opublikowanym potoku Machine Learning | Obiekt z parami wartości klucza (lub wyrażeniem z obiektem ResultType) | Nie
mlParentRunId | Identyfikator uruchomienia potoku Azure Machine Learning nadrzędnego | Ciąg (lub wyrażenie z typem ResultType ciągu) | Nie
dataPathAssignments | Słownik używany do zmieniania ścieżek datapath w usłudze Azure Machine Learning. Włącza przełączanie ścieżek datapaths | Obiekt z parami wartości klucza | Nie
continueOnStepFailure | Czy kontynuować wykonywanie innych kroków w potoku Machine Learning uruchomienie w przypadku niepowodzenia kroku | boolean | Nie

> [!NOTE]
> Aby wypełnić elementy rozwijane w Machine Learning nazwa i identyfikator potoku, użytkownik musi mieć uprawnienia do listy potoków ML. Interfejs użytkownika usługi ADF wywołuje interfejsy API AzureMLService bezpośrednio przy użyciu poświadczeń zalogowanych użytkowników.  

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami, które wyjaśniają sposób przekształcania danych w inny sposób:

* [Działanie wykonywania przepływu danych](control-flow-execute-data-flow-activity.md)
* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie Hive](transform-data-using-hadoop-hive.md)
* [Aktywność trzody chlewnej](transform-data-using-hadoop-pig.md)
* [Działanie MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działanie przesyłania strumieniowego Hadoop](transform-data-using-hadoop-streaming.md)
* [Działanie platformy Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)
