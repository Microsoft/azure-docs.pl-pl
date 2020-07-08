---
title: 'Włączanie uwierzytelniania wieloskładnikowego dla użytkowników sieci VPN: uwierzytelnianie usługi Azure AD'
description: Włączanie uwierzytelniania wieloskładnikowego dla użytkowników sieci VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/14/2020
ms.author: alzam
ms.openlocfilehash: 80a6b342990100b3e79cc8194722a6eb84080ef6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987287"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Włącz usługę Azure Multi-Factor Authentication (MFA) dla użytkowników sieci VPN

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enable-authentication"></a><a name="enableauth"></a>Włącz uwierzytelnianie

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="configure-sign-in-settings"></a><a name="enablesign"></a>Konfigurowanie ustawień logowania

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="option-1---per-user-access"></a><a name="peruser"></a>Opcja 1 — dostęp dla użytkownika

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="option-2---conditional-access"></a><a name="conditional"></a>Opcja 2 — dostęp warunkowy

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>Następne kroki

Aby nawiązać połączenie z siecią wirtualną, należy utworzyć i skonfigurować profil klienta sieci VPN. Zobacz [Konfigurowanie klienta VPN dla połączeń sieci VPN P2S](openvpn-azure-ad-client.md).