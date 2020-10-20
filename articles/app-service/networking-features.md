---
title: Funkcje sieci
description: Dowiedz się więcej o funkcjach sieciowych w Azure App Service, a także jakie funkcje potrzebne dla sieci muszą mieć zabezpieczenia lub funkcjonalność.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 10/18/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 860b1ac1713ac7afb7db2643d68974b399b5236b
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207062"
---
# <a name="app-service-networking-features"></a>App Service funkcje sieciowe

Aplikacje w Azure App Service można wdrożyć na wiele sposobów. Domyślnie aplikacje hostowane App Service są bezpośrednio dostępne dla Internetu i mogą uzyskiwać dostęp tylko do hostowanych punktów końcowych z Internetu. Wiele aplikacji klienta musi jednak kontrolować ruch sieciowy przychodzący i wychodzący. Istnieje kilka funkcji dostępnych w App Service, aby zaspokoić te wymagania. Wyzwanie polega na Poznaniu funkcji, która powinna być używana do rozwiązywania danego problemu. Ten dokument ma pomóc klientom w ustaleniu, która funkcja powinna być używana w oparciu o przykładowe przypadki użycia.

Istnieją dwa podstawowe typy wdrożeń dla Azure App Service. Istnieje usługa publiczna z wieloma dzierżawcami, która umożliwia hostowanie App Service planów w jednostkach SKU z cenami bezpłatna, współdzielona, podstawowa, standardowa, Premium, Premiumv2 i Premiumv3. Następnie istnieje pojedynczy dzierżawc App Service Environment (ASE), który hostuje izolowaną jednostkę SKU App Service plany bezpośrednio w Virtual Network platformy Azure. Używane funkcje różnią się w zależności od tego, czy jesteś w usłudze z wieloma dzierżawcami, czy też w środowisku ASE. 

## <a name="multi-tenant-app-service-networking-features"></a>Wielodostępne funkcje sieciowe App Service 

Azure App Service jest systemem rozproszonym. Role obsługujące przychodzące żądania HTTP/HTTPS są nazywane frontonami. Role obsługujące obciążenie klienta są nazywane pracownikami. Wszystkie role w App Service wdrożenia istnieją w sieci z wieloma dzierżawcami. Ponieważ istnieje wielu różnych klientów w tej samej App Serviceej jednostce skalowania, nie można połączyć sieci App Service bezpośrednio z siecią. Zamiast łączenia sieci potrzebujemy funkcji do obsługi różnych aspektów komunikacji aplikacji. Funkcje, które obsługują żądania do aplikacji, nie mogą być używane do rozwiązywania problemów podczas wykonywania wywołań z aplikacji. Podobnie funkcje, które rozwiązują problemy dotyczące wywołań z aplikacji, nie mogą być używane do rozwiązywania problemów z aplikacją.  

| Funkcje przychodzące | Funkcje wychodzące |
|---------------------|-------------------|
| Adres przypisany do aplikacji | Połączenia hybrydowe |
| Ograniczenia dostępu | Integracja sieci wirtualnej wymagana przez bramę |
| Punkty końcowe usługi | Integracja z siecią wirtualną |

O ile nie określono inaczej, wszystkie funkcje mogą być używane razem. Możesz mieszać funkcje, aby rozwiązać różne problemy.

## <a name="use-case-and-features"></a>Przypadek użycia i funkcje

W przypadku dowolnego danego przypadku użycia może istnieć kilka sposobów rozwiązania problemu.  Odpowiednia funkcja jest czasami spowodowana tylko przypadkiem użycia. Następujące przypadki użycia ruchu przychodzącego zasugerują sposób użycia App Service funkcje sieciowe do rozwiązywania problemów związanych z kontrolowaniem ruchu kierowanego do aplikacji. 
 
