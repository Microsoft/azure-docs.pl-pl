---
title: plik dołączany
description: plik dołączany
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 32e4658af48a0ae3bde08de18cf1d8204878d671
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91025013"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>Czy protokół BGP jest obsługiwany na wszystkich jednostkach SKU bramy sieci VPN platformy Azure?
Protokół BGP jest obsługiwany przez wszystkie jednostki SKU VPN Gateway platformy Azure z wyjątkiem podstawowej jednostki SKU.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Czy można użyć protokołu BGP z bramą sieci VPN platformy Azure opartej na zasadach?
Nie, protokół BGP jest obsługiwany tylko przez bramy sieci VPN opartych na trasach.

### <a name="what-asn-autonomous-system-numbers-can-i-use"></a>Z jakiego numeru ASN (autonomiczne numery systemu) mogę używać?
Możesz użyć własnego publicznego WPW lub prywatnego WPW dla sieci lokalnych i sieci wirtualnych platformy Azure, **z wyjątkiem** zakresów zarezerwowanych przez platformę Azure lub organizację IANA:

> [!IMPORTANT]
>
> Następujące numery ASN są zarezerwowane przez platformę Azure lub organizację IANA:
> * WPW zarezerwowane przez platformę Azure:
>    * Publiczne numery ASN: 8074, 8075, 12076
>    * Prywatne numery ASN: 65515, 65517, 65518, 65519, 65520
> * WPW [zarezerwowane przez organizację IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)
>    * 23456, 64496-64511, 65535–65551 i 429496729
>
> Nie można określić tych numerów ASN dla lokalnych urządzeń sieci VPN podczas nawiązywania połączenia z bramami sieci VPN platformy Azure.
>

### <a name="can-i-use-32-bit-4-byte-asns-autonomous-system-numbers"></a>Czy mogę użyć 32-bitowego (4-bajtowego) WPW (numery systemu autonomicznego)?
Tak. bramy sieci VPN platformy Azure obsługują teraz 32-bitowe (4-bajtowe) WPW. Użyj programu PowerShell/interfejsu wiersza polecenia/SDK, aby skonfigurować użycie numeru ASN w formacie dziesiętnym.

### <a name="what-private-asns-can-i-use"></a>Jakiego prywatnego WPW można użyć?
Użyteczny zakres prywatnych numerów ASN, których można użyć, to:

* 64512-65514, 65521-65534

Te WPW nie są zarezerwowane przez organizację IANA lub platformę Azure do użytku, dlatego mogą być używane do przypisywania do VPN Gateway platformy Azure.

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Jakiego adresu używa brama sieci VPN platformy Azure dla adresu IP elementu równorzędnego protokołu BGP?

* Domyślnie usługa Azure VPN Gateway przydzieli pojedynczy adres IP z zakresu GatewaySubnet w przypadku bram sieci VPN z aktywną gotowością lub dwóch adresów IP dla bram sieci VPN w usłudze Active-Active. Te adresy są przypisywane automatycznie podczas tworzenia bramy sieci VPN. Możesz uzyskać rzeczywiste adresy IP BGP przydzielone za pomocą programu PowerShell (Get-AzVirtualNetworkGateway, poszukaj właściwości "bgpPeeringAddress") lub w Azure Portal (w obszarze "Konfigurowanie protokołu BGP ASN" na stronie Konfiguracja bramy).

