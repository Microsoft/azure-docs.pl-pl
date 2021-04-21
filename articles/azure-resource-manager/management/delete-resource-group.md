---
title: Usuwanie grupy zasobów i zasobów
description: Opisuje sposób usuwania grup zasobów i zasobów. Opisano w nim Azure Resource Manager, jak można usunąć zasoby podczas usuwania grupy zasobów. Opisano w nim kody odpowiedzi i Resource Manager je, aby określić, czy usunięcie zakończyło się pomyślnie.
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom: seodec18
ms.openlocfilehash: 3c062c2f775e145347129f24b201748ee517daf4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768673"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure Resource Manager zasobów i usuwanie zasobów

W tym artykule pokazano, jak usunąć grupy zasobów i zasoby. W tym artykule opisano Azure Resource Manager żądania usunięcia zasobów podczas usuwania grupy zasobów.

## <a name="how-order-of-deletion-is-determined"></a>Jak jest określana kolejność usuwania

Po usunięciu grupy zasobów Resource Manager kolejność usuwania zasobów. Używa następującej kolejności:

1. Wszystkie zasoby podrzędne (zagnieżdżone) zostaną usunięte.

2. Zasoby, które zarządzają innymi zasobami, zostaną następnie usunięte. Zasób może mieć `managedBy` ustawioną właściwość , aby wskazać, że zarządza nim inny zasób. Gdy ta właściwość jest ustawiona, zasób, który zarządza innym zasobem, jest usuwany przed innymi zasobami.

3. Pozostałe zasoby zostaną usunięte po dwóch poprzednich kategoriach.

Po ujednaniu zamówienia Resource Manager operację DELETE dla każdego zasobu. Przed przystąpieniem czeka na zakończenie zależności.

W przypadku operacji synchronicznych oczekiwane kody pomyślnej odpowiedzi to:

* 200
* 204
* 404

W przypadku operacji asynchronicznych oczekiwana pomyślna odpowiedź to 202. Resource Manager śledzi nagłówek lokalizacji lub nagłówek operacji azure-async, aby określić stan asynchronicznej operacji usuwania.
  
### <a name="deletion-errors"></a>Błędy usuwania

Gdy operacja usuwania zwraca błąd, Resource Manager ponowne próby wywołania DELETE. Ponowne próby mają miejsce w przypadku kodów stanu 5xx, 429 i 408. Domyślnie okres ponawiania próby wynosi 15 minut.

## <a name="after-deletion"></a>Po usunięciu

Resource Manager wywołaniu GET dla każdego zasobu, który próbował usunąć. Oczekiwana odpowiedź tego wywołania GET to 404. Gdy Resource Manager 404, uznaje usunięcie za zakończone pomyślnie. Resource Manager usuwa zasób z pamięci podręcznej.

Jeśli jednak wywołanie GET zasobu zwraca wartość 200 lub 201, Resource Manager ponownie zasób.

Jeśli operacja GET zwróci błąd, Resource Manager wróci do metody GET, aby uzyskać następujący kod błędu:

* Mniej niż 100
* 408
* 429
* Więcej niż 500

W przypadku innych kodów Resource Manager nie można usunąć zasobu.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne.

## <a name="delete-resource-group"></a>Usuwanie grupy zasobów

Aby usunąć grupę zasobów, użyj jednej z następujących metod.

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

## <a name="delete-resource"></a>Usuwanie zasobu

Aby usunąć zasób, użyj jednej z następujących metod.

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

   ![Usuwanie zasobu](./media/delete-resource-group/delete-resource.png)

1. Po wyświetleniu monitu potwierdź usunięcie.

---

## <a name="required-access"></a>Wymagany dostęp

Aby usunąć grupę zasobów, musisz mieć dostęp do akcji usuwania dla zasobu **Microsoft.Resources/subscriptions/resourceGroups.** Musisz również usunąć wszystkie zasoby w grupie zasobów.

Aby uzyskać listę operacji, zobacz [Operacje dostawcy zasobów platformy Azure](../../role-based-access-control/resource-provider-operations.md). Aby uzyskać listę wbudowanych ról, zobacz [Role wbudowane platformy Azure.](../../role-based-access-control/built-in-roles.md)

Jeśli masz wymagany dostęp, ale żądanie usunięcia kończy się niepowodzeniem, może to być spowodowane blokadą grupy zasobów. [](lock-resources.md)

## <a name="next-steps"></a>Następne kroki

* Aby zrozumieć Resource Manager, zobacz [Azure Resource Manager omówienie](overview.md).
* Aby uzyskać polecenia usuwania, zobacz [PowerShell,](/powershell/module/az.resources/Remove-AzResourceGroup) [Interfejs wiersza polecenia platformy Azure](/cli/azure/group#az_group_delete)i Interfejs API [REST](/rest/api/resources/resourcegroups/delete).
