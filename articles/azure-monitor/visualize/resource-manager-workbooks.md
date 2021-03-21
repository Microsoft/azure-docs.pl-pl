---
title: Przykłady szablonów Menedżer zasobów dla skoroszytów
description: Przykładowe szablony Azure Resource Manager do wdrożenia Azure Monitor skoroszytów.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/18/2020
ms.openlocfilehash: bcd477d30c74f12e2836f41facec23103547c31b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043511"
---
# <a name="resource-manager-template-samples-for-workbooks-in-azure-monitor"></a>Przykłady szablonów Menedżer zasobów dla skoroszytów w Azure Monitor
Ten artykuł zawiera przykładowe [szablony Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) do tworzenia skoroszytów w programie Azure monitor. Każdy przykład zawiera plik szablonu i plik parametrów z przykładowymi wartościami do udostępnienia szablonowi.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

Skoroszyty mogą być złożone, więc typową strategią jest utworzenie skoroszytu w Azure Portal a następnie wygenerowanie szablonu Menedżer zasobów. Zobacz szczegóły tej metody w [Azure Resource Manager szablonu do wdrażania skoroszytów](../visualize/workbooks-automate.md).

## <a name="create-a-workbook"></a>Utwórz skoroszyt
Poniższy przykład tworzy prosty skoroszyt.


### <a name="template-file"></a>Plik szablonu

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="parameter-file"></a>Plik parametrów

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName": {
            "value": "Sample Hello World workbook"
        },
      "workbookType": {
        "value": "workbook"
      },
      "workbookSourceId": {
        "value": "Azure Monitor"
      }
    }
}
```

## <a name="next-steps"></a>Następne kroki

* [Pobierz inne przykładowe szablony dla Azure monitor](../resource-manager-samples.md).
* [Dowiedz się więcej na temat grup akcji](../visualize/workbooks-overview.md).
