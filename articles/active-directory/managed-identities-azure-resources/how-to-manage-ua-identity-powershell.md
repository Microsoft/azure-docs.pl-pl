---
title: Tworzenie, tworzenie i & tożsamości zarządzanej przypisanej przez użytkownika przy użyciu usługi Azure PowerShell — Azure AD
description: Instrukcje krok po kroku dotyczące tworzenia, tworzenia, tworzenia listy i usuwania tożsamości zarządzanej przypisanej przez użytkownika przy użyciu Azure PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: af76affd9f4034401225e82de4e25e8b0a51125a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784843"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Tworzenie, tworzenie listy lub usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu Azure PowerShell

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure tożsamość zarządzaną w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w usługach, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności poświadczeń w kodzie. 

Z tego artykułu dowiesz się, jak utworzyć, wyświetlić listę i usunąć tożsamość zarządzaną przypisaną przez użytkownika przy użyciu Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz informacji o tożsamościach zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przeglądu](overview.md). Zapoznaj się z różnicą między tożsamością zarządzaną przypisaną przez system i **[przypisaną przez użytkownika.](overview.md#managed-identity-types)**
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby uruchomić przykładowe skrypty, masz dwie opcje:
    - Użyj [Azure Cloud Shell](../../cloud-shell/overview.md), którą możesz otworzyć przy użyciu **przycisku Wypróbuj** w prawym górnym rogu bloków kodu.
    - Uruchamiaj skrypty lokalnie Azure PowerShell, zgodnie z opisem w następnej sekcji.

### <a name="configure-azure-powershell-locally"></a>Konfigurowanie Azure PowerShell lokalnego

Aby użyć Azure PowerShell w tym artykule (zamiast używać Cloud Shell), wykonaj następujące kroki:

1. Zainstaluj [najnowszą wersję Azure PowerShell,](/powershell/azure/install-az-ps) jeśli jeszcze nie zostało to jeszcze zainstalowane.

1. Zaloguj się do platformy Azure:

    ```azurepowershell
    Connect-AzAccount
    ```

1. Zainstaluj [najnowszą wersję modułu PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    Po uruchomieniu tego polecenia w następnym kroku może być konieczne poza bieżącą `Exit` sesją programu PowerShell.

1. Zainstaluj wersję wstępną modułu, aby wykonać operacje na tożsamości zarządzanej przypisane przez `Az.ManagedServiceIdentity` użytkownika w tym artykule:

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

Aby utworzyć tożsamość zarządzaną przypisaną przez użytkownika, Twoje konto musi mieć przypisanie roli [Współautor tożsamości zarządzanej.](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)

Aby utworzyć tożsamość zarządzaną przypisaną przez użytkownika, użyj `New-AzUserAssignedIdentity` polecenia . Parametr określa grupę zasobów, w której ma zostać utworzyć tożsamość zarządzaną przypisaną przez użytkownika, a `ResourceGroupName` parametr określa jej `-Name` nazwę. Zastąp wartości `<RESOURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` parametrów i własnymi wartościami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Lista tożsamości zarządzanych przypisanych przez użytkownika

Aby wyświetlić/odczytać tożsamość zarządzaną przypisaną przez użytkownika, Twoje konto wymaga przypisania roli [Współautor](../../role-based-access-control/built-in-roles.md#managed-identity-operator) tożsamości zarządzanej lub Operator tożsamości [zarządzanej.](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)

Aby wyświetlić listę tożsamości zarządzanych przypisanych przez użytkownika, użyj polecenia [Get-AzUserAssigned].  Parametr `-ResourceGroupName` określa grupę zasobów, w której utworzono tożsamość zarządzaną przypisaną przez użytkownika. Zastąp `<RESOURCE GROUP>` wartość własną wartością:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
W odpowiedzi tożsamości zarządzane przypisane przez użytkownika mają zwracaną wartość `"Microsoft.ManagedIdentity/userAssignedIdentities"` klucza , `Type` .

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika

Aby usunąć tożsamość zarządzaną przypisaną przez użytkownika, Twoje konto musi mieć przypisanie roli [Współautor tożsamości zarządzanej.](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)

Aby usunąć tożsamość zarządzaną przypisaną przez użytkownika, użyj `Remove-AzUserAssignedIdentity` polecenia .  Parametr określa grupę zasobów, w której utworzono tożsamość przypisaną przez użytkownika, a `-ResourceGroupName` parametr określa jej `-Name` nazwę. Zastąp `<RESOURCE GROUP>` wartości parametrów `<USER ASSIGNED IDENTITY NAME>` i własnymi wartościami:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Usunięcie tożsamości zarządzanej przypisanej przez użytkownika nie spowoduje usunięcia odwołania z żadnego zasobu, do którym została przypisana. Przypisania tożsamości należy usunąć oddzielnie.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pełną listę i więcej szczegółów Azure PowerShell tożsamości zarządzanych dla poleceń zasobów platformy Azure, zobacz [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).
