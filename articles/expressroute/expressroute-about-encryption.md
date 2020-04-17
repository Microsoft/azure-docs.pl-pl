---
title: 'Azure ExpressRoute: Informacje o szyfrowaniu'
description: Dowiedz się więcej o szyfrowaniu usługi ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: cherylmc
ms.openlocfilehash: d48f83df4e83fd1d1fe38eea76432e1b1c711b76
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461467"
---
# <a name="expressroute-encryption"></a>Szyfrowanie usługi ExpressRoute
 
Usługa ExpressRoute obsługuje kilka technologii szyfrowania, aby zapewnić poufność i integralność danych przesyłanych przez sieć między siecią a siecią firmy Microsoft.

## <a name="point-to-point-encryption-by-macsec-faq"></a>Szyfrowanie typu "punkt-punkt" przez macsec — często zadawane pytania
MACsec jest [standardem IEEE](https://1.ieee802.org/security/802-1ae/). Szyfruje dane na poziomie kontroli dostępu do multimediów (MAC) lub warstwy sieciowej 2. Za pomocą protokołu MACsec można szyfrować fizyczne łącza między urządzeniami sieciowymi a urządzeniami sieciowymi firmy Microsoft podczas łączenia się z firmą Microsoft za pośrednictwem [usługi ExpressRoute Direct.](expressroute-erdirect-about.md) MaCsec jest domyślnie wyłączona w portach Direct usługi ExpressRoute. Możesz przynieść własny klucz MACsec do szyfrowania i przechowywać go w [usłudze Azure Key Vault](../key-vault/general/overview.md). Ty decydujesz, kiedy obrócić klucz. Zobacz inne często zadawane pytania poniżej.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>Czy mogę włączyć macsec w obwodzie usługi ExpressRoute aprowizowanego przez dostawcę usługi ExpressRoute?
Nie. MACsec szyfruje cały ruch na fizycznym łączu z kluczem należącym do jednej jednostki (tj. klienta). W związku z tym jest dostępny tylko w użyczyenie usługi ExpressRoute Direct.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>Czy mogę zaszyfrować niektóre obwody usługi ExpressRoute na moich portach Direct usługi ExpressRoute i pozostawić inne obwody na tych samych portach w postaci niezaszyfrowanej? 
Nie. Po włączeniu MACsec cały ruch kontroli sieci, na przykład ruch danych BGP i ruch danych klienta są szyfrowane. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>Czy po włączeniu/wyłączeniu klucza MACsec lub aktualizacji klucza MACsec moja sieć lokalna utraci łączność z firmą Microsoft za pośrednictwem usługi ExpressRoute?
Tak. W przypadku konfiguracji MACsec obsługujemy tylko tryb klucza wstępnego. Oznacza to, że musisz zaktualizować klucz zarówno na swoich urządzeniach, jak i na microsoftie (za pośrednictwem naszego interfejsu API). Ta zmiana nie jest atomowa, więc utracisz łączność, gdy istnieje niezgodność klucza między obiema stronami. Zdecydowanie zaleca się zaplanowanie okna konserwacji dla zmiany konfiguracji. Aby zminimalizować przestoje, zalecamy zaktualizowanie konfiguracji jednego łącza usługi ExpressRoute Direct w czasie po przełączeniu ruchu sieciowego na inne łącze.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>Czy ruch będzie nadal płynąć, jeśli istnieje niezgodność w macsec klucz między moimi urządzeniami i microsoftu?
Nie. Jeśli macsec jest skonfigurowany i występuje niezgodność klucza, można utracić łączność z firmą Microsoft. Innymi słowy, nie wrócimy do niezaszyfrowanego połączenia, ujawniając twoje dane. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>Czy włączenie macsec w udzielono wydajności sieci Direct usługi ExpressRoute Direct?
Szyfrowanie i odszyfrowywanie MACsec odbywa się w sprzęcie na routerach, których używamy. Nie ma wpływu na wydajność po naszej stronie. Należy jednak skontaktować się z dostawcą sieci dla urządzeń, których używasz i sprawdzić, czy MACsec ma wpływ na wydajność.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>które mechanizmy szyfrowania są obsługiwane w przypadku szyfrowania?
Obsługujemy AES128 i AES256.

## <a name="end-to-end-encryption-by-ipsec-faq"></a>Szyfrowanie end-to-end przez IPsec — często zadawane pytania
Protokół IPsec jest [standardem IETF](https://tools.ietf.org/html/rfc6071). Szyfruje dane na poziomie protokołu internetowego (IP) lub warstwy sieciowej 3. Za pomocą protokołu IPsec można zaszyfrować połączenie end-to-end między siecią lokalną a siecią wirtualną (VNET) na platformie Azure. Zobacz inne często zadawane pytania poniżej.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>Czy oprócz protokołu MACsec można włączyć protokół IPsec na portach Direct usługi ExpressRoute?
Tak. MACsec zabezpiecza fizyczne połączenia między tobą a firmą Microsoft. Protokół IPsec zabezpiecza połączenie end-to-end między użytkownikiem a sieciami wirtualnymi na platformie Azure. Można je włączyć niezależnie. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>Czy mogę użyć bramy sieci VPN platformy Azure do skonfigurowania tunelu IPsec między moją siecią lokalną a moją siecią wirtualną platformy Azure?
Tak. Ten tunel protokołu IPsec można skonfigurować za pomocą systemu Microsoft Peering obwodu usługi ExpressRoute. Postępuj zgodnie z naszym [przewodnikiem konfiguracji](site-to-site-vpn-over-microsoft-peering.md).
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>Czy mogę użyć bramy sieci VPN platformy Azure do skonfigurowania tunelu IPsec za pośrednictwem prywatnej komunikacji równorzędnej platformy Azure?
Jeśli zastosujesz wirtualną sieć WAN platformy Azure, możesz wykonać [następujące kroki,](../virtual-wan/vpn-over-expressroute.md) aby zaszyfrować połączenie end-to-end. Jeśli masz regularne sieci wirtualnej platformy Azure można wdrożyć bramy sieci VPN innej firmy w sieci wirtualnej i ustanowić tunel IPsec między nim a lokalną bramą sieci VPN.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>Jaka jest przepustowość, jaką otrzymam po włączeniu protokołu IPsec w moim połączeniu Usługi ExpressRoute?
Jeśli używana jest brama sieci VPN platformy Azure, sprawdź [numery wydajności tutaj](../vpn-gateway/vpn-gateway-about-vpngateways.md). Jeśli używana jest brama sieci VPN innej firmy, skontaktuj się z dostawcą w celu uzyskania numerów wydajności.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat konfiguracji MACsec, zobacz [Konfigurowanie macsec.](expressroute-howto-macsec.md)

Aby uzyskać więcej informacji na temat konfiguracji protokołu IPsec, zobacz [Konfigurowanie protokołu IPsec.](site-to-site-vpn-over-microsoft-peering.md)
