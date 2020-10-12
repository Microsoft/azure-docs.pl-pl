---
title: Informacje o usłudze Azure ExpressRoute Direct
description: Poznaj kluczowe funkcje usługi Azure ExpressRoute Direct i informacje niezbędne do dołączenia do ExpressRoute bezpośrednio, takie jak dostępne jednostki SKU i wymagania techniczne.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: duau
ms.openlocfilehash: 0cc07f9647c4f7836438adbe2b4cf9fa24a1c890
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89566402"
---
# <a name="about-expressroute-direct"></a>Informacje o usłudze ExpressRoute Direct

Funkcja ExpressRoute Direct umożliwia bezpośrednie nawiązywanie połączenia z siecią globalną firmy Microsoft w przypadku lokalizacji komunikacji równorzędnej strategicznie dystrybuowanej na całym świecie. Funkcja ExpressRoute Direct oferuje dwie połączenia o szybkości 100 GB/s lub 10 GB/s, które obsługują łączność aktywną/aktywną w dużej skali.

Najważniejsze funkcje dostarczane przez program ExpressRoute Direct obejmują, ale nie są ograniczone do:

* Masywne pozyskiwanie danych do usług, takich jak Storage i Cosmos DB
* Fizyczna izolacja dla branż, które regulują i wymagają dedykowanych i izolowanych połączeń, takich jak bankowość, instytucje rządowe i sprzedaż detaliczna
* Pełna kontrola nad rozmieszczeniem obwodów na podstawie jednostek biznesowych

## <a name="onboard-to-expressroute-direct"></a>Dołączanie do ExpressRoute Direct

Przed użyciem usługi ExpressRoute Direct należy najpierw zarejestrować swoją subskrypcję. Aby zarejestrować się, Wyślij wiadomość E-mail <ExpressRouteDirect@microsoft.com> z identyfikatorem subskrypcji, w tym następujące szczegóły:

* Scenariusze, które chcesz zrealizować za pomocą **ExpressRoute Direct**
* Preferencje lokalizacji — zobacz [partnerzy i lokalizacje komunikacji równorzędnej](expressroute-locations-providers.md) , aby uzyskać pełną listę wszystkich lokalizacji
* Oś czasu dla wdrożenia
* Inne pytania

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute przy użyciu dostawcy usług i usługi ExpressRoute Direct

| **ExpressRoute przy użyciu dostawcy usług** | **Usługa ExpressRoute Direct** | 
| --- | --- |
| Wykorzystuje dostawców usług w celu umożliwienia szybkiego dołączania i łączności do istniejącej infrastruktury | Wymaga infrastruktury 100 GB/s/10 GB/s i pełnego zarządzania wszystkimi warstwami
| Integruje się z setkami dostawców, w tym Ethernet i MPLS | Bezpośrednia/dedykowana pojemność dla branż objętych regulacją i ogromne pozyskiwanie danych |
| Usługi SKU obwodów z 50 MB/s do 10 GB/s | Klient może wybrać kombinację następujących jednostek SKU obwodu na 100 GB/s ExpressRoute bezpośrednio: <ul><li>5 Gb/s</li><li>10 Gb/s</li><li>40 Gb/s</li><li>100 Gb/s</li></ul> Klient może wybrać kombinację następujących jednostek SKU obwodu na 10 GB/s ExpressRoute bezpośrednio:<ul><li>1 Gb/s</li><li>2 Gb/s</li><li>5 Gb/s</li><li>10 Gb/s</li></ul>
| Optymalizacja pod kątem pojedynczej dzierżawy | Optymalizacja pod kątem pojedynczej dzierżawy z wieloma jednostkami biznesowymi i wieloma środowiskami roboczymi

## <a name="expressroute-direct-circuits"></a>Obwody bezpośrednie ExpressRoute

Usługa Microsoft Azure ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft za pośrednictwem połączenia prywatnego obsługiwanego przez dostawcę połączenia. Dzięki usłudze ExpressRoute można nawiązywać połączenia z usługami w chmurze firmy Microsoft, takimi jak Microsoft Azure i Microsoft 365.

Każda lokalizacja komunikacji równorzędnej ma dostęp do sieci globalnej firmy Microsoft i ma domyślnie dostęp do każdego regionu w strefie geopolitycznej i może uzyskać dostęp do wszystkich regionów globalnych za pomocą obwodu Premium.  

