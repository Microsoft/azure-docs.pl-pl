---
title: Otrzymywanie alertów dziennika aktywności w powiadomieniach usługi platformy Azure przy użyciu Resource Manager szablonu
description: Otrzymuj powiadomienia za pośrednictwem wiadomości SMS, wiadomości e-mail lub webhook, gdy wystąpi usługa platformy Azure.
ms.date: 06/29/2020
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 730c023de61275d95fe594642149770af42b34b9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535750"
---
# <a name="quickstart-create-activity-log-alerts-on-service-notifications-using-an-arm-template"></a>Szybki start: tworzenie alertów dziennika aktywności dla powiadomień usługi przy użyciu szablonu usługi ARM

W tym artykule pokazano, jak skonfigurować alerty dziennika aktywności dla powiadomień dotyczących kondycji usługi przy użyciu szablonu Azure Resource Manager usługi Arm.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Powiadomienia o kondycji usługi są przechowywane w dzienniku [aktywności platformy Azure.](../azure-monitor/essentials/platform-logs-overview.md) Biorąc pod uwagę potencjalnie dużą ilość informacji przechowywanych w dzienniku aktywności, istnieje oddzielny interfejs użytkownika ułatwiający wyświetlanie i konfigurowanie alertów dotyczących powiadomień o kondycji usługi.

Gdy platforma Azure wysyła powiadomienia dotyczące kondycji usługi do subskrypcji platformy Azure, możesz otrzymać alert. Alert można skonfigurować na podstawie:

- Klasa powiadomień dotyczących kondycji usługi (problemy z usługą, planowana konserwacja, poradniki dotyczące kondycji).
- Dotyczy subskrypcji.
- Dotyczy to usług.
- Dotyczy to regionów.

> [!NOTE]
> Powiadomienia o kondycji usługi nie wysyłają alertów dotyczących zdarzeń dotyczących kondycji zasobów.

Możesz również skonfigurować, do kogo ma zostać wysłany alert:

- Wybierz istniejącą grupę akcji.
- Utwórz nową grupę akcji (która może być używana w przypadku przyszłych alertów).

Aby dowiedzieć się więcej na temat grup akcji, zobacz Tworzenie grup akcji [i zarządzanie nimi.](../azure-monitor/alerts/action-groups.md)

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Aby uruchamiać polecenia z komputera lokalnego, zainstaluj interfejs wiersza polecenia platformy Azure lub Azure PowerShell modułów. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i Instalowanie [Azure PowerShell.](/powershell/azure/install-az-ps)

## <a name="review-the-template"></a>Przegląd szablonu

Poniższy szablon tworzy grupę akcji z docelową pocztą e-mail i włącza wszystkie powiadomienia o kondycji usługi dla subskrypcji docelowej. Zapisz ten szablon jakoCreateServiceHealthAlert.js *w pliku*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "type": "String",
      "defaultValue": "SubHealth"
    },
    "activityLogAlerts_name": {
      "type": "String",
      "defaultValue": "ServiceHealthActivityLogAlert"
    },
    "emailAddress": {
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
      "comments": "Service Health Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ],
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ServiceHealth"
            },
            {
              "field": "properties.incidentType",
              "equals": "Incident"
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
      }
    }
  ]
}
```

Szablon definiuje dwa zasoby:

- [Microsoft.Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wd wdrażaj szablon przy użyciu dowolnej standardowej metody wdrażania szablonu [usługi ARM,](../azure-resource-manager/templates/deploy-portal.md) takiej jak poniższe przykłady przy użyciu interfejsu wiersza polecenia i programu PowerShell. Zastąp przykładowe wartości pól **Grupa zasobów** i **emailAddress** odpowiednimi wartościami dla swojego środowiska.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateServiceHealthAlert --resource-group my-resource-group --template-file CreateServiceHealthAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateServiceHealthAlert -ResourceGroupName my-resource-group -TemplateFile CreateServiceHealthAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Sprawdź, czy obszar roboczy został utworzony przy użyciu jednego z następujących poleceń. Zastąp przykładowe wartości dla **grupy zasobów** wartością u używaną powyżej.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name ServiceHealthActivityLogAlert
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name ServiceHealthActivityLogAlert
```

---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start i samouczkami, warto pozostawić te zasoby na miejscu. Gdy grupa zasobów nie będzie już potrzebna, usuń ją, co spowoduje usunięcie reguły alertu i powiązanych zasobów. Aby usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell

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

- Dowiedz się [więcej o najlepszych rozwiązaniach dotyczących konfigurowania Azure Service Health alertów.](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa)
- Dowiedz się, jak [skonfigurować powiadomienia wypychane dla urządzeń przenośnych Azure Service Health.](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw)
- Dowiedz się, jak [skonfigurować powiadomienia dotyczące urządzeń webhook dla istniejących systemów zarządzania problemami.](service-health-alert-webhook-guide.md)
- Dowiedz się więcej [o powiadomieniach dotyczących kondycji usługi.](service-notifications.md)
- Dowiedz się więcej [na temat ograniczania liczby powiadomień.](../azure-monitor/alerts/alerts-rate-limiting.md)
- Przejrzyj schemat [alertu dziennika aktywności dla webhook](../azure-monitor/alerts/activity-log-alerts-webhook.md).
- Zapoznaj się [z omówieniem alertów dziennika aktywności](../azure-monitor/alerts/alerts-overview.md)i dowiedz się, jak otrzymywać alerty.
- Dowiedz się więcej o [grupach akcji.](../azure-monitor/alerts/action-groups.md)
