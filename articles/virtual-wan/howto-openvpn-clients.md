---
title: Konfigurowanie klientów OpenVPN dla wirtualnej sieci WAN platformy Azure
description: Kroki konfigurowania klientów OpenVPN dla wirtualnej sieci WAN platformy Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: f955b5347bc5a2c0fe1fd8a93e14badab1f354c8
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84753271"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Konfigurowanie klienta OpenVPN dla wirtualnej sieci WAN platformy Azure

Ten artykuł pomaga w konfigurowaniu klientów **OpenVPN &reg; Protocol** .

## <a name="before-you-begin"></a>Przed rozpoczęciem

Utwórz konfigurację sieci VPN użytkownika (punkt-lokacja). Upewnij się, że wybrano opcję "OpenVPN" dla typu tunelu. Aby uzyskać instrukcje, zobacz [Tworzenie konfiguracji P2S dla wirtualnej sieci WAN platformy Azure](virtual-wan-point-to-site-portal.md#p2sconfig).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o sieci VPN użytkownika (punkt-lokacja), zobacz [Tworzenie połączeń sieci VPN użytkowników](virtual-wan-point-to-site-portal.md).

**"OpenVPN" jest znakiem towarowym OpenVPN Inc.**
