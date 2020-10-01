---
title: Tworzenie ustawień diagnostycznych na platformie Azure przy użyciu szablonu Menedżer zasobów
description: Tworzenie ustawień diagnostycznych przy użyciu szablonu Menedżer zasobów do przesyłania dalej dzienników platformy Azure do Azure Monitor dzienników, usługi Azure Storage lub platformy Azure Event Hubs.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 73f2b3ea90cc94fa3411552c7b812fe53eb4dbbb
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91607355"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Tworzenie ustawień diagnostycznych na platformie Azure przy użyciu szablonu Menedżer zasobów
[Ustawienia diagnostyczne](diagnostic-settings.md) w Azure monitor określają miejsce wysyłania [dzienników platformy](platform-logs-overview.md) zbieranych przez zasoby platformy Azure i platformę Azure, od których są one zależne. Ten artykuł zawiera szczegółowe informacje i przykłady dotyczące używania [szablonu Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) do tworzenia i konfigurowania ustawień diagnostycznych w celu zbierania dzienników platformy do różnych miejsc docelowych.

> [!NOTE]
> Ponieważ nie można [utworzyć ustawienia diagnostycznego](diagnostic-settings.md) dla dziennika aktywności platformy Azure przy użyciu programu PowerShell lub interfejsu wiersza polecenia, takiego jak ustawienia diagnostyczne dla innych zasobów platformy Azure, utwórz szablon Menedżer zasobów dla dziennika aktywności, korzystając z informacji podanych w tym artykule i Wdróż szablon przy użyciu programu PowerShell lub interfejsu wiersza polecenia.

## <a name="deployment-methods"></a>Metody wdrażania
Szablony Menedżer zasobów można wdrożyć przy użyciu dowolnej prawidłowej metody, w tym programu PowerShell i interfejsu wiersza polecenia. Ustawienia diagnostyczne dziennika aktywności muszą zostać wdrożone w subskrypcji przy użyciu `az deployment create` interfejsu wiersza polecenia lub programu `New-AzDeployment` PowerShell. Ustawienia diagnostyczne dzienników zasobów muszą zostać wdrożone w grupie zasobów przy użyciu `az group deployment create` interfejsu wiersza polecenia lub programu `New-AzResourceGroupDeployment` PowerShell.

Aby uzyskać szczegółowe informacje, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) i [wdrażania zasobów przy użyciu szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md) . 





## <a name="resource-logs"></a>Dzienniki zasobów
W przypadku dzienników zasobów Dodaj zasób typu `<resource namespace>/providers/diagnosticSettings` do szablonu. Sekcja właściwości jest zgodna z formatem opisanym w [ustawieniach diagnostycznych — Tworzenie lub aktualizowanie](/rest/api/monitor/diagnosticsettings/createorupdate). Podaj `category` w `logs` sekcji dla każdej z kategorii ważnych dla zasobu, który chcesz zebrać. Dodaj `metrics` Właściwość, aby zbierać metryki zasobów do tych samych miejsc docelowych, jeśli [zasób obsługuje metryki](metrics-supported.md).

Poniżej znajduje się szablon, który gromadzi informacje o kategorii dzienników zasobów dla określonego zasobu w obszarze roboczym Log Analytics, koncie magazynu i centrum zdarzeń.

```json
"resources": [
  {
    "type": "/<resource namespace>/providers/diagnosticSettings",
    "name": "[concat(parameters('resourceName'),'/microsoft.insights/', parameters('settingName'))]",
    "dependsOn": [
      "[<resource Id for which resource logs will be enabled>]"
    ],
    "apiVersion": "2017-05-01-preview",
    "properties": {
      "name": "[parameters('settingName')]",
      "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
      "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
      "eventHubName": "[parameters('eventHubName')]",
      "workspaceId": "[parameters('workspaceId')]",
      "logs": [
        {
          "category": "<category name>",
          "enabled": true
        }
      ],
      "metrics": [
        {
          "category": "AllMetrics",
          "enabled": true
        }
      ]
    }
  }
]
```



### <a name="example"></a>Przykład
Poniżej przedstawiono przykład, który tworzy ustawienia diagnostyczne dla ustawienia skalowania automatycznego, które umożliwia przesyłanie strumieniowe dzienników zasobów do centrum zdarzeń, konta magazynu i obszaru roboczego Log Analytics.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "autoscaleSettingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the autoscale setting"
            }
        },
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceIDl of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
    {
      "type": "microsoft.insights/autoscalesettings/providers/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[concat(parameters('autoscaleSettingName'),'/microsoft.insights/', parameters('settingName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/autoscalesettings', parameters('autoscaleSettingName'))]"
      ],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "storageAccountId": "[parameters('storageAccountId')]",
        "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
        "eventHubName": "[parameters('eventHubName')]",
        "logs": [
          {
            "category": "AutoscaleScaleActions",
            "enabled": true
          },
          {
            "category": "AutoscaleEvaluations",
            "enabled": true
          }
        ]
      }
    }
  ]
}
```

## <a name="activity-log"></a>Dziennik aktywności
W przypadku dziennika aktywności platformy Azure Dodaj zasób typu `Microsoft.Insights/diagnosticSettings` . Dostępne kategorie są wymienione w [kategorii w dzienniku aktywności](./activity-log.md#view-the-activity-log). Poniżej znajduje się szablon służący do zbierania wszystkich kategorii dzienników aktywności do obszaru roboczego Log Analytics, konta magazynu i centrum zdarzeń.


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceID of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Insights/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[parameters('settingName')]",
            "location": "global",
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "logs": [
                    {
                        "category": "Administrative",
                        "enabled": true
                    },
                    {
                        "category": "Security",
                        "enabled": true
                    },
                    {
                        "category": "ServiceHealth",
                        "enabled": true
                    },
                    {
                        "category": "Alert",
                        "enabled": true
                    },
                    {
                        "category": "Recommendation",
                        "enabled": true
                    },
                    {
                        "category": "Policy",
                        "enabled": true
                    },
                    {
                        "category": "Autoscale",
                        "enabled": true
                    },
                    {
                        "category": "ResourceHealth",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```


## <a name="next-steps"></a>Następne kroki
* Przeczytaj więcej [na temat dzienników platformy na platformie Azure](platform-logs-overview.md).
* Dowiedz się więcej na temat [ustawień diagnostycznych](diagnostic-settings.md).
