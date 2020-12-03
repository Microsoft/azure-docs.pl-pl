---
title: Tworzenie, lista & usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu usługi Azure PowerShell — Azure AD
description: Instrukcje krok po kroku dotyczące tworzenia, wyświetlania i usuwania tożsamości zarządzanej przypisanej przez użytkownika przy użyciu Azure PowerShell.
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
ms.openlocfilehash: 636f40f0c425c25dfe7f41f1f404afc90ed5ba56
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548226"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Tworzenie, wyświetlanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu Azure PowerShell

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z zarządzaną tożsamością w Azure Active Directory. Ta tożsamość służy do uwierzytelniania w usługach, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności podania poświadczeń w kodzie. 

Ten artykuł zawiera informacje na temat tworzenia, wyświetlania i usuwania tożsamości zarządzanej przypisanej przez użytkownika przy użyciu Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md). **Pamiętaj, aby zapoznać się z [różnicą między przypisaną przez system i tożsamością zarządzaną przez użytkownika](overview.md#managed-identity-types)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do uruchomienia przykładowych skryptów są dostępne dwie opcje:
    - Użyj [Azure Cloud Shell](../../cloud-shell/overview.md), którą można otworzyć za pomocą przycisku **Wypróbuj** w prawym górnym rogu bloków kodu.
    - Uruchom skrypty lokalnie przy użyciu Azure PowerShell, zgodnie z opisem w następnej sekcji.

### <a name="configure-azure-powershell-locally"></a>Skonfiguruj lokalnie Azure PowerShell

Aby używać Azure PowerShell lokalnie w tym artykule (zamiast używać Cloud Shell), wykonaj następujące czynności:

1. Zainstaluj [najnowszą wersję programu Azure PowerShell](/powershell/azure/install-az-ps) , jeśli jeszcze tego nie zrobiono.

1. Zaloguj się do platformy Azure:

    ```azurepowershell
    Connect-AzAccount
    ```

1. Zainstaluj [najnowszą wersję modułu PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    `Exit`Po uruchomieniu tego polecenia w następnym kroku może być konieczne wyjście z bieżącej sesji programu PowerShell.

1. Zainstaluj wersję wstępną `Az.ManagedServiceIdentity` modułu, aby wykonać operacje zarządzanej tożsamości przypisane przez użytkownika w tym artykule:

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

Do utworzenia tożsamości zarządzanej przypisanej przez użytkownika konto wymaga przypisania roli [współautor zarządzanej tożsamości](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Aby utworzyć tożsamość zarządzaną przypisaną przez użytkownika, użyj `New-AzUserAssignedIdentity` polecenia. `ResourceGroupName`Parametr określa grupę zasobów, w której ma zostać utworzona tożsamość zarządzana przypisana przez użytkownika, a `-Name` parametr określa nazwę. Zastąp `<RESOURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` wartości parametrów i własnymi wartościami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Wyświetlanie tożsamości zarządzanych przypisanych przez użytkownika

Aby wyświetlić/odczytać tożsamość zarządzaną przypisaną przez użytkownika, Twoje konto wymaga [operatora zarządzanej tożsamości](../../role-based-access-control/built-in-roles.md#managed-identity-operator) lub przypisania roli [współautor zarządzanej tożsamości](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Aby wyświetlić tożsamości zarządzane przypisane przez użytkownika, użyj polecenia [Get-AzUserAssigned].  `-ResourceGroupName`Parametr określa grupę zasobów, w której utworzono tożsamość zarządzaną przez użytkownika. Zamień na `<RESOURCE GROUP>` własną wartość:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
W odpowiedzi tożsamości zarządzane przypisane przez użytkownika mają `"Microsoft.ManagedIdentity/userAssignedIdentities"` wartość zwróconą dla klucza, `Type` .

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika

Aby usunąć tożsamość zarządzaną przypisaną przez użytkownika, Twoje konto wymaga przypisania roli [współautor zarządzanej tożsamości](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Aby usunąć tożsamość zarządzaną przypisaną przez użytkownika, użyj `Remove-AzUserAssignedIdentity` polecenia.  `-ResourceGroupName`Parametr określa grupę zasobów, w której utworzono tożsamość przypisaną przez użytkownika, a `-Name` parametr określa jej nazwę. Zastąp `<RESOURCE GROUP>` wartości i `<USER ASSIGNED IDENTITY NAME>` wartościami parametrów własnymi wartościami:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Usunięcie tożsamości zarządzanej przypisanej przez użytkownika nie spowoduje usunięcia odwołania z dowolnego zasobu, do którego została przypisana. Przypisania tożsamości muszą zostać usunięte osobno.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pełną listę i więcej szczegółów Azure PowerShell zarządzanych tożsamości dla zasobów platformy Azure, zobacz [AZ. ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).
