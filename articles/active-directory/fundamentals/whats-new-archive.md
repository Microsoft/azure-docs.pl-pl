---
title: Co nowego w Azure Active Directory? | Microsoft Docs
description: Informacje o nowościach wersji w sekcji przegląd tego zestawu zawartości zawierają 6 miesięcy aktywności. Po 6 miesiącach elementy zostaną usunięte z głównego artykułu i umieszczone w tym artykule archiwum.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1ef7d587cd8bec636f42673941edb69647ee8cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449997"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Co nowego w Azure Active Directory?

Podstawowe informacje o [nowościach w Azure Active Directory?](whats-new.md) artykuł dotyczący wersji zawiera aktualizacje dla ostatnich sześciu miesięcy, a ten artykuł zawiera wszystkie starsze dane.

Co nowego w Azure Active Directory? Informacje o wersji zawierają:

- Najnowsze wersje
- Znane problemy
- Poprawki błędów
- Funkcje uznane za przestarzałe
- Plany zmian

---

 ## <a name="march-2020"></a>Marzec 2020 r.

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march--2021"></a>Niezarządzane konta Azure Active Directory w aktualizacji B2B dla marca 2021

**Typ:** Planowanie zmiany  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C
 
**Począwszy od 31 marca 2021**, firma Microsoft nie będzie już obsługiwać wykupu zaproszeń przez tworzenie niezarządzanych kont Azure Active Directory (Azure AD) i dzierżawców na potrzeby scenariuszy współpracy B2B. Zachęcamy do tego, abyśmy mogli korzystać z [uwierzytelniania jednorazowego kodu dostępu za pośrednictwem poczty e-mail](../external-identities/one-time-passcode.md).

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Użytkownicy z domyślną rolą dostępu będą znajdować się w zakresie aprowizacji

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
W przeszłości użytkownicy z domyślną rolą dostępu znajdują się poza zakresem aprowizacji. Otrzymaliśmy Opinie, dla których klienci chcą, aby użytkownicy z tą rolą mieli zasięg w zakresie aprowizacji. Pracujemy nad wdrożeniem zmiany, dzięki czemu wszystkie nowe konfiguracje aprowizacji będą zezwalały użytkownikom z domyślną rolą dostępu. Stopniowo zmieniamy zachowanie istniejących konfiguracji aprowizacji, aby obsługiwać użytkowników aprowizacji z tą rolą. Nie jest wymagane wykonanie jakiejkolwiek czynności przez klienta. Po wprowadzeniu tej zmiany wyślemy aktualizację do naszej [dokumentacji](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md) .

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Współpraca B2B w usłudze Azure AD będzie dostępna w Microsoft Azure obsługiwanych przez firmę 21Vianet (Azure Chiny 21Vianet)

**Typ:** Planowanie zmiany  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C
 
