---
title: 'VPN Gateway: dzierżawa usługi Azure AD dla różnych grup użytkowników: uwierzytelnianie w usłudze Azure AD'
description: Aby nawiązać połączenie z siecią wirtualną przy użyciu uwierzytelniania usługi Azure AD, możesz użyć sieci VPN P2S
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: cherylmc
ms.openlocfilehash: 1305ca603aef63dafcc7b055d55e3f0fe281f4fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91819673"
---
# <a name="create-an-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>Utwórz dzierżawę usługi Active Directory (AD) dla połączeń protokołu P2S OpenVPN

Podczas nawiązywania połączenia z siecią wirtualną można użyć uwierzytelniania opartego na certyfikatach lub uwierzytelniania usługi RADIUS. Jednak w przypadku korzystania z otwartego protokołu sieci VPN można również użyć uwierzytelniania Azure Active Directory. Jeśli chcesz, aby inny zestaw użytkowników mógł łączyć się z różnymi bramami sieci VPN, możesz zarejestrować wiele aplikacji w usłudze AD i połączyć je z innymi bramami sieci VPN. Ten artykuł ułatwia skonfigurowanie dzierżawy usługi Azure AD na potrzeby uwierzytelniania P2S OpenVPN i tworzenie i rejestrowanie wielu aplikacji w usłudze Azure AD w celu umożliwienia różnym użytkownikom i grupom dostępu do różnych użytkowników.

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. Włącz uwierzytelnianie na bramie

W tym kroku zostanie włączone uwierzytelnianie usługi Azure AD na bramie sieci VPN.

1. Włącz uwierzytelnianie usługi Azure AD na bramie sieci VPN, przechodząc do **konfiguracji typu punkt-lokacja** i wybierając **OpenVPN (SSL)** jako **Typ tunelu**. Wybierz **Azure Active Directory** jako **Typ uwierzytelniania** , a następnie wypełnij informacje w sekcji **Azure Active Directory** .

    ![Widok Azure Portal](./media/openvpn-azure-ad-tenant-multi-app/azure-ad-auth-portal.png)

    > [!NOTE]
    > Nie używaj identyfikatora aplikacji klienta sieci VPN platformy Azure: przyznaje wszystkim użytkownikom dostęp do bramy sieci VPN. Użyj identyfikatora zarejestrowanej aplikacji.

2. Utwórz i Pobierz profil, klikając łącze **Pobierz klienta sieci VPN** .

3. Wyodrębnij pobrany plik zip.

4. Przejdź do folderu rozpakowanego "AzureVPN".

5. Zanotuj lokalizację pliku "azurevpnconfig.xml". azurevpnconfig.xml zawiera ustawienie dla połączenia sieci VPN i można je zaimportować bezpośrednio do aplikacji klienckiej sieci VPN platformy Azure. Możesz również dystrybuować ten plik do wszystkich użytkowników, którzy muszą łączyć się za pośrednictwem poczty e-mail lub w inny sposób. Aby nawiązać połączenie, użytkownik musi dysponować prawidłowymi poświadczeniami usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

Aby można było połączyć się z siecią wirtualną, należy utworzyć i skonfigurować profil klienta sieci VPN. Zobacz [Konfigurowanie klienta VPN dla połączeń sieci VPN P2S](openvpn-azure-ad-client.md).
