---
title: Najważniejsze wskazówki dotyczące zabezpieczeń zasobów platformy Azure
titleSuffix: Azure security
description: Ten artykuł zawiera zestaw najlepszych rozwiązań operacyjnych dotyczących ochrony danych, aplikacji i innych zasobów na platformie Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 5724a9e4308f05a82df84ae6a7d5602747f5a140
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757359"
---
# <a name="azure-operational-security-best-practices"></a>Najważniejsze wskazówki dotyczące zabezpieczeń operacyjnych platformy Azure
Ten artykuł zawiera zestaw najlepszych rozwiązań operacyjnych dotyczących ochrony danych, aplikacji i innych zasobów na platformie Azure.

Najlepsze rozwiązania są oparte na konsensusie opinii i działają z bieżącymi możliwościami platformy Azure i zestawami funkcji. Opinie i technologie zmieniają się w czasie, a ten artykuł jest regularnie aktualizowany w celu odzwierciedlenia tych zmian.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Definiowanie i wdrażanie silnych praktyk w zakresie zabezpieczeń operacyjnych
Zabezpieczenia operacyjne platformy Azure odnoszą się do usług, formantów i funkcji dostępnych dla użytkowników w celu ochrony ich danych, aplikacji i innych zasobów na platformie Azure. Zabezpieczenia operacyjne platformy Azure są oparte na platformie, która zawiera wiedzę uzyskaną dzięki funkcjom unikatowym dla firmy Microsoft, w tym [cyklu życia rozwoju zabezpieczeń (SDL),](https://www.microsoft.com/sdl)programowi [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) i głębokiej świadomości krajobrazu zagrożeń związanych z cyberbezpieczeństwem.

## <a name="manage-and-monitor-user-passwords"></a>Zarządzanie hasłami użytkowników i monitorowanie ich
W poniższej tabeli wymieniono niektóre najważniejsze wskazówki dotyczące zarządzania hasłami użytkowników:

**Najlepsze rozwiązanie:** Upewnij się, że masz odpowiedni poziom ochrony hasłem w chmurze.   
**Szczegóły:** Postępuj zgodnie ze wskazówkami [dotyczącymi haseł](https://www.microsoft.com/research/publication/password-guidance/)firmy Microsoft , które są ograniczone do użytkowników platform tożsamości firmy Microsoft (usługi Azure Active Directory, usługi Active Directory i konta Microsoft).

**Najlepsze rozwiązanie:** monitoruj pod kątem podejrzanych działań związanych z kontami użytkowników.   
**Szczegóły:** Monitoruj [użytkowników zagrożonych](/azure/active-directory/reports-monitoring/concept-user-at-risk) i [ryzykownych logowaniach](../../active-directory/reports-monitoring/concept-risk-events.md) przy użyciu raportów zabezpieczeń usługi Azure AD.

**Najlepsze rozwiązanie:** Automatyczne wykrywanie i korygowanie haseł wysokiego ryzyka.   
**Szczegóły:** [Usługa Azure AD Identity Protection](/azure/active-directory/identity-protection/overview) jest funkcją usługi Azure AD Premium P2, która umożliwia:

- Wykrywanie potencjalnych luk w zabezpieczeniach, które mają wpływ na tożsamość organizacji
- Konfigurowanie automatycznych odpowiedzi do wykrywania podejrzanych akcji związanych z tożsamościami organizacji
- Badanie podejrzanych incydentów i podjęcie odpowiednich działań w celu ich rozwiązania

## <a name="receive-incident-notifications-from-microsoft"></a>Otrzymywanie powiadomień o zdarzeniach od firmy Microsoft
Upewnij się, że zespół operacji zabezpieczeń odbiera powiadomienia o zdarzeniach platformy Azure od firmy Microsoft. Powiadomienie o zdarzeniu informuje zespół zabezpieczeń, że masz naruszone zasoby platformy Azure, dzięki czemu mogą szybko reagować na potencjalne zagrożenia bezpieczeństwa i korygować je.

W portalu rejestracji platformy Azure można upewnić się, że informacje kontaktowe administratora zawierają szczegóły, które powiadamiają o operacjach zabezpieczeń. Dane kontaktowe to adres e-mail i numer telefonu.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Organizowanie subskrypcji platformy Azure w grupy zarządzania
Jeśli Twoja organizacja ma wiele subskrypcji, konieczny może być sposób na wydajne zarządzanie dostępem, zasadami i zgodnością dla tych subskrypcji. [Grupy zarządzania platformy Azure](/azure/governance/management-groups/create) zapewniają poziom zakresu, który jest powyżej subskrypcji. Organizujesz subskrypcje w kontenerach nazywanych grupami zarządzania i stosujesz warunki nadzoru do grup zarządzania. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą warunki zastosowane do tej grupy zarządzania.

Można utworzyć elastyczną strukturę grup zarządzania i subskrypcji w katalogu. Każdy katalog otrzymuje jedną grupę zarządzania najwyższego poziomu o nazwie główna grupa zarządzania. Główna grupa zarządzania jest wbudowana w hierarchię, aby wszystkie grupy zarządzania i subskrypcje pod nią podlegały. Główna grupa zarządzania umożliwia stosowanie zasad globalnych i przypisań RBAC na poziomie katalogu.

Oto kilka sprawdzonych rozwiązań dotyczących korzystania z grup zarządzania:

**Najlepsze rozwiązanie:** Upewnij się, że nowe subskrypcje stosują elementy nadzoru, takie jak zasady i uprawnienia, gdy są dodawane.   
**Szczegóły:** Użyj głównej grupy zarządzania, aby przypisać elementy zabezpieczeń dla całego przedsiębiorstwa, które mają zastosowanie do wszystkich zasobów platformy Azure. Zasady i uprawnienia są przykładami elementów.

**Najlepsze rozwiązanie:** Wyrównaj najwyższe poziomy grup zarządzania ze strategią segmentacji, aby zapewnić punkt kontroli i spójności zasad w każdym segmencie.   
**Szczegóły:** Utwórz jedną grupę zarządzania dla każdego segmentu w głównej grupie zarządzania. Nie należy tworzyć żadnych innych grup zarządzania w katalogu głównym.

**Najlepsze rozwiązanie:** Ogranicz głębokość grupy zarządzania, aby uniknąć nieporozumień, które utrudniają zarówno operacje, jak i zabezpieczenia.   
**Szczegóły:** Ogranicz hierarchię do trzech poziomów, w tym katalogu głównego.

**Najlepsze rozwiązanie:** Ostrożnie wybierz elementy, które mają być stosowane do całej jednostki z główną grupą zarządzania.   
**Szczegóły:** Upewnij się, że główne elementy grupy zarządzania mają wyraźną potrzebę zastosowania w każdym zasobie i że mają niewielki wpływ.

Dobrymi kandydatami są:

- Wymogi regulacyjne, które mają wyraźny wpływ na działalność (na przykład ograniczenia związane z suwerennością danych)
- Wymagania z niemal zerowym potencjalnym negatywnym wpływem na operacje, takie jak zasady z efektem inspekcji lub przypisania uprawnień RBAC, które zostały dokładnie sprawdzone

**Najlepsze rozwiązanie:** Starannie zaplanować i przetestować wszystkie zmiany w całej organizacji w głównej grupie zarządzania przed zastosowaniem ich (zasady, model RBAC i tak dalej).   
**Szczegóły:** Zmiany w głównej grupie zarządzania mogą mieć wpływ na każdy zasób na platformie Azure. Chociaż zapewniają one skuteczny sposób zapewnienia spójności w całym przedsiębiorstwie, błędy lub niepoprawne użycie mogą negatywnie wpłynąć na operacje produkcyjne. Przetestuj wszystkie zmiany w głównej grupie zarządzania w laboratorium testowym lub pilotażowym produkcji.

## <a name="streamline-environment-creation-with-blueprints"></a>Usprawnij tworzenie środowiska dzięki planom
[Usługa Azure Blueprints](/azure/governance/blueprints/overview) umożliwia architektom chmury i centralnym grupom technologii informacyjnych definiowanie powtarzalny zestaw zasobów platformy Azure, który implementuje i przestrzega standardów, wzorców i wymagań organizacji. Plany platformy Azure umożliwiają zespołom deweloperskim szybkie tworzenie i tworzenie nowych środowisk za pomocą zestawu wbudowanych składników i pewności, że tworzą te środowiska w ramach zgodności z organizacjami.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorowanie usług pamięci masowej pod kątem nieoczekiwanych zmian w zachowaniu
Diagnozowanie i rozwiązywanie problemów w rozproszonej aplikacji hostowane w środowisku chmury może być bardziej skomplikowane niż w tradycyjnych środowiskach. Aplikacje można wdrażać w infrastrukturze PaaS lub IaaS, lokalnie, na urządzeniu przenośnym lub w niektórych kombinacjach tych środowisk. Ruch sieciowy aplikacji może przechodzić przez sieci publiczne i prywatne, a aplikacja może korzystać z wielu technologii magazynowania.

Należy stale monitorować usługi magazynu używane przez aplikację pod kątem nieoczekiwanych zmian w zachowaniu (takich jak wolniejsze czasy odpowiedzi). Rejestrowanie służy do zbierania bardziej szczegółowych danych i dogłębnej analizy problemu. Informacje diagnostyczne, które można uzyskać zarówno z monitorowania i rejestrowania pomaga określić główną przyczynę problemu, który napotkał aplikację. Następnie można rozwiązać problem i określić odpowiednie kroki, aby go rozwiązać.

[Usługa Azure Storage Analytics](../../storage/common/storage-analytics.md) wykonuje rejestrowanie i udostępnia dane metryki dla konta magazynu platformy Azure. Zaleca się używanie tych danych do śledzenia żądań, analizowania trendów użycia i diagnozowania problemów z kontem magazynu.

## <a name="prevent-detect-and-respond-to-threats"></a>Zapobieganie zagrożeniom, wykrywanie ich i reagowanie na nie
[Usługa Azure Security Center](../../security-center/security-center-intro.md) pomaga zapobiegać zagrożeniom, wykrywać ich i reagować na nie, zapewniając lepszy wgląd w zabezpieczenia zasobów platformy Azure (i kontrolę nad nim). Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogą przejść niezauważone, i współpracuje z różnymi rozwiązaniami zabezpieczeń.

Bezpłatna warstwa usługi Security Center oferuje ograniczone zabezpieczenia tylko dla zasobów platformy Azure. Warstwa Standardowa rozszerza te możliwości na chmury lokalne i inne. Usługa Security Center Standard pomaga znajdować i naprawiać luki w zabezpieczeniach, stosować mechanizmy kontroli dostępu i aplikacji w celu blokowania złośliwej aktywności, wykrywania zagrożeń przy użyciu analizy i analizy oraz szybkiego reagowania podczas ataku. Standardową warstwę cenową usługi Security Center możesz wypróbować bezpłatnie przez pierwsze 60 dni. Zaleca się [uaktualnienie subskrypcji platformy Azure do usługi Security Center Standard](../../security-center/security-center-get-started.md).

Użyj usługi Security Center, aby uzyskać centralny widok stanu zabezpieczeń wszystkich zasobów platformy Azure. Na pierwszy rzut oka sprawdź, czy odpowiednie zabezpieczenia są zainstalowane i poprawnie skonfigurowane, i szybko zidentyfikować wszystkie zasoby, które wymagają uwagi.

Usługa Security Center integruje się również z programem [Microsoft Defender Advanced Threat Protection (ATP),](../../security-center/security-center-wdatp.md)który zapewnia kompleksowe funkcje wykrywania i reagowania punktów końcowych (EDR). Dzięki integracji z programem Microsoft Defender ATP można wykryć nieprawidłowości. Można również wykrywać ataki zaawansowane na punkty końcowe serwera monitorowane przez usługę Security Center i reagować na nie.

Prawie wszystkie organizacje przedsiębiorstw mają system zarządzania informacjami i zdarzeniami (SIEM), który pomaga w identyfikowaniu pojawiających się zagrożeń poprzez konsolidację informacji dziennika z różnych urządzeń do zbierania sygnałów. Dzienniki są następnie analizowane przez system analizy danych, aby pomóc zidentyfikować to, co jest "interesujące" z hałasu, który jest nieunikniony we wszystkich rozwiązaniach do zbierania i analizy dzienników.

[Usługa Azure Sentinel](/azure/sentinel/overview) to skalowalne rozwiązanie do zarządzania informacjami i zdarzeniami (SIEM) oraz automatyczną odpowiedzią na zagrożenia zabezpieczeń (SOAR). Usługa Azure Sentinel zapewnia inteligentną analizę zabezpieczeń i analizę zagrożeń za pomocą wykrywania alertów, widoczności zagrożeń, proaktywnego polowania i automatycznej reakcji na zagrożenia.

Oto kilka sprawdzonych rozwiązań dotyczących zapobiegania zagrożeniom, wykrywania ich i reagowania na nie:

**Najlepsze rozwiązanie:** Zwiększ szybkość i skalowalność rozwiązania SIEM przy użyciu chmurowego SIEM.   
**Szczegóły:** Zbadaj funkcje i możliwości [usługi Azure Sentinel](/azure/sentinel/overview) i porównaj je z możliwościami tego, co obecnie używasz lokalnie. Należy rozważyć przyjęcie usługi Azure Sentinel, jeśli spełnia wymagania SIEM twojej organizacji.

**Najlepsze rozwiązanie:** Znajdź najpoważniejsze luki w zabezpieczeniach, aby ustalić priorytety badania.   
**Szczegóły:** Przejrzyj [bezpieczny wynik platformy Azure,](../../security-center/secure-score-security-controls.md) aby wyświetlić zalecenia wynikające z zasad i inicjatyw platformy Azure wbudowanych w usługę Azure Security Center. Zalecenia te pomagają rozwiązać najważniejsze zagrożenia, takie jak aktualizacje zabezpieczeń, ochrona punktów końcowych, szyfrowanie, konfiguracje zabezpieczeń, brak waf, maszyny wirtualne podłączone do Internetu i wiele innych.

Bezpieczny wynik, który jest oparty na formanty Center for Internet Security (CIS), umożliwia porównanie zabezpieczeń platformy Azure w organizacji ze źródłami zewnętrznymi. Zewnętrzna weryfikacja pomaga zweryfikować i wzbogacić strategię zabezpieczeń zespołu.

**Najlepsze rozwiązanie:** Monitorowanie postawy zabezpieczeń maszyn, sieci, usług pamięci masowej i danych oraz aplikacji w celu wykrywania i określania priorytetów potencjalnych problemów z bezpieczeństwem.  
**Szczegóły:** Postępuj zgodnie z [zaleceniami dotyczącymi zabezpieczeń](../../security-center/security-center-recommendations.md) w u źródła zabezpieczeń, począwszy od elementów o najwyższym priorytecie.

**Najlepsze rozwiązanie:** Integruj alerty w centrum zabezpieczeń z rozwiązaniem do zarządzania informacjami o zabezpieczeniach i zdarzeniami (SIEM).   
**Szczegóły:** Większość organizacji z SIEM używać go jako centralnej ejrze dla alertów zabezpieczeń, które wymagają odpowiedzi analityka. Przetworzone zdarzenia wywoływane przez usługę Security Center są publikowane w dzienniku aktywności platformy Azure, jednym z dzienników dostępnych za pośrednictwem usługi Azure Monitor. Usługa Azure Monitor oferuje skonsolidowany potok do routingu dowolnych danych monitorowania do narzędzia SIEM. Aby uzyskać [instrukcje, zobacz Eksportowanie alertów zabezpieczeń i zaleceń.](../../security-center/continuous-export.md#configuring-siem-integration-via-azure-event-hubs) Jeśli korzystasz z usługi Azure Sentinel, zobacz [Connect Azure Security Center](../../sentinel/connect-azure-security-center.md).

**Najlepsze rozwiązanie:** Integruj dzienniki platformy Azure z siem.   
**Szczegóły:** Zbieranie [i eksportowanie danych](/azure/azure-monitor/overview#integrate-and-export-data)za pomocą usługi Azure Monitor . Ta praktyka ma kluczowe znaczenie dla włączania badania incydentów zabezpieczeń, a przechowywanie dzienników online jest ograniczone. Jeśli korzystasz z usługi Azure Sentinel, zobacz [Łączenie źródeł danych](../../sentinel/connect-data-sources.md).

**Najlepsze praktyki:** Przyspiesz procesy dochodzeniowe i łowieckie oraz zmniejsz liczbę fałszywych alarmów, integrując funkcje wykrywania i reagowania punktów końcowych (EDR) w badaniu ataku.   
**Szczegóły:** [Włącz integrację usługi Microsoft Defender ATP](../../security-center/security-center-wdatp.md#enable-microsoft-defender-atp-integration) za pośrednictwem zasad zabezpieczeń centrum zabezpieczeń. Należy rozważyć użycie usługi Azure Sentinel do polowania na zagrożenia i reagowania na incydenty.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Monitorowanie kompleksowego monitorowania sieci opartego na scenariuszach
Klienci twórz sieć end-to-end na platformie Azure, łącząc zasoby sieciowe, takie jak sieć wirtualna, usługa ExpressRoute, brama aplikacji i moduły równoważenia obciążenia. Monitorowanie jest dostępne w każdym zasobach sieciowych.

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) to usługa regionalna. Użyj jego narzędzi diagnostycznych i wizualizacji do monitorowania i diagnozowania warunków na poziomie scenariusza sieci w, do i z platformy Azure.

Poniżej przedstawiono najlepsze rozwiązania dotyczące monitorowania sieci i dostępnych narzędzi.

**Najlepsze rozwiązanie:** Automatyzacja zdalnego monitorowania sieci za pomocą przechwytywania pakietów.  
**Szczegóły:** Monitoruj i diagnozuj problemy z siecią bez logowania się do maszyn wirtualnych za pomocą funkcji Kontrola sieci. Wyzwalanie [przechwytywania pakietów](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) przez ustawianie alertów i uzyskiwanie dostępu do informacji o wydajności w czasie rzeczywistym na poziomie pakietu. Po wyświetleniu problemu można szczegółowo zbadać, aby uzyskać lepsze diagnozy.

**Najlepsze rozwiązanie:** Uzyskaj wgląd w ruch sieciowy za pomocą dzienników przepływu.  
**Szczegóły:** Zbuduj głębsze zrozumienie wzorców ruchu sieciowego za pomocą [dzienników przepływu sieciowej grupy zabezpieczeń](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). Informacje w dziennikach przepływu ułatwiają zbieranie danych w celu zapewnienia zgodności, inspekcji i monitorowania profilu zabezpieczeń sieci.

**Najlepsze rozwiązanie**: Diagnozowanie problemów z łącznością sieci VPN.  
**Szczegóły:** Użyj funkcji Kontrola sieci, aby [zdiagnozować najczęstsze problemy z bramą sieci VPN i połączeniem.](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md) Można nie tylko zidentyfikować problem, ale także użyć szczegółowych dzienników do dalszego zbadania.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Bezpieczne wdrażanie przy użyciu sprawdzonych narzędzi DevOps
Skorzystaj z poniższych najlepszych rozwiązań devops, aby upewnić się, że twoje przedsiębiorstwo i zespoły są wydajne i wydajne.

**Najlepsze rozwiązanie:** Automatyzacja tworzenia i wdrażania usług.  
**Szczegóły:** [Infrastruktura jako kod](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) to zestaw technik i praktyk, które pomagają profesjonalistom IT usunąć ciężar codziennego tworzenia infrastruktury modułowej i zarządzania nią. Umożliwia ono profesjonalistom IT tworzenie i utrzymywanie ich nowoczesnego środowiska serwera w sposób, który przypomina sposób tworzenia i obsługi kodu aplikacji przez programistów.

Usługi [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) można użyć do aprowizowania aplikacji przy użyciu szablonu deklaratywnego. Pojedynczy szablon umożliwia wdrożenie wielu usług wraz z ich zależnościami. Ten sam szablon służy do wielokrotnego wdrażania aplikacji na każdym etapie cyklu życia aplikacji.

**Najlepsze rozwiązanie:** Automatyczne tworzenie i wdrażanie w aplikacjach sieci Web platformy Azure lub usługach w chmurze.  
**Szczegóły:** Można skonfigurować projekty programu Azure DevOps do [automatycznego tworzenia i wdrażania](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) w aplikacjach sieci Web platformy Azure lub usługach w chmurze. Usługa Azure DevOps automatycznie wdraża pliki binarne po wykonaniu kompilacji na platformie Azure po każdym zaewidencjonowaniu kodu. Proces kompilacji pakietu jest odpowiednikiem package polecenia w programie Visual Studio, a kroki publikowania są równoważne polecenia Publish w programie Visual Studio.

**Najlepsze rozwiązanie:** Automatyzacja zarządzania wersjami.  
**Szczegóły:** [Usługa Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) to rozwiązanie do automatyzacji wdrażania wieloetapowego i zarządzania procesem wydawania. Tworzenie zarządzanych potoków ciągłego wdrażania, aby szybko, łatwo i często je zwolnić. Za pomocą usługi Azure Pipelines można zautomatyzować proces wydawania i można mieć wstępnie zdefiniowane przepływy pracy zatwierdzania. Wdrażaj lokalnie i w chmurze, rozszerzaj i dostosowywaj w razie potrzeby.

**Najlepsze rozwiązanie:** sprawdź wydajność aplikacji przed jej uruchomieniem lub wdrożeniem aktualizacji w produkcji.  
**Szczegóły**: Uruchom [testy obciążenia](/azure/devops/test/load-test/overview#alternatives) oparte na chmurze, aby:

- Znajdź problemy z wydajnością w aplikacji.
- Poprawa jakości wdrażania.
- Upewnij się, że aplikacja jest zawsze dostępna.
- Upewnij się, że aplikacja może obsługiwać ruch podczas następnego uruchomienia lub kampanii marketingowej.

[Apache JMeter](https://jmeter.apache.org/) to darmowe, popularne narzędzie open source z silnym poparciem społeczności.

**Najlepsze rozwiązanie:** Monitorowanie wydajności aplikacji.  
**Szczegóły:** [Usługa Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) to usługa rozszerzalnego zarządzania wydajnością aplikacji (APM) dla deweloperów sieci web na wielu platformach. Usługa Application Insights służy do monitorowania aplikacji sieci web na żywo. Automatycznie wykrywa anomalie wydajności. Zawiera narzędzia analityczne ułatwiające diagnozowanie problemów i zrozumienie, co użytkownicy faktycznie robią z aplikacją. Usługa ta pomaga w ciągłym udoskonalaniu wydajności i użyteczności tworzonych rozwiązań.

## <a name="mitigate-and-protect-against-ddos"></a>Ograniczanie i ochrona przed atakami DDoS
Rozproszona odmowa usługi (DDoS) jest rodzajem ataku, który próbuje wyczerpywać zasoby aplikacji. Celem jest wpłynąć na dostępność aplikacji i jej zdolność do obsługi uzasadnionych żądań. Ataki te stają się coraz bardziej wyrafinowane i większe pod względem wielkości i wpływu. Mogą być kierowane do dowolnego punktu końcowego, który jest publicznie dostępny za pośrednictwem Internetu.

Projektowanie i tworzenie odporności DDoS wymaga planowania i projektowania dla różnych trybów awarii. Poniżej przedstawiono najlepsze rozwiązania dotyczące tworzenia usług odpornych na DDoS na platformie Azure.

**Najlepsze rozwiązanie:** Upewnij się, że zabezpieczenia są priorytetem w całym cyklu życia aplikacji, od projektowania i implementacji do wdrażania i operacji. Aplikacje mogą mieć błędy, które umożliwiają stosunkowo małą liczbę żądań do korzystania z wielu zasobów, co powoduje awarię usługi.  
**Szczegóły:** Aby chronić usługę działającą na platformie Microsoft Azure, należy dobrze zrozumieć architekturę aplikacji i skupić się na [pięciu filarach jakości oprogramowania.](https://docs.microsoft.com/azure/architecture/guide/pillars) Należy znać typowe woluminy ruchu, model łączności między aplikacją i innymi aplikacjami oraz punkty końcowe usługi, które są narażone na publiczny internet.

Zapewnienie, że aplikacja jest wystarczająco odporna do obsługi typu "odmowa usługi", która jest przeznaczona dla samej aplikacji, jest najważniejsza. Zabezpieczenia i prywatność są wbudowane w platformę Azure, począwszy od [cyklu cyklu bezpieczeństwa rozwoju (SDL).](https://www.microsoft.com/sdl) SDL adresy zabezpieczeń na każdym etapie rozwoju i zapewnia, że platforma Azure jest stale aktualizowana, aby uczynić go jeszcze bardziej bezpieczne.

**Najlepsze rozwiązanie:** Zaprojektuj aplikacje do [skalowania poziomego,](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) aby sprostać zapotrzebowaniu wzmocnionego obciążenia, w szczególności w przypadku ataku DDoS. Jeśli aplikacja zależy od pojedynczego wystąpienia usługi, tworzy pojedynczy punkt awarii. Inicjowanie obsługi administracyjnej wielu wystąpień sprawia, że system jest bardziej odporny i bardziej skalowalny.  
**Szczegóły:** Dla [usługi Azure App Service](/azure/app-service/app-service-value-prop-what-is)wybierz plan usługi app [service,](../../app-service/overview-hosting-plans.md) który oferuje wiele wystąpień.

W przypadku usług Azure Cloud Services skonfiguruj każdą z ról, aby [używały wielu wystąpień.](../../cloud-services/cloud-services-choose-me.md)

W przypadku [maszyn wirtualnych platformy Azure](/azure/virtual-machines/windows/overview)upewnij się, że architektura maszyny wirtualnej zawiera więcej niż jedną maszynę wirtualną i że każda maszyna wirtualna jest uwzględniona w zestawie [dostępności.](/azure/virtual-machines/virtual-machines-windows-manage-availability) Zaleca się używanie zestawów skalowania maszyny wirtualnej do skalowania automatycznego.

**Najlepsze rozwiązanie:** Nakładanie warstw zabezpieczeń w aplikacji zmniejsza ryzyko pomyślnego ataku. Zaimplementuj bezpieczne projekty dla aplikacji przy użyciu wbudowanych możliwości platformy Azure.  
**Szczegóły:** Ryzyko ataku wzrasta wraz z rozmiarem (powierzchnią) aplikacji. Powierzchnię można zmniejszyć, korzystając z białej listy w celu zamknięcia narażonej przestrzeni adresowej IP i portów nasłuchujących, które nie są potrzebne w modułach równoważenia obciążenia[(Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) i [Azure Application Gateway).](/azure/application-gateway/application-gateway-create-probe-portal)

[Sieciowe grupy zabezpieczeń](../../virtual-network/security-overview.md) to kolejny sposób na zmniejszenie powierzchni ataku. Za pomocą [tagów usług](../../virtual-network/security-overview.md#service-tags) i [grup zabezpieczeń aplikacji](../../virtual-network/security-overview.md#application-security-groups) można minimalizować złożoność tworzenia reguł zabezpieczeń i konfigurowania zabezpieczeń sieciowych jako naturalnego rozszerzenia struktury aplikacji.

Usługi platformy Azure należy wdrożyć w [sieci wirtualnej,](../../virtual-network/virtual-networks-overview.md) gdy tylko jest to możliwe. Ta praktyka umożliwia zasobów usługi do komunikowania się za pośrednictwem prywatnych adresów IP. Ruch usługi platformy Azure z sieci wirtualnej domyślnie używa publicznych adresów IP jako źródłowych adresów IP.

Za pomocą [punktów końcowych usługi](../../virtual-network/virtual-network-service-endpoints-overview.md) przełącza ruch usługi do używania adresów prywatnych sieci wirtualnej jako źródłowych adresów IP, gdy uzyskujesz dostęp do usługi platformy Azure z sieci wirtualnej.

Często widzimy, że zasoby lokalne klientów są atakowane wraz z ich zasobami na platformie Azure. Jeśli łączysz środowisko lokalne z platformą Azure, zminimalizuj narażenie zasobów lokalnych na publiczny Internet.

Platforma Azure ma dwie [oferty usług](../../virtual-network/ddos-protection-overview.md) DDoS, które zapewniają ochronę przed atakami sieciowymi:

- Podstawowa ochrona jest domyślnie zintegrowana z platformą Azure bez dodatkowych kosztów. Skala i pojemność globalnie wdrożonej sieci platformy Azure zapewnia odporność na typowe ataki w warstwie sieciowej za pośrednictwem zawsze włączonego monitorowania ruchu i ograniczania ryzyka w czasie rzeczywistym. Basic nie wymaga żadnych zmian konfiguracji użytkownika ani aplikacji i pomaga chronić wszystkie usługi platformy Azure, w tym usługi PaaS, takie jak usługa Azure DNS.
- Standardowa ochrona zapewnia zaawansowane możliwości ograniczania ddos przed atakami sieciowymi. Jest automatycznie dostrojony do ochrony określonych zasobów platformy Azure. Ochrona jest prosta do włączenia podczas tworzenia sieci wirtualnych. Można to również zrobić po utworzeniu i nie wymaga żadnych zmian aplikacji lub zasobów.

## <a name="enable-azure-policy"></a>Włącz zasady platformy Azure
[Zasady platformy Azure](/azure/governance/policy/overview) to usługa na platformie Azure używana do tworzenia, przypisywania i zarządzania zasadami. Te zasady wymuszają reguły i efekty dotyczące zasobów, dzięki czemu zasoby te pozostają zgodne ze standardami firmowymi i umowami dotyczącymi poziomu usług. Usługa Azure Policy spełnia to wymaganie, oceniając zasoby pod kątem niezgodności z przypisanymi zasadami.

Włącz zasady platformy Azure, aby monitorować i wymuszać pisemne zasady organizacji. Zapewni to zgodność z wymaganiami firmy lub wymogami zabezpieczeń regulacyjnych dzięki centralnemu zarządzaniu zasadami zabezpieczeń w ramach obciążeń chmury hybrydowej. Dowiedz się, jak [tworzyć zasady i zarządzać nimi, aby wymusić zgodność](../../governance/policy/tutorials/create-and-manage.md). Zobacz [struktury definicji usługi Azure Policy,](../../governance/policy/concepts/definition-structure.md) aby uzyskać omówienie elementów zasad.

Oto kilka najlepszych rozwiązań w zakresie zabezpieczeń, które należy wykonać po przyjęciu zasad platformy Azure:

**Najlepsze rozwiązanie**: Zasady obsługuje kilka rodzajów efektów. Można o nich przeczytać w [strukturze definicji usługi Azure Policy](../../governance/policy/concepts/definition-structure.md#policy-rule). Na operacje biznesowe może mieć negatywny wpływ efekt **odmowy** i efekt **korygowania,** więc zacznij od efektu **inspekcji,** aby ograniczyć ryzyko negatywnego wpływu polityki.   
**Szczegóły:** [Uruchom wdrożenia zasad w trybie inspekcji,](../../governance/policy/concepts/definition-structure.md#policy-rule) a następnie postępuj, aby **odmówić** lub **skorygować**. Przetestuj i przejrzyj wyniki efektu inspekcji przed przejściem do **odmowy** lub **skorygowanie**.

Aby uzyskać więcej informacji, zobacz Tworzenie zasad i zarządzanie nimi w [celu wymuszania zgodności](../../governance/policy/tutorials/create-and-manage.md).

**Najlepsze praktyki:** Określ role odpowiedzialne za monitorowanie naruszeń zasad i zapewnienie szybkiego podjęcia odpowiednich działań naprawczych.   
**Szczegóły:** Należy monitorować zgodność przypisanej roli za pośrednictwem [witryny Azure portal](../../governance/policy/how-to/get-compliance-data.md#portal) lub za pośrednictwem [wiersza polecenia](../../governance/policy/how-to/get-compliance-data.md#command-line).

**Najlepsze rozwiązanie:** Zasady platformy Azure to techniczna reprezentacja pisemnych zasad organizacji. Zamapuj wszystkie zasady platformy Azure na zasady organizacyjne, aby zmniejszyć zamieszanie i zwiększyć spójność.   
**Szczegóły:** Mapowanie dokumentów w dokumentacji organizacji lub w samej polityce platformy Azure przez dodanie odwołania do zasad organizacyjnych w [opisie zasad](../../governance/policy/concepts/definition-structure.md#display-name-and-description) platformy Azure lub opisie [inicjatywy dotyczącej](../../governance/policy/concepts/definition-structure.md#initiatives) zasad platformy Azure.

## <a name="monitor-azure-ad-risk-reports"></a>Monitorowanie raportów o ryzyku usługi Azure AD
Zdecydowana większość naruszeń zabezpieczeń ma miejsce, gdy osoby atakujące uzyskują dostęp do środowiska, kradnąc tożsamość użytkownika. Odnajdowanie naruszonych tożsamości nie jest łatwym zadaniem. Usługa Azure AD używa adaptacyjnych algorytmów uczenia maszynowego i heurystyki do wykrywania podejrzanych akcji, które są związane z kontami użytkowników. Każda wykryta podejrzana akcja jest przechowywana w rekordzie zwanym [wykrywaniem ryzyka.](../../active-directory/reports-monitoring/concept-risk-events.md) Wykrywanie ryzyka są rejestrowane w raportach zabezpieczeń usługi Azure AD. Aby uzyskać więcej informacji, przeczytaj o [raporcie zabezpieczeń użytkowników zagrożonych](../../active-directory/reports-monitoring/concept-user-at-risk.md) i [raporcie zabezpieczeń ryzykownych logowania](../../active-directory/reports-monitoring/concept-risky-sign-ins.md).

## <a name="next-steps"></a>Następne kroki
Zobacz [najlepsze rozwiązania i wzorce zabezpieczeń platformy Azure,](best-practices-and-patterns.md) aby uzyskać więcej najlepszych rozwiązań w zakresie zabezpieczeń, których należy używać podczas projektowania, wdrażania i zarządzania rozwiązaniami w chmurze przy użyciu platformy Azure.

Dostępne są następujące zasoby, aby zapewnić bardziej ogólne informacje na temat zabezpieczeń platformy Azure i powiązanych usług firmy Microsoft:
* [Blog zespołu zabezpieczeń platformy Azure](https://blogs.msdn.microsoft.com/azuresecurity/) — aby uzyskać aktualne informacje na temat najnowszych informacji w usłudze Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) — gdzie można zgłaszać luki w zabezpieczeniach firmy Microsoft, w tym problemy z platformą Azure, lubsecure@microsoft.com
