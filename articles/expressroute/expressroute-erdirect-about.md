---
title: Informacje o usłudze Azure ExpressRoute Direct
description: Poznaj kluczowe funkcje usługi Azure ExpressRoute Direct i informacje niezbędne do dołączenia do ExpressRoute bezpośrednio, takie jak dostępne jednostki SKU i wymagania techniczne.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: duau
ms.openlocfilehash: 4b129a218f0fe90f632adef1325288b3f8d97d16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585965"
---
# <a name="about-expressroute-direct"></a>Informacje o usłudze ExpressRoute Direct

Funkcja ExpressRoute Direct umożliwia bezpośrednie nawiązywanie połączenia z siecią globalną firmy Microsoft w przypadku lokalizacji komunikacji równorzędnej strategicznie rozmieszczonych na całym świecie. Funkcja ExpressRoute Direct oferuje dwie połączenia o szybkości 100 GB/s lub 10 GB/s, które obsługują łączność aktywną/aktywną w dużej skali.

Najważniejsze funkcje dostarczane przez program ExpressRoute Direct obejmują, ale nie są ograniczone do:

* Masywne pozyskiwanie danych do usług, takich jak Storage i Cosmos DB
* Fizyczna izolacja dla branż, które regulują i wymagają dedykowanych i izolowanych połączeń, takich jak bankowość, instytucje rządowe i sprzedaż detaliczna
* Pełna kontrola nad rozmieszczeniem obwodów na podstawie jednostek biznesowych

## <a name="onboard-to-expressroute-direct"></a>Dołączanie do ExpressRoute Direct

Przed użyciem usługi ExpressRoute Direct należy najpierw zarejestrować swoją subskrypcję. Aby zarejestrować się, uruchom następujące polecenia przy użyciu Azure PowerShell:

1.  Zaloguj się do platformy Azure i wybierz subskrypcję, którą chcesz zarejestrować.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. Zarejestruj swoją subskrypcję dla publicznej wersji zapoznawczej przy użyciu następującego polecenia:
1. 
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

Po zarejestrowaniu upewnij się, że dostawca zasobów **Microsoft. Network** został zarejestrowany w Twojej subskrypcji. Rejestracja dostawcy zasobów umożliwia skonfigurowanie subskrypcji do pracy z dostawcą zasobów.

1. Uzyskaj dostęp do ustawień subskrypcji zgodnie z opisem w temacie [dostawcy zasobów platformy Azure i typy](../azure-resource-manager/management/resource-providers-and-types.md).

1. W subskrypcji dla **dostawców zasobów** Sprawdź, czy dostawca **Microsoft. Network** wskazuje **zarejestrowany** stan. Jeśli dostawca zasobów Microsoft. Network nie znajduje się na liście zarejestrowanych dostawców, Dodaj go.

Jeśli zaczniesz korzystać z programu ExpressRoute Direct i Zauważ, że w wybranej lokalizacji komunikacji równorzędnej nie ma dostępnych portów, Wyślij wiadomość e-mail, ExpressRouteDirect@microsoft.com Aby zażądać więcej informacji o zapasach.

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute przy użyciu dostawcy usług i usługi ExpressRoute Direct

| **ExpressRoute przy użyciu dostawcy usług** | **Usługa ExpressRoute Direct** | 
| --- | --- |
| Umożliwia korzystanie z dostawców usług w celu szybkiego dołączania i łączności do istniejącej infrastruktury | Wymaga infrastruktury 100 GB/s/10 GB/s i pełnego zarządzania wszystkimi warstwami
| Integruje się z setkami dostawców, w tym Ethernet i MPLS | Bezpośrednia/dedykowana pojemność dla branż objętych regulacją i ogromne pozyskiwanie danych |
| Usługi SKU obwodów z 50 MB/s do 10 GB/s | Klient może wybrać kombinację następujących jednostek SKU obwodu na 100 GB/s ExpressRoute bezpośrednio: <ul><li>5 Gb/s</li><li>10 Gb/s</li><li>40 Gb/s</li><li>100 Gb/s</li></ul> Klient może wybrać kombinację następujących jednostek SKU obwodu 10 GB/s ExpressRoute Direct:<ul><li>1 Gb/s</li><li>2 Gb/s</li><li>5 Gb/s</li><li>10 Gb/s</li></ul>
| Optymalizacja pod kątem pojedynczej dzierżawy | Optymalizacja pod kątem pojedynczej dzierżawy z wieloma jednostkami biznesowymi i wieloma środowiskami roboczymi

## <a name="expressroute-direct-circuits"></a>Obwody bezpośrednie ExpressRoute

Microsoft Azure ExpressRoute umożliwia rozszerzonie sieci lokalnej do chmury firmy Microsoft przez połączenie prywatne przez dostawcę połączenia. Dzięki usłudze ExpressRoute można nawiązywać połączenia z usługami w chmurze firmy Microsoft, takimi jak Microsoft Azure i Microsoft 365.

