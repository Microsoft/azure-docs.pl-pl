---
title: Wirtualna sieć WAN platformy Azure i praca zdalna
description: Na tej stronie opisano, jak korzystać z wirtualnej sieci WAN platformy Azure, aby umożliwić pracę zdalną z powodu COVID-19 Pandemic.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: fc048f3da3156f5e17cfa32479b834b7320a60a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023498"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Wirtualna sieć WAN platformy Azure i obsługa zdalna

>[!NOTE]
>W tym artykule opisano sposób korzystania z usługi Azure Virtual Networks, platformy Azure, sieci firmy Microsoft i ekosystemu partnerów platformy Azure do zdalnego działania i rozwiązywania problemów z siecią, które są dostępne z powodu kryzysu COVID-19.
>

Czy są szyfrowane w celu zapewnienia łączności z użytkownikami zdalnymi?
Czy widzisz nieoczekiwaną konieczność obsługi większej liczby użytkowników niż planowano?
Czy użytkownik musi nawiązać połączenie z domu i nie tylko uzyskiwać dostęp do chmury, ale również mieć możliwość nawiązania połączenia lokalnego?
Czy chcesz, aby użytkownicy zdalni mieli dostęp do zasobów za prywatną siecią WAN?
Czy istnieje potrzeba, aby użytkownicy mieli dostęp do zasobów w chmurze bez konieczności konfigurowania łączności między regionami?
Ponieważ ta globalna Pandemic tworzy niespotykaną zmiany w naszym świecie, zespół sieci WAN platformy Azure jest tutaj przeznaczony do zaspokajania potrzeb związanych z łącznością.

Wirtualna sieć WAN platformy Azure to usługa sieciowa, która udostępnia wiele funkcji sieci, zabezpieczeń i routingu w celu zapewnienia pojedynczego interfejsu operacyjnego. Te funkcje obejmują łączność z gałęzią (za pośrednictwem automatyzacji łączności z urządzeń partnerskich sieci WAN, takich jak SD-WAN lub VPN CPE), łączności sieci VPN typu lokacja-lokacja, sieci VPN użytkowników zdalnych (połączeń punkt-lokacja), połączeń prywatnych (ExpressRoute), łączności z chmurą (łączności przechodniej dla sieci wirtualnych), sieci VPN ExpressRoute, routingu, zapory systemu Nie musisz mieć wszystkich tych przypadków użycia, aby rozpocząć korzystanie z wirtualnej sieci WAN. Możesz rozpocząć pracę z tylko jednym przypadkiem użycia i dostosować sieć w miarę rozwoju.

![Diagram usługi Virtual WAN](./media/virtual-wan-about/virtualwan1.png)

Teraz mówiąc o użytkownikach zdalnych, możesz sprawdzić, co jest potrzebne, aby zapewnić, że sieć zostanie uruchomiona:

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>Konfigurowanie łączności użytkowników zdalnych

Możesz połączyć się z zasobami na platformie Azure za pośrednictwem protokołu IPsec/IKE (IKEv2) lub połączenia OpenVPN. Ten typ połączenia wymaga skonfigurowania klienta sieci VPN dla użytkownika zdalnego. Ten klient może być klientem [sieci VPN platformy Azure](https://go.microsoft.com/fwlink/?linkid=2117554) lub klientem OpenVPN lub dowolnym klientem obsługującym protokół IKEv2. Aby uzyskać więcej informacji, zobacz [Tworzenie połączenia typu punkt-lokacja](virtual-wan-point-to-site-portal.md).

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>Łączność od użytkownika zdalnego do lokalnego

Dostępne są dwie opcje:

* Skonfiguruj połączenie lokacja-lokacja z dowolnym istniejącym urządzeniem sieci VPN. Po nawiązaniu połączenia między urządzeniem sieci VPN IPsec a usługą Azure Virtual WAN Hub połączenie między siecią VPN użytkownika punkt-lokacja (użytkownik zdalny) i siecią VPN typu lokacja-lokacja odbywa się automatycznie. Aby uzyskać więcej informacji na temat konfigurowania sieci VPN typu lokacja-lokacja z lokalnego urządzenia sieci VPN do wirtualnej sieci WAN platformy Azure, zobacz [Tworzenie połączenia typu lokacja-lokacja przy użyciu wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md).

* Połącz obwód ExpressRoute z koncentratorem wirtualnej sieci WAN. Połączenie obwodu ExpressRoute wymaga wdrożenia bramy ExpressRoute w wirtualnej sieci WAN. Po wdrożeniu jednej komunikacji między siecią VPN użytkownika punkt-lokacja a użytkownikiem ExpressRoute jest automatycznie. Aby utworzyć połączenie ExpressRoute, zobacz [Tworzenie połączenia usługi ExpressRoute przy użyciu wirtualnej sieci WAN](virtual-wan-expressroute-portal.md). Możesz użyć istniejącego obwodu usługi ExpressRoute, aby nawiązać połączenie z wirtualną siecią WAN platformy Azure.

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>Istniejący podstawowy wirtualny klient sieci WAN

Podstawowa wirtualna sieć WAN zapewnia tylko sieć VPN typu lokacja-lokacja. Aby użytkownicy zdalni mogli nawiązywać połączenia, należy uaktualnić wirtualną sieć WAN do standardowej wirtualnej sieci WAN. Aby uzyskać instrukcje dotyczące uaktualniania wirtualnej sieci WAN, zobacz [uaktualnianie wirtualnej sieci WAN z warstwy Podstawowa do standardowa](upgrade-virtual-wan.md)

## <a name="additional-information"></a><a name="other considerations"></a>Dodatkowe informacje

Wirtualna sieć WAN obsługuje jeden koncentrator na region/lokalizację. Informacje o lokalizacji znajdują się w artykule dotyczącym [partnerów i lokalizacji wirtualnych sieci WAN](virtual-wan-locations-partners.md) . Każde centrum obsługuje do 10 000 połączeń użytkowników zdalnych, połączenie z gałęzią 1 000, cztery obwody usługi ExpressRoute oraz do 500 połączeń Virtual Network. Podczas skalowania w górę użytkowników zdalnych, jeśli masz jakieś pytania, nie niechętnie zezwalają, aby uzyskać pomoc, wysyłając wiadomość e-mail na adres azurevirtualwan@microsoft.com . Jeśli wymagana jest pomoc techniczna, pamiętaj, aby otworzyć bilet pomocy technicznej z Azure Portal i uzyskać pomoc.

## <a name="faq"></a><a name="faq"></a>Często zadawane pytania

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [Omówienie usługi Virtual WAN](virtual-wan-about.md)