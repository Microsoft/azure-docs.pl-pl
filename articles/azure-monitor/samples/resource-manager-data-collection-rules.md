---
title: Przykłady szablonów Menedżer zasobów dla reguł zbierania danych
description: Przykładowe szablony Azure Resource Manager do tworzenia skojarzeń między regułami zbierania danych i maszynami wirtualnymi w programie Azure Monitor.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: 4983e4b7f76c627cb3cb3081bc12e27111b2b521
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98951465"
---
# <a name="resource-manager-template-samples-for-data-collection-rules-in-azure-monitor"></a>Przykłady szablonów Menedżer zasobów dla reguł zbierania danych w programie Azure Monitor
Ten artykuł zawiera przykładowe [szablony Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) do wdrożenia i skonfigurowania [agenta log Analytics](../platform/log-analytics-agent.md) i [rozszerzenia diagnostycznego](../platform/diagnostics-extension-overview.md) dla maszyn wirtualnych w Azure monitor. Każdy przykład zawiera plik szablonu i plik parametrów z przykładowymi wartościami do udostępnienia szablonowi.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-association-with-azure-vm"></a>Utwórz skojarzenie z maszyną wirtualną platformy Azure

Poniższy przykład instaluje agenta Azure Monitor na maszynie wirtualnej platformy Microsoft Azure. Skojarzenie jest tworzone między maszyną wirtualną platformy Azure a regułą zbierania danych.

### <a name="template-file"></a>Plik szablonu

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('vmName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this virtual machine.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
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
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```

## <a name="create-association-with-azure-arc"></a>Utwórz skojarzenie z usługą Azure Arc

Poniższy przykład instaluje agenta Azure Monitor na maszynie wirtualnej platformy Microsoft Azure. Skojarzenie jest tworzone między komputerem serwera z obsługą usługi Azure Arc i regułą zbierania danych.

### <a name="template-file"></a>Plik szablonu

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.HybridCompute/machines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('machineName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this Arc server.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
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
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```


## <a name="next-steps"></a>Następne kroki

* [Pobierz inne przykładowe szablony dla Azure monitor](resource-manager-samples.md).
* [Dowiedz się więcej o log Analytics agencie](../platform/log-analytics-agent.md).
* [Dowiedz się więcej o rozszerzeniu diagnostyki](../platform/diagnostics-extension-overview.md).
