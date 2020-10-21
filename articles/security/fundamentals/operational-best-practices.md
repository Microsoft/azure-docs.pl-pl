---
title: Najlepsze rozwiązania w zakresie zabezpieczeń dotyczące zasobów platformy Azure
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
ms.openlocfilehash: 2c949447635ccdf4cf36acec43a09c1104b9fdd4
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92280026"
---
# <a name="azure-operational-security-best-practices"></a>Najlepsze rozwiązania z zakresu zabezpieczeń operacyjnych platformy Azure
Ten artykuł zawiera zestaw najlepszych rozwiązań operacyjnych dotyczących ochrony danych, aplikacji i innych zasobów na platformie Azure.

Najlepsze rozwiązania są oparte na konsensusie opinii i współpracują z bieżącymi funkcjami platformy Azure i zestawami funkcji. Opinie i technologie zmieniają się wraz z upływem czasu, a ten artykuł jest regularnie aktualizowany w celu odzwierciedlenia tych zmian.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Zdefiniuj i Wdróż mocne praktyki dotyczące zabezpieczeń operacyjnych
Zabezpieczenia operacyjne platformy Azure odnoszą się do usług, formantów i funkcji dostępnych dla użytkowników w celu ochrony danych, aplikacji i innych zasobów na platformie Azure. Usługa Azure Operational Security jest oparta na strukturze, która obejmuje wiedzę uzyskaną na podstawie możliwości, które są unikatowe dla firmy Microsoft, w tym [cyklu projektowania zabezpieczeń (SDL)](https://www.microsoft.com/sdl), programu [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) , oraz głębokiej świadomości dotyczącej zagrożeń cyberbezpieczeństwa.

## <a name="manage-and-monitor-user-passwords"></a>Zarządzanie hasłami użytkowników i ich monitorowanie
W poniższej tabeli przedstawiono niektóre najlepsze rozwiązania związane z zarządzaniem hasłami użytkowników:

**Najlepsze rozwiązanie**: Upewnij się, że masz odpowiedni poziom ochrony hasłem w chmurze.   
**Szczegóły**: Postępuj zgodnie ze wskazówkami [zawartymi w temacie Wskazówki dotyczące haseł firmy Microsoft](https://www.microsoft.com/research/publication/password-guidance/), które są objęte zakresem użytkowników platform tożsamości firmy microsoft (Azure Active Directory, Active Directory i konto Microsoft).

**Najlepsze rozwiązanie**: Monitoruj podejrzane działania związane z kontami użytkowników.   
**Szczegóły**: Monitoruj [użytkowników na ryzyko](/azure/active-directory/reports-monitoring/concept-user-at-risk) i [ryzykowne logowania](../../active-directory/reports-monitoring/concept-risk-events.md) przy użyciu raportów zabezpieczeń usługi Azure AD.

**Najlepsze rozwiązanie**: automatyczne wykrywanie i korygowanie haseł o wysokim ryzyku.   
**Szczegóły**: [Azure AD Identity Protection](/azure/active-directory/identity-protection/overview) to funkcja Azure AD — wersja Premium P2 Edition, która umożliwia:

- Wykrywaj potencjalne luki w zabezpieczeniach, które mają wpływ na tożsamości organizacji
- Konfigurowanie zautomatyzowanych odpowiedzi w celu wykrycia podejrzanych działań, które są powiązane z tożsamościami organizacji
- Zbadaj podejrzane zdarzenia i podejmij odpowiednie działania w celu ich rozwiązania

## <a name="receive-incident-notifications-from-microsoft"></a>Odbieraj powiadomienia o zdarzeniach od firmy Microsoft
Upewnij się, że zespół ds. operacji zabezpieczeń otrzymuje powiadomienia o zdarzeniu platformy Azure od firmy Microsoft. Powiadomienie o zdarzeniu umożliwia zespołowi ds. zabezpieczeń poznanie naruszenia zasobów platformy Azure, dzięki czemu mogą oni szybko reagować na i skorygować potencjalne zagrożenia bezpieczeństwa.

W portalu rejestracji platformy Azure można upewnić się, że informacje kontaktowe administratora zawierają szczegóły powiadamiające o operacjach zabezpieczeń. Informacje kontaktowe to adres e-mail i numer telefonu.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Organizuj subskrypcje platformy Azure w grupach zarządzania
Jeśli Twoja organizacja ma wiele subskrypcji, konieczny może być sposób na wydajne zarządzanie dostępem, zasadami i zgodnością dla tych subskrypcji. [Grupy zarządzania platformy Azure](/azure/governance/management-groups/create) zapewniają poziom zakresu, który jest wyższy niż subskrypcje. Subskrypcje można organizować w kontenery nazywane grupami zarządzania i stosować warunki ładu do grup zarządzania. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą warunki zastosowane do tej grupy zarządzania.

Można utworzyć elastyczną strukturę grup zarządzania i subskrypcji w katalogu. Każdy katalog ma jedną grupę zarządzania najwyższego poziomu nazywaną główną grupą zarządzania. Główna grupa zarządzania jest wbudowana w hierarchię, aby wszystkie grupy zarządzania i subskrypcje pod nią podlegały. Główna Grupa zarządzania pozwala na stosowanie zasad globalnych i przypisań ról platformy Azure na poziomie katalogu.

Poniżej przedstawiono niektóre najlepsze rozwiązania dotyczące korzystania z grup zarządzania:

**Najlepsze rozwiązanie**: Upewnij się, że nowe subskrypcje stosują elementy ładu, takie jak zasady i uprawnienia w miarę ich dodawania.   
**Szczegóły**: Użyj głównej grupy zarządzania do przypisywania elementów zabezpieczeń dla całego przedsiębiorstwa, które są stosowane do wszystkich zasobów platformy Azure. Zasady i uprawnienia są przykładami elementów.

**Najlepsze rozwiązanie**: wyrównywanie najwyższego poziomu grup zarządzania z strategią segmentacji w celu zapewnienia punktu na potrzeby kontroli i spójności zasad w poszczególnych segmentach.   
**Szczegóły**: Utwórz pojedynczą grupę zarządzania dla każdego segmentu w ramach głównej grupy zarządzania. Nie należy tworzyć żadnych innych grup zarządzania w katalogu głównym.

**Najlepsze rozwiązanie**: ograniczanie głębokości grup zarządzania, aby uniknąć pomyłek, które hamują zarówno operacje, jak i zabezpieczenia.   
**Szczegóły**: Ogranicz hierarchię do trzech poziomów, w tym do katalogu głównego.

**Najlepsze rozwiązanie**: starannie wybieraj, które elementy mają być stosowane do całego przedsiębiorstwa z główną grupą zarządzania.   
**Szczegóły**: Upewnij się, że elementy głównych grup zarządzania mają niezbędny wpływ na wszystkie zasoby i że mają one niskie znaczenie.

Dobre kandydaci to:

- Wymagania prawne mające na celu wyraźny wpływ na działalność (na przykład ograniczenia dotyczące suwerenności danych)
- Wymagania o niezerowym negatywnym wpływie na operacje, takie jak zasady z efektem inspekcji lub przypisywanie uprawnień RBAC, które zostały starannie przejrzane

**Najlepsze rozwiązanie**: starannie Planuj i przetestuj wszystkie zmiany w całej firmie w głównej grupie zarządzania przed ich zastosowaniem (zasady, model RBAC itd.).   
**Szczegóły**: zmiany w głównej grupie zarządzania mogą mieć wpływ na każdy zasób na platformie Azure. Chociaż zapewniają one zaawansowany sposób zapewnienia spójności w całym przedsiębiorstwie, błędy lub nieprawidłowe użycie mogą negatywnie wpłynąć na operacje produkcyjne. Przetestuj wszystkie zmiany w głównej grupie zarządzania w laboratorium testowym lub pilotażu produkcyjnym.

## <a name="streamline-environment-creation-with-blueprints"></a>Usprawnianie tworzenia środowiska przy użyciu planów
Usługa [plany platformy Azure](/azure/governance/blueprints/overview) umożliwia architektom chmury i centralnym grupom technologii informatycznych Definiowanie powtarzalnych zestawów zasobów platformy Azure, które implementują i są zgodne ze standardami, wzorcami i wymaganiami organizacji. Plany platformy Azure umożliwiają zespołom programistycznym szybkie tworzenie i wdrażanie nowych środowisk z zestawem wbudowanych składników oraz pewność, że są one tworzone w ramach zgodności organizacyjnej.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorowanie usług magazynu pod kątem nieoczekiwanych zmian w zachowaniu
Diagnozowanie i rozwiązywanie problemów w aplikacji rozproszonej hostowanej w środowisku chmury może być bardziej skomplikowane niż w tradycyjnych środowiskach. Aplikacje można wdrażać w infrastrukturze PaaS lub IaaS, lokalnie, na urządzeniu przenośnym lub w pewnej kombinacji tych środowisk. Ruch sieciowy aplikacji może przechodzić między sieciami publicznymi i prywatnymi, a aplikacja może korzystać z wielu technologii magazynowania.

Należy stale monitorować usługi magazynu używane przez aplikację w przypadku wszelkich nieoczekiwanych zmian w zachowaniu (na przykład wolniejszych czasów odpowiedzi). Funkcja rejestrowania służy do zbierania bardziej szczegółowych danych i analizowania problemów. Informacje diagnostyczne uzyskane z monitorowania i rejestrowania ułatwiają określenie głównej przyczyny problemu, który wystąpił w aplikacji. Następnie możesz rozwiązać problem i ustalić odpowiednie kroki, aby je skorygować.

[Analityka magazynu platformy Azure](../../storage/common/storage-analytics.md) wykonuje rejestrowanie i udostępnia dane metryk dla konta usługi Azure Storage. Zalecamy używanie tych danych do śledzenia żądań, analizowania trendów użycia i diagnozowania problemów z kontem magazynu.

## <a name="prevent-detect-and-respond-to-threats"></a>Zapobieganie zagrożeniom, wykrywanie ich i reagowanie na nie
[Azure Security Center](../../security-center/security-center-intro.md) pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie, zapewniając lepszy wgląd w zabezpieczenia zasobów platformy Azure i kontrolę nad nimi. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogą być niezauważalne i działać z różnymi rozwiązaniami zabezpieczeń.

Bezpłatna warstwa Security Center oferuje ograniczone zabezpieczenia tylko dla zasobów platformy Azure. Warstwa standardowa rozszerza te możliwości na lokalne i inne chmury. Security Center Standard ułatwia znajdowanie i rozwiązywanie luk w zabezpieczeniach, stosowanie kontroli dostępu i aplikacji w celu blokowania złośliwych działań, wykrywanie zagrożeń przy użyciu analiz i analiz oraz szybkie reagowanie na ataki. Standardową warstwę cenową usługi Security Center możesz wypróbować bezpłatnie przez pierwsze 60 dni. Zalecamy [uaktualnienie subskrypcji platformy Azure do wersji Standard Security Center](../../security-center/security-center-get-started.md).

Użyj Security Center, aby uzyskać centralny widok stanu zabezpieczeń wszystkich zasobów platformy Azure. Na pierwszy rzut oka upewnij się, że odpowiednie mechanizmy kontroli zabezpieczeń są poprawnie skonfigurowane, i szybko Zidentyfikuj wszystkie zasoby, które wymagają uwagi.

Security Center integruje się również z usługą [Microsoft Defender Advanced Threat Protection (ATP)](../../security-center/security-center-wdatp.md), która oferuje kompleksowe możliwości wykrywania i reagowania punktów końcowych (EDR). Dzięki integracji z usługą Microsoft Defender ATP można wykasować nietypowe. Możesz także wykryć zaawansowane ataki i odpowiedzieć na nie w punktach końcowych serwera monitorowanych przez Security Center.

Prawie wszystkie organizacje korporacyjne mają system informacji o zabezpieczeniach i zarządzania zdarzeniami (SIEM), który ułatwia identyfikowanie nowych zagrożeń przez konsolidowanie informacji dziennika z różnych urządzeń zbierających sygnał. Dzienniki są następnie analizowane przez system analizy danych, aby pomóc w zidentyfikowaniu tego, co jest interesujące, od szumu, który jest nieunikniony we wszystkich rozwiązaniach do zbierania i analizowania dzienników.

[Azure — wskaźnikowy](/azure/sentinel/overview) to skalowalne, natywne i w chmurze rozwiązanie do zarządzania informacjami i zdarzeniami (Siem) oraz automatyczna odpowiedź aranżacji (o). Platforma Azure — wskaźnik przedstawia inteligentne funkcje analizy zabezpieczeń i wykrywania zagrożeń poprzez wykrywanie alertów, widoczność zagrożeń, proaktywne łowiectwo i automatyczną odpowiedź na zagrożenia.

Poniżej przedstawiono niektóre najlepsze rozwiązania dotyczące zapobiegania zagrożeniom, ich wykrywania i reagowania na nie:

**Najlepsze**rozwiązanie: zwiększenie szybkości i skalowalności rozwiązania Siem przy użyciu Siem opartego na chmurze.   
**Szczegóły**: Zbadaj funkcje i możliwości [platformy Azure](/azure/sentinel/overview) , a następnie porównaj je z możliwościami, które są obecnie używane lokalnie. Rozważ przyjęcie platformy Azure, jeśli spełnia wymagania SIEM organizacji.

**Najlepsze rozwiązanie**: Znajdź najważniejsze luki w zabezpieczeniach, aby określić priorytety badania.   
**Szczegóły**: Aby zapoznać się z zaleceniami wynikającymi z zasad i inicjatyw platformy Azure wbudowanych w Azure Security Center, przejrzyj [ocenę zabezpieczeń platformy Azure](../../security-center/secure-score-security-controls.md) . Te zalecenia ułatwiają rozwiązywanie najważniejszych zagrożeń, takich jak aktualizacje zabezpieczeń, program Endpoint Protection, szyfrowanie, konfiguracje zabezpieczeń, brak WAF, połączone z Internetem maszyn wirtualnych i wiele innych.

Wynik bezpiecznego, który jest oparty na kontrolkach w centrum zabezpieczeń internetowych (CIS), umożliwia testowanie zabezpieczeń platformy Azure w organizacji przed źródłami zewnętrznymi. Weryfikacja zewnętrzna pomaga zweryfikować i wzbogacić strategię zabezpieczeń zespołu.

**Najlepsze rozwiązanie**: monitorowanie stan zabezpieczeń maszyn, sieci, magazynu i usług danych oraz aplikacji w celu odnajdywania potencjalnych problemów z zabezpieczeniami i określania ich priorytetów.  
**Szczegóły**: Postępuj zgodnie z [zaleceniami dotyczącymi zabezpieczeń](../../security-center/security-center-recommendations.md) w Security Center rozpoczynając od elementów o najwyższym priorytecie.

**Najlepsze**rozwiązanie: Integruj Security Center alertów z rozwiązaniem do zarządzania informacjami i zdarzeniami zabezpieczeń (Siem).   
**Szczegóły**: Większość organizacji z Siem korzysta z niego jako centralnej usługi Clearinghouse do obsługi alertów zabezpieczeń, które wymagają odpowiedzi analityka. Przetworzone zdarzenia utworzone przez Security Center są publikowane w dzienniku aktywności platformy Azure, jeden z dzienników dostępnych za pomocą Azure Monitor. Azure Monitor oferuje skonsolidowany potok do routingu dowolnych danych monitorowania do narzędzia SIEM. Aby uzyskać instrukcje [, zobacz alerty przesyłania strumieniowego do rozwiązania do zarządzania usługami Siem, o lub IT](../../security-center/export-to-siem.md) . Jeśli używasz funkcji wskaźnikowej platformy Azure, zobacz [Connect Azure Security Center](../../sentinel/connect-azure-security-center.md).

**Najlepsze rozwiązanie**: Integrowanie dzienników platformy Azure z Siem.   
**Szczegóły**: Użyj [Azure monitor, aby zebrać i wyeksportować dane](/azure/azure-monitor/overview#integrate-and-export-data). To rozwiązanie ma kluczowe znaczenie dla włączenia badania zdarzeń związanych z zabezpieczeniami, a przechowywanie dzienników w trybie online jest ograniczone. Jeśli używasz funkcji wskaźnikowej platformy Azure, zobacz temat [łączenie ze źródłami danych](../../sentinel/connect-data-sources.md).

**Najlepsze rozwiązanie**: przyspieszanie procesów badania i polowania oraz zmniejszanie fałszywych wyników pozytywnych przez integrację funkcji wykrywania i reagowania punktów końcowych (EDR) z badaniem ataku.   
**Szczegóły**: [Włączanie integracji usługi Microsoft Defender dla punktów końcowych](../../security-center/security-center-wdatp.md#enabling-the-microsoft-defender-for-endpoint-integration) za pośrednictwem zasad zabezpieczeń Security Center. Rozważ użycie funkcji wskaźnikowej platformy Azure na potrzeby łowiectwa zagrożeń i reagowania na zdarzenia.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Monitorowanie kompleksowego monitorowania sieci opartego na scenariuszu
Klienci tworzą kompleksową sieć na platformie Azure, łącząc zasoby sieciowe, takie jak sieć wirtualna, ExpressRoute, Application Gateway i moduły równoważenia obciążenia. Monitorowanie jest dostępne w każdym z zasobów sieciowych.

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) to usługa regionalna. Narzędzia diagnostyczne i wizualizacje umożliwiają monitorowanie i diagnozowanie warunków na poziomie scenariusza sieci w, do i z platformy Azure.

Poniżej przedstawiono najlepsze rozwiązania dotyczące monitorowania sieci i dostępnych narzędzi.

**Najlepsze rozwiązanie**: Automatyzowanie zdalnego monitorowania sieci przy użyciu funkcji przechwytywania pakietów.  
**Szczegóły**: monitorowanie i diagnozowanie problemów z siecią bez logowania się do maszyn wirtualnych przy użyciu Network Watcher. Wyzwalanie [przechwytywania pakietów](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) przez ustawienie alertów i uzyskanie dostępu do informacji o wydajności w czasie rzeczywistym na poziomie pakietu. Gdy zobaczysz problem, możesz szczegółowo zbadać, aby lepiej zdiagnozować.

**Najlepsze rozwiązanie**: uzyskiwanie wglądu w ruch sieciowy przy użyciu dzienników przepływów.  
**Szczegóły: szczegółowe**Omówienie wzorców ruchu sieciowego przy użyciu [dzienników przepływu sieciowych grup zabezpieczeń](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). Informacje w dziennikach przepływu pomagają zbierać dane dotyczące zgodności, inspekcji i monitorowania profilu zabezpieczeń sieci.

**Najlepsze rozwiązanie**: diagnozowanie problemów z łącznością z siecią VPN.  
**Szczegóły**: Użyj Network Watcher, aby [zdiagnozować najczęstsze VPN Gateway i problemy z połączeniami](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Nie można zidentyfikować problemu, ale także użyć szczegółowych dzienników do dalszej analizy.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Bezpieczne wdrażanie za pomocą sprawdzonych narzędzi DevOps
Skorzystaj z następujących najlepszych rozwiązań DevOps, aby upewnić się, że przedsiębiorstwo i zespoły działają wydajnie i wydajniej.

**Najlepsze rozwiązanie**: Automatyzacja kompilowania i wdrażania usług.  
**Szczegóły**: [infrastruktura jako kod](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) jest zestawem technik i praktyk, które pomagają specjalistom IT w usunięciu obciążeń codziennej kompilacji i zarządzania infrastrukturą modularną. Umożliwia ona specjalistom IT tworzenie i konserwowanie nowoczesnego środowiska serwera w taki sposób, jak deweloperzy oprogramowania kompilują i utrzymują kod aplikacji.

Za pomocą [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) można zainicjować obsługę administracyjną aplikacji za pomocą szablonu deklaratywnego. Pojedynczy szablon umożliwia wdrożenie wielu usług wraz z ich zależnościami. Ten sam szablon jest używany do wielokrotnego wdrażania aplikacji na każdym etapie cyklu życia aplikacji.

**Najlepsze rozwiązanie**: automatyczne Kompilowanie i wdrażanie aplikacji sieci Web lub usług w chmurze platformy Azure.  
**Szczegóły**: można skonfigurować Azure DevOps Projects, aby  [automatycznie kompilować i wdrażać](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) aplikacje internetowe platformy Azure lub usługi w chmurze. Usługa Azure DevOps automatycznie wdraża pliki binarne po wykonaniu kompilacji na platformie Azure po każdym zaewidencjonowaniu kodu. Proces tworzenia pakietu jest równoważny z poleceniem Package w programie Visual Studio, a kroki publikowania są równoważne z poleceniem Publikuj w programie Visual Studio.

**Najlepsze rozwiązanie**: Automatyzowanie zarządzania wydaniami.  
**Szczegóły**: [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) to rozwiązanie służące do automatyzowania wdrożenia wielu etapów i zarządzania procesem wydania. Twórz zarządzane potoki ciągłego wdrażania, aby szybko, łatwo i często wydawania. Za pomocą Azure Pipelines można zautomatyzować proces wydania i wstępnie zdefiniowane przepływy pracy zatwierdzania. Wdrażaj lokalnie i w chmurze, rozszerzając i dostosowuj odpowiednio do potrzeb.

**Najlepsze rozwiązanie**: Sprawdź wydajność aplikacji przed jej uruchomieniem lub wdrożeniem aktualizacji w środowisku produkcyjnym.  
**Szczegóły**: wykonaj [testy obciążenia](/azure/devops/test/load-test/overview#alternatives) oparte na chmurze w:

- Znajdź problemy z wydajnością w aplikacji.
- Poprawa jakości wdrożenia.
- Upewnij się, że aplikacja jest zawsze dostępna.
- Upewnij się, że aplikacja może obsłużyć ruch na potrzeby następnego uruchomienia lub kampanii marketingowej.

[Apache JMeter](https://jmeter.apache.org/) to bezpłatne, popularne narzędzie typu "open source" z silnymi procesami społecznościowymi.

**Najlepsze rozwiązanie**: monitorowanie wydajności aplikacji.  
**Szczegóły**: [platforma Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) to rozszerzalna usługa zarządzania wydajnością aplikacji (APM) dla deweloperów sieci Web na wielu platformach. Użyj Application Insights do monitorowania działającej aplikacji sieci Web. Automatycznie wykrywa anomalie wydajności. Zawiera narzędzia analityczne, które ułatwiają diagnozowanie problemów i zrozumienie, które użytkownicy faktycznie robią z Twoją aplikacją. Usługa ta pomaga w ciągłym udoskonalaniu wydajności i użyteczności tworzonych rozwiązań.

## <a name="mitigate-and-protect-against-ddos"></a>Eliminowanie i ochrona przed DDoS
Rozproszony atak typu "odmowa usługi" (DDoS) jest typem ataku próbującego obsłużyć zasoby aplikacji. Celem jest wpływ na dostępność aplikacji i jej zdolność do obsługi uzasadnionych żądań. Te ataki stają się bardziej zaawansowane i większe w rozmiarze i wpływie. Mogą być przeznaczone dla każdego punktu końcowego, który jest publicznie dostępny przez Internet.

Projektowanie i kompilowanie odporności DDoS wymaga planowania i projektowania różnych trybów awarii. Poniżej przedstawiono najlepsze rozwiązania dotyczące tworzenia DDoSych odpornych usług na platformie Azure.

**Najlepsze rozwiązanie**: Upewnij się, że zabezpieczenia są priorytetem całego cyklu życia aplikacji, od projektowania i implementacji do wdrożenia i operacji. Aplikacje mogą mieć usterkę, która pozwala na stosunkowo małą liczbę żądań użycia dużej ilości zasobów, co spowodowało awarię usługi.  
**Szczegóły**: aby pomóc w ochronie usługi działającej na Microsoft Azure, należy dobrze zrozumieć swoją architekturę aplikacji i skupić się na [pięciu filarach jakości oprogramowania](https://docs.microsoft.com/azure/architecture/guide/pillars). Należy znać typowe woluminy ruchu, model łączności między aplikacją i innymi aplikacjami oraz punkty końcowe usługi, które są dostępne dla publicznego Internetu.

Upewnienie się, że aplikacja jest wystarczająco odporna na obsługę odmowy usługi, która jest przeznaczona dla samej aplikacji, jest najważniejsza. Zabezpieczenia i prywatność są wbudowane w platformę Azure, począwszy od [cyklu projektowania zabezpieczeń (SDL)](https://www.microsoft.com/sdl). SDL eliminuje bezpieczeństwo w każdej fazie tworzenia i gwarantuje, że platforma Azure jest stale aktualizowana w celu zapewnienia jeszcze większego bezpieczeństwa.

**Najlepsze rozwiązanie**: Projektuj aplikacje w celu [skalowania w poziomie](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) , aby spełnić zapotrzebowanie na rozbudowane obciążenie, w zależności od przypadku ataku DDoS. Jeśli aplikacja zależy od pojedynczego wystąpienia usługi, tworzy single point of failure. Inicjowanie obsługi wielu wystąpień systemu sprawia, że system jest bardziej odporny i bardziej skalowalny.  
**Szczegóły**: dla [Azure App Service](/azure/app-service/app-service-value-prop-what-is)wybierz [Plan App Service](../../app-service/overview-hosting-plans.md) , który oferuje wiele wystąpień.

W przypadku usługi Azure Cloud Services skonfiguruj każdą rolę, aby użyć [wielu wystąpień](../../cloud-services/cloud-services-choose-me.md).

W przypadku [usługi Azure Virtual Machines](../../virtual-machines/windows/overview.md)upewnij się, że architektura maszyny wirtualnej zawiera więcej niż jedną maszynę wirtualną i że każda maszyna wirtualna jest uwzględniona w [zestawie dostępności](../../virtual-machines/windows/tutorial-availability-sets.md). Zalecamy używanie zestawów skalowania maszyn wirtualnych do obsługi funkcji skalowania automatycznego.

**Najlepsze rozwiązanie**: Ochrona zabezpieczeń warstwowych w aplikacji zmniejsza prawdopodobieństwo pomyślnego ataku. Zaimplementuj bezpieczne projekty dla aplikacji, korzystając z wbudowanych możliwości platformy Azure.  
**Szczegóły**: ryzyko ataku zwiększa się wraz z rozmiarem (obszarem powierzchni) aplikacji. Obszar powierzchniowy można zmniejszyć przy użyciu listy zatwierdzania, aby zamknąć uwidocznioną przestrzeń adresów IP i porty nasłuchujące, które nie są potrzebne w przypadku modułów równoważenia obciążenia ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) i [Azure Application Gateway](/azure/application-gateway/application-gateway-create-probe-portal)).

[Sieciowe grupy zabezpieczeń](../../virtual-network/security-overview.md) to inny sposób zmniejszenia obszaru ataków. Możesz użyć [tagów usługi](../../virtual-network/security-overview.md#service-tags) i [grup zabezpieczeń aplikacji](../../virtual-network/security-overview.md#application-security-groups) , aby zminimalizować złożoność tworzenia reguł zabezpieczeń i konfigurowania zabezpieczeń sieci, jako naturalnego rozszerzenia struktury aplikacji.

W miarę możliwości należy wdrożyć usługi platformy Azure w [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md) . Dzięki temu zasoby usług mogą komunikować się za poorednictwem prywatnych adresów IP. Ruch usługi platformy Azure z sieci wirtualnej domyślnie używa publicznych adresów IP jako źródłowych adresów IP.

Za pomocą [punktów końcowych usługi](../../virtual-network/virtual-network-service-endpoints-overview.md) ruch usługi jest przełączany do adresów prywatnych w sieci wirtualnej jako źródłowe adresy IP, gdy uzyskują dostęp do usługi platformy Azure z sieci wirtualnej.

Często widzimy, że zasoby lokalne klientów atakują razem ze swoimi zasobami na platformie Azure. W przypadku łączenia środowiska lokalnego z platformą Azure należy zminimalizować narażenie zasobów lokalnych na publiczny Internet.

Platforma Azure ma dwie [oferty usługi](../../virtual-network/ddos-protection-overview.md) DDoS, które zapewniają ochronę przed atakami sieciowymi:

- Ochrona podstawowa jest domyślnie zintegrowana z platformą Azure bez dodatkowych kosztów. Skalowanie i pojemność sieci platformy Azure wdrożonej globalnie zapewnia ochronę przed typowymi atakami warstwy sieci przez zawsze włączone monitorowanie ruchu i środki zaradcze w czasie rzeczywistym. Podstawowa nie wymaga żadnych zmian konfiguracji ani aplikacji użytkownika i pomaga chronić wszystkie usługi platformy Azure, w tym usługi PaaS, takie jak Azure DNS.
- Ochrona standardowa zapewnia zaawansowane możliwości ograniczania DDoS przed atakami sieciowymi. Jest on automatycznie dostrojony do ochrony określonych zasobów platformy Azure. Ochrona jest prosta do włączenia podczas tworzenia sieci wirtualnych. Można to również zrobić po utworzeniu i nie wymaga żadnych zmian aplikacji ani zasobów.

## <a name="enable-azure-policy"></a>Włącz Azure Policy
[Azure Policy](/azure/governance/policy/overview) to usługa platformy Azure, za pomocą której można tworzyć i przypisywać zasady oraz zarządzać nimi. Te zasady wymuszają reguły i efekty dotyczące zasobów, dzięki czemu te zasoby pozostają zgodne ze standardami firmy i umowami dotyczącymi poziomu usług. Usługa Azure Policy spełnia to wymaganie, oceniając zasoby pod kątem niezgodności z przypisanymi zasadami.

Włącz Azure Policy monitorowania i wymuszania pisania zasad w organizacji. Pozwoli to zapewnić zgodność z wymaganiami firmy lub przepisami bezpieczeństwa przez centralne zarządzanie zasadami zabezpieczeń w ramach obciążeń chmury hybrydowej. Dowiedz się [, jak tworzyć zasady i zarządzać nimi w celu wymuszenia zgodności](../../governance/policy/tutorials/create-and-manage.md). Zobacz [strukturę definicji Azure Policy](../../governance/policy/concepts/definition-structure.md) , aby zapoznać się z omówieniem elementów zasad.

Poniżej przedstawiono niektóre najlepsze rozwiązania w zakresie zabezpieczeń po przyjęciu Azure Policy:

**Najlepsze rozwiązanie**: zasady obsługują kilka rodzajów efektów. Informacje o nich można znaleźć w [strukturze definicji Azure Policy](../../governance/policy/concepts/definition-structure.md#policy-rule). Operacje związane z działalnością biznesową mogą mieć negatywny wpływ na skutek **odmowy** i **skorygowany** efekt, więc Zacznij od efektu **audytu** , aby ograniczyć ryzyko negatywnego wpływu na zasady.   
**Szczegóły**: [Rozpocznij wdrożenia zasad w trybie inspekcji](../../governance/policy/concepts/definition-structure.md#policy-rule) , a później postępuj zgodnie z tym, aby **odmówić** lub **skorygować**. Przetestuj i przejrzyj wyniki działania inspekcji przed przeniesieniem do **odmowy** lub **skorygowania**.

Aby uzyskać więcej informacji, zobacz [Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../../governance/policy/tutorials/create-and-manage.md).

**Najlepsze rozwiązanie**: Zidentyfikuj role odpowiedzialne za monitorowanie naruszeń zasad i zapewnia szybkie podejmowanie odpowiednich działań naprawczych.   
**Szczegóły**: ma przypisaną rolę Monitoruj zgodność za pośrednictwem [Azure Portal](../../governance/policy/how-to/get-compliance-data.md#portal) lub za pomocą [wiersza polecenia](../../governance/policy/how-to/get-compliance-data.md#command-line).

**Najlepsze rozwiązanie**: Azure Policy stanowi reprezentację techniczną dla zapisaną przez organizację zasad. Mapuj wszystkie definicje Azure Policy na zasady organizacyjne, aby zmniejszyć liczbę pomyłek i zwiększyć spójność.   
**Szczegóły**: mapowanie dokumentu w dokumentacji organizacji lub w samej definicji Azure Policy przez dodanie odwołania do zasad organizacji w [definicji zasad](../../governance/policy/concepts/definition-structure.md#display-name-and-description) lub opis [definicji inicjatywy](../../governance/policy/concepts/initiative-definition-structure.md#metadata) .

## <a name="monitor-azure-ad-risk-reports"></a>Monitorowanie raportów o ryzyku usługi Azure AD
Większość naruszeń zabezpieczeń odbywa się, gdy osoby atakujące uzyskują dostęp do środowiska przez kradzież tożsamości użytkownika. Odnajdywanie zagrożonych tożsamości nie jest łatwe. Usługa Azure AD używa adaptacyjnych algorytmów uczenia maszynowego i heurystyki do wykrywania podejrzanych działań, które są związane z kontami użytkowników. Każda wykryte podejrzane działania są przechowywane w rekordzie nazywanym [wykryciem ryzyka](../../active-directory/reports-monitoring/concept-risk-events.md). Wykrywanie ryzyka są rejestrowane w raportach zabezpieczeń usługi Azure AD. Aby uzyskać więcej informacji, zapoznaj się z [raportem o zabezpieczeniach](../../active-directory/reports-monitoring/concept-user-at-risk.md) dotyczący użytkowników, a raport zabezpieczeń dotyczący [ryzykownych](../../active-directory/reports-monitoring/concept-risky-sign-ins.md)logowań.

## <a name="next-steps"></a>Następne kroki
Zobacz [najlepsze rozwiązania i wzorce dotyczące zabezpieczeń platformy Azure](best-practices-and-patterns.md) , aby uzyskać więcej najlepszych rozwiązań w zakresie zabezpieczeń, które są używane podczas projektowania i wdrażania rozwiązań w chmurze oraz zarządzania nimi przy użyciu platformy Azure.

Dostępne są następujące zasoby umożliwiające dostarczenie bardziej ogólnych informacji na temat zabezpieczeń platformy Azure i powiązanych usług firmy Microsoft:
* [Blog zespołu ds. zabezpieczeń platformy Azure](https://blogs.msdn.microsoft.com/azuresecurity/) — na bieżąco z najnowszymi informacjami na temat zabezpieczeń platformy Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) — w przypadku których luki w zabezpieczeniach firmy Microsoft, w tym problemy z platformą Azure, mogą być zgłaszane lub wysyłane pocztą e-mail secure@microsoft.com
