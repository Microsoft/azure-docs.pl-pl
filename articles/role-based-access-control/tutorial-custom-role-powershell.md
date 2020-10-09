---
title: 'Samouczek: tworzenie roli niestandardowej platformy Azure przy użyciu Azure PowerShell — kontrola RBAC na platformie Azure'
description: Rozpocznij tworzenie roli niestandardowej platformy Azure przy użyciu Azure PowerShell i kontroli dostępu opartej na rolach (RBAC) na platformie Azure w tym samouczku.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.openlocfilehash: 8aedc59fc34278bf84983d78bf0e9a31fe38ee93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82735593"
---
# <a name="tutorial-create-an-azure-custom-role-using-azure-powershell"></a>Samouczek: tworzenie roli niestandardowej platformy Azure przy użyciu Azure PowerShell

Jeśli [wbudowane role platformy Azure](built-in-roles.md) nie są zgodne z konkretnymi potrzebami organizacji, możesz utworzyć własne role niestandardowe. W tym samouczku utworzysz niestandardową rolę o nazwie Reader Support Tickets przy użyciu programu Azure PowerShell. Rola niestandardowa umożliwi użytkownikowi przeglądanie całej zawartości płaszczyzny zarządzania subskrypcją, a także otwieranie biletów pomocy technicznej.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie roli niestandardowej
> * Wyświetlanie ról niestandardowych
> * Aktualizacja roli niestandardowej
> * Usuwanie roli niestandardowej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Uprawnienia do tworzenia ról niestandardowych, takie jak [Właściciel](built-in-roles.md#owner) lub [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator)
- Usługa [Azure Cloud Shell](../cloud-shell/overview.md) lub program [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="sign-in-to-azure-powershell"></a>Logowanie się do programu Azure PowerShell

Zaloguj się do [Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Najprostszym sposobem na utworzenie roli niestandardowej jest rozpoczęcie od wbudowanej roli, poddanie jej edycji, a następnie utworzenie nowej roli.

1. W programie PowerShell użyj polecenia [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation), aby uzyskać listę operacji dla dostawcy zasobów Microsoft.Support. Dobrze jest znać operacje, które są dostępne do tworzenia uprawnień. Można także wyświetlić listę wszystkich operacji w temacie [Operacje dostawcy zasobów usługi Azure Resource Manager](resource-provider-operations.md#microsoftsupport).

    ```azurepowershell
    Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

1. Użyj polecenia [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) w celu wygenerowania roli [Czytelnik](built-in-roles.md#reader) w formacie JSON.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. Otwórz plik **ReaderSupportRole.json** w edytorze.

    Poniżej przedstawiono dane wyjściowe w formacie JSON. Aby uzyskać informacje o różnych właściwościach, zobacz [role niestandardowe platformy Azure](custom-roles.md).

    ```json
    {
      "Name": "Reader",
      "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
      "IsCustom": false,
      "Description": "Lets you view everything, but not make any changes.",
      "Actions": [
        "*/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/"
      ]
    }
    ```
    
1. Edytuj plik JSON, aby dodać operację `"Microsoft.Support/*"` do właściwości `Actions`. Pamiętaj o dodaniu przecinka po operacji odczytu. Ta akcja umożliwia użytkownikowi tworzenie biletów pomocy technicznej.

1. Pobierz identyfikator subskrypcji za pomocą polecenia [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

    ```azurepowershell
    Get-AzSubscription
    ```

1. W elemencie `AssignableScopes` dodaj swój identyfikator subskrypcji w następującym formacie: `"/subscriptions/00000000-0000-0000-0000-000000000000"`

    Musisz jawnie dodać identyfikatory subskrypcji, ponieważ w przeciwnym razie nie będzie można zaimportować roli do subskrypcji.

1. Usuń wiersz właściwości `Id` i zmień właściwość `IsCustom` na `true`.

1. Zmień właściwości `Name` i `Description` na „Reader Support Tickets” i „View everything in the subscription and also open support tickets”.

    Zawartość pliku JSON powinna wyglądać następująco:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. Aby utworzyć nową rolę niestandardową, użyj polecenia [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) i określ plik definicji roli JSON.

    ```azurepowershell
    New-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
        
    Nowa rola niestandardowa jest teraz dostępna w witrynie Azure Portal i można ją przypisywać do użytkowników, grup lub jednostek usług tak samo jak role wbudowane.

## <a name="list-custom-roles"></a>Wyświetlanie ról niestandardowych

- Aby wyświetlić listę wszystkich ról niestandardowych, użyj polecenia [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

    ```azurepowershell
    Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
    ```

    ```Output
    Name                   IsCustom
    ----                   --------
    Reader Support Tickets     True
    ```
    
    Rolę niestandardową można również sprawdzić w witrynie Azure Portal.

    ![zrzut ekranu przedstawiający rolę niestandardową zaimportowaną do witryny Azure Portal](./media/tutorial-custom-role-powershell/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Aktualizacja roli niestandardowej

Aby zaktualizować rolę niestandardową, zaktualizuj plik JSON lub użyj obiektu `PSRoleDefinition`.

1. Aby zaktualizować plik JSON, użyj polecenia [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) w celu wygenerowania roli niestandardowej w formacie JSON.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```

1. Otwórz plik w edytorze.

1. W elemencie `Actions` dodaj operację do tworzenia wdrożeń grupy zasobów i zarządzania nimi `"Microsoft.Resources/deployments/*"`.

    Zaktualizowany plik JSON powinien wyglądać następująco:

    ```json
    {
      "Name": "Reader Support Tickets",
      "Id": "22222222-2222-2222-2222-222222222222",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. Aby zaktualizować rolę niestandardową, użyj polecenia [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) i określ zaktualizowany plik JSON.

    ```azurepowershell
    Set-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```

1. Aby użyć obiektu `PSRoleDefintion` do aktualizacji roli niestandardowej, najpierw użyj polecenia [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) w celu pobrania roli.

    ```azurepowershell
    $role = Get-AzRoleDefinition "Reader Support Tickets"
    ```
    
1. Wywołaj metodę `Add` w celu dodania operacji do odczytu ustawień diagnostycznych.

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. Użyj polecenia [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) w celu zaktualizowania roli.

    ```azurepowershell
    Set-AzRoleDefinition -Role $role
    ```
    
    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*,
                       Microsoft.Insights/diagnosticSettings/*/read}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
    
## <a name="delete-a-custom-role"></a>Usuwanie roli niestandardowej

1. Użyj polecenia [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) w celu uzyskania identyfikatora roli niestandardowej.

    ```azurepowershell
    Get-AzRoleDefinition "Reader Support Tickets"
    ```

1. Użyj polecenia [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) i określ identyfikator roli w celu usunięcia roli niestandardowej.

    ```azurepowershell
    Remove-AzRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. Gdy zostanie wyświetlony monit o potwierdzenie, wpisz **Y** (T).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu Azure PowerShell](custom-roles-powershell.md)
