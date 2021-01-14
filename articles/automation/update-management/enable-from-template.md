---
title: Włączanie Update Management przy użyciu szablonu Azure Resource Manager | Microsoft Docs
description: W tym artykule opisano, jak włączyć Update Management przy użyciu szablonu Azure Resource Manager.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 09/18/2020
ms.openlocfilehash: e2ebdd3d0f4a4461521ee5f412d5b4c4f872b8a0
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183238"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Włączanie rozwiązania Update Management przy użyciu szablonu usługi Azure Resource Manager

Aby włączyć funkcję Update Management Azure Automation w grupie zasobów, można użyć [szablonu Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) . Ten artykuł zawiera przykładowy szablon, który automatyzuje następujące czynności:

* Automatyzuje tworzenie obszaru roboczego Log Analytics Azure Monitor.
* Automatyzuje tworzenie konta Azure Automation.
* Łączy konto usługi Automation z obszarem roboczym Log Analytics.
* Dodaje Przykładowe elementy Runbook automatyzacji do konta.
* Włącza funkcję Update Management.

Szablon nie automatyzuje włączania Update Management na co najmniej jednej platformie Azure lub na maszynach wirtualnych platformy Azure.

Jeśli masz już obszar roboczy Log Analytics i konto usługi Automation wdrożone w obsługiwanym regionie w ramach subskrypcji, nie są one połączone. Użycie tego szablonu spowoduje utworzenie linku i wdrożenie Update Management.

>[!NOTE]
>Tworzenie konta Uruchom jako usługi Automation nie jest obsługiwane w przypadku korzystania z szablonu ARM. Aby ręcznie utworzyć konto Uruchom jako w portalu lub za pomocą programu PowerShell, zobacz [Zarządzanie kontami Uruchom jako](../manage-runas-account.md).

Po wykonaniu tych kroków należy [skonfigurować ustawienia diagnostyczne](../automation-manage-send-joblogs-log-analytics.md) dla konta usługi Automation, aby wysyłać strumienie stanu zadań elementu Runbook i zadań do połączonego obszaru roboczego log Analytics.

## <a name="api-versions"></a>Wersje interfejsu API

W poniższej tabeli wymieniono wersje interfejsu API dla zasobów używanych w tym przykładzie.

