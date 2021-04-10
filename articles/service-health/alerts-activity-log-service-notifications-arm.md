---
title: Odbieraj alerty dziennika aktywności w powiadomieniach usługi platformy Azure przy użyciu szablonu Menedżer zasobów
description: Otrzymuj powiadomienia za pośrednictwem wiadomości SMS, poczty e-mail lub elementu webhook w przypadku wystąpienia usługi platformy Azure.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: 532fbae505e0bcaa6ab31a2e935362114537d134
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594950"
---
# <a name="quickstart-create-activity-log-alerts-on-service-notifications-using-an-arm-template"></a>Szybki Start: tworzenie alertów dziennika aktywności dla powiadomień usługi przy użyciu szablonu ARM

W tym artykule opisano sposób konfigurowania alertów dziennika aktywności dla powiadomień o kondycji usługi przy użyciu szablonu Azure Resource Manager (szablon ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Powiadomienia o kondycji usługi są przechowywane w [dzienniku aktywności platformy Azure](../azure-monitor/essentials/platform-logs-overview.md). W przypadku potencjalnie dużej ilości informacji przechowywanych w dzienniku aktywności istnieje oddzielny interfejs użytkownika ułatwiający wyświetlanie i Konfigurowanie alertów dotyczących powiadomień o kondycji usługi.

Możesz otrzymać Alert, gdy platforma Azure wyśle powiadomienia o kondycji usługi do subskrypcji platformy Azure. Alert można skonfigurować na podstawie:

- Klasa powiadomień o kondycji usługi (problemy z usługą, planowana konserwacja, klasyfikatory kondycji).
- Objęta subskrypcją.
- Uwzględnione usługi.
- Uwzględnione regiony.

> [!NOTE]
> Powiadomienia o kondycji usługi nie wysyłają alertów dotyczących zdarzeń związanych z kondycją zasobów.

Możesz również skonfigurować osobę, do której ma być wysyłany Alert:

- Wybierz istniejącą grupę akcji.
- Utwórz nową grupę akcji (która może być używana w przyszłych alertach).

Aby dowiedzieć się więcej na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi](../azure-monitor/alerts/action-groups.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Aby uruchomić polecenia z komputera lokalnego, zainstaluj interfejs wiersza polecenia platformy Azure lub moduły Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Instalowanie Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Przegląd szablonu

Poniższy szablon umożliwia utworzenie grupy akcji z miejscem docelowym poczty e-mail i włączenie wszystkich powiadomień o kondycji usługi dla subskrypcji docelowej. Zapisz ten szablon jako *CreateServiceHealthAlert.js*.

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

- [Microsoft. Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft. Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdróż szablon przy użyciu dowolnej standardowej metody [wdrażania szablonu ARM](../azure-resource-manager/templates/deploy-portal.md) , takiego jak poniższe przykłady przy użyciu interfejsu wiersza polecenia i programu PowerShell. Zastąp przykładowe wartości dla **grupy zasobów** i **EmailAddress** z odpowiednimi wartościami dla danego środowiska.

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

Sprawdź, czy obszar roboczy został utworzony przy użyciu jednego z następujących poleceń. Zastąp przykładowe wartości dla **grupy zasobów** wartością użytą powyżej.

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

- Poznaj [najlepsze rozwiązania dotyczące konfigurowania Azure Service Health alertów](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Dowiedz się, jak [skonfigurować mobilne powiadomienia wypychane dla Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Dowiedz się, jak [skonfigurować powiadomienia elementu webhook dla istniejących systemów zarządzania problemami](service-health-alert-webhook-guide.md).
- Dowiedz się więcej o [powiadomieniach o kondycji usługi](service-notifications.md).
- Dowiedz się więcej na temat [ograniczania liczby powiadomień](../azure-monitor/alerts/alerts-rate-limiting.md).
- Przejrzyj [schemat elementu webhook alertu dziennika aktywności](../azure-monitor/alerts/activity-log-alerts-webhook.md).
- Zapoznaj się z [omówieniem alertów dziennika aktywności](../azure-monitor/alerts/alerts-overview.md)i Dowiedz się, jak otrzymywać alerty.
- Dowiedz się więcej na temat [grup akcji](../azure-monitor/alerts/action-groups.md).
