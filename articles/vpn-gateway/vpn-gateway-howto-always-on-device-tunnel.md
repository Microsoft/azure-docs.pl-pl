---
title: Konfigurowanie tunelu VPN Always-On
titleSuffix: Azure VPN Gateway
description: Dowiedz się, jak skonfigurować opcję zawsze włączone, która utrzymuje połączenie sieci VPN oparte na wyzwalaczach, takie jak logowanie użytkownika, zmiana stanu sieci lub aktywny ekran urządzenia.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 24043576fca4910631ccddb3924303dd642c6842
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927065"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Konfigurowanie tunelu zawsze włączonego urządzenia sieci VPN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Konfigurowanie bramy

Skonfiguruj bramę sieci VPN tak, aby korzystała z protokołu IKEv2 i uwierzytelniania opartego na certyfikatach przy użyciu artykułu [Konfigurowanie połączenia VPN punkt-lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md) .

## <a name="configure-the-device-tunnel"></a>Konfigurowanie tunelu urządzenia

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Aby usunąć profil

Aby usunąć profil, uruchom następujące polecenie:

![Czyszczenie](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat rozwiązywania problemów, zobacz [problemy z połączeniem punkt-lokacja platformy Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
