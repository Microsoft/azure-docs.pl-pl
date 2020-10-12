---
title: 'Azure ExpressRoute: obwody i Komunikacja równorzędna'
description: Ta strona zawiera omówienie obwodów usługi ExpressRoute oraz domen routingu/komunikacji równorzędnej.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: duau
ms.openlocfilehash: 072eeb0bee0d0441549d2edad448f3b1c85a28a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89566504"
---
# <a name="expressroute-circuits-and-peering"></a>ExpressRoute circuits and peering (Obwody usługi ExpressRoute i komunikacja równorzędna)

Obwody usługi ExpressRoute łączą infrastrukturę lokalną z firmą Microsoft za pośrednictwem dostawcy łączności. Ten artykuł pomaga zrozumieć obwody usługi ExpressRoute i domeny routingu/komunikację równorzędną. Na poniższej ilustracji przedstawiono logiczną reprezentację łączności między siecią WAN i firmą Microsoft.

![Diagram przedstawiający sposób, w jaki obwody usługi ExpressRoute łączą infrastrukturę lokalną z firmą Microsoft za pośrednictwem dostawcy łączności.](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Publiczna Komunikacja równorzędna Azure jest przestarzała i nie jest dostępna dla nowych obwodów usługi ExpressRoute. Nowe obwody obsługują komunikację równorzędną firmy Microsoft i prywatną komunikację równorzędną.  
>

## <a name="expressroute-circuits"></a><a name="circuits"></a>Obwody usługi ExpressRoute

Obwód usługi ExpressRoute reprezentuje połączenie logiczne między infrastrukturą lokalną i usługami w chmurze firmy Microsoft przez dostawcę połączenia. Można zamówić wiele obwodów usługi ExpressRoute. Każdy obwód może znajdować się w tym samym lub różnych regionach i może być połączony z Twoim lokalem za pośrednictwem różnych dostawców łączności.

Obwody usługi ExpressRoute nie są mapowane na żadne jednostki fizyczne. Obwód jest jednoznacznie identyfikowany przez standardowy identyfikator GUID wywoływany jako klucz usługi (s-Key). Klucz usługi jest jedyną informacją wymienianą między firmą Microsoft, dostawcą połączenia i ty. Klucz s-Key nie jest wpisem tajnym ze względów bezpieczeństwa. Istnieje mapowanie 1:1 między obwodem ExpressRoute a kluczem s-Key.

Nowe obwody usługi ExpressRoute mogą obejmować dwie niezależne elementy równorzędne: prywatną komunikację równorzędną i komunikację równorzędną firmy Microsoft. Istniejące obwody usługi ExpressRoute mogą zawierać trzy sieci równorzędne: publiczne platformy Azure, prywatne i Microsoft. Każda Komunikacja równorzędna jest sparowana z niezależnymi sesjami BGP, a każda z nich jest konfigurowana w sposób nadmiarowy w celu zapewnienia wysokiej dostępności. Istnieje 1: N (1 <= N <= 3) mapowania między obwodem ExpressRoute i domenami routingu. Obwód ExpressRoute może mieć jeden, dwa lub wszystkie trzy elementy równorzędne włączone na obwód ExpressRoute.

Każdy obwód ma stałą przepustowość (50 MB/s, 100 MB/s, 200 MB/s, 500 MB/s, 1 GB/s) i jest mapowany do dostawcy połączenia i lokalizacji komunikacji równorzędnej. Wybrana przepustowość jest współdzielona przez wszystkie połączenia równorzędne obwodu

### <a name="quotas-limits-and-limitations"></a><a name="quotas"></a>Przydziały, limity i ograniczenia

Domyślne przydziały i limity mają zastosowanie do każdego obwodu usługi ExpressRoute. Informacje na temat limitów przydziału można znaleźć na stronie [limity subskrypcji i usługi Azure, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md) .

## <a name="expressroute-peering"></a><a name="routingdomains"></a>Komunikacja równorzędna ExpressRoute

Obwód usługi ExpressRoute ma skojarzone wiele domen routingu/komunikacji równorzędnej: publiczna platforma Azure, usługa Azure Private oraz firma Microsoft. Każda Komunikacja równorzędna jest konfigurowana identycznie na parze routerów (w konfiguracji aktywne-aktywne lub do udostępniania obciążenia) w celu zapewnienia wysokiej dostępności. Usługi platformy Azure są podzielone na *publiczne* i *prywatne* platformy Azure, aby reprezentować schematy adresowania IP.

![Diagram przedstawiający sposób, w jaki usługa Azure Public, Private i Komunikacja równorzędna firmy Microsoft jest konfigurowana w obwodzie usługi ExpressRoute.](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a><a name="privatepeering"></a>Prywatna komunikacja równorzędna platformy Azure

Usługi obliczeniowe platformy Azure, a w tym maszyny wirtualne (IaaS) i Cloud Services (PaaS), które są wdrożone w ramach sieci wirtualnej, można połączyć za pomocą domeny prywatnej komunikacji równorzędnej. Prywatna domena komunikacji równorzędnej jest uznawana za zaufane rozszerzenie sieci podstawowej na Microsoft Azure. Można skonfigurować dwukierunkową łączność między siecią podstawową i sieciami wirtualnymi platformy Azure (sieci wirtualnych). Ta Komunikacja równorzędna umożliwia łączenie się z maszynami wirtualnymi i usługami w chmurze bezpośrednio z ich prywatnych adresów IP.  

Można połączyć więcej niż jedną sieć wirtualną z domeną prywatnej komunikacji równorzędnej. Przejrzyj [stronę często zadawanych pytań](expressroute-faqs.md) , aby uzyskać informacje o ograniczeniach i ograniczeniach. Aby uzyskać aktualne informacje na temat limitów, można odwiedzić stronę [limity subskrypcji i usługi Azure, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md) .  Szczegółowe informacje na temat konfiguracji routingu można znaleźć na stronie [routingu](expressroute-routing.md) .

### <a name="microsoft-peering"></a><a name="microsoftpeering"></a>Komunikacja równorzędna firmy Microsoft

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Łączność z usługą Microsoft Usługi online (Microsoft 365 i usługi Azure PaaS Services) odbywa się za pośrednictwem komunikacji równorzędnej firmy Microsoft. Włączenie dwukierunkowej łączności między sieciami WAN i usługami firmy Microsoft w chmurze odbywa się za pośrednictwem domeny routingu komunikacji równorzędnej firmy Microsoft. Musisz nawiązać połączenie z usługami firmy Microsoft w chmurze tylko za pośrednictwem publicznych adresów IP, które są własnością użytkownika lub dostawcy połączenia, i musisz przestrzegać wszystkich zdefiniowanych reguł. Aby uzyskać więcej informacji, zobacz stronę [wymagań wstępnych ExpressRoute](expressroute-prerequisites.md) .

Na [stronie często zadawanych pytań](expressroute-faqs.md) znajdziesz więcej informacji na temat obsługiwanych usług, kosztów i szczegółów konfiguracji. Zapoznaj się z informacjami na stronie [lokalizacje ExpressRoute](expressroute-locations.md) , aby uzyskać informacje na temat listy dostawców połączeń oferujących obsługę komunikacji równorzędnej firmy Microsoft.

## <a name="peering-comparison"></a><a name="peeringcompare"></a>Porównanie komunikacji równorzędnej

Poniższa tabela zawiera porównanie trzech komunikacji równorzędnej:

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

W ramach obwodu usługi ExpressRoute można włączyć co najmniej jedną domenę routingu. Można wybrać, aby wszystkie domeny routingu zostały umieszczone w tej samej sieci VPN, jeśli chcesz połączyć je z jedną domeną routingu. Można je również umieścić w różnych domenach routingu, podobnie jak na diagramie. Zalecana konfiguracja polega na tym, że prywatna Komunikacja równorzędna jest połączona bezpośrednio z siecią podstawową, a linki komunikacji równorzędnej firmy Microsoft są połączone ze strefą DMZ.

Każda Komunikacja równorzędna wymaga oddzielnych sesji BGP (jedna para dla każdego typu komunikacji równorzędnej). Pary sesji BGP zapewniają link o wysokiej dostępności. W przypadku łączenia się za pośrednictwem dostawców połączeń w warstwie 2 użytkownik jest odpowiedzialny za Konfigurowanie routingu i zarządzanie nim. Więcej informacji można uzyskać, przeglądając [przepływy pracy](expressroute-workflows.md) służące do konfigurowania ExpressRoute.

## <a name="expressroute-health"></a><a name="health"></a>Kondycja usługi ExpressRoute

Obwody usługi ExpressRoute mogą być monitorowane pod kątem dostępności, łączności z sieci wirtualnychą i wykorzystaniem przepustowości przy użyciu [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (npm).

NPM monitoruje kondycję prywatnej komunikacji równorzędnej Azure i komunikacji równorzędnej firmy Microsoft. Zapoznaj się z naszym [wpisem](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) , aby uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki

* Znajdź dostawcę usługi. Zobacz [dostawcy usług ExpressRoute i lokalizacje](expressroute-locations.md).
* Upewnij się, że zostały spełnione wszystkie wymagania wstępne. Zobacz artykuł [ExpressRoute prerequisites](expressroute-prerequisites.md) (Wymagania wstępne usługi ExpressRoute).
* Skonfiguruj połączenie usługi ExpressRoute.
  * [Tworzenie obwodów usługi ExpressRoute i zarządzanie nimi](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurowanie routingu (komunikacji równorzędnej) dla obwodów usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
