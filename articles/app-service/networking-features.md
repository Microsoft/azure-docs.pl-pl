---
title: Funkcje sieci
description: Dowiedz się więcej o funkcjach sieciowych w Azure App Service i Dowiedz się, które funkcje są potrzebne do zabezpieczeń lub innych funkcji.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 10/18/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 53c0d37d4a25c2f2092a9e52bcae8ea494046bb0
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210022"
---
# <a name="app-service-networking-features"></a>App Service funkcje sieciowe

Aplikacje można wdrażać w Azure App Service na wiele sposobów. Domyślnie aplikacje hostowane w App Service są dostępne bezpośrednio przez Internet i mogą uzyskiwać dostęp do punktów końcowych hostowanych przez Internet. Jednak w przypadku wielu aplikacji należy kontrolować ruch sieciowy przychodzący i wychodzący. Istnieje kilka funkcji w App Service, które ułatwią spełnienie tych wymagań. Wyzwanie polega na tym, która funkcja służy do rozwiązywania danego problemu. Ten artykuł pomoże Ci określić, której funkcji użyć, w oparciu o przykładowe przypadki użycia.

Istnieją dwa główne typy wdrożeń dla Azure App Service: 
- Wielodostępna usługa publiczna obsługuje App Service plany w jednostkach SKU, współdzielona, podstawowa, standardowa, Premium, PremiumV2 i PremiumV3 cenowych. 
- App Service Environment z jedną dzierżawą (ASE) hostuje izolowane jednostki SKU App Service bezpośrednio w sieci wirtualnej platformy Azure. 

Używane funkcje zależą od tego, czy korzystasz z usługi wielodostępnej, czy też w środowisku ASE. 

## <a name="multitenant-app-service-networking-features"></a>Wielodostępne funkcje sieciowe App Service 

Azure App Service to system rozproszony. Role obsługujące przychodzące żądania HTTP lub HTTPS są nazywane *frontonami*. Role obsługujące obciążenie klienta są nazywane *pracownikami*. Wszystkie role we wdrożeniu App Service istnieją w sieci wielodostępnej. Ponieważ istnieje wielu różnych klientów w tej samej App Serviceej jednostce skalowania, nie można połączyć sieci App Service bezpośrednio z siecią. 

Zamiast łączyć sieci, potrzebne są funkcje do obsługi różnych aspektów komunikacji aplikacji. Funkcje obsługujące żądania *do* aplikacji nie mogą być używane do rozwiązywania problemów podczas wykonywania wywołań *z* aplikacji. Podobnie funkcje, które rozwiązują problemy dotyczące wywołań z aplikacji, nie mogą być używane do rozwiązywania problemów z aplikacją.  

| Funkcje przychodzące | Funkcje wychodzące |
|---------------------|-------------------|
| Adres przypisany do aplikacji | Połączenia hybrydowe |
| Ograniczenia dostępu | Integracja z siecią wirtualną wymagana przez bramę |
| Punkty końcowe usługi | Integracja z siecią wirtualną |
| Prywatne punkty końcowe ||

Inne niż zauważone wyjątki, można używać razem ze wszystkimi tymi funkcjami. Możesz mieszać funkcje, aby rozwiązać problemy.

## <a name="use-cases-and-features"></a>Przypadki użycia i funkcje

W przypadku dowolnego danego przypadku użycia może istnieć kilka sposobów rozwiązania problemu. Wybór najlepszej funkcji czasami wykracza poza sam przypadek użycia. Następujące przypadki użycia ruchu przychodzącego zasugerują sposób użycia App Service funkcje sieciowe do rozwiązywania problemów z kontrolowaniem ruchu kierowanego do aplikacji:
 
| Przypadek użycia dla ruchu przychodzącego | Cechy |
|---------------------|-------------------|
| Obsługa protokołu SSL opartego na protokole IP dla aplikacji | Adres przypisany do aplikacji |
| Obsługa nieudostępnianego dedykowanego adresu przychodzącego dla aplikacji | Adres przypisany do aplikacji |
| Ograniczanie dostępu do aplikacji z zestawu dobrze zdefiniowanych adresów | Ograniczenia dostępu |
| Ograniczanie dostępu do aplikacji z zasobów w sieci wirtualnej | Punkty końcowe usługi </br> ILB ASE </br> Prywatne punkty końcowe |
| Udostępnianie aplikacji w prywatnym adresie IP w sieci wirtualnej | ILB ASE </br> Prywatne punkty końcowe </br> Prywatny adres IP dla ruchu przychodzącego w wystąpieniu Application Gateway z punktami końcowymi usługi |
| Ochrona aplikacji za pomocą zapory aplikacji sieci Web (WAF) | Application Gateway i ILB ASE </br> Application Gateway z prywatnymi punktami końcowymi </br> Usługa Application Gateway z punktami końcowymi usługi </br> Moje drzwi platformy Azure z ograniczeniami dostępu |
| Równoważenie obciążenia ruchu do aplikacji w różnych regionach | Moje drzwi platformy Azure z ograniczeniami dostępu | 
| Równoważenie obciążenia ruchu w tym samym regionie | [Usługa Application Gateway z punktami końcowymi usługi][appgwserviceendpoints] | 

