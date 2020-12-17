---
title: Przykłady szablonów Menedżer zasobów dla Application Insights zasobów
description: Przykładowe szablony Azure Resource Manager do wdrażania zasobów Application Insights w Azure Monitor.
ms.subservice: application-insights
ms.topic: sample
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/08/2020
ms.openlocfilehash: 5818f1ffac9c89e24c1a12cf92fc5fbf1ddf2386
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657230"
---
# <a name="resource-manager-template-samples-for-creating-application-insights-resources"></a>Przykłady szablonów Menedżer zasobów do tworzenia zasobów Application Insights

Ten artykuł zawiera przykładowe [szablony Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) do wdrażania i konfigurowania [klasycznych zasobów Application Insights](../app/create-new-resource.md) i nowych [zasobów Application Insights opartych na obszarze roboczym](../app/create-workspace-resource.md). Każdy przykład zawiera plik szablonu i plik parametrów z przykładowymi wartościami do udostępnienia szablonowi.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="classic-application-insights-resource"></a>Zasób klasyczny Application Insights

Poniższy przykład tworzy [zasób klasyczny Application Insights](../app/create-new-resource.md). 

### <a name="template-file"></a>Plik szablonu

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string",
            "metadata": {
            "description": "Name of Application Insights resource."
          }
        },
        "type": {
            "type": "string",
            "metadata": {
            "description": "Type of app you are deploying. This field is for legacy reasons and will not impact the type of App Insights resource you deploy."
          }
        },
        "regionId": {
            "type": "string",
            "metadata": {
            "description": "Which Azure Region to deploy the resource to. This must be a valid Azure regionId."
          }
        },
        "tagsArray": {
            "type": "object",
            "metadata": {
            "description": "See documentation on tags:https://docs.microsoft.com/azure/azure-resource-manager/management/tag-resources."
          }
        },
        "requestSource": {
            "type": "string",
            "metadata": {
            "description": "Source of Azure Resource Manager deployment"
        }
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2014-08-01",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Plik parametrów

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "my_app_insights_resource"
        },
        "regionId": {
            "value": "westus2"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "CustomDeployment"
        }
    }
}
```

## <a name="workspace-based-application-insights-resource"></a>Zasób Application Insights oparty na obszarze roboczym 

Poniższy przykład tworzy [zasób Application Insights oparty na obszarze roboczym](../app/create-workspace-resource.md). Application Insights oparte na obszarze roboczym są obecnie dostępne w **wersji zapoznawczej**. 


### <a name="template-file"></a>Plik szablonu

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string",
            "metadata": {
            "description": "Name of Application Insights resource."
          }
        },
        "type": {
            "type": "string",
            "metadata": {
            "description": "Type of app you are deploying. This field is for legacy reasons and will not impact the type of App Insights resource you deploy."
          }
        },
        "regionId": {
            "type": "string",
            "metadata": {
            "description": "Which Azure Region to deploy the resource to. This must be a valid Azure regionId."
          }
        },
        "tagsArray": {
            "type": "object",
            "metadata": {
            "description": "See documentation on tags:https://docs.microsoft.com/azure/azure-resource-manager/management/tag-resources."
            }
        },
        "requestSource": {
            "type": "string",
            "metadata": {
            "description": "Source of Azure Resource Manager deployment"
        }
        },
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
            "description": "Log Analytics workspace ID to associate with your Application Insights resource."
        }
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Plik parametrów

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "name": {
      "value": "my_workspace_based_resource"
    },
    "type": {
      "value": "web"
    },
    "regionId": {
      "value": "westus2"
    },
    "tagsArray": {
      "value": {}
    },
    "requestSource": {
      "value": "CustomDeployment"
    },
    "workspaceResourceId": {
      "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testxxxx/providers/microsoft.operationalinsights/workspaces/testworkspace"
    }
  }
}
```

## <a name="next-steps"></a>Następne kroki

* [Pobierz inne przykładowe szablony dla Azure monitor](resource-manager-samples.md).
* [Dowiedz się więcej o klasycznych zasobach Application Insights](../app/create-new-resource.md).
* [Dowiedz się więcej o zasobach Application Insights opartych na obszarze roboczym](../app/create-workspace-resource.md).
