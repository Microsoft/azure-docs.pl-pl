---
title: Funkcje szablonu w wdrożeniach objętych zakresem
description: Opisuje, jak funkcje szablonu są rozwiązywane w wdrożeniach o określonym zakresie. Zakresem może być dzierżawca, grupy zarządzania, subskrypcje i grupy zasobów.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: f128448380612bc9b8d9114226e8a3036feeead8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99492097"
---
# <a name="arm-template-functions-in-deployment-scopes"></a>Funkcje szablonu ARM w zakresach wdrożenia

Za pomocą szablonów Azure Resource Manager (szablony ARM) można wdrażać w grupach zasobów, subskrypcjach, grupach zarządzania lub dzierżawach. Ogólnie rzecz biorąc, [funkcje szablonu ARM](template-functions.md) działają tak samo dla wszystkich zakresów. W tym artykule opisano różnice występujące w przypadku niektórych funkcji w zależności od zakresu.

## <a name="supported-functions"></a>Obsługiwane funkcje

W przypadku wdrażania w różnych zakresach istnieją pewne ważne zagadnienia:

* Funkcja Grouping [()](template-functions-resource.md#resourcegroup) jest **obsługiwana** w przypadku wdrożeń grup zasobów.
* Funkcja [Subscription ()](template-functions-resource.md#subscription) jest **obsługiwana** w przypadku wdrożeń grup zasobów i subskrypcji.
* Funkcje [Reference ()](template-functions-resource.md#reference) i [list ()](template-functions-resource.md#list) są **obsługiwane** dla wszystkich zakresów.
* Użyj [ResourceID ()](template-functions-resource.md#resourceid) , aby uzyskać identyfikator zasobu wdrożonego w grupie zasobów.

  ```json
  "subnet": {
    "id": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  }
  ```

* Użyj funkcji [subscriptionResourceId ()](template-functions-resource.md#subscriptionresourceid) , aby uzyskać identyfikator dla zasobu wdrożonego w ramach subskrypcji.

  Aby na przykład uzyskać identyfikator zasobu definicji zasad wdrożonej w ramach subskrypcji, użyj:

  ```json
  "roleDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  ```

* Użyj funkcji [extensionResourceId ()](template-functions-resource.md#extensionresourceid) dla zasobów, które są zaimplementowane jako rozszerzenia grupy zarządzania. Niestandardowe definicje zasad wdrożone w grupie zarządzania to rozszerzenia grupy zarządzania.

  Aby uzyskać identyfikator zasobu dla niestandardowej definicji zasad na poziomie grupy zarządzania, użyj:

  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

* Użyj funkcji [tenantResourceId ()](template-functions-resource.md#tenantresourceid) , aby uzyskać identyfikator dla zasobu wdrożonego w dzierżawie. Wbudowane definicje zasad to zasoby na poziomie dzierżawy. Podczas przypisywania wbudowanych zasad na poziomie grupy zarządzania Użyj funkcji tenantResourceId.

  Aby uzyskać identyfikator zasobu dla wbudowanej definicji zasad, użyj:

  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="function-resolution-in-scopes"></a>Rozpoznawanie funkcji w zakresach

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

* Aby zrozumieć, jak definiować parametry w szablonie, zobacz [Opis struktury i składni szablonów ARM](template-syntax.md).
* Aby uzyskać wskazówki dotyczące rozwiązywania typowych błędów wdrażania, zobacz [Rozwiązywanie typowych błędów wdrażania platformy Azure przy użyciu Azure Resource Manager](common-deployment-errors.md).
* Aby uzyskać informacje na temat wdrażania szablonu wymagającego tokenu SAS, zobacz [wdrażanie prywatnego szablonu usługi ARM z tokenem SAS](secure-template-with-sas-token.md).