| Zasób | Typ zasobu | Wersja interfejsu API |
|:---|:---|:---|
| [Workspace](/azure/templates/microsoft.operationalinsights/workspaces) | obszary robocze | 2020-03-01 — wersja zapoznawcza |
| [Konto usługi Automation](/azure/templates/microsoft.automation/automationaccounts) | automatyzacja | 2020-01-13 — wersja zapoznawcza |
| [Połączone usługi obszaru roboczego](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | obszary robocze | 2020-03-01 — wersja zapoznawcza |
| [Rozwiązania](/azure/templates/microsoft.operationsmanagement/solutions) | rozwiązania | 2015-11-01 — wersja zapoznawcza |

## <a name="before-using-the-template"></a>Przed użyciem szablonu

Szablon JSON jest skonfigurowany tak, aby monitował o:

* Nazwa obszaru roboczego.
* Region, w którym ma zostać utworzony obszar roboczy.
* Nazwa konta usługi Automation.
* Region, w którym ma zostać utworzone konto usługi Automation.

Następujące parametry w szablonie mają ustawioną wartość domyślną Log Analytics obszarze roboczym:

* Domyślna *jednostka SKU* to warstwa cenowa za GB wydaną w modelu cen z kwietnia 2018.
* wartość domyślna *przechowywania* danych to 30 dni.

>[!WARNING]
>Jeśli chcesz utworzyć lub skonfigurować obszar roboczy Log Analytics w ramach subskrypcji, która została wybrana w modelu cen z kwietnia 2018, jedyną prawidłową Log Analytics warstwą cenową jest *PerGB2018*.
>

Szablon JSON określa wartość domyślną dla innych parametrów, które mogą być używane jako Standardowa konfiguracja w danym środowisku. Szablon można przechowywać na koncie usługi Azure Storage w celu uzyskania dostępu współdzielonego w organizacji. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [wdrażanie zasobów przy użyciu szablonów ARM i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md).

Jeśli dopiero zaczynasz Azure Automation i Azure Monitor, ważne jest zapoznanie się z poniższymi szczegółami konfiguracji. Mogą one pomóc uniknąć błędów podczas próby utworzenia, skonfigurowania i użycia obszaru roboczego Log Analytics połączonego z nowym kontem usługi Automation.

* Zapoznaj się z [dodatkowymi szczegółami](../../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace) , aby w pełni zrozumieć opcje konfiguracji obszaru roboczego, takie jak tryb kontroli dostępu, warstwa cenowa, przechowywanie i poziom rezerwacji pojemności.

* Przejrzyj [mapowania obszaru roboczego](../how-to/region-mappings.md) , aby określić Obsługiwane regiony w tekście lub w pliku parametrów. Tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation w ramach subskrypcji.

* Jeśli dopiero zaczynasz korzystać z dzienników Azure Monitor i nie wdrożono już obszaru roboczego, zapoznaj się z tematem [wskazówki dotyczące projektowania obszaru roboczego](../../azure-monitor/platform/design-logs-deployment.md). Pomożemy Ci poznać kontrolę dostępu i zrozumieć strategie implementacji projektu, które zalecamy dla Twojej organizacji.

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

2. Edytuj szablon w celu spełnienia wymagań. Rozważ utworzenie [pliku parametrów Menedżer zasobów](../../azure-resource-manager/templates/parameter-files.md) zamiast przekazywania parametrów jako wartości wbudowanych.

3. Zapisz ten plik w folderze lokalnym jako **deployUMSolutiontemplate.js**.

4. Wszystko jest teraz gotowe do wdrożenia tego szablonu. Możesz użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Po wyświetleniu monitu o nazwę obszaru roboczego i konta usługi Automation Podaj nazwę globalnie unikatową we wszystkich subskrypcjach platformy Azure.

    **Program PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Interfejs wiersza polecenia platformy Azure**

    ```azurecli
    az deployment group create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    Wdrożenie może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do następującego:

    ![Przykładowy wynik po zakończeniu wdrażania](media/enable-from-template/template-output.png)

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W Azure Portal Otwórz utworzone konto usługi Automation.

3. W okienku po lewej stronie wybierz pozycję **elementy Runbook**. Na stronie **elementy Runbook** są wyświetlane trzy elementy Runbook samouczka utworzone przy użyciu konta usługi Automation.

    ![Elementy Runbook samouczka utworzone przy użyciu konta usługi Automation](../media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. W okienku po lewej stronie wybierz pozycję **połączony obszar roboczy**. Na stronie **połączony obszar roboczy** zostanie wyświetlony obszar roboczy log Analytics określony wcześniej połączony z kontem usługi Automation.

    ![Konto usługi Automation połączone z obszarem roboczym Log Analytics](../media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

5. W okienku po lewej stronie wybierz pozycję **Update Management**. Na stronie **Zarządzanie aktualizacjami** zostanie wyświetlona strona Ocena bez informacji w wyniku ich włączenia, a maszyny nie są skonfigurowane do zarządzania.

    ![Widok oceny funkcji Update Management](./media/enable-from-template/update-management-assessment-view.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie są już potrzebne, Usuń rozwiązanie **aktualizacji** w obszarze roboczym log Analytics, Odłącz konto usługi Automation od obszaru roboczego, a następnie usuń konto usługi Automation i obszar roboczy.

## <a name="next-steps"></a>Następne kroki

* Aby używać Update Management dla maszyn wirtualnych, zobacz [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych](manage-updates-for-vm.md).

* Jeśli nie chcesz już używać Update Management i chcesz go usunąć, zobacz instrukcje w temacie [usuwanie Update Management funkcji](remove-feature.md).

* Aby usunąć maszyny wirtualne z Update Management, zobacz [usuwanie maszyn wirtualnych z Update Management](remove-vms.md).