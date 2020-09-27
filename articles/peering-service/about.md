---
title: Omówienie usługi Komunikacja równorzędna Azure
description: Informacje o usłudze Azure peering Service — Omówienie
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 03ece391b6f33b47f34705cec1c5907602ac7b23
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400183"
---
# <a name="azure-peering-service-overview"></a>Omówienie usługi Komunikacja równorzędna Azure

Usługa komunikacji równorzędnej Azure to usługa sieciowa, która zwiększa łączność klienta z usługami w chmurze firmy Microsoft, takimi jak Microsoft 365, Dynamics 365, oprogramowanie jako usługa (SaaS), Azure lub wszelkie usługi firmy Microsoft dostępne za pośrednictwem publicznej sieci Internet. Firma Microsoft współpracuje z dostawcami usługodawców internetowych (ISP), internetowymi partnerami wymiany (IXPs) i zdefiniowanymi programowo dostawcami usług Cloud Interconnect (SDCI) w celu zapewnienia niezawodnej i wysoce wydajnej łączności publicznej z optymalnym routingiem od klienta do sieci firmy Microsoft.

Za pomocą usługi Komunikacja równorzędna klienci mogą wybrać dobrze połączonego dostawcę usług partnerskich w danym regionie. Łączność publiczna jest zoptymalizowana pod kątem wysokiej niezawodności i minimalnego opóźnienia z usług w chmurze do lokalizacji użytkownika końcowego.

![Łączność rozproszona z chmurą firmy Microsoft](./media/peering-service-about/peering-service-what.png)

Klienci mogą również wybrać usługę Komunikacja równorzędna, taką jak miary opóźnienia użytkowników w sieci firmy Microsoft, monitorowanie tras BGP i alerty względem wycieków i przejęcia przez zarejestrowanie połączenia usługi komunikacji równorzędnej w Azure Portal. 

Aby korzystać z usługi komunikacji równorzędnej, klienci nie muszą rejestrować się w firmie Microsoft. Jedynym wymaganiem jest skontaktowanie się z [partnerem usługi komunikacji równorzędnej](location-partners.md) w celu uzyskania usługi. Aby włączyć telemetrię usługi komunikacji równorzędnej, klienci muszą zarejestrować się w niej w Azure Portal.

Aby uzyskać instrukcje dotyczące sposobu rejestrowania usługi komunikacji równorzędnej, zobacz temat [Rejestrowanie usługi komunikacji równorzędnej przy użyciu Azure Portal](azure-portal.md). 

> [!NOTE]
> Ten artykuł jest przeznaczony dla architektów sieci, które są odpowiedzialne za łączność w przedsiębiorstwie z chmurą i Internetem.


## <a name="what-is-peering-service"></a>Co to jest usługa Peering Service?

Usługa komunikacji równorzędnej:

- Usługa IP, która korzysta z publicznego Internetu. 
- Platforma współpracy z dostawcami usług i usługa o wartości dodanej, która ma oferować optymalne i niezawodne kierowanie do klientów za pośrednictwem partnerów dostawcy usług w chmurze firmy Microsoft przez sieć publiczną.

Usługa komunikacji równorzędnej nie jest produktem do łączności prywatnej, takim jak Azure ExpressRoute lub produkt sieci VPN.

