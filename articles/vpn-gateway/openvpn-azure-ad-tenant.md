---
title: 'Brama sieci VPN: dzierżawa usługi Azure AD dla połączeń sieci VPN P2S: uwierzytelnianie usługi Azure AD'
description: Za pomocą sieci VPN P2S można łączyć się z siecią wirtualną przy użyciu uwierzytelniania usługi Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: alzam
ms.openlocfilehash: 00db2ed05285a1637414aa1e3adbe3b047ff0568
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641344"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Tworzenie dzierżawy usługi Azure Active Directory dla połączeń protokołów OpenVPN P2S

Podczas łączenia się z siecią wirtualną można użyć uwierzytelniania opartego na certyfikatach lub uwierzytelniania RADIUS. Jednak podczas korzystania z protokołu Open VPN można również użyć uwierzytelniania usługi Azure Active Directory. Ten artykuł ułatwia konfigurowanie dzierżawy usługi Azure AD dla uwierzytelniania sieci VPN otwartej usługi P2S.

> [!NOTE]
> Uwierzytelnianie usługi Azure AD jest obsługiwane tylko dla połączeń protokołu OpenVPN®.
>


## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. Weryfikowanie dzierżawy usługi Azure AD

Sprawdź, czy masz dzierżawę usługi Azure AD. Jeśli nie masz dzierżawy usługi Azure AD, możesz ją utworzyć, wykonując kroki opisane w artykule [Tworzenie nowej dzierżawy:](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Nazwa organizacji
* Początkowa nazwa domeny

Przykład:

   ![Nowa dzierżawa usługi Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Tworzenie użytkowników dzierżawy usługi Azure AD

Dzierżawa usługi Azure AD potrzebuje następujących kont: konta administratora globalnego i głównego konta użytkownika. Główne konto użytkownika jest używane jako główne konto osadzania (konto usługi). Podczas tworzenia konta użytkownika dzierżawy usługi Azure AD można dostosować rolę katalogu dla typu użytkownika, który chcesz utworzyć.

Użyj kroków w [tym artykule,](../active-directory/fundamentals/add-users-azure-active-directory.md) aby utworzyć co najmniej dwóch użytkowników dla dzierżawy usługi Azure AD. Pamiętaj, aby zmienić **rolę katalogu,** aby utworzyć typy kont:

* Administrator globalny
* Użytkownik

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Włącz uwierzytelnianie usługi Azure AD w bramie sieci VPN

1. Znajdź identyfikator katalogu, którego chcesz użyć do uwierzytelniania. Jest on wymieniony w sekcji właściwości na stronie usługi Active Directory.

    ![Identyfikator katalogu](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Skopiuj identyfikator katalogu.

3. Zaloguj się do witryny Azure portal jako użytkownik, który jest przypisany do roli **administratora globalnego.**

4. Następnie należy wyrazić zgodę administratora. Skopiuj i wklej adres URL odnoszący się do lokalizacji wdrożenia na pasku adresu przeglądarki:

    Public

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
   https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Niemcy

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure w Chinach — 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Wybierz konto **administratora globalnego,** jeśli zostanie wyświetlony monit.

    ![Identyfikator katalogu](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Po wyświetleniu monitu wybierz **pozycję Zaakceptuj.**

    ![Zaakceptuj](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. W obszarze usługi Azure AD w **aplikacjach dla przedsiębiorstw**na liście znajduje się lista **sieci VPN platformy Azure.**

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. Jeśli nie masz jeszcze działającego środowiska typu punkt-lokacja, postępuj zgodnie z instrukcją, aby je utworzyć. Zobacz [Tworzenie sieci VPN typu lokacja w celu](vpn-gateway-howto-point-to-site-resource-manager-portal.md) utworzenia i skonfigurowania bramy sieci VPN typu punkt-lokacja z natywnym uwierzytelnianiem certyfikatów platformy Azure. 

    > [!IMPORTANT]
    > Podstawowa jednostka SKU nie jest obsługiwana dla openvpn.

9. Włącz uwierzytelnianie usługi Azure AD w bramie sieci VPN, uruchamiając następujące polecenia, aby zmodyfikować polecenie w celu odzwierciedlenia własnego środowiska:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>/" -AadAudienceId "41b23e61-6c1e-4545-b367-cd054e0ed4b4" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24 -VpnClientProtocol OpenVPN
    ```

   > [!NOTE]
   > Upewnij się, że na końcu `AadIssuerUri` wartości znajduje się ukośnik końcowy. W przeciwnym razie polecenie zakończy się niepowodzeniem.

10. Utwórz i pobierz profil, uruchamiając następujące polecenia. Zmień wartości -ResourceGroupName i -Name, aby dopasować się do własnych.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

11. Po uruchomieniu poleceń, widać wynik podobny do poniższego. Skopiuj wynikowy adres URL do przeglądarki, aby pobrać plik zip profilu.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/profile.png)

12. Wyodrębnij pobrany plik zip.

13. Przejdź do rozpakowanego folderu "AzureVPN".

14. Zanotuj lokalizację pliku "azurevpnconfig.xml". Plik azurevpnconfig.xml zawiera ustawienie połączenia sieci VPN i można go zaimportować bezpośrednio do aplikacji klienta sieci VPN platformy Azure. Można również rozpowszechniać ten plik do wszystkich użytkowników, którzy muszą połączyć się za pośrednictwem poczty e-mail lub w inny sposób. Użytkownik będzie potrzebował prawidłowych poświadczeń usługi Azure AD, aby połączyć się pomyślnie.

## <a name="next-steps"></a>Następne kroki

Aby połączyć się z siecią wirtualną, należy utworzyć i skonfigurować profil klienta sieci VPN. Zobacz [Konfigurowanie klienta sieci VPN dla połączeń sieci VPN P2S](openvpn-azure-ad-client.md).
