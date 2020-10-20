---
title: Korzystanie z sieci VPN S2S jako kopii zapasowej prywatnej komunikacji równorzędnej Azure ExpressRoute | Microsoft Docs
description: Na tej stronie przedstawiono zalecenia dotyczące architektury tworzenia kopii zapasowych prywatnej komunikacji równorzędnej Azure ExpressRoute przy użyciu sieci VPN S2S.
services: networking
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/05/2020
ms.author: duau
ms.openlocfilehash: 752edea8078cf55fc3965bdc7aa9e1b4269dee34
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207924"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>Używanie sieci VPN S2S jako kopii zapasowej dla prywatnej komunikacji równorzędnej ExpressRoute

W artykule zatytułowanym [projektowanie pod kątem odzyskiwania po awarii za pomocą prywatnej komunikacji równorzędnej ExpressRoute][DR-PP]omówiono potrzebę rozwiązania połączenia z kopią zapasową dla łączności prywatnej komunikacji równorzędnej ExpressRoute oraz sposób używania obwodów z geograficznie nadmiarowym ExpressRoute. W tym artykule poinformuj nas, jak korzystać z sieci VPN typu lokacja-lokacja (S2S) i obsługiwać ją jako kopię zapasową prywatnej komunikacji równorzędnej ExpressRoute. 

W odróżnieniu od geograficznie nadmiarowych obwodów usługi ExpressRoute, można użyć kombinacji odzyskiwania po awarii w sieci VPN ExpressRoute — tylko w trybie aktywny-pasywny. Głównym wyzwaniem korzystania z dowolnej kopii zapasowej łączności sieciowej w trybie pasywnym jest to, że połączenie pasywne często kończy się niepowodzeniem wraz z połączeniem podstawowym. Typowy powód niepowodzeń połączenia pasywnego to brak aktywnej konserwacji. Dlatego w tym artykule koncentrujemy się na sprawdzaniu i aktywnie utrzymaniu łączności sieci VPN S2S, która wykonuje kopię zapasową prywatnej komunikacji równorzędnej usługi ExpressRoute.

>[!NOTE] 
>Gdy dana trasa jest anonsowana za pośrednictwem zarówno ExpressRoute, jak i sieci VPN, platforma Azure preferuje Routing na ExpressRoute.  
>

W tym artykule dowiesz się, jak zweryfikować połączenie zarówno z perspektywy platformy Azure, jak i z perspektywy krawędzi sieci po stronie klienta. Możliwość weryfikacji z dowolnego końca będzie pomocna niezależnie od tego, czy zarządzasz urządzeniami sieciowymi po stronie klienta, które są równorzędne z jednostkami sieci firmy Microsoft. 

## <a name="example-topology"></a>Przykładowa topologia

W naszej konfiguracji mamy sieć lokalną podłączoną do sieci wirtualnej usługi Azure Hub za pośrednictwem obwodu ExpressRoute i połączenia sieci VPN S2S. Sieć wirtualna usługi Azure Hub jest z kolei komunikacji równorzędnej z siecią wirtualną szprych, jak pokazano na poniższym diagramie:

![1][1]

W trakcie instalacji obwód ExpressRoute zostaje przerwany na parze routerów "brzegów klienta" (CE) w środowisku lokalnym. Lokalna sieć LAN jest połączona z routerami CE za pośrednictwem pary zapór, które działają w trybie podążania za liderem. Sieć VPN S2S jest bezpośrednio zakończona na zaporach.

W poniższej tabeli przedstawiono podstawowe prefiksy adresów IP topologii:

| **Jednostka** | **Prefiks** |
| --- | --- |
| Lokalna sieć LAN | 10.1.11.0/25 |
| Sieć wirtualna usługi Azure Hub | 10.17.11.0/25 |
| Sieć wirtualna Azure szprych | 10.17.11.128/26 |
| Lokalny serwer testowy | 10.1.11.10 |
| Testowa maszyna wirtualna dla sieci VNet | 10.17.11.132 |
| ExpressRoute połączenie podstawowe z podsiecią P2P | 192.168.11.16/30 |
| ExpressRoute połączenie pomocnicze — podsieć P2P | 192.168.11.20/30 |
| Podstawowy adres IP elementu równorzędnego BGP bramy sieci VPN | 10.17.11.76 |
| Dodatkowy adres IP elementu równorzędnego BGP bramy sieci VPN | 10.17.11.77 |
| Adres IP elementu równorzędnego BGP sieci VPN zapory lokalnej | 192.168.11.88 |
| Podstawowy router CE i/f w kierunku protokołu IP zapory | 192.168.11.0/31 |
| Zapora i/f do podstawowego protokołu IP routera CE | 192.168.11.1/31 |
| Router pomocniczy we/f na adres IP zapory | 192.168.11.2/31 |
| Zapora i/f na dodatkowy adres IP routera CE | 192.168.11.3/31 |


