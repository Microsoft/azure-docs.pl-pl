---
title: Praca zdalna przy użyciu usług sieciowych platformy Azure
description: Na tej stronie opisano, jak można korzystać z usług sieciowych platformy Azure, które są dostępne, aby umożliwić zdalne działanie i jak wyeliminować problemy dotyczące ruchu wynikające z zwiększonej liczby osób pracujących zdalnie.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: 835be5b867826d75732c9482743ad6a4ca2900e7
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98231846"
---
# <a name="working-remotely-using-azure-networking-services"></a>Praca zdalna przy użyciu usług sieciowych platformy Azure

>[!NOTE]
> W tym artykule opisano, jak korzystać z usług sieciowych platformy Azure, sieci firmy Microsoft i ekosystemu partnerów platformy Azure do pracy zdalnej i rozwiązywania problemów z siecią, które mogą się pojawić z powodu kryzysu COVID-19.

W tym artykule opisano opcje, które są dostępne dla organizacji w celu skonfigurowania dostępu zdalnego dla użytkowników lub uzupełniania istniejących rozwiązań o dodatkową pojemność w okresach szczytowego użycia. Architektom sieci podlegają następujące wyzwania:

- Zwiększ stopień wykorzystania sieci.
- Zapewnianie niezawodnej łączności z większą liczbą pracowników firmy i klientów.
- Zapewnianie łączności z lokalizacjami zdalnymi na całym świecie.

Nie wszystkie sieci (na przykład prywatne sieci WAN i korporacyjne przedsiębiorstwa) osiągają Przeciążenie z szczytowego ładowania zdalnego procesu roboczego. Wąskie gardła są często raportowane tylko w domowych sieciach szerokopasmowych i bramach sieci VPN lokalnych sieci firmowych.

Planisty sieci mogą pomóc w uproszczeniu wąskich gardeł i złagodzić Przeciążenie sieci, pamiętając, że różne typy ruchu wymagają różnych priorytetów traktowania sieci oraz niektórych inteligentnych przekierowań/dystrybucji obciążeń. Na przykład w czasie rzeczywistym ruch Medecine interakcji z pacjentem dla pacjentów ma duże znaczenie i opóźnienia/wahania. W związku z tym replikacja tego samego ruchu między magazynami nie jest uwzględniana z opóźnieniem. Poprzedni ruch musi być kierowany przez najbardziej optymalną ścieżkę sieciową o wyższej jakości usługi; możliwe jest kierowanie ruchu późniejszego za pośrednictwem nieoptymalnej trasy.



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>Udostępnianie naszych najlepszych rozwiązań — Sieć platformy Azure została zaprojektowana pod kątem elastyczności i wysokiej dostępności

Platforma Azure została zaprojektowana tak, aby wytrzymać nagłe zmiany w korzystaniu z zasobów i znacznie pomóc w okresach szczytowego użycia. Ponadto firma Microsoft utrzymuje i obsługuje jedną z największych sieci. Sieć firmy Microsoft została zaprojektowana w celu zapewnienia wysokiej dostępności, która może wytrzymać różne typy awarii: w przypadku awarii pojedynczego elementu sieciowego Wystąpił błąd w całym regionie.

Sieć firmy Microsoft została zaprojektowana tak, aby spełniała wymagania i zapewnić optymalną wydajność dla różnych typów ruchu sieciowego, w tym opóźnienia ruchu multimedialnego dla programów Skype i Teams, CDN, analizy danych Big Data, usługi Azure Storage, Bing i Xbox. Aby zapewnić optymalną wydajność dla różnych typów ruchu sieciowego, Sieć firmy Microsoft przyniesie cały ruch, który jest przeznaczony do przesyłania lub do tranzytu przez zasoby, jak najbliżej źródła ruchu.

