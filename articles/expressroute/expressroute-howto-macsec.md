---
title: 'Azure ExpressRoute: Konfigurowanie MACsec'
description: Ten artykuł pomaga w konfigurowaniu MACsec w celu zabezpieczenia połączeń między routerami brzegowymi i routerami granicznymi firmy Microsoft.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/22/2019
ms.author: duau
ms.openlocfilehash: 8ee7f42a4b05def7c2239c7a1e5bcef54f6f7e50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89395778"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>Konfigurowanie zabezpieczeń MACsec na portach usługi ExpressRoute Direct

Ten artykuł pomaga w konfigurowaniu MACsec w celu zabezpieczenia połączeń między routerami brzegowymi i routerami brzegowymi firmy Microsoft przy użyciu programu PowerShell.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Przed rozpoczęciem konfiguracji Potwierdź następujące kwestie:

* Rozumiesz, jak [ExpressRoute bezpośrednie inicjowanie przepływów pracy](expressroute-erdirect-about.md).
* Utworzono [zasób portu bezpośredniego ExpressRoute](expressroute-howto-erdirect.md).
* Jeśli chcesz uruchomić program PowerShell lokalnie, sprawdź, czy na komputerze jest zainstalowana najnowsza wersja Azure PowerShell.

### <a name="working-with-azure-powershell"></a>Praca z Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>Zaloguj się i wybierz odpowiednią subskrypcję

Aby rozpocząć konfigurację, zaloguj się do konta platformy Azure i wybierz subskrypcję, której chcesz użyć.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. Utwórz Azure Key Vault, MACsec Secret i tożsamość użytkownika

1. Utwórz wystąpienie Key Vault do przechowywania wpisów tajnych MACsec w nowej grupie zasobów.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    Jeśli masz już Magazyn kluczy lub grupę zasobów, możesz użyć ich ponownie. Należy jednak pamiętać o włączeniu [funkcji **usuwania nietrwałego**](../key-vault/general/soft-delete-overview.md) w istniejącym magazynie kluczy. Jeśli nie włączono usuwania nietrwałego, można użyć następujących poleceń, aby je włączyć:

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. Utwórz tożsamość użytkownika.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    Jeśli New-AzUserAssignedIdentity nie zostanie rozpoznany jako prawidłowe polecenie cmdlet programu PowerShell, zainstaluj następujący moduł (w trybie administratora) i ponownie uruchom powyższe polecenie.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. Utwórz klucz skojarzenia łączności (CAK) i nazwę klucza skojarzenia łączności (CKN) i Zapisz je w magazynie kluczy.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. Przypisz uprawnienie GET do tożsamości użytkownika.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   Teraz Ta tożsamość może pobrać wpisy tajne, na przykład CAK i CKN, z magazynu kluczy.
5. Ustaw tę tożsamość użytkownika, aby była używana przez ExpressRoute.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. Skonfiguruj MACsec na portach Direct ExpressRoute

### <a name="to-enable-macsec"></a>Aby włączyć MACsec

Każde wystąpienie ExpressRoute Direct ma dwa porty fizyczne. Można włączyć opcję Włącz MACsec na obu portach w tym samym czasie lub włączyć MACsec na jednym porcie jednocześnie. Przeprowadzenie jednego portu w czasie (przez przełączenie ruchu do aktywnego portu podczas obsługi innego portu) może pomóc zminimalizować przerwy w działaniu, gdy usługa ExpressRoute Direct jest już w użyciu.

1. Ustaw wpisy tajne MACsec i szyfru i skojarz tożsamość użytkownika z portem, aby kod zarządzania ExpressRoute mógł uzyskać dostęp do wpisów tajnych MACsec w razie potrzeby.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. Obowiązkowe Jeśli porty są w stanie down administracyjne, można uruchomić następujące polecenia, aby wyświetlić porty.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    W tym momencie MACsec jest włączona na portach Direct ExpressRoute po stronie firmy Microsoft. Jeśli nie został on skonfigurowany na urządzeniach brzegowych, możesz kontynuować konfigurację przy użyciu tych samych wpisów tajnych MACsec i szyfrowania.

### <a name="to-disable-macsec"></a>Aby wyłączyć MACsec

Jeśli MACsec nie są już potrzebne w wystąpieniu ExpressRoute Direct, można uruchomić następujące polecenia, aby je wyłączyć.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

W tym momencie MACsec jest wyłączona na portach Direct ExpressRoute po stronie firmy Microsoft.

### <a name="test-connectivity"></a>Testowanie łączności
Po skonfigurowaniu MACsec (łącznie z aktualizacją klucza MACsec) na portach bezpośrednich ExpressRoute [Sprawdź](expressroute-troubleshooting-expressroute-overview.md) , czy sesje protokołu BGP obwodów są uruchomione. Jeśli nie masz jeszcze żadnego obwodu na portach, najpierw utwórz go i skonfiguruj prywatną komunikację równorzędną Azure lub komunikację równorzędną firmy Microsoft. Jeśli MACsec jest błędnie skonfigurowana, w tym niezgodność klucza MACsec między urządzeniami sieciowymi i urządzeniami sieciowymi firmy Microsoft, rozpoznawanie protokołu ARP nie jest widoczne w przypadku warstwy 2 i BGP dla warstwy 3. Jeśli wszystko jest prawidłowo skonfigurowane, trasy protokołu BGP są anonsowane prawidłowo w obu kierunkach i przepływie danych aplikacji odpowiednio do ExpressRoute.

## <a name="next-steps"></a>Następne kroki
1. [Tworzenie obwodu ExpressRoute w usłudze ExpressRoute Direct](expressroute-howto-erdirect.md)
2. [Łączenie obwodu usługi ExpressRoute z siecią wirtualną platformy Azure](expressroute-howto-linkvnet-arm.md)
3. [Weryfikowanie łączności ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
