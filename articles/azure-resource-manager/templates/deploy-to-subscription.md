---
title: Wdrażanie zasobów w ramach subskrypcji
description: Opisuje sposób tworzenia grupy zasobów w szablonie Azure Resource Manager. Przedstawiono w nim również sposób wdrażania zasobów w zakresie subskrypcji platformy Azure.
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 0673ea5260c7312395acde8a62b5d457657b9793
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91729121"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Tworzenie grup zasobów i zasobów na poziomie subskrypcji

Aby uprościć zarządzanie zasobami, możesz użyć szablonu Azure Resource Manager (szablon ARM) do wdrażania zasobów na poziomie subskrypcji platformy Azure. Na przykład można wdrożyć [zasady](../../governance/policy/overview.md) i [kontrolę dostępu opartą na ROLACH (Azure RBAC)](../../role-based-access-control/overview.md) w ramach subskrypcji, która stosuje je w ramach subskrypcji. Możesz również utworzyć grupy zasobów w ramach subskrypcji i wdrożyć zasoby w grupach zasobów w ramach subskrypcji.

> [!NOTE]
> Można wdrożyć do 800 różnych grup zasobów w ramach wdrożenia na poziomie subskrypcji.

Aby wdrożyć szablony na poziomie subskrypcji, użyj interfejsu wiersza polecenia platformy Azure, programu PowerShell, interfejsu API REST lub portalu.

## <a name="supported-resources"></a>Obsługiwane zasoby

Nie wszystkie typy zasobów można wdrożyć na poziomie subskrypcji. W tej sekcji przedstawiono typy zasobów, które są obsługiwane.

W przypadku planów platformy Azure Użyj:

* [pojawia](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [plany](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [wersje (plany)](/azure/templates/microsoft.blueprint/blueprints/versions)

W przypadku zasad platformy Azure Użyj:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [korygowania](/azure/templates/microsoft.policyinsights/remediations)

W przypadku kontroli dostępu opartej na rolach platformy Azure (RBAC) Użyj:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

W przypadku szablonów zagnieżdżonych, które są wdrażane w grupach zasobów, użyj:

* [komputerów](/azure/templates/microsoft.resources/deployments)

Aby utworzyć nowe grupy zasobów, użyj:

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

Aby zarządzać subskrypcją, użyj:

* [Konfiguracje usługi Advisor](/azure/templates/microsoft.advisor/configurations)
* [budżetów](/azure/templates/microsoft.consumption/budgets)
* [Zmień profil analizy](/azure/templates/microsoft.changeanalysis/profile)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [tabliczk](/azure/templates/microsoft.resources/tags)

Inne obsługiwane typy to:

* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

## <a name="schema"></a>Schemat

Schemat używany do wdrożeń na poziomie subskrypcji różni się od schematu dla wdrożeń grup zasobów.

W przypadku szablonów Użyj:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

Schemat pliku parametrów jest taki sam dla wszystkich zakresów wdrożenia. W przypadku plików parametrów należy użyć:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-scopes"></a>Zakresy wdrożenia

Podczas wdrażania w ramach subskrypcji można wskazać jedną subskrypcję i grupy zasobów w ramach subskrypcji. Nie można przeprowadzić wdrożenia w ramach subskrypcji innej niż subskrypcja docelowa. Użytkownik wdrażający szablon musi mieć dostęp do określonego zakresu.

Zasoby zdefiniowane w sekcji zasobów szablonu są stosowane do subskrypcji.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-sub.json" highlight="5":::

Aby określić grupę zasobów w ramach subskrypcji, Dodaj wdrożenie zagnieżdżone i Uwzględnij `resourceGroup` Właściwość. W poniższym przykładzie zagnieżdżone wdrożenie jest przeznaczone dla grupy zasobów o nazwie `rg2` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-resource-group.json" highlight="9,13":::

W tym artykule można znaleźć szablony, które pokazują, jak wdrażać zasoby w różnych zakresach. Aby utworzyć szablon, który tworzy grupę zasobów i wdraża do niej konto magazynu, zobacz [Tworzenie grupy zasobów i zasobów](#create-resource-group-and-resources). W przypadku szablonu, który tworzy grupę zasobów, stosuje do niej blokadę i przypisuje rolę dla grupy zasobów, zobacz [Kontrola dostępu](#access-control).

## <a name="deployment-commands"></a>Polecenia wdrażania

Polecenia dla wdrożeń na poziomie subskrypcji są inne niż polecenia dla wdrożeń grup zasobów.

W przypadku interfejsu wiersza polecenia platformy Azure Użyj polecenia [AZ Deployment sub Create](/cli/azure/deployment/sub#az-deployment-sub-create). Poniższy przykład wdraża szablon w celu utworzenia grupy zasobów:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

W przypadku polecenia wdrażania programu PowerShell Użyj poleceń [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) i **New-AzSubscriptionDeployment**. Poniższy przykład wdraża szablon w celu utworzenia grupy zasobów:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

W przypadku interfejsu API REST Użyj [wdrożeń — Utwórz zakres subskrypcji](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

## <a name="deployment-location-and-name"></a>Lokalizacja i nazwa wdrożenia

W przypadku wdrożeń na poziomie subskrypcji należy podać lokalizację wdrożenia. Lokalizacja wdrożenia jest oddzielona od lokalizacji wdrażanych zasobów. Lokalizacja wdrożenia określa miejsce przechowywania danych wdrożenia.

Możesz podać nazwę wdrożenia lub użyć domyślnej nazwy wdrożenia. Nazwa domyślna to nazwa pliku szablonu. Na przykład wdrożenie szablonu o nazwie **azuredeploy.jsw** programie tworzy domyślną nazwę wdrożenia **azuredeploy**.

Dla każdej nazwy wdrożenia lokalizacja jest niezmienna. Nie można utworzyć wdrożenia w jednej lokalizacji, gdy istnieje wdrożenie o tej samej nazwie w innej lokalizacji. Jeśli zostanie wyświetlony kod błędu `InvalidDeploymentLocation` , użyj innej nazwy lub tej samej lokalizacji co poprzednie wdrożenie dla tej nazwy.

## <a name="use-template-functions"></a>Korzystanie z funkcji szablonu

W przypadku wdrożeń na poziomie subskrypcji istnieją pewne ważne zagadnienia dotyczące korzystania z funkcji szablonu:

* Funkcja [przesourceing ()](template-functions-resource.md#resourcegroup) **nie** jest obsługiwana.
* Obsługiwane są funkcje [Reference ()](template-functions-resource.md#reference) i [list ()](template-functions-resource.md#list) .
* Nie należy używać [ResourceID ()](template-functions-resource.md#resourceid) w celu uzyskania identyfikatora zasobu dla zasobów wdrożonych na poziomie subskrypcji. Zamiast tego należy użyć funkcji [subscriptionResourceId ()](template-functions-resource.md#subscriptionresourceid) .

  Aby na przykład uzyskać identyfikator zasobu definicji zasad wdrożonej w ramach subskrypcji, użyj:

  ```json
  subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```

  Identyfikator zwróconego zasobu ma następujący format:

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="resource-groups"></a>Grupy zasobów

### <a name="create-resource-groups"></a>Tworzenie grup zasobów

Aby utworzyć grupę zasobów w szablonie ARM, zdefiniuj zasób [Microsoft. resources/resourceGroups](/azure/templates/microsoft.resources/allversions) z nazwą i lokalizacją grupy zasobów.

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
      "apiVersion": "2020-06-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Użyj [kopiowania elementu](copy-resources.md) z grupami zasobów, aby utworzyć więcej niż jedną grupę zasobów.

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
      "apiVersion": "2020-06-01",
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

Aby uzyskać informacje o iteracji zasobów, zobacz [wdrażanie więcej niż jednego wystąpienia zasobu w szablonach Azure Resource Manager](./copy-resources.md)i [Samouczek: Tworzenie wielu wystąpień zasobów przy użyciu szablonów Menedżer zasobów](./template-tutorial-create-multiple-instances.md).

### <a name="create-resource-group-and-resources"></a>Tworzenie grupy zasobów i zasobów

Aby utworzyć grupę zasobów i wdrożyć do niej zasoby, użyj szablonu zagnieżdżonego. Szablon zagnieżdżony definiuje zasoby, które mają zostać wdrożone w grupie zasobów. Ustaw szablon zagnieżdżony jako zależny od grupy zasobów, aby upewnić się, że grupa zasobów istnieje przed wdrożeniem zasobów. Można wdrożyć maksymalnie 800 grup zasobów.

Poniższy przykład tworzy grupę zasobów i wdraża konto magazynu w grupie zasobów.

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
      "apiVersion": "2020-06-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-06-01",
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

## <a name="azure-policy"></a>Azure Policy

### <a name="assign-policy-definition"></a>Przypisz definicję zasad

Poniższy przykład przypisuje istniejącą definicję zasad do subskrypcji. Jeśli definicja zasad przyjmuje parametry, podaj je jako obiekt. Jeśli definicja zasad nie przyjmuje parametrów, Użyj domyślnego pustego obiektu.

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

Aby wdrożyć ten szablon przy użyciu interfejsu wiersza polecenia platformy Azure, użyj polecenia:

```azurecli-interactive
# Built-in policy definition that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Aby wdrożyć ten szablon przy użyciu programu PowerShell, użyj polecenia:

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

### <a name="create-and-assign-policy-definitions"></a>Tworzenie i przypisywanie definicji zasad

Można [zdefiniować](../../governance/policy/concepts/definition-structure.md) i przypisać definicję zasad w tym samym szablonie.

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
        "policyDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
      }
    }
  ]
}
```

Aby utworzyć definicję zasad w ramach subskrypcji, a następnie przypisać ją do subskrypcji, użyj następującego polecenia CLI:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

Aby wdrożyć ten szablon przy użyciu programu PowerShell, użyj polecenia:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="azure-blueprints"></a>Azure Blueprints

### <a name="create-blueprint-definition"></a>Utwórz definicję planu

Definicję planu można [utworzyć](../../governance/blueprints/tutorials/create-from-sample.md) na podstawie szablonu.

:::code language="json" source="~/quickstart-templates/subscription-deployments/blueprints-new-blueprint/azuredeploy.json":::

Aby utworzyć definicję planu w ramach subskrypcji, należy użyć następującego polecenia CLI:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

Aby wdrożyć ten szablon przy użyciu programu PowerShell, użyj polecenia:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

## <a name="access-control"></a>Kontrola dostępu

Aby dowiedzieć się więcej o przypisywaniu ról, zobacz [Dodawanie przypisań ról platformy Azure przy użyciu szablonów Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).

Poniższy przykład tworzy grupę zasobów, stosuje do niej blokadę i przypisuje rolę do podmiotu zabezpieczeń.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-rg-lock-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z przykładem wdrażania ustawień obszaru roboczego dla Azure Security Center, zobacz [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Przykładowe szablony można znaleźć w witrynie [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments).
* Szablony można także wdrażać na poziomie [grupy zarządzania](deploy-to-management-group.md) i na [poziomie dzierżawy](deploy-to-tenant.md).
