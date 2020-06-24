---
title: Jak skonfigurować klientów OpenVPN dla platformy Azure VPN Gateway | Microsoft Docs
description: Kroki konfigurowania klientów OpenVPN na platformie Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: bdddf097265e7af688175688b6f3214413a90fdc
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2020
ms.locfileid: "84984085"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Konfigurowanie klientów OpenVPN dla platformy Azure VPN Gateway

Ten artykuł pomaga w konfigurowaniu klientów **OpenVPN &reg; Protocol** .

## <a name="before-you-begin"></a>Przed rozpoczęciem

Sprawdź, czy zostały wykonane kroki konfigurowania usługi OpenVPN dla bramy sieci VPN. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie OpenVPN dla platformy Azure VPN Gateway](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz, aby klienci sieci VPN mieli dostęp do zasobów w innej sieci wirtualnej, postępuj zgodnie z instrukcjami w artykule [Sieć wirtualna-sieć](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) wirtualna, aby skonfigurować połączenie między sieciami wirtualnymi. Upewnij się, że włączono protokół BGP na bramach i połączeniach, w przeciwnym razie ruch nie będzie przepływać.

**"OpenVPN" jest znakiem towarowym OpenVPN Inc.**
