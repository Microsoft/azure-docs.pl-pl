---
title: 'Wirtualna sieć WAN platformy Azure: profile klienta sieci VPN użytkownika'
description: Pomaga to w pracy z plikiem profilu klienta
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 116ec518f7788e620fc6bd177e535c8f6af99d10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267775"
---
# <a name="working-with-user-vpn-client-profiles"></a>Praca z profilami klienta sieci VPN użytkownika

Pobrany plik profilu zawiera informacje niezbędne do skonfigurowania połączenia sieci VPN. Ten artykuł pomaga uzyskać i zrozumieć informacje niezbędne dla profilu klienta sieci VPN użytkownika.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Folder OpenVPN** zawiera profil *OVPN* , który należy zmodyfikować w taki sposób, aby zawierał klucz i certyfikat. Aby uzyskać więcej informacji, zobacz [Konfigurowanie klientów OpenVPN](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat sieci VPN użytkowników wirtualnej sieci WAN, zobacz [Tworzenie połączenia sieci VPN użytkownika](virtual-wan-point-to-site-portal.md).
