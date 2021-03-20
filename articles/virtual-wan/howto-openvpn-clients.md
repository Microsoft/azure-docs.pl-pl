---
title: Konfigurowanie klientów OpenVPN dla wirtualnej sieci WAN platformy Azure
description: Kroki konfigurowania klientów OpenVPN dla wirtualnej sieci WAN platformy Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 05502b344b1224dff5d12f95b96b05baace98970
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94491005"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Konfigurowanie klienta OpenVPN dla wirtualnej sieci WAN platformy Azure

Ten artykuł pomaga w konfigurowaniu klientów **OpenVPN &reg; Protocol** . Możesz również użyć klienta sieci VPN platformy Azure dla systemu Windows 10, aby nawiązać połączenie za pośrednictwem protokołu OpenVPN. Więcej instrukcji można znaleźć [tutaj](openvpn-azure-ad-client.md)

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Utwórz konfigurację sieci VPN użytkownika (punkt-lokacja). Upewnij się, że wybrano opcję "OpenVPN" dla typu tunelu. Aby uzyskać instrukcje, zobacz [Tworzenie konfiguracji P2S dla wirtualnej sieci WAN platformy Azure](virtual-wan-point-to-site-portal.md#p2sconfig).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o sieci VPN użytkownika (punkt-lokacja), zobacz [Tworzenie połączeń sieci VPN użytkowników](virtual-wan-point-to-site-portal.md).

**"OpenVPN" jest znakiem towarowym OpenVPN Inc.**
