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
ms.openlocfilehash: e5bd3d10e4e43daf3031aae5083ee917cfe65ede
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417984"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Ustaw aktywność zmiennej w Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Użyj działania Ustaw zmienną, aby ustawić wartość istniejącej zmiennej typu String, bool lub Array zdefiniowanej w potoku Data Factory.

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Wymagany
-------- | ----------- | --------
name | Nazwa działania w potoku | tak
description | Tekst opisujący działanie działania | nie
type | Musi być ustawiona na wartość **Setvariable** | tak
value | Wartość literału ciągu lub obiektu wyrażenia, do którego zostanie przypisana zmienna | tak
variableName | Nazwa zmiennej, która zostanie ustawiona przez to działanie | tak

## <a name="incrementing-a-variable"></a>Zwiększanie zmiennej

Typowy scenariusz obejmujący zmienne w Azure Data Factory używa zmiennej jako iteratora w działaniu until lub foreach. W działaniu z ustawioną zmienną nie można odwoływać się do zmiennej `value` ustawionej w polu. Aby obejść to ograniczenie, należy ustawić zmienną tymczasową, a następnie utworzyć drugie działanie zmiennej zestawu. Działanie drugiego zestawu zmiennych ustawia wartość iteratora na zmienną tymczasową. 

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
