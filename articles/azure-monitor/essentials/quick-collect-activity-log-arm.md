---
title: Wyślij dziennik aktywności platformy Azure do Log Analytics obszaru roboczego przy użyciu szablonu Azure Resource Manager
description: Użyj szablonów ARM, aby utworzyć obszar roboczy Log Analytics i ustawienia diagnostyczne do wysyłania dziennika aktywności do Azure Monitor dzienników.
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 56810cffcb9665810c452276be34e6924fd992b2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033286"
---
# <a name="quickstart-send-azure-activity-log-to-log-analytics-workspace-using-an-arm-template"></a>Szybki Start: wysyłanie dziennika aktywności platformy Azure do Log Analytics obszaru roboczego przy użyciu szablonu ARM

Dziennik aktywności jest to dziennik platformy Azure, który zapewnia wgląd w zdarzenia na poziomie subskrypcji. Zawiera on takie informacje jak czas zmodyfikowania zasobu lub czas uruchomienia maszyny wirtualnej. Dziennik aktywności można wyświetlić w Azure Portal lub pobrać wpisów przy użyciu programu PowerShell i interfejsu wiersza polecenia. W tym przewodniku szybki start pokazano, jak używać szablonów Azure Resource Manager (szablony ARM) do tworzenia Log Analytics obszaru roboczego i ustawienia diagnostycznego w celu wysyłania dziennika aktywności do dzienników Azure Monitor, w których można je analizować przy użyciu [zapytań dzienników](../logs/log-query-overview.md) i włączyć inne funkcje, takie jak [alerty dzienników](../alerts/alerts-log-query.md) i [skoroszyty](../visualize/workbooks-overview.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Aby uruchomić polecenia z komputera lokalnego, zainstaluj interfejs wiersza polecenia platformy Azure lub moduły Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Instalowanie Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="create-a-log-analytics-workspace"></a>Tworzenie obszaru roboczego usługi Log Analytics

### <a name="review-the-template"></a>Przegląd szablonu

Poniższy szablon tworzy pusty obszar roboczy Log Analytics. Zapisz ten szablon jako *CreateWorkspace.js*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the workspace."
      }
    },
    "sku": {
      "type": "string",
      "defaultValue": "pergb2018",
      "allowedValues": [
        "pergb2018",
        "Free",
        "Standalone",
        "PerNode",
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Pricing tier: PerGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "australiacentral",
        "australiaeast",
        "australiasoutheast",
        "brazilsouth",
        "canadacentral",
        "centralindia",
        "centralus",
        "eastasia",
        "eastus",
        "eastus2",
        "francecentral",
        "japaneast",
        "koreacentral",
        "northcentralus",
        "northeurope",
        "southafricanorth",
        "southcentralus",
        "southeastasia",
        "switzerlandnorth",
        "switzerlandwest",
        "uksouth",
        "ukwest",
        "westcentralus",
        "westeurope",
        "westus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for the workspace."
      }
    },
    "retentionInDays": {
      "type": "int",
      "defaultValue": 120,
      "metadata": {
        "description": "Number of days to retain data."
      }
    },
    "resourcePermissions": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "true to use resource or workspace permissions. false to require workspace permissions."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "properties": {
        "sku": {
          "name": "[parameters('sku')]"
        },
        "retentionInDays": "[parameters('retentionInDays')]",
        "features": {
          "searchVersion": 1,
          "legacy": 0,
          "enableLogAccessUsingOnlyResourcePermissions": "[parameters('resourcePermissions')]"
        }
      }
    }
  ]
}
```

Ten szablon definiuje jeden zasób:

- [Microsoft. OperationalInsights/obszary robocze](/azure/templates/microsoft.operationalinsights/workspaces)

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdróż szablon przy użyciu dowolnej standardowej metody [wdrażania szablonu ARM](../../azure-resource-manager/templates/deploy-portal.md) , takiego jak poniższe przykłady przy użyciu interfejsu wiersza polecenia i programu PowerShell. Zastąp przykładowe wartości dla **grupy zasobów**, **obszaru roboczegoname** i **lokalizacji** odpowiednimi wartościami dla danego środowiska. Nazwa obszaru roboczego musi być unikatowa wśród wszystkich subskrypcji platformy Azure.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli
az login
az deployment group create \
    --name CreateWorkspace \
    --resource-group my-resource-group \
    --template-file CreateWorkspace.json \
    --parameters workspaceName='my-workspace-01' location='eastus'

```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile CreateWorkspace.json -workspaceName my-workspace-01 -location eastus
```

---

### <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Sprawdź, czy obszar roboczy został utworzony przy użyciu jednego z następujących poleceń. Zastąp przykładowe wartości dla **grupy zasobów** i **obszaru roboczegoname** wartościami użytymi powyżej.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli
az monitor log-analytics workspace show --resource-group my-workspace-01 --workspace-name my-resource-group
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```powershell
Get-AzOperationalInsightsWorkspace -Name my-workspace-01 -ResourceGroupName my-resource-group
```

---

## <a name="create-diagnostic-setting"></a>Tworzenie ustawienia diagnostycznego

### <a name="review-the-template"></a>Przegląd szablonu

Poniższy szablon tworzy ustawienie diagnostyczne, które wysyła dziennik aktywności do obszaru roboczego Log Analytics. Zapisz ten szablon jako *CreateDiagnosticSetting.js*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "settingName": {
        "type": "String"
    },
    "workspaceId": {
        "type": "String"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[parameters('settingName')]",
      "dependsOn": [],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "logs": [
          {
          "category": "Administrative",
          "enabled": true
          },
          {
          "category": "Alert",
          "enabled": true
          },
          {
          "category": "Autoscale",
          "enabled": true
          },
          {
          "category": "Policy",
          "enabled": true
          },
          {
          "category": "Recommendation",
          "enabled": true
          },
          {
          "category": "ResourceHealth",
          "enabled": true
          },
          {
          "category": "Security",
          "enabled": true
          },
          {
          "category": "ServiceHealth",
          "enabled": true
          }
        ]
      }
    }
  ]
}
```

Ten szablon definiuje jeden zasób:

- [Microsoft. Insights/diagnosticSettings](/azure/templates/microsoft.insights/diagnosticsettings)

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdróż szablon przy użyciu dowolnej standardowej metody [wdrażania szablonu ARM](../../azure-resource-manager/templates/deploy-portal.md) , takiego jak poniższe przykłady przy użyciu interfejsu wiersza polecenia i programu PowerShell. Zastąp przykładowe wartości dla **grupy zasobów**, **obszaru roboczegoname** i **lokalizacji** odpowiednimi wartościami dla danego środowiska. Nazwa obszaru roboczego musi być unikatowa wśród wszystkich subskrypcji platformy Azure.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli
az deployment sub create --name CreateDiagnosticSetting --location eastus --template-file CreateDiagnosticSetting.json --parameters settingName='Send Activity log to workspace' workspaceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace-01'

```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```powershell
New-AzSubscriptionDeployment -Name CreateDiagnosticSetting -location eastus -TemplateFile CreateDiagnosticSetting.json -settingName "Send Activity log to workspace" -workspaceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace-01"
```
---

### <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Sprawdź, czy ustawienie diagnostyczne zostało utworzone przy użyciu jednego z następujących poleceń. Zastąp przykładowe wartości dla subskrypcji i nazwy ustawienia wartościami użytymi powyżej.

> [!NOTE]
> Nie można obecnie pobrać ustawień diagnostycznych poziomu subskrypcji przy użyciu programu PowerShell.

```azurecli
az monitor diagnostic-settings show --resource '/subscriptions/00000000-0000-0000-0000-000000000000' --name 'Send Activity log to workspace'
```

## <a name="generate-log-data"></a>Generowanie danych dziennika

Tylko nowe wpisy dziennika aktywności będą wysyłane do obszaru roboczego Log Analytics, więc wykonaj pewne działania w ramach subskrypcji, które będą rejestrowane, takie jak uruchamianie lub zatrzymywanie maszyny wirtualnej lub tworzenie lub modyfikowanie innego zasobu. Może być konieczne odczekanie kilku minut na utworzenie ustawienia diagnostycznego i początkowy zapis danych w obszarze roboczym. Po tym opóźnieniu wszystkie zdarzenia zapisywane w dzienniku aktywności będą wysyłane do obszaru roboczego w ciągu kilku sekund.

## <a name="retrieve-data-with-a-log-query"></a>Pobieranie danych przy użyciu zapytania dziennika

Użyj Azure Portal, aby użyć Log Analytics do pobierania danych z obszaru roboczego. W Azure Portal Wyszukaj, a następnie wybierz pozycję **Monitoruj**.

![Azure Portal](media/quick-collect-activity-log/azure-portal-monitor.png)

Wybierz pozycję **dzienniki** w menu **Azure monitor** . Zamknij **przykładową stronę zapytań** . Jeśli zakres nie jest ustawiony w utworzonym obszarze roboczym, kliknij pozycję **Wybierz zakres** i Znajdź go.

![Zakres Log Analytics](media/quick-collect-activity-log/log-analytics-scope.png)

W oknie zapytania wpisz, `AzureActivity` a następnie kliknij przycisk **Uruchom**. Jest to proste zapytanie, które zwraca wszystkie rekordy w tabeli *usługi Azure* , która zawiera wszystkie rekordy wysyłane z dziennika aktywności.

![Proste zapytanie](media/quick-collect-activity-log/query-01.png)

Rozwiń jeden z rekordów, aby wyświetlić jego szczegółowe właściwości.

![Rozwiń właściwości](media/quick-collect-activity-log/expand-properties.png)

Spróbuj użyć bardziej złożonej kwerendy, takiej jak, `AzureActivity | summarize count() by CategoryValue` która zawiera liczbę zdarzeń podsumowywanych według kategorii.

![Zapytanie złożone](media/quick-collect-activity-log/query-02.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

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

W tym przewodniku szybki start skonfigurowano wysyłanie dziennika aktywności do obszaru roboczego Log Analytics. Teraz można skonfigurować inne dane do zebrania w obszarze roboczym, w którym można je analizować przy użyciu [zapytań dzienników](../logs/log-query-overview.md) w Azure monitor i korzystać z funkcji, takich jak [alerty dzienników](../alerts/alerts-log-query.md) i [skoroszyty](../visualize/workbooks-overview.md). Następnie należy zebrać [dzienniki zasobów](../essentials/resource-logs.md) z zasobów platformy Azure, które pomogą uzyskać szczegółowe dane w dzienniku aktywności.

> [!div class="nextstepaction"]
> [Zbieranie i analizowanie dzienników zasobów przy użyciu Azure Monitor](../essentials/tutorial-resource-logs.md)