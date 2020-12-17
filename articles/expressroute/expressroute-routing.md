---
title: 'Azure ExpressRoute: wymagania dotyczące routingu'
description: Ta strona zawiera szczegółowe wymagania dotyczące konfigurowania routingu oraz zarządzania nim na potrzeby obwodów usługi ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: duau
ms.openlocfilehash: 436e866969d620389818bcebca3c5c37b8805309
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629038"
---
# <a name="expressroute-routing-requirements"></a>Wymagania dotyczące routingu w usłudze ExpressRoute
Aby połączyć się z usługami w chmurze firmy Microsoft przy użyciu usługi ExpressRoute, konieczne będzie skonfigurowanie routingu oraz zarządzanie nim. Niektórzy dostawcy połączenia oferują konfigurowanie routingu oraz zarządzanie nim jako usługą zarządzaną. Skontaktuj się z dostawcą połączenia, aby sprawdzić, czy taka usługa jest oferowana. Jeśli nie, musisz spełnić wymagania opisane poniżej:

Opis sesji routingu, które należy skonfigurować w celu ułatwienia łączności, znajduje się w artykule [Circuits and routing domains](expressroute-circuit-peerings.md) (Obwody i domeny routingu).

> [!NOTE]
> Firma Microsoft nie obsługuje protokołów nadmiarowości routerów (np. HSRP, VRRP) w konfiguracjach wysokiej dostępności. Polegamy na nadmiarowej parze sesji protokołu BGP na komunikację równorzędną w celu zapewnienia wysokiej dostępności.
> 
> 

## <a name="ip-addresses-used-for-peerings"></a>Adresy IP używane do komunikacji równorzędnej
Należy zarezerwować kilka bloków adresów IP, aby skonfigurować routing między siecią i routerami MSEE. Ta sekcja zawiera listę wymagań i opis zasad dotyczących sposobu pozyskiwania i użycia tych adresów IP.

### <a name="ip-addresses-used-for-azure-private-peering"></a>Adresy IP używane do prywatnej komunikacji równorzędnej Azure
Do konfigurowania komunikacji równorzędnej można użyć prywatnych lub publicznych adresów IP. Zakres adresów używany do konfigurowania tras nie może pokrywać się z zakresami adresów używanymi do tworzenia sieci wirtualnych na platformie Azure. 

