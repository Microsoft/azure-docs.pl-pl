---
title: Funkcje sieci
description: Dowiedz się więcej o funkcjach sieciowych w Azure App Service, a także jakie funkcje potrzebne dla sieci muszą mieć zabezpieczenia lub funkcjonalność.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 03/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: af4c333fb539ad533756c538cb3ecde1d9a91413
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743050"
---
# <a name="app-service-networking-features"></a>App Service funkcje sieciowe

Aplikacje w Azure App Service można wdrożyć na wiele sposobów. Domyślnie aplikacje hostowane App Service są bezpośrednio dostępne dla Internetu i mogą uzyskiwać dostęp tylko do hostowanych punktów końcowych z Internetu. Wiele aplikacji klienta musi jednak kontrolować ruch sieciowy przychodzący i wychodzący. Istnieje kilka funkcji dostępnych w App Service, aby zaspokoić te wymagania. Wyzwanie polega na Poznaniu funkcji, która powinna być używana do rozwiązywania danego problemu. Ten dokument ma pomóc klientom w ustaleniu, która funkcja powinna być używana w oparciu o przykładowe przypadki użycia.

Istnieją dwa podstawowe typy wdrożeń dla Azure App Service. Istnieje usługa publiczna z wieloma dzierżawcami, która umożliwia hostowanie App Service planów w jednostkach SKU z cenami bezpłatna, współdzielona, podstawowa, standardowa, Premium, PremiumV2 i PremiumV3. Następnie istnieje pojedynczy dzierżawc App Service Environment (ASE), który hostuje izolowaną jednostkę SKU App Service plany bezpośrednio w Virtual Network platformy Azure. Używane funkcje różnią się w zależności od tego, czy jesteś w usłudze z wieloma dzierżawcami, czy też w środowisku ASE. 

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
| Ograniczanie dostępu do mojej aplikacji z zasobów w sieci wirtualnej | Punkty końcowe usługi </br> ILB ASE </br> Prywatny punkt końcowy (wersja zapoznawcza) |
| Uwidacznianie mojej aplikacji w prywatnym adresie IP w mojej sieci wirtualnej | ILB ASE </br> prywatny adres IP dla ruchu przychodzącego na Application Gateway z punktami końcowymi usługi </br> Punkt końcowy usługi (wersja zapoznawcza) |
| Ochrona mojej aplikacji za pomocą WAF | Application Gateway i ILB ASE </br> Usługa Application Gateway z punktami końcowymi usługi </br> Moje drzwi platformy Azure z ograniczeniami dostępu |
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

Jednostki skalowania Azure App Service obsługują wielu klientów w każdym wdrożeniu. W planach bezpłatna i współdzielona jednostka SKU hostuje obciążenia klientów w przypadku wielu dzierżawców. Plany podstawowe i powyższe są przeznaczone do obsługi obciążeń klientów przeznaczonych tylko do jednego planu App Service (ASP). Jeśli masz plan App Service w warstwie Standardowa, wszystkie aplikacje w tym planie zostaną uruchomione na tym samym procesie roboczym. W przypadku skalowania procesu roboczego wszystkie aplikacje znajdujące się w tej ASP zostaną zreplikowane w nowym procesie roboczym dla każdego wystąpienia w środowisku ASP. Pracownicy, którzy są używani do PremiumV2 i PremiumV3, różnią się od pracowników używanych w innych planach. Każde wdrożenie App Service ma jeden adres IP, który jest używany przez cały ruch przychodzący do aplikacji w ramach tego App Service wdrożenia. Istnieją jednak od 4 do 11 adresów używanych do wykonywania wywołań wychodzących. Te adresy są udostępniane przez wszystkie aplikacje w tym App Service wdrożeniu. Adresy wychodzące różnią się w zależności od typów procesów roboczych. Oznacza to, że adresy używane przez usługi Free, Shared, Basic, standard i Premium nazywani różnią się od adresów używanych na wywołania wychodzące z PremiumV2 i PremiumV3 nazywani. Jeśli szukasz we właściwościach aplikacji, zobaczysz adresy przychodzące i wychodzące, które są używane przez aplikację. Jeśli musisz zablokować zależność z listą ACL protokołu IP, użyj possibleOutboundAddresses. 

![Właściwości aplikacji](media/networking-features/app-properties.png)

App Service ma wiele punktów końcowych, które są używane do zarządzania usługą.  Te adresy są publikowane w osobnym dokumencie i znajdują się również w tagu usługi AppServiceManagement IP. Tag AppServiceManagement jest używany tylko w przypadku App Service Environment (ASE), w którym należy zezwolić na ten ruch. Adresy przychodzące App Service są śledzone w tagu usługi IP AppService. Nie istnieje tag usługi IP zawierający adresy wychodzące używane przez App Service. 

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

