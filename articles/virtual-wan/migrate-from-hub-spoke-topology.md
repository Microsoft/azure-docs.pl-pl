---
title: 'Architektura: Migrowanie do wirtualnej sieci WAN platformy Azure'
description: Dowiedz się, jak przeprowadzić migrację do wirtualnej sieci WAN platformy Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: cherylmc
ms.openlocfilehash: e6078ffcaaf98702bf809dfeb435cdaa0f9b5701
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777194"
---
# <a name="migrate-to-azure-virtual-wan"></a>Migrowanie do usługi Azure Virtual WAN

Wirtualna sieć WAN platformy Azure umożliwia firmom uproszczenie łączności globalnej w celu skorzystania z skali sieci globalnej firmy Microsoft. Ten artykuł zawiera szczegółowe informacje techniczne dotyczące firm, które mają zostać zmigrowane z istniejącej topologii koncentratora i gwiazdy zarządzanego przez klienta, do projektu korzystającego z zarządzanych przez firmę Microsoft koncentratorów wirtualnych sieci WAN.

Aby uzyskać informacje o korzyściach, które usługa Azure Virtual WAN umożliwia przedsiębiorstwom wdrażanie nowoczesnej sieci globalnej opartej na chmurze, zobacz [Globalna architektura sieci tranzytowej i wirtualna sieć WAN](virtual-wan-global-transit-network-architecture.md).

:::image type="content" source="./media/migrate-from-hub-spoke-topology/hub-spoke.png" alt-text="koncentrator i szprycha":::
**Ilustracja: wirtualna sieć WAN platformy Azure**

Model łączności usługi Azure Hub i szprych został przyjęty przez tysiące naszych klientów w celu wykorzystania domyślnego, przechodniego zachowania routingu sieci platformy Azure w celu tworzenia prostych i skalowalnych sieci w chmurze. Wirtualne sieci WAN platformy Azure bazują na tych pojęciach i wprowadzają nowe funkcje, które umożliwiają globalne topologie łączności, nie tylko między lokalizacjami lokalnymi i platformą Azure, ale również umożliwiają klientom wykorzystanie skali sieci firmy Microsoft w celu rozszerzenia istniejących sieci globalnych.

W tym artykule pokazano, jak migrować istniejące środowisko gwiazdy i gwiazdy zarządzane przez klienta do topologii opartej na wirtualnej sieci WAN platformy Azure.

## <a name="scenario"></a>Scenariusz

Contoso to globalna organizacja finansowa z biurami w Europie i Azji. Planują przeniesienie istniejących aplikacji z lokalnego centrum danych na platformę Azure i skompilowano projekt podstaw oparty na architekturze koncentratora i gwiazdy zarządzanej przez klienta, w tym sieci wirtualnych centrów regionalnych do łączności hybrydowej. W ramach przechodzenia do technologii opartych na chmurze zespół sieci został poddany do zagwarantowania, że ich łączność jest zoptymalizowana pod kątem rozwoju firmy.

Na poniższej ilustracji przedstawiono ogólny widok istniejącej sieci globalnej, w tym połączenie z wieloma regionami świadczenia usługi Azure.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png" alt-text="koncentrator i szprycha":::
**Ilustracja: istniejąca topologia sieci firmy Contoso**

Następujące punkty można zrozumieć z istniejącej topologii sieci:

* Topologia gwiazdy jest używana w wielu regionach, w tym obwodów usługi ExpressRoute na potrzeby łączności z powrotem do wspólnej prywatnej sieci rozległej (WAN).

* Niektóre z tych lokacji mają również tunele VPN bezpośrednio na platformie Azure, aby uzyskiwać dostęp do aplikacji hostowanych w chmurze.

## <a name="requirements"></a>Wymagania

Zespół sieci został poddany procesowi dostarczającemu globalny model sieci, który umożliwia obsługę migracji contoso do chmury i musi być zoptymalizowany w obszarach kosztów, skali i wydajności. Podsumowując, należy spełnić następujące wymagania:

* Zapewniaj zarówno CENTRALĄ, jak i biura oddziałów z zoptymalizowaną ścieżką do aplikacji hostowanych w chmurze.
* Usuń zależność od istniejących lokalnych centrów danych (DC) do zakończenia sieci VPN, zachowując następujące ścieżki łączności:
  * **Odgałęzienie do sieci wirtualnej**: podłączane przez sieć VPN aplikacje muszą mieć możliwość dostępu do aplikacji migrowanych do chmury w lokalnym regionie platformy Azure.
  * **Gałąź-do-Hub**: połączone urzędy sieci VPN muszą mieć możliwość dostępu do aplikacji migrowanych do chmury w zdalnym regionie platformy Azure.
  * **Odgałęzienie do gałęzi**: regionalne połączenia sieci VPN muszą być w stanie komunikować się ze sobą i ExpressRoute połączone witryny CENTRALĄ/DC.
  * **Odgałęzienie**do koncentratora: oddzielone globalnie sieci VPN połączone urzędy muszą być w stanie komunikować się ze sobą i wszystkimi ExpressRoute połączonymi lokacjami CENTRALĄ/DC.
  * **Rozgałęzienie do Internetu**: połączone Lokacje muszą być w stanie komunikować się z Internetem. Ten ruch musi być filtrowany i zarejestrowany.
  * **Sieć wirtualna-sieć VNET**: sieci wirtualne szprych w tym samym regionie muszą być w stanie komunikować się ze sobą.
  * Połączenia między sieciami wirtualnymi **i piastą: sieć**wirtualna szprych w różnych regionach musi być w stanie komunikować się ze sobą.
* Umożliwienie użytkownikom mobilnym contoso (laptop i telefon) uzyskiwania dostępu do zasobów firmy, a nie w sieci firmowej.

## <a name="azure-virtual-wan-architecture"></a><a name="architecture"></a>Architektura wirtualnej sieci WAN platformy Azure

Na poniższej ilustracji przedstawiono ogólny widok zaktualizowanej topologii docelowej przy użyciu wirtualnej sieci WAN platformy Azure, aby spełnić wymagania opisane w poprzedniej sekcji.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/vwan-architecture.png" alt-text="koncentrator i szprycha":::
**Ilustracja: architektura wirtualnej sieci WAN platformy Azure**

Podsumowanie:

* CENTRALĄ w Europie pozostanie nieExpressRoutee, Europa lokalny kontroler domeny jest w pełni migrowany do platformy Azure i teraz zlikwidowany.
* Azja i CENTRALĄ pozostaną połączone z prywatną siecią WAN. Wirtualna sieć WAN platformy Azure została teraz użyta do rozszerzenia lokalnej sieci operatora i zapewnienia łączności globalnej.
* Wirtualne centra sieci WAN platformy Azure wdrożone w regionach Europa Zachodnia i południowe Azja Wschodnia platformy Azure w celu zapewnienia centrum łączności dla urządzeń z ExpressRoute i sieci VPN.
* Koncentratory zapewniają również zakończenie sieci VPN dla użytkowników mobilnych w wielu typach klientów korzystających z łączności OpenVPN z siecią globalnej siatki, co umożliwia dostęp do nie tylko aplikacji migrowanych do platformy Azure, ale również wszelkich zasobów pozostających w środowisku lokalnym.
* Łączność z Internetem dla zasobów w sieci wirtualnej udostępnianych przez wirtualną sieć WAN platformy Azure.

Łączność z Internetem dla witryn zdalnych zapewniana również przez wirtualną sieć WAN platformy Azure. Lokalne zagadnień internetowe obsługiwane przez integrację partnerów w celu zoptymalizowanego dostępu do usług SaaS, takich jak Microsoft 365.

## <a name="migrate-to-virtual-wan"></a>Migrowanie do usługi Virtual WAN

W tej sekcji przedstawiono różne kroki migracji do wirtualnej sieci WAN platformy Azure.

### <a name="step-1-single-region-customer-managed-hub-and-spoke"></a>Krok 1. centrum zarządzane przez klienta w jednym regionie i szprych

