---
title: Rozwiązanie VMware platformy Azure według cloudSimple — rozciągnij lokalną sieć warstwy 2 do chmury prywatnej
description: W tym artykule opisano sposób konfigurowania sieci VPN warstwy 2 między siecią NSX-T w chmurze prywatnej CloudSimple a lokalnym autonomicznym klientem usługi NSX Edge
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1f5ff48f4d5a658a1bbb4e6b9fb4b3f0f3fb190f
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81602693"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>Migrowanie obciążeń przy użyciu rozciągniętych sieci warstwy 2

W tym przewodniku dowiesz się, jak używać sieci VPN warstwy 2 (L2VPN) do rozciągania sieci warstwy 2 ze środowiska lokalnego do chmury CloudSimple Private Cloud. To rozwiązanie umożliwia migrację obciążeń działających w lokalnym środowisku VMware do chmury prywatnej na platformie Azure w tej samej przestrzeni adresowej podsieci bez konieczności ponownego współtworzenia obciążeń.

Rozciąganie sieci warstwy 2 oparte na L2VPN może współpracować z sieciami opartymi na sieciach NSX lub bez nich w lokalnym środowisku VMware. Jeśli nie masz sieci opartych na NSX dla obciążeń lokalnych, możesz użyć autonomicznej bramy usług NSX Edge.

> [!NOTE]
> W tym przewodniku opisano scenariusz, w którym lokalne i prywatne centra danych chmury są połączone za pośrednictwem sieci VPN lokacja-lokacja.

## <a name="deployment-scenario"></a>Scenariusz wdrożenia

Aby rozciągnąć sieć lokalną za pomocą L2VPN, należy skonfigurować serwer L2VPN (docelowy router NSX-T Tier0) i klienta L2VPN (klienta autonomicznego źródła).  

W tym scenariuszu wdrażania chmura prywatna jest połączona ze środowiskiem lokalnym za pośrednictwem tunelu sieci VPN lokacja lokacja, który umożliwia lokalne zarządzanie i podsieci vMotion komunikowanie się z podsieciami zarządzania prywatną i vMotion. Układ ten jest niezbędny dla Cross vCenter vMotion (xVC-vMotion). Router NSX-T Tier0 jest wdrażany jako serwer L2VPN w chmurze prywatnej.

Autonomiczna usługa NSX Edge jest wdrażana w środowisku lokalnym jako klient L2VPN, a następnie sparowana z serwerem L2VPN. Punkt końcowy tunelu GRE jest tworzony z każdej strony i skonfigurowany do "rozciągnięcia" lokalnej sieci warstwy 2 do chmury prywatnej. Ta konfiguracja jest przedstawiona na poniższym rysunku.

![Scenariusz wdrożenia](media/l2vpn-deployment-scenario.png)

Aby dowiedzieć się więcej o migracji przy użyciu sieci VPN L2, zobacz [Wirtualne sieci prywatne](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) w dokumentacji VMware.

## <a name="prerequisites-for-deploying-the-solution"></a>Wymagania wstępne dotyczące wdrażania rozwiązania

Przed wdrożeniem i skonfigurowaniem rozwiązania sprawdź, czy istnieją następujące elementy:

* Lokalna wersja vSphere to 6.7U1+ lub 6.5P03+.
* Lokalna licencja vSphere znajduje się na poziomie Enterprise Plus (dla przełącznika rozproszonego vSphere).
* Zidentyfikuj obciążenie sieci warstwy 2, które ma zostać rozciągnięte do chmury prywatnej.
* Zidentyfikuj sieć warstwy 2 w środowisku lokalnym w celu wdrożenia urządzenia klienckiego L2VPN.
* [Chmura prywatna jest już utworzona](create-private-cloud.md).
* Wersja autonomicznego urządzenia NSX-T Edge jest zgodna z wersją NSX-T Manager (NSX-T 2.3.0) używaną w środowisku private cloud.
* Grupa portów pnia została utworzona w lokalnym centrum vCenter z włączonymi sfałszowanymi transmisjami.
* Publiczny adres IP został zarezerwowany do użycia dla autonomicznego adresu IP klienta NSX-T, a 1:1 NAT jest na miejscu do tłumaczenia między dwoma adresami.
* Przekazywanie dns jest ustawiane na lokalnych serwerach DNS dla domeny az.cloudsimple.io, aby wskazywały serwery DNS chmury prywatnej.
* Opóźnienie RTT jest mniejsze lub równe 150 ms, zgodnie z wymaganiami dla vMotion do pracy w dwóch lokacjach.

