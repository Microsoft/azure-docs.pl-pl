---
title: Włącz Azure Monitor dla maszyn wirtualnych kondycję gościa (wersja zapoznawcza)
description: Zawiera opis sposobu włączania Azure Monitor dla maszyn wirtualnych kondycji gościa w ramach subskrypcji oraz dołączania maszyn wirtualnych.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/16/2020
ms.openlocfilehash: 6033ca5b0eaf6845d14407832c776dd8e006226b
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686974"
---
# <a name="enable-azure-monitor-for-vms-guest-health-preview"></a>Włącz Azure Monitor dla maszyn wirtualnych kondycję gościa (wersja zapoznawcza)
Azure Monitor dla maszyn wirtualnych kondycja gościa umożliwia wyświetlanie kondycji maszyny wirtualnej zdefiniowanej przez zestaw pomiarów wydajności, które są próbkowane w regularnych odstępach czasu. W tym artykule opisano, jak włączyć tę funkcję w ramach subskrypcji i jak włączyć monitorowanie Gości dla każdej maszyny wirtualnej.

## <a name="current-limitations"></a>Bieżące ograniczenia
Azure Monitor dla maszyn wirtualnych kondycja gościa ma następujące ograniczenia w publicznej wersji zapoznawczej:

- Obecnie są obsługiwane tylko maszyny wirtualne platformy Azure. Usługa Azure ARC dla serwerów nie jest obecnie obsługiwana.
- Na maszynie wirtualnej musi działać jeden z następujących systemów operacyjnych: 
  - Ubuntu 16,04 LTS, Ubuntu 18,04 LTS
  - Windows Server 2012 lub nowszy
- Maszyna wirtualna musi znajdować się w jednym z następujących regionów:
  - Australia Południowo-Wschodnia
  - Central US
  - East US
  - Wschodnie stany USA 2
  - Wschodnie stany USA 2 — EUAP
  - Europa Północna
  - Azja Południowo-Wschodnia
  - Południowe Zjednoczone Królestwo
  - West Europe
  - Zachodnie stany USA
  - Zachodnie stany USA 2
- Obszar roboczy Log Analytics musi znajdować się w jednym z następujących regionów:
  - East US
  - Wschodnie stany USA 2 — EUAP
  - Region Europa Zachodnia

## <a name="prerequisites"></a>Wymagania wstępne

- Maszyna wirtualna musi zostać dołączona do Azure Monitor dla maszyn wirtualnych.
- Wykonanie kroków dołączania musi mieć minimalny dostęp do subskrypcji, w której znajduje się maszyna wirtualna i Reguła zbierania danych.
- Wymagane dostawcy zasobów platformy Azure muszą być zarejestrowane zgodnie z opisem w poniższej sekcji.


## <a name="register-required-azure-resource-providers"></a>Zarejestruj wymaganych dostawców zasobów platformy Azure
Następujący dostawcy zasobów platformy Azure muszą być zarejestrowani dla subskrypcji, aby umożliwić Azure Monitor dla maszyn wirtualnych kondycję gościa. 

- Microsoft. Monitor obciążenia został
- Microsoft. Insights

Możesz użyć dowolnej z dostępnych metod, aby zarejestrować dostawcę zasobów zgodnie z opisem w temacie [dostawcy zasobów platformy Azure i typy](../../azure-resource-manager/management/resource-providers-and-types.md). Możesz również użyć następującego przykładowego polecenia przy użyciu armclient, Poster lub innej metody, aby wykonać uwierzytelnione wywołanie do Azure Resource Manager:

```
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.WorkloadMonitor/register?api-version=2019-10-01
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.Insights/register?api-version=2019-10-01
```


## <a name="enable-a-virtual-machine-using-the-azure-portal"></a>Włącz maszynę wirtualną przy użyciu Azure Portal
Po włączeniu kondycji gościa dla maszyny wirtualnej w Azure Portal jest wykonywana cała wymagana konfiguracja. Obejmuje to utworzenie reguły Wymagaj zbierania danych, zainstalowanie rozszerzenia kondycji gościa na maszynie wirtualnej i utworzenie skojarzenia z regułą zbierania danych.

**W widoku wprowadzenie** w Azure monitor dla maszyn wirtualnych kliknij link obok komunikatu uaktualnienia dla maszyny wirtualnej, a następnie kliknij przycisk **Uaktualnij** . Możesz również wybrać wiele maszyn wirtualnych, aby uaktualnić je razem.

