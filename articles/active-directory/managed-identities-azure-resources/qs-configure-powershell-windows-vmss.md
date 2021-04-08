---
title: Konfigurowanie zarządzanych tożsamości w zestawach skalowania maszyn wirtualnych przy użyciu programu PowerShell — Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych przez system i użytkownika w zestawie skalowania maszyn wirtualnych przy użyciu programu PowerShell.
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
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd7ec1f6cdfc8ecfe32de04f5d06b42f9492b88c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590906"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure w zestawach skalowania maszyn wirtualnych przy użyciu programu PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule, korzystając z programu PowerShell, dowiesz się, jak wykonywać zarządzane tożsamości dla operacji zasobów platformy Azure na zestawie skalowania maszyn wirtualnych:

- Włączanie i wyłączanie zarządzanej tożsamości przypisanej przez system w zestawie skalowania maszyn wirtualnych
- Dodawanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika w zestawie skalowania maszyn wirtualnych

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md). **Pamiętaj, aby zapoznać się z [różnicą między przypisaną przez system i zarządzaną tożsamością użytkownika](overview.md#managed-identity-types)**.

- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

- Aby móc wykonywać operacje zarządzania w tym artykule, Twoje konto musi mieć następujące przypisania kontroli dostępu oparte na rolach na platformie Azure:

    > [!NOTE]
    > Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

    - [Współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) w celu utworzenia zestawu skalowania maszyn wirtualnych oraz włączenia i usunięcia zarządzanej tożsamości przypisanej przez system i/lub użytkownika z zestawu skalowania maszyn wirtualnych.
    - Rola [współautor tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) do tworzenia tożsamości zarządzanej przypisanej przez użytkownika.
    - Rola [operatora tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-operator) do przypisywania i usuwania tożsamości zarządzanej przypisanej przez użytkownika z i do zestawu skalowania maszyn wirtualnych.

- Do uruchomienia przykładowych skryptów są dostępne dwie opcje:
    - Użyj [Azure Cloud Shell](../../cloud-shell/overview.md), którą można otworzyć za pomocą przycisku **Wypróbuj** w prawym górnym rogu bloków kodu.
    - Uruchom skrypty lokalnie, instalując najnowszą wersję [Azure PowerShell](/powershell/azure/install-az-ps), a następnie zaloguj się do platformy Azure przy użyciu `Connect-AzAccount` . 

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

W tej sekcji dowiesz się, jak włączyć i usunąć tożsamość zarządzaną przypisaną przez system przy użyciu Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Włącz tożsamość zarządzaną przypisaną przez system podczas tworzenia zestawu skalowania maszyn wirtualnych platformy Azure

Aby utworzyć zestaw skalowania maszyn wirtualnych z włączoną tożsamością zarządzaną przypisaną przez system:

1. Zapoznaj się z *przykładem 1* w artykule dotyczącym polecenia cmdlet [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) , aby utworzyć zestaw skalowania maszyn wirtualnych z tożsamością zarządzaną przypisaną przez system.  Dodaj parametr `-IdentityType SystemAssigned` do `New-AzVmssConfig` polecenia cmdlet:

    ```azurepowershell-interactive
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Włącz tożsamość zarządzaną przypisaną przez system w istniejącym zestawie skalowania maszyn wirtualnych platformy Azure

Jeśli musisz włączyć tożsamość zarządzaną przypisaną przez system w istniejącym zestawie skalowania maszyn wirtualnych platformy Azure:

1. Upewnij się, że konto platformy Azure, którego używasz, należy do roli zapewniającej uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych, na przykład "Współautor maszyny wirtualnej".
   
1. Pobierz właściwości zestawu skalowania maszyn wirtualnych przy użyciu [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) polecenia cmdlet. Następnie w celu włączenia zarządzanej tożsamości przypisanej do systemu Użyj `-IdentityType` przełącznika w poleceniu cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) :

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Wyłącz tożsamość zarządzaną przypisaną przez system z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli masz zestaw skalowania maszyn wirtualnych, który nie potrzebuje już tożsamości zarządzanej przypisanej do systemu, ale nadal wymaga tożsamości zarządzanej przez użytkownika, użyj następującego polecenia cmdlet:

1. Upewnij się, że Twoje konto należy do roli zapewniającej uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych, na przykład "Współautor maszyny wirtualnej".

1. Uruchom następujące polecenie cmdlet:

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

1. Jeśli masz zestaw skalowania maszyn wirtualnych, który nie wymaga już tożsamości zarządzanej przypisanej do systemu i nie ma tożsamości zarządzanej przypisanej przez użytkownika, użyj następującego polecenia:

    ```azurepowershell-interactive
    Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
    ```
    
## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przez użytkownika z zestawu skalowania maszyn wirtualnych przy użyciu Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia zestawu skalowania maszyn wirtualnych platformy Azure

Tworzenie nowego zestawu skalowania maszyn wirtualnych za pomocą tożsamości zarządzanej przypisanej przez użytkownika nie jest obecnie obsługiwane przez program PowerShell. Zapoznaj się z następną sekcją dodawania tożsamości zarządzanej przypisanej przez użytkownika do istniejącego zestawu skalowania maszyn wirtualnych. Wracaj tutaj, aby zapoznać się z aktualizacjami.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącego zestawu skalowania maszyn wirtualnych platformy Azure

Aby przypisać tożsamość zarządzaną przypisaną przez użytkownika do istniejącego zestawu skalowania maszyn wirtualnych platformy Azure:

1. Upewnij się, że Twoje konto należy do roli zapewniającej uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych, na przykład "Współautor maszyny wirtualnej".

1. Pobierz właściwości zestawu skalowania maszyn wirtualnych przy użyciu `Get-AzVM` polecenia cmdlet. Następnie do przypisywania tożsamości zarządzanej przypisanej przez użytkownika do zestawu skalowania maszyn wirtualnych `-IdentityType` Użyj `-IdentityID` przełącznika i w poleceniu cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) . Zamień `<VM NAME>` , `<SUBSCRIPTION ID>` ,,, `<RESROURCE GROUP>` `<USER ASSIGNED ID1>` `USER ASSIGNED ID2` z własnymi wartościami.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli zestaw skalowania maszyn wirtualnych ma wiele tożsamości zarządzanych przez użytkownika, można usunąć wszystkie oprócz ostatniego z nich przy użyciu następujących poleceń. Upewnij się, że parametry `<RESOURCE GROUP>` i `<VIRTUAL MACHINE SCALE SET NAME>` zostały zastąpione własnymi wartościami. `<USER ASSIGNED IDENTITY NAME>`Jest właściwością nazwa tożsamości zarządzanej przypisanej przez użytkownika, która powinna pozostać w zestawie skalowania maszyn wirtualnych. Te informacje można znaleźć w sekcji tożsamość zestawu skalowania maszyn wirtualnych przy użyciu `az vmss show` :

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Jeśli zestaw skalowania maszyn wirtualnych nie ma tożsamości zarządzanej przypisanej do systemu i chcesz usunąć wszystkie tożsamości zarządzane przypisane przez użytkownika, użyj następującego polecenia:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Jeśli zestaw skalowania maszyn wirtualnych ma zarządzane tożsamości przypisane do systemu i przypisane przez użytkownika, można usunąć wszystkie zarządzane tożsamości przypisane przez użytkownika, przełączając do korzystania tylko z tożsamości zarządzanej przypisanej do systemu.

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie tożsamościami dla zasobów platformy Azure — omówienie](overview.md)
- Aby zapoznać się z pełnymi przewodnikami szybki start tworzenia maszyn wirtualnych platformy Azure, zobacz:
  
  - [Tworzenie maszyny wirtualnej z systemem Windows za pomocą programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Tworzenie maszyny wirtualnej z systemem Linux za pomocą programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md)
