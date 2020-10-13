---
title: Jak skonfigurować klientów OpenVPN dla platformy Azure VPN Gateway | Microsoft Docs
description: Dowiedz się, jak skonfigurować usługę OpenVPN for Azure VPN Gateway dla klientów systemu operacyjnego Windows, Linux i Mac.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 4be903fa17ce95e96c82241249b421e1d794c80f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89435783"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Konfigurowanie klientów OpenVPN dla platformy Azure VPN Gateway

Ten artykuł pomaga w konfigurowaniu klientów **OpenVPN &reg; Protocol** .

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Sprawdź, czy zostały wykonane kroki konfigurowania usługi OpenVPN dla bramy sieci VPN. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie OpenVPN dla platformy Azure VPN Gateway](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz, aby klienci sieci VPN mieli dostęp do zasobów w innej sieci wirtualnej, postępuj zgodnie z instrukcjami w artykule [Sieć wirtualna-sieć](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) wirtualna, aby skonfigurować połączenie między sieciami wirtualnymi. Upewnij się, że włączono protokół BGP na bramach i połączeniach, w przeciwnym razie ruch nie będzie przepływać.

**"OpenVPN" jest znakiem towarowym OpenVPN Inc.**