![Włącz funkcję kondycji na maszynie wirtualnej](media/vminsights-health-enable/enable-agent.png)


## <a name="enable-a-virtual-machine-using-resource-manager-template"></a>Włączanie maszyny wirtualnej przy użyciu szablonu Menedżer zasobów
Aby włączyć maszyny wirtualne przy użyciu Azure Resource Manager, należy wykonać trzy czynności.

- Utwórz regułę zbierania danych.
- Zainstaluj rozszerzenie kondycji gościa na każdej maszynie wirtualnej
- Utwórz skojarzenie między maszyną wirtualną a regułą zbierania danych.

### <a name="create-data-collection-rule-dcr"></a>Utwórz regułę zbierania danych (DCR)

> [!NOTE]
> W przypadku włączenia maszyny wirtualnej przy użyciu Azure Portal zostanie utworzona opisana tutaj Reguła zbierania danych. W takim przypadku nie trzeba wykonywać tego kroku.

Konfiguracja monitorów w Azure Monitor dla maszyn wirtualnych kondycji gościa jest przechowywana w [zasadach zbierania danych (DCR)](../platform/data-collection-rule-overview.md). Zainstaluj regułę zbierania danych zdefiniowaną w szablonie Menedżer zasobów poniżej, aby włączyć wszystkie monitory dla maszyn wirtualnych z rozszerzeniem kondycji gościa. Każda maszyna wirtualna z rozszerzeniem kondycji gościa będzie potrzebować skojarzenia z tą regułą.

> [!NOTE]
> Można utworzyć dodatkowe reguły zbierania danych w celu zmodyfikowania konfiguracji domyślnej monitorów zgodnie z opisem w temacie [konfigurowanie monitorowania w Azure monitor dla maszyn wirtualnych kondycja gościa (wersja zapoznawcza)](vminsights-health-configure.md).

Szablon wymaga wartości dla następujących parametrów:

- **defaultHealthDataCollectionRuleName**: Zachowaj domyślną nazwę zdefiniowaną w szablonie.
- **destinationWorkspaceResourceId**: Identyfikator zasobu obszaru roboczego log Analytics używany na potrzeby zbierania danych maszyny wirtualnej.
- **dataCollectionRuleLocation**: region reguły zbierania danych. Musi on być zgodny z regionem obszaru roboczego Log Analytics.


Wdróż szablon przy użyciu dowolnej [metody wdrażania dla szablonów Menedżer zasobów](../../azure-resource-manager/templates/deploy-powershell.md). Poniższe polecenia wdrażają plik szablonu i parametrów przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDataCollectionRule -ResourceGroupName my-resource-group -TemplateFile Health.DataCollectionRule.template.json -TemplateParameterFile Health.DataCollectionRule.template.parameters.json
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

```cli
az deployment group create --name GuestHealthDataCollectionRule --resource-group my-resource-group --template-file Health.DataCollectionRule.template.json --parameters Health.DataCollectionRule.template.parameters.json
```

---



```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "defaultHealthDataCollectionRuleName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule to create."
      },
      "defaultValue": "Microsoft-VMInsights-Health"
    },
    "destinationWorkspaceResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the Azure resource ID of the Log Analytics workspace to use to store virtual machine health data."
      }
    },
    "dataCollectionRuleLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code in which the data colleciton rule should be deployed. Examples: eastus, westeurope, etc"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/dataCollectionRules",
      "name": "[parameters('defaultHealthDataCollectionRuleName')]",
      "location": "[parameters('dataCollectionRuleLocation')]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Data collection rule for VM Insights health.",
        "dataSources": {
          "extensions": [
            {
              "name": "Microsoft-VMInsights-Health",
              "streams": [
                "Microsoft-HealthStateChange"
              ],
              "extensionName": "HealthExtension",
              "extensionSettings": {
                "schemaVersion": "1.0",
                "contentVersion": "",
                "healthRuleOverrides": [
                  {
                    "scopes": [ "*" ],
                    "monitors": ["root"],
                    "alertConfiguration": {
                      "isEnabled": true
                    }
                  }
                ]
              }
            }
          ]
        },
        "destinations": {
          "logAnalytics": [
            {
              "workspaceResourceId": "[parameters('destinationWorkspaceResourceId')]",
              "name": "Microsoft-HealthStateChange-Dest"
            }
          ]
        },
        "dataFlows": [
          {
            "streams": [
              "Microsoft-HealthStateChange"
            ],
            "destinations": [
              "Microsoft-HealthStateChange-Dest"
            ]
          }
        ]
      }
    }
  ]
}
```

