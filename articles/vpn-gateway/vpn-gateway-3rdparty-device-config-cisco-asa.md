---
title: Przykładowa konfiguracja służąca do łączenia urządzeń Cisco ASA z bramami sieci VPN platformy Azure
description: Ten artykuł zawiera przykładową konfigurację służącą do łączenia urządzeń Cisco ASA z bramami sieci VPN platformy Azure.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: yushwang
ms.openlocfilehash: 82e00e0b519040c57fdfd798d8992086c61e5481
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89426182"
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Przykładowa konfiguracja: urządzenie Cisco ASA (IKEv2/No BGP)
Ten artykuł zawiera przykładowe konfiguracje do łączenia urządzeń Cisco adaptacyjnego urządzenia zabezpieczeń (ASA) z bramami sieci VPN platformy Azure. Przykład dotyczy urządzeń Cisco ASA z uruchomioną IKEv2 bez Border Gateway Protocol (BGP). 

## <a name="device-at-a-glance"></a>Szybkie urządzenie

* Dostawca urządzenia: **Cisco**
* Model urządzenia: **ASA**           
* Wersja docelowa: **8,4 i nowsza**
* Testowany model: **ASA 5505**
* Przetestowana wersja: **9,2**             
* Wersja protokołu IKE: **IKEv2**                  
* BGP: **nie**      
* Typ bramy sieci VPN platformy Azure: **Brama sieci VPN oparta na trasach**

