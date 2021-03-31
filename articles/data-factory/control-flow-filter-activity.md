---
title: Działanie filtrowania w Azure Data Factory
description: Działanie filtru filtruje dane wejściowe.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/04/2018
ms.openlocfilehash: 97693d9f31b01bf6187843586f6971c92fe79bff
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786196"
---
# <a name="filter-activity-in-azure-data-factory"></a>Działanie filtrowania w Azure Data Factory
Możesz użyć działania filtru w potoku, aby zastosować wyrażenie filtru do tablicy wejściowej. 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="syntax"></a>Składnia

```json
{
    "name": "MyFilterActivity",
    "type": "filter",
    "typeProperties": {
        "condition": "<condition>",
        "items": "<input array>"
    }
}
```

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
name | Nazwa `Filter` działania. | Ciąg | Tak
typ | Musi być ustawiona na wartość **Filter**. | Ciąg | Tak
rozgrzewa | Warunek, który ma być używany do filtrowania danych wejściowych. | Wyrażenie | Tak
produktów | Tablica wejściowa, w której ma zostać zastosowany filtr. | Wyrażenie | Tak

## <a name="example"></a>Przykład

W tym przykładzie potok ma dwie działania: **Filter** i **foreach**. Działanie filtru jest skonfigurowane do filtrowania tablicy wejściowej dla elementów o wartości większej niż 3. Działanie ForEach następnie wykonuje iterację nad filtrowanymi wartościami i ustawia zmienną **test** na bieżącą wartość.

```json
{
    "name": "PipelineName",
    "properties": {
        "activities": [{
                "name": "MyFilterActivity",
                "type": "filter",
                "typeProperties": {
                    "condition": "@greater(item(),3)",
                    "items": "@pipeline().parameters.inputs"
                }
            },
            {
            "name": "MyForEach",
            "type": "ForEach",
            "dependsOn": [
                {
                    "activity": "MyFilterActivity",
                    "dependencyConditions": [
                        "Succeeded"
                    ]
                }
            ],
            "userProperties": [],
            "typeProperties": {
                "items": {
                    "value": "@activity('MyFilterActivity').output.value",
                    "type": "Expression"
                },
                "isSequential": "false",
                "batchCount": 1,
                "activities": [
                    {
                        "name": "Set Variable1",
                        "type": "SetVariable",
                        "dependsOn": [],
                        "userProperties": [],
                        "typeProperties": {
                            "variableName": "test",
                            "value": {
                                "value": "@string(item())",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        }],
        "parameters": {
            "inputs": {
                "type": "Array",
                "defaultValue": [1, 2, 3, 4, 5, 6]
            }
        },
        "variables": {
            "test": {
                "type": "String"
            }
        },
        "annotations": []
    }
}
```

## <a name="next-steps"></a>Następne kroki
Zobacz inne działania przepływu sterowania obsługiwane przez Data Factory: 

- [Działanie If Condition](control-flow-if-condition-activity.md)
- [Działanie wykonywania potoku](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie pobierania metadanych](control-flow-get-metadata-activity.md)
- [Działanie wyszukiwania](control-flow-lookup-activity.md)
- [Działanie internetowe](control-flow-web-activity.md)
- [Działanie Until](control-flow-until-activity.md)
