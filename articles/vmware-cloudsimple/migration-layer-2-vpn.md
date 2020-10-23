---
title: Rozwiązanie VMware firmy Azure przez CloudSimple — rozciąga sieć lokalną do chmury prywatnej
description: Opisuje sposób konfigurowania sieci VPN warstwy 2 między NSX-T w chmurze prywatnej CloudSimple i lokalnym autonomicznym klientem programu Edge NSX
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f524bf6af66d44bc13b7c0957de7977968cbef28
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427261"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>Migrowanie obciążeń przy użyciu rozciągniętych sieci warstwy 2

W tym przewodniku dowiesz się, jak używać sieci VPN warstwy 2 (L2VPN) w celu rozciągnięcia sieci warstwy 2 ze środowiska lokalnego do prywatnej chmury CloudSimple. To rozwiązanie umożliwia migrację obciążeń działających w lokalnym środowisku VMware do chmury prywatnej na platformie Azure w tej samej przestrzeni adresowej podsieci bez konieczności ponownego tworzenia pakietów IP obciążeń.

L2VPN oparte na rozciągnięciu sieci warstwy 2 mogą współpracować z sieciami opartymi na NSX i bez nich w lokalnym środowisku VMware. Jeśli nie masz sieci opartych na NSX na potrzeby obciążeń lokalnych, możesz użyć autonomicznej bramy usługi NSX Edge.

> [!NOTE]
> Ten przewodnik obejmuje scenariusz, w którym lokalne centra danych w chmurze prywatnej są połączone za pośrednictwem sieci VPN typu lokacja-lokacja.

## <a name="deployment-scenario"></a>Scenariusz wdrożenia

Aby rozciągnąć sieć lokalną za pomocą usługi L2VPN, należy skonfigurować serwer L2VPN (router NSX-T tier0) i klienta programu L2VPN (źródłowy klient autonomiczny).  

W tym scenariuszu wdrażania Chmura prywatna jest połączona ze środowiskiem lokalnym za pośrednictwem tunelu sieci VPN typu lokacja-lokacja, która umożliwia lokalne zarządzanie chmurą i podsiecie vMotion w celu komunikowania się z nimi. To rozwiązanie jest niezbędne w przypadku programu vCenter vMotion (xVC-vMotion). NSX-T tier0 router jest wdrażany jako serwer L2VPN w chmurze prywatnej.

Autonomiczna NSX Edge jest wdrażana w środowisku lokalnym jako klient L2VPN, a następnie sparowany z serwerem L2VPN. Punkt końcowy tunelu GRE jest tworzony po każdej stronie i skonfigurowany do "rozciągnięcia" lokalnej sieci warstwy 2 do chmury prywatnej. Ta konfiguracja jest przedstawiona na poniższej ilustracji.

![Scenariusz wdrożenia](media/l2vpn-deployment-scenario.png)

Aby dowiedzieć się więcej na temat migracji przy użyciu sieci VPN L2, zobacz [wirtualne sieci prywatne](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) w dokumentacji programu VMware.

## <a name="prerequisites-for-deploying-the-solution"></a>Wymagania wstępne dotyczące wdrażania rozwiązania

Przed wdrożeniem i skonfigurowaniem rozwiązania Sprawdź, czy zostały wprowadzone następujące elementy:

* Lokalna wersja vSphere to 6.7 U1 + lub 6.5 P03 +.
* Licencja lokalna vSphere jest na poziomie przedsiębiorstwa plus (dla przełącznika rozproszonego vSphere).
* Określ sieć warstwy 2 obciążenia do rozciągnięcia do chmury prywatnej.
* Zidentyfikuj sieć warstwy 2 w środowisku lokalnym w celu wdrożenia urządzenia klienckiego usługi L2VPN.
* [Chmura prywatna została już utworzona](create-private-cloud.md).
* Wersja autonomicznego urządzenia NSX-T Edge jest zgodna z wersją Menedżera NSX-T (NSX-T 2.3.0) używaną w środowisku chmury prywatnej.
* Grupa portów magistrali została utworzona w lokalnym serwerze vCenter z włączonymi transmisjami sfałszowanymi.
* Publiczny adres IP został zarezerwowany do użycia dla autonomicznego adresu IP pasma klienta NSX-T, a do translacji między tymi dwoma adresami jest stosowane 1:1 NAT.
* Funkcja przekazywania DNS jest ustawiana na lokalnych serwerach DNS dla domeny az.cloudsimple.io, aby wskazywała na serwery DNS w chmurze prywatnej.
* Opóźnienie RTT jest mniejsze niż lub równe 150 MS, co jest wymagane, aby vMotion działały między dwiema lokacjami.

