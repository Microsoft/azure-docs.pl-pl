---
title: 'Azure VPN Gateway: informacje o profilach klienta sieci VPN P2S'
description: Skorzystaj z tego artykułu, aby znaleźć potrzebne informacje dotyczące profilu klienta sieci VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 6a09767a7992a5f902adea6f99e937f3fc6fa7fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985924"
---
# <a name="about-p2s-vpn-client-profiles"></a>Informacje o profilach klienta sieci VPN P2S

Pobrany plik profilu zawiera informacje niezbędne do skonfigurowania połączenia sieci VPN. Ten artykuł pomoże Ci uzyskać i zrozumieć informacje niezbędne dla profilu klienta sieci VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Folder OpenVPN** zawiera profil *OVPN* , który należy zmodyfikować w taki sposób, aby zawierał klucz i certyfikat. Aby uzyskać więcej informacji, zobacz [Konfigurowanie klientów OpenVPN dla platformy Azure VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows). Jeśli na bramie sieci VPN wybrano opcję uwierzytelnianie usługi Azure AD, ten folder nie znajduje się w pliku zip. Zamiast tego przejdź do folderu AzureVPN i Znajdź azurevpnconfig.xml.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Point-to-site, zobacz [Informacje o punkcie-to-site](point-to-site-about.md).