* Należy zarezerwować podsieć /29 lub dwie podsieci /30 dla interfejsów routingu.
* Podsieci używane do routingu mogą być prywatnymi adresami IP lub publicznymi adresami IP.
* Podsieci nie mogą powodować konfliktu z zakresem zarezerwowanym przez klienta do użycia w chmurze firmy Microsoft.
* Jeśli zostanie użyta podsieć /29, zostanie ona podzielona na dwie podsieci /30. 
  * Pierwsza podsieć /30 zostanie użyta na potrzeby linku podstawowego, a druga — dodatkowego.
  * Dla każdej podsieci /30 należy użyć pierwszego adresu IP podsieci /30 na routerze. Firma Microsoft używa drugiego adresu IP podsieci /30 do skonfigurowania sesji protokołu BGP.
  * Aby zapewnić ważność [umowy SLA dotyczącej dostępności](https://azure.microsoft.com/support/legal/sla/), musisz skonfigurować obie sesje protokołu BGP.  

#### <a name="example-for-private-peering"></a>Przykład prywatnej komunikacji równorzędnej
Jeśli do skonfigurowania komunikacji równorzędnej zdecydujesz się użyć podsieci a.b.c.d/29, zostanie ona podzielona na dwie podsieci /30. W poniższym przykładzie Zwróć uwagę na to, jak jest używana podsieć a. b. c. d/29:

* Podsieć a.b.c.d/29 zostanie podzielona na podsieci a.b.c.d/30 i a.b.c.d+4/30 i przekazana firmie Microsoft za pośrednictwem aprowizowanych interfejsów API.
  * Użytkownik użyje podsieci a.b.c.d+1 jako adresu IP VRF usługi Primary PE, a firma Microsoft użyje podsieci a.b.c.d+2 jako adresu IP VRF podstawowego rozwiązania MSEE.
  * Użytkownik użyje podsieci a.b.c.d+5 jako adresu IP VRF dodatkowej usługi PE, a firma Microsoft użyje podsieci a.b.c.d+6 jako adresu IP VRF dodatkowego rozwiązania MSEE.

Rozważ sytuację, w której w celu skonfigurowania prywatnej komunikacji równorzędnej wybierzesz podsieć 192.168.100.128/29. 192.168.100.128/29 obejmuje adresy od 192.168.100.128 do 192.168.100.135, z których:

* Adres 192.168.100.128/30 zostanie przypisany do linku link1, podczas gdy dostawca będzie używać 192.168.100.129, a firma Microsoft będzie używać 192.168.100.130.
* Adres 192.168.100.132/30 zostanie przypisany do linku link2, podczas gdy dostawca będzie używać adresu 192.168.100.133, a firma Microsoft będzie używać adresu 192.168.100.134.

### <a name="ip-addresses-used-for-microsoft-peering"></a>Adresy IP używane do komunikacji równorzędnej firmy Microsoft
Do konfigurowania sesji protokołu BGP należy użyć posiadanych publicznych adresów IP. Firma Microsoft musi mieć możliwość weryfikacji własności adresów IP za pomocą rejestrów internetowego routingu i internetowych rejestrów routingu.

* Za pomocą adresów IP wymienionych w portalu dla anonsowanych prefiksów publicznych na potrzeby komunikacji równorzędnej firmy Microsoft zostaną utworzone listy ACL, na podstawie których routery główne firmy Microsoft będą zezwalać na ruch przychodzący z tych adresów IP. 
* Należy użyć unikatowej podsieci /29 (IPv4) lub /125 (IPv6) albo dwóch podsieci /30 (IPv4) lub /126 (IPv6) do skonfigurowania komunikacji równorzędnej protokołu BGP dla każdego wystąpienia komunikacji równorzędnej na obwód usługi ExpressRoute (jeśli używasz więcej niż jednego obwodu).
* Jeśli zostanie użyta podsieć /29, zostanie ona podzielona na dwie podsieci /30.
* Pierwsza podsieć /30 zostanie użyta na potrzeby linku podstawowego, a druga — dodatkowego.
* Dla każdej podsieci /30 należy użyć pierwszego adresu IP podsieci /30 na routerze. Firma Microsoft używa drugiego adresu IP podsieci /30 do skonfigurowania sesji protokołu BGP.
* Jeśli zostanie użyta podsieć /125, zostanie ona podzielona na dwie podsieci /126.
* Pierwsza podsieć /126 zostanie użyta na potrzeby linku podstawowego, a druga — dodatkowego.
* Dla każdej podsieci /126 należy użyć pierwszego adresu IP podsieci /126 na routerze. Firma Microsoft używa drugiego adresu IP podsieci /126 do skonfigurowania sesji protokołu BGP.
* Aby zapewnić ważność [umowy SLA dotyczącej dostępności](https://azure.microsoft.com/support/legal/sla/), musisz skonfigurować obie sesje protokołu BGP.

### <a name="ip-addresses-used-for-azure-public-peering"></a>Adresy IP używane do publicznej komunikacji równorzędnej na platformie Azure

> [!NOTE]
> Publiczna Komunikacja równorzędna Azure nie jest dostępna dla nowych obwodów.
> 

Do konfigurowania sesji protokołu BGP należy użyć posiadanych publicznych adresów IP. Firma Microsoft musi mieć możliwość weryfikacji własności adresów IP za pomocą rejestrów internetowego routingu i internetowych rejestrów routingu. 

* Należy użyć unikatowej podsieci /29 lub dwóch podsieci /30 do skonfigurowania komunikacji równorzędnej protokołu BGP dla każdego wystąpienia komunikacji równorzędnej na obwód usługi ExpressRoute (jeśli używasz więcej niż jednego obwodu). 
* Jeśli zostanie użyta podsieć /29, zostanie ona podzielona na dwie podsieci /30. 
  * Pierwsza podsieć /30 zostanie użyta na potrzeby linku podstawowego, a druga — dodatkowego.
  * Dla każdej podsieci /30 należy użyć pierwszego adresu IP podsieci /30 na routerze. Firma Microsoft używa drugiego adresu IP podsieci /30 do skonfigurowania sesji protokołu BGP.
  * Aby zapewnić ważność [umowy SLA dotyczącej dostępności](https://azure.microsoft.com/support/legal/sla/), musisz skonfigurować obie sesje protokołu BGP.

## <a name="public-ip-address-requirement"></a>Wymagania dotyczące publicznego adresu IP

### <a name="private-peering"></a>Prywatna komunikacja równorzędna
Do prywatnej komunikacji równorzędnej można używać publicznych lub prywatnych adresów IPv4. Zapewniamy kompleksową izolację ruchu, w związku z czym w warunkach prywatnej komunikacji równorzędnej nie ma możliwości, aby adresy się nakładały. Te adresy nie są anonsowane w Internecie. 

### <a name="microsoft-peering"></a>Komunikacja równorzędna firmy Microsoft
Ścieżka komunikacji równorzędnej firmy Microsoft umożliwia nawiązanie połączenia z usługami w chmurze firmy Microsoft. Lista usług zawiera Microsoft 365 usług, takich jak Exchange Online, SharePoint Online, Skype dla firm i Microsoft Teams. Firma Microsoft zapewnia obsługę dwukierunkowej łączności w oparciu o komunikację równorzędną firmy Microsoft. Ruch skierowany do usług w chmurze firmy Microsoft musi uzyskać prawidłowe publiczne adresy IPv4, zanim wejdzie do sieci firmy Microsoft.

Zadbaj o to, aby adres IP i numer AS zostały zarejestrowane na Ciebie w jednym z następujących rejestrów:

* [ARIN](https://www.arin.net/)
* [APNIC](https://www.apnic.net/)
* [AFRINIC](https://www.afrinic.net/)
* [LACNIC](https://www.lacnic.net/)
* [RIPENCC](https://www.ripe.net/)
* [RADB](https://www.radb.net/)
* [ALTDB](https://altdb.net/)

Jeśli Twoje prefiksy i numer systemu autonomicznego nie są przypisane do Ciebie w powyższych rejestrach, musisz otworzyć zgłoszenie do pomocy technicznej w celu ręcznej weryfikacji Twoich prefiksów i numeru systemu autonomicznego. Pomoc techniczna będzie wymagać dokumentacji, takiej jak upoważnienie potwierdzające zezwolenie na korzystanie z zasobów.

Prywatny numer systemu autonomicznego jest dozwolony dla komunikacji równorzędnej firmy Microsoft, ale będzie również wymagał ręcznej weryfikacji. Ponadto firma Microsoft usuwa prywatne numery AS w ścieżce AS dla odebranych prefiksów. W związku z tym nie można dołączyć prywatnych numerów AS do ścieżki AS, aby [wpłynąć na routing komunikacji równorzędnej firmy Microsoft](expressroute-optimize-routing.md). 

> [!IMPORTANT]
> Nie należy anonsować tej samej publicznej trasy IP do publicznego Internetu i za pośrednictwem ExpressRoute. Aby zmniejszyć ryzyko niepoprawnej konfiguracji powodującej Routing asymetryczny, zdecydowanie zalecamy, aby [adresy IP translatora adresów sieciowych](expressroute-nat.md) anonsowane do firmy Microsoft przez ExpressRoute były z zakresu, który nie jest anonsowany w Internecie. Jeśli nie jest to możliwe, należy się upewnić, że anonsuje bardziej konkretny zakres od ExpressRoute niż ten, na którym nadano połączenie internetowe. Poza trasą publiczną dla translatora adresów sieciowych można także anonsować za pośrednictwem ExpressRoute publicznych adresów IP używanych przez serwery w sieci lokalnej, które komunikują się z punktami końcowymi Microsoft 365 w firmie Microsoft. 
> 
> 

### <a name="public-peering-deprecated---not-available-for-new-circuits"></a>Publiczna Komunikacja równorzędna (przestarzałe — niedostępne w przypadku nowych obwodów)
Ścieżka publicznej komunikacji równorzędnej Azure umożliwia łączenie ze wszystkimi usługami obsługiwanymi na platformie Azure za pośrednictwem ich publicznych adresów IP. Dotyczy to usług wymienionych w temacie [ExpessRoute FAQ](expressroute-faqs.md) (ExpessRoute — często zadawane pytania) i wszystkich usług obsługiwanych przez niezależnych dostawców oprogramowania na platformie Microsoft Azure. Połączenie z usługami Microsoft Azure w publicznej komunikacji równorzędnej jest zawsze inicjowane z sieci użytkownika do sieci Microsoft. W odniesieniu do ruchu skierowanego do sieci firmy Microsoft należy użyć publicznych adresów IP.

> [!IMPORTANT]
> Wszystkie usługi Azure PaaS są dostępne za pomocą komunikacji równorzędnej firmy Microsoft.
>   

Prywatny numer AS jest dozwolony w publicznej komunikacji równorzędnej.

## <a name="dynamic-route-exchange"></a>Wymiana tras dynamicznych
Wymiana routingu będzie odbywać się za pośrednictwem protokołu eBGP. Sesje eBGP są ustanawiane między rozwiązaniami MSEE a routerami użytkownika. Uwierzytelnianie sesji BGP nie jest wymagane. W razie potrzeby można skonfigurować skrót MD5. Informacje na temat konfigurowania sesji BGP znajdują się w artykułach [Configure routing](how-to-routefilter-portal.md) (Konfigurowanie routingu) i [Circuit provisioning workflows and circuit states](expressroute-workflows.md) (Przepływy pracy inicjowania obsługi obwodu i stany obwodu).

## <a name="autonomous-system-numbers"></a>Numery systemu autonomicznego
Firma Microsoft używa numeru AS 12076 do publicznej i prywatnej komunikacji równorzędnej Azure oraz komunikacji równorzędnej Microsoft. Zarezerwowaliśmy numery AS od 65515 do 65520 do użytku wewnętrznego. Obsługiwane są zarówno 16-, jak i 32-bitowe numery AS.

Nie ma żadnych wymagań związanych z symetrią transferu danych. Ścieżki przekazywania dalej i ścieżki zwracania mogą przechodzić różne pary routerów. Trasy identyczne muszą być anonsowane z obu stron w wielu parach obwodów należących do użytkownika. Metryki tras nie muszą być identyczne.

## <a name="route-aggregation-and-prefix-limits"></a>Agregacja tras i limity prefiksów
Firma Microsoft obsługuje do 4000 prefiksów anonsowanych nam za pośrednictwem prywatnej komunikacji równorzędnej Azure. Tę liczbę można zwiększyć do 10 000 prefiksów, jeśli zostanie włączony dodatek Premium usługi ExpressRoute. Akceptujemy do 200 prefiksów na sesję protokołu BGP dla publicznej komunikacji równorzędnej Azure i komunikacji równorzędnej Microsoft. 

Sesja protokołu BGP zostanie przerwana, jeśli liczba prefiksów przekroczy limit. Będziemy akceptować domyślne trasy tylko dla linku prywatnej komunikacji równorzędnej. Dostawca musi odfiltrować trasę domyślną i prywatne adresy IP (RFC 1918) ze ścieżek publicznej komunikacji równorzędnej Azure i komunikacji równorzędnej Microsoft. 

## <a name="transit-routing-and-cross-region-routing"></a>Routing tranzytowy i routing obejmujący wiele regionów
Usługi ExpressRoute nie można skonfigurować jako routera tranzytowego. W kwestii usług routingu tranzytowego trzeba polegać na dostawcy połączenia.

## <a name="advertising-default-routes"></a>Anonsowanie tras domyślnych
Trasy domyślne są dozwolone tylko w sesjach prywatnej komunikacji równorzędnej Azure. W takim przypadku firma Microsoft będzie kierować cały ruch ze skojarzonych sieci wirtualnych do sieci użytkownika. Anonsowanie tras domyślnych do prywatnej komunikacji równorzędnej spowoduje zablokowanie ścieżki internetowej z platformy Azure. Aby przekierować ruch do i z Internetu w przypadku usług hostowanych na platformie Azure, konieczne jest zastosowanie krawędzi sieci firmowej. 

 Aby można było włączyć łączność z innymi usługami i usługami infrastruktury Azure, musi być spełniony jeden z poniższych warunków:

* Publiczna Komunikacja równorzędna Azure jest włączona, aby kierować ruch do publicznych punktów końcowych.
* Używasz routingu zdefiniowanego przez użytkownika, aby zezwolić na połączenie z Internetem dla każdej podsieci wymagającej takiego połączenia.

> [!NOTE]
> Anonsowanie tras domyślnych spowoduje awarię aktywacji licencji maszyn wirtualnych systemu Windows i innych systemów. Aby obejść ten problem, postępuj zgodnie z instrukcjami zamieszczonymi [tutaj](/archive/blogs/mast/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling).
> 
> 

## <a name="support-for-bgp-communities"></a><a name="bgp"></a>Obsługa społeczności BGP
W tej sekcji przedstawiono sposób korzystania z protokołu BGP społeczności w usłudze ExpressRoute. Firma Microsoft będzie anonsować trasy w ścieżkach publicznej komunikacji równorzędnej oraz komunikacji równorzędnej firmy Microsoft za pomocą tras oznaczonych odpowiednimi wartościami społeczności. Uzasadnienie takiego postępowania oraz szczegółowe informacje dotyczące wartości społeczności zostały opisane poniżej. Firma Microsoft nie uzna jednak żadnych wartości społeczności oznaczonych do tras anonsowanych do firmy Microsoft.

Jeśli łączysz się z firmą Microsoft za pośrednictwem usługi ExpressRoute w dowolnej lokalizacji komunikacji równorzędnej w regionie geopolitycznym, będziesz mieć dostęp do wszystkich usług w chmurze firmy Microsoft we wszystkich regionach w ramach granic geopolitycznych. 

Na przykład po połączeniu się z firmą Microsoft w Amsterdamie za pośrednictwem usługi ExpressRoute będziesz mieć dostęp do wszystkich usług w chmurze Microsoft obsługiwanych w Europie Północnej i Europie Zachodniej. 

Zapoznaj się ze stroną [ExpressRoute partners and peering locations](expressroute-locations.md) (Partnerzy i lokalizacje komunikacji równorzędnej usługi ExpressRoute), która zawiera szczegółową listę regionów geopolitycznych, powiązanych regionów świadczenia usługi Azure i odpowiadających im lokalizacji komunikacji równorzędnej usługi ExpressRoute.

Możesz kupić więcej niż jeden obwód usługi ExpressRoute na region geopolityczny. Wiele ofert połączeń daje znaczące korzyści w zakresie wysokiej dostępności z powodu nadmiarowości geograficznej. W przypadkach, gdy masz wiele obwodów usługi ExpressRoute, otrzymasz ten sam zestaw prefiksów anonsowanych od firmy Microsoft na ścieżce komunikacji równorzędnej firmy Microsoft i publicznych komunikacji równorzędnych. Oznacza to, że będziesz mieć wiele ścieżek ze swojej sieci do firmy Microsoft. Może to teoretycznie spowodować podjęcie nieoptymalnych decyzji w zakresie routingu w ramach sieci. W efekcie połączenia z różnymi usługami mogą nie być optymalne. Wartości społeczności gwarantują, że zostaną podjęte odpowiednie decyzje w kwestii routingu i klienci będą mieli zapewniony [optymalny routing do użytkowników](expressroute-optimize-routing.md).

| **Region platformy Microsoft Azure** | **Regionalna społeczność protokołu BGP** | **Społeczność protokołu BGP magazynu** | **Społeczność protokołu BGP SQL** | **Cosmos DB społeczność protokołu BGP** | **Utwórz kopię zapasową społeczności BGP** |
| --- | --- | --- | --- | --- | --- |
| **Ameryka Północna** | |
| East US | 12076:51004 | 12076:52004 | 12076:53004 | 12076:54004 | 12076:55004 |
| Wschodnie stany USA 2 | 12076:51005 | 12076:52005 | 12076:53005 | 12076:54005 | 12076:55005 |
| Zachodnie stany USA | 12076:51006 | 12076:52006 | 12076:53006 | 12076:54006 | 12076:55006 |
| Zachodnie stany USA 2 | 12076:51026 | 12076:52026 | 12076:53026 | 12076:54026 | 12076:55026 |
| Zachodnio-środkowe stany USA | 12076:51027 | 12076:52027 | 12076:53027 | 12076:54027 | 12076:55027 |
| Północno-środkowe stany USA | 12076:51007 | 12076:52007 | 12076:53007 | 12076:54007 | 12076:55007 |
| South Central US | 12076:51008 | 12076:52008 | 12076:53008 | 12076:54008 | 12076:55008 |
| Central US | 12076:51009 | 12076:52009 | 12076:53009 | 12076:54009 | 12076:55009 |
| Kanada Środkowa | 12076:51020 | 12076:52020 | 12076:53020 | 12076:54020 | 12076:55020 |
| Kanada Wschodnia | 12076:51021 | 12076:52021 | 12076:53021 | 12076:54021 | 12076:55021 |
| **Ameryka Południowa** | |
| Brazil South | 12076:51014 | 12076:52014 | 12076:53014 | 12076:54014 | 12076:55014 |
| **Europa** | |
| Europa Północna | 12076:51003 | 12076:52003 | 12076:53003 | 12076:54003 | 12076:55003 |
| West Europe | 12076:51002 | 12076:52002 | 12076:53002 | 12076:54002 | 12076:55002 |
| Południowe Zjednoczone Królestwo | 12076:51024 | 12076:52024 | 12076:53024 | 12076:54024 | 12076:55024 |
| Zachodnie Zjednoczone Królestwo | 12076:51025 | 12076:52025 | 12076:53025 | 12076:54025 | 12076:55025 |
| Francja Środkowa | 12076:51030 | 12076:52030 | 12076:53030 | 12076:54030 | 12076:55030 |
| Francja Południowa | 12076:51031 | 12076:52031 | 12076:53031 | 12076:54031 | 12076:55031 |
| Szwajcaria Północna | 12076:51038 | 12076:52038 | 12076:53038 | 12076:54038 | 12076:55038 |
| Szwajcaria Zachodnia | 12076:51039 | 12076:52039 | 12076:53039 | 12076:54039 | 12076:55039 | 
| Niemcy Północne | 12076:51040 | 12076:52040 | 12076:53040 | 12076:54040 | 12076:55040 | 
| Niemcy Środkowo-Zachodnie | 12076:51041 | 12076:52041 | 12076:53041 | 12076:54041 | 12076:55041 | 
| Norwegia Wschodnia | 12076:51042 | 12076:52042 | 12076:53042 | 12076:54042 | 12076:55042 | 
| Norwegia Zachodnia | 12076:51043 | 12076:52043 | 12076:53043 | 12076:54043 | 12076:55043 | 
| **Azja i Pacyfik** | |
| Azja Wschodnia | 12076:51010 | 12076:52010 | 12076:53010 | 12076:54010 | 12076:55010 |
| Southeast Asia | 12076:51011 | 12076:52011 | 12076:53011 | 12076:54011 | 12076:55011 |
| **Japonia** | |
| Japan East | 12076:51012 | 12076:52012 | 12076:53012 | 12076:54012 | 12076:55012 |
| Japonia Zachodnia | 12076:51013 | 12076:52013 | 12076:53013 | 12076:54013 | 12076:55013 |
| **Australia** | |
| Australia Wschodnia | 12076:51015 | 12076:52015 | 12076:53015 | 12076:54015 | 12076:55015 |
| Australia Południowo-Wschodnia | 12076:51016 | 12076:52016 | 12076:53016 | 12076:54016 | 12076:55016 |
| **Australia — instytucje rządowe** | |
| Australia Środkowa | 12076:51032 | 12076:52032 | 12076:53032 | 12076:54032 | 12076:55032 |
| Australia Środkowa 2 | 12076:51033 | 12076:52033 | 12076:53033 | 12076:54033 | 12076:55033 |
| **Indie** | |
| Indie Południowe | 12076:51019 | 12076:52019 | 12076:53019 | 12076:54019 | 12076:55019 |
| Indie Zachodnie | 12076:51018 | 12076:52018 | 12076:53018 | 12076:54018 | 12076:55018 |
| Indie Środkowe | 12076:51017 | 12076:52017 | 12076:53017 | 12076:54017 | 12076:55017 |
| **Korea** | |
| Korea Południowa | 12076:51028 | 12076:52028 | 12076:53028 | 12076:54028 | 12076:55028 |
| Korea Środkowa | 12076:51029 | 12076:52029 | 12076:53029 | 12076:54029 | 12076:55029 |
| **Republika Południowej Afryki**| |
| Północna Republika Południowej Afryki | 12076:51034 | 12076:52034 | 12076:53034 | 12076:54034 | 12076:55034 |
| Zachodnia Republika Południowej Afryki | 12076:51035 | 12076:52035 | 12076:53035 | 12076:54035 | 12076:55035 |
| **Zjednoczone Emiraty Arabskie**| |
| Północne Zjednoczone Emiraty Arabskie | 12076:51036 | 12076:52036 | 12076:53036 | 12076:54036 | 12076:55036 |
| Środkowy Zjednoczone Emiraty Arabskie | 12076:51037 | 12076:52037 | 12076:53037 | 12076:54037 | 12076:55037 |


Wszystkie trasy anonsowane przez firmę Microsoft zostaną oznaczone odpowiednią wartością społeczności. 

> [!IMPORTANT]
> Prefiksy globalne są oznaczane odpowiednią wartością społeczności.
> 
> 

### <a name="service-to-bgp-community-value"></a>Wartość wspólnotowa usługi BGP
Oprócz tego firma Microsoft oznaczy również prefiksy w oparciu o usługę, do której się odnoszą. Dotyczy to tylko komunikacji równorzędnej firmy Microsoft. Poniższa tabela zawiera mapowanie usługi do wartości społeczności BGP. Można uruchomić polecenie cmdlet "Get-AzBgpServiceCommunity", aby zapoznać się z pełną listą najnowszych wartości.

| **Usługa** | **Wartość społeczności BGP** |
| --- | --- |
| Usługa Exchange Online\*\* | 12076:5010 |
| SharePoint Online\*\* | 12076:5020 |
| Skype dla firm Online\*\*/\*\*\* | 12076:5030 |
| CRM Online\*\*\*\* |12076:5040 |
| Usługi globalne platformy Azure\* | 12076:5050 |
| Usługa Azure Active Directory |12076:5060 |
| Azure Resource Manager |12076:5070 |
| Inne usługi online pakietu Office 365 * * | 12076:5100 |

\* Usługi globalne platformy Azure obejmują tylko usługę Azure DevOps.

\*\* Autoryzacja wymagana przez firmę Microsoft, zapoznaj się z tematem [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-portal.md)

\*\*\* Ta społeczność również publikuje odpowiednie trasy dla usług Microsoft Team Services.

\*\*\*\* Program CRM Online obsługuje Dynamics v 8.2 i poniżej. W przypadku wyższych wersji wybierz społeczność regionalną dla wdrożeń programu Dynamics.

> [!NOTE]
> Firma Microsoft nie uznaje żadnych wartości społeczności BGP ustawionych na trasach anonsowanych do firmy Microsoft.
> 
> 

### <a name="bgp-community-support-in-national-clouds"></a>Obsługa społeczności BGP w chmurach krajowych

| **Region chmur krajowych platformy Azure**| **Wartość społeczności BGP** |
| --- | --- |
| **Administracja USA** |  |
| US Gov Arizona | 12076:51106 |
| US Gov Iowa | 12076:51109 |
| US Gov Wirginia | 12076:51105 |
| US Gov Teksas | 12076:51108 |
| US DoD (region środkowy) | 12076:51209 |
| US DoD (region wschodni) | 12076:51205 |


| **Usługa w chmurach krajowych** | **Wartość społeczności BGP** |
| --- | --- |
| **Administracja USA** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| Skype dla firm Online |12076:5130 |
| Usługa Azure Active Directory |12076:5160 |
| Inne usługi online Office 365 |12076:5200 |

## <a name="next-steps"></a>Następne kroki
* Skonfiguruj połączenie usługi ExpressRoute.
  
  * [Tworzenie i modyfikowanie obwodu](expressroute-howto-circuit-arm.md)
  * [Tworzenie i modyfikowanie konfiguracji komunikacji równorzędnej](expressroute-howto-routing-arm.md)
  * [Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](expressroute-howto-linkvnet-arm.md)