## <a name="limitations-and-considerations"></a>Ograniczenia i istotne zagadnienia

W poniższej tabeli wymieniono obsługiwane wersje vSphere i typy adapterów sieci.  

| wersja vSphere | Typ źródłowego przełącznika vSwitch | Sterownik wirtualnej karty sieciowej | Docelowy typ przełącznika vSwitch | Obsługiwane? |
------------ | ------------- | ------------ | ------------- | ------------- 
| Wszystko | Usługa | Wszystko | Usługa | Tak |
| vSphere 6,7 UI lub nowszy, 6.5 P03 lub nowszy | Usługa | VMXNET3 | N-VDS | Tak |
| vSphere 6,7 UI lub nowszy, 6.5 P03 lub nowszy | Usługa | E1000 | N-VDS | [Nieobsługiwane na VWware](https://kb.vmware.com/s/article/56991) |
| vSphere 6,7 UI lub 6.5 P03, NSX-V lub wersje poniżej NSX-T 2.2, 6.5 P03 lub nowszy | Wszystko | Wszystko | N-VDS | [Nieobsługiwane na VWware](https://kb.vmware.com/s/article/56991) |

Od wersji programu VMware NSX-T 2,3:

* Nie można jednocześnie skierować przełącznika logicznego na stronie chmury prywatnej, który jest rozciągany do lokalizacji lokalnej przez L2VPN. Rozciągany przełącznik logiczny nie może być podłączony do routera logicznego.
* L2VPN i sieci VPN oparte na trasach można skonfigurować tylko przy użyciu wywołań interfejsu API.

Aby uzyskać więcej informacji, zobacz [wirtualne sieci prywatne](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) w dokumentacji programu VMware.

### <a name="sample-l2-vpn-deployment-addressing"></a>Przykładowe rozmieszczenie wdrożenia sieci VPN L2

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>Sieć lokalna, w której wdrożono Autonomiczny klient sieci VPN (L2) ESG

| **Element** | **Wartość** |
|------------|-----------------|
| Nazwa sieci | MGMT_NET_VLAN469 |
| OKREŚLONE | 469 |
| CIDR| 10.250.0.0/24 |
| Adres IP autonomicznego urządzenia brzegowego | 10.250.0.111 |
| Adres IP samodzielnego urządzenia brzegowego | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>Sieć lokalna do rozciągnięcia

| **Element** | **Wartość** |
|------------|-----------------|
| OKREŚLONE | 472 |
| CIDR| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>Schemat IP chmury prywatnej dla routera NSX-T tier0 (usługa sieci VPN L2)

| **Element** | **Wartość** |
|------------|-----------------|
| Interfejs sprzężenia zwrotnego | 192.168.254.254/32 |
| Interfejs tunelu | 5.5.5.1/29 |
| Przełącznik logiczny (rozciągnięty) | Stretch_LS |
| Interfejs sprzężenia zwrotnego (adres IP translatora adresów sieciowych) | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>Sieć chmury prywatnej, która ma być zmapowana do sieci rozproszonej

| **Element** | **Wartość** |
|------------|-----------------|
| OKREŚLONE | 712 |
| CIDR| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>Pobieranie identyfikatora routera logicznego wymaganego przez L2VPN

Poniższe kroki pokazują, jak pobrać identyfikator routera logicznego usługi tier0 DR dla usług IPsec i L2VPN. Identyfikator routera logicznego jest potrzebny później podczas implementowania L2VPN.

1. Zaloguj się do Menedżera NSX-T `https://*nsx-t-manager-ip-address*` i wybierz pozycję **Network**  >  **routers**  >  **Provider-LR**  >  **— Przegląd**. W obszarze **tryb wysokiej dostępności**wybierz pozycję **aktywne-w stanie wstrzymania**. Ta akcja powoduje otwarcie okna podręcznego, w którym jest wyświetlana maszyna wirtualna, na której znajduje się router tier0.

    ![Wybierz pozycję aktywne — wstrzymanie](media/l2vpn-fetch01.png)

2. Wybierz krawędzie węzłów **sieci szkieletowej**  >  **Nodes**  >  **Edges**. Zanotuj adres IP zarządzania aktywnej maszyny wirtualnej brzegowej (Edge VM1) zidentyfikowany w poprzednim kroku.

    ![Adres IP zarządzania uwagami](media/l2vpn-fetch02.png)

3. Otwórz sesję SSH na adres IP zarządzania dla maszyny wirtualnej brzegowej. Uruchom ```get logical-router``` polecenie z nazwą użytkownika **admin** i hasłem **CloudSimple 123!**.

    ![Zrzut ekranu pokazujący otwartą sesję SSH.](media/l2vpn-fetch03.png)

4. Jeśli nie widzisz wpisu "DR-Provider-LR", wykonaj następujące czynności.

5. Utwórz dwa przełączniki logiczne z przyłożeniem do wykonania. Jeden przełącznik logiczny jest rozciągany do lokalnego miejsca, w którym znajdują się zmigrowane obciążenia. Innym przełącznikiem logicznym jest przełącznik fikcyjny. Aby uzyskać instrukcje, zobacz [Tworzenie przełącznika logicznego](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html) w dokumentacji programu VMware.

    ![Tworzenie przełącznika logicznego](media/l2vpn-fetch04.png)

6. Dołącz fikcyjny przełącznik do routera pomoc z linkiem lokalnego adresu IP lub dowolną nienakładaną się podsiecią z lokalnej lub prywatnej chmury. Zobacz [Dodawanie portu pobranego do routera logicznego warstwy 1](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html) w dokumentacji programu VMware.

    ![Dołącz przełącznik fikcyjny](media/l2vpn-fetch05.png)

7. Ponownie uruchom `get logical-router` polecenie w sesji SSH maszyny wirtualnej brzegowej. Zostanie wyświetlony identyfikator UUID routera logicznego "DR-Provider-LR". Zanotuj identyfikator UUID, który jest wymagany podczas konfigurowania L2VPN.

    ![Zrzut ekranu pokazujący identyfikator UUID dla routera logicznego.](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>Pobieranie identyfikatora przełącznika logicznego wymaganego przez L2VPN

1. Zaloguj się do Menedżera NSX-T ( `https://nsx-t-manager-ip-address` ).
2. Wybierz **Networking**kolejno  >  **Switching**  >  **Opcje** przełączania sieci  >  **< \> przełącznik \logical**  >  **Overview**.
3. Zanotuj identyfikator UUID przełącznika logicznego rozciąganego, który jest wymagany podczas konfigurowania L2VPN.

    ![Pobieranie danych wyjściowych routera logicznego](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>Zagadnienia dotyczące routingu i zabezpieczeń dla L2VPN

Aby nawiązać połączenie sieci VPN opartej na trasach IPsec między routerem NSX-T tier0 i autonomicznym klientem NSX Edge, interfejs sprzężenia zwrotnego routera NSX-T tier0 musi być w stanie komunikować się z publicznym adresem IP NSX autonomicznego klienta lokalnego za pośrednictwem protokołu UDP 500/4500.

### <a name="allow-udp-5004500-for-ipsec"></a>Zezwalaj na protokół UDP 500/4500 dla protokołu IPsec

1. [Utwórz publiczny adres IP](public-ips.md) dla interfejsu sprzężenia zwrotnego NSX-T tier0 w portalu CloudSimple.

2. [Utwórz tabelę zapory](firewall.md) z regułami stanowymi, które zezwalają na ruch przychodzący UDP 500/4500 i Dołącz tabelę zapory do podsieci HostTransport NSX-T.

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>Anonsuj adres IP interfejsu sprzężenia zwrotnego w sieci underlay

1. Utwórz trasę o wartości null dla sieci interfejsu sprzężenia zwrotnego. Zaloguj się do Menedżera NSX-T i wybierz pozycję **Network**  >  **Routing**  >  **routers**  >  **Provider-LR**  >  **Routing**  >  **static Routes**. Kliknij pozycję **Dodaj**. W polu **Sieć**wprowadź adres IP interfejsu sprzężenia zwrotnego. W przypadku **następnych przeskoków**kliknij przycisk **Dodaj**, określ wartość "null" dla następnego przeskoku i pozostaw wartość domyślną 1 dla opcji Odległość dla administratorów.

    ![Dodawanie trasy statycznej](media/l2vpn-routing-security01.png)

2. Utwórz listę prefiksów IP. Zaloguj się do Menedżera NSX-T i wybierz kolejno pozycje Routing **sieci**  >  **Routing**  >  **routery**  >  **dostawcy — LR**  >  **Routing**  >  **adresów IP**. Kliknij pozycję **Dodaj**. Wprowadź nazwę, aby zidentyfikować listę. W przypadku **prefiksów**kliknij dwukrotnie przycisk **Dodaj** . W pierwszym wierszu wprowadź wartość "0.0.0.0/0" dla **sieci** i "Odmów" dla **akcji**. W drugim wierszu wybierz **dowolne** dla **sieci** i **Zezwalaj** na **działanie**.
3. Dołącz listę prefiksów IP do sąsiadów BGP (TOR). Dołączanie listy prefiksów IP do sąsiada BGP uniemożliwia anonsowanie trasy domyślnej w protokole BGP do przełączników TOR. Jednak każda inna trasa obejmująca trasę o wartości null anonsuje adres IP interfejsu sprzężenia zwrotnego z przełącznikami TOR.

    ![Utwórz listę prefiksów IP](media/l2vpn-routing-security02.png)

4. Zaloguj się do Menedżera NSX-T i wybierz pozycję **Sieć**  >  **Routing**  >  **routers**  >  **Provider-LR**  >  **Routing**  >  **BGP**  >  **Neighbors**. Wybierz pierwszy sąsiada. Kliknij pozycję **Edytuj**  >  **rodziny adresów**. W przypadku rodziny IPv4 należy edytować kolumnę **out Filter** i wybrać utworzoną przez siebie listę prefiksów IP. Kliknij pozycję **Zapisz**. Powtórz ten krok dla drugiego sąsiada.

    ![Dołącz prefiks IP lista 1 ](media/l2vpn-routing-security03.png) ![ dołączanie prefiksu IP lista 2](media/l2vpn-routing-security04.png)

5. Dystrybuuj ponownie wartość null statycznej trasy do protokołu BGP. Aby anonsować trasę interfejsu sprzężenia zwrotnego z underlay, należy ponownie przeprowadzić dystrybucję wartości null statycznej trasy do protokołu BGP. Zaloguj się do Menedżera NSX-T i wybierz pozycję **Network**  >  **Routing**  >  **routers**  >  **Provider-LR**  >  **Routing**  >  **redystrybucyjny**  >  **Neighbors**. Wybierz pozycję **Provider-LR-Route_Redistribution** i kliknij przycisk **Edytuj**. Zaznacz pole wyboru **statycznego** , a następnie kliknij przycisk **Zapisz**.

    ![Ponowne dystrybuowanie zerowej trasy statycznej do protokołu BGP](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>Konfigurowanie sieci VPN opartej na trasach na routerze tier0 NSX-T

Użyj poniższego szablonu, aby wypełnić wszystkie szczegóły dotyczące konfigurowania sieci VPN opartej na trasach na NSX-T tier0 router. Identyfikatory UUID w każdym wywołaniu POST są wymagane w kolejnych wywołaniach POST. Adresy IP dla interfejsów sprzężenia zwrotnego i tunelu dla L2VPN muszą być unikatowe i nie nakładają się na sieci lokalne lub w chmurze prywatnej.

Adresy IP wybrane dla sprzężenia zwrotnego i interfejsu tunelu używane dla L2VPN muszą być unikatowe i nie nakładają się na sieci lokalne lub w chmurze prywatnej. Sieć interfejsu sprzężenia zwrotnego musi być zawsze/32.

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

Dla wszystkich poniższych wywołań interfejsu API Zastąp adres IP adresem IP Menedżera NSX-T. Można uruchomić wszystkie te wywołania interfejsu API z klienta programu POSTer lub przy użyciu `curl` poleceń.

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>Włącz usługę IPSec sieci VPN na routerze logicznym

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

### <a name="create-profiles-ike"></a>Tworzenie profilów: IKE

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

### <a name="create-profiles-dpd"></a>Utwórz profile: DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>Tworzenie profilów: tunel

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

### <a name="create-a-peer-endpoint"></a>Tworzenie punktu końcowego równorzędnego

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

### <a name="create-a-route-based-vpn-session"></a>Tworzenie sesji sieci VPN opartej na trasach

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

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>Konfigurowanie L2VPN na routerze tier0 NSX-T

Wprowadź następujące informacje po każdym wywołaniu. Identyfikatory są wymagane w kolejnych wywołaniach POST.

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

Dla następującego polecenia POST, identyfikator routera logicznego to UUID tier0 routera logicznego DR pobranego wcześniej.

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>Tworzenie sesji L2VPN

Dla następującego polecenia POST identyfikator usługi L2VPN jest identyfikatorem, który został właśnie uzyskany, a identyfikator sesji sieci VPN protokołu IPsec to identyfikator uzyskany w poprzedniej sekcji.

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

Te wywołania tworzą punkt końcowy tunelu GRE. Aby sprawdzić stan, uruchom następujące polecenie.

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

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>Utwórz port logiczny z określonym IDENTYFIKATORem tunelu

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

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>Uzyskaj kod elementu równorzędnego dla L2VPN na stronie NSX-T

Uzyskaj kod równorzędny punktu końcowego NSX-T. Kod elementu równorzędnego jest wymagany podczas konfigurowania zdalnego punktu końcowego. Identyfikator sesji <L2VPN> można uzyskać z poprzedniej sekcji. Aby uzyskać więcej informacji, zobacz [Podręcznik interfejsu API NSX-T 2,3](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html).

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>Wdrażanie klienta autonomicznego NSX-T (lokalnego)

Przed wdrożeniem upewnij się, że lokalne reguły zapory zezwalają na ruch przychodzący i wychodzących protokołu UDP 500/4500 z/do publicznego adresu IP CloudSimple, który został wcześniej zarezerwowany dla interfejsu sprzężenia zwrotnego routera T0 NSX-T. 

1. [Pobierz autonomiczny klient NSX Edge](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230) OVF i Wyodrębnij pliki z pobranego pakietu do folderu.

    ![Pobierz autonomiczny klient NSX Edge](media/l2vpn-deploy-client01.png)

2. Przejdź do folderu zawierającego wszystkie wyodrębnione pliki. Wybierz wszystkie VMDK (NSX-l2t-Client-Large. MF i NSX-l2t-client-large. OVF dla dużego rozmiaru urządzenia lub NSX-l2t-Client-xlarge. MF i NSX-l2t-client-Xlarge. OVF dla dodatkowego rozmiaru urządzenia o dużym rozmiarze). Kliknij przycisk **Dalej**.

    ![Wybierz ](media/l2vpn-deploy-client02.png) ![ zrzut ekranu szablonu, który pokazuje wybrane pliki VMDK.](media/l2vpn-deploy-client03.png)

3. Wprowadź nazwę klienta autonomicznego NSX-T i kliknij przycisk **dalej**.

    ![Wprowadź nazwę szablonu](media/l2vpn-deploy-client04.png)

4. Kliknij przycisk **dalej** , aby uzyskać dostęp do ustawień magazynu danych. Wybierz odpowiedni magazyn danych dla klienta autonomicznego NSX-T i kliknij przycisk **dalej**.

    ![Wybieranie magazynu danych](media/l2vpn-deploy-client06.png)

5. Wybierz odpowiednie grupy portów dla magistrali sieci (magistrala PG), publicznego (pasma PG) i wysokiej dostępności (pasma) dla klienta autonomicznego NSX-T. Kliknij przycisk **Dalej**.

    ![Wybieranie grup portów](media/l2vpn-deploy-client07.png)

6. Wypełnij następujące szczegóły na ekranie **Dostosowywanie szablonu** , a następnie kliknij przycisk **dalej**:

    Rozwiń L2T:

    * **Adres elementu równorzędnego**. Wprowadź adres IP zarezerwowany w portalu Azure CloudSimple dla interfejsu sprzężenia zwrotnego NSX-T tier0.
    * **Kod elementu równorzędnego**. Wklej kod elementu równorzędnego uzyskany z ostatniego kroku wdrożenia serwera L2VPN.
    * **Interfejsy podrzędne VLAN (Identyfikator tunelu)**. Wprowadź identyfikator sieci VLAN do rozciągnięcia. W nawiasach () wprowadź wcześniej skonfigurowany identyfikator tunelu.

    Rozwiń węzeł pasma:

    * **Adres IP systemu DNS**. Wprowadź adres IP lokalnego systemu DNS.
    * **Brama domyślna**.  Wprowadź domyślną bramę sieci VLAN, która będzie pełnić rolę bramy domyślnej dla tego klienta.
    * **Adres IP**. Wprowadź adres IP dla klienta autonomicznego.
    * **Długość prefiksu**. Wprowadź długość prefiksu sieci VLAN/podsieci.
    * **Administrator interfejsu wiersza polecenia/Włącz/hasło użytkownika root**. Ustaw hasło dla konta admin/Enable/root.

      ![Dostosuj szablon ](media/l2vpn-deploy-client08.png)
       ![ Dostosuj szablon — więcej](media/l2vpn-deploy-client09.png)

7. Przejrzyj ustawienia i kliknij przycisk **Zakończ**.

    ![Ukończ konfigurację](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>Konfigurowanie lokalnego portu ujścia

Jeśli jedna z witryn sieci VPN nie ma wdrożonego NSX, można skonfigurować sieć VPN L2, wdrażając autonomiczną krawędź NSX w tej lokacji. Autonomiczna NSX Edge jest wdrażana za pomocą pliku OVF na hoście, który nie jest zarządzany przez NSX. Spowoduje to wdrożenie urządzenia bramy usług NSX Edge do działania jako klient sieci VPN L2.

Jeśli autonomiczna wirtualnej karty sieciowej magistrali brzegowej jest połączona z przełącznikiem rozproszonym vSphere, funkcja sieci VPN L2 musi mieć Tryb nieograniczony lub port ujścia. Użycie trybu ogólnego może spowodować zduplikowanie poleceń ping i zduplikowanych odpowiedzi. Z tego powodu Użyj trybu portu ujścia w konfiguracji autonomicznej NSX krawędzi sieci VPN L2. Zobacz [Konfigurowanie portu ujścia](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html) w dokumentacji programu VMware.

## <a name="ipsec-vpn-and-l2vpn-verification"></a>IPsec sieci VPN i weryfikacja L2VPN

Użyj następujących poleceń, aby zweryfikować sesje IPsec i L2VPN z autonomicznej krawędzi NSX-T.

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

Użyj następujących poleceń, aby zweryfikować sesje IPsec i L2VPN z routera tier0 NSX-T.

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

Użyj następujących poleceń, aby zweryfikować port ujścia na hoście ESXi, w którym znajduje się autonomiczna maszyna wirtualna klienta NSX-T w środowisku lokalnym.

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
