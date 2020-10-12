---
title: Konfigurowanie klientów OpenVPN dla wirtualnej sieci WAN platformy Azure
description: Kroki konfigurowania klientów OpenVPN dla wirtualnej sieci WAN platformy Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 68238a084684aeda557690812403995a4dc1e8c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317671"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Konfigurowanie klienta OpenVPN dla wirtualnej sieci WAN platformy Azure

Ten artykuł pomaga w konfigurowaniu klientów **OpenVPN &reg; Protocol** .

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Utwórz konfigurację sieci VPN użytkownika (punkt-lokacja). Upewnij się, że wybrano opcję "OpenVPN" dla typu tunelu. Aby uzyskać instrukcje, zobacz [Tworzenie konfiguracji P2S dla wirtualnej sieci WAN platformy Azure](virtual-wan-point-to-site-portal.md#p2sconfig).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o sieci VPN użytkownika (punkt-lokacja), zobacz [Tworzenie połączeń sieci VPN użytkowników](virtual-wan-point-to-site-portal.md).

**"OpenVPN" jest znakiem towarowym OpenVPN Inc.**
