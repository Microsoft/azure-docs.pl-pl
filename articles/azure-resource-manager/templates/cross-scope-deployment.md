---
title: Wdrażanie zasobów między zakresami
description: Pokazuje, w jaki sposób ma być przeznaczony więcej niż jeden zakres podczas wdrożenia. Zakresem może być dzierżawca, grupy zarządzania, subskrypcje i grupy zasobów.
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 6161401ac039551a814b595715f56df1ac62dd6c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87374558"
---
# <a name="deploy-azure-resources-across-scopes"></a>Wdrażanie zasobów platformy Azure między zakresami

Za pomocą szablonów Azure Resource Manager (szablony ARM) można wdrożyć do więcej niż jednego zakresu w jednym wdrożeniu. Dostępne zakresy to dzierżawa, grupy zarządzania, subskrypcje i grupy zasobów. Można na przykład wdrożyć zasoby w jednej grupie zasobów i w tym samym szablonie wdrożyć zasoby w innej grupie zasobów. Można też wdrożyć zasoby w grupie zarządzania, a także wdrożyć zasoby w grupie zasobów w ramach tej grupy zarządzania.

Użyj [szablonów zagnieżdżonych lub połączonych](linked-templates.md) , aby określić zakresy, które są inne niż zakres podstawowy dla operacji wdrożenia.

## <a name="available-scopes"></a>Dostępne zakresy

Zakres używany przez operację wdrażania określa, które inne zakresy są dostępne. Można je wdrożyć w [dzierżawie](deploy-to-tenant.md), [grupie zarządzania](deploy-to-management-group.md), [subskrypcji](deploy-to-subscription.md)lub [grupie zasobów](deploy-powershell.md). Na poziomie podstawowego wdrożenia nie można przejść do poziomów w hierarchii. Na przykład w przypadku wdrożenia w ramach subskrypcji nie można wykonać kroku do wdrożenia zasobów w grupie zarządzania. Można jednak wdrożyć w grupie zarządzania i krok po kroku w celu wdrożenia w ramach subskrypcji lub grupy zasobów.

Dla każdego zakresu użytkownik wdrażający szablon musi mieć uprawnienia wymagane do tworzenia zasobów.

## <a name="cross-resource-groups"></a>Grupy wielu zasobów

Aby określić grupę zasobów, która jest inna niż ta dla szablonu nadrzędnego, użyj [szablonu zagnieżdżone lub połączone](linked-templates.md). W obszarze Typ zasobu wdrożenia Określ wartości dla identyfikatora subskrypcji i grupy zasobów, na które ma zostać wdrożony szablon zagnieżdżony. Grupy zasobów mogą istnieć w różnych subskrypcjach.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

Jeśli nie określisz identyfikatora subskrypcji lub grupy zasobów, zostanie użyta subskrypcja i Grupa zasobów z szablonu nadrzędnego. Przed uruchomieniem wdrożenia muszą istnieć wszystkie grupy zasobów.

> [!NOTE]
> Można wdrożyć do **800 grup zasobów** w jednym wdrożeniu. Zwykle to ograniczenie oznacza, że można wdrożyć do jednej grupy zasobów określonej dla szablonu nadrzędnego oraz do 799 grup zasobów w zagnieżdżonych lub połączonych wdrożeniach. Jeśli jednak szablon nadrzędny zawiera tylko szablony zagnieżdżone lub połączone i nie wdraża żadnych zasobów, można dołączyć do 800 grup zasobów w ramach wdrożeń zagnieżdżonych lub połączonych.

Poniższy przykład służy do wdrażania dwóch kont magazynu. Pierwsze konto magazynu zostanie wdrożone w grupie zasobów określonej w operacji wdrażania. Drugie konto magazynu jest wdrażane w grupie zasobów określonej w `secondResourceGroup` `secondSubscriptionID` parametrach i:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Jeśli ustawisz `resourceGroup` nazwę grupy zasobów, która nie istnieje, wdrożenie nie powiedzie się.

