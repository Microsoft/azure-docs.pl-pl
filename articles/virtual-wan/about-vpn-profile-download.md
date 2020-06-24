---
title: 'Wirtualna sieć WAN platformy Azure: profile klienta sieci VPN użytkownika'
description: Pomaga to w pracy z plikiem profilu klienta
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: c64e7988094612077131029547682c7ae3d25c98
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84753143"
---
# <a name="working-with-user-vpn-client-profiles"></a>Praca z profilami klienta sieci VPN użytkownika

Pobrany plik profilu zawiera informacje niezbędne do skonfigurowania połączenia sieci VPN. Ten artykuł pomaga uzyskać i zrozumieć informacje niezbędne dla profilu klienta sieci VPN użytkownika.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Folder OpenVPN** zawiera profil *OVPN* , który należy zmodyfikować w taki sposób, aby zawierał klucz i certyfikat. Aby uzyskać więcej informacji, zobacz [Konfigurowanie klientów OpenVPN](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat sieci VPN użytkowników wirtualnej sieci WAN, zobacz [Tworzenie połączenia sieci VPN użytkownika](virtual-wan-point-to-site-portal.md).
