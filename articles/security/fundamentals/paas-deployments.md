---
title: Najważniejsze wskazówki dotyczące bezpiecznych wdrożeń PaaS — Microsoft Azure
description: Poznaj najlepsze rozwiązania dotyczące projektowania, tworzenia i zarządzania bezpiecznymi aplikacjami w chmurze na platformie Azure i poznaj zalety zabezpieczeń paas w porównaniu z innymi modelami usług w chmurze.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: techlake
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 9adbe7b03283a00f78222ffdc77dca7aaadcbda0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461705"
---
# <a name="securing-paas-deployments"></a>Zabezpieczanie wdrożeń typu PaaS

Ten artykuł zawiera informacje, które pomagają:

- Poznaj zalety zabezpieczeń hostowania aplikacji w chmurze
- Oceń zalety bezpieczeństwa platformy jako usługi (PaaS) w porównaniu z innymi modelami usług w chmurze
- Zmienianie skoncentrowania się na zabezpieczeniach z podejścia zorientowanego na sieć na zorientowane na tożsamość podejście do zabezpieczeń obwodowych
- Wdrażanie ogólnych zaleceń dotyczących najlepszych rozwiązań w zakresie zabezpieczeń PaaS

[Tworzenie bezpiecznych aplikacji na platformie Azure](abstract-develop-secure-apps.md) jest ogólnym przewodnikiem po pytaniach i formantach zabezpieczeń, które należy wziąć pod uwagę na każdym etapie cyklu życia tworzenia oprogramowania podczas tworzenia aplikacji dla chmury.

## <a name="cloud-security-advantages"></a>Zalety zabezpieczeń w chmurze
Ważne jest, aby zrozumieć [podział odpowiedzialności](shared-responsibility.md) między użytkownikiem a firmą Microsoft. Lokalnie jesteś właścicielem całego stosu, ale w miarę przenoszenia do chmury niektóre obowiązki są przenoszone do firmy Microsoft.