>[!NOTE] 
>Korzystanie z funkcji sieciowych platformy Azure opisanych poniżej wykorzystuje zachowanie przyciągania nie sieci globalnej firmy Microsoft w celu zapewnienia lepszej obsługi sieci klienta. Zachowanie przyciągania nie sieci firmy Microsoft pozwala na wyłączenie ładowania ruchu jak najszybciej z pierwszych/ostatnich sieci, które mogą powodować Przeciążenie w okresach szczytowego użycia.
>

## <a name="enable-employees-to-work-remotely"></a>Umożliwienie pracownikom pracy zdalnej

Usługa Azure VPN Gateway obsługuje połączenia sieci VPN typu punkt-lokacja (P2S) i połączeń między lokacjami (S2S). Za pomocą bramy sieci VPN platformy Azure można skalować połączenia pracowników, aby bezpiecznie uzyskiwać dostęp do zasobów wdrożonych na platformie Azure i zasobów lokalnych. Aby uzyskać więcej informacji, zobacz [jak umożliwić użytkownikom zdalne korzystanie z](../vpn-gateway/work-remotely-support.md)programu. 

W przypadku korzystania z protokołu SSTP (Secure Socket Tunneling Protocol) liczba współbieżnych połączeń jest ograniczona do 128. Aby uzyskać większą liczbę połączeń, zalecamy przejście do OpenVPN lub IKEv2. Aby uzyskać więcej informacji, zobacz [Przechodzenie do protokołu OpenVPN lub IKEv2 z SSTP](../vpn-gateway/ikev2-openvpn-from-sstp.md
).

Aby uzyskać dostęp do zasobów wdrożonych na platformie Azure, deweloperzy zdalni mogą korzystać z rozwiązania Azure bastionu zamiast połączenia sieci VPN w celu uzyskania bezpiecznego dostępu do powłoki (RDP lub SSH) bez konieczności używania publicznych adresów IP na maszynach wirtualnych, do których uzyskiwany jest dostęp. Aby uzyskać więcej informacji, zobacz [Work zdalne using Azure bastionu](../bastion/work-remotely-support.md).

Do agregowania połączenia sieci VPN o dużej skali w celu obsługi wszelkich połączeń między zasobami w różnych lokalnych lokalizacjach globalnych, w różnych regionalnych centrach i sieciach wirtualnych, a w celu zoptymalizowania użycia wielu domowych sieci szerokopasmowych możesz użyć wirtualnej sieci WAN platformy Azure. Aby uzyskać więcej informacji, zobacz zoptymalizowaniem, aby dowiedzieć się, jak [korzystać z domu? Oto, gdzie usługa Azure Virtual WAN może pomóc](../virtual-wan/work-remotely-support.md).

Innym sposobem obsługi pracowników zdalnych jest wdrożenie infrastruktury pulpitu wirtualnego (VDI) hostowanej w sieci wirtualnej platformy Azure, zabezpieczonej za pomocą zapory platformy Azure. Na przykład system Windows Virtual Desktop (WVD) to usługa wirtualizacji pulpitu i aplikacji działająca na platformie Azure. Za pomocą pulpitu wirtualnego systemu Windows można skonfigurować skalowalne i elastyczne środowisko w ramach subskrypcji platformy Azure bez konieczności uruchamiania jakichkolwiek dodatkowych serwerów bramy. Użytkownik jest odpowiedzialny za WVD maszyn wirtualnych w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Obsługa zdalnej współpracy z zaporą platformy Azure](../firewall/remote-work-support.md). 

Platforma Azure ma również bogaty zestaw partnerów systemu w zakresie oznakowań. Nasi partnerzy sieci na platformie Azure mogą również skalować łączność sieci VPN. Aby uzyskać więcej informacji, zobacz [zagadnienia dotyczące sieciowego urządzenia wirtualnego (urządzenie WUS) dotyczące zdalnego działania](../vpn-gateway/nva-work-remotely-support.md).

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>Zwiększ połączenie pracowników, aby uzyskać dostęp do zasobów rozproszonych globalnie

