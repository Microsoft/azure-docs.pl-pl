---
title: Konfigurowanie tunelu VPN Always-On
titleSuffix: Azure Virtual WAN
description: Procedura konfigurowania zawsze włączonego tunelu urządzenia sieci VPN dla wirtualnej sieci WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: e814487cb4dab9c8c19daab2ea3bb81391d4a98f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90983682"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Konfigurowanie tunelu zawsze włączonej sieci VPN dla wirtualnej sieci WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Należy utworzyć konfigurację typu punkt-lokacja i edytować przypisanie koncentratora wirtualnego. Aby uzyskać instrukcje, zobacz następujące sekcje:

* [Tworzenie konfiguracji P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Tworzenie centrum przy użyciu bramy P2S](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-the-device-tunnel"></a>Konfigurowanie tunelu urządzenia

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Aby usunąć profil

Aby usunąć profil, uruchom następujące polecenie:

![Zrzut ekranu przedstawia okno programu PowerShell, które uruchamia polecenie Remove-VpnConnection-Name MachineCertTest.](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).