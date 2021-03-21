---
title: 'Azure ExpressRoute: projektowanie na potrzeby odzyskiwania po awarii'
description: Ta strona zawiera zalecenia architektury dotyczące odzyskiwania po awarii podczas korzystania z usługi Azure ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: duau
ms.openlocfilehash: 2a5730cd75ccb76d25897e9109555113f7355c2f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92202417"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Projektowanie na potrzeby odzyskiwania po awarii za pomocą prywatnej komunikacji równorzędnej ExpressRoute

ExpressRoute jest przeznaczona do wysokiej dostępności w celu zapewnienia poufności łączności sieci prywatnej z zasobami firmy Microsoft. Innymi słowy, w ścieżce ExpressRoute w sieci firmy Microsoft nie ma single point of failure. Aby poznać zagadnienia dotyczące projektowania w celu zmaksymalizowania dostępności obwodu usługi ExpressRoute, zobacz [projektowanie pod kątem wysokiej dostępności dzięki ExpressRoute][HA].

Jednak dzięki Murphy popularnej powiedzeniem —*Jeśli coś się nie powiedzie, w tym artykule zauważamy*, że firma Microsoft koncentruje się na rozwiązaniach, które wykraczają poza awarie, które można rozwiązać za pomocą pojedynczego obwodu usługi ExpressRoute. Innymi słowy, w tym artykule poinformuj nas o architekturze sieci dotyczącej tworzenia niezawodnej łączności sieciowej zaplecza na potrzeby odzyskiwania po awarii za pomocą obwodów ExpressRoute geograficznie nadmiarowych.

>[!NOTE]
>Koncepcje opisane w tym artykule są równie stosowane, gdy obwód ExpressRoute jest tworzony w ramach wirtualnej sieci WAN lub poza nią.
>

## <a name="need-for-redundant-connectivity-solution"></a>Potrzebna do nadmiarowego rozwiązania łączności

Istnieją również możliwości i wystąpienia, w których cała usługa regionalna (powinna zostać obniżona przez firmę Microsoft, dostawców usług sieciowych, klientów lub innych dostawców usług w chmurze). Główną przyczyną takiego wpływu na obsługę wielu regionów jest naturalny Calamity. W związku z tym, aby uzyskać ciągłość biznesową i najważniejsze aplikacje, ważne jest, aby zaplanować odzyskiwanie po awarii.   

Bez względu na to, czy aplikacje o znaczeniu strategicznym są uruchamiane w regionie świadczenia usługi Azure, czy lokalnie czy w innym miejscu, możesz użyć innego regionu platformy Azure jako witryny trybu failover. Poniższe artykuły dotyczą odzyskiwania po awarii z aplikacji i perspektyw dostępu frontonu:

- [Odzyskiwanie po awarii w skali przedsiębiorstwa][Enterprise DR]
- [Odzyskiwanie po awarii dla małych i średnich firm za pomocą usługi Azure Site Recovery][SMB DR]

Jeśli polegasz na ExpressRoute łączności między siecią lokalną i firmą Microsoft w celu wykonywania krytycznych operacji, plan odzyskiwania po awarii powinien również obejmować geograficznie nadmiarową łączność sieciową. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Wyzwania związane z korzystaniem z wielu obwodów usługi ExpressRoute

W przypadku łączenia tego samego zestawu sieci przy użyciu więcej niż jednego połączenia wprowadza się ścieżki równoległe między sieciami. Ścieżki równoległe, gdy nie są poprawnie zaprojektowane, mogą prowadzić do routingu asymetrycznego. Jeśli masz jednostki stanowe (na przykład translator adresów sieciowych, Zapora) w ścieżce, routing asymetryczny może blokować przepływ ruchu.  Zazwyczaj za pośrednictwem prywatnej ścieżki komunikacji równorzędnej ExpressRoute nie będziesz mieć między jednostkami stanowymi, takimi jak translator adresów sieciowych lub zapory. W związku z tym Routing asymetryczny za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute nie musi blokować przepływu ruchu.
 
