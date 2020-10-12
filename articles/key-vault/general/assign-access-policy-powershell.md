---
title: Przypisywanie zasad dostępu Azure Key Vault
description: Jak używać Azure Portal, interfejsu wiersza polecenia platformy Azure lub Azure PowerShell do przypisywania zasad dostępu Key Vault do nazwy głównej usługi lub aplikacji.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 817b9bfc0af054b344ca9f770085ac022a8e6eac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89381081"
---
# <a name="assign-a-key-vault-access-policy-using-azure-powershell"></a>Przypisywanie zasad dostępu Key Vault przy użyciu Azure PowerShell

Zasady dostępu Key Vault określają, czy dana jednostka usługi, mianowicie aplikacja lub Grupa użytkowników, może wykonywać różne operacje na Key Vault [kluczach tajnych](../secrets/index.yml), [kluczach](../keys/index.yml)i [certyfikatach](../certificates/index.yml). Zasady dostępu można przypisywać przy użyciu [Azure Portal](assign-access-policy-portal.md), [interfejsu wiersza polecenia platformy Azure](assign-access-policy-cli.md)lub Azure PowerShell (tego artykułu).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Aby uzyskać więcej informacji na temat tworzenia grup w Azure Active Directory przy użyciu Azure PowerShell, zobacz [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) i [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember).

## <a name="configure-powershell-and-sign-in"></a>Skonfiguruj program PowerShell i zaloguj się

1. Aby uruchomić polecenia lokalnie, zainstaluj [Azure PowerShell](/powershell/azure/) , jeśli nie zostało to jeszcze zrobione.

    Aby uruchomić polecenia bezpośrednio w chmurze, użyj [Azure Cloud Shell](/azure/cloud-shell/overview).

1. Tylko lokalny program PowerShell:

    1. Zainstaluj [moduł Azure Active Directory PowerShell](https://www.powershellgallery.com/packages/AzureAD).

    1. Zaloguj się do platformy Azure:

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>Uzyskiwanie identyfikatora obiektu

Określ identyfikator obiektu aplikacji, grupy lub użytkownika, do którego chcesz przypisać zasady dostępu:

- Aplikacje i inne jednostki usługi: Użyj polecenia cmdlet [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) z `-SearchString` parametrem, aby przefiltrować wyniki do nazwy żądanej jednostki usługi:

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- Grupy: Użyj polecenia cmdlet [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) z `-SearchString` parametrem, aby przefiltrować wyniki do nazwy żądanej grupy:

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    W danych wyjściowych identyfikator obiektu jest wyświetlany jako `Id` .

- Użytkownicy: Użyj polecenia cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser) , przekazując adres e-mail użytkownika do `-UserPrincipalName` parametru.

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    W danych wyjściowych identyfikator obiektu jest wyświetlany jako `Id` .

## <a name="assign-the-access-policy"></a>Przypisywanie zasad dostępu

Użyj polecenia cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) , aby przypisać zasady dostępu:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

Wystarczy tylko `-PermissionsToSecrets` , `-PermissionsToKeys` i `-PermissionsToCertificates` podczas przypisywania uprawnień do tych konkretnych typów. Dozwolone wartości dla `<secret-permissions>` , `<key-permissions>` i `<certificate-permissions>` są określone w dokumentacji [Set-AzKeyVaultAccessPolicy-Parameters](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters) .

## <a name="next-steps"></a>Następne kroki

- [Zabezpieczenia Azure Key Vault: Zarządzanie tożsamościami i dostępem](overview-security.md#identity-and-access-management)
- [Zabezpiecz swój magazyn kluczy](secure-your-key-vault.md).
- [Przewodnik dewelopera Azure Key Vault](developers-guide.md)
- [Azure Key Vault najlepszych praktyk](best-practices.md)