| Przychodzące przypadki użycia | Cecha |
|---------------------|-------------------|
| Obsługa protokołu SSL opartego na protokole IP dla aplikacji | adres przypisany do aplikacji |
| Nieudostępniony, dedykowany adres przychodzący dla aplikacji | adres przypisany do aplikacji |
| Ograniczanie dostępu do aplikacji z zestawu dobrze zdefiniowanych adresów | Ograniczenia dostępu |
| Ograniczanie dostępu do mojej aplikacji z zasobów w sieci wirtualnej | Punkty końcowe usługi </br> ILB ASE </br> Prywatne punkty końcowe |
| Uwidacznianie mojej aplikacji w prywatnym adresie IP w mojej sieci wirtualnej | ILB ASE </br> Prywatne punkty końcowe </br> prywatny adres IP dla ruchu przychodzącego na Application Gateway z punktami końcowymi usługi |
| Ochrona mojej aplikacji za pomocą zapory aplikacji sieci Web (WAF) | Application Gateway i ILB ASE </br> Application Gateway z prywatnymi punktami końcowymi </br> Application Gateway z punktami końcowymi usługi </br> Moje drzwi platformy Azure z ograniczeniami dostępu |
| Równoważenie obciążenia ruchu do moich aplikacji w różnych regionach | Moje drzwi platformy Azure z ograniczeniami dostępu | 
| Równoważenie obciążenia ruchu w tym samym regionie | [Usługa Application Gateway z punktami końcowymi usługi][appgwserviceendpoints] | 

Następujące wychodzące przypadki użycia sugerują, jak używać funkcji sieciowych App Service do rozwiązywania potrzeby dostępu wychodzącego dla aplikacji. 

| Wychodzące przypadki użycia | Cecha |
|---------------------|-------------------|
| Dostęp do zasobów w Virtual Network platformy Azure w tym samym regionie | Integracja z siecią wirtualną </br> ASE |
| Dostęp do zasobów w usłudze Azure Virtual Network w innym regionie | Integracja sieci wirtualnej wymagana przez bramę </br> Środowisko ASE i Komunikacja równorzędna sieci wirtualnych |
| Dostęp do zasobów zabezpieczonych za pomocą punktów końcowych usługi | Integracja z siecią wirtualną </br> ASE |
| Dostęp do zasobów w sieci prywatnej, które nie są połączone z platformą Azure | Połączenia hybrydowe |
| Dostęp do zasobów między obwodymi usługi ExpressRoute | Integracja z siecią wirtualną </br> ASE | 
| Zabezpiecz ruch wychodzący z aplikacji sieci Web | Integracja sieci wirtualnej i sieciowe grupy zabezpieczeń </br> ASE | 
| Kierowanie ruchu wychodzącego z aplikacji sieci Web | Tabele integracji i routingu sieci wirtualnej </br> ASE | 


### <a name="default-networking-behavior"></a>Domyślne zachowanie sieci

Jednostki skalowania Azure App Service obsługują wielu klientów w każdym wdrożeniu. W planach bezpłatna i współdzielona jednostka SKU hostuje obciążenia klientów w przypadku wielu dzierżawców. Plany podstawowe i powyższe są przeznaczone do obsługi obciążeń klientów przeznaczonych tylko do jednego planu App Service (ASP). Jeśli masz plan App Service w warstwie Standardowa, wszystkie aplikacje w tym planie zostaną uruchomione na tym samym procesie roboczym. W przypadku skalowania procesu roboczego wszystkie aplikacje znajdujące się w tej ASP zostaną zreplikowane w nowym procesie roboczym dla każdego wystąpienia w środowisku ASP. 

#### <a name="outbound-addresses"></a>Adresy wychodzące

Maszyny wirtualne procesu roboczego są podzielone na dużą część przez App Service plany cenowe. Wszystkie wersje bezpłatna, współdzielona, podstawowa, standardowa i Premium używają tego samego typu maszyn wirtualnych procesu roboczego. Premiumv2 znajduje się na innym typie maszyny wirtualnej. Premiumv3 jest jeszcze innego typu maszyny wirtualnej. W przypadku każdej zmiany rodziny maszyn wirtualnych istnieje inny zestaw adresów wychodzących. W przypadku skalowania z warstwy Standardowa do Premiumv2 adresy wychodzące zmienią się. W przypadku skalowania z Premiumv2 do Premiumv3, Twoje adresy wychodzące zmienią się. Istnieją pewne starsze jednostki skalowania, które zmieniają zarówno adresy przychodzące, jak i wychodzące w przypadku skalowania z warstwy Standardowa do Premiumv2. Istnieje wiele adresów używanych do wykonywania wywołań wychodzących. Adresy wychodzące używane przez aplikację do wykonywania wywołań wychodzących są wyświetlane we właściwościach aplikacji. Te adresy są współużytkowane przez wszystkie aplikacje działające w ramach tej samej rodziny maszyn wirtualnych procesu roboczego w ramach tego App Service wdrożenia. Jeśli chcesz zobaczyć wszystkie możliwe adresy, których aplikacja może używać w tej jednostce skalowania, istnieje inna właściwość o nazwie possibleOutboundAddresses, która będzie zawierać listę. 

