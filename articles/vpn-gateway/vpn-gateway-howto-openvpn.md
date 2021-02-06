---
title: 'Jak skonfigurować OpenVPN na platformie Azure VPN Gateway: PowerShell'
description: Dowiedz się, jak włączyć protokół OpenVPN na platformie VPN Gateway Azure przy użyciu programu PowerShell dla środowiska punkt-lokacja.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 1e2f7f754ae9a1547d6543dba65c69511ab7ceb1
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99624916"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Skonfiguruj OpenVPN dla usługi Azure Point-to-site VPN Gateway

W tym artykule opisano konfigurowanie **protokołu OpenVPN®** na platformie Azure VPN Gateway. W tym artykule przyjęto założenie, że masz już środowisko pracy typu punkt-lokacja. Jeśli tego nie zrobisz, użyj instrukcji z kroku 1, aby utworzyć sieć VPN typu punkt-lokacja.



## <a name="1-create-a-point-to-site-vpn"></a><a name="vnet"></a>1. Utwórz sieć VPN typu punkt-lokacja

Jeśli nie masz jeszcze działającego środowiska punkt-lokacja, postępuj zgodnie z instrukcjami, aby je utworzyć. Zobacz [Tworzenie sieci VPN typu punkt-lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md) , aby utworzyć i skonfigurować bramę sieci VPN typu punkt-lokacja z natywnym uwierzytelnianiem certyfikatu platformy Azure. 

> [!IMPORTANT]
> Podstawowa jednostka SKU nie jest obsługiwana w przypadku OpenVPN.

## <a name="2-enable-openvpn-on-the-gateway"></a><a name="enable"></a>2. Włącz OpenVPN na bramie

Włącz OpenVPN na bramie.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować klientów dla usługi OpenVPN, zobacz [Konfigurowanie klientów OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" jest znakiem towarowym OpenVPN Inc.**