## <a name="limitations-and-considerations"></a>Ograniczenia i istotne zagadnienia

W poniższej tabeli wymieniono obsługiwane wersje vSphere i typy adapterów sieciowych.  

| Wersja vSphere | Źródło vSwitch typu | Wirtualny sterownik karty sieciowej | Typ przełącznika docelowego | Obsługiwane? |
------------ | ------------- | ------------ | ------------- | ------------- 
| Wszystkie | Dvs | Wszystkie | Dvs | Tak |
| vSfera 6.7UI lub wyższa, 6.5P03 lub wyższa | Dvs | VMXNET3 | N-VDS (N-VDS) | Tak |
| vSfera 6.7UI lub wyższa, 6.5P03 lub wyższa | Dvs | E1000 ( E1000 ) | N-VDS (N-VDS) | [Nie jest obsługiwany na VWware](https://kb.vmware.com/s/article/56991) |
| vSphere 6.7UI lub 6.5P03, NSX-V lub wersje poniżej NSX-T2.2, 6.5P03 lub nowsze | Wszystkie | Wszystkie | N-VDS (N-VDS) | [Nie jest obsługiwany na VWware](https://kb.vmware.com/s/article/56991) |

W wersji VMware NSX-T 2.3:

* Przełącznik logiczny po stronie chmury prywatnej, który jest rozciągnięty do lokalnego za L2VPN nie może być routowany w tym samym czasie. Rozciągniętego przełącznika logicznego nie można podłączyć do routera logicznego.
* L2VPN i oparte na trasach VPN IPSEC można skonfigurować tylko przy użyciu wywołań API.

Aby uzyskać więcej informacji, zobacz [wirtualne sieci prywatne](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) w dokumentacji VMware.

### <a name="sample-l2-vpn-deployment-addressing"></a>Adresowanie wdrażania sieci VPN w przykładzie L2

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>Sieć lokalna, w której wdrożono autonomiczną sieć ESG (L2 VPN client),

| **Element** | **Wartość** |
|------------|-----------------|
| Nazwa sieci | MGMT_NET_VLAN469 |
| Sieci vlan | 469 |
| Cidr| 10.250.0.0/24 |
| Adres IP urządzenia autonomicznego urządzenia Edge | 10.250.0.111 |
| Adres IP urządzenia autonomicznego urządzenia Brzegowego | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>Sieć lokalna, która ma zostać rozciągnięta

| **Element** | **Wartość** |
|------------|-----------------|
| Sieci vlan | 472 |
| Cidr| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>Schemat IP chmury prywatnej dla routera NSX-T Tier0 (obsługa sieci VPN L2)

| **Element** | **Wartość** |
|------------|-----------------|
| Interfejs sprzężenia zwrotnego | 192.168.254.254/32 |
| Interfejs tunelu | 5.5.5.1/29 |
| Przełącznik logiczny (rozciągnięty) | Stretch_LS |
| Interfejs sprzężenia zwrotnego (adres IP TRANSLATORA) | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>Sieć chmury prywatnej, która ma być mapowana do sieci rozciągniętej

| **Element** | **Wartość** |
|------------|-----------------|
| Sieci vlan | 712 |
| Cidr| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>Pobierz identyfikator routera logicznego potrzebny dla L2VPN

Poniższe kroki pokazują, jak pobrać identyfikator routera logicznego wystąpienia routera logicznego Tier0 DR dla usług IPsec i L2VPN. Identyfikator routera logicznego jest potrzebny później podczas implementowania L2VPN.

1. Zaloguj się do NSX-T `https://*nsx-t-manager-ip-address*` Manager i wybierz opcję Dostawca**routerów sieciowych-LR** > **Provider-LR** >  **Networking** > **Omówienie**. W **trybie wysokiej dostępności**wybierz pozycję **Active-Standby**. Ta akcja otwiera okno podręczne z maszyną wirtualną Edge, na której router Tier0 jest aktualnie aktywny.

    ![Wybieranie aktywnego stan gotowości](media/l2vpn-fetch01.png)

2. Wybierz opcję >  **Krawędzie** > **węzłów****sieci szkieletowej**. Zanotuj adres IP zarządzania aktywnej maszyny Wirtualnej usługi Edge (Edge VM1) określonej w poprzednim kroku.

    ![Adres IP zarządzania notatkami](media/l2vpn-fetch02.png)

3. Otwórz sesję SSH na adres IP zarządzania maszyny Wirtualnej usługi Edge. Uruchom ```get logical-router``` polecenie z **adminem** nazwy użytkownika i hasłem **CloudSimple 123!**.

    ![pobierz dane wyjściowe routera logicznego](media/l2vpn-fetch03.png)

4. Jeśli nie widzisz wpisu "DR-Provider-LR", wykonaj następujące kroki.

5. Utwórz dwa przełączniki logiczne z nakładki. Jeden przełącznik logiczny jest rozciągnięty do lokalnego miejsca, w którym znajdują się migrowane obciążenia. Innym przełącznikiem logicznym jest przełącznik manekina. Aby uzyskać instrukcje, zobacz [Tworzenie przełącznika logicznego](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html) w dokumentacji VMware.

    ![Tworzenie przełącznika logicznego](media/l2vpn-fetch04.png)

6. Dołącz fikcyjny przełącznik do routera Tier1 z lokalnym adresem IP łącza lub dowolną nienakładającą się podsiecią z lokalnego lub prywatnego chmury. Zobacz [Dodawanie portu łącza w dół na routerze logicznym warstwy 1](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html) w dokumentacji VMware.

    ![Podłącz przełącznik manekina](media/l2vpn-fetch05.png)

7. Uruchom `get logical-router` polecenie ponownie na sesji SSH maszyny Wirtualnej krawędzi. Zostanie wyświetlony identyfikator UUID routera logicznego "DR-Provider-LR". Zanotuj UUID, który jest wymagany podczas konfigurowania L2VPN.

    ![pobierz dane wyjściowe routera logicznego](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>Pobierz identyfikator przełącznika logicznego potrzebny dla L2VPN

1. Zaloguj się do [NSX-T Manager](https://nsx-t-manager-ip-address).
2. Wybierz**przełączniki**  >  >  **przełączania** >  **sieciowego****<\Przegląd\>przełączników** > **logicznych**.
3. Zanotuj UUID przełącznika logicznego stretch, który jest wymagany podczas konfigurowania L2VPN.

    ![pobierz dane wyjściowe routera logicznego](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>Routing i zagadnienia dotyczące zabezpieczeń dla L2VPN

Aby ustanowić sieć VPN opartą na routice IPsec między routerem NSX-T Tier0 a autonomicznym klientem usługi NSX Edge, interfejs sprzężenia zwrotnego routera NSX-T Tier0 musi być w stanie komunikować się z publicznym adresem IP klienta autonomicznego NSX lokalnie za pośrednictwem protokołu UDP 500/4500.

### <a name="allow-udp-5004500-for-ipsec"></a>Zezwalaj na protokół UDP 500/4500 dla protokołu IPsec

1. [Utwórz publiczny adres IP](public-ips.md) dla interfejsu sprzężenia pętli NSX-T Tier0 w portalu CloudSimple.

2. [Utwórz tabelę zapory](firewall.md) ze stanowymi regułami, które zezwalają na ruch przychodzący UDP 500/ 4500 i dołącz tabelę zapory do podsieci HostTransport NSX-T.

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>Anonsowanie adresu IP interfejsu sprzężenia zwrotnego do sieci podkładów

1. Utwórz trasę zerową dla sieci interfejsu sprzężenia zwrotnego. Zaloguj się do Menedżera NSX-T i wybierz pozycję**Routery** >  > **routingu** >  **sieciowego****Provider-LR** > **Routing** > **Tras statycznych**. Kliknij pozycję **Add** (Dodaj). W przypadku **aplikacji Sieć**wprowadź adres IP interfejsu sprzężenia zwrotnego. W polu **Następny przeskok**kliknij przycisk **Dodaj**, określ wartość "Null" dla następnego przeskoku i zachowaj domyślną wartość 1 dla odległości administratora.

    ![Dodawanie trasy statycznej](media/l2vpn-routing-security01.png)

2. Tworzenie listy prefiksów IP. Zaloguj się do Menedżera NSX-T i wybierz pozycję Listy**prefiksów IP****routerów** >  > **routingu** >  **sieciowego****Provider-LR** > **Routing** > . Kliknij pozycję **Add** (Dodaj). Wprowadź nazwę, aby zidentyfikować listę. W przypadku **prefiksów**kliknij pozycję **Dodaj** dwa razy. W pierwszym wierszu wprowadź "0.0.0.0/0" dla **sieci** i "Odmów" dla **akcji**. W drugim wierszu wybierz **opcję Dowolna** dla **sieci** i **Zezwolenie** na **działanie**.
3. Dołącz listę prefiksów IP do obu sąsiadów BGP (TOR). Dołączanie listy prefiksów IP do sąsiada Protokołu BGP zapobiega anonsowaniu trasy domyślnej w UD do przełączników TOR. Jednak każda inna trasa, która zawiera trasę null, anonsuje adres IP interfejsu sprzężenia zwrotnego do przełączników TOR.

    ![Tworzenie listy prefiksów IP](media/l2vpn-routing-security02.png)

4. Zaloguj się do Menedżera NSX-T i wybierz pozycję **Networking** > **Routing** > **Routers** > **Provider-LR** > **Routing** > **BGP** > **Neighbors**. Wybierz pierwszego sąsiada. Kliknij **pozycję Edytuj** > **rodziny adresów**. W przypadku rodziny IPv4 edytuj kolumnę **Filtr wyjścia** i wybierz utworzoną listę prefiksów IP. Kliknij pozycję **Zapisz**. Powtórz ten krok dla drugiego sąsiada.

    ![Dołącz listę prefiksów IP 1](media/l2vpn-routing-security03.png) ![Dołącz listę prefiksów IP 2](media/l2vpn-routing-security04.png)

5. Redystrybucja null statycznej trasy do protokołu BGP. Aby anonsować trasę interfejsu sprzężenia zwrotnego do podkładania, należy ponownie rozesłać null statycznej trasy do protokołu BGP. Zaloguj się do Menedżera NSX-T i wybierz pozycję Sąsiadów**redystrybucji** > **Routers** > trasy**routingu** >  **sieciowego** > **Provider-LR** > **Routing** > Route **.** Wybierz **pozycję Dostawca-LR-Route_Redistribution** i kliknij przycisk **Edytuj**. Zaznacz pole wyboru **Statyczne** i kliknij przycisk **Zapisz**.

    ![Redystrybucja zerowej trasy statycznej do protokołu BGP](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>Konfigurowanie sieci VPN opartej na trasach na routerze NSX-T Tier0

Użyj następującego szablonu, aby wypełnić wszystkie szczegóły konfigurowania sieci VPN opartej na trasie na routerze NSX-T Tier0. Identyfikatory UUID w każdym wywołaniu POST są wymagane w kolejnych wywołaniach POST. Adresy IP dla interfejsów sprzężenia zwrotnego i tunelu dla L2VPN muszą być unikatowe i nie pokrywają się z sieciami lokalnymi lub sieciami chmury prywatnej.

Adresy IP wybrane dla interfejsu sprzężenia zwrotnego i tunelu używanego dla L2VPN muszą być unikatowe i nie pokrywać się z sieciami lokalnymi lub sieciami chmury prywatnej. Sieć interfejsu sprzężenia zwrotnego musi zawsze być /32.

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section "Steps to fetch Logical-Router ID needed for L2VPN"
Logical-switch ID(Stretch) : UUID of Stretch Logical Switch obtained earlier
IPSec Service ID :
IKE profile ID :
DPD profile ID :
Tunnel Profile ID :
Local-endpoint ID :
Peer end-point ID :
IPSec VPN session ID (route-based) :
L2VPN service ID :
L2VPN session ID :
Logical-Port ID :
Peer Code :
```

W przypadku wszystkich następujących wywołań interfejsu API zastąp adres IP adresem IP menedżera NSX-T. Wszystkie te wywołania interfejsu API można uruchomić `curl` z klienta POSTMAN lub za pomocą poleceń.

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>Włączanie usługi IPSec VPN na routerze logicznym

```
POST   https://192.168.110.201/api/v1/vpn/ipsec/services/
{
"resource_type": "IPSecVPNService",
"description": "Manage VPN service",
"display_name": "IPSec VPN service",
"logical_router_id": "Logical-router ID",
"ike_log_level": "INFO",
"enabled": true
}
```

### <a name="create-profiles-ike"></a>Tworzenie profili: IKE

```
POST https://192.168.110.201/api/v1/vpn/ipsec/ike-profiles
 
{
"resource_type": "IPSecVPNIKEProfile",
"description": "IKEProfile for siteA",
"display_name": "IKEProfile siteA",
"encryption_algorithms": ["AES_128"],
"ike_version": "IKE_V2",
"digest_algorithms": ["SHA2_256"],
"sa_life_time":21600,
"dh_groups": ["GROUP14"]
}
```

### <a name="create-profiles-dpd"></a>Tworzenie profili: DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>Tworzenie profili: Tunel

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/tunnel-profiles
 
{
"resource_type": "IPSecVPNTunnelProfile",
"display_name": "nsx-default-tunnel-profile",
"enable_perfect_forward_secrecy": true,
"encryption_algorithms": ["AES_GCM_128"],
"digest_algorithms": [],
"sa_life_time":3600,
"dh_groups": ["GROUP14"],
"encapsulation_mode": "TUNNEL_MODE",
"transform_protocol": "ESP",
"df_policy": "COPY"
}
```

### <a name="create-a-local-endpoint"></a>Tworzenie lokalnego punktu końcowego

``` 
POST https://192.168.110.201/api/v1/vpn/ipsec/local-endpoints
 
{
"resource_type": "IPSecVPNLocalEndpoint",
"description": "Local endpoint",
"display_name": "Local endpoint",
"local_id": "<Public IP of Loopback interface>",
"ipsec_vpn_service_id": {
"target_id": "IPSec VPN service ID"},
"local_address": "<IP of Loopback interface>",
"trust_ca_ids": [],
"trust_crl_ids": []
}
```

### <a name="create-a-peer-endpoint"></a>Tworzenie równorzędnego punktu końcowego

```
POST https://192.168.110.201/api/v1/vpn/ipsec/peer-endpoints

{
"resource_type": "IPSecVPNPeerEndpoint",
"description": "Peer endpoint for site B",
"display_name": "Peer endpoint for site B",
"connection_initiation_mode": "INITIATOR",
"authentication_mode": "PSK",
"ipsec_tunnel_profile_id": "IPSec Tunnel profile ID",
"dpd_profile_id": "DPD profile ID",
"psk":"nsx",
"ike_profile_id": "IKE profile ID",
"peer_address": "<Public IP of Standalone client",
"peer_id": "<Public IP of Standalone client>"
}
```

### <a name="create-a-route-based-vpn-session"></a>Tworzenie sesji sieci VPN opartej na marszrutach

```
POST :  https://192.168.110.201/api/v1/vpn/ipsec/sessions
 
{
"resource_type": "RouteBasedIPSecVPNSession",
"peer_endpoint_id": "Peer Endpoint ID",
"ipsec_vpn_service_id": "IPSec VPN service ID",
"local_endpoint_id": "Local Endpoint ID",
"enabled": true,
"tunnel_ports": [
{
"ip_subnets": [
{
"ip_addresses": [
 "5.5.5.1"
],
"prefix_length": 24
}
  ]
}
]
}
```

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>Konfigurowanie L2VPN na routerze NSX-T Tier0

Wypełnij następujące informacje po każdym wywołaniu POST. Identyfikatory są wymagane w kolejnych wywołaniach POST.

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>Tworzenie usługi L2VPN

Dane wyjściowe następującego polecenia GET będą puste, ponieważ konfiguracja nie została jeszcze ukończona.

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

W przypadku następującego polecenia POST identyfikator routera logicznego jest identyfikatorem UUID routera logicznego sieci dr Tier0 uzyskanego wcześniej.

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>Tworzenie sesji L2VPN

Dla następującego polecenia POST identyfikator usługi L2VPN jest identyfikatorem, który właśnie uzyskałeś, a identyfikator sesji sieci VPN IPsec jest identyfikatorem uzyskanym w poprzedniej sekcji.

```    
POST: https://192.168.110.201/api/v1/vpn/l2vpn/sessions

{
"l2vpn_service_id" : "L2VPN service ID",
"transport_tunnels" : [
    {
    "target_id" : "IPSec VPN session ID"
    }]
}
```

Te wywołania utworzyć punkt końcowy tunelu GRE. Aby sprawdzić stan, uruchom następujące polecenie.

```
edge-2> get tunnel-port
Tunnel      : 44648dae-8566-5bc9-a065-b1c4e5c3e03f
IFUID       : 328
LOCAL       : 169.254.64.1
REMOTE      : 169.254.64.2
ENCAP       : GRE

Tunnel      : cf950ca1-5cf8-5438-9b1a-d2c8c8e7229b
IFUID       : 318
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.152
ENCAP       : GENEVE

Tunnel      : 63639321-87c5-529e-8a61-92c1939799b2
IFUID       : 304
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.156
ENCAP       : GENEVE
```

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>Tworzenie portu logicznego z określonym identyfikatorem tunelu

```
    POST https://192.168.110.201/api/v1/logical-ports/

{
"resource_type": "LogicalPort",
"display_name": "Extend logicalSwitch, port for service",
"logical_switch_id": "Logical switch ID",
"admin_state" : "UP",
"attachment": {
"attachment_type":"L2VPN_SESSION",
"id":"L2VPN session ID",
"context" : {
"resource_type" : "L2VpnAttachmentContext",
    "tunnel_id" : 10
}
    }
        }
```

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>Uzyskaj kod równorzędny dla L2VPN po stronie NSX-T

Uzyskaj kod równorzędny punktu końcowego NSX-T. Kod równorzędny jest wymagany podczas konfigurowania zdalnego punktu końcowego. L2VPN <> sesyjnym można uzyskać z poprzedniej sekcji. Aby uzyskać więcej informacji, zobacz [NSX-T 2.3 API Guide](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html).

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>Wdrażanie klienta autonomicznego NSX-T (lokalnego)

Przed wdrożeniem sprawdź, czy lokalne reguły zapory zezwalają na przychodzący i wychodzący ruch UDP 500/4500 z/do publicznego adresu IP CloudSimple, który został zarezerwowany wcześniej dla interfejsu zwrotnego sprzężenia zwrotnego routera NSX-T T0. 

1. [Pobierz autonomiczny klient NSX Edge](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230) OVF i wyodrębnić pliki z pobranego pakietu do folderu.

    ![Pobierz samodzielny klient NSX Edge](media/l2vpn-deploy-client01.png)

2. Przejdź do folderu ze wszystkimi wyodrębnionych plikami. Wybierz wszystkie vmdks (NSX-l2t-client-large.mf i NSX-l2t-client-large.ovf dla dużych rozmiarów urządzenia lub NSX-l2t-client-Xlarge.mf i NSX-l2t-client-Xlarge.ovf dla bardzo dużych rozmiarów urządzenia). Kliknij przycisk **Dalej**.

    ![Wybierz](media/l2vpn-deploy-client02.png) ![szablon Wybierz szablon](media/l2vpn-deploy-client03.png)

3. Wprowadź nazwę klienta autonomicznego NSX-T i kliknij przycisk **Dalej**.

    ![Wprowadź nazwę szablonu](media/l2vpn-deploy-client04.png)

4. Kliknij **przycisk Dalej zgodnie** z potrzebami, aby przejść do ustawień magazynu danych. Wybierz odpowiedni magazyn danych dla klienta autonomicznego NSX-T i kliknij przycisk **Dalej**.

    ![Wybierz magazyn danych](media/l2vpn-deploy-client06.png)

5. Wybierz odpowiednie grupy portów dla trunk (Trunk PG), public (Uplink PG) i HA interface (Uplink PG) dla klienta autonomicznego NSX-T. Kliknij przycisk **Dalej**.

    ![Wybieranie grup portów](media/l2vpn-deploy-client07.png)

6. Wypełnij następujące szczegóły na ekranie **Dostosowywanie szablonu** i kliknij przycisk **Dalej:**

    Rozwiń L2T:

    * **Adres równorzędny**. Wprowadź adres IP zarezerwowany w portalu Usługi Azure CloudSimple dla interfejsu sprzężenia pętli NSX-T Tier0.
    * **Kod równorzędny**. Wklej kod równorzędny uzyskany z ostatniego kroku wdrożenia serwera L2VPN.
    * **Interfejsy podrzędne VLAN (Tunnel ID)**. Wprowadź identyfikator sieci VLAN, który ma zostać rozciągnięty. W nawiasach () wprowadź identyfikator tunelu, który został wcześniej skonfigurowany.

    Rozwiń interfejs łącza nadrzędnego:

    * **DNS Adres IP**. Wprowadź lokalny adres IP DNS.
    * **Brama domyślna**.  Wprowadź bramę domyślną sieci VLAN, która będzie działać jako brama domyślna dla tego klienta.
    * **Adres IP**. Wprowadź adres IP łącza nadrzędnego klienta autonomicznego.
    * **Długość prefiksu**. Wprowadź długość prefiksu sieci VLAN/podsieci nadrzędnej.
    * **Cli admin/enable/root User Password**. Ustaw hasło dla konta admin /enable /root.

      ![Dostosowywanie](media/l2vpn-deploy-client08.png)
      ![szablonu Dostosowywanie szablonu - więcej](media/l2vpn-deploy-client09.png)

7. Przejrzyj ustawienia i kliknij przycisk **Zakończ**.

    ![Pełna konfiguracja](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>Konfigurowanie lokalnego portu ujścia

Jeśli jedna z lokacji sieci VPN nie ma wdrożonej sieci NSX, można skonfigurować sieć VPN L2, wdrażając autonomiczną krawędź NSX w tej lokacji. Autonomiczna krawędź NSX jest wdrażana przy użyciu pliku OVF na hoście, który nie jest zarządzany przez NSX. Spowoduje to wdrożenie urządzenia bramy usług krawędzi NSX, aby funkcjonować jako klient sieci VPN L2.

Jeśli autonomiczny magistral krawędzi vNIC jest podłączony do przełącznika rozproszonego vSphere, tryb rozwiązłe lub port ujścia jest wymagany dla funkcji sieci VPN L2. Użycie trybu rozwiązłego może powodować duplikaty pingów i duplikaty odpowiedzi. Z tego powodu użyj trybu portu ujścia w autonomicznej konfiguracji sieci VPN L2 NSX Edge. Zobacz [Konfigurowanie portu ujścia](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html) w dokumentacji VMware.

## <a name="ipsec-vpn-and-l2vpn-verification"></a>Weryfikacja IPsec VPN i L2VPN

Poniższe polecenia umożliwia weryfikację sesji IPsec i L2VPN ze autonomicznej krawędzi NSX-T.

```
nsx-l2t-edge> show service ipsec
-----------------------------------------------------------------------
vShield Edge IPSec Service Status:
IPSec Server is running.
AESNI is enabled.
Total Sites: 1, 1 UP, 0 Down
Total Tunnels: 1, 1 UP, 0 Down
----------------------------------
Site:  10.250.0.111_0.0.0.0/0-104.40.21.81_0.0.0.0/0
Channel: PeerIp: 104.40.21.81    LocalIP: 10.250.0.111  Version: IKEv2  Status: UP
Tunnel: PeerSubnet: 0.0.0.0/0    LocalSubnet: 0.0.0.0/0   Status: UP
----------------------------------
```

```
nsx-l2t-edge> show service l2vpn
L2 VPN is running
----------------------------------------
L2 VPN type: Client/Spoke

SITENAME                       IPSECSTATUS          VTI                  GRE
1ecb00fb-a538-4740-b788-c9049e8cb6c6 UP                   vti-100              l2t-1
```

Poniższe polecenia umożliwia weryfikację sesji IPsec i L2VPN z routera NSX-T Tier0.

```
edge-2> get ipsecvpn session
Total Number of Sessions: 1

IKE Session ID : 3
UUID           : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Type           : Route

Local IP       : 192.168.254.254      Peer IP        : 192.227.85.167
Local ID       : 104.40.21.81         Peer ID        : 192.227.85.167
Session Status : Up

Policy Rules
    VTI UUID       : 4bf96e3b-e50b-49cc-a16e-43a6390e3d53
    ToRule ID      : 560874406            FromRule ID    : 2708358054
    Local Subnet   : 0.0.0.0/0            Peer Subnet    : 0.0.0.0/0
    Tunnel Status  : Up
```

```
edge-2> get l2vpn session
Session       : f7147137-5dd0-47fe-9e53-fdc2b687b160
Tunnel        : b026095b-98c8-5932-96ff-dda922ffe316
IPSEC Session : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Status        : UP
```

Poniższe polecenia można zweryfikować port ujścia na hoście ESXi, gdzie maszyna wirtualna klienta autonomicznego NSX-T znajduje się w środowisku lokalnym.

```
 [root@esxi02:~] esxcfg-vswitch -l |grep NSX
  53                  1           NSXT-client-large.eth2
  225                1           NSXT-client-large.eth1
  52                  1           NSXT-client-large.eth0
```

```
[root@esxi02:~] net-dvs -l | grep "port\ [0-9]\|SINK\|com.vmware.common.alias"
                com.vmware.common.alias = csmlab2DS ,   propType = CONFIG
        port 24:
        port 25:
        port 26:
        port 27:
        port 13:
        port 19:
        port 169:
        port 54:
        port 110:
        port 6:
        port 107:
        port 4:
        port 199:
        port 168:
        port 201:
        port 0:
        port 49:
        port 53:
        port 225:
                com.vmware.etherswitch.port.extraEthFRP =   SINK
        port 52:
```
