---
title: 'Szybki Start: Tworzenie zapytania udostępnionego z Azure PowerShell'
description: W tym przewodniku szybki start wykonaj kroki tworzenia zapytania udostępnionego grafu zasobów przy użyciu Azure PowerShell.
ms.date: 01/11/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b771253b1dea4bd1d2913bf7c48062112019a19
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981548"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-powershell"></a>Szybki Start: Tworzenie zapytania udostępnionego grafu zasobów przy użyciu Azure PowerShell

W tym artykule opisano sposób tworzenia zapytania udostępnionego grafu zasobów platformy Azure przy użyciu polecenia [AZ. ResourceGraph](/powershell/module/az.resourcegraph) PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Mimo że moduł **AZ. ResourceGraph** PowerShell jest w wersji zapoznawczej, należy go zainstalować oddzielnie przy użyciu `Install-Module` polecenia cmdlet.

  ```azurepowershell-interactive
  Install-Module -Name Az.ResourceGraph
  ```

- Jeśli masz wiele subskrypcji platformy Azure, wybierz odpowiednią subskrypcję, w której będą naliczane opłaty za zasoby. Wybierz określoną subskrypcję za pomocą polecenia cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) .

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-graph-shared-query"></a>Tworzenie zapytania udostępnionego wykresu zasobów

Za pomocą polecenia **AZ. ResourceGraph** PowerShell dodanego do wybranego środowiska można utworzyć zapytanie udostępnione grafu zasobów. Udostępnione zapytanie jest obiektem Azure Resource Manager, w którym można udzielić uprawnienia lub uruchomić je w Eksploratorze Azure Resource Graph. Zapytanie podsumowuje liczbę wszystkich zasobów pogrupowanych według _lokalizacji_.

1. Utwórz grupę zasobów przy użyciu usługi [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) , aby zachować udostępnione zapytanie grafu zasobów platformy Azure. Ta grupa zasobów ma nazwę `resource-graph-queries` , a lokalizacja to `westus2` .

   ```azurepowershell-interactive
   # Login first with `Connect-AzAccount` if not using Cloud Shell

   # Create the resource group
   New-AzResourceGroup -Name resource-graph-queries -Location westus2
   ```

1. Utwórz zapytanie udostępnione grafu zasobów platformy Azure za pomocą polecenia **AZ. ResourceGraph** PowerShell i polecenie cmdlet [New-AzResourceGraphQuery](/powershell/module/az.resourcegraph/new-azresourcegraphquery) :

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

1. Wyświetl listę udostępnionych zapytań w nowej grupie zasobów. Polecenie cmdlet [Get-AzResourceGraphQuery](/powershell/module/az.resourcegraph/get-azresourcegraphquery) zwraca tablicę wartości.

   ```azurepowershell-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries
   ```

1. Aby uzyskać tylko jeden wynik zapytania udostępnionego, użyj `Get-AzResourceGraphQuery` z jego `Name` parametrem.

   ```azurepowershell-interactive
   # Show a specific Azure Resource Graph shared query
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'
   ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć zapytanie udostępnione i grupę zasobów wykresu zasobów ze środowiska platformy Azure, możesz to zrobić za pomocą następujących poleceń:

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

W tym przewodniku szybki start utworzono zapytanie udostępnione grafu zasobów przy użyciu Azure PowerShell. Aby dowiedzieć się więcej na temat języka grafu zasobów, przejdź do strony szczegółów języka zapytań.

> [!div class="nextstepaction"]
> [Uzyskaj więcej informacji na temat języka zapytań](./concepts/query-language.md)