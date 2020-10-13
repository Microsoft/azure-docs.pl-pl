---
title: Łączenie z Chinami przy użyciu wirtualnej sieci WAN platformy Azure i bezpiecznego centrum
description: Dowiedz się, jak łączyć się z Chinami przy użyciu wirtualnej sieci WAN platformy Azure i bezpiecznego centrum.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: sukishen
ms.openlocfilehash: 83cc7757f31a631af755155b49c7c26753618426
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91399112"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Łączenie z Chinami przy użyciu wirtualnej sieci WAN platformy Azure i bezpiecznego centrum

W przypadku korzystania z popularnych motoryzacyjnych, produkcyjnych, branżowych i innych instytutów, takich jak ambasady, często jest pytania dotyczące ulepszania połączeń z Chiną. Te ulepszenia są głównie istotne w przypadku używania Cloud Services takich jak Microsoft 365, usługi globalne platformy Azure lub gałęzie połączeń w Chinach ze szkieletem klientów.

W większości przypadków klienci są zoptymalizowaniem z dużymi opóźnieniami, niską przepustowością, niestabilnym połączeniem i wysokim kosztem łączącym się z poza Chinami (na przykład Europa lub Stany Zjednoczone).

Powodem tego problemu jest "Świetna Zapora Chin", która chroni w chińskiej części Internetu i filtruje ruch do Chin. Niemal cały ruch z Chin do Chin poza Chiny, z wyjątkiem specjalnych stref administracyjnych, takich jak Hongkong i Makau, przekazuje doskonałą zaporę. Ruch uruchomiony za pomocą Hongkongu i Makau nie uderzy w pełni wszechstronnej zapory, jest obsługiwany przez podzestaw doskonałej zapory.

![Połączenie dostawcy](./media/interconnect-china/provider.png)

Korzystając z wirtualnej sieci WAN, klient może nawiązać bardziej wydajne i stabilne połączenie z usługami Microsoft Cloud i nawiązać połączenie z siecią przedsiębiorstwa bez naruszenia chińskiego ustawodawstwa cyberbezpieczeństwa.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>Wymagania i przepływ pracy

Jeśli chcesz zachować zgodność z chińskim prawem cyberbezpieczeństwa, musisz spełnić zestaw określonych warunków.

Najpierw należy współpracować z siecią i USŁUGODAWCą internetowym, który jest właścicielem licencji dostawcy ICP (Internet Content Provider) dla Chin. W większości przypadków zostanie wyświetlony jeden z następujących dostawców:

* Chiny Telecom Global Ltd.
* Chiny Mobile Ltd.
* Chiny Unicom Ltd.
* PCCW Global Ltd.
* Hongkong. Telecom Ltd.

W zależności od dostawcy i Twoich potrzeb musisz zakupić jedną z następujących usług łączności sieciowej, aby połączyć swoje gałęzie w Chinach.

* Sieć MPLS/IPVPN 
* Sieć WAN zdefiniowana przez oprogramowanie (SDWAN)
* Dedykowany dostęp do Internetu

Następnie musisz wyrazić zgodę na tego dostawcę, aby zagadnień sieć globalną firmy Microsoft i jej brzeg Ruch przychodzący w sieci Hongkong, a nie w Pekin lub Szanghaj. W takim przypadku Hongkong jest bardzo ważne ze względu na swoje fizyczne połączenie i lokalizację z Chiną.

Chociaż większość klientów sądzi, że w przypadku korzystania z usługi Interconnect jest to najlepszy przypadek, ponieważ wygląda na to, że w przypadku szukania mapy nie jest to zgodne z Chinami. Po obserwowania sieciowych map światłowodowych niemal wszystkie połączenia sieciowe przechodzą przez Pekin, Szanghaj i Hongkong SAR. Powoduje to, że w Hong Kongu lepszym rozwiązaniem jest wybór połączenia z Chiną.

W zależności od dostawcy mogą wystąpić różne oferty usług. W poniższej tabeli przedstawiono przykład dostawców i oferowane przez nich usługi, na podstawie informacji w momencie zapisania tego artykułu.

| Usługa | Przykłady dla dostawcy |
| --- | --- |
| Sieć MPLS/IPVPN |PCCW, Chiny — globalna Telecom |
|SDWAN| PCCW, Chiny — globalna Telecom|
| Dedykowany dostęp do Internetu | PCCW, Hongkong, a Chiny — pozyskiwanie|

Za pomocą swojego dostawcy można wyrazić zgodę na następujące dwa rozwiązania do osiągnięcia dostępu do globalnego szkieletu firmy Microsoft:

