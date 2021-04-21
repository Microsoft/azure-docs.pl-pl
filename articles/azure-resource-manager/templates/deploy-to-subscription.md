---
title: Wdrażanie zasobów w subskrypcji
description: Opisuje sposób tworzenia grupy zasobów w szablonie Azure Resource Manager szablonu. Przedstawiono w nim również sposób wdrażania zasobów w zakresie subskrypcji platformy Azure.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 3598fe290fd993cbbc662ba9d3a3c5ba8c207bc0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781923"
---
# <a name="subscription-deployments-with-arm-templates"></a>Wdrożenia subskrypcji z szablonami usługi ARM

Aby uprościć zarządzanie zasobami, możesz użyć szablonu usługi Azure Resource Manager (arm) do wdrażania zasobów na poziomie subskrypcji platformy Azure. Możesz na przykład [](../../governance/policy/overview.md) wdrożyć zasady i kontrolę dostępu na podstawie ról [(RBAC)](../../role-based-access-control/overview.md) platformy Azure w swojej subskrypcji, co będzie ich dotyczyć w ramach twojej subskrypcji. Możesz również tworzyć grupy zasobów w ramach subskrypcji i wdrażać zasoby w grupach zasobów w ramach subskrypcji.

> [!NOTE]
> W ramach wdrożenia na poziomie subskrypcji można wdrożyć 800 różnych grup zasobów.

Aby wdrożyć szablony na poziomie subskrypcji, użyj interfejsu wiersza polecenia platformy Azure, programu PowerShell, interfejsu API REST lub portalu.

## <a name="supported-resources"></a>Obsługiwane zasoby

Nie wszystkie typy zasobów można wdrożyć na poziomie subskrypcji. W tej sekcji wymieniono obsługiwane typy zasobów.

Na Azure Blueprints użyj:

* [Artefakty](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [Plany](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versions (Blueprints)](/azure/templates/microsoft.blueprint/blueprints/versions)

W przypadku zasad platformy Azure użyj:

* [policyAssignments (przypisania zasad)](/azure/templates/microsoft.authorization/policyassignments)
* [Policydefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [korygowanie](/azure/templates/microsoft.policyinsights/remediations)

W przypadku kontroli dostępu opartej na rolach (RBAC) na platformie Azure użyj:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

W przypadku zagnieżdżonych szablonów wdrażanych w grupach zasobów użyj:

* [Wdrożeń](/azure/templates/microsoft.resources/deployments)

Do tworzenia nowych grup zasobów użyj:

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

Aby zarządzać subskrypcją, użyj:

* [Konfiguracje usługi Advisor](/azure/templates/microsoft.advisor/configurations)
* [Budżetów](/azure/templates/microsoft.consumption/budgets)
* [Analiza zmian profilu](/azure/templates/microsoft.changeanalysis/profile)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [Tagi](/azure/templates/microsoft.resources/tags)

Inne obsługiwane typy to:

* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

## <a name="schema"></a>Schemat

Schemat wdrożeń na poziomie subskrypcji różni się od schematu wdrożeń grup zasobów.

W przypadku szablonów użyj:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
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

Aby wdrożyć w subskrypcji, użyj poleceń wdrażania na poziomie subskrypcji.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

W przypadku interfejsu wiersza polecenia platformy Azure użyj [polecenia az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create). Poniższy przykład wdraża szablon w celu utworzenia grupy zasobów:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

W przypadku polecenia wdrażania programu PowerShell użyj [polecenia New-AzDeployment](/powershell/module/az.resources/new-azdeployment) lub jego aliasu `New-AzSubscriptionDeployment` . Poniższy przykład wdraża szablon w celu utworzenia grupy zasobów:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

---

Aby uzyskać bardziej szczegółowe informacje na temat poleceń wdrażania i opcji wdrażania szablonów usługi ARM, zobacz:

* [Wdrażanie zasobów przy użyciu szablonów usługi ARM i Azure Portal](deploy-portal.md)
* [Wdrażanie zasobów przy użyciu szablonów usługi ARM i interfejsu wiersza polecenia platformy Azure](deploy-cli.md)
* [Wdrażanie zasobów przy użyciu szablonów usługi ARM i Azure PowerShell](deploy-powershell.md)
* [Wdrażanie zasobów przy użyciu szablonów usługi ARM i Azure Resource Manager API REST](deploy-rest.md)
* [Wdrażanie szablonów z repozytorium GitHub przy użyciu przycisku wdrażania](deploy-to-azure-button.md)
* [Wdrażanie szablonów usługi ARM z Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Lokalizacja i nazwa wdrożenia

W przypadku wdrożeń na poziomie subskrypcji należy podać lokalizację wdrożenia. Lokalizacja wdrożenia jest oddzielona od lokalizacji wdrażanych zasobów. Lokalizacja wdrożenia określa miejsce przechowywania danych wdrożenia. [Wdrożenia grupy](deploy-to-management-group.md) zarządzania [i](deploy-to-tenant.md) dzierżawy również wymagają lokalizacji. W [przypadku wdrożeń](deploy-to-resource-group.md) grup zasobów lokalizacja grupy zasobów jest używana do przechowywania danych wdrożenia.

Możesz podać nazwę wdrożenia lub użyć domyślnej nazwy wdrożenia. Domyślna nazwa to nazwa pliku szablonu. Na przykład wdrożenie szablonu o nazwie _azuredeploy.jsna powoduje_ utworzenie domyślnej nazwy wdrożenia **azuredeploy**.

Dla każdej nazwy wdrożenia lokalizacja jest niezmienna. Nie można utworzyć wdrożenia w jednej lokalizacji, jeśli istnieje wdrożenie o tej samej nazwie w innej lokalizacji. Jeśli na przykład utworzysz wdrożenie subskrypcji o nazwie **deployment1** w lokalizacji **centralus,** nie będzie można później utworzyć kolejnego wdrożenia o nazwie **deployment1,** ale lokalizacji **westus**. Jeśli zostanie wyświetlany kod błędu , użyj innej nazwy lub tej samej lokalizacji co poprzednie `InvalidDeploymentLocation` wdrożenie dla tej nazwy.

## <a name="deployment-scopes"></a>Zakresy wdrożenia

Podczas wdrażania w subskrypcji można wdrażać zasoby w:

* subskrypcja docelowa z operacji
* dowolna subskrypcja w dzierżawie
* grupy zasobów w ramach subskrypcji lub innych subskrypcji
* dzierżawa subskrypcji

Zakres [zasobu rozszerzenia](scope-extension-resources.md) może być ograniczony do obiektu docelowego, który jest inny niż cel wdrożenia.

Użytkownik wdrażający szablon musi mieć dostęp do określonego zakresu.

W tej sekcji przedstawiono sposób określania różnych zakresów. Te różne zakresy można połączyć w jednym szablonie.

### <a name="scope-to-target-subscription"></a>Zakres do subskrypcji docelowej

Aby wdrożyć zasoby w subskrypcji docelowej, dodaj te zasoby do sekcji zasobów szablonu.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-sub.json" highlight="5":::

Przykłady wdrażania w subskrypcji można znaleźć w tematach Create resource groups (Tworzenie [grup zasobów)](#create-resource-groups) [i Assign policy definition (Przypisywanie definicji zasad).](#assign-policy-definition)

### <a name="scope-to-other-subscription"></a>Zakres do innej subskrypcji

Aby wdrożyć zasoby w subskrypcji innej niż subskrypcja z operacji, dodaj wdrożenie zagnieżdżone. Ustaw właściwość `subscriptionId` na identyfikator subskrypcji, w której chcesz wdrożyć. Ustaw właściwość `location` dla wdrożenia zagnieżdżonych.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-sub.json" highlight="9,10,14":::

### <a name="scope-to-resource-group"></a>Zakres do grupy zasobów

Aby wdrożyć zasoby w grupie zasobów w ramach subskrypcji, dodaj wdrożenie zagnieżdżone i dołącz `resourceGroup` właściwość . W poniższym przykładzie wdrożenie zagnieżdżone dotyczy grupy zasobów o nazwie `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-resource-group.json" highlight="9,13":::

Aby uzyskać przykład wdrażania w grupie zasobów, zobacz Create resource group and resources (Tworzenie [grupy zasobów i zasobów).](#create-resource-group-and-resources)

### <a name="scope-to-tenant"></a>Zakres do dzierżawy

Aby utworzyć zasoby w dzierżawie, ustaw wartość `scope` `/` . Użytkownik wdrażający szablon musi mieć wymagany dostęp do wdrożenia [w dzierżawie](deploy-to-tenant.md#required-access).

Aby użyć wdrożenia zagnieżdżonych, ustaw `scope` i `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-to-tenant.json" highlight="9,10,14":::

Można też ustawić zakres na dla `/` niektórych typów zasobów, takich jak grupy zarządzania.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-create-mg.json" highlight="12,15":::

Aby uzyskać więcej informacji, zobacz [Grupa zarządzania](deploy-to-management-group.md#management-group).

## <a name="resource-groups"></a>Grupy zasobów

### <a name="create-resource-groups"></a>Tworzenie grup zasobów

Aby utworzyć grupę zasobów w szablonie usługi ARM, zdefiniuj [zasób Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) z nazwą i lokalizacją grupy zasobów.

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
      "apiVersion": "2020-10-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Użyj elementu [copy z](copy-resources.md) grupami zasobów, aby utworzyć więcej niż jedną grupę zasobów.

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
      "apiVersion": "2020-10-01",
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

Aby uzyskać informacje o iteracji zasobów, zobacz [Iteracja zasobów w szablonach usługi ARM](./copy-resources.md)i [Samouczek: tworzenie wielu](./template-tutorial-create-multiple-instances.md)wystąpień zasobów za pomocą szablonów usługi ARM.

### <a name="create-resource-group-and-resources"></a>Tworzenie grupy zasobów i zasobów

Aby utworzyć grupę zasobów i wdrożyć w tej grupie zasoby, użyj zagnieżdżonych szablonów. Zagnieżdżony szablon definiuje zasoby do wdrożenia w grupie zasobów. Ustaw zagnieżdżony szablon jako zależny od grupy zasobów, aby upewnić się, że grupa zasobów istnieje przed wdrożeniem zasobów. Można wdrożyć maksymalnie 800 grup zasobów.

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
      "apiVersion": "2020-10-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
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

### <a name="assign-policy-definition"></a>Przypisywanie definicji zasad

Poniższy przykład przypisuje istniejącą definicję zasad do subskrypcji. Jeśli definicja zasad przyjmuje parametry, podaj je jako obiekt. Jeśli definicja zasad nie ma parametrów, użyj domyślnego pustego obiektu.

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

Aby wdrożyć ten szablon za pomocą interfejsu wiersza polecenia platformy Azure, użyj:

```azurecli-interactive
# Built-in policy definition that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Aby wdrożyć ten szablon przy użyciu programu PowerShell, użyj:

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

Definicję [zasad](../../governance/policy/concepts/definition-structure.md) można zdefiniować i przypisać w tym samym szablonie.

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

Aby utworzyć definicję zasad w subskrypcji i przypisać ją do subskrypcji, użyj następującego polecenia interfejsu wiersza polecenia:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

Aby wdrożyć ten szablon przy użyciu programu PowerShell, użyj:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="azure-blueprints"></a>Azure Blueprints

### <a name="create-blueprint-definition"></a>Tworzenie definicji strategii

Definicję [](../../governance/blueprints/tutorials/create-from-sample.md) strategii można utworzyć na podstawie szablonu.

:::code language="json" source="~/quickstart-templates/subscription-deployments/blueprints-new-blueprint/azuredeploy.json":::

Aby utworzyć definicję strategii w ramach subskrypcji, użyj następującego polecenia interfejsu wiersza polecenia:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

Aby wdrożyć ten szablon przy użyciu programu PowerShell, użyj:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

## <a name="access-control"></a>Kontrola dostępu

Aby dowiedzieć się więcej na temat przypisywania ról, zobacz [Dodawanie przypisań ról platformy Azure przy użyciu Azure Resource Manager szablonów.](../../role-based-access-control/role-assignments-template.md)

Poniższy przykład tworzy grupę zasobów, stosuje do niego blokadę i przypisuje rolę do podmiotu zabezpieczeń.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-rg-lock-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać przykład wdrażania ustawień obszaru roboczego dla Azure Security Center, zobacz [deployASCwithWorkspaceSettings.jsna stronie](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Przykładowe szablony można znaleźć w [witrynie GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments).
* Szablony można również wdrażać na [poziomie grupy zarządzania](deploy-to-management-group.md) i [dzierżawy.](deploy-to-tenant.md)