Funkcje w większości scenariuszy są równoważne z obwodami używanymi przez dostawcę usług ExpressRoute. Aby zapewnić możliwość dalszej szczegółowości i nowych możliwości oferowanych przy użyciu usługi ExpressRoute Direct, istnieją pewne kluczowe możliwości, które istnieją w obwodach bezpośrednich ExpressRoute.

## <a name="circuit-skus"></a>Jednostki SKU obwodu

Usługa ExpressRoute Direct obsługuje ogromne scenariusze pozyskiwania danych w usłudze Azure Storage i innych usługach Big Data. Obwody usługi ExpressRoute na 100 GB/s ExpressRoute bezpośrednio obsługują także **40 GB/s** i 100 jednostki SKU obwodów **GB** /s. Fizyczne pary portów to **100 lub 10 GB/s** i mogą mieć wiele obwodów wirtualnych. Rozmiary obwodu:

| **100 GB/s ExpressRoute Direct** | **10 GB/s ExpressRoute Direct** | 
| --- | --- |
| **Zasubskrybowana przepustowość**: 200 GB/s | **Zasubskrybowana przepustowość**: 20 GB/s |
| <ul><li>5 Gb/s</li><li>10 Gb/s</li><li>40 Gb/s</li><li>100 Gb/s</li></ul> | <ul><li>1 Gb/s</li><li>2 Gb/s</li><li>5 Gb/s</li><li>10 Gb/s</li></ul>

## <a name="technical-requirements"></a>Wymagania techniczne

* Interfejsy Microsoft Enterprise Edge router (MSEE):
    * Dwa lub 100 Gigabit Ethernet Ports tylko dla pary routerów
    * LR łączności Fiber z pojedynczym trybem
    * IPv4 i IPv6
    * IP MTU 1500 bajtów

* Połączenia warstwy 2/warstwy 3/routera:
    * Musi obsługiwać 1 tag 802.1 Q (Dot1Q) lub dwa Tagi 802.1 Q (QinQ) tag
    * Etertype = 0x8100
    * Należy dodać zewnętrzny tag sieci VLAN (STAG) na podstawie identyfikatora sieci VLAN określonego przez firmę Microsoft — *dotyczy tylko QinQ*
    * Musi obsługiwać wiele sesji protokołu BGP (VLAN) na port i urządzenie
    * Łączność z protokołami IPv4 i IPv6. *W przypadku protokołu IPv6 nie zostanie utworzony dodatkowy interfejs podrzędny. Adres IPv6 zostanie dodany do istniejącego interfejsu podrzędnego*. 
    * Opcjonalne: Obsługa funkcji [wykrywania przekazywania dwukierunkowego (BFD)](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) , która jest konfigurowana domyślnie na wszystkich prywatnych komunikacji równorzędnej w obwodach usługi ExpressRoute

## <a name="vlan-tagging"></a>Znakowanie sieci VLAN

Funkcja ExpressRoute Direct obsługuje znakowanie sieci VLAN QinQ i Dot1Q.

* **Znakowanie sieci VLAN QinQ** umożliwia izolowanie domen routingu na podstawie obwodu ExpressRoute. Platforma Azure dynamicznie przydziela znacznik S do podczas tworzenia obwodu i nie można go zmienić. Każda Komunikacja równorzędna w obwodzie (prywatna i Microsoft) będzie używać unikatowego tagu C jako sieci VLAN. Tag C-nie musi być unikatowy w obrębie obwodów w portach bezpośrednich ExpressRoute.

* **Znakowanie sieci VLAN Dot1Q** umożliwia korzystanie z pojedynczej OTAGOWANEJ sieci VLAN na podstawie pary portów bezpośredniego ExpressRoute. Tag języka C używany w komunikacji równorzędnej musi być unikatowy dla wszystkich obwodów i komunikacji równorzędnej w parze portu ExpressRoute Direct.

## <a name="workflow"></a>Przepływ pracy

[![utworzonego](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>Umowa SLA

Usługa ExpressRoute Direct zapewnia taką samą umowę SLA klasy korporacyjnej z aktywnymi/aktywnymi nadmiarowymi połączeniami w sieci globalnej firmy Microsoft. Infrastruktura ExpressRoute jest nadmiarowa, a łączność z siecią globalną firmy Microsoft jest nadmiarowa i zróżnicowana i odpowiednio skalowana w wymaganiach klientów. 

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie usługi ExpressRoute Direct](expressroute-howto-erdirect.md)