Poniższe usługi platformy Azure mogą pomóc pracownikom w dostępie do zasobów rozproszonych globalnie. Zasoby mogą znajdować się w dowolnym z regionów świadczenia usługi Azure, sieci lokalnych, a nawet w innych chmurach publicznych lub prywatnych. 

- **Komunikacja równorzędna sieci wirtualnych platformy Azure**: w przypadku wdrażania zasobów w więcej niż jednym regionie platformy Azure i/lub w przypadku agregowania łączności zdalnie pracujących pracowników przy użyciu wielu sieci wirtualnych można nawiązać połączenie między wieloma sieciami wirtualnymi platformy Azure przy użyciu komunikacji równorzędnej sieci wirtualnych. Aby uzyskać więcej informacji, zobacz [wirtualne sieci równorzędne][VNet-peer].

- **Rozwiązanie oparte na sieci VPN platformy Azure**: dla pracowników zdalnych połączonych z platformą Azure za pośrednictwem usługi P2S lub sieci VPN S2S można włączyć dostęp do sieci lokalnych przez skonfigurowanie sieci VPN S2S między sieciami lokalnymi i bramą Azure VPN Gateway. Aby uzyskać więcej informacji, zobacz [Tworzenie połączenia typu lokacja-lokacja][S2S].

- **ExpressRoute**: korzystanie z prywatnej komunikacji równorzędnej ExpressRoute umożliwia włączenie prywatnej łączności między wdrożeniami platformy Azure a infrastrukturą lokalną lub infrastrukturą w ramach funkcji wspólnej lokalizacji. ExpressRoute za pośrednictwem komunikacji równorzędnej firmy Microsoft umożliwia również dostęp do publicznych punktów końcowych w firmie Microsoft z sieci lokalnej. Połączenia ExpressRoute nie odbywają się za pośrednictwem publicznego Internetu. Oferują one bezpieczną łączność, niezawodność, wyższą przepływność i krótsze i spójne opóźnienia niż typowe połączenia przez Internet. Aby uzyskać więcej informacji, zobacz [Omówienie usługi ExpressRoute][ExR]. Korzystanie z istniejącego dostawcy sieci, który jest już częścią naszego [ekosystemu partnera ExpressRoute][ExR-eco] , może pomóc skrócić czas uzyskiwania połączeń o dużej przepustowości do firmy Microsoft.  Korzystając z usługi [ExpressRoute Direct][ExR-D] , możesz bezpośrednio połączyć sieć lokalną ze szkieletem firmy Microsoft. ExpressRoute Direct oferuje dwie różne opcje stawki liniowej (10 GB/s lub 100 GB/s). 

- **Wirtualna sieć WAN platformy Azure**: usługa Azure Virtual WAN umożliwia bezproblemowe współdziałanie między połączeniami sieci VPN i obwodami usługi ExpressRoute. Jak wspomniano wcześniej, wirtualne sieci WAN obsługują również dowolne połączenia między zasobami w różnych lokalizacjach globalnych Premium w różnych regionalnych centrach i sieciach wirtualnych szprych

## <a name="scale-customer-connectivity-to-frontend-resources"></a>Skalowanie połączenia klientów z zasobami frontonu

Gdy większa liczba osób przejdzie w tryb online, wiele firmowych witryn sieci Web zwiększa ruch klientów. Usługa Azure Application Gateway może pomóc w zarządzaniu tym zwiększonym obciążeniem frontonu. Aby uzyskać więcej informacji, zobacz [Application Gateway obsługa dużej ilości ruchu](../application-gateway/high-traffic-support.md).

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Pomoc techniczna firmy Microsoft dla ruchu w chmurze

