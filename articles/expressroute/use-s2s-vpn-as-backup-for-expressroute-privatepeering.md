---
title: Używanie sieci VPN S2S jako kopii zapasowej prywatnej komunikacji równorzędnej usługi Azure ExpressRoute | Dokumenty firmy Microsoft
description: Ta strona zawiera zalecenia architektoniczne dotyczące tworzenia kopii zapasowych prywatnej komunikacji równorzędnej usługi Azure ExpressRoute za pomocą sieci VPN S2S.
services: networking
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 02/05/2020
ms.author: rambala
ms.openlocfilehash: a6a22b667bc66d6ee69bfbd7ad1db88f72d8df0e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687824"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>Używanie sieci VPN S2S jako kopii zapasowej dla prywatnej komunikacji równorzędnej usługi ExpressRoute

W artykule zatytułowanym [Projektowanie do odzyskiwania po awarii za pomocą prywatnej komunikacji równorzędnej usługi ExpressRoute][DR-PP]omówiliśmy potrzebę rozwiązania łączności kopii zapasowej dla prywatnej łączności równorzędnej usługi ExpressRoute oraz sposobu korzystania z geobbicznych obwodów usługi ExpressRoute w tym celu. W tym artykule zastanówmy się, jak wykorzystać i utrzymać sieć VPN typu lokacja-lokacja (S2S) jako powrót do prywatnej komunikacji równorzędnej usługi ExpressRoute. 

W przeciwieństwie do geobbicznych obwodów usługi ExpressRoute, można używać kombinacji odzyskiwania po awarii usługi ExpressRoute-VPN tylko w trybie aktywnym i pasywnym. Głównym wyzwaniem związanym z używaniem dowolnej kopii zapasowej łączności sieciowej w trybie pasywnym jest to, że połączenie pasywne często nie powiedzie się wraz z połączeniem podstawowym. Częstą przyczyną awarii połączenia pasywnego jest brak aktywnej konserwacji. W związku z tym w tym artykule skupmy się na tym, jak zweryfikować i aktywnie utrzymywać łączność sieci VPN S2S, która jest tworzenie kopii zapasowej prywatnej komunikacji równorzędnej usługi ExpressRoute.

>[!NOTE] 
>Gdy dana trasa jest anonsowana za pośrednictwem usługi ExpressRoute i SIECI VPN, platforma Azure wolałaby routingu za pośrednictwem usługi ExpressRoute.  
>

W tym artykule zobaczmy, jak zweryfikować łączność zarówno z punktu widzenia platformy Azure, jak i perspektywy krawędzi sieci po stronie klienta. Możliwość sprawdzania poprawności z obu stron pomoże niezależnie od tego, czy zarządzasz urządzeniami sieciowymi po stronie klienta, które równorzędne z jednostkami sieci firmy Microsoft. 

## <a name="example-topology"></a>Przykładowa topologia

W naszej konfiguracji mamy sieć lokalną połączoną z siecią wirtualną usługi Azure hub za pośrednictwem obwodu usługi ExpressRoute i połączenia sieci VPN S2S. Sieć wirtualna usługi Azure hub jest z kolei równorzędna z siecią wirtualną szprychy, jak pokazano na poniższym diagramie:

![1][1]

W konfiguracji obwód usługi ExpressRoute jest kończony na parach routerów "Customer Edge" (CE) w środowisku lokalnym. Lokalna sieć LAN jest podłączona do routerów CE za pośrednictwem pary zapór, które działają w trybie leader-follower. Sieć VPN S2S jest bezpośrednio zakończona na zaporach.

W poniższej tabeli wymieniono kluczowe prefiksy adresów IP topologii:

