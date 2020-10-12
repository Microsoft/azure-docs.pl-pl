---
title: Przejście do OpenVPN lub IKEv2 z protokołu SSTP | VPN Gateway platformy Azure
description: Ten artykuł zawiera informacje na temat sposobów pokonania współbieżnego limitu połączeń protokołu SSTP przez 128.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: c7f71d24ab516044a0ce48ad40f78bc659268866
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91442178"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>Przejście do protokołu OpenVPN lub IKEv2 z SSTP

Połączenie bramy VPN Gateway typu punkt-lokacja pozwala utworzyć bezpieczne połączenie z siecią wirtualną z indywidualnego komputera klienckiego. Połączenie typu punkt-lokacja jest ustanawiane przez uruchomienie z komputera klienckiego. Ten artykuł ma zastosowanie do modelu wdrażania Menedżer zasobów i rozmowy o sposobach pokonania współbieżnego limitu połączeń (128) dla protokołu SSTP przez przejście do OpenVPN Protocol lub IKEv2.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Jakiego protokołu używa P2S?

Sieć VPN typu punkt-lokacja może korzystać z jednego z następujących protokołów:

* **OpenVPN &reg; Protokół**, protokół VPN oparty na protokole SSL/TLS. Rozwiązanie sieci VPN z protokołem SSL może przeniknąć zapory, ponieważ większość zapór otwiera port TCP 443 wychodzący, który jest używany przez protokół SSL. OpenVPN można użyć do nawiązania połączenia z urządzeń z systemami Android, iOS (wersja 11,0 i nowsze), Windows, Linux i Mac (OSX wersje 10,13 i nowsze).

* **Protokół SSTP (Secure Socket Tunneling Protocol)**— własny protokół sieci VPN oparty na protokole SSL. Rozwiązanie sieci VPN z protokołem SSL może przeniknąć zapory, ponieważ większość zapór otwiera port TCP 443 wychodzący, który jest używany przez protokół SSL. Protokół SSTP jest obsługiwany tylko na urządzeniach z systemem Windows. Platforma Azure obsługuje wszystkie wersje systemu Windows, które mają protokół SSTP (system Windows 7 i nowsze). Protokół **SSTP obsługuje maksymalnie 128 połączeń współbieżnych niezależnie od jednostki SKU bramy**.

* Sieć VPN z protokołem IKEv2 to oparte na standardach rozwiązanie sieci VPN korzystające z protokołu IPsec. Sieci VPN z protokołem IKEv2 można używać do łączenia z urządzeniami Mac (z systemem OSX 10.11 lub nowszym).


>[!NOTE]
>Protokoły IKEv2 i OpenVPN dla P2S są dostępne tylko dla modelu wdrażania Menedżer zasobów. Nie są one dostępne dla klasycznego modelu wdrażania. Jednostka SKU bramy podstawowej nie obsługuje protokołów IKEv2 i OpenVPN. W przypadku korzystania z podstawowej jednostki SKU należy usunąć i utworzyć ponownie produkcyjną jednostkę SKU Virtual Network bramy.
>

## <a name="migrating-from-sstp-to-ikev2-or-openvpn"></a>Migrowanie z protokołu SSTP do protokołu IKEv2 lub OpenVPN

Mogą wystąpić sytuacje, w których chcesz obsługiwać więcej niż 128 współbieżnych połączeń P2S z bramą sieci VPN, ale używają protokołu SSTP. W takim przypadku należy przejść do protokołu IKEv2 lub OpenVPN.

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>Opcja 1 — Dodawanie protokołu IKEv2 oprócz protokołu SSTP na bramie

Jest to najprostsza opcja. Protokoły SSTP i IKEv2 mogą współistnieć w tej samej bramie i zapewniają większą liczbę jednoczesnych połączeń. Można po prostu włączyć protokół IKEv2 w istniejącej bramie i ponownie pobrać klienta.

Dodawanie protokołu IKEv2 do istniejącej bramy sieci VPN SSTP nie wpłynie na istniejących klientów i można je skonfigurować do korzystania z protokołu IKEv2 w małych partiach lub po prostu skonfigurować nowych klientów do korzystania z protokołu IKEv2. Jeśli klient systemu Windows jest skonfigurowany dla protokołu SSTP i IKEv2, podejmie próbę nawiązania połączenia przy użyciu protokołu IKEV2, a jeśli to się nie powiedzie, nastąpi powrót do protokołu SSTP.

**Protokół IKEv2 używa niestandardowych portów UDP, aby upewnić się, że te porty nie są blokowane w zaporze użytkownika. Używane porty to UDP 500 i 4500.**

Aby dodać protokół IKEv2 do istniejącej bramy, po prostu przejdź do karty "Konfiguracja punktu do lokacji" w obszarze Brama Virtual Network w portalu, a następnie w polu listy rozwijanej wybierz pozycję **IKEv2 i SSTP (SSL)** .

