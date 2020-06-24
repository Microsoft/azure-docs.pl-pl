---
title: Konfigurowanie tunelu VPN Always-On
titleSuffix: Azure Virtual WAN
description: Procedura konfigurowania zawsze włączonego tunelu urządzenia sieci VPN dla wirtualnej sieci WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: e911bf6e3736c931ca5c1563ab42f52ecb5cf3c1
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84750585"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Konfigurowanie tunelu zawsze włączonej sieci VPN dla wirtualnej sieci WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Należy utworzyć konfigurację typu punkt-lokacja i edytować przypisanie koncentratora wirtualnego. Aby uzyskać instrukcje, zobacz następujące sekcje:

* [Tworzenie konfiguracji P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Edytowanie przypisania centrum](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-the-device-tunnel"></a>Konfigurowanie tunelu urządzenia

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Aby usunąć profil

Aby usunąć profil, uruchom następujące polecenie:

![Czyszczenie](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).