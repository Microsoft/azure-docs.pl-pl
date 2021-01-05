---
title: Rozwiązanie VMware firmy Azure według CloudSimple — Konfigurowanie wysokiej dostępności z poziomu lokalnego do CloudSimple bramy sieci VPN
description: Opisuje sposób konfigurowania połączenia wysokiej dostępności z poziomu środowiska lokalnego do bramy sieci VPN CloudSimple z włączoną obsługą wysokiej dostępności
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 80805aaa172518c40c7ad123ca24361ee0f15e69
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895703"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>Konfigurowanie połączenia o wysokiej dostępności z poziomu lokalnego z bramą sieci VPN CloudSimple

Administratorzy sieci mogą skonfigurować połączenie sieci VPN typu lokacja-lokacja protokołu IPsec o wysokiej dostępności ze środowiska lokalnego do bramy sieci VPN CloudSimple.

W tym przewodniku przedstawiono procedurę konfigurowania zapory lokalnej na potrzeby połączenia wysokiej dostępności sieci VPN typu lokacja-lokacja protokołu IPsec. Szczegółowe kroki są specyficzne dla typu zapory lokalnej. Przykładowo w tym przewodniku przedstawiono kroki dla dwóch typów zapór: Cisco ASA i Palo Alto Networks.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed skonfigurowaniem zapory lokalnej należy wykonać poniższe zadania.

1. Sprawdź, czy Twoja organizacja [zabrała](create-nodes.md) wymagane węzły i utworzyła co najmniej jedną chmurę prywatną CloudSimple.
2. [Skonfiguruj bramę sieci VPN typu lokacja-lokacja](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) między siecią lokalną i chmurą prywatną CloudSimple.

Zobacz [Omówienie bram sieci VPN](cloudsimple-vpn-gateways.md) dla obsługiwanych propozycji fazy 1 i fazy 2.

## <a name="configure-on-premises-cisco-asa-firewall"></a>Konfigurowanie lokalnej zapory Cisco ASA

Instrukcje w tej sekcji dotyczą programu Cisco ASA w wersji 8,4 lub nowszej. W przykładzie konfiguracji jest wdrażana i konfigurowana w trybie protokołu IKEv1 oprogramowanie firmy Cisco adaptacyjnego urządzenia zabezpieczeń w wersji 9,10.

Aby sieć VPN typu lokacja-lokacja działała, należy zezwolić na ruch wychodzący UDP 500/4500 i ESP (protokół IP 50) z podstawowego i pomocniczego publicznego adresu IP (peer IP) CloudSimple w interfejsie zewnętrznym lokalnej bramy sieci VPN Cisco ASA.

### <a name="1-configure-phase-1-ikev1"></a>1. Skonfiguruj fazę 1 (IKEv1)

Aby włączyć fazę 1 (IKEv1) w interfejsie zewnętrznym, wprowadź następujące polecenie interfejsu wiersza polecenia w zaporze Cisco ASA.

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2. Utwórz zasady protokołu IKEv1

Utwórz zasady protokołu IKEv1 definiujące algorytmy i metody, które mają być używane na potrzeby tworzenia skrótów, uwierzytelniania, Diffie-Hellman grupy, okresu istnienia i szyfrowania.

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. Utwórz grupę tuneli