W przypadku wdrożeń w innych chmurach publicznych firma Microsoft może zapewnić łączność globalną. Z tego względu mogą pomóc wirtualne sieci WAN platformy Azure, VPN lub ExpressRoute. Aby zwiększyć łączność z platformy Azure do innych chmur, można skonfigurować sieci VPN S2S między dwiema chmurami. Możesz również nawiązać połączenie z platformy Azure z innymi chmurami publicznymi przy użyciu ExpressRoute. Chmura firmy Oracle jest częścią ekosystemu partnera ExpressRoute. Można [skonfigurować bezpośrednie połączenie między platformą Azure a infrastrukturą chmurową firmy Oracle][Az-OCI]. Większość dostawców usług, które są częścią ekosystemu partnera ExpressRoute, również oferuje prywatną łączność z innymi chmurami publicznymi. Korzystając z tych dostawców usług, można nawiązać połączenie prywatne między wdrożeniami na platformie Azure a innymi chmurami za pośrednictwem ExpressRoute.

## <a name="next-steps"></a>Następne kroki

W poniższych artykułach omówiono, jak można użyć różnych funkcji sieciowych platformy Azure do skalowania użytkowników zdalnie:

| **Artykuł** | **Opis** |
| --- | --- |
| [Jak umożliwić użytkownikom zdalne działanie](../vpn-gateway/work-remotely-support.md) | Przejrzyj dostępne opcje, aby skonfigurować dostęp zdalny dla użytkowników lub uzupełnić swoje istniejące rozwiązania dodatkową pojemnością dla organizacji.|
| [Zoptymalizowaniem chcesz korzystać z potrzeb domowych? Oto, gdzie usługa Azure Virtual WAN może pomóc](../virtual-wan/work-remotely-support.md) | Użyj wirtualnej sieci WAN platformy Azure, aby rozwiązać wymagania dotyczące łączności zdalnej w organizacji.|
| [Obsługa dużego natężenia ruchu usługi Application Gateway](../application-gateway/high-traffic-support.md) | Użyj Application Gateway z zaporą aplikacji sieci Web (WAF), aby uzyskać skalowalny i bezpieczny sposób zarządzania ruchem do aplikacji sieci Web. |
| [Zagadnienia dotyczące sieciowego urządzenia wirtualnego (urządzenie WUS) na potrzeby pracy zdalnej](../vpn-gateway/nva-work-remotely-support.md)|Zapoznaj się ze wskazówkami dotyczącymi korzystania z urządzeń WUS na platformie Azure w celu zapewnienia rozwiązań dostępu zdalnego. |
| [Przejście do protokołu OpenVPN lub IKEv2 z SSTP](../vpn-gateway/ikev2-openvpn-from-sstp.md) | Przezwyciężenie współbieżnego limitu połączeń 128 protokołu SSTP przez przejście do OpenVPN Protocol lub IKEv2.|
| [Praca zdalna przy użyciu usługi Azure bastionu](../bastion/work-remotely-support.md) | Zapewnianie bezpiecznego i bezproblemowej łączności protokołu RDP/SSH z maszynami wirtualnymi w sieci wirtualnej platformy Azure, bezpośrednio w Azure Portal bez użycia publicznego adresu IP. |
| [Tworzenie łączności hybrydowej do obsługi użytkowników zdalnych za pomocą usługi Azure ExpressRoute](../expressroute/work-remotely-support.md) | Użyj ExpressRoute na potrzeby łączności hybrydowej, aby umożliwić użytkownikom w organizacji zdalne działanie.|
| [Zdalna pomoc techniczna dla zapory platformy Azure](../firewall/remote-work-support.md)|Ochrona zasobów usługi Azure Virtual Network przy użyciu zapory platformy Azure. |

<!--Link References-->
[VNet-peer]: ../virtual-network/virtual-network-peering-overview.md
[S2S]: ../vpn-gateway/tutorial-site-to-site-portal.md
[ExR]: ../expressroute/expressroute-introduction.md
[ExR-eco]: ../expressroute/expressroute-locations.md
[ExR-D]: ../expressroute/expressroute-erdirect-about.md
[Az-OCI]: ../virtual-machines/workloads/oracle/configure-azure-oci-networking.md