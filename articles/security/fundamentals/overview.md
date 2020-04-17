---
title: Wprowadzenie do zabezpieczeń platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o usłudze Azure Security, jej usługach i o tym, jak działa.
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2019
ms.author: TomSh
ms.openlocfilehash: a7957c7cbcfa511ea441d8c7bd4371f56ab87560
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461688"
---
# <a name="introduction-to-azure-security"></a>Wprowadzenie do zabezpieczeń platformy Azure
## <a name="overview"></a>Omówienie
Wiemy, że bezpieczeństwo jest jednym z zadań w chmurze i jak ważne jest, aby znaleźć dokładne i aktualne informacje na temat zabezpieczeń platformy Azure. Jednym z najlepszych powodów, aby korzystać z platformy Azure dla aplikacji i usług jest skorzystanie z szerokiej gamy narzędzi i możliwości zabezpieczeń. Te narzędzia i funkcje ułatwiają tworzenie bezpiecznych rozwiązań na bezpiecznej platformie Azure. Platforma Microsoft Azure zapewnia poufność, integralność i dostępność danych klientów, a jednocześnie zapewnia przejrzystą odpowiedzialność.

Ten artykuł zawiera kompleksowe spojrzenie na zabezpieczenia dostępne na platformie Azure.

### <a name="azure-platform"></a>Platforma Azure
Platforma Azure to platforma usług w chmurze publicznej, która obsługuje szeroki wybór systemów operacyjnych, języków programowania, struktur, narzędzi, baz danych i urządzeń. Można uruchomić kontenery systemu Linux z integracji platformy Docker; tworzenie aplikacji z javascript, Python, .NET, PHP, Java i Node.js; tworzenie zaplecza dla urządzeń z systemami iOS, Android i Windows.

Usługi w chmurze publicznej platformy Azure obsługują te same technologie, na które już polegają miliony programistów i specjalistów IT i którym ufaj. Podczas tworzenia lub migracji zasobów IT do dostawcy usług w chmurze publicznej, na którym polegasz, możesz chronić aplikacje i dane za pomocą usług i formantów, które zapewniają w celu zarządzania bezpieczeństwem zasobów chmurowych.

Infrastruktura platformy Azure została zaprojektowana od obiektu do aplikacji do obsługi milionów klientów jednocześnie i zapewnia godną zaufania podstawę, na której firmy mogą spełnić swoje wymagania dotyczące zabezpieczeń.

Ponadto platforma Azure zapewnia szeroką gamę konfigurowalnych opcji zabezpieczeń i możliwość kontrolowania ich, dzięki czemu można dostosować zabezpieczenia do unikatowych wymagań wdrożeń organizacji. Ten dokument pomaga zrozumieć, jak funkcje zabezpieczeń platformy Azure mogą pomóc w spełnieniu tych wymagań.

> [!Note]
> Głównym celem tego dokumentu jest na kontroli opartych na kliencie, które można użyć do dostosowania i zwiększenia bezpieczeństwa aplikacji i usług.
>
> Aby uzyskać informacje na temat zabezpieczania samej platformy Azure przez firmę Microsoft, zobacz [zabezpieczenia infrastruktury platformy Azure.](infrastructure.md)

## <a name="summary-of-azure-security-capabilities"></a>Podsumowanie możliwości zabezpieczeń platformy Azure

### <a name="features-to-secure-the-azure-platform"></a>Funkcje zabezpieczania platformy Azure
Następujące funkcje są możliwości, które można przejrzeć, aby zapewnić, że platforma Azure jest zarządzana w bezpieczny sposób. Łącza zostały dostarczone do dalszych szczegółów dotyczących sposobu, w jaki firma Microsoft zajmuje się pytaniami dotyczącymi zaufania klientów w czterech obszarach: bezpieczna platforma, kontrola prywatności &, zgodność i przejrzystość.

