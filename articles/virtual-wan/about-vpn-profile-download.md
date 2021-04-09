---
title: 'Wirtualna sieć WAN platformy Azure: profile klienta sieci VPN użytkownika'
description: Pomaga to w pracy z plikiem profilu klienta
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: d83b6ed2ae83db569d3c61e3cf4cd887f875eb25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99980919"
---
# <a name="working-with-user-vpn-client-profile-files"></a>Praca z plikami profilu klienta sieci VPN użytkownika

Pliki profilu zawierają informacje niezbędne do skonfigurowania połączenia sieci VPN. Ten artykuł pomaga uzyskać i zrozumieć informacje niezbędne dla profilu klienta sieci VPN użytkownika.

## <a name="download-the-profile"></a>Pobierz profil

Aby pobrać plik zip profilu klienta, można użyć kroków z artykułu [pobieranie profilów](global-hub-profile.md) .

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Folder OpenVPN** zawiera profil *OVPN* , który należy zmodyfikować w taki sposób, aby zawierał klucz i certyfikat. Aby uzyskać więcej informacji, zobacz [Konfigurowanie klientów OpenVPN](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat sieci VPN użytkowników wirtualnej sieci WAN, zobacz [Tworzenie połączenia sieci VPN użytkownika](virtual-wan-point-to-site-portal.md).
