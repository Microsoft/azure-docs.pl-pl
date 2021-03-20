---
title: Najlepsze rozwiązania dotyczące bezpiecznych wdrożeń PaaS — Microsoft Azure
description: Poznaj najlepsze rozwiązania dotyczące projektowania, tworzenia i zarządzania bezpiecznymi aplikacjami w chmurze na platformie Azure oraz Poznaj zalety zabezpieczeń PaaS i innych modeli usług w chmurze.
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
ms.openlocfilehash: 77a2a32b9a6358c39a14cfe37eeb44f7cb90af0a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94841992"
---
# <a name="securing-paas-deployments"></a>Zabezpieczanie wdrożeń typu PaaS

Ten artykuł zawiera informacje ułatwiające:

- Zapoznaj się z zaletami zabezpieczeń aplikacji hostingowych w chmurze
- Oceń zalety zabezpieczeń platformy jako usługi (PaaS) a inne modele usług w chmurze
- Zmień koncentrację zabezpieczeń z skoncentrowanego na sieci do podejścia do zabezpieczeń obwodowych ukierunkowanych na tożsamość
- Zaimplementuj ogólne zalecenia dotyczące najlepszych rozwiązań w zakresie zabezpieczeń PaaS

[Opracowywanie bezpiecznych aplikacji na platformie Azure](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/) to ogólny przewodnik dotyczący pytań zabezpieczających i kontrolek, które należy wziąć pod uwagę w każdej fazie cyklu projektowania oprogramowania podczas tworzenia aplikacji dla chmury.

## <a name="cloud-security-advantages"></a>Zalety chmury
Ważne jest zrozumienie [działu odpowiedzialności](shared-responsibility.md) między ty i firmą Microsoft. W środowisku lokalnym jest własnością całego stosu, ale podczas przechodzenia do chmury niektóre odpowiedzialności są przekazywane do firmy Microsoft.