Następujące wychodzące przypadki użycia sugerują, jak używać funkcji sieciowych App Service do rozwiązywania potrzeby dostępu wychodzącego dla aplikacji:

| Wychodzący przypadek użycia | Cechy |
|---------------------|-------------------|
| Dostęp do zasobów w sieci wirtualnej platformy Azure w tym samym regionie | Integracja z siecią wirtualną </br> ASE |
| Dostęp do zasobów w sieci wirtualnej platformy Azure w innym regionie | Integracja z siecią wirtualną wymagana przez bramę </br> Środowisko ASE i Komunikacja równorzędna sieci wirtualnych |
| Dostęp do zasobów zabezpieczonych za pomocą punktów końcowych usługi | Integracja z siecią wirtualną </br> ASE |
| Dostęp do zasobów w sieci prywatnej, która nie jest połączona z platformą Azure | Połączenia hybrydowe |
| Dostęp do zasobów w obwodach usługi Azure ExpressRoute | Integracja z siecią wirtualną </br> ASE | 
| Zabezpiecz ruch wychodzący z aplikacji sieci Web | Integracja sieci wirtualnej i sieciowe grupy zabezpieczeń </br> ASE | 
| Kierowanie ruchu wychodzącego z aplikacji sieci Web | Tabele integracji i routingu sieci wirtualnej </br> ASE | 


### <a name="default-networking-behavior"></a>Domyślne zachowanie sieci

Jednostki skalowania Azure App Service obsługują wielu klientów w każdym wdrożeniu. W planach bezpłatna i współdzielona jednostka SKU hostuje obciążenia klientów dla wielodostępnych procesów roboczych. Plany podstawowe i wyższe są obsługiwane przez klientów, które są przeznaczone tylko dla jednego planu App Service. Jeśli korzystasz ze standardowego planu App Service, wszystkie aplikacje w tym planie zostaną uruchomione na tym samym procesie roboczym. W przypadku skalowania procesu roboczego wszystkie aplikacje w tym planie App Service będą replikowane na nowym procesie roboczym dla każdego wystąpienia w planie App Service. 

#### <a name="outbound-addresses"></a>Adresy wychodzące

Maszyny wirtualne procesu roboczego są podzielone na dużą część według planów App Service. Plany bezpłatna, współdzielona, podstawowa, standardowa i Premium używają tego samego typu maszyn wirtualnych procesu roboczego. Plan PremiumV2 używa innego typu maszyny wirtualnej. PremiumV3 używa jeszcze innego typu maszyny wirtualnej. Zmiana rodziny maszyn wirtualnych uzyskuje inny zestaw adresów wychodzących. W przypadku skalowania z warstwy Standardowa do PremiumV2 adresy wychodzące zmienią się. W przypadku skalowania z PremiumV2 do PremiumV3, Twoje adresy wychodzące zmienią się. W przypadku niektórych starszych jednostek skalowania adresy przychodzące i wychodzące zmienią się po skalowaniu z warstwy Standardowa do PremiumV2. 

Istnieje wiele adresów używanych na potrzeby wywołań wychodzących. Adresy wychodzące używane przez aplikację do wykonywania wywołań wychodzących są wyświetlane we właściwościach aplikacji. Te adresy są udostępniane wszystkim aplikacjom działającym w ramach tej samej rodziny maszyn wirtualnych procesu roboczego we wdrożeniu App Service. Jeśli chcesz zobaczyć wszystkie adresy, które mogą być używane przez aplikację w jednostce skalowania, właściwość o nazwie zostanie wyświetlona `possibleOutboundAddresses` . 

![Zrzut ekranu, na którym są wyświetlane właściwości aplikacji.](media/networking-features/app-properties.png)

App Service ma wiele punktów końcowych, które są używane do zarządzania usługą.  Te adresy są publikowane w osobnym dokumencie i znajdują się również w `AppServiceManagement` tagu usługi IP. `AppServiceManagement`Tag jest używany tylko w środowiskach App Service, w których trzeba zezwolić na ten ruch. Adresy przychodzące App Service są śledzone w `AppService` tagu usługi IP. Nie istnieje tag usługi IP zawierający adresy wychodzące używane przez App Service. 

