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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1eca17106067d964ba2f280d358b2973b41459a6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783273"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu programu PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez konieczności posiadania poświadczeń w kodzie.

Z tego artykułu, korzystając z programu PowerShell, dowiesz się, jak wykonywać następujące tożsamości zarządzane dla operacji zasobów platformy Azure na maszynie wirtualnej platformy Azure.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz informacji o tożsamościach zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przeglądu](overview.md). Zapoznaj się z różnicą między tożsamością zarządzaną przypisaną przez system i **[przypisaną przez użytkownika.](overview.md#managed-identity-types)**
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby uruchomić przykładowe skrypty, masz dwie opcje:
    - Użyj [Azure Cloud Shell](../../cloud-shell/overview.md), którą możesz otworzyć przy użyciu **przycisku Wypróbuj** w prawym górnym rogu bloków kodu.
    - Uruchom skrypty lokalnie, instalując najnowszą wersję programu Azure PowerShell , [a](/powershell/azure/install-az-ps)następnie zaloguj się do platformy Azure przy użyciu polecenia `Connect-AzAccount` . 

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

W tej sekcji dowiesz się, jak włączyć i wyłączyć tożsamość zarządzaną przypisaną przez system przy użyciu Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Włączanie tożsamości zarządzanej przypisanej przez system podczas tworzenia maszyny wirtualnej platformy Azure

Aby utworzyć maszynę wirtualną platformy Azure z włączoną tożsamością zarządzaną przypisaną przez system, Twoje konto musi mieć przypisanie roli [Współautor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) maszyny wirtualnej.  Nie są wymagane żadne dodatkowe przypisania roli katalogu usługi Azure AD.

1. Zapoznaj się z jednym z następujących przewodników Szybki start maszyny wirtualnej platformy Azure, wykonując tylko niezbędne sekcje ("Zaloguj się do platformy Azure", "Tworzenie grupy zasobów", "Tworzenie grupy sieci", "Tworzenie maszyny wirtualnej").

    Po dojściu do sekcji "Tworzenie maszyny wirtualnej" dokonaj niewielkich modyfikacji składni polecenia cmdlet [New-AzVMConfig.](/powershell/module/az.compute/new-azvm) Pamiętaj, aby dodać parametr w celu aprowizowania maszyny wirtualnej z włączoną tożsamością przypisaną przez `-IdentityType SystemAssigned` system, na przykład:

    ```azurepowershell-interactive
    $vmConfig = New-AzVMConfig -VMName myVM -IdentityType SystemAssigned ...
    ```

   - [Tworzenie maszyny wirtualnej z systemem Windows za pomocą programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Włączanie tożsamości zarządzanej przypisanej przez system na istniejącej maszynie wirtualnej platformy Azure

Aby włączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej, która została pierwotnie aprowizowana bez tej tożsamości, twoje konto musi mieć przypisanie roli [Współautor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) maszyny wirtualnej.  Nie są wymagane żadne dodatkowe przypisania roli katalogu usługi Azure AD.

1. Pobierz właściwości maszyny wirtualnej przy użyciu `Get-AzVM` polecenia cmdlet . Następnie, aby włączyć tożsamość zarządzaną przypisaną przez system, użyj `-IdentityType` przełącznika w poleceniach cmdlet [Update-AzVM:](/powershell/module/az.compute/update-azvm)

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType SystemAssigned
   ```

### <a name="add-vm-system-assigned-identity-to-a-group"></a>Dodawanie tożsamości przypisanej przez system maszyny wirtualnej do grupy

Po włączeniu tożsamości przypisanej przez system na maszynie wirtualnej możesz dodać ją do grupy.  Następująca procedura dodaje do grupy tożsamość przypisaną przez system maszyny wirtualnej.

1. Pobierz i zanotuj wartość (określoną w polu zwracanych `ObjectID` `Id` wartości) jednostki usługi maszyny wirtualnej:

   ```azurepowershell-interactive
   Get-AzADServicePrincipal -displayname "myVM"
   ```

1. Pobierz i zanotuj `ObjectID` wartość `Id` (określoną w polu zwracanych wartości) grupy:

   ```azurepowershell-interactive
   Get-AzADGroup -searchstring "myGroup"
   ```

1. Dodaj jednostkę usługi maszyny wirtualnej do grupy:

   ```azurepowershell-interactive
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Wyłączanie tożsamości zarządzanej przypisanej przez system z maszyny wirtualnej platformy Azure

Aby wyłączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej, Twoje konto musi mieć przypisanie roli [Współautor maszyny](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) wirtualnej.  Nie są wymagane żadne dodatkowe przypisania roli katalogu usługi Azure AD.

Jeśli masz maszynę wirtualną, która nie wymaga już tożsamości zarządzanej przypisanej przez system, ale nadal wymaga tożsamości zarządzanych przypisanych przez użytkownika, użyj następującego polecenia cmdlet:

1. Pobierz właściwości maszyny wirtualnej przy użyciu `Get-AzVM` polecenia cmdlet i ustaw `-IdentityType` parametr na `UserAssigned` :

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Jeśli masz maszynę wirtualną, która nie wymaga już tożsamości zarządzanej przypisanej przez system i nie ma tożsamości zarządzanych przypisanych przez użytkownika, użyj następujących poleceń:

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przypisaną przez użytkownika z maszyny wirtualnej przy użyciu Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do maszyny wirtualnej podczas tworzenia

Aby przypisać tożsamość przypisaną przez użytkownika do maszyny [](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) wirtualnej, Twoje konto wymaga przypisań ról Współautor maszyny wirtualnej i [Operator](../../role-based-access-control/built-in-roles.md#managed-identity-operator) tożsamości zarządzanej. Nie są wymagane żadne dodatkowe przypisania roli katalogu usługi Azure AD.

1. Zapoznaj się z jednym z następujących przewodników Szybki start maszyny wirtualnej platformy Azure, wykonując tylko niezbędne sekcje ("Zaloguj się do platformy Azure", "Tworzenie grupy zasobów", "Tworzenie grupy sieci", "Tworzenie maszyny wirtualnej").

    Po dojechanie do sekcji "Tworzenie maszyny wirtualnej" należy wprowadzić niewielką modyfikację składni [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) polecenia cmdlet. Dodaj parametry `-IdentityType UserAssigned` i `-IdentityID` , aby aprowizować maszynę wirtualną przy użyciu tożsamości przypisanej przez użytkownika.  Zastąp `<VM NAME>` wartości , , i `<SUBSCRIPTION ID>` `<RESROURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` własnymi wartościami.  Na przykład:

    ```azurepowershell-interactive
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```

    - [Tworzenie maszyny wirtualnej z systemem Windows za pomocą programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md)


### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącej maszyny wirtualnej platformy Azure

Aby przypisać tożsamość przypisaną przez użytkownika do maszyny [](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) wirtualnej, Twoje konto wymaga przypisań ról Współautor maszyny wirtualnej i [Operator](../../role-based-access-control/built-in-roles.md#managed-identity-operator) tożsamości zarządzanej. Nie są wymagane żadne dodatkowe przypisania roli katalogu usługi Azure AD.

1. Utwórz tożsamość zarządzaną przypisaną przez użytkownika przy użyciu polecenia cmdlet [New-AzUserAssignedIdentity.](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity)  `Id`Zanotuj w danych wyjściowych , ponieważ będzie on potrzebny w następnym kroku.

   > [!IMPORTANT]
   > Tworzenie tożsamości zarządzanych przypisanych przez użytkownika obsługuje tylko znaki alfanumeryczne, podkreślenie i łącznik (od 0 do 9, a–z, A–Z \_ lub -). Ponadto nazwa powinna mieć ograniczoną długość od 3 do 128 znaków, aby przypisanie do maszyny wirtualnej/zestawu vmss działało prawidłowo. Aby uzyskać więcej informacji, zobacz [często zadawane pytania i znane problemy](known-issues.md)

   ```azurepowershell-interactive
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
1. Pobierz właściwości maszyny wirtualnej przy użyciu `Get-AzVM` polecenia cmdlet . Następnie, aby przypisać tożsamość zarządzaną przypisaną przez użytkownika do maszyny wirtualnej platformy Azure, użyj przełącznika i polecenia `-IdentityType` `-IdentityID` cmdlet [Update-AzVM.](/powershell/module/az.compute/update-azvm)  Wartość `-IdentityId` parametru to wartość `Id` zanotowana w poprzednim kroku.  Zastąp `<VM NAME>` wartości , , i `<SUBSCRIPTION ID>` `<RESROURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` własnymi wartościami.

   > [!WARNING]
   > Aby zachować wszystkie wcześniej przypisane przez użytkownika tożsamości zarządzane przypisane do maszyny wirtualnej, należy odpytować właściwość obiektu maszyny wirtualnej `Identity` (na przykład `$vm.Identity` ).  Jeśli zostaną zwrócone jakiekolwiek tożsamości zarządzane przypisane przez użytkownika, dołącz je w następującym poleceniu wraz z nową tożsamością zarządzaną przypisaną przez użytkownika, która ma zostać przypisana do maszyny wirtualnej.

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z maszyny wirtualnej platformy Azure

Aby usunąć tożsamość przypisaną przez użytkownika do maszyny wirtualnej, Twoje konto musi mieć przypisanie roli [Współautor maszyny](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) wirtualnej.

Jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przypisanych przez użytkownika, możesz usunąć wszystkie tożsamości oprócz ostatniej przy użyciu następujących poleceń. Upewnij się, że parametry `<RESOURCE GROUP>` i `<VM NAME>` zostały zastąpione własnymi wartościami. To właściwość nazwy przypisanej przez użytkownika `<USER ASSIGNED IDENTITY NAME>` tożsamości zarządzanej, która powinna pozostać na maszynie wirtualnej. Te informacje można znaleźć, odpytując `Identity` właściwość obiektu maszyny wirtualnej.  Na `$vm.Identity` przykład:

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Jeśli maszyna wirtualna nie ma przypisanej przez system tożsamości zarządzanej i chcesz usunąć z niego wszystkie tożsamości zarządzane przypisane przez użytkownika, użyj następującego polecenia:

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Jeśli maszyna wirtualna ma przypisane przez system i przypisane przez użytkownika tożsamości zarządzane, możesz usunąć wszystkie tożsamości zarządzane przypisane przez użytkownika, przełączając się na używanie tylko tożsamości zarządzanych przypisanych przez system.

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Następne kroki

- [Omówienie tożsamości zarządzanych dla zasobów platformy Azure](overview.md)
- Aby uzyskać pełne informacje o przewodnikach Szybki start tworzenia maszyn wirtualnych platformy Azure, zobacz:

  - [Tworzenie maszyny wirtualnej z systemem Windows za pomocą programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
  - [Tworzenie maszyny wirtualnej z systemem Linux za pomocą programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md)
