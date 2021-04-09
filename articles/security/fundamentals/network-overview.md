---
title: Pojęcia i wymagania dotyczące zabezpieczeń sieci na platformie Azure | Microsoft Docs
description: Ten artykuł zawiera podstawowe informacje o podstawowych pojęciach dotyczących zabezpieczeń i wymaganiach dotyczących sieci oraz informacje o ofertach platformy Azure w każdym z tych obszarów.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: 4fb4981925593a22ed65df9d3ed0da5f45062f23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578929"
---
# <a name="azure-network-security-overview"></a>Omówienie zabezpieczeń sieci platformy Azure

Zabezpieczenia sieci można definiować jako proces ochrony zasobów przed nieautoryzowanym dostępem lub atakami przez zastosowanie kontroli do ruchu sieciowego. Celem jest zapewnienie, że dozwolony jest tylko wiarygodny ruch. Platforma Azure oferuje niezawodną infrastrukturę sieciową, która obsługuje wymagania dotyczące łączności aplikacji i usług. Możliwe jest połączenie sieciowe między zasobami znajdującymi się na platformie Azure, między zasobami lokalnymi i hostowanymi przez platformę Azure oraz z Internetu i platformy Azure.

W tym artykule omówiono niektóre opcje oferowane przez platformę Azure w dziedzinie zabezpieczeń sieci. Możesz uzyskać informacje na temat:

* Sieć platformy Azure
* Kontrola dostępu do sieci
* Azure Firewall
* Bezpieczny dostęp zdalny i łączność między różnymi lokalizacjami
* Dostępność
* Rozpoznawanie nazw
* Architektura sieci obwodowej (DMZ)
* Azure DDoS Protection
* Azure Front Door
* Usługa Traffic Manager
* Monitorowanie i wykrywanie zagrożeń

## <a name="azure-networking"></a>Sieć platformy Azure

Platforma Azure wymaga, aby maszyny wirtualne były połączone z usługą Virtual Network platformy Azure. Sieć wirtualna to konstrukcja logiczna oparta na fizycznej sieci szkieletowej platformy Azure. Każda sieć wirtualna jest odizolowana od wszystkich innych sieci wirtualnych. Pozwala to zagwarantować, że ruch sieciowy we wdrożeniach nie będzie dostępny dla innych klientów platformy Azure.

Więcej informacji:

* [Omówienie sieci wirtualnej](../../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Kontrola dostępu do sieci

Kontrola dostępu do sieci to czynność ograniczająca łączność do i z określonych urządzeń lub podsieci w obrębie sieci wirtualnej. Celem kontroli dostępu do sieci jest ograniczenie dostępu do maszyn wirtualnych i usług do zatwierdzonych użytkowników i urządzeń. Kontrola dostępu jest oparta na decyzjach o zezwalaniu lub odmowie połączeń z i z maszyny wirtualnej lub usługi.

Platforma Azure obsługuje kilka typów kontroli dostępu do sieci, takich jak:

* Kontrolka warstwy sieciowej
* Kontrola trasy i Wymuszone tunelowanie
* Urządzenia zabezpieczeń sieci wirtualnej

### <a name="network-layer-control"></a>Kontrolka warstwy sieciowej

Każde bezpieczne wdrożenie wymaga pewnej miary kontroli dostępu do sieci. Celem kontroli dostępu do sieci jest ograniczenie komunikacji maszyny wirtualnej z niezbędnymi systemami. Inne próby komunikacji są blokowane.

> [!NOTE]
> Zapory magazynu są opisane w artykule [Przegląd zabezpieczeń usługi Azure Storage](../../storage/blobs/security-recommendations.md)

#### <a name="network-security-rules-nsgs"></a>Reguły zabezpieczeń sieci (sieciowych grup zabezpieczeń)

Jeśli potrzebujesz podstawowej kontroli dostępu na poziomie sieci (na podstawie adresu IP i protokołów TCP lub UDP), możesz użyć sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń). SIECIOWEJ grupy zabezpieczeń jest podstawową, stanową zaporą filtrowania pakietów i pozwala na kontrolowanie dostępu na podstawie [5-spójnej kolekcji](https://www.techopedia.com/definition/28190/5-tuple). Sieciowych grup zabezpieczeń obejmują funkcje upraszczające zarządzanie i zmniejszają ryzyko błędów konfiguracji:

* **Rozszerzone reguły zabezpieczeń** upraszczają definicję reguły sieciowej grupy zabezpieczeń i umożliwiają tworzenie złożonych reguł zamiast tworzenia wielu prostych reguł, aby osiągnąć ten sam wynik.
* **Tagi usług** to utworzone przez firmę Microsoft etykiety, które reprezentują grupę adresów IP. Są one aktualizowane dynamicznie w celu uwzględnienia zakresów adresów IP, które spełniają warunki, które definiują uwzględnienie w etykiecie. Na przykład jeśli chcesz utworzyć regułę, która ma zastosowanie do wszystkich magazynów Azure w regionie wschodnim, możesz użyć magazynu. wschód
* **Grupy zabezpieczeń aplikacji** umożliwiają wdrażanie zasobów w grupach aplikacji i kontrolowanie dostępu do tych zasobów przez tworzenie reguł, które używają tych grup aplikacji. Jeśli na przykład masz wdrożone serwery WebServer w grupie aplikacji "webservers", możesz utworzyć regułę, która stosuje sieciowej grupy zabezpieczeń 443 zezwalające na ruch z Internetu do wszystkich systemów w grupie aplikacji "webservers".

Sieciowych grup zabezpieczeń nie zapewniają kontroli warstwy aplikacji ani kontroli dostępu uwierzytelnionego.

Więcej informacji:

* [Grupy zabezpieczeń sieci](../../virtual-network/network-security-groups-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>Dostęp just in Time do maszyny wirtualnej

[Usługa Azure Security Center](../../security-center/security-center-introduction.md) może zarządzać sieciowych grup zabezpieczeń na maszynach wirtualnych i blokować dostęp do maszyny wirtualnej, dopóki użytkownik z odpowiednią rolą kontroli dostępu opartej na [rolach platformy Azure](../../role-based-access-control/overview.md) zażąda dostępu. Gdy użytkownik zostanie pomyślnie autoryzowany, wprowadza modyfikacje sieciowych grup zabezpieczeń, aby zezwolić na dostęp do wybranych portów przez określony czas. Po upływie tego czasu sieciowych grup zabezpieczeń są przywracane do poprzedniego stanu bezpiecznego.

Więcej informacji:

* [Azure Security Center dostęp just in Time](../../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Punkty końcowe usługi

Punkty końcowe usługi to inny sposób zastosowania kontroli nad ruchem. Możesz ograniczyć komunikację z obsługiwanymi usługami do sieci wirtualnych za pośrednictwem bezpośredniego połączenia. Ruch z sieci wirtualnej do określonej usługi platformy Azure pozostaje w sieci szkieletowej Microsoft Azure.  

Więcej informacji:

* [Punkty końcowe usługi](../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Kontrola trasy i Wymuszone tunelowanie

Możliwość sterowania zachowaniem routingu w sieciach wirtualnych jest krytyczna. Jeśli Routing jest niepoprawnie skonfigurowany, aplikacje i usługi hostowane na maszynie wirtualnej mogą łączyć się z nieautoryzowanymi urządzeniami, w tym systemami należącymi do firmy i obsługiwanymi przez potencjalni atakujący.

Usługa Azure Networks obsługuje możliwość dostosowywania zachowania routingu dla ruchu sieciowego w sieciach wirtualnych. Dzięki temu można zmienić domyślne wpisy tabeli routingu w sieci wirtualnej. Kontrola zachowania routingu pomaga upewnić się, że cały ruch z określonego urządzenia lub grupy urządzeń wejdzie w sieć wirtualną lub opuści ją za pośrednictwem określonej lokalizacji.

Na przykład możesz mieć urządzenie zabezpieczeń sieci wirtualnej w sieci wirtualnej. Należy upewnić się, że cały ruch do i z sieci wirtualnej przechodzi przez to wirtualne urządzenie zabezpieczające. Można to zrobić przez skonfigurowanie [tras zdefiniowanych przez użytkownika](../../virtual-network/virtual-networks-udr-overview.md) (UDR) na platformie Azure.

[Wymuszone tunelowanie](https://www.petri.com/azure-forced-tunneling) jest mechanizmem, którego można użyć, aby upewnić się, że usługi nie mogą inicjować połączenia z urządzeniami w Internecie. Należy zauważyć, że różni się to od akceptowania połączeń przychodzących, a następnie odpowiadania na nie. Serwery frontonu sieci Web muszą odpowiadać na żądania z hostów internetowych, a więc ruch internetowy jest dozwolony dla ruchu przychodzącego do tych serwerów sieci Web, a serwery sieci Web mogą reagować.

Na serwerze frontonu sieci Web można zainicjować żądanie wychodzące. Takie żądania mogą stanowić zagrożenie bezpieczeństwa, ponieważ te połączenia mogą służyć do pobierania złośliwego oprogramowania. Nawet jeśli chcesz, aby te serwery frontonu mogły inicjować żądania wychodzące do Internetu, możesz wymusić przechodzenie między lokalnymi serwerami proxy sieci Web. Dzięki temu można korzystać z funkcji filtrowania i rejestrowania adresów URL.

Zamiast tego należy użyć tunelowania wymuszonego, aby temu zapobiec. Po włączeniu wymuszonego tunelowania wszystkie połączenia z Internetem są wymuszane przez bramę lokalną. Wymuszone tunelowanie można skonfigurować przez skorzystanie z UDR.

Więcej informacji:

* [Co to są trasy zdefiniowane przez użytkownika i przekazywanie adresów IP](../../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Urządzenia zabezpieczeń sieci wirtualnej

Podczas gdy sieciowych grup zabezpieczeń, UDR i Wymuszone tunelowanie zapewniają poziom zabezpieczeń w warstwach sieci i transportu [modelu osi](https://en.wikipedia.org/wiki/OSI_model), można również włączyć zabezpieczenia na poziomach wyższych niż sieć.

Na przykład wymagania dotyczące zabezpieczeń mogą obejmować:

* Uwierzytelnianie i autoryzacja przed zezwoleniem na dostęp do aplikacji
* Wykrywanie włamań i reagowanie intruzów
* Inspekcja warstwy aplikacji dla protokołów wysokiego poziomu
* Filtrowanie adresów URL
* Oprogramowanie antywirusowe i chroniące przed złośliwym kodem
* Ochrona przed bot
* Kontrola dostępu do aplikacji
* Dodatkowa ochrona DDoS (powyżej ochrony DDoS zapewnianej przez samą sieć szkieletową platformy Azure)

Dostęp do tych zaawansowanych funkcji zabezpieczeń sieci można uzyskać przy użyciu rozwiązania partnerskiego platformy Azure. Najbardziej aktualne rozwiązania zabezpieczeń sieci partnerów platformy Azure można znaleźć, odwiedzając [witrynę Azure Marketplace](https://azure.microsoft.com/marketplace/), a następnie wyszukując frazę "zabezpieczenia" i "zabezpieczenia sieci".

## <a name="azure-firewall"></a>Azure Firewall

Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Jest to w pełni stanowa zapora oferowana jako usługa z wbudowaną wysoką dostępnością i możliwością nieograniczonego skalowania w chmurze. Dostępne są następujące funkcje:

* Wysoka dostępność
* Skalowalność chmury
* Reguły filtrowania w pełni kwalifikowanych nazw domen aplikacji
* Reguły filtrowania ruchu sieciowego

Więcej informacji:

* [Omówienie Zapory platformy Azure](../../firewall/overview.md)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Bezpieczny dostęp zdalny i łączność między różnymi lokalizacjami

Konfiguracja, konfiguracja i zarządzanie zasobami platformy Azure należy wykonać zdalnie. Ponadto możesz chcieć wdrożyć [hybrydowe rozwiązania IT](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) , które mają składniki lokalne i w chmurze publicznej platformy Azure. Te scenariusze wymagają bezpiecznego dostępu zdalnego.

Sieć Azure obsługuje następujące scenariusze bezpiecznego dostępu zdalnego:

* Łączenie poszczególnych stacji roboczych z siecią wirtualną
* Łączenie sieci lokalnej z siecią wirtualną przy użyciu sieci VPN
* Łączenie sieci lokalnej z siecią wirtualną za pomocą dedykowanego linku sieci WAN
* Łączenie sieci wirtualnych ze sobą

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Łączenie poszczególnych stacji roboczych z siecią wirtualną

Możesz chcieć umożliwić indywidualnym deweloperom lub pracownikom operacji zarządzanie maszynami wirtualnymi i usługami na platformie Azure. Załóżmy na przykład, że potrzebujesz dostępu do maszyny wirtualnej w sieci wirtualnej. Jednak zasady zabezpieczeń nie zezwalają na dostęp zdalny do poszczególnych maszyn wirtualnych za pośrednictwem protokołu RDP lub SSH. W takim przypadku można użyć połączenia [sieci VPN typu punkt-lokacja](../../vpn-gateway/point-to-site-about.md) .

Połączenie sieci VPN typu punkt-lokacja umożliwia skonfigurowanie prywatnego i bezpiecznego połączenia między użytkownikiem i siecią wirtualną. Po nawiązaniu połączenia sieci VPN użytkownik może łączyć się za pośrednictwem protokołu RDP lub SSH przez sieć VPN z dowolną maszyną wirtualną w sieci wirtualnej. (Przyjęto założenie, że użytkownik może uwierzytelniać i jest autoryzowany). Sieć VPN typu punkt-lokacja obsługuje:

* Protokół SSTP (Secure Socket Tunneling Protocol) — własny protokół sieci VPN oparty na protokole SSL. Rozwiązanie SSL sieci VPN może przeniknąć zapory, ponieważ większość zapór otwiera port TCP 443, który jest używany przez protokół TLS/SSL. Protokół SSTP jest obsługiwany tylko na urządzeniach z systemem Windows. Platforma Azure obsługuje wszystkie wersje systemu Windows, które mają protokół SSTP (system Windows 7 i nowsze).

* Sieć VPN z protokołem IKEv2 to oparte na standardach rozwiązanie sieci VPN korzystające z protokołu IPsec. Sieci VPN z protokołem IKEv2 można używać do łączenia z urządzeniami Mac (z systemem OSX 10.11 lub nowszym).

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

Więcej informacji:

* [Konfigurowanie połączenia typu punkt-lokacja z siecią wirtualną przy użyciu programu PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Łączenie sieci lokalnej z siecią wirtualną przy użyciu sieci VPN

Możesz chcieć połączyć całą sieć firmową lub jej fragmenty z siecią wirtualną. Jest to typowe scenariusze hybrydowe IT, w przypadku których organizacje [zwiększają swoje lokalne centrum danych na platformę Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). W wielu przypadkach organizacje obsługujące części usługi na platformie Azure i lokalnie. Mogą to być na przykład, gdy rozwiązanie obejmuje serwery frontonu sieci Web na platformie Azure i bazy danych zaplecza lokalnie. Te typy "połączeń obejmujących wiele lokalizacji" umożliwiają również bezpieczniejsze zarządzanie zasobami platformy Azure i umożliwiają wykonywanie takich scenariuszy jak rozszerzanie Active Directory kontrolerów domeny na platformę Azure.

Jednym ze sposobów osiągnięcia tego celu jest użycie [sieci VPN typu lokacja-lokacja](https://www.techopedia.com/definition/30747/site-to-site-vpn). Różnica między siecią VPN typu lokacja-lokacja a siecią VPN typu punkt-lokacja polega na tym, że drugie łączy pojedyncze urządzenie z siecią wirtualną. Sieć VPN typu lokacja-lokacja łączy całą sieć (na przykład sieć lokalną) z siecią wirtualną. Połączenia sieci VPN typu lokacja-lokacja z siecią wirtualną używają protokołu VPN trybu tunelowania wysoce bezpiecznego protokołu IPsec.

Więcej informacji:

* [Tworzenie Menedżer zasobów sieci wirtualnej z połączeniem sieci VPN typu lokacja-lokacja przy użyciu Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md)
* [VPN Gateway — informacje](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Łączenie sieci lokalnej z siecią wirtualną za pomocą dedykowanego linku sieci WAN

Połączenia sieci VPN typu punkt-lokacja i lokacja-lokacja są skuteczne do włączenia łączności między lokalizacjami. Jednak w niektórych organizacjach należy wziąć pod uwagę następujące wady:

* Połączenia sieci VPN przenosiją dane za pośrednictwem Internetu. Dzięki temu można nawiązać te połączenia z potencjalnymi problemami z zabezpieczeniami związanymi z przeniesieniem danych za pośrednictwem sieci publicznej. Ponadto nie można zagwarantować niezawodności i dostępności dla połączeń internetowych.
* Połączenia sieci VPN z sieciami wirtualnymi mogą nie mieć przepustowości dla niektórych aplikacji i zastosowań, ponieważ maksymalne wartości przekraczają 200 MB/s.

Organizacje, które potrzebują najwyższego poziomu zabezpieczeń i dostępności dla swoich połączeń obejmujących wiele lokalizacji, zazwyczaj używają dedykowanych łączy sieci WAN do łączenia się z lokacjami zdalnymi. Platforma Azure umożliwia korzystanie z dedykowanego linku WAN, który służy do łączenia sieci lokalnej z siecią wirtualną. W przypadku usług Azure ExpressRoute, Express Route Direct i Global Route.

Więcej informacji:

* [ExpressRoute — opis techniczny](../../expressroute/expressroute-introduction.md)
* [ExpressRoute bezpośrednie](../../expressroute/expressroute-erdirect-about.md)
* [Globalny zasięg trasy Express Route](../../expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Łączenie sieci wirtualnych ze sobą

Istnieje możliwość użycia wielu sieci wirtualnych we wdrożeniach. Istnieje kilka powodów, dla których możesz to zrobić. Może być konieczne Uproszczenie zarządzania lub zwiększenie bezpieczeństwa. Niezależnie od motywacji do umieszczania zasobów w różnych sieciach wirtualnych mogą wystąpić sytuacje, w których zasoby w poszczególnych sieciach mają być połączone ze sobą.

Jedną z opcji jest usługa w jednej sieci wirtualnej do łączenia się z usługami w innej sieci wirtualnej przez Internet. Połączenie zostanie uruchomione w jednej sieci wirtualnej, przejdzie przez Internet, a następnie powróci do docelowej sieci wirtualnej. Ta opcja udostępnia połączenie z problemami z zabezpieczeniami związanymi z komunikacją internetową.

Lepszym rozwiązaniem może być utworzenie sieci VPN typu lokacja-lokacja, która łączy się między dwiema sieciami wirtualnymi. Ta metoda używa tego samego protokołu [IPSec Tunnel Mode](/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) , co powyżej połączenie sieci VPN między lokacjami, określone powyżej.

Zaletą tego podejścia jest to, że połączenie sieci VPN jest nawiązywane za pośrednictwem sieci szkieletowej platformy Azure, a nie za pośrednictwem Internetu. Zapewnia to dodatkową warstwę zabezpieczeń w porównaniu z sieciami VPN typu lokacja-lokacja, które łączą się za pośrednictwem Internetu.

Więcej informacji:

* [Konfigurowanie połączenia między sieciami wirtualnymi przy użyciu Azure Resource Manager i programu PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Innym sposobem łączenia sieci wirtualnych jest wirtualne połączenie  [równorzędne](../../virtual-network/virtual-network-peering-overview.md). Ta funkcja umożliwia łączenie dwóch sieci platformy Azure, dzięki czemu komunikacja między nimi odbywa się za pośrednictwem infrastruktury sieci szkieletowej firmy Microsoft bez konieczności przechodzenia przez Internet. Wirtualne sieci równorzędne mogą łączyć dwa sieci wirtualnych w tym samym regionie lub dwa sieci wirtualnych w różnych regionach platformy Azure. Sieciowych grup zabezpieczeń może służyć do ograniczania łączności między różnymi podsieciami lub systemami.

## <a name="availability"></a>Dostępność

Dostępność to kluczowy składnik dowolnego programu zabezpieczającego. Jeśli użytkownicy i systemy nie mogą uzyskać dostępu do tego, czego potrzebują do uzyskiwania dostępu za pośrednictwem sieci, można rozważyć naruszenie bezpieczeństwa usługi. Platforma Azure oferuje technologie sieciowe, które obsługują następujące mechanizmy wysokiej dostępności:

* Równoważenia obciążenia opartego na protokole HTTP
* Równoważenie obciążenia na poziomie sieci
* Globalne Równoważenie obciążenia

Równoważenie obciążenia jest mechanizmem przeznaczonym do równomiernego dystrybuowania połączeń między wieloma urządzeniami. Cele równoważenia obciążenia są następujące:

* Aby zwiększyć dostępność. W przypadku równoważenia obciążenia połączeń na wielu urządzeniach, co najmniej jedno urządzenie może stać się niedostępne bez naruszenia usługi. Usługi działające na pozostałych urządzeniach online mogą nadal udostępniać zawartość z usługi.
* W celu zwiększenia wydajności. W przypadku równoważenia obciążenia połączeń na wielu urządzeniach pojedyncze urządzenie nie musi obsługiwać całego przetwarzania. Zamiast tego wymagania dotyczące przetwarzania i pamięci do obsługi zawartości są rozłożone na wiele urządzeń.

### <a name="http-based-load-balancing"></a>Równoważenia obciążenia opartego na protokole HTTP

Organizacje korzystające z usług sieci Web często chcą mieć moduł równoważenia obciążenia oparty na protokole HTTP przed tymi usługami sieci Web. Pozwala to zapewnić odpowiednie poziomy wydajności i wysoką dostępność. Tradycyjne usługi równoważenia obciążenia oparte na sieci korzystają z protokołów warstwy sieciowej i transportowej. Usługi równoważenia obciążenia oparte na protokole HTTP z drugiej strony podejmują decyzje na podstawie charakterystyki protokołu HTTP.

Usługa Azure Application Gateway zapewnia równoważenia obciążenia opartego na protokole HTTP dla usług opartych na sieci Web. Application Gateway obsługuje:

* Koligacja sesji oparta na plikach cookie. Ta funkcja sprawdza, czy połączenia ustanowione na jednym z serwerów znajdujących się za tym modułem równoważenia obciążenia pozostają nienaruszone między klientem i serwerem. Zapewnia to stabilność transakcji.
* Odciążanie protokołu TLS. Gdy klient łączy się z usługą równoważenia obciążenia, ta sesja jest zaszyfrowana przy użyciu protokołu HTTPS (TLS). Jednak aby zwiększyć wydajność, można użyć protokołu HTTP (unencrypted) do łączenia się między usługą równoważenia obciążenia i serwerem sieci Web za modułem równoważenia obciążenia. Jest to określane jako "odciążanie protokołu TLS", ponieważ serwery sieci Web za modułem równoważenia obciążenia nie napotykają obciążenia procesora związanego z szyfrowaniem. W związku z tym serwery sieci Web mogą szybciej żądania obsługi.
* Routing zawartości oparty na adresach URL. Ta funkcja umożliwia modułowi równoważenia obciążenia podejmowanie decyzji o tym, gdzie przekazywać połączenia na podstawie docelowego adresu URL. Zapewnia to większą elastyczność niż rozwiązania, które umożliwiają podejmowanie decyzji dotyczących równoważenia obciążenia na podstawie adresów IP.

Więcej informacji:

* [Application Gateway — omówienie](../../application-gateway/overview.md)

### <a name="network-level-load-balancing"></a>Równoważenie obciążenia na poziomie sieci

W przeciwieństwie do równoważenia obciążenia opartego na protokole HTTP usługa równoważenia obciążenia na poziomie sieci podejmuje decyzje na podstawie numerów adresów IP i portów (TCP lub UDP).
Korzystając z Azure Load Balancer, można uzyskać korzyści wynikające z równoważenia obciążenia na poziomie sieci na platformie Azure. Niektóre kluczowe cechy Load Balancer obejmują:

* Równoważenie obciążenia na poziomie sieci na podstawie adresów IP i numerów portów.
* Obsługa dowolnego protokołu warstwy aplikacji.
* Równoważy obciążenia z maszynami wirtualnymi platformy Azure i wystąpieniami ról usług Cloud Services.
* Może być używany zarówno w przypadku aplikacji internetowych (zewnętrznego równoważenia obciążenia), jak i niepołączonych z Internetem (wewnętrzne równoważenie obciążenia) i maszyn wirtualnych.
* Monitorowanie punktu końcowego, które służy do określenia, czy dowolna z usług za modułem równoważenia obciążenia stanie się niedostępna.

Więcej informacji:

* [Moduł równoważenia obciążenia dostępny z Internetu między wieloma maszynami wirtualnymi lub usługami](../../load-balancer/load-balancer-overview.md)
* [Przegląd wewnętrznego modułu równoważenia obciążenia](../../load-balancer/load-balancer-overview.md)

### <a name="global-load-balancing"></a>Globalne Równoważenie obciążenia

Niektóre organizacje chcą mieć najwyższy możliwy poziom dostępności. Jednym ze sposobów osiągnięcia tego celu jest hostowanie aplikacji w globalnie rozproszonych centrach danych. Gdy aplikacja jest hostowana w centrach danych znajdujących się na całym świecie, możliwe jest, aby cały region geopolityczny stał się niedostępny i nadal mieć aplikację.

Ta strategia równoważenia obciążenia może również przynieść korzyści z wydajności. Można kierować żądania dotyczące usługi do centrum danych znajdującego się najbliżej urządzenia, które wykonuje żądanie.

Na platformie Azure możesz uzyskać korzyści wynikające z globalnego równoważenia obciążenia, korzystając z usługi Azure Traffic Manager.

Więcej informacji:

* [Co to jest usługa Traffic Manager?](../../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Rozpoznawanie nazw

Rozpoznawanie nazw jest funkcją krytyczną dla wszystkich usług hostowanych na platformie Azure. Z punktu widzenia zabezpieczeń naruszenie funkcji rozpoznawania nazw może prowadzić do przekierowania żądań od lokacji do lokacji osoby atakującej. Bezpieczne rozpoznawanie nazw jest wymagane dla wszystkich usług hostowanych w chmurze.

Istnieją dwa typy rozpoznawania nazw, które należy rozwiązać:

* Wewnętrzne rozpoznawanie nazw. Jest to używane przez usługi w sieci wirtualnych, w sieciach lokalnych lub obu. Nazwy używane do rozpoznawania nazw wewnętrznych nie są dostępne za pośrednictwem Internetu. W celu uzyskania optymalnego bezpieczeństwa należy pamiętać, że wewnętrzny schemat rozpoznawania nazw nie jest dostępny dla użytkowników zewnętrznych.
* Rozpoznawanie nazw zewnętrznych. Jest to używane przez osoby i urządzenia poza sieciami lokalnymi i sieciami wirtualnymi. Są to nazwy, które są widoczne dla Internetu, i służą do bezpośredniego połączenia z usługami w chmurze.

Do rozpoznawania nazw wewnętrznych są dostępne dwie opcje:

* Serwer DNS sieci wirtualnej. Podczas tworzenia nowej sieci wirtualnej jest tworzony serwer DNS. Ten serwer DNS może rozpoznać nazwy maszyn znajdujących się w tej sieci wirtualnej. Ten serwer DNS nie jest konfigurowalny, jest zarządzany przez Menedżera sieci szkieletowej Azure i w związku z tym może pomóc w zabezpieczeniu rozwiązania rozpoznawania nazw.
* Przenieś własny serwer DNS. Istnieje możliwość wprowadzenia serwera DNS własnego wyboru w sieci wirtualnej. Ten serwer DNS może być Active Directory zintegrowanym serwerem DNS lub dedykowanym rozwiązaniem serwera DNS udostępnionym przez partnera platformy Azure, który można uzyskać z portalu Azure Marketplace.

Więcej informacji:

* [Omówienie sieci wirtualnej](../../virtual-network/virtual-networks-overview.md)
* [Zarządzanie serwerami DNS używanymi przez sieć wirtualną](../../virtual-network/manage-virtual-network.md#change-dns-servers)

W przypadku rozpoznawania nazw zewnętrznych dostępne są dwie opcje:

* Hostowanie własnego zewnętrznego serwera DNS w środowisku lokalnym.
* Hostowanie własnego zewnętrznego serwera DNS z dostawcą usług.

Wiele dużych organizacji hostuje własne serwery DNS lokalnie. Można to zrobić, ponieważ posiadają wiedzę o sieci i globalną obecność.

W większości przypadków lepiej jest hostować usługi rozpoznawania nazw DNS z dostawcą usług. Tacy dostawcy usług mają doświadczenie w zakresie sieci i globalną obecność, aby zapewnić wysoką dostępność usług rozpoznawania nazw. Dostępność jest istotna dla usług DNS, ponieważ w przypadku niepowodzenia usług rozpoznawania nazw nikt nie będzie mógł nawiązać połączenia z usługami internetowymi.

System Azure oferuje wysoce dostępne i wysoko działające rozwiązanie zewnętrznego serwera DNS w postaci Azure DNS. To rozwiązanie rozpoznawania nazw zewnętrznych wykorzystuje światową infrastrukturę Azure DNS. Umożliwia Hostowanie domeny na platformie Azure przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń, co w przypadku innych usług platformy Azure. W ramach platformy Azure dziedziczy również silne mechanizmy kontroli zabezpieczeń wbudowane w platformę.

Więcej informacji:

* [Przegląd Azure DNS](../../dns/dns-overview.md)
* [Azure DNS strefy prywatne](../../dns/private-dns-overview.md) umożliwiają skonfigurowanie prywatnych nazw DNS dla zasobów platformy Azure, a nie nazw przypisanych automatycznie bez konieczności dodawania NIESTANDARDOWEGO rozwiązania DNS.

## <a name="perimeter-network-architecture"></a>Architektura sieci obwodowej

Wiele dużych organizacji używa sieci obwodowych do segmentacji swoich sieci i tworzenia strefy buforu między Internetem i ich usługami. Część obwodu sieci jest traktowana jako strefa o niskim poziomie zabezpieczeń, a w tym segmencie sieci nie są umieszczane żadne zasoby o dużej wartości. Zwykle widzisz urządzenia zabezpieczeń sieciowych, które mają interfejs sieciowy w segmencie sieci obwodowej. Inny interfejs sieciowy jest podłączony do sieci z maszynami wirtualnymi i usługami, które akceptują połączenia przychodzące z Internetu.

Sieci obwodowe można projektować na wiele różnych sposobów. Decyzja o wdrożeniu sieci obwodowej, a następnie rodzaj sieci obwodowej, która ma być używana, jeśli zdecydujesz się użyć jednej z nich, zależy od wymagań dotyczących zabezpieczeń sieci.

Więcej informacji:

* [Microsoft Cloud usług i zabezpieczeń sieci](network-best-practices.md)

## <a name="azure-ddos-protection"></a>Azure DDoS Protection

Ataki typu „rozproszona odmowa usługi” (Distributed Denial of Service, DDoS) należą do największych obaw związanych z dostępnością i zabezpieczeniami wśród klientów, którzy planują przeniesienie swoich aplikacji do chmury. Atak DDoS próbuje wymusić wyczerpanie zasobów aplikacji, co sprawia, że aplikacja jest niedostępna dla uprawnionych użytkowników. Celem ataku DDoS może być dowolny punkt końcowy publicznie dostępny za pośrednictwem Internetu.
Firma Microsoft zapewnia ochronę DDoSą znaną jako **podstawową** w ramach platformy Azure. Jest to bezpłatne i obejmuje zawsze monitorowanie i łagodzenie w czasie rzeczywistym wspólnych ataków na poziomie sieci. Oprócz ochrony zawartej w programie DDoS Protection **Basic** można włączyć opcję **Standard** . Standardowe funkcje DDoS Protection obejmują:

* **Integracja z platformą natywną:** Natywnie zintegrowane z platformą Azure. Obejmuje konfigurację za pomocą Azure Portal. W standardzie DDoS Protection są poznanie zasobów i konfiguracji zasobów.
* **Ochrona klucza:** Uproszczona konfiguracja natychmiast chroni wszystkie zasoby w sieci wirtualnej zaraz po włączeniu DDoS Protection Standard. Nie jest wymagana żadna interwencja ani definicja użytkownika. Program DDoS Protection Standard natychmiast i automatycznie ogranicza atak po jego wykryciu.
* **Zawsze włączone monitorowanie ruchu:** Wzorce ruchu aplikacji są monitorowane przez 24 godziny dziennie, 7 dni w tygodniu, szukając wskaźników ataków DDoS. Środki zaradcze są wykonywane w przypadku przekroczenia zasad ochrony.
* **Raporty ograniczenia ataków** Raporty ograniczenia ataków używają zagregowanych danych przepływu sieci do udostępniania szczegółowych informacji o atakach przeznaczonych dla Twoich zasobów.
* **Ataki — dzienniki przepływów** Za pomocą dzienników przepływów zaradczych ataków można sprawdzić porzucony ruch, ruch przekazywany i inne dane ataku niemal w czasie rzeczywistym podczas aktywnego ataku DDoS.
* **Dostrajanie adaptacyjne:** Profilowanie ruchu inteligentnego uzyskuje informacje o ruchu aplikacji w czasie i wybiera i aktualizuje profil, który jest najbardziej odpowiedni dla Twojej usługi. Profil dostosowuje się w miarę zmiany ruchu w czasie. Ochrona warstwy 3 do warstwy 7: zapewnia pełną ochronę DDoS w stosie, gdy jest używana z zaporą aplikacji sieci Web.
* **Rozbudowana Skala łagodzenia:** Ponad 60 różnych typów ataków można ograniczyć, korzystając z globalnej pojemności, aby chronić przed największym znanymi atakami DDoS.
* **Metryki ataków:** Podsumowania metryk z każdego ataku są dostępne za pośrednictwem Azure Monitor.
* **Alerty ataków:** Alerty można skonfigurować przy uruchamianiu i zatrzymywaniu ataku oraz przez czas trwania ataku przy użyciu wbudowanych metryk ataku. Alerty integrują się z oprogramowaniem operacyjnym, takimi jak dzienniki monitora Microsoft Azure, Splunk, Azure Storage, Poczta E-mail i Azure Portal.
* **Gwarancja kosztów:**  Kredyty na korzystanie z usług transferu i skalowanie w poziomie aplikacji dla udokumentowanych ataków DDoS.
* **DDoS szybkie reagowanie** Klienci w DDoS Protection Standard mają teraz dostęp do szybkiego zespołu odpowiedzi podczas aktywnego ataku. DRR może pomóc w zbadaniu ataków, niestandardowych ograniczeniach podczas ataku i analizy po ataku.


Więcej informacji:

* [Omówienie ochrony DDOS](../../ddos-protection/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Azure Front Door

Usługa frontonu platformy Azure umożliwia definiowanie i monitorowanie globalnego routingu ruchu internetowego oraz zarządzanie nim. Optymalizuje Routing ruchu w celu uzyskania najlepszej wydajności i wysokiej dostępności. Usługa Azure Front Door umożliwia tworzenie reguł zapory aplikacji internetowej w celu kontrolowania dostępu, aby chronić obciążenia protokołów HTTP/HTTPS przed wykorzystywaniem w oparciu o adresy IP, kod kraju i parametry protokołu HTTP klientów. Ponadto drzwi z przodu umożliwiają również tworzenie reguł ograniczania szybkości na potrzeby sprawdzonej złośliwego ruchu bot, w tym żądania odciążania protokołu TLS i protokołu HTTP/HTTPS, przetwarzania warstwy aplikacji.

Sama platforma Front Door jest chroniona przez usługę Azure DDoS Protection w warstwie Podstawowa. W celu zastosowania dalszej ochrony przez atakami w warstwie sieci (protokół TCP/UDP) za pośrednictwem funkcji automatycznego dostrajania i ograniczania ryzyka można włączyć usługę Azure DDoS Protection w warstwie Standardowa w sieciach wirtualnych i zasobach zabezpieczeń. Przód drzwi to zwrotny serwer proxy warstwy 7, który umożliwia przekazywanie ruchu internetowego tylko do serwerów zaplecza i blokowanie innych typów ruchu domyślnego.

Więcej informacji:

* Aby uzyskać więcej informacji na temat całego zestawu możliwości platformy Azure, zobacz [Przegląd drzwi z przodu platformy Azure](../../frontdoor/front-door-overview.md)

## <a name="azure-traffic-manager"></a>Usługa Azure Traffic Manager

Usługa Azure Traffic Manager to oparty na systemie DNS moduł równoważenia obciążenia ruchu, który umożliwia optymalną dystrybucję ruchu do usług w wielu regionach platformy Azure na świecie, przy jednoczesnym zapewnieniu wysokiej dostępności i krótkiego czasu odpowiedzi. Usługa Traffic Manager używa systemu DNS do kierowania żądań klientów do najodpowiedniejszego punktu końcowego usługi w oparciu o metodę routingu ruchu i kondycję punktów końcowych. Punkt końcowy to dowolna internetowa usługa hostowana wewnątrz platformy Azure lub poza nią. Usługa Traffic Manager monitoruje punkty końcowe i nie kieruje ruchu do żadnych punktów końcowych, które są niedostępne.

Więcej informacji:

* [Omówienie usługi Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>Monitorowanie i wykrywanie zagrożeń

Platforma Azure udostępnia funkcje ułatwiające w tym kluczowym obszarze wczesne wykrywanie, monitorowanie i gromadzenie i przeglądanie ruchu sieciowego.

### <a name="azure-network-watcher"></a>Azure Network Watcher

Usługa Azure Network Watcher może pomóc w rozwiązywaniu problemów i udostępnia cały nowy zestaw narzędzi, które ułatwiają identyfikację problemów z zabezpieczeniami.

[Widok grupy zabezpieczeń](../../network-watcher/network-watcher-security-group-view-overview.md) ułatwia inspekcję i zgodność zabezpieczeń Virtual Machines. Ta funkcja służy do przeprowadzania inspekcji programistycznych, porównując zasady bazowe zdefiniowane przez organizację z obowiązującymi regułami dla każdej z maszyn wirtualnych. Może to ułatwić zidentyfikowanie dowolnego dryfu konfiguracji.

[Przechwytywanie pakietów](../../network-watcher/network-watcher-packet-capture-overview.md) umożliwia przechwytywanie ruchu sieciowego do i z maszyny wirtualnej. Można zbierać statystyki sieci i rozwiązywać problemy z aplikacjami, które mogą być niecenne podczas badania nieautoryzowanych sieci. Można również użyć tej funkcji razem z Azure Functions do uruchamiania przechwytywania sieciowego w odpowiedzi na określone alerty platformy Azure.

Aby uzyskać więcej informacji na temat Network Watcher i sposobu rozpoczynania testowania niektórych funkcji w laboratoriach, zobacz [Omówienie monitorowania obserwatora sieciowego platformy Azure](../../network-watcher/network-watcher-monitoring-overview.md).

> [!NOTE]
> Najbardziej aktualne powiadomienia dotyczące dostępności i stanu tej usługi można znaleźć na [stronie aktualizacji platformy Azure](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie oraz zapewnia lepszą widoczność i kontrolę nad bezpieczeństwem zasobów platformy Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogą być niezauważalne i działać z dużym zestawem rozwiązań zabezpieczeń.

Security Center pomaga zoptymalizować i monitorować zabezpieczenia sieci przez:

* Zapewnianie zaleceń dotyczących zabezpieczeń sieci.
* Monitorowanie stanu konfiguracji zabezpieczeń sieci.
* Powiadamianie o zagrożeniach opartych na sieci, zarówno na poziomie punktu końcowego, jak i sieci.

Więcej informacji:

* [Wprowadzenie do usługi Azure Security Center](../../security-center/security-center-introduction.md)

### <a name="virtual-network-tap"></a>Virtual Network naciśnij pozycję

Usługa Azure Virtual Network TAP (punkt dostępu terminalu) umożliwia ciągłą przesyłanie strumieniowego ruchu sieciowego maszyny wirtualnej do narzędzia do zbierania pakietów sieciowych lub narzędzi analitycznych. Moduł zbierający lub narzędzie analityczne jest dostarczany przez partnera wirtualnego urządzenia sieciowego. Możesz użyć tego samego zasobu sieci wirtualnej, aby agregować ruch z wielu interfejsów sieciowych w ramach tej samej lub różnych subskrypcji.

Więcej informacji:

* [Virtual Network TAP](../../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>Rejestrowanie

Rejestrowanie na poziomie sieci jest kluczową funkcją dla dowolnego scenariusza zabezpieczeń sieci. Na platformie Azure można rejestrować informacje uzyskane dla sieciowych grup zabezpieczeń, aby uzyskać informacje dotyczące rejestrowania na poziomie sieci. Dzięki rejestrowaniu sieciowej grupy zabezpieczeń uzyskasz informacje z:

* [Dzienniki aktywności](../../azure-monitor/essentials/platform-logs-overview.md). Użyj tych dzienników, aby wyświetlić wszystkie operacje przesłane do subskrypcji platformy Azure. Te dzienniki są domyślnie włączone i mogą być używane w Azure Portal. Wcześniej były znane jako inspekcje lub dzienniki operacyjne.
* Dzienniki zdarzeń. Te dzienniki zawierają informacje o tym, jakie reguły sieciowej grupy zabezpieczeń zostały zastosowane.
* Dzienniki liczników. Te dzienniki informują o tym, ile razy została zastosowana Każda reguła sieciowej grupy zabezpieczeń, aby odmówić lub zezwolić na ruch.

Do wyświetlania i analizowania tych dzienników można także użyć [programu Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), zaawansowanego narzędzia do wizualizacji danych.
Więcej informacji:

* [Dzienniki Azure Monitor dla sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń)](../../virtual-network/virtual-network-nsg-manage-log.md)