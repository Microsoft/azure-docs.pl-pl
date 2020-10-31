---
title: Tworzenie grup akcji przy użyciu szablonów Menedżer zasobów
description: Dowiedz się, jak utworzyć grupę akcji przy użyciu szablonu Azure Resource Manager.
author: dkamstra
services: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 01fb93a4b74a35501d0684b822ea83fc7b20770a
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130582"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>Tworzenie grupy akcji przy użyciu szablonu Menedżer zasobów
W tym artykule pokazano, jak skonfigurować grupy akcji przy użyciu [szablonu Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) . Za pomocą szablonów można automatycznie konfigurować grupy akcji, które mogą być ponownie używane w niektórych typach alertów. Te grupy akcji zapewniają, że wszystkie odpowiednie strony są powiadamiane po wyzwoleniu alertu.

Podstawowe kroki są następujące:

1. Utwórz szablon jako plik JSON, który opisuje sposób tworzenia grupy akcji.

2. Wdróż szablon przy użyciu [dowolnej metody wdrażania](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="resource-manager-templates-for-an-action-group"></a>Menedżer zasobów szablonów dla grupy akcji

Aby utworzyć grupę akcji przy użyciu szablonu Menedżer zasobów, należy utworzyć zasób typu `Microsoft.Insights/actionGroups` . Następnie Wypełnij wszystkie powiązane właściwości. Poniżej przedstawiono dwa przykładowe szablony, które tworzą grupę akcji.

Pierwszy szablon zawiera opis sposobu tworzenia szablonu Menedżer zasobów dla grupy akcji, w której definicje akcji są zakodowane w szablonie. Drugi szablon zawiera opis sposobu tworzenia szablonu, który pobiera informacje o konfiguracji elementu webhook jako parametry wejściowe podczas wdrażania szablonu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com"
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com"
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1"
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    },
    "webhookReceiverName": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service Name."
      }
    },    
    "webhookServiceUri": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    }    
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
        ],
        "emailReceivers": [
        ],
        "webhookReceivers": [
          {
            "name": "[parameters('webhookReceiverName')]",
            "serviceUri": "[parameters('webhookServiceUri')]"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupResourceId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej na temat [grup akcji](./action-groups.md).
* Dowiedz się więcej o [alertach](alerts-overview.md).
* Dowiedz się, jak dodawać [alerty przy użyciu szablonu Menedżer zasobów](./alerts-activity-log.md).

