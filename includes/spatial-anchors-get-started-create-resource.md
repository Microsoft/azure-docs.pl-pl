---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 596b73f8fb205b6a5681fecf3d00fd2a67c1f59f
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97628729"
---
## <a name="create-a-spatial-anchors-resource"></a>Tworzenie zasobu usługi Spatial Anchors

### <a name="portal"></a>[Portal](#tab/azure-portal)

Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

W okienku po lewej stronie wybierz pozycję **Utwórz zasób**.

Użyj pola wyszukiwania, aby wyszukać **kotwice przestrzenne**.

![Zrzut ekranu przedstawiający wyniki wyszukiwania kotwice przestrzenne.](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Wybierz pozycję **kotwice przestrzenne**, a następnie wybierz pozycję **Utwórz**.

W okienku **konto kotwice przestrzenne** wykonaj następujące czynności:

* Wprowadź unikatową nazwę zasobu, używając zwykłych znaków alfanumerycznych.
* Wybierz subskrypcję, do której chcesz dołączyć zasób.
* Utwórz grupę zasobów, wybierając pozycję **Utwórz nową**. **Nadaj jej nazwę**, a następnie wybierz przycisk **OK**.

  [!INCLUDE [resource group intro text](resource-group.md)]

* Wybierz lokalizację (region), w której ma zostać umieszczony zasób.
* Wybierz pozycję **Nowy**, aby rozpocząć tworzenie zasobu.

![Zrzut ekranu przedstawiający okienko kotwice przestrzenne służące do tworzenia zasobów.](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Po utworzeniu zasobu Azure Portal pokazuje, że wdrożenie zostało zakończone.

![Zrzut ekranu przedstawiający ukończenie wdrożenia zasobów.](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Wybierz pozycję **Przejdź do zasobu**. Teraz możesz wyświetlić właściwości zasobów.

Skopiuj wartość **identyfikatora konta** zasobu do edytora tekstu w celu późniejszego użycia.

![Zrzut ekranu przedstawiający okienko właściwości zasobu.](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Skopiuj również wartość **domeny konta** zasobu do edytora tekstu w celu późniejszego użycia.

![Zrzut ekranu przedstawiający wartość domeny konta zasobu.](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

W obszarze **Ustawienia** wybierz pozycję **klucz**. Skopiuj wartość **klucza podstawowego** , **klucz konta** do edytora tekstów w celu późniejszego użycia.

![Zrzut ekranu przedstawiający okienko klucze dla konta.](./media/spatial-anchors-get-started-create-resource/view-account-key.png)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Rozpocznij od przygotowania środowiska dla interfejsu wiersza polecenia platformy Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](azure-cli-prepare-your-environment-no-header.md)]

1. Po zalogowaniu Użyj polecenia [AZ Account Set](/cli/azure/account#az_account_set) , aby wybrać subskrypcję, w której ma zostać skonfigurowane konto kotwice przestrzenne:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Uruchom polecenie [AZ Group Create](/cli/azure/group#az_group_create) , aby utworzyć grupę zasobów, lub Użyj istniejącej grupy zasobów:

   ```azurecli
   az group create --name myResourceGroup --location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   Bieżące konta zakotwiczeń przestrzennych dla grupy zasobów można wyświetlić za pomocą polecenia [AZ przestrzenny — kotwice — lista kont](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_list) :

   ```azurecli
   az spatial-anchors-account list --resource-group myResourceGroup
   ```

   Możesz również wyświetlić konta kotwic przestrzennych dla Twojej subskrypcji:

   ```azurecli
   az spatial-anchors-account list
   ```

1. Uruchom polecenie [AZ przestrzenny kotwice-Account Create](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_create) , aby utworzyć konto zakotwiczeń przestrzennych:

   ```azurecli
   az spatial-anchors-account create --resource-group myResourceGroup --name MySpatialAnchorsQuickStart --location eastus2
   ```

1. Wyświetl właściwości zasobu za pomocą polecenia [AZ przestrzenny-kotwice-Account show](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_show) :

   ```azurecli
   az spatial-anchors-account show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Skopiuj wartość **Identyfikator konta** zasobu i wartość **domeny konta** zasobu do edytora tekstów w celu późniejszego użycia.

1. Aby uzyskać klucze podstawowe i pomocnicze, uruchom polecenie [AZ przestrzenny kotwics-Account Key show](/cli/azure/ext/mixed-reality/spatial-anchors-account/key#ext_mixed_reality_az_spatial_anchors_account_key_show) :

   ```azurecli
   az spatial-anchors-account key show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Skopiuj wartości klucza do edytora tekstu w celu późniejszego użycia.

   Jeśli musisz ponownie wygenerować klucze, użyj polecenia [AZ przestrzenny-zakotwiczes-Account Renew](/cli/azure/ext/mixed-reality/spatial-anchors-account/key#ext_mixed_reality_az_spatial_anchors_account_key_renew) :

   ```azurecli
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key primary
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key secondary
   ```

Można usunąć konto za pomocą polecenia [AZ przestrzenny-kotwice-Account Delete](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_delete) :

```azurecli
az spatial-anchors-account delete --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Rozpocznij od przygotowania środowiska pod kątem programu Azure:

[!INCLUDE [azure-powershell-requirements-no-header.md](azure-powershell-requirements-no-header.md)]

> [!IMPORTANT]
> Mimo że moduł **AZ. MixedReality** PowerShell jest w wersji zapoznawczej, należy go zainstalować oddzielnie przy użyciu `Install-Module` polecenia cmdlet. Gdy ten moduł programu PowerShell stanie się ogólnie dostępny, będzie częścią przyszłych wydań modułu Az programu PowerShell i będzie domyślnie dostępny z poziomu usługi Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.MixedReality
```

1. Po zalogowaniu Użyj polecenia cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) , aby wybrać subskrypcję, w której ma zostać skonfigurowane konto kotwice przestrzenne:

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Uruchom polecenie cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) , aby utworzyć grupę zasobów, lub Użyj istniejącej grupy zasobów:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   Bieżące konta zakotwiczeń przestrzennych dla grupy zasobów można wyświetlić za pomocą polecenia cmdlet [Get-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/get-azspatialanchorsaccount) :

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup
   ```

   Możesz również wyświetlić konta kotwic przestrzennych dla Twojej subskrypcji:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount
   ```

1. Uruchom polecenie cmdlet [New-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/new-azspatialanchorsaccount) , aby utworzyć konto kotwice przestrzenne:

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart -Location eastus2
   ```

1. Wyświetl właściwości zasobu za pomocą polecenia cmdlet [Get-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/get-azspatialanchorsaccount) :

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   Skopiuj wartość **accountId** i Właściwość **accountDomain** do edytora tekstu w celu późniejszego użycia.

1. Uruchom polecenie cmdlet [Get-AzSpatialAnchorsAccountKey](/powershell/module/az.mixedreality/get-azspatialanchorsaccountkey) , aby pobrać klucze podstawowe i pomocnicze:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccountKey -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   Skopiuj wartości klucza do edytora tekstu w celu późniejszego użycia.

   Jeśli musisz ponownie wygenerować klucze, użyj polecenia cmdlet [New-AzSpatialAnchorsAccountKey](/powershell/module/az.mixedreality/new-azspatialanchorsaccountkey) :

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Primary
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Secondary
   ```

Konto można usunąć za pomocą polecenia cmdlet [Remove-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/remove-azspatialanchorsaccount) :

```azurepowershell-interactive
Remove-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
```

---
