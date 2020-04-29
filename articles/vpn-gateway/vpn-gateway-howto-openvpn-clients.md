---
title: Jak skonfigurować klientów OpenVPN dla platformy Azure VPN Gateway | Microsoft Docs
description: Kroki konfigurowania klientów OpenVPN na platformie Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 09ff3ccebad0baa4148e68995254c818a29d7bd4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066051"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Konfigurowanie klientów OpenVPN dla platformy Azure VPN Gateway

Ten artykuł pomaga w konfigurowaniu klientów **OpenVPN &reg; Protocol** .

## <a name="before-you-begin"></a>Przed rozpoczęciem

Sprawdź, czy zostały wykonane kroki konfigurowania usługi OpenVPN dla bramy sieci VPN. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie OpenVPN dla platformy Azure VPN Gateway](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz, aby klienci sieci VPN mieli dostęp do zasobów w innej sieci wirtualnej, postępuj zgodnie z instrukcjami w artykule [Sieć wirtualna-sieć](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) wirtualna, aby skonfigurować połączenie między sieciami wirtualnymi. Upewnij się, że włączono protokół BGP na bramach i połączeniach, w przeciwnym razie ruch nie będzie przepływać.

**"OpenVPN" jest znakiem towarowym OpenVPN Inc.**
