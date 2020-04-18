---
title: Tworzenie konta usługi Automation za pomocą szablonów usługi Azure Resource Manager | Dokumenty firmy Microsoft
description: Szablon usługi Azure Resource Manager służy do tworzenia konta usługi Azure Automation.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2020
ms.openlocfilehash: efe51fbada8ac70b24c16a5c7c1e0e91879e5e9f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618687"
---
# <a name="create-automation-account-using-azure-resource-manager-template"></a>Tworzenie konta automatyzacji przy użyciu szablonu usługi Azure Resource Manager

Za pomocą [szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) można utworzyć konto usługi Azure Automation w grupie zasobów. Ten artykuł zawiera przykładowy szablon, który automatyzuje następujące elementy:

* Tworzenie obszaru roboczego usługi Azure Monitor Log Analytics.
* Tworzenie konta usługi Azure Automation.
* Łączy konto automatyzacji z obszarem roboczym usługi Log Analytics.

Szablon nie automatyzuje dołączania jednej lub więcej maszyn wirtualnych platformy Azure lub innych niż Azure lub rozwiązań. 

>[!NOTE]
>Tworzenie konta Uruchamianie automatyzacji jako nie jest obsługiwane podczas korzystania z szablonu usługi Azure Resource Manager. Aby utworzyć konto Uruchom jako ręcznie z portalu lub za pomocą programu PowerShell, zobacz [Zarządzanie kontem uruchom jako](manage-runas-account.md).

## <a name="api-versions"></a>Wersje interfejsu API

W poniższej tabeli wymieniono wersję interfejsu API dla zasobów używanych w tym przykładzie.

| Zasób | Typ zasobu | Wersja API |
|:---|:---|:---|
| Workspace | obszary robocze | 2017-03-15-podgląd |
| Konto usługi Automation | automation | 2015-10-31 | 

## <a name="before-using-the-template"></a>Przed użyciem szablonu

Jeśli zdecydujesz się zainstalować i używać programu PowerShell lokalnie, ten artykuł wymaga modułu Az programu Azure PowerShell. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu programu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure. W programie Azure PowerShell wdrożenie używa [new-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia lokalnie, ten artykuł wymaga, aby uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.1.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). W przypadku interfejsu wiersza polecenia platformy Azure to wdrożenie używa [tworzenia wdrożenia grupy az.](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create) 

Szablon JSON jest skonfigurowany tak, aby monitować o:

* Nazwa obszaru roboczego
* Region, w który ma powstać obszar roboczy w
* Nazwa konta automatyzacji
* Region, w który ma utworzyć konto w

Szablon JSON określa wartość domyślną dla innych parametrów, które prawdopodobnie będą używane jako standardowa konfiguracja w twoim środowisku. Szablon można przechowywać na koncie magazynu platformy Azure w celu uzyskania dostępu współużytkowego w organizacji. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Wdrażanie zasobów za pomocą szablonów Usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md).

Następujące parametry w szablonie są ustawiane z wartością domyślną dla obszaru roboczego usługi Log Analytics:

* sku - domyślnie nowa warstwa cenowa Per-GB wydana w modelu cenowym z kwietnia 2018 r.
* przechowywanie danych — domyślnie trzydzieści dni
* rezerwacja pojemności — domyślnie 100 GB

>[!WARNING]
>Jeśli tworzenie lub konfigurowanie obszaru roboczego usługi Log Analytics w ramach subskrypcji, która wybrała nowy model cenowy z kwietnia 2018 r., jedyną prawidłową warstwą cenową usługi Log Analytics jest **PerGB2018**.
>

>[!NOTE]
>Przed użyciem tego szablonu przejrzyj [dodatkowe szczegóły,](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) aby w pełni zrozumieć opcje konfiguracji obszaru roboczego, takie jak tryb kontroli dostępu, warstwa cenowa, retencja i poziom rezerwacji pojemności. Jeśli jesteś nowy w dziennikach usługi Azure Monitor i nie wdrożyłeś już obszaru roboczego, zapoznaj się ze wskazówkami dotyczącymi [projektowania obszaru roboczego,](../azure-monitor/platform/design-logs-deployment.md) aby dowiedzieć się więcej o kontroli dostępu i zrozumieniu strategii wdrażania projektowania, które zalecana jest dla twojej organizacji.

## <a name="deploy-template"></a>Wdrażanie szablonu

1. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Workspace name"
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
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
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
                "uksouth",
                "ukwest",
                "westcentralus",
                "westeurope",
                "westus",
                "westus2"
            ],
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "automationAccountName": {
            "type": "string",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "automationAccountLocation": {
            "type": "string",
            "metadata": {
                "description": "Specify the location in which to create the Automation account."
            }
        },
        "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook which gets all the ARM resources using the Run As Account (Service Principal)."
            },
            "sampleGraphicalRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.graphrunbook"
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook which gets all the ARM resources using the Run As Account (Service Principal)."
            },
            "samplePowerShellRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.ps1"
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook which gets all the ARM resources using the Run As Account (Service Principal)."
            },
            "samplePython2RunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorialPython2.py"
            }
    },
    "resources": [
        {
        "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]",
                    "name": "CapacityReservation",
                    "capacityReservationLevel": 100
                },
                "retentionInDays": "[parameters('dataRetention')]",
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            },
        "resources": [
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [
                 "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
            ],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
            "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('sampleGraphicalRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePowerShellRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePython2RunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
        },
        {
            "apiVersion": "2015-11-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
                "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            ],
            "properties": {
                "resourceId": "[resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            }
        }
       ]
      }
     ]
    }
    ```

2. Edytuj szablon, aby spełnić wymagania. Należy rozważyć utworzenie [pliku parametrów Menedżera zasobów](../azure-resource-manager/templates/parameter-files.md) zamiast przekazywania parametrów jako wartości wbudowanych.

3. Zapisz ten plik jako deployAzAutomationAccttemplate.json w folderze lokalnym.

4. Wszystko jest teraz gotowe do wdrożenia tego szablonu. Można użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Po wyświetleniu monitu o podanie nazwy konta obszaru roboczego i usługi Automation podaj nazwę, która jest globalnie unikatowa we wszystkich subskrypcjach platformy Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **Interfejs wiersza polecenia platformy Azure**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    Wdrożenie może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do następującego, który zawiera wynik:

    ![Przykładowy wynik po zakończeniu wdrażania](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz konto automatyzacji, możesz tworzyć elementy runbook i automatyzować procesy ręczne.