Na poniższej ilustracji przedstawiono topologię jednego regionu dla firmy Contoso przed wdrożeniem wirtualnej sieci WAN platformy Azure:

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure1.png" alt-text="koncentrator i szprycha" (IaaS).
* Usługi zapory IP/routingu są udostępniane przez urządzenie wirtualne sieci innej firmy, co umożliwia routing protokołu IP dla satelity.
* Internetowe usługi przychodzące/wychodzące, w tym Application Gateway platformy Azure dla przychodzących żądań HTTPS i usług serwera proxy innych firm działające na maszynach wirtualnych na potrzeby filtrowanego dostępu wychodzącego do zasobów internetowych.
* Brama sieci wirtualnej ExpressRoute i VPN na potrzeby łączności z sieciami lokalnymi.

### <a name="step-2-deploy-virtual-wan-hubs"></a>Krok 2. wdrażanie koncentratorów wirtualnych sieci WAN

Wdróż Wirtualne Centrum sieci WAN w każdym regionie. Skonfiguruj Wirtualne Centrum sieci WAN przy użyciu sieci VPN i funkcji ExpressRoute zgodnie z opisem w następujących artykułach:

* [Samouczek: tworzenie połączenia lokacja-lokacja przy użyciu usługi Azure Virtual WAN](virtual-wan-site-to-site-portal.md)
* [Samouczek: Tworzenie skojarzenia ExpressRoute przy użyciu wirtualnej sieci WAN platformy Azure](virtual-wan-expressroute-portal.md)

> [!NOTE]
> Wirtualna sieć WAN platformy Azure musi używać standardowej jednostki SKU do włączania niektórych ścieżek ruchu pokazanych w tym artykule.
>

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure2.png" alt-text="koncentrator i szprycha":::
**Rysunek 2: zarządzane przez klienta centrum-i-szprycha do migracji wirtualnej sieci WAN**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>Krok 3. Łączenie z witrynami zdalnymi (ExpressRoute i VPN) z wirtualną siecią WAN

Podłącz wirtualne koncentrator sieci WAN do istniejących obwodów usługi ExpressRoute i skonfiguruj połączenia sieci VPN typu lokacja-lokacja za pośrednictwem Internetu w oddziałach zdalnych.

> [!NOTE]
> Obwody usługi Express Routes muszą zostać uaktualnione do typu SKU Premium, aby połączyć się z koncentratorem wirtualnej sieci WAN.
>

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure3.png" alt-text="koncentrator i szprycha":::
**Rysunek 3: zarządzane przez klienta centrum i współdziałanie z wirtualną migracją sieci WAN**

W tym momencie lokalne urządzenie sieciowe rozpocznie odbieranie tras odzwierciedlających przestrzeń adresową IP przypisaną do sieci wirtualnej koncentratora zarządzanego przez sieć WAN. Rozgałęzienia połączonej sieci VPN na tym etapie zostaną wyświetlone dwie ścieżki do wszystkich istniejących aplikacji w sieciach wirtualnych szprych. Te urządzenia powinny być skonfigurowane tak, aby w dalszym ciągu używać tunelu do koncentratora zarządzanego przez klienta w celu zapewnienia symetrycznego routingu podczas fazy przejścia.

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>Krok 4. Testowanie łączności hybrydowej za pośrednictwem wirtualnej sieci WAN

Przed rozpoczęciem korzystania z zarządzanego wirtualnego koncentratora sieci WAN na potrzeby łączności produkcyjnej zalecamy skonfigurowanie sieci wirtualnej szprychy testowej i wirtualnego połączenia z siecią wirtualną sieci WAN. Sprawdź, czy połączenia z tym środowiskiem testowym działają przez ExpressRoute i sieci VPN między lokacjami, przed przejściem do następnego kroku.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure4.png" alt-text="koncentrator i szprycha":::
**Rysunek 4: zarządzane przez klienta centrum-i-szprycha do migracji wirtualnej sieci WAN**