![Właściwości aplikacji](media/networking-features/app-properties.png)

App Service ma wiele punktów końcowych, które są używane do zarządzania usługą.  Te adresy są publikowane w osobnym dokumencie i znajdują się również w tagu usługi AppServiceManagement IP. Tag AppServiceManagement jest używany tylko w przypadku App Service Environment, w którym należy zezwolić na ten ruch. Adresy przychodzące App Service są śledzone w tagu usługi IP AppService. Nie istnieje tag usługi IP zawierający adresy wychodzące używane przez App Service. 

![App Service diagramy przychodzące i wychodzące](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Adres przypisany do aplikacji 

Funkcja adresów przypisanych do aplikacji jest offshootem protokołu SSL opartego na protokole IP i jest dostępna przez skonfigurowanie protokołu SSL w aplikacji. Ta funkcja może być używana dla wywołań SSL opartych na protokole IP, ale może również służyć do nadawania aplikacji adresu, który ma tylko ten adres. 

![Diagram adresów przypisanych do aplikacji](media/networking-features/app-assigned-address.png)

W przypadku korzystania z adresu przypisanego do aplikacji ruch nadal odbywa się przez te same role frontonu, które obsługują cały ruch przychodzący do jednostki skalowania App Service. Adres przypisany do aplikacji jest jednak używany tylko przez aplikację. W przypadku tej funkcji przypadki użycia są następujące:

* Obsługa protokołu SSL opartego na protokole IP dla aplikacji
* Ustaw dedykowany adres dla aplikacji, która nie jest udostępniana innym osobom

Możesz dowiedzieć się, jak ustawić adres w aplikacji za pomocą samouczka na temat [dodawania certyfikatu TLS/SSL w Azure App Service][appassignedaddress]. 

### <a name="access-restrictions"></a>Ograniczenia dostępu 

Funkcja ograniczenia dostępu umożliwia filtrowanie żądań **przychodzących** na podstawie źródłowego adresu IP. Akcja filtrowania odbywa się w rolach frontonu, które są nadrzędne od ról procesu roboczego, w których są uruchomione aplikacje. Ponieważ role frontonu są nadrzędne dla procesów roboczych, możliwość ograniczenia dostępu może być traktowana jako ochrona na poziomie sieci dla aplikacji. Funkcja ta umożliwia tworzenie listy bloków dozwolonych i zablokowanych adresów, które są oceniane w kolejności priorytetów. Jest podobna do sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń), która istnieje w sieci platformy Azure.  Tej funkcji można użyć w środowisku ASE lub w usłudze wielu dzierżawców. Gdy jest używany z ILB ASE, można ograniczyć dostęp z bloków adresów prywatnych.

![Ograniczenia dostępu](media/networking-features/access-restrictions.png)

Funkcja ograniczeń dostępu pomaga w scenariuszach, w których chcesz ograniczyć adresy IP, które mogą być używane do uzyskiwania dostępu do aplikacji. Wśród przypadków użycia tej funkcji są następujące:

* Ograniczanie dostępu do aplikacji z zestawu dobrze zdefiniowanych adresów 
* Ogranicz dostęp do sieci za pomocą usługi równoważenia obciążenia, takiej jak frontony platformy Azure. Jeśli chcesz zablokować ruch przychodzący do platformy Azure, utwórz reguły zezwalające na ruch z 147.243.0.0/16 i 2a01:111:2050::/44. 

![Ograniczenia dostępu z przednim Drzwiem](media/networking-features/access-restrictions-afd.png)

Jeśli chcesz zablokować dostęp do aplikacji, tak aby można ją było uzyskać tylko z zasobów w ramach usługi Azure Virtual Network (VNet), musisz mieć statyczny adres publiczny, na którym znajduje się źródło w sieci wirtualnej. Jeśli zasoby nie mają adresu publicznego, należy zamiast tego użyć funkcji punktów końcowych usługi. Dowiedz się, jak włączyć tę funkcję w samouczku dotyczącym [konfigurowania ograniczeń dostępu][iprestrictions].

### <a name="service-endpoints"></a>Punkty końcowe usługi

