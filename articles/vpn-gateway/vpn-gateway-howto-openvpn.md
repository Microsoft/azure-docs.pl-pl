---
title: Jak skonfigurować OpenVPN na platformie Azure VPN Gateway
description: Dowiedz się, jak włączyć protokół OpenVPN na platformie VPN Gateway Azure przy użyciu programu PowerShell dla środowiska punkt-lokacja.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 4b48e98ab35b620030b15165d4c9341c0f11b440
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393279"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Skonfiguruj OpenVPN dla usługi Azure Point-to-site VPN Gateway

W tym artykule opisano konfigurowanie **protokołu OpenVPN®** na platformie Azure VPN Gateway. Możesz użyć portalu lub instrukcji programu PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

* W tym artykule przyjęto założenie, że masz już środowisko pracy typu punkt-lokacja. Jeśli go nie masz, utwórz go przy użyciu jednej z poniższych metod.

  * [Portal — Tworzenie punktu na lokację](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [PowerShell — Tworzenie punktu na lokację](vpn-gateway-howto-point-to-site-rm-ps.md)

* Sprawdź, czy Brama sieci VPN nie korzysta z podstawowej jednostki SKU. Podstawowa jednostka SKU nie jest obsługiwana w przypadku OpenVPN.

## <a name="portal"></a>Portal

1. W portalu przejdź do **bramy sieci wirtualnej — > konfigurację punkt-lokacja**.
1. W polu **Typ tunelu** wybierz pozycję **OpenVPN (SSL)** z listy rozwijanej.

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="Z listy rozwijanej wybierz pozycję OpenVPN SSL":::
1. Zapisz zmiany i przejdź do **następnych kroków**.

## <a name="enable-openvpn-on-your-gateway-using-powershell"></a>Włącz OpenVPN w bramie przy użyciu programu PowerShell.

1. Włącz OpenVPN na bramie, korzystając z następującego przykładu:

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. Przejdź do **następnych kroków**.

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować klientów dla usługi OpenVPN, zobacz [Konfigurowanie klientów OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" jest znakiem towarowym OpenVPN Inc.**