Utwórz grupę tuneli w obszarze atrybuty protokołu IPsec. Skonfiguruj adres IP elementu równorzędnego i klucz wstępny tunelu ustawiany podczas [konfigurowania bramy sieci VPN typu lokacja-lokacja](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. Konfigurowanie fazy 2 (IPsec)

Aby skonfigurować fazę 2 (IPsec), Utwórz listę kontroli dostępu (ACL), która definiuje ruch do zaszyfrowania i tunelowania. W poniższym przykładzie związany z tym ruch pochodzi z tunelu pochodzącego z lokalnej podsieci lokalnego (10.16.1.0/24) do podsieci zdalnej chmury prywatnej (192.168.0.0/24). Lista ACL może zawierać wiele wpisów w przypadku wielu podsieci między lokacjami.

W programie Cisco ASA w wersji 8,4 i nowszych można utworzyć obiekty lub grupy obiektów, które służą jako kontenery dla sieci, podsieci, adresów IP hostów lub wielu obiektów. Utwórz obiekt dla lokalnego i obiektu dla podsieci zdalnych i użyj ich dla list ACL kryptograficznego i instrukcji NAT.

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>Zdefiniuj lokalną podsieć lokalna jako obiekt

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>Zdefiniuj podsieć zdalną CloudSimple jako obiekt

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>Skonfiguruj listę dostępu dla interesującego ruchu

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. Skonfiguruj zestaw transformacji

Skonfiguruj zestaw transformacji (TS), który musi zawierać słowo kluczowe ```ikev1``` . Atrybuty szyfrowania i wyznaczania wartości skrótu określone w TS muszą być zgodne z parametrami wymienionymi w [konfiguracji domyślnej dla bram sieci VPN CloudSimple](cloudsimple-vpn-gateways.md).

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. Konfigurowanie mapy kryptograficznej

Skonfiguruj mapę kryptograficzną, która zawiera następujące składniki:

* Adres IP elementu równorzędnego
* Zdefiniowana lista kontroli dostępu, która zawiera ruch interesujący
* Zestaw transformacji

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. stosowanie mapy kryptograficznej

Zastosuj mapę kryptograficzną w interfejsie zewnętrznym:

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8. Potwierdź odpowiednie reguły NAT

Poniżej znajduje się reguła NAT, która jest używana. Upewnij się, że ruch sieci VPN nie jest objęty żadną inną regułą NAT.

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>Przykładowe połączenie sieci VPN typu lokacja-lokacja z usługą Cisco ASA

Wyjście fazy 1:

![Wyjście fazy 1 dla zapory Cisco ASA](media/ha-vpn-connection-cisco-phase1.png)

Wyjście fazy 2:

![Wyjście fazy 2 dla zapory Cisco ASA](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>Skonfiguruj zaporę lokalnej Palo Alto Networks

Instrukcje w tej sekcji dotyczą Palo Alto Networks w wersji 7,1 lub nowszej. W tym przykładzie konfiguracji Palo Alto Networks VM-Series Software Version 8.1.0 jest wdrażany i konfigurowany w trybie protokołu IKEv1.

Aby sieć VPN typu lokacja-lokacja działała, należy zezwolić na ruch wychodzący UDP 500/4500 i ESP (protokół IP 50) z podstawowego i pomocniczego publicznego adresu IP (peer IP) CloudSimple w interfejsie zewnętrznym bramy Palo Alto Networks.

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. Tworzenie podstawowych i pomocniczych interfejsów tunelu

Zaloguj się do zapory Palo Alto, wybierz pozycję   >  **interfejsy** sieciowe  >  **tunel**  >  **Dodaj**, skonfiguruj następujące pola i kliknij przycisk **OK**.

* Nazwa interfejsu. Pierwsze pole jest wypełniane automatycznie za pomocą słowa kluczowego "Tunnel". W sąsiednim polu Wprowadź dowolną liczbę z zakresu od 1 do 9999. Ten interfejs będzie używany jako podstawowy interfejs tunelowania do przenoszenia ruchu między lokacjami między lokalnym centrum danych a chmurą prywatną.
* Komentować. Wprowadź komentarze umożliwiające łatwą identyfikację przeznaczenie tunelu
* Profil przepływu. Pozostaw wartość domyślną.
* Sygnatur. Przypisz interfejs do: routera wirtualnego: wybierz pozycję **domyślne**. 
        Strefa zabezpieczeń: Wybierz strefę dla ruchu zaufanej sieci LAN. W tym przykładzie nazwa strefy dla ruchu LAN to "Trust".
* Adresów. Kliknij przycisk **Dodaj** i Dodaj wszystkie nienakładające się adresy ip/32 w środowisku, które zostaną przypisane do podstawowego interfejsu tunelu i będą używane do monitorowania tuneli (wyjaśnione później).

Ponieważ ta konfiguracja jest dla sieci VPN o wysokiej dostępności, wymagane są dwa interfejsy tunelu: jeden podstawowy i jeden pomocniczy. Powtórz poprzednie kroki, aby utworzyć pomocniczy interfejs tunelu. Wybierz inny identyfikator tunelu i inny nieużywany adres IP/32.

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. Skonfiguruj trasy statyczne dla podsieci chmury prywatnej, aby można było uzyskać dostęp do sieci VPN typu lokacja-lokacja.

Trasy są niezbędne, aby można było połączyć się z podsieciami w chmurze prywatnej usługi CloudSimple.

Wybierz pozycję **Sieć**  >  **wirtualne routery**  >  *domyślne*  >  **trasy statyczne**  >  **Dodaj**, skonfiguruj następujące pola i kliknij przycisk **OK**.

* Nazwa. Wprowadź dowolną nazwę, aby ułatwić identyfikację przeznaczenie trasy.
* Punktu. Określ CloudSimple podsieci chmury prywatnej, które mają być osiągalne za pośrednictwem interfejsów tunelu S2S z lokalnego
* Interfejsu. Wybierz podstawowy interfejs tunelu utworzony w kroku-1 (sekcja-2) z listy rozwijanej. W tym przykładzie jest to Tunnel. 20.
* Następny przeskok. Wybierz pozycję **Brak**.
* Odległość administratora. Pozostaw wartość domyślną.
* Metryki. Wprowadź dowolną wartość z przewartości od 1 do 65535. Klucz polega na wprowadzeniu mniejszej metryki dla trasy odpowiadającej podstawowemu interfejsowi tunelu w porównaniu do trasy odpowiedniego tunelu pomocniczego, co oznacza, że poprzednia trasa była preferowana. Jeśli tunel. 20 ma wartość metryki 20 w przeciwieństwie do wartości metryki 30 dla tunelu. 30, preferowany jest tunel. 20.
* Tabela tras. Pozostaw wartość domyślną.
* Profil BFD. Pozostaw wartość domyślną.
* Monitorowanie ścieżki. Pozostaw niezaznaczone.

Powtórz poprzednie kroki, aby utworzyć kolejną trasę dla podsieci chmury prywatnej, która ma być używana jako trasa pomocnicza/zapasowa za pośrednictwem pomocniczego interfejsu tunelu. Tym razem wybierz inny identyfikator tunelu i wyższą metrykę niż dla trasy podstawowej.

### <a name="3-define-the-cryptographic-profile"></a>3. Zdefiniuj profil kryptograficzny

Zdefiniuj profil kryptograficzny określający protokoły i algorytmy do identyfikacji, uwierzytelniania i szyfrowania, które mają być używane do konfigurowania tuneli VPN w ramach protokołu IKEv1 fazy 1.

Wybierz kolejno pozycje **Sieć**  >  **Rozwiń Profile sieciowe**  >  **Dodaj Kryptografia IKE**  >  , skonfiguruj następujące pola i kliknij przycisk **OK**.

* Nazwa. Wprowadź dowolną nazwę profilu kryptografii IKE.
* Grupa DH. Kliknij przycisk **Dodaj** i wybierz odpowiednią grupę DH.
* Szyfrowanie. Kliknij przycisk **Dodaj** i wybierz odpowiednią metodę szyfrowania.
* Uwierzytelnianie. Kliknij przycisk **Dodaj** i wybierz odpowiednią metodę uwierzytelniania.
* Okres istnienia klucza. Pozostaw wartość domyślną.
* Uwierzytelnianie IKEv2 wiele. Pozostaw wartość domyślną.

### <a name="4-define-ike-gateways"></a>4. Definiowanie bram IKE

Zdefiniuj bramy IKE w celu nawiązania komunikacji między elementami równorzędnymi na każdym końcu tunelu sieci VPN.

Wybierz kolejno opcje **Sieć**  >  **Rozwiń sieć profile**  >  **usługi IKE bramy**  >  **Dodaj**, skonfiguruj następujące pola i kliknij przycisk **OK**.

Karta Ogólne:

* Nazwa. Wprowadź nazwę bramy IKE, z którą ma zostać nawiązana Komunikacja równorzędna z podstawowym elementem równorzędnym sieci VPN CloudSimple.
* Wersja. Wybierz **tryb tylko** do protokołu IKEv1.
* Typ adresu. Wybierz pozycję **IPv4**.
* Interfejsu. Wybierz interfejs publiczny lub zewnętrzny.
* Lokalny adres IP. Pozostaw wartość domyślną.
* Typ adresu IP elementu równorzędnego. Wybierz pozycję **adres IP**.
* Adres elementu równorzędnego. Wprowadź adres IP elementu równorzędnego podstawowego CloudSimple sieci VPN.
* Uwierzytelnianie. Wybierz **klucz wstępny**.
* Klucz wstępny/Potwierdź klucz wstępny. Wprowadź klucz wstępny, aby dopasować klucz bramy sieci VPN CloudSimple.
* Identyfikacja lokalna. Wprowadź publiczny adres IP lokalnej zapory Palo Alto.
* Tożsamość elementu równorzędnego. Wprowadź adres IP elementu równorzędnego podstawowego CloudSimple sieci VPN.

Karta Opcje zaawansowane:

* Włącz tryb pasywny. Pozostaw niezaznaczone.
* Włącz przechodzenie translatora adresów sieciowych. Pozostaw niezaznaczone, jeśli lokalna Zapora Palo Alto nie znajduje się za żadnym urządzeniem NAT. W przeciwnym razie zaznacz pole wyboru.

IKEv1

* Tryb wymiany. Wybierz pozycję **Main**.
* Profil kryptograficzny IKE. Wybierz utworzony wcześniej profil kryptografii IKE. Pozostaw zaznaczenie pola wyboru Włącz fragmentację.
* Wykrywanie utraconych elementów równorzędnych. Pozostaw pole niezaznaczone.

Powtórz poprzednie kroki, aby utworzyć pomocniczą bramę IKE.

### <a name="5-define-ipsec-crypto-profiles"></a>5. Zdefiniuj profile kryptograficzne protokołu IPSEC

Wybierz kolejno pozycje **Sieć**  >  **Rozwiń Profile sieciowe**  >  **IPSec Kryptografia**  >  **Dodaj**, skonfiguruj następujące pola i kliknij przycisk **OK**.

* Nazwa. Wprowadź nazwę dla profilu kryptograficznego protokołu IPsec.
* Protokół IPsec. Wybierz pozycję **ESP**.
* Szyfrowanie. Kliknij przycisk **Dodaj** i wybierz odpowiednią metodę szyfrowania.
* Uwierzytelnianie. Kliknij przycisk **Dodaj** i wybierz odpowiednią metodę uwierzytelniania.
* Grupa DH. Wybierz opcję **bez PFS**.
* Okres istnienia. Ustaw jako 30 minut.
* Włącz. Pozostaw pole niezaznaczone.

Powtórz poprzednie kroki, aby utworzyć inny profil kryptograficzny protokołu IPsec, który będzie używany jako element równorzędny CloudSimple sieci VPN. Ten sam profil kryptograficzny protokołu IPSEC może być również używany zarówno dla podstawowego, jak i pomocniczego tunelu IPsec (patrz Poniższa procedura).

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. Zdefiniuj profile monitora dla monitorowania tunelu

Wybierz pozycję **Sieć**  >  **rozwiń węzeł Profile sieci**  >    >  **Dodaj**, skonfiguruj następujące pola i kliknij przycisk **OK**.

* Nazwa. Wprowadź dowolną nazwę profilu monitora, która ma być używana do monitorowania tunelu w celu proaktywnej reakcji na awarię.
* Transakcji. Wybierz pozycję **tryb failover**.
* Dat. Wprowadź wartość **3**.
* Próg. Wprowadź wartość **7**.

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. Skonfiguruj podstawowe i pomocnicze tunele protokołu IPsec.

Wybierz opcję **sieciowe**  >  **tunele IPSec**  >  **Dodaj**, skonfiguruj następujące pola i kliknij przycisk **OK**.

Karta Ogólne:

* Nazwa. Wprowadź dowolną nazwę podstawowego tunelu IPSEC do komunikacji równorzędnej z podstawowym elementem równorzędnym sieci VPN CloudSimple.
* Interfejs tunelu. Wybierz podstawowy interfejs tunelu.
* Wprowadź. Pozostaw wartość domyślną.
* Typ adresu. Wybierz pozycję **IPv4**.
* Brama IKE. Wybierz podstawową bramę IKE.
* Profil kryptograficzny protokołu IPsec. Wybierz podstawowy profil IPsec. Wybierz pozycję **Pokaż opcje zaawansowane**.
* Włącz ochronę powtarzania. Pozostaw wartość domyślną.
* Kopiuj nagłówek OT. Pozostaw pole niezaznaczone.
* Monitor tunelu. Zaznacz pole wyboru.
* Docelowy adres IP. Wprowadź dowolny adres IP należący do podsieci prywatnej chmury CloudSimple, który jest dozwolony za pośrednictwem połączenia lokacja-lokacja. Upewnij się, że interfejsy tunelu (takie jak Tunnel. 20-10.64.5.2/32 i Tunnel. 30-10.64.6.2/32) w Palo Alto mogą dotrzeć do adresu IP chmury prywatnej CloudSimple za pośrednictwem sieci VPN typu lokacja-lokacja. Zapoznaj się z następującą konfiguracją identyfikatorów serwera proxy.
* Profilu. Wybierz profil monitora.

Karta identyfikatory serwera proxy: kliknij pozycję **IPv4**  >  **Dodaj** i skonfiguruj następujące elementy:

* Identyfikator serwera proxy. Wprowadź dowolną nazwę dla interesującego ruchu. W jednym tunelu IPsec przeprowadzono wiele identyfikatorów serwera proxy.
* LAN. Określ lokalnej podsieci lokalne, które mogą komunikować się z podsieciami chmury prywatnej za pośrednictwem sieci VPN typu lokacja-lokacja.
* Zdalnego. Określ zdalne podsieci chmury prywatnej, które mogą komunikować się z podsieciami lokalnymi.
* Protokol. Wybierz **dowolny**.

Powtórz poprzednie kroki, aby utworzyć inny tunel protokołu IPsec do użycia dla równorzędnego CloudSimple sieci VPN.

## <a name="references"></a>Dokumentacja

Konfigurowanie translatora adresów sieciowych w programie Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">Przewodnik konfigurowania serii Cisco ASA 5500</a>

Obsługiwane atrybuty protokołu IKEv1 i IKEv2 w Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">Przewodnik konfigurowania interfejsu wiersza polecenia serii Cisco ASA</a>

Konfigurowanie sieci VPN typu lokacja-lokacja protokołu IPsec w programie Cisco ASA z wersją 8,4 i nowszymi:

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">Skonfigurowanie tuneli protokołu IKEv1 IPsec lokacja-lokacja za pomocą ASDM lub interfejsu wiersza polecenia w ASA</a>

Konfigurowanie urządzeń z systemem Cisco adaptacyjne (ASAv) na platformie Azure:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">Przewodnik Szybki Start dotyczący urządzeń wirtualnych (ASAv) firmy Cisco z zabezpieczeniami adaptacyjnymi</a>

Konfigurowanie sieci VPN typu lokacja-lokacja przy użyciu identyfikatorów serwera proxy w programie Palo Alto:

[Konfigurowanie sieci VPN typu lokacja-lokacja](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

Konfigurowanie monitora tunelu:

[Skonfiguruj monitorowanie tunelu](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

Operacje związane z bramą IKE lub tunelu IPsec:

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">Włączenie/wyłączenie, odświeżenie lub ponowne uruchomienie bramy IKE lub tunelu IPsec</a>