* Jeśli lokalne routery sieci VPN używają adresów IP **APIPA** (169.254. x. x) jako adresów IP protokołu BGP, należy określić dodatkowy **adres IP funkcji APIPA platformy Azure** w bramie sieci VPN platformy Azure. Usługa Azure VPN Gateway wybierze adres APIPA do użycia z lokalnym elementem równorzędnym protokołu BGP w trybie APIPA określonym w bramie sieci lokalnej lub prywatnym adresem IP dla lokalnego elementu równorzędnego protokołu BGP. Aby uzyskać więcej informacji, zobacz [Konfigurowanie protokołu BGP](../articles/vpn-gateway/bgp-howto.md).

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Jakie są wymagania dotyczące adresów IP elementów równorzędnych protokołu BGP na urządzeniu sieci VPN użytkownika?
Adres lokalnego elementu równorzędnego protokołu BGP **nie może** być taki sam jak publiczny adres IP urządzenia sieci VPN lub z przestrzeni adresów wirtualnej VPN Gateway. Na urządzeniu sieci VPN należy użyć innego adresu IP dla adresu IP elementu równorzędnego protokołu BGP. Może być adresem przypisanym do interfejsu sprzężenia zwrotnego na urządzeniu, czyli zwykłym adresem IP lub adresem APIPA. Jeśli urządzenie używa adresu APIPA dla protokołu BGP, należy określić adres IP BGP w bramie sieci VPN platformy Azure, zgodnie z opisem w temacie [Konfigurowanie protokołu BGP](../articles/vpn-gateway/bgp-howto.md). Ten adres należy określić w odpowiedniej bramie sieci lokalnej reprezentującej daną lokalizację.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>Co należy określić jako prefiksy adresów użytkownika dla bramy sieci lokalnej w przypadku korzystania z protokołu BGP?

> [!IMPORTANT]
>
>Jest to zmiana z wcześniej udokumentowanego wymagania. W przypadku korzystania z protokołu BGP dla połączenia należy pozostawić pole **przestrzeń adresowa** ***puste*** dla odpowiedniego zasobu bramy sieci lokalnej. Usługa Azure VPN Gateway będzie wewnętrznie dodawać trasę hosta do lokalnego adresu IP elementu równorzędnego BGP za pośrednictwem tunelu IPsec. **Nie** Dodawaj trasy/32 w polu przestrzeń adresowa. Jest on nadmiarowy i jeśli używasz adresu APIPA jako IP lokalnego protokołu BGP urządzenia sieci VPN, nie można go dodać do tego pola. Jeśli dodasz wszystkie inne prefiksy w polu przestrzeń adresowa, zostaną one dodane jako **trasy statyczne** w bramie sieci VPN platformy Azure, a także trasy uzyskane za pośrednictwem protokołu BGP.
>

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>Czy można użyć tego samego numeru ASN zarówno dla lokalnych sieci VPN, jak sieci wirtualnych platformy Azure?
Nie, należy przypisać różne numery ASN sieciom lokalnym i sieciom wirtualnym platformy Azure, jeśli są łączone za pomocą protokołu BGP. Bramy sieci VPN platformy Azure mają przypisany domyślny numer ASN 65515, niezależnie od tego, czy protokół BGP jest włączony dla łączności między różnymi lokalizacjami firmy. Można zastąpić to ustawienie domyślne, przypisując inny numer ASN podczas tworzenia bramy sieci VPN, lub zmienić numer ASN po utworzeniu bramy. Konieczne będzie przypisanie lokalnych numerów ASN do odpowiednich bram sieci lokalnej platformy Azure.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Jakie prefiksy adresów będą anonsowane do użytkownika przez bramy sieci VPN platformy Azure?
Brama sieci VPN będzie anonsować następujące trasy na urządzeniach lokalnych działających według protokołu BGP:

* Prefiksy adresów sieci wirtualnej użytkownika
* Prefiksy adresów dla każdej bramy sieci lokalnej podłączonej do bramy sieci VPN platformy Azure
* Trasy zostały uzyskane na podstawie innych sesji równorzędnych protokołu BGP podłączonych do bramy sieci VPN platformy Azure **z wyjątkiem trasy domyślnej lub tras nakładających się dla dowolnego prefiksu sieci wirtualnej**.

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Ile prefiksów można anonsować do usługi Azure VPN Gateway?
Obsługujemy do 4000 prefiksów. Sesja protokołu BGP zostanie przerwana, jeśli liczba prefiksów przekroczy limit.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Czy można anonsować trasę domyślną (0.0.0.0/0) do bram sieci VPN platformy Azure?
Tak.

