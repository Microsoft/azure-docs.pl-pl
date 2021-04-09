---
title: Wdrażanie zasobów w grupie zarządzania
description: Opisuje sposób wdrażania zasobów w zakresie grupy zarządzania w szablonie Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: dc7418d9e93fb50590c5e2502b3a3ffb3847273f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043312"
---
# <a name="management-group-deployments-with-arm-templates"></a>Wdrożenia grup zarządzania przy użyciu szablonów ARM

Jako Twoja organizacja można wdrożyć szablon Azure Resource Manager (szablon ARM), aby utworzyć zasoby na poziomie grupy zarządzania. Na przykład może być konieczne zdefiniowanie i przypisanie [zasad](../../governance/policy/overview.md) lub [kontroli dostępu opartej na ROLACH (Azure RBAC)](../../role-based-access-control/overview.md) dla grupy zarządzania. Szablony na poziomie grupy zarządzania umożliwiają deklaratywne stosowanie zasad i przypisywanie ról na poziomie grupy zarządzania.

## <a name="supported-resources"></a>Obsługiwane zasoby

Nie wszystkie typy zasobów można wdrożyć na poziomie grupy zarządzania. W tej sekcji przedstawiono typy zasobów, które są obsługiwane.

W przypadku planów platformy Azure Użyj:

* [pojawia](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [plany](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [wersje](/azure/templates/microsoft.blueprint/blueprints/versions)

Aby uzyskać Azure Policy, użyj:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [korygowania](/azure/templates/microsoft.policyinsights/remediations)

W przypadku kontroli dostępu opartej na rolach platformy Azure (RBAC) Użyj:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

W przypadku szablonów zagnieżdżonych wdrażanych w ramach subskrypcji lub grup zasobów należy użyć:

* [komputerów](/azure/templates/microsoft.resources/deployments)

Aby zarządzać zasobami, użyj:

* [tabliczk](/azure/templates/microsoft.resources/tags)

Grupy zarządzania to zasoby na poziomie dzierżawy. Można jednak utworzyć grupy zarządzania w ramach wdrożenia grupy zarządzania przez ustawienie zakresu nowej grupy zarządzania dla dzierżawy. Zobacz [Grupa zarządzania](#management-group).

## <a name="schema"></a>Schemat

Schemat używany do wdrożeń grup zarządzania różni się od schematu dla wdrożeń grup zasobów.

W przypadku szablonów Użyj:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    ...
}
```

Schemat pliku parametrów jest taki sam dla wszystkich zakresów wdrożenia. W przypadku plików parametrów należy użyć:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Polecenia wdrażania

Aby wdrożyć w grupie zarządzania, Użyj poleceń wdrożenia grupy zarządzania.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

W przypadku interfejsu wiersza polecenia platformy Azure Użyj polecenia [AZ Deployment mg Create](/cli/azure/deployment/mg#az-deployment-mg-create):

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uzyskać Azure PowerShell, użyj polecenie [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

---

Aby uzyskać bardziej szczegółowe informacje na temat poleceń wdrażania i opcji wdrażania szablonów ARM, zobacz:

* [Wdrażanie zasobów za pomocą szablonów ARM i Azure Portal](deploy-portal.md)
* [Wdrażanie zasobów za pomocą szablonów ARM i interfejsu wiersza polecenia platformy Azure](deploy-cli.md)
* [Wdrażanie zasobów za pomocą szablonów ARM i Azure PowerShell](deploy-powershell.md)
* [Wdrażanie zasobów za pomocą szablonów ARM i interfejsu API REST Azure Resource Manager](deploy-rest.md)
* [Użyj przycisku wdrożenia, aby wdrożyć szablony z repozytorium GitHub](deploy-to-azure-button.md)
* [Wdrażanie szablonów usługi ARM na podstawie Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Lokalizacja i nazwa wdrożenia

W przypadku wdrożeń na poziomie grupy zarządzania należy podać lokalizację wdrożenia. Lokalizacja wdrożenia jest oddzielona od lokalizacji wdrażanych zasobów. Lokalizacja wdrożenia określa miejsce przechowywania danych wdrożenia. Wdrożenia [subskrypcji](deploy-to-subscription.md) i [dzierżawców](deploy-to-tenant.md) wymagają również lokalizacji. W przypadku wdrożeń [grup zasobów](deploy-to-resource-group.md) lokalizacja grupy zasobów jest używana do przechowywania danych wdrożenia.

Możesz podać nazwę wdrożenia lub użyć domyślnej nazwy wdrożenia. Nazwa domyślna to nazwa pliku szablonu. Na przykład wdrożenie szablonu o nazwie _azuredeploy.jsw_ programie tworzy domyślną nazwę wdrożenia **azuredeploy**.

Dla każdej nazwy wdrożenia lokalizacja jest niezmienna. Nie można utworzyć wdrożenia w jednej lokalizacji, gdy istnieje wdrożenie o tej samej nazwie w innej lokalizacji. Jeśli na przykład utworzysz wdrożenie grupy zarządzania o nazwie **deployment1** w **centrali**, nie będzie można utworzyć innego wdrożenia o nazwie **deployment1** , ale lokalizacji **zachodniej**. Jeśli zostanie wyświetlony kod błędu `InvalidDeploymentLocation` , użyj innej nazwy lub tej samej lokalizacji co poprzednie wdrożenie dla tej nazwy.

## <a name="deployment-scopes"></a>Zakresy wdrożenia

Podczas wdrażania w grupie zarządzania można wdrożyć zasoby w programie:

* docelowa Grupa zarządzania z operacji
* inna grupa zarządzania w dzierżawie
* subskrypcje w grupie zarządzania
* grupy zasobów w grupie zarządzania
* Dzierżawca dla grupy zasobów

[Zasób rozszerzenia](scope-extension-resources.md) może być objęty zakresem docelowym, który jest inny niż cel wdrożenia.

Użytkownik wdrażający szablon musi mieć dostęp do określonego zakresu.

W tej sekcji pokazano, jak określić różne zakresy. Można połączyć te różne zakresy w jednym szablonie.

### <a name="scope-to-target-management-group"></a>Zakres docelowy grupy zarządzania

Zasoby zdefiniowane w sekcji zasoby szablonu są stosowane do grupy zarządzania z polecenia wdrożenia.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

### <a name="scope-to-another-management-group"></a>Zakres do innej grupy zarządzania

Aby wskazać inną grupę zarządzania, Dodaj wdrożenie zagnieżdżone i określ `scope` Właściwość. Ustaw `scope` Właściwość na wartość w formacie `Microsoft.Management/managementGroups/<mg-name>` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Zakres subskrypcji

Możesz również kierować subskrypcje w grupie zarządzania. Użytkownik wdrażający szablon musi mieć dostęp do określonego zakresu.

Aby uzyskać subskrypcję w grupie zarządzania, należy użyć zagnieżdżonego wdrożenia i `subscriptionId` właściwości.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Zakres do grupy zasobów

Można również kierować grupy zasobów w grupie zarządzania. Użytkownik wdrażający szablon musi mieć dostęp do określonego zakresu.

Aby określić grupę zasobów w grupie zarządzania, należy użyć wdrożenia zagnieżdżonego. Ustaw `subscriptionId` właściwości i `resourceGroup` . Nie ustawiaj lokalizacji dla wdrożenia zagnieżdżonego, ponieważ jest ono wdrożone w lokalizacji grupy zasobów.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="9,10,18":::

Aby użyć wdrożenia grupy zarządzania do utworzenia grupy zasobów w ramach subskrypcji i wdrożenia konta magazynu w tej grupie zasobów, zobacz [wdrażanie w ramach subskrypcji i grupy zasobów](#deploy-to-subscription-and-resource-group).

### <a name="scope-to-tenant"></a>Zakres do dzierżawy

Aby utworzyć zasoby w dzierżawie, należy ustawić `scope` na `/` . Użytkownik wdrażający szablon musi mieć [wymagany dostęp do wdrożenia w dzierżawie](deploy-to-tenant.md#required-access).

Aby użyć wdrożenia zagnieżdżonego, ustaw `scope` i `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-to-tenant.json" highlight="9,10,14":::

Lub można ustawić zakres `/` dla niektórych typów zasobów, takich jak grupy zarządzania. Tworzenie nowej grupy zarządzania zostało opisane w następnej sekcji.

## <a name="management-group"></a>Grupa zarządzania

Aby utworzyć grupę zarządzania w ramach wdrożenia grupy zarządzania, należy ustawić zakres na `/` dla grupy zarządzania.

Poniższy przykład tworzy nową grupę zarządzania w głównej grupie zarządzania.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-create-mg.json" highlight="12,15":::

W następnym przykładzie zostanie utworzona nowa grupa zarządzania w grupie zarządzania określonej jako nadrzędna. Należy zauważyć, że zakres jest ustawiony na `/` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mgName": {
            "type": "string",
            "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
        },
        "parentMG": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('mgName')]",
            "type": "Microsoft.Management/managementGroups",
            "apiVersion": "2020-05-01",
            "scope": "/",
            "location": "eastus",
            "properties": {
                "details": {
                    "parent": {
                        "id": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('parentMG'))]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "output": {
            "type": "string",
            "value": "[parameters('mgName')]"
        }
    }
}
```

## <a name="subscriptions"></a>Subskrypcje

Aby utworzyć nową subskrypcję platformy Azure w grupie zarządzania przy użyciu szablonu ARM, zobacz:

* [Programowe tworzenie subskrypcji usługi Azure Enterprise Agreement](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)
* [Programowe tworzenie subskrypcji platformy Azure dla umowy klienta Microsoft](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-customer-agreement.md)
* [Programistyczne tworzenie subskrypcji platformy Azure na potrzeby umowy partnerskiej firmy Microsoft](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-partner-agreement.md)

Aby wdrożyć szablon, który przenosi istniejącą subskrypcję platformy Azure do nowej grupy zarządzania, zobacz [przenoszenie subskrypcji w ramach szablonu ARM](../../governance/management-groups/manage.md#move-subscriptions-in-arm-template)

## <a name="azure-policy"></a>Azure Policy

Niestandardowe definicje zasad wdrożone w grupie zarządzania to rozszerzenia grupy zarządzania. Aby uzyskać identyfikator niestandardowej definicji zasad, użyj funkcji [extensionResourceId ()](template-functions-resource.md#extensionresourceid) . Wbudowane definicje zasad to zasoby na poziomie dzierżawy. Aby uzyskać identyfikator wbudowanej definicji zasad, użyj funkcji [tenantResourceId ()](template-functions-resource.md#tenantresourceid) .

Poniższy przykład pokazuje, jak [zdefiniować](../../governance/policy/concepts/definition-structure.md) zasady na poziomie grupy zarządzania i przypisać je.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMG": {
            "type": "string",
            "metadata": {
                "description": "Target Management Group"
            }
        },
        "allowedLocations": {
            "type": "array",
            "defaultValue": [
                "australiaeast",
                "australiasoutheast",
                "australiacentral"
            ],
            "metadata": {
                "description": "An array of the allowed locations, all other locations will be denied by the created policy."
            }
        }
    },
    "variables": {
        "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
        "policyDefinition": "LocationRestriction"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "[variables('policyDefinition')]",
            "apiVersion": "2019-09-01",
            "properties": {
                "policyType": "Custom",
                "mode": "All",
                "parameters": {
                },
                "policyRule": {
                    "if": {
                        "not": {
                            "field": "location",
                            "in": "[parameters('allowedLocations')]"
                        }
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2019-09-01",
            "dependsOn": [
                "[variables('policyDefinition')]"
            ],
            "properties": {
                "scope": "[variables('mgScope')]",
                "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
            }
        }
    ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>Wdrażanie w ramach subskrypcji i grupy zasobów

Na podstawie wdrożenia na poziomie grupy zarządzania można określić subskrypcję w grupie zarządzania. Poniższy przykład obejmuje tworzenie grupy zasobów w ramach subskrypcji i wdrożenie konta magazynu w tej grupie zasobów.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "nestedsubId": {
            "type": "string"
        },
        "nestedRG": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        },
        "nestedLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-10-01",
            "name": "nestedSub",
            "location": "[parameters('nestedLocation')]",
            "subscriptionId": "[parameters('nestedSubId')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                    },
                    "variables": {
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Resources/resourceGroups",
                            "apiVersion": "2020-10-01",
                            "name": "[parameters('nestedRG')]",
                            "location": "[parameters('nestedLocation')]"
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-10-01",
            "name": "nestedRG",
            "subscriptionId": "[parameters('nestedSubId')]",
            "resourceGroup": "[parameters('nestedRG')]",
            "dependsOn": [
                "nestedSub"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2019-04-01",
                            "name": "[parameters('storageAccountName')]",
                            "location": "[parameters('nestedLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o przypisywaniu ról, zobacz [Dodawanie przypisań ról platformy Azure przy użyciu szablonów Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Aby zapoznać się z przykładem wdrażania ustawień obszaru roboczego dla Azure Security Center, zobacz [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Szablony można także wdrażać na poziomie [subskrypcji](deploy-to-subscription.md) i na [poziomie dzierżawy](deploy-to-tenant.md).
