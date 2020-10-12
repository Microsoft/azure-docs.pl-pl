---
title: 'Azure ExpressRoute: przykłady konfiguracji routera — translator adresów sieciowych'
description: Ta strona zawiera przykłady konfiguracji routera dla routerów Cisco i Juniper.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: duau
ms.openlocfilehash: 4b57dec32a3a008cadf68c19ea52353a23fb2cd3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89397498"
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>Przykłady konfiguracji routera do konfigurowania translatora adresów sieciowych i zarządzania nim

Ta strona zawiera przykłady konfiguracji NAT dla routerów serii Cisco ASA i Juniper SRX podczas pracy z usługą ExpressRoute. Są one przeznaczone wyłącznie do celów informacyjnych i nie mogą być używane zgodnie z oczekiwaniami. Możesz skontaktować się z dostawcą, aby uzyskać odpowiednie konfiguracje dla sieci.

> [!IMPORTANT]
> Przykłady na tej stronie mają być przeznaczone wyłącznie do celów informacyjnych. Musisz współpracować z zespołem ds. sprzedaży/technicznym i zespołem sieciowym, aby sprostać potrzebom. Firma Microsoft nie będzie obsługiwać problemów związanych z konfiguracjami wymienionymi na tej stronie. Aby uzyskać pomoc techniczną, należy skontaktować się z dostawcą urządzenia.
> 
> 

* Poniższe przykłady konfiguracji routera dotyczą usług komunikacji równorzędnej Azure Public i Microsoft. Nie należy konfigurować translatora adresów sieciowych dla prywatnej komunikacji równorzędnej Azure. Aby uzyskać więcej informacji, przejrzyj wymagania dotyczące [komunikacji równorzędnej ExpressRoute](expressroute-circuit-peerings.md) i [ExpressRoute translatora adresów sieciowych](expressroute-nat.md) .

* Do połączenia z Internetem i ExpressRoute należy użyć oddzielnych pul adresów IP NAT. Używanie tej samej puli adresów IP NAT przez Internet i ExpressRoute spowoduje asymetryczne Routing i utratę łączności.


## <a name="cisco-asa-firewalls"></a>Zapory Cisco ASA
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>Konfiguracja przesyłania ruchu z sieci klienta do firmy Microsoft

```console
object network MSFT-PAT
  range <SNAT-START-IP> <SNAT-END-IP>


object-group network MSFT-Range
  network-object <IP> <Subnet_Mask>

object-group network on-prem-range-1
  network-object <IP> <Subnet-Mask>

object-group network on-prem-range-2
  network-object <IP> <Subnet-Mask>

object-group network on-prem
  network-object object on-prem-range-1
  network-object object on-prem-range-2

nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range
```

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>Konfiguracja przesyłania ruchu od firmy Microsoft do sieci klienta

**Interfejsy i kierunek:**

Interfejs źródłowy (w którym ruch przechodzi do ASA): wewnątrz interfejsu docelowego (w którym ruch wychodzący z ASA): poza

**Skonfigurować**

Pula NAT:

```console
object network outbound-PAT
    host <NAT-IP>
```

Serwer docelowy:

```console
object network Customer-Network
    network-object <IP> <Subnet-Mask>
```

Grupa obiektów dla adresów IP klientów:

```console
object-group network MSFT-Network-1
    network-object <MSFT-IP> <Subnet-Mask>

object-group network MSFT-PAT-Networks
    network-object object MSFT-Network-1
```

Polecenia translatora adresów sieciowych:

```console
nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network
```


## <a name="juniper-srx-series-routers"></a>Routery serii SRX Juniper
### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. Utwórz nadmiarowe interfejsy sieci Ethernet dla klastra

```console
    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }
```

### <a name="2-create-two-security-zones"></a>2. Utwórz dwie strefy zabezpieczeń
* Strefa zaufania dla sieci wewnętrznej i strefy zaufania dla routerów granicznych skierowanych do sieci zewnętrznej
* Przypisywanie odpowiednich interfejsów do stref
* Zezwalaj na usługi w interfejsach

```console
    security {
        zones {
            security-zone Trust {
                host-inbound-traffic {
                    system-services {
                        ping;
                    }
                    protocols {
                        bgp;
                    }
                }
                interfaces {
                    reth0.100;
                }
            }
            security-zone Untrust {
                host-inbound-traffic {
                    system-services {
                        ping;
                    }
                    protocols {
                        bgp;
                    }
                }
                interfaces {
                    reth1.100;
                }
            }
        }
    }
```


### <a name="3-create-security-policies-between-zones"></a>3. Tworzenie zasad zabezpieczeń między strefami

```console
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
            from-zone Untrust to-zone Trust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
    }
```

### <a name="4-configure-nat-policies"></a>4. Skonfiguruj zasady NAT
* Utwórz dwie pule NAT. Zostanie ona użyta do TRANSLACJi ruchu wychodzącego do firmy Microsoft przez firmę Microsoft i innych użytkowników z programu.
* Tworzenie reguł translatora adresów sieciowych odpowiedniego ruchu

```console
       security {
           nat {
               source {
                   pool SNAT-To-ExpressRoute {
                       routing-instance {
                           External-ExpressRoute;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   pool SNAT-From-ExpressRoute {
                       routing-instance {
                           Internal;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   rule-set Outbound_NAT {
                       from routing-instance Internal;
                       to routing-instance External-ExpressRoute;
                       rule SNAT-Out {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-To-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
                   rule-set Inbound-NAT {
                       from routing-instance External-ExpressRoute;
                       to routing-instance Internal;
                       rule SNAT-In {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-From-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
               }
           }
       }
```

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. Skonfiguruj protokół BGP do anonsowania selektywnych prefiksów w każdym kierunku
Zapoznaj się z przykładami na stronie Przykłady [konfiguracji routingu](expressroute-config-samples-routing.md) .

### <a name="6-create-policies"></a>6. Utwórz zasady

```console
    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }
```

## <a name="next-steps"></a>Następne kroki
Szczegółowe informacje znajdują się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).

