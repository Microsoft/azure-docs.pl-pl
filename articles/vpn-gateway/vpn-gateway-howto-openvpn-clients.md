---
title: Jak skonfigurować klientów OpenVPN dla platformy Azure VPN Gateway | Microsoft Docs
description: Dowiedz się, jak skonfigurować klientów protokołu OpenVPN dla VPN Gateway platformy Azure, w tym klientów z systemami Windows, Mac i Linux.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 136dcb0b2d1740aa5cadbd716b4a8386ad5cf486
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926216"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Konfigurowanie klientów OpenVPN dla platformy Azure VPN Gateway

Ten artykuł pomaga w konfigurowaniu klientów **OpenVPN &reg; Protocol** .

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Sprawdź, czy zostały wykonane kroki konfigurowania usługi OpenVPN dla bramy sieci VPN. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie OpenVPN dla platformy Azure VPN Gateway](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz, aby klienci sieci VPN mieli dostęp do zasobów w innej sieci wirtualnej, postępuj zgodnie z instrukcjami w artykule [Sieć wirtualna-sieć](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) wirtualna, aby skonfigurować połączenie między sieciami wirtualnymi. Upewnij się, że włączono protokół BGP na bramach i połączeniach, w przeciwnym razie ruch nie będzie przepływać.

**"OpenVPN" jest znakiem towarowym OpenVPN Inc.**
