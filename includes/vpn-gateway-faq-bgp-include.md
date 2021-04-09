---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/22/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0eb509d543bc9b4a8846319126185eb1f27ec7a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104953460"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>Czy protokół BGP jest obsługiwany na wszystkich jednostkach SKU bramy sieci VPN platformy Azure?

Protokół BGP jest obsługiwany przez wszystkie jednostki SKU VPN Gateway platformy Azure z wyjątkiem podstawowej jednostki SKU.

### <a name="can-i-use-bgp-with-azure-policy-vpn-gateways"></a>Czy można użyć protokołu BGP z Azure Policy bramami sieci VPN?

Nie, protokół BGP jest obsługiwany tylko w przypadku bram sieci VPN opartych na trasach.

### <a name="what-asns-autonomous-system-numbers-can-i-use"></a>Z jakiego numeru WPW (autonomiczne numery systemu) mogę używać?

Możesz użyć własnego publicznego WPW lub prywatnego WPW dla sieci lokalnych i sieci wirtualnych platformy Azure. Nie można używać zakresów zarezerwowanych przez platformę Azure ani organizację IANA.

Następujące numery ASN są zarezerwowane przez platformę Azure lub organizację IANA:
* WPW zarezerwowane przez platformę Azure:

  * Publiczne numery ASN: 8074, 8075, 12076
  * Prywatne numery ASN: 65515, 65517, 65518, 65519, 65520
* WPW [zarezerwowane przez organizację IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml):

  * 23456, 64496-64511, 65535–65551 i 429496729

Nie można określić tych numerów ASN dla lokalnych urządzeń sieci VPN podczas łączenia się z bramami sieci VPN platformy Azure.

### <a name="can-i-use-32-bit-4-byte-asns"></a>Czy mogę użyć 32-bitowego (4-bajtowego) WPW?

Tak, VPN Gateway obsługuje teraz 32-bitowe (4-bajtowe) WPW. Aby skonfigurować przy użyciu numeru ASN w formacie dziesiętnym, użyj programu PowerShell, interfejsu wiersza polecenia platformy Azure lub zestawu Azure SDK.

### <a name="what-private-asns-can-i-use"></a>Jakiego prywatnego WPW można użyć?

Do użytecznych zakresów prywatnych numerów ASN należą:

* 64512-65514 i 65521-65534

Te WPW nie są zarezerwowane przez organizację IANA ani na korzystanie z platformy Azure, dlatego mogą być używane do przypisywania do bramy sieci VPN platformy Azure.

### <a name="what-address-does-vpn-gateway-use-for-bgp-peer-ip"></a>Jakiego adresu VPN Gateway używać dla adresu IP elementu równorzędnego BGP?

Domyślnie program VPN Gateway przydziela pojedynczy adres IP z zakresu *GatewaySubnet* w przypadku bram sieci VPN z aktywnymi Stanami gotowości lub dwóch adresów IP dla bram sieci VPN w usłudze Active-Active. Te adresy są przypisywane automatycznie podczas tworzenia bramy sieci VPN. Możesz uzyskać rzeczywisty adres IP BGP przydzielonego za pomocą programu PowerShell lub przez znalezienie go w Azure Portal. W programie PowerShell Użyj polecenia **Get-AzVirtualNetworkGateway** i Wyszukaj Właściwość **bgpPeeringAddress** . W Azure Portal na stronie **Konfiguracja bramy** poszukaj właściwości konfiguracja **BGP ASN** .

Jeśli lokalne routery sieci VPN używają adresów IP **APIPA** (169.254. x. x) jako adresów IP protokołu BGP, należy określić dodatkowy **adres IP funkcji APIPA platformy Azure** w bramie sieci VPN platformy Azure. Usługa Azure VPN Gateway wybiera adres APIPA do użycia z lokalnym elementem równorzędnym protokołu BGP w trybie APIPA określonym w bramie sieci lokalnej lub prywatnym adresem IP dla nieapipa, lokalnego elementu równorzędnego BGP. Aby uzyskać więcej informacji, zobacz [Konfigurowanie protokołu BGP](../articles/vpn-gateway/bgp-howto.md).

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Jakie są wymagania dotyczące adresów IP elementów równorzędnych BGP na moim urządzeniu sieci VPN?

Adres lokalnego elementu równorzędnego protokołu BGP nie może być taki sam jak publiczny adres IP urządzenia sieci VPN lub z przestrzeni adresowej sieci wirtualnej bramy sieci VPN. Użyj innego adresu IP na urządzeniu sieci VPN dla adresu IP elementu równorzędnego protokołu BGP. Może być adresem przypisanym do interfejsu sprzężenia zwrotnego na urządzeniu (zwykły adres IP lub adres APIPA). Jeśli urządzenie używa adresu APIPA dla protokołu BGP, należy określić adres IP BGP w bramie sieci VPN platformy Azure, zgodnie z opisem w temacie [Konfigurowanie protokołu BGP](../articles/vpn-gateway/bgp-howto.md). Określ ten adres w odpowiedniej bramie sieci lokalnej reprezentującej lokalizację.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>Co należy określić jako prefiksy adresów dla bramy sieci lokalnej, gdy używam protokołu BGP?

