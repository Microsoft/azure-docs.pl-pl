---
title: Tworzenie Azure Advisor alertów dotyczących nowych zaleceń przy użyciu Resource Manager szablonu
description: Dowiedz się, jak skonfigurować alert dla nowych zaleceń z usługi Azure Advisor użyciu szablonu Azure Resource Manager (szablonu usługi ARM).
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.date: 06/29/2020
ms.openlocfilehash: 716ab104ce6517aeb554b42522e5906829877259
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765671"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>Szybki start: tworzenie alertów Azure Advisor dotyczących nowych zaleceń przy użyciu szablonu usługi ARM

W tym artykule pokazano, jak skonfigurować alert dla nowych zaleceń z usługi Azure Advisor użyciu szablonu Azure Resource Manager (szablonu usługi ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Zawsze Azure Advisor wykryje nowe zalecenie dla jednego z twoich zasobów, zdarzenie jest przechowywane w dzienniku [aktywności platformy Azure.](../azure-monitor/essentials/platform-logs-overview.md) Alerty dla tych zdarzeń można skonfigurować na Azure Advisor przy użyciu funkcji tworzenia alertów specyficznych dla rekomendacji. Możesz wybrać subskrypcję i opcjonalnie grupę zasobów, aby określić zasoby, dla których chcesz otrzymywać alerty.

Typy zaleceń można również określić przy użyciu następujących właściwości:

- Kategoria
- Poziom wpływu
- Typ zalecenia

Możesz również skonfigurować akcję, która będzie miała miejsce po wyzwoleniu alertu przez:

- Wybieranie istniejącej grupy akcji
- Tworzenie nowej grupy akcji

Aby dowiedzieć się więcej na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi.](../azure-monitor/alerts/action-groups.md)

> [!NOTE]
> Alerty usługi Advisor są obecnie dostępne tylko w przypadku rekomendacji dotyczących wysokiej dostępności, wydajności i kosztów. Zalecenia dotyczące zabezpieczeń nie są obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Aby uruchamiać polecenia z komputera lokalnego, zainstaluj interfejs wiersza polecenia platformy Azure lub Azure PowerShell modułów. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i Instalowanie [Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Przegląd szablonu

Poniższy szablon tworzy grupę akcji z docelową pocztą e-mail i włącza wszystkie powiadomienia o kondycji usługi dla subskrypcji docelowej. Zapisz ten szablon jakoCreateAdvisorAlert.js *w pliku*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "apiVersion": "2019-06-01",
      "name": "[parameters('actionGroups_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [],
      "tags": {},
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      }
    },
    {
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
```

Szablon definiuje dwa zasoby:

- [Microsoft.Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wd wdrażaj szablon przy użyciu dowolnej standardowej metody wdrażania szablonu [usługi ARM,](../azure-resource-manager/templates/deploy-portal.md) takiej jak poniższe przykłady przy użyciu interfejsu wiersza polecenia i programu PowerShell. Zastąp przykładowe wartości **dla grup zasobów** i **emailAddress** odpowiednimi wartościami dla twojego środowiska. Nazwa obszaru roboczego musi być unikatowa wśród wszystkich subskrypcji platformy Azure.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateAdvisorAlert --resource-group my-resource-group --template-file CreateAdvisorAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateAdvisorAlert -ResourceGroupName my-resource-group -TemplateFile CreateAdvisorAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Sprawdź, czy obszar roboczy został utworzony przy użyciu jednego z następujących poleceń. Zastąp przykładowe wartości dla **grupy zasobów** wartością u używaną powyżej.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name AdvisorAlertsTest
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name AdvisorAlertsTest
```

---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start i samouczkami, możesz pozostawić te zasoby na miejscu. Gdy grupa zasobów nie będzie już potrzebna, usuń ją, co spowoduje usunięcie reguły alertu i powiązanych zasobów. Aby usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli
az group delete --name my-resource-group
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```powershell
Remove-AzResourceGroup -Name my-resource-group
```

---

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się [z omówieniem alertów dziennika aktywności](../azure-monitor/alerts/alerts-overview.md)i dowiedz się, jak otrzymywać alerty.
- Dowiedz się więcej o [grupach akcji.](../azure-monitor/alerts/action-groups.md)