![Diagram przedstawiający App Service ruchu przychodzącego i wychodzącego.](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Adres przypisany do aplikacji 

Funkcja adresu przypisanego do aplikacji jest offshootem protokołu SSL opartego na protokole IP. Uzyskujesz do niego dostęp przez skonfigurowanie protokołu SSL za pomocą aplikacji. Tej funkcji można użyć dla wywołań SSL opartych na protokole IP. Można go również użyć do nadania aplikacji adresu, który posiada. 

![Diagram, który ilustruje adres przypisany do aplikacji.](media/networking-features/app-assigned-address.png)

W przypadku korzystania z adresu przypisanego do aplikacji ruch nadal odbywa się przez te same role frontonu, które obsługują cały ruch przychodzący do jednostki skalowania App Service. Ale adres przypisany do aplikacji jest używany tylko przez aplikację. Przypadki użycia dla tej funkcji:

* Obsługa protokołu SSL opartego na protokole IP dla Twojej aplikacji.
* Ustaw dedykowany adres dla aplikacji, która nie jest udostępniona.

Aby dowiedzieć się, jak ustawić adres w aplikacji, zobacz [Dodawanie certyfikatu TLS/SSL w Azure App Service][appassignedaddress]. 

### <a name="access-restrictions"></a>Ograniczenia dostępu 

Ograniczenia dostępu umożliwiają filtrowanie żądań *przychodzących* . Akcja filtrowania odbywa się w rolach frontonu, które są nadrzędne od ról procesu roboczego, w których są uruchomione aplikacje. Ponieważ role frontonu są nadrzędne dla procesów roboczych, można traktować ograniczenia dostępu jako ochronę na poziomie sieci dla aplikacji. 

Ta funkcja umożliwia utworzenie listy reguł zezwalania i odmowy, które są oceniane w kolejności priorytetów. Jest podobna do funkcji sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w sieci platformy Azure. Tej funkcji można użyć w środowisku ASE lub w usłudze wielodostępnej. Gdy jest używany z ILB ASE lub prywatnym punktem końcowym, można ograniczyć dostęp z bloków adresów prywatnych.
> [!NOTE]
> Dla każdej aplikacji można skonfigurować maksymalnie 512 reguł ograniczeń dostępu. 

![Diagram, który ilustruje ograniczenia dostępu.](media/networking-features/access-restrictions.png)

#### <a name="ip-based-access-restriction-rules"></a>Reguły ograniczeń dostępu opartych na protokole IP

Funkcja ograniczeń dostępu opartych na protokole IP umożliwia ograniczenie adresów IP, które mogą być używane do uzyskiwania dostępu do aplikacji. Obsługiwane są zarówno adresy IPv4, jak i IPv6. Niektóre przypadki użycia tej funkcji:
* Ogranicz dostęp do aplikacji z zestawu dobrze zdefiniowanych adresów. 
* Ogranicz dostęp do ruchu przychodzącego przez zewnętrzną usługę równoważenia obciążenia lub inne urządzenia sieciowe ze znanymi adresami IP ruchu wychodzącego. 

Aby dowiedzieć się, jak włączyć tę funkcję, zobacz [Konfigurowanie ograniczeń dostępu][iprestrictions].

#### <a name="access-restriction-rules-based-on-service-endpoints"></a>Reguły ograniczeń dostępu oparte na punktach końcowych usługi 

Punkty końcowe usługi umożliwiają zablokowanie dostępu *przychodzącego* do aplikacji w taki sposób, aby adres źródłowy musiał pochodzić z zestawu podsieci, które zostały wybrane. Ta funkcja działa wraz z ograniczeniami dostępu do adresów IP. Punkty końcowe usługi nie są zgodne z debugowaniem zdalnym. Jeśli chcesz używać zdalnego debugowania z aplikacją, klient nie może znajdować się w podsieci z włączonymi punktami końcowymi usługi. Proces ustawiania punktów końcowych usługi jest podobny do procesu ustawiania ograniczeń dostępu do adresów IP. Można utworzyć listę dozwolonych/zablokowanych reguł dostępu, które zawierają adresy publiczne i podsieci w sieciach wirtualnych. 

Niektóre przypadki użycia tej funkcji:

* Skonfiguruj bramę aplikacji przy użyciu aplikacji, aby zablokować ruch przychodzący do aplikacji.
* Ogranicz dostęp do aplikacji do zasobów w sieci wirtualnej. Te zasoby mogą obejmować maszyny wirtualne, środowisk ASE, a nawet inne aplikacje korzystające z integracji sieci wirtualnej. 

![Diagram przedstawiający korzystanie z punktów końcowych usługi z Application Gateway.](media/networking-features/service-endpoints-appgw.png)

Aby dowiedzieć się więcej na temat konfigurowania punktów końcowych usługi przy użyciu aplikacji, zobacz [Azure App Service ograniczenia dostępu][serviceendpoints].
#### <a name="access-restriction-rules-based-on-service-tags-preview"></a>Reguły ograniczeń dostępu oparte na tagach usług (wersja zapoznawcza)
[Tagi usług platformy Azure][servicetags] są dobrze zdefiniowanymi zestawami adresów IP dla usług platformy Azure. Znaczniki usługi grupują zakresy adresów IP używane w różnych usługach platformy Azure i często są również w zasięgu w określonych regionach. Dzięki temu można filtrować ruch *przychodzący* z określonych usług platformy Azure. 

Aby zapoznać się z pełną listą tagów i więcej informacji, odwiedź Link Service Tag powyżej. Aby dowiedzieć się, jak włączyć tę funkcję, zobacz [Konfigurowanie ograniczeń dostępu][iprestrictions].
#### <a name="http-header-filtering-for-access-restriction-rules-preview"></a>Filtrowanie nagłówków HTTP na potrzeby reguł ograniczeń dostępu (wersja zapoznawcza)
Dla każdej reguły ograniczeń dostępu można dodać dodatkowe filtrowanie nagłówka HTTP. Pozwala to na dalsze sprawdzenie przychodzącego żądania i filtru na podstawie określonych wartości nagłówka HTTP. Każdy nagłówek może zawierać maksymalnie 8 wartości dla każdej reguły. Następująca lista nagłówków HTTP jest obecnie obsługiwana: 
* X-Forwarded-For
* X-Forward-Host
* X-Azure-FDID
* X-FD-HealthProbe

Niektóre przypadki użycia dotyczące filtrowania nagłówków HTTP są następujące:
* Ograniczanie dostępu do ruchu z serwerów proxy przekazujących nazwę hosta
* Ograniczanie dostępu do określonego wystąpienia usługi Azure front-drzwi przy użyciu reguły znacznika usługi i ograniczenia nagłówka X-FDID
### <a name="private-endpoint"></a>Prywatny punkt końcowy

Prywatny punkt końcowy to interfejs sieciowy, który łączy Cię prywatnie i bezpiecznie z Twoją aplikacją internetową za pomocą prywatnego linku platformy Azure. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, efektywnie łącząc aplikację sieci Web z siecią wirtualną. Ta funkcja jest tylko dla przepływów *przychodzących* do aplikacji sieci Web.
Aby uzyskać więcej informacji, zobacz [Używanie prywatnych punktów końcowych dla aplikacji internetowej platformy Azure][privateendpoints].

Niektóre przypadki użycia tej funkcji:

* Ogranicz dostęp do aplikacji z zasobów w sieci wirtualnej. 
* Uwidocznij swoją aplikację w prywatnym adresie IP w sieci wirtualnej. 
* Ochrona aplikacji za pomocą WAF.

Prywatne punkty końcowe uniemożliwiają eksfiltracji danych, ponieważ Jedyną czynnością, którą można uzyskać w prywatnym punkcie końcowym, jest aplikacja, z którą została skonfigurowana. 
 
### <a name="hybrid-connections"></a>Połączenia hybrydowe

App Service Połączenia hybrydowe umożliwia aplikacjom wykonywanie wywołań *wychodzących* do określonych punktów końcowych TCP. Punkt końcowy może być lokalny, w sieci wirtualnej lub w dowolnym miejscu, który zezwala na ruch wychodzący do platformy Azure na porcie 443. Aby skorzystać z tej funkcji, należy zainstalować agenta przekaźnika o nazwie Menedżer połączeń hybrydowych na hoście z systemem Windows Server 2012 lub nowszym. Menedżer połączeń hybrydowych musi mieć możliwość uzyskania dostępu do Azure Relay na porcie 443. Menedżer połączeń hybrydowych można pobrać z poziomu interfejsu użytkownika App Service Połączenia hybrydowe w portalu. 

![Diagram przedstawiający przepływ sieci Połączenia hybrydowe.](media/networking-features/hybrid-connections.png)

Połączenia hybrydowe App Service jest oparta na możliwościach Azure Relay Połączenia hybrydowe. App Service używa wyspecjalizowanej formy funkcji, która obsługuje tylko wykonywanie wywołań wychodzących z aplikacji do hosta i portu TCP. Ten host i port muszą zostać rozwiązane tylko na hoście, na którym zainstalowano Menedżer połączeń hybrydowych. 

Gdy aplikacja w App Service, przeszukiwania DNS na hoście i porcie zdefiniowanym w połączeniu hybrydowym, ruch automatycznie przekieruje się, aby przejść przez połączenie hybrydowe i z Menedżer połączeń hybrydowych. Aby dowiedzieć się więcej, zobacz [App Service połączenia hybrydowe][hybridconn].

Ta funkcja jest często używana do:

* Dostęp do zasobów w sieciach prywatnych, które nie są połączone z platformą Azure za pomocą sieci VPN lub ExpressRoute.
* Obsługa migracji aplikacji lokalnych w celu App Service bez konieczności przenoszenia pomocniczych baz danych.  
* Zapewnianie dostępu z ulepszonymi zabezpieczeniami do jednego hosta i portu na połączenie hybrydowe. Większość funkcji sieciowych otwiera dostęp do sieci. Za pomocą Połączenia hybrydowe można uzyskać dostęp tylko do jednego hosta i portu.
* Scenariusze okładki, które nie są objęte innymi metodami łączności wychodzącej.
* Wykonaj programowanie w App Service w sposób umożliwiający aplikacjom łatwe korzystanie z zasobów lokalnych. 

Ponieważ ta funkcja zapewnia dostęp do zasobów lokalnych bez otworu zapory przychodzącej, jest to popularne dla deweloperów. Inne funkcje sieci wychodzącej App Service są powiązane z usługą Azure Virtual Network. Połączenia hybrydowe nie zależy od przechodzenia przez sieć wirtualną. Może służyć do szerszej gamy potrzeb sieciowych. 

Należy pamiętać, że App Service Połączenia hybrydowe nie jest w posiadaniu informacji o tym, co wykonujesz nad nim. Dzięki temu można uzyskać dostęp do bazy danych, usługi sieci Web lub dowolnego gniazda TCP na komputerze typu mainframe. Funkcja zasadniczo tuneluje pakiety TCP. 

Połączenia hybrydowe jest popularne do programowania, ale jest również używany w aplikacjach produkcyjnych. Doskonale nadaje się do uzyskiwania dostępu do usługi sieci Web lub bazy danych, ale nie jest to konieczne w przypadku sytuacji, w których nie ma potrzeby tworzenia wielu połączeń. 

### <a name="gateway-required-vnet-integration"></a>Integracja z siecią wirtualną wymagana przez bramę 

Brama — wymagana App Service integracja z siecią wirtualną umożliwia aplikacji wykonywanie żądań *wychodzących* do sieci wirtualnej platformy Azure. Funkcja działa przez połączenie hosta, na którym uruchomiono aplikację, do bramy Virtual Network w sieci wirtualnej przy użyciu sieci VPN typu punkt-lokacja. Po skonfigurowaniu tej funkcji aplikacja pobiera jeden z adresów typu punkt-lokacja przypisanych do każdego wystąpienia. Ta funkcja umożliwia dostęp do zasobów w klasycznym lub Azure Resource Manager sieci wirtualnych w dowolnym regionie. 

![Diagram przedstawiający integrację sieci wirtualnej wymaganą przez bramę.](media/networking-features/gw-vnet-integration.png)

Ta funkcja rozwiązuje problem z uzyskaniem dostępu do zasobów w innych sieciach wirtualnych. Może być nawet używany do nawiązywania połączenia przez sieć wirtualną z innymi sieciami wirtualnymi lub lokalnymi. Nie współpracuje z sieciami wirtualnymi połączonymi z usługą ExpressRoute, ale działa z sieciami połączonymi z siecią VPN typu lokacja-lokacja. Zwykle nie jest to konieczne użycie tej funkcji z aplikacji w App Service Environment (ASE), ponieważ środowisko ASE znajduje się już w sieci wirtualnej. Przypadki użycia dla tej funkcji:

* Dostęp do zasobów prywatnych adresów IP w sieciach wirtualnych platformy Azure. 
* Dostęp do zasobów lokalnych w przypadku sieci VPN typu lokacja-lokacja. 
* Dostęp do zasobów w wirtualnych sieciach równorzędnych. 

Gdy ta funkcja jest włączona, aplikacja będzie używać serwera DNS, z którym jest skonfigurowana docelowa sieć wirtualna. Aby uzyskać więcej informacji na temat tej funkcji, zobacz [App Service integracja][vnetintegrationp2s]z siecią wirtualną. 

### <a name="vnet-integration"></a>Integracja z siecią wirtualną

Integracja sieci wirtualnej wymagana przez bramę jest przydatna, ale nie rozwiązuje problemu z uzyskaniem dostępu do zasobów w ExpressRoute. W celu uzyskania dostępu do połączeń ExpressRoute istnieje potrzeba, aby aplikacje mogły wykonywać wywołania usług zabezpieczonych przez punkt końcowy usługi. Inna możliwość integracji sieci wirtualnej może spełnić te wymagania. 

Nowa funkcja integracji sieci wirtualnej umożliwia umieszczenie zaplecza aplikacji w podsieci w sieci wirtualnej Menedżer zasobów w tym samym regionie, w którym znajduje się aplikacja. Ta funkcja jest niedostępna z App Service Environment, która znajduje się już w sieci wirtualnej. Przypadki użycia dla tej funkcji:

* Dostęp do zasobów w Menedżer zasobów sieci wirtualnych w tym samym regionie.
* Dostęp do zasobów zabezpieczonych za pomocą punktów końcowych usługi. 
* Dostęp do zasobów, które są dostępne dla połączeń ExpressRoute lub sieci VPN.
* Zabezpiecz cały ruch wychodzący. 
* Wymuś tunelowanie całego ruchu wychodzącego. 

![Diagram przedstawiający integrację sieci wirtualnej.](media/networking-features/vnet-integration.png)

Aby dowiedzieć się więcej, zobacz [App Service integracja][vnetintegration]z siecią wirtualną.

### <a name="app-service-environment"></a>Środowisko usługi App Service 

App Service Environment (ASE) to wdrożenie z jedną dzierżawą Azure App Service, które działa w sieci wirtualnej. Niektóre przypadki takie jak w przypadku tej funkcji:

* Dostęp do zasobów w sieci wirtualnej.
* Dostęp do zasobów w wielu ExpressRouteach.
* Uwidocznij swoje aplikacje za pomocą adresu prywatnego w sieci wirtualnej. 
* Dostęp do zasobów między punktami końcowymi usługi. 

W środowisku ASE nie trzeba używać funkcji, takich jak integracja sieci wirtualnej czy punkty końcowe usługi, ponieważ środowisko ASE znajduje się już w sieci wirtualnej. Aby uzyskać dostęp do zasobów, takich jak SQL lub Azure Storage, należy włączyć punkty końcowe usługi w podsieci środowiska ASE. Jeśli chcesz uzyskać dostęp do zasobów w sieci wirtualnej, nie musisz wykonywać żadnych dodatkowych czynności konfiguracyjnych. Jeśli chcesz uzyskać dostęp do zasobów w usłudze ExpressRoute, jesteś już w sieci wirtualnej i nie musisz konfigurować żadnych elementów w środowisku ASE ani w aplikacjach. 

Ponieważ aplikacje w środowisku ILB ASE mogą być uwidocznione na prywatnym adresie IP, można łatwo dodać urządzenia WAF, aby uwidocznić tylko aplikacje, które mają być połączone z Internetem, i zapewnić bezpieczeństwo reszty. Ta funkcja ułatwia tworzenie aplikacji wielowarstwowych. 

Niektóre rzeczy nie są obecnie dostępne w usłudze z wieloma dzierżawami, ale mogą być dostępne w środowisku ASE. Oto kilka przykładów:

* Uwidaczniaj swoje aplikacje na prywatnym adresie IP.
* Pomóż zabezpieczyć cały ruch wychodzący z kontrolkami sieci, które nie są częścią aplikacji.
* Hostowanie aplikacji w ramach usługi pojedynczej dzierżawy. 
* Skalowanie w górę do znacznie większej liczby wystąpień, niż jest to możliwe w usłudze wielodostępnej. 
* Załaduj certyfikaty klienta prywatnego urzędu certyfikacji do użytku przez aplikacje z prywatnymi punktami końcowymi zabezpieczonymi przez urząd certyfikacji.
* Wymuś protokół TLS 1,1 dla wszystkich aplikacji hostowanych w systemie bez możliwości wyłączania go na poziomie aplikacji. 
* Podaj dedykowany adres wychodzący dla wszystkich aplikacji w środowisku ASE, które nie są udostępniane klientom. 

![Diagram przedstawiający środowisko ASE w sieci wirtualnej.](media/networking-features/app-service-environment.png)

Środowisko ASE oferuje najlepszą historię hostingu aplikacji izolowanych i dedykowanych, ale wiąże się to z pewnymi problemami związanymi z zarządzaniem. Niektóre kwestie, które należy wziąć pod uwagę przed użyciem środowiska ASE:
 
 * Środowisko ASE działa w sieci wirtualnej, ale ma zależności poza siecią wirtualną. Te zależności muszą być dozwolone. Aby uzyskać więcej informacji, zobacz [zagadnienia dotyczące sieci w App Service Environment][networkinfo].
 * Środowisko ASE nie jest skalowane bezpośrednio jak usługa wielodostępna. Należy przewidzieć potrzeby skalowania zamiast ponownego skalowania. 
 * Środowisko ASE ma wyższy koszt z góry. Aby maksymalnie wykorzystać środowisko ASE, należy zaplanować umieszczenie wielu obciążeń w jednym środowisku ASE, zamiast korzystać z nich na potrzeby małych wysiłków.
 * Aplikacje w środowisku ASE nie mogą wybiórczo ograniczyć dostępu do niektórych aplikacji w ASE i innych.
 * Środowisko ASE znajduje się w podsieci, a wszystkie reguły sieciowe mają zastosowanie do całego ruchu do i z tego środowiska ASE. Jeśli chcesz przypisać reguły ruchu przychodzącego tylko dla jednej aplikacji, użyj ograniczeń dostępu. 

## <a name="combining-features"></a>Łączenie funkcji 

Funkcje zanotowane dla wielodostępnej usługi mogą być używane razem w celu rozwiązywania bardziej rozbudowanych przypadków użycia. W tym miejscu opisano dwa z najpopularniejszych przypadków użycia, ale są one tylko przykładami. Zrozumienie, jakie są różne funkcje, można spełnić niemal wszystkie potrzeby związane z architekturą systemu.

### <a name="place-an-app-into-a-virtual-network"></a>Umieszczanie aplikacji w sieci wirtualnej

Możesz zastanawiać się, jak umieścić aplikację w sieci wirtualnej. Jeśli aplikacja zostanie umieszczona w sieci wirtualnej, przychodzące i wychodzące punkty końcowe aplikacji znajdują się w sieci wirtualnej. Środowisko ASE jest najlepszym sposobem rozwiązania tego problemu. Można jednak spełnić większość potrzeb w ramach usługi wielodostępnej przez połączenie funkcji. Na przykład można hostować aplikacje tylko intranet z prywatnymi adresami przychodzącymi i wychodzącymi przez:

* Tworzenie bramy aplikacji przy użyciu prywatnych adresów przychodzących i wychodzących.
* Zabezpieczanie ruchu przychodzącego do aplikacji za pomocą punktów końcowych usługi. 
* Korzystanie z nowej funkcji integracji sieci wirtualnej, aby zaplecze Twojej aplikacji została nadana Sieć wirtualna. 

Ten styl wdrożenia nie zapewnia dedykowanego adresu dla ruchu wychodzącego do Internetu lub możliwość blokowania całego ruchu wychodzącego z aplikacji. Pozwoli to na wiele elementów, z których można korzystać w środowisku ASE. 

### <a name="create-multitier-applications"></a>Tworzenie aplikacji wielowarstwowych

Aplikacja wielowarstwowa to aplikacja, w której aplikacje zaplecza API są dostępne tylko z warstwy frontonu. Istnieją dwa sposoby tworzenia aplikacji wielowarstwowej. Oba zaczynają korzystać z integracji sieci wirtualnej, aby połączyć swoją aplikację internetową frontonu z podsiecią w sieci wirtualnej. Dzięki temu aplikacja sieci Web będzie mogła nawiązywać wywołania do sieci wirtualnej. Po połączeniu aplikacji frontonu z siecią wirtualną należy zdecydować, jak zablokować dostęp do aplikacji interfejsu API. Można:

* Hostowanie zarówno frontonu, jak i aplikacji interfejsu API w tym samym ILB ASE i udostępnianie aplikacji frontonu w Internecie przy użyciu bramy aplikacji.
* Zahostaj frontonu w usłudze wielodostępnej i zapleczu w środowisku ILB ASE.
* Hostowanie zarówno frontonu, jak i aplikacji interfejsu API w usłudze wielodostępnej.

Jeśli zarządzasz zarówno aplikacją frontonu, jak i interfejsem API dla aplikacji wielowarstwowej, możesz:

- Uwidaczniaj aplikację interfejsu API za pomocą prywatnych punktów końcowych w sieci wirtualnej:

  ![Diagram przedstawiający używanie prywatnych punktów końcowych w aplikacji dwuwarstwowej.](media/networking-features/multi-tier-app-private-endpoint.png)

- Za pomocą punktów końcowych usługi upewnij się, że ruch przychodzący do aplikacji interfejsu API jest dostępny tylko z podsieci używanej przez aplikację frontonu sieci Web:

  ![Diagram przedstawiający korzystanie z punktów końcowych usługi w celu zabezpieczenia aplikacji.](media/networking-features/multi-tier-app.png)

Poniżej przedstawiono kilka kwestii, które ułatwią podjęcie decyzji o tym, której metody użyć:

* W przypadku korzystania z punktów końcowych usługi należy tylko zabezpieczyć ruch do aplikacji interfejsu API w podsieci integracji. Dzięki temu można zabezpieczyć aplikację interfejsu API, ale nadal można eksfiltracji dane z aplikacji frontonu do innych aplikacji w usłudze App Service.
* Gdy korzystasz z prywatnych punktów końcowych, masz dwie podsieci podczas odtwarzania, które zwiększają złożoność. Ponadto prywatny punkt końcowy jest zasobem najwyższego poziomu i dodaje narzuty związane z zarządzaniem. Korzystanie z prywatnych punktów końcowych polega na tym, że nie masz możliwości eksfiltracji danych. 

Każda z tych metod będzie działała z wieloma frontonami. W przypadku niewielkiej skali punkty końcowe usługi są łatwiejsze w użyciu, ponieważ po prostu włączasz punkty końcowe usługi dla aplikacji interfejsu API w podsieci integracji frontonu. Podczas dodawania większej liczby aplikacji frontonu należy dostosować każdą aplikację interfejsu API w celu uwzględnienia punktów końcowych usługi z podsiecią integracji. Jeśli używasz prywatnych punktów końcowych, istnieje większa złożoność, ale nie musisz niczego zmieniać w aplikacjach interfejsu API po ustawieniu prywatnego punktu końcowego. 

### <a name="line-of-business-applications"></a>Aplikacje biznesowe

Aplikacje biznesowe (LOB) są wewnętrznymi aplikacjami, które nie są zwykle ujawniane w celu uzyskania dostępu z Internetu. Te aplikacje są wywoływane z wnętrza sieci firmowej, gdzie dostęp może być ściśle kontrolowany. Jeśli używasz środowiska ILB ASE, możesz łatwo hostować swoje aplikacje biznesowe. W przypadku korzystania z usługi wielodostępnej można użyć prywatnych punktów końcowych lub użyć punktów końcowych usługi połączonych z bramą aplikacji. Istnieją dwa powody użycia bramy aplikacji z punktami końcowymi usługi zamiast używania prywatnych punktów końcowych:
* Potrzebujesz ochrony WAF w aplikacjach biznesowych.
* Chcesz równoważyć obciążenie z wieloma wystąpieniami aplikacji biznesowych.

Jeśli żadne z tych wymagań nie są spełnione, można lepiej korzystać z prywatnych punktów końcowych. Dzięki prywatnym punktom końcowym dostępnym w App Service można uwidocznić swoje aplikacje na adresach prywatnych w sieci wirtualnej. Prywatny punkt końcowy, który znajduje się w sieci wirtualnej, można połączyć z połączeniami ExpressRoute i sieci VPN. 

Skonfigurowanie prywatnych punktów końcowych spowoduje udostępnienie aplikacji na adresie prywatnym, ale musisz skonfigurować serwer DNS, aby osiągnął ten adres z lokalnego. Aby ta konfiguracja działała, należy przesłać dalej Azure DNS strefy prywatnej zawierającej prywatne punkty końcowe do lokalnych serwerów DNS. Azure DNS strefy prywatne nie obsługują przesyłania dalej strefy, ale w tym celu można obsługiwać przekazywanie stref przy użyciu serwera DNS. Szablon [usługi przesyłania dalej DNS](https://azure.microsoft.com/resources/templates/301-dns-forwarder/) ułatwia przekazanie Azure DNS strefy prywatnej do lokalnych serwerów DNS.

## <a name="app-service-ports"></a>Porty App Service

W przypadku skanowania App Service znajdziesz kilka portów, które są dostępne dla połączeń przychodzących. Nie ma możliwości blokowania lub kontrolowania dostępu do tych portów w usłudze wielodostępnej. Poniżej znajduje się lista uwidocznionych portów:

| Zastosowanie | Port lub porty |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  Zarządzanie | 454, 455 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Zdalne debugowanie programu Visual Studio  |  4020, 4022, 4024 |
|  Usługa Web Deploy | 8172 |
|  Użycie infrastruktury | 7654, 1221 |

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
[servicetags]: ../virtual-network/service-tags-overview.md