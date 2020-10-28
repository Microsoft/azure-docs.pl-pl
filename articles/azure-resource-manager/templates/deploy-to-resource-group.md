---
title: Wdrażanie zasobów w grupach zasobów
description: Opisuje sposób wdrażania zasobów w szablonie Azure Resource Manager. Pokazuje, jak należy określić więcej niż jedną grupę zasobów.
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: fd211641d7fcc02a1db154053597497583b21ae5
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681545"
---
# <a name="resource-group-deployments-with-arm-templates"></a>Wdrożenia grup zasobów przy użyciu szablonów ARM

W tym artykule opisano sposób określania zakresu wdrożenia do grupy zasobów. Do wdrożenia służy szablon Azure Resource Manager (szablon ARM). W tym artykule pokazano również, jak rozszerzyć zakres poza grupę zasobów w operacji wdrażania.

## <a name="supported-resources"></a>Obsługiwane zasoby

Większość zasobów można wdrożyć w grupie zasobów. Listę dostępnych zasobów można znaleźć w temacie [odwołanie do szablonu usługi ARM](/azure/templates).

## <a name="schema"></a>Schemat

W przypadku szablonów należy użyć następującego schematu:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
}
```

W przypadku plików parametrów należy użyć:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Polecenia wdrażania

Aby wdrożyć w grupie zasobów, Użyj poleceń wdrażania grupy zasobów.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

W przypadku interfejsu wiersza polecenia platformy Azure Użyj polecenia [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create). Poniższy przykład wdraża szablon w celu utworzenia grupy zasobów:

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

W przypadku polecenia wdrażania programu PowerShell Użyj polecenie [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Poniższy przykład wdraża szablon w celu utworzenia grupy zasobów:

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS `
```

---

Aby uzyskać bardziej szczegółowe informacje na temat poleceń wdrażania i opcji wdrażania szablonów ARM, zobacz:

* [Wdrażanie zasobów za pomocą szablonów ARM i Azure Portal](deploy-portal.md)
* [Wdrażanie zasobów za pomocą szablonów ARM i interfejsu wiersza polecenia platformy Azure](deploy-cli.md)
* [Wdrażanie zasobów za pomocą szablonów ARM i Azure PowerShell](deploy-powershell.md)
* [Wdrażanie zasobów za pomocą szablonów ARM i interfejsu API REST Azure Resource Manager](deploy-rest.md)
* [Użyj przycisku wdrożenia, aby wdrożyć szablony z repozytorium GitHub](deploy-to-azure-button.md)
* [Wdrażanie szablonów usługi ARM na podstawie Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>Zakresy wdrożenia

Podczas wdrażania w grupie zasobów można wdrożyć zasoby w:

* docelowa Grupa zasobów z operacji
* inne grupy zasobów w ramach tej samej subskrypcji lub innych subskrypcji
* [zasoby rozszerzeń](scope-extension-resources.md) można stosować do zasobów

Użytkownik wdrażający szablon musi mieć dostęp do określonego zakresu.

W tej sekcji pokazano, jak określić różne zakresy. Można połączyć te różne zakresy w jednym szablonie.

### <a name="scope-to-target-resource-group"></a>Zakres docelowej grupy zasobów

Aby wdrożyć zasoby do zasobu docelowego, należy dodać te zasoby do sekcji zasoby szablonu.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-rg.json" highlight="5":::

### <a name="scope-to-resource-group-in-same-subscription"></a>Zakres do grupy zasobów w tej samej subskrypcji

Aby wdrożyć zasoby w innej grupie zasobów w ramach tej samej subskrypcji, Dodaj wdrożenie zagnieżdżone i Uwzględnij `resourceGroup` Właściwość. Jeśli nie określisz identyfikatora subskrypcji lub grupy zasobów, zostanie użyta subskrypcja i Grupa zasobów z szablonu nadrzędnego. Przed uruchomieniem wdrożenia muszą istnieć wszystkie grupy zasobów.

W poniższym przykładzie zagnieżdżone wdrożenie jest przeznaczone dla grupy zasobów o nazwie `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/same-sub-to-resource-group.json" highlight="9,13":::

### <a name="scope-to-resource-group-in-different-subscription"></a>Zakres do grupy zasobów w innej subskrypcji

Aby wdrożyć zasoby w grupie zasobów w innej subskrypcji, należy dodać wdrożenie zagnieżdżone i uwzględnić `subscriptionId` `resourceGroup` właściwości i. W poniższym przykładzie zagnieżdżone wdrożenie jest przeznaczone dla grupy zasobów o nazwie `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/different-sub-to-resource-group.json" highlight="9,10,14":::

## <a name="cross-resource-groups"></a>Grupy wielu zasobów

W pojedynczym szablonie ARM można wdrożyć wiele grup zasobów. Aby określić grupę zasobów, która jest inna niż ta dla szablonu nadrzędnego, użyj [szablonu zagnieżdżone lub połączone](linked-templates.md). W obszarze Typ zasobu wdrożenia Określ wartości dla identyfikatora subskrypcji i grupy zasobów, na które ma zostać wdrożony szablon zagnieżdżony. Grupy zasobów mogą istnieć w różnych subskrypcjach.

> [!NOTE]
> Można wdrożyć do **800 grup zasobów** w jednym wdrożeniu. Zwykle to ograniczenie oznacza, że można wdrożyć do jednej grupy zasobów określonej dla szablonu nadrzędnego oraz do 799 grup zasobów w zagnieżdżonych lub połączonych wdrożeniach. Jeśli jednak szablon nadrzędny zawiera tylko szablony zagnieżdżone lub połączone i nie wdraża żadnych zasobów, można dołączyć do 800 grup zasobów w ramach wdrożeń zagnieżdżonych lub połączonych.

Poniższy przykład służy do wdrażania dwóch kont magazynu. Pierwsze konto magazynu zostanie wdrożone w grupie zasobów określonej w operacji wdrażania. Drugie konto magazynu jest wdrażane w grupie zasobów określonej w `secondResourceGroup` `secondSubscriptionID` parametrach i:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Jeśli ustawisz `resourceGroup` nazwę grupy zasobów, która nie istnieje, wdrożenie nie powiedzie się.

Aby przetestować poprzedni szablon i zobaczyć wyniki, użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aby wdrożyć dwa konta magazynu w dwóch grupach zasobów w ramach **tej samej subskrypcji** , użyj:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Aby wdrożyć dwa konta magazynu w **dwóch subskrypcjach** , użyj:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wdrożyć dwa konta magazynu w dwóch grupach zasobów w ramach **tej samej subskrypcji** , użyj:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Aby wdrożyć dwa konta magazynu w **dwóch subskrypcjach** , użyj:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z przykładem wdrażania ustawień obszaru roboczego dla Azure Security Center, zobacz [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
