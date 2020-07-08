---
title: Wdrażanie zasobów między subskrypcjami & grupy zasobów
description: Pokazuje, w jaki sposób można określić więcej niż jedną subskrypcję platformy Azure i grupę zasobów podczas wdrażania.
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 34de1d9df53d61d849ffbb81a57b468020bc3b65
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057385"
---
# <a name="deploy-azure-resources-across-subscriptions-or-resource-groups"></a>Wdrażanie zasobów platformy Azure w ramach subskrypcji lub grup zasobów

Menedżer zasobów umożliwia wdrożenie do więcej niż jednej grupy zasobów w jednym wdrożeniu. Użyj szablonów zagnieżdżonych, aby określić grupy zasobów, które różnią się od grupy zasobów w operacji wdrażania. Grupy zasobów mogą istnieć w różnych subskrypcjach.

> [!NOTE]
> Można wdrożyć do **800 grup zasobów** w jednym wdrożeniu. Zwykle to ograniczenie oznacza, że można wdrożyć do jednej grupy zasobów określonej dla szablonu nadrzędnego oraz do 799 grup zasobów w zagnieżdżonych lub połączonych wdrożeniach. Jeśli jednak szablon nadrzędny zawiera tylko szablony zagnieżdżone lub połączone i nie wdraża żadnych zasobów, można dołączyć do 800 grup zasobów w ramach wdrożeń zagnieżdżonych lub połączonych.

## <a name="specify-subscription-and-resource-group"></a>Określ subskrypcję i grupę zasobów

Aby określić grupę zasobów, która jest inna niż ta dla szablonu nadrzędnego, użyj [szablonu zagnieżdżone lub połączone](linked-templates.md). W obszarze Typ zasobu wdrożenia Określ wartości dla identyfikatora subskrypcji i grupy zasobów, na które ma zostać wdrożony szablon zagnieżdżony.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

Jeśli nie określisz identyfikatora subskrypcji lub grupy zasobów, zostanie użyta subskrypcja i Grupa zasobów z szablonu nadrzędnego. Przed uruchomieniem wdrożenia muszą istnieć wszystkie grupy zasobów.

Konto, które wdraża szablon, musi mieć uprawnienia do wdrożenia określonego identyfikatora subskrypcji. Jeśli określona subskrypcja istnieje w innej dzierżawie Azure Active Directory, należy [dodać użytkowników-Gości z innego katalogu](../../active-directory/b2b/what-is-b2b.md).

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

## <a name="use-functions"></a>Korzystanie z funkcji

Funkcje [resourceing ()](template-functions-resource.md#resourcegroup) i [Subscription ()](template-functions-resource.md#subscription) rozwiązują się inaczej w zależności od sposobu określania szablonu. Podczas łączenia z szablonem zewnętrznym funkcje zawsze rozwiązują zakres tego szablonu. Podczas zagnieżdżania szablonu w szablonie nadrzędnym Użyj `expressionEvaluationOptions` właściwości, aby określić, czy funkcje są rozpoznawane jako Grupa zasobów i subskrypcja dla szablonu nadrzędnego, czy dla szablonu zagnieżdżonego. Ustaw właściwość na `inner` , aby można było rozpoznać zakres dla szablonu zagnieżdżonego. Ustaw właściwość na `outer` , aby można było rozpoznać zakres szablonu nadrzędnego.

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
