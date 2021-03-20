---
title: 'Azure ExpressRoute: Przykłady konfiguracji routera'
description: Użyj tych przykładów konfiguracji interfejsu i routingu dla routerów Cisco IOS-XE i Juniper MX Series jako przykłady do pracy z usługą Azure ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: duau
ms.openlocfilehash: 3bc850f02884ae0547c2ecf56a46a57a4e66a752
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89397410"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Przykłady konfiguracji routera umożliwiające skonfigurowanie routingu i zarządzanie nim
Ta strona zawiera przykłady konfiguracji interfejsu i routingu dla routerów z serii Cisco IOS-XE i Juniper MX podczas pracy z usługą Azure ExpressRoute.

> [!IMPORTANT]
> Przykłady na tej stronie są wyłącznie dla wskazówek. Aby znaleźć odpowiednie konfiguracje, należy skontaktować się z zespołem handlowym/technicznym dostawcy i zespołem sieciowym. Firma Microsoft nie będzie obsługiwać problemów związanych z konfiguracjami wymienionymi na tej stronie. Skontaktuj się z dostawcą urządzenia w celu uzyskania problemów z pomocą techniczną.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Ustawienia MTU i TCP/Settings w interfejsach routera
Maksymalna jednostka transmisji (MTU) dla interfejsu ExpressRoute to 1500, który jest typowym domyślną wartością MTU dla interfejsu Ethernet na routerze. Jeśli router nie ma domyślnie innej jednostki MTU, nie ma potrzeby określania wartości w interfejsie routera.

W przeciwieństwie do bramy sieci VPN platformy Azure nie trzeba określać maksymalnego rozmiaru segmentu TCP dla obwodu ExpressRoute.

Przykłady konfiguracji routera w tym artykule mają zastosowanie do wszystkich komunikacji równorzędnych. Aby uzyskać więcej informacji na temat routingu, przejrzyj wymagania dotyczące [komunikacji równorzędnej ExpressRoute](expressroute-circuit-peerings.md) i [routingu ExpressRoute](expressroute-routing.md) .


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS — systemy oparte na interfejsie XE
Przykłady w tej sekcji dotyczą dowolnego routera z rodziną systemów operacyjnych IOS-XE.

### <a name="configure-interfaces-and-subinterfaces"></a>Konfigurowanie interfejsów i podinterfejsów
Na każdym routerze, który nawiązuje połączenie z firmą Microsoft, będzie potrzebny jeden interfejs podrzędny. Interfejs podrzędny może być identyfikowany za pomocą identyfikatora sieci VLAN lub skumulowanej pary identyfikatorów sieci VLAN i adresu IP.

**Definicja interfejsu Dot1Q**

Ten przykład zawiera definicję podinterfejsu dla podinterfejsu z pojedynczym IDENTYFIKATORem sieci VLAN. Identyfikator sieci VLAN jest unikatowy dla poszczególnych komunikacji równorzędnych. Ostatni oktet adresu IPv4 zawsze będzie liczbą nieparzystą.

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <VLAN_ID>
 ip address <IPv4_Address><Subnet_Mask>
```

**Definicja interfejsu QinQ**

Ten przykład zawiera definicję podinterfejsu dla podinterfejsu z dwoma identyfikatorami sieci VLAN. Zewnętrzny identyfikator sieci VLAN (s-tag), jeśli jest używany, pozostaje taki sam dla wszystkich komunikacji równorzędnych. Wewnętrzny identyfikator sieci VLAN (c-tag) jest unikatowy dla komunikacji równorzędnej. Ostatni oktet adresu IPv4 zawsze będzie liczbą nieparzystą.

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
 ip address <IPv4_Address><Subnet_Mask>
```

### <a name="set-up-ebgp-sessions"></a>Konfigurowanie sesji eBGP
Należy skonfigurować sesję protokołu BGP dla każdej komunikacji równorzędnej z firmą Microsoft. Skonfiguruj sesję BGP przy użyciu poniższego przykładu. Jeśli adres IPv4 używany przez interfejs podrzędny to a. b. c. d, wówczas adres IP sąsiada BGP (Microsoft) będzie a. b. c. d + 1. Ostatni oktet adresu IPv4 sąsiada BGP zawsze będzie liczbą parzystą.

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
 neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Skonfiguruj prefiksy do anonsowania za pośrednictwem sesji BGP