### <a name="sample-parameter-file"></a>Przykładowy plik parametrów

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "healthDataCollectionRuleResourceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
      },
      "dataCollectionRuleLocation": {
        "value": "eastus"
      }
  }
}
```



## <a name="install-guest-health-extension-and-associate-with-data-collection-rule"></a>Instalowanie rozszerzenia kondycji gościa i kojarzenie z regułą zbierania danych
Aby włączyć maszynę wirtualną dla kondycji gościa, użyj następującego szablonu Menedżer zasobów. Spowoduje to zainstalowanie rozszerzenia kondycji gościa i utworzenie skojarzenia z regułą zbierania danych. Ten szablon można wdrożyć przy użyciu dowolnej [metody wdrażania dla szablonów Menedżer zasobów](../../azure-resource-manager/templates/deploy-powershell.md).


Na przykład użyj następujących poleceń, aby wdrożyć plik szablonu i parametrów do grupy zasobów przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.


# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

```cli
az deployment group create --name GuestHealthDeployment --resource-group my-resource-group --template-file Health.VirtualMachine.template.json --parameters Health.VirtualMachine.template.parameters.json
```

---

### <a name="template-file"></a>Plik szablonu

``` json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "virtualMachineName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the virtual machine."
      }
    },
    "virtualMachineLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code of the virtual machine region (location). Examples: eastus, westeurope, etc"
      }
    },
    "virtualMachineOsType": {
      "type": "string",
      "metadata": {
        "description": "Specifies operating system type of the target virtual machine."
      },
      "allowedValues": ["windows", "linux"]
    },
    "dataCollectionRuleAssociationName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule association to create."
      },
      "defaultValue": "VM-Health-Dcr-Association"
    },
    "healthDataCollectionRuleResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies resource id of Azure Monitor Data Collection Rule for virtual machine health data."
      }
    }
  },
  "variables": {
    "healthExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthWindowsAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthLinuxAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      }
    },
    "azureMonitorAgentExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorWindowsAgent", 
        "typeHandlerVersion": "1.0", 
        "autoUpgradeMinorVersion": false 
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorLinuxAgent", 
        "typeHandlerVersion": "1.5", 
        "autoUpgradeMinorVersion": false 
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('virtualMachineName')]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2017-03-30",
      "identity": {
        "type": "SystemAssigned"
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
      "name": "[concat(parameters('virtualMachineName'), '/Microsoft.Insights/', parameters('dataCollectionRuleAssociationName'))]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Association of data collection rule for VM Insights Health.",
        "dataCollectionRuleId": "[parameters('healthDataCollectionRuleResourceId')]"
      }
    },
    { 
      "type": "Microsoft.Compute/virtualMachines/extensions", 
      "apiVersion": "2019-12-01", 
      "name": "[concat(parameters('virtualMachineName'), '/', variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ], 
      "properties": "[variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')]]"
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'), '/', variables('healthExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2018-06-01",
      "dependsOn": [ 
        "[resourceId('Microsoft.Compute/virtualMachines/extensions', parameters('virtualMachineName'), variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ],    
      "properties": "[variables('healthExtensionProperties')[parameters('virtualMachineOsType')]]"
    }               
  ]
}
```

### <a name="sample-parameter-file"></a>Przykładowy plik parametrów

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualMachineName": {
        "value": "myvm"
      },
      "virtualMachineLocation": {
        "value": "eastus"
      },
      "virtualMachineOsType": {
        "value": "linux"
      },
      "healthDataCollectionRuleResourceId": {
        "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/dataCollectionRules/Microsoft-VMInsights-Health"
      },
      "healthExtensionVersion": {
        "value": "private-preview"
      }
  }
}
```

## <a name="next-steps"></a>Następne kroki

- [Dostosowywanie monitorów włączonych przez Azure Monitor dla maszyn wirtualnych](vminsights-health-configure.md)