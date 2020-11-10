---
title: Najlepsze rozwiązania dotyczące zabezpieczeń sieci — Microsoft Azure
description: Ten artykuł zawiera zestaw najlepszych rozwiązań dotyczących zabezpieczeń sieci opartych na platformie Azure.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2019
ms.author: TomSh
ms.openlocfilehash: 49c4f7888150446316a04710bc5de5e618cd66eb
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413313"
---
# <a name="azure-best-practices-for-network-security"></a>Najlepsze rozwiązania dotyczące zabezpieczeń sieci na platformie Azure
W tym artykule omówiono zbiór najlepszych rozwiązań dotyczących platformy Azure w celu zwiększenia bezpieczeństwa sieci. Te najlepsze rozwiązania wynikają z naszych rozwiązań związanych z obsługą sieci platformy Azure i klientami.

Dla każdego z najlepszych rozwiązań w tym artykule wyjaśniono:

* Najlepsze rozwiązanie to
* Dlaczego chcesz włączyć to najlepsze rozwiązanie
* Co może być wynikiem, jeśli nie można włączyć najlepszego rozwiązania
* Możliwe alternatywy dla najlepszych rozwiązań
* Jak można dowiedzieć się, jak włączyć najlepsze rozwiązanie

Te najlepsze rozwiązania są oparte na jednomyślnych opiniach i możliwościach platformy Azure oraz zestawach funkcji, ponieważ istnieją one w momencie zapisania tego artykułu. Opinie i technologie zmieniają się wraz z upływem czasu, a ten artykuł zostanie regularnie zaktualizowany w celu odzwierciedlenia tych zmian.

