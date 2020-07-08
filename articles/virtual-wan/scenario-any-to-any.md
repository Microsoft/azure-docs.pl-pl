---
title: 'Scenariusz: dowolne'
titleSuffix: Azure Virtual WAN
description: Scenariusze routingu-dowolnego-do-dowolnego
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: ecc2b3cf236cb2a78fd595189649e7f6b176d709
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568713"
---
# <a name="scenario-any-to-any"></a>Scenariusz: dowolne

Podczas pracy z routingiem wirtualnego koncentratora sieci WAN jest dość kilka dostępnych scenariuszy. W dowolnym scenariuszu każdy szprych może nawiązać połączenie z innym szprychą. Gdy istnieje wiele centrów, routing między centrami (znany również jako Inter-Hub) jest domyślnie włączony w standardowej wirtualnej sieci WAN. 

W tym scenariuszu połączenia sieci VPN, ExpressRoute i VPN użytkowników są skojarzone z tą samą tabelą tras. Wszystkie połączenia sieci VPN, ExpressRoute i VPN użytkowników propagują trasy do tego samego zestawu tabel tras. Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Architektura scenariusza

Na **rysunku 1**wszystkie sieci wirtualnych i gałęzie (VPN, EXPRESSROUTE, P2S) mogą się łączyć ze sobą. W koncentratorze wirtualnym połączenia działają w następujący sposób:

* Połączenie sieci VPN łączy lokację sieci VPN z bramą sieci VPN.
* Połączenie sieci wirtualnej łączy sieć wirtualną z koncentratorem wirtualnym. Router koncentratora wirtualnego zapewnia funkcjonalność tranzytu między sieci wirtualnych.
* Połączenie ExpressRoute łączy obwód usługi ExpressRoute z bramą ExpressRoute.

Te połączenia (domyślnie podczas tworzenia) są skojarzone z domyślną tabelą tras, chyba że skonfigurowano konfigurację routingu połączenia do **żadnej**lub niestandardowej tabeli tras. Te połączenia również propagują trasy domyślnie do domyślnej tabeli tras. Dzięki temu można dowolnie dowolnie korzystać z dowolnego scenariusza, w którym wszystkie szprychy (Sieć wirtualna, sieci VPN, ER, P2S) mogą dotrzeć do siebie nawzajem.

**Rysunek 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="Rysunek 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Przepływ pracy scenariusza

Ten scenariusz jest domyślnie włączony dla standardowej wirtualnej sieci WAN. Jeśli ustawienie rozgałęzienia do gałęzi jest wyłączone w konfiguracji sieci WAN, to nie będzie można nawiązać połączenia między szprychami. Sieci VPN/ExpressRoute/użytkownika są traktowane jako szprychy gałęzie w wirtualnej sieci WAN

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).
* Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