* Microsoft Azure ExpressRoute przerwany w Hong Kongu. Jest to przypadek użycia MPLS/IPVPN. Obecnie jedynym dostawcą licencji ICP z ExpressRouteem do Hongkongu jest Chińska ogólna Telecom. Jednak mogą również komunikować się z innymi dostawcami, jeśli korzystają z dostawców usług Exchange w chmurze, takich jak Megaport lub incloud. Aby uzyskać więcej informacji, zobacz [dostawcy połączeń ExpressRoute](../expressroute/expressroute-locations-providers.md#partners).

* Korzystanie z dedykowanego dostępu do Internetu bezpośrednio w jednym z następujących punktów wymiany Internet lub połączenia z siecią prywatną.

Na poniższej liście przedstawiono wymianę internetową, którą można wykonać w Hongkongu:

* AMS-IX Hongkong
* BBIX Hongkong
* Equinix Hongkong
* HKIX

W przypadku korzystania z tego połączenia następnym przeskokiem protokołu BGP dla usług firmy Microsoft musi być numer systemu autonomicznego firmy Microsoft (jako #) 8075. Jeśli używasz pojedynczej lokalizacji lub rozwiązania SDWAN, które będzie możliwe do wybrania połączenia.

W obu przypadkach firma Microsoft zaleca, aby w chińskim Kontynentie były dostępne dwa i regularne Zagadnieńe internetowe. Jest to dzielenie ruchu między ruchem przedsiębiorstwa do usług w chmurze, takich jak Microsoft 365 i Azure, i regulowanego ruchu internetowego przez prawo.

Zgodna architektura sieci w Chinach może wyglądać podobnie do poniższego przykładu:

![Wiele gałęzi](./media/interconnect-china/multi-branch.png)

W tym przykładzie, jeśli masz połączenie z firmą Microsoft Global Ruch przychodzący w sieci Hongkong, możesz teraz zacząć korzystać z [globalnej architektury tranzytowej sieci WAN platformy Azure](virtual-wan-global-transit-network-architecture.md) i dodatkowych usług, takich jak Azure Secure Virtual WAN Hub, aby korzystać z usług i połączeń z gałęziami i centrum danych poza Chiny.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>Komunikacja między centrami

W tej sekcji używamy wirtualnej komunikacji między centrami sieci WAN a połączeniem z usługą. W tym scenariuszu utworzysz nowy wirtualny zasób koncentratora sieci WAN, aby połączyć się z koncentratorem wirtualnym sieci WAN w Hongkongu, inne odpowiednie regiony, region, w którym masz już zasoby platformy Azure lub w którym chcesz się połączyć.

Przykładowa architektura może wyglądać następująco:

![Przykładowa sieć WAN](./media/interconnect-china/sample.png)

W tym przykładzie gałęzie Chin łączą się z Chiny chmury platformy Azure i nawzajem przy użyciu połączeń VPN lub MPLS. Gałęzie, które muszą być połączone z usługami globalnymi, korzystają z usług MPLS lub internetowych, które są połączone bezpośrednio z Hongkong Hongkong. Jeśli chcesz użyć ExpressRoute w Hong Kongu, a także w innym regionie, musisz skonfigurować [ExpressRoute Global REACH](../expressroute/expressroute-global-reach.md) do łączenia obu obwodów usługi ExpressRoute.

ExpressRoute Global Reach nie jest dostępna w niektórych regionach. Jeśli chcesz połączyć się z Brazylią lub Indiami, na przykład musisz skorzystać z [dostawców programu Exchange w chmurze](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers) , aby zapewnić usługi routingu.

Na poniższym rysunku przedstawiono oba przykłady dla tego scenariusza.

![Global Reach](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-microsoft-365"></a><a name="secure"></a>Zabezpieczanie Internetu zagadnień Microsoft 365

Innym zagadnieniem jest bezpieczeństwo sieci, a także rejestrowanie punktu wejścia między Chin i wirtualnego sieci WAN ze składnika szkieletu i szkieletem klienta. W większości przypadków istnieje potrzeba zagadnień się do Internetu w Hong Kongu, aby bezpośrednio uzyskać dostęp do sieci Microsoft Edge i z tego powodu serwery z drzwiami platformy Azure używane dla usług Microsoft 365.

W przypadku obu scenariuszy z wirtualną siecią WAN można korzystać z [bezpiecznego Centrum sieci wirtualnej platformy Azure](../firewall-manager/secured-virtual-hub.md). Za pomocą Menedżera zapory platformy Azure można zmienić regularne wirtualne koncentratory sieci WAN na zabezpieczone centrum, a następnie wdrożyć zaporę platformy Azure w tym centrum i zarządzać nią.

Na poniższej ilustracji przedstawiono przykład tego scenariusza:

![Internet zagadnień dla ruchu w sieci Web i usług firmy Microsoft](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>Architektura i przepływy ruchu

W zależności od wyboru dotyczącego połączenia z Hongkong, ogólna architektura może ulec zmianie nieco. W tej sekcji przedstawiono trzy dostępne architektury w różnych kombinacjach z sieciami VPN lub SDWAN i/lub ExpressRoute.

Wszystkie te opcje korzystają z bezpiecznego Centrum sieci wirtualnej platformy Azure w celu zapewnienia bezpośredniej łączności Microsoft 365 w Hong Kongu. Te architektury obsługują również wymagania dotyczące zgodności dla [Microsoft 365 wiele geograficznych](/microsoft-365/enterprise/microsoft-365-multi-geo) i utrzymujeją ten ruch blisko najbliższej lokalizacji drzwi platformy Azure. W związku z tym również udoskonalenie użycia Microsoft 365 poza Chin.

W przypadku korzystania z wirtualnej sieci WAN platformy Azure razem z połączeniami internetowymi każde połączenie może korzystać z dodatkowych usług, takich jak [Microsoft Azure usług komunikacji równorzędnej (Maps)](https://docs.microsoft.com/azure/peering-service/about). MAPY zostały skompilowane w celu zoptymalizowania ruchu przychodzącego do sieci globalnej firmy Microsoft od innych dostawców usług internetowych innych firm.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>Opcja 1: SDWAN lub VPN

W tej sekcji omówiono projektowanie, która używa SDWAN lub sieci VPN do Hongkongu i innych gałęzi. Ta opcja umożliwia wyświetlenie przepływu ruchu i użycia w przypadku korzystania z czystego połączenia internetowego w obu lokacjach wirtualnego szkieletu sieci WAN. W takim przypadku połączenie jest nawiązywane z Hongkongu w przypadku korzystania z dedykowanego dostępu do Internetu lub rozwiązania SDWAN dostawcy ICP. Inne gałęzie używają również czystych rozwiązań internetowych lub SDWAN.

![Chiny do ruchu w Hongkongu](./media/interconnect-china/china-traffic.png)

W tej architekturze każda lokacja jest połączona z siecią globalną firmy Microsoft przy użyciu sieci VPN i wirtualnej sieci WAN platformy Azure. Ruch między lokacjami i Hongkongiem jest przesyłany trough sieci firmy Microsoft i używa zwykłych połączeń internetowych w ostatniej milowej.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>Opcja 2: ExpressRoute i SDWAN lub VPN

W tej sekcji omówiono projektowanie, która używa ExpressRoute w Hongkongu i innych gałęziach z gałęziami sieci VPN/SDWAN. Ta opcja pokazuje, jak używać i ExpressRoute zakończonych w Hong Kongu i innych gałęziach połączonych za pośrednictwem SDWAN lub sieci VPN. ExpressRoute w Hong Kongu jest obecnie ograniczone do krótkiej listy dostawców, które można znaleźć na liście partnerów w usłudze [Express Route](../expressroute/expressroute-locations-providers.md#global-commercial-azure).

![Chiny do ExpressRoute ruchu w Hongkongu](./media/interconnect-china/expressroute.png)

Dostępne są również opcje kończenia ExpressRoute z Chin, na przykład w Korei Południowej lub Japonii. Jednak ze względu na zgodność, regulację i opóźnienie, Hongkong SAR jest obecnie najlepszym wyborem.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>Opcja 3: tylko ExpressRoute

W tej sekcji omówiono projekt, w którym ExpressRoute jest używany dla Hongkongu i innych gałęzi. Ta opcja umożliwia pokazanie połączeń przy użyciu programu ExpressRoute na obu końcach. W tym miejscu masz inny przepływ ruchu niż drugi. Ruch Microsoft 365 będzie przepływał do chronionego koncentratora sieci wirtualnej platformy Azure i z sieci Microsoft Edge oraz z Internetu.

Ruch kierowany do rozłączonych gałęzi lub z nich do lokalizacji w Chinach będzie postępować zgodnie z różnymi rozwiązaniami w ramach tej architektury. Obecnie wirtualna sieć WAN nie obsługuje ExpressRoute do przesyłania ExpressRoute. Ruch będzie korzystał z ExpressRoute Global Reach lub z połączeniami innych firm bez przekazywania wirtualnego centrum sieci WAN. Będzie ona bezpośrednio przepływać z jednej firmy Microsoft Enterprise Edge (MSEE) do innej.

![ExpressRoute Global Reach](./media/interconnect-china/expressroute-virtual.png)

Obecnie ExpressRoute Global Reach nie jest dostępna w każdym kraju/regionie, ale można skonfigurować rozwiązanie przy użyciu wirtualnej sieci WAN platformy Azure.

Można na przykład skonfigurować ExpressRoute za pomocą komunikacji równorzędnej firmy Microsoft i połączyć tunel VPN za pośrednictwem tej komunikacji równorzędnej z wirtualną siecią WAN platformy Azure. Teraz włączono, jeszcze raz, tranzyt między sieciami VPN i ExpressRoute bez Global Reach i dostawcy i usługi innej firmy, takich jak Megaport Cloud.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły:

* [Globalna architektura sieci tranzytowej z wirtualną siecią WAN platformy Azure](virtual-wan-global-transit-network-architecture.md)

* [Tworzenie wirtualnego centrum sieci WAN](virtual-wan-site-to-site-portal.md)

* [Konfigurowanie bezpiecznego Centrum sieci WAN](../firewall-manager/secure-cloud-network.md)

* [Omówienie usługi Azure peering Service w wersji zapoznawczej](https://docs.microsoft.com/azure/peering-service/about)
