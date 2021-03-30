---
title: Konfigurowanie tunelu VPN Always-On
titleSuffix: Azure VPN Gateway
description: Dowiedz się, jak używać bram z systemem Windows 10, które są zawsze włączone, aby ustanawiać i konfigurować trwałe tunele urządzeń na platformie Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 7688e42175b2b4e35b63979f5df25702f3bb869d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90986586"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Konfigurowanie tunelu zawsze włączonego urządzenia sieci VPN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Konfigurowanie bramy

Skonfiguruj bramę sieci VPN tak, aby korzystała z protokołu IKEv2 i uwierzytelniania opartego na certyfikatach przy użyciu artykułu [Konfigurowanie połączenia VPN punkt-lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md) .

## <a name="configure-the-device-tunnel"></a>Konfigurowanie tunelu urządzenia

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Aby usunąć profil

Aby usunąć profil, uruchom następujące polecenie:

![Zrzut ekranu przedstawia okno programu PowerShell, które uruchamia polecenie Remove-VpnConnection-Name MachineCertTest.](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat rozwiązywania problemów, zobacz [problemy z połączeniem punkt-lokacja platformy Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