Na tym etapie ważne jest, aby rozpoznać, że zarówno oryginalna Sieć wirtualna koncentratora zarządzana przez klienta, jak i Nowa wirtualna koncentrator sieci WAN, są połączone z tym samym obwodem usługi ExpressRoute. Ze względu na to mamy ścieżkę ruchu, która może być używana do włączania szprych w obu środowiskach do komunikacji. Na przykład ruch z szprychy, który jest dołączony do sieci wirtualnej centrum zarządzanego przez klienta, przejdzie do MSEE urządzeń używanych do obwodu usługi ExpressRoute, aby dotrzeć do dowolnego szprych połączonego za pośrednictwem połączenia sieci wirtualnej z nowym wirtualnym koncentratorem sieci WAN. Pozwala to na przemieszczenie etapowe migracji szprych w kroku 5.

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>Krok 5. przejście do koncentratora wirtualnej sieci WAN

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure5.png" alt-text="koncentrator i szprycha":::
**Rysunek 5: zarządzane przez klienta centrum i współdziałanie z wirtualną migracją sieci WAN**

**a**. Usuń istniejące połączenia komunikacji równorzędnej z sieci wirtualnych szprych do starego centrum zarządzanego przez klienta. Dostęp do aplikacji w sieciach wirtualnych szprych jest niedostępny do momentu ukończenia kroków a-c.

**b**. Połącz sieci wirtualne szprych z koncentratorem wirtualnej sieci WAN za pośrednictwem połączeń sieci wirtualnej.

**c**. Usuń wszystkie trasy zdefiniowane przez użytkownika (UDR) wcześniej używane w sieciach wirtualnych szprych w przypadku komunikacji między satelitami. Ta ścieżka jest teraz włączona przez dynamiczny Routing dostępny w koncentratorze sieci wirtualnej.

**d**. Istniejące bramy ExpressRoute i VPN w centrum zarządzanym przez klienta zostały teraz zlikwidowane, aby zezwolić na następny krok (e).

**e**. Podłącz stare centrum zarządzane przez klienta (koncentrator sieci wirtualnej) do wirtualnego koncentratora sieci WAN za pośrednictwem nowego połączenia sieci wirtualnej.

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>Krok 6. stare centrum staną się usługami udostępnionymi szprych

Przeprojektowano teraz sieć platformy Azure w celu udostępnienia koncentratora wirtualnego sieci WAN centralnym punktem w naszej nowej topologii.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure6.png" alt-text="koncentrator i szprycha":::
**Rysunek 6: zarządzane przez klienta centrum i współdziałanie z wirtualną migracją sieci WAN**

Ponieważ wirtualne Centrum sieci WAN jest zarządzaną jednostką i nie zezwala na wdrażanie zasobów niestandardowych, takich jak maszyny wirtualne, blok usługi udostępnione teraz istnieje jako sieć wirtualna szprych i udostępnia funkcje, takie jak ruch internetowy za pośrednictwem platformy Azure Application Gateway lub sieciowego zwirtualizowanej sieci. Ruch między środowiskiem usług udostępnionych i maszynami wirtualnymi zaplecza teraz przesyła koncentrator zarządzany przez wirtualną sieć WAN.

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>Krok 7. Optymalizacja łączności lokalnej w celu pełnego wykorzystania wirtualnej sieci WAN

Na tym etapie firma Contoso przede wszystkim ukończyła migrację aplikacji firmowych do Microsoft Cloud z uwzględnieniem tylko kilku starszych aplikacji w lokalnym kontrolerze domeny.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure7.png" alt-text="koncentrator i szprycha":::
**Rysunek 7: zarządzane przez klienta centrum i współdziałanie z wirtualną migracją sieci WAN**

Aby skorzystać z pełnej funkcjonalności wirtualnej sieci WAN platformy Azure, firma Contoso decyduje o zlikwidowaniu starszych lokalnych połączeń sieci VPN. Wszystkie gałęzie w celu uzyskania dostępu do sieci CENTRALĄ lub DC mogą przekierować sieć globalną firmy Microsoft przy użyciu wbudowanego routingu tranzytowego wirtualnej sieci WAN platformy Azure.

> [!NOTE]
> ExpressRoute Global Reach jest wymagana dla klientów, którzy chcą korzystać z sieci szkieletowej firmy Microsoft w celu zapewnienia ExpressRoute do ExpressRouteego przesyłania (nie przedstawiono na rysunku 7).
>

