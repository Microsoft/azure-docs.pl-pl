---
title: Ustaw aktywność zmiennej w Azure Data Factory
description: Dowiedz się, jak użyć działania Ustaw zmienną, aby ustawić wartość istniejącej zmiennej zdefiniowanej w potoku Data Factory
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
ms.openlocfilehash: a0b5fa16658d3e354bcb4f90ad998997fc844a84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83832793"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Ustaw aktywność zmiennej w Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Użyj działania Ustaw zmienną, aby ustawić wartość istniejącej zmiennej typu String, bool lub Array zdefiniowanej w potoku Data Factory.

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Wymagane
-------- | ----------- | --------
name | Nazwa działania w potoku | tak
description | Tekst opisujący działanie działania | nie
typ | Musi być ustawiona na wartość **Setvariable** | tak
value | Wartość literału ciągu lub obiektu wyrażenia, do którego jest przypisana zmienna | tak
variableName | Nazwa zmiennej, która jest ustawiona przez to działanie | tak

## <a name="incrementing-a-variable"></a>Zwiększanie zmiennej

Typowy scenariusz obejmujący zmienne w Azure Data Factory używa zmiennej jako iteratora w działaniu until lub foreach. W działaniu z ustawioną zmienną nie można odwoływać się do zmiennej ustawionej w `value` polu. Aby obejść to ograniczenie, należy ustawić zmienną tymczasową, a następnie utworzyć drugie działanie zmiennej zestawu. Działanie drugiego zestawu zmiennych ustawia wartość iteratora na zmienną tymczasową. 

Poniżej znajduje się przykład tego wzorca:

![Zwiększ zmienną](media/control-flow-set-variable-activity/increment-variable.png "Zwiększ zmienną")

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
Informacje na temat działania związanego z przepływem sterowania obsługiwanego przez Data Factory: 

- [Działanie dołączania zmiennej](control-flow-append-variable-activity.md)
