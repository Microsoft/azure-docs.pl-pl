---
title: Usuwanie grupy zasobów i zasobów
description: Opisuje sposób usuwania grup zasobów i zasobów. Opisano w nim, jak Azure Resource Manager zamówienia usunięcia zasobów podczas usuwania grupy zasobów. Opisano w nim kody odpowiedzi i sposób, w jaki Menedżer zasobów je obsłużyć, aby określić, czy usunięcie zakończyło się pomyślnie.
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom: seodec18
ms.openlocfilehash: 244d59ffc096b5e219e27fd376b07baecde3670e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587665"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure Resource Manager grupy zasobów i usuwania zasobów

W tym artykule przedstawiono sposób usuwania grup zasobów i zasobów. Opisano w nim, jak Azure Resource Manager zamówienia usunięcia zasobów podczas usuwania grupy zasobów.

## <a name="how-order-of-deletion-is-determined"></a>Sposób określania kolejności usuwania

Po usunięciu grupy zasobów Menedżer zasobów określa kolejność usuwania zasobów. Używa następującej kolejności:

1. Wszystkie zasoby podrzędne (zagnieżdżone) są usuwane.

2. Zasoby zarządzające innymi zasobami są usuwane dalej. Zasób może mieć `managedBy` ustawioną właściwość, aby wskazać, że zarządza innym zasobem. Gdy ta właściwość jest ustawiona, zasób zarządzający innym zasobem zostanie usunięty przed innymi zasobami.

3. Pozostałe zasoby zostaną usunięte po poprzednich dwóch kategoriach.

Po ustaleniu zamówienia Menedżer zasobów wystawia operację usuwania dla każdego zasobu. Czeka na zakończenie wszystkich zależności przed kontynuowaniem.

W przypadku operacji synchronicznych oczekiwane kody odpowiedzi są następujące:

* 200
* 204
* 404

W przypadku operacji asynchronicznych oczekiwana odpowiedź to 202. Menedżer zasobów śledzi nagłówka lokalizacji lub nagłówka operacji Azure-Async w celu określenia stanu asynchronicznej operacji usuwania.
  
### <a name="deletion-errors"></a>Błędy usuwania

Gdy operacja usuwania zwróci błąd, Menedżer zasobów ponawianie próby wywołania usuwania. Dla kodów stanu 5xx, 429 i 408 nastąpi ponowna próba. Domyślnie okres ponawiania prób wynosi 15 minut.

## <a name="after-deletion"></a>Po usunięciu

Menedżer zasobów wystawia wywołania GET dla każdego zasobu, który próbował usunąć. Odpowiedź na to wywołanie GET powinna być 404. Gdy Menedżer zasobów pobiera 404, uważa, że usunięcie zostało ukończone pomyślnie. Menedżer zasobów usuwa zasób z jego pamięci podręcznej.

Jeśli jednak wywołanie GET w zasobie zwróci wartość 200 lub 201, Menedżer zasobów ponownie utworzy zasób.

Jeśli operacja GET zwróci błąd, Menedżer zasobów ponawianie próby pobrania dla następującego kodu błędu:

* Mniej niż 100
* 408
* 429
* Większe niż 500

W przypadku innych kodów błędów Menedżer zasobów usunięcie zasobu nie powiodło się.

> [!IMPORTANT]
> Usuwanie grupy zasobów jest nieodwracalne.

## <a name="delete-resource-group"></a>Usuwanie grupy zasobów

Aby usunąć grupę zasobów, użyj jednej z poniższych metod.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [portalu](https://portal.azure.com)wybierz grupę zasobów, którą chcesz usunąć.

1. Wybierz pozycję **Usuń grupę zasobów**.

   ![Usuwanie grupy zasobów](./media/delete-resource-group/delete-group.png)

1. Aby potwierdzić usunięcie, wpisz nazwę grupy zasobów

---

## <a name="delete-resource"></a>Usuń zasób

Użyj jednej z poniższych metod, aby usunąć zasób.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [portalu](https://portal.azure.com)wybierz zasób, który chcesz usunąć.

1. Wybierz pozycję **Usuń**. Poniższy zrzut ekranu przedstawia opcje zarządzania dla maszyny wirtualnej.

   ![Usuń zasób](./media/delete-resource-group/delete-resource.png)

1. Po wyświetleniu monitu potwierdź usunięcie.

---

## <a name="required-access"></a>Wymagany dostęp

Aby można było usunąć grupę zasobów, musisz mieć dostęp do akcji usuwania dla zasobu **Microsoft. resources/subscriptions/resourceGroups** . Konieczne jest również usunięcie wszystkich zasobów w grupie zasobów.

Aby zapoznać się z listą operacji, zobacz operacje związane z [dostawcą zasobów platformy Azure](../../role-based-access-control/resource-provider-operations.md). Aby zapoznać się z listą wbudowanych ról, zobacz [role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md).

Jeśli masz wymagany dostęp, ale żądanie usunięcia nie powiedzie się, może to być spowodowane [blokadą](lock-resources.md) grupy zasobów.

## <a name="next-steps"></a>Następne kroki

* Aby zrozumieć koncepcje Menedżer zasobów, zobacz [Azure Resource Manager omówienie](overview.md).
* Polecenia usuwania można znaleźć w temacie [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), interfejs [wiersza polecenia platformy Azure](/cli/azure/group#az-group-delete)i [interfejs API REST](/rest/api/resources/resourcegroups/delete).