## <a name="end-state-architecture-and-traffic-paths"></a>Architektura stanu końcowego i ścieżki ruchu

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure8.png" alt-text="koncentrator i szprycha":::
**Rysunek: wirtualna sieć WAN z podwójnym regionem**

Ta sekcja zawiera podsumowanie, w jaki sposób Ta topologia spełnia pierwotne wymagania, patrząc na kilka przykładowych przepływów ruchu.

### <a name="path-1"></a>Ścieżka 1

Ścieżka 1 pokazuje przepływ ruchu z połączonej gałęzi sieci VPN S2S w Azji do sieci wirtualnej platformy Azure w regionie Azja Wschodnia Południowo-Wschodnia.

Ruch jest kierowany w następujący sposób:

* Gałąź Azja jest połączona za pośrednictwem odpornych tuneli z włączonym protokołem BGP w Republice Południowej Azja Wschodnia wirtualnej sieci WAN.

* Azja wirtualnego centrum WAN kieruje ruch lokalnie do połączonej sieci wirtualnej.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow1.png" alt-text="koncentrator i szprycha":::

### <a name="path-2"></a>Ścieżka 2

Ścieżka 2 przedstawia przepływ ruchu z ExpressRoute połączonego Europejskiego CENTRALĄ do sieci wirtualnej platformy Azure w regionie Południowo Azja Wschodnia.

Ruch jest kierowany w następujący sposób:

* CENTRALĄ Europejski jest podłączony za pośrednictwem obwodu ExpressRoute w warstwie Premium do wirtualnego koncentratora sieci WAN Europa Zachodnia.

* Wirtualna sieć WAN między koncentratorem a globalnym połączeniem umożliwia przesyłanie ruchu do sieci wirtualnej połączonej w regionie zdalnym.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow2.png" alt-text="koncentrator i szprycha":::

### <a name="path-3"></a>Ścieżka 3

Ścieżka 3 przedstawia przepływ ruchu z terenu Azji lokalnego kontrolera domeny podłączonego do prywatnej sieci WAN do Europejskiej gałęzi połączonej S2S.

Ruch jest kierowany w następujący sposób:

* Azja jest połączona z lokalnym prywatnym przewoźnikiem sieci WAN.

* Obwód ExpressRoutey lokalnie kończy się w prywatnej sieci WAN, łączy się z wirtualnym koncentratorem sieci WAN w południowej Azja Wschodnia.

* Wirtualna sieć WAN między koncentratorem a globalnym połączeniem umożliwia przesyłanie ruchu.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow3.png" alt-text="koncentrator i szprycha":::

### <a name="path-4"></a>Ścieżka 4

Ścieżka 4 przedstawia przepływ ruchu z sieci wirtualnej platformy Azure w regionie Południowo Azja Wschodnia do sieci wirtualnej platformy Azure w regionie Europa Zachodnia.

Ruch jest kierowany w następujący sposób:

* Łączność globalna między koncentratorem wirtualnej sieci WAN umożliwia natywne tranzyt wszystkich połączonych sieci wirtualnych platformy Azure bez dalszej konfiguracji użytkownika.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow4.png" alt-text="koncentrator i szprycha":::

### <a name="path-5"></a>Ścieżka 5

Ścieżka 5 przedstawia przepływ ruchu z użytkowników mobilnych sieci VPN (P2S) do sieci wirtualnej platformy Azure w regionie Europa Zachodnia.

Ruch jest kierowany w następujący sposób:

* Użytkownicy laptopów i urządzeń przenośnych używają klienta OpenVPN do przezroczystego połączenia w programie z bramą sieci VPN P2S w regionie Europa Zachodnia.

* Wirtualne centrum WAN Europa Zachodnia kieruje ruch lokalnie do połączonej sieci wirtualnej.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow5.png" alt-text="koncentrator i szprycha":::

## <a name="security-and-policy-control-via-azure-firewall"></a>Zabezpieczenia i kontrola zasad za pośrednictwem zapory platformy Azure

