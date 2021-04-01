---
title: 'Azure ExpressRoute: informacje o szyfrowaniu'
description: Dowiedz się więcej na temat szyfrowania ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: duau
ms.openlocfilehash: 693d2304324bdfcac298b3e20ddd0d882a16533c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92899868"
---
# <a name="expressroute-encryption"></a>Szyfrowanie ExpressRoute
 
ExpressRoute obsługuje kilka technologii szyfrowania, aby zapewnić poufność i integralność danych przechodzących między siecią i siecią firmy Microsoft.

## <a name="point-to-point-encryption-by-macsec-faq"></a>Szyfrowanie punkt-punkt przez MACsec — często zadawane pytania
MACsec jest [standardem IEEE](https://1.ieee802.org/security/802-1ae/). Szyfruje dane na poziomie kontroli dostępu do nośnika (MAC) lub 2. Można użyć MACsec do szyfrowania fizycznych linków między urządzeniami sieciowymi i urządzeniami sieciowymi firmy Microsoft w przypadku łączenia się z firmą Microsoft za pośrednictwem [ExpressRoute Direct](expressroute-erdirect-about.md). MACsec jest domyślnie wyłączona w portach bezpośrednie ExpressRoute. Możesz wprowadzić własny klucz MACsec na potrzeby szyfrowania i zapisać go w [Azure Key Vault](../key-vault/general/overview.md). Należy określić, kiedy należy obrócić klucz. Zobacz inne często zadawane pytania poniżej.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>Czy mogę włączyć MACsec w ramach obwodu ExpressRoute obsługiwanego przez dostawcę ExpressRoute?
Nie. MACsec szyfruje cały ruch na fizycznym łączu z kluczem należącym do jednej jednostki (np. Klient). W związku z tym jest dostępny tylko w ExpressRoute bezpośredniej.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>Czy mogę zaszyfrować niektóre obwody usługi ExpressRoute na portach bezpośrednich ExpressRoute i pozostawić inne obwody na tych samych portach, które nie są szyfrowane? 
Nie. Po włączeniu MACsec cały ruch związany z kontrolą sieci, na przykład ruch danych BGP i ruch danych klienta, są szyfrowane. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>Gdy włączam/wyłączam MACsec lub aktualizować klucz MACsec, Sieć lokalna utraci łączność z firmą Microsoft przez ExpressRoute?
Tak. W przypadku konfiguracji MACsec obsługuje tylko tryb klucza wstępnego. Oznacza to, że należy zaktualizować klucz na urządzeniach i w firmie Microsoft (za pośrednictwem naszego interfejsu API). Ta zmiana nie jest niepodzielna, więc utracisz łączność, gdy występuje niezgodność klucza między obiema stronami. Zdecydowanie zalecamy zaplanowanie okna obsługi dla zmiany konfiguracji. Aby zminimalizować czas przestoju, Zalecamy zaktualizowanie konfiguracji na jednym z linków ExpressRoute bezpośrednio po przełączeniu ruchu sieciowego do drugiego łącza.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>Czy ruch będzie nadal przepływać, jeśli wystąpi niezgodność klucza MACsec między urządzeniami i firmą Microsoft?
Nie. Jeśli MACsec jest skonfigurowany i występuje niezgodność kluczy, utracisz łączność z firmą Microsoft. Innymi słowy, nie powrócimy do nieszyfrowanego połączenia, ponieważ uwidaczniasz dane. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>Czy włączenie MACsec na ExpressRoute bezpośrednie obniża wydajność sieci?
MACsec szyfrowanie i odszyfrowywanie odbywa się na sprzęcie na routerach, z których korzystamy. Po naszej stronie nie ma wpływu na wydajność. Należy jednak skontaktować się z dostawcą sieci dla używanych urządzeń i sprawdzić, czy MACsec ma dowolną wydajność.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>Które mechanizmy szyfrowania są obsługiwane na potrzeby szyfrowania?
Obsługujemy tylko [rozszerzoną wersję numerowania pakietów](https://1.ieee802.org/security/802-1aebw/) aes-128 i AES-256. Ponadto należy wyłączyć [Identyfikator Secure Channel (Sci)](https://wikipedia.org/wiki/IEEE_802.1AE) w konfiguracji MACsec na urządzeniu. 

## <a name="end-to-end-encryption-by-ipsec-faq"></a>Kompleksowe szyfrowanie przy użyciu protokołu IPsec — często zadawane pytania
Protokół IPsec jest [standardem IETF](https://tools.ietf.org/html/rfc6071). Szyfruje dane na poziomie protokołu internetowego (IP) lub w warstwie sieciowej 3. Za pomocą protokołu IPsec można szyfrować kompleksowe połączenie między siecią lokalną a siecią wirtualną (VNET) na platformie Azure. Zobacz inne często zadawane pytania poniżej.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>Czy oprócz MACsec na portach Direct ExpressRoute można włączyć protokół IPsec?
Tak. MACsec zabezpiecza połączenia fizyczne między ty i firmą Microsoft. Protokół IPsec zabezpiecza połączenie między ty i sieciami wirtualnymi na platformie Azure. Można je niezależnie włączać. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>Czy można używać usługi Azure VPN Gateway do konfigurowania tunelu IPsec za pośrednictwem prywatnej komunikacji równorzędnej Azure?
Tak. Jeśli zostanie przyjęta wirtualna sieć WAN platformy Azure, możesz wykonać następujące [kroki](../virtual-wan/vpn-over-expressroute.md) , aby zaszyfrować kompleksowe połączenie. Jeśli używasz zwykłej sieci wirtualnej platformy Azure, możesz wykonać [następujące kroki](../vpn-gateway/site-to-site-vpn-private-peering.md) , aby ustanowić tunel IPSec między bramą sieci VPN platformy Azure i lokalną bramą sieci VPN.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>Jaka jest przepływność po włączeniu protokołu IPsec na moim połączeniu ExpressRoute?
Jeśli jest używana usługa Azure VPN Gateway, sprawdź [tutaj numery wydajności](../vpn-gateway/vpn-gateway-about-vpngateways.md). Jeśli jest używana Brama sieci VPN innej firmy, należy skontaktować się z dostawcą dla numerów wydajności.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat konfiguracji MACsec, zobacz [Configure MACsec](expressroute-howto-macsec.md) .

Aby uzyskać więcej informacji na temat konfiguracji protokołu IPsec, zobacz [Konfigurowanie protokołu IPSec](site-to-site-vpn-over-microsoft-peering.md) .
