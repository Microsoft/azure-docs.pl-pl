---
title: 'Azure VPN Gateway: informacje o profilach klienta sieci VPN P2S'
description: Pomaga to w pracy z plikiem profilu klienta
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 05/13/2020
ms.author: cherylmc
ms.openlocfilehash: 4526611fa8b80a7b97ee7317f5e285c50f05b0de
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650663"
---
# <a name="about-p2s-vpn-client-profiles"></a>Informacje o profilach klienta sieci VPN P2S

Pobrany plik profilu zawiera informacje niezbędne do skonfigurowania połączenia sieci VPN. Ten artykuł pomoże Ci uzyskać i zrozumieć informacje niezbędne dla profilu klienta sieci VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Folder OpenVPN** zawiera profil *OVPN* , który należy zmodyfikować w taki sposób, aby zawierał klucz i certyfikat. Aby uzyskać więcej informacji, zobacz [Konfigurowanie klientów OpenVPN dla platformy Azure VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows). Jeśli na bramie sieci VPN wybrano opcję uwierzytelnianie usługi Azure AD, ten folder nie znajduje się w pliku zip. Zamiast tego przejdź do folderu AzureVPN i Znajdź azurevpnconfig. XML.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Point-to-site, zobacz [Informacje o punkcie-to-site](point-to-site-about.md).
