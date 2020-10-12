---
title: 'Azure ExpressRoute: projektowanie pod kątem wysokiej dostępności'
description: Ta strona zawiera zalecenia dotyczące architektury w celu zapewnienia wysokiej dostępności podczas korzystania z usługi Azure ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: duau
ms.openlocfilehash: d6e63ad5781b0b8d6f46aee1cb88373721bb9dfc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89397903"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Projektowanie pod kątem wysokiej dostępności dzięki ExpressRoute

ExpressRoute jest przeznaczona do wysokiej dostępności w celu zapewnienia poufności łączności sieci prywatnej z zasobami firmy Microsoft. Innymi słowy, w ścieżce ExpressRoute w sieci firmy Microsoft nie ma single point of failure. Aby zmaksymalizować dostępność, segment klienta i usługodawcy obwodu usługi ExpressRoute powinien być również zaprojektowany w celu zapewnienia wysokiej dostępności. W tym artykule najpierw Przyjrzyjmy się zagadnieniom dotyczącym architektury sieci w przypadku tworzenia niezawodnej łączności sieciowej przy użyciu ExpressRoute, a następnie Przyjrzyjmy się funkcjom dostrajania, które pomogą Ci w zwiększeniu wysokiej dostępności obwodu usługi ExpressRoute.

>[!NOTE]
>Koncepcje opisane w tym artykule są równie stosowane, gdy obwód ExpressRoute jest tworzony w ramach wirtualnej sieci WAN lub poza nią.
>

## <a name="architecture-considerations"></a>Zagadnienia dotyczące architektury

Na poniższej ilustracji przedstawiono zalecany sposób nawiązywania połączenia przy użyciu obwodu usługi ExpressRoute w celu zmaksymalizowania dostępności obwodu usługi ExpressRoute.

 [![1]][1]

Aby zapewnić wysoką dostępność, niezbędne jest zachowanie nadmiarowości obwodu usługi ExpressRoute w całej sieci. Innymi słowy, konieczna jest obsługa nadmiarowości w sieci lokalnej i nie należy naruszać nadmiarowości w sieci dostawcy usług. Utrzymywanie nadmiarowości na minimalnym poziomie oznacza uniknięcie pojedynczych punktów awarii sieci. Zapewnienie nadmiarowej mocy i chłodzenia urządzeń sieciowych zwiększy wysoką dostępność.

### <a name="first-mile-physical-layer-design-considerations"></a>Zagadnienia dotyczące projektowania warstwy fizycznej pierwszego przebiegu

 W przypadku przerwania zarówno podstawowego, jak i dodatkowego połączenia obwodów usługi ExpressRoute na tym samym urządzeniu lokalnym klienta (CPE), nastąpi naruszenie wysokiej dostępności w sieci lokalnej. Ponadto w przypadku skonfigurowania zarówno połączenia podstawowego, jak i pomocniczego za pośrednictwem tego samego portu CPE (przez przerwanie dwóch połączeń w ramach różnych podinterfejsów lub przez scalanie dwóch połączeń w ramach sieci partnerskiej) wymuszenie naruszenia wysokiej dostępności segmentu sieci przez partnera. Ten kompromis został przedstawiony na poniższej ilustracji.

[![2]][2]

Z drugiej strony, jeśli zakończy się podstawowe i pomocnicze połączenia obwodów usługi ExpressRoute w różnych lokalizacjach geograficznych, nastąpi naruszenie wydajności sieci łączności. Jeśli ruch jest aktywnie zrównoważony przez podstawowe i pomocnicze połączenia, które kończą się w różnych lokalizacjach geograficznych, potencjalną istotną różnicą opóźnienia sieci między tymi dwiema ścieżkami może być nieoptymalna wydajność sieci. 

Zagadnienia dotyczące projektowania geograficznie nadmiarowego znajdują się w temacie [projektowanie na potrzeby odzyskiwania po awarii za pomocą ExpressRoute][DR].

### <a name="active-active-connections"></a>Połączenia aktywne-aktywne

Sieć firmy Microsoft jest skonfigurowana do obsługi połączeń podstawowych i pomocniczych obwodów usługi ExpressRoute w trybie aktywny-aktywny. Jednak za pomocą anonsów tras można wymusić, aby nadmiarowe połączenia obwodu usługi ExpressRoute działały w trybie aktywny-pasywny. Anonsowanie bardziej konkretnych tras i protokołu BGP jako ścieżki w oczekiwany sposób są typowymi technikami używanymi do udostępniania jednej ścieżki.

Aby zwiększyć wysoką dostępność, zaleca się przeprowadzenie obu połączeń obwodu usługi ExpressRoute w trybie aktywny-aktywny. Jeśli zezwolisz na połączenia działające w trybie aktywny-aktywny, Sieć Microsoft Network będzie równoważyć obciążenie ruchu przez połączenia na podstawie poszczególnych przepływów.

Uruchamianie podstawowych i pomocniczych połączeń obwodu usługi ExpressRoute w trybie aktywny-pasywny jest ryzykowne w przypadku niepowodzenia połączeń w aktywnej ścieżce. Typowymi przyczynami niepowodzenia przełączania jest brak aktywnego zarządzania połączeniem pasywnym, a połączenie pasywne anonsuje stare trasy.

Alternatywnie, uruchamianie podstawowych i pomocniczych połączeń obwodu usługi ExpressRoute w trybie aktywny-aktywny powoduje, że tylko połowa przepływów kończy się niepowodzeniem i przekierowania następuje po błędzie połączenia ExpressRoute. W ten sposób Tryb aktywny-aktywny znacznie pomaga zwiększyć średni czas odzyskiwania (MTTR).

### <a name="nat-for-microsoft-peering"></a>Translator adresów sieciowych dla komunikacji równorzędnej firmy Microsoft 

