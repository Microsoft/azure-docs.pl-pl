---
title: 'Azure ExpressRoute: Konfigurowanie macsec'
description: Ten artykuł ułatwia skonfigurowanie macsec do zabezpieczania połączeń między routerami brzegowymi a routerami brzegowymi firmy Microsoft.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: cherylmc
ms.openlocfilehash: 572147ca43e9a4dea9d9601dfa1dac8ba1c97ed0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458236"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>Konfigurowanie macsec na portach Direct usługi ExpressRoute

Ten artykuł ułatwia skonfigurowanie programu MACsec w celu zabezpieczenia połączeń między routerami brzegowymi a routerami brzegowymi firmy Microsoft przy użyciu programu PowerShell.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji upewnij się, że:

* Rozumiesz [przepływy pracy inicjowania obsługi administracyjnej usługi ExpressRoute Direct](expressroute-erdirect-about.md).
* Utworzono [zasób portu Direct usługi ExpressRoute](expressroute-howto-erdirect.md).
* Jeśli chcesz uruchomić program PowerShell lokalnie, sprawdź, czy na komputerze jest zainstalowana najnowsza wersja programu Azure PowerShell.

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>Zaloguj się i wybierz odpowiednią subskrypcję

Aby rozpocząć konfigurację, zaloguj się do konta platformy Azure i wybierz subskrypcję, której chcesz użyć.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. Tworzenie usługi Azure Key Vault, wpisów tajnych MACsec i tożsamości użytkownika

1. Utwórz wystąpienie usługi Key Vault do przechowywania wpisów tajnych MACsec w nowej grupie zasobów.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    Jeśli masz już magazyn kluczy lub grupę zasobów, możesz ich użyć ponownie. Ważne jest jednak, aby włączyć funkcję [ **usuwania nietrwałego** ](../key-vault/general/overview-soft-delete.md) w istniejącym magazynie kluczy. Jeśli usuwanie nie jest włączone, można użyć następujących poleceń, aby go włączyć:

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. Tworzenie tożsamości użytkownika.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    Jeśli polecenie cmdlet programu PowerShell nie jest rozpoznawane jako prawidłowe polecenie cmdlet programu PowerShell, zainstaluj następujący moduł (w trybie administratora) i uruchom ponownie powyższe polecenie.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. Utwórz klucz skojarzenia łączności (CAK) i nazwę klucza skojarzenia łączności (CKN) i przechowuj je w magazynie kluczy.

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

   Teraz ta tożsamość może uzyskać wpisy tajne, na przykład CAK i CKN, z magazynu kluczy.
5. Ustaw tę tożsamość użytkownika do użycia przez program ExpressRoute.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. Konfigurowanie macsec na portach Direct usługi ExpressRoute

### <a name="to-enable-macsec"></a>Aby włączyć macsec

Każde wystąpienie Direct usługi ExpressRoute ma dwa porty fizyczne. Można włączyć macsec na obu portach w tym samym czasie lub włączyć MACsec na jednym porcie naraz. Wykonanie jednego portu naraz (przez przełączenie ruchu na aktywny port podczas obsługi drugiego portu) może pomóc zminimalizować przerwę, jeśli usługa Direct usługi ExpressRoute Direct jest już w eksploatacji.

1. Ustaw wpisy tajne i szyfrowanie MACsec i skojarz tożsamość użytkownika z portem, aby kod zarządzania usługi ExpressRoute mógł w razie potrzeby uzyskać dostęp do wpisów tajnych MACsec.

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
2. (Opcjonalnie) Jeśli porty są w stanie W dół administracyjny, można uruchomić następujące polecenia, aby przywołać porty.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    W tym momencie macsec jest włączona na portach Direct usługi ExpressRoute po stronie firmy Microsoft. Jeśli nie skonfigurowano go na urządzeniach brzegowych, można przystąpić do konfigurowania ich z tymi samymi wpisami tajnymi i szyfrowaniem MACsec.

### <a name="to-disable-macsec"></a>Aby wyłączyć macsec

Jeśli macsec nie jest już pożądane w wystąpieniu Direct usługi ExpressRoute Direct, można uruchomić następujące polecenia, aby go wyłączyć.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

W tym momencie MACsec jest wyłączona na portach Direct usługi ExpressRoute po stronie firmy Microsoft.

### <a name="test-connectivity"></a>Testowanie łączności
Po skonfigurowaniu protokołu MACsec (w tym aktualizacji klucza MACsec) na portach Direct usługi ExpressRoute [sprawdź,](expressroute-troubleshooting-expressroute-overview.md) czy sesje protokołu BGP obwodów są uruchomione. Jeśli nie masz jeszcze żadnego obwodu na portach, utwórz najpierw jeden i skonfiguruj prywatną komunikację równorzędnych platformy Azure lub komunikację równorzędnej firmy Microsoft obwodu. Jeśli macsec jest nieprawidłowo skonfigurowany, w tym macsec niezgodność klucza, między urządzeniami sieciowymi i urządzeń sieciowych firmy Microsoft, nie będzie widać rozdzielczość ARP w warstwie 2 i BGP ustanowienia w warstwie 3. Jeśli wszystko jest poprawnie skonfigurowane, powinny być wyświetlane trasy BGP anonsowane poprawnie w obu kierunkach i przepływ danych aplikacji odpowiednio za pomocą usługi ExpressRoute.

## <a name="next-steps"></a>Następne kroki
1. [Tworzenie obwodu usługi ExpressRoute w ujmowanej bezpośrednio w użyczyeniu usługi ExpressRoute Direct](expressroute-howto-erdirect.md)
2. [Łączenie obwodu usługi ExpressRoute z siecią wirtualną platformy Azure](expressroute-howto-linkvnet-arm.md)
3. [Weryfikowanie łączności usługi ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
