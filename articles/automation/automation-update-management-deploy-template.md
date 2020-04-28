---
title: Użyj szablonów Azure Resource Manager, aby dołączyć Update Management | Microsoft Docs
description: Możesz użyć szablonu Azure Resource Manager, aby dołączyć Azure Automation Update Management rozwiązanie.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/24/2020
ms.openlocfilehash: 45045cb1360658d394e5469d022ac03033d11aff
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "82165794"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>Dołączanie Update Management rozwiązania przy użyciu szablonu Azure Resource Manager

Za pomocą [szablonów Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) można włączyć rozwiązanie Update Management Azure Automation w grupie zasobów. Ten artykuł zawiera przykładowy szablon, który automatyzuje następujące czynności:

* Tworzenie obszaru roboczego Log Analytics Azure Monitor.
* Tworzenie konta Azure Automation.
* Łączenie konta usługi Automation z obszarem roboczym Log Analytics, jeśli nie jest jeszcze połączone.
* Dołączanie rozwiązania Azure Automation Update Management.

Szablon nie automatyzuje dołączania do co najmniej jednej maszyny wirtualnej platformy Azure lub innej niż Azure.

Jeśli masz już obszar roboczy Log Analytics i konto usługi Automation wdrożone w obsługiwanym regionie w ramach subskrypcji, nie są one połączone. W obszarze roboczym nie wdrożono jeszcze rozwiązania Update Management. Użycie tego szablonu spowoduje utworzenie linku i wdrożenie Update Management rozwiązanie. 

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](automation-update-azure-modules.md).

## <a name="api-versions"></a>Wersje interfejsu API

W poniższej tabeli wymieniono wersje interfejsu API dla zasobów używanych w tym szablonie.

| Zasób | Typ zasobu | Wersja interfejsu API |
|:---|:---|:---|
| Workspace | obszary robocze | 2017-03-15 — wersja zapoznawcza |
| Konto usługi Automation | automation | 2015-10-31 | 
| Rozwiązanie | rozwiązania | 2015-11-01 — wersja zapoznawcza |

## <a name="before-using-the-template"></a>Przed użyciem szablonu

Jeśli zdecydujesz się zainstalować program PowerShell i używać go lokalnie, ten artykuł będzie wymagał Azure PowerShell AZ module. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu programu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz również uruchomić polecenie [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) , aby utworzyć połączenie z platformą Azure. W przypadku Azure PowerShell wdrożenie używa polecenie [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.1.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). W przypadku korzystania z interfejsu wiersza polecenia platformy Azure w tym wdrożeniu zostanie użyte polecenie [AZ Group Deployment Create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

Szablon JSON jest skonfigurowany tak, aby monitował o:

* Nazwa obszaru roboczego
* Region, w którym ma zostać utworzony obszar roboczy
* Nazwa konta usługi Automation
* Region, w którym ma zostać utworzone konto.

Szablon JSON określa wartość domyślną dla innych parametrów, które mogą być używane do konfiguracji standardowej w danym środowisku. Szablon można przechowywać na koncie usługi Azure Storage w celu uzyskania dostępu współdzielonego w organizacji. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md).

Następujące parametry w szablonie mają ustawioną wartość domyślną Log Analytics obszarze roboczym:

* jednostka SKU — wartość domyślna to nowa warstwa cenowa według GB wydana w modelu cen z kwietnia 2018
* przechowywanie danych — wartość domyślna to 30 dni.
* Rezerwacja pojemności — wartość domyślna to 100 GB

>[!WARNING]
>W przypadku tworzenia lub konfigurowania obszaru roboczego Log Analytics w ramach subskrypcji, która została wybrana w nowym modelu cen 2018 kwietnia, jedyną prawidłową warstwą cenową Log Analytics jest **PerGB2018**.
>

Szablon JSON określa wartość domyślną dla innych parametrów, które mogą być używane jako Standardowa konfiguracja w danym środowisku. Szablon można przechowywać na koncie usługi Azure Storage w celu uzyskania dostępu współdzielonego w organizacji. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md).

Należy zapoznać się z poniższymi szczegółami konfiguracji, jeśli jesteś nowym użytkownikiem Azure Automation i Azure Monitor, aby uniknąć błędów podczas próby utworzenia, skonfigurowania i użycia obszaru roboczego Log Analytics połączonego z nowym kontem usługi Automation.

* Zapoznaj się z [dodatkowymi szczegółami](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) , aby w pełni zrozumieć opcje konfiguracji obszaru roboczego, takie jak tryb kontroli dostępu, warstwa cenowa, przechowywanie i poziom rezerwacji pojemności.

* Ponieważ tylko niektóre regiony są obsługiwane do łączenia obszaru roboczego Log Analytics i konta usługi Automation w ramach subskrypcji, przejrzyj [mapowania obszaru roboczego](how-to/region-mappings.md) , aby określić Obsługiwane regiony w tekście lub w pliku parametrów.

* Jeśli dopiero zaczynasz korzystać z dzienników Azure Monitor i nie wdrożono już obszaru roboczego, zapoznaj się ze wskazówkami dotyczącymi [projektowania obszaru roboczego](../azure-monitor/platform/design-logs-deployment.md) , aby dowiedzieć się więcej na temat kontroli dostępu, a następnie zapoznaj się z strategiami implementacji projektu zalecanymi dla Twojej organizacji.

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

2. Edytuj szablon w celu spełnienia wymagań. Rozważ utworzenie [pliku parametrów Menedżer zasobów](../azure-resource-manager/templates/parameter-files.md) zamiast przekazywania parametrów jako wartości wbudowanych.

3. Zapisz ten plik w folderze lokalnym jako **deployUMSolutiontemplate. JSON**.

4. Wszystko jest teraz gotowe do wdrożenia tego szablonu. Możesz użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Po wyświetleniu monitu o nazwę obszaru roboczego i konta usługi Automation Podaj nazwę globalnie unikatową we wszystkich subskrypcjach platformy Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Interfejs wiersza polecenia platformy Azure**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    Wdrożenie może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do następującego:

    ![Przykładowy wynik po zakończeniu wdrażania](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz wdrożone rozwiązanie Update Management, możesz włączyć maszyny wirtualne do zarządzania, przejrzeć oceny aktualizacji i wdrożyć aktualizacje, aby zapewnić ich zgodność.

- Z [konta usługi Azure Automation](automation-onboard-solutions-from-automation-account.md) dla jednej lub wielu maszyn platformy Azure i ręcznie dla maszyn nienależących do platformy Azure.

- Dla jednej maszyny wirtualnej platformy Azure na stronie maszyny wirtualnej w Azure Portal. Ten scenariusz jest dostępny dla maszyn wirtualnych z systemami [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) i [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) .

- Dla [wielu maszyn wirtualnych platformy Azure](manage-update-multi.md) , wybierając je na stronie **maszyny wirtualne** w Azure Portal. 