Każda lokalizacja komunikacji równorzędnej ma dostęp do sieci globalnej firmy Microsoft i domyślnie ma dostęp do każdego regionu w strefie geopolitycznej. Dostęp do wszystkich regionów globalnych można uzyskać za pomocą obwodu Premium.  

Funkcje w większości scenariuszy są równoważne z obwodami używanymi przez dostawcę usług ExpressRoute. Aby zapewnić możliwość dalszej szczegółowości i nowych możliwości oferowanych przy użyciu usługi ExpressRoute Direct, istnieją pewne kluczowe możliwości, które istnieją w obwodach bezpośrednich ExpressRoute.

## <a name="circuit-skus"></a>Jednostki SKU obwodu

Usługa ExpressRoute Direct obsługuje ogromne scenariusze pozyskiwania danych w usłudze Azure Storage i innych usługach Big Data. Obwody usługi ExpressRoute na 100-GB/s ExpressRoute bezpośrednio obsługują również **40 GB/** s 100 oraz jednostki SKU obwodów z procesorem GB/s. Fizyczne pary portów to **100 GB/s lub 10 GB/s** i mogą mieć wiele obwodów wirtualnych. Rozmiary obwodu:

| **100 – GB/s ExpressRoute Direct** | **10 GB/s ExpressRoute Direct** | 
| --- | --- |
| **Zasubskrybowana przepustowość**: 200 GB/s | **Zasubskrybowana przepustowość**: 20 GB/s |
| <ul><li>5 Gb/s</li><li>10 Gb/s</li><li>40 Gb/s</li><li>100 Gb/s</li></ul> | <ul><li>1 Gb/s</li><li>2 Gb/s</li><li>5 Gb/s</li><li>10 Gb/s</li></ul>

## <a name="technical-requirements"></a>Wymagania techniczne

* Interfejsy Microsoft Enterprise Edge router (MSEE):
    * Dwu 10 Gigabit lub 100-Gigabit Ethernet Ports tylko między parą router
    * LR łączności Fiber z pojedynczym trybem
    * IPv4 i IPv6
    * IP MTU 1500 bajtów

* Przełącz/router warstwa 2/warstwa trzy połączenia:
    * Musi obsługiwać 1 tag 802.1 Q (Dot1Q) lub dwa Tagi 802.1 Q (QinQ) tag
    * Etertype = 0x8100
    * Należy dodać zewnętrzny tag sieci VLAN (STAG) na podstawie identyfikatora sieci VLAN określonego przez firmę Microsoft — *dotyczy tylko QinQ*
    * Musi obsługiwać wiele sesji protokołu BGP (VLAN) na port i urządzenie
    * Łączność z protokołami IPv4 i IPv6. *W przypadku protokołu IPv6 nie zostanie utworzony dodatkowy interfejs podrzędny. Adres IPv6 zostanie dodany do istniejącego interfejsu podrzędnego*. 
    * Opcjonalne: Obsługa funkcji [wykrywania przekazywania dwukierunkowego (BFD)](./expressroute-bfd.md) , która jest konfigurowana domyślnie na wszystkich prywatnych komunikacji równorzędnej w obwodach usługi ExpressRoute

## <a name="vlan-tagging"></a>Znakowanie sieci VLAN

Funkcja ExpressRoute Direct obsługuje znakowanie sieci VLAN QinQ i Dot1Q.

* **Znakowanie sieci VLAN QinQ** umożliwia izolowanie domen routingu na podstawie obwodu ExpressRoute. Platforma Azure dynamicznie udostępnia tag S podczas tworzenia obwodu i nie może zostać zmieniony. Każda Komunikacja równorzędna w obwodzie (prywatna i Microsoft) będzie używać unikatowego tagu C jako sieci VLAN. Tag C-nie musi być unikatowy w obrębie obwodów w portach bezpośrednich ExpressRoute.

* **Znakowanie sieci VLAN Dot1Q** umożliwia korzystanie z pojedynczej OTAGOWANEJ sieci VLAN na podstawie pary portów bezpośredniego ExpressRoute. Tag języka C używany w komunikacji równorzędnej musi być unikatowy dla wszystkich obwodów i komunikacji równorzędnej w parze portu ExpressRoute Direct.

## <a name="workflow"></a>Przepływ pracy

[![utworzonego](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>Umowa SLA

Usługa ExpressRoute Direct zapewnia taką samą umowę SLA klasy korporacyjnej z aktywnymi/aktywnymi nadmiarowymi połączeniami w sieci globalnej firmy Microsoft. Infrastruktura ExpressRoute jest nadmiarowa, a łączność z siecią globalną firmy Microsoft jest nadmiarowa i różnorodna i skalowana w wymaganiach klientów. 

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie usługi ExpressRoute Direct](expressroute-howto-erdirect.md)
