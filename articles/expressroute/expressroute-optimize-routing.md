---
title: 'Azure ExpressRoute: Optymalizowanie routingu'
description: Ta strona zawiera szczegółowe informacje dotyczące optymalizacji routingu w przypadku, gdy występują co najmniej dwa obwody usługi ExpressRoute łączące firmę Microsoft z siecią firmową.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 07/11/2019
ms.author: duau
ms.openlocfilehash: 731101b1a8236e20a9af07f1bbf5a7b70d53c0ac
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653387"
---
# <a name="optimize-expressroute-routing"></a>Optymalizacja routingu usługi ExpressRoute
Jeśli masz wiele obwodów usługi ExpressRoute, masz więcej niż jedną ścieżkę łączenia z firmą Microsoft. W związku z tym może wystąpić routing nieoptymalny, tzn. ruch może użyć dłuższej ścieżki w celu dotarcia do firmy Microsoft lub z firmy Microsoft do sieci użytkownika. Im dłuższa ścieżka sieciowa, tym większe opóźnienie. Opóźnienie ma bezpośredni wpływ na wydajność aplikacji i środowisko użytkownika. W tym artykule przedstawiono ten problem i wyjaśniono, jak zoptymalizować routing przy użyciu standardowych technologii routingu.

## <a name="path-selection-on-microsoft-and-public-peerings"></a>Wybór ścieżki dla firmy Microsoft i publicznych komunikacji równorzędnych
Ważne jest, aby zapewnić, że podczas używania usługi Microsoft lub publicznej komunikacji równorzędnej ruch przepływający nad żądaną ścieżką, jeśli istnieje co najmniej jeden obwody usługi ExpressRoute, a także ścieżki do Internetu za pośrednictwem Internetu (9) lub usługodawcy internetowego (ISP). Protokół BGP wykorzystuje najlepszy algorytm wyboru ścieżki na podstawie różnych czynników, takich jak najdłuższy odpowiednik prefiksu (LPM). Aby zapewnić, że ruch przeznaczony dla platformy Azure za pośrednictwem firmy Microsoft lub publicznej komunikacji równorzędnej przechodzi przez ścieżkę ExpressRoute, klienci muszą zaimplementować *lokalny atrybut preferencji* , aby upewnić się, że ścieżka jest zawsze preferowana w ExpressRoute. 

> [!NOTE]
> Domyślna preferencja lokalna to zwykle 100. Wyższe preferencje lokalne są bardziej preferowane. 
>
>

Rozważmy następujący przykładowy scenariusz:

![Diagram przedstawiający problem z ExpressRouteą w przypadku wystąpienia 1 — nieoptymalny Routing od klienta do firmy Microsoft](./media/expressroute-optimize-routing/expressroute-localPreference.png)

W powyższym przykładzie, aby preferować ExpressRoute ścieżki skonfigurować preferencję lokalną w następujący sposób. 

**Cisco IOS — konfiguracja XE z perspektywy R1:**

- R1 (config) #route-map Preferuj ExR 10
- R1 (Konfiguracja-trasa-mapa) #set preferencja lokalna 150

- R1 (Konfiguracja) #router BGP 345
- R1 (Konfiguracja-router) #neighbor 1.1.1.2 zdalnego — jako 12076
- R1 (Konfiguracja-router) #neighbor aktywowanie 1.1.1.2
- R1 (Konfiguracja-router) #neighbor 1.1.1.2 Route-map Preferuj ExR

**Junos konfigurację z perspektywy R1:**

- user@R1# Set protokoły protokołu BGP Grupa iBGP typ wewnętrzny
- user@R1# Set protokoły BGP Grupa iBGP Local — preferencja 150



