---
title: 'VPN Gateway: dzierżawa usługi Azure AD dla połączeń sieci VPN P2S: uwierzytelnianie usługi Azure AD'
description: Aby nawiązać połączenie z siecią wirtualną przy użyciu uwierzytelniania usługi Azure AD, możesz użyć sieci VPN P2S
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: alzam
ms.openlocfilehash: 00db2ed05285a1637414aa1e3adbe3b047ff0568
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641344"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Tworzenie dzierżawy Azure Active Directory dla połączeń protokołu OpenVPN P2S

Podczas nawiązywania połączenia z siecią wirtualną można użyć uwierzytelniania opartego na certyfikatach lub uwierzytelniania usługi RADIUS. Jednak w przypadku korzystania z otwartego protokołu sieci VPN można również użyć uwierzytelniania Azure Active Directory. Ten artykuł ułatwia skonfigurowanie dzierżawy usługi Azure AD na potrzeby P2S Open VPN Authentication.

> [!NOTE]
> Uwierzytelnianie usługi Azure AD jest obsługiwane tylko dla połączeń protokołu OpenVPN®.
>


## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. Upewnij się, że dzierżawa usługi Azure AD

Upewnij się, że masz dzierżawę usługi Azure AD. Jeśli nie masz dzierżawy usługi Azure AD, możesz ją utworzyć, wykonując czynności opisane w artykule [Tworzenie nowej dzierżawy](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nazwa organizacji
* Początkowa nazwa domeny

Przykład:

   ![Nowa dzierżawa usługi Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. tworzenie użytkowników dzierżawy usługi Azure AD

Dzierżawa usługi Azure AD wymaga następujących kont: konta administratora globalnego i konta użytkownika głównego. Główne konto użytkownika jest używane jako główne konto osadzania (konto usługi). Podczas tworzenia konta użytkownika dzierżawy usługi Azure AD można dostosować rolę katalogu dla typu użytkownika, który ma zostać utworzony.

Wykonaj kroki opisane w [tym artykule](../active-directory/fundamentals/add-users-azure-active-directory.md) , aby utworzyć co najmniej dwóch użytkowników dla dzierżawy usługi Azure AD. Pamiętaj, aby zmienić **rolę katalogu** w celu utworzenia typów kont:

* Administrator globalny
* Użytkownik

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Włącz uwierzytelnianie usługi Azure AD na bramie sieci VPN

1. Znajdź identyfikator katalogu katalogu, który ma być używany do uwierzytelniania. Jest on wymieniony w sekcji właściwości na stronie Active Directory.

    ![Identyfikator katalogu](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Skopiuj identyfikator katalogu.

3. Zaloguj się do Azure Portal jako użytkownik, do którego przypisano rolę **administratora globalnego** .

4. Następnie udziel zgody administratora. Skopiuj i wklej adres URL odnoszący się do lokalizacji wdrożenia na pasku adresu przeglądarki:

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

5. Jeśli zostanie wyświetlony monit, wybierz konto **administratora globalnego** .

    ![Identyfikator katalogu](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Wybierz pozycję **Akceptuj** po wyświetleniu monitu.

    ![Zaakceptuj](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. W ramach usługi Azure AD w **aplikacjach dla przedsiębiorstw**zostanie wyświetlona lista **Azure VPN** .

    ![Sieć VPN platformy Azure](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. Jeśli nie masz jeszcze działającego środowiska punkt-lokacja, postępuj zgodnie z instrukcjami, aby je utworzyć. Zobacz [Tworzenie sieci VPN typu punkt-lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md) , aby utworzyć i skonfigurować bramę sieci VPN typu punkt-lokacja z natywnym uwierzytelnianiem certyfikatu platformy Azure. 

    > [!IMPORTANT]
    > Podstawowa jednostka SKU nie jest obsługiwana w przypadku OpenVPN.

9. Włącz uwierzytelnianie usługi Azure AD na bramie sieci VPN, uruchamiając następujące polecenia, pamiętając, aby zmodyfikować polecenie w celu odzwierciedlenia własnego środowiska:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>/" -AadAudienceId "41b23e61-6c1e-4545-b367-cd054e0ed4b4" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24 -VpnClientProtocol OpenVPN
    ```

   > [!NOTE]
   > Upewnij się, że na końcu `AadIssuerUri` wartości dołączysz końcowy ukośnik. W przeciwnym razie polecenie zakończy się niepowodzeniem.

10. Utwórz i Pobierz profil, uruchamiając następujące polecenia. Zmień wartości parametrów-ResourceGroupName i-Name, aby były zgodne z własnymi.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

11. Po uruchomieniu poleceń zobaczysz wynik podobny do przedstawionego poniżej. Skopiuj adres URL wyniku do przeglądarki, aby pobrać plik zip profilu.

    ![Sieć VPN platformy Azure](./media/openvpn-create-azure-ad-tenant/profile.png)

12. Wyodrębnij pobrany plik zip.

13. Przejdź do folderu rozpakowanego "AzureVPN".

14. Zanotuj lokalizację pliku "azurevpnconfig. xml". Azurevpnconfig. xml zawiera ustawienie dla połączenia sieci VPN i można je zaimportować bezpośrednio do aplikacji klienckiej sieci VPN platformy Azure. Możesz również dystrybuować ten plik do wszystkich użytkowników, którzy muszą łączyć się za pośrednictwem poczty e-mail lub w inny sposób. Aby nawiązać połączenie, użytkownik musi dysponować prawidłowymi poświadczeniami usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

Aby można było połączyć się z siecią wirtualną, należy utworzyć i skonfigurować profil klienta sieci VPN. Zobacz [Konfigurowanie klienta VPN dla połączeń sieci VPN P2S](openvpn-azure-ad-client.md).
