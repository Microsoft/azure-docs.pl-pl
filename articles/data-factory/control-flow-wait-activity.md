---
title: Działanie oczekiwania w Azure Data Factory
description: Działanie oczekiwania wstrzymuje wykonywanie potoku w określonym przedziale czasu.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: e6158938d01b6e5da74ed046d2a74e0dfd827f47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81417916"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Działanie wykonywania oczekiwania w Azure Data Factory
Gdy używasz działania Wait w potoku, potok czeka przez określony okres z kontynuowaniem wykonywania kolejnych działań. 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


## <a name="syntax"></a>Składnia

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
name | Nazwa `Wait` działania. | Ciąg | Tak
typ | Musi być ustawiony na wartość **czekaj**. | Ciąg | Tak
waitTimeInSeconds | Liczba sekund oczekiwania przez potok przed kontynuowaniem przetwarzania. | Liczba całkowita | Tak

## <a name="example"></a>Przykład

> [!NOTE]
> Ta sekcja zawiera definicje JSON i przykładowe polecenia programu PowerShell umożliwiające uruchomienie potoku. Aby zapoznać się z instrukcjami krok po kroku dotyczącymi tworzenia potoku Data Factory przy użyciu definicji Azure PowerShell i JSON, zobacz [Samouczek: Tworzenie fabryki danych przy użyciu Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Potok z działaniem oczekiwania
W tym przykładzie potok ma dwie działania: **do** **czekania i oczekiwania**. Działanie oczekiwania jest skonfigurowane tak, aby czekało na sekundę. Potok uruchamia działanie sieci Web w pętli z upływem jednego sekundy czasu oczekiwania między każdym przebiegiem. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
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