## <a name="suboptimal-routing-from-customer-to-microsoft"></a>Suboptymalny routing od klienta do firmy Microsoft
Przyjrzyjmy się problemowi z routingiem, korzystając z przykładu. Załóżmy, że masz dwa biura w USA: jedno w Los Angeles i jedno w Nowym Jorku. Biura są połączone za pośrednictwem sieci WAN, która może być Twoją siecią podstawową lub siecią IP VPN dostawcy usług. Dostępne są dwa obwody usługi ExpressRoute: jeden w zachodnich stanach USA i jeden we wschodnich stanach USA, które są również połączone w ramach sieci WAN. Oczywiście masz dwie ścieżki połączenia z siecią firmy Microsoft. Teraz wyobraź sobie, że dysponujesz wdrożeniem platformy Azure (np. usługą Azure App Service) zarówno w zachodnich, jak i wschodnich stanach USA. Masz zamiar połączyć użytkowników z Los Angeles z regionem świadczenia usługi Azure Zachodnie stany USA Azure, a użytkowników w Nowym Jorku z regionem Wschodnie stany USA, ponieważ administrator usług anonsuje, że użytkownicy z poszczególnych biur uzyskują w ten sposób dostęp do pobliskich usług Azure w celu uzyskania optymalnego środowiska. Niestety plan działa dobrze w przypadku użytkowników ze wschodniego wybrzeża, ale nie w przypadku użytkowników z zachodniego wybrzeża. Przyczyną tego problemu jest następująca. W poszczególnych obwodach usługi ExpressRoute anonsujemy zarówno prefiks dla wschodnich stanów USA (23.100.0.0/16), jak i prefiks w zachodnich stanach USA (13.100.0.0/16). Jeśli nie wiesz, który prefiks odpowiada któremu regionowi, nie możesz ich traktować w różny sposób. Sieć WAN może uznać, że oba prefiksy są bliżej wschodnich niż zachodnich stanów USA, i skierować obu użytkowników biura do obwodu usługi ExpressRoute we wschodnich stanach USA. W rezultacie wielu użytkowników w biurze z Los Angeles będzie niezadowolonych.

