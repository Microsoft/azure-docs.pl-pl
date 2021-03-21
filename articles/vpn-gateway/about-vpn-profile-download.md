---
title: 'Azure VPN Gateway: informacje o profilach klienta sieci VPN P2S'
description: Skorzystaj z tego artykułu, aby znaleźć potrzebne informacje dotyczące profilu klienta sieci VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: 9bb66363d525648df08f32451842402ad1d0d93b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979088"
---
# <a name="working-with-p2s-vpn-client-profile-files"></a>Praca z plikami profilu klienta sieci VPN P2S

Pliki profilu zawierają informacje niezbędne do skonfigurowania połączenia sieci VPN. Ten artykuł pomoże Ci uzyskać i zrozumieć informacje niezbędne dla profilu klienta sieci VPN.

## <a name="generate-and-download-profile"></a>Generowanie i pobieranie profilu

Można generować pliki konfiguracji klienta przy użyciu programu PowerShell lub za pomocą Azure Portal. Każda metoda zwraca ten sam plik zip.

### <a name="portal"></a>Portal

1. W Azure Portal przejdź do bramy sieci wirtualnej dla sieci wirtualnej, z którą chcesz nawiązać połączenie.
1. Na stronie Brama sieci wirtualnej wybierz opcję **Konfiguracja punktu do lokacji**.
1. W górnej części strony Konfiguracja punktu do lokacji wybierz pozycję **Pobierz klienta sieci VPN**. Generowanie pakietu konfiguracji klienta może potrwać kilka minut.
1. Twoja przeglądarka wskazuje, że plik zip konfiguracji klienta jest dostępny. Nazwa taka sama nazywa się bramą. Rozpakuj plik, aby wyświetlić foldery.

### <a name="powershell"></a>PowerShell

Aby wygenerować przy użyciu programu PowerShell, można użyć następującego przykładu:

1. Podczas generowania plików konfiguracji klienta sieci VPN wartość "-bioEapTls" jest równa "{0}". Wygeneruj pliki konfiguracji klienta VPN za pomocą następującego polecenia:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. Skopiuj adres URL do przeglądarki, aby pobrać plik zip, a następnie Rozpakuj plik, aby wyświetlić foldery.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Folder OpenVPN** zawiera profil *OVPN* , który należy zmodyfikować w taki sposób, aby zawierał klucz i certyfikat. Aby uzyskać więcej informacji, zobacz [Konfigurowanie klientów OpenVPN dla platformy Azure VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows). Jeśli na bramie sieci VPN wybrano opcję uwierzytelnianie usługi Azure AD, ten folder nie znajduje się w pliku zip. Zamiast tego przejdź do folderu AzureVPN i Znajdź azurevpnconfig.xml.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Point-to-site, zobacz [Informacje o punkcie-to-site](point-to-site-about.md).