## <a name="use-strong-network-controls"></a>Używanie silnych kontrolek sieci
[Maszyny wirtualne platformy Azure](https://azure.microsoft.com/services/virtual-machines/) i urządzenia można połączyć z innymi urządzeniami sieciowymi, umieszczając je w [sieciach wirtualnych platformy Azure](../../virtual-network/index.yml). Oznacza to, że można podłączyć karty interfejsu sieci wirtualnej do sieci wirtualnej, aby umożliwić komunikację opartą na protokole TCP/IP między urządzeniami z obsługą sieci. Maszyny wirtualne połączone z siecią wirtualną platformy Azure mogą łączyć się z urządzeniami w tej samej sieci wirtualnej, w różnych sieciach wirtualnych, Internecie lub własnych sieciach lokalnych.

W miarę planowania sieci i bezpieczeństwa sieci zalecamy scentralizowanie:

- Zarządzanie podstawowymi funkcjami sieciowymi, takimi jak ExpressRoute, Sieć wirtualna i obsługa podsieci oraz adresowanie IP.
- Zarządzanie elementami zabezpieczeń sieci, takimi jak ExpressRoute, sieci wirtualnych i aprowizacji podsieci oraz adresowanie IP.

Jeśli używasz wspólnego zestawu narzędzi do zarządzania do monitorowania sieci i bezpieczeństwa sieci, będziesz mieć wyraźny wgląd w oba elementy. Prosta, ujednolicona strategia zabezpieczeń zmniejsza liczbę błędów, ponieważ zwiększa to zrozumienie przez człowieka i niezawodność automatyzacji.

## <a name="logically-segment-subnets"></a>Logiczne segmenty podsieci
Usługi Azure Virtual Networks są podobne do sieci LAN w sieci lokalnej. Pomysłem związanym z usługą Azure Virtual Network jest utworzenie sieci na podstawie pojedynczej prywatnej przestrzeni adresowej IP, na której można umieścić wszystkie maszyny wirtualne platformy Azure. Dostępne przestrzenie prywatnych adresów IP znajdują się w klasie A (10.0.0.0/8), klasie B (172.16.0.0/12) i klasie C (192.168.0.0/16).

Najlepsze rozwiązania dotyczące logicznego segmentacji podsieci obejmują:

**Najlepsze rozwiązanie** : Nie przypisuj reguł zezwalania z szeroką liczbą zakresów (na przykład Zezwalaj na 0.0.0.0 – 255.255.255.255).  
**Szczegóły** : Upewnij się, że procedury rozwiązywania problemów uniemożliwiają skonfigurowanie tych typów reguł. Te reguły umożliwiają wykonywanie fałszywych luk w zabezpieczeniach i są często wykrywane i wykorzystywane przez czerwone zespoły.

**Najlepsze rozwiązanie** : segmentowanie większej przestrzeni adresowej w podsieciach.   
**Szczegóły** : Aby utworzyć podsieci, użyj zasad podsieci opartych na protokole [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

**Najlepsze rozwiązanie** : Tworzenie kontroli dostępu do sieci między podsieciami. Routing między podsieciami odbywa się automatycznie i nie trzeba ręcznie konfigurować tabel routingu. Domyślnie nie ma żadnych kontroli dostępu do sieci między podsieciami, które tworzysz w sieci wirtualnej platformy Azure.   
**Szczegóły** : Użyj [sieciowej grupy zabezpieczeń](../../virtual-network/virtual-network-vnet-plan-design-arm.md) , aby chronić przed niechcianym ruchem w podsieciach platformy Azure. Sieciowe grupy zabezpieczeń są prostymi urządzeniami do inspekcji pakietów stanowych, które wykorzystują podejście 5-krotne (źródłowy adres IP, port źródłowy, docelowy adres IP, port docelowy i protokół warstwy 4) do tworzenia reguł zezwalania/odmowy dla ruchu sieciowego. Zezwalasz na ruch do i z jednego adresu IP do i z wielu adresów IP, a także do i z całych podsieci.

W przypadku korzystania z sieciowych grup zabezpieczeń do kontroli dostępu do sieci między podsieciami można umieścić zasoby należące do tej samej strefy zabezpieczeń lub roli w ich własnych podsieciach.

**Najlepsze rozwiązanie** : Unikaj niewielkich sieci wirtualnych i podsieci, aby zapewnić prostotę i elastyczność.   
**Szczegóły** : Większość organizacji dodaje więcej zasobów niż początkowo zaplanowano, a ponowne przydzielanie adresów jest czasochłonne. Użycie małych podsieci powoduje dodanie ograniczonej wartości zabezpieczeń, a mapowanie sieciowej grupy zabezpieczeń do każdej podsieci dodaje obciążenie. Definiuj podsieci w szerokim stopniu, aby zapewnić elastyczność wzrostu.

**Najlepsze rozwiązanie** : Uprość zarządzanie zasadami grupy zabezpieczeń sieci przez zdefiniowanie [grup zabezpieczeń aplikacji](https://azure.microsoft.com/blog/applicationsecuritygroups/).  
**Szczegóły** : Zdefiniuj grupę zabezpieczeń aplikacji dla list adresów IP, które mogą ulec zmianie w przyszłości lub które będą używane w wielu sieciowych grupach zabezpieczeń. Pamiętaj o nazwie grup zabezpieczeń aplikacji jasno, aby inni użytkownicy mogli zrozumieć ich zawartość i przeznaczenie.

## <a name="adopt-a-zero-trust-approach"></a>Przyjęcie nierównego podejścia zaufania
Sieci obwodowe działają zgodnie z założeniami, że wszystkie systemy w sieci mogą być zaufane. Jednak obecnie pracownicy uzyskują dostęp do zasobów organizacji z dowolnego miejsca na różnych urządzeniach i aplikacjach, co sprawia, że nie ma znaczenia, czy nie są one odpowiednie. Zasady kontroli dostępu, które koncentrują się tylko na tym, kto może uzyskać dostęp do zasobu, są niewystarczające. Aby określić równowagę między zabezpieczeniami i produktywnością, administratorzy zabezpieczeń muszą także wziąć pod uwagę *sposób* uzyskiwania dostępu do zasobu.

Sieci muszą rozwijać się od tradycyjnych zabezpieczeń, ponieważ sieci mogą być podatne na naruszenia: atakujący może złamać pojedynczy punkt końcowy w ramach zaufanej granicy, a następnie szybko rozwijać przyczółka w całej sieci. [Zerowe sieci zaufania](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) eliminują koncepcję zaufania na podstawie lokalizacji sieciowej w obrębie obwodu. Zamiast tego nie ma żadnych architektur zaufania użytkowników do uzyskiwania dostępu do bramy do danych i zasobów organizacji. W przypadku nowych inicjatyw należy wdrożyć zero podejścia zaufania, które sprawdzają poprawność zaufania w momencie dostępu.

Najlepsze rozwiązania:

**Najlepsze rozwiązanie** : zapewnianie warunkowego dostępu do zasobów na podstawie urządzenia, tożsamości, gwarancji, lokalizacji sieciowej i nie tylko.  
**Szczegóły** : [dostęp warunkowy usługi Azure AD](../../active-directory/conditional-access/overview.md) umożliwia stosowanie odpowiednich kontroli dostępu, implementując automatyczne decyzje dotyczące kontroli dostępu w zależności od wymaganych warunków. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem do usługi Azure Management przy użyciu dostępu warunkowego](../../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md).

**Najlepsze rozwiązanie** : Włącz dostęp do portów tylko po zatwierdzeniu przepływu pracy.  
**Szczegóły** : można użyć [dostępu just in Time do maszyny wirtualnej w Azure Security Center](../../security-center/security-center-just-in-time.md) do blokowania ruchu przychodzącego na maszynach wirtualnych platformy Azure, co pozwala ograniczyć narażenie na ataki, zapewniając łatwy dostęp do łączenia się z maszynami wirtualnymi w razie potrzeby.

**Najlepsze rozwiązanie** : Przyznawanie uprawnień tymczasowych do wykonywania uprzywilejowanych zadań, co uniemożliwia złośliwym lub nieautoryzowanym użytkownikom uzyskanie dostępu po wygaśnięciu uprawnień. Dostęp jest udzielany tylko wtedy, gdy użytkownicy go potrzebują.  
**Szczegóły** : Użyj dostępu just in time w Azure AD Privileged Identity Management lub rozwiązania innej firmy, aby przyznać uprawnienia do wykonywania uprzywilejowanych zadań.

Zaufanie równe zeru to kolejna ewolucja w zabezpieczeniach sieci. Stan cyberattacks umożliwia organizacjom podejmowanie "założenie naruszenia" sposób myślenia, ale takie podejście nie powinno mieć ograniczenia. Zerowe sieci zaufania chronią dane i zasoby firmowe, zapewniając, że organizacje mogą tworzyć nowoczesne miejsce pracy przy użyciu technologii, które umożliwiają pracownikom wydajne zwiększenie produktywności w dowolnym miejscu i czasie.

## <a name="control-routing-behavior"></a>Sterowanie zachowaniem routingu
Po umieszczeniu maszyny wirtualnej w sieci wirtualnej platformy Azure maszyna wirtualna może połączyć się z dowolną inną MASZYNą wirtualną w tej samej sieci wirtualnej, nawet jeśli inne maszyny wirtualne znajdują się w różnych podsieciach. Jest to możliwe, ponieważ kolekcja tras systemowych włączona domyślnie zezwala na ten typ komunikacji. Te trasy domyślne umożliwiają maszynom wirtualnym w tej samej sieci wirtualnej Inicjowanie połączeń ze sobą i z Internetem (tylko w przypadku komunikacji wychodzącej tylko do Internetu).

Mimo że domyślne trasy systemowe są przydatne w wielu scenariuszach wdrażania, istnieją przypadki, w których chcesz dostosować konfigurację routingu dla wdrożeń. Adres następnego przeskoku można skonfigurować w celu uzyskania dostępu do określonych miejsc docelowych.

Zaleca się skonfigurowanie [tras zdefiniowanych przez użytkownika](../../virtual-network/virtual-networks-udr-overview.md) podczas wdrażania urządzenia zabezpieczeń dla sieci wirtualnej. Porozmawiamy z tym w dalszej części zatytułowanej [Zabezpieczanie krytycznych zasobów usługi platformy Azure tylko do sieci wirtualnych](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Trasy zdefiniowane przez użytkownika nie są wymagane, a domyślne trasy systemowe są zwykle wykonywane.
>
>

## <a name="use-virtual-network-appliances"></a>Korzystanie z urządzeń sieci wirtualnej
Sieciowe grupy zabezpieczeń i Routing zdefiniowany przez użytkownika mogą zapewnić pewną miarę bezpieczeństwa sieci w warstwach sieci i transportu [modelu osi](https://en.wikipedia.org/wiki/OSI_model). Jednak w niektórych sytuacjach trzeba lub trzeba włączyć zabezpieczenia na wysokim poziomie stosu. W takich sytuacjach zalecamy wdrożenie urządzeń zabezpieczeń sieci wirtualnej udostępnianych przez partnerów platformy Azure.

Urządzenia zabezpieczeń sieci platformy Azure mogą zapewniać lepsze zabezpieczenia niż zapewniane przez formanty na poziomie sieci. Możliwości zabezpieczeń sieci urządzeń zabezpieczeń sieci wirtualnej obejmują:


* Zaporę
* Wykrywanie włamania/Zapobieganie włamaniom
* Zarządzanie lukami w zabezpieczeniach
* Sterowanie aplikacjami
* Wykrywanie anomalii oparte na sieci
* Filtrowanie sieci web
* Oprogramowanie antywirusowe
* Ochrona botnet

Aby znaleźć dostępne urządzenia zabezpieczeń sieci wirtualnej platformy Azure, przejdź do [witryny Azure Marketplace](https://azure.microsoft.com/marketplace/) i wyszukaj ciąg "zabezpieczenia" oraz "zabezpieczenia sieci".

## <a name="deploy-perimeter-networks-for-security-zones"></a>Wdrażanie sieci obwodowych pod kątem stref zabezpieczeń
[Sieć obwodowa](/azure/architecture/vdc/networking-virtual-datacenter) (nazywana również strefą DMZ) jest segmentem sieci fizycznej lub logicznej, który zapewnia dodatkową warstwę zabezpieczeń między zasobami i Internetem. Wyspecjalizowane urządzenia kontroli dostępu do sieci na granicy sieci obwodowej zezwalają tylko na żądany ruch do sieci wirtualnej.

Sieci obwodowe są przydatne, ponieważ możesz skoncentrować się na zarządzaniu, monitorowaniu, rejestrowaniu i raportowaniu kontroli dostępu do sieci na urządzeniach na granicy sieci wirtualnej platformy Azure. Sieć obwodowa polega na tym, że zwykle włączane jest zapobieganie atakom typu "odmowa usługi" (DDoS), Wykrywanie intruzów/systemy zapobiegania włamaniom (identyfikatory/adresy IP), reguły i zasady zapory, filtrowanie sieci Web, ochrona przed złośliwym oprogramowaniem w sieci i wiele innych. Urządzenia zabezpieczeń sieci są dostępne między Internetem a siecią wirtualną platformy Azure i mają interfejs w obu sieciach.

Chociaż jest to podstawowy projekt sieci obwodowej, istnieje wiele różnych projektów, takich jak back-to-back, Tri-Home i wieloadresowe.

Na podstawie wymienionej wcześniej koncepcji zaufania należy rozważyć użycie sieci obwodowej do wszystkich wdrożeń o wysokim poziomie zabezpieczeń, aby zwiększyć poziom bezpieczeństwa sieci i kontroli dostępu do zasobów platformy Azure. Możesz użyć platformy Azure lub rozwiązania innej firmy, aby zapewnić dodatkową warstwę zabezpieczeń między zasobami i Internetem:

- Natywne formanty platformy Azure. Zapora [platformy Azure](../../firewall/overview.md) i [Zapora aplikacji sieci web w programie Application Gateway](../../application-gateway/features.md#web-application-firewall) oferują podstawowe zabezpieczenia za pomocą w pełni bezstanowej zapory jako usługi, wbudowanej wysokiej dostępności, nieograniczonej skalowalności chmury, filtrowania nazw FQDN, obsługi zestawów reguł OWASP Core oraz prostej instalacji i konfiguracji.
- Oferty innych firm. Wyszukaj w [witrynie Azure Marketplace usługę](https://azuremarketplace.microsoft.com/) Zapora nowej generacji (zapory następnej generacji) i inne oferty innych firm, które zapewniają znane narzędzia zabezpieczające oraz znacznie ulepszone poziomy zabezpieczeń sieci. Konfiguracja może być bardziej złożona, ale oferta innej firmy może umożliwić korzystanie z istniejących możliwości i umiejętności.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Unikaj ekspozycji z Internetem za pomocą dedykowanych łączy sieci WAN
W wielu organizacjach została wybrana hybrydowa trasa IT. Dzięki hybrydowej IT niektóre z zasobów informacyjnych firmy znajdują się na platformie Azure, a inne pozostają w środowisku lokalnym. W wielu przypadkach niektóre składniki usługi działają na platformie Azure, podczas gdy inne składniki pozostają w środowisku lokalnym.

W hybrydowym scenariuszu IT zwykle jest kilka typów łączności między lokalizacjami. Łączność między lokalizacjami pozwala firmie na łączenie sieci lokalnych z sieciami wirtualnymi platformy Azure. Dostępne są dwa rozwiązania łączności obejmującej wiele lokalizacji:

* [Sieć VPN typu lokacja-lokacja](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). Jest to zaufana, niezawodna i ustanowiona technologia, ale połączenie odbywa się za pośrednictwem Internetu. Przepustowość jest ograniczona do maksymalnie 1,25 GB/s. Sieci VPN typu lokacja-lokacja jest pożądaną opcją w niektórych scenariuszach.
* **Azure ExpressRoute**. Zalecamy używanie [ExpressRoute](../../expressroute/expressroute-introduction.md) do połączeń obejmujących wiele lokalizacji. Usługa ExpressRoute umożliwia rozszerzenie sieci lokalnych na chmurę firmy Microsoft za pośrednictwem połączenia prywatnego obsługiwanego przez dostawcę połączenia. Dzięki usłudze ExpressRoute można nawiązywać połączenia z usługami w chmurze firmy Microsoft, takimi jak Azure, Microsoft 365 i Dynamics 365. ExpressRoute to dedykowany link sieci WAN między lokalizacją lokalną lub dostawcą hostingu programu Microsoft Exchange. Ponieważ jest to połączenie odpływ, dane nie podróżują przez Internet, więc nie są narażone na potencjalne ryzyko związane z komunikacją internetową.

Lokalizacja połączenia usługi ExpressRoute może wpłynąć na wydajność zapory, skalowalność, niezawodność i widoczność ruchu sieciowego. Należy określić, gdzie należy zakończyć ExpressRoute w istniejących sieciach (lokalnych). Można:

- Zakończ poza zaporą (model sieci obwodowej), jeśli potrzebujesz wglądu w ruch, jeśli chcesz kontynuować istniejącą sprawność izolowania centrów danych, lub jeśli nie masz wyłącznie zasobów ekstranetu na platformie Azure.
- Przerwij wewnątrz zapory (model rozszerzenia sieci). Jest to domyślne zalecenie. We wszystkich innych przypadkach zalecamy traktowanie platformy Azure jako n-ty centrum danych.

## <a name="optimize-uptime-and-performance"></a>Optymalizowanie czasu przestoju i wydajności
Jeśli usługa nie działa, nie można uzyskać dostępu do informacji. Jeśli wydajność jest tak niska, że dane są bezużyteczne, możesz uznać, że dane będą niedostępne. Z punktu widzenia zabezpieczeń należy się upewnić, że usługi mają optymalny czas pracy i wydajność.

Popularną i efektywną metodą zwiększenia dostępności i wydajności jest równoważenie obciążenia. Równoważenie obciążenia to metoda dystrybucji ruchu sieciowego między serwerami, które są częścią usługi. Na przykład w przypadku serwerów frontonu sieci Web w ramach usługi można użyć równoważenia obciążenia do dystrybucji ruchu na wielu serwerach frontonu sieci Web.

Ta dystrybucja ruchu zwiększa dostępność, ponieważ w przypadku niedostępności jednego z serwerów sieci Web moduł równoważenia obciążenia przestaje wysyłać ruch do tego serwera i przekierowuje go do serwerów, które są nadal w trybie online. Równoważenie obciążenia pomaga również w wydajności, ponieważ obciążenie procesora, sieci i pamięci dla obsługi żądań jest dystrybuowane na wszystkich serwerach z równoważeniem obciążenia.

Zalecamy stosowanie równoważenia obciążenia zawsze, gdy tylko jest to możliwe, i zgodnie z potrzebami usług. Poniżej znajdują się scenariusze zarówno na poziomie sieci wirtualnej platformy Azure, jak i na poziomie globalnym, a także opcje równoważenia obciążenia dla każdego z nich.

**Scenariusz** : masz aplikację, która:

- Wymaga żądań z tego samego użytkownika/sesji klienta, aby uzyskać dostęp do tej samej maszyny wirtualnej zaplecza. Przykładami są aplikacje koszyka zakupów i serwery poczty sieci Web.
- Akceptuje tylko bezpieczne połączenie, dlatego nie jest to akceptowalna opcja komunikacji nieszyfrowanej z serwerem.
- Wymaga wielu żądań HTTP w ramach tego samego długotrwałego połączenia TCP do kierowania lub równoważenia obciążenia do różnych serwerów zaplecza.

**Opcja równoważenia obciążenia** : Użyj [usługi Azure Application Gateway](../../application-gateway/overview.md), modułu równoważenia obciążenia sieci Web http. Application Gateway obsługuje kompleksowe szyfrowanie protokołu TLS i [zakończenie protokołu TLS](../../application-gateway/overview.md) na bramie. Serwery sieci Web mogą być następnie wycofywane z obciążeń szyfrowania i odszyfrowywania, a ruch przepływający z niezaszyfrowanych serwerów zaplecza.

**Scenariusz** : należy równoważyć obciążenie połączeń przychodzących z Internetu między serwerami znajdującymi się w sieci wirtualnej platformy Azure. Scenariusze są następujące:

- Aplikacje bezstanowe, które akceptują żądania przychodzące z Internetu.
- Nie wymagaj sesji programu Sticky lub odciążania TLS. Sesje usługi Sticky Notes to metoda używana z równoważeniem obciążenia aplikacji w celu osiągnięcia koligacji serwera.

**Opcja równoważenia obciążenia** : Użyj Azure Portal, aby [utworzyć zewnętrzny moduł równoważenia obciążenia](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) , który rozkłada przychodzące żądania na wiele maszyn wirtualnych, aby zapewnić wyższy poziom dostępności.

**Scenariusz** : należy zrównoważyć obciążenie z maszyn wirtualnych, które nie znajdują się w Internecie. W większości przypadków połączenia akceptowane na potrzeby równoważenia obciążenia są inicjowane przez urządzenia w sieci wirtualnej platformy Azure, takie jak wystąpienia SQL Server lub wewnętrzne serwery sieci Web.   
**Opcja równoważenia obciążenia** : Użyj Azure Portal, aby [utworzyć wewnętrzny moduł równoważenia obciążenia](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) , który rozprasza przychodzące żądania na wielu maszynach wirtualnych w celu zapewnienia wyższego poziomu dostępności.

**Scenariusz** : wymagane jest globalne Równoważenie obciążenia, ponieważ:

- Rozwiązanie w chmurze, które jest szeroko dystrybuowane w wielu regionach i wymaga najwyższego poziomu czasu pracy.
- Konieczny jest najwyższy możliwy stopień przestoju, aby upewnić się, że usługa jest dostępna, nawet jeśli całe centrum danych przestanie być dostępne.

**Opcja równoważenia obciążenia** : Użyj usługi Azure Traffic Manager. Traffic Manager umożliwia Równoważenie obciążenia połączeń z usługami na podstawie lokalizacji użytkownika.

Na przykład jeśli użytkownik wysyła żądanie do usługi z UE, połączenie jest kierowane do usług znajdujących się w centrum danych w Unii Europejskiej. Ta część Traffic Manager globalnego równoważenia obciążenia pomaga zwiększyć wydajność, ponieważ połączenie z najbliższym centrum danych jest szybsze niż łączenie z centrami danych, które są daleko od siebie.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Wyłącz dostęp RDP/SSH do maszyn wirtualnych
Można nawiązać połączenie z maszynami wirtualnymi platformy Azure przy użyciu protokołu [Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) i [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Protokoły te umożliwiają zarządzanie maszynami wirtualnymi z lokalizacji zdalnych i są używane jako standard w centrach danych.

Potencjalny problem z zabezpieczeniami związany z korzystaniem z tych protokołów przez Internet polega na tym, że atakujący mogą korzystać [z technik](https://en.wikipedia.org/wiki/Brute-force_attack) bezprawnego, aby uzyskać dostęp do usługi Azure Virtual Machines. Po uzyskaniu dostępu atakujący mogą korzystać z maszyny wirtualnej jako punktu uruchamiania w celu naruszenia zabezpieczeń innych maszyn w sieci wirtualnej, a nawet zaatakowania urządzeń sieciowych poza platformą Azure.

Zalecamy wyłączenie bezpośredniego dostępu RDP i SSH do maszyn wirtualnych platformy Azure z Internetu. Po wyłączeniu bezpośredniego dostępu do protokołu RDP i SSH z Internetu dostępne są inne opcje umożliwiające dostęp do tych maszyn wirtualnych na potrzeby zdalnego zarządzania.

**Scenariusz** : umożliwia jednemu użytkownikowi łączenie się z siecią wirtualną platformy Azure za pośrednictwem Internetu.   
**Opcja** : [Sieć VPN typu punkt-lokacja](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md) jest kolejnym terminem dla połączenia klienta/serwera dostępu zdalnego VPN. Po nawiązaniu połączenia punkt-lokacja użytkownik może używać protokołu RDP lub SSH do łączenia się z maszynami wirtualnymi znajdującymi się w sieci wirtualnej platformy Azure, z którymi użytkownik nawiązał połączenie za pośrednictwem sieci VPN typu punkt-lokacja. Przyjęto założenie, że użytkownik jest autoryzowany do uzyskiwania dostępu do tych maszyn wirtualnych.

Sieć VPN typu punkt-lokacja jest bezpieczniejsza niż bezpośrednie połączenia RDP lub SSH, ponieważ użytkownik musi uwierzytelnić się dwa razy przed nawiązaniem połączenia z maszyną wirtualną. Najpierw użytkownik musi uwierzytelnić się (i być autoryzowany) w celu ustanowienia połączenia sieci VPN typu punkt-lokacja. Następnie użytkownik musi uwierzytelnić się (i być autoryzowany) w celu nawiązania połączenia RDP lub SSH.

**Scenariusz** : Umożliwianie użytkownikom w sieci lokalnej łączenie się z maszynami wirtualnymi w sieci wirtualnej platformy Azure.   
**Opcja** : Sieć [VPN typu lokacja-lokacja](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) łączy całą sieć z inną siecią za pośrednictwem Internetu. Sieci VPN typu lokacja-lokacja można użyć, aby połączyć sieć lokalną z siecią wirtualną platformy Azure. Użytkownicy lokalnej sieci nawiązują połączenie za pomocą protokołu RDP lub SSH za pośrednictwem połączenia sieci VPN typu lokacja-lokacja. Nie trzeba zezwalać na bezpośredni dostęp do protokołu RDP lub SSH przez Internet.

**Scenariusz** : Użyj dedykowanego LINKu WAN, aby zapewnić funkcjonalność podobną do sieci VPN typu lokacja-lokacja.   
**Opcja** : Użyj [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Zapewnia funkcjonalność podobną do sieci VPN typu lokacja-lokacja. Główne różnice to:

- Dedykowany link WAN nie przechodzi przez Internet.
- Dedykowane linki WAN są zwykle bardziej stabilne i bardziej wydajne.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Zabezpiecz najważniejsze zasoby usługi platformy Azure tylko do sieci wirtualnych
Za pomocą punktów końcowych usługi sieci wirtualnej można zwiększyć prywatną przestrzeń adresową sieci wirtualnej i tożsamość sieci wirtualnej do usług platformy Azure za pośrednictwem bezpośredniego połączenia. Punkty końcowe umożliwiają zabezpieczanie krytycznych zasobów usługi platformy Azure tylko do sieci wirtualnych. Ruch z sieci wirtualnej do usługi platformy Azure zawsze pozostaje w sieci szkieletowej Microsoft Azure.

Punkty końcowe usługi oferują następujące korzyści:

- **Lepsze zabezpieczenia zasobów usługi platformy Azure** : dzięki punktom końcowym zasoby usługi platformy Azure mogą być chronione w sieci wirtualnej. Zabezpieczenie zasobów usługi w sieci wirtualnej zapewnia większe bezpieczeństwo dzięki całkowitemu uniemożliwieniu dostępu do tych zasobów z publicznego Internetu i zezwoleniu na ruch tylko z Twojej sieci wirtualnej.
- **Optymalny Routing ruchu usługi platformy Azure z sieci wirtualnej** : wszystkie trasy w sieci wirtualnej, które wymuszają ruch internetowy do urządzeń lokalnych i/lub wirtualnych, znane jako Wymuszone tunelowanie, wymuszają również ruch usługi platformy Azure w taki sam sposób jak ruch internetowy. Punkty końcowe usługi zapewniają optymalny routing ruchu platformy Azure.

  Punkty końcowe zawsze pobierają ruch bezpośrednio z sieci wirtualnej do usługi w sieci szkieletowej platformy Azure. Utrzymywanie ruchu w sieci szkieletowej platformy Azure umożliwia kontynuowanie inspekcji i monitorowania wychodzącego ruchu internetowego z sieci wirtualnych za pośrednictwem tunelowania wymuszonego bez wpływu na ruch usługi. Dowiedz się więcej na temat [tras zdefiniowanych przez użytkownika i wymuszonego tunelowania](../../virtual-network/virtual-networks-udr-overview.md).

- **Prosta konfiguracja z mniejszym obciążeniem zarządzania** : nie potrzebujesz już zarezerwowanych publicznych adresów IP w sieciach wirtualnych, aby zabezpieczyć zasoby platformy Azure za pomocą zapory IP. Do skonfigurowania punktów końcowych usługi nie jest wymagany translator adresów sieciowych ani urządzenie bramy. Punkty końcowe usługi można skonfigurować za pomocą prostego kliknięcia w podsieci. Obsługa punktów końcowych nie wiąże się z dodatkowymi kosztami.

Aby dowiedzieć się więcej o punktach końcowych usług oraz usługach i regionach platformy Azure, dla których są dostępne punkty końcowe usługi, zobacz [punkty końcowe usługi sieci wirtualnej](../../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="next-steps"></a>Następne kroki
Zobacz [najlepsze rozwiązania i wzorce dotyczące zabezpieczeń platformy Azure](best-practices-and-patterns.md) , aby uzyskać więcej najlepszych rozwiązań w zakresie zabezpieczeń, które są używane podczas projektowania i wdrażania rozwiązań w chmurze oraz zarządzania nimi przy użyciu platformy Azure.