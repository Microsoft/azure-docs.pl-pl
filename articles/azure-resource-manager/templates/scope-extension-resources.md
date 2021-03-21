---
title: Zakres dla typów zasobów rozszerzenia
description: Opisuje sposób użycia właściwości Scope podczas wdrażania typów zasobów rozszerzenia.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: ce08ca951e24c1c0a5450052cf814a68888837c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99492165"
---
# <a name="setting-scope-for-extension-resources-in-arm-templates"></a>Ustawianie zakresu dla zasobów rozszerzenia w szablonach ARM

Zasób rozszerzenia jest zasobem, który modyfikuje inny zasób. Na przykład można przypisać rolę do zasobu. Przypisanie roli jest typem zasobu rozszerzenia.

Aby uzyskać pełną listę typów zasobów rozszerzeń, zobacz [typy zasobów, które rozszerzają możliwości innych zasobów](../management/extension-resource-types.md).

W tym artykule pokazano, jak ustawić zakres dla typu zasobu rozszerzenia w przypadku wdrożenia z szablonem Azure Resource Manager (szablon ARM). Opisuje właściwość Scope, która jest dostępna dla zasobów rozszerzeń w przypadku zastosowania do zasobu.

> [!NOTE]
> Właściwość Scope jest dostępna tylko dla typów zasobów rozszerzenia. Aby określić inny zakres dla typu zasobu, który nie jest typem rozszerzenia, należy zastosować wdrożenie zagnieżdżone lub połączone. Aby uzyskać więcej informacji, zobacz [wdrożenia grup zasobów](deploy-to-resource-group.md), [wdrożenia subskrypcji](deploy-to-subscription.md), [wdrożenia grup zarządzania](deploy-to-management-group.md)i [wdrożenia dzierżawców](deploy-to-tenant.md).

## <a name="apply-at-deployment-scope"></a>Zastosuj w zakresie wdrożenia

Aby zastosować typ zasobu rozszerzenia w docelowym zakresie wdrożenia, należy dodać zasób do szablonu, tak jak w przypadku dowolnego typu zasobu. Dostępne zakresy to [Grupa zasobów](deploy-to-resource-group.md), [subskrypcja](deploy-to-subscription.md), [Grupa zarządzania](deploy-to-management-group.md)i [dzierżawca](deploy-to-tenant.md). Zakres wdrożenia musi obsługiwać typ zasobu.

Poniższy szablon wdraża blokadę.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/locktargetscope.json":::

Po wdrożeniu w grupie zasobów zablokuje grupę zasobów.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
 New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

---

W następnym przykładzie przypisze rolę.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/roletargetscope.json":::

Po wdrożeniu w ramach subskrypcji przypisze ona rolę do subskrypcji.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

---

## <a name="apply-to-resource"></a>Zastosuj do zasobu

Aby zastosować zasób rozszerzenia do zasobu, użyj `scope` właściwości. Ustaw właściwość Scope na nazwę zasobu, do którego chcesz dodać rozszerzenie. Właściwość Scope jest właściwością główną dla typu zasobu rozszerzenia.

Poniższy przykład tworzy konto magazynu i stosuje do niego rolę.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/storageandrole.json" highlight="56":::

## <a name="next-steps"></a>Następne kroki

* Aby zrozumieć, jak definiować parametry w szablonie, zobacz [Opis struktury i składni szablonów ARM](template-syntax.md).
* Aby uzyskać wskazówki dotyczące rozwiązywania typowych błędów wdrażania, zobacz [Rozwiązywanie typowych błędów wdrażania platformy Azure przy użyciu Azure Resource Manager](common-deployment-errors.md).
* Aby uzyskać informacje na temat wdrażania szablonu wymagającego tokenu SAS, zobacz [wdrażanie prywatnego szablonu usługi ARM z tokenem SAS](secure-template-with-sas-token.md).