Jednak w przypadku równoważenia obciążenia ruchu między geograficznie nadmiarowymi ścieżkami równoległymi, niezależnie od tego, czy są to jednostki stanowe, czy nie, wystąpi niespójna wydajność sieci. W tym artykule omówiono sposoby rozwiązywania tych problemów.

## <a name="small-to-medium-on-premises-network-considerations"></a>Zagadnienia dotyczące małych i średnich sieci lokalnych

Rozważmy przykładową sieć zilustrowaną na poniższym diagramie. W przykładzie geograficznie nadmiarowe połączenie ExpressRoute jest ustanawiane między lokalizacją lokalną firmy Contoso i siecią wirtualną firmy Contoso w regionie świadczenia usługi Azure. Na diagramie pełny zielony wiersz wskazuje preferowaną ścieżkę (za pośrednictwem ExpressRoute 1), a jeden z kropek reprezentuje ścieżkę "ścieżka do" (za pośrednictwem ExpressRoute 2).

[![jedno]][1]

Podczas projektowania łączności ExpressRoute na potrzeby odzyskiwania po awarii należy rozważyć następujące kwestie:

- Używanie obwodów ExpressRoute geograficznie nadmiarowych
- Używanie różnorodnych sieci usługodawców dla różnych obwodów usługi ExpressRoute
- Projektowanie każdego obwodu usługi ExpressRoute w celu zapewnienia [wysokiej dostępności][HA]
- Kończenie innego obwodu ExpressRoute w innej lokalizacji w sieci klienta

Domyślnie, jeśli Anonsy tras są identyczne na wszystkich ścieżkach ExpressRoute, platforma Azure będzie równoważyć obciążenie ruchu lokalnego między wszystkimi ścieżkami ExpressRoute przy użyciu routingu o podwójnej trasie (ECMP).

Jednak w przypadku obwodów ExpressRoute geograficznie nadmiarowych musimy wziąć pod uwagę różne wydajności sieci z różnymi ścieżkami sieciowymi (szczególnie w przypadku opóźnień sieci). Aby zapewnić bardziej spójną wydajność sieci podczas normalnego działania, można preferować obwód ExpressRoute, który oferuje minimalne opóźnienia.

Możesz mieć wpływ na platformę Azure, aby preferować jeden obwód ExpressRoute na inny przy użyciu jednej z następujących technik (wymienionych w kolejności skuteczności):

- Anonsowanie bardziej konkretnej trasy przez preferowany obwód ExpressRoute w porównaniu do innych obwodów ExpressRoute
- Konfigurowanie wyższych wag połączeń w połączeniu, które łączy sieć wirtualną z preferowanym obwodem usługi ExpressRoute
- Anonsowanie tras za pośrednictwem mniej preferowanego obwodu ExpressRoute z dłuższą ścieżką (jako prefiks ścieżki)

### <a name="more-specific-route"></a>Bardziej konkretna trasa

Poniższy diagram ilustruje wpływ na wybór ścieżki ExpressRoute przy użyciu bardziej konkretnego anonsu trasy. W zilustrowanym przykładzie zakres adresów IP firmy Contoso w środowisku lokalnym/24 jest anonsowany jako dwa/25 zakresy adresowe za pośrednictwem preferowanej ścieżki (ExpressRoute 1) i jako/24 za pośrednictwem ścieżki pośredniej (ExpressRoute 2).

[![dwóch]][2]

Ponieważ/25 jest bardziej szczegółowy, w porównaniu z/24, platforma Azure wyśle ruch do 10.1.11.0/24 za pośrednictwem ExpressRoute 1 w normalnym stanie. Jeśli oba połączenia ExpressRoute 1 przechodzą w dół, Sieć wirtualna zobaczy anons trasy 10.1.11.0/24 tylko za pośrednictwem ExpressRoute 2; w związku z tym w tym stanie awarii jest używany obwód gotowości.

### <a name="connection-weight"></a>Waga połączenia

Poniższy zrzut ekranu ilustruje konfigurację wagi połączenia ExpressRoute za pośrednictwem Azure Portal.

[![r.3]][3]