![Zrzut ekranu przedstawiający stronę "Konfiguracja punktu do lokacji" z otwartym menu rozwijanym "typ tunelu" i "IKEv2 i SSTP (SSL)".](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>Opcja 2 — Usuwanie protokołu SSTP i włączanie OpenVPN na bramie

Ponieważ protokoły SSTP i OpenVPN są protokołem opartym na protokole TLS, nie mogą współistnieć w tej samej bramie. Jeśli zdecydujesz się na przejście z protokołu SSTP do OpenVPN, konieczne będzie wyłączenie protokołu SSTP i włączenie usługi OpenVPN na bramie. Ta operacja spowoduje utratę łączności z bramą sieci VPN przez istniejących klientów do momentu skonfigurowania nowego profilu na kliencie.

Jeśli wolisz, możesz włączyć OpenVPNę wraz z protokołem IKEv2. OpenVPN jest oparty na protokole TLS i używa standardowego portu TCP 443. Aby przełączyć się na OpenVPN, przejdź do karty "Konfiguracja punktu do lokacji" w obszarze Brama Virtual Network w portalu, a następnie wybierz pozycję **OpenVPN (SSL)** lub **IKEv2 i OpenVPN (SSL)** z listy rozwijanej.

![punkt-lokacja](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

Po skonfigurowaniu bramy istniejący klienci nie będą mogli nawiązywać połączeń do momentu [wdrożenia i skonfigurowania klientów OpenVPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn-clients).

Jeśli używasz systemu Windows 10, możesz również użyć [klienta sieci VPN platformy Azure dla systemu Windows](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-client#to-download-the-azure-vpn-client)


## <a name="frequently-asked-questions"></a>Często zadawane pytania
### <a name="what-are-the-client-configuration-requirements"></a>Jakie są wymagania dotyczące konfiguracji klienta?

>[!NOTE]
>W przypadku klientów z systemem Windows należy mieć uprawnienia administratora na urządzeniu klienckim w celu zainicjowania połączenia sieci VPN z urządzenia klienckiego do platformy Azure.
>

Użytkownicy korzystają z natywnych klientów sieci VPN na urządzeniach z systemem Windows i Mac w celu P2S. Platforma Azure udostępnia plik zip konfiguracji klienta sieci VPN, który zawiera ustawienia wymagane przez tych natywnych klientów do łączenia się z platformą Azure.

* W przypadku urządzeń z systemem Windows konfiguracja klienta sieci VPN obejmuje pakiet Instalatora instalowany przez użytkowników na ich urządzeniach.
* Urządzenia Mac obejmują plik MOBILECONFIG instalowany przez użytkowników na ich urządzeniach.

Plik zip zawiera również wartości niektórych ważnych ustawień po stronie platformy Azure, których można użyć do utworzenia własnego profilu dla tych urządzeń. Niektóre wartości obejmują adres bramy sieci VPN, skonfigurowane typy tuneli, trasy i certyfikat główny na potrzeby weryfikacji bramy.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Które jednostki SKU bramy obsługują sieć VPN P2S?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Zalecenia dotyczące jednostki SKU bramy znajdują się w temacie [Informacje o ustawieniach VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>Podstawowa jednostka SKU nie obsługuje uwierzytelniania za pomocą protokołu IKEv2 ani usługi RADIUS.
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Jakie zasady IKE/IPsec są skonfigurowane na bramach sieci VPN dla usługi P2S?


**IKEv2**

|**Odrzucono** | **Integralność** | **PRF** | **Grupa DH** |
|---        | ---            | ---        | ---     |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP256 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384        | SHA384    | GROUP_24 |
|AES256     |   SHA384        | SHA384    | GROUP_14 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_24 |
|AES256     |   SHA256        | SHA256    | GROUP_14 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_2 |

**IPsec**

|**Odrzucono** | **Integralność** | **Grupa PFS** |
|---        | ---            | ---        |
|GCM_AES256    | GCM_AES256 | GROUP_NONE |
|GCM_AES256    | GCM_AES256 | GROUP_24 |
|GCM_AES256    | GCM_AES256 | GROUP_14 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Jakie zasady protokołu TLS są skonfigurowane na bramach sieci VPN dla usługi P2S?
**Protokół**

|**Zasady** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Jak mogę skonfigurować połączenie P2S?

Konfiguracja P2S wymaga wykonania kilku konkretnych kroków. Poniższe artykuły zawierają instrukcje dotyczące konfiguracji P2S oraz linki do konfigurowania urządzeń klienckich sieci VPN:

* [Konfigurowanie połączenia P2S — uwierzytelnianie usługi RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurowanie połączenia P2S — natywne uwierzytelnianie certyfikatu platformy Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Konfigurowanie oprogramowania OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie połączenia P2S — uwierzytelnianie usługi RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurowanie połączenia P2S — natywne uwierzytelnianie certyfikatu platformy Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" jest znakiem towarowym OpenVPN Inc.**