> [!NOTE]
> Przykładowa konfiguracja łączy urządzenie Cisco ASA z bramą sieci VPN **opartą na trasach** platformy Azure. Połączenie używa niestandardowych zasad protokołu IPsec/IKE z opcją **UsePolicyBasedTrafficSelectors** , zgodnie z opisem w [tym artykule](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> Przykład wymaga, aby urządzenia ASA korzystały z zasad protokołu **IKEv2** z konfiguracjami opartymi na liście, nie opartymi na VTi. Sprawdź specyfikacje dostawcy urządzenia sieci VPN, aby sprawdzić, czy zasady protokołu IKEv2 są obsługiwane na lokalnych urządzeniach sieci VPN.


## <a name="vpn-device-requirements"></a>Wymagania dotyczące urządzeń sieci VPN
Bramy sieci VPN platformy Azure używają standardowych pakietów protokołu IPsec/IKE do ustanawiania tuneli VPN między lokacjami (S2S). Szczegółowe parametry protokołu IPsec/IKE i domyślne algorytmy kryptograficzne dla bram sieci VPN platformy Azure znajdują się w temacie [Informacje o urządzeniach sieci VPN](vpn-gateway-about-vpn-devices.md).

> [!NOTE]
> Opcjonalnie można określić dokładną kombinację algorytmów kryptograficznych i siły klucza dla określonego połączenia, zgodnie z opisem w [temacie Informacje o wymaganiach kryptograficznych](vpn-gateway-about-compliance-crypto.md). W przypadku określenia dokładnej kombinacji algorytmów i siły klucza należy pamiętać, aby użyć odpowiednich specyfikacji na urządzeniach sieci VPN.

## <a name="single-vpn-tunnel"></a>Pojedynczy tunel VPN
Ta konfiguracja składa się z pojedynczego tunelu sieci VPN S2S między bramą sieci VPN platformy Azure a lokalnym urządzeniem sieci VPN. Opcjonalnie można skonfigurować protokół BGP przez tunel VPN.

![Pojedynczy tunel VPN S2S](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Aby uzyskać instrukcje krok po kroku dotyczące kompilowania konfiguracji platformy Azure, zobacz [Konfiguracja pojedynczego tunelu sieci VPN](vpn-gateway-3rdparty-device-config-overview.md#singletunnel).

### <a name="virtual-network-and-vpn-gateway-information"></a>Informacje o sieci wirtualnej i bramie sieci VPN
Ta sekcja zawiera listę parametrów dla przykładu.

| **Parametr**                | **Wartość**                    |
| ---                          | ---                          |
| Prefiksy adresów sieci wirtualnej        | 10.11.0.0/16<br>10.12.0.0/16 |
| Adres IP bramy sieci VPN platformy Azure         | Azure_Gateway_Public_IP      |
| Prefiksy adresów lokalnych | 10.51.0.0/16<br>10.52.0.0/16 |
| Adres IP lokalnego urządzenia sieci VPN    | OnPrem_Device_Public_IP     |
| * Numer ASN protokołu BGP sieci wirtualnej                | 65010                        |
| * Adres IP elementu równorzędnego protokołu BGP platformy Azure           | 10.12.255.30                 |
| * Lokalne ASN protokołu BGP         | 65050                        |
| * Lokalny adres IP elementu równorzędnego BGP     | 10.52.255.254                |
|                              |                              |

\* Opcjonalny parametr tylko dla protokołu BGP.

### <a name="ipsecike-policy-and-parameters"></a>Parametry i zasady protokołu IPsec/IKE
W poniższej tabeli wymieniono Algorytmy IPsec/IKE i parametry, które są używane w przykładzie. Aby sprawdzić algorytmy obsługiwane przez modele urządzeń sieci VPN i wersje oprogramowania układowego, należy zapoznać się ze specyfikacją urządzenia sieci VPN.

| **IPsec/IKEv2**  | **Wartość**                            |
| ---              | ---                                  |
| Szyfrowanie IKEv2 | AES256                               |
| Integralność IKEv2  | SHA384                               |
| Grupa DH         | DHGroup24                            |
| * Szyfrowanie IPsec | AES256                               |
| * Integralność protokołu IPsec  | SHA1                                 |
| Grupa PFS        | PFS24                                |
| Okres istnienia skojarzeń zabezpieczeń QM   | 7 200 sekund                         |
| Selektor ruchu | UsePolicyBasedTrafficSelectors $True |
| Klucz wstępny   | PreSharedKey                         |
|                  |                                      |

\* Na niektórych urządzeniach integralność protokołu IPsec musi mieć wartość null, jeśli algorytm szyfrowania IPsec to AES-GCM.

### <a name="asa-device-support"></a>Obsługa urządzeń ASA

* Obsługa protokołu IKEv2 wymaga ASA w wersji 8,4 lub nowszej.

* Obsługa grup DH i grup PFS poza grupą 5 wymaga ASA w wersji 9. x.

* Obsługa szyfrowania IPsec za pomocą algorytmu AES-GCM i integralności IPsec z algorytmem SHA-256, SHA-384 lub SHA-512 wymaga ASA w wersji 9. x. To wymaganie dotyczące obsługi ma zastosowanie do nowszych urządzeń ASA. W momencie publikacji ASA modele 5505, 5510, 5520, 5540, 5550 i 5580 nie obsługują tych algorytmów. Aby sprawdzić algorytmy obsługiwane przez modele urządzeń sieci VPN i wersje oprogramowania układowego, należy zapoznać się ze specyfikacją urządzenia sieci VPN.


### <a name="sample-device-configuration"></a>Przykładowa konfiguracja urządzenia
Skrypt zawiera przykład, który jest oparty na konfiguracji i parametrach, które zostały opisane w poprzednich sekcjach. Konfiguracja tunelu sieci VPN S2S składa się z następujących części:

1. Interfejsy i trasy
2. Listy dostępu
3. Zasady i parametry IKE (faza 1 lub tryb główny)
4. Zasady i parametry protokołu IPsec (faza 2 lub tryb szybki)
5. Inne parametry, takie jak ograniczanie TCP

> [!IMPORTANT]
> Przed użyciem przykładowego skryptu wykonaj następujące czynności. Zastąp wartości symboli zastępczych w skrypcie ustawieniami urządzenia dla konfiguracji.

* Określ konfigurację interfejsu zarówno w interfejsie, jak i na zewnątrz.
* Zidentyfikuj trasy dla sieci firmowej/prywatnej i zewnętrznej/publicznej.
* Upewnij się, że wszystkie nazwy i numery zasad są unikatowe na urządzeniu.
* Upewnij się, że algorytmy kryptograficzne są obsługiwane na urządzeniu.
* Zastąp następujące **wartości symboli zastępczych** wartościami rzeczywistymi dla danej konfiguracji:
  - Zewnętrzna nazwa interfejsu: **poza**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - IKE: **Pre_Shared_Key**
  - Nazwy sieci wirtualnej i bramy sieci lokalnej: **VNetName** i **LNGName**
  - **Prefiksy** adresów sieci wirtualnych i lokalnych
  - Poprawne **maski** sieci

#### <a name="sample-script"></a>Przykładowy skrypt

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec proposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>Proste polecenia debugowania

Użyj następujących poleceń ASA do celów debugowania:

* Pokaż skojarzenie zabezpieczeń protokołu IPsec lub IKE:
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* Wprowadź tryb debugowania:
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    `debug`Polecenia mogą generować znaczące dane wyjściowe w konsoli programu.

* Pokaż bieżące konfiguracje na urządzeniu:
    ```
    show run
    ```
    Użyj `show` podpoleceń, aby wyświetlić listę określonych części konfiguracji urządzenia, na przykład:
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>Następne kroki
Aby skonfigurować aktywne-aktywne połączenia między lokalizacjami i połączeń między sieciami wirtualnymi, zobacz [Konfigurowanie bram VPN Gateway Active-Active](vpn-gateway-activeactive-rm-powershell.md).