Punkty końcowe usługi umożliwiają zablokowanie dostępu **przychodzącego** do aplikacji w taki sposób, że adres źródłowy musi pochodzić z zestawu podsieci, które zostały wybrane. Ta funkcja działa w połączeniu z ograniczeniami dostępu do adresów IP. Punkty końcowe usługi nie są zgodne z debugowaniem zdalnym. Aby używać zdalnego debugowania z aplikacją, klient nie może znajdować się w podsieci z włączonymi punktami końcowymi usługi. Punkty końcowe usługi są ustawiane w tym samym środowisku użytkownika co ograniczenia dostępu do adresów IP. Można utworzyć listę dozwolonych/zablokowanych reguł dostępu, które zawierają adresy publiczne, a także podsieci w sieci wirtualnych. Ta funkcja obsługuje takie scenariusze, jak:

![punkty końcowe usługi](media/networking-features/service-endpoints.png)

* Konfigurowanie Application Gateway przy użyciu aplikacji do blokowania ruchu przychodzącego do aplikacji
* Ograniczanie dostępu do aplikacji do zasobów w sieci wirtualnej. Może to obejmować maszyny wirtualne, środowisk ASE, a nawet inne aplikacje korzystające z integracji sieci wirtualnej. 

![punkty końcowe usługi z bramą aplikacji](media/networking-features/service-endpoints-appgw.png)

Więcej informacji na temat konfigurowania punktów końcowych usługi przy użyciu aplikacji można znaleźć w samouczku dotyczącym [konfigurowania ograniczeń dostępu do punktu końcowego usługi][serviceendpoints]

### <a name="private-endpoints"></a>Prywatne punkty końcowe

Prywatny punkt końcowy to interfejs sieciowy, który łączy Cię prywatnie i bezpiecznie z Twoją aplikacją internetową za pomocą prywatnego linku platformy Azure. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, efektywnie łącząc aplikację sieci Web z siecią wirtualną. Ta funkcja jest tylko dla przepływów **przychodzących** do aplikacji sieci Web.
[Używanie prywatnych punktów końcowych dla aplikacji internetowej platformy Azure][privateendpoints]

Prywatne punkty końcowe umożliwiają obsługę scenariuszy, takich jak:

* Ograniczanie dostępu do mojej aplikacji z zasobów w sieci wirtualnej 
* Uwidacznianie mojej aplikacji w prywatnym adresie IP w mojej sieci wirtualnej 
* Ochrona mojej aplikacji za pomocą WAF 

Prywatne punkty końcowe uniemożliwiają eksfiltracji danych, ponieważ Jedyną czynnością, którą można osiągnąć w prywatnym punkcie końcowym, jest aplikacja, z którą jest skonfigurowana. 
 
### <a name="hybrid-connections"></a>Połączenia hybrydowe

App Service Połączenia hybrydowe umożliwia aplikacjom wykonywanie wywołań **wychodzących** do określonych punktów końcowych TCP. Punkt końcowy może być lokalny w sieci wirtualnej lub w dowolnym miejscu, który zezwala na ruch wychodzący do platformy Azure na porcie 443. Ta funkcja wymaga zainstalowania agenta przekazywania o nazwie Menedżer połączeń hybrydowych (HCM) na hoście z systemem Windows Server 2012 lub nowszym. HCM musi mieć możliwość uzyskania dostępu Azure Relay na porcie 443. HCM można pobrać z poziomu interfejsu użytkownika Połączenia hybrydowe App Service w portalu. 

![Połączenia hybrydowe przepływ sieci](media/networking-features/hybrid-connections.png)

Funkcja Połączenia hybrydowe App Service jest oparta na możliwościach Azure Relay Połączenia hybrydowe. App Service używa wyspecjalizowanej formy funkcji, która obsługuje tylko wykonywanie wywołań wychodzących z aplikacji do hosta i portu TCP. Ten host i port muszą zostać rozwiązane tylko na hoście, na którym zainstalowano HCM. Gdy aplikacja, w App Service, przeszukiwania DNS na hoście i porcie zdefiniowanym w połączeniu hybrydowym, ruch jest automatycznie przekierowywany, aby przechodzić przez połączenie hybrydowe i Menedżer połączeń hybrydowych. Aby dowiedzieć się więcej na temat Połączenia hybrydowe, zapoznaj się z dokumentacją [App Service połączenia hybrydowe][hybridconn]