Punkty końcowe usługi umożliwiają zablokowanie dostępu **przychodzącego** do aplikacji w taki sposób, że adres źródłowy musi pochodzić z zestawu podsieci, które zostały wybrane. Ta funkcja działa w połączeniu z ograniczeniami dostępu do adresów IP. Punkty końcowe usługi są ustawiane w tym samym środowisku użytkownika co ograniczenia dostępu do adresów IP. Można utworzyć listę dozwolonych/zablokowanych reguł dostępu, które zawierają adresy publiczne, a także podsieci w sieci wirtualnych. Ta funkcja obsługuje takie scenariusze, jak:

![punkty końcowe usługi](media/networking-features/service-endpoints.png)

* Konfigurowanie Application Gateway przy użyciu aplikacji do blokowania ruchu przychodzącego do aplikacji
* Ograniczanie dostępu do aplikacji do zasobów w sieci wirtualnej. Może to obejmować maszyny wirtualne, środowisk ASE, a nawet inne aplikacje korzystające z integracji sieci wirtualnej. 

![punkty końcowe usługi z bramą aplikacji](media/networking-features/service-endpoints-appgw.png)

Więcej informacji na temat konfigurowania punktów końcowych usługi przy użyciu aplikacji można znaleźć w samouczku dotyczącym [konfigurowania ograniczeń dostępu do punktu końcowego usługi][serviceendpoints]

### <a name="private-endpoint-preview"></a>Prywatny punkt końcowy (wersja zapoznawcza)

Prywatny punkt końcowy to interfejs sieciowy, który łączy Cię prywatnie i bezpiecznie z Twoją aplikacją internetową za pomocą prywatnego linku platformy Azure. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, efektywnie łącząc aplikację sieci Web z siecią wirtualną. Ta funkcja jest tylko dla przepływów **przychodzących** do aplikacji sieci Web.
[Używanie prywatnych punktów końcowych dla usługi Azure Web App (wersja zapoznawcza)][privateendpoints]
 
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

Ponieważ funkcja umożliwia dostęp do zasobów lokalnych bez otworu zapory przychodzącej, jest to popularne dla deweloperów. Inne funkcje sieci wychodzącej App Service są bardzo powiązane z usługą Azure Virtual Network. Połączenia hybrydowe nie ma zależności między przechodzeniem do sieci wirtualnej i może służyć do szerszej gamy potrzeb sieciowych. Należy pamiętać, że funkcja Połączenia hybrydowe App Service nie ma znaczenia ani nie wie, co wykonujesz na jego podstawie. Oznacza to, że można go użyć w celu uzyskania dostępu do bazy danych, usługi sieci Web lub dowolnego gniazda TCP na komputerze typu mainframe. Funkcja zasadniczo tuneluje pakiety TCP. 

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

Funkcja integracji sieci wirtualnej wymagana przez bramę jest bardzo przydatna, ale nadal nie rozwiązuje dostępu do zasobów w ExpressRoute. W celu uzyskania dostępu do połączeń ExpressRoute istnieje potrzeba, aby aplikacje mogły wykonywać wywołania do zabezpieczonych usług punktu końcowego usługi. Aby rozwiązać oba te dodatkowe potrzeby, dodano inną możliwość integracji sieci wirtualnej. Nowa funkcja integracji sieci wirtualnej umożliwia umieszczenie zaplecza aplikacji w podsieci w sieci wirtualnej Menedżer zasobów w tym samym regionie. Ta funkcja jest niedostępna z App Service Environment, która znajduje się już w sieci wirtualnej. Ta funkcja umożliwia:

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

Aplikacja wielowarstwowa to aplikacja, w której do aplikacji zaplecza API można uzyskać dostęp tylko z warstwy frontonu. Aby utworzyć aplikację wielowarstwową, możesz:

* Łączenie zaplecza aplikacji sieci Web frontonu z podsiecią przy użyciu integracji z siecią wirtualną
* Korzystanie z punktów końcowych usługi do zabezpieczania ruchu przychodzącego do aplikacji interfejsu API tylko z podsieci używanej przez aplikację frontonu sieci Web

![aplikacja wielowarstwowa](media/networking-features/multi-tier-app.png)

Wiele aplikacji frontonu może korzystać z tej samej aplikacji interfejsu API przy użyciu integracji z siecią wirtualną z innych aplikacji frontonu i punktów końcowych usługi z aplikacji interfejsu API z ich podsieciami.  

<!--Links-->
[appassignedaddress]: ./configure-ssl-certificate.md
[iprestrictions]: ./app-service-ip-restrictions.md
[serviceendpoints]: ./app-service-ip-restrictions.md
[hybridconn]: ./app-service-hybrid-connections.md
[vnetintegrationp2s]: ./web-sites-integrate-with-vnet.md
[vnetintegration]: ./web-sites-integrate-with-vnet.md
[networkinfo]: ./environment/network-info.md
[appgwserviceendpoints]: ./networking/app-gateway-with-service-endpoints.md
[privateendpoints]: ./networking/private-endpoint.md