Możliwości współpracy B2B usługi Azure AD będą dostępne w Microsoft Azure obsługiwanych przez firmę 21Vianet (platformy Azure z Chin), umożliwiając użytkownikom w Chinach firmy 21Vianet współdziałanie z użytkownikami w innych dzierżawach usługi Azure Chiny. [Dowiedz się więcej o współpracy B2B usługi Azure AD](/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Przeprojektowana wiadomość e-mail z zaproszeniem do współpracy B2B usługi Azure AD

**Typ:** Planowanie zmiany  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C
 
[Wiadomości e-mail](../external-identities/invitation-email-elements.md) wysyłane przez usługę zaproszeń do współpracy B2B usługi Azure AD w celu zapraszania użytkowników do katalogu zostaną przeprojektowane w celu uzyskania informacji o zaproszeniach i kolejnych krokach użytkownika.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Zmiany zasad HomeRealmDiscovery będą widoczne w dziennikach inspekcji

**Typ:** FIXED  
**Kategoria usługi:** Wizyjn  
**Możliwość produktu:** Monitorowanie & raportowania
 
Rozwiązano problem polegający na tym, że zmiany [zasad HomeRealmDiscovery](../manage-apps/configure-authentication-for-federated-users-portal.md) nie zostały uwzględnione w dziennikach inspekcji. Teraz będzie można zobaczyć, kiedy i w jaki sposób zasady zostały zmienione. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — marzec 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W marcu 2020 dodaliśmy do galerii aplikacji 51 te nowe aplikacje z obsługą Federacji: 

[Cisco AnyConnect](../saas-apps/cisco-anyconnect.md), [Zoho jeden Chiny](../saas-apps/zoho-one-china-tutorial.md), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co aplikacji SAML](../saas-apps/profitco-saml-app-tutorial.md) [iPoint Service Provider](../saas-apps/ipoint-service-provider-tutorial.md), [Node Insight](https://admin.nodeinsight.com/AADLogin.aspx) [Campus Café](../saas-apps/campus-cafe-tutorial.md) [contexxt.AI Sphere](https://contexxt-sphere.com/login), [mądry przez Invictus](../saas-apps/wisdom-by-invictus-tutorial.md), [Flara cyfrowa](https://spark-dev.pixelnebula.com/login),, wgląd [w chmurę dla inżynierów](../saas-apps/logzio-cloud-observability-for-engineers-tutorial.md), [Logz.io](../saas-apps/spectrumu-tutorial.md), [SpectrumU](https://bizzcontact.app/), [BizzContact rejestracji jednokrotnej](../saas-apps/elqano-sso-tutorial.md), [Elqano](http://www.signshare.com/), [MarketSignShare Learning Suite](../saas-apps/crossknowledge-learning-suite-tutorial.md),, CrossKnowledge, COMPAS [Hub](../saas-apps/fcm-hub-tutorial.md) [,](../saas-apps/lift-tutorial.md) [żebra](../saas-apps/planview-enterprise-one-tutorial.md) [A/S FCM Mobile, Byggeweb](https://apps.apple.com/us/app/docia/id529058757) [,](https://demo.asterapp.io/login) [GoLinks](../saas-apps/golinks-tutorial.md), [usługi Datadog](../saas-apps/datadog-tutorial.md)Planview Enterprise one [platforma](../saas-apps/zscaler-b2b-user-portal-tutorial.md) [IP platform,](../saas-apps/ip-platform-tutorial.md)InVision [,](../saas-apps/invision-tutorial.md)usługi PIPEDRIVE [,](../saas-apps/pipedrive-tutorial.md)Zaprezentuj [warsztat,](https://app.showcaseworkshop.com/)Greenlight [Integration platform,](../saas-apps/greenlight-integration-platform-tutorial.md)Greenlight [zgodnego dostępu,](../saas-apps/greenlight-compliant-access-management-tutorial.md)Grok [uczenie](../saas-apps/grok-learning-tutorial.md), Miradore [online,](https://login.online.miradore.com/)Khoros [opieki,](../saas-apps/khoros-care-tutorial.md)AskYourTeam [,](../saas-apps/askyourteam-tutorial.md)TruNarrative [,](../saas-apps/trunarrative-tutorial.md)Smartwaiver [,](https://www.smartwaiver.com/m/user/sw_login.php?wms_login)Bizagi [Studio for Digital Process Automation,](../saas-apps/bizagi-studio-for-digital-process-automation-tutorial.md)insuiteX [,](https://www.insuite.jp/)Sybo [,](https://www.systexsoftware.com.tw/)Britive [,](../saas-apps/britive-tutorial.md)WhosOffice [,](../saas-apps/whosoffice-tutorial.md)E [-dni,](../saas-apps/e-days-tutorial.md)Kollective [SDN,](https://portal.kollective.app/login) [WatchTeams](https://www.devfinition.com/)Witivio [,](https://app.witivio.com/) [PlayVox](../saas-apps/code42-tutorial.md) [,](https://my.playvox.com/login)360 [Netvision Compas](../saas-apps/netvision-compas-tutorial.md) [Skills Workflow](../saas-apps/skills-workflow-tutorial.md) [Korn Ferry 360](../saas-apps/korn-ferry-360-tutorial.md) [Catchpoint](../saas-apps/catchpoint-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Współpraca B2B w usłudze Azure AD jest dostępna w dzierżawach Azure Government

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C
 
Funkcje współpracy B2B usługi Azure AD są teraz dostępne między niektórymi dzierżawami Azure Government.  Aby dowiedzieć się, czy Dzierżawca może korzystać z tych możliwości, postępuj zgodnie z instrukcjami w temacie [Jak mogę sprawdzić, czy w mojej dzierżawie platformy Azure USA jest dostępna współpraca B2B?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure Monitor integracja z usługą Azure Logs jest teraz dostępna w Azure Government

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania
 
Azure Monitor integracja z dziennikami usługi Azure AD jest teraz dostępna w programie Azure Government. Dzienniki usługi Azure AD (inspekcja i dzienniki logowania) można kierować do konta magazynu, centrum zdarzeń i Log Analytics. Zapoznaj się ze [szczegółową dokumentacją](https://aka.ms/aadlogsinamd) , [a także planami wdrażania raportowania i monitorowania](../reports-monitoring/plan-monitoring-and-reporting.md) scenariuszy usługi Azure AD.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Odświeżanie programu Identity Protection w Azure Government

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Przyjemnością się z tym, że udostępnimy teraz odświeżone środowisko [Azure AD Identity Protection](https://aka.ms/IdentityProtectionDocs)   w [portalu Microsoft Azure Government](https://portal.azure.us/). Aby uzyskać więcej informacji, zobacz [wpis w blogu dotyczący anonsu](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Odzyskiwanie po awarii: pobieranie i przechowywanie konfiguracji aprowizacji

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
Usługa Azure AD Provisioning oferuje bogaty zestaw funkcji konfiguracji. Klienci muszą mieć możliwość zapisania swojej konfiguracji, aby mogły odwoływać się do niej później lub przywracać do znanej dobrej wersji. Dodaliśmy możliwość pobrania konfiguracji aprowizacji jako pliku JSON i przekazania jej w razie potrzeby. [Dowiedz się więcej](../app-provisioning/export-import-provisioning-configuration.md).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (Samoobsługowe resetowanie hasła) teraz wymaga dwóch bram dla administratorów Microsoft Azure obsługiwanych przez firmę 21Vianet (Azure Chiny 21Vianet) 

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Resetowanie hasła Self-Service  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Wcześniej w Microsoft Azure obsługiwane przez firmę 21Vianet (Azure Chiny 21Vianet) Administratorzy korzystający z funkcji samoobsługowego resetowania hasła (SSPR) do resetowania własnych haseł potrzebują tylko jednego "bramy" (wyzwanie), aby potwierdzić swoją tożsamość. W publicznych i innych chmurach narodowych Administratorzy zwykle muszą użyć dwóch bram, aby potwierdzić swoją tożsamość podczas korzystania z usługi SSPR. Ale ponieważ nie obsługujemy wiadomości SMS lub połączeń telefonicznych na platformie 21Vianet firmy Microsoft, zezwalamy na Resetowanie hasła przez administratorów.

Tworzymy SSPR funkcji między platformą Azure Chiny 21Vianet a chmurą publiczną. W przyszłości Administratorzy muszą używać dwóch bram podczas korzystania z usługi SSPR. Obsługa wiadomości SMS, połączeń telefonicznych i powiadomień aplikacji uwierzytelniania oraz kodów. [Dowiedz się więcej](../authentication/concept-sspr-policy.md#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>Długość hasła jest ograniczona do 256 znaków

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Aby zapewnić niezawodność usługi Azure AD, hasła użytkowników mają teraz ograniczoną długość do 256 znaków. Użytkownicy z hasłami dłużej niż zostanie wyświetlony monit o zmianę hasła podczas kolejnego logowania przez skontaktowanie się z administratorem lub funkcją samoobsługowego resetowania hasła.

Ta zmiana została włączona 13 marca 2020, at od 10:00 PST (18:00 UTC), a błąd to AADSTS 50052, InvalidPasswordExceedsMaxLength. Aby uzyskać więcej informacji, zobacz [powiadomienie o zmianie](../develop/reference-breaking-changes.md#user-passwords-will-be-restricted-to-256-characters) .

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Dzienniki logowania usługi Azure AD są teraz dostępne dla wszystkich bezpłatnych dzierżawców za pomocą Azure Portal

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania
 
Od tej pory Klienci, którzy mają bezpłatnych dzierżawców, mogą uzyskać dostęp do [dzienników logowania usługi Azure AD z Azure Portal](../reports-monitoring/concept-sign-ins.md) przez maksymalnie 7 dni. Wcześniej dzienniki logowania były dostępne tylko dla klientów z licencjami na Azure Active Directory — wersja Premium. W przypadku tej zmiany wszyscy dzierżawcy będą mogli uzyskiwać dostęp do tych dzienników za pomocą portalu.

> [!NOTE]
> Klienci nadal potrzebują licencji Premium (Azure Active Directory — wersja Premium P1 lub P2), aby uzyskać dostęp do dzienników logowania za pomocą Microsoft Graph API i Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Wycofanie opcji grup obejmujących cały katalog z ustawień ogólnych grup na Azure Portal

**Typ:** Przestarzałe  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności

Aby zapewnić klientom bardziej elastyczny sposób tworzenia grup obejmujących katalogi, które najlepiej odpowiadają potrzebom, zamienisz opcję **grupy w poziomie katalogów** z **Groups**  >  ustawień**ogólnych** grupy w Azure Portal z linkiem do [dokumentacji grupy dynamicznej](../users-groups-roles/groups-dynamic-membership.md). Ulepszono nasze dokumenty w celu uwzględnienia dodatkowych instrukcji, aby administratorzy mogli tworzyć wszystkie grupy użytkowników, które obejmują lub wykluczają użytkowników-Gości.

---

## <a name="february-2020"></a>Luty 2020 r.

### <a name="upcoming-changes-to-custom-controls"></a>Nadchodzące zmiany w kontrolkach niestandardowych

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Funkcja  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Planujemy zamienić bieżące kontrolki niestandardowe w wersji zapoznawczej z podejściem umożliwiającym bezproblemowe współdziałanie funkcji uwierzytelniania udostępnianych przez partnera z uprawnieniami administratora Azure Active Directory i użytkownikami końcowymi. Obecnie rozwiązania MFA partnerów podlegają następującym ograniczeniom: działają tylko po wprowadzeniu hasła; nie służą one jako uwierzytelnianie wieloskładnikowe na potrzeby uwierzytelniania krok po kroku w innych kluczowych scenariuszach. nie integrują się z funkcjami zarządzania poświadczeniami użytkownika końcowego ani administratora. Nowa implementacja umożliwi współpracujące ze sobą współczynniki uwierzytelniania udostępniane przez partnera, które obejmują kluczowe scenariusze, w tym rejestrację, użycie, oświadczenia usługi MFA, krokowe uwierzytelnianie, raportowanie i rejestrowanie. 

Niestandardowe kontrolki będą nadal obsługiwane w wersji zapoznawczej wraz z nowym projektem do momentu osiągnięcia ogólnej dostępności. W tym momencie przekażemy klientom czas na przeprowadzenie migracji do nowego projektu. Ze względu na ograniczenia bieżącego podejścia nie będziemy dołączać nowych dostawców do momentu udostępnienia nowego projektu. Pracujemy ściśle z klientami i dostawcami, co pozwoli na bliższe przekazanie osi czasu. [Dowiedz się więcej](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Bezpieczna ocena tożsamości — aktualizacje akcji poprawy usługi MFA

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Funkcja  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Aby odzwierciedlić potrzebę firm, aby zapewnić najwyższy poziom bezpieczeństwa podczas stosowania zasad, które współpracują z firmą, firma Microsoft Security Score usuwa trzy akcje ulepszania w centrum uwierzytelniania wieloskładnikowego (MFA) i dodaje dwa.

Następujące akcje poprawy zostaną usunięte:

- Zarejestruj wszystkich użytkowników usługi MFA
- Wymaganie uwierzytelniania wieloskładnikowego dla wszystkich użytkowników
- Wymagaj uwierzytelniania wieloskładnikowego dla ról uprzywilejowanych usługi Azure AD

Zostaną dodane następujące akcje ulepszania:

- Upewnij się, że wszyscy użytkownicy mogą ukończyć uwierzytelnianie wieloskładnikowe w celu zapewnienia bezpiecznego dostępu
- Wymagaj uwierzytelniania wieloskładnikowego dla ról administracyjnych

Te nowe akcje poprawy będą wymagały zarejestrowania użytkowników lub administratorów usługi MFA w katalogu i ustanowienia odpowiedniego zestawu zasad pasujących do potrzeb organizacji. Głównym celem jest zapewnienie elastyczności, dzięki czemu wszyscy użytkownicy i Administratorzy mogą uwierzytelniać się przy użyciu wielu czynników lub z zapytaniem do weryfikacji tożsamości opartej na ryzyku. Może to mieć formę ustawień domyślnych zabezpieczeń, które umożliwiają firmie Microsoft decydowanie o tym, kiedy należy zadawać użytkownikom uwierzytelnianie MFA, lub mając wiele zasad, które stosują decyzje o zakresie. W ramach tych aktualizacji akcji poprawy zasady ochrony linii bazowej nie będą już uwzględniane w obliczeniach oceniania. [Przeczytaj więcej na temat tego, co jest dostępne w usłudze Microsoft Secure Score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD Domain Services wybór jednostki SKU

**Typ:** Nowa funkcja  
**Kategoria usługi:** Azure AD Domain Services  
**Możliwość produktu:** Azure AD Domain Services
 
Otrzymaliśmy Opinie, które Azure AD Domain Services klienci chcą zwiększyć elastyczność w wyborze poziomów wydajności dla ich wystąpień. Od 1 lutego 2020 przejdziemy na model dynamiczny (gdzie usługa Azure AD określa wydajność i warstwę cenową na podstawie liczby obiektów) do modelu samodzielnego wyboru. Teraz klienci mogą wybrać warstwę wydajności, która odpowiada ich środowisku. Ta zmiana pozwala również na włączenie nowych scenariuszy, takich jak lasy zasobów, oraz funkcji premium, takich jak codzienne kopie zapasowe. Liczba obiektów jest teraz nieograniczona dla wszystkich jednostek SKU, ale będziemy nadal oferować sugestie dotyczące liczby obiektów dla każdej warstwy.

**Nie jest wymagana żadna bezpośrednia akcja klienta.** W przypadku istniejących klientów warstwa dynamiczna używana w dniu 1 lutego 2020 określa nową warstwę domyślną. W wyniku tej zmiany nie ma wpływu na cenę ani wpływ na wydajność. W przyszłości klienci korzystający z platformy Azure AD DS będą musieli oszacować wymagania dotyczące wydajności, ponieważ zmienią się ich rozmiary katalogów i charakterystyki obciążeń. Przełączenie między warstwami usług będzie nadal trwać bez przestojów i nie będzie już automatycznie przenosić klientów do nowych warstw na podstawie wzrostu ich katalogu. Ponadto nie będzie można zwiększyć cen, a nowe ceny będą wyrównane z naszym bieżącym modelem rozliczeń. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure AD DS SKU](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) i [stronę cennika Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — luty 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W lutym 2020 dodaliśmy te 31 nowych aplikacji z obsługą Federacji do galerii aplikacji: 

[IamIP platformę patentową](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [chmurę doświadczenia](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [ns1 Logowanie jednokrotne na platformie Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [usługę zabezpieczeń poczty e-mail Barracuda](https://ess.barracudanetworks.com/sso/azure), [raportowanie ABA](https://myaba.co.uk/client-access/signin/auth/msad), [na wypadek kryzysu — portal online](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), [BIC projektowanie chmurowe](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), program [pszczelarstwa usługi Azure AD Data Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), [oceny promów](https://www.kornferry.com/solutions/kf-digital/kf-assess), [Verkada](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [EAB](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), [New Relic (Limited Release)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [Thulium](https://admin.thulium.com/login/instance), [kierownik biletów](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), selektor [szablonów dla zespołów](https://links.officeatwork.com/templatechooser-download-teams), [pszczoły](https://www.beesy.me/index.php/site/login), [system pomocy technicznej](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [mural](https://app.mural.co/signup), [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), LavaDo [,](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial) [Wakelet](https://wakelet.com/login), [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [ThingLink dla nauczycieli i szkół](https://www.thinglink.com/), [Coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product) [, WeDo,](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview) [InvitePeople,](https://invitepeople.com/login) [reprinters Desk, artykuł Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nowe łączniki aprowizacji w galerii aplikacji usługi Azure AD — luty 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzacja aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Obsługa kluczy zabezpieczeń usługi Azure AD dla FIDO2 w środowiskach hybrydowych

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Ogłaszamy publiczną wersję zapoznawczą obsługi kluczy zabezpieczeń usługi Azure AD dla FIDO2 w środowiskach hybrydowych. Użytkownicy mogą teraz używać kluczy zabezpieczeń FIDO2 do logowania się do swoich urządzeń z systemem Windows 10 hybrydowej usługi Azure AD i zapewnienia bezproblemowego logowania do zasobów lokalnych i w chmurze. Obsługa środowisk hybrydowych była najczęściej żądaną funkcją od naszych klientów, od momentu pierwszego uruchomienia publicznej wersji zapoznawczej obsługi FIDO2 na urządzeniach dołączonych do usługi Azure AD. Uwierzytelnianie bez hasła przy użyciu zaawansowanych technologii, takich jak biometry oraz Kryptografia klucza publicznego/prywatnego, zapewnia wygodę i łatwość używania, gdy jest to bezpieczne. Korzystając z tej publicznej wersji zapoznawczej, można teraz używać nowoczesnego uwierzytelniania, takiego jak FIDO2 klucze zabezpieczeń, aby uzyskiwać dostęp do tradycyjnych zasobów Active Directory. Aby uzyskać więcej informacji, przejdź do obszaru [Logowanie jednokrotne do zasobów lokalnych](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises). 

Aby rozpocząć, odwiedź stronę [Włączanie kluczy zabezpieczeń FIDO2 dla Twojej dzierżawy](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) , aby uzyskać instrukcje krok po kroku. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>Nowe środowisko moje konto jest teraz ogólnie dostępne

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Mój profil/konto  
**Możliwość produktu:** Środowiska użytkownika końcowego
 
Moje konto — ten, który zatrzymał się w przypadku wszystkich potrzeb związanych z zarządzaniem kontem użytkownika końcowego, jest teraz ogólnie dostępny. Użytkownicy końcowi mogą uzyskiwać dostęp do tej nowej witryny za pośrednictwem adresu URL lub w nagłówku nowego środowiska moje aplikacje. Dowiedz się więcej na temat wszystkich funkcji samoobsługowych oferowanych przez nowe środowisko w [portalu mojego konta](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Adres URL witryny My Account do aktualizowania na myaccount.microsoft.com

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Mój profil/konto  
**Możliwość produktu:** Środowiska użytkownika końcowego
 
Nowe środowisko użytkownika końcowego moje konto będzie aktualizować jego adres URL `https://myaccount.microsoft.com` w ciągu następnego miesiąca. Znajdź więcej informacji na temat środowiska oraz wszystkie możliwości samoobsługowego konta, które oferuje użytkownikom końcowym w [pomocy portalu mojego konta](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---

## <a name="january-2020"></a>Styczeń 2020 r.
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Nowy portal moje aplikacje jest teraz ogólnie dostępny

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** Środowiska użytkownika końcowego
 
Uaktualnij swoją organizację do nowego portalu Moje aplikacje, który jest teraz ogólnie dostępny! Więcej informacji na temat nowego portalu i kolekcji znajduje się w [sekcji Tworzenie kolekcji w portalu Moje aplikacje](../manage-apps/access-panel-collections.md).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Nazwy obszarów roboczych w usłudze Azure AD zostały zmienione na Kolekcje

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Moje aplikacje   
**Możliwość produktu:** Środowiska użytkownika końcowego
 
Obszary robocze, Administratorzy filtrów mogą konfigurować, aby organizować aplikacje użytkowników, będą teraz określane jako kolekcje. Więcej informacji na temat sposobu ich konfigurowania można znaleźć w [sekcji Tworzenie kolekcji w portalu Moje aplikacje](../manage-apps/access-panel-collections.md).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C Rejestracja i logowanie przy użyciu zasad niestandardowych (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C
 
Dzięki rejestrowaniu i rejestrowaniu numerów telefonów deweloperzy i przedsiębiorstwa mogą umożliwić klientom rejestrowanie się i logowanie przy użyciu hasła jednorazowego wysyłanego do numeru telefonu użytkownika za pośrednictwem wiadomości SMS. Ta funkcja umożliwia także zmianę numeru telefonu przez klienta w przypadku utraty dostępu do telefonu. Dzięki możliwości stosowania zasad niestandardowych i rejestrowania i logowania na telefon, deweloperzy i przedsiębiorstwa mogą komunikować swoją marką przez dostosowanie strony. Dowiedz się [, jak skonfigurować rejestrowanie i logowanie za pomocą zasad niestandardowych w Azure AD B2C](../../active-directory-b2c/phone-authentication.md).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Nowe łączniki aprowizacji w galerii aplikacji usługi Azure AD — styczeń 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

- [Promapp]( ../saas-apps/promapp-provisioning-tutorial.md)
- [Zscaler Private Access](../saas-apps/zscaler-private-access-provisioning-tutorial.md)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzacja aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](../app-provisioning/user-provisioning.md).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — styczeń 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W styczniu 2020 dodaliśmy do galerii aplikacji 33 te nowe aplikacje z obsługą Federacji: 

[JOSA](../saas-apps/josa-tutorial.md), [szybka granica w chmurze](../saas-apps/fastly-edge-cloud-tutorial.md), [Terraform Enterprise](../saas-apps/terraform-enterprise-tutorial.md), [Spintr SSO](../saas-apps/spintr-sso-tutorial.md), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [odstrzelone](../saas-apps/upshotly-tutorial.md), [LeaveBot](https://leavebot.io/#home), [datacamp](../saas-apps/datacamp-tutorial.md), [TripActions](../saas-apps/tripactions-tutorial.md), [SmartWork](https://www.intumit.com/english/SmartWork.html) [EZRentOut](../saas-apps/ezrentout-tutorial.md), [dotcom-monitor](../saas-apps/dotcom-monitor-tutorial.md), [SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla Oracle E-Business Suite — EBS, PeopleSoft i JDE](../saas-apps/ssogen-tutorial.md), [Hostowana usługa MyCirqa SSO](../saas-apps/hosted-mycirqa-sso-tutorial.md), [yuhu Property Management Platform](../saas-apps/yuhu-property-management-platform-tutorial.md), [LumApps](https://sites.lumapps.com/login), Enterprise-and- [Work](../saas-apps/upwork-enterprise-tutorial.md), [Talentsoft](../saas-apps/talentsoft-tutorial.md), [SmartDB for Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](../saas-apps/presspage-tutorial.md), [ContractSafe SAML2 SSO](../saas-apps/contractsafe-saml2-sso-tutorial.md), [Maxients Manager](../saas-apps/maxient-conduct-manager-software-tutorial.md), [helpshift,](../saas-apps/helpshift-tutorial.md) [PortalTalk 365](https://www.portaltalk.com/) [,](../saas-apps/taskize-connect-tutorial.md) [Przegląd](https://portal.coreview.com/), [squelch Cloud](https://laxmi.squelch.io/login) [, PingFlow,](https://app-staging.pingview.io/) [PrinterLogic](../saas-apps/assetsonar-tutorial.md), [Sandwai](https://app.sandwai.com/) [SaaS, Taskize](../saas-apps/printerlogic-saas-tutorial.md) [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="two-new-identity-protection-detections"></a>Dwa nowe wykrywania ochrony tożsamości

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Dodaliśmy dwa nowe typy wykrywania połączonego logowania do ochrony tożsamości: podejrzane reguły manipulowania skrzynką odbiorczą i niemożliwa podróż. Te wykrywania w trybie offline są wykrywane przez Microsoft Cloud App Security (MCAS) i wpływają na ryzyko związane z logowaniem w usłudze Identity Protection. Aby uzyskać więcej informacji na temat tych wykryć, zobacz [typy ryzyka logowania](../identity-protection/concept-identity-protection-risks.md#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Istotna zmiana: fragmenty identyfikatorów URI nie będą przenoszone przy użyciu przekierowania logowania

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Od 8 lutego 2020, gdy żądanie jest wysyłane do login.microsoftonline.com w celu zalogowania użytkownika, usługa dołączy pusty fragment do żądania.  Pozwala to zapobiec atakom klasy w celu przekierowania przez zagwarantowanie, że przeglądarka wymaże wszystkie istniejące fragmenty w żądaniu. Żadna aplikacja nie powinna mieć zależności dotyczącej tego zachowania. Aby uzyskać więcej informacji, zobacz istotne [zmiany](../develop/reference-breaking-changes.md#february-2020) w dokumentacji platformy tożsamości firmy Microsoft.

---

## <a name="december-2019"></a>Grudzień 2019 r.

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integrowanie aprowizacji SAP SuccessFactors w usłudze Azure AD i lokalnej usłudze AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości

Teraz możesz zintegrować rozwiązanie SAP SuccessFactors jako autorytatywne Źródło tożsamości w usłudze Azure AD. Dzięki tej integracji można zautomatyzować kompleksowy cykl życia tożsamości, w tym używanie wydarzeń opartych na usłudze HR, takich jak nowe zatrudnienie lub zakończenia, w celu kontrolowania aprowizacji kont usługi Azure AD.

Aby uzyskać więcej informacji na temat sposobu konfigurowania inicjowania obsługi przychodzącej SAP SuccessFactors w usłudze Azure AD, zobacz samouczek [Konfigurowanie funkcji automatycznej APROWIZACJI SAP SuccessFactors](https://aka.ms/SAPSuccessFactorsInboundTutorial) .

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Obsługa dostosowanych wiadomości e-mail w Azure AD B2C (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C

Możesz teraz używać Azure AD B2C do tworzenia niestandardowych wiadomości e-mail, gdy użytkownicy logują się do korzystania z aplikacji. Korzystając z usługi DisplayControls (obecnie w wersji zapoznawczej) i dostawcy poczty e-mail innej firmy (na przykład [SendGrid](https://sendgrid.com/), [SparkPost](https://sparkpost.com/)lub niestandardowego interfejsu API REST), możesz użyć własnego szablonu wiadomości e-mail, **adresu i** tekstu tematu, a także do obsługi lokalizacji i niestandardowych ustawień hasła jednorazowego (OTP).

Aby uzyskać więcej informacji, zobacz temat [niestandardowa Weryfikacja poczty e-mail w Azure Active Directory B2C](../../active-directory-b2c/custom-email-sendgrid.md).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Zastąpienie zasad odniesienia przy użyciu domyślnych ustawień zabezpieczeń

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

W ramach modelu bezpiecznego i domyślnego uwierzytelniania usuwamy istniejące zasady ochrony linii bazowej ze wszystkich dzierżawców. To usunięcie jest przeznaczone do ukończenia na koniec lutego. Zastąpienie tych zasad ochrony linii bazowej jest ustawieniami domyślnymi zabezpieczeń. Jeśli używasz zasad ochrony linii bazowej, musisz zaplanować przejście na nowe zasady ustawień zabezpieczeń lub dostęp warunkowy. Jeśli te zasady nie są używane, nie ma żadnych działań do wykonania.

Aby uzyskać więcej informacji na temat nowych domyślnych ustawień zabezpieczeń, zobacz [co to są ustawienia domyślne zabezpieczeń?](./concept-fundamentals-security-defaults.md) Aby uzyskać więcej informacji na temat zasad dostępu warunkowego, zobacz [typowe zasady dostępu warunkowego](../conditional-access/concept-conditional-access-policy-common.md).

---

## <a name="november-2019"></a>Listopad 2019 r.

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Obsługa atrybutu SameSite i programu Chrome 80

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

W ramach modelu bezpiecznego i domyślnego dla plików cookie Przeglądarka Chrome 80 zmienia sposób traktowania plików cookie bez `SameSite` atrybutu. Każdy plik cookie, który nie określa `SameSite` atrybutu będzie traktowany jako, który został ustawiony na `SameSite=Lax` , co spowoduje zablokowanie niektórych scenariuszy udostępniania plików cookie między domenami, od których zależy aplikacja. Aby zachować starsze zachowanie programu Chrome, można użyć `SameSite=None` atrybutu i dodać dodatkowy `Secure` atrybut, dzięki czemu dostęp do plików cookie między lokacjami można uzyskać tylko za pośrednictwem połączeń HTTPS. Zaplanowano ukończenie tej zmiany do 4 lutego 2020.

Zalecamy, aby wszyscy z naszych deweloperów testowali swoje aplikacje, korzystając z tych wskazówek:

- Dla ustawienia **Użyj bezpiecznego pliku cookie** ustaw wartość **tak**.

- Ustaw wartość domyślną dla atrybutu **SameSite** na **none**.

- Dodaj dodatkowy `SameSite` atrybut **Secure**.

Aby uzyskać więcej informacji, zobacz [nadchodzące zmiany plików cookie SameSite w ASP.NET i ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) oraz [potencjalne zakłócenia dla witryn internetowych klienta oraz produktów i usług firmy Microsoft w programie Chrome w wersji 79 lub nowszej](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Nowa poprawka dla Microsoft Identity Manager (MIM) 2016 z dodatkiem Service Pack 2 (SP2)

**Typ:** FIXED  
**Kategoria usługi:** Microsoft Identity Manager  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości

Pakiet zbiorczy poprawek (build 4.6.34.0) jest dostępny dla Microsoft Identity Manager (MIM) 2016 z dodatkiem Service Pack 2 (SP2). Ten pakiet zbiorczy rozwiązuje problemy i dodaje ulepszenia, które są opisane w sekcji "problemy rozwiązane i ulepszenia dodane w tej aktualizacji".

Aby uzyskać więcej informacji i pobrać pakiet poprawek, zobacz [pakiet zbiorczy aktualizacji pakietu Microsoft Identity Manager 2016 Service Pack 2 (kompilacja 4.6.34.0)](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Nowy raport działania aplikacji AD FS, który pomoże migrować aplikacje do usługi Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ

Użyj nowego raportu aktywność aplikacji Active Directory Federation Services (AD FS) w Azure Portal, aby określić, które aplikacje mogą być migrowane do usługi Azure AD. Raport ocenia wszystkie AD FS aplikacje pod kątem zgodności z usługą Azure AD, sprawdza pod kątem problemów i zapewnia wskazówki dotyczące przygotowywania poszczególnych aplikacji do migracji.

Aby uzyskać więcej informacji, zobacz temat [Używanie raportu działania aplikacji AD FS do migrowania aplikacji do usługi Azure AD](../manage-apps/migrate-adfs-application-activity.md).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Nowy przepływ pracy do żądania zgody administratora (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Access Control

Nowy przepływ pracy zgody administratora zapewnia administratorom możliwość udzielenia dostępu do aplikacji, które wymagają zatwierdzenia przez administratora. Jeśli użytkownik próbuje uzyskać dostęp do aplikacji, ale nie może zapewnić zgody, może teraz wysłać żądanie zatwierdzenia przez administratora. Żądanie jest wysyłane pocztą e-mail i umieszczane w kolejce, która jest dostępna w Azure Portal, do wszystkich administratorów, którzy zostali wyznaczeni jako recenzenci. Gdy recenzent podejmuje działania dotyczące oczekujących żądań, żądający użytkowników są powiadamiani o tej akcji.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie przepływu pracy zgody administratora (wersja zapoznawcza)](../manage-apps/configure-admin-consent-workflow.md).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nowe środowisko konfiguracji aplikacja usługi Azure AD rejestracji tokenu do zarządzania opcjonalnymi oświadczeniami (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Środowisko deweloperskie

W bloku **Konfiguracja nowego tokenu aplikacja usługi Azure AD rejestracji** na Azure Portal teraz są wyświetlane aplikacje dla deweloperów aplikacji, która jest dynamiczną listą opcjonalnych oświadczeń dla aplikacji. To nowe środowisko pomaga usprawnić migracje aplikacji usługi Azure AD i zminimalizować opcjonalne konfiguracje oświadczeń.

Aby uzyskać więcej informacji, zobacz [dostarczanie opcjonalnych oświadczeń do aplikacji usługi Azure AD](../develop/active-directory-optional-claims.md).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Nowy przepływ pracy zatwierdzania dwuetapowego w usłudze Azure AD uprawnia do zarządzania (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Zarządzanie prawami

Wprowadziliśmy Nowy dwuetapowy przepływ pracy zatwierdzania, który pozwala wymagać od dwóch osób zatwierdzających zatwierdzenie żądania użytkownika do pakietu dostępu. Na przykład można ustawić go tak, aby Menedżer żądającego użytkownika musiał najpierw ją zatwierdzić, a następnie można wymagać od właściciela zasobu zatwierdzić. Jeśli jedna z osób zatwierdzających nie zatwierdzi, dostęp nie zostanie udzielony.

Aby uzyskać więcej informacji, zobacz temat [Zmiana ustawień żądania i zatwierdzania dla pakietu dostępu w usłudze Azure AD uprawnienia zarządzania](../governance/entitlement-management-access-package-request-policy.md).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Aktualizacje strony Moje aplikacje oraz nowe obszary robocze (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwości produktu:** integracja innej firmy

Teraz możesz dostosować sposób wyświetlania przez użytkowników w organizacji i uzyskiwania dostępu do odświeżonych funkcji Moje aplikacje. To nowe środowisko obejmuje również nowe funkcje obszarów roboczych, które ułatwiają użytkownikom znajdowanie i organizowanie aplikacji.

Aby uzyskać więcej informacji na temat nowych funkcji Moje aplikacje i tworzenia obszarów roboczych, zobacz [Tworzenie obszarów roboczych w portalu Moje aplikacje](../manage-apps/access-panel-collections.md).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Obsługa identyfikatorów firmy Google Social dla współpracy B2B usługi Azure AD (ogólna dostępność)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Nowe wsparcie w zakresie korzystania z identyfikatorów usługi Google Social (konta w usłudze Gmail) w usłudze Azure AD pomaga uprościć współpracę dla użytkowników i partnerów. Nie jest już konieczne, aby partnerzy mogli tworzyć nowe konta specyficzne dla firmy Microsoft i zarządzać nimi. Usługi Microsoft Teams są teraz w pełni obsługiwane przez użytkowników Google na wszystkich klientach i we wszystkich punktach końcowych uwierzytelniania związanych z dzierżawcami.

Aby uzyskać więcej informacji, zobacz temat [Dodawanie usługi Google jako dostawcy tożsamości dla użytkowników-Gości B2B](../external-identities/google-federation.md).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Microsoft Edge mobile support dla dostępu warunkowego i logowania jednokrotnego (ogólnie dostępna)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Usługa Azure AD dla przeglądarki Microsoft Edge w systemach iOS i Android obsługuje teraz pojedyncze Sign-On i dostęp warunkowy usługi Azure AD:

- Logowanie jednokrotne **w przeglądarce Microsoft Edge:** Logowanie jednokrotne jest teraz dostępne na natywnych klientach (takich jak Microsoft Outlook i Microsoft Edge) dla wszystkich aplikacji połączonych z usługą Azure AD.

- **Dostęp warunkowy do programu Microsoft Edge:** Korzystając z zasad dostępu warunkowego opartego na aplikacji, użytkownicy muszą używać przeglądarek chronionych przez Microsoft Intune, takich jak Microsoft Edge.

Aby uzyskać więcej informacji na temat dostępu warunkowego i logowania jednokrotnego przy użyciu przeglądarki Microsoft Edge, zobacz artykuł [Obsługa urządzeń przenośnych w usłudze Microsoft Edge Mobile dla dostępu warunkowego i logowania](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) jednokrotnego. Aby uzyskać więcej informacji o sposobie konfigurowania aplikacji klienckich przy użyciu [dostępu warunkowego opartego na aplikacji](../conditional-access/app-based-conditional-access.md) lub [dostępu warunkowego opartego na urządzeniach](../conditional-access/require-managed-devices.md), zobacz [Zarządzanie dostępem do sieci Web za pomocą przeglądarki Microsoft Intuneej chronionej przez zasady](/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Zarządzanie prawami w usłudze Azure AD (ogólna dostępność)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Zarządzanie prawami

Zarządzanie prawami w usłudze Azure AD to nowa funkcja zarządzania tożsamościami, która pomaga organizacjom zarządzać cyklem życia tożsamości i dostępu na dużą skalę. Ta nowa funkcja pomaga zautomatyzować przepływy pracy żądań dostępu, przypisań, przeglądów i wygaśnięcia między grupami, aplikacjami i witrynami usługi SharePoint Online.

Korzystając z usługi Azure AD uprawnia do zarządzania, możesz wydajnie zarządzać dostępem dla pracowników, a także dla użytkowników spoza organizacji, którzy potrzebują dostępu do tych zasobów.

Aby uzyskać więcej informacji, zobacz [co to jest zarządzanie prawami w usłudze Azure AD?](../governance/entitlement-management-overview.md#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatyzowanie aprowizacji kont użytkowników dla tych nowo obsługiwanych aplikacji SaaS

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy  

Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

[Usługa SAP Cloud Platform Authentication Identity](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md), [RingCentral](../saas-apps/ringcentral-provisioning-tutorial.md), [SpaceIQ](../saas-apps/spaceiq-provisioning-tutorial.md), [Miro](../saas-apps/miro-provisioning-tutorial.md), [Cloudgate](../saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial.md), [infor CloudSuite](../saas-apps/infor-cloudsuite-provisioning-tutorial.md), [OfficeSpace Software](../saas-apps/officespace-software-provisioning-tutorial.md), [Priority Matrix](../saas-apps/priority-matrix-provisioning-tutorial.md)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzacja aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — listopad 2019

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W listopadzie 2019 dodaliśmy następujące 21 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Airtable](../saas-apps/airtable-tutorial.md) [HootSuite](../saas-apps/hootsuite-tutorial.md), [Blue Access (BAM)](../saas-apps/blue-access-for-members-tutorial.md), [Bitly](../saas-apps/bitly-tutorial.md), [Riva](../saas-apps/riva-tutorial.md), [ResLife](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), NegometrixPortal — Logowanie jednokrotne [(SSO)](../saas-apps/negometrixportal-tutorial.md), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](../saas-apps/motus-tutorial.md), [MyAryaka](../saas-apps/myaryaka-tutorial.md), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](../saas-apps/visma-tutorial.md), [OneDesk](../saas-apps/onedesk-tutorial.md), [Fokoe](../saas-apps/foko-retail-tutorial.md), Qmarkets [pomysł & zarządzanie innowacyjnością](../saas-apps/qmarkets-idea-innovation-management-tutorial.md), Netskopee [uwierzytelnianie użytkowników](../saas-apps/netskope-user-authentication-tutorial.md), [uniFLOW online](../saas-apps/uniflow-online-tutorial.md), [Claromentis](../saas-apps/claromentis-tutorial.md), [JISC student głosujących Registration](../saas-apps/jisc-student-voter-registration-tutorial.md), [e4enable](https://portal.e4enable.com/)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Nowa i ulepszona Galeria aplikacji usługi Azure AD

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ

Zaktualizowaliśmy galerię aplikacji usługi Azure AD, aby ułatwić znajdowanie wstępnie zintegrowanych aplikacji obsługujących obsługę administracyjną, OpenID Connect Connect i SAML w dzierżawie Azure Active Directory.

Aby uzyskać więcej informacji, zobacz [Dodawanie aplikacji do dzierżawy Azure Active Directory](../manage-apps/add-application-portal.md).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Zwiększono limit długości definicji roli aplikacji z 120 do 240 znaków

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ

Firma Microsoft wysłuchuje od klientów, że długość limitu dla wartości definicji roli aplikacji w niektórych aplikacjach i usługach jest za mała o 120 znaków. W odpowiedzi Zwiększono maksymalną długość definicji wartości roli do 240 znaków.

Aby uzyskać więcej informacji o korzystaniu z definicji ról specyficznych dla aplikacji, zobacz [Dodawanie ról aplikacji w aplikacji i odbieranie ich w tokenie](../develop/howto-add-app-roles-in-azure-ad-apps.md).

---

## <a name="october-2019"></a>Październik 2019 r.

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Wycofanie interfejsu API identityRiskEvent na potrzeby wykrywania ryzyka Azure AD Identity Protection

**Typ:** Zaplanuj **kategorię usługi zmiany:** **zdolność produktu** do ochrony tożsamości: zabezpieczenia tożsamości & ochrona

W odpowiedzi na opinie dla deweloperów Azure AD — wersja Premium w przypadku subskrybentów P2 mogą teraz wykonywać złożone zapytania dotyczące danych wykrywania ryzyka Azure AD Identity Protection przy użyciu nowego interfejsu API riskDetection na potrzeby Microsoft Graph. Istniejąca wersja beta interfejsu API [identityRiskEvent](/graph/api/resources/identityriskevent?view=graph-rest-beta) przestanie zwracać dane **na około 10 stycznia 2020**. Jeśli Twoja organizacja korzysta z interfejsu API identityRiskEvent, należy przejść do nowego interfejsu API riskDetection.

Więcej informacji na temat nowego interfejsu API riskDetection można znaleźć w [dokumentacji dotyczącej interfejsu API wykrywania ryzyka](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Obsługa atrybutu SameSite i programu Chrome 80 przez serwer proxy aplikacji

**Typ:** Zaplanuj **kategorię usługi zmiany:** produkt proxy aplikacji **:** Access Control

Kilka tygodni przed publikacją w przeglądarce Chrome 80, firma Microsoft planuje aktualizację sposobu, w jaki pliki cookie serwera proxy aplikacji traktują atrybut **SameSite** . W wersji programu Chrome 80 każdy plik cookie, który nie określa atrybutu **SameSite** , będzie traktowany jak, gdyby był ustawiony na `SameSite=Lax` .

Aby uniknąć potencjalnie ujemnych wpływów spowodowanych tą zmianą, aktualizujemy dostęp do serwera proxy aplikacji i plików cookie sesji przez:

- Ustawienie wartości domyślnej dla ustawienia **Użyj bezpiecznego pliku cookie** na wartość **tak**.

- Ustawianie wartości domyślnej dla atrybutu **SameSite** na **none**.

    >[!NOTE]
    > Pliki cookie dostępu do serwera proxy aplikacji są zawsze przesyłane wyłącznie za pośrednictwem bezpiecznych kanałów. Te zmiany dotyczą tylko plików cookie sesji.

Aby uzyskać więcej informacji na temat ustawień plików cookie serwera proxy aplikacji, zobacz [Ustawienia plików cookie dotyczące uzyskiwania dostępu do aplikacji lokalnych w Azure Active Directory](../manage-apps/application-proxy-configure-cookie-settings.md).

---

### <a name="app-registrations-legacy-and-app-management-in-the-application-registration-portal-appsdevmicrosoftcom-is-no-longer-available"></a>Rejestracje aplikacji (starsze) i zarządzanie aplikacjami w portalu rejestracji aplikacji (apps.dev.microsoft.com) nie jest już dostępne

**Typ:** Zaplanuj **kategorię usługi zmiany:** brak **możliwości produktu:** środowisko programistyczne

Użytkownicy z kontami usługi Azure AD nie mogą już rejestrować aplikacji ani zarządzać nimi za pomocą portalu rejestracji aplikacji (apps.dev.microsoft.com) ani rejestrować aplikacji i zarządzać nimi Rejestracje aplikacji w Azure Portal.

Aby dowiedzieć się więcej na temat nowego środowiska Rejestracje aplikacji, zobacz [rejestracje aplikacji w przewodniku szkolenia Azure Portal](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Użytkownicy nie muszą już być ponownie rejestrowani podczas migracji z usługi MFA dla poszczególnych użytkowników do usługi MFA opartej na dostępie warunkowym

**Typ:** **Kategoria stałej usługi:** **możliwości produktu MFA:** zabezpieczenia tożsamości & ochrona

Rozwiązano znany problem polegający na tym, że użytkownicy musieli ponownie zarejestrować się, jeśli zostały wyłączone dla Multi-Factor Authentication poszczególnych użytkowników (MFA), a następnie włączono usługę MFA za pomocą zasad dostępu warunkowego.

Aby wymagać od użytkowników ponownego zarejestrowania, można wybrać opcję **wymagana ponowna rejestracja usługi MFA** z metod uwierzytelniania użytkownika w portalu usługi Azure AD. Aby uzyskać więcej informacji na temat migrowania użytkowników z usługi MFA do usługi MFA na podstawie dostępu warunkowego, zobacz [konwertowanie użytkowników z usługi MFA na użytkownika na podstawie dostępu warunkowego](../authentication/howto-mfa-getstarted.md#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nowe możliwości przekształcania i wysyłania oświadczeń w tokenie SAML

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** Logowanie jednokrotne

Dodaliśmy dodatkowe możliwości, które ułatwiają Dostosowywanie i wysyłanie oświadczeń w tokenie SAML. Te nowe możliwości obejmują:

- Dodatkowe funkcje przekształcania oświadczeń, które ułatwiają modyfikowanie wartości wysyłanej w ramach oświadczenia.

- Możliwość zastosowania wielu przekształceń do pojedynczego żądania.

- Możliwość określenia źródła roszczeń w oparciu o typ użytkownika i grupę, do której należy użytkownik.

Aby uzyskać szczegółowe informacje na temat tych nowych funkcji, w tym ich używania, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](../develop/active-directory-saml-claims-customization.md).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nowa strona logowania dla użytkowników końcowych w usłudze Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** uwierzytelnienia (logowania) **możliwości produktu:** monitorowanie & raportowanie

Dodaliśmy nową stronę **Moje logowania** ( https://mysignins.microsoft.com) Aby umożliwić użytkownikom w organizacji wyświetlanie ich ostatnich historii logowania w celu sprawdzenia, czy nie występują nietypowe działania. Ta nowa strona umożliwia użytkownikom wyświetlanie:

- Jeśli ktoś podejmie próbę odgadnięcia hasła.

- Jeśli osoba atakująca pomyślnie zalogował się na swoje konto i z lokalizacji.

- Jakie aplikacje próbowały uzyskać dostęp osoby atakującej.

Aby uzyskać więcej informacji, zobacz, czy [Użytkownicy mogą teraz sprawdzić historię logowania w blogu dotyczącym nietypowej aktywności](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) .

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migracja Azure AD Domain Services (Azure AD DS) z wersji klasycznej do Azure Resource Manager sieci wirtualnych

**Typ:** Nowa **Kategoria usługi funkcji:** Azure AD Domain Services **możliwości produktu:** Azure AD Domain Services

Naszym klientom, którzy mieli zablokowaną obsługę klasycznych sieci wirtualnych — mamy wspaniałe wiadomości! Teraz można przeprowadzić jednorazową migrację z klasycznej sieci wirtualnej do istniejącej Menedżer zasobów sieci wirtualnej. Po przejściu do sieci wirtualnej Menedżer zasobów będzie można korzystać z dodatkowych i uaktualnionych funkcji, takich jak szczegółowe zasady haseł, powiadomienia e-mail i dzienniki inspekcji.

Aby uzyskać więcej informacji, zobacz [wersja zapoznawcza — migruj Azure AD Domain Services z klasycznego modelu sieci wirtualnej do Menedżer zasobów](../../active-directory-domain-services/migrate-from-classic-vnet.md).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Aktualizacje układu strony Azure AD B2C

**Typ:** Nowa **Kategoria usługi funkcji:** B2C — **możliwości produktu** zarządzania tożsamościami konsumenta: B2B/B2C

Wprowadzono nowe zmiany w wersji 1.2.0 kontraktu dotyczącego strony dla Azure AD B2C. W tej zaktualizowanej wersji można teraz sterować kolejnością obciążeń dla elementów, co może również pomóc w zatrzymaniu migotania wykonywanego po załadowaniu arkusza stylów (CSS).

Aby uzyskać pełną listę zmian wprowadzonych w umowie dotyczącej strony, zobacz [Dziennik zmian wersji](../../active-directory-b2c/page-layout.md#other-pages-providerselection-claimsconsent-unifiedssd).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Aktualizowanie na stronie Moje aplikacje wraz z nowymi obszarami roboczymi (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** produkt moje aplikacje **:** Access Control

Teraz możesz dostosować sposób, w jaki użytkownicy organizacji wyświetlają i uzyskują dostęp do nowego środowiska moje aplikacje, w tym za pomocą funkcji nowe obszary robocze, aby ułatwić im znajdowanie aplikacji. Nowe funkcje obszarów roboczych działają jako filtr dla aplikacji, do których użytkownicy w organizacji mają już dostęp.

Aby uzyskać więcej informacji na temat wdrażania nowych funkcji Moje aplikacje i tworzenia obszarów roboczych, zobacz [Tworzenie obszarów roboczych w portalu My Apps (wersja zapoznawcza)](../manage-apps/access-panel-collections.md).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Obsługa miesięcznego aktywnego modelu rozliczania opartego na użytkownikach (ogólna dostępność)

**Typ:** Nowa **Kategoria usługi funkcji:** B2C — **możliwości produktu** zarządzania tożsamościami konsumenta: B2B/B2C

Azure AD B2C teraz obsługuje rozliczenia comiesięcznych aktywnych użytkowników (MAU). Rozliczenia MAU są oparte na liczbie unikatowych użytkowników z aktywnością uwierzytelniania w miesiącu kalendarzowym. Istniejący klienci mogą w dowolnym momencie przełączyć się do tej nowej metody rozliczania.

Od 1 listopada 2019 Wszyscy nowi klienci będą automatycznie rozliczani przy użyciu tej metody. Ta metoda rozliczania przynosi klientom korzyści wynikające z kosztów i możliwości planowania.

Aby uzyskać więcej informacji, zobacz [uaktualnianie do miesięcznego modelu rozliczeń aktywnych użytkowników](../../active-directory-b2c/billing.md#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — październik 2019

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** integracja z firmą

W październiku 2019 dodaliśmy do galerii aplikacji 35 te nowe aplikacje z obsługą Federacji:

[W przypadku kryzysu — Mobile](../saas-apps/in-case-of-crisis-mobile-tutorial.md), [Juno](../saas-apps/juno-journey-tutorial.md), [ExponentHR](../saas-apps/exponenthr-tutorial.md), [nienaruszone](https://tact.ai/assistant/) [OpusCapita Management](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](../saas-apps/learnster-tutorial.md), [dynaTrace](../saas-apps/dynatrace-tutorial.md), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](../saas-apps/freshworks-tutorial.md), [eCornell](../saas-apps/ecornell-tutorial.md), [ShipHazmat](../saas-apps/shiphazmat-tutorial.md), [NETSKOPE Cloud Security](../saas-apps/netskope-cloud-security-tutorial.md), [content](../saas-apps/contentful-tutorial.md), [Bindtuning](https://bindtuning.com/login), [HireVue koordynuje — Europa](https://www.hirevue.com/), Współrzędna [HireVue-USOnly](https://www.hirevue.com/), [HireVue koordynuje-US](https://www.hirevue.com/), [WittyParrot](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup) [, Cloudmore,](../saas-apps/cloudmore-tutorial.md) [Visit.org](../saas-apps/visitorg-tutorial.md), Cambium [Xirrus EasyPass Portal,](https://login.xirrus.com/azure-signup)Paylocity [,](../saas-apps/paylocity-tutorial.md)Emailing! [,](../saas-apps/mail-luck-tutorial.md) [teamal](https://theteamie.com/), prędkość [dla zespołów,](https://velocity.peakup.org/teams/login)aplikacji signl4 [,](https://account.signl4.com/manage)EAB [,](../saas-apps/eab-navigate-impl-tutorial.md)Impl, ScreenMeet [,](https://console.screenmeet.com/), [w](https://pi.ompnt.com/) [przypadku usługi (iPhone](https://speaking.email/FAQ/98/email-access-via-microsoft-intune)/ [Android),](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct) [ExactCare](https://qubie.azurewebsites.net/static/adminTab/authorize.html) [rejestracji jednokrotnej](../saas-apps/exactcare-sso-tutorial.md) [, iHealthHome](https://ihealthnav.com/account/signin)365

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Skonsolidowany element menu zabezpieczeń w portalu usługi Azure AD

**Typ:** Zmieniono **kategorię usługi funkcji:** **zdolność produktu** do ochrony tożsamości: zabezpieczenia tożsamości & ochrona

Teraz możesz uzyskać dostęp do wszystkich dostępnych funkcji zabezpieczeń usługi Azure AD z poziomu nowego elementu menu **zabezpieczenia** i z paska **wyszukiwania** w Azure Portal. Ponadto nowa strona docelowa **zabezpieczeń** , nazywana **zabezpieczeniami — wprowadzenie**, oferuje linki do naszej dokumentacji publicznej, wskazówek dotyczących zabezpieczeń i przewodników wdrażania.

Nowe menu **zabezpieczeń** zawiera:

- Dostęp warunkowy
- Identity Protection
- Security Center
- Ocena bezpiecznego tożsamości
- Metody uwierzytelniania
- Uwierzytelnianie wieloskładnikowe
- Raporty o podwyższonym ryzyku — Ryzykowni użytkownicy, ryzykowne logowania, wykrywanie ryzyka
- I nie tylko...

Aby uzyskać więcej informacji, zobacz [zabezpieczenia — wprowadzenie](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Zasady wygasania grup pakietu Office 365 z funkcją autoodnawiania

**Typ:** Zmieniono **kategorię usługi funkcji:** Zarządzanie grupą **:** Zarządzanie cyklem życia tożsamości

Zasady wygasania grup pakietu Office 365 zostały udoskonalone w celu automatycznego odnawiania grup, które są aktywnie używane przez jego członków. Grupy będą autoodnawiane na podstawie aktywności użytkownika we wszystkich aplikacjach pakietu Office 365, w tym programów Outlook, SharePoint i Teams.

To ulepszenie pomaga zmniejszyć liczbę powiadomień o wygasaniu grup i pomaga upewnić się, że aktywne grupy są nadal dostępne. Jeśli masz już aktywne zasady wygasania dla grup programu Office 365, nie musisz nic robić, aby włączyć tę nową funkcję.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad wygasania dla grup pakietu Office 365](../users-groups-roles/groups-lifecycle.md).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Zaktualizowano środowisko tworzenia Azure AD Domain Services (Azure AD DS)

**Typ:** Zmieniono **kategorię usługi funkcji:** Azure AD Domain Services **możliwości produktu:** Azure AD Domain Services

Zaktualizowaliśmy Azure AD Domain Services (Azure AD DS), aby uwzględnić nowe i udoskonalone środowisko tworzenia, co pomoże Ci utworzyć domenę zarządzaną w zaledwie trzech kliknięciach. Ponadto możesz teraz przekazywać i wdrażać AD DS platformy Azure z szablonu.

Aby uzyskać więcej informacji, zobacz [Samouczek: Tworzenie i Konfigurowanie wystąpienia Azure Active Directory Domain Services](../../active-directory-domain-services/tutorial-create-instance.md).

---

## <a name="september-2019"></a>Wrzesień 2019 r.

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Zaplanuj zmianę: zaniechanie Power BI pakietów zawartości

**Typ:** Zaplanuj **kategorię usługi zmiany:** raportowanie **możliwości produktu:** monitorowanie & raportowanie

Od 1 października 2019 Power BI rozpocznie się wycofanie wszystkich pakietów zawartości, w tym pakietu zawartości usługi Azure AD Power BI. Alternatywą dla tego pakietu zawartości jest użycie skoroszytów usługi Azure AD w celu uzyskania szczegółowych informacji dotyczących usług związanych z usługą Azure AD. Dostępne są dodatkowe skoroszyty, w tym skoroszyty dotyczące zasad dostępu warunkowego w trybie tylko raportowanie, informacje oparte na zgodzie aplikacji i inne.

Aby uzyskać więcej informacji na temat skoroszytów, zobacz [jak używać skoroszytów Azure monitor dla Azure Active Directory raportów](../reports-monitoring/howto-use-azure-monitor-workbooks.md). Aby uzyskać więcej informacji na temat wycofania pakietów zawartości, zobacz wpis w blogu dotyczący [ogłaszania Power BI szablonów aplikacji ogólnie dostępna](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) .

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Mój profil zmienia nazwę i integruje się ze stroną konta Microsoft Office

**Typ:** Planowanie **kategorii usługi zmiany:** moja profil/ **możliwość produktu konta:** współpraca

Od października środowisko my profilu zostanie moje konto. W ramach tej zmiany wszędzie tam, gdzie się znajduje, **mój profil** zmieni się na **Moje konto**. W oparciu o zmiany nazw i niektóre ulepszenia projektowe zaktualizowane środowisko oferuje dodatkową integrację ze stroną konta Microsoft Office. W związku z tym będziesz mieć dostęp do instalacji i subskrypcji pakietu Office na stronie z **omówieniem konta** oraz z preferencjami kontaktu związanymi z pakietem Office na stronie **prywatność** .

Aby uzyskać więcej informacji na temat środowiska my profile (wersja zapoznawcza), zobacz temat [Omówienie portalu My profil (wersja zapoznawcza)](../user-help/my-account-portal-overview.md).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Zbiorcze zarządzanie grupami i członkami przy użyciu plików CSV w portalu usługi Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** funkcja zarządzania grupami **:** współpraca

Z przyjemnością ogłaszamy publiczną wersję zapoznawczą środowiska zarządzania grupami zbiorczymi w portalu usługi Azure AD. Można teraz używać pliku CSV i portalu usługi Azure AD do zarządzania grupami i listami elementów członkowskich, w tym:

- Dodawanie członków do grupy lub usuwanie ich z niej.

- Pobieranie listy grup z katalogu.

- Pobieranie listy członków grupy dla określonej grupy.

Aby uzyskać więcej informacji, zobacz [zbiorcze Dodawanie członków](../users-groups-roles/groups-bulk-import-members.md), [zbiorcze usuwanie](../users-groups-roles/groups-bulk-remove-members.md)członków, [listy członków pobierania zbiorczego](../users-groups-roles/groups-bulk-download-members.md)i [listę grup pobierania zbiorczego](../users-groups-roles/groups-bulk-download.md).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Zgoda dynamiczna jest teraz obsługiwana za pomocą nowego punktu końcowego zgody na administratora

**Typ:** Nowa **Kategoria usługi funkcji:** uwierzytelnienia (logowania) **możliwości produktu:** uwierzytelnianie użytkownika

Utworzyliśmy nowy punkt końcowy zgody administratora, który będzie obsługiwał zgodę dynamiczną, co jest przydatne w przypadku aplikacji, które chcą korzystać z modelu zgody dynamicznej na platformie tożsamości firmy Microsoft.

Aby uzyskać więcej informacji o sposobach korzystania z tego nowego punktu końcowego, zobacz [Korzystanie z punktu końcowego zgody administratora](../develop/v2-admin-consent.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — wrzesień 2019

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** integracja z firmą

We wrześniu 2019 dodaliśmy te 29 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [dostęp z logowaniem jednokrotnym w usłudze MS Azure do usługi Ethidex w biurze &trade; — Logowanie jednokrotne](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md) [iServer Portal](../saas-apps/iserver-portal-tutorial.md), [SKYSITE](../saas-apps/skysite-tutorial.md), [Concur podróży i wydatków](../saas-apps/concur-travel-and-expense-tutorial.md), [WorkBoard](../saas-apps/workboard-tutorial.md), `https://apps.yeeflow.com/` , [obiekty łuku](../saas-apps/arc-facilities-tutorial.md), [Luware Stratus zespołu](https://stratus.emea.luware.cloud/login), [szerokie pomysły](https://wideideas.online/wideideas/), [Prisma Cloud](../saas-apps/prisma-cloud-tutorial.md), [JDLT Hub](https://clients.jdlt.co.uk/login) [, Renraku](../saas-apps/renraku-tutorial.md), [SealPath Secure Browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [Prisma Cloud](../saas-apps/prisma-cloud-tutorial.md),, `https://app.penneo.com/` , Cintoo `https://app.testhtm.com/settings/email-integration` [Cloud](https://aec.cintoo.com/login), [Whitesource](../saas-apps/whitesource-tutorial.md), hostowane [BIS](../saas-apps/bis-tutorial.md), w [Coo Kai Team Build](https://ms-contacts.coo-kai.jp/) [trybie online](../saas-apps/hosted-heritage-online-sso-tutorial.md), [IDC](../saas-apps/idc-tutorial.md), [Sonarqube](../saas-apps/sonarqube-tutorial.md) [CakeHR](../saas-apps/cakehr-tutorial.md) [Adobe Identity Management](../saas-apps/tutorial-list.md) [Discovery Benefits SSO](../saas-apps/discovery-benefits-sso-tutorial.md) [Amelio](https://app.amelio.co/)`https://itask.yipinapp.com/`

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-global-reader-role"></a>Nowa rola czytnika globalnego usługi Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** **możliwości produktu** ról usługi Azure AD: Access Control

Od 24 września 2019 zostanie rozpoczęta nowa rola Azure Active Directory (AD) o nazwie Reader Global. To wdrożenie rozpocznie się wraz z produkcyjnym i globalnym klientom w chmurze (w zatoce), kończąc na całym świecie w październiku.

Globalna rola czytnika to odpowiedni dla administratora globalnego tylko do odczytu. Użytkownicy w tej roli mogą odczytywać ustawienia i informacje administracyjne w ramach usług Microsoft 365, ale nie mogą podejmować działań związanych z zarządzaniem. Utworzyliśmy globalną rolę czytelnika, która pomaga w zmniejszeniu liczby administratorów globalnych w organizacji. Ponieważ konta administratorów globalnych są zaawansowane i podatne na ataki, zalecamy użycie mniej niż pięciu administratorów globalnych. Zalecamy korzystanie z roli czytnika globalnego na potrzeby planowania, inspekcji lub badań. Zalecamy również korzystanie z roli czytnika globalnego w połączeniu z innymi ograniczonymi rolami administratora, takimi jak administrator programu Exchange, aby ułatwić wykonanie pracy bez konieczności posiadania roli administratora globalnego.

Globalna rola czytnika współpracuje z nowym centrum administracyjnym Microsoft 365, centrum administracyjnym programu Exchange, centrum administracyjnym zespołów, Security Center, centrum zgodności, centrum administracyjnym usługi Azure AD oraz centrum administracyjnym zarządzania urządzeniami.

>[!NOTE]
> Na początku publicznej wersji zapoznawczej rola czytnika globalnego nie będzie współpracująca z: SharePoint, Privileged Access Management, Skrytka klienta, etykietami czułości, cyklem życia zespołów, raportowaniem & wywołań analizy, zespoły i zarządzanie urządzeniami telefonem IP oraz zespoły.

Aby uzyskać więcej informacji, zobacz [uprawnienia roli administrator w Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Uzyskiwanie dostępu do lokalnego serwera raportów z aplikacji mobilnej Power BI przy użyciu serwer proxy aplikacji usługi Azure Active Directory

**Typ:** Nowa **Kategoria usługi funkcji:** **możliwości produktu** serwera proxy aplikacji: Access Control

Nowa integracja między aplikacją mobilną Power BI a usługą Azure serwer proxy aplikacji usługi Azure AD umożliwia bezpieczne logowanie do aplikacji mobilnej Power BI i wyświetlanie dowolnych raportów organizacji hostowanych w Serwer raportów usługi Power BI lokalnym.

Aby uzyskać informacje o aplikacji mobilnej Power BI, w tym o lokalizacji, w której należy pobrać aplikację, zapoznaj się z [witryną Power BI](https://powerbi.microsoft.com/mobile/). Aby uzyskać więcej informacji na temat sposobu konfigurowania aplikacji mobilnej Power BI przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD, zobacz [Włączanie dostępu zdalnego do Power BI Mobile przy użyciu usługi azure serwer proxy aplikacji usługi Azure AD](../manage-apps/application-proxy-integrate-with-power-bi.md).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Dostępna jest nowa wersja modułu AzureADPreview PowerShell

**Typ:** Zmieniono **kategorię usługi funkcji:** inny **produkt:** katalog

Do modułu AzureADPreview dodano nowe polecenia cmdlet, które ułatwiają Definiowanie i przypisywanie ról niestandardowych w usłudze Azure AD, w tym:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nowa wersja Azure AD Connect

**Typ:** Zmieniono **kategorię usługi funkcji:** inny **produkt:** katalog

Firma Microsoft udostępniła zaktualizowaną wersję Azure AD Connect dla klientów korzystających z aktualizacji. Ta nowa wersja zawiera kilka nowych funkcji, ulepszeń i poprawek błędów. Aby uzyskać więcej informacji o tej nowej wersji, zobacz [Azure AD Connect: historia](../hybrid/reference-connect-version-history.md#14250)wersji.

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Serwer usługi Azure Multi-Factor Authentication (MFA) w wersji 8.0.2 jest teraz dostępny

**Typ:** **Kategoria stałej usługi:** **możliwości produktu MFA:** zabezpieczenia tożsamości & ochrona

Jeśli jesteś istniejącym klientem, który aktywuje serwer usługi MFA przed 1 lipca 2019, możesz teraz pobrać najnowszą wersję serwera usługi MFA (wersja 8.0.2). W tej nowej wersji:

- Rozwiązano problem polegający na tym, że gdy usługa Azure AD Sync zmieni użytkownika z wyłączone na włączone, do użytkownika zostanie wysłana wiadomość e-mail.

- Rozwiązano problem, aby klienci mogli pomyślnie uaktualnić program, jednocześnie używając funkcji tagów.

- Dodano kod kraju Kosowa (+ 383).

- Dodano jednorazowe rejestrowanie inspekcji do dziennika MultiFactorAuthSvc. log.

- Ulepszona wydajność zestawu SDK usługi sieci Web.

- Rozwiązano inne drobne błędy.

Od 1 lipca 2019 firma Microsoft zatrzymała ofertę serwera usługi MFA dla nowych wdrożeń. Nowi klienci, którzy wymagają uwierzytelniania wieloskładnikowego, powinni korzystać z usługi Azure Multi-Factor Authentication opartej na chmurze. Aby uzyskać więcej informacji, zobacz [Planowanie wdrożenia usługi Azure Multi-Factor Authentication opartej na chmurze](../authentication/howto-mfa-getstarted.md).

---

## <a name="august-2019"></a>Sierpień 2019 r.

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Ulepszone wyszukiwanie, filtrowanie i sortowanie dla grup jest dostępne w portalu usługi Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** funkcja zarządzania grupami **:** współpraca

Z przyjemnością ogłaszamy publiczną wersję zapoznawczą rozszerzonych środowisk związanych z grupami w portalu usługi Azure AD. Te ulepszenia ułatwiają lepsze zarządzanie grupami i listami składowymi, zapewniając:

- Zaawansowane możliwości wyszukiwania, takie jak wyszukiwanie podciągów na listach grup.
- Zaawansowane opcje filtrowania i sortowania na listach elementów członkowskich i właścicieli.
- Nowe możliwości wyszukiwania dla list elementów członkowskich i właścicieli.
- Dokładniejsze liczby grup dla dużych grup.

Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami w Azure Portal](./active-directory-groups-members-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Nowe role niestandardowe są dostępne dla zarządzania rejestracją aplikacji (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** **możliwości produktu** ról usługi Azure AD: Access Control

Role niestandardowe (dostępne w ramach subskrypcji usługi Azure AD P1 lub P2) mogą teraz pomóc zapewnić szczegółowy dostęp, umożliwiając tworzenie definicji ról z określonymi uprawnieniami, a następnie przypisywanie tych ról do określonych zasobów. Obecnie można tworzyć role niestandardowe przy użyciu uprawnień do zarządzania rejestracjami aplikacji, a następnie przypisywania roli do określonej aplikacji. Aby uzyskać więcej informacji na temat ról niestandardowych, zobacz [Niestandardowe role administratorów w Azure Active Directory (wersja zapoznawcza)](../users-groups-roles/roles-custom-overview.md).

Jeśli potrzebujesz dodatkowych uprawnień lub zasobów, które nie są obecnie widoczne, możesz wysłać opinię do naszej [witryny opinii o platformie Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) i dodać Twoje żądanie do naszej mapy aktualizacji.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Nowe dzienniki aprowizacji mogą pomóc w monitorowaniu i rozwiązywaniu problemów z wdrożeniem aprowizacji aplikacji (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** **możliwości produktu** aprowizacji aplikacji: Zarządzanie cyklem życia tożsamości

Nowe dzienniki aprowizacji są dostępne, aby ułatwić monitorowanie i rozwiązywanie problemów z wdrożeniem aprowizacji użytkowników i grup. Te nowe pliki dziennika zawierają informacje o:

- Które grupy zostały pomyślnie utworzone w [usługi ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)
- Jakie role zostały zaimportowane z [Amazon Web Services (AWS)](../saas-apps/amazon-web-service-tutorial.md#configure-and-test-azure-ad-sso-for-amazon-web-services-aws)
- Którzy pracownicy nie zaimportowali z [produktu Workday](../saas-apps/workday-inbound-tutorial.md)

Aby uzyskać więcej informacji, zobacz artykuł [aprowizacji raportów w portalu Azure Active Directory (wersja zapoznawcza)](../reports-monitoring/concept-provisioning-logs.md).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nowe raporty zabezpieczeń dla wszystkich administratorów usługi Azure AD (ogólna dostępność)

**Typ:** Nowa **Kategoria usługi funkcji:** **zdolność produktu** do ochrony tożsamości: zabezpieczenia tożsamości & ochrona

Domyślnie wszyscy Administratorzy usługi Azure AD wkrótce będą mogli uzyskiwać dostęp do nowoczesnych raportów zabezpieczeń w ramach usługi Azure AD. Do końca września będzie można użyć transparentu w górnej części nowoczesnych raportów zabezpieczeń, aby powrócić do starych raportów.

Nowoczesne raporty zabezpieczeń zapewniają dodatkowe możliwości ze starszych wersji, w tym:

- Zaawansowane filtrowanie i sortowanie
- Akcje zbiorcze, takie jak odrzucanie ryzyka użytkownika
- Potwierdzenie ochrony jednostek naruszonych lub bezpiecznych
- Stan ryzyka, obejmujący: zagrożone, odrzucone, korygowane i potwierdzone naruszone
- Nowe wykrywania związane z ryzykiem (dostępne dla subskrybentów Azure AD — wersja Premium)

Aby uzyskać więcej informacji, zobacz [ryzykowni użytkownicy](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users), [ryzykowne logowania](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins)i [wykrywanie ryzyka](../identity-protection/howto-identity-protection-investigate-risk.md#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Tożsamość zarządzana przypisana przez użytkownika jest dostępna dla Virtual Machines i Virtual Machine Scale Sets (ogólna dostępność)

**Typ:** Nowa **Kategoria usługi funkcji:** zarządzane tożsamości dla **możliwości produktu** platformy Azure: środowisko programistyczne

Tożsamości zarządzane przypisane przez użytkownika są teraz ogólnie dostępne dla Virtual Machines i Virtual Machine Scale Sets. W ramach tego działania platforma Azure może utworzyć tożsamość w dzierżawie usługi Azure AD, która jest zaufana dla używanej subskrypcji, i może być przypisana do co najmniej jednego wystąpienia usługi platformy Azure. Aby uzyskać więcej informacji na temat tożsamości zarządzanych przypisanych przez użytkownika, zobacz [co to jest tożsamość zarządzana dla zasobów platformy Azure?](https://aka.ms/azuremanagedidentity).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Użytkownicy mogą resetować swoje hasła przy użyciu aplikacji mobilnej lub tokenu sprzętowego (ogólna dostępność)

**Typ:** Zmieniono **kategorię usługi funkcji:** funkcja samoobsługowego resetowania hasła **:** uwierzytelnianie użytkownika

Użytkownicy, którzy zarejestrowali aplikację mobilną w organizacji, mogą teraz resetować swoje hasła, zatwierdzając powiadomienie w aplikacji Microsoft Authenticator lub wprowadzając kod z aplikacji mobilnej lub tokenu sprzętowego.

Aby uzyskać więcej informacji, zobacz [jak to działa: Samoobsługowe resetowanie hasła w usłudze Azure AD](https://aka.ms/authappsspr). Aby uzyskać więcej informacji na temat środowiska użytkownika, zobacz [Resetowanie własnego hasła służbowego — Omówienie](../user-help/active-directory-passwords-reset-register.md).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignoruje udostępnioną pamięć podręczną MSAL.NET dla scenariuszy w imieniu

**Typ:** **Kategoria stałych usług:** uwierzytelnienia (logowania) **możliwości produktu:** uwierzytelnianie użytkownika

Począwszy od biblioteki Azure AD Authentication Library (ADAL.NET) w wersji 5.0.0 — wersja zapoznawcza, deweloperzy aplikacji muszą [serializować jedną pamięć podręczną na konto dla aplikacji sieci Web i interfejsów API sieci Web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). W przeciwnym razie niektóre scenariusze korzystające z usługi [Flow w miejscu](../develop/scenario-web-api-call-api-app-configuration.md?tabs=java) dla środowiska Java wraz z pewnymi przypadkami użycia programu `UserAssertion` mogą spowodować podniesienie uprawnień. Aby uniknąć tej luki w zabezpieczeniach, ADAL.NET teraz ignoruje bibliotekę uwierzytelniania Microsoft dla udostępnionej pamięci podręcznej usługi dotnet (MSAL.NET) dla scenariuszy w imieniu użytkownika.

Aby uzyskać więcej informacji o tym problemie, zobacz temat [Luka w zabezpieczeniach Azure Active Directory podniesienia uprawnień w bibliotece uwierzytelniania](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — sierpień 2019

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** integracja z firmą

W sierpniu 2019 dodaliśmy te 26 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Platforma projektowi Civic Innovation](../saas-apps/civic-platform-tutorial.md), [Amazon Business](../saas-apps/amazon-business-tutorial.md), [ProNovos Ops Manager](../saas-apps/pronovos-ops-manager-tutorial.md), [Cognidox](../saas-apps/cognidox-tutorial.md), [Viareport — Portal Inativ (Europa)](../saas-apps/viareports-inativ-portal-europe-tutorial.md), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](../saas-apps/robin-tutorial.md), [Academy](../saas-apps/academy-attendance-tutorial.md), [macierze priorytetowe](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint Backup](../saas-apps/carbonite-endpoint-backup-tutorial.md), [CPQSync, Cincom](../saas-apps/cpqsync-by-cincom-tutorial.md), [Chargebee](../saas-apps/chargebee-tutorial.md), [. &trade; Media Portal](https://portal.deliver.media), [teraźniejszości Education](../saas-apps/frontline-education-tutorial.md), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD Connect](https://www.stashcat.com), [migotanie](../saas-apps/blink-tutorial.md), [Vocoli](../saas-apps/vocoli-tutorial.md), [ProNovos Analytics](../saas-apps/pronovos-analytics-tutorial.md), [Sigstr](../saas-apps/sigstr-tutorial.md), [Darwinbox](../saas-apps/darwinbox-tutorial.md), [Watch przez kolory](../saas-apps/watch-by-colors-tutorial.md), [zespół](../saas-apps/harness-tutorial.md), EAB [Nawigacja strategiczna](../saas-apps/eab-navigate-strategic-care-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Dostępne są nowe wersje modułów AzureAD PowerShell i AzureADPreview PowerShell

**Typ:** Zmieniono **kategorię usługi funkcji:** inny **produkt:** katalog

Dostępne są nowe aktualizacje modułów programu PowerShell AzureAD i AzureAD w wersji zapoznawczej:

- Dodano nowy `-Filter` parametr do `Get-AzureADDirectoryRole` parametru w module AzureAD. Ten parametr ułatwia filtrowanie ról w katalogu zwracanych przez polecenie cmdlet.
- Do modułu AzureADPreview dodano nowe polecenia cmdlet, które ułatwiają Definiowanie i przypisywanie ról niestandardowych w usłudze Azure AD, w tym:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Ulepszenia interfejsu użytkownika konstruktora reguł grupy dynamicznej w Azure Portal

**Typ:** Zmieniono **kategorię usługi funkcji:** Zarządzanie grupą **: możliwości** współpracy

Wprowadziliśmy pewne ulepszenia interfejsu użytkownika dla konstruktora dynamicznej reguły grupy, dostępne w Azure Portal, aby łatwiej skonfigurować nową regułę lub zmienić istniejące reguły. To ulepszenie projektu pozwala tworzyć reguły z maksymalnie pięcioma wyrażeniami zamiast tylko jeden. Zaktualizowaliśmy także listę właściwości urządzenia w celu usunięcia przestarzałych właściwości urządzenia.

Aby uzyskać więcej informacji, zobacz [Zarządzanie dynamicznymi regułami członkostwa](../users-groups-roles/groups-dynamic-membership.md).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nowe uprawnienie Microsoft Graph aplikacji dostępne do przeglądu dostępu

**Typ:** Zmieniono **kategorię usługi funkcji:** **możliwości produktu** przeglądy dostępu: Zarządzanie tożsamościami

Wprowadziliśmy nowe uprawnienie Microsoft Graph aplikacji, `AccessReview.ReadWrite.Membership` co umożliwia aplikacjom automatyczne tworzenie i pobieranie przeglądów dostępu dla członkostwa w grupach i przypisań aplikacji. To uprawnienie może być używane przez zaplanowane zadania lub jako część automatyzacji, bez konieczności logowania kontekstu użytkownika.

Aby uzyskać więcej informacji, zobacz [przykład jak utworzyć przeglądy dostępu do usługi Azure AD przy użyciu uprawnień aplikacji Microsoft Graph przy użyciu blogu programu PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Dzienniki aktywności usługi Azure AD są teraz dostępne dla wystąpień chmury dla instytucji rządowych w Azure Monitor

**Typ:** Zmieniono **kategorię usługi funkcji:** raportowanie **możliwości produktu:** monitorowanie & raportowanie

Przyjemnością się ogłaszamy, że dzienniki aktywności usługi Azure AD są teraz dostępne dla wystąpień chmury dla instytucji rządowych w Azure Monitor. Teraz możesz wysyłać dzienniki usługi Azure AD na konto magazynu lub do centrum zdarzeń, aby zintegrować je z narzędziami SIEM, takimi jak [SumoLogic](../reports-monitoring/howto-integrate-activity-logs-with-sumologic.md), [Splunk](../reports-monitoring/howto-integrate-activity-logs-with-splunk.md)i [ArcSight](../reports-monitoring/howto-integrate-activity-logs-with-arcsight.md).

Aby uzyskać więcej informacji na temat konfigurowania Azure Monitor, zobacz [dzienniki aktywności usługi Azure AD w Azure monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Aktualizowanie użytkowników do nowego, ulepszonego środowiska informacji o zabezpieczeniach

**Typ:** Zmieniono **kategorię usługi funkcji:**  uwierzytelnienia (logowania) **możliwości produktu:** uwierzytelnianie użytkownika

25 września 2019 zostanie wyłączone stare, nieulepszone środowisko informacyjne zabezpieczeń na potrzeby rejestrowania i zarządzania informacjami o zabezpieczeniach użytkownika i włączania tylko nowej, [ulepszonej wersji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). Oznacza to, że użytkownicy nie będą już mogli używać starego środowiska.

Aby uzyskać więcej informacji na temat udoskonalonego środowiska informacji o zabezpieczeniach, zapoznaj się z [dokumentacją administratora](https://aka.ms/securityinfodocs) i [dokumentacją użytkownika](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Aby włączyć to nowe środowisko, musisz:

1. Zaloguj się do Azure Portal jako Administrator globalny lub administrator użytkowników.

2. Przejdź do pozycji **Azure Active Directory > ustawienia użytkownika > zarządzanie ustawieniami funkcji Podgląd panelu dostępu**.

3. **Użytkownicy mogą korzystać z funkcji w wersji zapoznawczej na potrzeby rejestrowania obszaru informacje zabezpieczające i zarządzania nim** , wybrać opcję **wybrane**, a następnie wybrać grupę użytkowników lub wybrać opcję **wszystkie** , aby włączyć tę funkcję dla wszystkich użytkowników w dzierżawie.

4. W programie * * użytkownicy mogą używać funkcji w wersji zapoznawczej do rejestrowania i zarządzania zabezpieczeniami * * info * * * * — wybierz opcję **Brak**.

5. Zapisz ustawienia.

    Po zapisaniu ustawień nie będziesz mieć już dostępu do starych informacji zabezpieczających.

>[!Important]
>Jeśli te kroki nie zostaną wykonane przed 25 września 2019, dzierżawa Azure Active Directory zostanie automatycznie włączona dla udoskonalonego środowiska. Jeśli masz pytania, skontaktuj się z nami pod adresem registrationpreview@microsoft.com .

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Żądania uwierzytelniania przy użyciu wpisów logowania będą bardziej ścisłe.

**Typ:** Zmieniono **kategorię usługi funkcji:** uwierzytelnienia (logowania) **możliwości produktu:** standardy

Od 2 września 2019 żądanie uwierzytelnienia przy użyciu metody POST będzie bardziej ściśle zweryfikowane względem standardów protokołu HTTP. W przypadku opcji spacje i podwójne cudzysłowy (") nie będą już usuwane z wartości formularza żądania. Te zmiany nie są oczekiwane na podział istniejących klientów i ułatwiają zapewnienie, że żądania wysyłane do usługi Azure AD są niezawodnie obsługiwane za każdym razem.

Aby uzyskać więcej informacji, zobacz [powiadomienia o zmianach w usłudze Azure AD](../develop/reference-breaking-changes.md#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Lipiec 2019 r.

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Zaplanuj zmianę: Aktualizacja usługi proxy aplikacji do obsługi tylko protokołu TLS 1,2

**Typ:** Zaplanuj **kategorię usługi zmiany:** produkt proxy aplikacji **:** Access Control

Aby zapewnić sobie Najmocniejsze szyfrowanie, zaczniemy ograniczać dostęp usługi serwera proxy aplikacji tylko do protokołów TLS 1,2. To ograniczenie zostanie początkowo wdrożone dla klientów, którzy już korzystają z protokołów TLS 1,2, więc nie zobaczysz wpływu. Zakończenie korzystania z protokołów TLS 1,0 i TLS 1,1 zostanie zakończone 31 sierpnia 2019. Klienci nadal korzystający z protokołów TLS 1,0 i TLS 1,1 otrzymają zaawansowaną informację na temat przygotowania do tej zmiany.

Aby zachować połączenie z usługą serwera proxy aplikacji w ramach tej zmiany, zalecamy, aby upewnić się, że kombinacje na serwerze klienta i przeglądarki zostały zaktualizowane pod kątem korzystania z protokołu TLS 1,2. Zalecamy również, aby dołączać do aplikacji publikowanych za pomocą usługi serwera proxy aplikacji wszystkie systemy klienckie używane przez pracowników.

Aby uzyskać więcej informacji, zobacz [Dodawanie aplikacji lokalnej dla dostępu zdalnego przy użyciu serwera proxy aplikacji w Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Planowanie zmian: Aktualizacje projektu są dostępne dla galerii aplikacji

**Typ:** Planowanie **kategorii usługi zmiany:** aplikacje dla przedsiębiorstw **:** Logowanie jednokrotne

Nowe zmiany w interfejsie użytkownika są nachodzące do projektu **dodawania z obszaru galerii** w bloku **Dodawanie aplikacji** . Te zmiany ułatwią łatwiejsze znajdowanie aplikacji obsługujących automatyczne Inicjowanie obsługi, OpenID Connect Connect, SAML (SAML) i logowanie jednokrotne (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Zaplanuj zmianę: Usuwanie adresu IP serwera usługi MFA z adresu IP pakietu Office 365

**Typ:** Planowanie **kategorii usługi zmiany:** **możliwości produktu MFA:** zabezpieczenia tożsamości & ochrona

Usuwamy adres IP serwera usługi MFA z [usługi sieci Web adresu IP pakietu Office 365 i adresu URL](/office365/enterprise/office-365-ip-web-service). Jeśli obecnie korzystasz z tych stron do aktualizowania ustawień zapory, musisz się upewnić, że zawarto również listę adresów IP udokumentowaną w sekcji **wymagania dotyczące zapory serwer Multi-Factor Authentication systemu Azure** w artykule [wprowadzenie do usługi Azure serwer Multi-Factor Authentication](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements) .

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Tokeny tylko dla aplikacji wymagają teraz, aby aplikacja kliencka istniała w dzierżawie zasobów

**Typ:** **Kategoria stałych usług:** uwierzytelnienia (logowania) **możliwości produktu:** uwierzytelnianie użytkownika

26 lipca 2019 zmieniono sposób dostarczania tokenów tylko aplikacji za pomocą [uprawnień do przydzielenia poświadczeń klienta](../azuread-dev/v1-oauth2-client-creds-grant-flow.md). Wcześniej aplikacje mogły uzyskać tokeny do wywoływania innych aplikacji, niezależnie od tego, czy aplikacja kliencka była w dzierżawie. Zaktualizowaliśmy to zachowanie, więc zasoby o pojedynczej dzierżawie nazywane czasami interfejsami API sieci Web mogą być wywoływane tylko przez aplikacje klienckie, które znajdują się w dzierżawie zasobów.

Jeśli aplikacja nie znajduje się w dzierżawie zasobów, zostanie wyświetlony komunikat o błędzie z informacją `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` o tym, aby rozwiązać ten problem, musisz utworzyć jednostkę usługi App Service w dzierżawie przy użyciu [punktu końcowego zgody użytkownika](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint) lub [za pomocą programu PowerShell](../develop/howto-authenticate-service-principal-powershell.md), co gwarantuje, że dzierżawca uzyska uprawnienia aplikacji do działania w ramach dzierżawy.

Aby uzyskać więcej informacji, zobacz [co nowego w uwierzytelnianiu?](../develop/reference-breaking-changes.md#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Istniejąca zgoda między klientem i interfejsem API nadal nie jest wymagana. Aplikacje powinny nadal przeprowadzać własne testy autoryzacji.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nowe logowanie za pomocą hasła do usługi Azure AD przy użyciu kluczy zabezpieczeń FIDO2

**Typ:** Nowa **Kategoria usługi funkcji:** uwierzytelnienia (logowania) **możliwości produktu:** uwierzytelnianie użytkownika

Klienci usługi Azure AD mogą teraz ustawiać zasady zarządzania kluczami zabezpieczeń FIDO2 dla użytkowników i grup organizacji. Użytkownicy końcowi mogą również rejestrować swoje klucze zabezpieczeń, używać tych kluczy do logowania się do swoich kont Microsoft w witrynach sieci Web na urządzeniach z systemem FIDO, a także do logowania się do urządzeń systemu Windows 10 przyłączonych do usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Włączanie logowania bezhasła w usłudze Azure AD (wersja zapoznawcza)](../authentication/concept-authentication-passwordless.md) w celu uzyskania informacji związanych z administratorem i [Konfigurowanie informacji zabezpieczających do korzystania z klucza zabezpieczeń (wersja zapoznawcza)](../user-help/security-info-setup-security-key.md) dla informacji związanych z użytkownikiem końcowym.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — lipiec 2019

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** integracja z firmą

W lipcu 2019 dodaliśmy te 18 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Ungerboeck Software](../saas-apps/ungerboeck-software-tutorial.md), [jasnozielony wzorzec omnichannel Center contacting](../saas-apps/bright-pattern-omnichannel-contact-center-tutorial.md), [sprytne Nelly](../saas-apps/clever-nelly-tutorial.md), [AcquireIO](../saas-apps/acquireio-tutorial.md), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](../saas-apps/productboard-tutorial.md) [Wandera](../saas-apps/wandera-tutorial.md) [TwineSocial](https://twinesocial.com/) [HyperAnna](../saas-apps/hyperanna-tutorial.md) [Kallidus](../saas-apps/kallidus-tutorial.md) [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html) [Abstract](../saas-apps/abstract-tutorial.md) [Ascentis](../saas-apps/ascentis-tutorial.md) [Hype](../saas-apps/hype-tutorial.md) [ &trade; productboard, MS Azure SSO Access for Ethidex](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md), pogłoskami narosłymi, abstract,, Flipsnack, Wandera, TwineSocial, Kallidus, HyperAnna, [PharmID WasteWitness](https://pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](../saas-apps/jfrog-artifactory-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatyzowanie aprowizacji kont użytkowników dla tych nowo obsługiwanych aplikacji SaaS

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** monitorowanie & raportowanie

Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federated Directory](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzowanie aprowizacji użytkowników do aplikacji SaaS przy użyciu usługi Azure AD](../app-provisioning/user-provisioning.md)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nowy tag usługi Azure AD Domain Services dla sieciowej grupy zabezpieczeń

**Typ:** Nowa **Kategoria usługi funkcji:** Azure AD Domain Services **możliwości produktu:** Azure AD Domain Services

Jeśli nie masz już możliwości zarządzania długimi listami adresów IP i zakresów, możesz użyć nowego tagu usługi sieciowej **AzureActiveDirectoryDomainServices** w grupie zabezpieczeń sieci platformy Azure, aby zabezpieczyć ruch przychodzący do podsieci sieci wirtualnej Azure AD Domain Services.

Aby uzyskać więcej informacji na temat tego nowego tagu usługi, zobacz [sieciowe grupy zabezpieczeń dla Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nowe inspekcje zabezpieczeń dla Azure AD Domain Services (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** Azure AD Domain Services **możliwości produktu:** Azure AD Domain Services

Z przyjemnością ogłaszamy wydanie inspekcji zabezpieczeń usługi Azure AD Domain Service do publicznej wersji zapoznawczej. Inspekcja zabezpieczeń pomaga zapewnić krytyczny wgląd w usługi uwierzytelniania przez przesyłanie strumieniowe zdarzeń inspekcji zabezpieczeń do zasobów kierowanych, w tym usługi Azure Storage, obszarów roboczych usługi Azure Log Analytics i usługi Azure Event Hub przy użyciu portalu usługi Azure AD Domain Service.

Aby uzyskać więcej informacji, zobacz [Włączanie inspekcji zabezpieczeń dla Azure AD Domain Services (wersja zapoznawcza)](../../active-directory-domain-services/security-audit-events.md).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nowe metody uwierzytelniania użycie & Insights (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** funkcja samoobsługowego resetowania hasła **:** monitorowanie & raportowanie

Nowe metody uwierzytelniania & Raporty ułatwiają zrozumienie, jak funkcje takie jak Azure Multi-Factor Authentication i Samoobsługowe resetowanie haseł są rejestrowane i używane w organizacji, w tym liczbę zarejestrowanych użytkowników dla każdej funkcji, jak często funkcja samoobsługowego resetowania hasła jest używana do resetowania haseł i metody resetowania.

Aby uzyskać więcej informacji, zobacz [metody uwierzytelniania użycie & Insights (wersja zapoznawcza)](../authentication/howto-authentication-methods-usage-insights.md).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Nowe raporty zabezpieczeń są dostępne dla wszystkich administratorów usługi Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** **zdolność produktu** do ochrony tożsamości: zabezpieczenia tożsamości & ochrona

Wszyscy Administratorzy usługi Azure AD mogą teraz wybrać transparent w górnej części istniejących raportów zabezpieczeń, takich jak Raport **o podwyższonym ryzyku** , aby rozpocząć korzystanie z nowego środowiska zabezpieczeń, jak pokazano w raportach **ryzykownych użytkowników** i **ryzykownych** logowań. Wraz z upływem czasu wszystkie raporty zabezpieczeń będą przenoszone ze starszych wersji do nowych wersji, a nowe raporty udostępniają następujące dodatkowe możliwości:

- Zaawansowane filtrowanie i sortowanie

- Akcje zbiorcze, takie jak odrzucanie ryzyka użytkownika

- Potwierdzenie ochrony jednostek naruszonych lub bezpiecznych

- Stan ryzyka, obejmujący: zagrożone, odrzucone, korygowane i potwierdzone naruszone

Aby uzyskać więcej informacji, zobacz Raport [dotyczący ryzykownych użytkowników](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users) i [raport dotyczący ryzykownych](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins)logowań.

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nowe inspekcje zabezpieczeń dla Azure AD Domain Services (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** Azure AD Domain Services **możliwości produktu:** Azure AD Domain Services

Z przyjemnością ogłaszamy wydanie inspekcji zabezpieczeń usługi Azure AD Domain Service do publicznej wersji zapoznawczej. Inspekcja zabezpieczeń pomaga zapewnić krytyczny wgląd w usługi uwierzytelniania przez przesyłanie strumieniowe zdarzeń inspekcji zabezpieczeń do zasobów kierowanych, w tym usługi Azure Storage, obszarów roboczych usługi Azure Log Analytics i usługi Azure Event Hub przy użyciu portalu usługi Azure AD Domain Service.

Aby uzyskać więcej informacji, zobacz [Włączanie inspekcji zabezpieczeń dla Azure AD Domain Services (wersja zapoznawcza)](../../active-directory-domain-services/security-audit-events.md).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nowa Federacja usługi B2B Direct z użyciem protokołu SAML/WS-karmionego (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** **produkt B2B:** B2B/B2C

Federacja bezpośrednia pomaga ułatwić współpracę z partnerami, których rozwiązanie do zarządzania tożsamościami zarządzanymi przez dział IT nie jest usługą Azure AD, pracując z systemami tożsamości, które obsługują standardy SAML lub WS-Fed. Po skonfigurowaniu bezpośredniej relacji Federacji z partnerem każdy nowy użytkownik-Gość, który Cię zaprosił z tej domeny, może współpracować z użytkownikiem przy użyciu istniejącego konta organizacji, dzięki czemu środowisko użytkownika dla Gości jest bardziej bezproblemowe.

Aby uzyskać więcej informacji, zobacz [bezpośrednia Federacja z AD FSami i dostawcami innych firm dla użytkowników-Gości (wersja zapoznawcza)](../external-identities/direct-federation.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatyzowanie aprowizacji kont użytkowników dla tych nowo obsługiwanych aplikacji SaaS

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** monitorowanie & raportowanie

Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federated Directory](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzacja aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nowe sprawdzenie dla zduplikowanych nazw grup w portalu usługi Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** funkcja zarządzania grupami **:** współpraca

Teraz po utworzeniu lub zaktualizowaniu nazwy grupy w portalu usługi Azure AD sprawdzimy, czy duplikat istniejącej nazwy grupy jest duplikowany w zasobie. Jeśli okaże się, że nazwa jest już używana przez inną grupę, zostanie wyświetlony monit o zmianę nazwy.

Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami w portalu usługi Azure AD](./active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Usługa Azure AD obsługuje teraz statyczne parametry zapytań w identyfikatorach URI odpowiedzi (redirect)

**Typ:** Nowa **Kategoria usługi funkcji:** uwierzytelnienia (logowania) **możliwości produktu:** uwierzytelnianie użytkownika

Aplikacje usługi Azure AD mogą teraz rejestrować i używać identyfikatorów URI odpowiedzi (redirect) przy użyciu statycznych parametrów zapytania (na przykład `https://contoso.com/oauth2?idp=microsoft` ) dla żądań OAuth 2,0. Statyczny parametr zapytania jest uzależniony od ciągu dla identyfikatorów URI odpowiedzi, podobnie jak każda inna część identyfikatora URI odpowiedzi. Jeśli nie ma żadnego zarejestrowanego ciągu zgodnego z zdekodowanym adresem URL, żądanie zostanie odrzucone. Jeśli zostanie znaleziony identyfikator URI odpowiedzi, cały ciąg jest używany do przekierowania użytkownika, łącznie z parametrem zapytania statycznego.

Dynamiczne identyfikatory URI odpowiedzi są nadal zabronione, ponieważ stanowią zagrożenie bezpieczeństwa i nie mogą być używane do przechowywania informacji o stanie w ramach żądania uwierzytelnienia. W tym celu należy użyć `state` parametru.

Obecnie ekrany rejestracji aplikacji Azure Portal nadal blokują parametry zapytania. Można jednak ręcznie edytować manifest aplikacji, aby dodawać i testować parametry zapytania w aplikacji. Aby uzyskać więcej informacji, zobacz [co nowego w uwierzytelnianiu?](../develop/reference-breaking-changes.md#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Dzienniki aktywności (interfejsy API MS Graph) dla usługi Azure AD są teraz dostępne za pomocą poleceń cmdlet programu PowerShell

**Typ:** Nowa **Kategoria usługi funkcji:** raportowanie **możliwości produktu:** monitorowanie & raportowanie

Przyjemnością ogłaszamy, że dzienniki aktywności usługi Azure AD (raporty inspekcji i logowania) są teraz dostępne za pomocą modułu Azure AD PowerShell. Wcześniej można było tworzyć własne skrypty za pomocą punktów końcowych MS interfejs API programu Graph i teraz rozszerzamy tę możliwość na polecenia cmdlet programu PowerShell.

Aby uzyskać więcej informacji na temat sposobu korzystania z tych poleceń cmdlet, zobacz [polecenia cmdlet programu PowerShell usługi Azure AD na potrzeby raportowania](../reports-monitoring/reference-powershell-reporting.md).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Zaktualizowano kontrolki filtru dla dzienników inspekcji i logowania w usłudze Azure AD

**Typ:** Zmieniono **kategorię usługi funkcji:** raportowanie **możliwości produktu:** monitorowanie & raportowanie

Zaktualizowaliśmy raporty inspekcji i logowania, dzięki czemu można teraz zastosować różne filtry bez konieczności dodawania ich jako kolumn na ekranach raportów. Ponadto możesz wybrać liczbę filtrów, które mają być wyświetlane na ekranie. Wszystkie te aktualizacje współpracują ze sobą, aby ułatwić ich odczytywanie i zwiększanie zakresu do Twoich potrzeb.

Aby uzyskać więcej informacji o tych aktualizacjach, zobacz [filtrowanie dzienników inspekcji](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) i [filtrowanie działań związanych z logowaniem](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

---

## <a name="june-2019"></a>Czerwiec 2019 r.

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nowy interfejs API riskDetections dla Microsoft Graph (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** **zdolność produktu** do ochrony tożsamości: zabezpieczenia tożsamości & ochrona

Z przyjemnością ogłaszamy nowy interfejs API riskDetections dla Microsoft Graph jest teraz dostępny w publicznej wersji zapoznawczej. Ten nowy interfejs API umożliwia wyświetlenie listy wykrytych użytkowników związanych z ochroną tożsamości w organizacji oraz ich wykrycia. Możesz również użyć tego interfejsu API, aby efektywniej wykonywać zapytania dotyczące wykrywania ryzyka, w tym szczegółowe informacje o typie wykrywania, stanie, poziomie i innych.

Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą interfejsu API wykrywania zagrożeń](/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — czerwiec 2019

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** integracja z firmą

W czerwcu 2019 dodaliśmy te 22 nowe aplikacje z obsługą Federacji do galerii aplikacji:

[Azure AD SAML Toolkit](../saas-apps/saml-toolkit-tutorial.md), [Otsuka Shokai (大塚商会)](../saas-apps/otsuka-shokai-tutorial.md), [ANAQUA](../saas-apps/anaqua-tutorial.md), [Klient sieci VPN platformy Azure](https://portal.azure.com/), [wydatki](../saas-apps/expensein-tutorial.md) [, pomocnik](../saas-apps/helper-helper-tutorial.md)pomocnika, [Costpoint](../saas-apps/costpoint-tutorial.md), [GlobalOne](../saas-apps/globalone-tutorial.md), [Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/), [skore](https://app.justskore.it/), [konsola infrastruktury Oracle Cloud Infrastructure](../saas-apps/oracle-cloud-tutorial.md), [CyberArk Authentication SAML](../saas-apps/cyberark-saml-authentication-tutorial.md), [Scrible edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](../saas-apps/pandadoc-tutorial.md), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), Proptimise [OS](https://proptimise.co.uk/software/), [vtiger CRM (SAML)](../saas-apps/vtiger-crm-saml-tutorial.md), Oracle Access Manager for Oracle, Oracle Access Manager dla Oracle webbusiness Suite, Oracle IDCS for E-Business Suite, Oracle IDCS for PeopleSoft, Oracle IDCS for JD Edwards

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatyzowanie aprowizacji kont użytkowników dla tych nowo obsługiwanych aplikacji SaaS

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** monitorowanie & raportowanie

Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

- [Powiększenie](../saas-apps/zoom-provisioning-tutorial.md)

- [Envoy](../saas-apps/envoy-provisioning-tutorial.md)

- [Proxyclick](../saas-apps/proxyclick-provisioning-tutorial.md)

- [4me](../saas-apps/4me-provisioning-tutorial.md)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzowanie aprowizacji użytkowników do aplikacji SaaS przy użyciu usługi Azure AD](../app-provisioning/user-provisioning.md)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Wyświetlanie postępu w czasie rzeczywistym usługi Azure AD Provisioning

**Typ:** Zmieniono **kategorię usługi funkcji:** **możliwości produktu** aprowizacji aplikacji: Zarządzanie cyklem życia tożsamości

Zaktualizowaliśmy środowisko aprowizacji usługi Azure AD, aby uwzględnić nowy pasek postępu, który pokazuje, jak daleko jesteś w procesie aprowizacji użytkowników. To zaktualizowane środowisko zawiera również informacje o liczbie użytkowników, których zainicjowano w bieżącym cyklu, a także o tym, jak wiele użytkowników zostało zainicjowanych do tej pory.

Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu aprowizacji użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Znakowanie firmowe pojawia się teraz na ekranach Wyloguj się i błędy

**Typ:** Zmieniono **kategorię usługi funkcji:** uwierzytelnienia (logowania) **możliwości produktu:** uwierzytelnianie użytkownika

Zaktualizowaliśmy usługę Azure AD, dzięki czemu logo firmy pojawia się teraz na ekranach Wyloguj i błędy, a także na stronie logowania. Nie musisz nic robić, aby włączyć tę funkcję, usługa Azure AD po prostu korzysta z zasobów, które zostały już skonfigurowane w obszarze **znakowania firmowego** Azure Portal.

Aby uzyskać więcej informacji na temat konfigurowania znakowania firmowego, zobacz [Dodawanie znakowania do stron Azure Active Directory organizacji](./customize-branding.md).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Serwer usługi Azure Multi-Factor Authentication (MFA) nie jest już dostępny dla nowych wdrożeń

**Typ:** Kategoria przestarzałych **usług:** **możliwości produktu MFA:** zabezpieczenia tożsamości & ochrona

Od 1 lipca 2019 firma Microsoft nie będzie już oferować serwera MFA dla nowych wdrożeń. Nowi klienci, którzy chcą wymagać uwierzytelniania wieloskładnikowego w organizacji, muszą teraz korzystać z usługi Azure Multi-Factor Authentication opartej na chmurze. Klienci, którzy aktywowali serwer usługi MFA przed 1 lipca, nie zobaczą zmiany. Nadal będzie można pobrać najnowszą wersję, pobrać przyszłe aktualizacje i wygenerować poświadczenia aktywacji.

Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą Azure serwer Multi-Factor Authentication](../authentication/howto-mfaserver-deploy.md). Aby uzyskać więcej informacji na temat usługi Azure Multi-Factor Authentication opartej na chmurze, zobacz [Planowanie wdrożenia usługi azure Multi-Factor Authentication opartego na chmurze](../authentication/howto-mfa-getstarted.md).

---

## <a name="may-2019"></a>Maj 2019 r.

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Zmiana usługi: w przyszłości obsługuje tylko protokoły TLS 1,2 w usłudze serwera proxy aplikacji.

**Typ:** Zaplanuj **kategorię usługi zmiany:** produkt proxy aplikacji **:** Access Control

Aby pomóc w zapewnieniu najlepszej klasy szyfrowania dla naszych klientów, ograniczamy dostęp do protokołów TLS 1,2 w usłudze serwera proxy aplikacji. Ta zmiana jest stopniowo wprowadzana do klientów, którzy już korzystają z protokołów TLS 1,2, dlatego nie będzie można zobaczyć żadnych zmian.

Wycofanie protokołu TLS 1,0 i TLS 1,1 występuje 31 sierpnia 2019, ale postanowimy dodatkowe zaawansowane powiadomienie, dzięki czemu będziesz mieć czas na przygotowanie się do tej zmiany. Aby przygotować się do tej zmiany, upewnij się, że kombinacja klient-serwer i przeglądarka-serwer, łącznie z klientami używanymi przez użytkowników do uzyskiwania dostępu do aplikacji publikowanych za pomocą serwera proxy aplikacji, zostały zaktualizowane do korzystania z protokołu TLS 1,2 do obsługi połączenia z usługą serwera proxy aplikacji. Aby uzyskać więcej informacji, zobacz [Dodawanie aplikacji lokalnej dla dostępu zdalnego przy użyciu serwera proxy aplikacji w Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md#prerequisites).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Użyj raportu Użycie i szczegółowe informacje, aby wyświetlić dane logowania związane z aplikacją

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** monitorowanie & raportowanie

Możesz teraz użyć raportu Użycie i szczegółowe informacje znajdującego się w obszarze aplikacje dla **przedsiębiorstw** Azure Portal, aby uzyskać widok skoncentrowany na aplikacji, w tym informacje o:

- Najpopularniejsze używane aplikacje dla Twojej organizacji

- Aplikacje z największą liczbą nieudanych logowań

- Najważniejsze błędy logowania dla każdej aplikacji

Aby uzyskać więcej informacji na temat tej funkcji, zobacz [raport dotyczący użycia i usługi Insights w portalu Azure Active Directory](../reports-monitoring/concept-usage-insights-report.md)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatyzowanie aprowizacji użytkowników w aplikacjach w chmurze przy użyciu usługi Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** monitorowanie & raportowanie

Postępuj zgodnie z tymi nowymi samouczkami, aby użyć usługi Azure AD Provisioning w celu zautomatyzowania tworzenia, usuwania i aktualizowania kont użytkowników dla następujących aplikacji opartych na chmurze:

- [Współspotykanie](../saas-apps/comeet-recruiting-software-provisioning-tutorial.md)

- [DynamicSignal](../saas-apps/dynamic-signal-provisioning-tutorial.md)

- [KeeperSecurity](../saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial.md)

Możesz również wykonać czynności opisane w [samouczku](../saas-apps/dropboxforbusiness-provisioning-tutorial.md)dotyczącym usługi Dropbox, który zawiera informacje o sposobach aprowizacji obiektów grup.

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przez automatyczne Inicjowanie obsługi kont użytkowników, zobacz [Automatyzowanie aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Ocena bezpiecznego tożsamości jest teraz dostępna w usłudze Azure AD (ogólna dostępność)

**Typ:** Nowa **Kategoria usługi funkcji:** brak **możliwości produktu:** zabezpieczenia tożsamości & ochrona

Teraz można monitorować i ulepszać stan zabezpieczeń tożsamości za pomocą funkcji "Funkcja bezpiecznego oceny" w usłudze Azure AD. Funkcja oceny zabezpieczeń tożsamości używa jednego pulpitu nawigacyjnego, aby ułatwić:

- Obiektywnie Zmierz stan zabezpieczeń tożsamości na podstawie wyniku z zakresu od 1 do 223.

- Planowanie ulepszeń w zakresie zabezpieczeń tożsamości

- Zapoznaj się z sukcesem ulepszeń zabezpieczeń

Aby uzyskać więcej informacji na temat funkcji oceny zabezpieczeń tożsamości, zobacz artykuł [co to jest bezpieczna ocena tożsamości w Azure Active Directory?](./identity-secure-score.md).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nowe środowisko Rejestracje aplikacji jest teraz dostępne (ogólna dostępność)

**Typ:** Nowa **Kategoria usługi funkcji:** uwierzytelnienia (logowania) **możliwości produktu:** środowisko programistyczne

Nowe środowisko [rejestracje aplikacji](https://aka.ms/appregistrations) jest teraz ogólnie dostępne. To nowe środowisko obejmuje wszystkie kluczowe funkcje, które znasz z Azure Portal i portalu rejestracji aplikacji, i ulepszają je w następujący sposób:

- **Lepsza Zarządzanie aplikacjami.** Zamiast oglądać aplikacje w różnych portalach, możesz teraz zobaczyć wszystkie Twoje aplikacje w jednej lokalizacji.

- **Uproszczona rejestracja aplikacji.** W ulepszonym środowisku nawigacyjnym funkcji wyboru uprawnień odnowionych teraz łatwiej jest zarejestrować aplikacje i zarządzać nimi.

- **Bardziej szczegółowe informacje.** Możesz znaleźć więcej szczegółowych informacji o aplikacji, w tym przewodnikach Szybki Start i nie tylko.

Aby uzyskać więcej informacji, zobacz [Microsoft Identity platform](../develop/index.yml) i [środowisko rejestracje aplikacji są teraz ogólnie dostępne!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) ogłoszenie blogu.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nowe funkcje dostępne w interfejsie API użytkowników ryzykownych usługi Identity Protection

**Typ:** Nowa **Kategoria usługi funkcji:** **zdolność produktu** do ochrony tożsamości: zabezpieczenia tożsamości & ochrona

Z przyjemnością ogłaszamy, że możesz teraz używać interfejsu API ryzykownych użytkowników, aby pobierać historię ryzyka użytkowników, odrzucać ryzykownych użytkowników i potwierdzać bezpieczeństwo użytkowników. Ta zmiana ułatwia efektywniejsze aktualizowanie stanu ryzyka dla użytkowników i zrozumienie ich historii ryzyka.

Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą interfejsu API ryzykownych użytkowników](/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — maj 2019

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** integracja z firmą

W maju 2019 dodaliśmy następujące 21 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Freedcamp](../saas-apps/freedcamp-tutorial.md), [prawdziwe linki](../saas-apps/real-links-tutorial.md), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Signer](../saas-apps/simple-sign-tutorial.md), [Braze](../saas-apps/braze-tutorial.md), [Display](../saas-apps/displayr-tutorial.md), [TEMPLAFY](../saas-apps/templafy-tutorial.md), [Marketo](https://toutapp.com/login),, [ACLP](../saas-apps/aclp-tutorial.md), [systemy](../saas-apps/outsystems-tutorial.md),, META4 [Global HR](../saas-apps/meta4-global-hr-tutorial.md), [miejsce pracy](../saas-apps/quantum-workplace-tutorial.md)w usłudze Quantum, [, interfejsy](../saas-apps/cobalt-tutorial.md) [API usługi webMethods](../saas-apps/webmethods-integration-cloud-tutorial.md), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](../saas-apps/whatfix-tutorial.md), [Control](../saas-apps/control-tutorial.md), [JOBHUB](../saas-apps/jobhub-tutorial.md), [NEOGOV](../saas-apps/neogov-tutorial.md), [Foodee](../saas-apps/foodee-tutorial.md), [MyVR](../saas-apps/myvr-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Udoskonalone środowiska tworzenia i zarządzania grupami w portalu usługi Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** funkcja zarządzania grupami **:** współpraca

Wprowadzono ulepszenia dotyczące środowisk związanych z grupami w portalu usługi Azure AD. Te udoskonalenia umożliwiają administratorom lepsze zarządzanie listami grup, listami członków i udostępnianiem dodatkowych opcji tworzenia.

Wprowadzono następujące ulepszenia:

- Podstawowe filtrowanie według typu członkostwa i typu grupy.

- Dodanie nowych kolumn, takich jak źródło i adres E-mail.

- Możliwość łatwego usuwania grup, elementów członkowskich i list właścicieli.

- Możliwość wybrania adresu e-mail i dodania właścicieli podczas tworzenia grupy.

Aby uzyskać więcej informacji, zobacz [Tworzenie podstawowej grupy i dodawanie członków w usłudze Azure Active Directory](./active-directory-groups-create-azure-portal.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Konfigurowanie zasad nazewnictwa dla grup Office 365 w portalu usługi Azure AD (ogólna dostępność)

**Typ:** Zmieniono **kategorię usługi funkcji:** Zarządzanie grupą **: możliwości** współpracy

Administratorzy mogą teraz konfigurować zasady nazewnictwa dla grup pakietu Office 365 przy użyciu portalu usługi Azure AD. Ta zmiana pomaga wymusić spójne konwencje nazewnictwa dla grup pakietu Office 365 utworzonych lub edytowanych przez użytkowników w organizacji.

Zasady nazewnictwa dla grup pakietu Office 365 można skonfigurować na dwa różne sposoby:

- Zdefiniuj prefiksy lub sufiksy, które są automatycznie dodawane do nazwy grupy.

- Przekaż dostosowany zestaw zablokowanych słów dla swojej organizacji, które nie są dozwolone w nazwach grup (na przykład "dyrektor generalny, ListaPłac, HR").

Aby uzyskać więcej informacji, zobacz [wymuszanie zasad nazewnictwa dla grup pakietu Office 365](../users-groups-roles/groups-naming-policy.md).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Punkty końcowe interfejsu API Microsoft Graph są teraz dostępne dla dzienników aktywności usługi Azure AD (ogólna dostępność)

**Typ:** Zmieniono **kategorię usługi funkcji:** raportowanie **możliwości produktu:** monitorowanie & raportowanie

Z przyjemnością ogłaszamy ogólną dostępność Microsoft Graph punktów końcowych interfejsu API dla dzienników aktywności usługi Azure AD. W tej wersji można teraz używać wersji 1,0 zarówno dzienników inspekcji usługi Azure AD, jak i interfejsów API logowania.

Aby uzyskać więcej informacji, zobacz [Omówienie interfejsu API dziennika inspekcji usługi Azure AD](/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Administratorzy mogą teraz korzystać z dostępu warunkowego dla połączonego procesu rejestracji (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** dostęp warunkowy do **produktu:** zabezpieczenia tożsamości & ochrona

Administratorzy mogą teraz tworzyć zasady dostępu warunkowego do użycia przez łączną stronę rejestracji. Obejmuje to zastosowanie zasad umożliwiających rejestrację, jeśli:

- Użytkownicy znajdują się w zaufanej sieci.

- Użytkownicy są niskim ryzykiem związanym z logowaniem.

- Użytkownicy znajdują się na zarządzanym urządzeniu.

- Użytkownicy zgadzają się na warunki użytkowania organizacji (warunków użytkowania).

Aby uzyskać więcej informacji na temat dostępu warunkowego i resetowania hasła, można zobaczyć [dostęp warunkowy do wpisu w blogu dotyczącego połączenia MFA i rejestracji resetowania haseł usługi Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Aby uzyskać więcej informacji na temat zasad dostępu warunkowego dla połączonego procesu rejestracji, zobacz [zasady dostępu warunkowego dla połączonej rejestracji](../authentication/howto-registration-mfa-sspr-combined.md#conditional-access-policies-for-combined-registration). Aby uzyskać więcej informacji na temat funkcji warunków użytkowania usługi Azure AD, zobacz [Azure Active Directory warunki użytkowania](../conditional-access/terms-of-use.md).

---

## <a name="april-2019"></a>Kwiecień 2019 r.

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Nowe wykrywanie analizy zagrożeń w usłudze Azure AD jest teraz dostępne w ramach Azure AD Identity Protection

**Typ:** Nowa **Kategoria usługi funkcji:** Azure AD Identity Protection **możliwości produktu:** zabezpieczenia tożsamości & ochrona

Funkcja wykrywania analizy zagrożeń w usłudze Azure AD jest teraz dostępna w ramach zaktualizowanej Azure AD Identity Protection funkcji. Ta nowa funkcja pomaga wskazać nietypowe działania użytkownika dla określonego użytkownika lub działania, które są zgodne ze znanymi wzorcami ataków na podstawie wewnętrznych i zewnętrznych źródeł analizy zagrożeń firmy Microsoft.

Aby uzyskać więcej informacji na temat odświeżonej wersji Azure AD Identity Protection, zobacz [cztery główne Azure AD Identity Protection ulepszenia znajdują się teraz w publicznym blogu w wersji zapoznawczej](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) , a [co to jest Azure Active Directory Identity Protection (odświeżane)?](../identity-protection/overview-identity-protection.md) (Jak działa usługa Azure RMS). Aby uzyskać więcej informacji o wykrywaniu analizy zagrożeń w usłudze Azure AD, zobacz artykuł dotyczący [wykrywania zagrożeń Azure Active Directory Identity Protection](../identity-protection/concept-identity-protection-risks.md) .

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Zarządzanie prawami w usłudze Azure AD jest teraz dostępne (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** **możliwości produktu** ładu tożsamości: Zarządzanie tożsamościami

Zarządzanie prawami usługi Azure AD, teraz w publicznej wersji zapoznawczej, ułatwia klientom delegowanie zarządzania pakietami dostępu, które określają sposób, w jaki pracownicy i partnerzy biznesowi mogą żądać dostępu, którzy muszą zatwierdzić i jak długo mają dostęp. Pakiety dostępu mogą zarządzać członkostwem w grupach usługi Azure AD i Office 365, przypisaniach ról w aplikacjach dla przedsiębiorstw i przypisaniach ról dla witryn programu SharePoint Online. Przeczytaj więcej na temat zarządzania uprawnieniami w temacie [Omówienie zarządzania prawami usługi Azure AD](../governance/entitlement-management-overview.md). Aby dowiedzieć się więcej o szerokiej części funkcji Azure AD Identity Governance, w tym o Privileged Identity Management, przeglądach dostępu i warunkach użytkowania, zobacz [co to jest Azure AD Identity Governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Konfigurowanie zasad nazewnictwa dla grup Office 365 w portalu usługi Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** funkcja zarządzania grupami **:** współpraca

Administratorzy mogą teraz konfigurować zasady nazewnictwa dla grup pakietu Office 365 przy użyciu portalu usługi Azure AD. Ta zmiana pomaga wymusić spójne konwencje nazewnictwa dla grup pakietu Office 365 utworzonych lub edytowanych przez użytkowników w organizacji.

Zasady nazewnictwa dla grup pakietu Office 365 można skonfigurować na dwa różne sposoby:

- Zdefiniuj prefiksy lub sufiksy, które są automatycznie dodawane do nazwy grupy.

- Przekaż dostosowany zestaw zablokowanych słów dla swojej organizacji, które nie są dozwolone w nazwach grup (na przykład "dyrektor generalny, ListaPłac, HR").

Aby uzyskać więcej informacji, zobacz [wymuszanie zasad nazewnictwa dla grup pakietu Office 365](../users-groups-roles/groups-naming-policy.md).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Dzienniki aktywności usługi Azure AD są teraz dostępne w Azure Monitor (ogólna dostępność)

**Typ:** Nowa **Kategoria usługi funkcji:** raportowanie **możliwości produktu:** monitorowanie & raportowanie

Aby pomóc w rozwiązaniu opinii na temat wizualizacji z dziennikami aktywności usługi Azure AD, wprowadzamy nową funkcję Insights w Log Analytics. Ta funkcja ułatwia uzyskiwanie szczegółowych informacji o zasobach usługi Azure AD przy użyciu interaktywnych szablonów, nazywanych skoroszytami. Te wstępnie skompilowane skoroszyty mogą zawierać szczegóły dotyczące aplikacji lub użytkowników, a także:

- **Logowania.** Zawiera szczegółowe informacje dotyczące aplikacji i użytkowników, w tym lokalizacji logowania, systemu operacyjnego lub klienta przeglądarki oraz wersji, a także liczbę logowań zakończonych powodzeniem lub niepowodzeniem.

- **Starsza wersja uwierzytelniania i dostęp warunkowy.** Zawiera szczegółowe informacje dotyczące aplikacji i użytkowników korzystających ze starszego uwierzytelniania, w tym Multi-Factor Authentication użycie wyzwalane przez zasady dostępu warunkowego, aplikacje korzystające z zasad dostępu warunkowego itd.

- **Analiza niepowodzeń logowania.** Pomaga ustalić, czy występują błędy logowania z powodu akcji użytkownika, problemów z zasadami lub infrastruktury.

- **Raporty niestandardowe.** Możesz tworzyć nowe lub edytować istniejące skoroszyty, aby pomóc w dostosowaniu funkcji wglądu w dane organizacji.

Aby uzyskać więcej informacji, zobacz [jak używać skoroszytów Azure monitor dla Azure Active Directory raportów](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — kwiecień 2019

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** integracja z firmą

W kwietniu 2019 dodaliśmy następujące 21 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[SAP Fiori](../saas-apps/sap-fiori-tutorial.md), [HRworks logowania](../saas-apps/hrworks-single-sign-on-tutorial.md)jednokrotnego, [Percolate](../saas-apps/percolate-tutorial.md), [MobiControl](../saas-apps/mobicontrol-tutorial.md), [Citrix](../saas-apps/citrix-netscaler-tutorial.md), [Shibumi](../saas-apps/shibumi-tutorial.md), [kanaper](../saas-apps/benchling-tutorial.md), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](../saas-apps/pagedna-tutorial.md), [EduBrite](../saas-apps/edubrite-lms-tutorial.md), [RStudio](../saas-apps/rstudio-connect-tutorial.md), AMMS, e [AMMS](../saas-apps/amms-tutorial.md), e [Indiggo](https://indiggolead.com/) [SurveyMonkey Enterprise](../saas-apps/surveymonkey-enterprise-tutorial.md) [, Alibaba](../saas-apps/greenorbit-tutorial.md) [Cloud (oparta na rolach — Logowanie jednokrotne)](../saas-apps/alibaba-cloud-service-role-based-sso-tutorial.md), [Certent Management](../saas-apps/certent-equity-management-tutorial.md) [, Sectigo](../saas-apps/sectigo-certificate-manager-tutorial.md) [Mitel Connect](../saas-apps/mitel-connect-tutorial.md) [Workgrid](../saas-apps/workgrid-tutorial.md) [monday.com](../saas-apps/mondaycom-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nowa opcja częstotliwości przeglądy dostępu i wybór wielu ról

**Typ:** Nowa **Kategoria usługi funkcji:** **możliwości produktu** przeglądy dostępu: Zarządzanie tożsamościami

Nowe aktualizacje w przeglądach dostępu usługi Azure AD umożliwiają:

- Oprócz wcześniej istniejących opcji co tydzień, co miesiąc, co kwartał i co rok należy zmienić częstotliwość kontroli dostępu do **półrocza**.

- Podczas tworzenia jednego przeglądu dostępu wybierz wiele ról usługi Azure AD i Azure Resource. W tej sytuacji wszystkie role są skonfigurowane z tymi samymi ustawieniami, a wszyscy recenzenci są powiadamiani w tym samym czasie.

Aby uzyskać więcej informacji na temat sposobu tworzenia przeglądu dostępu, zobacz [Tworzenie przeglądu dostępu do grup lub aplikacji w przeglądach dostępu usługi Azure AD](../governance/create-access-review.md).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect przechodzenia do systemu alertów poczty e-mail, co umożliwia wysyłanie nowych informacji o nadawcy wiadomości e-mail dla niektórych klientów

**Typ:** Zmieniono **kategorię usługi funkcji:** AD Sync **możliwości produktu:** platforma

Azure AD Connect jest w trakcie przechodzenia do naszych systemów alertów e-mail, co może spowodować, że niektórzy klienci są nowym nadawcą poczty e-mail. Aby rozwiązać ten problemy, należy dodać `azure-noreply@microsoft.com` do listy dozwolonych organizacji lub nie będzie można kontynuować otrzymywania ważnych alertów z pakietu Office 365, platformy Azure ani usług synchronizacji.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Zmiany sufiksu UPN są teraz pomyślne między domenami federacyjnymi w Azure AD Connect

**Typ:** **Kategoria stałej usługi:** AD Sync **możliwości produktu:** platforma

Teraz można pomyślnie zmienić sufiks nazwy UPN użytkownika z jednej domeny federacyjnej na inną domenę federacyjną w Azure AD Connect. Ta poprawka oznacza, że w trakcie cyklu synchronizacji nie ma już komunikatu o błędzie FederatedDomainChangeError lub odebrać wiadomość e-mail z powiadomieniem "nie można zaktualizować tego obiektu w Azure Active Directory, ponieważ atrybut [FederatedUser. UserPrincipalName] jest nieprawidłowy. Zaktualizuj wartość w lokalnych usługach katalogowych.

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów podczas synchronizacji](../hybrid/tshoot-connect-sync-errors.md#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Zwiększone bezpieczeństwo przy użyciu zasad dostępu warunkowego opartego na ochronie aplikacji w usłudze Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** dostęp warunkowy do **produktu:** zabezpieczenia tożsamości & ochrona

Dostęp warunkowy oparty na ochronie aplikacji jest teraz dostępny przy użyciu zasad **Wymagaj ochrony aplikacji** . Nowe zasady ułatwiają zwiększenie bezpieczeństwa organizacji, pomagając w uniknięciu:

- Użytkownicy uzyskują dostęp do aplikacji bez licencji na Microsoft Intune.

- Użytkownicy nie mogą uzyskać Microsoft Intune zasad ochrony aplikacji.

- Użytkownicy uzyskują dostęp do aplikacji bez skonfigurowanych zasad ochrony aplikacji Microsoft Intune.

Aby uzyskać więcej informacji, zobacz [jak wymagać zasad ochrony aplikacji dla dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego](../conditional-access/app-protection-based-conditional-access.md).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nowa obsługa logowania jednokrotnego w usłudze Azure AD i dostępu warunkowego w programie Microsoft Edge (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** dostęp warunkowy do **produktu:** zabezpieczenia tożsamości & ochrona

Ulepszono obsługę usługi Azure AD dla przeglądarki Microsoft Edge, w tym zapewnianie nowej obsługi logowania jednokrotnego usługi Azure AD i dostępu warunkowego. Jeśli wcześniej używasz Managed Browser Microsoft Intune, możesz teraz użyć przeglądarki Microsoft Edge.

Aby uzyskać więcej informacji na temat konfigurowania urządzeń i aplikacji oraz zarządzania nimi przy użyciu dostępu warunkowego, zobacz [Wymagaj zarządzanych urządzeń dla dostępu do aplikacji w chmurze z dostępem warunkowym](../conditional-access/require-managed-devices.md) i [Wymagaj zatwierdzonych aplikacji klienckich do dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego](../conditional-access/app-based-conditional-access.md). Aby uzyskać więcej informacji o sposobach zarządzania dostępem przy użyciu programu Microsoft Edge z zasadami Microsoft Intune, zobacz [Zarządzanie dostępem do Internetu za pomocą przeglądarki Microsoft Intune chronionej przez zasady](/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Marzec 2019 r.

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Platforma obsługi tożsamości i obsługa zasad niestandardowych w programie Azure Active Directory B2C jest teraz dostępna (GA)

**Typ:** Nowa **Kategoria usługi funkcji:** B2C — **możliwości produktu** zarządzania tożsamościami konsumenta: B2B/B2C

Teraz można tworzyć niestandardowe zasady w Azure AD B2C, w tym następujące zadania, które są obsługiwane na skalę i w ramach umowy SLA platformy Azure:

- Tworzenie i przekazywanie niestandardowych podróży użytkowników uwierzytelniania przy użyciu zasad niestandardowych.

- Opisz przedziały użytkownika krok po kroku jako wymianę między dostawcami oświadczeń.

- Definiowanie rozgałęziania warunkowego w podróży użytkownika.

- Przekształcaj i Mapuj oświadczenia do użycia w decyzjach i komunikacji w czasie rzeczywistym.

- Korzystanie z usług z obsługą interfejsu API REST w przypadku niestandardowych podróży użytkowników uwierzytelniania. Na przykład z dostawcami poczty e-mail, CRMs i systemami autoryzacji własnościowej.

- Sfederować z dostawcami tożsamości, którzy są zgodni z protokołem OpenIDConnect. Na przykład z wieloma dzierżawcami usługi Azure AD, dostawcami kont społecznościowych lub dostawcami weryfikacji dwuskładnikowej.

Aby uzyskać więcej informacji na temat tworzenia zasad niestandardowych, zobacz [uwagi dla deweloperów dotyczące zasad niestandardowych w Azure Active Directory B2C](../../active-directory-b2c/custom-policy-developer-notes.md) i przeczytaj [wpis w blogu pozostałej Simon, w tym analizy przypadków](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — marzec 2019

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** integracja z firmą

W marcu 2019 dodaliśmy te 14 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](../saas-apps/eplatform-tutorial.md), [Fulcrum](../saas-apps/fulcrum-tutorial.md), [ExcelityGlobal](../saas-apps/excelityglobal-tutorial.md), [system inspekcji oparty na wyjaśnieniu](../saas-apps/explanation-based-auditing-system-tutorial.md), [Leanion](../saas-apps/lean-tutorial.md), [Powerschool — zagadnienia dotyczące wydajności](../saas-apps/powerschool-performance-matters-tutorial.md), [Cinode](https://cinode.com/), [Sieć intranetowa Iris](../saas-apps/iris-intranet-tutorial.md), [Empactis](../saas-apps/empactis-tutorial.md), [SmartDraw](../saas-apps/smartdraw-tutorial.md), [Confirmit Horizons](../saas-apps/confirmit-horizons-tutorial.md) [, zadanie](../saas-apps/tas-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nowe łączniki inicjowania obsługi rozwiązania Zscaler i Atlassian w galerii usługi Azure AD — marzec 2019

**Typ:** Nowa **Kategoria usługi funkcji:** funkcja aprowizacji aplikacji w **produkcie:** integracja innej firmy

Automatyzuj tworzenie, aktualizowanie i usuwanie kont użytkowników dla następujących aplikacji:

[Rozwiązania Zscaler](https://aka.ms/ZscalerProvisioning), [rozwiązania Zscaler beta](https://aka.ms/ZscalerBetaProvisioning), [rozwiązania Zscaler One](https://aka.ms/ZscalerOneProvisioning), [rozwiązania Zscaler dwa](https://aka.ms/ZscalerTwoProvisioning), [rozwiązania Zscaler trzy](https://aka.ms/ZscalerThreeProvisioning), [rozwiązania Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przez automatyczne Inicjowanie obsługi kont użytkowników, zobacz [Automatyzowanie aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Przywracanie usuniętych grup programu Office 365 i zarządzanie nimi w portalu usługi Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** funkcja zarządzania grupami **:** współpraca

Można teraz wyświetlać usunięte grupy programu Office 365 i zarządzać nimi z poziomu portalu usługi Azure AD. Ta zmiana ułatwia sprawdzenie, które grupy są dostępne do przywrócenia, a także umożliwia trwałe usunięcie wszystkich grup, które nie są potrzebne w organizacji.

Aby uzyskać więcej informacji, zobacz [przywracanie wygasłych lub usuniętych grup](../users-groups-roles/groups-restore-deleted.md#view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Logowanie jednokrotne jest teraz dostępne dla aplikacji lokalnych opartych na protokole SAML usługi Azure AD za pomocą serwera proxy aplikacji (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** **możliwości produktu** serwera proxy aplikacji: Access Control

Teraz możesz udostępnić Logowanie jednokrotne (SSO) dla aplikacji lokalnych, uwierzytelnianych przy użyciu protokołu SAML oraz dostępu zdalnego do tych aplikacji za pomocą serwera proxy aplikacji. Aby uzyskać więcej informacji na temat sposobu konfigurowania logowania jednokrotnego SAML przy użyciu aplikacji lokalnych, zobacz Usługa [SAML Single Sign-in dla aplikacji lokalnych z serwerem proxy aplikacji (wersja zapoznawcza)](../manage-apps/application-proxy-configure-single-sign-on-on-premises-apps.md).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Pętle aplikacji klienckich w żądaniach zostaną zakłócone w celu poprawy niezawodności i środowiska użytkownika

**Typ:** Nowa **Kategoria usługi funkcji:** uwierzytelnienia (logowania) **możliwości produktu:** uwierzytelnianie użytkownika

Aplikacje klienckie mogą nieprawidłowo wystawiać setki tych samych żądań logowania w krótkim czasie. Te żądania, niezależnie od tego, czy zakończyły się powodzeniem, czy nie, przyczyniają się do słabego środowiska użytkownika i zwiększonego obciążenia dla dostawcy tożsamości, zwiększając opóźnienia dla wszystkich użytkowników i zmniejszając dostępność dostawcy tożsamości.

Ta aktualizacja wysyła `invalid_grant` błąd: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` do aplikacji klienckich, które wydają zduplikowane żądania wiele razy w krótkim czasie, poza zakresem normalnej operacji. Aplikacje klienckie, które napotykają ten problem, powinny wyświetlać interaktywny monit, co wymaga ponownego zalogowania użytkownika. Aby uzyskać więcej informacji na temat tej zmiany i sposobu naprawy aplikacji w przypadku wystąpienia tego błędu, zobacz [co nowego w uwierzytelnianiu?](../develop/reference-breaking-changes.md#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nowe środowisko użytkownika dzienników inspekcji jest teraz dostępne

**Typ:** Zmieniono **kategorię usługi funkcji:** raportowanie **możliwości produktu:** monitorowanie & raportowanie

Utworzyliśmy nową stronę **dzienników inspekcji** usługi Azure AD, która ułatwia zwiększenie czytelności i sposób wyszukiwania informacji. Aby wyświetlić stronę nowe **dzienniki inspekcji** , wybierz pozycję **dzienniki inspekcji** w sekcji **aktywność** usługi Azure AD.

![Nowa strona dzienników inspekcji z przykładowymi informacjami](media/whats-new/audit-logs-page.png)

Aby uzyskać więcej informacji na temat nowych **dzienników inspekcji** , zobacz [Raporty aktywności inspekcji w portalu Azure Active Directory](../reports-monitoring/concept-audit-logs.md#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nowe ostrzeżenia i wskazówki pomagające w zapobieganiu przypadkowej blokady administratora z nieprawidłowo skonfigurowanymi zasadami dostępu warunkowego

**Typ:** Zmieniono **kategorię usługi funkcji:** dostęp warunkowy do **produktu:** zabezpieczenia tożsamości & ochrona

Aby zapobiec przypadkowemu zablokowaniu przez administratorów własnych dzierżaw za pomocą nieprawidłowo skonfigurowanych zasad dostępu warunkowego, zostały utworzone nowe ostrzeżenia i zaktualizowane wskazówki w Azure Portal. Aby uzyskać więcej informacji o nowych wskazówkach, zobacz [co to są zależności usługi w Azure Active Directory dostęp warunkowy](../conditional-access/service-dependencies.md).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Udoskonalone środowiska użytkowania dotyczące użytkowników końcowych na urządzeniach przenośnych

**Typ:** Zmieniono **kategorię usługi funkcji:** warunki użytkowania **możliwości produktu:** ładu

Zaktualizowaliśmy nasze istniejące warunki użytkowania, aby pomóc w ulepszaniu sposobu przeglądania i wyrażania zgody na warunki użytkowania na urządzeniu przenośnym. Teraz możesz powiększać i pomniejszać, wrócić, pobrać informacje i wybierać hiperlinki. Aby uzyskać więcej informacji o zaktualizowanych warunkach użytkowania, zobacz [Azure Active Directory warunki użytkowania](../conditional-access/terms-of-use.md#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Dostępne są nowe środowisko pobierania dzienników aktywności usługi Azure AD

**Typ:** Zmieniono **kategorię usługi funkcji:** raportowanie **możliwości produktu:** monitorowanie & raportowanie

Teraz można pobrać duże ilości dzienników aktywności bezpośrednio z Azure Portal. Ta aktualizacja umożliwia:

- Pobierz do 250 000 wierszy.

- Otrzymuj powiadomienie po zakończeniu pobierania.

- Dostosuj nazwę pliku.

- Określ format danych wyjściowych — JSON lub CSV.

Aby uzyskać więcej informacji na temat tej funkcji, zobacz [Szybki Start: pobieranie raportu inspekcji przy użyciu Azure Portal](../reports-monitoring/quickstart-download-audit-report.md)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Zmiana podziału: aktualizacje oceny warunku przez program Exchange ActiveSync (EAS)

**Typ:** Zaplanuj **kategorię usługi zmiany:** dostęp warunkowy **możliwości produktu:** Access Control

Jesteśmy w trakcie aktualizowania sposobu, w jaki program Exchange ActiveSync (EAS) ocenia następujące warunki:

- Lokalizacja użytkownika, na podstawie kraju, regionu lub adresu IP

- Ryzyko związane z logowaniem

- Platforma urządzeń

Jeśli te warunki zostały wcześniej użyte w zasadach dostępu warunkowego, należy pamiętać, że zachowanie warunku może się zmienić. Na przykład jeśli w zasadach użyto wcześniej warunku lokalizacji użytkownika, zasady te mogą być teraz pomijane na podstawie lokalizacji użytkownika.

---

## <a name="february-2019"></a>Luty 2019 r.

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurowalne szyfrowanie tokenów SAML usługi Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** Logowanie jednokrotne

Teraz można skonfigurować dowolną obsługiwaną aplikację SAML do odbierania szyfrowanych tokenów SAML. Po skonfigurowaniu i użyciu z aplikacją usługa Azure AD szyfruje wyemitowane potwierdzenia SAML przy użyciu klucza publicznego uzyskanego z certyfikatu przechowywanego w usłudze Azure AD.

Aby uzyskać więcej informacji o konfigurowaniu szyfrowania tokenów SAML, zobacz [Konfigurowanie szyfrowania tokenów SAML usługi Azure AD](../manage-apps/howto-saml-token-encryption.md).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Tworzenie przeglądu dostępu dla grup lub aplikacji za pomocą przeglądów dostępu w usłudze Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** **możliwości produktu** przeglądy dostępu: ładu

Teraz można uwzględnić wiele grup lub aplikacji w jednym przeglądzie dostępu do usługi Azure AD na potrzeby członkostwa w grupie lub przypisywania aplikacji. Przeglądy dostępu z wieloma grupami lub aplikacjami są konfigurowane przy użyciu tych samych ustawień, a wszyscy włączeni recenzenci są powiadamiani w tym samym czasie.

Aby uzyskać więcej informacji na temat tworzenia przeglądu dostępu za pomocą przeglądów dostępu w usłudze Azure AD, zobacz [Tworzenie przeglądu dostępu do grup lub aplikacji w przeglądach dostępu do usługi Azure AD](../governance/create-access-review.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — luty 2019

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** integracja z firmą

W lutym 2019 dodaliśmy następujące 27 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Euromonitor paszport](../saas-apps/euromonitor-passport-tutorial.md), [MindTickle](../saas-apps/mindtickle-tutorial.md), [odcisk palca](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [STOSy](../saas-apps/airstack-tutorial.md)zawartości systemu plików Oracle, [ERP](../saas-apps/oracle-fusion-erp-tutorial.md), [iDrive](../saas-apps/idrive-tutorial.md), [skyward Qmlativ](../saas-apps/skyward-qmlativ-tutorial.md), [Brightidea](../saas-apps/brightidea-tutorial.md), [AlertOps](../saas-apps/alertops-tutorial.md), [Soloinsight-CloudGate SSO](../saas-apps/soloinsight-cloudgate-sso-tutorial.md), uprawnienie kliknij, [Brandfolder](../saas-apps/brandfolder-tutorial.md), [StoregateSmartFile,](../saas-apps/smartfile-tutorial.md) [Pexip](../saas-apps/pexip-tutorial.md), [Stormboard](../saas-apps/stormboard-tutorial.md), [sejsmiczne](../saas-apps/seismic-tutorial.md), [udostępniaj marzenie](https://www.shareadream.org/how-it-works) [,](../saas-apps/periscope-data-tutorial.md) [Bugsnag](../saas-apps/bugsnag-tutorial.md) [,](../saas-apps/purecloud-by-genesys-tutorial.md) [webMethods Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md) [,](../saas-apps/knowledge-anywhere-lms-tutorial.md)Periscope [OU Campus](../saas-apps/ou-campus-tutorial.md) [, NetOp](../saas-apps/smartvid.io-tutorial.md) [Netop Portal](../saas-apps/netop-portal-tutorial.md) [ClickUp Productivity Platform](../saas-apps/clickup-productivity-platform-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Ulepszona Rejestracja usługi MFA/SSPR

**Typ:** Zmieniono **kategorię usługi funkcji:** funkcja samoobsługowego resetowania hasła **:** uwierzytelnianie użytkownika

W odpowiedzi na Opinie klientów Ulepszono środowisko w wersji zapoznawczej usługi MFA/SSPR, dzięki czemu użytkownicy mogą szybko rejestrować swoje informacje zabezpieczające dla usługi MFA i SSPR.

**Aby włączyć już dziś udoskonalone środowisko dla użytkowników, wykonaj następujące kroki:**

1. Jako Administrator globalny lub administrator użytkownika Zaloguj się do Azure Portal i przejdź do pozycji **Azure Active Directory > ustawienia użytkownika > Zarządzaj ustawieniami funkcji w wersji zapoznawczej panelu dostępu**.

2. W przypadku **użytkowników, którzy mogą używać funkcji w wersji zapoznawczej do rejestrowania i zarządzania informacjami o zabezpieczeniach — odświeżanie** , wybierz opcję włączenia funkcji dla **wybranej grupy użytkowników** lub dla **wszystkich użytkowników**.

W ciągu najbliższych kilku tygodni usuniemy możliwość włączenia starego połączenia usługi SSPR w wersji zapoznawczej usługi MFA w przypadku dzierżawców, które nie są jeszcze włączone.

**Aby sprawdzić, czy formant zostanie usunięty dla dzierżawy, wykonaj następujące kroki:**

1. Jako Administrator globalny lub administrator użytkownika Zaloguj się do Azure Portal i przejdź do pozycji **Azure Active Directory > ustawienia użytkownika > Zarządzaj ustawieniami funkcji w wersji zapoznawczej panelu dostępu**.

2. Jeśli **Użytkownicy, którzy mogą korzystać z funkcji w wersji zapoznawczej do rejestrowania i zarządzania informacjami o zabezpieczeniach** , mają ustawioną wartość **Brak**, opcja zostanie usunięta z dzierżawy.

Bez względu na to, czy wcześniej włączono poprzednie środowisko w wersji zapoznawczej rejestracji usługi MFA/SSPR dla użytkowników, stare środowisko zostanie wyłączone w przyszłości. Ze względu na to zdecydowanie zalecamy przechodzenie do nowego, ulepszonego środowiska tak szybko, jak to możliwe.

Aby uzyskać więcej informacji na temat udoskonalonego środowiska rejestracji, zobacz [chłodne ulepszenia usługi Azure AD połączenia MFA i rejestracji resetowania haseł](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Zaktualizowano środowisko zarządzania zasadami dla przepływów użytkowników

**Typ:** Zmieniono **kategorię usługi funkcji:** B2C — **możliwości produktu** zarządzania tożsamościami konsumenta: B2B/B2C

Zaktualizowaliśmy proces tworzenia i zarządzania zasadami dla przepływów użytkowników (wcześniej znanych jako wbudowane zasady). To nowe środowisko jest teraz domyślne dla wszystkich dzierżawców usługi Azure AD.

Dodatkowe opinie i sugestie można przekazać, korzystając z ikon uśmiech lub niezadowolenia w obszarze **Wyślij nam opinię** w górnej części ekranu portalu.

Aby uzyskać więcej informacji na temat nowego środowiska zarządzania zasadami, zapoznaj się z tematem [Azure AD B2C teraz zawiera on dostosowanie JavaScript i wiele nowych funkcji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) .

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Wybierz określone wersje elementów strony udostępniane przez Azure AD B2C

**Typ:** Nowa **Kategoria usługi funkcji:** B2C — **możliwości produktu** zarządzania tożsamościami konsumenta: B2B/B2C

Teraz można wybrać określoną wersję elementów strony dostarczonych przez Azure AD B2C. Wybierając określoną wersję, można przetestować aktualizacje, zanim zostaną one wyświetlone na stronie i można uzyskać przewidywalne zachowanie. Ponadto możesz teraz wyrazić zgodę na wymuszanie określonych wersji strony, aby umożliwić dostosowywanie języka JavaScript. Aby włączyć tę funkcję, przejdź do strony **Właściwości** w obszarze przepływy użytkownika.

Aby uzyskać więcej informacji na temat wybierania określonych wersji elementów strony, zapoznaj się z tematem [Azure AD B2C teraz zawiera on dostosowanie JavaScript i wiele nowych funkcji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) .

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurowalne wymagania dotyczące hasła użytkownika końcowego dla B2C (GA)

**Typ:** Nowa **Kategoria usługi funkcji:** B2C — **możliwości produktu** zarządzania tożsamościami konsumenta: B2B/B2C

Teraz możesz skonfigurować złożoność hasła organizacji dla użytkowników końcowych, zamiast korzystać z natywnych zasad haseł usługi Azure AD. W bloku **Właściwości** przepływów użytkownika (wcześniej znanych jako wbudowane zasady) można wybrać złożoność hasła lub **silne** **lub można** utworzyć **niestandardowy** zestaw wymagań.

Więcej informacji o konfiguracji wymagań dotyczących złożoności haseł znajduje się [w temacie Konfigurowanie wymagań dotyczących złożoności haseł w Azure Active Directory B2C](../../active-directory-b2c/user-flow-password-complexity.md).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nowe domyślne szablony dla niestandardowych środowisk uwierzytelniania markowego

**Typ:** Nowa **Kategoria usługi funkcji:** B2C — **możliwości produktu** zarządzania tożsamościami konsumenta: B2B/B2C

Możesz użyć naszych nowych szablonów domyślnych znajdujących się w bloku **układy stron** przepływów użytkownika (znanych wcześniej jako zasady wbudowane), aby utworzyć niestandardowe środowisko uwierzytelniania markowego dla użytkowników.

Aby uzyskać więcej informacji o korzystaniu z szablonów, zobacz [Azure AD B2C teraz zawiera ona dostosowanie JavaScript i wiele nowych funkcji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Styczeń 2019 r.

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Active Directory współpracy B2B przy użyciu jednorazowego uwierzytelniania kodu dostępu (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** **produkt B2B:** B2B/B2C

Wprowadziliśmy jednorazowe uwierzytelnianie kodu dostępu (OTP) dla użytkowników Gości B2B, którzy nie mogą być uwierzytelniani przy użyciu innych metod, takich jak Azure AD, konto Microsoft (MSA) lub Google Federation. Ta nowa metoda uwierzytelniania oznacza, że użytkownicy-Goście nie muszą tworzyć nowych konto Microsoft. Zamiast tego podczas realizowania zaproszenia lub uzyskiwania dostępu do zasobu udostępnionego użytkownik-Gość może zażądać, aby kod tymczasowy został wysłany na adres e-mail. Przy użyciu tego kodu tymczasowego użytkownik-Gość może nadal logować się.

Aby uzyskać więcej informacji, zobacz [jednorazowe uwierzytelnianie kodu dostępu za pomocą poczty e-mail (wersja zapoznawcza)](../external-identities/one-time-passcode.md) i blog, [usługa Azure AD zapewnia bezproblemowe udostępnianie i współpracę dla dowolnego użytkownika z dowolnym kontem](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nowe ustawienia pliku cookie usługi Azure serwer proxy aplikacji usługi Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** **możliwości produktu** serwera proxy aplikacji: Access Control

Wprowadziliśmy trzy nowe ustawienia plików cookie dostępne dla aplikacji publikowanych za poorednictwem serwera proxy aplikacji:

- **Użyj HTTP-Only pliku cookie.** Ustawia flagę **HTTPOnly** na serwerze proxy aplikacji i plikach cookie sesji. Włączenie tego ustawienia zapewnia dodatkowe korzyści z zabezpieczeń, takie jak uniemożliwienie kopiowania lub modyfikowania plików cookie za pomocą skryptów po stronie klienta. Zalecamy włączenie tej flagi (wybierz opcję **tak**), aby uzyskać dodatkowe korzyści.

- **Użyj bezpiecznego pliku cookie.** Ustawia **bezpieczną** flagę na serwerze proxy aplikacji i plikach cookie sesji. Włączenie tego ustawienia zapewnia dodatkowe korzyści z zabezpieczeń, zapewniając, że pliki cookie są przesyłane tylko za pośrednictwem bezpiecznych kanałów protokołu TLS, takich jak HTTPS. Zalecamy włączenie tej flagi (wybierz opcję **tak**), aby uzyskać dodatkowe korzyści.

- **Użyj trwałego pliku cookie.** Zapobiega utracie dostępu plików cookie po zamknięciu przeglądarki sieci Web. Te pliki cookie są ostatnie przez okres istnienia tokenu dostępu. Pliki cookie są jednak resetowane po osiągnięciu czasu wygaśnięcia lub ręcznym usunięciu pliku cookie przez użytkownika. Zalecamy zachowanie ustawienia domyślnego **nie**, włączając ustawienie dla starszych aplikacji, które nie udostępniają plików cookie między procesami.

Aby uzyskać więcej informacji na temat nowych plików cookie, zobacz [Ustawienia plików cookie dotyczące uzyskiwania dostępu do aplikacji lokalnych w Azure Active Directory](../manage-apps/application-proxy-configure-cookie-settings.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — styczeń 2019

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** integracja z firmą

W styczniu 2019 dodaliśmy do galerii aplikacji 35 te nowe aplikacje z obsługą Federacji:

[Firstbird](../saas-apps/firstbird-tutorial.md), [Folloze](../saas-apps/folloze-tutorial.md), [paleta talent](../saas-apps/talent-palette-tutorial.md), [infor CloudSuite](../saas-apps/infor-cloud-suite-tutorial.md), [Cisco parasol](../saas-apps/cisco-umbrella-tutorial.md), [rozwiązania Zscaler Internet Access administrator](../saas-apps/zscaler-internet-access-administrator-tutorial.md), przypomnienie o [wygaśnięciu](../saas-apps/expiration-reminder-tutorial.md), [InstaVR Viewer](../saas-apps/instavr-viewer-tutorial.md), [CORPTAX](../saas-apps/corptax-tutorial.md), [czasownik](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](../saas-apps/pavaso-digital-close-tutorial.md), [GoodPractice Toolkit](../saas-apps/goodpractice-toolkit-tutorial.md), [Cloud Service Picco](../saas-apps/cloud-service-picco-tutorial.md), [AuditBoard](../saas-apps/auditboard-tutorial.md), [iProva](../saas-apps/iprova-tutorial.md), [działające,](../saas-apps/workable-tutorial.md) [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus](../saas-apps/gtnexus-sso-module-tutorial.md), CBRE [ServiceInsight](../saas-apps/cbre-serviceinsight-tutorial.md), [Deskradar](../saas-apps/deskradar-tutorial.md), [Coralogixv](../saas-apps/coralogix-tutorial.md), [Signagelive](../saas-apps/signagelive-tutorial.md) [,](../saas-apps/visitly-tutorial.md) [arach for Enterprise](../saas-apps/ares-for-enterprise-tutorial.md), [K2 dla pakietu Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/) [, iDiD HighGear, Alp](../saas-apps/idid-manager-tutorial.md), [Korn Ferry ALP](../saas-apps/korn-ferry-alp-tutorial.md) [Acadia](../saas-apps/highgear-tutorial.md) [Acadia](../saas-apps/acadia-tutorial.md) [Adoddle cSaas Platform](../saas-apps/adoddle-csaas-platform-tutorial.md)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nowe udoskonalenia Azure AD Identity Protection (publiczna wersja zapoznawcza)

**Typ:** Zmieniono **kategorię usługi funkcji:** **zdolność produktu** do ochrony tożsamości: zabezpieczenia tożsamości & ochrona

Przyjemnością się, że dodaliśmy następujące usprawnienia do oferty Azure AD Identity Protection publicznej wersji zapoznawczej, w tym:

- Zaktualizowany i bardziej zintegrowany interfejs użytkownika

- Dodatkowe interfejsy API

- Ulepszona Ocena ryzyka za poorednictwem uczenia maszynowego

- Wyrównywanie całego produktu wśród ryzykownych użytkowników i ryzykowne logowania

Aby uzyskać więcej informacji na temat ulepszeń, zobacz [co to jest Azure Active Directory Identity Protection (odświeżone)?](https://aka.ms/IdentityProtectionDocs) Aby dowiedzieć się więcej i podzielić się swoimi pomysłami, zapoznaj się z instrukcjami w ramach produktu.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nowa funkcja blokowania aplikacji dla aplikacji Microsoft Authenticator na urządzeniach z systemami iOS i Android

**Typ:** Nowa **Kategoria usługi funkcji:** Microsoft Authenticator **możliwości produktu aplikacji:** zabezpieczenia tożsamości & ochrona

Aby zapewnić bezpieczeństwo jednorazowych kodów dostępu, informacji o aplikacji i ustawień aplikacji, możesz włączyć funkcję blokowania aplikacji w aplikacji Microsoft Authenticator. Włączenie blokady aplikacji oznacza, że użytkownik zostanie poproszony o uwierzytelnienie przy użyciu kodu PIN lub biometrycznego za każdym razem, gdy otworzysz aplikację Microsoft Authenticator.

Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące aplikacji Microsoft Authenticator](../user-help/user-help-auth-app-faq.md).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Ulepszone możliwości eksportowania Azure AD Privileged Identity Management (PIM)

**Typ:** Nowa **Kategoria usługi funkcji:** Privileged Identity Management **możliwości produktu:** Privileged Identity Management

Administratorzy usługi Privileged Identity Management (PIM) mogą teraz wyeksportować wszystkie aktywne i kwalifikujące się przypisania ról dla określonego zasobu, w tym przypisania ról dla wszystkich zasobów podrzędnych. Wcześniej było trudne, aby administratorzy mogli uzyskać pełną listę przypisań ról dla subskrypcji i musiały wyeksportować przypisania ról dla każdego określonego zasobu.

Aby uzyskać więcej informacji, zobacz [Wyświetlanie historii aktywności i inspekcji dla ról zasobów platformy Azure w programie PIM](../privileged-identity-management/azure-pim-resource-rbac.md).

---

## <a name="novemberdecember-2018"></a>Listopad/grudzień 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Użytkownicy usunięci z zakresu synchronizacji nie przełączają się już do kont tylko w chmurze

**Typ:** **Kategoria stałej usługi:** **możliwości produktu** zarządzania użytkownikami: katalog

>[!Important]
>W związku z tym nasłuchuje i rozumiemy frustrację. Z tego powodu przywrócono tę zmianę do momentu, w którym można ułatwić wdrożenie w organizacji.

Rozwiązano problem polegający na tym, że flaga DirSyncEnabled użytkownika zostanie błędnie przełączona na **Fałsz** , gdy obiekt Active Directory Domain Services (AD DS) został wykluczony z zakresu synchronizacji, a następnie przeniesiony do kosza w usłudze Azure AD w następującym cyklu synchronizacji. W wyniku tej poprawki Jeśli użytkownik zostanie wykluczony z zakresu synchronizacji, a następnie ponownie przywrócony z Kosza usługi Azure AD, konto użytkownika pozostanie jako zsynchronizowane z lokalnej usługi AD zgodnie z oczekiwaniami i nie będzie możliwe do zarządzania w chmurze, ponieważ jego źródło (SoA) pozostaje jako lokalna usługa AD.

Przed tą poprawką wystąpił problem podczas przełączania flagi DirSyncEnabled na wartość false. Nastąpiło niewłaściwe wrażenie, że te konta zostały przekonwertowane na obiekty tylko w chmurze, a konta mogą być zarządzane w chmurze. Jednak konta nadal przechowują swoje SoA jako lokalne i wszystkie zsynchronizowane właściwości (atrybuty cienia) pochodzące z lokalnej usługi AD. Ten stan powodował wiele problemów dotyczących usługi Azure AD i innych obciążeń w chmurze (na przykład usługi Exchange Online), które oczekują traktowania tych kont jako zsynchronizowanych z usługi AD, ale teraz zachowują się one jak konta tylko w chmurze.

W tym momencie jedynym sposobem na prawdziwą konwersję konta synchronizacji z usługi AD na konto tylko w chmurze jest wyłączenie narzędzia DirSync na poziomie dzierżawy, który wyzwala operację zaplecza do transferu SoA. Ten typ zmiany SoA wymaga (ale nie jest ograniczony do) czyszczenia wszystkich lokalnych atrybutów powiązanych (takich jak atrybuty LastDirSyncTime i Shadow) i wysyłania sygnału do innych obciążeń w chmurze, aby miało on również odpowiedni obiekt konwertowany na konto tylko w chmurze.

W związku z tym ta poprawka uniemożliwia bezpośrednie aktualizacje atrybutu ImmutableID użytkownika synchronizowanego z usługi AD, co w niektórych scenariuszach w przeszłości było wymagane. Zgodnie z projektem, ImmutableID obiektu w usłudze Azure AD, jak nazwa oznacza, ma być niezmienny. Nowe funkcje zaimplementowane w Azure AD Connect Health i Azure AD Connect klienta synchronizacji są dostępne do rozwiązania takich scenariuszy:

- **Aktualizacja ImmutableID o dużej skali dla wielu użytkowników w podejściu przygotowanym**

  Na przykład należy wykonać długość AD DS migracji między lasami. Rozwiązanie: Użyj Azure AD Connect, aby **skonfigurować kotwicę źródłową** i, w miarę migrowania użytkownika, skopiować istniejące wartości ImmutableID z usługi Azure AD do atrybutu "MS-ds-Consistency-GUID systemu lokalnego AD DS użytkownika" w nowym lesie. Aby uzyskać więcej informacji, zobacz [Używanie MS-ds-ConsistencyGuid jako sourceAnchor](../hybrid/plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

- **Aktualizacje ImmutableID na dużą skalę dla wielu użytkowników w jednym zrzucie**

  Na przykład podczas wdrażania Azure AD Connect pomyłkę i teraz trzeba zmienić atrybut SourceAnchor. Rozwiązanie: Wyłącz narzędzie DirSync na poziomie dzierżawy i wyczyść wszystkie nieprawidłowe wartości ImmutableID. Aby uzyskać więcej informacji, zobacz Wyłączanie [synchronizacji katalogów dla pakietu Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Dopasowanie użytkownika lokalnego z istniejącym użytkownikiem w usłudze Azure AD** Na przykład użytkownik, który został utworzony w AD DS, generuje duplikat na koncie usługi Azure AD, a nie dopasowuje go przy użyciu istniejącego konta usługi Azure AD (obiektu oddzielonego). Rozwiązanie: Użyj Azure AD Connect Health w Azure Portal, aby ponownie zmapować kotwicę źródłową/ImmutableID. Aby uzyskać więcej informacji, zobacz [scenariusz obiektu oddzielonego](../hybrid/how-to-connect-health-diagnose-sync-errors.md#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Zmiana istotna: aktualizacje schematu inspekcji i dzienników logowania za pomocą Azure Monitor

**Typ:** Zmieniono **kategorię usługi funkcji:** raportowanie **możliwości produktu:** monitorowanie & raportowanie

Obecnie publikujemy strumienie inspekcji i logowania za pomocą Azure Monitor, dzięki czemu możesz bezproblemowo zintegrować pliki dziennika z narzędziami SIEM lub z Log Analytics. Na podstawie opinii i przygotowania do ogólnej dostępności tej funkcji wprowadzamy następujące zmiany w naszym schemacie. Te zmiany schematu i powiązane z nimi aktualizacje dokumentacji będą wykonywane przez pierwszy tydzień stycznia.

#### <a name="new-fields-in-the-audit-schema"></a>Nowe pola w schemacie inspekcji
Dodajemy nowe pole **typu operacji** , aby zapewnić typ operacji wykonanej na zasobie. Na przykład **Dodaj**, **zaktualizuj**lub **Usuń**.

#### <a name="changed-fields-in-the-audit-schema"></a>Zmieniono pola w schemacie inspekcji
Następujące pola są zmieniane w schemacie inspekcji:

|Nazwa pola|Co zostało zmienione|Stare wartości|Nowe wartości|
|----------|------------|----------|----------|
|Kategoria|To jest pole **nazwa usługi** . Jest teraz polem **kategorie inspekcji** . **Nazwa usługi** została zmieniona na pole **loggedByService** .|<ul><li>Aprowizacja kont</li><li>Katalog podstawowy</li><li>Samoobsługowe resetowanie hasła</li></ul>|<ul><li>Zarządzanie użytkownikami</li><li>Zarządzanie grupami</li><li>Zarządzanie aplikacjami</li></ul>|
|targetResources|Obejmuje **TargetResourceType** na najwyższego poziomu.|&nbsp;|<ul><li>Zasady</li><li>Aplikacja</li><li>Użytkownik</li><li>Group (Grupa)</li></ul>|
|loggedByService|Zawiera nazwę usługi, która wygenerowała dziennik inspekcji.|Zero|<ul><li>Aprowizacja kont</li><li>Katalog podstawowy</li><li>Samoobsługowe resetowanie haseł</li></ul>|
|Wynik|Zawiera wyniki dzienników inspekcji. Wcześniej było to wyliczone, ale teraz pokazywane są rzeczywiste wartości.|<ul><li>0</li><li>1</li></ul>|<ul><li>Powodzenie</li><li>Niepowodzenie</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Zmieniono pola w schemacie logowania
Następujące pola są zmieniane w schemacie logowania:

|Nazwa pola|Co zostało zmienione|Stare wartości|Nowe wartości|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|To było pole **conditionalaccessPolicies** . To teraz pole **appliedConditionalAccessPolicies** .|Bez zmian|Bez zmian|
|conditionalAccessStatus|Zapewnia wynik stanu zasad dostępu warunkowego podczas logowania. Wcześniej było to wyliczone, ale teraz pokazywane są rzeczywiste wartości.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Powodzenie</li><li>Niepowodzenie</li><li>Nie zastosowano</li><li>Disabled</li></ul>|
|appliedConditionalAccessPolicies: wynik|Zapewnia wynik poszczególnych stanów zasad dostępu warunkowego podczas logowania. Wcześniej było to wyliczone, ale teraz pokazywane są rzeczywiste wartości.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Powodzenie</li><li>Niepowodzenie</li><li>Nie zastosowano</li><li>Disabled</li></ul>|

Aby uzyskać więcej informacji na temat schematu, zobacz [interpretowanie schematu dzienników inspekcji usługi Azure AD w Azure monitor (wersja zapoznawcza)](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Ulepszenia ochrony tożsamości dla nadzorowanego modelu uczenia maszynowego i aparatu oceny ryzyka

**Typ:** Zmieniono **kategorię usługi funkcji:** **zdolność produktu** do ochrony tożsamości: oceny ryzyka

Ulepszenia aparatu do oceny ryzyka związanego z ochroną tożsamości oraz możliwości logowania mogą pomóc w zwiększeniu dokładności i zakresu ryzyka dla użytkowników. Administratorzy mogą zauważyć, że poziom ryzyka użytkownika nie jest już bezpośrednio połączony z poziomem ryzyka specyficznych wykryć i że występuje wzrost liczby i poziomu ryzykownych zdarzeń związanych z logowaniem.

Wykrywanie ryzyka jest teraz oceniane przez nadzorowany model uczenia maszynowego, który oblicza ryzyko użytkownika przy użyciu dodatkowych funkcji logowania użytkownika i wzorca wykrywania. W oparciu o ten model administrator może znaleźć użytkowników z wynikami wysokiego ryzyka, nawet jeśli wykrycia związane z tym użytkownikiem mają niski lub średni ryzyko.

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Administratorzy mogą zresetować własne hasło przy użyciu aplikacji Microsoft Authenticator (publiczna wersja zapoznawcza)

**Typ:** Zmieniono **kategorię usługi funkcji:** funkcja samoobsługowego resetowania hasła **:** uwierzytelnianie użytkownika

Administratorzy usługi Azure AD mogą teraz resetować swoje hasła przy użyciu powiadomień aplikacji Microsoft Authenticator lub kodu z dowolnej aplikacji lub tokenu sprzętowego usług uwierzytelniania mobilnego. Aby zresetować własne hasło, Administratorzy mogą teraz korzystać z dwóch następujących metod:

- Powiadomienie Microsoft Authenticator aplikacji

- Inna aplikacja/token sprzętowy do uwierzytelniania mobilnego

- Poczta e-mail

- Połączenie telefoniczne

- Wiadomość SMS

Aby uzyskać więcej informacji na temat używania aplikacji Microsoft Authenticator do resetowania haseł, zobacz samoobsługowe [resetowanie haseł w usłudze Azure AD — aplikacja mobilna i SSPR (wersja zapoznawcza)](../authentication/concept-sspr-howitworks.md#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nowa rola administratora urządzenia w chmurze usługi Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** Funkcja rejestracji urządzeń i **produktu zarządzania:** kontrola dostępu

Administratorzy mogą przypisywać użytkowników do nowej roli administratora urządzenia w chmurze w celu wykonywania zadań administratora urządzenia w chmurze. Użytkownicy z przypisaną rolą Administratorzy urządzeń w chmurze mogą włączać, wyłączać i usuwać urządzenia w usłudze Azure AD oraz mogą odczytywać klucze funkcji BitLocker systemu Windows 10 (jeśli istnieją) w Azure Portal.

Aby uzyskać więcej informacji o rolach i uprawnieniach, zobacz [Przypisywanie ról administratorów w Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Zarządzanie urządzeniami przy użyciu nowej sygnatury czasowej działania w usłudze Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** rejestracja urządzeń i **możliwość zarządzania produktu:** Zarządzanie cyklem życia urządzeń

Zdajemy sobie sprawę, że w miarę upływu czasu musisz odświeżyć i wycofać urządzenia organizacji w usłudze Azure AD, aby uniknąć używania starych urządzeń w środowisku. Aby pomóc w tym procesie, usługa Azure AD teraz aktualizuje urządzenia przy użyciu nowej sygnatury czasowej działania, co ułatwia zarządzanie cyklem życia urządzenia.

Aby uzyskać więcej informacji o tym, jak uzyskać i korzystać z tej sygnatury czasowej, zobacz [How to: Manage The Devices in Azure AD](../devices/manage-stale-devices.md)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Administratorzy mogą wymagać od użytkowników zaakceptowania warunków użytkowania na poszczególnych urządzeniach

**Typ:** Nowa **Kategoria usługi funkcji:** warunki użytkowania **możliwości produktu:** ładu

Administratorzy mogą teraz włączyć opcję **Wymagaj od użytkowników zgody na każde urządzenie** , aby wymagać od użytkowników akceptacji warunków użytkowania na każdym urządzeniu, na którym są używane w dzierżawie.

Aby uzyskać więcej informacji, zobacz [sekcję warunki użytkowania poszczególnych urządzeń w funkcji Azure Active Directory warunki użytkowania](../conditional-access/terms-of-use.md#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Administratorzy mogą skonfigurować warunki użytkowania, aby wygasnąć na podstawie harmonogramu cyklicznego

**Typ:** Nowa **Kategoria usługi funkcji:** warunki użytkowania **możliwości produktu:** ładu


Administratorzy mogą teraz włączyć opcję **wygasania** , aby wygaśnie warunki użytkowania dla wszystkich użytkowników na podstawie określonego harmonogramu cyklicznego. Harmonogram może być roczny, dwuroczny, kwartalny lub miesięczny. Po wygaśnięciu warunków użytkowania użytkownicy muszą ponownie zaakceptować.

Aby uzyskać więcej informacji, zobacz [sekcję Dodawanie warunków użytkowania w artykule Azure Active Directory warunki użytkowania](../conditional-access/terms-of-use.md#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Administratorzy mogą skonfigurować warunki użytkowania, aby wygasnąć na podstawie harmonogramu każdego użytkownika

**Typ:** Nowa **Kategoria usługi funkcji:** warunki użytkowania **możliwości produktu:** ładu

Administratorzy mogą teraz określić czas trwania, przez który użytkownik musi ponownie zaakceptować warunki użytkowania. Administratorzy mogą na przykład określić, że użytkownicy muszą ponownie akceptować warunki użytkowania co 90 dni.

Aby uzyskać więcej informacji, zobacz [sekcję Dodawanie warunków użytkowania w artykule Azure Active Directory warunki użytkowania](../conditional-access/terms-of-use.md#add-terms-of-use).

---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nowe wiadomości e-mail w usłudze Azure AD Privileged Identity Management (PIM) dla ról Azure Active Directory

**Typ:** Nowa **Kategoria usługi funkcji:** Privileged Identity Management **możliwości produktu:** Privileged Identity Management

Klienci korzystający z programu Azure AD Privileged Identity Management (PIM) mogą teraz odbierać cotygodniowe wiadomości e-mail z podsumowaniem, w tym następujące informacje w ciągu ostatnich siedmiu dni:

- Przegląd najbardziej uprawnionych i trwałych przypisań ról

- Liczba użytkowników, którzy aktywują role

- Liczba użytkowników przypisanych do ról w usłudze PIM

- Liczba użytkowników przypisanych do ról poza usługą PIM

- Liczba użytkowników trwałych w usłudze PIM

Aby uzyskać więcej informacji na temat usługi PIM i dostępnych powiadomień e-mail, zobacz [powiadomienia e-mail w usłudze PIM](../privileged-identity-management/pim-email-notifications.md).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Licencjonowanie oparte na grupach jest teraz ogólnie dostępne

**Typ:** Zmieniono **kategorię usługi funkcji:** inny **produkt:** katalog

Licencjonowanie oparte na grupach jest w publicznej wersji zapoznawczej i jest teraz ogólnie dostępne. W ramach tej ogólnej wersji ta funkcja jest bardziej skalowalna i dodaliśmy możliwość przetworzenia przypisań licencjonowania opartego na grupach dla jednego użytkownika i możliwość korzystania z licencjonowania opartego na grupach z licencjami pakietu Office 365 E3/a3.

Aby uzyskać więcej informacji na temat licencjonowania opartego na grupach, zobacz [co to jest Licencjonowanie oparte na grupach w Azure Active Directory?](./active-directory-licensing-whatis-azure-portal.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — listopad 2018

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** integracja z firmą

W listopadzie 2018 dodaliśmy następujące 26 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](../saas-apps/hubspot-tutorial.md), [GetThere](../saas-apps/getthere-tutorial.md)Getin, [gra-PE](../saas-apps/grape-tutorial.md), [eHour](https://getehour.com/try-now), [Consent2Go](../saas-apps/consent2go-tutorial.md), [Appinux](../saas-apps/appinux-tutorial.md), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](../saas-apps/useall-tutorial.md), [nieskończony](../saas-apps/infinitecampus-tutorial.md), [Alaya](https://alayagood.com), [HEYBUDDY](../saas-apps/heybuddy-tutorial.md), [Wrike SAML](../saas-apps/wrike-tutorial.md), [dryf](../saas-apps/drift-tutorial.md), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [EverBridge — Portal członkowski](../saas-apps/everbridge-tutorial.md), [ideo](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](../saas-apps/ivanti-service-manager-tutorial.md), [Peakon](../saas-apps/peakon-tutorial.md), [Allbound SSO](../saas-apps/allbound-sso-tutorial.md), [aplikacje typu](https://cloud.plex.com/sso) [Plex](https://getcrafts.ca/craftsregistration) — [test klasyczny,](https://test.plexonline.com/signon)aplikacje dla [obiektów typu plex](https://accounts.plex.com/)— klasyczne [,](https://www.plexonline.com/signon)aplikacje [typu plex — test środowiska &](https://test.cloud.plex.com/sso)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Październik 2018 r.

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Dzienniki usługi Azure AD teraz pracują z usługą Azure Log Analytics (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** raportowanie **możliwości produktu:** monitorowanie & raportowanie

Przyjemnością się, że możesz teraz przekazać dzienniki usługi Azure AD do usługi Azure Log Analytics! Ta funkcja w górnej części pomaga zapewnić jeszcze lepszy dostęp do analiz dla Twojej firmy, operacji i zabezpieczeń, a także ułatwić monitorowanie infrastruktury. Aby uzyskać więcej informacji, zobacz [dzienniki aktywności Azure Active Directory w usłudze Azure log Analytics teraz dostępny](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blog.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — październik 2018

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** integracja z firmą

W październiku 2018 dodaliśmy następujące 14 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Moje punkty przyznawania](../saas-apps/myawardpoints-tutorial.md), [Vibe HCM](../saas-apps/vibehcm-tutorial.md), ambyint, [MyWorkDrive](../saas-apps/myworkdrive-tutorial.md), [BorrowBox](../saas-apps/borrowbox-tutorial.md), Dialpad [, ON24 środowisko wirtualne](../saas-apps/on24-tutorial.md), [RingCentral](../saas-apps/ringcentral-tutorial.md), [rozwiązania Zscaler trzy](../saas-apps/zscaler-three-tutorial.md), [Phraseanet](../saas-apps/phraseanet-tutorial.md), [Appraisd](../saas-apps/appraisd-tutorial.md), [Workspot](../saas-apps/workspotcontrol-tutorial.md), [Shuccho Navi](../saas-apps/shucchonavi-tutorial.md), [Glassfrog](../saas-apps/glassfrog-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services powiadomienia E-mail

**Typ:** Nowa **Kategoria usługi funkcji:** Azure AD Domain Services **możliwości produktu:** Azure AD Domain Services

Azure AD Domain Services zapewnia alerty dotyczące Azure Portal dotyczących błędów konfiguracji lub problemów z domeną zarządzaną. Te alerty obejmują Przewodniki krok po kroku, dzięki czemu można spróbować rozwiązać problemy bez konieczności kontaktowania się z pomocą techniczną.

Od października będzie można dostosować ustawienia powiadomień dla domeny zarządzanej, tak aby w przypadku wystąpienia nowych alertów wiadomość e-mail została wysłana do wygenerowanej grupy osób, co eliminuje konieczność stałego sprawdzenia portalu pod kątem aktualizacji.

Aby uzyskać więcej informacji, zobacz [Ustawienia powiadomień w Azure AD Domain Services](../../active-directory-domain-services/notifications.md).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Portal usługi Azure AD obsługuje używanie interfejsu API domeny ForceDelete do usuwania domen niestandardowych

**Typ:** Zmieniono **kategorię usługi funkcji:** zarządzanie katalogami — **możliwości produktu:** katalog

Z przyjemnością ogłaszamy, że możesz teraz używać interfejsu API domeny ForceDelete do usuwania niestandardowych nazw domen, asynchronicznie zmieniając nazwy odwołań, takich jak użytkownicy, grupy i aplikacje, z niestandardowej nazwy domeny (contoso.com) z powrotem do początkowej domyślnej nazwy domeny (contoso.onmicrosoft.com).

Ta zmiana ułatwia szybkie usuwanie niestandardowych nazw domen, jeśli organizacja nie używa już nazwy lub jeśli musisz użyć nazwy domeny z inną usługą Azure AD.

Aby uzyskać więcej informacji, zobacz [usuwanie niestandardowej nazwy domeny](../users-groups-roles/domains-manage.md#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Wrzesień 2018 r.

### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Zaktualizowano uprawnienia roli administratora dla grup dynamicznych

**Typ:** **Kategoria stałej usługi:** funkcja zarządzania grupami **:** współpraca

Rozwiązano problem, dzięki czemu określone role administratorów mogą teraz tworzyć i aktualizować dynamiczne reguły członkostwa, bez konieczności być właścicielem grupy.

Role są następujące:

- Administrator globalny

- Administrator usługi Intune

- Administrator użytkowników

Aby uzyskać więcej informacji, zobacz [Tworzenie grupy dynamicznej i sprawdzanie stanu](../users-groups-roles/groups-create-rule.md)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Uproszczone ustawienia konfiguracji pojedynczego Sign-On (SSO) dla niektórych aplikacji innych firm

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** Logowanie jednokrotne

Firma Microsoft zdaje sobie sprawę, że skonfigurowanie pojedynczych Sign-On (SSO) dla aplikacji SaaS (Software as a Service) może być trudne z powodu unikatowego charakteru każdej konfiguracji aplikacji. Utworzyliśmy uproszczone środowisko konfiguracji, aby automatycznie wypełnić ustawienia konfiguracji logowania jednokrotnego dla następujących aplikacji SaaS innych firm:

- Zendesk

- ArcGis w trybie online

- Jamf Pro

Aby rozpocząć korzystanie z tego samego kliknięcia, przejdź do **Azure portal**  >  strony**Konfiguracja logowania jednokrotnego** w Azure Portal stronie konfiguracji usługi SSO dla aplikacji. Aby uzyskać więcej informacji, zobacz [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory — gdzie znajdują się Twoje dane? stronic

**Typ:** Nowa **Kategoria usługi funkcji:** inna **możliwość produktu:** GoLocal

Wybierz region firmy na stronie **Azure Active Directory — gdzie znajdują się Twoje dane** , aby zobaczyć, które centrum danych platformy Azure przechowuje dane usługi Azure AD w spoczynku dla wszystkich usług Azure AD. Informacje można filtrować według określonych usług Azure AD dla regionu firmy.

Aby uzyskać dostęp do tej funkcji i uzyskać więcej informacji, zobacz [Azure Active Directory — gdzie znajdują się Twoje dane](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Nowy plan wdrożenia dostępny dla panelu dostępu moje aplikacje

**Typ:** Nowa **Kategoria usługi funkcji:** **możliwości produktu** moje aplikacje: Logowanie jednokrotne

Zapoznaj się z nowym planem wdrożenia dostępnym dla panelu dostępu moje aplikacje ( https://aka.ms/deploymentplans) .
Panel dostępu moje aplikacje udostępnia użytkownikom pojedyncze miejsce do znajdowania i uzyskiwania dostępu do aplikacji. Ten portal udostępnia również użytkownikom możliwości samoobsługi, takich jak żądanie dostępu do aplikacji i grup lub zarządzanie dostępem do tych zasobów w imieniu innych użytkowników.

Aby uzyskać więcej informacji, zobacz artykuł [co to jest portal moje aplikacje?](../user-help/my-apps-portal-end-user-access.md)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nowa karta Rozwiązywanie problemów i pomoc techniczna na stronie dzienniki logowania Azure Portal

**Typ:** Nowa **Kategoria usługi funkcji:** raportowanie **możliwości produktu:** monitorowanie & raportowanie

Nowa karta **Rozwiązywanie problemów i pomoc techniczna** na stronie **logowania** Azure Portal ma pomóc administratorom i inżynierom pomocy technicznej rozwiązywać problemy związane z logowaniem do usługi Azure AD. Ta nowa karta zawiera kod błędu, komunikat o błędzie i zalecenia dotyczące korygowania (jeśli istnieją), aby pomóc w rozwiązaniu problemu. Jeśli nie możesz rozwiązać tego problemu, udostępnimy również nowy sposób tworzenia biletu pomocy technicznej przy użyciu funkcji **kopiowania do schowka** , która wypełnia pola **Identyfikator żądania** i **Data (UTC)** dla pliku dziennika w biletu pomocy technicznej.

![Dzienniki logowania pokazujące nową kartę](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Ulepszona obsługa niestandardowych właściwości rozszerzeń używanych do tworzenia reguł członkostwa dynamicznego

**Typ:** Zmieniono **kategorię usługi funkcji:** Zarządzanie grupą **: możliwości** współpracy

Dzięki tej aktualizacji można teraz kliknąć link **Pobierz niestandardowe właściwości rozszerzenia** z konstruktora reguły dynamicznej grupy użytkowników, wprowadzić unikatowy identyfikator aplikacji i otrzymać pełną listę niestandardowych właściwości rozszerzenia, które będą używane podczas tworzenia reguły członkostwa dynamicznego dla użytkowników. Tę listę można również odświeżyć, aby uzyskać nowe niestandardowe właściwości rozszerzenia dla tej aplikacji.

Aby uzyskać więcej informacji o używaniu niestandardowych właściwości rozszerzenia dla reguł członkostwa dynamicznego, zobacz [Właściwości rozszerzenia i niestandardowe właściwości rozszerzenia](../users-groups-roles/groups-dynamic-membership.md#extension-properties-and-custom-extension-properties) .

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie dla dostępu warunkowego opartego na aplikacji usługi Azure AD

**Typ:** Planowanie **kategorii usługi zmiany:** dostęp warunkowy do **produktu:** zabezpieczenia i Ochrona tożsamości

Następujące aplikacje znajdują się na liście [zatwierdzonych aplikacji klienckich](../conditional-access/concept-conditional-access-conditions.md#client-apps):

- Microsoft To-Do

- Usługa Microsoft Stream

Aby uzyskać więcej informacji, zobacz:

- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nowa obsługa resetowania hasła Self-Service z poziomu ekranu blokady systemu Windows 7/8/8.1

**Typ:** Nowa **Kategoria usługi funkcji:** SSPR **produktu:** uwierzytelnianie użytkownika

Po skonfigurowaniu tej nowej funkcji użytkownicy zobaczą link do resetowania hasła na ekranie **blokady** urządzenia z systemem Windows 7, Windows 8 lub Windows 8.1. Kliknięcie tego linku powoduje przeprowadzenie tego samego przepływu resetowania hasła, tak jak w przypadku przeglądarki sieci Web.

Aby uzyskać więcej informacji, zobacz [jak włączyć resetowanie haseł w systemach Windows 7, 8 i 8,1](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Zmiana powiadomienia: kody autoryzacji nie będą już dostępne do ponownego użycia

**Typ:** Planowanie **kategorii usługi zmiany:** uwierzytelnienia (logowania) **możliwości produktu:** uwierzytelnianie użytkownika

Od 15 listopada 2018 usługa Azure AD nie będzie akceptować poprzednio używanych kodów uwierzytelniania dla aplikacji. Ta zmiana zabezpieczeń pomaga w przeniesieniu usługi Azure AD zgodnie ze specyfikacją OAuth i wymuszać zarówno punkty końcowe V1, jak i v2.

Jeśli aplikacja ponownie używa kodów autoryzacji w celu uzyskania tokenów dla wielu zasobów, zalecamy użycie kodu w celu uzyskania tokenu odświeżania, a następnie użycie tego tokenu odświeżania do uzyskania dodatkowych tokenów dla innych zasobów. Kody autoryzacji mogą być używane tylko raz, ale tokeny odświeżania można wielokrotnie używać w wielu zasobach. W przypadku aplikacji próbującej ponownie użyć kodu uwierzytelniania podczas przepływu kodu OAuth zostanie wyświetlony błąd invalid_grant.

Aby zapoznać się z tymi i innymi zmianami dotyczącymi protokołów, zapoznaj [się z pełną listą nowości związanych z uwierzytelnianiem](../develop/reference-breaking-changes.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — wrzesień 2018

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** integracja z firmą

We wrześniu 2018 dodaliśmy te 16 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Uberflip](../saas-apps/uberflip-tutorial.md), [Współspotykać oprogramowanie do rekrutacji](../saas-apps/comeetrecruitingsoftware-tutorial.md), [Workteam](../saas-apps/workteam-tutorial.md), [ArcGIS Enterprise](../saas-apps/arcgisenterprise-tutorial.md), [Nuclino](../saas-apps/nuclino-tutorial.md), [JDA Cloud](../saas-apps/jdacloud-tutorial.md), [Płatne](../saas-apps/snowflake-tutorial.md), [NAVIGOCLOUD, Figma](../saas-apps/figma-tutorial.md), join.me, [ZephyrSSO](../saas-apps/zephyrsso-tutorial.md), [Silverback](../saas-apps/silverback-tutorial.md), Riverbed Xirrus EasyPass, [Rackspace SSO](../saas-apps/rackspacesso-tutorial.md), Enlyft SSO dla platformy Azure, SurveyMonkey [dmarcian](../saas-apps/dmarcian-tutorial.md) , [zwoływanie](../saas-apps/convene-tutorial.md)dmarcian

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Obsługa dodatkowych metod przekształceń oświadczeń

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** Logowanie jednokrotne

Wprowadziliśmy nowe metody przekształcania, ToLower () i ToUpper (), które mogą być stosowane do tokenów SAML na stronie **konfiguracji pojedynczej Sign-On** opartej na protokole SAML.

Aby uzyskać więcej informacji, zobacz [How to Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw w usłudze Azure AD](../develop/active-directory-saml-claims-customization.md)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Zaktualizowano interfejs użytkownika konfiguracji aplikacji opartej na protokole SAML (wersja zapoznawcza)

**Typ:** Zmieniono **kategorię usługi funkcji:** aplikacje dla przedsiębiorstw **:** Logowanie jednokrotne

W ramach naszego zaktualizowanego interfejsu użytkownika konfiguracji aplikacji opartej na protokole SAML uzyskasz następujące korzyści:

- Zaktualizowane środowisko instruktażowe dotyczące konfigurowania aplikacji opartych na protokole SAML.

- Więcej informacji na temat braku lub nieprawidłowego działania w konfiguracji.

- Możliwość dodawania wielu adresów e-mail w celu powiadomienia o wygaśnięciu certyfikatu.

- Nowe metody przekształcania roszczeń, ToLower () i ToUpper () i nie tylko.

- Sposób przekazania własnego tokenu certyfikatu podpisywania aplikacji dla przedsiębiorstw.

- Sposób ustawiania formatu NameID dla aplikacji SAML i sposobu ustawiania wartości NameID jako rozszerzeń katalogów.

Aby włączyć ten zaktualizowany widok, kliknij link **Wypróbuj nowe środowisko** w górnej części strony **logowania** jednokrotnego. Aby uzyskać więcej informacji, zobacz [Samouczek: Konfigurowanie logowania jednokrotnego opartego na protokole SAML dla aplikacji przy użyciu Azure Active Directory](../manage-apps/view-applications-portal.md).

---

## <a name="august-2018"></a>Sierpień 2018 r.

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Zmiany w zakresach adresów IP Azure Active Directory

**Typ:** Planowanie **kategorii usługi zmiany:** inne **możliwości produktu:** platforma

Wprowadzamy większy zakres adresów IP do usługi Azure AD, co oznacza, że w przypadku skonfigurowania zakresów adresów IP usługi Azure AD dla zapór, routerów lub grup zabezpieczeń sieci należy je zaktualizować. Wprowadzamy tę aktualizację, aby nie trzeba było ponownie zmieniać konfiguracji zakresu adresów IP zapory, routera lub sieci grupy zabezpieczeń, gdy usługa Azure AD dodaje nowe punkty końcowe.

Ruch sieciowy jest przenoszony do tych nowych zakresów w ciągu następnych dwóch miesięcy. Aby nadal korzystać z niezakłóconej usługi, należy dodać te zaktualizowane wartości do adresów IP przed 10 września 2018:

- 20.190.128.0/18

- 40.126.0.0/18

Zdecydowanie zalecamy, aby nie usuwać starych zakresów adresów IP, dopóki cały ruch sieciowy nie zostanie przeniesiony do nowych zakresów. Aby uzyskać aktualizacje dotyczące przenoszenia i dowiedzieć się, kiedy można usunąć stare zakresy, zobacz [adresy URL i zakresy adresów IP usługi Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Zmiana powiadomienia: kody autoryzacji nie będą już dostępne do ponownego użycia

**Typ:** Planowanie **kategorii usługi zmiany:** uwierzytelnienia (logowania) **możliwości produktu:** uwierzytelnianie użytkownika

Od 15 listopada 2018 usługa Azure AD nie będzie akceptować poprzednio używanych kodów uwierzytelniania dla aplikacji. Ta zmiana zabezpieczeń pomaga w przeniesieniu usługi Azure AD zgodnie ze specyfikacją OAuth i wymuszać zarówno punkty końcowe V1, jak i v2.

Jeśli aplikacja ponownie używa kodów autoryzacji w celu uzyskania tokenów dla wielu zasobów, zalecamy użycie kodu w celu uzyskania tokenu odświeżania, a następnie użycie tego tokenu odświeżania do uzyskania dodatkowych tokenów dla innych zasobów. Kody autoryzacji mogą być używane tylko raz, ale tokeny odświeżania można wielokrotnie używać w wielu zasobach. W przypadku aplikacji próbującej ponownie użyć kodu uwierzytelniania podczas przepływu kodu OAuth zostanie wyświetlony błąd invalid_grant.

Aby zapoznać się z tymi i innymi zmianami dotyczącymi protokołów, zapoznaj [się z pełną listą nowości związanych z uwierzytelnianiem](../develop/reference-breaking-changes.md).

---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Zbieżne zarządzanie informacjami o zabezpieczeniach dla hasła samoobsługowego (SSPR) i Multi-Factor Authentication (MFA)

**Typ:** Nowa **Kategoria usługi funkcji:** SSPR **produktu:** uwierzytelnianie użytkownika

Ta nowa funkcja ułatwia osobom zarządzanie swoimi informacjami o zabezpieczeniach (na przykład numeru telefonu, aplikacji mobilnej itd.) dla usługi SSPR i uwierzytelniania wieloskładnikowego w jednej lokalizacji i doświadczeniu. w porównaniu do wcześniej, gdzie zostało wykonane w dwóch różnych lokalizacjach.

To zbieżne środowisko działa również dla osób korzystających z usługi SSPR lub MFA. Ponadto, jeśli Twoja organizacja nie wymusi rejestracji MFA lub SSPR, użytkownicy mogą nadal rejestrować wszelkie metody dotyczące zabezpieczeń MFA lub SSPR, które mogą być używane przez organizację, z portalu My Apps.

To jest publiczna wersja zapoznawcza. Administratorzy mogą włączyć nowe środowisko (w razie potrzeby) dla wybranej grupy lub dla wszystkich użytkowników w dzierżawie. Aby uzyskać więcej informacji na temat zbieżności środowiska, zobacz [Blog dotyczący zbieżności](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nowe HTTP-Only ustawienia plików cookie w aplikacjach serwera proxy aplikacji usługi Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** **możliwości produktu** serwera proxy aplikacji: Access Control

Istnieje nowe ustawienie o nazwie, czyli **pliki cookie** w aplikacjach serwera proxy aplikacji. To ustawienie pomaga zapewnić dodatkowe zabezpieczenia, dołączając flagę HTTPOnly w nagłówku odpowiedzi HTTP w przypadku dostępu do serwera proxy aplikacji i plików cookie sesji, zatrzymując dostęp do pliku cookie ze skryptu po stronie klienta i bardziej zapobiegając tym akcjom, takim jak kopiowanie lub modyfikowanie pliku cookie. Mimo że ta flaga nie została wcześniej użyta, pliki cookie są zawsze szyfrowane i przesyłane przy użyciu połączenia TLS, aby chronić je przed niewłaściwymi modyfikacjami.

To ustawienie nie jest zgodne z aplikacjami korzystającymi z kontrolek ActiveX, takich jak Pulpit zdalny. W tej sytuacji zalecamy wyłączenie tego ustawienia.

Aby uzyskać więcej informacji na temat ustawienia HTTP-Only plików cookie, zobacz [publikowanie aplikacji za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD](../manage-apps/application-proxy-add-on-premises-application.md).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) dla zasobów platformy Azure obsługuje typy zasobów grupy zarządzania

**Typ:** Nowa **Kategoria usługi funkcji:** Privileged Identity Management **możliwości produktu:** Privileged Identity Management

Ustawienia aktywacji just in Time i przypisania można teraz zastosować do typów zasobów grupy zarządzania, podobnie jak w przypadku subskrypcji, grup zasobów i zasobów (takich jak maszyny wirtualne, App Services i inne). Ponadto każda osoba z rolą, która zapewnia dostęp administratora dla grupy zarządzania, może odnajdywać ten zasób i zarządzać nim w usłudze PIM.

Aby uzyskać więcej informacji na temat usług PIM i zasobów platformy Azure, zobacz [odnajdywanie zasobów platformy Azure i zarządzanie nimi przy użyciu Privileged Identity Management](../privileged-identity-management/pim-resource-roles-discover-resources.md)

---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Dostęp do aplikacji (wersja zapoznawcza) zapewnia szybszy dostęp do portalu usługi Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** Privileged Identity Management **możliwości produktu:** Privileged Identity Management

Obecnie w przypadku aktywowania roli przy użyciu usługi PIM, aby uprawnienia zaczęły obowiązywać, może upłynąć ponad 10 minut. Jeśli zdecydujesz się korzystać z dostępu do aplikacji, który jest obecnie dostępny w publicznej wersji zapoznawczej, Administratorzy mogą uzyskać dostęp do portalu usługi Azure AD zaraz po zakończeniu żądania aktywacji.

Obecnie dostęp do aplikacji obsługuje tylko środowisko portalu usługi Azure AD i zasoby platformy Azure. Aby uzyskać więcej informacji na temat usługi PIM i dostępu do aplikacji, zobacz [co to jest Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — sierpień 2018

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** integracja z firmą

W sierpniu 2018 dodaliśmy te 16 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Hornbill](../saas-apps/hornbill-tutorial.md), [bridgeline Unbound](../saas-apps/bridgelineunbound-tutorial.md), [sosy Labs — testowanie dla urządzeń przenośnych i sieci Web](../saas-apps/saucelabs-mobileandwebtesting-tutorial.md), [Łączniki meta Networks](../saas-apps/metanetworksconnector-tutorial.md), [jak to zrobisz](../saas-apps/waywedo-tutorial.md), [Spotinst](../saas-apps/spotinst-tutorial.md), [premaster (Inlogik)](../saas-apps/promaster-tutorial.md) [eLuminate](../saas-apps/eluminate-tutorial.md), [Dovetale](../saas-apps/dovetale-tutorial.md) [SchoolBooking,](../saas-apps/safeconnect-tutorial.md) [4me](../saas-apps/4me-tutorial.md) [,](../saas-apps/zenqms-tutorial.md) [Dokumentacja](../saas-apps/dossier-tutorial.md), [N2F](../saas-apps/n2f-expensereports-tutorial.md) [Comm100 Live Chat](../saas-apps/comm100livechat-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Natywna obsługa Tableau jest teraz dostępna na platformie Azure serwer proxy aplikacji usługi Azure AD

**Typ:** Zmieniono **kategorię usługi funkcji:** produkt proxy aplikacji **:** Access Control

Nasza Aktualizacja z usługi OpenID Connect nawiązuje połączenie z protokołem uwierzytelniania OAuth 2,0 dla tego protokołu, nie trzeba już wykonywać żadnych dodatkowych czynności konfiguracyjnych, aby używać Tableau z serwerem proxy aplikacji. Ta zmiana protokołu pomaga również lepiej obsługiwać serwer proxy aplikacji, używając tylko przekierowań HTTP, które są powszechnie obsługiwane w języku JavaScript i w tagach HTML.

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nowe wsparcie do dodawania usługi Google jako dostawcy tożsamości dla użytkowników gościa B2B w Azure Active Directory (wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** **produkt B2B:** B2B/B2C

Przez skonfigurowanie Federacji w usłudze Google w organizacji możesz zezwolić zaproszonym użytkownikom usługi Gmail na logowanie się do udostępnionych aplikacji i zasobów przy użyciu istniejącego konta Google, bez konieczności tworzenia osobistego konta Microsoft (kont MSA) lub konta usługi Azure AD.

To jest publiczna wersja zapoznawcza. Aby uzyskać więcej informacji na temat usługi Google Federation, zobacz [Dodawanie usługi Google jako dostawcy tożsamości dla użytkowników-Gości B2B](../external-identities/google-federation.md).

---

## <a name="july-2018"></a>Lipiec 2018 r.

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Ulepszenia Azure Active Directory powiadomień e-mail

**Typ:** Zmieniono **kategorię usługi funkcji:** inna **możliwość produktu:** Zarządzanie cyklem życia tożsamości

Wiadomości e-mail w usłudze Azure Active Directory (Azure AD) teraz oferują zaktualizowany projekt, a także zmiany w adresie e-mail nadawcy i nazwę wyświetlaną nadawcy, gdy są wysyłane z następujących usług:

- Przeglądy dostępu w usłudze Azure AD
- Azure AD Connect Health
- Usługa Azure AD Identity Protection
- Azure AD Privileged Identity Management
- Powiadomienie o wygaśnięciu certyfikatu aplikacji dla przedsiębiorstw
- Powiadomienia usługi aprowizacji aplikacji dla przedsiębiorstw

Powiadomienia e-mail będą wysyłane z następującego adresu e-mail i nazwy wyświetlanej:

- Adres e-mail: azure-noreply@microsoft.com
- Nazwa wyświetlana: Microsoft Azure

Aby zapoznać się z przykładem niektórych nowych projektów poczty e-mail i więcej informacji, zobacz [powiadomienia e-mail w usłudze Azure AD PIM](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Dzienniki aktywności usługi Azure AD są teraz dostępne za pomocą Azure Monitor

**Typ:** Nowa **Kategoria usługi funkcji:** raportowanie **możliwości produktu:** monitorowanie & raportowanie

Dzienniki aktywności usługi Azure AD są teraz dostępne w publicznej wersji zapoznawczej dla Azure Monitor (usługa monitorowania na poziomie platformy Azure). Azure Monitor oferuje długoterminowe przechowywanie i bezproblemową integrację, a także następujące ulepszenia:

- Długoterminowe przechowywanie przez kierowanie plików dziennika na własne konto usługi Azure Storage.

- Bezproblemowa integracja z usługą SIEM, bez konieczności pisania i konserwowania skryptów niestandardowych.

- Bezproblemowa integracja z własnymi rozwiązaniami niestandardowymi, narzędziami do analizy lub rozwiązaniami do zarządzania zdarzeniami.

Aby uzyskać więcej informacji na temat tych nowych funkcji, zobacz [dzienniki aktywności usługi Azure AD w blogu w Azure monitor Diagnostyka jest teraz w publicznej wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) i naszej dokumentacji, [Azure Active Directory dzienników aktywności w Azure monitor (wersja zapoznawcza)](../reports-monitoring/concept-activity-logs-azure-monitor.md).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informacje o dostępie warunkowym dodane do raportu logowania do usługi Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** raportowanie **możliwości produktu:** zabezpieczenia tożsamości & ochrona

Ta aktualizacja pozwala zobaczyć, które zasady są oceniane po zalogowaniu się użytkownika wraz z wynikiem zasad. Ponadto raport zawiera teraz typ aplikacji klienckiej używanej przez użytkownika, aby można było zidentyfikować starszy ruch protokołu. W przypadku wpisów raportu można również przeszukać identyfikator korelacji, który można znaleźć w komunikacie o błędzie dostępnym dla użytkownika i może służyć do identyfikowania i rozwiązywania problemów dotyczących pasującego żądania logowania.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Wyświetlanie starszych uwierzytelnień za poorednictwem dzienników aktywności logowania

**Typ:** Nowa **Kategoria usługi funkcji:** raportowanie **możliwości produktu:** monitorowanie & raportowanie

Po wprowadzeniu pola **aplikacji klienta** w dziennikach aktywności logowania klienci mogą teraz zobaczyć użytkowników korzystających ze starszych uwierzytelnień. Klienci będą mogli uzyskać dostęp do tych informacji przy użyciu interfejsu API logowania Microsoft Graph lub za pośrednictwem dzienników aktywności logowania w portalu usługi Azure AD, gdzie można użyć kontrolki **App Client** do filtrowania starszych uwierzytelnień. Zapoznaj się z dokumentacją, aby uzyskać więcej szczegółów.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — lipiec 2018

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** integracja z firmą

W lipcu 2018 dodaliśmy te 16 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Centra innowacji](../saas-apps/innovationhub-tutorial.md), [Leapsome](../saas-apps/leapsome-tutorial.md), [niektóre administratora logowania jednokrotnego](../saas-apps/certainadminsso-tutorial.md), PSUC, [IPass SmartConnect](../saas-apps/ipasssmartconnect-tutorial.md), [SmartLPA](../saas-apps/smartlpa-tutorial.md) [Kanbanize](../saas-apps/kanbanize-tutorial.md) [zrzut ekranu przedstawiający-O-Matic](../saas-apps/screencast-tutorial.md), PowerSchool ujednoliconej klasy, [eli](../saas-apps/elionboarding-tutorial.md), [Bomgar, NimbleX](../saas-apps/bomgarremotesupport-tutorial.md), [Insight4GRC,](../saas-apps/nimblex-tutorial.md) [Imagineer WebVision](../saas-apps/imagineerwebvision-tutorial.md) [Insight4GRC](../saas-apps/insight4grc-tutorial.md) [SecureW2 JoinNow Connector](../saas-apps/securejoinnow-tutorial.md) [Skills Base](../saas-apps/skillsbase-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Nowi użytkownicy z integracją aplikacji SaaS — lipiec 2018

**Typ:** Nowa **Kategoria usługi funkcji:** funkcja aprowizacji aplikacji w **produkcie:** integracja innej firmy

Usługa Azure AD umożliwia automatyzację tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach SaaS, takich jak Dropbox, Salesforce, usługi ServiceNow i inne. W lipcu 2018 dodaliśmy obsługę aprowizacji użytkowników dla następujących aplikacji w galerii aplikacji usługi Azure AD:

- [Cisco WebEx](../saas-apps/cisco-webex-provisioning-tutorial.md)

- [Bonusly](../saas-apps/bonusly-provisioning-tutorial.md)

Aby uzyskać listę wszystkich aplikacji, które obsługują Inicjowanie obsługi użytkowników w galerii usługi Azure AD, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Łączenie kondycji z synchronizacją — łatwiejszym sposobem naprawienia błędów synchronizacji atrybutów oddzielonych i zduplikowanych

**Typ:** Nowa **Kategoria usługi funkcji:** **możliwości produktu** AD Connect: monitorowanie & raportowanie

Azure AD Connect Health wprowadza korekty samoobsługowe, aby pomóc w wyróżnieniu i usunięciu błędów synchronizacji. Ta funkcja rozwiązuje zduplikowane błędy synchronizacji atrybutów i naprawia obiekty oddzielone od usługi Azure AD. Ta Diagnostyka ma następujące zalety:

- Ogranicza powielone błędy synchronizacji atrybutów, dostarczając określone poprawki

- Stosuje poprawkę dla dedykowanych scenariuszy usługi Azure AD, rozwiązując błędy w jednym kroku

- Aby włączyć tę funkcję i korzystać z niej, uaktualnienie lub konfiguracja nie są wymagane

Aby uzyskać więcej informacji, zobacz [diagnozowanie i korygowanie zduplikowanych błędów synchronizacji atrybutów](../hybrid/how-to-connect-health-diagnose-sync-errors.md)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Aktualizacje wizualne dla środowiska logowania do usługi Azure AD i MSA

**Typ:** Zmieniono **kategorię usługi funkcji:** produkt Azure AD **:** uwierzytelnianie użytkownika

Zaktualizowaliśmy interfejs użytkownika dla środowiska logowania Usługi online firmy Microsoft, na przykład dla pakietu Office 365 i platformy Azure. Ta zmiana powoduje, że ekrany są mniej czytelne i bardziej proste. Aby uzyskać więcej informacji na temat tej zmiany, zobacz [nadchodzące ulepszenia blogu logowania do usługi Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) .

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nowa wersja Azure AD Connect — lipiec 2018

**Typ:** Zmieniono **kategorię usługi funkcji:** **możliwości produktu** aprowizacji aplikacji: Zarządzanie cyklem życia tożsamości

Najnowsza wersja Azure AD Connect obejmuje:

- Poprawki błędów i obsługa aktualizacji

- Ogólna dostępność integracji Ping-Federate

- Aktualizacje najnowszego klienta programu SQL 2012

Aby uzyskać więcej informacji na temat tej aktualizacji, zobacz [Azure AD Connect: historia](../hybrid/reference-connect-version-history.md) wersji

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Aktualizacje warunków użytkowania użytkownika końcowego

**Typ:** Zmieniono **kategorię usługi funkcji:** warunki użytkowania **możliwości produktu:** ładu

Aktualizujemy ciąg akceptacji w INTERFEJSie użytkownika końcowego warunków użytkowania.

**Bieżący tekst.** Aby uzyskać dostęp do zasobów [dzierżawcname], musisz zaakceptować warunki użytkowania.<br>**Nowy tekst.** Aby uzyskać dostęp do zasobu [dzierżawcname], należy przeczytać warunki użytkowania.

**Bieżący tekst:** Wybranie opcji Akceptuj oznacza, że akceptujesz wszystkie powyższe warunki użytkowania.<br>**Nowy tekst:** Kliknij przycisk Akceptuj, aby potwierdzić, że znasz i rozumiesz warunki użytkowania.

---

### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Uwierzytelnianie przekazywane obsługuje starsze protokoły i aplikacje

**Typ:** Zmieniono **kategorię usługi funkcji:** uwierzytelnienia (logowania) **możliwości produktu:** uwierzytelnianie użytkownika

Uwierzytelnianie przekazywane obsługuje teraz starsze protokoły i aplikacje. Następujące ograniczenia są obecnie w pełni obsługiwane:

- Logowania użytkowników do starszych aplikacji klienckich pakietu Office, pakietu Office 2010 i pakietu Office 2013, bez konieczności korzystania z nowoczesnego uwierzytelniania.

- Dostęp do udostępniania kalendarza oraz informacji o wolnych/zajętych środowiskach programu Exchange można uzyskać tylko w pakiecie Office 2010.

- Logowania użytkowników do aplikacji klienckich Skype dla firm, które nie wymagają nowoczesnego uwierzytelniania.

- Logowania użytkowników do programu PowerShell w wersji 1,0.

- Apple Device Enrollment Program (Apple DEP) przy użyciu Asystenta ustawień systemu iOS.

---

### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Zbieżne zarządzanie informacjami o zabezpieczeniach w celu samoobsługowego resetowania haseł i Multi-Factor Authentication

**Typ:** Nowa **Kategoria usługi funkcji:** SSPR **produktu:** uwierzytelnianie użytkownika

Ta nowa funkcja umożliwia użytkownikom zarządzanie informacjami o zabezpieczeniach (na przykład numeru telefonu, adresu e-mail, aplikacji mobilnej itd.) na potrzeby samoobsługowego resetowania haseł (SSPR) i Multi-Factor Authentication (MFA) w jednym środowisku. Użytkownicy nie będą już musieli rejestrować tych samych informacji zabezpieczających dla usługi SSPR i MFA w dwóch różnych środowiskach. To nowe środowisko dotyczy również użytkowników, którzy mają SSPR lub MFA.

Jeśli organizacja nie wymusza rejestracji MFA lub SSPR, użytkownicy mogą zarejestrować swoje informacje zabezpieczające za pomocą portalu **My Apps** . W tym miejscu użytkownicy mogą rejestrować wszystkie metody z włączonym uwierzytelnianiem MFA lub SSPR.

To jest publiczna wersja zapoznawcza. Administratorzy mogą włączyć nowe środowisko (jeśli to konieczne) dla wybranej grupy użytkowników lub wszystkich użytkowników w dzierżawie.

---

### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Użyj aplikacji Microsoft Authenticator, aby zweryfikować swoją tożsamość w przypadku zresetowania hasła

**Typ:** Zmieniono **kategorię usługi funkcji:** SSPR **produktu:** uwierzytelnianie użytkownika

Ta funkcja umożliwia administratorom innym niż administratorzy weryfikowanie tożsamości podczas resetowania hasła przy użyciu powiadomienia lub kodu z Microsoft Authenticator (lub dowolnej innej aplikacji uwierzytelniania). Gdy administratorzy włączą tę metodę samoobsługowego resetowania hasła, użytkownicy, którzy zarejestrowali aplikację mobilną za pomocą usługi aka.ms/mfasetup lub aka.ms/setupsecurityinfo, mogą używać swojej aplikacji mobilnej jako metody weryfikacji podczas resetowania hasła.

Powiadomienie aplikacji mobilnej można włączyć tylko w ramach zasad, które wymagają dwóch metod resetowania hasła.

---

## <a name="june-2018"></a>Czerwiec 2018 r.

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Zmiana powiadomienia: Poprawka zabezpieczeń do delegowanego przepływu autoryzacji dla aplikacji korzystających z interfejsu API dzienników aktywności usługi Azure AD

**Typ:** Zaplanuj **kategorię usługi zmiany:** raportowanie **możliwości produktu:** monitorowanie & raportowanie

Ze względu na nasze silniejsze Wymuszanie zabezpieczeń musiałeś wprowadzić zmianę uprawnień dla aplikacji, które korzystają z delegowanego przepływu autoryzacji w celu uzyskiwania dostępu do [interfejsów API dzienników aktywności usługi Azure AD](https://aka.ms/aadreportsapi). Ta zmiana będzie miała miejsce do **26 czerwca 2018**.

Jeśli dowolna z aplikacji korzysta z interfejsów API dzienników aktywności usługi Azure AD, wykonaj następujące kroki, aby upewnić się, że aplikacja nie przerwie się po zmianie.

**Aby zaktualizować uprawnienia aplikacji**

1. Zaloguj się do Azure Portal, wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **rejestracje aplikacji**.
2. Wybierz aplikację korzystającą z interfejsu API dzienników aktywności usługi Azure AD, wybierz pozycję **Ustawienia**, wybierz pozycję **wymagane uprawnienia**, a następnie wybierz pozycję **Windows Azure Active Directory** API.
3. W obszarze **delegowane uprawnienia** w bloku **Włącz dostęp** zaznacz pole wyboru obok pozycji **Odczytaj dane katalogu** , a następnie wybierz pozycję **Zapisz**.
4. Wybierz pozycję **Udziel uprawnień**, a następnie wybierz pozycję **tak**.

    >[!Note]
    >Musisz być administratorem globalnym, aby udzielić uprawnień do aplikacji.

Aby uzyskać więcej informacji, zobacz temat [udzielanie uprawnień](../reports-monitoring/howto-configure-prerequisites-for-reporting-api.md#grant-permissions) w obszarze wymagania wstępne, aby uzyskać dostęp do artykułu interfejsu API raportowania usługi Azure AD.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Konfigurowanie ustawień protokołu TLS w celu nawiązania połączenia z usługami Azure AD na potrzeby PCI DSS zgodności

**Typ:** Nowa **Kategoria usługi funkcji:** brak **możliwości produktu:** platforma

Transport Layer Security (TLS) to protokół, który zapewnia prywatność i integralność danych między dwiema komunikującymi się aplikacjami i jest najczęściej wdrożonym protokołem zabezpieczeń.

[Rada standardu zabezpieczeń PCI](https://www.pcisecuritystandards.org/) ustaliła, że wczesne wersje protokołu TLS i SSL (SSL) muszą zostać wyłączone na korzyść, aby włączyć nowe i bezpieczniejsze protokoły aplikacji, z zachowaniem zgodności rozpoczynającym się **30 czerwca 2018**. Ta zmiana oznacza, że w przypadku nawiązania połączenia z usługami Azure AD i wymagania zgodności PCI DSS należy wyłączyć protokół TLS 1,0. Dostępnych jest wiele wersji protokołu TLS, ale protokół TLS 1,2 to Najnowsza wersja dostępna dla Azure Active Directory usług. Zdecydowanie zalecamy Przechodzenie bezpośrednio do protokołu TLS 1,2 dla kombinacji klient/serwer i przeglądarka/serwer.

Nieaktualne przeglądarki mogą nie obsługiwać nowszych wersji protokołu TLS, takich jak TLS 1,2. Aby sprawdzić, które wersje protokołu TLS są obsługiwane przez przeglądarkę, przejdź do witryny [Qualyse SSL Labs](https://www.ssllabs.com/) i kliknij pozycję **Testuj przeglądarkę**. Zalecamy przeprowadzenie uaktualnienia do najnowszej wersji przeglądarki sieci Web i zaleca włączenie tylko protokołu TLS 1,2.

**Aby włączyć protokół TLS 1,2 w przeglądarce**

- **Microsoft Edge i Internet Explorer (oba są ustawiane przy użyciu programu Internet Explorer)**

    1. Otwórz program Internet Explorer, wybierz pozycję **Narzędzia**  >  **Opcje internetowe**  >  **Zaawansowane**.
    2. W obszarze **zabezpieczenia** wybierz opcję **użyj protokołu TLS 1,2**, a następnie wybierz przycisk **OK**.
    3. Zamknij wszystkie okna przeglądarki i ponownie uruchom program Internet Explorer.

- **Google Chrome**

    1. Otwórz program Google Chrome, wpisz *Chrome://Settings/* na pasku adresu i naciśnij klawisz **Enter**.
    2. Rozwiń opcje **Zaawansowane** , przejdź do obszaru **system** , a następnie wybierz pozycję **Otwórz ustawienia serwera proxy**.
    3. W oknie **Właściwości internetowe** wybierz kartę **Zaawansowane** , przejdź do obszaru **zabezpieczenia** , wybierz opcję **Użyj protokołu TLS 1,2**, a następnie wybierz przycisk **OK**.
    4. Zamknij wszystkie okna przeglądarki i uruchom ponownie usługę Google Chrome.

- **Mozilla Firefox**

    1. Otwórz przeglądarkę Firefox, wpisz *informacje: config* na pasku adresu, a następnie naciśnij klawisz **Enter**.
    2. Wyszukaj termin, *TLS*, a następnie wybierz wpis **Security. TLS. Version. Max** .
    3. Ustaw wartość na **3** , aby wymusić, aby przeglądarka korzystała z do wersji TLS 1,2, a następnie wybierz **przycisk OK**.

        >[!NOTE]
        >Firefox w wersji 60,0 obsługuje protokół TLS 1,3, więc można także ustawić wartość Security. TLS. Version. max na **4**.

    4. Zamknij wszystkie okna przeglądarki i uruchom ponownie Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — czerwiec 2018

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** integracja z firmą

W czerwcu 2018 dodaliśmy następujące 15 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Skytap](../saas-apps/skytap-tutorial.md), [rozliczanie muzyki](../saas-apps/settlingmusic-tutorial.md), [obsługa tokenów SAML 1,1 — aplikacja biznesowa](../saas-apps/saml-tutorial.md), [Nadstrój](../saas-apps/supermood-tutorial.md), [autozadanie](../saas-apps/autotaskendpointbackup-tutorial.md), [kopia zapasowa punktu końcowego](../saas-apps/autotaskendpointbackup-tutorial.md), [sieci SkyHigh](../saas-apps/skyhighnetworks-tutorial.md), Smartway2, [TONICDM](../saas-apps/tonicdm-tutorial.md), [Moconavi](../saas-apps/moconavi-tutorial.md), [Zoho one](../saas-apps/zohoone-tutorial.md), [lokalne, program SharePoint](../saas-apps/sharepoint-on-premises-tutorial.md), [przewidzieć pakiet CX](../saas-apps/foreseecxsuite-tutorial.md), [Vidyard](../saas-apps/vidyard-tutorial.md), [ChronicX](../saas-apps/chronicx-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md).

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Ochrona hasłem w usłudze Azure AD jest dostępna w publicznej wersji zapoznawczej

**Typ:** Nowa **Kategoria usługi funkcji:** **zdolność produktu** do ochrony tożsamości: uwierzytelnianie użytkownika

Użyj ochrony hasłem usługi Azure AD, aby wyeliminować łatwo odgadnąć hasła ze środowiska. Usunięcie tych haseł ułatwia zmniejszenie ryzyka naruszenia typu rozpylania hasła.

W programie Ochrona hasłem w usłudze Azure AD ułatwia:

- Chroń konta organizacji zarówno w usłudze Azure AD, jak i w systemie Windows Server Active Directory (AD).
- Uniemożliwia użytkownikom korzystanie z haseł na liście ponad 500 najczęściej używanych haseł oraz o ponad 1 000 000 zmienności znaków zastępczych tych haseł.
- Administruj ochroną hasłem usługi Azure AD z pojedynczej lokalizacji w portalu usługi Azure AD, w przypadku usługi Azure AD i lokalnej usługi AD systemu Windows Server.

Aby uzyskać więcej informacji na temat ochrony hasłem w usłudze Azure AD, zobacz [eliminowanie nieprawidłowych haseł w organizacji](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nowy szablon zasad dostępu warunkowego "Wszyscy Goście" utworzony podczas tworzenia warunków użytkowania

**Typ:** Nowa **Kategoria usługi funkcji:** warunki użytkowania **możliwości produktu:** ładu

Podczas tworzenia warunków użytkowania nowy szablon zasad dostępu warunkowego jest również tworzony dla "Wszyscy Goście" i "wszystkie aplikacje". Ten nowy szablon zasad stosuje nowo utworzone warunków użytkowania, usprawniając proces tworzenia i wymuszania dla Gości.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory warunki użytkowania funkcji](../conditional-access/terms-of-use.md).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nowy "niestandardowy" szablon zasad dostępu warunkowego utworzony podczas tworzenia warunków użytkowania

**Typ:** Nowa **Kategoria usługi funkcji:** warunki użytkowania **możliwości produktu:** ładu

Podczas tworzenia warunków użytkowania tworzony jest również nowy "niestandardowy" szablon zasad dostępu warunkowego. Ten nowy szablon zasad umożliwia utworzenie warunków użytkowania, a następnie natychmiastowe przejście do bloku tworzenia zasad dostępu warunkowego, bez konieczności ręcznego nawigowania po portalu.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory warunki użytkowania funkcji](../conditional-access/terms-of-use.md).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Nowe i kompleksowe wskazówki dotyczące wdrażania usługi Azure Multi-Factor Authentication

**Typ:** Nowa **Kategoria usługi funkcji:** inna **możliwość produktu:** zabezpieczenia tożsamości & ochrona

Opublikowano nowe wskazówki krok po kroku dotyczące sposobu wdrażania usługi Azure Multi-Factor Authentication (MFA) w organizacji.

Aby wyświetlić przewodnik wdrażania usługi MFA, przejdź do repozytorium [przewodniki wdrażania tożsamości](https://aka.ms/DeploymentPlans) w witrynie GitHub. Aby przekazać opinię na temat przewodników wdrażania, użyj [formularza Opinia dotycząca planu wdrożenia](https://aka.ms/deploymentplanfeedback). Jeśli masz jakieś pytania dotyczące przewodników wdrażania, skontaktuj się z nami pod adresem [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Role zarządzania aplikacjami delegowanymi usługi Azure AD znajdują się w publicznej wersji zapoznawczej

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** Access Control

Administratorzy mogą teraz delegować zadania zarządzania aplikacjami bez przypisywania roli administratora globalnego. Nowe role i możliwości są następujące:

- **Nowe role administratorów usługi Azure AD:**

    - **Administrator aplikacji.** Umożliwia zarządzanie wszystkimi aspektami wszystkich aplikacji, w tym rejestrowaniem, ustawieniami logowania jednokrotnego, przypisaniami aplikacji i licencjonowaniem, ustawieniami serwera proxy aplikacji oraz zgodą (z wyjątkiem zasobów usługi Azure AD).

    - **Administrator aplikacji w chmurze.** Przyznaje wszystkim możliwościom administratora aplikacji, z wyjątkiem serwera proxy aplikacji, ponieważ nie zapewnia dostępu lokalnego.

    - **Deweloper aplikacji.** Przyznaje możliwość tworzenia rejestracji aplikacji, nawet jeśli opcja **Zezwalaj użytkownikom na rejestrowanie aplikacji** jest wyłączona.

- **Własność (skonfiguruj rejestrację dla aplikacji i aplikację dla przedsiębiorstw, podobnie jak w przypadku procesu własności grupy:**

    - **Właściciel rejestracji aplikacji.** Umożliwia zarządzanie wszystkimi aspektami rejestracji aplikacji, w tym manifestem aplikacji i dodawaniem dodatkowych właścicieli.

    - **Właściciel aplikacji przedsiębiorstwa.** Umożliwia zarządzanie wieloma aspektami należącymi do firmowych aplikacji, w tym ustawieniami logowania jednokrotnego, przypisaniami aplikacji i zgodą (z wyjątkiem zasobów usługi Azure AD).

Aby uzyskać więcej informacji na temat publicznej wersji zapoznawczej, zobacz [role zarządzania aplikacjami delegowanymi usługi Azure AD są w publicznej wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) . wpisów. Aby uzyskać więcej informacji o rolach i uprawnieniach, zobacz [Przypisywanie ról administratorów w Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

---

## <a name="may-2018"></a>Maj 2018 r.

### <a name="expressroute-support-changes"></a>ExpressRoute zmiany obsługi

**Typ:** Zaplanuj **kategorię usługi zmiany:** uwierzytelnienia (logowania) **możliwości produktu:** platforma

Oprogramowanie jako oferta usługi, takie jak Azure Active Directory (Azure AD), jest przeznaczone do pracy najlepiej, przechodząc bezpośrednio przez Internet, bez konieczności ExpressRoute lub innych prywatnych tuneli VPN. W związku z tym w dniu **1 sierpnia 2018**ExpressRoutemy obsługę usługi Azure AD przy użyciu publicznej komunikacji równorzędnej Azure i społeczności platformy Azure w komunikacji równorzędnej firmy Microsoft. Wszystkie usługi, których dotyczy ta zmiana, mogą zauważyć, że ruch usługi Azure AD stopniowo przesunie się z ExpressRoute do Internetu.

Chociaż zmieniamy naszą pomoc techniczną, wiemy, że nadal są sytuacje, w których może być konieczne użycie dedykowanego zestawu obwodów dla ruchu związanego z uwierzytelnianiem. W związku z tym usługa Azure AD będzie kontynuowała obsługę ograniczeń zakresu adresów IP dla dzierżawców przy użyciu ExpressRoute i usług, które są już w komunikacji równorzędnej firmy Microsoft z społecznością "inne usługi Office 365 Online Services". Jeśli ma to wpływ na usługi, ale wymagane jest ExpressRoute, należy wykonać następujące czynności:

- **Jeśli korzystasz z publicznej komunikacji równorzędnej Azure.** Przejdź do komunikacji równorzędnej firmy Microsoft i zarejestruj się, aby skorzystać z **innych społeczności usługi Office 365 Online Services (12076:5100)** . Aby uzyskać więcej informacji na temat przenoszenia z publicznej komunikacji równorzędnej Azure do komunikacji równorzędnej firmy Microsoft, zobacz artykuł [przenoszenie publicznej komunikacji równorzędnej do firmy Microsoft](../../expressroute/how-to-move-peering.md) .

- **Jeśli używasz komunikacji równorzędnej firmy Microsoft.** Utwórz konto w **innej społeczności usługi Office 365 Online Service (12076:5100)** . Więcej informacji o wymaganiach dotyczących routingu znajduje się w [sekcji Obsługa Wspólnot protokołu BGP](../../expressroute/expressroute-routing.md#bgp) w artykule wymagania dotyczące routingu ExpressRoute.

Jeśli musisz nadal używać dedykowanych obwodów, musisz skontaktować się z zespołem ds. kont Microsoft, aby uzyskać autoryzację do korzystania z **innej społeczności usługi Office 365 Online Service (12076:5100)** . Tablica przeglądu zarządzanego przez pakiet MS Office sprawdzi, czy potrzebne są te obwody, i zadbaj o to, aby poznać techniczne konsekwencje ich utrzymania. Nieautoryzowane subskrypcje próbujące utworzyć filtry tras dla pakietu Office 365 otrzymają komunikat o błędzie.

---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph interfejsów API dla scenariuszy administracyjnych dla warunków użytkowania

**Typ:** Nowa **Kategoria usługi funkcji:** warunki użytkowania **możliwości produktu:** środowisko programistyczne

Dodaliśmy Microsoft Graph interfejsów API dla operacji administrowania warunkami użytkowania usługi Azure AD. Możesz tworzyć, aktualizować i usuwać obiekty warunków użytkowania.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Dodawanie wielodostępnego punktu końcowego usługi Azure AD jako dostawcy tożsamości w Azure AD B2C

**Typ:** Nowa **Kategoria usługi funkcji:** B2C — **możliwości produktu** zarządzania tożsamościami konsumenta: B2B/B2C

Korzystając z zasad niestandardowych, można teraz dodać wspólny punkt końcowy usługi Azure AD jako dostawcę tożsamości w Azure AD B2C. Dzięki temu można mieć pojedynczy punkt wejścia dla wszystkich użytkowników usługi Azure AD, którzy logują się do aplikacji. Aby uzyskać więcej informacji, zobacz [Azure Active Directory B2C: Zezwalanie użytkownikom na logowanie się do dostawcy tożsamości usługi Azure AD z wieloma dzierżawcami przy użyciu zasad niestandardowych](../../active-directory-b2c/identity-provider-azure-ad-multi-tenant-custom.md).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Użyj wewnętrznych adresów URL, aby uzyskiwać dostęp do aplikacji z dowolnego miejsca przy użyciu rozszerzenia logowania moje aplikacje i usługi Azure serwer proxy aplikacji usługi Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** **możliwości produktu** moje aplikacje: Logowanie jednokrotne

Użytkownicy mogą teraz uzyskiwać dostęp do aplikacji za pośrednictwem wewnętrznych adresów URL nawet w przypadku, gdy poza siecią firmową, za pomocą rozszerzenia moje aplikacje bezpieczne logowanie do usługi Azure AD. Będzie to współpracować z każdą aplikacją opublikowaną przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD, w dowolnej przeglądarce, która ma także zainstalowane rozszerzenie przeglądarki panelu dostępu. Funkcja przekierowywania adresów URL jest włączana automatycznie po zalogowaniu się użytkownika do rozszerzenia. Rozszerzenie jest dostępne do pobrania w przeglądarkach [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)i [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---

### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory — dane w Europie dla klientów Europy

**Typ:** Nowa **Kategoria usługi funkcji:** inna **możliwość produktu:** GoLocal

Klienci w Europie wymagają, aby dane były przechowywane w Europie i nie zostały zreplikowane poza europejskimi centrami danych w celu zachowania poufności i prawa europejskiego. Ten [artykuł](https://go.microsoft.com/fwlink/?linkid=872328) zawiera szczegółowe informacje dotyczące informacji o tożsamościach, które będą przechowywane w Europie, a także informacje o informacjach, które będą przechowywane poza europejskimi centrami danych.

---

### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nowe Integracje aplikacji SaaS dla użytkowników — może 2018

**Typ:** Nowa **Kategoria usługi funkcji:** funkcja aprowizacji aplikacji w **produkcie:** integracja innej firmy

Usługa Azure AD umożliwia automatyzację tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach SaaS, takich jak Dropbox, Salesforce, usługi ServiceNow i inne. W przypadku 2018 maja dodaliśmy obsługę aprowizacji użytkowników dla następujących aplikacji w galerii aplikacji usługi Azure AD:

- [BlueJeans](../saas-apps/bluejeans-provisioning-tutorial.md)

- [Cornerstone OnDemand](../saas-apps/cornerstone-ondemand-provisioning-tutorial.md)

- [Zendesk](../saas-apps/zendesk-provisioning-tutorial.md)

Aby uzyskać listę wszystkich aplikacji, które obsługują Inicjowanie obsługi użytkowników w galerii usługi Azure AD, zobacz [https://aka.ms/appstutorial](https://aka.ms/appstutorial) .

---

### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Przeglądy dostępu w usłudze Azure AD dotyczące grup i dostępu do aplikacji oferują teraz cykliczne przeglądy

**Typ:** Nowa **Kategoria usługi funkcji:** **możliwości produktu** przeglądy dostępu: ładu

Przegląd dostępu do grup i aplikacji jest teraz ogólnie dostępny w ramach Azure AD — wersja Premium P2.  Administratorzy będą mogli konfigurować przeglądy dostępu dla członkostwa w grupach i przypisania aplikacji, aby automatycznie powtarzać się w regularnych odstępach czasu, takich jak co miesiąc lub co kwartał.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Dzienniki aktywności usługi Azure AD (logowania i inspekcja) są teraz dostępne za pomocą programu MS Graph

**Typ:** Nowa **Kategoria usługi funkcji:** raportowanie **możliwości produktu:** monitorowanie & raportowanie

Dzienniki aktywności usługi Azure AD, które obejmują logowania i dzienniki inspekcji, są teraz dostępne za pomocą interfejsu API Microsoft Graph. Dodaliśmy dwa punkty końcowe za pomocą interfejsu API Microsoft Graph, aby uzyskać dostęp do tych dzienników. Zapoznaj się z naszymi [dokumentami](../reports-monitoring/concept-reporting-api.md) , aby uzyskać programistyczny dostęp do interfejsów API raportowania usługi Azure AD, aby rozpocząć pracę.

---

### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Ulepszenia środowiska związanego z wykupem B2B i opuszczanie organizacji

**Typ:** Nowa **Kategoria usługi funkcji:** **produkt B2B:** B2B/B2C

**Wykupu just in Time:** Po udostępnieniu zasobu użytkownikowi gość przy użyciu interfejsu API B2B — nie musisz wysyłać specjalnej wiadomości e-mail z zaproszeniem. W większości przypadków użytkownik-Gość może uzyskać dostęp do zasobu i zostanie on przetworzony przez cały czas. Nie ma żadnego wpływu ze względu na brakujące wiadomości e-mail. Nie ma więcej pytania dla użytkowników-Gości "po kliknięciu linku do wykupu przez system?". Oznacza to, że gdy SPO korzysta z Menedżera zaproszeń — załączniki w chmurze mogą mieć ten sam kanoniczny adres URL dla wszystkich użytkowników — wewnętrzny i zewnętrzny — w dowolnym stanie realizacji.

**Nowoczesne środowisko wykupu:** Nie ma więcej informacji na stronie początkowej dzielenia ekranu. Użytkownicy będą widzieć nowoczesne środowisko wyrażania zgody z zasadami zachowania poufności informacji w organizacji, podobnie jak w przypadku aplikacji innych firm.

**Użytkownicy-Goście mogą opuścić organizacji:** Gdy relacja użytkownika z organizacją zostanie przełączona, może ona samoobsługowo wychodzić z organizacji. Nie ma więcej informacji o tym, że zapraszasz administratora organizacji do usuwania ", nie ma więcej dodatkowych biletów pomocy technicznej.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — maj 2018

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** integracja z firmą

W maju 2018 dodaliśmy te 18 nowych aplikacji z obsługą Federacji do naszej galerii aplikacji:

[AwardSpring](../saas-apps/awardspring-tutorial.md), Infogix Data3Sixty, [Yodeck](../saas-apps/infogix-tutorial.md), [Jamf Pro](../saas-apps/jamfprosamlconnector-tutorial.md), [KnowledgeOwl](../saas-apps/knowledgeowl-tutorial.md), [ENVI MMIS](../saas-apps/envimmis-tutorial.md), [LaunchDarkly](../saas-apps/launchdarkly-tutorial.md), [Adobe Captivateal](../saas-apps/adobecaptivateprime-tutorial.md), [Montage online](../saas-apps/montageonline-tutorial.md), [まなびポケット](../saas-apps/manabipocket-tutorial.md), OpenReel, [Arc Publishing-SSO](../saas-apps/arc-tutorial.md), [PlanGrid](../saas-apps/plangrid-tutorial.md), [iWellnessNow](../saas-apps/iwellnessnow-tutorial.md), [Proxyclick](../saas-apps/proxyclick-tutorial.md), [potencjalnie szkodliwe oprogramowanie](../saas-apps/riskware-tutorial.md), [stado](../saas-apps/flock-tutorial.md), [ReviewSnap](../saas-apps/reviewsnap-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md).

---

### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nowe przewodniki wdrażania krok po kroku dla Azure Active Directory

**Typ:** Nowa **Kategoria usługi funkcji:** inny **produkt:** katalog

Nowe instrukcje krok po kroku dotyczące sposobu wdrażania Azure Active Directory (Azure AD), w tym samoobsługowego resetowania hasła (SSPR), logowania jednokrotnego (SSO), dostępu warunkowego (CA), serwera proxy aplikacji, aprowizacji użytkowników, Active Directory Federation Services (ADFS) do uwierzytelniania przekazywanego (PTA) i usług ADFS do synchronizacji skrótów haseł (PHS).

Aby wyświetlić przewodniki wdrażania, przejdź do repozytorium [przewodniki wdrażania tożsamości](https://aka.ms/DeploymentPlans) w witrynie GitHub. Aby przekazać opinię na temat przewodników wdrażania, użyj [formularza Opinia dotycząca planu wdrożenia](https://aka.ms/deploymentplanfeedback). Jeśli masz jakieś pytania dotyczące przewodników wdrażania, skontaktuj się z nami pod adresem [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Wyszukiwanie aplikacji dla przedsiębiorstw — Załaduj więcej aplikacji

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** Logowanie jednokrotne

Masz problemy z znalezieniem aplikacji/nazw głównych usług? Dodaliśmy możliwość załadowania większej liczby aplikacji na liście wszystkie aplikacje dla aplikacji w przedsiębiorstwie. Domyślnie pokazujemy 20 aplikacji. Teraz możesz kliknąć przycisk **Załaduj więcej** , aby wyświetlić dodatkowe aplikacje.

---

### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Wersja AADConnect może zawierać publiczną wersję zapoznawczą integracji z serwera pingfederate, ważne aktualizacje zabezpieczeń, wiele poprawek błędów i nowe wspaniałe nowe narzędzia do rozwiązywania problemów.

**Typ:** Zmieniono **kategorię usługi funkcji:** **możliwości produktu** AD Connect: Zarządzanie cyklem życia tożsamości

Wersja AADConnect może zawierać publiczną wersję zapoznawczą integracji z serwera pingfederate, ważne aktualizacje zabezpieczeń, wiele poprawek błędów i nowe wspaniałe nowe narzędzia do rozwiązywania problemów. Informacje o wersji można znaleźć [tutaj](../hybrid/reference-connect-version-history.md).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Przeglądy dostępu do usługi Azure AD: autoapply

**Typ:** Zmieniono **kategorię usługi funkcji:** przegląd dostępu **możliwości produktu:** ładu

Przeglądy dostępu do grup i aplikacji są teraz ogólnie dostępne w ramach Azure AD — wersja Premium P2. Administrator może skonfigurować program w taki sposób, aby automatycznie stosował zmiany recenzenta do tej grupy lub aplikacji po zakończeniu przeglądu dostępu. Administrator może także określić, co się dzieje z ciągłym dostępem użytkownika, jeśli recenzenci nie odpowiedzieli, Usuń dostęp, Zachowaj dostęp lub zapoznaj się z zaleceniami dotyczącymi systemu.

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Tokenów identyfikatorów nie można już zwrócić przy użyciu response_mode kwerendy dla nowych aplikacji.

**Typ:** Zmieniono **kategorię usługi funkcji:** uwierzytelnienia (logowania) **możliwości produktu:** uwierzytelnianie użytkownika

Aplikacje utworzone w dniu lub po 25 kwietnia 2018 nie będą już mogły żądać **id_token** przy użyciu response_mode **zapytań** .  Dzięki temu usługa Azure AD jest wbudowana ze specyfikacjami OIDC i pomaga w zmniejszeniu obszaru ataków na aplikacje.  Aplikacje utworzone przed 25 kwietnia 2018 nie są blokowane przed użyciem **zapytania** response_mode z response_type **id_token**.  Zwrócony błąd podczas żądania id_token z usługi AAD jest **AADSTS70007: "Query" nie jest obsługiwaną wartością "response_mode" podczas żądania tokenu**.

**Fragmenty** i **form_post** response_modes nadal działały — podczas tworzenia nowych obiektów aplikacji (na przykład w przypadku użycia serwera proxy aplikacji) przed utworzeniem nowej aplikacji upewnij się, że używasz jednej z tych response_modes.

---

## <a name="april-2018"></a>Kwiecień 2018 r.

### <a name="azure-ad-b2c-access-token-are-ga"></a>Token dostępu Azure AD B2C to GA

**Typ:** Nowa **Kategoria usługi funkcji:** B2C — **możliwości produktu** zarządzania tożsamościami konsumenta: B2B/B2C

Teraz możesz uzyskiwać dostęp do interfejsów API sieci Web zabezpieczonych przez Azure AD B2C przy użyciu tokenów dostępu. Ta funkcja jest przenoszona z publicznej wersji zapoznawczej na GA. Udoskonalono interfejs użytkownika służący do konfigurowania Azure AD B2C aplikacji i interfejsów API sieci Web, a także wprowadzono inne drobne ulepszenia.

Aby uzyskać więcej informacji, zobacz [Azure AD B2C: żądanie tokenów dostępu](../../active-directory-b2c/access-tokens.md).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testowanie konfiguracji logowania jednokrotnego dla aplikacji opartych na protokole SAML

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** Logowanie jednokrotne

Podczas konfigurowania aplikacji SSO opartych na protokole SAML można testować integrację na stronie Konfiguracja. Jeśli wystąpi błąd podczas logowania, możesz podać błąd w środowisku testowym, a usługa Azure AD udostępnia kroki umożliwiające rozwiązanie określonego problemu.

Aby uzyskać więcej informacji, zobacz:

- [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji Azure Active Directory](../manage-apps/view-applications-portal.md)
- [Jak debugować Logowanie jednokrotne oparte na protokole SAML do aplikacji w Azure Active Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md)

---

### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Warunki użytkowania usługi Azure AD mają teraz raportowanie na użytkownika

**Typ:** Nowa **Kategoria usługi funkcji:** warunki użytkowania **możliwości produktu:** zgodność

Administratorzy mogą teraz wybrać daną warunków użytkowania i zobaczyć wszystkich użytkowników, którzy wyraziły zgodę na tę warunków użytkowaniaę oraz datę/czas, w którym miało miejsce.

Aby uzyskać więcej informacji, zobacz [Funkcja warunki użytkowania usługi Azure AD](../conditional-access/terms-of-use.md).

---

### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: ryzykowny adres IP dla AD FS ochrony blokad w ekstranecie

**Typ:** Nowa **Kategoria usługi funkcji:** inna **możliwość produktu:** monitorowanie & raportowanie

Program Connect Health obsługuje teraz możliwość wykrywania adresów IP, które przekraczają próg nieudanych logowań U/P co godzinę lub codziennie. Możliwości oferowane przez tę funkcję są następujące:

- Obszerny raport przedstawiający adres IP oraz liczbę nieudanych logowań generowanych w ciągu godzinowym/codziennym przez dostosowywalny próg.
- Alerty na podstawie poczty e-mail pokazujące, kiedy określony adres IP przekroczył próg nieudanych logowań U/P w ujęciu godzinowym/codziennym.
- Opcja pobierania służąca do wykonania szczegółowej analizy danych

Aby uzyskać więcej informacji, zobacz [raport dotyczący ryzykownych adresów IP](https://aka.ms/aadchriskyip).

---

### <a name="easy-app-config-with-metadata-file-or-url"></a>Łatwa konfiguracja aplikacji przy użyciu pliku lub adresu URL metadanych

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** Logowanie jednokrotne

Na stronie aplikacje przedsiębiorstwa Administratorzy mogą przekazać plik metadanych SAML w celu skonfigurowania logowania opartego na protokole SAML dla galerii usługi AAD i aplikacji spoza galerii.

Ponadto możesz użyć adresu URL metadanych federacji aplikacji usługi Azure AD, aby skonfigurować Logowanie jednokrotne za pomocą aplikacji dostosowanej.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji Azure Active Directory](../manage-apps/view-applications-portal.md).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Usługa Azure AD Warunki użytkowania teraz ogólnie dostępna

**Typ:** Nowa **Kategoria usługi funkcji:** warunki użytkowania **możliwości produktu:** zgodność


Warunki użytkowania usługi Azure AD zostały przeniesione z publicznej wersji zapoznawczej do ogólnie dostępnej.

Aby uzyskać więcej informacji, zobacz [Funkcja warunki użytkowania usługi Azure AD](../conditional-access/terms-of-use.md).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Zezwalanie na zaproszenia dla użytkowników B2B z określonych organizacji i blokowanie ich

**Typ:** Nowa **Kategoria usługi funkcji:** **produkt B2B:** B2B/B2C


Teraz możesz określić, którzy organizacje partnerskie mają być współużytkowane i współpracujące z usługą Azure AD B2B. W tym celu można wybrać opcję tworzenia listy określonych domen dozwolonych lub Odmów. Gdy domena zostanie zablokowana przy użyciu tych funkcji, pracownicy nie będą już mogli wysyłać zaproszeń do osób w tej domenie.

Dzięki temu można kontrolować dostęp do zasobów, jednocześnie zapewniając bezproblemowe środowisko dla zatwierdzonych użytkowników.

Ta funkcja współpracy B2B jest dostępna dla wszystkich Azure Active Directory klientów i może być używana w połączeniu z funkcjami Azure AD — wersja Premium, takimi jak dostęp warunkowy i Ochrona tożsamości, aby uzyskać bardziej szczegółową kontrolę nad tym, kiedy i w jaki sposób zewnętrzni użytkownicy biznesowi logują się i uzyskują dostęp.

Aby uzyskać więcej informacji, zobacz [Zezwalanie lub blokowanie zaproszeń użytkownikom B2B z określonych organizacji](../external-identities/allow-deny-list.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** integracja z firmą

W kwietniu 2018 dodaliśmy te 13 nowych aplikacji z obsługą Federacji do naszej galerii aplikacji:

Kryterium HCM, [FiscalNote](../saas-apps/fiscalnote-tutorial.md), [Secret Server (on-premises)](../saas-apps/secretserver-on-premises-tutorial.md), [sygnał dynamiczny](../saas-apps/dynamicsignal-tutorial.md), [mindWireless](../saas-apps/mindwireless-tutorial.md), [schemat organizacyjny teraz](../saas-apps/orgchartnow-tutorial.md), [Ziflow](../saas-apps/ziflow-tutorial.md), [AppNeta Performance Monitor](../saas-apps/appneta-tutorial.md), [Elium](../saas-apps/elium-tutorial.md) , [FluxX Labs](../saas-apps/fluxxlabs-tutorial.md), [Cisco Cloud](../saas-apps/ciscocloud-tutorial.md), półka i [SafetyNET](../saas-apps/safetynet-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md).

---

### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Przyznaj użytkownikom B2B dostęp do aplikacji lokalnych (publiczna wersja zapoznawcza) w usłudze Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** **produkt B2B:** B2B/B2C

Jako organizacja, która używa funkcji współpracy B2B Azure Active Directory (Azure AD), aby zapraszać użytkowników-Gości od organizacji partnerskich do usługi Azure AD, możesz teraz udostępnić tym użytkownikom B2B dostęp do aplikacji lokalnych. Te aplikacje lokalne mogą korzystać z uwierzytelniania opartego na języku SAML lub zintegrowanego uwierzytelniania systemu Windows (IWA) przy użyciu ograniczonego delegowania protokołu Kerberos (KCD).

Aby uzyskać więcej informacji, zobacz [udzielanie użytkownikom B2B w usłudze Azure AD dostępu do aplikacji lokalnych](../external-identities/hybrid-cloud-to-on-premises.md).

---

### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Pobierz samouczki dotyczące integracji z logowaniem jednokrotnym z portalu Azure Marketplace

**Typ:** Zmieniono **kategorię usługi funkcji:** inna **możliwość produktu:** integracja innej firmy

Jeśli aplikacja, która jest wymieniona w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) obsługuje logowanie jednokrotne oparte na protokole SAML, kliknij przycisk **Pobierz teraz** udostępnia samouczek integracji skojarzony z tą aplikacją.

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Szybsza wydajność automatycznej aprowizacji użytkowników w usłudze Azure AD do aplikacji SaaS

**Typ:** Zmieniono **kategorię usługi funkcji:** **możliwości produktu** aprowizacji aplikacji: integracja innej firmy

Wcześniej klienci korzystający z łączników aprowizacji użytkowników programu Azure Active Directory dla aplikacji SaaS (na przykład Salesforce, usługi ServiceNow i Box) mogą mieć niską wydajność, jeśli dzierżawy usługi Azure AD zabyli ponad 100 000 połączonymi użytkownikami i grupami, a następnie korzystają z przypisań użytkowników i grup w celu ustalenia, którzy użytkownicy powinni być obsługiwani.

2 kwietnia 2018, w usłudze Azure AD Provisioning zostały wdrożone znaczące ulepszenia wydajności, które znacznie skracają czas wymagany do wykonywania synchronizacji początkowej między Azure Active Directory i docelowymi aplikacjami SaaS.

W związku z tym wielu klientów, którzy mieli wstępne synchronizacje dla aplikacji, które zajęły wiele dni lub nigdy nie ukończyłą pracy, są teraz wykonywane w ciągu kilku minut lub godzin.

Aby uzyskać więcej informacji, zobacz [co się stanie w trakcie aprowizacji?](../..//active-directory/app-provisioning/how-provisioning-works.md)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Samoobsługowe resetowanie haseł z ekranu blokady systemu Windows 10 dla maszyn przyłączonych do hybrydowej usługi Azure AD

**Typ:** Zmieniono **kategorię usługi funkcji:** funkcja samoobsługowego resetowania hasła **:** uwierzytelnianie użytkownika

Funkcja SSPR systemu Windows 10 została zaktualizowana w celu uwzględnienia obsługi maszyn, które są dołączone do hybrydowej usługi Azure AD. Ta funkcja jest dostępna w systemie Windows 10 RS4 umożliwia użytkownikom Resetowanie swoich haseł na ekranie blokady na komputerze z systemem Windows 10. Użytkownicy, którzy są włączeni do samoobsługowego resetowania hasła, mogą korzystać z tej funkcji.

Aby uzyskać więcej informacji, zobacz [Resetowanie hasła usługi Azure AD na ekranie logowania](../authentication/howto-sspr-windows.md).

---

## <a name="march-2018"></a>Marzec 2018 r.

### <a name="certificate-expire-notification"></a>Powiadomienie o wygaśnięciu certyfikatu

**Typ:** **Kategoria stałej usługi:** funkcja aplikacji dla przedsiębiorstw **:** Logowanie jednokrotne

Usługa Azure AD wysyła powiadomienie, gdy certyfikat galerii lub aplikacji z galerii nie zostanie wkrótce wygaśnie.

Niektórzy użytkownicy nie otrzymali powiadomień o aplikacjach dla przedsiębiorstw skonfigurowanych do logowania jednokrotnego opartego na protokole SAML. Ten problem został rozwiązany. Usługa Azure AD wysyła powiadomienie o certyfikatach, które wygasną w dniach 7, 30 i 60. To zdarzenie można zobaczyć w dziennikach inspekcji.

Aby uzyskać więcej informacji, zobacz:

- [Zarządzanie certyfikatami federacyjnego logowania jednokrotnego w Azure Active Directory](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
- [Raporty dotyczące inspekcji w portalu usługi Azure Active Directory](../reports-monitoring/concept-audit-logs.md)

---

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Dostawcy tożsamości Twitter i GitHub w Azure AD B2C

**Typ:** Nowa **Kategoria usługi funkcji:** B2C — **możliwości produktu** zarządzania tożsamościami konsumenta: B2B/B2C

W Azure AD B2C możesz teraz dodawać usługi Twitter lub GitHub jako dostawcę tożsamości. Serwis Twitter jest przenoszony z publicznej wersji zapoznawczej na GA. Serwis GitHub jest publikowany w publicznej wersji zapoznawczej.

Aby uzyskać więcej informacji, zobacz [co to jest współpraca B2B w usłudze Azure AD?](../external-identities/what-is-b2b.md).

---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Ograniczanie dostępu do przeglądarki przy użyciu Intune Managed Browser z dostępem warunkowym opartym na aplikacji w usłudze Azure AD dla systemów iOS i Android

**Typ:** Nowa **Kategoria usługi funkcji:** dostęp warunkowy do **produktu:** zabezpieczenia tożsamości & ochrona

**Teraz w publicznej wersji zapoznawczej!**

**Intune Managed Browser Logowanie jednokrotne:** Pracownicy mogą korzystać z logowania jednokrotnego na klientach natywnych (takich jak program Microsoft Outlook) i Intune Managed Browser dla wszystkich aplikacji połączonych z usługą Azure AD.

**Intune Managed Browser obsługa dostępu warunkowego:** Teraz można wymagać od pracowników korzystania z zarządzanej przeglądarki Intune przy użyciu zasad dostępu warunkowego opartego na aplikacji.

Więcej informacji na ten temat można znaleźć w naszym [wpisie w blogu](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Aby uzyskać więcej informacji, zobacz:

- [Konfigurowanie dostępu warunkowego opartego na aplikacji](../conditional-access/app-based-conditional-access.md)

- [Konfigurowanie zasad przeglądarki Managed Browser](https://aka.ms/managedbrowser)

---

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Polecenia cmdlet serwera proxy aplikacji w module środowiska PowerShell GA

**Typ:** Nowa **Kategoria usługi funkcji:** **możliwości produktu** serwera proxy aplikacji: Access Control

Obsługa poleceń cmdlet serwera proxy aplikacji jest teraz w module GA programu PowerShell. Wymaga to pozostawania aktualizacji w modułach programu PowerShell — w przypadku przekroczenia roku niektóre polecenia cmdlet mogą przestać działać.

Aby uzyskać więcej informacji, zobacz [AzureAD](/powershell/module/Azuread/?view=azureadps-2.0).

---

### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Natywni klienci pakietu Office 365 są obsługiwani przez bezproblemowe logowanie jednokrotne przy użyciu protokołu nieinterakcyjnego

**Typ:** Nowa **Kategoria usługi funkcji:** uwierzytelnienia (logowania) **możliwości produktu:** uwierzytelnianie użytkownika

Użytkownik korzystający z natywnych klientów pakietu Office 365 (wersja 16.0.8730. xxxx i nowsze) uzyskuje ciche środowisko logowania, wykorzystując bezproblemową rejestrację jednokrotną. To wsparcie jest zapewniane przez dodanie protokołu nieinterakcyjnego (WS-Trust) do usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Jak zalogować się na natywnym kliencie z bezproblemowym logowaniem JEDNOkrotnym?](../hybrid/how-to-connect-sso-how-it-works.md#how-does-sign-in-on-a-native-client-with-seamless-sso-work)

---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Użytkownicy uzyskują dyskretne środowisko logowania z bezproblemowym logowaniem jednokrotnym, jeśli aplikacja wysyła żądania logowania do punktów końcowych dzierżawy usługi Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** uwierzytelnienia (logowania) **możliwości produktu:** uwierzytelnianie użytkownika

Użytkownicy uzyskują ciche środowisko logowania z bezproblemowym logowaniem jednokrotnym, jeśli aplikacja (na przykład `https://contoso.sharepoint.com` ) wysyła żądania logowania do punktów końcowych dzierżawy usługi Azure AD, czyli do `https://login.microsoftonline.com/contoso.com/<..>` `https://login.microsoftonline.com/<tenant_ID>/<..>` wspólnego punktu końcowego () usługi Azure AD `https://login.microsoftonline.com/common/<...>` .

Aby uzyskać więcej informacji, zobacz [Azure Active Directory bezproblemowe logowanie jednokrotne](../hybrid/how-to-connect-sso.md).

---

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Aby zapewnić bezproblemowe logowanie jednokrotne, należy dodać tylko jeden adres URL usługi Azure AD, a nie dwa adresy URL w ustawieniach strefy intranetowej użytkownika.

**Typ:** Nowa **Kategoria usługi funkcji:** uwierzytelnienia (logowania) **możliwości produktu:** uwierzytelnianie użytkownika

Aby zapewnić użytkownikom bezproblemowe logowanie jednokrotne, musisz dodać tylko jeden adres URL usługi Azure AD do ustawień strefy intranetowej użytkowników przy użyciu zasad grupy w Active Directory: `https://autologon.microsoftazuread-sso.com` . Wcześniej Klienci musieli dodać dwa adresy URL.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory bezproblemowe logowanie jednokrotne](../hybrid/how-to-connect-sso.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** integracja z firmą

W marcu 2018 dodaliśmy następujące 15 nowych aplikacji z obsługą Federacji do naszej galerii aplikacji:

[BoxCryptor](../saas-apps/boxcryptor-tutorial.md), [CylancePROTECT](../saas-apps/cylanceprotect-tutorial.md), Wrike, [SignalFx](../saas-apps/signalfx-tutorial.md), Assistant przez FirstAgenda, [YardiOne](../saas-apps/yardione-tutorial.md), vtiger CRM, inobrazkowe, [amplituda](../saas-apps/amplitude-tutorial.md), [Spacio](../saas-apps/spacio-tutorial.md), [ContractWorks](../saas-apps/contractworks-tutorial.md), [Bersin](../saas-apps/bersin-tutorial.md), [Mercell](../saas-apps/mercell-tutorial.md), [Trisotech Digital Enterprise Server](../saas-apps/trisotechdigitalenterpriseserver-tutorial.md), [Qumu Cloud](../saas-apps/qumucloud-tutorial.md).

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md).

---

### <a name="pim-for-azure-resources-is-generally-available"></a>Usługa PIM dla zasobów platformy Azure jest ogólnie dostępna

**Typ:** Nowa **Kategoria usługi funkcji:** Privileged Identity Management **możliwości produktu:** Privileged Identity Management

Jeśli używasz Azure AD Privileged Identity Management dla ról katalogu, możesz teraz korzystać z możliwości dostępu i przypisywania w usłudze PIM dla ról zasobów platformy Azure, takich jak subskrypcje, grupy zasobów, Virtual Machines i inne zasoby obsługiwane przez Azure Resource Manager. Wymuś Multi-Factor Authentication podczas aktywacji ról just-in-Time i Zaplanuj aktywację w koordynacji z zatwierdzonymi zmianami systemu Windows. Ponadto w tej wersji dodano ulepszenia, które nie są dostępne w publicznej wersji zapoznawczej, w tym zaktualizowany interfejs użytkownika, przepływy pracy zatwierdzania i możliwość rozszerzenia ról, które wkrótce wygasną i odnawiania wygasłych ról.

Aby uzyskać więcej informacji, zobacz temat [PIM dla zasobów platformy Azure (wersja zapoznawcza)](../privileged-identity-management/azure-pim-resource-rbac.md)

---

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Dodawanie opcjonalnych oświadczeń do tokenów aplikacji (publiczna wersja zapoznawcza)

**Typ:** Nowa **Kategoria usługi funkcji:** uwierzytelnienia (logowania) **możliwości produktu:** uwierzytelnianie użytkownika

Aplikacja usługi Azure AD może teraz żądać niestandardowych lub opcjonalnych oświadczeń w tokenach JWTs lub SAML.  Są to oświadczenia dotyczące użytkownika lub dzierżawy, które nie są uwzględnione domyślnie w tokenie z powodu ograniczeń rozmiaru lub zastosowania.  Jest to obecnie dostępna w publicznej wersji zapoznawczej dla aplikacji usługi Azure AD w punktach końcowych wersji 1.0 i 2.0.  Zapoznaj się z dokumentacją, aby uzyskać informacje na temat tego, jakie oświadczenia można dodać, oraz sposobu edytowania manifestu aplikacji w celu ich żądania.

Aby uzyskać więcej informacji, zobacz [opcjonalne oświadczenia w usłudze Azure AD](../develop/active-directory-optional-claims.md).

---

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Usługa Azure AD obsługuje PKCE na potrzeby bardziej bezpiecznych przepływów OAuth

**Typ:** Nowa **Kategoria usługi funkcji:** uwierzytelnienia (logowania) **możliwości produktu:** uwierzytelnianie użytkownika

Zaktualizowano dokumentację usługi Azure AD w celu zanotowania pomocy technicznej PKCE, która pozwala na bardziej bezpieczną komunikację w ramach przepływu przydzielenia kodu autoryzacji OAuth 2,0.  Dla punktów końcowych v 1.0 i v 2.0 obsługiwane są zarówno S256, jak i code_challenges zwykłego tekstu.

Aby uzyskać więcej informacji, zobacz [żądanie kodu autoryzacji](../develop/v2-oauth2-auth-code-flow.md#request-an-authorization-code).

---

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Obsługa aprowizacji wszystkich wartości atrybutów użytkownika dostępnych w interfejsie API Get_Workers produktu Workday

**Typ:** Nowa **Kategoria usługi funkcji:** funkcja aprowizacji aplikacji w **produkcie:** integracja innej firmy

Publiczna wersja zapoznawcza udostępniania przychodzącego z produktu Workday do Active Directory i usługi Azure AD obsługuje teraz możliwość wyodrębnienia i aprowizacji wszystkich wartości atrybutów dostępnych w interfejsie API Get_Workers roboczych. To dodanie obsługuje setki dodatkowych atrybutów standardowych i niestandardowych poza tymi, które są dostarczane z początkową wersją łącznika inicjowania obsługi ruchu przychodzącego dla programu Workday.

Aby uzyskać więcej informacji, zobacz: [Dostosowywanie listy atrybutów użytkownika produktu Workday](../saas-apps/workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Zmiana przynależności do grupy z dynamicznego na static i na odwrót

**Typ:** Nowa **Kategoria usługi funkcji:** funkcja zarządzania grupami **:** współpraca

Istnieje możliwość zmiany sposobu zarządzania członkostwem w grupie. Jest to przydatne, gdy chcesz zachować tę samą nazwę grupy i identyfikator w systemie, tak aby wszystkie istniejące odwołania do grupy były nadal ważne; utworzenie nowej grupy wymaga aktualizacji tych odwołań.
Zaktualizowaliśmy centrum administracyjne usługi Azure AD w celu zapewnienia obsługi tej funkcji. Teraz klienci mogą konwertować istniejące grupy z członkostwa dynamicznego na przypisane członkostwo i na odwrót. Istniejące polecenia cmdlet programu PowerShell są również nadal dostępne.

Aby uzyskać więcej informacji, zobacz [dynamiczne reguły członkostwa dla grup w Azure Active Directory](../users-groups-roles/groups-dynamic-membership.md)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Ulepszone zachowanie podczas logowania za pomocą bezproblemowego logowania jednokrotnego

**Typ:** Zmieniono **kategorię usługi funkcji:** uwierzytelnienia (logowania) **możliwości produktu:** uwierzytelnianie użytkownika

Wcześniej, nawet jeśli użytkownicy jawnie wylogowani z aplikacji zabezpieczonej przez usługę Azure AD, zostaną automatycznie zarejestrowani z użyciem bezproblemowego logowania jednokrotnego, jeśli próbujesz uzyskać dostęp do aplikacji usługi Azure AD w sieci firmowej ze swoich urządzeń przyłączonych do domeny. Ta zmiana powoduje, że wylogowanie jest obsługiwane.  Umożliwia to użytkownikom wybranie tego samego lub innego konta usługi Azure AD w celu zalogowania się za pomocą programu, a nie automatyczne logowanie za pomocą bezproblemowego logowania jednokrotnego.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory bezproblemowe logowanie jednokrotne](../hybrid/how-to-connect-sso.md)

---

### <a name="application-proxy-connector-version-154020-released"></a>Wydano wersję łącznika serwera proxy aplikacji 1.5.402.0

**Typ:** Zmieniono **kategorię usługi funkcji:** **możliwości produktu** serwera proxy aplikacji: zabezpieczenia tożsamości & ochrona

Wersja tego łącznika jest stopniowo wprowadzana do listopada. Ta nowa wersja łącznika obejmuje następujące zmiany:

- Łącznik teraz ustawia pliki cookie na poziomie domeny zamiast na poziomie domeny podrzędnej. Zapewnia to bezproblemowe środowisko logowania jednokrotnego i pozwala uniknąć nadmiarowych wierszy uwierzytelniania.
- Obsługa fragmentarycznych żądań kodowania
- Ulepszone monitorowanie kondycji łącznika
- Kilka poprawek usterek i ulepszeń stabilności

Aby uzyskać więcej informacji, zobacz [Omówienie łączników usługi Azure serwer proxy aplikacji usługi Azure AD](../manage-apps/application-proxy-connectors.md).

---

## <a name="february-2018"></a>Luty 2018 r.

### <a name="improved-navigation-for-managing-users-and-groups"></a>Ulepszona nawigacja w celu zarządzania użytkownikami i grupami

**Typ:** Plan for Change **Service Category:** możliwość zarządzania katalogami **:** katalog

Środowisko nawigacji dla zarządzania użytkownikami i grupami zostało udoskonalone. Teraz można nawigować z poziomu omówienia katalogów bezpośrednio do listy wszystkich użytkowników, z łatwiejszym dostępem do listy usuniętych użytkowników. Możesz również przejść z omówienia katalogów bezpośrednio do listy wszystkich grup z ułatwieniami dostępu do ustawień zarządzania grupami. Ponadto na stronie Przegląd katalogów można wyszukiwać użytkowników, grupy, aplikacje dla przedsiębiorstw lub rejestrację aplikacji.

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Dostępność raportów logowania i inspekcji w Microsoft Azure obsługiwane przez firmę 21Vianet (Azure Chiny 21Vianet)

**Typ:** Nowa **Kategoria usługi funkcji:** Azure Stack **możliwości produktu:** monitorowanie & raportowanie

Raporty dzienników aktywności usługi Azure AD są teraz dostępne w Microsoft Azure obsługiwane przez firmę 21Vianet (Azure Chiny 21Vianet). Uwzględniono następujące dzienniki:

- **Dzienniki aktywności logowania**  — obejmuje wszystkie dzienniki logowania skojarzone z dzierżawcą.

- **Dzienniki inspekcji hasła samoobsługowego** — obejmuje wszystkie dzienniki inspekcji SSPR.

- **Dzienniki inspekcji zarządzania katalogami** — obejmuje wszystkie dzienniki inspekcji związane z zarządzaniem katalogiem, takie jak zarządzanie użytkownikami, zarządzanie aplikacjami i inne.

Korzystając z tych dzienników, można uzyskać wgląd w działanie środowiska. Na podstawie udostępnionych danych można:

- Określ, w jaki sposób aplikacje i usługi są wykorzystywane przez użytkowników.

- Rozwiązywanie problemów uniemożliwiających użytkownikom wykonywanie pracy.

Aby uzyskać więcej informacji na temat korzystania z tych raportów, zobacz [Azure Active Directory raportowanie](../reports-monitoring/overview-reports.md).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Aby wyświetlić raporty aktywności usługi Azure AD, użyj roli "czytelnik raportu" (rola niebędąca administratorem)

**Typ:** Nowa **Kategoria usługi funkcji:** raportowanie **możliwości produktu:** monitorowanie & raportowanie

Aby uzyskać dostęp do dzienników aktywności usługi Azure AD w ramach klientów, skontaktuj się z tą funkcją, aby umożliwić użytkownikom należącym do roli "czytelnika raportu" dostęp do operacji logowania i inspekcji w ramach Azure Portal, jak również przy użyciu interfejsu API Microsoft Graph.

Aby uzyskać więcej informacji, jak korzystać z tych raportów, zobacz [Azure Active Directory raportowanie](../reports-monitoring/overview-reports.md).

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Rolę IDPracownika dostępną jako atrybut użytkownika i identyfikator użytkownika

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** Logowanie jednokrotne

Wartość **IDPracownika** można skonfigurować jako identyfikator użytkownika i atrybut użytkownika dla użytkowników należących do członków i gościa B2B w aplikacjach logowania opartych na języku SAML w interfejsie użytkownika aplikacji przedsiębiorstwa.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw w Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Uproszczone zarządzanie aplikacjami przy użyciu symboli wieloznacznych na platformie Azure serwer proxy aplikacji usługi Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** **Funkcja produktu** serwera proxy aplikacji: uwierzytelnianie użytkownika

Aby ułatwić wdrażanie aplikacji i zmniejszyć koszty administracyjne, Obsługujemy teraz możliwość publikowania aplikacji przy użyciu symboli wieloznacznych. Aby opublikować aplikację z symbolami wieloznacznymi, możesz użyć standardowego przepływu publikowania aplikacji, ale w wewnętrznych i zewnętrznych adresach URL Użyj symbolu wieloznacznego.

Aby uzyskać więcej informacji, zobacz [symbole wieloznaczne w Azure Active Directory serwerze proxy aplikacji](../manage-apps/application-proxy-wildcard.md)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nowe polecenia cmdlet do obsługi konfiguracji serwera proxy aplikacji

**Typ:** Nowa **Kategoria usługi funkcji:** **Funkcja produktu** serwera proxy aplikacji: platforma

Najnowsza wersja modułu AzureAD PowerShell w wersji zapoznawczej zawiera nowe polecenia cmdlet, które umożliwiają klientom Konfigurowanie aplikacji serwera proxy aplikacji przy użyciu programu PowerShell.

Nowe polecenia cmdlet są następujące:

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Nowe polecenia cmdlet do obsługi konfiguracji grup

**Typ:** Nowa **Kategoria usługi funkcji:** **Funkcja produktu** serwera proxy aplikacji: platforma

Najnowsza wersja modułu AzureAD PowerShell zawiera polecenia cmdlet służące do zarządzania grupami w usłudze Azure AD. Te polecenia cmdlet były wcześniej dostępne w module AzureADPreview i są teraz dodawane do modułu AzureAD

Polecenia cmdlet grupy, które są teraz dostępne do ogólnej dostępności, to:

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

---

### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Dostępna jest nowa wersja Azure AD Connect

**Typ:** Nowa **Kategoria usługi funkcji:** AD Sync **możliwości produktu:** platforma

Azure AD Connect jest preferowanym narzędziem do synchronizowania danych między usługą Azure AD i lokalnymi źródłami danych, w tym z systemem Windows Server Active Directory i protokołem LDAP.

>[!Important]
>Ta kompilacja wprowadza zmiany reguł schematu i synchronizacji. Usługa synchronizacji Azure AD Connect wyzwala pełne kroki importowania i pełnej synchronizacji po uaktualnieniu. Aby uzyskać informacje na temat zmiany tego zachowania, zobacz [jak odroczyć pełną synchronizację po uaktualnieniu](../hybrid/how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).

Ta wersja obejmuje następujące aktualizacje i zmiany:

**Rozwiązano problemy**

- Naprawianie okna chronometrażu zadań w tle dla strony filtrowania partycji podczas przełączania na następną stronę.

- Rozwiązano błąd, który spowodował naruszenie zasad dostępu podczas akcji niestandardowej ConfigDB.

- Rozwiązano usterkę do odzyskania z limitu czasu połączenia SQL.

- Rozwiązano problem polegający na tym, że certyfikaty z symbolami wieloznacznymi sieci SAN kończą się niepowodzeniem.

- Naprawiono usterkę powodującą miiserver.exe awarię podczas eksportowania łącznika usługi AAD.

- Naprawiono usterkę polegającą na tym, że podczas uruchamiania w kontrolerze domeny zostało zarejestrowane złe hasło przy użyciu Kreatora połączenia usługi AAD, aby zmienić konfigurację

**Nowe funkcje i ulepszenia**

- Telemetria aplikacji — Administratorzy mogą przełączać tę klasę danych na włączony/wyłączony.

- Dane dotyczące kondycji usługi Azure AD — Administratorzy muszą odwiedzić portal kondycji, aby kontrolować ich ustawienia kondycji. Po zmianie zasad usługi Agenty będą je odczytywać i wymuszać.

- Dodano akcje konfiguracji zapisywania zwrotnego urządzeń i pasek postępu dla inicjowania strony.

- Ulepszona ogólna Diagnostyka z raportem HTML i pełną kolekcją danych w raporcie ZIP-Text/HTML.

- Ulepszona niezawodność autouaktualnienia i dodano dodatkową telemetrię, aby zapewnić możliwość określenia kondycji serwera.

- Ogranicz uprawnienia dostępne dla kont uprzywilejowanych na koncie łącznika usługi AD. W przypadku nowych instalacji Kreator ogranicza uprawnienia, które konta uprzywilejowane znajdują się na koncie MSOL po utworzeniu konta MSOL. Zmiany dotyczą instalacji ekspresowych i instalacji niestandardowych przy użyciu konta AutoCreate.

- Zmieniono Instalatora, aby nie wymagał uprawnień SA po czystej instalacji programu AADConnect.

- Nowe narzędzie do rozwiązywania problemów z synchronizacją dla określonego obiektu. Obecnie narzędzie sprawdza następujące kwestie:

    - Niezgodność UserPrincipalName między zsynchronizowanym obiektem użytkownika a kontem użytkownika w dzierżawie usługi Azure AD.

    - Jeśli obiekt jest filtrowany z synchronizacji z powodu filtrowania domeny

    - Jeśli obiekt jest filtrowany z synchronizacji z powodu filtrowania jednostki organizacyjnej (OU)

- Nowe narzędzie do synchronizowania bieżącego skrótu hasła przechowywanego w Active Directory lokalnym dla określonego konta użytkownika. Narzędzie nie wymaga zmiany hasła.

---

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Aplikacje obsługujące zasady Intune App Protection dodane do użycia z dostępem warunkowym opartym na aplikacji usługi Azure AD

**Typ:** Zmieniono **kategorię usługi funkcji:** dostęp warunkowy do **produktu:** zabezpieczenia tożsamości & ochrona

Dodaliśmy więcej aplikacji obsługujących dostęp warunkowy oparty na aplikacji. Teraz możesz uzyskać dostęp do pakietu Office 365 i innych aplikacji w chmurze połączonych z usługą Azure AD przy użyciu tych zatwierdzonych aplikacji klienckich.

Następujące aplikacje zostaną dodane do końca lutego:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Aby uzyskać więcej informacji, zobacz:

- [Wymagania dotyczące zatwierdzonej aplikacji klienckiej](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Warunki użytkowania aktualizacji do środowiska mobilnego

**Typ:** Zmieniono **kategorię usługi funkcji:** warunki użytkowania **możliwości produktu:** zgodność

Kiedy są wyświetlane warunki użytkowania, możesz teraz kliknąć problemy z **wyświetlaniem? Kliknij tutaj**. Kliknięcie tego linku otwiera warunki użytkowania natywnie na urządzeniu. Niezależnie od rozmiaru czcionki w dokumencie lub rozmiaru ekranu urządzenia, możesz powiększyć i odczytać dokument zgodnie z wymaganiami.

---

## <a name="january-2018"></a>Styczeń 2018 r.

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** integracja z firmą

W styczniu 2018 dodano następujące nowe aplikacje z obsługą Federacji w galerii aplikacji:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust Management Software](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk federacyjnego Directory i [wierność zalety](https://go.microsoft.com/fwlink/?linkid=864701).

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md).

---

### <a name="sign-in-with-additional-risk-detected"></a>Zalogować się przy wykryciu dodatkowego ryzyka

**Typ:** Nowa **Kategoria usługi funkcji:** **zdolność produktu** do ochrony tożsamości: zabezpieczenia tożsamości & ochrona

Szczegółowe informacje na temat wykrytego wykrywania ryzyka są powiązane z subskrypcją usługi Azure AD. Za pomocą Azure AD — wersja Premium P2 można uzyskać najbardziej szczegółowe informacje na temat wszystkich podstawowych wykryć.

W przypadku wersji Azure AD — wersja Premium P1 wykryte wykrywania, które nie są objęte licencją, są wyświetlane w przypadku wykrycia ryzyka podczas logowania przy użyciu dodatkowego ryzyka.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory wykrywania zagrożeń](../identity-protection/overview-identity-protection.md).

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Ukryj aplikacje pakietu Office 365 z poziomu paneli dostępu użytkownika końcowego

**Typ:** Nowa **Kategoria usługi funkcji:** **możliwości produktu** moje aplikacje: Logowanie jednokrotne

Teraz można lepiej zarządzać sposobem wyświetlania aplikacji pakietu Office 365 w panelach dostępu użytkownika za pomocą nowego ustawienia użytkownika. Ta opcja jest pomocna w przypadku zmniejszenia liczby aplikacji w panelu dostępu użytkownika, jeśli wolisz wyświetlać tylko aplikacje pakietu Office w portalu usługi Office. Ustawienie znajduje się w **ustawieniach użytkownika** i ma etykietę, **Użytkownicy mogą widzieć tylko aplikacje pakietu Office 365 w portalu pakietu Office 365**.

Aby uzyskać więcej informacji, zobacz [ukrywanie aplikacji ze środowiska użytkownika w Azure Active Directory](../manage-apps/hide-application-from-user-portal.md).

---

### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Bezproblemowe logowanie do aplikacji z włączonym logowaniem jednokrotnym hasła bezpośrednio z adresu URL aplikacji

**Typ:** Nowa **Kategoria usługi funkcji:** **możliwości produktu** moje aplikacje: Logowanie jednokrotne

Rozszerzenie przeglądarki Moje aplikacje jest teraz dostępne za pośrednictwem wygodnego narzędzia zapewniającego możliwość logowania jednokrotnego na stronie Moje aplikacje jako skrót w przeglądarce. Po zainstalowaniu użytkownik zobaczy ikonę Gofr w swojej przeglądarce, która zapewni im szybki dostęp do aplikacji. Użytkownicy mogą teraz korzystać z programu:

- Możliwość bezpośredniej rejestracji w aplikacjach opartych na rejestracji jednokrotnej na stronie logowania aplikacji
- Uruchamianie dowolnej aplikacji przy użyciu funkcji szybkiego wyszukiwania
- Skróty do ostatnio używanych aplikacji z rozszerzenia
- Rozszerzenie jest dostępne dla przeglądarki Microsoft Edge, Chrome i Firefox.

Aby uzyskać więcej informacji, zobacz [Moje aplikacje bezpieczne logowanie](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Środowisko administrowania usługą Azure AD w klasyczny portal Azure zostało wycofane

**Typ:** Kategoria przestarzałych **usług:** **Funkcja produktu** Azure AD: katalog

Od 8 stycznia 2018 środowisko administracyjne Azure AD w klasycznym portalu Azure zostało wycofane. Miało to miejsce w połączeniu z wycofywaniem z klasycznego portalu Azure. W przyszłości należy użyć [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) dla całej administracji opartej na portalu w usłudze Azure AD.

---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Portal sieci Web PhoneFactor został wycofany

**Typ:** Kategoria przestarzałych **usług:** **Funkcja produktu** Azure AD: katalog

Od 8 stycznia 2018 Portal sieci Web PhoneFactor został wycofany. Ten portal był używany do administrowania serwerem usługi MFA, ale te funkcje zostały przeniesione do Azure Portal w portal.azure.com.

Konfiguracja usługi MFA znajduje się w lokalizacji: **Azure Active Directory \> serwer MFA**

---

### <a name="deprecate-azure-ad-reports"></a>Przestarzałe raporty usługi Azure AD

**Typ:** Kategoria przestarzałych **usług:** raportowanie **możliwości produktu:** Zarządzanie cyklem życia tożsamości


Dzięki ogólnej dostępności nowej konsoli administracyjnej Azure Active Directory i nowych interfejsów API dla obu raportów o działaniach i zabezpieczeniach interfejsy API raportów w punkcie końcowym "/Reports" zostały wycofane z 31 grudnia 2017.

**Co jest dostępne?**

W ramach przejścia do nowej konsoli administracyjnej udostępniono 2 nowe interfejsy API do pobierania dzienników aktywności usługi Azure AD. Nowy zestaw interfejsów API zapewnia bogatsze funkcje filtrowania i sortowania, a także zapewnia bogatsze działania związane z inspekcją i logowaniem. Dostęp do danych dostępnych wcześniej za pomocą raportów zabezpieczeń można teraz uzyskać za pomocą interfejsu API wykrywania ryzyka ochrony tożsamości w Microsoft Graph.

Aby uzyskać więcej informacji, zobacz:

- [Wprowadzenie do interfejsu API raportowania Azure Active Directory](../reports-monitoring/concept-reporting-api.md)

- [Wprowadzenie do Azure Active Directory Identity Protection i Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md)

---

## <a name="december-2017"></a>Grudzień 2017

### <a name="terms-of-use-in-the-access-panel"></a>Warunki użytkowania w panelu dostępu

**Typ:** Nowa **Kategoria usługi funkcji:** warunki użytkowania **możliwości produktu:** zgodność

Teraz możesz przejść do panelu dostępu i wyświetlić warunki użytkowania, które zostały wcześniej zaakceptowane.

Wykonaj następujące kroki:

1. Przejdź do [portalu Moje aplikacje](https://myapps.microsoft.com)i zaloguj się.

2. W prawym górnym rogu wybierz swoją nazwę, a następnie z listy wybierz pozycję **profil** .

3. W **profilu**wybierz pozycję **Przejrzyj warunki użytkowania**.

4. Teraz możesz przejrzeć zaakceptowane warunki użytkowania.

Aby uzyskać więcej informacji, zobacz [Funkcja warunki użytkowania usługi Azure AD (wersja zapoznawcza)](../conditional-access/terms-of-use.md).

---

### <a name="new-azure-ad-sign-in-experience"></a>Nowe środowisko logowania do usługi Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** **Funkcja produktu** Azure AD: uwierzytelnianie użytkownika

Interfejsów użytkownika systemu tożsamości usługi Azure AD i konto Microsoft zostały zaprojektowane tak, aby miały spójny wygląd i działanie. Ponadto strona logowania usługi Azure AD najpierw zbiera nazwę użytkownika, a następnie poświadczenie na drugim ekranie.

Aby uzyskać więcej informacji, zobacz [nowe środowisko logowania do usługi Azure AD jest teraz dostępne w publicznej wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).

---

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Mniej monitów logowania: nowe środowisko "nie wylogowuj mnie" dla logowania za pomocą usługi Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** **Funkcja produktu** Azure AD: uwierzytelnianie użytkownika

Pole wyboru nie wylogowuj **mnie** na stronie logowania usługi Azure AD zostało zastąpione nowym monitem, który jest wyświetlany po pomyślnym uwierzytelnieniu.

Jeśli **odpowie na** ten monit, usługa daje trwały token odświeżania. Takie zachowanie jest takie samo, jak w przypadku wybrania pola wyboru nie **wylogowuj mnie** w starym środowisku. W przypadku dzierżawców federacyjnych ten monit jest wyświetlany po pomyślnym uwierzytelnieniu w usłudze federacyjnej.

Aby uzyskać więcej informacji, zobacz [mniej monitów logowania: nowe środowisko "nie wylogowuj mnie" w usłudze Azure AD jest w wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/).

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Dodaj konfigurację, aby wymagać, aby warunki użytkowania były rozwinięte przed zaakceptowaniem

**Typ:** Nowa **Kategoria usługi funkcji:** warunki użytkowania **możliwości produktu:** zgodność

Opcja dla administratorów wymaga, aby użytkownicy mogli rozwijać warunki użytkowania przed zaakceptowaniem warunków.

Wybierz opcję **Włącz** lub **Wyłącz** , aby wymagać od użytkowników rozwinięcia warunków użytkowania. Ustawienie **on** wymaga od użytkowników wyświetlenia warunków użytkowania przed ich zaakceptowaniem.

Aby uzyskać więcej informacji, zobacz [Funkcja warunki użytkowania usługi Azure AD (wersja zapoznawcza)](../conditional-access/terms-of-use.md).

---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Aktywacja z zakresem dla kwalifikujących się przypisań ról

**Typ:** Nowa **Kategoria usługi funkcji:** Privileged Identity Management **możliwości produktu:** Privileged Identity Management

Aktywacji z zakresem można użyć do aktywowania kwalifikujących się ról zasobów platformy Azure z mniejszą autonomią niż oryginalne wartości domyślne przypisania. Przykładem jest to, że użytkownik jest przypisywany jako właściciel subskrypcji w dzierżawie. W przypadku aktywacji z zakresem można aktywować rolę właściciela dla maksymalnie pięciu zasobów zawartych w ramach subskrypcji (takich jak grupy zasobów i maszyny wirtualne). Określanie zakresu aktywacji może ograniczyć możliwość wykonywania niepożądanych zmian w krytycznych zasobach platformy Azure.

Aby uzyskać więcej informacji, zobacz [co to jest Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md).

---

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne w galerii aplikacji usługi Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** aplikacja dla przedsiębiorstw **:** integracja z firmą

W grudniu 2017 dodaliśmy te nowe aplikacje z obsługą Federacji do naszej galerii aplikacji:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [Digital](https://go.microsoft.com/fwlink/?linkid=861685)websklepie, [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [Image działa](https://go.microsoft.com/fwlink/?linkid=863517), [mobi](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD Integration](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO dla Bamboo, rezolucja GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [Logowanie jednokrotne SAML dla BitBucket, rezolucja GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD Integration.

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md).

---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Przepływy pracy zatwierdzania dla ról w katalogu usługi Azure AD

**Typ:** Zmieniono **kategorię usługi funkcji:** Privileged Identity Management **możliwości produktu:** Privileged Identity Management

Przepływ pracy zatwierdzania dla ról w katalogu usługi Azure AD jest ogólnie dostępny.

Za pomocą przepływu pracy zatwierdzania Administratorzy ról uprzywilejowanych mogą wymagać od członków ról uprawnionych do żądania aktywacji roli, zanim będą mogli korzystać z roli uprzywilejowanej. Wiele użytkowników i grup może być delegowana odpowiedzialność za zatwierdzenie. Uprawnieni członkowie roli otrzymują powiadomienia po zakończeniu zatwierdzania, a ich rola jest aktywna.

---

### <a name="pass-through-authentication-skype-for-business-support"></a>Uwierzytelnianie przekazywane: obsługa usługi Skype dla firm

**Typ:** Zmieniono **kategorię usługi funkcji:** uwierzytelnienia (logowania) **możliwości produktu:** uwierzytelnianie użytkownika

Uwierzytelnianie przekazywane teraz obsługuje Logowanie użytkowników do aplikacji klienckich programu Skype dla firm, które obsługują nowoczesne uwierzytelnianie, w tym topologie online i hybrydowe.

Aby uzyskać więcej informacji, zobacz temat [topologie programu Skype dla firm obsługujące nowoczesne uwierzytelnianie](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported).

---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Aktualizacje Azure AD Privileged Identity Management dla usługi Azure RBAC (wersja zapoznawcza)

**Typ:** Zmieniono **kategorię usługi funkcji:** Privileged Identity Management **możliwości produktu:** Privileged Identity Management

Korzystając z publicznej wersji zapoznawczej programu Azure AD Privileged Identity Management (PIM) na potrzeby kontroli dostępu opartej na rolach (RBAC) na platformie Azure, możesz teraz:

* Używaj wystarczającej ilości miejsca do administrowania.
* Wymagaj zatwierdzenia w celu aktywowania ról zasobów.
* Zaplanuj przyszłą aktywację roli wymagającą zatwierdzenia zarówno dla ról usługi Azure AD, jak i platformy Azure.

Aby uzyskać więcej informacji, zobacz [Privileged Identity Management dla zasobów platformy Azure (wersja zapoznawcza)](../privileged-identity-management/azure-pim-resource-rbac.md).

---

## <a name="november-2017"></a>Listopad 2017

### <a name="access-control-service-retirement"></a>Wycofanie usługi Access Control

**Typ:** Zaplanuj **kategorię usługi zmiany:** Access Control **możliwości produktu:** Access Control usługi

Azure Active Directory Access Control (znana także jako usługa Access Control) zostanie wycofana z opóźnieniem 2018. Więcej informacji obejmujących szczegółowy harmonogram i wskazówki dotyczące migracji wysokiego poziomu zostaną podane w ciągu następnych kilku tygodni. Możesz pozostawić Komentarze na tej stronie z wszelkimi pytaniami dotyczącymi usługi Access Control, a członek zespołu otrzyma odpowiedzi na nie.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Ogranicz dostęp do przeglądarki do Intune Managed Browser

**Typ:** Planowanie **kategorii usługi zmiany:** dostęp warunkowy do **produktu:** zabezpieczenia i Ochrona tożsamości

Możesz ograniczyć dostęp do przeglądarki do pakietu Office 365 i innych aplikacji w chmurze połączonych z usługą Azure AD przy użyciu Intune Managed Browser jako zatwierdzonej aplikacji.

Teraz można skonfigurować następujący warunek dla dostępu warunkowego opartego na aplikacji:

**Aplikacje klienckie:** Przeglądarka

**Jaki jest wpływ zmiany?**

Obecnie dostęp jest blokowany w przypadku korzystania z tego warunku. Gdy wersja zapoznawcza jest dostępna, wszystkie uprawnienia dostępu będą wymagały używania aplikacji Managed Browser.

Zapoznaj się z tą możliwością i więcej informacji w nadchodzących blogach i informacjach o wersji.

Aby uzyskać więcej informacji, zobacz [dostęp warunkowy w usłudze Azure AD](../conditional-access/overview.md).

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie dla dostępu warunkowego opartego na aplikacji usługi Azure AD

**Typ:** Planowanie **kategorii usługi zmiany:** dostęp warunkowy do **produktu:** zabezpieczenia i Ochrona tożsamości

Następujące aplikacje znajdują się na liście [zatwierdzonych aplikacji klienckich](../conditional-access/concept-conditional-access-conditions.md#client-apps):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Aby uzyskać więcej informacji, zobacz:

- [Wymagania dotyczące zatwierdzonej aplikacji klienckiej](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Obsługa warunków użytkowania w wielu językach

**Typ:** Nowa **Kategoria usługi funkcji:** warunki użytkowania **możliwości produktu:** zgodność

Administratorzy mogą teraz tworzyć nowe warunki użytkowania zawierające wiele dokumentów PDF. Można oznaczyć te dokumenty PDF przy użyciu odpowiedniego języka. Użytkownicy są wyświetlani w formacie PDF przy użyciu odpowiedniego języka w oparciu o ich preferencje. Jeśli nie ma dopasowania, wyświetlany jest język domyślny.

---

### <a name="real-time-password-writeback-client-status"></a>Stan klienta zapisywania zwrotnego hasła w czasie rzeczywistym

**Typ:** Nowa **Kategoria usługi funkcji:** funkcja samoobsługowego resetowania hasła **:** uwierzytelnianie użytkownika

Teraz można sprawdzić stan lokalnego klienta zapisywania zwrotnego haseł. Ta opcja jest dostępna w sekcji **integracja lokalna na** stronie [resetowania hasła](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) .

Jeśli występują problemy z połączeniem z lokalnym klientem zapisywania zwrotnego, zobaczysz komunikat o błędzie, który oferuje następujące informacje:

- Informacje o tym, dlaczego nie można nawiązać połączenia z lokalnym klientem zapisywania zwrotnego.
- Link do dokumentacji, która pomaga w rozwiązaniu problemu.

Aby uzyskać więcej informacji, zobacz [integracja lokalna](../authentication/concept-sspr-howitworks.md#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Dostęp warunkowy oparty na aplikacji usługi Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** **Funkcja produktu** Azure AD: zabezpieczenia i Ochrona tożsamości

Teraz możesz ograniczyć dostęp do pakietu Office 365 i innych aplikacji w chmurze połączonych z usługą Azure AD do [zatwierdzonych aplikacji klienckich](../conditional-access/concept-conditional-access-conditions.md#client-apps) , które obsługują zasady ochrony aplikacji usługi Intune przy użyciu [dostępu warunkowego opartego na aplikacji w usłudze Azure AD](../conditional-access/app-based-conditional-access.md). Zasady ochrony aplikacji usługi Intune służą do konfigurowania i ochrony danych firmowych w tych aplikacjach klienckich.

Łącząc [aplikacje oparte](../conditional-access/app-based-conditional-access.md) na urządzeniach z zasadami dostępu warunkowego [opartymi na urządzeniu](../conditional-access/require-managed-devices.md) , można zapewnić ochronę danych na urządzeniach osobistych i firmowych.

Następujące warunki i kontrolki są teraz dostępne do użycia z dostępem warunkowym opartym na aplikacji:

**Obsługiwany warunek platformy**

- iOS
- Android

**Warunek aplikacji klienckich**

- Aplikacje mobilne i klienci stacjonarni

**Kontrola dostępu**

- Wymaganie zatwierdzonej aplikacji klienckiej

Aby uzyskać więcej informacji, zobacz [dostęp warunkowy oparty na aplikacji usługi Azure AD](../conditional-access/app-based-conditional-access.md).

---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Zarządzanie urządzeniami usługi Azure AD w Azure Portal

**Typ:** Nowa **Kategoria usługi funkcji:** rejestracja urządzeń i **możliwość zarządzania produktu:** zabezpieczenia i Ochrona tożsamości

Teraz można znaleźć wszystkie urządzenia połączone z usługą Azure AD oraz działania związane z urządzeniami w jednym miejscu. Istnieje nowe środowisko administracyjne do zarządzania wszystkimi tożsamościami i ustawieniami urządzeń w Azure Portal. W tej wersji można:

- Wyświetl wszystkie urządzenia, które są dostępne dla dostępu warunkowego w usłudze Azure AD.
- Wyświetl właściwości, w tym urządzenia dołączone do hybrydowej usługi Azure AD.
- Znajdź klucze funkcji BitLocker dla urządzeń przyłączonych do usługi Azure AD, Zarządzaj urządzeniem w usłudze Intune i nie tylko.
- Zarządzanie ustawieniami związanymi z urządzeniami usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Zarządzanie urządzeniami przy użyciu Azure Portal](../devices/device-management-azure-portal.md).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Obsługa macOS jako platformy urządzeń dla dostępu warunkowego usługi Azure AD

**Typ:** Nowa **Kategoria usługi funkcji:** dostęp warunkowy do **produktu:** zabezpieczenia i Ochrona tożsamości

Teraz można uwzględnić (lub wykluczyć) macOS jako warunek platformy urządzenia w zasadach dostępu warunkowego usługi Azure AD. Dodanie macOS do obsługiwanych platform urządzeń pozwala wykonać następujące działania:

- **Rejestrowanie urządzeń macOS i zarządzanie nimi za pomocą usługi Intune.** Podobnie jak w przypadku innych platform, takich jak iOS i Android, aplikacja Portal firmy jest dostępna do macOS na potrzeby ujednoliconych rejestracji. Możesz użyć nowej aplikacji Portal firmy dla usługi macOS, aby zarejestrować urządzenie w usłudze Intune i zarejestrować je w usłudze Azure AD.
- **Upewnij się, że urządzenia macOS są zgodne z zasadami zgodności organizacji zdefiniowanymi w usłudze Intune.** W usłudze Intune na Azure Portal można teraz skonfigurować zasady zgodności dla urządzeń macOS.
- **Ogranicz dostęp do aplikacji w usłudze Azure AD tylko do zgodnych urządzeń macOS.** Tworzenie zasad dostępu warunkowego ma macOS jako osobną opcję platformy urządzeń. Teraz można tworzyć zasady dostępu warunkowego dla macOS dla zestawu aplikacji dla konkretnych platform na platformie Azure.

Aby uzyskać więcej informacji, zobacz:

- [Tworzenie zasad zgodności urządzeń dla urządzeń z systemem macOS w usłudze Intune](https://aka.ms/macoscompliancepolicy)
- [Dostęp warunkowy w usłudze Azure Active Directory](../conditional-access/overview.md)

---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Rozszerzenie serwera zasad sieciowych dla usługi Azure Multi-Factor Authentication

**Typ:** Nowa **Kategoria usługi funkcji:**  funkcja uwierzytelniania wieloskładnikowego **produktu:** uwierzytelnianie użytkownika

Rozszerzenie serwera zasad sieciowych dla platformy Azure Multi-Factor Authentication dodaje do infrastruktury uwierzytelniania możliwości Multi-Factor Authentication oparte na chmurze, używając istniejących serwerów. Za pomocą rozszerzenia serwera zasad sieciowych możesz dodać połączenie telefoniczne, wiadomość SMS lub weryfikację aplikacji telefonicznej do istniejącego przepływu uwierzytelniania. Nie trzeba instalować, konfigurować i obsługiwać nowych serwerów.

To rozszerzenie zostało utworzone dla organizacji, które chcą chronić połączenia wirtualnej sieci prywatnej bez wdrażania Serwer Multi-Factor Authentication platformy Azure. Rozszerzenie serwera zasad sieciowych pełni rolę karty między usługą RADIUS i opartymi na chmurze Multi-Factor Authentication Azure, aby zapewnić drugi czynnik uwierzytelniania dla federacyjnych lub synchronizowanych użytkowników.

Aby uzyskać więcej informacji, zobacz [integrowanie istniejącej infrastruktury serwera zasad sieciowych z usługą Azure Multi-Factor Authentication](../authentication/howto-mfa-nps-extension.md).

---

### <a name="restore-or-permanently-remove-deleted-users"></a>Przywracanie lub trwałe usuwanie usuniętych użytkowników

**Typ:** Nowa **Kategoria usługi funkcji:** **możliwości produktu** zarządzania użytkownikami: katalog

W centrum administracyjnym usługi Azure AD możesz teraz:

- Przywracanie usuniętego użytkownika.
- Trwale Usuń użytkownika.

**Aby wypróbować ten element:**

1. W centrum administracyjnym usługi Azure AD wybierz pozycję [Wszyscy użytkownicy](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) w sekcji **Zarządzanie** .

2. Z listy **Pokaż** wybierz pozycję **ostatnio usunięci użytkownicy**.

3. Wybierz co najmniej jednego z ostatnio usuniętych użytkowników, a następnie Przywróć je lub trwale Usuń.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie dla dostępu warunkowego opartego na aplikacji usługi Azure AD

**Typ:** Zmieniono **kategorię usługi funkcji:** dostęp warunkowy do **produktu:** zabezpieczenia i Ochrona tożsamości

Następujące aplikacje zostały dodane do listy [zatwierdzonych aplikacji klienckich](../conditional-access/concept-conditional-access-conditions.md#client-apps):

- Microsoft Planner
- Azure Information Protection

Aby uzyskać więcej informacji, zobacz:

- [Wymagania dotyczące zatwierdzonej aplikacji klienckiej](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Używanie "OR" między kontrolkami w zasadach dostępu warunkowego

**Typ:** Zmieniono **kategorię usługi funkcji:** dostęp warunkowy do **produktu:** zabezpieczenia i Ochrona tożsamości

Teraz można użyć opcji "OR" (wymaga jednej z wybranych kontrolek) do kontroli dostępu warunkowego. Za pomocą tej funkcji można tworzyć zasady z "lub" między kontrolkami dostępu. Można na przykład użyć tej funkcji w celu utworzenia zasad, które wymagają od użytkownika zalogowania się za pomocą Multi-Factor Authentication "lub" jako na zgodnym urządzeniu.

Aby uzyskać więcej informacji, zobacz [mechanizmy kontroli dostępu warunkowego usługi Azure AD](../conditional-access/controls.md).

---

### <a name="aggregation-of-real-time-risk-detections"></a>Agregacja wykrycia ryzyka w czasie rzeczywistym

**Typ:** Zmieniono **kategorię usługi funkcji:** **zdolność produktu** do ochrony tożsamości: bezpieczeństwo i Ochrona tożsamości

W Azure AD Identity Protection wszystkie wykrycia ryzyka w czasie rzeczywistym pochodzące z tego samego adresu IP w danym dniu są teraz agregowane dla każdego typu wykrywania ryzyka. Ta zmiana ogranicza ilość wykrytych wykryć ryzyka bez zmian w zabezpieczeniach użytkownika.

Podstawowe wykrywanie w czasie rzeczywistym działa po każdym zalogowaniu się użytkownika. W przypadku skonfigurowania zasad zabezpieczeń dotyczących ryzyka związanego z logowaniem do Multi-Factor Authentication lub blokowania dostępu jest on nadal wyzwalany podczas każdego ryzykownego logowania.

---

## <a name="october-2017"></a>Październik 2017

### <a name="deprecate-azure-ad-reports"></a>Przestarzałe raporty usługi Azure AD

**Typ:** Zaplanuj **kategorię usługi zmiany:** raportowanie **możliwości produktu:** Zarządzanie cyklem życia tożsamości

Azure Portal oferuje następujące informacje:

- Nowa konsola administracyjna usługi Azure AD.
- Nowe interfejsy API dla raportów działań i zabezpieczeń.

Ze względu na te nowe możliwości zostały wycofane interfejsy API raportów w punkcie końcowym/Reports w dniu 10 grudnia 2017.

---

### <a name="automatic-sign-in-field-detection"></a>Automatyczne wykrywanie pól logowania

**Typ:** **Kategoria stałej usługi:** **możliwości produktu** moje aplikacje: Logowanie jednokrotne

Usługa Azure AD obsługuje automatyczne wykrywanie pól logowania dla aplikacji, które renderują pole Nazwa użytkownika i hasło w formacie HTML. Te kroki są udokumentowane w [sposób automatycznego przechwytywania pól logowania dla aplikacji](../manage-apps/configure-password-single-sign-on-non-gallery-applications-problems.md#manually-capture-sign-in-fields-for-an-app). Tę możliwość można znaleźć, dodając aplikację *spoza galerii* na stronie aplikacje dla **przedsiębiorstw** w [Azure Portal](https://aad.portal.azure.com). Ponadto można skonfigurować tryb **logowania** jednokrotnego w tej nowej aplikacji do **logowania jednokrotnego opartego na hasłach**, wprowadzić adres URL sieci Web, a następnie zapisać stronę.

Ze względu na problem z usługą ta funkcja została tymczasowo wyłączona. Problem został rozwiązany, a automatyczne wykrywanie pól logowania jest dostępne ponownie.

---

### <a name="new-multi-factor-authentication-features"></a>Nowe funkcje Multi-Factor Authentication

**Typ:** Nowa **Kategoria usługi funkcji:** funkcja usługi uwierzytelniania wieloskładnikowego **:** zabezpieczenia i Ochrona tożsamości

Uwierzytelnianie wieloskładnikowe (MFA) to istotna część ochrony organizacji. Aby poświadczenia były bardziej adaptacyjne i bardziej bezproblemowe, dodano następujące funkcje:

- Wyniki testu wieloskładnikowego są bezpośrednio zintegrowane z raportem logowania do usługi Azure AD, który obejmuje dostęp programistyczny do wyników usługi MFA.
- Konfiguracja usługi MFA jest bardziej głęboko zintegrowana z konfiguracją środowiska Azure AD w Azure Portal.

Dzięki tej publicznej wersji zapoznawczej zarządzanie i raportowanie usługi MFA jest zintegrowaną częścią podstawowego środowiska konfiguracji usługi Azure AD. Teraz możesz zarządzać funkcjami portalu zarządzania usługą MFA w ramach środowiska usługi Azure AD.

Aby uzyskać więcej informacji, zobacz temat [Informacje o raportowaniu usługi MFA w Azure Portal](../authentication/howto-mfa-reporting.md).

---

### <a name="terms-of-use"></a>Warunki użytkowania

**Typ:** Nowa **Kategoria usługi funkcji:** warunki użytkowania **możliwości produktu:** zgodność

Warunki użytkowania usługi Azure AD umożliwiają prezentowanie informacji, takich jak odpowiednie odrzuty do wymagań prawnych lub zgodności dla użytkowników.

Warunków użytkowania usługi Azure AD można używać w następujących scenariuszach:

- Ogólne warunki użytkowania dla wszystkich użytkowników w organizacji
- Określone warunki użytkowania oparte na atrybutach użytkownika (na przykład lekarze lub pracownicy krajowi w porównaniu z pracownikami lokalnymi i międzynarodowymi, wykonane przez grupy dynamiczne)
- Specyficzne warunki użytkowania dotyczące uzyskiwania dostępu do aplikacji firmowych o dużym wpływie, takich jak Salesforce

Aby uzyskać więcej informacji, zobacz [warunki użytkowania usługi Azure AD](../conditional-access/terms-of-use.md).

---

### <a name="enhancements-to-privileged-identity-management"></a>Ulepszenia Privileged Identity Management

**Typ:** Nowa **Kategoria usługi funkcji:** Privileged Identity Management **możliwości produktu:** Privileged Identity Management

Za pomocą Azure AD Privileged Identity Management możesz zarządzać dostępem do zasobów platformy Azure (wersja zapoznawcza) i kontrolować je oraz monitorować, aby:

- Subskrypcje
- Grupy zasobów
- Maszyny wirtualne

Wszystkie zasoby w Azure Portal, które korzystają z funkcji RBAC platformy Azure, mogą korzystać ze wszystkich możliwości zarządzania zabezpieczeniami i cyklem życia, które Azure AD Privileged Identity Management oferty.

Aby uzyskać więcej informacji, zobacz [Privileged Identity Management dla zasobów platformy Azure](../privileged-identity-management/azure-pim-resource-rbac.md).

---

### <a name="access-reviews"></a>Przeglądy dostępu

**Typ:** Nowa **Kategoria usługi funkcji:** **możliwości produktu** przeglądy dostępu: zgodność

Organizacje mogą korzystać z przeglądów dostępu (wersja zapoznawcza) w celu wydajnego zarządzania członkostwem w grupach i dostępem do aplikacji dla przedsiębiorstw:

- Przeprowadzając przeglądy dostępu, można ponownie certyfikować dostęp użytkowników-gości do aplikacji i ich członkostwo w grupach. Recenzenci mogą efektywnie decydować, czy zezwolić Gościom na kontynuowanie dostępu na podstawie szczegółowych informacji dostępnych w ramach przeglądów dostępu.
- Przeprowadzając przeglądy dostępu, można ponownie certyfikować dostęp do aplikacji i członkostwo w grupach pracowników.

Kontrole z przeglądem dostępu można łączyć w programy odpowiednie dla danej organizacji, aby śledzić przeglądy dla aplikacji dotyczących zgodności lub aplikacji narażonych na ryzyko.

Aby uzyskać więcej informacji, zobacz [przeglądy dostępu w usłudze Azure AD](../governance/access-reviews-overview.md).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Ukryj aplikacje innych firm od moich aplikacji i uruchamiania aplikacji pakietu Office 365

**Typ:** Nowa **Kategoria usługi funkcji:** produkt moje aplikacje **: funkcja** rejestracji jednokrotnej

Teraz można lepiej zarządzać aplikacjami, które są wyświetlane w portalach użytkowników, za pomocą nowej właściwości **Ukryj aplikację** . Możesz ukryć aplikacje, aby pomóc w wykorzystaniu kafelków aplikacji na potrzeby usług zaplecza lub zduplikowanych kafelków oraz nieczytelnych uruchomień aplikacji użytkowników. Przełącznik znajduje się w sekcji **Właściwości** aplikacji innej firmy i jest oznaczony jako **widoczny dla użytkownika?** Możesz również programowo ukryć aplikację za pomocą programu PowerShell.

Aby uzyskać więcej informacji, zobacz [ukrywanie aplikacji innych firm ze środowiska użytkownika w usłudze Azure AD](../manage-apps/hide-application-from-user-portal.md).


**Co jest dostępne?**

 W ramach przejścia do nowej konsoli administracyjnej dostępne są dwa nowe interfejsy API służące do pobierania dzienników aktywności usługi Azure AD. Nowy zestaw interfejsów API zapewnia bogatsze funkcje filtrowania i sortowania, a także zapewnia bogatsze działania związane z inspekcją i logowaniem. Dane, które wcześniej były dostępne za pomocą raportów zabezpieczeń, są teraz dostępne za pomocą interfejsu API wykrywania ryzyka ochrony tożsamości w Microsoft Graph.


## <a name="september-2017"></a>Wrzesień 2017

### <a name="hotfix-for-identity-manager"></a>Poprawka dla programu Identity Manager

**Typ:** Zmieniono **kategorię usługi funkcji:** **możliwości produktu** programu Identity Manager: Zarządzanie cyklem życia tożsamości

Pakiet zbiorczy poprawek (build 4.4.1642.0) jest dostępny od 25 września 2017 dla programu Identity Manager 2016 z dodatkiem Service Pack 1. Ten pakiet zbiorczy:

- Rozwiązuje problemy i dodaje ulepszenia.
- Jest aktualizacją zbiorczą, która zastępuje wszystkie aktualizacje programu Identity Manager 2016 z dodatkiem Service Pack 1 do kompilacji 4.4.1459.0 dla programu Identity Manager 2016.
- Wymaga posiadania 4.4.1302.0 kompilacji programu Identity Manager 2016.

Aby uzyskać więcej informacji, zobacz [pakiet zbiorczy poprawek (build 4.4.1642.0) jest dostępny dla programu Identity Manager 2016 z dodatkiem Service Pack 1](https://support.microsoft.com/help/4021562).

---