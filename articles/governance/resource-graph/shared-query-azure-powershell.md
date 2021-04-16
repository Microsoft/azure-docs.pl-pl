---
title: 'Szybki start: tworzenie udostępnionego zapytania za pomocą Azure PowerShell'
description: W tym przewodniku Szybki start wykonaj kroki, aby utworzyć zapytanie Resource Graph przy użyciu Azure PowerShell.
ms.date: 01/11/2021
ms.topic: quickstart
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 9015b6df99bdd6f153194e8f4cbbe7658cf1d6dc
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535811"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-powershell"></a>Szybki start: tworzenie Resource Graph udostępnionego przy użyciu Azure PowerShell

W tym artykule opisano, jak utworzyć Azure Resource Graph udostępnione przy użyciu [modułu Az.ResourceGraph](/powershell/module/az.resourcegraph) programu PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Gdy moduł **Az.ResourceGraph** programu PowerShell jest w wersji zapoznawczej, należy zainstalować go oddzielnie przy użyciu `Install-Module` polecenia cmdlet .

  ```azurepowershell-interactive
  Install-Module -Name Az.ResourceGraph
  ```

- Jeśli masz wiele subskrypcji platformy Azure, wybierz odpowiednią subskrypcję, w ramach której mają być naliczane opłaty za zasoby. Wybierz określoną subskrypcję za pomocą polecenia cmdlet [Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-graph-shared-query"></a>Tworzenie Resource Graph udostępnionego

Po **dodaniu modułu Az.ResourceGraph** programu PowerShell do wybranego środowiska czas na utworzenie Resource Graph udostępnionego. Udostępnione zapytanie jest obiektem Azure Resource Manager który można przyznać uprawnienia lub uruchamiać w Azure Resource Graph Explorer. Zapytanie podsumowuje liczbę wszystkich zasobów pogrupowanych według _lokalizacji_.

1. Utwórz grupę zasobów za [pomocą programu New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) do przechowywania Azure Resource Graph udostępnionego zapytania. Ta grupa zasobów ma nazwę `resource-graph-queries` , a lokalizacja to `westus2` .

   ```azurepowershell-interactive
   # Login first with `Connect-AzAccount` if not using Cloud Shell

   # Create the resource group
   New-AzResourceGroup -Name resource-graph-queries -Location westus2
   ```

1. Utwórz udostępnione Azure Resource Graph przy użyciu modułu **Az.ResourceGraph** programu PowerShell i polecenia cmdlet [New-AzResourceGraphQuery:](/powershell/module/az.resourcegraph/new-azresourcegraphquery)

   ```azurepowershell-interactive
   # Create the Azure Resource Graph shared query
   $Params = @{
     Name = 'Summarize resources by location'
     ResourceGroupName = 'resource-graph-queries'
     Location = 'westus2'
     Description = 'This shared query summarizes resources by location for a pinnable map graphic.'
     Query = 'Resources | summarize count() by location'
   }
   New-AzResourceGraphQuery @Params
   ```

1. Wylicza listę udostępnionych zapytań w nowej grupie zasobów. Polecenie cmdlet [Get-AzResourceGraphQuery](/powershell/module/az.resourcegraph/get-azresourcegraphquery) zwraca tablicę wartości.

   ```azurepowershell-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries
   ```

1. Aby uzyskać tylko jeden udostępniony wynik zapytania, użyj `Get-AzResourceGraphQuery` parametru z `Name` parametrem .

   ```azurepowershell-interactive
   # Show a specific Azure Resource Graph shared query
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'
   ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć zapytanie udostępnione Resource Graph grupę zasobów ze środowiska platformy Azure, możesz to zrobić za pomocą następujących poleceń:

- [Remove-AzResourceGraphQuery](/powershell/module/az.resourcegraph/remove-azresourcegraphquery)
- [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)

```azurepowershell-interactive
# Delete the Azure Resource Graph shared query
Remove-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group
Remove-AzResourceGroup -Name resource-graph-queries
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono zapytanie udostępnione Resource Graph użyciu Azure PowerShell. Aby dowiedzieć się więcej o języku Resource Graph, przejdź do strony szczegółów języka zapytań.

> [!div class="nextstepaction"]
> [Uzyskaj więcej informacji na temat języka zapytań](./concepts/query-language.md)
