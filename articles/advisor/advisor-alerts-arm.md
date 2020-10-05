---
title: Tworzenie alertów Azure Advisor dla nowych zaleceń przy użyciu szablonu Menedżer zasobów
description: Dowiedz się, jak skonfigurować alert dotyczący nowych zaleceń z Azure Advisor przy użyciu szablonu Azure Resource Manager (szablon ARM).
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: f03bf6eaf4f3045e00fc67efe6faa9f53d962089
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91629900"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>Szybki Start: tworzenie alertów Azure Advisor na temat nowych zaleceń przy użyciu szablonu ARM

W tym artykule opisano sposób konfigurowania alertu dotyczącego nowych zaleceń z Azure Advisor przy użyciu szablonu Azure Resource Manager (szablon ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Za każdym razem, gdy Azure Advisor wykryje nowe zalecenie dla jednego z zasobów, zdarzenie jest przechowywane w [dzienniku aktywności platformy Azure](../azure-monitor/platform/platform-logs-overview.md). Alerty dotyczące tych zdarzeń można skonfigurować w Azure Advisor przy użyciu funkcji tworzenia alertów specyficznych dla zalecenia. Możesz wybrać subskrypcję i opcjonalnie grupę zasobów, aby określić zasoby, dla których chcesz otrzymywać alerty.

Możesz również określić typy zaleceń, korzystając z następujących właściwości:

- Kategoria
- Poziom wpływu
- Typ zalecenia

Możesz również skonfigurować akcję, która będzie wykonywana po wyzwoleniu alertu przez:

- Wybieranie istniejącej grupy akcji
- Tworzenie nowej grupy akcji

Aby dowiedzieć się więcej na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi](../azure-monitor/platform/action-groups.md).

> [!NOTE]
> Alerty usługi Advisor są obecnie dostępne tylko w celu uzyskania wysokiej dostępności, wydajności i zaleceń dotyczących kosztów. Zalecenia dotyczące zabezpieczeń nie są obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Aby uruchomić polecenia z komputera lokalnego, zainstaluj interfejs wiersza polecenia platformy Azure lub moduły Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Instalowanie Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Przegląd szablonu

Poniższy szablon umożliwia utworzenie grupy akcji z miejscem docelowym poczty e-mail i włączenie wszystkich powiadomień o kondycji usługi dla subskrypcji docelowej. Zapisz ten szablon jako *CreateAdvisorAlert.js*.

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

- [Microsoft. Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft. Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdróż szablon przy użyciu dowolnej standardowej metody [wdrażania szablonu ARM](../azure-resource-manager/templates/deploy-portal.md) , takiego jak poniższe przykłady przy użyciu interfejsu wiersza polecenia i programu PowerShell. Zastąp przykładowe wartości dla **grupy zasobów**i **EmailAddress** z odpowiednimi wartościami dla danego środowiska. Nazwa obszaru roboczego musi być unikatowa wśród wszystkich subskrypcji platformy Azure.

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

Sprawdź, czy obszar roboczy został utworzony przy użyciu jednego z następujących poleceń. Zastąp przykładowe wartości dla **grupy zasobów** wartością użytą powyżej.

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

Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki Start i samouczkami, możesz pozostawić te zasoby na miejscu. Gdy grupa zasobów nie będzie już konieczna, usuń ją, usuwając regułę alertu i powiązane zasoby. Aby usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell

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

- Zapoznaj się z [omówieniem alertów dziennika aktywności](../azure-monitor/platform/alerts-overview.md)i Dowiedz się, jak otrzymywać alerty.
- Dowiedz się więcej na temat [grup akcji](../azure-monitor/platform/action-groups.md).
