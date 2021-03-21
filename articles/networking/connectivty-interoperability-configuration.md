---
title: 'Współdziałanie z funkcjami łączności zaplecza platformy Azure: szczegóły konfiguracji | Microsoft Docs'
description: W tym artykule opisano szczegółowo konfigurację testu, którego można użyć do analizowania współdziałania między ExpressRoute, sieci VPN typu lokacja-lokacja i komunikacji równorzędnej sieci wirtualnych na platformie Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 50fd5641c61d08939eca1f2cbafb1d077254e37d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98233274"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Współdziałanie z funkcjami łączności zaplecza platformy Azure: szczegóły konfiguracji testu

W tym artykule opisano szczegóły konfiguracji [testu][Setup]. Konfiguracja testu pomaga analizować sposób współdziałania usług sieciowych platformy Azure na poziomie płaszczyzny kontroli i poziomie płaszczyzny danych.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Łączność z siecią wirtualną przy użyciu komunikacji równorzędnej sieci wirtualnej

Na poniższej ilustracji przedstawiono szczegóły dotyczące komunikacji równorzędnej platformy Azure Virtual Network sieci wirtualnej szprychy (VNet). Aby dowiedzieć się, jak skonfigurować komunikację równorzędną między dwoma sieci wirtualnych, zobacz [Zarządzanie wirtualnymi sieciami równorzędnymi][VNet-Config]. Jeśli sieć wirtualna szprych ma używać bram, które są połączone z siecią wirtualną koncentratora, wybierz opcję **Użyj bram zdalnych**.

[![jedno]][1]

Na poniższej ilustracji przedstawiono szczegółowe informacje o komunikacji równorzędnej sieci wirtualnej centrum. Jeśli chcesz, aby Sieć wirtualna z koncentratorem mogła korzystać z bram centrum, wybierz opcję **Zezwalaj na tranzyt bramy**.