Ta funkcja jest często używana do:

* Dostęp do zasobów w sieciach prywatnych, które nie są połączone z platformą Azure za pomocą sieci VPN lub ExpressRoute
* Obsługa podnoszenia i przemieszczania aplikacji lokalnych w celu App Service bez konieczności przenoszenia również pomocniczych baz danych  
* Bezpiecznie zapewniają dostęp do jednego hosta i portu na połączenie hybrydowe. Większość funkcji sieciowych otwiera dostęp do sieci i z Połączenia hybrydowe masz tylko jeden host i port, do których można się połączyć.
* Scenariusze pokrywania nieobjęte innymi metodami łączności wychodzącej
* Wykonaj programowanie w App Service, w którym aplikacje mogą łatwo korzystać z zasobów lokalnych 

Ponieważ funkcja umożliwia dostęp do zasobów lokalnych bez otworu zapory przychodzącej, jest to popularne dla deweloperów. Inne funkcje sieci wychodzącej App Service są powiązane z usługą Azure Virtual Network. Połączenia hybrydowe nie ma zależności między przechodzeniem do sieci wirtualnej i może służyć do szerszej gamy potrzeb sieciowych. Należy pamiętać, że funkcja Połączenia hybrydowe App Service nie ma znaczenia ani nie wie, co wykonujesz na jego podstawie. Oznacza to, że można go użyć w celu uzyskania dostępu do bazy danych, usługi sieci Web lub dowolnego gniazda TCP na komputerze typu mainframe. Funkcja zasadniczo tuneluje pakiety TCP. 

Chociaż Połączenia hybrydowe są popularne do celów deweloperskich, również są także używane w wielu aplikacjach produkcyjnych. Doskonale nadaje się do uzyskiwania dostępu do usługi sieci Web lub bazy danych, ale nie jest ona odpowiednia dla sytuacji związanych z tworzeniem wielu połączeń. 

### <a name="gateway-required-vnet-integration"></a>Integracja sieci wirtualnej wymagana przez bramę 

Brama wymagana App Service funkcja integracji sieci wirtualnej umożliwia aplikacji wykonywanie żądań **wychodzących** do Virtual Network platformy Azure. Funkcja działa przez połączenie hosta, na którym uruchomiono aplikację, do bramy Virtual Network w sieci wirtualnej z siecią VPN typu punkt-lokacja. Po skonfigurowaniu tej funkcji aplikacja pobiera jeden z adresów typu punkt-lokacja przypisanych do każdego wystąpienia. Ta funkcja umożliwia dostęp do zasobów w klasycznym lub Menedżer zasobów sieci wirtualnych w dowolnym regionie. 

![Integracja sieci wirtualnej wymagana przez bramę](media/networking-features/gw-vnet-integration.png)

Ta funkcja rozwiązuje problem z uzyskaniem dostępu do zasobów w innych sieci wirtualnych i może być nawet używana do nawiązywania połączenia przez sieć wirtualną z innymi sieci wirtualnych, a nawet lokalnie. Nie działa z ExpressRoute połączony sieci wirtualnych, ale ma sieci VPN typu lokacja-lokacja. Zwykle nie jest to możliwe, aby użyć tej funkcji z aplikacji w App Service Environment (ASE), ponieważ środowisko ASE znajduje się już w sieci wirtualnej. Przypadki użycia, które są rozwiązywane przez tę funkcję:

* Uzyskiwanie dostępu do zasobów prywatnych adresów IP w sieciach wirtualnych platformy Azure 
* Dostęp do zasobów lokalnych w przypadku sieci VPN typu lokacja-lokacja 
* Uzyskiwanie dostępu do zasobów w sieci wirtualnych komunikacji równorzędnej 

