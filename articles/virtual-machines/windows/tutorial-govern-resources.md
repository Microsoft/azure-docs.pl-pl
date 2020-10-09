---
title: Samouczek — zarządzanie maszynami wirtualnymi za pomocą programu PowerShell
description: Z tego samouczka dowiesz się, jak za pomocą programu Azure PowerShell zarządzać maszynami wirtualnymi platformy Azure, stosując kontrolę dostępu opartą na rolach, zasady, blokady i tagi
author: tfitzmac
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: tutorial
ms.date: 12/05/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 723eaeb6eb8946473b31b447e817a0a3b696f1cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87926573"
---
# <a name="tutorial-learn-about-windows-virtual-machine-management-with-azure-powershell"></a>Samouczek: informacje o zarządzaniu maszynami wirtualnymi systemu Windows za pomocą Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

## <a name="understand-scope"></a>Objaśnienie zakresu

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

W tym samouczku zastosujesz wszystkie ustawienia zarządzania do grupy zasobów, aby móc łatwo usunąć te ustawienia po zakończeniu.

Utwórzmy tę grupę zasobów.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Grupa zasobów jest obecnie pusta.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Upewnij się, że użytkownicy w organizacji mają odpowiedni poziom dostępu do tych zasobów. Nie udzielaj użytkownikom nieograniczonego dostępu, ale upewnij się, że mogą oni wykonywać swoją pracę. [Kontrola dostępu oparta na rolach (Azure RBAC)](../../role-based-access-control/overview.md) umożliwia zarządzanie użytkownikami, którzy mają uprawnienia do wykonywania określonych działań w zakresie.

Aby móc tworzyć i usuwać przypisania roli, użytkownicy muszą mieć dostęp `Microsoft.Authorization/roleAssignments/*`. Ten dostęp jest udzielany za pośrednictwem ról Właściciel lub Administrator dostępu użytkowników.

W przypadku zarządzania rozwiązaniami maszyn wirtualnych dostępne są 3 role specyficzne dla zasobów, które zapewniają najczęściej potrzebny dostęp:

* [Współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Współautor sieci](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Współautor konta magazynu](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Zamiast przypisywać role poszczególnym użytkownikom, często łatwiej jest użyć grupy usługi Azure Active Directory zawierającej użytkowników, którzy muszą wykonywać podobne działania. Następnie należy przypisać tę grupę do odpowiedniej roli. Na potrzeby tego artykułu użyj istniejącej grupy do zarządzania maszyną wirtualną lub użyj portalu do [utworzenia grupy usługi Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Po utworzeniu nowej grupy lub znalezieniu istniejącej grupy przypisz grupę usługi Azure Active Directory do roli współautora maszyny wirtualnej dla grupy zasobów za pomocą polecenia [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).  

```azurepowershell-interactive
$adgroup = Get-AzADGroup -DisplayName <your-group-name>

New-AzRoleAssignment -ObjectId $adgroup.id `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Jeśli zostanie zgłoszony błąd wskazujący, że **jednostka\<guid> nie istnieje w katalogu**, oznacza to, że nowa grupa nie została rozpropagowana w usłudze Azure Active Directory. Spróbuj ponownie uruchomić polecenie.

Zazwyczaj należy powtórzyć ten proces dla roli *Współautor sieci* i *Współautor konta magazynu*, aby upewnić się, że użytkownicy mogą zarządzać wdrożonymi zasobami. W tym artykule można pominąć te kroki.

## <a name="azure-policy"></a>Azure Policy

Usługa [Azure Policy](../../governance/policy/overview.md) ułatwia zapewnienie, że wszystkie zasoby w subskrypcji spełniają standardy firmy. Subskrypcja ma już kilka definicji zasad. Aby wyświetlić dostępne definicje zasad, użyj polecenia [Get-AzPolicyDefinition](/powershell/module/az.resources/get-azpolicydefinition):

```azurepowershell-interactive
(Get-AzPolicyDefinition).Properties | Format-Table displayName, policyType
```

Zostaną wyświetlone istniejące definicje zasad. Typ zasad to **Wbudowane** albo **Niestandardowe**. Przejrzyj definicje i znajdź te opisujące warunek, który chcesz przypisać. W tym artykule przypiszesz zasady określające następujące działania:

* Ograniczanie lokalizacji dla wszystkich zasobów.
* Ograniczanie jednostek SKU dla maszyn wirtualnych.
* Przeprowadzanie inspekcji maszyn wirtualnych, które nie korzystają z dysków zarządzanych.

W poniższym przykładzie pobierane są 3 definicje zasad na podstawie nazwy wyświetlanej. Aby przypisać te definicje do grup zasobów, użyj polecenia [New-AzPolicyAssignment](/powershell/module/az.resources/new-azpolicyassignment). W przypadku niektórych zasad należy podać wartości parametrów, aby określić dozwolone wartości.

```azurepowershell-interactive
# Values to use for parameters
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

# Get the resource group
$rg = Get-AzResourceGroup -Name myResourceGroup

# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
$locationDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine size SKUs"}
$auditDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

# Assign policy for allowed locations
New-AzPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations

# Assign policy for allowed SKUs
New-AzPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus

# Assign policy for auditing unmanaged disks
New-AzPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>Wdrażanie maszyny wirtualnej

Role i zasady są przypisane, a zatem pora na wdrożenie rozwiązania. Rozmiar domyślny to Standard_DS1_v2, który jest jedną z dozwolonych jednostek SKU. Podczas wykonywania tego kroku jest wyświetlany monit o poświadczenia. Wprowadzane wartości są konfigurowane jako nazwa użytkownika i hasło dla maszyny wirtualnej.

```azurepowershell-interactive
New-AzVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

Po zakończeniu wdrażania możesz zastosować do rozwiązania więcej ustawień zarządzania.

## <a name="lock-resources"></a>Blokowanie zasobów

[Blokady zasobów](../../azure-resource-manager/management/lock-resources.md) uniemożliwiają użytkownikom w organizacji przypadkowe usunięcie lub zmodyfikowanie krytycznych zasobów. W przeciwieństwie do kontroli dostępu opartej na rolach blokady zasobów stosują ograniczenie do wszystkich użytkowników i ról. Poziom blokady można ustawić na wartość *CanNotDelete* lub *ReadOnly*.

Aby zablokować maszynę wirtualną i sieciową grupę zabezpieczeń, użyj polecenia [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock):

```azurepowershell-interactive
# Add CanNotDelete lock to the VM
New-AzResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup

# Add CanNotDelete lock to the network security group
New-AzResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Aby przetestować blokadę, spróbuj uruchomić następujące polecenie:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup
```

Zostanie wyświetlony komunikat o błędzie informujący, że nie można zakończyć operacji usuwania z powodu blokady. Grupę zasobów można usunąć tylko po zdjęciu blokad. Ten krok przedstawiono w sekcji [Oczyszczanie zasobów](#clean-up-resources).

## <a name="tag-resources"></a>Tagowanie zasobów

[Znaczniki](../../azure-resource-manager/management/tag-resources.md) do zasobów platformy Azure są stosowane w celu logicznego organizowania ich według kategorii. Każdy tag składa się z nazwy i wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

Aby zastosować tagi do maszyny wirtualnej, użyj polecenia [Set-AzResource](/powershell/module/az.resources/set-azresource):

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Znajdowanie zasobów według tagów

Aby znaleźć zasoby według wartości i nazwy tagu, użyj polecenia [Get-AzResource](/powershell/module/az.resources/get-azresource):

```azurepowershell-interactive
(Get-AzResource -Tag @{ Environment="Test"}).Name
```

Zwróconych wartości można użyć na potrzeby zadań zarządzania, takich jak zatrzymanie wszystkich maszyn wirtualnych z daną wartością tagu.

```azurepowershell-interactive
Get-AzResource -Tag @{ Environment="Test"} | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzVM
```

### <a name="view-costs-by-tag-values"></a>Wyświetlanie kosztów według wartości tagów

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Zablokowanej sieciowej grupy zabezpieczeń nie można usunąć, dopóki blokada nie zostanie zdjęta. Aby zdjąć blokadę, użyj polecenia [Remove-AzResourceLock](/powershell/module/az.resources/remove-azresourcelock):

```azurepowershell-interactive
Remove-AzResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="manage-costs"></a>Zarządzanie kosztami

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony obraz niestandardowy maszyny wirtualnej. W tym samouczku omówiono:

> [!div class="checklist"]
> * Przypisywanie użytkowników do roli
> * Stosowanie zasad wymuszających standardy
> * Ochrona krytycznych zasobów za pomocą blokad
> * Tagowanie zasobów na potrzeby rozliczeń i zarządzania

Przejdź do następnego samouczka, aby dowiedzieć się, jak identyfikować zmiany i zarządzać aktualizacjami pakietów na maszynie wirtualnej z systemem Linux.

> [!div class="nextstepaction"]
> [Zarządzanie maszynami wirtualnymi](tutorial-config-management.md)
