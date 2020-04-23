---
title: 'Azure ExpressRoute: przykłady konfiguracji routera'
description: Ta strona zawiera próbki konfiguracji routera dla routerów Cisco i Juniper.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: osamaz
ms.openlocfilehash: 3603bc45b920dc62eb8bf6f2eb8557f98e21638e
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024816"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Przykłady konfiguracji routera do konfigurowania routingu i zarządzania nim
Ta strona zawiera przykłady konfiguracji interfejsu i routingu dla routerów z serii Cisco IOS-XE i Juniper MX podczas pracy z usługą Azure ExpressRoute.

> [!IMPORTANT]
> Przykłady na tej stronie są wyłącznie w celu uzyskania wskazówek. Aby znaleźć odpowiednie konfiguracje, aby spełnić twoje potrzeby, należy współpracować z zespołem sprzedaży/techniki dostawcy i zespołem sieciowym. Firma Microsoft nie będzie obsługiwać problemów związanych z konfiguracjami wymienionymi na tej stronie. Skontaktuj się z dostawcą urządzenia, aby uzyskać problemy z pomocą techniczną.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Ustawienia MMU i TCP w interfejsach routera
Maksymalna jednostka transmisji (MTU) dla interfejsu usługi ExpressRoute wynosi 1500, co jest typową domyślną jednostką MTU dla interfejsu Ethernet na routerze. Jeśli router nie ma domyślnie innej jednostki MTU, nie ma potrzeby określania wartości w interfejsie routera.

W przeciwieństwie do bramy sieci VPN platformy Azure maksymalny rozmiar segmentu TCP (MSS) dla obwodu usługi ExpressRoute nie musi być określony.

Przykłady konfiguracji routera w tym artykule dotyczą wszystkich komunikacji równorzędnej. Przejrzyj wymagania [dotyczące komunikacji równorzędnej usługi ExpressRoute](expressroute-circuit-peerings.md) i [routingu usługi ExpressRoute,](expressroute-routing.md) aby uzyskać więcej informacji na temat routingu.


## <a name="cisco-ios-xe-based-routers"></a>Routery z systemem Cisco IOS-XE
Przykłady w tej sekcji dotyczą dowolnego routera z rodziną systemu operacyjnego IOS-XE.

### <a name="configure-interfaces-and-subinterfaces"></a>Konfigurowanie interfejsów i podinterpołew
Będziesz potrzebować jednego podinterface na komunikację równorzędnej w każdym routerze, który łączysz się z firmą Microsoft. Podprzeszczegłów można zidentyfikować za pomocą identyfikatora sieci VLAN lub skumulowanej pary identyfikatorów sieci VLAN i adresu IP.

**Definicja interfejsu Dot1Q**

Ten przykład zawiera definicję podinterpołeza dla podintertwa z pojedynczym identyfikatorem sieci VLAN. Identyfikator sieci VLAN jest unikatowy dla komunikacji równorzędnej. Ostatni oktet twojego adresu IPv4 będzie zawsze nieparzystą liczbą.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Definicja interfejsu QinQ**

Ten przykład zawiera definicję podinterpołezy dla podintertwa z dwoma identyfikatorami VLAN. Zewnętrzny identyfikator sieci VLAN (s-tag), jeśli jest używany, pozostaje taki sam we wszystkich elementach równorzędnych. Wewnętrzny identyfikator sieci VLAN (c-tag) jest unikatowy na komunikację równorzędną. Ostatni oktet twojego adresu IPv4 będzie zawsze nieparzystą liczbą.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="set-up-ebgp-sessions"></a>Konfigurowanie sesji eBGP
Należy skonfigurować sesję Protokołu BGP z firmą Microsoft dla każdej komunikacji równorzędnej. Skonfiguruj sesję BGP przy użyciu następującego przykładu. Jeśli adres IPv4 użyty dla podinterpołu był a.b.c.d, adres IP sąsiada BGP (Microsoft) będzie a.b.c.d+1. Ostatni oktet adresu IPv4 sąsiada protokołu IPv4 protokołu BGP będzie zawsze liczbą parzysty.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Konfigurowanie prefiksów, które mają być anonsowane w sesji BGP
Skonfiguruj router do anonsowania wybranych prefiksów do firmy Microsoft przy użyciu poniższego przykładu.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="route-maps"></a>Mapy tras
Użyj map tras i list prefiksów, aby filtrować prefiksy propagowane do sieci. Zobacz poniższy przykład i upewnij się, że masz skonfigurowane listy prefiksów.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !

### <a name="configure-bfd"></a>Konfigurowanie BFD

BfD skonfigurujesz w dwóch miejscach: jednym na poziomie interfejsu, a drugim na poziomie BGP. Przykładem jest interfejs QinQ. 

    interface GigabitEthernet<Interface_Number>.<Number>
     bfd interval 300 min_rx 300 multiplier 3
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> fall-over bfd
     exit-address-family
    !


## <a name="juniper-mx-series-routers"></a>Routery z serii Juniper MX
Przykłady w tej sekcji dotyczą dowolnego routera serii Juniper MX.

### <a name="configure-interfaces-and-subinterfaces"></a>Konfigurowanie interfejsów i podinterpołew

**Definicja interfejsu Dot1Q**

Ten przykład zawiera definicję podinterpołeza dla podintertwa z pojedynczym identyfikatorem sieci VLAN. Identyfikator sieci VLAN jest unikatowy dla komunikacji równorzędnej. Ostatni oktet twojego adresu IPv4 będzie zawsze nieparzystą liczbą.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


**Definicja interfejsu QinQ**

Ten przykład zawiera definicję podinterpołezy dla podintertwa z dwoma identyfikatorami VLAN. Zewnętrzny identyfikator sieci VLAN (s-tag), jeśli jest używany, pozostaje taki sam we wszystkich elementach równorzędnych. Wewnętrzny identyfikator sieci VLAN (c-tag) jest unikatowy na komunikację równorzędną. Ostatni oktet twojego adresu IPv4 będzie zawsze nieparzystą liczbą.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="set-up-ebgp-sessions"></a>Konfigurowanie sesji eBGP
Należy skonfigurować sesję Protokołu BGP z firmą Microsoft dla każdej komunikacji równorzędnej. Skonfiguruj sesję BGP przy użyciu następującego przykładu. Jeśli adres IPv4 użyty dla podinterpołu był a.b.c.d, adres IP sąsiada BGP (Microsoft) będzie a.b.c.d+1. Ostatni oktet adresu IPv4 sąsiada protokołu IPv4 protokołu BGP będzie zawsze liczbą parzysty.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Konfigurowanie prefiksów, które mają być anonsowane w sesji BGP
Skonfiguruj router do anonsowania wybranych prefiksów do firmy Microsoft przy użyciu poniższego przykładu.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter 
    <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="route-policies"></a>Zasady trasy
Mapy tras i listy prefiksów można używać do filtrowania prefiksów propagowanych w sieci. Zobacz poniższy przykład i upewnij się, że masz skonfigurowane listy prefiksów.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
                prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="configure-bfd"></a>Konfigurowanie BFD
Skonfiguruj BFD tylko w sekcji protokołu BGP.

    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
                bfd-liveness-detection {
                       minimum-interval 3000;
                       multiplier 3;
                }
            }                               
        }                                   
    }


## <a name="next-steps"></a>Następne kroki
Szczegółowe informacje znajdują się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).



