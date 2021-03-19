---
title: Konfigurowanie tożsamości zarządzanych na maszynie wirtualnej platformy Azure przy użyciu programu PowerShell — Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu programu PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/19/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b4209ce159c9d0bbee01dd422b98832f6bb5713
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90968999"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu programu PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie.

W tym artykule, korzystając z programu PowerShell, dowiesz się, jak wykonywać następujące tożsamości zarządzane dla operacji zasobów platformy Azure na maszynie wirtualnej platformy Azure.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md). **Pamiętaj, aby zapoznać się z [różnicą między przypisaną przez system i tożsamością zarządzaną przez użytkownika](overview.md#managed-identity-types)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do uruchomienia przykładowych skryptów są dostępne dwie opcje:
    - Użyj [Azure Cloud Shell](../../cloud-shell/overview.md), którą można otworzyć za pomocą przycisku **Wypróbuj** w prawym górnym rogu bloków kodu.
    - Uruchom skrypty lokalnie, instalując najnowszą wersję [Azure PowerShell](/powershell/azure/install-az-ps), a następnie zaloguj się do platformy Azure przy użyciu `Connect-AzAccount` . 

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

Ta sekcja zawiera informacje na temat włączania i wyłączania tożsamości zarządzanej przypisanej przez system przy użyciu Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Włącz tożsamość zarządzaną przypisaną przez system podczas tworzenia maszyny wirtualnej platformy Azure

Aby można było utworzyć maszynę wirtualną platformy Azure z włączoną tożsamością zarządzaną przypisaną przez system, Twoje konto wymaga przypisania roli [współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Zapoznaj się z jednym z następujących przewodników szybki start dotyczących maszyn wirtualnych platformy Azure, wykonując tylko niezbędne sekcje ("Logowanie do platformy Azure", "Tworzenie grupy zasobów", "Tworzenie grupy sieciowej", "Tworzenie maszyny wirtualnej").

    Po wyświetleniu sekcji "Tworzenie maszyny wirtualnej" wprowadź niewielką modyfikację składni polecenia cmdlet [New-AzVMConfig](/powershell/module/az.compute/new-azvm) . Pamiętaj, aby dodać `-IdentityType SystemAssigned` parametr w celu aprowizacji maszyny wirtualnej z włączoną tożsamością przypisaną do systemu, na przykład:

    ```azurepowershell-interactive
    $vmConfig = New-AzVMConfig -VMName myVM -IdentityType SystemAssigned ...
    ```

   - [Tworzenie maszyny wirtualnej z systemem Windows za pomocą programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Włącz tożsamość zarządzaną przypisaną przez system na istniejącej maszynie wirtualnej platformy Azure

Aby włączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej, która została pierwotnie zainicjowana bez niej, konto wymaga przypisania roli [współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Pobierz właściwości maszyny wirtualnej przy użyciu `Get-AzVM` polecenia cmdlet. Następnie w celu włączenia zarządzanej tożsamości przypisanej do systemu Użyj `-IdentityType` przełącznika w poleceniu cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm) :

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType SystemAssigned
   ```

### <a name="add-vm-system-assigned-identity-to-a-group"></a>Dodawanie tożsamości przypisanej do systemu maszyn wirtualnych do grupy

Po włączeniu tożsamości przypisanej przez system na maszynie wirtualnej można dodać ją do grupy.  Poniższa procedura umożliwia dodanie tożsamości przypisanej do grupy przez maszynę wirtualną.

1. Pobierz i zanotuj `ObjectID` (jak określono w `Id` polu zwracanych wartości) nazwy głównej usługi maszyny wirtualnej:

   ```azurepowershell-interactive
   Get-AzADServicePrincipal -displayname "myVM"
   ```

1. Pobierz i zanotuj `ObjectID` (jak określono w `Id` polu zwracanych wartości) grupy:

   ```azurepowershell-interactive
   Get-AzADGroup -searchstring "myGroup"
   ```

1. Dodaj nazwę główną usługi maszyny wirtualnej do grupy:

   ```azurepowershell-interactive
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Wyłączanie tożsamości zarządzanej przypisanej przez system z maszyny wirtualnej platformy Azure

Aby wyłączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej, Twoje konto wymaga przypisania roli [współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

Jeśli masz maszynę wirtualną, która nie potrzebuje już tożsamości zarządzanej przypisanej do systemu, ale nadal wymaga tożsamości zarządzanej przez użytkownika, użyj następującego polecenia cmdlet:

1. Pobierz właściwości maszyny wirtualnej za pomocą `Get-AzVM` polecenia cmdlet i ustaw `-IdentityType` parametr na `UserAssigned` :

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Jeśli masz maszynę wirtualną, która nie wymaga już tożsamości zarządzanej przypisanej do systemu i nie ma żadnych tożsamości zarządzanych przez użytkownika, użyj następujących poleceń:

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przez użytkownika z maszyny wirtualnej przy użyciu Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do maszyny wirtualnej podczas jej tworzenia

Do przypisywania tożsamości przypisanej przez użytkownika do maszyny wirtualnej wymagane są przypisania ról współautor i [operator tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-operator) [maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) . Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Zapoznaj się z jednym z następujących przewodników szybki start dotyczących maszyn wirtualnych platformy Azure, wykonując tylko niezbędne sekcje ("Logowanie do platformy Azure", "Tworzenie grupy zasobów", "Tworzenie grupy sieciowej", "Tworzenie maszyny wirtualnej").

    Po wyświetleniu sekcji "Tworzenie maszyny wirtualnej" wprowadź niewielką modyfikację [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) składni polecenia cmdlet. Dodaj `-IdentityType UserAssigned` Parametry i, `-IdentityID` Aby zainicjować obsługę administracyjną maszyny wirtualnej przy użyciu tożsamości przypisanej do użytkownika.  Zastąp `<VM NAME>` wartości,, `<SUBSCRIPTION ID>` `<RESROURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` własnymi wartościami.  Na przykład:

    ```azurepowershell-interactive
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```

    - [Tworzenie maszyny wirtualnej z systemem Windows za pomocą programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md)


### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącej maszyny wirtualnej platformy Azure

Do przypisywania tożsamości przypisanej przez użytkownika do maszyny wirtualnej wymagane są przypisania ról współautor i [operator tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-operator) [maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) . Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Utwórz tożsamość zarządzaną przypisaną przez użytkownika przy użyciu polecenia cmdlet [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity) .  Zwróć uwagę na `Id` dane wyjściowe, ponieważ będą potrzebne w następnym kroku.

   > [!IMPORTANT]
   > Tworzenie tożsamości zarządzanych przypisanych przez użytkownika obsługuje tylko znaki alfanumeryczne, podkreślenia i łącznik (0-9 lub a-z lub A-Z, lub-Z, \_ lub). Ponadto nazwa powinna mieć ograniczoną długość od 3 do 128 znaków, aby przypisanie do maszyny wirtualnej/VMSS działało prawidłowo. Aby uzyskać więcej informacji [, zobacz często zadawane pytania i znane problemy](known-issues.md)

   ```azurepowershell-interactive
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
1. Pobierz właściwości maszyny wirtualnej przy użyciu `Get-AzVM` polecenia cmdlet. Następnie aby przypisać tożsamość zarządzaną przypisaną przez użytkownika do maszyny wirtualnej platformy Azure, `-IdentityType` Użyj `-IdentityID` przełącznika i w poleceniu cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm) .  Wartość `-IdentityId` parametru jest `Id` zanotowany w poprzednim kroku.  Zastąp `<VM NAME>` wartości,, `<SUBSCRIPTION ID>` `<RESROURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` własnymi wartościami.

   > [!WARNING]
   > Aby zachować wszystkie skojarzone z nią tożsamości zarządzane przypisane przez użytkownika do maszyny wirtualnej, należy wykonać zapytanie o `Identity` właściwość obiektu maszyny wirtualnej (na przykład `$vm.Identity` ).  Jeśli zwracane są tożsamości zarządzane przypisane przez użytkownika, należy uwzględnić je w następującym poleceniu wraz z nową tożsamością zarządzaną przypisanego przez użytkownika, którą chcesz przypisać do maszyny wirtualnej.

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z maszyny wirtualnej platformy Azure

Aby usunąć tożsamość przypisaną przez użytkownika do maszyny wirtualnej, Twoje konto wymaga przypisania roli [współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .

Jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przez użytkownika, możesz usunąć wszystkie z nich oprócz ostatnich przy użyciu następujących poleceń. Upewnij się, że parametry `<RESOURCE GROUP>` i `<VM NAME>` zostały zastąpione własnymi wartościami. `<USER ASSIGNED IDENTITY NAME>`Jest właściwością nazwa tożsamości zarządzanej przypisanej przez użytkownika, która powinna pozostać na maszynie wirtualnej. Te informacje można znaleźć, badając `Identity` właściwość obiektu maszyny wirtualnej.  Na przykład `$vm.Identity` :

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Jeśli maszyna wirtualna nie ma tożsamości zarządzanej przypisanej do systemu i chcesz usunąć wszystkie zarządzane przez użytkownika tożsamości, użyj następującego polecenia:

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Jeśli maszyna wirtualna ma zarządzane tożsamości przypisane do systemu i przypisane przez użytkownika, możesz usunąć wszystkie zarządzane tożsamości przypisane przez użytkownika, przełączając się do korzystania tylko z zarządzanych tożsamości przypisanych do systemu.

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie tożsamościami dla zasobów platformy Azure — omówienie](overview.md)
- Aby zapoznać się z pełnymi przewodnikami szybki start tworzenia maszyn wirtualnych platformy Azure, zobacz:

  - [Tworzenie maszyny wirtualnej z systemem Windows za pomocą programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
  - [Tworzenie maszyny wirtualnej z systemem Linux za pomocą programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md)