| [Bezpieczna platforma](https://www.microsoft.com/trustcenter/Security/default.aspx)  | [Kontrola & prywatności](https://www.microsoft.com/trustcenter/Privacy/default.aspx)  |[Zgodność](https://www.microsoft.com/trustcenter/Compliance/default.aspx)   | [Przezroczystość](https://www.microsoft.com/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Cykl rozwoju bezpieczeństwa,](https://www.microsoft.com/sdl/)audyty wewnętrzne | [Zarządzaj swoimi danymi przez cały czas](https://www.microsoft.com/trustcenter/Privacy/You-own-your-data) | [Centrum zaufania](https://www.microsoft.com/trustcenter/default.aspx) |[Jak firma Microsoft zabezpiecza dane klientów w usługach platformy Azure](https://www.microsoft.com/trustcenter/Transparency/default.aspx) |
| [Obowiązkowe szkolenie w zakresie bezpieczeństwa, kontrole przeszłości](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) |  [Kontrola lokalizacji danych](https://www.microsoft.com/trustcenter/Privacy/Where-your-data-is-located) |  [Centrum typowych kontrolek](https://www.microsoft.com/trustcenter/Common-Controls-Hub) |[Jak firma Microsoft zarządza lokalizacją danych w usługach platformy Azure](https://azuredatacentermap.azurewebsites.net/)|
| [Testy penetracyjne,](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) [wykrywanie włamań, DDoS,](https://www.microsoft.com/trustcenter/Security/ThreatManagement) [audyty & rejestrowanie](https://www.microsoft.com/trustcenter/Security/AuditingAndLogging) | [Zapewnij dostęp do danych na swoich warunkach](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [Lista kontrolna usług w chmurze due diligence](https://www.microsoft.com/trustcenter/Compliance/Due-Diligence-Checklist) |[Kto w firmie Microsoft może uzyskać dostęp do danych użytkownika na jakich warunkach](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [Najnowocześniejsze centrum danych](https://www.microsoft.com/cloud-platform/global-datacenters), bezpieczeństwo fizyczne, [Bezpieczna sieć](network-overview.md) | [Reagowanie na działania organów ścigania](https://www.microsoft.com/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Zgodność z usługami, lokalizacją & przemysłem](https://www.microsoft.com/trustcenter/Compliance/default.aspx) |[Jak firma Microsoft zabezpiecza dane klientów w usługach platformy Azure](https://www.microsoft.com/trustcenter/Transparency/default.aspx)|
|  [Reagowanie na incydenty bezpieczeństwa](https://aka.ms/SecurityResponsepaper), [Wspólna odpowiedzialność](https://aka.ms/sharedresponsibility) |[Rygorystyczne standardy prywatności](https://www.microsoft.com/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Weryfikacja certyfikacji usług platformy Azure, Centrum przezroczystości](https://www.microsoft.com/trustcenter/Compliance/default.aspx)|

### <a name="features-to-secure-data-and-application"></a>Funkcje zabezpieczania danych i aplikacji
W zależności od modelu usługi w chmurze istnieje zmienna odpowiedzialność za to, kto jest odpowiedzialny za zarządzanie bezpieczeństwem aplikacji lub usługi. Istnieją możliwości dostępne w platformie Azure Platform, aby pomóc w spełnienie tych obowiązków za pomocą wbudowanych funkcji i za pośrednictwem rozwiązań partnerskich, które można wdrożyć w ramach subskrypcji platformy Azure.

Wbudowane funkcje są zorganizowane w sześciu obszarach funkcjonalnych: operacje, aplikacje, magazyn, sieć, obliczenia i tożsamość. Dodatkowe szczegóły dotyczące funkcji i możliwości dostępnych w platformie Azure w tych sześciu obszarach są dostarczane za pośrednictwem informacji podsumowujących.

## <a name="operations"></a>Operacje
Ta sekcja zawiera dodatkowe informacje dotyczące kluczowych funkcji w operacjach zabezpieczeń i informacje podsumowujące o tych możliwościach.

### <a name="security-and-audit-dashboard"></a>Pulpit nawigacyjny zabezpieczeń i inspekcji
[Rozwiązanie Zabezpieczeń i inspekcji](../../security-center/security-center-intro.md) zapewnia kompleksowy widok na postawę bezpieczeństwa IT w organizacji z [wbudowanymi zapytaniami wyszukiwania dla](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) istotnych problemów, które wymagają uwagi. Pulpit nawigacyjny [zabezpieczeń i inspekcji](https://technet.microsoft.com/library/mt484091.aspx) jest ekranem głównym dla wszystkiego, co związane z zabezpieczeniami w dziennikach usługi Azure Monitor. Zapewnia wgląd wysokiego poziomu w stan zabezpieczeń komputerów. Daje również możliwość wyświetlenia wszystkich zdarzeń z ostatnich 24 godzin, 7 dni lub dowolnego innego przedziału czasu.

Ponadto można skonfigurować zabezpieczenia & zgodności, aby [automatycznie wykonywać określone akcje](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) po wykryciu określonego zdarzenia.

### <a name="azure-resource-manager"></a>Azure Resource Manager
[Usługa Azure Resource Manager](../../azure-resource-manager/management/deployment-models.md) umożliwia pracę z zasobami w rozwiązaniu jako grupa. Wszystkie zasoby danego rozwiązania można wdrożyć, zaktualizować lub usunąć w ramach jednej skoordynowanej operacji. Szablon [usługi Azure Resource Manager](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) służy do wdrożenia i ten szablon może pracować dla różnych środowisk, takich jak testowanie, przemieszczania i produkcji. Usługa Resource Manager zapewnia funkcje zabezpieczeń, inspekcji i tagowania ułatwiające zarządzanie zasobami po wdrożeniu.

Wdrożenia oparte na szablonach usługi Azure Resource Manager pomagają zwiększyć bezpieczeństwo rozwiązań wdrożonych na platformie Azure, ponieważ standardowe ustawienia kontroli zabezpieczeń i można je zintegrować ze standardowymi wdrożeniami opartymi na szablonach. Zmniejsza to ryzyko błędów konfiguracji zabezpieczeń, które mogą mieć miejsce podczas ręcznego wdrażania.

### <a name="application-insights"></a>Application Insights
[Usługa Application Insights](https://docs.microsoft.com/azure/application-insights/) to usługa zarządzania wydajnością aplikacji rozszerzalna (APM) dla deweloperów sieci Web. Dzięki usłudze Application Insights można monitorować aplikacje internetowe na żywo i automatycznie wykrywać anomalie wydajności. Zawiera zaawansowane narzędzia analityczne ułatwiające diagnozowanie problemów i zrozumienie, co użytkownicy faktycznie robią z twoimi aplikacjami. Monitoruje aplikację przez cały czas, gdy jest uruchomiona, zarówno podczas testowania, jak i po opublikowaniu lub wdrożeniu.

Usługa Application Insights tworzy wykresy i tabele, które pokazują, na przykład, o jakich porach dnia otrzymujesz większość użytkowników, jak elastyczna jest aplikacja i jak dobrze jest obsługiwana przez wszystkie usługi zewnętrzne, od których zależy.

Jeśli występują awarie, awarie lub problemy z wydajnością, można szczegółowo przeszukiwać dane telemetryczne, aby zdiagnozować przyczynę. Usługa wysyła wiadomości e-mail w przypadku jakichkolwiek zmian w dostępności i wydajności aplikacji. Usługa Application Insight staje się w ten sposób cennym narzędziem zabezpieczeń, ponieważ pomaga w dostępności triady zabezpieczeń poufności, integralności i dostępności.

### <a name="azure-monitor"></a>Azure Monitor
[Usługa Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) oferuje wizualizację, kwerendy, routing, alerty, automatyczne skalowanie i automatyzację danych zarówno z infrastruktury platformy Azure ([dziennik aktywności), jak](../../azure-monitor/platform/platform-logs-overview.md)i każdego pojedynczego zasobu platformy Azure ([Dzienniki diagnostyczne).](../../azure-monitor/platform/platform-logs-overview.md) Za pomocą usługi Azure Monitor można powiadamiać o zdarzeniach związanych z zabezpieczeniami, które są generowane w dziennikach platformy Azure.

### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor
[Dzienniki usługi Azure Monitor](https://azure.microsoft.com/documentation/services/log-analytics/) — oprócz zasobów platformy Azure zawiera rozwiązanie do zarządzania it dla infrastruktury lokalnej i chmurowej innych firm (takiej jak AWS). Dane z usługi Azure Monitor mogą być kierowane bezpośrednio do dzienników usługi Azure Monitor, dzięki czemu można zobaczyć metryki i dzienniki dla całego środowiska w jednym miejscu.

Dzienniki usługi Azure Monitor mogą być użytecznym narzędziem w analizie kryminalistycznej i innych analiz zabezpieczeń, ponieważ narzędzie umożliwia szybkie przeszukiwanie dużych ilości wpisów związanych z zabezpieczeniami za pomocą elastycznego podejścia do zapytań. Ponadto [lokalne dzienniki zapory i serwera proxy mogą być eksportowane na platformę Azure i udostępniane do analizy przy użyciu dzienników usługi Azure Monitor.](../../log-analytics/log-analytics-agent-windows.md)

### <a name="azure-advisor"></a>Azure Advisor
[Usługa Azure Advisor](../../advisor/index.yml) to spersonalizowany konsultant w chmurze, który pomaga zoptymalizować wdrożenia platformy Azure. Analizuje on konfigurację zasobów i dane telemetryczne użycia. Następnie zaleca rozwiązania, które pomogą poprawić [wydajność,](../../advisor/advisor-performance-recommendations.md) [bezpieczeństwo](../../advisor/advisor-security-recommendations.md)i [wysoką dostępność](../../advisor/advisor-high-availability-recommendations.md) zasobów, jednocześnie szukając możliwości [zmniejszenia ogólnych wydatków na platformę Azure.](../../advisor/advisor-cost-recommendations.md) Usługa Azure Advisor zawiera zalecenia dotyczące zabezpieczeń, które mogą znacznie poprawić ogólną postawę zabezpieczeń dla rozwiązań wdrażanych na platformie Azure. Te zalecenia są pobierane z analizy zabezpieczeń przeprowadzonej przez [usługę Azure Security Center.](../../security-center/security-center-intro.md)

### <a name="azure-security-center"></a>Azure Security Center
[Usługa Security Center](../../security-center/security-center-intro.md) pomaga zapobiegać zagrożeniom, wykrywać i reagować na nie, zwiększając wgląd w zabezpieczenia zasobów platformy Azure i kontrolę nad nim. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań z zakresu zabezpieczeń.

Ponadto usługa Security Center pomaga w operacjach zabezpieczeń, udostępniając jeden pulpit nawigacyjny, który udostępnia alerty i zalecenia, które można podjąć natychmiast. Często można rozwiązać problemy za pomocą jednego kliknięcia w konsoli usługi Security Center.
## <a name="applications"></a>Aplikacje
Sekcja zawiera dodatkowe informacje dotyczące kluczowych funkcji w zabezpieczeniach aplikacji i informacje podsumowujące o tych możliwościach.

### <a name="web-application-vulnerability-scanning"></a>Skanowanie luk w zabezpieczeniach aplikacji sieci Web
Jednym z najprostszych sposobów rozpoczęcia testowania luk w zabezpieczeniach [aplikacji Usługi App Service](../../app-service/overview.md) jest użycie [integracji z tinfoil security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) do wykonywania skanowania luk w zabezpieczeniach jednym kliknięciem w aplikacji. Wyniki testów można wyświetlić w łatwym do zrozumienia raporcie i dowiedzieć się, jak rozwiązać każdą lukę, korzystając z instrukcji krok po kroku.

### <a name="penetration-testing"></a>Testy penetracyjne
Jeśli wolisz wykonać własne testy penetracyjne lub chcesz użyć innego pakietu skanera lub dostawcy, należy wykonać [proces zatwierdzania testów penetracyjnych platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/pen-testing ) i uzyskać uprzednią zgodę, aby wykonać żądane testy penetracyjne.

### <a name="web-application-firewall"></a>Zapora aplikacji sieci Web
Zapora aplikacji sieci web (WAF) w [usłudze Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) pomaga chronić aplikacje sieci web przed typowymi atakami opartymi na sieci Web, takimi jak iniekcja SQL, ataki skryptów między witrynami i porwanie sesji. Jest wstępnie skonfigurowany z ochroną przed zagrożeniami zidentyfikowanymi przez [open web application security project (OWASP) jako 10 najczęstszych typowych luk w zabezpieczeniach.](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)

### <a name="authentication-and-authorization-in-azure-app-service"></a>Uwierzytelnianie i autoryzacja w usłudze Azure App Service
[Uwierzytelnianie/ autoryzacja usługi aplikacji](../../app-service/overview-authentication-authorization.md) jest funkcją, która umożliwia aplikacji logowanie użytkowników, dzięki czemu nie trzeba zmieniać kodu w wewnętrznej cepcie aplikacji. Zapewnia łatwy sposób ochrony aplikacji i pracy z danymi dla użytkownika.

### <a name="layered-security-architecture"></a>Warstwowa architektura zabezpieczeń
Ponieważ [środowiska usługi app service](../../app-service/environment/app-service-app-service-environment-intro.md) zapewniają izolowane środowisko uruchomieniowe wdrożone w sieci [wirtualnej platformy Azure,](../../virtual-network/virtual-networks-overview.md)deweloperzy mogą utworzyć architekturę zabezpieczeń warstwowych, zapewniając różne poziomy dostępu do sieci dla każdej warstwy aplikacji. Typowym pragnieniem jest ukrycie zaplecza interfejsu API przed ogólnym dostępem do Internetu i zezwalanie tylko na wywoływanie interfejsów API przez nadrzędne aplikacje sieci web. [Sieciowe grupy zabezpieczeń (NSG)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) mogą być używane w podsieciach sieci wirtualnej platformy Azure zawierających środowiska usługi app service w celu ograniczenia publicznego dostępu do aplikacji interfejsu API.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Diagnostyka i diagnostyka aplikacji serwera sieci Web
Aplikacje sieci Web usługi App Service zapewniają funkcje diagnostyczne do rejestrowania informacji z serwera sieci web i aplikacji sieci web. Są one logicznie podzielone na [diagnostykę serwera www](../../app-service/troubleshoot-diagnostic-logs.md) i [diagnostykę aplikacji.](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx) Serwer sieci Web zawiera dwa główne postępy w diagnozowaniu i rozwiązywaniu problemów z witrynami i aplikacjami.

Pierwszą nową funkcją są informacje o stanie w czasie rzeczywistym dotyczące pul aplikacji, procesów roboczych, lokacji, domen aplikacji i uruchomionych żądań. Drugą nowością są szczegółowe zdarzenia śledzenia, które śledzą żądanie w całym procesie żądania i odpowiedzi.

Aby włączyć zbieranie tych zdarzeń śledzenia, usługi IIS 7 można skonfigurować do automatycznego przechwytywania pełnych dzienników śledzenia w formacie XML dla każdego konkretnego żądania na podstawie kodów odpowiedzi czasu lub błędu.

#### <a name="web-server-diagnostics"></a>Diagnostyka serwera sieci Web
Można włączyć lub wyłączyć następujące rodzaje dzienników:

-   Szczegółowe rejestrowanie błędów — szczegółowe informacje o błędach dla kodów stanu HTTP, które wskazują błąd (kod stanu 400 lub wyższy). Może to zawierać informacje, które mogą pomóc w określeniu, dlaczego serwer zwrócił kod błędu.

-   Śledzenie żądań nie powiodło się — szczegółowe informacje na temat nieudanych żądań, w tym śledzenia składników usług IIS używanych do przetwarzania żądania i czasu poświęconego każdemu składnikowi. Może to być przydatne, jeśli próbujesz zwiększyć wydajność witryny lub wyizolować, co jest przyczyną zwracania określonego błędu HTTP.

-   Rejestrowanie serwera sieci Web — informacje o transakcjach HTTP przy użyciu rozszerzonego formatu pliku dziennika W3C. Jest to przydatne podczas określania ogólnych metryk lokacji, takich jak liczba obsługiwanych żądań lub liczba żądań z określonego adresu IP.

#### <a name="application-diagnostics"></a>Diagnostyka aplikacji
[Diagnostyka aplikacji](../../app-service/troubleshoot-diagnostic-logs.md) umożliwia przechwytywanie informacji wytwarzanych przez aplikację sieci web. ASP.NET aplikacje mogą używać [klasy System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) do rejestrowania informacji w dzienniku diagnostyki aplikacji. W diagnostyce aplikacji istnieją dwa główne typy zdarzeń — powiązane z wydajnością aplikacja oraz powiązane z awariami i błędami aplikacji. Awarie i błędy można rozdzielić na łączność, zabezpieczenia i problemy dotyczące awarii. Problemy dotyczące awarii zazwyczaj obejmują kod aplikacji.

W diagnostyce aplikacji można wyświetlać zdarzenia pogrupowane w następujący sposób:

-   Wszystkie (wyświetla wszystkie zdarzenia)
-   Błędy aplikacji (wyświetla zdarzenia wyjątków)
-   Wydajność (zdarzenia dotyczące wydajności)

## <a name="storage"></a>Magazyn
Sekcja zawiera dodatkowe informacje dotyczące kluczowych funkcji zabezpieczeń magazynu platformy Azure i informacje podsumowujące o tych możliwościach.

### <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)
Konto magazynu można zabezpieczyć za pomocą kontroli dostępu opartej na rolach (RBAC). Ograniczenie dostępu na podstawie [konieczności znać](https://en.wikipedia.org/wiki/Need_to_know) i [najmniej uprzywilejowanych](https://en.wikipedia.org/wiki/Principle_of_least_privilege) zasad zabezpieczeń jest konieczne dla organizacji, które chcą wymusić zasady zabezpieczeń dostępu do danych. Te prawa dostępu są przyznawane przez przypisanie odpowiedniej roli RBAC do grup i aplikacji w określonym zakresie. Do przypisywania użytkownikom uprawnień do uprawnień można użyć [wbudowanych ról RBAC,](../../role-based-access-control/built-in-roles.md)takich jak Współautor konta magazynu. Dostęp do kluczy magazynu dla konta magazynu przy użyciu modelu [usługi Azure Resource Manager](../../storage/blobs/security-recommendations.md) można kontrolować za pomocą kontroli dostępu opartej na rolach (RBAC).

### <a name="shared-access-signature"></a>Podpis dostępu współdzielonego
[Sygnatura dostępu współdzielonego (SAS, shared access signature)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) zapewnia delegowany dostęp do zasobów w ramach konta magazynu. Sygnatury dostępu Współdzielonego oznacza, że można udzielić klientowi ograniczone uprawnienia do obiektów na koncie magazynu przez określony czas i z określonym zestawem uprawnień. Możesz udzielić tych ograniczonych uprawnień bez konieczności udostępniania kluczy dostępu do konta.

### <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu
Szyfrowanie podczas przesyłania jest mechanizmem ochrony danych przesyłanych przez sieci. Za pomocą usługi Azure Storage można zabezpieczyć dane przy użyciu:
-   [Szyfrowanie na poziomie transportu](../../storage/blobs/security-recommendations.md), takie jak HTTPS podczas przesyłania danych do lub z usługi Azure Storage.

-   [Szyfrowanie przewodowe](../../storage/blobs/security-recommendations.md), takie jak [szyfrowanie SMB 3.0](../../storage/blobs/security-recommendations.md) dla [udziałów plików azure](../../storage/files/storage-dotnet-how-to-use-files.md).

-   Szyfrowanie po stronie klienta, szyfrowanie danych przed ich przeniesieniem do magazynu i odszyfrowanie danych po ich przeniesieniu z magazynu.

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku
Dla wielu organizacji szyfrowanie danych w spoczynku jest obowiązkowym krokiem w kierunku prywatności, zgodności i suwerenności danych. Istnieją trzy funkcje zabezpieczeń magazynu platformy Azure, które zapewniają szyfrowanie danych, które są "w spoczynku":

-   [Szyfrowanie usługi magazynu](../../storage/common/storage-service-encryption.md) umożliwia żądanie, aby usługa magazynu automatycznie szyfrować dane podczas zapisywania ich w usłudze Azure Storage.

-   [Szyfrowanie po stronie klienta](../../storage/common/storage-client-side-encryption.md) zapewnia również funkcję szyfrowania w spoczynku.

-   [Szyfrowanie dysków platformy Azure](../azure-security-disk-encryption-overview.md) umożliwia szyfrowanie dysków systemu operacyjnego i dysków danych używanych przez maszynę wirtualną IaaS.

### <a name="storage-analytics"></a>Analityka magazynu
[Usługa Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) wykonuje rejestrowanie i udostępnia dane metryki dla konta magazynu. Te dane można używać do śledzenia żądań, analizowania trendów użycia i diagnozowania problemów z kontem magazynu. Usługa Storage Analytics rejestruje szczegółowe informacje dotyczące żądań do usługi magazynu zakończonych powodzeniem i niepowodzeniem. Tych informacji można używać na potrzeby monitorowania poszczególnych żądań i diagnozowania problemów z usługą magazynu. Żądania są rejestrowane na podstawie najlepszych starań. Rejestrowane są następujące typy żądań uwierzytelnionych:
-   Pomyślne żądania.

-   Nieudane żądania, w tym limit czasu, ograniczanie przepustowości, sieć, autoryzacja i inne błędy.

-   Żądania przy użyciu sygnatury dostępu współdzielonego (SAS), w tym nieudane i pomyślne żądania.

-   Żądania do analizy danych.

### <a name="enabling-browser-based-clients-using-cors"></a>Włączanie klientów opartych na przeglądarce przy użyciu usługi CORS
[Udostępnianie zasobów między źródłami (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) to mechanizm, który umożliwia domenom udzielanie sobie nawzajem uprawnień dostępu do zasobów. Agent użytkownika wysyła dodatkowe nagłówki, aby upewnić się, że kod JavaScript załadowany z określonej domeny może uzyskać dostęp do zasobów znajdujących się w innej domenie. Ta ostatnia domena odpowiada następnie dodatkowymi nagłówkami zezwalającymi lub odmawiającymi oryginalnej domeny dostępu do jej zasobów.

Usługi usługi magazynu platformy Azure obsługują teraz usługę CORS, dzięki czemu po ustawieniu reguł CORS dla usługi poprawnie uwierzytelnione żądanie utworzone względem usługi z innej domeny jest oceniane w celu ustalenia, czy jest dozwolone zgodnie z określonymi regułami.

## <a name="networking"></a>Networking
Sekcja zawiera dodatkowe informacje dotyczące kluczowych funkcji zabezpieczeń sieci platformy Azure i informacje podsumowujące o tych możliwościach.

### <a name="network-layer-controls"></a>Formanty warstwy sieciowej
Kontrola dostępu do sieci jest działaniem ograniczającym łączność do i z określonych urządzeń lub podsieci i reprezentuje rdzeń zabezpieczeń sieci. Celem kontroli dostępu do sieci jest upewnienie się, że maszyny wirtualne i usługi są dostępne tylko dla użytkowników i urządzeń, do których mają być dostępne.

#### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
[Network Security Group (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) jest podstawową zaporą filtrowania pakietów stanowych i umożliwia kontrolowanie dostępu na podstawie [5-krotki.](https://www.techopedia.com/definition/28190/5-tuple) Sieciowe sieci zabezpieczeń nie zapewniają inspekcji warstwy aplikacji ani uwierzytelniania kontroli dostępu. Mogą one służyć do kontrolowania ruchu przemieszczających się między podsieciami w ramach sieci wirtualnej platformy Azure i ruchu między siecią wirtualną platformy Azure a Internetem.

#### <a name="route-control-and-forced-tunneling"></a>Kontrola trasy i wymuszone tunelowanie
Możliwość kontrolowania zachowania routingu w sieciach wirtualnych platformy Azure jest krytyczna funkcja zabezpieczeń sieci i kontroli dostępu. Na przykład jeśli chcesz upewnić się, że cały ruch do i z sieci wirtualnej platformy Azure przechodzi przez to wirtualne urządzenie zabezpieczeń, musisz mieć możliwość kontrolowania i dostosowywania zachowania routingu. Można to zrobić, konfigurując trasy zdefiniowane przez użytkownika na platformie Azure.

[Trasy zdefiniowane przez użytkownika](../../virtual-network/virtual-networks-udr-overview.md) umożliwiają dostosowanie ścieżek przychodzących i wychodzących do ruchu przechodzącego do i z poszczególnych maszyn wirtualnych lub podsieci, aby zapewnić możliwie najbezpieczniejszą trasę. [Wymuszone tunelowanie](https://www.petri.com/azure-forced-tunneling) to mechanizm, którego można użyć, aby upewnić się, że usługi nie mogą inicjować połączenia z urządzeniami w Internecie.

To różni się od możliwości akceptowania połączeń przychodzących, a następnie odpowiadania na nie. Front-endowe serwery sieci web muszą odpowiadać na żądania od hostów internetowych, a więc ruch internetowy jest dozwolony przychodzący do tych serwerów sieci web, a serwery sieci web mogą odpowiadać.

Wymuszone tunelowanie jest powszechnie używane do wymuszania ruchu wychodzącego do Internetu, aby przejść przez lokalne serwery proxy zabezpieczeń i zapory.

#### <a name="virtual-network-security-appliances"></a>Urządzenia zabezpieczające sieć wirtualną
Podczas gdy sieciowe grupy zabezpieczeń, trasy zdefiniowane przez użytkownika i wymuszone tunelowanie zapewniają poziom bezpieczeństwa w warstwach sieci i transportu [modelu OSI,](https://en.wikipedia.org/wiki/OSI_model)mogą wystąpić chwile, w których chcesz włączyć zabezpieczenia na wyższych poziomach stosu. Dostęp do tych ulepszonych funkcji zabezpieczeń sieci można uzyskać przy użyciu rozwiązania urządzenia zabezpieczeń sieci partnerskiej platformy Azure. Najnowsze rozwiązania zabezpieczeń sieci partnerów platformy Azure można znaleźć, odwiedzając [witrynę Azure Marketplace](https://azure.microsoft.com/marketplace/) i wyszukując "zabezpieczenia" i "zabezpieczenia sieci".

### <a name="azure-virtual-network"></a>Azure Virtual Network
Sieć wirtualna Azure odzwierciedla w chmurze Twoją sieć. Jest to logiczna izolacja sieci szkieletowej platformy Azure poświęconej subskrypcji. W ramach tej sieci można w pełni kontrolować bloki adresów IP, ustawienia DNS, zasady zabezpieczeń i tabele tras. Sieć wirtualną można podzielić na podsieci i umieścić maszyny wirtualne usługi Azure IaaS (maszyny wirtualne) i/lub [usługi w chmurze (wystąpienia roli PaaS)](../../cloud-services/cloud-services-choose-me.md) w sieciach wirtualnych platformy Azure.

Dodatkowo można połączyć sieć wirtualną z siecią lokalną przy użyciu jednej z [opcji łączności](../../vpn-gateway/index.yml) dostępnej na platformie Azure. W zasadzie można rozbudować swoją sieć do sieci Azure, zachowując pełną kontrolę nad blokami adresów IP i wykorzystując zapewnianą przez platformę Azure skalowalność klasy korporacyjnej.

Sieć platformy Azure obsługuje różne scenariusze bezpiecznego dostępu zdalnego. Oto niektóre poprawki:

-   [Łączenie pojedynczych stacji roboczych z siecią wirtualną platformy Azure](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

-   [Łączenie sieci lokalnej z siecią wirtualną platformy Azure za pomocą sieci VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

-   [Łączenie sieci lokalnej z siecią wirtualną platformy Azure za pomocą dedykowanego łącza WAN](../../expressroute/expressroute-introduction.md)

-   [Łączenie sieci wirtualnych platformy Azure ze sobą](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

### <a name="vpn-gateway"></a>VPN Gateway
Aby wysłać ruch sieciowy między siecią wirtualną platformy Azure a witryną lokalną, należy utworzyć bramę sieci VPN dla sieci wirtualnej platformy Azure. [Brama sieci VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) to typ bramy sieci wirtualnej, która wysyła zaszyfrowany ruch przez połączenie publiczne. Bramy sieci VPN można również używać do wysyłania ruchu między sieciami wirtualnymi platformy Azure za pośrednictwem sieci szkieletowej platformy Azure.

### <a name="express-route"></a>ExpressRoute
Microsoft Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) to dedykowane łącze WAN, które umożliwia rozszerzenie sieci lokalnych do chmury firmy Microsoft za pośrednictwem dedykowanego połączenia prywatnego ułatwione przez dostawcę łączności.

![ExpressRoute](./media/overview/azure-security-fig1.png)

Dzięki usłudze ExpressRoute można ustanowić połączenia z usługami Microsoft w chmurze, np. Microsoft Azure, Office 365 i CRM Online. Połączenie może być z sieci typu dowolna-dowolna (IP VPN), sieci Ethernet typu punkt-punkt lub przy użyciu łączności obejmującej wiele połączeń wirtualnych przez dostawcę połączenia w ramach infrastruktury współlokacji.

Połączenia usługi ExpressRoute nie przechodzą przez publiczny Internet i dlatego można je uznać za bezpieczniejsze niż rozwiązania oparte na sieci VPN. Dzięki temu oferują one większą niezawodność i szybkość oraz mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet.


### <a name="application-gateway"></a>Application Gateway
Usługa Microsoft [Azure Application Gateway](../../application-gateway/overview.md) udostępnia kontroler dostarczania aplikacji [(ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller) jako usługę, oferując różne funkcje równoważenia obciążenia warstwy 7 dla aplikacji.

![Application Gateway](./media/overview/azure-security-fig2.png)

Pozwala na optymalizację wydajności farmy internetowej poprzez odciążanie intensywnego zakończenia TLS procesora do bramy aplikacji (znanej również jako "odciążanie TLS" lub "mostkowanie TLS"). Zapewnia również inne funkcje routingu warstwy 7, w tym dystrybucję ruchu przychodzącego okrężną, koligacji sesji opartej na plikach cookie, routing oparty na ścieżce adresów URL oraz możliwość hostowania wielu witryn sieci Web za jedną bramą aplikacji. Usługa Azure Application Gateway to moduł równoważenia obciążenia warstwy 7.

Udostępnia tryb failover, oparty na wydajności routing żądań HTTP między różnymi serwerami — w chmurze i lokalnymi.

Aplikacja udostępnia wiele funkcji kontrolera dostarczania aplikacji (ADC), w tym równoważenie obciążenia HTTP, koligacja sesji oparta na plikach cookie, [odciążanie TLS,](../../application-gateway/tutorial-restrict-web-traffic-powershell.md)niestandardowe sondy kondycji, obsługę wielu lokacji i wiele innych.

### <a name="web-application-firewall"></a>Zapora aplikacji internetowej
Zapora aplikacji sieci Web jest funkcją [bramy aplikacji platformy Azure,](../../application-gateway/overview.md) która zapewnia ochronę aplikacjom sieci Web, które używają bramy aplikacji dla standardowych funkcji kontroli dostarczania aplikacji (ADC). Zapora aplikacji internetowej realizuje ten cel, chroniąc je przed większością z 10 najpopularniejszych luk w zabezpieczeniach w Internecie OWASP.

![Zapora aplikacji internetowej](./media/overview/azure-security-fig1.png)

-   Ochrona przed atakami polegającymi na iniekcji SQL

-   Częste ataki w ramach sieci Web polegające na iniekcji poleceń, przemycaniu żądań HTTP, rozdzielaniu odpowiedzi HTTP i zdalnym dołączaniu plików

-   Ochrona przed naruszeniami protokołu HTTP

-   Ochrona przed nieprawidłowościami protokołu HTTP, takimi jak brakujące powiązania agenta i użytkownika hosta oraz akceptowanie nagłówków

-   Zapobieganie atakom z użyciem robotów, przeszukiwarek i skanerów

-   Wykrywanie typowych błędnych konfiguracji aplikacji (czyli Apache, IIS itp.)

Scentralizowana zapora aplikacji internetowej chroniąca przed atakami z Internetu sprawia, że zarządzanie zabezpieczeniami jest znacznie prostsze oraz zapewnia lepszą ochronę aplikacji przed intruzami. Zapora aplikacji internetowej może reagować na zagrożenia bezpieczeństwa szybciej — poprzez wdrażanie poprawek zapobiegających wykorzystaniu znanych luk w zabezpieczeniach w centralnej lokalizacji zamiast w poszczególnych aplikacjach internetowych. Istniejące bramy aplikacji można łatwo przekonwertować na bramę aplikacji z zaporą aplikacji internetowej.

### <a name="traffic-manager"></a>Traffic Manager
Usługa Microsoft [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) umożliwia kontrolowanie dystrybucji ruchu użytkowników dla punktów końcowych usługi w różnych centrach danych. Punkty końcowe usługi obsługiwane przez usługę Traffic Manager obejmują maszyny wirtualne platformy Azure, aplikacje sieci Web i usługi w chmurze. Usługi Traffic Manager można też użyć z zewnętrznymi punktami końcowymi nienależącymi do platformy Azure. Usługa Traffic Manager używa systemu nazw domen (DNS) do kierowania żądań klientów do najbardziej odpowiedniego punktu końcowego na podstawie [metody routingu ruchu](../../traffic-manager/traffic-manager-routing-methods.md) i kondycji punktów końcowych.

Usługa Traffic Manager udostępnia szereg metod routingu ruchu dostosowanych do różnych potrzeb aplikacji, [monitorowania](../../traffic-manager/traffic-manager-monitoring.md)kondycji punktu końcowego i automatycznego trybu failover. Usługa Traffic Manager jest odporna na awarie, w tym awarię całego regionu platformy Azure.

### <a name="azure-load-balancer"></a>Azure Load Balancer
Usługa [Azure Load Balancer](../../load-balancer/load-balancer-overview.md) zapewnia aplikacjom wysoką dostępność i wydajność sieci. Jest to moduł równoważenia obciążenia warstwy 4 (TCP, UDP), który rozdziela ruch przychodzący między zdrowymi wystąpieniami usług zdefiniowanych w zestawie z równoważenia obciążenia. Moduł równoważenia obciążenia platformy Azure można skonfigurować w celu:

-   Równoważenie obciążenia przychodzącego ruchu internetowego do maszyn wirtualnych. Ta konfiguracja jest znana jako [równoważenie obciążenia skierowane do Internetu.](../../load-balancer/concepts-limitations.md#publicloadbalancer)

-   Ruch równoważenia obciążenia między maszynami wirtualnymi w sieci wirtualnej, między maszynami wirtualnymi w usługach w chmurze lub między komputerami lokalnymi a maszynami wirtualnymi w sieci wirtualnej między lokalami. Ta konfiguracja jest znana jako [wewnętrzne równoważenie obciążenia](../../load-balancer/concepts-limitations.md#internalloadbalancer).

- Przesyłanie dalej ruchu zewnętrznego do określonej maszyny wirtualnej

### <a name="internal-dns"></a>Wewnętrzny dns
Można zarządzać listą serwerów DNS używanych w sieci wirtualnej w portalu zarządzania lub w pliku konfiguracji sieci. Klient może dodać maksymalnie 12 serwerów DNS dla każdej sieci wirtualnej. Podczas określania serwerów DNS należy sprawdzić, czy serwery DNS klienta są wystawiane we właściwej kolejności dla środowiska klienta. Listy serwerów DNS nie działają okrężnie. Są one używane w kolejności, w jakiej są określone. Jeśli można uzyskać dostęp do pierwszego serwera DNS na liście, klient używa tego serwera DNS niezależnie od tego, czy serwer DNS działa poprawnie, czy nie. Aby zmienić kolejność serwera DNS dla sieci wirtualnej klienta, usuń serwery DNS z listy i dodaj je z powrotem w kolejności żądanej przez klienta. System DNS obsługuje aspekt dostępności triady zabezpieczeń "CIA".

### <a name="azure-dns"></a>System DNS platformy Azure
[System nazw domen](https://technet.microsoft.com/library/bb629410.aspx)lub DNS jest odpowiedzialny za tłumaczenie (lub rozwiązywanie) nazwy witryny sieci Web lub usługi na jej adres IP. [Azure DNS](../../dns/dns-overview.md) to usługa hostingowa przeznaczona dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury platformy Microsoft Azure. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure. System DNS obsługuje aspekt dostępności triady zabezpieczeń "CIA".

### <a name="azure-monitor-logs-nsgs"></a>Usługa Azure Monitor rejestruje nsg
Można włączyć następujące kategorie dzienników diagnostycznych dla sieciowych grup zabezpieczeń:

-   Zdarzenie: Zawiera wpisy, dla których reguły sieciowej grupy płciowych są stosowane do maszyn wirtualnych i ról wystąpień na podstawie adresu MAC. Stan tych reguł jest zbierany co 60 sekund.

-   Licznik reguł: zawiera wpisy dotyczące liczby razy, ile każda reguła sieciowych grup sieciowych jest stosowana do odmowy lub zezwolenia na ruch.

### <a name="security-center"></a>Security Center
[Usługa Azure Security Center](../../security-center/security-center-intro.md) stale analizuje stan zabezpieczeń zasobów platformy Azure pod kątem najlepszych rozwiązań dotyczących zabezpieczeń sieci. Gdy usługa Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy [zalecenia,](../../security-center/security-center-recommendations.md) które prowadzą użytkownika przez proces konfigurowania potrzebnych formantów w celu wzmocnienia i ochrony zasobów.

## <a name="compute"></a>Wystąpienia obliczeniowe
Sekcja zawiera dodatkowe informacje dotyczące kluczowych funkcji w tym obszarze i podsumowanie informacji o tych możliwościach.

### <a name="antimalware--antivirus"></a>Antimalware & Antivirus
Za pomocą usługi Azure IaaS można używać oprogramowania chroniącego przed złośliwym oprogramowaniem od dostawców zabezpieczeń, takich jak Microsoft, Symantec, Trend Micro, McAfee i Kaspersky, aby chronić maszyny wirtualne przed złośliwymi plikami, adware i innymi zagrożeniami. [Ochrona przed złośliwym oprogramowaniem](antimalware.md) firmy Microsoft dla usług w chmurze i maszyn wirtualnych platformy Azure to funkcja ochrony, która pomaga identyfikować i usuwać wirusy, programy szpiegujące i inne złośliwe oprogramowanie. Ochrona przed złośliwym oprogramowaniem firmy Microsoft udostępnia konfigurowalne alerty, gdy znane złośliwe lub niechciane oprogramowanie próbuje zainstalować się lub uruchomić w systemach platformy Azure. Microsoft Antimalware można również wdrożyć za pomocą usługi Azure Security Center

### <a name="hardware-security-module"></a>Sprzętowy moduł zabezpieczeń
Szyfrowanie i uwierzytelnianie nie zwiększają bezpieczeństwa, chyba że same klucze są chronione. Możesz uprościć zarządzanie krytycznymi wpisami tajnymi i kluczami i zabezpieczeniami, przechowując je w [usłudze Azure Key Vault.](../../key-vault/general/overview.md) Usługa Key Vault umożliwia przechowywanie kluczy w sprzętowych modułach zabezpieczeń (HSM) certyfikowanych zgodnie ze standardami FIPS 140-2 Poziomu 2. Klucze szyfrowania programu SQL Server do szyfrowania kopii zapasowych lub [przezroczystych danych](https://msdn.microsoft.com/library/bb934049.aspx) mogą być przechowywane w ucho. Uprawnienia i dostęp do tych elementów chronionych są zarządzane za pośrednictwem [usługi Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Kopia zapasowa maszyny wirtualnej
[Usługa Azure Backup](../../backup/backup-overview.md) to rozwiązanie, które chroni dane aplikacji przy zerowym kapitale i minimalnych kosztach operacyjnych. Błędy aplikacji mogą uszkodzić dane, a błędy ludzkie mogą powodować błędy w aplikacjach, które mogą prowadzić do problemów z zabezpieczeniami. Dzięki usłudze Azure Backup maszyny wirtualne z systemem Windows i Linux są chronione.

### <a name="azure-site-recovery"></a>Azure Site Recovery
Ważną częścią strategii [ciągłości/odzyskiwania po awarii (BCDR)](../../best-practices-availability-paired-regions.md) organizacji jest ustalenie, jak utrzymać obciążenia i aplikacje firmowe w stanie roboczym w przypadku planowanych i nieplanowanych awarii. [Usługa Azure Site Recovery](../../site-recovery/site-recovery-overview.md) ułatwia organizowanie replikacji, pracy awaryjnej i odzyskiwania obciążeń i aplikacji, dzięki czemu są one dostępne z lokalizacji dodatkowej, jeśli lokalizacja podstawowa ustępuje.

### <a name="sql-vm-tde"></a>TDE maszyny wirtualnej SQL
[Przezroczyste szyfrowanie danych (TDE)](../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md) i szyfrowanie na poziomie kolumny (CLE) są funkcjami szyfrowania serwera SQL. Ta forma szyfrowania wymaga od klientów zarządzania kluczami kryptograficznymi używanymi do szyfrowania i przechowywania ich.

Usługa Azure Key Vault (AKV) została zaprojektowana w celu zwiększenia bezpieczeństwa i zarządzania tymi kluczami w bezpiecznej i wysoce dostępnej lokalizacji. Łącznik programu SQL Server umożliwia programowi SQL Server używanie tych kluczy z usługi Azure Key Vault.

Jeśli używasz programu SQL Server z komputerami lokalnymi, istnieją kroki, które można wykonać, aby uzyskać dostęp do usługi Azure Key Vault z lokalnego komputera programu SQL Server. Jednak w przypadku programu SQL Server na maszynach wirtualnych platformy Azure można zaoszczędzić czas przy użyciu funkcji integracji usługi Azure Key Vault. Za pomocą kilku poleceń cmdlet programu Azure PowerShell, aby włączyć tę funkcję, można zautomatyzować konfigurację niezbędną do uzyskania dostępu do magazynu kluczy.

### <a name="vm-disk-encryption"></a>Szyfrowanie dysku maszyny Wirtualnej
[Szyfrowanie dysków platformy Azure](../azure-security-disk-encryption-overview.md) to nowa funkcja, która pomaga szyfrować dyski maszyn wirtualnych systemu Windows i Linux IaaS. Stosuje standardową w branży funkcję BitLocker systemu Windows i funkcję DM-Crypt systemu Linux, aby zapewnić szyfrowanie woluminów dla systemu operacyjnego i dysków z danymi. Rozwiązanie jest zintegrowane z usługą Azure Key Vault, aby ułatwić kontrolowanie kluczy szyfrowania dysków i wpisów tajnych w subskrypcji usługi Key Vault oraz zarządzanie nimi. Rozwiązanie zapewnia również, że wszystkie dane na dyskach maszyny wirtualnej są szyfrowane w spoczynku w magazynie platformy Azure.

### <a name="virtual-networking"></a>Sieć wirtualna
Maszyny wirtualne potrzebują łączności sieciowej. Aby obsługiwać to wymaganie, platforma Azure wymaga, aby maszyny wirtualne były połączone z siecią wirtualną platformy Azure. Sieć wirtualna platformy Azure to logiczna konstrukcja zbudowana na podstawie fizycznej sieci szkieletowej platformy Azure. Każda [logiczna sieć wirtualna platformy Azure](../../virtual-network/virtual-networks-overview.md) jest odizolowana od wszystkich innych sieci wirtualnych platformy Azure. Ta izolacja pomaga zapewnić, że ruch sieciowy w wdrożeniach nie jest dostępny dla innych klientów platformy Microsoft Azure.

### <a name="patch-updates"></a>Aktualizacje poprawek
Aktualizacje poprawek stanowią podstawę do znajdowania i rozwiązywania potencjalnych problemów oraz upraszczają proces zarządzania aktualizacjami oprogramowania, zarówno poprzez zmniejszenie liczby aktualizacji oprogramowania, które należy wdrożyć w przedsiębiorstwie, jak i poprzez zwiększenie możliwości monitorowania zgodności.

### <a name="security-policy-management-and-reporting"></a>Zarządzanie zasadami bezpieczeństwa i raportowanie
[Usługa Security Center](../../security-center/security-center-intro.md) pomaga zapobiegać zagrożeniom, wykrywać i reagować na nie, a także zapewnia lepszy wgląd w zabezpieczenia zasobów platformy Azure i kontrolę nad nim. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby przejść niezauważone, i współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem
Zabezpieczanie systemów, aplikacji i danych rozpoczyna się od kontroli dostępu opartych na tożsamości. Funkcje zarządzania tożsamościami i dostępem wbudowane w produkty i usługi biznesowe firmy Microsoft pomagają chronić informacje organizacyjne i osobiste przed nieautoryzowanym dostępem, udostępniając je legalnym użytkownikom w dowolnym miejscu i czasie.

### <a name="secure-identity"></a>Bezpieczna tożsamość
Firma Microsoft używa wielu praktyk i technologii zabezpieczeń w swoich produktach i usługach do zarządzania tożsamością i dostępem.

-   [Uwierzytelnianie wieloskładnikowe](https://azure.microsoft.com/services/multi-factor-authentication/) wymaga od użytkowników używania wielu metod dostępu lokalnego i w chmurze. Zapewnia silne uwierzytelnianie z szeregiem łatwych opcji weryfikacji, a jednocześnie umożliwia użytkownikom prosty proces logowania.

-   [Usługa Microsoft Authenticator](https://aka.ms/authenticator) zapewnia przyjazne dla użytkownika uwierzytelnianie wieloskładnikowe, które współpracuje zarówno z usługą Microsoft Azure Active Directory, jak i kont Microsoft, a także obsługuje urządzenia ubieralne i zatwierdzanie oparte na odciskach palców.

-   [Wymuszanie zasad haseł](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) zwiększa bezpieczeństwo tradycyjnych haseł, nakładając wymagania dotyczące długości i złożoności, wymuszoną rotację okresową i blokadę konta po nieudanych próbach uwierzytelnienia.

-   [Uwierzytelnianie oparte na tokenie](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) umożliwia uwierzytelnianie za pośrednictwem usługi Azure Active Directory.

-   [Kontrola dostępu oparta na rolach (RBAC)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) umożliwia udzielenie dostępu na podstawie przypisanej roli użytkownika, dzięki czemu można łatwo przyznać użytkownikom tylko taką ilość dostępu, jakiej potrzebują do wykonywania swoich obowiązków związanych z zadaniami. Można dostosować RBAC według modelu biznesowego organizacji i tolerancji ryzyka.

-   [Zintegrowane zarządzanie tożsamościami (tożsamość hybrydowa)](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) umożliwia utrzymanie kontroli nad dostępem użytkowników w wewnętrznych centrach danych i platformach w chmurze, tworząc jedną tożsamość użytkownika do uwierzytelniania i autoryzacji do wszystkich zasobów.

### <a name="secure-apps-and-data"></a>Bezpieczne aplikacje i dane
[Usługa Azure Active Directory](https://azure.microsoft.com/services/active-directory/)— kompleksowe rozwiązanie do zarządzania tożsamościami i dostępem w chmurze — ułatwia dostęp do danych w aplikacjach w miejscu i w chmurze oraz upraszcza zarządzanie użytkownikami i grupami. Łączy podstawowe usługi katalogowe, zaawansowane zarządzanie tożsamościami, zabezpieczenia i zarządzanie dostępem do aplikacji i ułatwia deweloperom tworzenie zarządzania tożsamościami opartymi na zasadach w swoich aplikacjach. Aby zwiększyć możliwości usługi Azure Active Directory i uzyskać dostęp do płatnych funkcji, możesz zdecydować się na wykupienie usługi Azure Active Directory w wersji Podstawowej, Premium P1 lub Premium P2.

| Darmowe / wspólne funkcje     | Podstawowe funkcje    |Funkcje Premium P1 |Funkcje Premium P2 | Dołączanie do usługi Azure Active Directory — tylko powiązane funkcje systemu Windows 10|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Obiekty katalogu](../../active-directory/active-directory-whatis.md), [Zarządzanie użytkownikami/grupami (dodawanie/aktualizowanie/usuwanie)/ Inicjowanie obsługi administracyjnej oparte na użytkownikach, rejestracja urządzenia,](../../active-directory/active-directory-whatis.md) [logowanie jednokrotne (logowanie jednokrotne)](../../active-directory/active-directory-whatis.md), [samoobsługowa zmiana hasła dla użytkowników w chmurze,](../../active-directory/active-directory-whatis.md) [Łączenie (aparat synchronizacji rozszerzający katalogi lokalne na usługę Azure Active Directory)](../../active-directory/active-directory-whatis.md), [Raporty zabezpieczeń / użycia](../../active-directory/active-directory-whatis.md)       |   [Zarządzanie dostępem grupowym / inicjowanie obsługi administracyjnej,](../../active-directory/active-directory-whatis.md) [Samoobsługowe resetowanie hasła dla użytkowników chmury,](../../active-directory/active-directory-whatis.md) [znakowanie firmy (dostosowywanie stron logowania/panelu dostępu),](../../active-directory/active-directory-whatis.md) [serwer proxy aplikacji,](../../active-directory/active-directory-whatis.md) [SLA 99,9%](../../active-directory/active-directory-whatis.md) |  [Grupa samoobsługowa i dodawanie aplikacji/dodatki do aplikacji samoobsługowych/grupy dynamiczne,](../../active-directory/active-directory-whatis.md) [Samoobsługowe resetowanie/zmienianie/odblokowywanie haseł z lokalnym odpisem,](../../active-directory/active-directory-whatis.md) [uwierzytelnianie wieloskładnikowe (chmura i lokalna (serwer MFA))](../../active-directory/active-directory-whatis.md), [MIM CAL + MIM Server](../../active-directory/active-directory-whatis.md), [odnajdowanie aplikacji w chmurze,](../../active-directory/active-directory-whatis.md) [kondycja połączania,](../../active-directory/active-directory-whatis.md) [automatyczne przerzukiwanie haseł dla kont grupowych](../../active-directory/active-directory-whatis.md)|    [Ochrona tożsamości](../../active-directory/identity-protection/overview.md), [Zarządzanie tożsamościami uprzywilejowanymi](../../active-directory/privileged-identity-management/pim-configure.md)|   [Dołączanie urządzenia do usługi Azure AD, pulpitu— usługi SSO, usługi Microsoft Passport for Azure AD, odzyskiwanie funkcji BitLocker administratora,](../../active-directory/active-directory-whatis.md) [automatyczna rejestracja mdm, samoobsługowe odzyskiwanie funkcji BitLocker, dodatkowi administratorzy lokalni do urządzeń z systemem Windows 10 za pośrednictwem usługi Azure AD Join](../../active-directory/active-directory-whatis.md)|


- [Odnajdowanie aplikacji w chmurze](../../active-directory/cloudappdiscovery-get-started.md) to funkcja premium usługi Azure Active Directory, która umożliwia identyfikowanie aplikacji w chmurze, które są używane przez pracowników w organizacji.

- [Usługa Azure Active Directory Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) to usługa zabezpieczeń, która korzysta z funkcji wykrywania anomalii usługi Azure Active Directory w celu zapewnienia skonsolidowanego widoku do wykrywania ryzyka i potencjalnych luk w zabezpieczeniach, które mogą mieć wpływ na tożsamość organizacji.

- [Usługi domenowe Active Directory platformy Azure](https://azure.microsoft.com/services/active-directory-ds/) umożliwiają dołączanie maszyn wirtualnych platformy Azure do domeny bez konieczności wdrażania kontrolerów domeny. Użytkownicy logują się do tych maszyn wirtualnych przy użyciu poświadczeń firmowej usługi Active Directory i bezproblemowo uzyskują dostęp do zasobów.

- [Usługa Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) to wysoce dostępna usługa globalnego zarządzania tożsamościami dla aplikacji przeznaczonych dla konsumentów, która może skalować do setek milionów tożsamości i integrować się z platformami mobilnymi i sieci web. Klienci mogą logować się do wszystkich aplikacji za pomocą konfigurowalnych środowisk korzystających z istniejących kont w mediach społecznościowych lub utworzyć nowe autonomiczne poświadczenia.

- [Usługa Azure Active Directory B2B Collaboration](https://aka.ms/aad-b2b-collaboration) to bezpieczne rozwiązanie do integracji partnerów, które obsługuje relacje między firmami, umożliwiając partnerom selektywny dostęp do aplikacji firmowych i danych przy użyciu ich tożsamości zarządzanych przez siebie.

- [Dołączenie usługi Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) umożliwia rozszerzenie możliwości chmury na urządzenia z systemem Windows 10 w celu scentralizowanego zarządzania. Umożliwia użytkownikom łączenie się z chmurą firmową lub organizacyjną za pośrednictwem usługi Azure Active Directory i upraszcza dostęp do aplikacji i zasobów.

- [Usługa Azure Active Directory Application Proxy](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) zapewnia sytua dla osób typu SSO i bezpieczny dostęp zdalny dla aplikacji sieci web hostowanych lokalnie.

## <a name="next-steps"></a>Następne kroki

- Poznaj [swoją wspólną odpowiedzialność w chmurze](shared-responsibility.md).

- Dowiedz się, jak [usługa Azure Security Center](https://azure.microsoft.com/services/security-center/) może pomóc w zapobieganiu zagrożeniom, wykrywaniu ich i reagowaniu na nie dzięki zwiększonej widoczności i kontroli nad bezpieczeństwem zasobów platformy Azure.
