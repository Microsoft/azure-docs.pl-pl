---
title: Konfigurowanie tunelu użytkownika z zawsze włączonymi sieciami VPN
titleSuffix: Azure VPN Gateway
description: W tym artykule opisano sposób konfigurowania tunelu użytkownika zawsze włączone sieci VPN dla bramy sieci VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 8cfe1d6434c0f5765196776ae9f6712fe14c52a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84984127"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Konfigurowanie zawsze włączonego tunelu VPN użytkownika

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Konfigurowanie bramy

 Aby skonfigurować bramę sieci VPN do korzystania z protokołu IKEv2 i uwierzytelniania opartego na certyfikatach, Skorzystaj z instrukcji przedstawionych w artykule [Konfigurowanie połączenia sieci VPN typu punkt-lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md) .

## <a name="configure-a-user-tunnel"></a>Konfigurowanie tunelu użytkownika

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Aby usunąć profil

Aby usunąć profil, wykonaj następujące czynności:

1. Uruchom następujące polecenie:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Odłącz połączenie i wyczyść pole wyboru **Połącz automatycznie** .

   ![Czyszczenie](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Następne kroki

Aby rozwiązać problemy z połączeniem, które mogą wystąpić, zobacz [problemy z połączeniem punkt-lokacja platformy Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