| **Jednostka** | **Prefiks** |
| --- | --- |
| Lokalna sieć LAN | 10.1.11.0/25 |
| Usługa Wirtualna usługi Azure Hub | 10.17.11.0/25 |
| W sieci VNet z szprychami na platformie Azure | 10.17.11.128/26 |
| Lokalny serwer testowy | 10.1.11.10 |
| Maszyna wirtualna szprychowa | 10.17.11.132 |
| Podsieć połączenia podstawowego usługi ExpressRoute p2p | 192.168.11.16/30 |
| Podsieć połączenia pomocniczego usługi ExpressRoute p2p | 192.168.11.20/30 |
| Podstawowy adres IP elementu równorzędnego BGP bramy sieci VPN | 10.17.11.76 |
| Pomocniczy adres IP elementu równorzędnego BGP bramy sieci VPN | 10.17.11.77 |
| Lokalny adres IP sieci VPN protokołu SIECI VPN | 192.168.11.88 |
| Podstawowy router CE i/f w kierunku zapory IP | 192.168.11.0/31 |
| Zapora i/f w kierunku podstawowego adresu IP routera CE | 192.168.11.1/31 |
| Pomocniczy router CE i/f w kierunku zapory IP | 192.168.11.2/31 |
| Zapora sieciowa i/f w kierunku pomocniczego adresu IP routera CE | 192.168.11.3/31 |


W poniższej tabeli wymieniono numery ASN topologii:

| **System autonomiczny** | **Asn** |
| --- | --- |
| Lokalnie | 65020 |
| Microsoft Enterprise Edge | 12076 |
| Sieć wirtualna GW (ExR) | 65515 |
| Sieć wirtualna GW (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>Wysoka dostępność bez asymetryczności

### <a name="configuring-for-high-availability"></a>Konfigurowanie wysokiej dostępności

[Konfiguruj połączenia współistniejące w układzie ExpressRoute i Lokacja w][Conf-CoExist] tym artykule omówiono sposób konfigurowania współistniejącego obwodu usługi ExpressRoute i połączeń sieci VPN S2S. Jak omówiliśmy w [Projektowanie dla wysokiej dostępności z usługi ExpressRoute][HA], aby poprawić wysoką dostępność usługi ExpressRoute, nasza konfiguracja utrzymuje nadmiarowość sieci (pozwala uniknąć pojedynczego punktu awarii) aż do punktów końcowych. Również połączenia podstawowe i pomocnicze obwodów usługi ExpressRoute są skonfigurowane do pracy w trybie aktywnym i aktywnym, reklamując prefiksy lokalne w ten sam sposób za pośrednictwem obu połączeń. 

Poniżej znajduje się lokalna reklama trasy podstawowego routera CE za pośrednictwem połączenia podstawowego obwodu usługi ExpressRoute (polecenia Junos):

    user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

Poniżej znajduje się lokalna reklama trasy pomocniczego routera CE za pośrednictwem połączenia pomocniczego obwodu usługi ExpressRoute (polecenia Junos):

    user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

Aby zwiększyć wysoką dostępność połączenia kopii zapasowej, sieć VPN S2S jest również skonfigurowana w trybie aktywnym i aktywnym. Konfiguracja bramy sieci VPN platformy Azure jest przedstawiona poniżej. Uwaga W ramach sieci VPN konfiguracji sieci VPN znajdują się również adresy IP elementów równorzędnych BGP bramy — 10.17.11.76 i 10.17.11.77 — podano także.

![2][2]

Trasa lokalna jest anonsowana przez zapory do podstawowych i pomocniczych elementów równorzędnych BGP bramy sieci VPN. Poniżej przedstawiono reklamy trasy (Junos):

    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

    {primary:node0}
    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

>[!NOTE] 
>Konfigurowanie sieci VPN S2S w trybie aktywnym i aktywnym zapewnia nie tylko wysoką dostępność połączenia sieciowego kopii zapasowej odzyskiwania po awarii, ale także zapewnia wyższą przepustowość łączności kopii zapasowej. Innymi słowy, konfigurowanie sieci VPN S2S w trybie aktywnym i aktywnym jest zalecane, ponieważ wymusza tworzenie wielu tuneli źródłowych.
>

### <a name="configuring-for-symmetric-traffic-flow"></a>Konfigurowanie symetrycznego przepływu ruchu

Firma Microsoft zauważyła, że gdy dana trasa lokalna jest anonsowana za pośrednictwem usługi ExpressRoute i S2S VPN, platforma Azure wolałaby ścieżkę usługi ExpressRoute. Aby wymusić, aby platforma Azure preferowała ścieżkę sieci VPN S2S nad współistniejącym programem ExpressRoute, musisz anonsować bardziej szczegółowe trasy (dłuższy prefiks z większą maską podsieci) za pośrednictwem połączenia sieci VPN. Naszym celem jest użycie połączeń VPN tylko jako z powrotem. Tak więc domyślne zachowanie wyboru ścieżki platformy Azure jest zgodne z naszym celem. 

Naszym obowiązkiem jest upewnienie się, że ruch przeznaczony do platformy Azure z lokalnego preferuje również ścieżkę usługi ExpressRoute za pośrednictwem sieci VPN S2S. Domyślna preferencja lokalna routerów i zapór CE w naszej konfiguracji lokalnej wynosi 100. Tak więc, konfigurując lokalne preferencje tras odebranych za pośrednictwem prywatnych komunikacji równorzędnej usługi ExpressRoute większej niż 100 (powiedzmy 150), możemy sprawić, że ruch przeznaczony na platformę Azure preferuje obwód Usługi ExpressRoute w stanie stacjonarnym.

Poniżej przedstawiono konfigurację protokołu BGP podstawowego routera CE, który kończy połączenie podstawowe obwodu usługi ExpressRoute. Należy zauważyć, że wartość preferencji lokalnych tras anonsowanych w ramach sesji iBGP jest skonfigurowana na 150. Podobnie musimy zapewnić lokalne preferencje pomocniczego routera CE, który kończy połączenie pomocnicze obwodu usługi ExpressRoute, jest również skonfigurowane na 150.

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

Tabela routingu zapory lokalnej potwierdza (pokazano poniżej), że dla ruchu lokalnego, który jest przeznaczony do platformy Azure preferowana ścieżka jest za pomocą usługi ExpressRoute w stanie stacjonarnym.

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

W powyższej tabeli tras trasy trasy sieci wirtualnej i szprychy --10.17.11.0/25 i 10.17.11.128/26 - widzimy, że obwód Usługi ExpressRoute jest preferowany w stosunku do połączeń VPN. 192.168.11.0 i 192.168.11.2 są adresami IP na interfejsie zapory w stosunku do routerów CE.

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>Sprawdzanie poprawności wymiany trasy przez S2S VPN

Wcześniej w tym artykule zweryfikowaliśmy lokalną reklamę trasy zapór do podstawowych i pomocniczych elementów równorzędnych BGP bramy sieci VPN. Ponadto upewnijmy się, że trasy platformy Azure odebrane przez zapory z podstawowych i pomocniczych elementów równorzędnych BGP bramy sieci VPN.

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

Podobnie sprawdźmy prefiksy trasy sieci lokalnej odebrane przez bramę sieci VPN platformy Azure. 

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

Jak widać powyżej, brama sieci VPN ma trasy odebrane zarówno przez podstawowe, jak i pomocnicze elementy równorzędne BGP bramy sieci VPN. Ma również wgląd w trasy odbierane za pośrednictwem podstawowych i pomocniczych połączeń usługi ExpressRoute (te z as-path poprzedza 12076). Aby potwierdzić trasy odebrane za pośrednictwem połączeń SIECI VPN, musimy znać lokalny adres IP równorzędnych BGP połączeń. W naszej konfiguracji pod uwagę, jest to 192.168.11.88 i widzimy trasy otrzymane od niego.

Następnie sprawdźmy trasy anonsowane przez bramę sieci VPN platformy Azure do lokalnego elementu równorzędnego BGP zapory lokalnej (192.168.11.88).

    PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

    Network         NextHop     AsPath Weight
    -------         -------     ------ ------
    10.17.11.0/25   10.17.11.76 65515       0
    10.17.11.128/26 10.17.11.76 65515       0
    10.17.11.0/25   10.17.11.77 65515       0
    10.17.11.128/26 10.17.11.77 65515       0


Nieu wyświetlenie wymiany tras wskazuje błąd połączenia. Zobacz [Rozwiązywanie problemów: Połączenie sieci VPN między lokacjami platformy Azure nie może się połączyć i przestaje działać,][VPN Troubleshoot] aby uzyskać pomoc dotyczącą rozwiązywania problemów z połączeniem sieci VPN.

## <a name="testing-failover"></a>Testowanie pracy awaryjnej

Teraz, gdy potwierdziliśmy pomyślne wymiany tras za pośrednictwem połączenia VPN (płaszczyzny sterowania), jesteśmy ustawione, aby przełączyć ruch (płaszczyznę danych) z łączności ExpressRoute do łączności VPN. 

>[!NOTE] 
>W środowiskach produkcyjnych testowanie pracy awaryjnej musi być wykonane podczas zaplanowanej konserwacji sieci pracy okna, ponieważ może to być uciążliwe dla usług.
>

Przed zrobieniem przełącznika ruchu, let's śledzenia trasy bieżącej ścieżki w naszej konfiguracji z lokalnego serwera testowego do testowej maszyny Wirtualnej w sieci wirtualnej szprychy.

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2    <1 ms    <1 ms    11 ms  192.168.11.0
      3    <1 ms    <1 ms    <1 ms  192.168.11.18
      4     *        *        *     Request timed out.
      5     6 ms     6 ms     5 ms  10.17.11.132

    Trace complete.

Podstawowe i pomocnicze podsieci połączeń typu express-to-point naszej konfiguracji to odpowiednio 192.168.11.16/30 i 192.168.11.20/30. W powyższej trasie śledzenia, w kroku 3 widzimy, że uderzamy 192.168.11.18, który jest interfejs IP podstawowego MSEE. Obecność interfejsu MSEE potwierdza, że zgodnie z oczekiwaniami nasza bieżąca ścieżka znajduje się za platformą ExpressRoute.

Zgodnie z raportem w [resetowania układu równorzędnego obwodu usługi ExpressRoute][RST], użyjmy następujących poleceń programu PowerShell, aby wyłączyć zarówno podstawowe, jak i pomocnicze komunikacji równorzędnej obwodu usługi ExpressRoute.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Disabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

Czas przełączania trybu failover zależy od czasu zbieżności BGP. W naszej konfiguracji przełącznik pracy awaryjnej trwa kilka sekund (mniej niż 10). Po przełączeniu powtórzenie traceroute pokazuje następującą ścieżkę:

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2     *        *        *     Request timed out.
      3     6 ms     7 ms     9 ms  10.17.11.132

    Trace complete.

Wynik traceroute potwierdza, że połączenie kopii zapasowej za pośrednictwem sieci VPN S2S jest aktywne i może zapewnić ciągłość usług, jeśli zarówno podstawowe, jak i pomocnicze połączenia usługi ExpressRoute nie powiodą się. Aby zakończyć testowanie pracy awaryjnej, włączmy połączenia usługi ExpressRoute z powrotem i znormalizuj przepływ ruchu, używając następującego zestawu poleceń.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Enabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

Aby potwierdzić, że ruch jest przełączany z powrotem do usługi ExpressRoute, powtórz traceroute i upewnij się, że przechodzi przez prywatną komunikację równorzędnie usługi ExpressRoute.

## <a name="next-steps"></a>Następne kroki

Usługa ExpressRoute jest przeznaczona do wysokiej dostępności bez pojedynczego punktu awarii w sieci Firmy Microsoft. Nadal obwód usługi ExpressRoute jest ograniczony do jednego regionu geograficznego i do dostawcy usług. S2S VPN może być dobrym rozwiązaniem do pasywnego odzyskiwania po awarii w obwodzie usługi ExpressRoute. W przypadku niezawodnego rozwiązania do pasywnego połączenia kopii zapasowej ważne jest regularne utrzymywanie konfiguracji pasywnej i okresowa weryfikacja połączenia. Istotne jest, aby nie pozwolić, aby konfiguracja sieci VPN się nie zestarzała, a okresowo (powiedzmy co kwartał) powtarzać kroki sprawdzania poprawności i pracy awaryjnej opisane w tym artykule podczas obsługi technicznej.

Aby włączyć monitorowanie i alerty oparte na metrykach bramy sieci VPN, zobacz [Konfigurowanie alertów w metrykach bramy sieci VPN][VPN-alerts].

Aby przyspieszyć konwergencję protokołu BGP po niepowodzeniu usługi ExpressRoute, [skonfiguruj program BFD za dodatkową treścią ExpressRoute][BFD].

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "topologia pod uwagę"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "Konfiguracja VPN GW"

<!--Link References-->
[DR-PP]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering
[Conf-CoExist]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[VPN Troubleshoot]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect
[VPN-alerts]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[RST]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering



