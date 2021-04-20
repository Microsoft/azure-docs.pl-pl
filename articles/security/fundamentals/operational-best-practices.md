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
ms.openlocfilehash: 6634a536828b3c19d771d135fdb3a1224d3dfdf3
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717427"
---
# <a name="azure-operational-security-best-practices"></a>Najlepsze rozwiązania dotyczące zabezpieczeń operacyjnych platformy Azure
Ten artykuł zawiera zestaw najlepszych rozwiązań operacyjnych dotyczących ochrony danych, aplikacji i innych zasobów na platformie Azure.

Najlepsze rozwiązania opierają się na konsensusie opinii i działają z bieżącymi możliwościami platformy Azure i zestawami funkcji. Opinie i technologie zmieniają się w czasie. Ten artykuł jest regularnie aktualizowany w celu odzwierciedlenia tych zmian.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Definiowanie i wdrażanie silnych praktyk w zakresie zabezpieczeń operacyjnych
Zabezpieczenia operacyjne platformy Azure to usługi, mechanizmy kontroli i funkcje dostępne dla użytkowników w celu ochrony ich danych, aplikacji i innych zasobów na platformie Azure. Zabezpieczenia operacyjne platformy Azure są zbudowane na platformie, która obejmuje wiedzę uzyskaną dzięki możliwościom unikatowym dla firmy Microsoft, w tym cyklem projektowania zabezpieczeń [(SDL, Security Development Lifecycle),](https://www.microsoft.com/sdl)programem [Centrum zabezpieczeń firmy Microsoft](https://www.microsoft.com/msrc?rtc=1) oraz głęboką świadomością zagrożenia cyberbezpieczeństwa.

## <a name="manage-and-monitor-user-passwords"></a>Monitorowanie haseł użytkowników i zarządzanie nimi
W poniższej tabeli wymieniono niektóre najlepsze rozwiązania związane z zarządzaniem hasłami użytkowników:

**Najlepsze rozwiązanie:** Upewnij się, że w chmurze masz odpowiedni poziom ochrony hasłem.   
**Szczegóły:** postępuj zgodnie ze wskazówkami w te tematach Wskazówki dotyczące haseł firmy [Microsoft,](https://www.microsoft.com/research/publication/password-guidance/)które są zakresem użytkowników platform tożsamości firmy Microsoft (Azure Active Directory, Active Directory i konto Microsoft).

**Najlepsze rozwiązanie:** Monitorowanie podejrzanych akcji związanych z kontami użytkowników.   
**Szczegóły:** monitoruj [ryzykowne](../../active-directory/identity-protection/overview-identity-protection.md) logowania użytkowników za [pomocą](../../active-directory/identity-protection/overview-identity-protection.md) raportów zabezpieczeń usługi Azure AD.

**Najlepsze rozwiązanie:** Automatyczne wykrywanie i korygowanie haseł wysokiego ryzyka.   
**Szczegóły:** [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) to funkcja wersji Azure AD — wersja Premium P2, która umożliwia:

- Wykrywanie potencjalnych luk w zabezpieczeniach, które mają wpływ na tożsamości w organizacji
- Konfigurowanie automatycznych odpowiedzi na wykryte podejrzane akcje związane z tożsamościami w organizacji
- Badanie podejrzanych zdarzeń i odpowiednie działania w celu ich rozwiązania

## <a name="receive-incident-notifications-from-microsoft"></a>Otrzymywanie powiadomień o zdarzeniach od firmy Microsoft
Upewnij się, że zespół ds. operacji zabezpieczeń otrzymuje powiadomienia o zdarzeniach platformy Azure od firmy Microsoft. Powiadomienie o zdarzeniu informuje zespół ds. zabezpieczeń, że naruszono zabezpieczenia zasobów platformy Azure, dzięki czemu mogą szybko reagować i korygować potencjalne zagrożenia bezpieczeństwa.

W portalu rejestracji platformy Azure możesz upewnić się, że informacje kontaktowe administratora zawierają szczegółowe informacje, które powiadamiają operacje zabezpieczeń. Informacje kontaktowe to adres e-mail i numer telefonu.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Organizowanie subskrypcji platformy Azure w grupy zarządzania
Jeśli Twoja organizacja ma wiele subskrypcji, konieczny może być sposób na wydajne zarządzanie dostępem, zasadami i zgodnością dla tych subskrypcji. [Grupy zarządzania platformy Azure](../../governance/management-groups/create-management-group-portal.md) zapewniają poziom zakresu powyżej subskrypcji. Subskrypcje można organizować w kontenery nazywane grupami zarządzania i stosować warunki ładu do grup zarządzania. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą warunki zastosowane do tej grupy zarządzania.

Możesz utworzyć elastyczną strukturę grup zarządzania i subskrypcji w katalogu. Każdy katalog ma pojedynczą grupę zarządzania najwyższego poziomu o nazwie główna grupa zarządzania. Główna grupa zarządzania jest wbudowana w hierarchię, aby wszystkie grupy zarządzania i subskrypcje pod nią podlegały. Główna grupa zarządzania umożliwia zastosowanie zasad globalnych i przypisań ról platformy Azure na poziomie katalogu.

Oto kilka najlepszych rozwiązań w zakresie używania grup zarządzania:

**Najlepsze rozwiązanie:** Upewnij się, że nowe subskrypcje stosują elementy ładu, takie jak zasady i uprawnienia podczas ich dodawana.   
**Szczegóły:** użyj głównej grupy zarządzania, aby przypisać elementy zabezpieczeń dla całego przedsiębiorstwa, które mają zastosowanie do wszystkich zasobów platformy Azure. Zasady i uprawnienia to przykłady elementów.

**Najlepsze rozwiązanie:** Dopasuj najwyższe poziomy grup zarządzania do strategii segmentacji, aby zapewnić punkt kontroli i spójności zasad w każdym segmencie.   
**Szczegóły:** utwórz pojedynczą grupę zarządzania dla każdego segmentu w ramach głównej grupy zarządzania. Nie należy tworzyć żadnych innych grup zarządzania w katalogu głównym.

**Najlepsze rozwiązanie:** Ogranicz głębokość grupy zarządzania, aby uniknąć nieporozumień, które zakłócą zarówno operacje, jak i zabezpieczenia.   
**Szczegóły:** ogranicz hierarchię do trzech poziomów, w tym do poziomu głównego.

**Najlepsze rozwiązanie:** Starannie wybierz elementy, które mają być stosowane do całego przedsiębiorstwa z główną grupą zarządzania.   
**Szczegóły:** Upewnij się, że główne elementy grupy zarządzania mają jasno rozewaną potrzebę stosowania do każdego zasobu i że mają niewielki wpływ.

Dobrzy kandydaci to:

- Wymagania prawne, które mają jasny wpływ na działalność biznesową (na przykład ograniczenia związane z niezależnością danych)
- Wymagania o potencjalnie negatywnym wpływie niemal zera na operacje, takie jak zasady z efektem inspekcji lub przypisania uprawnień RBAC platformy Azure, które zostały dokładnie przejmowane

**Najlepsze rozwiązanie:** Starannie zaplanuj i przetestuj wszystkie zmiany w całej organizacji w głównej grupie zarządzania przed ich zastosowaniem (zasady, model RBAC platformy Azure itd.).   
**Szczegóły:** Zmiany w głównej grupie zarządzania mogą mieć wpływ na każdy zasób na platformie Azure. Chociaż zapewniają one zaawansowany sposób zapewnienia spójności w całym przedsiębiorstwie, błędy lub nieprawidłowe użycie mogą negatywnie wpłynąć na operacje produkcyjne. Przetestuj wszystkie zmiany w głównej grupie zarządzania w laboratorium testowym lub pilotażu produkcyjnym.

## <a name="streamline-environment-creation-with-blueprints"></a>Usprawnianie tworzenia środowiska za pomocą strategii
[Usługa Azure Blueprints](../../governance/blueprints/overview.md) umożliwia architektom chmury i centralnym grupom technologii informatycznych definiowanie powtarzalnego zestawu zasobów platformy Azure, który implementuje standardy, wzorce i wymagania organizacji oraz jest z nim zgodny. Azure Blueprints umożliwia zespołom programistów szybkie tworzenie i tworzenie nowych środowisk z zestawem wbudowanych składników i zaufaniem, że tworzą te środowiska zgodnie z zasadami organizacji.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorowanie usług magazynu pod celu nieoczekiwanych zmian w zachowaniu
Diagnozowanie i rozwiązywanie problemów w aplikacji rozproszonej hostowanej w środowisku chmury może być bardziej złożone niż w tradycyjnych środowiskach. Aplikacje można wdrażać w infrastrukturze PaaS lub IaaS, lokalnie, na urządzeniu przenośnym lub w niektórych kombinacjach tych środowisk. Ruch sieciowy aplikacji może przechodzić przez sieci publiczne i prywatne, a aplikacja może korzystać z wielu technologii magazynowania.

Należy stale monitorować usługi magazynu używane przez aplikację w celu zachowania nieoczekiwanych zmian (takich jak wolniejsze czasy odpowiedzi). Rejestrowanie jest wykorzystywane do zbierania bardziej szczegółowych danych i szczegółowego analizowania problemu. Informacje diagnostyczne uzyskiwane zarówno z monitorowania, jak i rejestrowania pomagają określić główną przyczynę problemu napotkaną przez aplikację. Następnie możesz rozwiązać problem i określić odpowiednie kroki jego rozwiązania.

[Azure Storage Analytics](../../storage/common/storage-analytics.md) wykonuje rejestrowanie i dostarcza dane metryk dla konta usługi Azure Storage. Zalecamy używanie tych danych do śledzenia żądań, analizowania trendów użycia i diagnozowania problemów z kontem magazynu.

## <a name="prevent-detect-and-respond-to-threats"></a>Zapobieganie zagrożeniom, wykrywanie ich i reagowanie na nie
[Azure Security Center](../../security-center/security-center-introduction.md) pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie, zapewniając zwiększony wgląd w zabezpieczenia zasobów platformy Azure (i kontrolę nad nich). Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w subskrypcjach platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby zostać niezauwagowane, i współpracuje z różnymi rozwiązaniami w zakresie zabezpieczeń.

Warstwa Bezpłatna usługi Security Center zapewnia ograniczone zabezpieczenia tylko dla zasobów platformy Azure. Warstwa Standardowa rozszerza te możliwości na chmury lokalne i inne. Security Center Standardowa ułatwia znajdowanie i naprawianie luk w zabezpieczeniach, stosowanie kontroli dostępu i aplikacji w celu blokowania złośliwych działań, wykrywanie zagrożeń przy użyciu analizy i analizy oraz szybkie reagowanie w przypadku ataku. Standardową warstwę cenową usługi Security Center możesz wypróbować bezpłatnie przez pierwsze 60 dni. Zalecamy uaktualnienie subskrypcji platformy Azure do wersji [Security Center Standardowa.](../../security-center/security-center-get-started.md)

Użyj Security Center, aby uzyskać centralny widok stanu zabezpieczeń wszystkich zasobów platformy Azure. Na pierwszy rzut oka sprawdź, czy odpowiednie mechanizmy kontroli zabezpieczeń są stosowane i poprawnie skonfigurowane, oraz szybko zidentyfikuj wszystkie zasoby, które wymagają uwagi.

Security Center integruje się również z [usługą Zaawansowana ochrona przed zagrożeniami w usłudze Microsoft Defender (ATP),](../../security-center/security-center-wdatp.md)która zapewnia kompleksowe możliwości wykrywania i reagowania punktu końcowego (EDR). Dzięki integracji z usługą Microsoft Defender ATP można wykryć nieprawidłowości. Można również wykrywać zaawansowane ataki na punkty końcowe serwera monitorowane przez Security Center.

Prawie wszystkie organizacje przedsiębiorstwa mają system zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM), który pomaga identyfikować pojawiające się zagrożenia, konsolidując informacje dziennika z różnorodnych urządzeń zbierających sygnał. Dzienniki są następnie analizowane przez system analizy danych, aby ułatwić identyfikację tego, co jest "interesujące" od szumu, który jest nieunikniony we wszystkich rozwiązaniach do zbierania dzienników i analizy.

[Azure Sentinel](../../sentinel/overview.md) to skalowalne, natywne dla chmury rozwiązanie do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM) oraz zautomatyzowanej odpowiedzi orkiestracji zabezpieczeń (SOAR). Azure Sentinel zapewnia inteligentną analizę zabezpieczeń i analizę zagrożeń za pośrednictwem wykrywania alertów, widoczności zagrożeń, proaktywnego wykrywania zagrożeń i zautomatyzowanego reagowania na zagrożenia.

Oto kilka najlepszych rozwiązań w zakresie zapobiegania zagrożeniom, wykrywania ich i reagowania na nie:

**Najlepsze rozwiązanie:** Zwiększ szybkość i skalowalność rozwiązania SIEM przy użyciu rozwiązania SIEM opartego na chmurze.   
**Szczegóły:** Zbadaj funkcje i możliwości usługi [Azure Sentinel](../../sentinel/overview.md) i porównaj je z możliwościami aktualnie używaną lokalnie. Rozważ przyjęcie Azure Sentinel, jeśli spełnia wymagania rozwiązania SIEM organizacji.

**Najlepsze rozwiązanie:** znajdowanie najpoważniejszych luk w zabezpieczeniach, dzięki czemu można ustalić priorytety badania.   
**Szczegóły:** Przejrzyj swój poziom [bezpieczeństwa platformy Azure,](../../security-center/secure-score-security-controls.md) aby zobaczyć zalecenia wynikające z zasad i inicjatyw platformy Azure wbudowanych w Azure Security Center. Te zalecenia pomagają rozwiązać najważniejsze zagrożenia, takie jak aktualizacje zabezpieczeń, ochrona punktu końcowego, szyfrowanie, konfiguracje zabezpieczeń, brakująca ochrona aplikacji internetowej, maszyny wirtualne połączone z Internetem i wiele innych.

Wskaźnik bezpieczeństwa oparty na mechanizmach kontroli usługi Center for Internet Security (CIS) umożliwia porównanie zabezpieczeń platformy Azure w organizacji z zewnętrznymi źródłami. Zewnętrzna walidacja pomaga zweryfikować i wzbogacić strategię zabezpieczeń twojego zespołu.

**Najlepsze rozwiązanie:** Monitorowanie stanu zabezpieczeń maszyn, sieci, magazynu i usług danych oraz aplikacji w celu odnajdywania potencjalnych problemów z zabezpieczeniami i ustalania ich priorytetów.  
**Szczegóły:** postępuj zgodnie z [zaleceniami Security Center,](../../security-center/security-center-recommendations.md) z elementami o najwyższym priorytecie.

**Najlepsze rozwiązanie:** Integrowanie alertów Security Center z rozwiązaniem do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM).   
**Szczegóły:** Większość organizacji z SIEM używa go jako centralnego centrum rozliczeń dla alertów zabezpieczeń, które wymagają odpowiedzi analityka. Przetworzone zdarzenia Security Center są publikowane w dzienniku aktywności platformy Azure, jednym z dzienników dostępnych za pośrednictwem Azure Monitor. Azure Monitor oferuje skonsolidowany potok do kierowania dowolnych danych monitorowania do narzędzia SIEM. Aby [uzyskać instrukcje, zobacz Stream alerts to a SIEM, SOAR, or IT Service Management solution (Przesyłanie strumieniowe alertów do rozwiązania SIEM, SOAR lub zarządzania usługami IT).](../../security-center/export-to-siem.md) Jeśli używasz usługi Azure Sentinel, zobacz [Connect Azure Security Center (Nawiązywanie połączenia z Azure Security Center](../../sentinel/connect-azure-security-center.md)).

**Najlepsze rozwiązanie:** Integrowanie dzienników platformy Azure z rozwiązaniem SIEM.   
**Szczegóły:** użyj [Azure Monitor, aby zbierać i eksportować dane.](../../azure-monitor/overview.md#integrate-and-export-data) To rozwiązanie ma kluczowe znaczenie dla umożliwienia badania zdarzeń zabezpieczeń, a przechowywanie dzienników online jest ograniczone. Jeśli używasz usługi Azure Sentinel, zobacz Connect data sources (Łączenie [źródeł danych).](../../sentinel/connect-data-sources.md)

**Najlepsze rozwiązanie:** Przyspiesz procesy badania i wyszukiwania zagrożeń oraz zmniejsz liczbę wyników fałszywie dodatnich, integrując możliwości wykrywania i reagowania punktów końcowych (EDR) w badaniu ataku.   
**Szczegóły:** [włącz integrację usługi Microsoft Defender dla punktu końcowego](../../security-center/security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration) za pośrednictwem Security Center zabezpieczeń. Rozważ użycie Azure Sentinel zagrożeń i reagowania na zdarzenia.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Monitorowanie end-to-end opartego na scenariuszu monitorowania sieci
Klienci tworzyją sieć end-to-end na platformie Azure, łącząc zasoby sieciowe, takie jak sieć wirtualna, usługa ExpressRoute, Application Gateway i usługi równoważenia obciążenia. Monitorowanie jest dostępne dla każdego z zasobów sieciowych.

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) to usługa regionalna. Narzędzia do diagnostyki i wizualizacji pozwalają monitorować i diagnozować warunki na poziomie sieci na platformie Azure, do i z platformy Azure.

Poniżej przedstawiono najlepsze rozwiązania dotyczące monitorowania sieci i dostępnych narzędzi.

**Najlepsze rozwiązanie:** Automatyzowanie zdalnego monitorowania sieci za pomocą przechwytywania pakietów.  
**Szczegóły:** monitoruj i diagnozuj problemy z siecią bez logowania się do maszyn wirtualnych przy użyciu Network Watcher. Wyzwalanie [przechwytywania pakietów](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) przez ustawienie alertów i uzyskanie dostępu do informacji o wydajności w czasie rzeczywistym na poziomie pakietu. Gdy zobaczysz problem, możesz szczegółowo zbadać, aby uzyskać lepsze diagnozy.

**Najlepsze rozwiązanie:** Uzyskaj wgląd w ruch sieciowy przy użyciu dzienników przepływu.  
**Szczegóły:** lepsze zrozumienie wzorców ruchu sieciowego za pomocą dzienników przepływu [sieciowych grup zabezpieczeń.](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) Informacje w dziennikach przepływu pomagają w zbieraniu danych dotyczących zgodności, inspekcji i monitorowania profilu zabezpieczeń sieci.

**Najlepsze rozwiązanie:** Diagnozowanie problemów z łącznością sieci VPN.  
**Szczegóły:** użyj Network Watcher, aby [zdiagnozować najczęstsze problemy z VPN Gateway połączeniami.](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md) Możesz nie tylko zidentyfikować problem, ale także użyć szczegółowych dzienników, aby dokładniej zbadać problem.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Bezpieczne wdrażanie przy użyciu sprawdzonych narzędzi DevOps
Skorzystaj z poniższych najlepszych rozwiązań metodyki DevOps, aby upewnić się, że Twoje przedsiębiorstwo i zespoły są wydajne i wydajne.

**Najlepsze rozwiązanie:** Automatyzowanie kompilowania i wdrażania usług.  
**Szczegóły:** [Infrastruktura jako kod](/azure/devops/learn/what-is-infrastructure-as-code) to zestaw technik i praktyk, które pomagają specjalistom IT usunąć obciążenie związane z tworzeniem i zarządzaniem infrastrukturą modularną. Dzięki temu informatycy mogą tworzyć i konserwować nowoczesne środowisko serwera w taki sposób, jak deweloperzy oprogramowania mogą tworzyć i konserwować kod aplikacji.

Za pomocą [Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) aprowizować aplikacje przy użyciu szablonu deklaratywnego. Pojedynczy szablon umożliwia wdrożenie wielu usług wraz z ich zależnościami. Ten sam szablon umożliwia wielokrotne wdrażanie aplikacji na każdym etapie cyklu życia aplikacji.

**Najlepsze rozwiązanie:** Automatyczne kompilowanie i wdrażanie w aplikacjach internetowych platformy Azure lub usługach w chmurze.  
**Szczegóły:** możesz skonfigurować własne Azure DevOps Projects do automatycznego  [kompilowania i](/azure/devops/pipelines/index) wdrażania w aplikacjach internetowych platformy Azure lub usługach w chmurze. Azure DevOps automatycznie wdraża pliki binarne po wykonaniu kompilacji na platformie Azure po każdym zaewidencjeniu kodu. Proces kompilacji pakietu jest odpowiednikiem polecenia Package w Visual Studio, a kroki publikowania są równoważne poleceniu Publish w Visual Studio.

**Najlepsze rozwiązanie:** Automatyzowanie zarządzania wydaniami.  
**Szczegóły:** [Azure Pipelines](/azure/devops/pipelines/index) to rozwiązanie do automatyzacji wdrażania wieloetapowego i zarządzania procesem wydania. Twórz zarządzane potoki ciągłego wdrażania, aby szybko, łatwo i często zwalniać. Dzięki Azure Pipelines można zautomatyzować proces wydania i mieć wstępnie zdefiniowane przepływy pracy zatwierdzania. Wdrażanie w środowisku lokalnym i w chmurze, rozszerzanie i dostosowywanie zgodnie z potrzebami.

**Najlepsze rozwiązanie:** Sprawdź wydajność aplikacji przed jej uruchomieniem lub wdrożeniem aktualizacji w środowisku produkcyjnym.  
**Szczegóły:** Uruchamianie testów obciążeniowych [opartych na chmurze w](/azure/devops/test/load-test/overview#alternatives) celu:

- Znajdowanie problemów z wydajnością w aplikacji.
- Poprawianie jakości wdrożenia.
- Upewnij się, że aplikacja jest zawsze dostępna.
- Upewnij się, że Twoja aplikacja może obsługiwać ruch na następny start lub kampanię marketingową.

[Apache JMeter](https://jmeter.apache.org/) to bezpłatne, popularne narzędzie open source z silną społecznością.

**Najlepsze rozwiązanie:** Monitorowanie wydajności aplikacji.  
**Szczegóły:** [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) to rozszerzalna usługa zarządzania wydajnością aplikacji (APM) dla deweloperów sieci Web na wielu platformach. Użyj Application Insights, aby monitorować aplikację internetową na żywo. Automatycznie wykrywa anomalie wydajności. Obejmuje ona narzędzia analityczne, które ułatwiają diagnozowanie problemów i zrozumienie, co użytkownicy faktycznie robią z Twoją aplikacją. Usługa ta pomaga w ciągłym udoskonalaniu wydajności i użyteczności tworzonych rozwiązań.

## <a name="mitigate-and-protect-against-ddos"></a>Ograniczanie ryzyka i ochrona przed DDoS
Rozproszona odmowa usługi (DDoS) to typ ataku, który próbuje wyczerpać zasoby aplikacji. Celem jest wpływanie na dostępność aplikacji i jej zdolność do obsługi uprawnionych żądań. Te ataki stają się coraz bardziej zaawansowane i mają większy rozmiar i wpływ. Mogą być one kierowane do dowolnego punktu końcowego, który jest publicznie dostępny za pośrednictwem Internetu.

Projektowanie i tworzenie pod kątem odporności na awarie DDoS wymaga planowania i projektowania dla różnych trybów awarii. Poniżej przedstawiono najlepsze rozwiązania dotyczące tworzenia usług odpornych na działania DDoS na platformie Azure.

**Najlepsze rozwiązanie:** Upewnij się, że bezpieczeństwo jest priorytetem przez cały cykl życia aplikacji, od projektowania i implementacji po wdrożenie i działanie. Aplikacje mogą mieć usterki, które pozwalają stosunkowo małej ilości żądań na korzystanie z dużej ilości zasobów, co spowoduje błąd usługi.  
**Szczegóły:** Aby ułatwić ochronę usługi działającej na platformie Microsoft Azure, należy dobrze zrozumieć architekturę aplikacji i skoncentrować się na pięciu [filarach jakości oprogramowania.](/azure/architecture/guide/pillars) Należy znać typowe woluminy ruchu, model łączności między aplikacją i innymi aplikacjami oraz punkty końcowe usługi, które są widoczne w publicznym Internecie.

Zapewnienie, że aplikacja jest wystarczająco odporna, aby obsłużyć atak typu "odmowa usługi", która jest ukierunkowana na samą aplikację, jest najważniejsze. Zabezpieczenia i prywatność są wbudowane w platformę Azure, począwszy od cyklu projektowania zabezpieczeń [(SDL).](https://www.microsoft.com/sdl) Proces SDL dotyczy zabezpieczeń na każdej fazie projektowania i zapewnia, że platforma Azure jest stale aktualizowana w celu zapewnienia jeszcze większej bezpieczeństwa.

**Najlepsze rozwiązanie:** Zaprojektuj aplikacje do skalowania w poziomie, aby zaspokoić zapotrzebowanie na wzrost obciążenia, szczególnie w przypadku ataku DDoS. [](/azure/architecture/guide/design-principles/scale-out) Jeśli aplikacja zależy od pojedynczego wystąpienia usługi, tworzy single point of failure. Aprowizowanie wielu wystąpień sprawia, że system jest bardziej odporny i skalowalny.  
**Szczegóły:** w [Azure App Service](../../app-service/overview.md)wybierz [plan App Service,](../../app-service/overview-hosting-plans.md) który oferuje wiele wystąpień.

Na Azure Cloud Services skonfiguruj każdą z ról, aby używać [wielu wystąpień](../../cloud-services/cloud-services-choose-me.md).

W [przypadku usługi Azure Virtual Machines](../../virtual-machines/windows/overview.md)upewnij się, że architektura maszyny wirtualnej zawiera więcej niż jedną maszynę wirtualną i że każda maszyna wirtualna jest uwzględniona w zestawie [dostępności.](../../virtual-machines/windows/tutorial-availability-sets.md) Zalecamy używanie zestawów skalowania maszyn wirtualnych w celu skalowania automatycznego.

**Najlepsze rozwiązanie:** Nakładanie warstw zabezpieczeń w aplikacji zmniejsza prawdopodobieństwo pomyślnego ataku. Implementowanie bezpiecznych projektów dla aplikacji przy użyciu wbudowanych możliwości platformy Azure.  
**Szczegóły:** Ryzyko ataku zwiększa się wraz z rozmiarem (obszarem powierzchni) aplikacji. Obszar powierzchni można zmniejszyć przy użyciu listy zatwierdzeń w celu zamknięcia dostępnej przestrzeni adresowej IP i portów nasłuchiwania, które nie są potrzebne w usługach[równoważenia](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) obciążenia (Azure Load Balancer i [Azure Application Gateway](../../application-gateway/application-gateway-create-probe-portal.md)).

[Sieciowe grupy zabezpieczeń](../../virtual-network/network-security-groups-overview.md) to inny sposób zmniejszenia powierzchni ataku. Za pomocą [tagów usług](../../virtual-network/network-security-groups-overview.md#service-tags) i grup zabezpieczeń aplikacji można zminimalizować złożoność tworzenia reguł zabezpieczeń i konfigurowania zabezpieczeń sieci jako naturalnego rozszerzenia struktury aplikacji. [](../../virtual-network/network-security-groups-overview.md#application-security-groups)

Usługi platformy Azure należy wdrażać w sieci [wirtualnej zawsze, gdy](../../virtual-network/virtual-networks-overview.md) jest to możliwe. To rozwiązanie umożliwia zasobom usługi komunikowanie się za pośrednictwem prywatnych adresów IP. Ruch usług platformy Azure z sieci wirtualnej domyślnie używa publicznych adresów IP jako źródłowych adresów IP.

Użycie [punktów końcowych usługi umożliwia](../../virtual-network/virtual-network-service-endpoints-overview.md) przełączanie ruchu usługi w celu używania prywatnych adresów sieci wirtualnej jako źródłowych adresów IP podczas uzyskiwania dostępu do usługi platformy Azure z sieci wirtualnej.

Często widzimy, że zasoby lokalne klientów są atakowane wraz z zasobami na platformie Azure. Jeśli łączysz środowisko lokalne z platformą Azure, zminimalizuj narażenie zasobów lokalnych na publiczny Internet.

Platforma Azure oferuje dwie oferty [usług](../../ddos-protection/ddos-protection-overview.md) DDoS, które zapewniają ochronę przed atakami sieciowym:

- Podstawowa ochrona jest domyślnie zintegrowana z platformą Azure bez dodatkowych kosztów. Skala i pojemność globalnie wdrożonej sieci platformy Azure zapewniają ochronę przed powszechnymi atakami w warstwie sieciowej dzięki zawsze stosowanemu monitorowaniu ruchu i zaradczemu w czasie rzeczywistym. Podstawowa usługa nie wymaga żadnych zmian konfiguracji użytkownika ani aplikacji i pomaga chronić wszystkie usługi platformy Azure, w tym usługi PaaS, takie jak Azure DNS.
- Standardowa ochrona zapewnia zaawansowane możliwości ograniczania ryzyka ataków DDoS w przypadku ataków sieciowych. Jest ona automatycznie dostrojona w celu ochrony określonych zasobów platformy Azure. Ochronę można łatwo włączyć podczas tworzenia sieci wirtualnych. Można to również zrobić po utworzeniu i nie wymaga żadnych zmian aplikacji ani zasobów.

## <a name="enable-azure-policy"></a>Włącz Azure Policy
[Azure Policy](../../governance/policy/overview.md) to usługa platformy Azure, która umożliwia tworzenie i przypisywanie zasad oraz zarządzanie nimi. Te zasady wymuszają reguły i efekty dotyczące zasobów, dzięki czemu zasoby te pozostają zgodne ze standardami firmy i umowami poziomu usług. Usługa Azure Policy spełnia to wymaganie, oceniając zasoby pod kątem niezgodności z przypisanymi zasadami.

Włącz Azure Policy, aby monitorować i wymuszać zapisane zasady organizacji. Zapewni to zgodność z firmowymi lub prawnymi wymaganiami w zakresie zabezpieczeń przez centralne zarządzanie zasadami zabezpieczeń w obciążeniach chmury hybrydowej. Dowiedz się, jak [tworzyć zasady i zarządzać nimi w celu wymuszania zgodności.](../../governance/policy/tutorials/create-and-manage.md) Zobacz [Azure Policy definicji,](../../governance/policy/concepts/definition-structure.md) aby uzyskać przegląd elementów zasad.

Oto kilka najlepszych rozwiązań w zakresie zabezpieczeń, które należy stosować po Azure Policy:

**Najlepsze rozwiązanie:** Zasady obsługują kilka typów efektów. Możesz przeczytać o nich w [Azure Policy definicji .](../../governance/policy/concepts/definition-structure.md#policy-rule) Na operacje biznesowe może mieć negatywny wpływ  efekt odmowy i efekt  korygowania, więc zacznij od efektu inspekcji, aby ograniczyć ryzyko negatywnego wpływu zasad.    
**Szczegóły:** [Uruchamianie wdrożeń zasad w trybie inspekcji,](../../governance/policy/concepts/definition-structure.md#policy-rule) a następnie późniejsze **odmawianie** lub **korygowanie.** Przed przejściem do odmowy lub skorygowania  przetestuj i przejrzyj wyniki efektu **inspekcji.**

Aby uzyskać więcej informacji, zobacz [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności.](../../governance/policy/tutorials/create-and-manage.md)

**Najlepsze rozwiązanie:** Zidentyfikuj role odpowiedzialne za monitorowanie naruszeń zasad i zapewnienie szybkiego podejmowania odpowiednich działań korygujących.   
**Szczegóły:** przypisz zgodność monitora roli za pośrednictwem [Azure Portal](../../governance/policy/how-to/get-compliance-data.md#portal) lub za [pośrednictwem wiersza polecenia](../../governance/policy/how-to/get-compliance-data.md#command-line).

**Najlepsze rozwiązanie:** Azure Policy to techniczna reprezentacja napisanych zasad organizacji. Zamapuj wszystkie Azure Policy na zasady organizacji, aby ograniczyć dezorientację i zwiększyć spójność.   
**Szczegóły:** Mapowanie dokumentów w dokumentacji organizacji lub w samej definicji Azure Policy przez dodanie [](../../governance/policy/concepts/definition-structure.md#display-name-and-description) odwołania do zasad organizacyjnych w definicji zasad lub [opisie definicji](../../governance/policy/concepts/initiative-definition-structure.md#metadata) inicjatywy.

## <a name="monitor-azure-ad-risk-reports"></a>Monitorowanie raportów o ryzyku usługi Azure AD
Ogromna większość naruszeń zabezpieczeń ma miejsce, gdy osoby atakujące uzyskują dostęp do środowiska przez kradzież tożsamości użytkownika. Odnajdywanie tożsamości, których bezpieczeństwo zostało naruszone, nie jest łatwym zadaniem. Usługa Azure AD używa adaptacyjnych algorytmów uczenia maszynowego i algorytmów heurystycznych do wykrywania podejrzanych akcji związanych z kontami użytkowników. Każda wykryta podejrzana akcja jest przechowywana w rekordzie nazywanym [wykrywaniem ryzyka](../../active-directory/identity-protection/overview-identity-protection.md). Wykrycia ryzyka są rejestrowane w raportach zabezpieczeń usługi Azure AD. Aby uzyskać więcej informacji, przeczytaj raport o zabezpieczeniach dla użytkowników zagrożonych oraz raport zabezpieczeń [ryzykownych logów.](../../active-directory/identity-protection/overview-identity-protection.md) [](../../active-directory/identity-protection/overview-identity-protection.md)

## <a name="next-steps"></a>Następne kroki
Zobacz Najlepsze rozwiązania i wzorce dotyczące zabezpieczeń platformy [Azure,](best-practices-and-patterns.md) aby uzyskać więcej najlepszych rozwiązań w zakresie zabezpieczeń, które należy stosować podczas projektowania i wdrażania rozwiązań w chmurze oraz zarządzania nimi przy użyciu platformy Azure.

Dostępne są następujące zasoby, które zawierają bardziej ogólne informacje na temat zabezpieczeń platformy Azure i powiązanych usługi firmy Microsoft:
* [Blog zespołu ds.](/archive/blogs/azuresecurity/) zabezpieczeń platformy Azure — aktualne informacje na temat najnowszych informacji w usłudze Azure Security
* [Centrum zabezpieczeń firmy Microsoft](https://technet.microsoft.com/library/dn440717.aspx) — gdzie luki w zabezpieczeniach firmy Microsoft, w tym problemy z platformą Azure, mogą być zgłaszane lub za pośrednictwem poczty e-mail secure@microsoft.com