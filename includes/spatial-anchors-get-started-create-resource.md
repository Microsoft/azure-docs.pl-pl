---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 2c85e26d5a9115b00621c4099e3ed36afb224e3f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880802"
---
## <a name="create-a-spatial-anchors-resource"></a>Tworzenie zasobu usługi Spatial Anchors

### <a name="portal"></a>[Portal](#tab/azure-portal)

Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

W okienku po lewej stronie wybierz **pozycję Utwórz zasób.**

Użyj pola wyszukiwania, aby **wyszukać** Spatial Anchors .

![Zrzut ekranu przedstawiający wyniki wyszukiwania Spatial Anchors.](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Wybierz **Spatial Anchors**, a następnie wybierz pozycję **Utwórz.**

W **okienku Spatial Anchors konto** wykonaj następujące czynności:

* Wprowadź unikatową nazwę zasobu przy użyciu zwykłych znaków alfanumerycznych.
* Wybierz subskrypcję, do której chcesz dołączyć zasób.
* Utwórz grupę zasobów, wybierając **pozycję Utwórz nową.** **Nadaj jej nazwę myResourceGroup,** a następnie wybierz **przycisk OK.**

  [!INCLUDE [resource group intro text](resource-group.md)]

* Wybierz lokalizację (region), w której ma być umieszczany zasób.
* Wybierz pozycję **Nowy**, aby rozpocząć tworzenie zasobu.

![Zrzut ekranu przedstawiający Spatial Anchors tworzenia zasobu.](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Po utworzeniu zasobu w Azure Portal, że wdrożenie jest ukończone.

![Zrzut ekranu przedstawiający ukończenie wdrażania zasobów.](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Wybierz pozycję **Przejdź do zasobu**. Teraz możesz wyświetlić właściwości zasobu.

Skopiuj wartość Account **ID zasobu** do edytora tekstów do późniejszego użycia.

![Zrzut ekranu przedstawiający okienko właściwości zasobu.](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Skopiuj również wartość Account **Domain zasobu** do edytora tekstów do późniejszego użycia.

![Zrzut ekranu przedstawiający wartość domeny konta zasobu.](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

W **obszarze Ustawienia** wybierz pozycję **Klucz**. Skopiuj wartość **Klucz podstawowy,** **Klucz konta,** do edytora tekstów do późniejszego użycia.

![Zrzut ekranu przedstawiający okienko Klucze dla konta.](./media/spatial-anchors-get-started-create-resource/view-account-key.png)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Rozpocznij od przygotowania środowiska dla interfejsu wiersza polecenia platformy Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](azure-cli-prepare-your-environment-no-header.md)]

1. Po zalogowaniu użyj polecenia [az account set,](/cli/azure/account#az_account_set) aby wybrać subskrypcję, w której chcesz skonfigurować konto usługi Spatial Anchors:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Uruchom polecenie [az group create,](/cli/azure/group#az_group_create) aby utworzyć grupę zasobów, lub użyj istniejącej grupy zasobów:

   ```azurecli
   az group create --name myResourceGroup --location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   Bieżące konta zakotwiczeń przestrzennych dla grupy zasobów można wyświetlić za pomocą polecenia [az spatial-anchors-account list:](/cli/azure/spatial-anchors-account#az_spatial_anchors_account_list)

   ```azurecli
   az spatial-anchors-account list --resource-group myResourceGroup
   ```

   Możesz również wyświetlić konta zakotwiczeń przestrzennych dla subskrypcji:

   ```azurecli
   az spatial-anchors-account list
   ```

1. Uruchom polecenie [az spatial-anchors-account create,](/cli/azure/spatial-anchors-account#az_spatial_anchors_account_create) aby utworzyć konto zakotwiczeń przestrzennych:

   ```azurecli
   az spatial-anchors-account create --resource-group myResourceGroup --name MySpatialAnchorsQuickStart --location eastus2
   ```

1. Wyświetl właściwości zasobu za pomocą polecenia [az spatial-anchors-account show:](/cli/azure/spatial-anchors-account#az_spatial_anchors_account_show)

   ```azurecli
   az spatial-anchors-account show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Skopiuj wartość identyfikator **konta zasobu** i wartość domeny **konta** zasobu do edytora tekstów do późniejszego użycia.

1. Uruchom polecenie [az spatial-anchors-account key show,](/cli/azure/spatial-anchors-account/key#az_spatial_anchors_account_key_show) aby uzyskać klucze podstawowe i pomocnicze:

   ```azurecli
   az spatial-anchors-account key show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Skopiuj wartości klucza do edytora tekstów do późniejszego użycia.

   Jeśli musisz ponownie wygenerować klucze, użyj [polecenia az spatial-anchors-account renew:](/cli/azure/spatial-anchors-account/key#az_spatial_anchors_account_key_renew)

   ```azurecli
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key primary
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key secondary
   ```

Konto można usunąć za pomocą [polecenia az spatial-anchors-account delete:](/cli/azure/spatial-anchors-account#az_spatial_anchors_account_delete)

```azurecli
az spatial-anchors-account delete --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Rozpocznij od przygotowania środowiska pod kątem programu Azure:

[!INCLUDE [azure-powershell-requirements-no-header.md](azure-powershell-requirements-no-header.md)]

> [!IMPORTANT]
> Gdy moduł **Az.MixedReality** programu PowerShell jest w wersji zapoznawczej, należy zainstalować go oddzielnie przy użyciu `Install-Module` polecenia cmdlet . Gdy ten moduł programu PowerShell stanie się ogólnie dostępny, będzie częścią przyszłych wydań modułu Az programu PowerShell i będzie domyślnie dostępny z poziomu usługi Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.MixedReality
```

1. Po zalogowaniu użyj polecenia cmdlet [Set-AzContext,](/powershell/module/az.accounts/set-azcontext) aby wybrać subskrypcję, w której chcesz skonfigurować konto usługi Spatial Anchors:

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Uruchom polecenie cmdlet [New-AzResourceGroup,](/powershell/module/az.resources/new-azresourcegroup) aby utworzyć grupę zasobów, lub użyj istniejącej grupy zasobów:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   Bieżące konta zakotwiczeń przestrzennych dla grupy zasobów można wyświetlić za pomocą polecenia cmdlet [Get-AzSpatialAnchorsAccount:](/powershell/module/az.mixedreality/get-azspatialanchorsaccount)

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup
   ```

   Możesz również wyświetlić konta zakotwiczeń przestrzennych dla subskrypcji:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount
   ```

1. Uruchom polecenie cmdlet [New-AzSpatialAnchorsAccount,](/powershell/module/az.mixedreality/new-azspatialanchorsaccount) aby utworzyć konto zakotwiczeń przestrzennych:

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart -Location eastus2
   ```

1. Wyświetl właściwości zasobu za pomocą polecenia cmdlet [Get-AzSpatialAnchorsAccount:](/powershell/module/az.mixedreality/get-azspatialanchorsaccount)

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   Skopiuj wartość **accountId właściwości** i wartość **accountDomain** właściwości do edytora tekstów do późniejszego użycia.

1. Uruchom polecenie cmdlet [Get-AzSpatialAnchorsAccountKey,](/powershell/module/az.mixedreality/get-azspatialanchorsaccountkey) aby uzyskać klucze podstawowe i pomocnicze:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccountKey -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   Skopiuj wartości kluczy do edytora tekstów do późniejszego użycia.

   Jeśli musisz ponownie wygenerować klucze, użyj polecenia cmdlet [New-AzSpatialAnchorsAccountKey:](/powershell/module/az.mixedreality/new-azspatialanchorsaccountkey)

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Primary
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Secondary
   ```

Konto można usunąć za pomocą polecenia cmdlet [Remove-AzSpatialAnchorsAccount:](/powershell/module/az.mixedreality/remove-azspatialanchorsaccount)

```azurepowershell-interactive
Remove-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
```

---