[![dwóch]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>Łączność między sieciami wirtualnymi przy użyciu połączenia VPN typu lokacja-lokacja

Skonfiguruj połączenie sieci VPN typu lokacja-lokacja między węzłem i sieci wirtualnych rozgałęzienia przy użyciu bram sieci VPN w usłudze Azure VPN Gateway. Domyślnie bramy sieci VPN i bramy usługi Azure ExpressRoute używają wartości prywatnego numeru systemu autonomicznego (ASN) **65515**. Wartość ASN można zmienić w VPN Gateway. W konfiguracji testowej wartość ASN bramy sieci VPN gałęzi VNet jest zmieniana na **65516** , aby umożliwić obsługę routingu eBGP między węzłem a gałęzią sieci wirtualnych.


[![r.3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>Lokalna Lokalizacja 1 łączność przy użyciu ExpressRoute i sieci VPN typu lokacja-lokacja

### <a name="expressroute-1-configuration-details"></a>Szczegóły konfiguracji ExpressRoute 1

Na poniższej ilustracji przedstawiono konfigurację obwodu usługi Azure region 1 ExpressRoute w kierunku lokalizacji lokalnej 1 routera brzegowej klienta (CE):

[![4]][4]

Na poniższej ilustracji przedstawiono konfigurację połączenia między obwodem ExpressRoute 1 i siecią wirtualną koncentratora:

[![5000]][5]

Poniższa lista zawiera podstawową konfigurację routera CE na potrzeby łączności prywatnej komunikacji równorzędnej ExpressRoute. (Routery Cisco ASR1000 są używane jako routery CE w konfiguracji testów). Gdy obwody sieci VPN typu lokacja-lokacja i obwodów ExpressRoute są skonfigurowane równolegle w celu połączenia sieci lokalnej z platformą Azure, platforma Azure domyślnie ustala priorytet obwodu usługi ExpressRoute. Aby uniknąć asymetrycznego routingu, Sieć lokalna powinna również określać priorytet łączności ExpressRoute przez połączenie sieci VPN typu lokacja-lokacja. Następująca konfiguracja ustala priorytetyzację przy użyciu atrybutu **Local-preferencja** BGP:

```config
interface TenGigabitEthernet0/0/0.300
 description Customer 30 private peering to Azure
 encapsulation dot1Q 30 second-dot1q 300
 ip vrf forwarding 30
 ip address 192.168.30.17 255.255.255.252
!
interface TenGigabitEthernet1/0/0.30
 description Customer 30 to south bound LAN switch
 encapsulation dot1Q 30
 ip vrf forwarding 30
 ip address 192.168.30.0 255.255.255.254
 ip ospf network point-to-point
!
router ospf 30 vrf 30
 router-id 10.2.30.253
 redistribute bgp 65021 subnets route-map BGP2OSPF
 network 192.168.30.0 0.0.0.1 area 0.0.0.0
default-information originate always
 default-metric 10
!
router bgp 65021
 !
 address-family ipv4 vrf 30
  network 10.2.30.0 mask 255.255.255.128
  neighbor 192.168.30.18 remote-as 12076
  neighbor 192.168.30.18 activate
  neighbor 192.168.30.18 next-hop-self
  neighbor 192.168.30.18 soft-reconfiguration inbound
  neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
  neighbor 192.168.30.18 prefix-list Cust30_to_Private out
 exit-address-family
!
route-map prefer-ER-over-VPN permit 10
 set local-preference 200
!
ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
!
```

### <a name="site-to-site-vpn-configuration-details"></a>Szczegóły konfiguracji sieci VPN typu lokacja-lokacja

Na poniższej liście przedstawiono konfigurację podstawowego routera CE dla połączeń sieci VPN typu lokacja-lokacja:

```config
crypto ikev2 proposal Cust30-azure-proposal
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy Cust30-azure-policy
 match address local 66.198.12.106
 proposal Cust30-azure-proposal
!
crypto ikev2 keyring Cust30-azure-keyring
 peer azure
  address 52.168.162.84
  pre-shared-key local IamSecure123
  pre-shared-key remote IamSecure123
!
crypto ikev2 profile Cust30-azure-profile
 match identity remote address 52.168.162.84 255.255.255.255
 identity local address 66.198.12.106
 authentication local pre-share
 authentication remote pre-share
 keyring local Cust30-azure-keyring
!
crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile Cust30-azure-ipsec-profile
 set transform-set Cust30-azure-ipsec-proposal-set
 set ikev2-profile Cust30-azure-profile
!
interface Loopback30
 ip address 66.198.12.106 255.255.255.255
!
interface Tunnel30
 ip vrf forwarding 30
 ip address 10.2.30.125 255.255.255.255
 tunnel source Loopback30
 tunnel mode ipsec ipv4
 tunnel destination 52.168.162.84
 tunnel protection ipsec profile Cust30-azure-ipsec-profile
!
router bgp 65021
 !
 address-family ipv4 vrf 30
  network 10.2.30.0 mask 255.255.255.128
  neighbor 10.10.30.254 remote-as 65515
  neighbor 10.10.30.254 ebgp-multihop 5
  neighbor 10.10.30.254 update-source Tunnel30
  neighbor 10.10.30.254 activate
  neighbor 10.10.30.254 soft-reconfiguration inbound
 exit-address-family
!
ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30
```

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>Lokalna lokalizacja 2 łączność przy użyciu ExpressRoute

Drugi obwód ExpressRoute w bliżej lokalizacji lokalnej 2 łączy lokalizację lokalną 2 z centralną siecią wirtualną. Na poniższej ilustracji przedstawiono drugą konfigurację ExpressRoute:

[![ust]][6]

Na poniższej ilustracji przedstawiono konfigurację połączenia między drugim obwodem usługi ExpressRoute i siecią wirtualną piasty:

[![7]][7]

ExpressRoute 1 nawiązuje połączenie z siecią wirtualną sieci wirtualnej i lokalizacji lokalnej 1 ze zdalną siecią wirtualną w innym regionie świadczenia usługi Azure:

[![8]][8]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute i połączenie sieci VPN typu lokacja-lokacja wspólnie

###  <a name="site-to-site-vpn-over-expressroute"></a>Sieć VPN typu lokacja-lokacja za pośrednictwem ExpressRoute

Sieć VPN typu lokacja-lokacja można skonfigurować za pomocą komunikacji równorzędnej firmy Microsoft, aby prywatnie wymieniać dane między siecią lokalną i usługą Azure sieci wirtualnych. Za pomocą tej konfiguracji można wymieniać dane z poufnością, autentycznością i integralnością. Wymiana danych to również ochrona przed odtwarzaniem. Aby uzyskać więcej informacji o sposobie konfigurowania sieci VPN typu lokacja-lokacja w trybie tunelowania przy użyciu ExpressRoute komunikacji równorzędnej firmy Microsoft, zobacz [sieci VPN typu lokacja-lokacja za pośrednictwem usługi ExpressRoute Microsoft peering][S2S-Over-ExR]. 

Podstawowe ograniczenie konfiguracji sieci VPN typu lokacja-lokacja, która używa komunikacji równorzędnej firmy Microsoft, to przepływność. Przepływność przez tunel IPsec jest ograniczona przez pojemność bramy sieci VPN. Przepływność bramy sieci VPN jest mniejsza niż ExpressRoute przepływność. W tym scenariuszu użycie tunelu IPsec w przypadku wysoce bezpiecznego ruchu i użycie prywatnej komunikacji równorzędnej dla całego ruchu pomaga zoptymalizować wykorzystanie przepustowości ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Sieć VPN typu lokacja-lokacja jako bezpieczna ścieżka trybu failover dla usługi ExpressRoute

ExpressRoute służy jako para nadmiarowego obwodu w celu zapewnienia wysokiej dostępności. W różnych regionach świadczenia usługi Azure można skonfigurować ExpressRoute Geograficznie nadmiarowy. Ponadto, jak pokazano w naszej konfiguracji testowej w regionie świadczenia usługi Azure, można użyć sieci VPN typu lokacja-lokacja, aby utworzyć ścieżkę trybu failover dla łączności ExpressRoute. Gdy te same prefiksy są anonsowane zarówno w ExpressRoute, jak i w sieci VPN typu lokacja-lokacja, platforma Azure ma priorytet ExpressRoute. Aby uniknąć asymetrycznego routingu między ExpressRoute i siecią VPN typu lokacja-lokacja, konfiguracja sieci lokalnej powinna również natłokować przy użyciu połączenia ExpressRoute przed użyciem połączenia sieci VPN typu lokacja-lokacja.

Aby uzyskać więcej informacji o konfigurowaniu współistniejących połączeń dla ExpressRoute i sieci VPN typu lokacja-lokacja, zobacz [współistnienie ExpressRoute i lokacja-][ExR-S2S-CoEx]lokacja.

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Zwiększanie łączności zaplecza z sieci wirtualnych i lokalizacjami gałęzi

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Łączność z siecią wirtualną przy użyciu komunikacji równorzędnej sieci wirtualnej

Architektura sieci wirtualnej typu Hub i szprych jest szeroko używana. Centrum to sieć wirtualna na platformie Azure, która działa jako centralny punkt łączności między satelitami sieci wirtualnych i sieci lokalnej. Szprychy są sieci wirtualnych tego elementu równorzędnego z koncentratorem i którego można użyć do izolowania obciążeń. Przepływy ruchu między lokalnym centrum danych a centrum za pośrednictwem połączenia ExpressRoute lub sieci VPN. Aby uzyskać więcej informacji o architekturze, zobacz [implementowanie topologii sieci Hub i gwiazdy na platformie Azure][Hub-n-Spoke].

W przypadku komunikacji równorzędnej sieci wirtualnych w obrębie regionu szprycha sieci wirtualnych może używać bram sieci wirtualnej Hub (zarówno bramy VPN, jak i ExpressRoute) do komunikowania się z sieciami zdalnymi.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Łączność między sieciami wirtualnymi przy użyciu połączenia VPN typu lokacja-lokacja

Możesz potrzebować oddziału sieci wirtualnych, które znajdują się w różnych regionach, i sieci lokalnych, aby komunikować się ze sobą za pośrednictwem sieci wirtualnej centrum. Natywne rozwiązanie platformy Azure dla tej konfiguracji to połączenie sieci VPN typu lokacja-lokacja przy użyciu sieci VPN. Alternatywą jest użycie wirtualnego urządzenia sieciowego (urządzenie WUS) do routingu w centrum.

Aby uzyskać więcej informacji, zobacz [co to jest VPN Gateway?][VPN] i [Wdróż urządzenie WUS o wysokiej][Deploy-NVA]dostępności.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [analizy płaszczyzny kontroli][Control-Analysis] konfiguracji testów oraz widoków różnych sieci wirtualnych lub sieci VLAN w topologii.

Dowiedz się więcej na temat [analizy płaszczyzny danych][Data-Analysis] w widokach testów i funkcji monitorowania sieci platformy Azure.

Zobacz [często zadawane pytania dotyczące ExpressRoute][ExR-FAQ] :
-   Dowiedz się, ile obwodów usługi ExpressRoute można połączyć z bramą ExpressRoute.
-   Dowiedz się, ile bram ExpressRoute można połączyć z obwodem ExpressRoute.
-   Dowiedz się więcej na temat innych limitów skalowania ExpressRoute.


<!--Image References-->
[]: ./media/backend-interoperability/SpokeVNet_peering.png "Komunikacja równorzędna sieci" wirtualnej 1 szprych
[2]: ./media/backend-interoperability/HubVNet-peering.png "Komunikacja równorzędna sieci wirtualnej koncentratora"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "VPN Gateway konfiguracja sieci wirtualnej gałęzi"
Konfiguracja [4]: ./media/backend-interoperability/ExR1.png "ExpressRoute 1"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "Konfiguracja połączenia ExpressRoute 1 z bramą ExR sieci wirtualnej koncentratora"
Konfiguracja [6]: ./media/backend-interoperability/ExR2.png "ExpressRoute 2"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "Konfiguracja połączenia z ExpressRoute 2 do bramy ExR sieci wirtualnej koncentratora"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "Konfiguracja połączenia z ExpressRoute 2 do zdalnej bramy ExR sieci wirtualnej"

<!--Link References-->
[Setup]: ./connectivty-interoperability-preface.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Control-Analysis]: ./connectivty-interoperability-control-plane.md
[Data-Analysis]: ./connectivty-interoperability-data-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: ../virtual-network/virtual-network-manage-peering.md