---
title: Włącz usługę MFA dla użytkowników sieci VPN za pomocą uwierzytelniania usługi Azure AD
description: Włączanie uwierzytelniania wieloskładnikowego dla użytkowników sieci VPN
services: virtual-wan
author: kumudD
ms.service: virtual-wan
ms.topic: how-to
ms.date: 01/16/2020
ms.author: alzam
ms.openlocfilehash: 7adcdcf00f22020daf66192e777b1aca151349e0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082124"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users-by-using-azure-ad-authentication"></a>Włączanie usługi Azure Multi-Factor Authentication (MFA) dla użytkowników sieci VPN przy użyciu uwierzytelniania usługi Azure AD

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

Aby nawiązać połączenie z siecią wirtualną, należy utworzyć i skonfigurować profil klienta sieci VPN. Zobacz [Konfigurowanie uwierzytelniania usługi Azure AD na potrzeby połączenia punkt-lokacja z platformą Azure](virtual-wan-point-to-site-azure-ad.md).