Skonfiguruj router, aby anonsować wybór prefiksów do firmy Microsoft przy użyciu poniższego przykładu.

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  network <Prefix_to_be_advertised> mask <Subnet_mask>
  neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="route-maps"></a>Mapy tras
Użyj map tras i list prefiksów do filtrowania prefiksów rozmnożonych do sieci. Zapoznaj się z poniższym przykładem i upewnij się, że skonfigurowano odpowiednie listy prefiksów.

```console
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
```

### <a name="configure-bfd"></a>Konfigurowanie BFD

Skonfigurujesz BFD w dwóch miejscach: jeden na poziomie interfejsu i inny na poziomie protokołu BGP. Przykład dotyczy interfejsu QinQ. 

```console
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
```


## <a name="juniper-mx-series-routers"></a>Routery serii Juniper MX
Przykłady w tej sekcji dotyczą dowolnego routera serii Juniper MX.

### <a name="configure-interfaces-and-subinterfaces"></a>Konfigurowanie interfejsów i podinterfejsów

**Definicja interfejsu Dot1Q**

Ten przykład zawiera definicję podinterfejsu dla podinterfejsu z pojedynczym IDENTYFIKATORem sieci VLAN. Identyfikator sieci VLAN jest unikatowy dla poszczególnych komunikacji równorzędnych. Ostatni oktet adresu IPv4 zawsze będzie liczbą nieparzystą.

```console
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
```


**Definicja interfejsu QinQ**

Ten przykład zawiera definicję podinterfejsu dla podinterfejsu z dwoma identyfikatorami sieci VLAN. Zewnętrzny identyfikator sieci VLAN (s-tag), jeśli jest używany, pozostaje taki sam dla wszystkich komunikacji równorzędnych. Wewnętrzny identyfikator sieci VLAN (c-tag) jest unikatowy dla komunikacji równorzędnej. Ostatni oktet adresu IPv4 zawsze będzie liczbą nieparzystą.

```console
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
```

### <a name="set-up-ebgp-sessions"></a>Konfigurowanie sesji eBGP
Należy skonfigurować sesję protokołu BGP dla każdej komunikacji równorzędnej z firmą Microsoft. Skonfiguruj sesję BGP przy użyciu poniższego przykładu. Jeśli adres IPv4 używany przez interfejs podrzędny to a. b. c. d, wówczas adres IP sąsiada BGP (Microsoft) będzie a. b. c. d + 1. Ostatni oktet adresu IPv4 sąsiada BGP zawsze będzie liczbą parzystą.

```console
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
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Skonfiguruj prefiksy do anonsowania za pośrednictwem sesji BGP
Skonfiguruj router, aby anonsować wybór prefiksów do firmy Microsoft przy użyciu poniższego przykładu.

```console
    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
                route-filter; 
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
                export <Policy_Name>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="route-policies"></a>Zasady tras
Za pomocą map tras i list prefiksów można filtrować prefiksy rozmnożone do sieci. Zapoznaj się z poniższym przykładem i upewnij się, że skonfigurowano odpowiednie listy prefiksów.

```console
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
                export <Policy_Name>;
                import <MS_Prefixes_Inbound>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="configure-bfd"></a>Konfigurowanie BFD
Skonfiguruj BFD w sekcji protokołu BGP.

```console
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
```

### <a name="configure-macsec"></a>Konfigurowanie MACSec
W przypadku konfiguracji MACSec, klucz skojarzenia łączności (CAK) i nazwa klucza skojarzenia łączności (CKN) muszą być zgodne ze skonfigurowanymi wartościami za pośrednictwem poleceń programu PowerShell.

```console
    security {
        macsec {
            connectivity-association <Connectivity_Association_Name> {
                cipher-suite gcm-aes-xpn-128;
                security-mode static-cak;
                pre-shared-key {
                    ckn <Connectivity_Association_Key_Name>;
                    cak <Connectivity_Association_Key>; ## SECRET-DATA
                }
            }
            interfaces {
                <Interface_Number> {
                    connectivity-association <Connectivity_Association_Name>;
                }
            }
        }
    }
```

## <a name="next-steps"></a>Następne kroki
Szczegółowe informacje znajdują się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).



