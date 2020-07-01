---
title: Wyślij dziennik aktywności platformy Azure do Log Analytics obszaru roboczego przy użyciu szablonu Azure Resource Manager
description: Użyj szablonów ARM, aby utworzyć obszar roboczy Log Analytics i ustawienia diagnostyczne do wysyłania dziennika aktywności do Azure Monitor dzienników.
ms.subservice: logs
ms.topic: quickstart
ms.custom: subject-armqs
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: ce7c8df0fcea66d21ba2640ba26213a49efcb1c0
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85601653"
---
# <a name="quickstart-send-azure-activity-log-to-log-analytics-workspace-using-an-arm-template"></a>Szybki Start: wysyłanie dziennika aktywności platformy Azure do Log Analytics obszaru roboczego przy użyciu szablonu ARM
Dziennik aktywności to dziennik platformy na platformie Azure, który zapewnia wgląd w zdarzenia na poziomie subskrypcji. Obejmuje to takie informacje, jak w przypadku zmodyfikowania zasobu lub uruchomienia maszyny wirtualnej. Dziennik aktywności można wyświetlić w Azure Portal lub pobrać wpisów przy użyciu programu PowerShell i interfejsu wiersza polecenia. W tym przewodniku szybki start pokazano, jak używać szablonów Azure Resource Manager (szablony ARM) do tworzenia Log Analytics obszaru roboczego i ustawienia diagnostycznego w celu wysyłania dziennika aktywności do dzienników Azure Monitor, w których można je analizować przy użyciu [zapytań dzienników](../log-query/log-query-overview.md) i włączyć inne funkcje, takie jak [alerty dzienników](../platform/alerts-log-query.md) i [skoroszyty](../platform/workbooks-overview.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

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
      "allowedValues": [
        "pergb2018",
        "Free",
        "Standalone",
        "PerNode",
        "Standard",
        "Premium"
      ],
      "defaultValue": "pergb2018",
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
      "apiVersion": "2020-03-01-preview",
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
Wdróż szablon przy użyciu dowolnej standardowej metody [wdrażania szablonu ARM](../../azure-resource-manager/templates/deploy-portal.md) , takiego jak poniższe przykłady przy użyciu interfejsu wiersza polecenia i programu PowerShell. Zastąp przykładowe wartości dla **grupy zasobów**, **obszaru roboczegoname**i **lokalizacji** odpowiednimi wartościami dla danego środowiska. Nazwa obszaru roboczego musi być unikatowa wśród wszystkich subskrypcji platformy Azure.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI1)

```azurecli
az login
az deployment group create \
    --name CreateWorkspace \
    --resource-group my-resource-group \
    --template-file CreateWorkspace.json \
    --parameters workspaceName='my-workspace-01' location='eastus'

```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell1)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile CreateWorkspace.json -workspaceName my-workspace-01 -location eastus
```

---

### <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia
Sprawdź, czy obszar roboczy został utworzony przy użyciu jednego z następujących poleceń. Zastąp przykładowe wartości dla **grupy zasobów** i **obszaru roboczegoname** wartościami użytymi powyżej.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI2)

```azurecli
az monitor log-analytics workspace show --resource-group my-workspace-01 --workspace-name my-resource-group
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell2)

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
Wdróż szablon przy użyciu dowolnej standardowej metody [wdrażania szablonu ARM](/azure-resource-manager/templates/deploy-portal) , takiego jak poniższe przykłady przy użyciu interfejsu wiersza polecenia i programu PowerShell. Zastąp przykładowe wartości dla **grupy zasobów**, **obszaru roboczegoname**i **lokalizacji** odpowiednimi wartościami dla danego środowiska. Nazwa obszaru roboczego musi być unikatowa wśród wszystkich subskrypcji platformy Azure.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI3)

```azurecli
az deployment sub create --name CreateDiagnosticSetting --location eastus --template-file CreateDiagnosticSetting.json --parameters settingName='Send Activity log to workspace' workspaceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace-01'

```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell3)

```powershell
New-AzSubscriptionDeployment -Name CreateDiagnosticSetting -location eastus -TemplateFile CreateDiagnosticSetting.json -settingName="Send Activity log to workspace" -workspaceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace-01"
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

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki Start i samouczkami, możesz pozostawić te zasoby na miejscu. Gdy grupa zasobów nie będzie już konieczna, usuń ją, usuwając regułę alertu i powiązane zasoby. Aby usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell


 
# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI3)

```azurecli
az group delete --name my-resource-group
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell3)

```powershell
Remove-AzResourceGroup -Name my-resource-group
```

---

## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybki start skonfigurowano wysyłanie dziennika aktywności do obszaru roboczego Log Analytics. Teraz można skonfigurować inne dane do zebrania w obszarze roboczym, w którym można je analizować przy użyciu [zapytań dzienników](../log-query/log-query-overview.md) w Azure monitor i korzystać z funkcji, takich jak [alerty dzienników](../platform/alerts-log-query.md) i [skoroszyty](../platform/workbooks-overview.md). Następnie należy zebrać [dzienniki zasobów](../platform/resource-logs.md) z zasobów platformy Azure, które pomogą uzyskać szczegółowe dane w dzienniku aktywności.


> [!div class="nextstepaction"]
> [Zbieranie i analizowanie dzienników zasobów przy użyciu Azure Monitor](tutorial-resource-logs.md)