Komunikacja równorzędna firmy Microsoft jest przeznaczona do komunikacji między publicznymi punktami końcowymi. Często lokalne, prywatne punkty końcowe to adresy sieci tłumaczone (z identyfikatorem firmy) z publicznym adresem IP w sieci klienta lub partnera, zanim będą komunikować się za pośrednictwem komunikacji równorzędnej firmy Microsoft. Zakładając, że używane są zarówno podstawowe, jak i pomocnicze połączenia w trybie aktywny-aktywny, gdzie i w jaki sposób translator adresów sieciowych ma wpływ na sposób szybkiego odzyskiwania po awarii w jednym z połączeń ExpressRoute. Dwie różne opcje NAT przedstawiono na poniższej ilustracji:

[![r.3]][3]

W opcji 1 jest stosowane translator adresów sieciowych po rozdzieleniu ruchu między podstawowymi i pomocniczymi połączeniami ExpressRoute. Aby spełnić wymagania stanowe dotyczące translatora adresów sieciowych, na urządzeniach podstawowych i pomocniczych są używane niezależne pule NAT, dzięki czemu ruch powrotny dociera do tego samego urządzenia brzegowego, za pomocą którego przepływ egressed.

W opcji 2 jest używana wspólna Pula NAT przed podziałem ruchu między podstawowe i pomocnicze połączenia ExpressRoute. Ważne jest, aby rozróżniać, że wspólna Pula NAT przed podziałem ruchu nie oznacza wprowadzenia pojedynczego punktu awarii w celu uzyskania wysokiego poziomu dostępności.

W przypadku opcji 1, po błędzie połączenia ExpressRoute, możliwość osiągnięcia odpowiedniej puli translatora adresów sieciowych zostanie przerwana. W związku z tym wszystkie uszkodzone przepływy muszą być ponownie nawiązane przez TCP lub warstwę aplikacji po odpowiednim limicie czasu okna. Jeśli jedna z pul NAT jest używana do frontonu dowolnego serwera lokalnego i jeśli nastąpi awaria połączenia, serwery lokalne nie są dostępne na platformie Azure, dopóki nie zostanie naprawiona łączność.

W przypadku opcji 2 translator adresów sieciowych jest dostępny nawet po awarii połączenia podstawowego lub pomocniczego. W związku z tym warstwa sieci może przekierować pakiety i przyspieszyć odzyskiwanie po awarii. 

> [!NOTE]
> W przypadku używania opcji translatora adresów sieciowych (niezależnych pul NAT dla podstawowych i pomocniczych połączeń ExpressRoute) i mapowania portu adresu IP z jednej puli translatora adresów sieciowych na serwer lokalny serwer nie będzie dostępny za pośrednictwem obwodu usługi ExpressRoute w przypadku niepowodzenia odpowiedniego połączenia.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Funkcje dostrajania dla prywatnej komunikacji równorzędnej

W tej sekcji poinformuj nas o opcjonalnych (w zależności od wdrożenia platformy Azure oraz o tym, jak poufne dane są MTTR), które ułatwiają zwiększenie wysokiej dostępności obwodu usługi ExpressRoute. Zapoznaj się z tym tematem, aby zapoznać się z tematem Wdrażanie bram sieci wirtualnej ExpressRoute oraz funkcji wykrywania dwukierunkowego przekazywania (BFD).

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>ExpressRoute bramy sieci wirtualnej z obsługą stref dostępności

Strefa dostępności w regionie świadczenia usługi Azure jest kombinacją domeny błędów i domeny aktualizacji. W przypadku wybrania dla Strefowo nadmiarowego wdrożenia platformy Azure IaaS można również skonfigurować strefowo nadmiarowe bramy sieci wirtualnej, które kończą ExpressRoute prywatnej komunikacji równorzędnej. Aby dowiedzieć się więcej, zobacz [about Zone-nadmiarowe bramy sieci wirtualnej w strefy dostępności platformy Azure][zone redundant vgw]. Aby skonfigurować strefowo nadmiarową bramę sieci wirtualnej, zobacz [Tworzenie strefy nadmiarowej bramy sieci wirtualnej w strefy dostępności platformy Azure][conf zone redundant vgw].

### <a name="improving-failure-detection-time"></a>Poprawianie czasu wykrywania awarii

ExpressRoute obsługuje BFD za pośrednictwem prywatnej komunikacji równorzędnej. BFD zmniejsza czas wykrywania awarii w sieci warstwy 2 między Microsoft Enterprise Edge (MSEE) i ich sąsiadów BGP w miejscu lokalnym od około 3 minut (wartość domyślna) do mniej niż sekundy. Czas wykrywania krótkich błędów ułatwia hastening odzyskiwania po awarii. Aby dowiedzieć się więcej, zobacz [Konfigurowanie BFD na ExpressRoute][BFD].

## <a name="next-steps"></a>Następne kroki

W tym artykule omówiono sposób projektowania pod kątem wysokiej dostępności łączności obwodu usługi ExpressRoute. Punkt komunikacji równorzędnej obwodu ExpressRoute jest przypięty do lokalizacji geograficznej, w związku z czym może mieć wpływ Katastrofalny błąd, który ma wpływ na całą lokalizację. 

Aby poznać zagadnienia dotyczące projektowania w celu utworzenia geograficznie nadmiarowej łączności sieciowej z siecią szkieletową firmy Microsoft, która może wytrzymywać błędy krytyczne, które mają wpływ na cały region, zobacz [projektowanie pod kątem odzyskiwania po awarii za pomocą prywatnej komunikacji równorzędnej][DR]

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "zalecany sposób nawiązywania połączenia przy użyciu ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "najoptymalna łączność z ostatnim kilometrem"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "Opcje NAT"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




