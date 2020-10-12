---
title: 'Azure ExpressRoute: wymagania dotyczące translatora adresów sieciowych'
description: Ta strona zawiera szczegółowe wymagania dotyczące konfigurowania translatora adresów sieciowych oraz zarządzania nim na potrzeby obwodów usługi ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: a2c322c765d39a3afe4974ed88bf4dc18fd467a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89569649"
---
# <a name="expressroute-nat-requirements"></a>Wymagania dotyczące translatora adresów sieciowych w usłudze ExpressRoute
Aby połączyć się z usługami w chmurze firmy Microsoft przy użyciu usługi ExpressRoute, należy skonfigurować translatory adresów sieciowych oraz zarządzać nimi. Niektórzy dostawcy połączenia oferują konfigurowanie translatora adresów sieciowych oraz zarządzanie nim jako usługę zarządzaną. Skontaktuj się z dostawcą połączenia, aby sprawdzić, czy taka usługa jest oferowana. Jeśli nie, musisz spełnić wymagania opisane poniżej. 

Przejrzyj stronę [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (Obwody i domeny routingu usługi ExpressRoute), która zawiera omówienie różnych domen routingu. Aby spełnić wymagania dotyczące publicznego adresu IP dla publicznej komunikacji równorzędnej Azure i komunikacji równorzędnej Microsoft, warto skonfigurować translator adresów sieciowych między siecią a firmą Microsoft. W tej sekcji przedstawiono szczegółowy opis infrastruktury translatora adresów sieciowych, którego należy skonfigurować.

## <a name="nat-requirements-for-microsoft-peering"></a>Wymagania dotyczące translatora adresów sieciowych dla komunikacji równorzędnej firmy Microsoft
Ścieżka komunikacji równorzędnej firmy Microsoft umożliwia nawiązanie połączenia z usługami w chmurze firmy Microsoft, które nie są obsługiwane przez ścieżkę publicznej komunikacji równorzędnej Azure. Lista usług zawiera Microsoft 365 usług, takich jak Exchange Online, SharePoint Online i Skype dla firm. Firma Microsoft planuje obsługę dwukierunkowej łączności w oparciu o komunikację równorzędną firmy Microsoft. Ruch skierowany usług w chmurze firmy Microsoft musi zostać podłączony do funkcji SNAT i uzyskać prawidłowe publiczne adresy IPv4, zanim wejdzie do sieci firmy Microsoft. Ruch skierowany do Twojej sieci z usług w chmurze firmy Microsoft należy przetworzyć na granicy Internetu, aby zapobiec [routingowi asymetrycznemu](expressroute-asymmetric-routing.md). Poniższy rysunek przedstawia ogólny obraz sposobu konfigurowania translatora adresów sieciowych dla komunikacji równorzędnej firmy Microsoft.

![Diagram wysokiego poziomu, w jaki sposób należy skonfigurować translator adresów sieciowych dla komunikacji równorzędnej firmy Microsoft.](./media/expressroute-nat/expressroute-nat-microsoft.png) 

### <a name="traffic-originating-from-your-network-destined-to-microsoft"></a>Ruch pochodzący z sieci skierowany do firmy Microsoft
* Należy zadbać o to, by ruch wchodził na ścieżkę komunikacji równorzędnej Microsoft z prawidłowym publicznym adresem IPv4. Firma Microsoft musi mieć możliwość weryfikacji właściciela puli adresów translatora adresów sieciowych IPv4 względem regionalnego rejestru internetowego routingu (RIR) lub internetowego rejestru routingu (IRR). Nastąpi sprawdzenie w oparciu o numer AS połączony za pomocą komunikacji równorzędnej oraz adresy IP używane do translatora adresów sieciowych. Informacje dotyczące rejestrów routingu znajdują się w temacie [ExpressRoute routing requirements](expressroute-routing.md) (Wymagania dotyczące routingu w usłudze ExpressRoute).
* Adresów IP używanych do konfiguracji publicznej komunikacji równorzędnej Azure oraz innych obwodów usługi ExpressRoute nie można anonsować do firmy Microsoft za pośrednictwem sesji protokołu BGP. Nie ma żadnych ograniczeń w zakresie długości prefiksu IP translatora adresów sieciowych anonsowanego za pośrednictwem komunikacji równorzędnej.
  
  > [!IMPORTANT]
  > Pula adresów IP translatora adresów sieciowych anonsowana do firmy Microsoft nie może być anonsowana do Internetu. Spowodowałoby to przerwanie łączności z innymi usługami firmy Microsoft.
  > 
  > 

### <a name="traffic-originating-from-microsoft-destined-to-your-network"></a>Ruch pochodzący z firmy Microsoft skierowany do sieci
* Niektóre scenariusze wymagają od firmy Microsoft zainicjowania połączenia z punktami końcowymi usługi obsługiwanymi w ramach sieci użytkownika. Typowym przykładem tego scenariusza jest łączność z serwerami usług ADFS hostowanymi w sieci z Microsoft 365. W takich przypadkach należy przefiltrować odpowiednie prefiksy z sieci użytkownika do komunikacji równorzędnej firmy Microsoft. 
* Należy użyć funkcji SNAT względem ruchu z firmy Microsoft na granicy Internetu w punktach końcowych usługi w Twojej sieci, aby zapobiec [routingowi asymetrycznemu](expressroute-asymmetric-routing.md). Żądania **i odpowiedzi** z docelowym adresem IP zgodne z trasą odebraną za pomocą usługi ExpressRoute będą zawsze wysyłane przy użyciu usługi ExpressRoute. Routing asymetryczny występuje w przypadku odebrania żądania przez Internet z odpowiedzią wysłaną za pomocą usługi ExpressRoute. Użycie funkcji SNAT względem ruchu przychodzącego z firmy Microsoft na granicy Internetu wymusza zwrócenie ruchu odpowiedzi do granicy Internetu, co rozwiązuje problem.

![Routing asymetryczny przy użyciu usługi ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="nat-requirements-for-azure-public-peering"></a>Wymagania dotyczące translatora adresów sieciowych dla publicznej komunikacji równorzędnej Azure

> [!NOTE]
> Publiczna Komunikacja równorzędna Azure nie jest dostępna dla nowych obwodów.
> 

Ścieżka publicznej komunikacji równorzędnej Azure umożliwia łączenie ze wszystkimi usługami obsługiwanymi na platformie Azure za pośrednictwem ich publicznych adresów IP. Dotyczy to usług wymienionych w temacie [ExpessRoute FAQ](expressroute-faqs.md) (ExpessRoute — często zadawane pytania) i wszystkich usług obsługiwanych przez niezależnych dostawców oprogramowania na platformie Microsoft Azure. 

> [!IMPORTANT]
> Połączenie z usługami Microsoft Azure w publicznej komunikacji równorzędnej jest zawsze inicjowane z sieci użytkownika do sieci Microsoft. Dlatego sesji nie można zainicjować z poziomu usług Microsoft Azure do sieci za pośrednictwem usługi ExpressRoute. Po podjęciu takiej próby pakiety wysłane do anonsowanych adresów IP będą korzystać z Internetu, zamiast z usługi ExpressRoute.
> 

Ruch skierowany do platformy Microsoft Azure w publicznej komunikacji równorzędnej musi zostać podłączony do funkcji SNAT i uzyskać prawidłowe publiczne adresy IPv4, zanim wejdzie do sieci firmy Microsoft. Poniższy rysunek przedstawia, w jaki sposób na wysokim poziomie można skonfigurować translatora adresów sieciowych i spełnić powyższe wymaganie.

![Ogólny diagram przedstawiający sposób, w jaki można skonfigurować translator adresów sieciowych, aby był podłączony na prawidłowe publiczne adresy IPv4 przed wprowadzeniem do sieci firmy Microsoft.](./media/expressroute-nat/expressroute-nat-azure-public.png) 

### <a name="nat-ip-pool-and-route-advertisements"></a>Anonse puli adresów IP translatora adresów sieciowych oraz tras
Należy zadbać o to, by ruch wchodził na ścieżkę publicznej komunikacji równorzędnej Azure z prawidłowym publicznym adresem IPv4. Firma Microsoft musi mieć możliwość weryfikacji własności puli adresów translatora adresów sieciowych IPv4 względem regionalnego rejestru internetowego routingu (RIR) lub internetowego rejestru routingu (IRR). Nastąpi sprawdzenie w oparciu o numer AS połączony za pomocą komunikacji równorzędnej oraz adresy IP używane do translatora adresów sieciowych. Informacje dotyczące rejestrów routingu znajdują się w temacie [ExpressRoute routing requirements](expressroute-routing.md) (Wymagania dotyczące routingu w usłudze ExpressRoute).

Nie ma żadnych ograniczeń w zakresie długości prefiksu IP translatora adresów sieciowych anonsowanego za pośrednictwem komunikacji równorzędnej. Należy monitorować pulę translatora adresów sieciowych i zadbać o to, by nie zabrakło jego sesji.

> [!IMPORTANT]
> Pula adresów IP translatora adresów sieciowych anonsowana do firmy Microsoft nie może być anonsowana do Internetu. Spowodowałoby to przerwanie łączności z innymi usługami firmy Microsoft.
> 
> 

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z wymaganiami dotyczącymi [routingu](expressroute-routing.md) i technologii [QoS](expressroute-qos.md).
* Informacje o przepływach pracy można znaleźć w artykule [ExpressRoute circuit provisioning workflows and circuit states](expressroute-workflows.md) (Przepływy pracy inicjowania obsługi obwodu i stany obwodu usługi ExpressRoute).
* Skonfiguruj połączenie usługi ExpressRoute.
  
  * [Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)](expressroute-howto-circuit-portal-resource-manager.md)
  * [Configure routing (Konfigurowanie routingu)](expressroute-howto-routing-portal-resource-manager.md)
  * [Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](expressroute-howto-linkvnet-portal-resource-manager.md)

