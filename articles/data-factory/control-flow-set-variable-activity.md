---
title: Ustawianie aktywności zmiennej w fabryce danych platformy Azure
description: Dowiedz się, jak ustawić wartość istniejącej zmiennej zdefiniowanej w potoku Fabryki danych za pomocą działania Ustaw zmienną.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/07/2020
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: e5bd3d10e4e43daf3031aae5083ee917cfe65ede
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417984"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Ustawianie aktywności zmiennej w fabryce danych platformy Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Użyj działania Ustaw zmienną, aby ustawić wartość istniejącej zmiennej typu String, Bool lub Array zdefiniowanej w potoku fabryki danych.

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Wymagany
-------- | ----------- | --------
name | Nazwa działania w potoku | tak
description | Tekst opisujący działanie | nie
type | Musi być ustawiona na **SetVariable** | tak
value | Wartość obiektu literału ciągu lub wyrażenia, do których zostanie przypisana zmienna | tak
Variablename | Nazwa zmiennej, która zostanie ustawiona przez to działanie | tak

## <a name="incrementing-a-variable"></a>Zwiększanie zmiennej

Typowy scenariusz obejmujący zmienne w usłudze Azure Data Factory używa zmiennej jako iteratora w ramach działania do lub foreach. W ustawienym działaniu zmiennej nie można `value` odwoływać się do zmiennej ustawionej w tym polu. Aby obejść to ograniczenie, ustaw zmienną tymczasową, a następnie utwórz działanie zmiennej drugiego zestawu. Działanie zmiennej drugiego zestawu ustawia wartość iteratora na zmienną tymczasową. 

Poniżej znajduje się przykład tego wzorca:

![Zmienna przyrostu](media/control-flow-set-variable-activity/increment-variable.png "Zmienna przyrostu")

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```


## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o powiązanej aktywności przepływu sterowania obsługiwanej przez fabrykę danych: 

- [Działanie dołączania zmiennej](control-flow-append-variable-activity.md)
