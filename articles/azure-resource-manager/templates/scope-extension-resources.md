---
title: Zakres dla typów zasobów rozszerzenia
description: Opisuje sposób użycia właściwości Scope podczas wdrażania typów zasobów rozszerzenia.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: bd468d31454c38bd314269243702d7df4f279a5e
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681585"
---
# <a name="setting-scope-for-extension-resources-in-arm-templates"></a>Ustawianie zakresu dla zasobów rozszerzenia w szablonach ARM

Zasób rozszerzenia jest zasobem, który modyfikuje inny zasób. Na przykład możesz przypisać rolę do zasobu, aby ograniczyć dostęp. Przypisanie roli jest typem zasobu rozszerzenia.

Aby uzyskać pełną listę typów zasobów rozszerzeń, zobacz [typy zasobów, które rozszerzają możliwości innych zasobów](../management/extension-resource-types.md).

W tym artykule pokazano, jak ustawić zakres dla typu zasobu rozszerzenia w przypadku wdrożenia z szablonem Azure Resource Manager (szablon ARM). Opisuje właściwość Scope, która jest dostępna dla zasobów rozszerzeń w przypadku zastosowania do zasobu.

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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

* Aby zrozumieć, jak definiować parametry w szablonie, zobacz [Opis struktury i składni szablonów Azure Resource Manager](template-syntax.md).
* Aby uzyskać wskazówki dotyczące rozwiązywania typowych błędów wdrażania, zobacz [Rozwiązywanie typowych błędów wdrażania platformy Azure przy użyciu Azure Resource Manager](common-deployment-errors.md).
* Informacje o wdrażaniu szablonu wymagającego tokenu SAS można znaleźć w temacie [Deploy Private Template with SAS token](secure-template-with-sas-token.md).