Istnieją [zalety bezpieczeństwa, aby być w chmurze.](shared-responsibility.md#cloud-security-advantages) W środowisku lokalnym organizacje prawdopodobnie mają niezaspokojone obowiązki i ograniczone zasoby dostępne do inwestowania w zabezpieczenia, co tworzy środowisko, w którym osoby atakujące mogą wykorzystywać luki we wszystkich warstwach.

Organizacje są w stanie poprawić czas wykrywania zagrożeń i reagowania przy użyciu usług dostawcy w chmurze i analizy w chmurze.  Przenosząc obowiązki na dostawcę chmury, organizacje mogą uzyskać większy zakres zabezpieczeń, co umożliwia im przeniesienie zasobów zabezpieczeń i budżetu na inne priorytety biznesowe.

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Zalety zabezpieczeń modelu usługi w chmurze PaaS
Przyjrzyjmy się zaletom zabezpieczeń wdrożenia usługi Azure PaaS w porównaniu z lokalnymi.

![Zalety bezpieczeństwa PaaS](./media/paas-deployments/advantages-of-paas.png)

Począwszy od dolnej części stosu, infrastruktury fizycznej, Firma Microsoft zmniejsza typowe zagrożenia i obowiązki. Ponieważ chmura Microsoft jest stale monitorowana przez firmę Microsoft, trudno jest atakować. Nie ma sensu, aby osoba atakująca ścigała chmurę Microsoft jako cel. Jeśli osoba atakująca nie ma dużo pieniędzy i zasobów, osoba atakująca prawdopodobnie przejdzie do innego celu.  

W środku stosu nie ma różnicy między wdrożeniem PaaS i lokalnie. W warstwie aplikacji oraz warstwie zarządzania kontami i dostępem istnieje podobne ryzyko. W następnych krokach tego artykułu poprowadzimy Cię do najlepszych rozwiązań dotyczących eliminowania lub minimalizowania tych zagrożeń.

W górnej części stosu, zarządzania danymi i zarządzania prawami, można podjąć się jednego ryzyka, które mogą być złagodzone przez zarządzanie kluczami. (Zarządzanie kluczami jest objęte najlepszymi praktykami). Podczas zarządzania kluczami jest dodatkową odpowiedzialnością, masz obszary we wdrożeniu PaaS, które nie trzeba już zarządzać, dzięki czemu można przenieść zasoby do zarządzania kluczami.

Platforma Azure zapewnia również silną ochronę przed atakami DDoS przy użyciu różnych technologii sieciowych. Jednak wszystkie typy metod ochrony DDoS opartych na sieci mają swoje limity dla per-link i per-datacenter. Aby uniknąć wpływu dużych ataków DDoS, można skorzystać z podstawowej funkcji chmury platformy Azure, umożliwiając szybkie i automatyczne skalowanie w poziomie w celu obrony przed atakami DDoS. Przejdziemy do bardziej szczegółowych informacji na temat tego, jak można to zrobić w polecanych artykułach praktyk.

## <a name="modernizing-the-defenders-mindset"></a>Modernizacja mentalności obrońcy
Z wdrożeń PaaS przyjść zmiany w ogólnym podejściu do bezpieczeństwa. Przejście od konieczności kontrolowania wszystkiego samodzielnie do dzielenia się odpowiedzialnością z firmą Microsoft.

Inną istotną różnicą między PaaS i tradycyjnych wdrożeń lokalnych, jest nowy widok tego, co definiuje obwód zabezpieczeń podstawowych. Historycznie podstawowym obwodem zabezpieczeń lokalnych była sieć, a większość lokalnych projektów zabezpieczeń używa sieci jako podstawowej wersji obrotu zabezpieczeń. W przypadku wdrożeń PaaS lepiej jest obsługiwać, biorąc pod uwagę tożsamość jako podstawowy obwód zabezpieczeń.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Przyjęcie zasady tożsamości jako obwodu zabezpieczeń podstawowych
Jedną z pięciu podstawowych cech przetwarzania w chmurze jest szeroki dostęp do sieci, co sprawia, że myślenie zorientowane na sieć jest mniej istotne. Celem wielu przetwarzania w chmurze jest umożliwienie użytkownikom dostępu do zasobów niezależnie od lokalizacji. Dla większości użytkowników ich lokalizacja będzie gdzieś w Internecie.

Na poniższej ilustracji pokazano, jak obwód zabezpieczeń ewoluował z obwodu sieci do obwodu tożsamości. Bezpieczeństwo staje się mniej o obronie sieci, a więcej o obronie danych, a także zarządzanie bezpieczeństwem aplikacji i użytkowników. Kluczową różnicą jest to, że chcesz przybliżyć bezpieczeństwo do tego, co jest ważne dla Twojej firmy.

![Tożsamość jako nowy obwód zabezpieczeń](./media/paas-deployments/identity-perimeter.png)

Początkowo usługi PaaS platformy Azure (na przykład role sieci web i sql platformy Azure) pod warunkiem, że niewiele lub nie tradycyjnych ochrony obwodowej sieci. Zrozumiano, że celem elementu było być narażone na Internet (rola sieci web) i że uwierzytelnianie zapewnia nowy obwód (na przykład BLOB lub Azure SQL).

Nowoczesne praktyki bezpieczeństwa zakładają, że przeciwnik naruszył obwód sieci. W związku z tym nowoczesne praktyki obrony przeniosły się do tożsamości. Organizacje muszą ustanowić obwód zabezpieczeń oparty na tożsamości z silnym uwierzytelnianiem i higieną autoryzacji (najlepsze rozwiązania).

Zasady i wzorce dla obwodu sieci są dostępne od dziesięcioleci. W przeciwieństwie do tego, przemysł ma stosunkowo mniejsze doświadczenie z używaniem tożsamości jako podstawowego obwodu zabezpieczeń. Mając to na uwadze, zgromadziliśmy wystarczająco dużo doświadczenia, aby przedstawić ogólne zalecenia, które są sprawdzone w tej dziedzinie i mają zastosowanie do prawie wszystkich usług PaaS.

Poniżej przedstawiono najlepsze rozwiązania dotyczące zarządzania obwodem tożsamości.

**Najlepsze rozwiązanie:** Zabezpiecz klucze i poświadczenia, aby zabezpieczyć wdrożenie PaaS.   
**Szczegóły:** Utrata kluczy i poświadczeń jest częstym problemem. Można użyć scentralizowanego rozwiązania, w którym klucze i wpisy tajne mogą być przechowywane w sprzętowych modułach zabezpieczeń (HSM). [Usługa Azure Key Vault](../../key-vault/general/overview.md) chroni klucze i wpisy tajne, szyfrując klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych, pliki .pfx i hasła przy użyciu kluczy chronionych przez moduły HSM.

**Najlepsze rozwiązanie:** Nie umieszczaj poświadczeń i innych wpisów tajnych w kodzie źródłowym lub usłudze GitHub.   
**Szczegóły:** Jedyną rzeczą gorszą niż utrata kluczy i poświadczeń jest uzyskanie dostępu do nich przez nieautoryzowaną stronę. Osoby atakujące mogą korzystać z technologii botów, aby znaleźć klucze i wpisy tajne przechowywane w repozytoriach kodu, takich jak GitHub. Nie należy umieszczać kluczy i wpisów tajnych w tych publicznych repozytoriach kodu.

**Najlepsze rozwiązanie:** Chroń interfejsy zarządzania maszynami wirtualnymi w hybrydowych usługach PaaS i IaaS za pomocą interfejsu zarządzania, który umożliwia bezpośrednie zdalne zarządzanie tymi maszynami wirtualnymi.   
**Szczegóły:** Można używać protokołów zdalnego zarządzania, takich jak [SSH,](https://en.wikipedia.org/wiki/Secure_Shell) [RDP](https://support.microsoft.com/kb/186607)i [PowerShell.](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) Ogólnie rzecz biorąc zaleca się, aby nie włączać bezpośredniego zdalnego dostępu do maszyn wirtualnych z Internetu.

Jeśli to możliwe, należy użyć alternatywnych metod, takich jak przy użyciu wirtualnych sieci prywatnych w sieci wirtualnej platformy Azure. Jeśli alternatywne metody nie są dostępne, upewnij się, że używasz złożonych haseł i uwierzytelniania dwuskładnikowego (na przykład [uwierzytelnianie wieloskładnikowe platformy Azure).](/azure/active-directory/authentication/multi-factor-authentication)

**Najlepsze rozwiązanie:** Użyj platform silnego uwierzytelniania i autoryzacji.   
**Szczegóły:** Użyj tożsamości federacyjne w usłudze Azure AD zamiast niestandardowych magazynów użytkowników. Korzystając z tożsamości federacyjnej, można skorzystać z podejścia opartego na platformie i delegować zarządzanie autoryzowanych tożsamości do partnerów. Podejście tożsamości federacyjnej jest szczególnie ważne, gdy pracownicy są zamknięci, a informacje muszą być odzwierciedlane za pomocą wielu systemów tożsamości i autoryzacji.

Zamiast kodu niestandardowego należy używać mechanizmów uwierzytelniania i autoryzacji dostarczonych przez platformę. Powodem jest to, że tworzenie niestandardowego kodu uwierzytelniania może być podatne na błędy. Większość programistów nie są ekspertami w dziedzinie zabezpieczeń i jest mało prawdopodobne, aby być świadomi subtelności i najnowszych osiągnięć w zakresie uwierzytelniania i autoryzacji. Kod komercyjny (na przykład firmy Microsoft) jest często szeroko sprawdzany w zakresie zabezpieczeń.

Użyj uwierzytelniania dwuskładnikowego. Uwierzytelnianie dwuskładnikowe jest aktualnym standardem uwierzytelniania i autoryzacji, ponieważ pozwala uniknąć zabezpieczeń związanych z nazwami użytkowników i hasła typami uwierzytelniania. Dostęp zarówno do interfejsów zarządzania platformą Azure (portal/remote PowerShell), jak i usług przeznaczonych dla klientów powinien być zaprojektowany i skonfigurowany do korzystania z [uwierzytelniania wieloskładnikowego platformy Azure.](/azure/active-directory/authentication/multi-factor-authentication)

Użyj standardowych protokołów uwierzytelniania, takich jak OAuth2 i Kerberos. Protokoły te zostały szeroko ocenione i prawdopodobnie są implementowane jako część bibliotek platformy do uwierzytelniania i autoryzacji.

## <a name="use-threat-modeling-during-application-design"></a>Używanie modelowania zagrożeń podczas projektowania aplikacji
Cykl [życia rozwoju zabezpieczeń](https://www.microsoft.com/en-us/sdl) firmy Microsoft określa, że zespoły powinny angażować się w proces zwany modelowania zagrożeń w fazie projektowania. Aby ułatwić ten proces, firma Microsoft utworzyła [narzędzie do modelowania zagrożeń SDL](/azure/security/azure-security-threat-modeling-tool). Modelowanie projektu aplikacji i wyliczanie zagrożeń [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) we wszystkich granicach zaufania może wcześnie wyłapać błędy projektowe.

W poniższej tabeli wymieniono zagrożenia STRIDE i podano kilka przykładowych środków zaradczych korzystających z funkcji platformy Azure. Te środki zaradcze nie będą działać w każdej sytuacji.

| Zagrożenie | Właściwość zabezpieczeń | Potencjalne środki zaradcze platformy Azure |
| --- | --- | --- |
| Fałszowanie zawartości | Authentication | Wymagaj połączeń HTTPS. |
| Manipulowanie | Integralność | Sprawdzanie poprawności certyfikatów TLS/SSL. |
| Odrzucenie | Brak odrzucenia | Włącz [monitorowanie i diagnostykę](/azure/architecture/best-practices/monitoring)platformy Azure . |
| Ujawnianie informacji | Poufność | Szyfruj poufne dane w spoczynku przy użyciu [certyfikatów usług](/rest/api/appservice/certificates). |
| Odmowa usługi | Dostępność | Monitoruj metryki wydajności pod kątem potencjalnych warunków typu "odmowa usługi". Implementowanie filtrów połączeń. |
| Podniesienie uprawnień | Autoryzacja | Użyj [zarządzania tożsamościami uprzywilejowanymi](/azure/active-directory/privileged-identity-management/subscription-requirements). |

## <a name="develop-on-azure-app-service"></a>Tworzenie usługi aplikacji platformy Azure
[Usługa Azure App Service](/azure/app-service/overview) to usługa PaaS, która umożliwia tworzenie aplikacji sieci Web i aplikacji mobilnych dla dowolnej platformy lub urządzenia oraz łączenie się z danymi w dowolnym miejscu, w chmurze lub lokalnie. Usługa App Service zawiera funkcje sieci web i urządzeń przenośnych, które były wcześniej dostarczane oddzielnie jako witryny sieci Web platformy Azure i usługi azure mobile. Obejmuje ona także nowe funkcje automatyzacji procesów biznesowych i hostowania interfejsów API w chmurze. Jako pojedyncza zintegrowana usługa usługa App Service zapewnia bogaty zestaw funkcji do scenariuszy sieci Web, urządzeń mobilnych i integracji.

Poniżej przedstawiono najlepsze rozwiązania dotyczące korzystania z usługi app service.

**Najlepsze rozwiązanie:** [Uwierzytelnij się za pośrednictwem usługi Azure Active Directory](/azure/app-service/overview-authentication-authorization).   
**Szczegóły:** Usługa app service zapewnia usługę OAuth 2.0 dla dostawcy tożsamości. OAuth 2.0 koncentruje się na prostocie dewelopera klienta, zapewniając jednocześnie określone przepływy autoryzacji dla aplikacji sieci web, aplikacji komputerowych i telefonów komórkowych. Usługa Azure AD używa usługi OAuth 2.0, aby umożliwić autoryzowanie dostępu do aplikacji mobilnych i sieci Web.

**Najlepsze rozwiązanie:** Ogranicz dostęp na podstawie konieczności poznania i najmniejszych zasad zabezpieczeń uprawnień.   
**Szczegóły:** Ograniczenie dostępu jest konieczne dla organizacji, które chcą wymusić zasady zabezpieczeń dostępu do danych. Za pomocą funkcji RBAC można przypisywać uprawnienia użytkownikom, grupom i aplikacjom w określonym zakresie. Aby dowiedzieć się więcej o przyznawaniu użytkownikom dostępu do aplikacji, zobacz [Wprowadzenie do zarządzania dostępem](/azure/role-based-access-control/overview).

**Najlepsze rozwiązanie:** Chroń swoje klucze.   
**Szczegóły:** Usługa Azure Key Vault pomaga chronić klucze kryptograficzne i wpisy tajne używane przez aplikacje i usługi w chmurze. Za pomocą usługi Key Vault można szyfrować klucze i wpisy tajne (takie jak klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych, . PFX i hasła) przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń (HSM). W celu zapewnienia dodatkowego bezpieczeństwa możesz zaimportować lub wygenerować klucze w modułach HSM. Więcej informacji można znaleźć w [usłudze Azure Key Vault.](/azure/key-vault/key-vault-overview) Za pomocą usługi Key Vault można również zarządzać certyfikatami TLS za pomocą automatycznego odnawiania.

**Najlepsze rozwiązanie:** Ograniczanie przychodzących źródłowych adresów IP.   
**Szczegóły:** [Środowisko usługi app service](/azure/app-service/environment/intro) ma funkcję integracji sieci wirtualnej, która pomaga ograniczyć przychodzące źródłowe adresy IP za pośrednictwem sieciowych grup zabezpieczeń. Sieci wirtualne umożliwiają umieszczanie zasobów platformy Azure w sieci niekonserwowalnej, kontrolowanej przez ciebie. Aby dowiedzieć się więcej, zobacz [Integrowanie aplikacji z siecią wirtualną platformy Azure](/azure/app-service/web-sites-integrate-with-vnet).

**Najlepsze rozwiązanie:** monitorowanie stanu zabezpieczeń środowisk usługi app service.   
**Szczegóły:** Użyj usługi Azure Security Center do monitorowania środowisk usługi App Service. Gdy usługa Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy [zalecenia,](../../security-center/security-center-virtual-machine-protection.md) które poprowadzą Użytkownika przez proces konfigurowania potrzebnych formantów.

> [!NOTE]
> Usługa monitorowania aplikacji jest w wersji zapoznawczej i jest dostępna tylko w [warstwie Standardowa](/azure/security-center/security-center-pricing) Usługi Zabezpieczeń.
>
>

## <a name="install-a-web-application-firewall"></a>Instalowanie zapory aplikacji sieci Web
Aplikacje internetowe coraz częściej stają się obiektami złośliwych ataków wykorzystujących znane luki w zabezpieczeniach. Wśród nich często zdarzają się np. ataki polegające na iniekcji SQL i ataki z użyciem skryptów wykorzystywanych w wielu witrynach. Zapobieganie takim atakom z poziomu kodu aplikacji może być trudne. Może też wymagać rygorystycznego przestrzegania harmonogramu konserwacji, poprawek i monitorowania na wielu warstwach topologii aplikacji. Scentralizowana zapora aplikacji internetowej ułatwia zarządzanie zabezpieczeniami oraz zapewnia lepszą ochronę administratorów aplikacji przed zagrożeniami i intruzami. Zapora aplikacji internetowej może reagować na zagrożenia bezpieczeństwa szybciej — poprzez wdrażanie poprawek zapobiegających wykorzystaniu znanych luk w zabezpieczeniach w centralnej lokalizacji zamiast w poszczególnych aplikacjach internetowych. Istniejące bramy Application Gateway można łatwo przekonwertować na bramę Application Gateway obsługującą zaporę aplikacji internetowej.

[Zapora aplikacji sieci Web (WAF)](/azure/frontdoor/waf-overview) jest funkcją bramy aplikacji, która zapewnia scentralizowaną ochronę aplikacji internetowych przed typowymi exploitami i lukami w zabezpieczeniach. WAF opiera się na regułach z [podstawowych reguł projektu OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 lub 2.2.9.

## <a name="monitor-the-performance-of-your-applications"></a>Monitorowanie wydajności aplikacji
Monitorowanie jest czynnością zbierania i analizowania danych w celu określenia wydajności, kondycji i dostępności aplikacji. Skuteczna strategia monitorowania pomaga w zrozumieniu szczegółów działania składników aplikacji. Pomaga zwiększyć czas pracy bez przestojów, powiadamiając o krytycznych problemach, dzięki czemu można je rozwiązać, zanim staną się problemami. Pomaga również wykryć anomalie, które mogą być związane z zabezpieczeniami.

Usługa [Azure Application Insights](https://azure.microsoft.com/documentation/services/application-insights) umożliwia monitorowanie dostępności, wydajności i użycia aplikacji, niezależnie od tego, czy jest hostowana w chmurze, czy lokalnie. Za pomocą usługi Application Insights, można szybko zidentyfikować i zdiagnozować błędy w aplikacji bez oczekiwania na użytkownika do ich raportu. Dzięki zbieranym informacjom można podejmować świadome decyzje w zakresie konserwacji i ulepszeń aplikacji.

Usługa Application Insights ma szeroką gamę narzędzi do interakcji z danymi, które zbiera. Usługa Application Insights przechowuje dane we wspólnym repozytorium. Może korzystać z funkcji udostępnionych, takich jak alerty, pulpity nawigacyjne i dogłębnej analizy z języka zapytań Kusto.

## <a name="perform-security-penetration-testing"></a>Przeprowadzanie testów penetracji zabezpieczeń
Sprawdzanie poprawności zabezpieczeń jest równie ważne jak testowanie innych funkcji. Spraw, aby [testy penetracyjne](pen-testing.md) były standardową częścią procesu kompilacji i wdrażania. Zaplanuj regularne testy zabezpieczeń i skanowanie luk w zabezpieczeniach wdrożonych aplikacji oraz monitoruj otwarte porty, punkty końcowe i ataki.

Testy fuzz to metoda znajdowania błędów programu (błędów kodu) przez dostarczanie nieprawidłowo sformułowanych danych wejściowych do interfejsów programu (punktów wejścia), które analizują i zużywają te dane. [Wykrywanie zagrożeń bezpieczeństwa](https://www.microsoft.com/en-us/security-risk-detection/) firmy Microsoft to narzędzie oparte na chmurze, którego można użyć do wyszukiwania błędów i innych luk w zabezpieczeniach oprogramowania przed wdrożeniem go na platformie Azure. Narzędzie jest przeznaczone do wyładunku luk w zabezpieczeniach przed wdrożeniem oprogramowania, dzięki czemu nie trzeba załatać błędu, radzić sobie z awariami lub reagować na atak po wydaniu oprogramowania.


## <a name="next-steps"></a>Następne kroki
W tym artykule skupiliśmy się na zaletach zabezpieczeń wdrożenia usługi Azure PaaS i najlepszych praktyk w zakresie zabezpieczeń dla aplikacji w chmurze. Następnie zapoznaj się z zalecanymi praktykami dotyczącymi zabezpieczania rozwiązań sieci Web i mobilnych PaaS przy użyciu określonych usług platformy Azure. Zaczniemy od usługi Azure App Service, usługi Azure SQL Database i usługi Azure SQL Data Warehouse oraz usługi Azure Storage. W miarę dostępności artykułów na temat zalecanych rozwiązań dla innych usług platformy Azure łącza będą dostępne na następującej liście:

- [Azure App Service](paas-applications-using-app-services.md)
- [Usługa Azure SQL Database i usługa Azure SQL Data Warehouse](paas-applications-using-sql.md)
- [Azure Storage](paas-applications-using-storage.md)
- Azure Cache for Redis
- Azure Service Bus
- Zapory aplikacji sieci Web

Zobacz [Tworzenie bezpiecznych aplikacji na platformie Azure](abstract-develop-secure-apps.md) w celu uzyskania pytań i formantów, które należy wziąć pod uwagę na każdym etapie cyklu życia tworzenia oprogramowania podczas tworzenia aplikacji dla chmury.

Zobacz [najlepsze rozwiązania i wzorce zabezpieczeń platformy Azure,](best-practices-and-patterns.md) aby uzyskać więcej najlepszych rozwiązań w zakresie zabezpieczeń, których należy używać podczas projektowania, wdrażania i zarządzania rozwiązaniami w chmurze przy użyciu platformy Azure.

Dostępne są następujące zasoby, aby zapewnić bardziej ogólne informacje na temat zabezpieczeń platformy Azure i powiązanych usług firmy Microsoft:
* [Blog zespołu zabezpieczeń platformy Azure](https://blogs.msdn.microsoft.com/azuresecurity/) — aby uzyskać aktualne informacje na temat najnowszych informacji w usłudze Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) — gdzie można zgłaszać luki w zabezpieczeniach firmy Microsoft, w tym problemy z platformą Azure, lubsecure@microsoft.com