Gdy ta funkcja jest włączona, aplikacja będzie używać serwera DNS, z którym jest skonfigurowana docelowa sieć wirtualna. Więcej informacji na temat tej funkcji można znaleźć w dokumentacji dotyczącej [integracji sieci wirtualnej App Service][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>Integracja z siecią wirtualną

Funkcja integracji sieci wirtualnej wymagana przez bramę jest przydatna, ale nadal nie rozwiązuje dostępu do zasobów w ExpressRoute. W celu uzyskania dostępu do połączeń ExpressRoute istnieje potrzeba, aby aplikacje mogły wykonywać wywołania do zabezpieczonych usług punktu końcowego usługi. Aby rozwiązać oba te dodatkowe potrzeby, dodano inną możliwość integracji sieci wirtualnej. Nowa funkcja integracji sieci wirtualnej umożliwia umieszczenie zaplecza aplikacji w podsieci w sieci wirtualnej Menedżer zasobów w tym samym regionie. Ta funkcja jest niedostępna z App Service Environment, która znajduje się już w sieci wirtualnej. Ta funkcja umożliwia:

* Uzyskiwanie dostępu do zasobów w Menedżer zasobów sieci wirtualnych w tym samym regionie
* Uzyskiwanie dostępu do zasobów zabezpieczonych za pomocą punktów końcowych usługi 
* Uzyskiwanie dostępu do zasobów, które są dostępne dla połączeń ExpressRoute lub sieci VPN
* Zabezpieczanie całego ruchu wychodzącego 
* Wymuś tunelowanie całego ruchu wychodzącego. 

![Integracja z siecią wirtualną](media/networking-features/vnet-integration.png)

Aby dowiedzieć się więcej na temat tej funkcji, przeczytaj dokumenty dotyczące [integracji sieci wirtualnej App Service][vnetintegration].

## <a name="app-service-environment"></a>Środowisko usługi App Service 

App Service Environment (ASE) jest wdrożeniem pojedynczej dzierżawy Azure App Service uruchamianej w sieci wirtualnej. Środowisko ASE umożliwia używanie takich przypadków, jak:

* Dostęp do zasobów w sieci wirtualnej
* Dostęp do zasobów w ramach ExpressRoute
* Uwidacznianie aplikacji przy użyciu adresu prywatnego w sieci wirtualnej 
* Dostęp do zasobów między punktami końcowymi usługi 

Środowisko ASE nie wymaga używania funkcji, takich jak integracja sieci wirtualnej czy punkty końcowe usługi, ponieważ środowisko ASE znajduje się już w sieci wirtualnej. Aby uzyskać dostęp do zasobów, takich jak SQL lub Storage, należy włączyć punkty końcowe usługi w podsieci środowiska ASE. Jeśli chcesz uzyskać dostęp do zasobów w sieci wirtualnej, nie jest wymagana żadna dodatkowa konfiguracja.  Jeśli chcesz uzyskać dostęp do zasobów w ExpressRoute, jesteś już w sieci wirtualnej i nie musisz konfigurować żadnych elementów w środowisku ASE ani w aplikacjach. 

Ponieważ aplikacje w środowisku ILB ASE mogą być uwidocznione na prywatnym adresie IP, można łatwo dodać urządzenia WAF, aby uwidocznić tylko aplikacje, które mają być połączone z Internetem, i zapewnić bezpieczeństwo reszty. Pozwala ona na łatwe programowanie aplikacji wielowarstwowych. 

Istnieje kilka rzeczy, które nie są jeszcze dostępne w usłudze z wieloma dzierżawcami, które pochodzą z platformy ASE. Należą do nich następujące elementy:

* Uwidaczniaj swoje aplikacje na prywatnym adresie IP
* Zabezpiecz cały ruch wychodzący z kontrolkami sieci, które nie są częścią aplikacji 
* Hostowanie aplikacji w pojedynczej usłudze dzierżawy 
* Skalowanie w górę do wielu wystąpień, niż jest to możliwe w usłudze z wieloma dzierżawcami 
* Ładuj certyfikaty klienta prywatnego urzędu certyfikacji do użytku przez aplikacje z bezpiecznymi punktami końcowymi urzędu certyfikacji 
* Wymuś protokół TLS 1,1 we wszystkich aplikacjach hostowanych w systemie bez możliwości wyłączenia na poziomie aplikacji 
* Podaj dedykowany adres wychodzący dla wszystkich aplikacji w środowisku ASE, które nie są udostępniane żadnym klientom 

![Środowisko ASE w sieci wirtualnej](media/networking-features/app-service-environment.png)

Środowisko ASE zapewnia najlepszą historię hostingu izolowanych aplikacji, ale wiąże się z pewnymi problemami związanymi z zarządzaniem. Niektóre zagadnienia, które należy wziąć pod uwagę przed użyciem środowiska ASE, to:
 
 * Środowisko ASE działa w sieci wirtualnej, ale ma zależności poza siecią wirtualną. Te zależności muszą być dozwolone. Przeczytaj więcej w temacie [zagadnienia dotyczące sieci dla App Service Environment][networkinfo]
 * Środowisko ASE nie jest skalowane bezpośrednio, podobnie jak usługa wielodostępna. Należy przewidzieć potrzeby skalowania zamiast ponownego skalowania. 
 * Środowisko ASE ma przypisany wyższy koszt z góry. Aby maksymalnie wykorzystać środowisko ASE, należy zaplanować umieszczenie wielu obciążeń w jednym środowisku ASE, zamiast korzystać z małych nakładów pracy
 * Aplikacje w środowisku ASE nie mogą ograniczać dostępu do niektórych aplikacji w środowisku ASE, a nie innych.
 * Środowisko ASE znajduje się w podsieci, a wszystkie reguły sieciowe mają zastosowanie do całego ruchu do i z tego środowiska ASE. Jeśli chcesz przypisać reguły ruchu przychodzącego tylko dla jednej aplikacji, użyj ograniczeń dostępu. 

## <a name="combining-features"></a>Łączenie funkcji 

Funkcje zanotowane dla usługi wielodostępnej mogą być używane razem w celu rozwiązywania bardziej rozbudowanych przypadków użycia. W tym miejscu opisano dwa z najpopularniejszych przypadków użycia, ale są one tylko przykładami. Zrozumienie, jakie są różne funkcje, można rozwiązać niemal wszystkie potrzeby związane z architekturą systemu.

### <a name="inject-app-into-a-vnet"></a>Wsuń aplikację do sieci wirtualnej

Typowym żądaniem jest umieszczenie aplikacji w sieci wirtualnej. Umieszczenie aplikacji w sieci wirtualnej oznacza, że przychodzące i wychodzące punkty końcowe aplikacji znajdują się w sieci wirtualnej. Środowisko ASE oferuje najlepsze rozwiązanie umożliwiające rozwiązanie tego problemu, ale w przypadku usługi wielodostępnej można uzyskać większość potrzebnych informacji w usłudze z wieloma dzierżawcami przez połączenie funkcji. Na przykład można hostować tylko aplikacje intranetowe z prywatnymi adresami przychodzącymi i wychodzącymi przez:

* Tworzenie Application Gateway z prywatnym adresem przychodzącym i wychodzącym
* Zabezpieczanie ruchu przychodzącego do aplikacji za pomocą punktów końcowych usługi 
* Użyj nowej integracji sieci wirtualnej, aby zaplecze aplikacji w sieci wirtualnej 

Ten styl wdrożenia nie daje dedykowanego adresu dla ruchu wychodzącego do Internetu lub umożliwia blokowanie całego ruchu wychodzącego z aplikacji.  Ten styl wdrożenia zapewnia wiele możliwości, z których można korzystać w środowisku ASE. 

### <a name="create-multi-tier-applications"></a>Tworzenie aplikacji wielowarstwowych

Aplikacja wielowarstwowa to aplikacja, w której do aplikacji zaplecza API można uzyskać dostęp tylko z warstwy frontonu. Istnieją dwa sposoby tworzenia aplikacji wielowarstwowej. Oba zaczynają korzystać z integracji sieci wirtualnej, aby połączyć swoją aplikację internetową frontonu z podsiecią w sieci wirtualnej. Dzięki temu aplikacja sieci Web będzie mogła nawiązywać wywołania do sieci wirtualnej. Gdy aplikacja frontonu zostanie połączona z siecią wirtualną, musisz wybrać sposób blokowania dostępu do aplikacji interfejsu API.  Można:

* Hostowanie zarówno aplikacji frontonu, jak i interfejsu API w tym samym ILB ASE i Uwidacznianie aplikacji frontonu w Internecie za pomocą bramy aplikacji
* Hostowanie frontonu w usłudze z wieloma dzierżawcami i zaplecza w środowisku ILB ASE
* Hostowanie zarówno aplikacji frontonu, jak i interfejsu API w usłudze z wieloma dzierżawcami

Jeśli zarządzasz zarówno aplikacją frontonu, jak i aplikacją interfejsu API dla aplikacji wielowarstwowej, możesz:

Uwidacznianie aplikacji interfejsu API za pomocą prywatnych punktów końcowych w sieci wirtualnej

![prywatne punkty końcowe — aplikacja dwuwarstwowa](media/networking-features/multi-tier-app-private-endpoint.png)

Korzystanie z punktów końcowych usługi do zabezpieczania ruchu przychodzącego do aplikacji interfejsu API tylko z podsieci używanej przez aplikację frontonu sieci Web

![zabezpieczona aplikacja punktów końcowych usługi](media/networking-features/multi-tier-app.png)

Są to między innymi następujące kompromisy między tymi dwoma technikami:

* w przypadku punktów końcowych usługi trzeba tylko zabezpieczyć ruch do aplikacji interfejsu API w podsieci integracji. Pozwala to zabezpieczyć aplikację interfejsu API, ale nadal może być eksfiltracji danych z aplikacji frontonu do innych aplikacji w App Service.
* z prywatnymi punktami końcowymi masz dwie podsieci podczas odtwarzania. Powoduje to dodanie do złożoności. Ponadto prywatny punkt końcowy jest zasobem najwyższego poziomu i dodaje więcej do zarządzania. Korzystanie z prywatnych punktów końcowych polega na tym, że nie masz możliwości eksfiltracji danych. 

Każda z tych technik będzie działała z wieloma frontonami. W małej skali punkty końcowe usługi są znacznie łatwiejsze w użyciu, ponieważ po prostu włączasz punkty końcowe usługi dla aplikacji interfejsu API w podsieci integracji frontonu. Po dodaniu większej liczby aplikacji frontonu należy dostosować każdą aplikację interfejsu API, aby zawierała punkty końcowe usługi z podsiecią integracji. Dzięki prywatnym punktom końcowym masz większą złożoność, ale nie musisz niczego zmieniać w aplikacjach interfejsu API po ustawieniu prywatnego punktu końcowego. 

### <a name="line-of-business-applications"></a>Aplikacje biznesowe

Aplikacje biznesowe (LOB) są wewnętrznymi aplikacjami, które nie są zwykle ujawniane w celu uzyskania dostępu z Internetu. Te aplikacje są wywoływane z wnętrza sieci firmowej, gdzie dostęp może być ściśle kontrolowany. Jeśli używasz środowiska ILB ASE, możesz łatwo hostować swoje aplikacje biznesowe. W przypadku korzystania z usługi wielodostępnej można używać prywatnych punktów końcowych lub punktów końcowych usługi połączonych z Application Gateway. Istnieją dwa powody użycia Application Gateway z punktami końcowymi usługi zamiast prywatnych punktów końcowych:

* potrzebujesz ochrony WAF w aplikacjach biznesowych
* aby równoważyć obciążenie z wieloma wystąpieniami aplikacji biznesowych

Jeśli nie jest to przypadek, lepiej jest używać prywatnych punktów końcowych. Dzięki prywatnym punktom końcowym dostępnym w App Service można uwidocznić swoje aplikacje na prywatnych adresach w sieci wirtualnej. Prywatny punkt końcowy, który znajduje się w sieci wirtualnej, można połączyć z połączeniami ExpressRoute i sieci VPN. Skonfigurowanie prywatnych punktów końcowych spowoduje udostępnienie aplikacji na adresie prywatnym, ale należy skonfigurować serwer DNS tak, aby osiągnął ten adres z lokalnego. Aby to umożliwić, należy przesłać dalej Azure DNS strefy prywatnej zawierającej prywatne punkty końcowe do lokalnych serwerów DNS. Azure DNS strefy prywatne nie obsługują przesyłania dalej strefy, ale można obsługiwać te usługi przy użyciu serwera DNS. Ten szablon, usługa [przesyłania dalej DNS](https://azure.microsoft.com/resources/templates/301-dns-forwarder/), ułatwia przekazanie Azure DNS strefy prywatnej do lokalnych serwerów DNS.

## <a name="app-service-ports"></a>Porty App Service

Jeśli przeskanujesz App Service, znajdziesz kilka portów, które są dostępne dla połączeń przychodzących. Nie ma możliwości blokowania lub kontrolowania dostępu do tych portów w usłudze z wieloma dzierżawcami. Dostępne są następujące porty:

| Zastosowanie | Porty |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  Zarządzanie | 454, 455 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Zdalne debugowanie programu Visual Studio  |  4020, 4022, 4024 |
|  Usługa Web Deploy | 8172 |
|  Użycie infrastruktury | 7654, 1221 |

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/configure-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
[appgwserviceendpoints]: https://docs.microsoft.com/azure/app-service/networking/app-gateway-with-service-endpoints
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