> [!IMPORTANT]
>
>Jest to zmiana z wcześniej udokumentowanego wymagania. Jeśli używasz protokołu BGP dla połączenia, pozostaw puste pole **przestrzeń adresowa** dla odpowiedniego zasobu bramy sieci lokalnej. Usługa Azure VPN Gateway dodaje trasę hosta wewnętrznie do lokalnego adresu IP elementu równorzędnego BGP za pośrednictwem tunelu IPsec. Nie dodawaj trasy/32 w polu **przestrzeń adresowa** . Jest on nadmiarowy i jeśli używasz adresu APIPA jako IP lokalnego protokołu BGP urządzenia sieci VPN, nie można go dodać do tego pola. Jeśli dodasz wszystkie inne prefiksy w polu **przestrzeń adresowa** , zostaną one dodane jako trasy statyczne w bramie sieci VPN platformy Azure, a także trasy uzyskane za pośrednictwem protokołu BGP.
>

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-virtual-networks"></a>Czy można używać tego samego numeru ASN dla lokalnych sieci VPN i sieci wirtualnych platformy Azure?

Nie, należy przypisać różne numery ASN między sieciami lokalnymi i sieciami wirtualnymi platformy Azure, Jeśli nawiązujesz połączenie ze sobą przy użyciu protokołu BGP. Bramy sieci VPN platformy Azure mają przypisany domyślny numer ASN 65515, niezależnie od tego, czy protokół BGP jest włączony, czy nie dla łączności między lokalizacjami. Można zastąpić to ustawienie domyślne, przypisując inny numer ASN podczas tworzenia bramy sieci VPN. można też zmienić numer ASN po utworzeniu bramy. Konieczne będzie przypisanie lokalnego WPW do odpowiednich bram sieci lokalnej platformy Azure.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Jakie prefiksy adresów będą anonsowane do użytkownika przez bramy sieci VPN platformy Azure?

Bramy anonsują następujące trasy na urządzeniach lokalnych protokołu BGP:

* Prefiksy adresów sieci wirtualnej.
* Prefiksy adresów dla każdej bramy sieci lokalnej połączonej z bramą Azure VPN Gateway.
* Trasy uzyskane z innych sesji komunikacji równorzędnej BGP połączonej z bramą sieci VPN platformy Azure, z wyjątkiem trasy domyślnej lub tras, które pokrywają się z dowolnym prefiksem sieci wirtualnej.

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Ile prefiksów można ogłaszać na platformie Azure VPN Gateway?

Usługa Azure VPN Gateway obsługuje do 4000 prefiksy. Sesja protokołu BGP zostanie przerwana, jeśli liczba prefiksów przekroczy limit.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Czy można anonsować trasę domyślną (0.0.0.0/0) do bram sieci VPN platformy Azure?

Tak. Należy zauważyć, że to wymusza cały ruch wychodzący z sieci wirtualnej do lokacji lokalnej. Zapobiega to również, że maszyny wirtualne sieci wirtualnej nie akceptują publicznej komunikacji bezpośrednio z Internetu, takiego protokołu RDP lub SSH z Internetu do maszyn wirtualnych.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Czy mogę anonsować dokładne prefiksy jako prefiksy sieci wirtualnej?

Nie, anonsowanie tych samych prefiksów co jeden z prefiksów adresów sieci wirtualnej zostanie zablokowane lub odfiltrowane przez platformę Azure. Można jednak anonsować prefiks, który jest nadzbiorem w sieci wirtualnej. 

Na przykład jeśli w sieci wirtualnej była używana przestrzeń adresowa 10.0.0.0/16, można anonsować 10.0.0.0/8. Nie można jednak anonsować 10.0.0.0/16 lub 10.0.0.0/24.

### <a name="can-i-use-bgp-with-my-connections-between-virtual-networks"></a>Czy mogę używać protokołu BGP z moimi połączeniami między sieciami wirtualnymi?

Tak, można użyć protokołu BGP dla połączeń między różnymi lokalizacjami i połączeń między sieciami wirtualnymi.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Czy można mieszać połączenia BGP z połączeniami protokołów innych niż BGP dla bram sieci VPN użytkownika platformy Azure?

