---
title: Wycofanie interfejsu API Azure Monitor
description: Opisuje wycofanie starszych wersji interfejsu API dostawcy zasobów OperationalInsights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/29/2020
ms.openlocfilehash: 03e534564b4a32f112c2aaff44a187b10eb2a537
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723116"
---
# <a name="operationalinsights-api-version-retirement"></a>Wycofanie wersji interfejsu API OperationalInsights
Firma Microsoft zapewnia powiadomienie co najmniej 12 miesięcy przed wycofaniem interfejsu API w celu zapewnienia sprawnego przejścia do nowszej/obsługiwanej wersji. Firma Microsoft udostępniła nową wersję (2020-08-01) dla interfejsów API dostawcy zasobów **OperationalInsights** i wycofa wszystkie starsze wersje interfejsu API w dniu 29 lutego 2024.

Zachęcamy do korzystania z wersji 2020-08-01 teraz w celu uzyskania korzyści płynących z nowych funkcji, takich jak [dedykowany klaster](./logs-dedicated-clusters.md), [klucze zarządzane przez klienta](../logs/customer-managed-keys.md), [prywatne łącze](./private-link-security.md) i [eksport danych](./logs-data-export.md). Ponadto nowe funkcje i funkcje i optymalizacje są dodawane tylko do bieżącego interfejsu API.

Po 29 lutego 2024 Azure Monitor nie będzie już obsługiwać wcześniejszych wersji interfejsów API niż 2020-08-01. Jeśli wolisz nie aktualizować, żądania wysyłane z wcześniejszych wersji będą nadal obsługiwane przez usługę Azure Monitor do 29 lutego 2024.

## <a name="migration-steps"></a>Kroki migracji
W zależności od używanej metody konfiguracji należy zaktualizować nową wersję w **szablonach** żądania **rest** i Menedżer zasobów. Aby zaktualizować wersję interfejsu API, postępuj zgodnie z poniższymi przykładami:

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


### <a name="more-information"></a>Więcej informacji
Jeśli masz pytania, uzyskaj odpowiedzi od [naszych specjalistów ds. społeczności Tech]( https://techcommunity.microsoft.com/t5/azure-monitor/bd-p/AzureMonitor). Jeśli masz plan pomocy technicznej i potrzebujesz pomocy technicznej, Utwórz [żądanie pomocy technicznej]( https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest): 
1.  W obszarze *typ problemu* wybierz pozycję **techniczne**. 
2.  W obszarze *Subskrypcja* wybierz swoją subskrypcję. 
3.  W obszarze *Usługa* wybierz pozycję **Moje usługi**, a następnie wybierz pozycję **log Analytics**. 
4.  W obszarze *Podsumowanie* wpisz opis problemu. 
5.  W obszarze *typ problemu* wybierz pozycję **log Analytics zarządzanie obszarem roboczym**.  
6.  W obszarze *podtyp problemu* wybierz pozycję **Szablony ARM, program PowerShell i interfejs wiersza polecenia**. 

## <a name="next-steps"></a>Następne kroki

- Zobacz [odwołanie do interfejsu API obszaru roboczego OperationalInsights](/rest/api/loganalytics/workspaces).