> [!NOTE]
> Aby uzyskać więcej informacji na temat ExpressRoute, zobacz [dokumentację ExpressRoute](https://docs.microsoft.com/azure/expressroute/).
>

## <a name="background"></a>Tło

Microsoft 365, Dynamics 365 i inne usługi Microsoft SaaS Services są hostowane w wielu centrach danych firmy Microsoft i można do nich uzyskać dostęp z dowolnej lokalizacji geograficznej. Sieć globalna firmy Microsoft zawiera lokalizacje punktu obecności (PoP) przeglądarki Microsoft Edge na całym świecie, w którym może się połączyć z użytkownikiem końcowym za pośrednictwem swoich dostawców usług. 

Dostawcy usług firmy Microsoft i partnerów zapewniają, że ruch dla prefiksów zarejestrowanych przy użyciu połączenia z usługą komunikacji równorzędnej przechodzi i opuszcza najbliższe lokalizacje PoP przeglądarki Microsoft Edge w sieci globalnej firmy Microsoft. Firma Microsoft zapewnia, że ruch sieciowy pochodzący z prefiksów zarejestrowanych przy użyciu połączeń usług komunikacji równorzędnej przenosi najbliższe lokalizacje PoP przeglądarki Microsoft Edge w sieci globalnej firmy Microsoft.

![Sieć firmy Microsoft i łączność publiczna](./media/peering-service-about/peering-service-background-final.png)

> [!NOTE]
> Aby uzyskać więcej informacji o sieci globalnej firmy Microsoft, zobacz [Microsoft Global Network](https://docs.microsoft.com/azure/networking/microsoft-global-network).
>

## <a name="why-use-peering-service"></a>Dlaczego warto używać usługi komunikacji równorzędnej?

Przedsiębiorstwa poszukujące dostępu przez Internet do chmury lub biorąc pod uwagę architekturę SD-WAN lub wysokie użycie usług Microsoft SaaS Services potrzebują niezawodnej i wysoce wydajnej łączności z Internetem. Klienci mogą dokonywać tego przejścia przy użyciu usługi komunikacji równorzędnej. Usługodawcy firmy Microsoft i usługodawcy współpracują z zapewnianiem niezawodnej i zorientowanej na wydajność publicznej łączności z chmurą firmy Microsoft. Niektóre z najważniejszych funkcji klienta są wymienione tutaj:

- Najlepsze Routing publiczny przez Internet w celu Microsoft Azure Cloud Services w celu uzyskania optymalnej wydajności i niezawodności.
- Możliwość wybrania preferowanego dostawcy usług w celu nawiązania połączenia z chmurą firmy Microsoft.
- Informacje o ruchu, takie jak raportowanie opóźnień i monitorowanie prefiksów.
- Optymalne przeskoki sieciowe (jako przeskoki) z chmury firmy Microsoft.
- Kierowanie analiz i statystyk: zdarzenia dotyczące anomalii tras ([BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) (Wykrywanie przecieków lub przejęcia) i nieoptymalny Routing.

### <a name="robust-reliable-peering"></a>Niezawodne, niezawodne komunikację równorzędną

Usługa komunikacji równorzędnej używa dwóch typów nadmiarowości:

- **Nadmiarowość lokalna**

   Dostawcy firmy Microsoft i usług mogą łączyć się z wieloma lokalizacjami PoP przeglądarki Microsoft Edge w celu dostarczania usługi komunikacji równorzędnej. W każdej lokalizacji połączenie musi obsługiwać tryb failover między dwoma routerami.

   Każda lokalizacja komunikacji równorzędnej jest inicjowana przy użyciu nadmiarowych i różnorodnych linków komunikacji równorzędnej.

- **Nadmiarowość geograficzna**

   Firma Microsoft nawiązuje połączenie z dostawcami usług w wielu lokalizacjach Metro, dzięki czemu w przypadku, gdy jeden z węzłów brzegowych ma obniżoną wydajność, trasy ruchu do i od firmy Microsoft za pośrednictwem lokacji alternatywnych. Firma Microsoft kieruje ruch w sieci globalnej przy użyciu zasad routingu opartych na SDN w celu uzyskania optymalnej wydajności.

    Ten typ nadmiarowości używa najkrótszej ścieżki routingu przez zawsze wybranie najbliższego punktu PoP przeglądarki Microsoft Edge do użytkownika końcowego i gwarantuje, że klient będzie jednym przeskokiem sieci (w postaci przeskoków) od firmy Microsoft.

   ![Nadmiarowość geograficzna](./media/peering-service-about/peering-service-geo-shortest.png)

### <a name="optimal-routing"></a>Optymalny Routing

Preferowana jest następująca technika routingu:

-  **Routing zimnej ziemniaka**

   Zdefiniowana programowo technika routingu zimnej oferty oferuje kontrolę nad ruchem sieciowym, który pochodzi z chmury firmy Microsoft. Gwarantuje to, że ruch w sieci globalnej o dużej pojemności, małym opóźnieniu i o wysokiej niezawodności jest niezawodny do momentu, gdy jest to możliwe.
   
   Routing, który nie korzysta z techniki zimnej ziemniaka, jest określany jako funkcja routingu gorąca. W przypadku routingu gorąca, ruch pochodzący z chmury firmy Microsoft jest kierowany przez Internet.

   ![Routing zimnej ziemniaka](./media/peering-service-about/peering-service-cold-potato.png)

### <a name="monitoring-platform"></a>Platforma monitorowania

   Funkcja monitorowania usług jest oferowana do analizowania ruchu i routingu klientów oraz zapewnia następujące możliwości: 

-  **Wykrywanie anomalii trasy internetowego protokołu BGP**
          
   Ta usługa służy do wykrywania i wyzwalania alertów dotyczących wszelkich zdarzeń związanych z anomalią tras, takich jak przejmowanie trasy do prefiksów klientów.

-  **Opóźnienie klienta**

   Ta usługa monitoruje wydajność routingu między lokalizacją klienta i firmą Microsoft. 
   
   Wydajność routingu jest mierzona przez weryfikację czasu rundy wykonywanego z klienta w celu uzyskania dostępu do okna przeglądarki Microsoft Edge. Klienci mogą przeglądać raporty o opóźnieniu dla różnych lokalizacji geograficznych.

   Monitorowanie przechwytuje zdarzenia w przypadku ewentualnego obniżenia poziomu usługi.

   ![Platforma monitorowania dla usługi Komunikacja równorzędna](media/peering-service-about/peering-service-latency-report.png)

### <a name="traffic-protection"></a>Ochrona ruchu

Routing odbywa się tylko za pośrednictwem preferowanej ścieżki zdefiniowanej podczas rejestracji klienta w usłudze komunikacji równorzędnej.

Firma Microsoft gwarantuje kierowanie ruchu sieciowego za pośrednictwem preferowanych ścieżek nawet w przypadku wykrycia złośliwego działania.

Anomalie trasy BGP są zgłaszane w Azure Portal, jeśli istnieją.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o połączeniach komunikacji równorzędnej, zobacz [Komunikacja równorzędna usługi](connection.md).
- Aby dowiedzieć się więcej o telemetrii połączenia z usługą komunikacji równorzędnej, zobacz [Komunikacja równorzędna połączenia z usługą](connection-telemetry.md).
- Aby znaleźć partnera dostawcy usług, zobacz [Komunikacja równorzędna z partnerami i lokalizacjami usług](location-partners.md).
- Aby dołączyć połączenie z usługą komunikacji równorzędnej, zobacz Dołączanie [modelu usług komunikacji równorzędnej](onboarding-model.md).
- Aby zarejestrować połączenie przy użyciu Azure Portal, zobacz [Rejestrowanie połączenia z usługą komunikacji równorzędnej przy użyciu Azure Portal](azure-portal.md).
- Aby mierzyć dane telemetryczne, zobacz [mierzenie danych telemetrycznych połączenia](measure-connection-telemetry.md).
