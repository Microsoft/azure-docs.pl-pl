---
title: 'Azure ExpressRoute: przepływy pracy konfiguracji obwodu'
description: Ta strona zawiera przepływy pracy służące do konfigurowania obwodu usługi ExpressRoute i komunikacji równorzędnej
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75864370"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Przepływy pracy ExpressRoute dla aprowizacji obwodu i stanów obwodu
Ta strona przeprowadzi Cię przez przepływy pracy konfigurowania aprowizacji i routingu usług na wysokim poziomie.

![przepływ pracy obwodu](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Poniższy rysunek i odpowiednie kroki przedstawiają zadania w celu zapewnienia kompleksowego obwodu ExpressRoute. 

1. Użyj programu PowerShell, aby skonfigurować obwód ExpressRoute. Aby uzyskać więcej informacji, postępuj zgodnie z instrukcjami w artykule [Tworzenie obwodów usługi ExpressRoute](expressroute-howto-circuit-classic.md) .
2. Zamów łączność z dostawcą usług. Ten proces jest różny. Skontaktuj się z dostawcą połączenia, aby uzyskać więcej informacji o sposobach kolejności łączności.
3. Upewnij się, że obwód został pomyślnie zainicjowany przez zweryfikowanie stanu aprowizacji obwodu ExpressRoute za pomocą programu PowerShell. 
4. Skonfiguruj domeny routingu. Jeśli dostawca połączenia zarządza konfiguracją warstwy 3, skonfiguruje Routing dla obwodu. Jeśli dostawca połączenia oferuje tylko usługi warstwy 2, należy skonfigurować Routing zgodnie z wytycznymi opisanymi na stronach [wymagania dotyczące routingu](expressroute-routing.md) i [konfiguracji routingu](expressroute-howto-routing-classic.md) .
   
   * Włącz prywatną komunikację równorzędną Azure — Włącz tę komunikację równorzędną, aby połączyć się z maszynami wirtualnymi/usługami w chmurze wdrożonymi w ramach sieci wirtualnych.

   * Włącz komunikację równorzędną firmy Microsoft — Włącz tę opcję, aby uzyskać dostęp do programu Microsoft Usługi online, takiego jak Office 365. Wszystkie usługi Azure PaaS są dostępne za pomocą komunikacji równorzędnej firmy Microsoft.
     
     > [!IMPORTANT]
     > Musisz się upewnić, że używasz oddzielnego serwera proxy/krawędzi do łączenia się z firmą Microsoft niż ten, który jest używany przez Internet. Korzystanie z tej samej krawędzi zarówno dla ExpressRoute, jak i Internetu spowoduje, że Routing asymetryczny i spowoduje awarię łączności dla sieci.
     > 
     > 
     
     ![przepływy pracy routingu](./media/expressroute-workflows/routing-workflow.png)
5. Łączenie sieci wirtualnych z obwodami usługi ExpressRoute — można połączyć sieci wirtualne z obwodem ExpressRoute. Postępuj zgodnie [z instrukcjami, aby połączyć sieci wirtualnych](expressroute-howto-linkvnet-arm.md) z obwodem. Te sieci wirtualnych mogą znajdować się w tej samej subskrypcji platformy Azure co obwód usługi ExpressRoute lub mogą znajdować się w innej subskrypcji.

## <a name="expressroute-circuit-provisioning-states"></a>Stany aprowizacji obwodu ExpressRoute
Każdy obwód ExpressRoute ma dwa stany:

* Stan aprowizacji dostawcy usług
* Stan

Stan przedstawia stan aprowizacji firmy Microsoft. Ta właściwość jest ustawiona na wartość Enabled podczas tworzenia obwodu ExpressRoute

Stan aprowizacji dostawcy łączności reprezentuje stan po stronie dostawcy łączności. Może to być *NotProvisioned*, *Inicjowanie obsługi administracyjnej*lub *Inicjowanie obsługi administracyjnej*. Obwód ExpressRoute musi być w stanie aprowizacji, aby można było skonfigurować komunikację równorzędną.

### <a name="possible-states-of-an-expressroute-circuit"></a>Możliwe stany obwodu ExpressRoute
Ta sekcja zawiera listę możliwych stanów obwodu ExpressRoute.

**W czasie tworzenia**

Obwód ExpressRoute będzie zgłaszał następujące stany podczas tworzenia zasobu.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Gdy dostawca łączności jest w trakcie aprowizacji obwodu**

Obwód usługi ExpressRoute będzie zgłaszał następujące stany, gdy dostawca łączności pracuje nad udostępnieniem obwodu.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Gdy dostawca połączenia zakończył proces aprowizacji**

Obwód usługi ExpressRoute będzie zgłaszał następujące stany, gdy dostawca połączenia pomyślnie udostępnił obwód.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**Gdy dostawca łączności jest w trakcie anulowania aprowizacji obwodu**

Jeśli obwód usługi ExpressRoute musi zostać anulowany, obwód będzie zgłaszał następujące stany, gdy dostawca usług ukończy proces anulowania aprowizacji.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


W razie potrzeby możesz ponownie włączyć tę funkcję lub uruchomić polecenia cmdlet programu PowerShell, aby usunąć obwód.  

> [!IMPORTANT]
> Nie można usunąć obwodu, gdy ServiceProviderProvisioningState jest aprowizacji lub aprowizacji. Dostawca łączności wymaga anulowania aprowizacji obwodu, zanim będzie można go usunąć. Firma Microsoft będzie kontynuować rozliczanie obwodu do momentu usunięcia zasobu obwodu usługi ExpressRoute na platformie Azure.
> 

## <a name="routing-session-configuration-state"></a>Stan konfiguracji sesji routingu
Stan inicjowania obsługi protokołu BGP jest raportowany, jeśli sesja BGP została włączona w przeglądarce Microsoft Edge. Stan musi być włączony, aby można było używać komunikacji równorzędnej prywatnej lub firmy Microsoft.

Ważne jest, aby sprawdzić stan sesji BGP szczególnie w przypadku komunikacji równorzędnej firmy Microsoft. Oprócz stanu inicjowania obsługi protokołu BGP istnieje inny stan o nazwie *anonsowane publiczne prefiksy stanu*. Stan anonsowanych publicznych prefiksów musi znajdować się w *skonfigurowanym* stanie, zarówno w przypadku sesji protokołu BGP, jak i dla routingu do końca. 

Jeśli stan zaanonsowanego prefiksu publicznego jest ustawiony na stan *konieczny weryfikacji* , sesja protokołu BGP nie jest włączona, ponieważ anonsowane prefiksy nie pasują do numeru AS w żadnym z rejestrów routingu. 

> [!IMPORTANT]
> Jeśli anonsowane publiczne prefiksy stanu są w stanie *walidacji ręcznej* , należy otworzyć bilet pomocy technicznej w ramach [pomocy technicznej firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) i podać dowód, że adresy IP są anonsowane wraz ze skojarzonym numerem systemu autonomicznego.
> 
> 

## <a name="next-steps"></a>Następne kroki
* Skonfiguruj połączenie usługi ExpressRoute.
  
  * [Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)](expressroute-howto-circuit-arm.md)
  * [Configure routing (Konfigurowanie routingu)](expressroute-howto-routing-arm.md)
  * [Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](expressroute-howto-linkvnet-arm.md)

