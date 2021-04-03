---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b283add6cff1400cc3141f4fba3f0f3939ee34aa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "97665079"
---
|  | **Punkt-lokacja** | **Lokacja-lokacja** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Usługi obsługiwane przez platformę Azure** |Usługi Cloud Services i Virtual Machines |Usługi Cloud Services i Virtual Machines |[Lista usług](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Typowe przepustowości** |Na podstawie jednostki SKU bramy |Zwykle < 1 Gb/s łącznie |50 Mb/s, 100 Mb/s, 200 Mb/s, 500 Mb/s, 1 Gb/s, 2 Gb/s, 5 Gb/s, 10 Gb/s |
| **Obsługiwane protokoły** |Secure Socket Tunneling Protocol (SSTP), OpenVPN i IPsec |IPsec |Bezpośrednie połączenie za pośrednictwem sieci VLAN, technologie VPN NSP (MPLS, VPLS itp.) |
| **Routing** |RouteBased (dynamiczny) |Firma Microsoft obsługuje routing typu PolicyBased (statyczny) i RouteBased (dynamiczny VPN) |BGP |
| **Odporność połączenia** |aktywne-pasywne |aktywne/pasywne lub aktywne/aktywne |aktywne-aktywne |
| **Typowy przypadek użycia** |Bezpieczny dostęp do sieci wirtualnych platformy Azure dla użytkowników zdalnych |Scenariusze deweloperskie/testowe/laboratoryjne oraz małe i średnie obciążenia produkcyjne dla usług Cloud Services i Virtual Machines |Dostęp do wszystkich usług Azure (zatwierdzona lista), obciążenia o znaczeniu krytycznym oraz klasy korporacyjnej, Backup, dane big data, platforma Azure jako lokacja DR |
| **Umowa SLA** |[Umowa SLA](https://azure.microsoft.com/support/legal/sla/) |[Umowa SLA](https://azure.microsoft.com/support/legal/sla/) |[Umowa SLA](https://azure.microsoft.com/support/legal/sla/) |
| **Cennik** |[Cennik](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Cennik](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Cennik](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Dokumentacja techniczna** |[Dokumentacja VPN Gateway](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Dokumentacja VPN Gateway](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Dokumentacja ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) |
| **Często zadawane pytania** |[Brama sieci VPN — często zadawane pytania](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Brama sieci VPN — często zadawane pytania](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Usługa ExpressRoute — często zadawane pytania](../articles/expressroute/expressroute-faqs.md) |