Firma Contoso ma teraz zweryfikowane połączenie między wszystkimi gałęziami i sieci wirtualnych zgodnie z wymaganiami omówionymi wcześniej w tym artykule. Aby spełnić wymagania dotyczące kontroli zabezpieczeń i izolacji sieci, muszą one nadal oddzielić ruch i rejestrować go za pośrednictwem sieci centrum. Wcześniej ta funkcja była wykonywana przez wirtualne urządzenie sieciowe (urządzenie WUS). Firma Contoso chce również zlikwidować istniejące usługi proxy i korzystać z natywnych usług platformy Azure na potrzeby filtrowania ruchu internetowego.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/security-policy.png" alt-text="koncentrator i szprycha":::
**Ilustracja: Zapora platformy Azure w wirtualnej sieci WAN (zabezpieczony koncentrator wirtualny)**

Poniższe kroki wysokiego poziomu są wymagane do wprowadzenia zapory platformy Azure do wirtualnych koncentratorów sieci WAN w celu zapewnienia ujednoliconego punktu kontroli zasad. Aby uzyskać więcej informacji na temat tego procesu i koncepcji bezpiecznych centrów wirtualnych, zobacz [Menedżer zapory platformy Azure](../firewall-manager/index.yml).

1. Utwórz zasady zapory platformy Azure.
2. Połącz zasady zapory z usługą Azure Virtual WAN Hub. Ten krok pozwala istniejącemu wirtualnemu koncentratorowi sieci WAN działać jako zabezpieczone centrum wirtualne i wdrażać wymagane zasoby zapory platformy Azure.

> [!NOTE]
> Istnieją ograniczenia dotyczące używania bezpiecznych koncentratorów wirtualnych, w tym ruchu między regionami. Aby uzyskać więcej informacji, zobacz temat [znane problemy w Menedżerze zapory](../firewall-manager/overview.md#known-issues).
>

Poniższe ścieżki pokazują ścieżki łączności włączone przy użyciu zabezpieczonych wirtualnych centrów platformy Azure:

### <a name="path-6"></a>Ścieżka 6

Ścieżka 6 pokazuje bezpieczny przepływ ruchu między sieci wirtualnych w tym samym regionie.

Ruch jest kierowany w następujący sposób:

* Sieci wirtualne połączone z tym samym zabezpieczonym koncentratorem wirtualnym teraz kierują ruch do za pośrednictwem zapory platformy Azure.

* Zapora platformy Azure może stosować zasady do tych przepływów.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow6.png" alt-text="koncentrator i szprycha":::

### <a name="path-7"></a>Ścieżka 7

Ścieżka 7 przedstawia przepływ ruchu z sieci wirtualnej platformy Azure do Internetu lub usługi zabezpieczeń innych firm.

Ruch jest kierowany w następujący sposób:

* Sieci wirtualne połączone z bezpiecznym koncentratorem wirtualnym mogą wysyłać ruch do publicznych miejsc w Internecie przy użyciu bezpiecznego centrum jako centralnego punktu dostępu do Internetu.

* Ten ruch może być filtrowany lokalnie przy użyciu reguł FQDN zapory platformy Azure lub wysyłany do usługi zabezpieczeń innej firmy w celu przeprowadzenia inspekcji.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow7.png" alt-text="koncentrator i szprycha":::

### <a name="path-8"></a>Ścieżka 8

Ścieżka 8 przedstawia przepływ ruchu z usługi zabezpieczeń z gałęzi do Internetu lub innej firmy.

Ruch jest kierowany w następujący sposób:

* Gałęzie połączone z bezpiecznym koncentratorem wirtualnym mogą wysyłać ruch do publicznych miejsc docelowych w Internecie przy użyciu bezpiecznego centrum jako centralnego punktu dostępu do Internetu.

* Ten ruch może być filtrowany lokalnie przy użyciu reguł FQDN zapory platformy Azure lub wysyłany do usługi zabezpieczeń innej firmy w celu przeprowadzenia inspekcji.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow8.png" alt-text="koncentrator i szprycha":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wirtualnej sieci WAN platformy Azure](virtual-wan-about.md).