Należy zauważyć, że spowoduje to wymuszenie całego ruchu wychodzącego z sieci wirtualnej do lokacji lokalnej i uniemożliwi maszynom wirtualnym sieci wirtualnej akceptowanie publicznej komunikacji z Internetu, takich jak RDP lub SSH z Internetu do maszyn wirtualnych.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Czy można anonsować takie same prefiksy jak prefiksy mojej sieci wirtualnej?

Nie, anonsowanie takich samych prefiksów jak prefiksy adresów Twojej sieci wirtualnej zostanie zablokowane lub odfiltrowane przez platformę Azure. Można jednak anonsować prefiks, który jest podzbiorem tego, co znajduje się wewnątrz sieci wirtualnej. 

Na przykład jeśli sieć wirtualna używała przestrzeni adresów 10.0.0.0/16, można anonsować przestrzeń 10.0.0.0/8. Nie można jednak anonsować przestrzeni 10.0.0.0/16 ani 10.0.0.0/24.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>Czy można użyć protokołu BGP do połączeń między sieciami wirtualnymi użytkownika?
Tak, protokołu BGP można użyć do połączeń zarówno między różnymi lokalizacjami, jak i połączeń między sieciami wirtualnymi.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Czy można mieszać połączenia BGP z połączeniami protokołów innych niż BGP dla bram sieci VPN użytkownika platformy Azure?
Tak, dla tej samej bramy sieci VPN platformy Azure można łączyć zarówno połączenia protokołu BGP, jak i połączenia innych protokołów.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Czy brama sieci VPN platformy Azure obsługuje routing tranzytowy protokołu BGP?
Tak, routing tranzytowy protokołu BGP jest obsługiwany, ale bramy sieci VPN platformy Azure **nie** anonsują tras domyślnych do innych elementów równorzędnych protokołu BGP. Aby włączyć routing tranzytowy dla wielu bram sieci VPN platformy Azure, należy włączyć protokół BGP dla wszystkich pośrednich połączeń między sieciami wirtualnymi. Aby uzyskać więcej informacji, zobacz [Informacje o](../articles/vpn-gateway/vpn-gateway-bgp-overview.md)protokole BGP.

### <a name="can-i-have-more-than-one-tunnel-between-azure-vpn-gateway-and-my-on-premises-network"></a>Czy można mieć więcej niż jeden tunel między bramą sieci VPN platformy Azure i lokalną siecią użytkownika?
Tak, można utworzyć więcej niż jeden tunel VPN S2S między bramą sieci VPN platformy Azure i siecią lokalną. Należy pamiętać, że wszystkie te tunele będą zliczane względem łącznej liczby tuneli dla bram sieci VPN platformy Azure i należy włączyć protokół BGP na obu tunelach.

Na przykład jeśli masz dwa nadmiarowe tunele między bramą sieci VPN platformy Azure i jedną z sieci lokalnych, będą zużywać 2 tunele z całkowitego przydziału dla bramy sieci VPN platformy Azure.

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>Czy można mieć wiele tuneli między dwiema sieciami wirtualnymi platformy Azure z protokołem BGP?
Tak, ale co najmniej jedna z bram sieci wirtualnej musi mieć konfigurację aktywne-aktywne.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-coexistence-configuration"></a>Czy można użyć protokołu BGP dla sieci VPN S2S w konfiguracji współistnienia sieci VPN ExpressRoute/S2S?
Tak. 

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>Co należy dodać do lokalnego urządzenia sieci VPN dla sesji połączenia równorzędnego protokołu BGP?
Na urządzeniu sieci VPN należy dodać trasę hosta dla adresu IP elementu równorzędnego protokołu BGP platformy Azure wskazującego tunel sieci VPN S2S protokołu IPsec. Na przykład jeśli adresem IP elementu równorzędnego sieci VPN platformy Azure jest „10.12.255.30”, należy dodać trasę hosta dla adresu „10.12.255.30” z interfejsem następnego skoku pasującym do interfejsu tunelu protokołu IPsec urządzenia sieci VPN użytkownika.