Tak, dla tej samej bramy sieci VPN platformy Azure można łączyć zarówno połączenia protokołu BGP, jak i połączenia innych protokołów.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Czy usługa Azure VPN Gateway obsługuje routing przesyłania protokołu BGP?

Tak, routing przesyłania protokołu BGP jest obsługiwany, z wyjątkiem tego, że bramy sieci VPN platformy Azure nie anonsują tras domyślnych do innych elementów równorzędnych protokołu BGP. Aby włączyć routing tranzytowy między wieloma bramami sieci VPN platformy Azure, należy włączyć protokół BGP dla wszystkich pośrednich połączeń między sieciami wirtualnymi. Aby uzyskać więcej informacji, zobacz [Informacje o](../articles/vpn-gateway/vpn-gateway-bgp-overview.md)protokole BGP.

### <a name="can-i-have-more-than-one-tunnel-between-an-azure-vpn-gateway-and-my-on-premises-network"></a>Czy mogę mieć więcej niż jeden tunel między bramą sieci VPN platformy Azure a siecią lokalną?

Tak, można utworzyć więcej niż jeden tunel VPN typu lokacja-lokacja między bramą sieci VPN platformy Azure i siecią lokalną. Należy pamiętać, że wszystkie te tunele są zliczane względem łącznej liczby tuneli dla bram sieci VPN platformy Azure i należy włączyć protokół BGP na obu tunelach.

Na przykład jeśli masz dwa nadmiarowe tunele między bramą sieci VPN platformy Azure i jedną z sieci lokalnych, wykorzystuje 2 tunele z całkowitego przydziału dla bramy sieci VPN platformy Azure.

### <a name="can-i-have-multiple-tunnels-between-two-azure-virtual-networks-with-bgp"></a>Czy mogę mieć wiele tuneli między dwiema sieciami wirtualnymi platformy Azure z protokołem BGP?

Tak, ale co najmniej jedna z bram sieci wirtualnej musi mieć konfigurację aktywne-aktywne.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-azure-expressroute-and-s2s-vpn-coexistence-configuration"></a>Czy można użyć protokołu BGP dla sieci VPN S2S w konfiguracji współistnienia usługi Azure ExpressRoute i sieci VPN S2S?

Tak.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>Co należy dodać do lokalnego urządzenia sieci VPN dla sesji połączenia równorzędnego protokołu BGP?

Dodaj trasę hosta dla adresu IP elementu równorzędnego protokołu BGP platformy Azure na urządzeniu sieci VPN. Ta trasa wskazuje na tunel IPsec S2S VPN. Na przykład jeśli adres IP elementu równorzędnego usługi Azure VPN jest 10.12.255.30, należy dodać trasę hosta dla 10.12.255.30 z interfejsem następnego przeskoku pasującego do interfejsu tunelu IPsec na urządzeniu sieci VPN.

### <a name="does-the-virtual-network-gateway-support-bfd-for-s2s-connections-with-bgp"></a>Czy Brama sieci wirtualnej obsługuje BFD połączeń S2S z protokołem BGP?

Nie. Wykrywanie dwukierunkowego przekazywania (BFD) to protokół, którego można użyć w połączeniu z protokołem BGP w celu szybszego wykrywania przestojów w przypadku korzystania ze standardowego protokołu BGP "utrzymywanie aktywności". BFD używa podsekundowych czasomierzy zaprojektowanych do pracy w środowiskach sieci LAN, ale nie między publicznymi internetowymi lub rozległymi połączeniami sieciowymi.

W przypadku połączeń za pośrednictwem publicznej sieci Internet pewne pakiety opóźnione lub nawet porzucone nie są nietypowe, więc wprowadzenie tych agresywnych czasomierzy może zwiększyć stabilność. Niestabilność może spowodować, że trasy będą tłumione przez protokół BGP. Alternatywnie można skonfigurować urządzenie lokalne przy użyciu czasomierzy mniejszego niż domyślny, 60-sekundowy interwał utrzymywania aktywności i 180 sekund. Powoduje to szybszy czas zbieżności.

### <a name="do-azure-vpn-gateways-initiate-bgp-peering-sessions-or-connections"></a>Czy bramy sieci VPN platformy Azure inicjują sesje lub połączenia komunikacji równorzędnej BGP?

Brama będzie inicjować sesje komunikacji równorzędnej BGP z lokalnymi adresami IP elementów równorzędnych BGP określonych w zasobach bramy sieci lokalnej przy użyciu prywatnych adresów IP na bramach sieci VPN. Jest to niezależnie od tego, czy lokalne adresy IP protokołu BGP znajdują się w zakresie funkcji APIPA czy zwykłych prywatnych adresów IP. Jeśli lokalne urządzenia sieci VPN używają adresów APIPA jako adresu IP protokołu BGP, należy skonfigurować głośnik BGP do inicjowania połączeń.