Na poniższym diagramie przedstawiono wpływ wybierania ścieżki ExpressRoute przy użyciu wagi połączeń. Domyślna waga połączenia to 0. W poniższym przykładzie waga połączenia dla ExpressRoute 1 jest skonfigurowana jako 100. Gdy sieć wirtualna odbiera prefiks trasy anonsowany za pośrednictwem więcej niż jednego obwodu usługi ExpressRoute, Sieć wirtualna będzie preferować połączenie o najwyższej wadze.

[![4]][4]

Jeśli oba połączenia ExpressRoute 1 przechodzą w dół, Sieć wirtualna zobaczy anons trasy 10.1.11.0/24 tylko za pośrednictwem ExpressRoute 2; w związku z tym w tym stanie awarii jest używany obwód gotowości.

### <a name="as-path-prepend"></a>Ścieżka do ścieżki

Na poniższym diagramie przedstawiono wpływ na wybór ścieżki ExpressRoute przy użyciu ścieżki do dołączania. Na diagramie anons trasy dla ExpressRoute 1 wskazuje domyślne zachowanie eBGP. W przypadku anonsu trasy dla ExpressRoute 2 numer ASN sieci lokalnej jest dołączany również do trasy jako ścieżki. Gdy ta sama trasa jest odbierana przez wiele obwodów usługi ExpressRoute, za pomocą procesu wyboru trasy eBGP, Sieć wirtualna będzie preferować trasy z najkrótszą ścieżką. 

[![5000]][5]

Jeśli oba połączenia ExpressRoute 1 przechodzą w dół, Sieć wirtualna zobaczy anons trasy 10.1.11.0/24 tylko za pośrednictwem ExpressRoute 2. Z kolei, dłuższa ścieżka staje się nieistotna. W związku z tym w tym stanie niepowodzenia zostanie użyty obwód gotowości.

Korzystając z dowolnych z tych technik, jeśli zamierzasz korzystać z platformy Azure w celu preferowania jednego z ExpressRoute na innych, musisz również upewnić się, że sieć lokalna również preferuje tę samą ścieżkę ExpressRoute dla ruchu związanego z platformą Azure, aby uniknąć przepływów asymetrycznych. Zazwyczaj lokalna wartość preferencji jest używana w celu wpływania sieci lokalnej w celu preferowania jednego obwodu ExpressRoute na inne. Preferencja lokalna to wewnętrzna Metryka BGP (iBGP). Preferowana jest trasa BGP o najwyższej wartości preferencji lokalnej.

> [!IMPORTANT]
> W przypadku korzystania z pewnych obwodów usługi ExpressRoute jako usługi autonomicznej należy aktywnie zarządzać nimi i okresowo testować pracę w trybie failover. 
> 

## <a name="large-distributed-enterprise-network"></a>Duże rozproszone sieci przedsiębiorstwa

W przypadku dużej rozproszonej sieci przedsiębiorstwa istnieje wiele obwodów usługi ExpressRoute. W tej sekcji Zobaczmy, jak projektować odzyskiwanie po awarii za pomocą obwodów usługi ExpressRoute w aktywnym miejscu, bez konieczności używania dodatkowych obwodów. 

Rozważmy przykład przedstawiony na poniższym diagramie. W tym przykładzie firma Contoso ma dwie lokalizacje lokalne połączone z dwoma różnymi regionami platformy Azure za pośrednictwem obwodów usługi ExpressRoute w dwóch różnych lokalizacjach komunikacji równorzędnej. 

[![ust]][6]

Sposób, w jaki oferujemy środowisko odzyskiwania po awarii, ma wpływ na to, jak odbywa się kierowanie ruchu między różnymi lokalizacjami (Region1/region2 do Location2/Location1). Rozważmy dwie różne architektury awaryjne, które przekierowują ruch między lokalizacjami między regionami.

### <a name="scenario-1"></a>Scenariusz 1

W pierwszym scenariuszu należy zaprojektować odzyskiwanie po awarii, aby cały ruch między regionem platformy Azure i przepływem sieci lokalnej przez lokalny obwód usługi ExpressRoute był w stanie stałym. Jeśli lokalny obwód usługi ExpressRoute nie powiedzie się, zostanie użyty zdalny obwód ExpressRoute dla wszystkich przepływów ruchu między platformą Azure i siecią lokalną.

