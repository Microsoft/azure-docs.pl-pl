---
title: Ustaw aktywność zmiennej w Azure Data Factory
description: Dowiedz się, jak użyć działania Ustaw zmienną, aby ustawić wartość istniejącej zmiennej zdefiniowanej w potoku Data Factory
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/07/2020
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.openlocfilehash: 113829dd35c14b5efae39c55a8085dcd2c1ecef4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786162"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Ustaw aktywność zmiennej w Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Użyj działania Ustaw zmienną, aby ustawić wartość istniejącej zmiennej typu String, bool lub Array zdefiniowanej w potoku Data Factory.

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Wymagane
-------- | ----------- | --------
name | Nazwa działania w potoku | tak
description (opis) | Tekst opisujący działanie działania | nie
typ | Musi być ustawiona na wartość **Setvariable** | tak
wartość | Wartość literału ciągu lub obiektu wyrażenia, do którego jest przypisana zmienna | tak
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