Aby przetestować poprzedni szablon i zobaczyć wyniki, użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby wdrożyć dwa konta magazynu w dwóch grupach zasobów w ramach **tej samej subskrypcji**, użyj:

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

Aby wdrożyć dwa konta magazynu w **dwóch subskrypcjach**, użyj:

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

Aby wdrożyć dwa konta magazynu w dwóch grupach zasobów w ramach **tej samej subskrypcji**, użyj:

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

Aby wdrożyć dwa konta magazynu w **dwóch subskrypcjach**, użyj:

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

## <a name="cross-subscription-management-group-and-tenant"></a>Między subskrypcjami, grupami zarządzania i dzierżawcą

Podczas określania różnych zakresów dla subskrypcji, grupy zarządzania i wdrożeń na poziomie dzierżawy, należy użyć zagnieżdżonych wdrożeń, takich jak przykład dla grup zasobów. Właściwości, które są używane do określania zakresu, mogą się różnić. Te scenariusze zostały omówione w artykułach o poziomach wdrożeń. Aby uzyskać więcej informacji, zobacz:

* [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](deploy-to-subscription.md)
* [Tworzenie zasobów na poziomie grupy zarządzania](deploy-to-management-group.md)
* [Tworzenie zasobów na poziomie dzierżawy](deploy-to-tenant.md)

## <a name="how-functions-resolve-in-scopes"></a>Jak działają funkcje w zakresach

W przypadku wdrażania do więcej niż jednego zakresu funkcje [zasobów ()](template-functions-resource.md#resourcegroup) i [subskrypcji ()](template-functions-resource.md#subscription) są rozwiązywane inaczej w zależności od sposobu określania szablonu. Podczas łączenia z szablonem zewnętrznym funkcje zawsze rozwiązują zakres tego szablonu. Podczas zagnieżdżania szablonu w szablonie nadrzędnym Użyj `expressionEvaluationOptions` właściwości, aby określić, czy funkcje są rozpoznawane jako Grupa zasobów i subskrypcja dla szablonu nadrzędnego, czy dla szablonu zagnieżdżonego. Ustaw właściwość na `inner` , aby można było rozpoznać zakres dla szablonu zagnieżdżonego. Ustaw właściwość na `outer` , aby można było rozpoznać zakres szablonu nadrzędnego.

W poniższej tabeli przedstawiono, czy funkcje są rozpoznawane jako nadrzędne, czy osadzone grupy zasobów i subskrypcje.

| Typ szablonu | Zakres | Rozwiązanie |
| ------------- | ----- | ---------- |
| zagnieżdża        | zewnętrzny (domyślnie) | Nadrzędna grupa zasobów |
| zagnieżdża        | fabryk | Podgrupa zasobów |
| połączone        | Nie dotyczy   | Podgrupa zasobów |

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) pokazuje:

* zagnieżdżony szablon z domyślnym zakresem (zewnętrznym)
* zagnieżdżony szablon z zakresem wewnętrznym
* połączony szablon

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

Aby przetestować poprzedni szablon i zobaczyć wyniki, użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Dane wyjściowe z poprzedniego przykładu to:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Dane wyjściowe z poprzedniego przykładu to:

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---



## <a name="next-steps"></a>Następne kroki

* Aby zrozumieć, jak definiować parametry w szablonie, zobacz [Opis struktury i składni szablonów Azure Resource Manager](template-syntax.md).
* Aby uzyskać wskazówki dotyczące rozwiązywania typowych błędów wdrażania, zobacz [Rozwiązywanie typowych błędów wdrażania platformy Azure przy użyciu Azure Resource Manager](common-deployment-errors.md).
* Informacje o wdrażaniu szablonu wymagającego tokenu SAS można znaleźć w temacie [Deploy Private Template with SAS token](secure-template-with-sas-token.md).