Scenariusz 1 przedstawiono na poniższym diagramie. Na diagramie zielone linie wskazują ścieżki ruchu sieciowego między VNet1 i sieciami lokalnymi. Niebieskie linie wskazują ścieżki ruchu sieciowego między VNet2 i sieciami lokalnymi. Linie kryjące wskazują pożądaną ścieżkę w stanie stałym i linie kreskowane wskazują ścieżkę ruchu w przypadku awarii odpowiedniego obwodu ExpressRoute, który przenosi przepływ ruchu stałego stanu. 

[![7]][7]

Możesz zaprojektować scenariusz przy użyciu wagi połączeń, aby mieć wpływ na sieci wirtualnych w celu nawiązania połączenia z lokalną lokalizacją komunikacji równorzędnej ExpressRoute dla ruchu w sieci lokalnej. Aby ukończyć rozwiązanie, należy zapewnić symetryczny przepływ ruchu odwrotnego. Możesz użyć preferencji lokalnych w sesji iBGP między routerami BGP (na których obwody ExpressRoute kończą się po stronie lokalnej), aby preferować obwód ExpressRoute. Rozwiązanie jest zilustrowane na poniższym diagramie. 

[![8]][8]

### <a name="scenario-2"></a>Scenariusz 2

Scenariusz 2 przedstawiono na poniższym diagramie. Na diagramie zielone linie wskazują ścieżki ruchu sieciowego między VNet1 i sieciami lokalnymi. Niebieskie linie wskazują ścieżki ruchu sieciowego między VNet2 i sieciami lokalnymi. W stanie stałym (pełne linie na diagramie) cały ruch między lokalizacjami sieci wirtualnych i lokalnymi odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, a następnie przepływy przez połączenie między lokalizacjami lokalnymi tylko w stanie niepowodzenia (linie kropkowane na diagramie) ExpressRoute.

[![9]][9]

Rozwiązanie jest zilustrowane na poniższym diagramie. Jak widać, można zaprojektować scenariusz przy użyciu bardziej określonej trasy (opcja 1) lub ścieżki (opcja 2) w celu wpływu na wybór ścieżki sieci wirtualnej. Aby mieć wpływ na wybór trasy sieci lokalnej dla ruchu związanego z platformą Azure, należy skonfigurować połączenie między lokalizacją lokalną i bardziej preferowaną. Howe Konfigurowanie linku połączenia, zgodnie z preferowaną zależnością, zależy od protokołu routingu używanego w sieci lokalnej. Możesz użyć preferencji lokalnego z iBGP lub metryką z IGP (OSPF lub IS-IS).

[![dziesięć]][10]


## <a name="next-steps"></a>Następne kroki

W tym artykule omówiono sposób projektowania na potrzeby odzyskiwania po awarii prywatnej komunikacji równorzędnej obwodu usługi ExpressRoute. Poniższe artykuły dotyczą odzyskiwania po awarii z aplikacji i perspektyw dostępu frontonu:

- [Odzyskiwanie po awarii w skali przedsiębiorstwa][Enterprise DR]
- [Odzyskiwanie po awarii dla małych i średnich firm za pomocą usługi Azure Site Recovery][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "zagadnienia dotyczące małych i średnich wielkości w sieci lokalnej"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "wpływ na wybór ścieżki przy użyciu bardziej konkretnych tras"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "Konfigurowanie wagi połączeń za pośrednictwem Azure Portal"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "wpływ na wybór ścieżki przy użyciu wagi połączeń"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "wpływanie na wybór ścieżki przy użyciu" prefiksu ścieżki
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "duże zagadnienia dotyczące rozproszonej sieci lokalnej"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "scenariusz 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "— rozwiązanie do obwody usługi ExpressRoute" w usłudze Active-Active
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png— "Scenariusz 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "aktywne-aktywne obwody usługi ExpressRoute 2"

<!--Link References-->
[HA]: ./designing-for-high-availability-with-expressroute.md
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: ./expressroute-optimize-routing.md#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: ./expressroute-optimize-routing.md#solution-use-as-path-prepending