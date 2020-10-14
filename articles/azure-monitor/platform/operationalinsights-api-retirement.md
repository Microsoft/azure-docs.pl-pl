---
title: Wycofanie interfejsu API Azure Monitor
description: Opisuje wycofanie starszych wersji interfejsu API dostawcy zasobów OperationalInsights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: c9a7ba63246f747a132c315e22452ca9c7144448
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058186"
---
# <a name="operationalinsights-api-version-retirement"></a>Wycofanie wersji interfejsu API OperationalInsights
Firma Microsoft zapewnia powiadomienie co najmniej 12 miesięcy przed wycofaniem interfejsu API w celu zapewnienia sprawnego przejścia do nowszej/obsługiwanej wersji. Firma Microsoft udostępniła nową wersję (2020-08-01) dla interfejsów API dostawcy zasobów **OperationalInsights** i wycofa wszystkie starsze wersje interfejsu API w dniu 31 października 2023. Ponieważ nowe funkcje i funkcje i optymalizacje są dodawane tylko do bieżącego interfejsu API, należy przeprowadzić uaktualnienie do najnowszej wersji interfejsu API tak szybko, jak to możliwe.

Po 31 października 2023 Azure Monitor nie będą już obsługiwać wcześniejszych wersji interfejsów API niż 2020-08-01. Jeśli wolisz nie aktualizować, żądania wysyłane z wcześniejszych wersji będą nadal obsługiwane przez usługę Azure Monitor do 31 października 2023.

## <a name="migration-steps"></a>Kroki migracji
W zależności od używanej metody konfiguracji należy zaktualizować nową wersję w **szablonach**żądania **rest** i Menedżer zasobów. Aby zaktualizować wersję interfejsu API, postępuj zgodnie z poniższymi przykładami:

1. Żądania interfejsu API REST używają wersji interfejsu API w adresie URL żądania. Zastąp tę wersję najnowszą wersją (2020-08-01), jak pokazano w poniższym przykładzie.

    ```rest
    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
    ```

2. Szablony Azure Resource Manager korzystają z wersji interfejsu API we właściwości **apiVersion** zasobu. Zastąp tę wersję najnowszą wersją (2020-08-01), jak pokazano w poniższym przykładzie.


    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string",
                "metadata": {
                "description": "Name of the workspace."
                }
            },
            "resources": [
            {
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('workspaceName')]",
                "apiVersion": "2020-08-01",
                "location": "westus",
                "properties": {
                    "sku": {
                        "name": "pergb2018"
                    },
                    "retentionInDays": 30,
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0,
                        "enableLogAccessUsingOnlyResourcePermissions": true
                    }
                }
            }
        ]
    }
    }
    ```


## <a name="next-steps"></a>Następne kroki

- Zobacz [odwołanie do interfejsu API obszaru roboczego OperationalInsights](https://docs.microsoft.com/rest/api/loganalytics/workspaces).
