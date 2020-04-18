---
title: Wdrażanie zasobów w ramach subskrypcji
description: W tym artykule opisano sposób tworzenia grupy zasobów w szablonie usługi Azure Resource Manager. Pokazano również, jak wdrożyć zasoby w zakresie subskrypcji platformy Azure.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 6bec29a07653ff5ad7d1e2f8317246049e127c8c
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605009"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Tworzenie grup zasobów i zasobów na poziomie subskrypcji

Aby uprościć zarządzanie zasobami w ramach subskrypcji platformy Azure, można zdefiniować i przypisać [zasady](../../governance/policy/overview.md) lub [formanty dostępu oparte na rolach](../../role-based-access-control/overview.md) w całej subskrypcji. Za pomocą szablonów poziomu subskrypcji można deklaratywnie zastosować zasady i przypisać role w subskrypcji. Można również tworzyć grupy zasobów i wdrażać zasoby.

Aby wdrożyć szablony na poziomie subskrypcji, użyj interfejsu API platformy Azure, programu PowerShell lub REST. Witryna Azure portal nie obsługuje wdrażania na poziomie subskrypcji.

## <a name="supported-resources"></a>Obsługiwane zasoby

Na poziomie subskrypcji można wdrożyć następujące typy zasobów:

* [Budżetów](/azure/templates/microsoft.consumption/budgets)
* [wdrożenia](/azure/templates/microsoft.resources/deployments) — dla szablonów zagnieżdżonych, które wdrażają w grupach zasobów.
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns (równorzędny)](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)
* [zmiany zasadAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [Policydefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [zasadySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [środki zaradcze](/azure/templates/microsoft.policyinsights/2019-07-01/remediations)
* [zasobyGrupy](/azure/templates/microsoft.resources/resourcegroups)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [definicje ról](/azure/templates/microsoft.authorization/roledefinitions)
* [zakresPodpisy](/azure/templates/microsoft.managednetwork/scopeassignments)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [Tagi](/azure/templates/microsoft.resources/tags)

### <a name="schema"></a>Schemat

Schemat używany do wdrożeń na poziomie subskrypcji różni się od schematu dla wdrożeń grup zasobów.

W przypadku szablonów użyj:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

Schemat pliku parametrów jest taki sam dla wszystkich zakresów wdrażania. W przypadku plików parametrów należy użyć:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Polecenia wdrażania

Polecenia dla wdrożeń na poziomie subskrypcji różnią się od poleceń dla wdrożeń grup zasobów.

W przypadku interfejsu wiersza polecenia platformy Azure należy użyć [sub create wdrożenia az](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create). Poniższy przykład wdraża szablon w celu utworzenia grupy zasobów:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

W przypadku polecenia wdrażania programu PowerShell należy użyć polecenia [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) lub **New-AzSubscriptionDeployment**. Poniższy przykład wdraża szablon w celu utworzenia grupy zasobów:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

W przypadku interfejsu API REST użyj [wdrożeń — tworzenie w zakresie subskrypcji](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

## <a name="deployment-location-and-name"></a>Lokalizacja i nazwa wdrożenia

W przypadku wdrożeń na poziomie subskrypcji należy podać lokalizację wdrożenia. Lokalizacja wdrożenia jest oddzielona od lokalizacji wydrążenych zasobów. Lokalizacja wdrożenia określa, gdzie mają być przechowywane dane wdrożenia.

Można podać nazwę wdrożenia lub użyć domyślnej nazwy wdrożenia. Domyślna nazwa to nazwa pliku szablonu. Na przykład wdrożenie szablonu o nazwie **azuredeploy.json** tworzy domyślną nazwę wdrożenia **azuredeploy**.

Dla każdej nazwy wdrożenia lokalizacja jest niezmienna. Nie można utworzyć wdrożenia w jednej lokalizacji, gdy istnieje istniejące wdrożenie o tej samej nazwie w innej lokalizacji. Jeśli zostanie wyświetlony `InvalidDeploymentLocation`kod błędu, użyj innej nazwy lub tej samej lokalizacji co poprzednie wdrożenie dla tej nazwy.

## <a name="use-template-functions"></a>Korzystanie z funkcji szablonu

W przypadku wdrożeń na poziomie subskrypcji, istnieją pewne ważne zagadnienia podczas korzystania z funkcji szablonu:

* Funkcja [resourceGroup()](template-functions-resource.md#resourcegroup) **nie** jest obsługiwana.
* Obsługiwane są funkcje [reference()](template-functions-resource.md#reference) i [list().](template-functions-resource.md#list)
* Użyj funkcji [subscriptionResourceId(),](template-functions-resource.md#subscriptionresourceid) aby uzyskać identyfikator zasobu dla zasobów, które są wdrażane na poziomie subskrypcji.

  Na przykład, aby uzyskać identyfikator zasobu dla definicji zasad, należy użyć:
  
  ```json
  subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```
  
  Identyfikator zwróconego zasobu ma następujący format:

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-resource-groups"></a>Tworzenie grup zasobów

Aby utworzyć grupę zasobów w szablonie usługi Azure Resource Manager, należy zdefiniować [zasób Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) o nazwie i lokalizacji dla grupy zasobów. Można utworzyć grupę zasobów i wdrożyć zasoby do tej grupy zasobów w tym samym szablonie.

Poniższy szablon tworzy pustą grupę zasobów.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Użyj [elementu kopiowania](copy-resources.md) z grupami zasobów, aby utworzyć więcej niż jedną grupę zasobów.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgNamePrefix": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "instanceCount": {
      "type": "int"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "location": "[parameters('rgLocation')]",
      "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
      "copy": {
        "name": "rgCopy",
        "count": "[parameters('instanceCount')]"
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Aby uzyskać informacje na temat iteracji zasobów, zobacz [Wdrażanie więcej niż jednego wystąpienia zasobu w szablonach usługi Azure Resource Manager](./copy-resources.md)i [samouczek: Tworzenie wielu wystąpień zasobów za pomocą szablonów Menedżera zasobów](./template-tutorial-create-multiple-instances.md).

## <a name="resource-group-and-resources"></a>Grupa zasobów i zasoby

Aby utworzyć grupę zasobów i wdrożyć do niej zasoby, użyj szablonu zagnieżdżonego. Szablon zagnieżdżony definiuje zasoby do wdrożenia w grupie zasobów. Ustaw szablon zagnieżdżony jako zależny od grupy zasobów, aby upewnić się, że grupa zasobów istnieje przed wdrożeniem zasobów.

Poniższy przykład tworzy grupę zasobów i wdraża konto magazynu do grupy zasobów.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "location": "[parameters('rgLocation')]",
      "name": "[parameters('rgName')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2017-10-01",
              "name": "[variables('storageName')]",
              "location": "[parameters('rgLocation')]",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ],
          "outputs": {}
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="create-policies"></a>Tworzenie zasad

### <a name="assign-policy"></a>Przypisywanie zasad

Poniższy przykład przypisuje istniejącą definicję zasad do subskrypcji. Jeśli zasady przyjmuje parametry, podaj je jako obiekt. Jeśli zasady nie przyjmują parametrów, należy użyć domyślnego pustego obiektu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string"
    },
    "policyName": {
      "type": "string"
    },
    "policyParameters": {
      "type": "object",
      "defaultValue": {}
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

Aby wdrożyć ten szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Aby wdrożyć ten szablon za pomocą programu PowerShell, należy użyć:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>Definiowanie i przypisywanie zasad

Można [zdefiniować](../../governance/policy/concepts/definition-structure.md) i przypisać zasadę w tym samym szablonie.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
      "properties": {
        "policyType": "Custom",
        "parameters": {},
        "policyRule": {
          "if": {
            "field": "location",
            "equals": "northeurope"
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    },
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-05-01",
      "name": "location-lock",
      "dependsOn": [
        "locationpolicy"
      ],
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
      }
    }
  ]
}
```

Aby utworzyć definicję zasad w ramach subskrypcji i zastosować ją do subskrypcji, użyj następującego polecenia CLI:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

Aby wdrożyć ten szablon za pomocą programu PowerShell, należy użyć:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="template-samples"></a>Przykłady szablonów

* [Utwórz grupę zasobów, zablokuj ją i nadaj jej uprawnienia](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).
* [Tworzenie grupy zasobów, zasad i przypisań zasad](https://github.com/Azure/azure-docs-json-samples/blob/master/subscription-level-deployment/azuredeploy.json).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o przypisywaniu ról, zobacz [Zarządzanie dostępem do zasobów platformy Azure przy użyciu szablonów RBAC i Usługi Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Na przykład wdrażania ustawień obszaru roboczego dla usługi Azure Security Center zobacz [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Przykładowe szablony można znaleźć w [usłudze GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).
* Szablony można również wdrażać na [poziomie grupy zarządzania](deploy-to-management-group.md) i [dzierżawy](deploy-to-tenant.md).