![Przypadek 1 dotyczący usługi ExpressRoute — suboptymalny routing od klienta do firmy Microsoft](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### <a name="solution-use-bgp-communities"></a>Rozwiązanie: użyj społeczności BGP
Aby zoptymalizować routing dla użytkowników obu biur, trzeba wiedzieć, który prefiks odpowiada zachodnim, a który wschodnim stanom USA. Kodujemy te informacje przy użyciu [wartości społeczności BGP](expressroute-routing.md). Do każdego regionu platformy Azure przypisano unikatową wartość wspólnotową BGP, np. "12076:51004" dla Wschodnie stany USA, "12076:51006" dla zachodnich Stanów Zjednoczonych. Skoro już wiesz, który prefiks odpowiada któremu regionowi świadczenia usługi Azure, możesz skonfigurować preferowany obwód usługi ExpressRoute. Ponieważ do wymiany informacji o routingu używamy protokołu BGP, w celu wpłynięcia na routing możesz użyć preferencji lokalnej BGP. W naszym przykładzie można przypisać wyższą wartość preferencji lokalnej na 13.100.0.0/16 w zachodnich niż we wschodnich stanach USA i podobnie wyższą wartość preferencji lokalnej na 23.100.0.0/16 we wschodnich niż w zachodnich stanach USA. Ta konfiguracja pozwoli zagwarantować, że gdy obie ścieżki do firmy Microsoft będą dostępne, użytkownicy w Los Angeles będą korzystać z obwodu usługi ExpressRoute w zachodnich stanach USA do połączenia ze stanami zachodnimi, natomiast użytkownicy z Nowego Jorku będą korzystać z usługi ExpressRoute we wschodnich stanach USA w celu połączenia ze stanami wschodnimi. Routing jest zoptymalizowany po obu stronach. 

![Rozwiązanie przypadku 1 dotyczącego usługi ExpressRoute — używanie społeczności BGP](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

> [!NOTE]
> Tę samą technikę, obejmującą preferencję lokalną, można zastosować do routingu od klienta do usługi Azure Virtual Network. Wartością społeczności BGP nie oznaczamy prefiksów anonsowanych z platformy Azure do Twojej sieci. Jednak ponieważ wiadomo, które wdrożenie usługi Virtual Network znajduje się najbliżej danego biura, można odpowiednio skonfigurować routery, ustawiając preferowanie określonego obwodu usługi ExpressRoute.
>
>

## <a name="suboptimal-routing-from-microsoft-to-customer"></a>Suboptymalny routing od firmy Microsoft do klienta
Oto inny przykład, w którym połączenia z firmy Microsoft używają dłuższej ścieżki, by dotrzeć do sieci. W tym przypadku używasz lokalnych serwerów programu Exchange i usługi Exchange Online w [środowisku hybrydowym](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx). Biura są połączone z siecią WAN. Prefiksy serwerów lokalnych w obu biurach są anonsowane do firmy Microsoft za pośrednictwem dwóch obwodów usługi ExpressRoute. Usługa Exchange Online inicjuje połączenia z serwerami lokalnymi w takich sytuacjach jak migracja skrzynek pocztowych. Niestety połączenie z biurem w Los Angeles zostaje skierowane do obwodu usługi ExpressRoute we wschodnich stanach USA, po czym przechodzi cały kontynent z powrotem na zachodnie wybrzeże. Przyczyna tego problemu jest podobna do pierwszej. Bez żadnej wskazówki sieć firmy Microsoft nie może rozróżnić, który prefiks klienta jest blisko wschodnich, a który blisko zachodnich stanów USA. Czasami wybiera nieprawidłową ścieżkę do biura w Los Angeles.

![Przypadek 2 dotyczący usługi ExpressRoute — suboptymalny routing od firmy Microsoft do klienta](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### <a name="solution-use-as-path-prepending"></a>Rozwiązanie: użyj dołączania ścieżki AS
Istnieją dwa rozwiązania tego problemu. Pierwsze z nich polega na tym, by po prostu anonsować prefiks lokalny biura w Los Angeles, 177.2.0.0/31, w obwodzie usługi ExpressRoute w stanach zachodnich, a prefiks lokalny biura w Nowym Jorku, 177.2.0.2/31, w obwodzie w stanach wschodnich. W wyniku tego istnieje tylko jedna ścieżka dla firmy Microsoft do połączenia się z każdym z biur. Nie ma żadnych niejednoznaczności i routing zostaje zoptymalizowany. Dla tego projektu należy przemyśleć strategię pracy awaryjnej. W przypadku, gdy ścieżka do firmy Microsoft za pośrednictwem usługi ExpressRoute zostanie uszkodzona, musisz zadbać o to, by usługa Exchange Online mogła nadal łączyć się z serwerami lokalnymi. 

Drugim rozwiązaniem jest dalsze anonsowanie obu prefiksów w obu obwodach usługi ExpressRoute i dodatkowo podawanie wskazówki z informacją, który prefiks jest blisko którego biura. Ponieważ firma Microsoft obsługuje ścieżkę AS BGP do wywołania, można skonfigurować ścieżkę AS dla prefiksu, aby wpłynąć na routing. W tym przykładzie można wydłużyć ścieżkę AS dla 172.2.0.0/31 we wschodnich stanach USA, aby firma Microsoft preferowała obwód usługi ExpressRoute w stanach zachodnich dla ruchu przeznaczonego dla tego prefiksu (dla naszej sieci ścieżka do tego prefiksu jest krótsza na zachodzie). Podobnie można wydłużyć ścieżkę AS dla 172.2.0.2/31 w zachodnich stanach USA, by firma Microsoft preferowała obwód usługi ExpressRoute w stanach wschodnich. Routing zostaje zoptymalizowany dla obu biur. W tym projekcie jeśli jeden obwód usługi ExpressRoute zostanie uszkodzony, usługa Exchange Online może nadal uzyskać dostęp do użytkownika za pośrednictwem innego obwodu usługi ExpressRoute i sieci WAN. 

> [!IMPORTANT]
> Firma Microsoft usuwa prywatne numery AS w ścieżce AS dla prefiksów odebranych w przypadku komunikacji równorzędnej między komputerami, gdy Komunikacja równorzędna jest używana jako numer prywatny. Musisz być elementem równorzędnym z publiczną usługą i dołączać publiczne jako cyfry w ścieżce AS, aby mieć wpływ na Routing komunikacji równorzędnej firmy Microsoft.
> 
> 

![Rozwiązanie przypadku 2 dotyczącego usługi ExpressRoute — używanie dołączania ścieżki AS](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

> [!NOTE]
> Choć przedstawione przykłady odnoszą się do komunikacji równorzędnej firmy Microsoft i publicznej komunikacji równorzędnej, to firma Microsoft nie obsługuje takich samych możliwości dla prywatnej komunikacji równorzędnej. Ponadto dołączanie ścieżki AS działa w obrębie pojedynczego obwodu usługi ExpressRoute i wpływa na wybór ścieżek podstawowej i pomocniczej.
> 
> 

## <a name="suboptimal-routing-between-virtual-networks"></a>Suboptymalny routing między sieciami wirtualnymi
Usługa ExpressRoute umożliwia skonfigurowanie usługi Virtual Network pod kątem komunikacji za pośrednictwem sieci wirtualnych przez połączenie ich z obwodem usługi ExpressRoute. W przypadku połączenia sieci wirtualnych z wieloma obwodami usługi ExpressRoute między sieciami wirtualnymi może wystąpić suboptymalny routing. Rozważmy przykład. Dostępne są dwa obwody usługi ExpressRoute: jeden w zachodnich stanach USA i jeden we wschodnich stanach USA. W każdym regionie znajdują się dwie sieci wirtualne. W jednej sieci wirtualnej są wdrożone serwery sieci Web, a w drugiej — serwery aplikacji. W celu zapewnienia nadmiarowości dwie sieci wirtualne w każdym regionie są połączone z lokalnym obwodem usługi ExpressRoute i zdalnym obwodem usługi ExpressRoute. Jak widać na poniższej ilustracji, każdą sieć wirtualną łączą dwie ścieżki prowadzące do drugiej sieci wirtualnej. W ramach połączenia sieci wirtualnych nie wiadomo, który obwód usługi ExpressRoute jest lokalny, a który zdalny. Ponieważ do równoważenia obciążenia ruchu między sieciami wirtualnymi jest używany routing wielościeżkowy z równym kosztem (ECMP, Equal-Cost-Multi-Path), część ruchu przepływa dłuższą ścieżką i jest kierowana do zdalnego obwodu usługi ExpressRoute.

![Przypadek 3 dotyczący usługi ExpressRoute — suboptymalny routing między sieciami wirtualnymi](./media/expressroute-optimize-routing/expressroute-case3-problem.png)

### <a name="solution-assign-a-high-weight-to-local-connection"></a>Rozwiązanie: przypisanie wysokiej wagi połączeniu lokalnemu
Rozwiązanie jest proste. Ponieważ wiadomo, gdzie znajdują się sieci wirtualne i obwody, można określić preferowaną ścieżkę dla poszczególnych sieci wirtualnych. Na potrzeby tego przykładu połączeniu lokalnemu jest przypisywana wyższa waga niż połączeniu zdalnemu (zobacz przykład konfiguracji [tutaj](expressroute-howto-linkvnet-arm.md#modify-a-virtual-network-connection)). Gdy sieć wirtualna z wieloma połączeniami odbierze prefiks drugiej sieci wirtualnej, w celu wysłania danych przeznaczonych dla tego prefiksu zostanie wybrane połączenie z najwyższą wagą.

![Rozwiązanie przypadku 3 dotyczącego usługi ExpressRoute — przypisanie wysokiej wagi połączeniu lokalnemu](./media/expressroute-optimize-routing/expressroute-case3-solution.png)

> [!NOTE]
> Jeśli korzystasz z kilku obwodów usługi ExpressRoute, możesz również zmieniać routing z sieci wirtualnej do sieci lokalnej, konfigurując wagę połączenia, zamiast stosowania dołączania ścieżki AS, które zostało opisane w drugim scenariuszu. Podczas określania trasy danych w przypadku wszystkich prefiksów waga połączenia ma zawsze pierwszeństwo przed długością ścieżki AS.
>
>
