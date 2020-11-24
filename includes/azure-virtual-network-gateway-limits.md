---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 9fe9ef5549ced3b73d18d553fa0b62ec019684fe
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555916"
---
| Zasób                                | Limit        |
|-----------------------------------------|------------------------------|
| Prefiksy adresów sieci wirtualnej                   | 600 za bramę sieci VPN          |
| Agregowanie tras BGP                    | 4 000 za bramę sieci VPN        |
| Prefiksy adresów bramy sieci lokalnej  | 1000 na bramę sieci lokalnej               |
| Połączenia S2S                         | [Zależy od jednostki SKU bramy](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)|
| Połączenia P2S                         | [Zależy od jednostki SKU bramy](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |
| Limit tras P2S-IKEv2                 | 256 dla systemu Windows w systemie innym niż Windows **/**           |
| Limit tras P2S — OpenVPN               | 1000                         |
| Maksymalnie z flows (przepływy)                              | 100 000 dla VpnGw1/AZ  **/**  512K dla VpnGw2-4/AZ|