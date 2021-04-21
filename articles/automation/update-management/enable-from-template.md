---
title: Włączanie rozwiązania Update Management przy użyciu szablonu usługi Azure Resource Manager
description: W tym artykule opisano sposób użycia szablonu Azure Resource Manager w celu włączenia Update Management.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 09/18/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 687c3d49f98fe6832d23dc1529a9761d862e0666
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830884"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Włączanie rozwiązania Update Management przy użyciu szablonu usługi Azure Resource Manager

Możesz użyć szablonu [Azure Resource Manager,](../../azure-resource-manager/templates/template-syntax.md) aby włączyć funkcję Azure Automation Update Management w grupie zasobów. Ten artykuł zawiera przykładowy szablon, który automatyzuje następujące czynności:

* Automatyzuje tworzenie nowego obszaru roboczego Azure Monitor Log Analytics.
* Automatyzuje tworzenie konta Azure Automation konta.
* Łączy konto usługi Automation z obszarem roboczym usługi Log Analytics.
* Dodaje do konta przykładowe podręczniki automatyzacji.
* Włącza funkcję Update Management.

Szablon nie automatyzuje włączania Update Management co najmniej jednej maszyny wirtualnej platformy Azure lub spoza platformy Azure.

Jeśli masz już obszar roboczy usługi Log Analytics i konto usługi Automation wdrożone w obsługiwanym regionie w ramach subskrypcji, nie są one połączone. Użycie tego szablonu pomyślnie tworzy link i wdraża Update Management.

>[!NOTE]
>Tworzenie konta Uruchom jako usługi Automation nie jest obsługiwane w przypadku korzystania z szablonu usługi ARM. Aby ręcznie utworzyć konto Uruchom jako z portalu lub przy użyciu programu PowerShell, zobacz [Tworzenie konta Uruchom jako.](../create-run-as-account.md)

Po ukończeniu tych kroków [](../automation-manage-send-joblogs-log-analytics.md) należy skonfigurować ustawienia diagnostyczne dla konta usługi Automation w celu wysyłania stanu zadania i strumieni zadań runbook do połączonego obszaru roboczego usługi Log Analytics.

## <a name="api-versions"></a>Wersje interfejsu API

W poniższej tabeli wymieniono wersję interfejsu API dla zasobów używanych w tym przykładzie.

