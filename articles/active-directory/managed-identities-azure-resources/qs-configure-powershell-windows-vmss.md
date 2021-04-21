---
title: Konfigurowanie tożsamości zarządzanych w zestawach skalowania maszyn wirtualnych przy użyciu programu PowerShell — Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych przypisanych przez system i użytkowników w zestawie skalowania maszyn wirtualnych przy użyciu programu PowerShell.
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eb651df2fe3b9771154f9e188d41c660f7ffd028
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774145"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure w zestawach skalowania maszyn wirtualnych przy użyciu programu PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez konieczności posiadania poświadczeń w kodzie. 

Z tego artykułu, korzystając z programu PowerShell, dowiesz się, jak wykonywać operacje tożsamości zarządzanych dla zasobów platformy Azure w zestawie skalowania maszyn wirtualnych:

- Włączanie i wyłączanie przypisanej przez system tożsamości zarządzanej w zestawie skalowania maszyn wirtualnych
- Dodawanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika w zestawie skalowania maszyn wirtualnych

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz informacji o tożsamościach zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przeglądu](overview.md). Zapoznaj się z różnicą między tożsamością przypisaną przez system a tożsamością **[przypisaną przez użytkownika.](overview.md#managed-identity-types)**

- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

- Aby wykonać operacje zarządzania w tym artykule, Twoje konto musi mieć następujące przypisania kontroli dostępu na podstawie ról platformy Azure:

    > [!NOTE]
    > Nie są wymagane żadne dodatkowe przypisania roli katalogu usługi Azure AD.

    - [Współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) do tworzenia zestawu skalowania maszyn wirtualnych oraz włączania i usuwania tożsamości zarządzanej przypisanej przez system i/lub przypisanej przez użytkownika tożsamości zarządzanej z zestawu skalowania maszyn wirtualnych.
    - [Rola Współautor tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) do tworzenia tożsamości zarządzanej przypisanej przez użytkownika.
    - [Rola operatora tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-operator) do przypisywania i usuwania tożsamości zarządzanej przypisanej przez użytkownika z i do zestawu skalowania maszyn wirtualnych.

- Aby uruchomić przykładowe skrypty, masz dwie opcje:
    - Użyj [Azure Cloud Shell](../../cloud-shell/overview.md), którą możesz otworzyć przy użyciu **przycisku Wypróbuj** w prawym górnym rogu bloków kodu.
    - Uruchom skrypty lokalnie, instalując najnowszą wersję programu Azure PowerShell , [a](/powershell/azure/install-az-ps)następnie zaloguj się do platformy Azure przy użyciu polecenia `Connect-AzAccount` . 

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

W tej sekcji dowiesz się, jak włączyć i usunąć tożsamość zarządzaną przypisaną przez system przy użyciu Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Włączanie tożsamości zarządzanej przypisanej przez system podczas tworzenia zestawu skalowania maszyn wirtualnych platformy Azure

Aby utworzyć zestaw skalowania maszyn wirtualnych z włączoną tożsamością zarządzaną przypisaną przez system:

1. Zapoznaj się *z przykładem 1* w artykule z odwołaniem do polecenia cmdlet [New-AzVmssConfig,](/powershell/module/az.compute/new-azvmssconfig) aby utworzyć zestaw skalowania maszyn wirtualnych z tożsamością zarządzaną przypisaną przez system.  Dodaj parametr `-IdentityType SystemAssigned` do `New-AzVmssConfig` polecenia cmdlet :

    ```azurepowershell-interactive
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Włączanie tożsamości zarządzanej przypisanej przez system w istniejącym zestawie skalowania maszyn wirtualnych platformy Azure

Jeśli musisz włączyć tożsamość zarządzaną przypisaną przez system w istniejącym zestawie skalowania maszyn wirtualnych platformy Azure:

1. Upewnij się, że konto platformy Azure, którego używasz, należy do roli, która zapewnia uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych, taką jak "Współautor maszyny wirtualnej".
   
1. Pobierz właściwości zestawu skalowania maszyn wirtualnych za pomocą [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) polecenia cmdlet . Następnie, aby włączyć tożsamość zarządzaną przypisaną przez `-IdentityType` system, użyj przełącznika polecenia cmdlet [Update-AzVmss:](/powershell/module/az.compute/update-azvmss)

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Wyłączanie tożsamości zarządzanej przypisanej przez system z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli masz zestaw skalowania maszyn wirtualnych, który nie wymaga już tożsamości zarządzanej przypisanej przez system, ale nadal wymaga tożsamości zarządzanych przypisanych przez użytkownika, użyj następującego polecenia cmdlet:

1. Upewnij się, że Twoje konto należy do roli, która zapewnia uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych, taką jak "Współautor maszyny wirtualnej".

1. Uruchom następujące polecenie cmdlet:

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

1. Jeśli masz zestaw skalowania maszyn wirtualnych, który nie wymaga już tożsamości zarządzanej przypisanej przez system i nie ma tożsamości zarządzanych przypisanych przez użytkownika, użyj następującego polecenia:

    ```azurepowershell-interactive
    Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
    ```
    
## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przypisaną przez użytkownika z zestawu skalowania maszyn wirtualnych przy użyciu Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia zestawu skalowania maszyn wirtualnych platformy Azure

Tworzenie nowego zestawu skalowania maszyn wirtualnych przy użyciu tożsamości zarządzanej przypisanej przez użytkownika nie jest obecnie obsługiwane za pośrednictwem programu PowerShell. Zobacz następną sekcję na temat dodawania tożsamości zarządzanej przypisanej przez użytkownika do istniejącego zestawu skalowania maszyn wirtualnych. Wracaj tutaj, aby zapoznać się z aktualizacjami.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącego zestawu skalowania maszyn wirtualnych platformy Azure

Aby przypisać tożsamość zarządzaną przypisaną przez użytkownika do istniejącego zestawu skalowania maszyn wirtualnych platformy Azure:

1. Upewnij się, że Twoje konto należy do roli, która zapewnia uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych, takie jak "Współautor maszyny wirtualnej".

1. Pobierz właściwości zestawu skalowania maszyn wirtualnych za pomocą `Get-AzVM` polecenia cmdlet . Następnie, aby przypisać tożsamość zarządzaną przypisaną przez użytkownika do zestawu skalowania maszyn wirtualnych, użyj przełącznika i polecenia `-IdentityType` `-IdentityID` cmdlet [Update-AzVmss.](/powershell/module/az.compute/update-azvmss) Zastąp `<VM NAME>` wartości , , , `<SUBSCRIPTION ID>` `<RESROURCE GROUP>` `<USER ASSIGNED ID1>` `USER ASSIGNED ID2` własnymi wartościami.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli zestaw skalowania maszyn wirtualnych ma wiele tożsamości zarządzanych przypisanych przez użytkownika, możesz usunąć wszystkie elementy oprócz ostatniej przy użyciu następujących poleceń. Upewnij się, że parametry `<RESOURCE GROUP>` i `<VIRTUAL MACHINE SCALE SET NAME>` zostały zastąpione własnymi wartościami. To właściwość nazwy tożsamości zarządzanej przypisanej przez użytkownika, która powinna pozostać `<USER ASSIGNED IDENTITY NAME>` w zestawie skalowania maszyn wirtualnych. Te informacje można znaleźć w sekcji tożsamości zestawu skalowania maszyn wirtualnych przy użyciu narzędzia `az vmss show` :

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Jeśli zestaw skalowania maszyn wirtualnych nie ma przypisanej przez system tożsamości zarządzanej i chcesz usunąć z niego wszystkie tożsamości zarządzane przypisane przez użytkownika, użyj następującego polecenia:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Jeśli zestaw skalowania maszyn wirtualnych ma przypisane przez system i przypisane przez użytkownika tożsamości zarządzane, możesz usunąć wszystkie tożsamości zarządzane przypisane przez użytkownika, przełączając się na używanie tylko tożsamości zarządzanej przypisanej przez system.

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Następne kroki

- [Omówienie tożsamości zarządzanych dla zasobów platformy Azure](overview.md)
- Aby uzyskać pełne informacje o przewodnikach Szybki start tworzenia maszyn wirtualnych platformy Azure, zobacz:
  
  - [Tworzenie maszyny wirtualnej z systemem Windows za pomocą programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Tworzenie maszyny wirtualnej z systemem Linux za pomocą programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md)