Istnieją [zalety zabezpieczeń w chmurze](shared-responsibility.md#cloud-security-advantages). W środowisku lokalnym organizacje mogą korzystać z niewypełnienia obowiązków i ograniczonych zasobów w celu inwestowania w zabezpieczeniach, co tworzy środowisko, w którym atakujący mogą wykorzystać luki we wszystkich warstwach.

Organizacje mogą ulepszyć czas wykrywania zagrożeń i czasy odpowiedzi przy użyciu opartych na chmurze funkcji zabezpieczeń i analizy chmury.  Dzięki przesunięciu obowiązków do dostawcy usług w chmurze organizacje mogą uzyskać więcej informacji o zabezpieczeniach, co umożliwia im ponowne przydzielanie zasobów zabezpieczeń i budżetu do innych priorytetów firmy.

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Zalety zabezpieczeń modelu usług w chmurze PaaS
Przyjrzyjmy się zabezpieczeniom dotyczącym wdrożenia usługi Azure PaaS, a w środowisku lokalnym.

![Zalety zabezpieczeń PaaS](./media/paas-deployments/advantages-of-paas.png)

Począwszy od dolnej części stosu, infrastruktura fizyczna, firma Microsoft ogranicza typowe zagrożenia i obowiązki. Ponieważ chmura firmy Microsoft jest stale monitorowana przez firmę Microsoft, trudno jest uzyskać atak. Nie ma sensu, aby osoba atakująca prowadziła chmurę firmy Microsoft jako element docelowy. Jeśli osoba atakująca nie ma dużej ilości pieniędzy i zasobów, osoba atakująca prawdopodobnie przejdzie do innego obiektu docelowego.  

W środku stosu nie ma różnicy między wdrożeniem PaaS i środowiskiem lokalnym. W warstwie aplikacji i w warstwie zarządzania kontami i dostępem są podobne zagrożenia. W następnych krokach tego artykułu przeprowadzimy Cię do najlepszych rozwiązań dotyczących eliminowania i minimalizowania tych zagrożeń.

W górnej części stosu, zarządzania danymi i zarządzaniem prawami podejmuje się ryzyko związane z zarządzaniem kluczami. (Zarządzanie kluczami jest omówione w najlepszych rozwiązaniach). Chociaż zarządzanie kluczami jest dodatkową odpowiedzialnością, istnieją obszary w PaaS wdrożenia, które nie są już potrzebne do zarządzania, dzięki czemu można przenieść zasoby do zarządzania kluczami.

Platforma Azure zapewnia także silną DDoSą ochronę za pomocą różnych technologii sieciowych. Jednak wszystkie typy opartych na sieci metod ochrony DDoS mają swoje limity dla poszczególnych linków i dla poszczególnych centrów. Aby zapobiec wpływowi na duże ataki DDoS, możesz skorzystać z możliwości chmury podstawowej platformy Azure w celu umożliwienia szybkiego i automatycznego skalowania w poziomie w celu obrony przed atakami DDoS. Więcej szczegółów na ten temat można znaleźć w artykułach zalecane rozwiązania.

## <a name="modernizing-the-defenders-mindset"></a>Modernizacja sposób myśleniai Defender
Dzięki wdrożeniom PaaS można przejść do ogólnego podejścia do zabezpieczeń. Przesuniesz się z konieczności kontroli wszystkiego, aby współużytkować z firmą Microsoft.

Kolejną istotną różnicą między PaaS i tradycyjnymi wdrożeniami lokalnymi jest nowy widok definiowania podstawowego obwodu zabezpieczeń. Historycznie podstawowa lokalna sieć obwodu zabezpieczeń była siecią i większość lokalnych projektów zabezpieczeń korzysta z sieci jako podstawowego obszaru wystawcy zabezpieczeń. W przypadku wdrożeń PaaS lepszym rozwiązaniem jest rozważanie tożsamości jako podstawowego obwodu zabezpieczeń.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Przyjmowanie zasad tożsamości jako podstawowego obwodu zabezpieczeń
Jedną z pięciu najważniejszych cech w chmurze obliczeniowej jest szeroki dostęp do sieci, co sprawia, że zorientowane na sieć mniej istotne. Celem większości obliczeń w chmurze jest umożliwienie użytkownikom dostępu do zasobów niezależnie od lokalizacji. W przypadku większości użytkowników ich lokalizacja powinna znajdować się w Internecie.

Na poniższej ilustracji przedstawiono sposób, w jaki obwód zabezpieczeń został rozwijający się od obwodu sieci do obwodu tożsamości. Zabezpieczenia są mniej dotyczące obrony sieci i więcej informacji na temat obrony danych, a także do zarządzania zabezpieczeniami aplikacji i użytkowników. Kluczową różnicą jest to, że chcesz wypchnąć zabezpieczenia bliżej tego, co jest ważne dla Twojej firmy.

![Tożsamość jako nowy obwód zabezpieczeń](./media/paas-deployments/identity-perimeter.png)

Początkowo usługi Azure PaaS Services (na przykład role sieci Web i Azure SQL) zapewniają małą lub nietradycyjne zabezpieczenia sieci obwodowej. Stwierdzono, że cel elementu miał być narażony na Internet (rolę sieci Web) i że uwierzytelnianie zapewnia nowy obwód (na przykład obiekt BLOB lub Azure SQL).

Nowoczesne praktyki w zakresie zabezpieczeń założono, że atakującej narusza granicę sieci. W związku z tym nowoczesne praktyki obrony zostały przeniesione do tożsamości. Organizacje muszą nawiązywać zabezpieczenia oparte na tożsamościach z silnym uwierzytelnianiem i higieną autoryzacji (najlepsze rozwiązania).

Zasady i wzorce dla obwodu sieci są dostępne dla dekad. Z kolei branża ma stosunkowo mniej doświadczenia z używaniem tożsamości jako podstawowego obwodu zabezpieczeń. Mając to na celu zakumulowanie wystarczającej liczby zaleceń, które są sprawdzane w polu i dotyczą niemal wszystkich usług PaaS Services.

Poniżej przedstawiono najlepsze rozwiązania dotyczące zarządzania obwodem tożsamości.

**Najlepsze rozwiązanie**: Zabezpiecz klucze i poświadczenia, aby zabezpieczyć wdrożenie PaaS.   
**Szczegóły**: problem polega na utracie kluczy i poświadczeń. Możesz użyć scentralizowanego rozwiązania, w którym klucze i wpisy tajne mogą być przechowywane w sprzętowych modułach zabezpieczeń (sprzętowych modułów zabezpieczeń). [Azure Key Vault](../../key-vault/general/overview.md) zabezpiecza klucze i wpisy tajne, szyfrując klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych, pliki PFX i hasła przy użyciu kluczy chronionych przez sprzętowych modułów zabezpieczeń.

**Najlepsze rozwiązanie**: nie umieszczaj poświadczeń i innych wpisów tajnych w kodzie źródłowym ani w witrynie GitHub.   
**Szczegóły**: jedynym warunkiem, aby nie utracić kluczy i poświadczeń, jest posiadanie nieautoryzowanej strony dostępu do nich. Osoby atakujące mogą skorzystać z technologii bot, aby znaleźć klucze i wpisy tajne przechowywane w repozytoriach kodu, takich jak GitHub. Nie umieszczaj kluczy i wpisów tajnych w tych publicznych repozytoriach kodu.

**Najlepsze rozwiązanie**: Zabezpiecz interfejsy zarządzania maszyną wirtualną w ramach hybrydowych usług PaaS i IaaS przy użyciu interfejsu zarządzania, który umożliwia zdalne zarządzanie tymi maszynami wirtualnymi.   
**Szczegóły**: można używać protokołów zarządzania zdalnego, takich jak [SSH](https://en.wikipedia.org/wiki/Secure_Shell), [RDP](https://support.microsoft.com/kb/186607)i [komunikacja zdalna programu PowerShell](/powershell/module/microsoft.powershell.core/enable-psremoting) . Ogólnie rzecz biorąc, zaleca się, aby nie włączać bezpośredniego dostępu zdalnego do maszyn wirtualnych z Internetu.

Jeśli to możliwe, użyj alternatywnych metod, takich jak korzystanie z wirtualnych sieci prywatnych w sieci wirtualnej platformy Azure. Jeśli alternatywne podejścia nie są dostępne, należy się upewnić, że są używane złożone hasła i uwierzytelnianie dwuskładnikowe (na przykład [usługa Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)).

**Najlepsze rozwiązanie**: Użyj mocnych platform uwierzytelniania i autoryzacji.   
**Szczegóły**: Użyj tożsamości federacyjnych w usłudze Azure AD zamiast niestandardowych magazynów użytkowników. W przypadku korzystania z tożsamości federacyjnych należy skorzystać z podejścia opartego na platformie i delegować zarządzanie autoryzowanymi tożsamościami do partnerów. Podejście do tożsamości federacyjnej jest szczególnie ważne, gdy pracownicy są zwolnieni i że informacje muszą być widoczne przez wiele systemów tożsamości i autoryzacji.

Używanie mechanizmów uwierzytelniania i autoryzacji dostarczonych przez platformę zamiast kodu niestandardowego. Przyczyną jest to, że programowanie niestandardowego kodu uwierzytelniania może być podatne na błędy. Większość deweloperów nie jest ekspertami ds. zabezpieczeń i nie jest mało prawdopodobne, że subtleties i najnowsze postępy w zakresie uwierzytelniania i autoryzacji. Kod komercyjny (na przykład od firmy Microsoft) jest często szeroko przeglądany pod kątem zabezpieczeń.

Użyj uwierzytelniania dwuskładnikowego. Uwierzytelnianie dwuskładnikowe jest bieżącym standardem uwierzytelniania i autoryzacji, ponieważ pozwala to uniknąć słabych zagrożeń związanych z typami nazw użytkowników i haseł uwierzytelniania. Dostęp do interfejsów Azure Management (Portal/Remote PowerShell) i usług związanych z klientem powinien zostać zaprojektowany i skonfigurowany do korzystania z usługi [Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md).

Użyj standardowych protokołów uwierzytelniania, takich jak OAuth2 i Kerberos. Te protokoły zostały rozbudowane i mogą być wdrażane w ramach bibliotek platformy na potrzeby uwierzytelniania i autoryzacji.

## <a name="use-threat-modeling-during-application-design"></a>Korzystanie z modelowania zagrożeń podczas projektowania aplikacji
[Cykl rozwoju zabezpieczeń](https://www.microsoft.com/en-us/sdl) firmy Microsoft określa, że zespoły powinny być zaangażowane w proces nazywany modelem zagrożeń podczas fazy projektowania. Aby ułatwić ten proces, firma Microsoft stworzyła [Threat Modeling Tool SDL](../develop/threat-modeling-tool.md). Modelowanie projektu aplikacji i wyliczanie zagrożeń [krokowych](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) we wszystkich granicach zaufania może przechwycić błędy projektu wczesnie.

Poniższa tabela zawiera listę zagrożeń związanych z KROKami i zawiera przykładowe środki zaradcze, które korzystają z funkcji platformy Azure. Te środki zaradcze nie będą działały w każdej sytuacji.

| Zagrożenie | Właściwość zabezpieczeń | Potencjalne ograniczenia dotyczące platformy Azure |
| --- | --- | --- |
| Fałszowanie | Uwierzytelnianie | Wymagaj połączeń HTTPS. |
| Manipulowanie | Integralność | Sprawdź poprawność certyfikatów TLS/SSL. |
| Wypieranie się | Weryfikacja tożsamości | Włącz [monitorowanie i diagnostykę](/azure/architecture/best-practices/monitoring)platformy Azure. |
| Ujawnienie informacji | Poufność | Szyfruj poufne dane przy użyciu [certyfikatów usługi](/rest/api/appservice/certificates). |
| Odmowa usługi | Dostępność | Monitoruj metryki wydajności dla potencjalnych warunków odmowy usług. Implementuj filtry połączeń. |
| Podniesienie uprawnień | Autoryzacja | Użyj [Privileged Identity Management](../../active-directory/privileged-identity-management/subscription-requirements.md). |

## <a name="develop-on-azure-app-service"></a>Opracowywanie na Azure App Service
[Azure App Service](../../app-service/overview.md) to oferta PaaS, która umożliwia tworzenie aplikacji sieci Web i mobilnych dla dowolnej platformy lub urządzenia oraz łączenie się z danymi gdziekolwiek w chmurze lub lokalnie. App Service obejmuje możliwości sieci Web i urządzeń przenośnych, które zostały wcześniej dostarczone osobno jako usługi Azure Websites i Azure Mobile Services. Obejmuje ona także nowe funkcje automatyzacji procesów biznesowych i hostowania interfejsów API w chmurze. Jako pojedyncza zintegrowana usługa App Service oferuje bogaty zestaw funkcji dla scenariuszy sieci Web, mobilnych i integracji.

Poniżej przedstawiono najlepsze rozwiązania dotyczące korzystania z App Service.

**Najlepsze rozwiązanie**: [uwierzytelnianie za Azure Active Directory](../../app-service/overview-authentication-authorization.md).   
**Szczegóły**: App Service udostępnia usługę OAuth 2,0 dla dostawcy tożsamości. Uwierzytelnianie OAuth 2,0 koncentruje się na prostotie deweloperów klienta, a jednocześnie udostępnia określone przepływy autoryzacji dla aplikacji sieci Web, aplikacji klasycznych i telefonów komórkowych. Usługa Azure AD korzysta z protokołu OAuth 2,0, aby umożliwić autoryzowanie dostępu do aplikacji mobilnych i sieci Web.

**Najlepsze rozwiązanie**: ograniczanie dostępu na podstawie potrzeb znajomości zasad zabezpieczeń i ich najniższych uprawnień.   
**Szczegóły**: ograniczanie dostępu jest konieczne dla organizacji, które chcą wymusić zasady zabezpieczeń dostępu do danych. Możesz użyć kontroli RBAC platformy Azure do przypisywania uprawnień użytkownikom, grupom i aplikacjom w określonym zakresie. Aby dowiedzieć się więcej o udzielaniu użytkownikom dostępu do aplikacji, zobacz Wprowadzenie do [zarządzania dostępem](../../role-based-access-control/overview.md).

**Najlepsze rozwiązanie**: Ochrona kluczy.   
**Szczegóły**: Azure Key Vault pomaga chronić klucze kryptograficzne i wpisy tajne używane przez aplikacje i usługi w chmurze. Za pomocą Key Vault można szyfrować klucze i wpisy tajne (takie jak klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych). Pliki i hasła PFX) przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń (sprzętowych modułów zabezpieczeń). W celu zapewnienia dodatkowego bezpieczeństwa możesz zaimportować lub wygenerować klucze w modułach HSM. Aby dowiedzieć się więcej, zobacz [Azure Key Vault](../../key-vault/general/overview.md) . Za pomocą Key Vault można także zarządzać certyfikatami TLS przy użyciu autoodnawiania.

**Najlepsze rozwiązanie**: ograniczanie przychodzących źródłowych adresów IP.   
**Szczegóły**: [App Service Environment](../../app-service/environment/intro.md) ma funkcję integracji sieci wirtualnej, która pomaga ograniczyć przychodzące źródłowe adresy IP za pomocą sieciowych grup zabezpieczeń. Sieci wirtualne umożliwiają umieszczanie zasobów platformy Azure w nieinternetowej, rutowanej sieci, do której kontroluje się dostęp. Aby dowiedzieć się więcej, zobacz [Integrowanie aplikacji z siecią wirtualną platformy Azure](../../app-service/web-sites-integrate-with-vnet.md).

**Najlepsze rozwiązanie**: monitorowanie stanu zabezpieczeń środowisk App Serviceych.   
**Szczegóły**: Aby monitorować środowiska App Service, użyj Azure Security Center. Gdy Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy [zalecenia](../../security-center/asset-inventory.md) , które przeprowadzą Cię przez proces konfigurowania wymaganych kontrolek.

> [!NOTE]
> App Service monitorowania jest w wersji zapoznawczej i jest dostępna tylko w [warstwie standardowa](../../security-center/security-center-pricing.md) Security Center.
>
>

## <a name="install-a-web-application-firewall"></a>Zainstaluj zaporę aplikacji sieci Web
Aplikacje internetowe coraz częściej stają się obiektami złośliwych ataków wykorzystujących znane luki w zabezpieczeniach. Wśród nich często zdarzają się np. ataki polegające na iniekcji SQL i ataki z użyciem skryptów wykorzystywanych w wielu witrynach. Zapobieganie takim atakom z poziomu kodu aplikacji może być trudne. Może też wymagać rygorystycznego przestrzegania harmonogramu konserwacji, poprawek i monitorowania na wielu warstwach topologii aplikacji. Scentralizowana zapora aplikacji internetowej ułatwia zarządzanie zabezpieczeniami oraz zapewnia lepszą ochronę administratorów aplikacji przed zagrożeniami i intruzami. Zapora aplikacji internetowej może reagować na zagrożenia bezpieczeństwa szybciej — poprzez wdrażanie poprawek zapobiegających wykorzystaniu znanych luk w zabezpieczeniach w centralnej lokalizacji zamiast w poszczególnych aplikacjach internetowych. Istniejące bramy Application Gateway można łatwo przekonwertować na bramę Application Gateway obsługującą zaporę aplikacji internetowej.

[Zapora aplikacji sieci Web (WAF)](../../web-application-firewall/afds/afds-overview.md) to funkcja Application Gateway, która zapewnia scentralizowaną ochronę aplikacji sieci Web przed typowymi atakami i lukami w zabezpieczeniach. WAF opiera się na regułach z [zestawu reguł programu Open Web Security Project (OWASP) Core](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) , 3,0 lub 2.2.9.

## <a name="monitor-the-performance-of-your-applications"></a>Monitorowanie wydajności aplikacji
Monitorowanie to czynność zbierania i analizowania danych w celu określenia wydajności, kondycji i dostępności aplikacji. Skuteczna strategia monitorowania pomaga w zrozumieniu szczegółów działania składników aplikacji. Ułatwia to zwiększenie czasu pracy przez powiadomienie o krytycznych problemach, dzięki czemu można je rozwiązać, zanim staną się problemy. Pomaga również wykrywać anomalie, które mogą być powiązane z zabezpieczeniami.

Użyj [usługi Azure Application Insights](https://azure.microsoft.com/documentation/services/application-insights) , aby monitorować dostępność, wydajność i użycie aplikacji, niezależnie od tego, czy jest ona hostowana w chmurze, czy lokalnie. Za pomocą Application Insights można szybko identyfikować i diagnozować błędy w aplikacji bez czekania, aż użytkownik zgłosi je. Dzięki zbieranym informacjom można podejmować świadome decyzje w zakresie konserwacji i ulepszeń aplikacji.

Usługa Application Insights ma szeroką gamę narzędzi do interakcji z danymi, które zbiera. Usługa Application Insights przechowuje dane we wspólnym repozytorium. Korzystanie z funkcji udostępnionych, takich jak alerty, pulpity nawigacyjne i szczegółowa analiza, może być możliwe przy użyciu języka zapytań Kusto.

## <a name="perform-security-penetration-testing"></a>Przeprowadź testowanie penetracji zabezpieczeń
Sprawdzanie poprawności zabezpieczeń ochrony jest tak ważne jak testowanie wszelkich innych funkcji. [Przetestowanie penetracji](pen-testing.md) standardowej części procesu kompilowania i wdrażania. Zaplanuj regularne testy zabezpieczeń i skanowania w poszukiwaniu wdrożonych aplikacji oraz monitoruj otwarte porty, punkty końcowe i ataki.

Testowanie rozmyte to metoda odnajdywania awarii programu (błędy kodu) przez dostarczanie nieprawidłowo sformułowanych danych wejściowych do interfejsów programu (punktów wejścia), które analizują i zużywają te dane. [Wykrywanie zagrożeń zabezpieczeń firmy Microsoft](https://www.microsoft.com/en-us/security-risk-detection/) jest narzędziem opartym na chmurze, które służy do wyszukiwania usterek i innych luk w zabezpieczeniach oprogramowania przed wdrożeniem go na platformie Azure. Narzędzie jest przeznaczone do przechwytywania luk w zabezpieczeniach przed wdrożeniem oprogramowania, dzięki czemu nie trzeba poprawiać usterki, rozwiązywać awarii ani reagować na ataki po wydaniu oprogramowania.


## <a name="next-steps"></a>Następne kroki
W tym artykule koncentrujemy się na zaletach zabezpieczeń wdrożenia usługi Azure PaaS i najlepszych rozwiązaniach dotyczących zabezpieczeń dla aplikacji w chmurze. Następnie zapoznaj się z zalecanymi rozwiązaniami dotyczącymi zabezpieczania rozwiązań sieci Web i urządzeń przenośnych PaaS przy użyciu określonych usług platformy Azure. Zaczniemy od Azure App Service, Azure SQL Database i usługi Azure Synapse Analytics oraz usługi Azure Storage. Ponieważ artykuły dotyczące zalecanych praktyk dla innych usług platformy Azure stają się dostępne, linki zostaną podane na poniższej liście:

- [Azure App Service](paas-applications-using-app-services.md)
- [Azure SQL Database i analiza Synapse Azure](paas-applications-using-sql.md)
- [Azure Storage](paas-applications-using-storage.md)
- Azure Cache for Redis
- Usługa Azure Service Bus
- Zapory aplikacji sieci Web

Zapoznaj się z tematem [opracowywanie bezpiecznych aplikacji na platformie Azure](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/) , aby poznać pytania zabezpieczające i kontrolki, które należy wziąć pod uwagę w każdej fazie cyklu projektowania oprogramowania podczas tworzenia aplikacji dla chmury.

Zobacz [najlepsze rozwiązania i wzorce dotyczące zabezpieczeń platformy Azure](best-practices-and-patterns.md) , aby uzyskać więcej najlepszych rozwiązań w zakresie zabezpieczeń, które są używane podczas projektowania i wdrażania rozwiązań w chmurze oraz zarządzania nimi przy użyciu platformy Azure.

Dostępne są następujące zasoby umożliwiające dostarczenie bardziej ogólnych informacji na temat zabezpieczeń platformy Azure i powiązanych usług firmy Microsoft:
* [Blog zespołu ds. zabezpieczeń platformy Azure](/archive/blogs/azuresecurity/) — na bieżąco z najnowszymi informacjami na temat zabezpieczeń platformy Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) — w przypadku których luki w zabezpieczeniach firmy Microsoft, w tym problemy z platformą Azure, mogą być zgłaszane lub wysyłane pocztą e-mail secure@microsoft.com