---
title: Wdrażanie zasobów w grupie zarządzania
description: Opisuje sposób wdrażania zasobów w zakresie grupy zarządzania w szablonie Azure Resource Manager zarządzania.
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 74e00921a1170a7750f4a2d239bb778150ac2cae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781941"
---
# <a name="management-group-deployments-with-arm-templates"></a>Wdrożenia grup zarządzania przy użyciu szablonów usługi ARM

W czasie, gdy Twoja organizacja dojrzeje, możesz wdrożyć szablon usługi Azure Resource Manager (arm), aby tworzyć zasoby na poziomie grupy zarządzania. Na przykład może być konieczne [](../../governance/policy/overview.md) zdefiniowanie i przypisanie zasad lub kontroli dostępu na podstawie ról [(RBAC)](../../role-based-access-control/overview.md) platformy Azure dla grupy zarządzania. Za pomocą szablonów poziomu grupy zarządzania można deklaratywnie stosować zasady i przypisywać role na poziomie grupy zarządzania.

## <a name="supported-resources"></a>Obsługiwane zasoby

Nie wszystkie typy zasobów można wdrożyć na poziomie grupy zarządzania. W tej sekcji wymieniono obsługiwane typy zasobów.

Na Azure Blueprints użyj:

* [Artefakty](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [Plany](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [Wersje](/azure/templates/microsoft.blueprint/blueprints/versions)

Na Azure Policy użyj:

* [policyAssignments (przypisania zasad)](/azure/templates/microsoft.authorization/policyassignments)
* [Policydefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [korygowanie](/azure/templates/microsoft.policyinsights/remediations)

W przypadku kontroli dostępu opartej na rolach (RBAC) na platformie Azure użyj:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

W przypadku zagnieżdżonych szablonów wdrażanych w subskrypcjach lub grupach zasobów użyj:

* [Wdrożeń](/azure/templates/microsoft.resources/deployments)

Do zarządzania zasobami użyj:

* [Tagi](/azure/templates/microsoft.resources/tags)

Grupy zarządzania to zasoby na poziomie dzierżawy. Grupy zarządzania można jednak utworzyć we wdrożeniu grupy zarządzania, ustawiając zakres nowej grupy zarządzania na dzierżawę. Zobacz [Grupa zarządzania](#management-group).

## <a name="schema"></a>Schemat

Schemat wdrożeń grup zarządzania różni się od schematu wdrożeń grup zasobów.

W przypadku szablonów użyj:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    ...
}
```

Schemat pliku parametrów jest taki sam dla wszystkich zakresów wdrożenia. W przypadku plików parametrów użyj:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Polecenia wdrażania

Aby wdrożyć w grupie zarządzania, użyj poleceń wdrażania grupy zarządzania.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

W przypadku interfejsu wiersza polecenia platformy Azure użyj [polecenia az deployment mg create:](/cli/azure/deployment/mg#az_deployment_mg_create)

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Na Azure PowerShell użyj [new-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

---

Aby uzyskać bardziej szczegółowe informacje na temat poleceń wdrażania i opcji wdrażania szablonów usługi ARM, zobacz:

* [Wdrażanie zasobów przy użyciu szablonów usługi ARM i Azure Portal](deploy-portal.md)
* [Wdrażanie zasobów przy użyciu szablonów usługi ARM i interfejsu wiersza polecenia platformy Azure](deploy-cli.md)
* [Wdrażanie zasobów przy użyciu szablonów usługi ARM i Azure PowerShell](deploy-powershell.md)
* [Wdrażanie zasobów za pomocą szablonów usługi ARM i Azure Resource Manager API REST](deploy-rest.md)
* [Używanie przycisku wdrażania do wdrażania szablonów z repozytorium GitHub](deploy-to-azure-button.md)
* [Wdrażanie szablonów usługi ARM z Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Lokalizacja i nazwa wdrożenia

W przypadku wdrożeń na poziomie grupy zarządzania należy podać lokalizację wdrożenia. Lokalizacja wdrożenia jest oddzielona od lokalizacji wdrażanych zasobów. Lokalizacja wdrożenia określa miejsce przechowywania danych wdrożenia. [Wdrożenia](deploy-to-subscription.md) subskrypcji [i](deploy-to-tenant.md) dzierżawy również wymagają lokalizacji. W [przypadku wdrożeń](deploy-to-resource-group.md) grup zasobów lokalizacja grupy zasobów jest używana do przechowywania danych wdrożenia.

Możesz podać nazwę wdrożenia lub użyć domyślnej nazwy wdrożenia. Domyślna nazwa to nazwa pliku szablonu. Na przykład wdrożenie szablonu o nazwie _azuredeploy.jsna powoduje_ utworzenie domyślnej nazwy wdrożenia **azuredeploy**.

Dla każdej nazwy wdrożenia lokalizacja jest niezmienna. Nie można utworzyć wdrożenia w jednej lokalizacji, jeśli istnieje wdrożenie o tej samej nazwie w innej lokalizacji. Jeśli na przykład utworzysz wdrożenie grupy zarządzania o nazwie **deployment1** w lokalizacji **centralus,** nie będzie można później utworzyć kolejnego wdrożenia o nazwie **deployment1,** ale w lokalizacji **westus**. Jeśli zostanie wyświetlany kod błędu , użyj innej nazwy lub tej samej lokalizacji co poprzednie `InvalidDeploymentLocation` wdrożenie dla tej nazwy.

## <a name="deployment-scopes"></a>Zakresy wdrożenia

Podczas wdrażania w grupie zarządzania można wdrażać zasoby w:

* docelową grupę zarządzania z operacji
* inna grupa zarządzania w dzierżawie
* subskrypcje w grupie zarządzania
* grupy zasobów w grupie zarządzania
* dzierżawa grupy zasobów

Zasób [rozszerzenia może](scope-extension-resources.md) być ograniczony do obiektu docelowego, który jest inny niż cel wdrożenia.

Użytkownik wdrażający szablon musi mieć dostęp do określonego zakresu.

W tej sekcji przedstawiono sposób określania różnych zakresów. Te różne zakresy można połączyć w jednym szablonie.

### <a name="scope-to-target-management-group"></a>Zakres do docelowej grupy zarządzania

Zasoby zdefiniowane w sekcji resources szablonu są stosowane do grupy zarządzania za pomocą polecenia wdrożenia.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

### <a name="scope-to-another-management-group"></a>Zakres do innej grupy zarządzania

Aby określić inną grupę zarządzania, dodaj wdrożenie zagnieżdżone i określ `scope` właściwość . Ustaw właściwość `scope` na wartość w formacie `Microsoft.Management/managementGroups/<mg-name>` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Zakres do subskrypcji

Można również określić docelowe subskrypcje w grupie zarządzania. Użytkownik wdrażający szablon musi mieć dostęp do określonego zakresu.

Aby ukierunkować subskrypcję w grupie zarządzania, użyj zagnieżdżonych wdrożeń i `subscriptionId` właściwości .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Zakres do grupy zasobów

Można również określić docelowe grupy zasobów w grupie zarządzania. Użytkownik wdrażający szablon musi mieć dostęp do określonego zakresu.

Aby ukierunkować grupę zasobów w grupie zarządzania, należy użyć wdrożenia zagnieżdżone. Ustaw właściwości `subscriptionId` `resourceGroup` i . Nie ustawiaj lokalizacji dla zagnieżdżonych wdrożeń, ponieważ jest ono wdrażane w lokalizacji grupy zasobów.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="9,10,18":::

Aby użyć wdrożenia grupy zarządzania w celu utworzenia grupy zasobów w ramach subskrypcji i wdrożenia konta magazynu w tej grupie zasobów, zobacz Deploy to subscription and resource group (Wdrażanie w subskrypcji i [grupie zasobów).](#deploy-to-subscription-and-resource-group)

### <a name="scope-to-tenant"></a>Zakres do dzierżawy

Aby utworzyć zasoby w dzierżawie, ustaw wartość `scope` `/` . Użytkownik wdrażający szablon musi mieć wymagany dostęp do wdrożenia [w dzierżawie](deploy-to-tenant.md#required-access).

Aby użyć wdrożenia zagnieżdżone, ustaw `scope` i `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-to-tenant.json" highlight="9,10,14":::

Można też ustawić zakres na dla `/` niektórych typów zasobów, takich jak grupy zarządzania. Tworzenie nowej grupy zarządzania jest opisane w następnej sekcji.

## <a name="management-group"></a>Grupa zarządzania

Aby utworzyć grupę zarządzania we wdrożeniu grupy zarządzania, należy ustawić zakres na `/` dla grupy zarządzania.

Poniższy przykład tworzy nową grupę zarządzania w głównej grupie zarządzania.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-create-mg.json" highlight="12,15":::

W następnym przykładzie grupę zarządzania tworzy się w grupie zarządzania określonej jako nadrzędna. Zwróć uwagę, że zakres jest ustawiony na `/` wartość .

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

Aby utworzyć nową subskrypcję platformy Azure w grupie zarządzania przy użyciu szablonu usługi ARM, zobacz:

* [Programowe tworzenie subskrypcji Enterprise Agreement Azure](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)
* [Programowe tworzenie subskrypcji platformy Azure dla Umowa z Klientem Microsoft](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-customer-agreement.md)
* [Programowe tworzenie subskrypcji platformy Azure dla Microsoft Partner Agreement](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-partner-agreement.md)

Aby wdrożyć szablon, który przenosi istniejącą subskrypcję platformy Azure do nowej grupy zarządzania, zobacz [Przenoszenie subskrypcji w szablonie usługi ARM](../../governance/management-groups/manage.md#move-subscriptions-in-arm-template)

## <a name="azure-policy"></a>Azure Policy

Definicje zasad niestandardowych wdrażane w grupie zarządzania są rozszerzeniami grupy zarządzania. Aby uzyskać identyfikator niestandardowej definicji zasad, użyj [funkcji extensionResourceId().](template-functions-resource.md#extensionresourceid) Wbudowane definicje zasad to zasoby na poziomie dzierżawy. Aby uzyskać identyfikator wbudowanej definicji zasad, użyj funkcji [tenantResourceId().](template-functions-resource.md#tenantresourceid)

W poniższym przykładzie [pokazano, jak zdefiniować](../../governance/policy/concepts/definition-structure.md) zasady na poziomie grupy zarządzania i przypisać je.

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

## <a name="deploy-to-subscription-and-resource-group"></a>Wdrażanie w subskrypcji i grupie zasobów

Z poziomu wdrożenia na poziomie grupy zarządzania można wybrać subskrypcję w grupie zarządzania. Poniższy przykład tworzy grupę zasobów w ramach subskrypcji i wdraża konto magazynu w tej grupie zasobów.

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

* Aby dowiedzieć się więcej na temat przypisywania ról, zobacz [Dodawanie przypisań ról platformy Azure przy użyciu Azure Resource Manager szablonów.](../../role-based-access-control/role-assignments-template.md)
* Aby uzyskać przykład wdrażania ustawień obszaru roboczego dla Azure Security Center, zobacz [deployASCwithWorkspaceSettings.jsna stronie](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Szablony można również wdrażać na [poziomie subskrypcji](deploy-to-subscription.md) [i dzierżawy.](deploy-to-tenant.md)
