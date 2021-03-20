---
title: Konfigurowanie Always-On tunelu użytkownika sieci VPN
titleSuffix: Azure VPN Gateway
description: W tym artykule opisano sposób konfigurowania tunelu użytkownika zawsze włączone sieci VPN dla bramy sieci VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 99d21222a62ed6b27a6a1b2a73b704f4cb26457b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89435800"
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
