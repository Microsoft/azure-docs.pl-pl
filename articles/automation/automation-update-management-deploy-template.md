---
title: Korzystanie z szablonów usługi Azure Resource Manager w celu dołączania do zarządzania aktualizacjami | Dokumenty firmy Microsoft
description: Szablon usługi Azure Resource Manager służy do dołączania rozwiązania usługi Azure Automation Update Management.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 03/30/2020
ms.openlocfilehash: 81f9d242d93ffe513c0c3733ceb9d38ca9cadc1c
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617461"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>Dołączane rozwiązanie do zarządzania aktualizacjami przy użyciu szablonu usługi Azure Resource Manager

Za pomocą [szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) można włączyć rozwiązanie usługi Azure Automation Update Management w grupie zasobów. Ten artykuł zawiera przykładowy szablon, który automatyzuje następujące elementy:

* Tworzenie obszaru roboczego usługi Azure Monitor Log Analytics.
* Tworzenie konta usługi Azure Automation.
* Łączenie konta automatyzacji z obszarem roboczym usługi Log Analytics, jeśli nie jest już połączone.
* Dołączanie rozwiązania usługi Azure Automation Update Management.

Szablon nie automatyzuje dołączania jednej lub więcej maszyn wirtualnych platformy Azure lub innych niż Azure.

Jeśli masz już obszar roboczy usługi Log Analytics i konto automatyzacji wdrożone w obsługiwanym regionie w subskrypcji, nie są one połączone. Obszar roboczy nie ma jeszcze wdrożone rozwiązanie do zarządzania aktualizacjami. Za pomocą tego szablonu pomyślnie tworzy łącze i wdraża rozwiązanie zarządzania aktualizacjami. 

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="api-versions"></a>Wersje interfejsu API

W poniższej tabeli wymieniono wersje interfejsu API dla zasobów używanych w tym szablonie.

| Zasób | Typ zasobu | Wersja API |
|:---|:---|:---|
| Workspace | obszary robocze | 2017-03-15-podgląd |
| Konto usługi Automation | automation | 2015-10-31 | 
| Rozwiązanie | rozwiązania | 2015-11-01-podgląd |

## <a name="before-using-the-template"></a>Przed użyciem szablonu

Jeśli zdecydujesz się zainstalować i używać programu PowerShell lokalnie, ten artykuł wymaga modułu Az programu Azure PowerShell. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu programu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli korzystasz z programu PowerShell lokalnie, należy również uruchomić [Connect-AzAccount,](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) aby utworzyć połączenie z platformą Azure. W programie Azure PowerShell wdrożenie używa [new-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia lokalnie, ten artykuł wymaga, aby uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.1.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). W przypadku interfejsu wiersza polecenia platformy Azure to wdrożenie używa [tworzenia wdrożenia grupy az.](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create) 

Szablon JSON jest skonfigurowany tak, aby monitować o:

* Nazwa obszaru roboczego
* Region, w którym ma być utworzony obszar roboczy
* Nazwa konta automatyzacji
* Region, w którym ma być utworzone konto

Szablon JSON określa wartość domyślną dla innych parametrów, które mogą być używane dla konfiguracji standardowej w twoim środowisku. Szablon można przechowywać na koncie magazynu platformy Azure w celu uzyskania dostępu współużytkowego w organizacji. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Wdrażanie zasobów za pomocą szablonów Usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md).

Następujące parametry w szablonie są ustawiane z wartością domyślną dla obszaru roboczego usługi Log Analytics:

* sku - domyślnie nowa warstwa cenowa Per-GB wydana w modelu cenowym z kwietnia 2018 r.
* przechowywanie danych — domyślnie trzydzieści dni
* rezerwacja pojemności — domyślnie 100 GB

>[!WARNING]
>Jeśli tworzenie lub konfigurowanie obszaru roboczego usługi Log Analytics w ramach subskrypcji, która wybrała model cenowy z kwietnia 2018 r., jedyną prawidłową warstwą cenową usługi Log Analytics jest **PerGB2018**.
>

>[!NOTE]
>Przed użyciem tego szablonu przejrzyj [dodatkowe szczegóły,](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) aby w pełni zrozumieć opcje konfiguracji obszaru roboczego, takie jak tryb kontroli dostępu, warstwa cenowa, retencja i poziom rezerwacji pojemności. Jeśli jesteś nowy w dziennikach usługi Azure Monitor i nie wdrożyłeś już obszaru roboczego, zapoznaj się ze wskazówkami dotyczącymi [projektowania obszaru roboczego,](../azure-monitor/platform/design-logs-deployment.md) aby dowiedzieć się więcej o kontroli dostępu i zrozumieć strategie implementacji projektu zalecane dla twojej organizacji.

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
        }
    },
    "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
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
                    "apiVersion": "2015-11-01-preview",
                    "location": "[resourceGroup().location]",
                    "name": "[variables('Updates').name]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    },
                    "plan": {
                        "name": "[variables('Updates').name]",
                        "publisher": "Microsoft",
                        "promotionCode": "",
                        "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                    }
                }
            ]
        },
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
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
    ```

2. Edytuj szablon, aby spełnić wymagania. Należy rozważyć utworzenie [pliku parametrów Menedżera zasobów](../azure-resource-manager/templates/parameter-files.md) zamiast przekazywania parametrów jako wartości wbudowanych.

3. Zapisz ten plik w folderze lokalnym jako **deployUMSolutiontemplate.json**.

4. Wszystko jest teraz gotowe do wdrożenia tego szablonu. Można użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Po wyświetleniu monitu o podanie nazwy konta obszaru roboczego i usługi Automation podaj nazwę, która jest globalnie unikatowa we wszystkich subskrypcjach platformy Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Interfejs wiersza polecenia platformy Azure**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    Wdrożenie może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do następującego, który zawiera wynik:

    ![Przykładowy wynik po zakończeniu wdrażania](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz wdrożone rozwiązanie zarządzanie aktualizacjami, można włączyć maszyny wirtualne do zarządzania, przeglądu ocen aktualizacji i wdrażania aktualizacji, aby zapewnić im zgodność.

- Z [konta usługi Azure Automation](automation-onboard-solutions-from-automation-account.md) dla jednej lub więcej maszyn platformy Azure i ręcznie dla maszyn innych niż Azure.

- Dla pojedynczej maszyny wirtualnej platformy Azure ze strony maszyny wirtualnej w witrynie Azure portal. Ten scenariusz jest dostępny dla maszyn wirtualnych [z systemem Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) i [Windows.](../virtual-machines/windows/tutorial-config-management.md#enable-update-management)

- Dla [wielu maszyn wirtualnych platformy Azure,](manage-update-multi.md) wybierając je ze strony **maszyny wirtualne** w witrynie Azure portal. 