W poniższej tabeli wymieniono WPW dla topologii:

| **System autonomiczny** | **Właściwość** |
| --- | --- |
| Środowiska lokalne | 65020 |
| Microsoft Enterprise Edge | 12076 |
| Virtual Network GW (ExR) | 65515 |
| Virtual Network GW (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>Wysoka dostępność bez asymetryczny

### <a name="configuring-for-high-availability"></a>Konfigurowanie wysokiej dostępności

[Konfigurowanie współistniejących połączeń ExpressRoute i typu lokacja-lokacja][Conf-CoExist] omawia sposób konfigurowania współistniejącego obwodu ExpressRoute i połączeń sieci VPN S2S. Zgodnie z opisem w temacie [projektowanie pod kątem wysokiej dostępności przy użyciu usługi ExpressRoute][HA], aby zwiększyć ExpressRoute wysoką dostępność, nasza konfiguracja utrzymuje nadmiarowość sieci (pozwala uniknąć Single Point of Failure) we wszystkich punktach końcowych. Zarówno podstawowe, jak i pomocnicze połączenia obwodów usługi ExpressRoute są skonfigurowane do działania w trybie aktywny-aktywny przez reklamowanie lokalnych prefiksów w taki sam sposób, jak w przypadku obu połączeń. 

Anons trasy lokalnej podstawowego routera CE przy użyciu połączenia podstawowego obwodu usługi ExpressRoute jest wyświetlany poniżej (Junos polecenia):

```console
user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

Anons trasy lokalnej pomocniczego routera CE przez połączenie pomocnicze obwodu usługi ExpressRoute jest wyświetlany poniżej (Junos polecenia):

```console
user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

Aby zwiększyć wysoką dostępność połączenia kopii zapasowej, Sieć VPN S2S jest również konfigurowana w trybie aktywny-aktywny. Poniżej przedstawiono konfigurację bramy sieci VPN platformy Azure. Należy pamiętać, że jako część sieci VPN konfiguracja sieci VPN adresy IP elementu równorzędnego BGP bramy--10.17.11.76 i 10.17.11.77--są również wymienione na liście.

![2][2]

Trasa lokalna jest anonsowana przez zapory dla podstawowych i pomocniczych elementów równorzędnych protokołu BGP bramy sieci VPN. Poniżej przedstawiono anonse tras (Junos):

```console
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I

{primary:node0}
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

>[!NOTE] 
>Konfigurowanie sieci VPN S2S w trybie aktywny-aktywny nie tylko zapewnia wysoką dostępność dla łączności sieciowej kopii zapasowej odzyskiwania po awarii, ale również zapewnia wyższą przepływność dla łączności kopii zapasowej. Innymi słowy zaleca się skonfigurowanie sieci VPN S2S w trybie aktywny-aktywny, ponieważ wymusza to utworzenie wielu podstawowych tuneli.
>

### <a name="configuring-for-symmetric-traffic-flow"></a>Konfigurowanie dla przepływu ruchu symetrycznego

Zauważono, że gdy dana trasa lokalna jest anonsowana za pośrednictwem sieci VPN ExpressRoute i S2S, platforma Azure preferuje ścieżkę ExpressRoute. Aby wymusić, że platforma Azure preferuje ścieżkę sieci VPN S2S przez ExpressRoute, musisz anonsować bardziej szczegółowe trasy (dłuższy prefiks o większej masce podsieci) za pośrednictwem połączenia sieci VPN. Naszym celem jest użycie połączeń sieci VPN jako kopii zapasowej. W związku z tym domyślne zachowanie wyboru ścieżki na platformie Azure jest zgodne z naszym celem. 

Jest to nasza odpowiedzialność za zapewnienie, że ruch kierowany do platformy Azure z firmy lokalnej również preferuje ścieżkę ExpressRoute za pośrednictwem sieci VPN S2S. Domyślną preferencją lokalną routery i zapory CE w ramach instalacji lokalnej jest 100. Dlatego przez skonfigurowanie preferencji lokalnych dla tras odbieranych za pomocą prywatnych komunikacji równorzędnej ExpressRoute większej niż 100 (Powiedz 150), możemy wprowadzić ruch kierowany do platformy Azure preferuje obwód ExpressRoute w stanie stałym.

Poniżej przedstawiono konfigurację protokołu BGP podstawowego routera CE, który kończy połączenie podstawowe obwodu usługi ExpressRoute. Należy pamiętać, że wartość lokalnego preferencji tras anonsowanych w sesji iBGP jest skonfigurowana jako 150. Podobnie musimy upewnić się, że lokalna preferencja dodatkowego routera CE, która przerywa połączenie pomocnicze obwodu usługi ExpressRoute, jest również skonfigurowana jako 150.

```console
user@SEA-MX03-01> show configuration routing-instances Cust11
description "Customer 11 VRF";
instance-type virtual-router;
interface xe-0/0/0:0.110;
interface ae0.11;
protocols {
  bgp {
    group ibgp {
        type internal;
        local-preference 150;
        neighbor 192.168.11.1;
    }
    group ebgp {
        peer-as 12076;
        bfd-liveness-detection {
            minimum-interval 300;
            multiplier 3;
        }
        neighbor 192.168.11.18;
    }
  }
}
```

Tabela routingu lokalnych zapór potwierdza (pokazane poniżej), która dla ruchu lokalnego, który jest przeznaczony dla platformy Azure, preferowaną ścieżką jest ponad ExpressRoute w stanie stałym.

```console
user@SEA-SRX42-01> show route table Cust11.inet.0 10.17.11.0/24

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

10.17.11.0/25      *[BGP/170] 2d 00:34:04, localpref 150
                      AS path: 12076 I, validation-state: unverified
                    > to 192.168.11.0 via reth1.11
                      to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 00:34:01, localpref 150
                      AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                       AS path: 65515 I, validation-state: unverified
                    > via st0.118
                    [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                       AS path: 65515 I, validation-state: unverified
                     > via st0.119
10.17.11.76/32     *[Static/5] 2d 21:12:16
                     > via st0.118
10.17.11.77/32     *[Static/5] 2d 00:41:56
                    > via st0.119
10.17.11.128/26    *[BGP/170] 2d 00:34:04, localpref 150
                       AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.0 via reth1.11
                       to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 00:34:01, localpref 150
                      AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                       AS path: 65515 I, validation-state: unverified
                    > via st0.118
                     [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                       AS path: 65515 I, validation-state: unverified
                     > via st0.119
```

W powyższej tabeli tras dla tras sieci wirtualnej Hub i szprych--10.17.11.0/25 i 10.17.11.128/26--widzimy obwód ExpressRoute jest preferowany przez połączenia VPN. 192.168.11.0 i 192.168.11.2 są adresami IP w interfejsie zapory skierowanymi do routerów CE.

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>Sprawdzanie poprawności trasy wymiany za pośrednictwem sieci VPN S2S

Wcześniej w tym artykule zostały zweryfikowane lokalne anonse dotyczące zapór w ramach podstawowych i pomocniczych elementów równorzędnych protokołu BGP bramy sieci VPN. Dodatkowo Potwierdźmy trasy platformy Azure odbierane przez zapory z podstawowego i pomocniczego elementu równorzędnego protokołu BGP bramy sieci VPN.

```console
user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.76 table Cust11.inet.0 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
  10.17.11.0/25           10.17.11.76                             65515 I
  10.17.11.128/26         10.17.11.76                             65515 I

{primary:node0}
user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.77 table Cust11.inet.0    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
  10.17.11.0/25           10.17.11.77                             65515 I
  10.17.11.128/26         10.17.11.77                             65515 I
```

W podobny sposób sprawdzimy prefiksy tras sieci lokalnych odebrane przez bramę sieci VPN platformy Azure. 

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn | where {$_.Network -eq "10.1.11.0/25"} | select Network, NextHop, AsPath, Weight

Network      NextHop       AsPath      Weight
-------      -------       ------      ------
10.1.11.0/25 192.168.11.88 65020        32768
10.1.11.0/25 10.17.11.76   65020        32768
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 192.168.11.88 65020        32768
10.1.11.0/25 10.17.11.77   65020        32768
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 10.17.11.69   12076-65020  32769
```

Jak pokazano powyżej, Brama sieci VPN ma trasy odbierane zarówno przez podstawowy, jak i pomocniczy element równorzędny protokołu BGP bramy sieci VPN. Ma także wgląd w trasy odbierane za pośrednictwem podstawowych i pomocniczych połączeń ExpressRoute (te, które zostały poprzedzone ścieżką do 12076). Aby potwierdzić trasy otrzymane za pośrednictwem połączeń sieci VPN, musimy znać lokalny adres IP elementu równorzędnego protokołu BGP dla połączeń. W naszym rozważaniu konfiguracja jest 192.168.11.88 i będziemy widzieć otrzymane trasy.

Następnie Sprawdźmy trasy anonsowane przez bramę sieci VPN platformy Azure do lokalnego elementu równorzędnego protokołu BGP zapory (192.168.11.88).

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

Network         NextHop     AsPath Weight
-------         -------     ------ ------
10.17.11.0/25   10.17.11.76 65515       0
10.17.11.128/26 10.17.11.76 65515       0
10.17.11.0/25   10.17.11.77 65515       0
10.17.11.128/26 10.17.11.77 65515       0
```

Niepowodzenie wyświetlania wymiany tras wskazuje na błąd połączenia. Zobacz [Rozwiązywanie problemów: połączenie sieci VPN typu lokacja-lokacja platformy Azure nie może nawiązać połączenia i przestaje działać][VPN Troubleshoot] w celu uzyskania pomocy w rozwiązywaniu problemów z połączeniem sieci VPN

## <a name="testing-failover"></a>Testowanie pracy w trybie failover

Po potwierdzeniu pomyślnej wymiany trasy przez połączenie sieci VPN (płaszczyzna kontroli) ustawimy przełączenie ruchu (płaszczyzny danych) z łączności ExpressRoute na połączenie sieci VPN. 

>[!NOTE] 
>Testowanie trybu failover w środowisku produkcyjnym należy wykonać podczas zaplanowanej konserwacji sieci w oknie, ponieważ może to powodować zakłócenia działania usługi.
>

Przed przełączeniem ruchu śledźmy bieżącą ścieżkę w naszej konfiguracji z lokalnego serwera testowego do testowej maszyny wirtualnej w sieci wirtualnej szprychy.

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2    <1 ms    <1 ms    11 ms  192.168.11.0
  3    <1 ms    <1 ms    <1 ms  192.168.11.18
  4     *        *        *     Request timed out.
  5     6 ms     6 ms     5 ms  10.17.11.132

Trace complete.
```

Podstawowa i pomocnicza podsieć połączeń punkt-punkt ExpressRoute Instalatora jest odpowiednio 192.168.11.16/30 i 192.168.11.20/30. W powyższej trasie śledzenia w kroku 3 zobaczymy, że 192.168.11.18, który jest interfejsem IP głównego MSEE. Obecność interfejsu MSEE potwierdza, że zgodnie z oczekiwaniami nasza bieżąca ścieżka znajduje się na ExpressRoute.

Jak zostało zgłoszone w [komunikacji równorzędnej obwodu usługi ExpressRoute][RST], użyj następujących poleceń programu PowerShell, aby wyłączyć podstawową i dodatkową komunikację równorzędną obwodu usługi ExpressRoute.

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Disabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Czas przełączania trybu failover zależy od czasu zbieżności protokołu BGP. W naszej instalacji przełączanie trybu failover trwa kilka sekund (mniej niż 10). Po przełączniku Powtórz traceroute pokazuje następującą ścieżkę:

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2     *        *        *     Request timed out.
  3     6 ms     7 ms     9 ms  10.17.11.132

Trace complete.
```

Wynik traceroute potwierdza, że połączenie kopii zapasowej za pośrednictwem sieci VPN S2S jest aktywne i może zapewnić ciągłość usługi, jeśli zarówno podstawowe, jak i pomocnicze połączenia ExpressRoute kończą się niepowodzeniem. Aby ukończyć Testowanie pracy w trybie failover, Włącz ExpressRoute połączenia z powrotem i normalizowanie przepływu ruchu przy użyciu następującego zestawu poleceń.

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Enabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Aby potwierdzić, że ruch jest przełączany z powrotem do ExpressRoute, powtórz traceroute i upewnij się, że przechodzą przez prywatną komunikację równorzędną ExpressRoute.

## <a name="next-steps"></a>Następne kroki

ExpressRoute jest przeznaczona do wysokiej dostępności bez single point of failure w sieci firmy Microsoft. Nadal obwód usługi ExpressRoute jest ograniczony do jednego regionu geograficznego i dostawcy usług. Sieci VPN S2S mogą być dobrym rozwiązaniem do pasywnego odzyskiwania po awarii do obwodu ExpressRoute. W przypadku niezawodnego rozwiązania do obsługi połączeń pasywnych, regularnej konserwacji konfiguracji pasywnej i okresowego sprawdzania poprawności połączenia są ważne. Nie jest możliwe, aby konfiguracja sieci VPN stała się nieaktualna, i okresowo (co kwartał należy powiedzieć) Powtórz kroki testu walidacji i przełączenia w tryb failover opisany w tym artykule w oknie obsługi.

Aby włączyć monitorowanie i alerty na podstawie metryki bramy sieci VPN, zobacz [Konfigurowanie alertów na VPN Gateway metrykach][VPN-alerts].

Aby przyspieszyć zbieżność protokołu BGP po wystąpieniu błędu ExpressRoute, [Skonfiguruj BFD na ExpressRoute][BFD].

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "topologia w rozważaniu"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "Konfiguracja sieci VPN GW"

<!--Link References-->
[DR-PP]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md
[Conf-CoExist]: ./expressroute-howto-coexist-resource-manager.md
[HA]: ./designing-for-high-availability-with-expressroute.md
[VPN Troubleshoot]: ../vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect.md
[VPN-alerts]: ../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md
[BFD]: ./expressroute-bfd.md
[RST]: ./expressroute-howto-reset-peering.md