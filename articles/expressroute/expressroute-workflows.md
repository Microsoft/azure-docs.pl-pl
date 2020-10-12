---
title: 'Azure ExpressRoute: przepływ pracy konfiguracji obwodu'
description: Na tej stronie przedstawiono przepływ pracy konfigurowania obwodów usługi ExpressRoute i komunikacji równorzędnej
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: duau
ms.custom: contperfq1
ms.openlocfilehash: bb0d3cebd9382ef2c81b217417cfbcb6198b6ebb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89565927"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Przepływy pracy ExpressRoute dla aprowizacji obwodu i stanów obwodu

Ten artykuł przeprowadzi Cię przez proces aprowizacji usług i przepływów pracy konfiguracji routingu na wysokim poziomie. W poniższych sekcjach opisano zadania umożliwiające zainicjowanie obwodu ExpressRoute.

## <a name="workflow-steps"></a>Etapy przepływu pracy

### <a name="1-prerequisites"></a>1. wymagania wstępne

Upewnij się, że wymagania wstępne zostały spełnione. Aby uzyskać pełną listę, zobacz [wymagania wstępne i listę kontrolną](expressroute-prerequisites.md).

* Utworzono subskrypcję platformy Azure.
* Łączność fizyczna została ustanowiona z partnerem ExpressRoute lub skonfigurowana za pośrednictwem usługi ExpressRoute Direct. Przejrzyj lokalizację, zobacz [lokalizacje i partnerzy](expressroute-locations-providers.md#partners) , aby wyświetlać ExpressRoute partnera i ExpressRoute bezpośrednie połączenia między lokalizacjami komunikacji równorzędnej.

### <a name="2-order-connectivity-or-configure-expressroute-direct"></a>2. Zamów połączenie lub skonfiguruj usługę ExpressRoute Direct

Zamów łączność z dostawcą usług lub skonfiguruj usługę ExpressRoute Direct.

#### <a name="expressroute-partner-model"></a>Model partnerski ExpressRoute

Zamów łączność z dostawcą usług. Ten proces jest różny. Skontaktuj się z dostawcą połączenia, aby uzyskać więcej informacji o sposobach kolejności łączności.

* Wybierz partnera ExpressRoute
* Wybierz lokalizację komunikacji równorzędnej
* Wybierz przepustowość
* Wybierz model rozliczeń
* Wybierz dodatek standardowy lub Premium

#### <a name="expressroute-direct-model"></a>Model bezpośredni ExpressRoute

* Wyświetl dostępne ExpressRoute bezpośrednie w różnych lokalizacjach komunikacji równorzędnej.
* Zarezerwuj porty, tworząc zasób ExpressRoute Direct w ramach subskrypcji platformy Azure.
* Żądaj i odbieraj autoryzację oraz Zamów fizyczne połączenia krzyżowe od dostawcy lokalizacji komunikacji równorzędnej.
* Włącz stan administratora i Wyświetl poziomy światła i link fizyczny przy użyciu [Azure monitor](expressroute-monitoring-metrics-alerts.md#expressroute-direct-metrics).

### <a name="3-create-an-expressroute-circuit"></a>3. Tworzenie obwodu ExpressRoute

#### <a name="expressroute-partner-model"></a>Model partnerski ExpressRoute

Sprawdź, czy partner ExpressRoute jest gotowy do aprowizacji łączności. Obwód usługi ExpressRoute jest rozliczany od momentu, gdy zostanie wystawiony klucz usług. Aby utworzyć obwód, Skorzystaj z instrukcji przedstawionych w temacie [Tworzenie obwodu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) .

#### <a name="expressroute-direct-model"></a>Model bezpośredni ExpressRoute

Upewnij się, że łącze fizyczne i stan administratora są włączone dla obu linków. Zapoznaj się z [tematem Konfigurowanie usługi ExpressRoute Direct](how-to-expressroute-direct-portal.md) w celu uzyskania wskazówek. Obwód usługi ExpressRoute jest rozliczany od momentu, gdy zostanie wystawiony klucz usług. Aby utworzyć obwód, Skorzystaj z instrukcji przedstawionych w temacie [Tworzenie obwodu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) .

### <a name="4-service-provider-provisions-connectivity"></a>4. dostawca usług zapewnia łączność z przepisami

Ta sekcja dotyczy tylko modelu łączności partnerskiej ExpressRoute:

* Podaj klucz usługi (s-Key) dostawcy połączenia.
* Podaj dodatkowe informacje wymagane przez dostawcę połączenia (na przykład identyfikator sieci VPN).
* Jeśli dostawca zarządza konfiguracją routingu, podaj niezbędne szczegóły.

Można upewnić się, że obwód został pomyślnie zainicjowany przez zweryfikowanie stanu aprowizacji obwodu usługi ExpressRoute przy użyciu programu PowerShell, Azure Portal lub interfejsu wiersza polecenia.

### <a name="5-configure-routing-domains"></a>5. Konfigurowanie domen routingu

Skonfiguruj domeny routingu. Jeśli dostawca połączenia zarządza konfiguracją warstwy 3, skonfiguruje Routing dla obwodu. Jeśli dostawca łączności oferuje tylko usługi warstwy 2 lub jeśli używasz programu ExpressRoute Direct, musisz skonfigurować Routing zgodnie z wytycznymi opisanymi w artykułach [wymagania dotyczące routingu](expressroute-routing.md) i [konfiguracji routingu](expressroute-howto-routing-classic.md) .

#### <a name="for-azure-private-peering"></a>W przypadku prywatnej komunikacji równorzędnej Azure

Włącz prywatną komunikację równorzędną, aby połączyć się z maszynami wirtualnymi i usługami w chmurze wdrożonymi w ramach sieci wirtualnej platformy Azure.

* Podsieć komunikacji równorzędnej dla ścieżki 1 (/30)
* Podsieć komunikacji równorzędnej dla ścieżki 2 (/30)
* Identyfikator sieci VLAN dla komunikacji równorzędnej
* ASN dla komunikacji równorzędnej
* ExpressRoute ASN = 12076
* Hash MD5 (opcjonalnie)

#### <a name="for-microsoft-peering"></a>W przypadku komunikacji równorzędnej firmy Microsoft

Włącz tę opcję, aby uzyskać dostęp do programu Microsoft Usługi online, takiego jak Microsoft 365. Ponadto wszystkie usługi Azure PaaS są dostępne za pomocą komunikacji równorzędnej firmy Microsoft. Musisz się upewnić, że używasz oddzielnego serwera proxy/krawędzi do łączenia się z firmą Microsoft niż ten, który jest używany przez Internet. Korzystanie z tej samej krawędzi zarówno dla ExpressRoute, jak i Internetu spowoduje, że Routing asymetryczny i spowoduje awarię łączności dla sieci.

* Podsieć komunikacji równorzędnej dla ścieżki 1 (/30) — musi być publicznym adresem IP
* Podsieć komunikacji równorzędnej dla ścieżki 2 (/30) — musi być publicznym adresem IP
* Identyfikator sieci VLAN dla komunikacji równorzędnej
* ASN dla komunikacji równorzędnej
* Anonsowane prefiksy — muszą być publicznymi prefiksami adresów IP
* Numer ASN klienta (opcjonalnie, jeśli różni się od numeru ASN komunikacji równorzędnej)
* RIR/IRR dla weryfikacji adresów IP i ASN
* ExpressRoute ASN = 12076
* Hash MD5 (opcjonalnie)

### <a name="6-start-using-the-expressroute-circuit"></a>6. Rozpocznij korzystanie z obwodu usługi ExpressRoute

* Możesz połączyć sieci wirtualne platformy Azure z obwodem usługi ExpressRoute, aby umożliwić połączenie z sieci wirtualnej platformy Azure. Aby uzyskać wskazówki, zapoznaj się z artykułem [łączenie sieci wirtualnej z obwodem](expressroute-howto-linkvnet-arm.md) . Te sieci wirtualnych mogą znajdować się w tej samej subskrypcji platformy Azure co obwód usługi ExpressRoute lub mogą znajdować się w innej subskrypcji.
* Połącz się z usługami platformy Azure i usługami w chmurze firmy Microsoft za pomocą komunikacji równorzędnej firmy Microsoft.

##  <a name="expressroute-partner-circuit-provisioning-states"></a><a name="expressroute-circuit-provisioning-states"></a>Stany aprowizacji obwodu partnerskiego ExpressRoute

W poniższej sekcji opisano różne stany obwodów usługi ExpressRoute dla modelu łączności partnerskiej ExpressRoute.
Każdy obwód partnerski ExpressRoute ma dwa stany:

* **ServiceProviderProvisioningState** reprezentuje stan po stronie dostawcy łączności. Może to być *NotProvisioned*, *Inicjowanie obsługi administracyjnej*lub *Inicjowanie obsługi administracyjnej*. Obwód ExpressRoute musi być w stanie aprowizacji, aby można było skonfigurować komunikację równorzędną. **Ten stan dotyczy tylko obwodów partnerskich ExpressRoute i nie jest wyświetlany we właściwościach obwodu bezpośredniego ExpressRoute**.

* **Stan** przedstawia stan aprowizacji firmy Microsoft. Ta właściwość jest ustawiona na wartość Enabled podczas tworzenia obwodu ExpressRoute

### <a name="possible-states-of-an-expressroute-circuit"></a>Możliwe stany obwodu ExpressRoute

W tej sekcji przedstawiono możliwe stany obwodu usługi ExpressRoute utworzonego w ramach modelu łączności partnerskiej ExpressRoute.

**W czasie tworzenia**

Obwód ExpressRoute będzie zgłaszał następujące stany podczas tworzenia zasobu.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

**Gdy dostawca łączności jest w trakcie aprowizacji obwodu**

Obwód usługi ExpressRoute będzie zgłaszał następujące stany, gdy dostawca łączności pracuje nad udostępnieniem obwodu.

```output
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

**Gdy dostawca łączności ukończy proces aprowizacji**

Obwód usługi ExpressRoute będzie zgłaszał następujące stany, gdy dostawca połączenia pomyślnie udostępnił obwód.

```output
ServiceProviderProvisioningState : Provisioned
Status                           : Enabled
```

**Gdy dostawca połączeń anulowauje obsługę administracyjną obwodu**

Jeśli obwód usługi ExpressRoute musi zostać anulowany, obwód będzie zgłaszał następujące stany, gdy dostawca usług ukończy proces anulowania aprowizacji.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

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