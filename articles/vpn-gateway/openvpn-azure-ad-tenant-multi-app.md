---
title: 'VPN Gateway: dzierżawa usługi Azure AD dla różnych grup użytkowników: uwierzytelnianie w usłudze Azure AD'
description: Aby nawiązać połączenie z siecią wirtualną przy użyciu uwierzytelniania usługi Azure AD, możesz użyć sieci VPN P2S
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 118ea21cbdd2e0527659c7c1beb40d8e42fa1d10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77485724"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Tworzenie dzierżawy Azure Active Directory dla połączeń protokołu OpenVPN P2S

Podczas nawiązywania połączenia z siecią wirtualną można użyć uwierzytelniania opartego na certyfikatach lub uwierzytelniania usługi RADIUS. Jednak w przypadku korzystania z otwartego protokołu sieci VPN można również użyć uwierzytelniania Azure Active Directory. Jeśli chcesz, aby inny zestaw użytkowników mógł łączyć się z różnymi bramami sieci VPN, możesz zarejestrować wiele aplikacji w usłudze AD i połączyć je z innymi bramami sieci VPN. Ten artykuł ułatwia skonfigurowanie dzierżawy usługi Azure AD na potrzeby uwierzytelniania P2S OpenVPN i tworzenie i rejestrowanie wielu aplikacji w usłudze Azure AD w celu umożliwienia różnym użytkownikom i grupom dostępu do różnych użytkowników.

> [!NOTE]
> Uwierzytelnianie usługi Azure AD jest obsługiwane tylko dla połączeń protokołu OpenVPN®.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. Włącz uwierzytelnianie na bramie

W tym kroku zostanie włączone uwierzytelnianie usługi Azure AD na bramie sieci VPN.

1. Włącz uwierzytelnianie usługi Azure AD na bramie sieci VPN, uruchamiając następujące polecenia. Pamiętaj, aby zmodyfikować polecenia, aby odzwierciedlały własne środowisko:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
    > [!NOTE]
    > Nie używaj identyfikatora aplikacji klienta sieci VPN platformy Azure w powyższych poleceniach: spowoduje to przyznanie wszystkim użytkownikom dostępu do bramy sieci VPN. Użyj identyfikatora zarejestrowanej aplikacji.

2. Utwórz i Pobierz profil, uruchamiając następujące polecenia. Zmień wartości parametrów-ResourcGroupName i-Name, aby były zgodne z własnymi.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. Po uruchomieniu poleceń zobaczysz wynik podobny do przedstawionego poniżej. Skopiuj adres URL wyniku do przeglądarki, aby pobrać plik zip profilu.

    ![Sieć VPN platformy Azure](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. Wyodrębnij pobrany plik zip.

5. Przejdź do folderu rozpakowanego "AzureVPN".

6. Zanotuj lokalizację pliku "azurevpnconfig. xml". Azurevpnconfig. xml zawiera ustawienie dla połączenia sieci VPN i można je zaimportować bezpośrednio do aplikacji klienckiej sieci VPN platformy Azure. Możesz również dystrybuować ten plik do wszystkich użytkowników, którzy muszą łączyć się za pośrednictwem poczty e-mail lub w inny sposób. Aby nawiązać połączenie, użytkownik musi dysponować prawidłowymi poświadczeniami usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

Aby można było połączyć się z siecią wirtualną, należy utworzyć i skonfigurować profil klienta sieci VPN. Zobacz [Konfigurowanie klienta VPN dla połączeń sieci VPN P2S](openvpn-azure-ad-client.md).
