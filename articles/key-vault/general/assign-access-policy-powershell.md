---
title: Przypisywanie zasad Azure Key Vault dostępu
description: Jak używać interfejsu Azure Portal, interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby przypisać Key Vault dostępu do podmiotu zabezpieczeń lub tożsamości aplikacji.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 1c7c31f38d6a59f4ded17e1e1fd7e985ce59922a
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751422"
---
# <a name="assign-a-key-vault-access-policy-using-azure-powershell"></a>Przypisywanie zasad Key Vault dostępu przy użyciu Azure PowerShell

Zasady Key Vault [określają,](../secrets/index.yml)czy dany podmiot zabezpieczeń, czyli użytkownik, aplikacja lub grupa użytkowników, może wykonywać różne operacje na wpisach tajnych Key Vault [,](../keys/index.yml)kluczach i [certyfikatach.](../certificates/index.yml) Zasady dostępu można przypisywać przy użyciu Azure Portal [,](assign-access-policy-portal.md)interfejsu wiersza polecenia platformy [Azure](assign-access-policy-cli.md)lub Azure PowerShell (ten artykuł).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Aby uzyskać więcej informacji na temat tworzenia grup w Azure Active Directory przy użyciu Azure PowerShell, zobacz [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) i [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember).

## <a name="configure-powershell-and-sign-in"></a>Konfigurowanie programu PowerShell i logowanie

1. Aby uruchamiać polecenia lokalnie, [Azure PowerShell,](/powershell/azure/) jeśli nie zostało to jeszcze zainstalowane.

    Aby uruchamiać polecenia bezpośrednio w chmurze, użyj [polecenia Azure Cloud Shell](../../cloud-shell/overview.md).

1. Tylko lokalny program PowerShell:

    1. Zainstaluj moduł [Azure Active Directory PowerShell.](https://www.powershellgallery.com/packages/AzureAD)

    1. Zaloguj się do platformy Azure:

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>Uzyskanie identyfikatora obiektu

Określ identyfikator obiektu aplikacji, grupy lub użytkownika, do którego chcesz przypisać zasady dostępu:

- Aplikacje i inne jednostki usługi: użyj polecenia cmdlet [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) z parametrem , aby filtrować wyniki do nazwy żądanej `-SearchString` jednostki usługi:

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- Grupy: użyj polecenia cmdlet [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) z parametrem , aby filtrować wyniki do `-SearchString` nazwy żądanej grupy:

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    W danych wyjściowych identyfikator obiektu jest wymieniony jako `Id` .

- Użytkownicy: użyj polecenia cmdlet [Get-AzADUser,](/powershell/module/az.resources/get-azaduser) przekazując adres e-mail użytkownika do `-UserPrincipalName` parametru .

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    W danych wyjściowych identyfikator obiektu jest wymieniony jako `Id` .

## <a name="assign-the-access-policy"></a>Przypisywanie zasad dostępu

Użyj polecenia cmdlet [Set-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) aby przypisać zasady dostępu:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

Podczas przypisywania uprawnień do tych konkretnych typów należy uwzględnić tylko `-PermissionsToSecrets` `-PermissionsToKeys` wartości , i `-PermissionsToCertificates` . Dopuszczalne wartości parametrów , i znajdują się w `<secret-permissions>` `<key-permissions>` `<certificate-permissions>` dokumentacji [polecenia Set-AzKeyVaultAccessPolicy -](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters) Parameters.

## <a name="next-steps"></a>Następne kroki

- [Azure Key Vault zabezpieczeń: Zarządzanie tożsamościami i dostępem](security-overview.md#identity-management)
- [Zabezpiecz magazyn kluczy.](security-overview.md)
- [Azure Key Vault dewelopera](developers-guide.md)