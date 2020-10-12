---
title: Wdrażanie zasobów w grupie zarządzania
description: Opisuje sposób wdrażania zasobów w zakresie grupy zarządzania w szablonie Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: 23f86d7d0b7e1f882cf3fb74adc484e0fe47db87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372429"
---
# <a name="create-resources-at-the-management-group-level"></a>Tworzenie zasobów na poziomie grupy zarządzania

Jako Twoja organizacja można wdrożyć szablon Azure Resource Manager (szablon ARM), aby utworzyć zasoby na poziomie grupy zarządzania. Na przykład może być konieczne zdefiniowanie i przypisanie [zasad](../../governance/policy/overview.md) lub [kontroli dostępu opartej na ROLACH (Azure RBAC)](../../role-based-access-control/overview.md) dla grupy zarządzania. Szablony na poziomie grupy zarządzania umożliwiają deklaratywne stosowanie zasad i przypisywanie ról na poziomie grupy zarządzania.

## <a name="supported-resources"></a>Obsługiwane zasoby

Nie wszystkie typy zasobów można wdrożyć na poziomie grupy zarządzania. W tej sekcji przedstawiono typy zasobów, które są obsługiwane.

W przypadku planów platformy Azure Użyj:

* [pojawia](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [plany](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [wersje](/azure/templates/microsoft.blueprint/blueprints/versions)

W przypadku zasad platformy Azure Użyj:

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

## <a name="schema"></a>Schemat

Schemat używany do wdrożeń grup zarządzania różni się od schematu dla wdrożeń grup zasobów.

W przypadku szablonów Użyj:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

Schemat pliku parametrów jest taki sam dla wszystkich zakresów wdrożenia. W przypadku plików parametrów należy użyć:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-scopes"></a>Zakresy wdrożenia

W przypadku wdrażania w grupie zarządzania można wskazać grupę zarządzania określoną w poleceniu wdrożenia lub wybrać inne grupy zarządzania w dzierżawie.

Zasoby zdefiniowane w sekcji zasoby szablonu są stosowane do grupy zarządzania z polecenia wdrożenia.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

Aby wskazać inną grupę zarządzania, Dodaj wdrożenie zagnieżdżone i określ `scope` Właściwość. Ustaw `scope` Właściwość na wartość w formacie `Microsoft.Management/managementGroups/<mg-name>` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,22":::

Możesz również kierować subskrypcje lub grupy zasobów w grupie zarządzania. Użytkownik wdrażający szablon musi mieć dostęp do określonego zakresu.

Aby uzyskać subskrypcję w grupie zarządzania, należy użyć zagnieżdżonego wdrożenia i `subscriptionId` właściwości.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="10,18":::

Aby wskazać grupę zasobów w ramach tej subskrypcji, Dodaj kolejne zagnieżdżone wdrożenie i `resourceGroup` Właściwość.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="10,21,25":::

Aby użyć wdrożenia grupy zarządzania do utworzenia grupy zasobów w ramach subskrypcji i wdrożenia konta magazynu w tej grupie zasobów, zobacz [wdrażanie w ramach subskrypcji i grupy zasobów](#deploy-to-subscription-and-resource-group).

## <a name="deployment-commands"></a>Polecenia wdrażania

Polecenia dla wdrożeń grup zarządzania są inne niż polecenia dla wdrożeń grup zasobów.

W przypadku interfejsu wiersza polecenia platformy Azure Użyj polecenia [AZ Deployment mg Create](/cli/azure/deployment/mg#az-deployment-mg-create):

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Aby uzyskać Azure PowerShell, użyj polecenie [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

W przypadku interfejsu API REST Użyj funkcji [wdrożenia — Utwórz w zakresie grupy zarządzania](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Lokalizacja i nazwa wdrożenia

W przypadku wdrożeń na poziomie grupy zarządzania należy podać lokalizację wdrożenia. Lokalizacja wdrożenia jest oddzielona od lokalizacji wdrażanych zasobów. Lokalizacja wdrożenia określa miejsce przechowywania danych wdrożenia.

Możesz podać nazwę wdrożenia lub użyć domyślnej nazwy wdrożenia. Nazwa domyślna to nazwa pliku szablonu. Na przykład wdrożenie szablonu o nazwie **azuredeploy.jsw** programie tworzy domyślną nazwę wdrożenia **azuredeploy**.

Dla każdej nazwy wdrożenia lokalizacja jest niezmienna. Nie można utworzyć wdrożenia w jednej lokalizacji, gdy istnieje wdrożenie o tej samej nazwie w innej lokalizacji. Jeśli zostanie wyświetlony kod błędu `InvalidDeploymentLocation` , użyj innej nazwy lub tej samej lokalizacji co poprzednie wdrożenie dla tej nazwy.

## <a name="use-template-functions"></a>Korzystanie z funkcji szablonu

W przypadku wdrożeń grup zarządzania istnieją pewne ważne zagadnienia dotyczące korzystania z funkcji szablonu:

* Funkcja [przesourceing ()](template-functions-resource.md#resourcegroup) **nie** jest obsługiwana.
* Funkcja [Subscription ()](template-functions-resource.md#subscription) **nie** jest obsługiwana.
* Obsługiwane są funkcje [Reference ()](template-functions-resource.md#reference) i [list ()](template-functions-resource.md#list) .
* Nie należy używać funkcji [ResourceID ()](template-functions-resource.md#resourceid) dla zasobów wdrożonych w grupie zarządzania.

  Zamiast tego należy użyć funkcji [extensionResourceId ()](template-functions-resource.md#extensionresourceid) dla zasobów, które są zaimplementowane jako rozszerzenia grupy zarządzania. Niestandardowe definicje zasad wdrożone w grupie zarządzania to rozszerzenia grupy zarządzania.

  Aby uzyskać identyfikator zasobu dla niestandardowej definicji zasad na poziomie grupy zarządzania, użyj:
  
  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

  Użyj funkcji [tenantResourceId](template-functions-resource.md#tenantresourceid) dla zasobów dzierżawy, które są dostępne w grupie zarządzania. Wbudowane definicje zasad to zasoby na poziomie dzierżawy.

  Aby uzyskać identyfikator zasobu dla wbudowanej definicji zasad, użyj:
  
  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="azure-policy"></a>Azure Policy

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
      "apiVersion": "2020-06-01",
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
              "apiVersion": "2020-06-01",
              "name": "[parameters('nestedRG')]",
              "location": "[parameters('nestedLocation')]",
            },
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedSubRG",
              "resourceGroup": "[parameters('nestedRG')]",
              "dependsOn": [
                "[parameters('nestedRG')]"
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
      }
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o przypisywaniu ról, zobacz [Dodawanie przypisań ról platformy Azure przy użyciu szablonów Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Aby zapoznać się z przykładem wdrażania ustawień obszaru roboczego dla Azure Security Center, zobacz [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Szablony można także wdrażać na poziomie [subskrypcji](deploy-to-subscription.md) i na [poziomie dzierżawy](deploy-to-tenant.md).
