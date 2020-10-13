---
title: plik dołączany
description: plik dołączany
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317a480c13c5c6e00653fd61878a379df3f65ac4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67182967"
---
### <a name="what-is-expressroute-global-reach"></a>Co to jest ExpressRoute Global Reach?

ExpressRoute Global Reach to usługa platformy Azure, która łączy sieci lokalne za pośrednictwem usługi ExpressRoute za pośrednictwem sieci globalnej firmy Microsoft. Na przykład jeśli masz prywatne centrum danych w Kalifornii połączone z ExpressRoute w Dolina krzemu i inne prywatne centrum danych w sieci Texas Instruments do ExpressRoute w Dallas, z ExpressRoute Global Reach, możesz połączyć prywatne centra danych ze sobą za pomocą dwóch połączeń ExpressRoute, a ruch między centrami danych będzie przepływał przez sieć szkieletową firmy Microsoft.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Jak mogę włączać lub wyłączać Global Reach ExpressRoute?

ExpressRoute Global Reach można włączyć przez połączenie obwodów usługi ExpressRoute. Tę funkcję można wyłączyć, odłączając obwody. Zapoznaj się z [konfiguracją](../articles/expressroute/expressroute-howto-set-global-reach.md).

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>Czy potrzebuję ExpressRoute Premium dla ExpressRoute Global Reach?

Jeśli obwody usługi ExpressRoute znajdują się w tym samym regionie geopolitycznym, nie potrzebujesz ExpressRoute Premium, aby połączyć je ze sobą. Jeśli dwa obwody usługi ExpressRoute znajdują się w różnych regionach geopolitycznych, potrzebujesz ExpressRoute Premium dla obu obwodów, aby umożliwić połączenie między nimi. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Jak zostanie naliczona opłata za ExpressRoute Global Reach?

ExpressRoute umożliwia łączność z sieci lokalnej z usługami w chmurze firmy Microsoft. ExpressRoute Global Reach umożliwia łączność między sieciami lokalnymi za pośrednictwem istniejących obwodów usługi ExpressRoute, wykorzystując sieć globalną firmy Microsoft. ExpressRoute Global Reach są rozliczane oddzielnie od istniejącej usługi ExpressRoute. Włączenie tej funkcji w każdym obwodzie usługi ExpressRoute jest możliwe. Ruch między sieciami lokalnymi włączonym przez ExpressRoute Global Reach będzie rozliczany według stawki ruchu wychodzącego w źródle oraz dla szybkości transferu danych przychodzących w miejscu docelowym. Stawki są zależne od strefy, w której znajdują się obwody.

### <a name="where-is-expressroute-global-reach-supported"></a>Gdzie jest ExpressRoute Global Reach obsługiwane?

ExpressRoute Global Reach jest obsługiwana w [wybranych krajach/regionach lub miejscach](../articles/expressroute/expressroute-global-reach.md). Obwody usługi ExpressRoute muszą być tworzone w lokalizacjach komunikacji równorzędnej w tych krajach/regionach lub miejscach.

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>Mam więcej niż dwie sieci lokalne, z których każda jest połączona z obwodem ExpressRoute. Czy mogę włączyć ExpressRoute Global Reach do łączenia wszystkich sieci lokalnych?

Tak, można, o ile obwody znajdują się w obsługiwanych krajach/regionach. Musisz połączyć dwa obwody usługi ExpressRoute w danym momencie. Aby utworzyć w pełni siatkę, należy wyliczyć wszystkie pary obwodów i powtórzyć konfigurację. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Czy mogę włączyć ExpressRoute Global Reach między dwoma obwodami ExpressRoute w tej samej lokalizacji komunikacji równorzędnej?

Nie. Dwa obwody muszą pochodzić z różnych lokalizacji komunikacji równorzędnej. Jeśli linia metra w obsługiwanym kraju/regionie ma więcej niż jedną lokalizację komunikacji równorzędnej ExpressRoute, można połączyć obwody usługi ExpressRoute utworzone w różnych lokalizacjach komunikacji równorzędnej w tej linii metra. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Jeśli ExpressRoute Global Reach jest włączony między obwodem X i obwodem Y, a między obwodem a i obwodem Z systemem, sieci lokalne połączone z obwodem X i obwodem Z komunikują się ze sobą za pośrednictwem sieci firmy Microsoft?

Nie. Aby włączyć łączność między dowolnymi dwoma sieciami lokalnymi, należy jawnie podłączyć odpowiednie obwody usługi ExpressRoute. W powyższym przykładzie należy połączyć obwód X i obwód Z. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>Jaką przepustowość sieci mogę oczekiwać między sieciami lokalnymi po włączeniu Global Reach ExpressRoute?

Przepływność sieci między sieciami lokalnymi, włączona przez ExpressRoute Global Reach, jest ograniczona przez mniejsze z dwóch obwodów usługi ExpressRoute. Ruch przychodzący do platformy Azure i ruch z miejscami lokalnymi korzystają z tego samego obwodu i podlegają tej samej przepustowości. 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>ExpressRoute Global Reach, jakie są limity liczby tras, które mogę anonsować, oraz liczby odbieranych tras?

Liczba tras, które można anonsować firmie Microsoft w prywatnej komunikacji równorzędnej Azure 10000, pozostaje w 4000 w standardowym obwodzie lub w obwodzie w warstwie Premium. Liczba tras otrzymywanych od firmy Microsoft w ramach prywatnej komunikacji równorzędnej Azure będzie sumą tras sieci wirtualnych platformy Azure oraz tras innych sieci lokalnych połączonych za pośrednictwem ExpressRoute Global Reach. Upewnij się, że ustawiono odpowiedni maksymalny limit prefiksu na routerze lokalnym. 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>Co to jest umowa SLA dotycząca ExpressRoute Global Reach?

ExpressRoute Global Reach zapewnia taką samą umowę [SLA dostępności](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) jak zwykła usługa ExpressRoute.