| Zasób | Typ zasobu | Wersja interfejsu API |
|:---|:---|:---|
| [Workspace](/azure/templates/microsoft.operationalinsights/workspaces) | obszary robocze | 2020-03-01-preview |
| [Konto usługi Automation](/azure/templates/microsoft.automation/automationaccounts) | automatyzacja | 2020-01-13-preview |
| [Połączone usługi obszaru roboczego](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | obszary robocze | 2020-03-01-preview |
| [Rozwiązania](/azure/templates/microsoft.operationsmanagement/solutions) | rozwiązania | 2015-11-01-preview |

## <a name="before-using-the-template"></a>Przed użyciem szablonu

Szablon JSON jest skonfigurowany w celu monitowania o:

* Nazwa obszaru roboczego.
* Region, w których ma być utworzyć obszar roboczy.
* Nazwa konta usługi Automation.
* Region, w których ma być utworzenia konto usługi Automation.

Następujące parametry w szablonie są ustawiane z wartością domyślną dla obszaru roboczego usługi Log Analytics:

* *Wartość domyślna* jednostki SKU to warstwa cenowa za GB wydana w modelu cenowym z kwietnia 2018 r.
* *Wartość domyślna dla ustawienia dataRetention* to 30 dni.

>[!WARNING]
>Jeśli chcesz utworzyć lub skonfigurować obszar roboczy usługi Log Analytics w subskrypcji, która wybrała model cenowy z kwietnia 2018 r., jedyną prawidłową warstwą cenową usługi Log Analytics jest *PerGB2018.*
>

Szablon JSON określa wartość domyślną dla innych parametrów, które prawdopodobnie będą używane jako standardowa konfiguracja w środowisku. Szablon można przechowywać na koncie usługi Azure Storage, aby uzyskać dostęp współdzielony w organizacji. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz Deploy resources with ARM templates and the Azure CLI (Wdrażanie zasobów za [pomocą szablonów usługi ARM i interfejsu wiersza polecenia platformy Azure).](../../azure-resource-manager/templates/deploy-cli.md)

Jeśli dopiero poznajemy Azure Automation i Azure Monitor, ważne jest, aby zrozumieć następujące szczegóły konfiguracji. Mogą one pomóc uniknąć błędów podczas próby utworzenia, skonfigurowania i użycia obszaru roboczego usługi Log Analytics połączonego z nowym kontem usługi Automation.

* Przejrzyj [dodatkowe szczegóły,](../../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace) aby w pełni zrozumieć opcje konfiguracji obszaru roboczego, takie jak tryb kontroli dostępu, warstwa cenowa, przechowywanie i poziom rezerwacji pojemności.

* Przejrzyj [mapowania obszarów roboczych,](../how-to/region-mappings.md) aby określić obsługiwane regiony w tekście lub w pliku parametrów. Tylko niektóre regiony są obsługiwane w przypadku łączenia obszaru roboczego usługi Log Analytics i konta usługi Automation w ramach subskrypcji.

* Jeśli nie wiesz już, jak Azure Monitor obszaru roboczego, zapoznaj się ze wskazówkami projektowania [obszaru roboczego.](../../azure-monitor/logs/design-logs-deployment.md) Pomoże ci to dowiedzieć się więcej na temat kontroli dostępu i poznać strategie implementacji projektu zalecane dla Twojej organizacji.

## <a name="deploy-template"></a>Wdrażanie szablonu

1. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
                    "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium), which are not available to all customers."
                }
            },
            "dataRetention": {
                "type": "int",
                "defaultValue": 30,
                "minValue": 7,
                "maxValue": 730,
                "metadata": {
                    "description": "Number of days to retain data."
                }
            },
            "location": {
                "type": "string",
                "defaultValue": "[resourceGroup().location]",
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
                    "description": "Specifies the location in which to create the Automation account."
                }
            },
            "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "_artifactsLocation": {
                "type": "string",
                "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-automation/",
                "metadata": {
                    "description": "URI to artifacts location"
                }
            },
            "_artifactsLocationSasToken": {
                "type": "securestring",
                "defaultValue": "",
                "metadata": {
                    "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated"
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
                "apiVersion": "2020-03-01-preview",
                "name": "[parameters('workspaceName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "sku": {
                        "name": "[parameters('sku')]"
                    },
                    "retentionInDays": "[parameters('dataRetention')]",
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "location": "[parameters('location')]",
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
            },
            {
                "type": "Microsoft.Automation/automationAccounts",
                "apiVersion": "2020-01-13-preview",
                "name": "[parameters('automationAccountName')]",
                "location": "[parameters('automationAccountLocation')]",
                "dependsOn": [
                    "[parameters('workspaceName')]"
                ],
                "properties": {
                    "sku": {
                        "name": "Basic"
                    }
                },
                "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.graphrunbook', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.ps1', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorialPython2.py', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
            },
            {
                "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
                "apiVersion": "2020-03-01-preview",
                "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[parameters('workspaceName')]",
                    "[parameters('automationAccountName')]"
                ],
                "properties": {
                    "resourceId": "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccountName'))]"
                }
            }
        ]
    }
    ```

2. Edytuj szablon, aby spełnić wymagania. Rozważ utworzenie pliku [Resource Manager parametrów zamiast](../../azure-resource-manager/templates/parameter-files.md) przekazywania parametrów jako wartości w tekście.

3. Zapisz ten plik w folderze lokalnym jakodeployUMSolutiontemplate.js **w folderze**.

4. Wszystko jest teraz gotowe do wdrożenia tego szablonu. Możesz użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Po wyświetleniu monitu o nazwę obszaru roboczego i konta usługi Automation podaj nazwę, która jest globalnie unikatowa we wszystkich subskrypcjach platformy Azure.

    **Program PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Interfejs wiersza polecenia platformy Azure**

    ```azurecli
    az deployment group create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    Wdrożenie może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do następującego, który zawiera wynik:

    ![Przykładowy wynik po zakończeniu wdrażania](media/enable-from-template/template-output.png)

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W Azure Portal otwórz utworzone konto usługi Automation.

3. W okienku po lewej stronie wybierz pozycję **Runbook**. Na stronie **Runbook wymieniono** trzy samouczki runbook utworzone przy użyciu konta usługi Automation.

    ![Samouczek dotyczący podręczników Runbook utworzonych przy użyciu konta usługi Automation](../media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. W okienku po lewej stronie wybierz pozycję **Połączony obszar roboczy.** Na stronie **Połączony obszar roboczy** jest on przedstawia określony wcześniej obszar roboczy usługi Log Analytics połączony z kontem usługi Automation.

    ![Konto usługi Automation połączone z obszarem roboczym usługi Log Analytics](../media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

5. W okienku po lewej stronie wybierz pozycję **Update Management.** Na **stronie Zarządzanie aktualizacjami** jest przedstawiana strona oceny bez żadnych informacji w wyniku tego, że zostały włączone, a maszyny nie są skonfigurowane do zarządzania.

    ![Update Management oceny funkcji](./media/enable-from-template/update-management-assessment-view.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie będą już potrzebne, usuń rozwiązanie **Updates** w obszarze roboczym usługi Log Analytics, odłącz konto usługi Automation od obszaru roboczego, a następnie usuń konto i obszar roboczy usługi Automation.

## <a name="next-steps"></a>Następne kroki

* Aby użyć Update Management dla maszyn wirtualnych, zobacz Zarządzanie aktualizacjami i [poprawkami dla maszyn wirtualnych.](manage-updates-for-vm.md)

* Jeśli nie chcesz już używać Update Management i chcesz go usunąć, zobacz instrukcje w te Update Management [funkcji](remove-feature.md).

* Aby usunąć maszyny wirtualne z Update Management, zobacz [Usuwanie maszyn wirtualnych z Update Management](remove-vms.md).
