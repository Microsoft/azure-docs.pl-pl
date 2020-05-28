---
title: Co nowego? Informacje o wersji — Azure Active Directory | Microsoft Docs
description: Dowiedz się, co nowego w Azure Active Directory, takich jak Najnowsze informacje o wersji, znane problemy, poprawki błędów, przestarzałe funkcje i nadchodzące zmiany.
services: active-directory
author: msmimart
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f879ebd2f3628b8282342d730a5f3957cf2a615f
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83994992"
---
# <a name="whats-new-in-azure-active-directory"></a>Co nowego w Azure Active Directory?

>Otrzymuj powiadomienia o tym, kiedy należy ponownie odwiedzić Tę stronę pod kątem aktualizacji przez skopiowanie i wklejenie tego adresu URL: do czytnika kanału informacyjnego kanału informacyjnego `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` ![ RSS ](./media/whats-new/feed-icon-16x16.png) .

Usługa Azure AD otrzymuje ulepszenia na bieżąco. Aby zachować aktualność w zakresie najnowszych zmian, ten artykuł zawiera informacje na temat:

- Najnowsze wersje
- Znane problemy
- Poprawki błędów
- Funkcje uznane za przestarzałe
- Plany zmian

Ta strona jest aktualizowana co miesiąc, dlatego należy ją regularnie odwiedzać. Jeśli szukasz elementów, które są starsze niż sześć miesięcy, możesz je znaleźć w archiwum, aby poznać nowości [w Azure Active Directory](whats-new-archive.md).

---

## <a name="april-2020"></a>Kwiecień 2020 r.

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>Środowisko rejestracji informacji o zabezpieczeniach jest teraz ogólnie dostępne

**Typ:** Nowa funkcja

**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)

**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Połączone środowisko rejestracji dla Multi-Factor Authentication (MFA) i samoobsługowego resetowania hasła (SSPR) jest teraz ogólnie dostępne. To nowe środowisko rejestracji umożliwia użytkownikom rejestrację usługi MFA i SSPR w jednym procesie krok po kroku. Po wdrożeniu nowego środowiska dla organizacji użytkownicy mogą rejestrować się w krótszym czasie i z mniejszą ilością problemów. Zapoznaj się z wpisem w blogu [tutaj](https://bit.ly/3etiRyQ).

---

### <a name="continuous-access-evaluation"></a>Ciągła ocena dostępu

**Typ:** Nowa funkcja

**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)

**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Ciągła ocena dostępu to nowa funkcja zabezpieczeń, która umożliwia niemal w czasie rzeczywistym egzekwowanie zasad dotyczących jednostek uzależnionych korzystających z tokenów dostępu usługi Azure AD w przypadku wystąpienia zdarzeń w usłudze Azure AD (takich jak usuwanie konta użytkownika). Ta funkcja jest najpierw wycofywana dla zespołów i klientów programu Outlook. Aby uzyskać więcej informacji, Przeczytaj nasz [blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) i [dokumentację](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation).

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>Logowanie za pomocą programu SMS: pracownicy Firstline mogą logować się do aplikacji z usługą Azure AD, używając numeru telefonu i bez hasła

**Typ:** Nowa funkcja

**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)

**Możliwość produktu:** Uwierzytelnianie użytkownika

Pakiet Office uruchamia serię aplikacji firmy dla urządzeń przenośnych, które są przeznaczone dla nietradycyjnych organizacji i pracowników w dużych organizacjach, które nie używają poczty e-mail jako podstawowej metody komunikacji. Te aplikacje umożliwiają pracownikom teraźniejszości, pracownikom, agentom pól lub pracownikom detalicznym, którzy nie otrzymują adresu e-mail od ich pracodawcy, mają dostęp do komputera lub do niego. Ten projekt umożliwi tym pracownikom logowanie do aplikacji firmowych, wprowadzając numer telefonu i roundtripping kod. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją administratora](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-sms-signin) i [dokumentacją użytkownika końcowego](https://docs.microsoft.com/azure/active-directory/user-help/sms-sign-in-explainer).

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Zapraszanie użytkowników wewnętrznych do korzystania z współpracy B2B

**Typ:** Nowa funkcja

**Kategoria usługi:** B2B

**Możliwość produktu:**

Rozszerzamy możliwości zaproszenia B2B, aby zezwolić na zapraszanie istniejących kont wewnętrznych do korzystania z poświadczeń współpracy B2B. Jest to realizowane przez przekazanie obiektu użytkownika do interfejsu API zapraszania jako uzupełnienie typowych parametrów, takich jak zaproszony adres e-mail. Identyfikator obiektu użytkownika, nazwa UPN, członkostwo w grupie, przypisywanie aplikacji itp. pozostają nienaruszone, ale w przyszłości będą używać B2B do uwierzytelniania przy użyciu poświadczeń dzierżawy głównej, a nie poświadczeń wewnętrznych używanych przed zaproszeniem. Aby uzyskać szczegółowe informacje, zapoznaj się z [dokumentacją](https://docs.microsoft.com/azure/active-directory/b2b/invite-internal-users).

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Tryb tylko do raportowania dla dostępu warunkowego jest teraz ogólnie dostępny

**Typ:** Nowa funkcja

**Kategoria usługi:** Dostęp warunkowy

**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

[Tryb tylko do raportowania dla dostępu warunkowego usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) pozwala oszacować wynik zasad bez wymuszania kontroli dostępu. Można testować zasady dotyczące tylko raportów w organizacji i zrozumieć ich wpływ przed ich włączeniem, zapewniając bezpieczniejsze i łatwiejsze wdrażanie. W ciągu ostatnich kilku miesięcy zaobserwowano silne wdrożenie trybu tylko do raportowania, a w przypadku ponad 26M użytkowników jest już w zasięgu zasad opartych na raportach. W tym ogłoszeniu nowe zasady dostępu warunkowego usługi Azure AD zostaną domyślnie utworzone w trybie tylko do raportowania. Oznacza to, że można monitorować wpływ zasad od momentu ich tworzenia. W przypadku użytkowników korzystających z interfejsów API programu MS Graph można również [programowo zarządzać zasadami dotyczącymi tylko raportów](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta). 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Szczegółowe informacje o dostępie warunkowym i skoroszycie raportów są ogólnie dostępne

**Typ:** Nowa funkcja

**Kategoria usługi:** Dostęp warunkowy

**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Skoroszyt uzyskiwania dostępu warunkowego [i raportów](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) zapewnia administratorom Widok podsumowania dostępu warunkowego usługi Azure AD w swojej dzierżawie. Korzystając z możliwości wybierania poszczególnych zasad, Administratorzy mogą lepiej zrozumieć, jakie zasady są wykonywane i które są monitorowane w czasie rzeczywistym. Skoroszyt strumieniuje dane przechowywane w Azure Monitor, które można skonfigurować w kilka minut, postępując zgodnie z [tymi instrukcjami](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics). Aby pulpit nawigacyjny był bardziej wykrywalny, został przeniesiony na nowe informacje i kartę Raportowanie w menu dostęp warunkowy usługi Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Blok Szczegóły zasad dostępu warunkowego jest w publicznej wersji zapoznawczej

**Typ:** Nowa funkcja

**Kategoria usługi:** Dostęp warunkowy

**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Nowy [blok Szczegóły zasad](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) przedstawia, które przypisania, warunki i formanty zostały spełnione podczas obliczania zasad dostępu warunkowego. Możesz uzyskać dostęp do bloku, zaznaczając wiersz na kartach **dostęp warunkowy** lub **tylko raport** w obszarze szczegóły logowania.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — kwiecień 2020

**Typ:** Nowa funkcja

**Kategoria usługi:** Aplikacje dla przedsiębiorstw

**Możliwości produktu:** integracja innej firmy

W kwietniu 2020 dodaliśmy te 31 nowych aplikacji z obsługą Federacji do galerii aplikacji: 

[SincroPool Apps](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [float](https://docs.microsoft.com/azure/active-directory/saas-apps/float-tutorial), [LMS365](https://lms.365.systems/), [IWT — pakiet zaopatrzenia](https://docs.microsoft.com/azure/active-directory/saas-apps/iwt-procurement-suite-tutorial), [Lunni](https://lunni.fi/), [EasySSO for JIRA](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-jira-tutorial), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [pulpit nawigacyjny Meraki](https://docs.microsoft.com/azure/active-directory/saas-apps/meraki-dashboard-tutorial), [pakiet Office 365 Przeprowadzka](https://app.mover.io/login), [głośniki](https://speakerengage.com/login.php), [uczciwie](https://docs.microsoft.com/azure/active-directory/saas-apps/honestly-tutorial), [Ally](https://docs.microsoft.com/azure/active-directory/saas-apps/ally-tutorial) [, DutyFlow,](https://app.dutyflow.nl/)AlertMedia, [gr8](https://docs.microsoft.com/azure/active-directory/saas-apps/gr8-people-tutorial), [pendo,](https://docs.microsoft.com/azure/active-directory/saas-apps/pendo-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/highground-tutorial) [, HighGround](https://docs.microsoft.com/azure/active-directory/saas-apps/harmony-tutorial) [, Timetabling](https://docs.microsoft.com/azure/active-directory/saas-apps/timetabling-solutions-tutorial) [,](https://www.made-in-office.com/en/) [SynchroNet,](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial) [, w](https://docs.microsoft.com/azure/active-directory/saas-apps/synchronet-click-tutorial)przypadku zmiany w [chmurze](https://docs.microsoft.com/azure/active-directory/saas-apps/fortes-change-cloud-tutorial) [, Litmus,](https://docs.microsoft.com/azure/active-directory/saas-apps/litmus-tutorial) [GroupTalk,](https://recorder.grouptalk.com/) [Frontify](https://docs.microsoft.com/azure/active-directory/saas-apps/frontify-tutorial) [,](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial) [Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/mongodb-cloud-tutorial) [, MongoDB](https://docs.microsoft.com/azure/active-directory/saas-apps/tickitlms-learn-tutorial), [TickitLMS](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/), [Coco](https://docs.microsoft.com/azure/active-directory/saas-apps/nitro-productivity-suite-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Obsługa zapytań różnicowych Microsoft Graph dla element oauth2permissiongrant dostępnych dla publicznej wersji zapoznawczej

**Typ:** Nowa funkcja

**Kategoria usługi:** Program MS Graph

**Możliwość produktu:** Środowisko deweloperskie

Zapytanie różnicowa dla element oauth2permissiongrant jest dostępne w publicznej wersji zapoznawczej! Teraz można śledzić zmiany bez konieczności ciągłego sondowania Microsoft Graph. [Dowiedz się więcej.](https://docs.microsoft.com/graph/api/oAuth2PermissionGrant-delta?view=graph-rest-beta&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Obsługa zapytań różnicowych Microsoft Graph na potrzeby kontaktu z organizacjami jest ogólnie dostępna

**Typ:** Nowa funkcja

**Kategoria usługi:** Program MS Graph

**Możliwość produktu:** Środowisko deweloperskie

Zapytanie różnicowe dotyczące kontaktów organizacyjnych jest ogólnie dostępne. Teraz można śledzić zmiany w aplikacjach produkcyjnych bez konieczności ciągłego sondowania Microsoft Graph. Zastąp wszelki istniejący kod, który stale sonduje dane orgContact przez zapytanie różnicowe, aby znacząco poprawić wydajność. [Dowiedz się więcej.](https://docs.microsoft.com/graph/api/orgcontact-delta?view=graph-rest-1.0&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Obsługa zapytań różnicowych Microsoft Graph dla aplikacji jest ogólnie dostępna

**Typ:** Nowa funkcja

**Kategoria usługi:** Program MS Graph

**Możliwość produktu:** Środowisko deweloperskie

Zapytanie różnicowa dla aplikacji jest ogólnie dostępne. Teraz można śledzić zmiany w aplikacjach produkcyjnych bez konieczności ciągłego sondowania Microsoft Graph. Zastąp wszystkie istniejące kod, który ciągle sonduje dane aplikacji przez zapytanie różnicowe, aby znacząco poprawić wydajność. [Dowiedz się więcej.](https://docs.microsoft.com/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Obsługa zapytań różnicowych Microsoft Graph dla jednostek administracyjnych dostępnych do publicznej wersji zapoznawczej

**Typ:** Nowa funkcja

**Kategoria usługi:** Program MS Graph

**Możliwość produktu:** Zapytanie różnicowe dotyczące środowiska deweloperskiego dla jednostek administracyjnych jest dostępne w publicznej wersji zapoznawczej. Teraz można śledzić zmiany bez konieczności ciągłego sondowania Microsoft Graph. [Dowiedz się więcej.](https://docs.microsoft.com/graph/api/administrativeunit-delta?view=graph-rest-beta&tabs=http)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Zarządzanie numerami telefonów i innymi innymi wersjami interfejsów API Microsoft Graph beta

**Typ:** Nowa funkcja

**Kategoria usługi:** Program MS Graph

**Możliwość produktu:** Środowisko deweloperskie

Te interfejsy API są kluczowym narzędziem do zarządzania metodami uwierzytelniania użytkowników. Teraz można programowo zarejestrować i zarządzać wystawcami używanymi na potrzeby uwierzytelniania wieloskładnikowego i samoobsługowego resetowania hasła (SSPR). Jest to jedna z najbardziej żądanych funkcji usługi Azure MFA, SSPR i Microsoft Graph. Nowe interfejsy API, które zostały opublikowane w tym elemencie Wave, dają możliwość:

- Odczytywanie, Dodawanie, aktualizowanie i usuwanie telefonów uwierzytelniania użytkownika
- Resetowanie hasła użytkownika
- Włączanie i wyłączanie programu SMS — Logowanie

Aby uzyskać więcej informacji, zobacz [Omówienie interfejsu API metod uwierzytelniania usługi Azure AD](https://docs.microsoft.com/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

---

### <a name="administrative-units-public-preview"></a>Jednostki administracyjne — publiczna wersja zapoznawcza

**Typ:** Nowa funkcja

**Kategoria usługi:** RBAC

**Możliwość produktu:** Access Control

Jednostki administracyjne umożliwiają Przyznawanie uprawnień administratora, które są ograniczone do działu, regionu lub innego segmentu zdefiniowanej organizacji. Jednostki administracyjne mogą służyć do delegowania uprawnień do administratorów regionalnych lub do ustawiania zasad na poziomie szczegółowym. Administrator konta użytkownika może na przykład zaktualizować informacje o profilu, zresetować hasła i przypisać licencje dla użytkowników tylko w ich jednostce administracyjnej.

Korzystając z jednostek administracyjnych, administrator centralny może:

- Tworzenie jednostki administracyjnej na potrzeby zdecentralizowanego zarządzania zasobami
- Przypisywanie roli z uprawnieniami administracyjnymi tylko dla użytkowników usługi Azure AD w jednostce administracyjnej
- W razie konieczności Wypełnij jednostki administracyjne użytkownikami i grupami

Aby uzyskać więcej informacji, zobacz [Zarządzanie jednostkami administracyjnymi w Azure Active Directory (wersja zapoznawcza)](https://aka.ms/AdminUnitsDocs).

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Role wbudowane dla administratorów drukarek i drukarek

**Typ:** Nowa funkcja

**Kategoria usługi:** RBAC

**Możliwość produktu:** Access Control

**Administrator drukarki**: Użytkownicy z tą rolą mogą rejestrować drukarki i zarządzać wszystkimi aspektami wszystkich konfiguracji drukarek w rozwiązaniu uniwersalnego drukowania firmy Microsoft, w tym ustawieniami uniwersalnego łącznika wydruku. Mogą oni wyrazić zgodę na wszystkie delegowane żądania uprawnień do drukowania. Administratorzy drukarek mają również dostęp do raportów drukowania. 

**Technika drukowania**: Użytkownicy z tą rolą mogą rejestrować drukarki i zarządzać stanem drukarki w uniwersalnym rozwiązaniu firmy Microsoft. Mogą również odczytywać wszystkie informacje o łączniku. Najważniejsze zadania nie można ustawić uprawnień użytkowników na drukarkach i udostępniających drukarki. [Dowiedz się więcej.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Wbudowana rola administratora tożsamości hybrydowej

**Typ:** Nowa funkcja

**Kategoria usługi:** RBAC

**Możliwość produktu:** Access Control

Użytkownicy w tej roli mogą włączać i konfigurować usługi oraz ustawienia związane z włączaniem tożsamości hybrydowej w usłudze Azure AD oraz zarządzać nimi. Ta rola zapewnia możliwość skonfigurowania usługi Azure AD na jednej z trzech obsługiwanych metod uwierzytelniania&#8212;synchronizacji skrótów haseł (PHS), uwierzytelniania przekazywanego (PTA) lub Federacji (AD FS lub dostawcy Federacji innej firmy) &#8212;oraz do wdrożenia powiązanej infrastruktury lokalnej w celu jej włączenia. Infrastruktura lokalna obejmuje agentów aprowizacji i PTA. Ta rola zapewnia możliwość włączenia bezproblemowego logowania jednokrotnego (SSO) w celu zapewnienia bezproblemowego uwierzytelniania na urządzeniach z systemem innym niż Windows 10 lub komputerach z systemem innym niż Windows Server 2016. Ponadto ta rola zapewnia możliwość wyświetlania dzienników logowania i uzyskiwania dostępu do kondycji i analizy w celu monitorowania i rozwiązywania problemów. [Dowiedz się więcej.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Wbudowana rola administratora sieci

**Typ:** Nowa funkcja

**Kategoria usługi:** RBAC

**Możliwość produktu:** Access Control

Użytkownicy z tą rolą mogą zapoznać się z zaleceniami dotyczącymi architektury obwodowej sieci firmy Microsoft, które są oparte na telemetrii sieci z lokalizacji użytkownika. Wydajność sieci dla pakietu Office 365 opiera się na starannej architekturze sieci firmowej klienta, która jest ogólnie specyficzna dla lokalizacji użytkownika. Ta rola pozwala edytować odnalezione lokalizacje użytkowników i konfigurację parametrów sieci dla tych lokalizacji, aby ułatwić lepsze pomiary telemetrii i zalecenia dotyczące projektowania. [Dowiedz się więcej.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Działania zbiorcze i pliki do pobrania w środowisku portalu administracyjnego usługi Azure AD

**Typ:** Nowa funkcja

**Kategoria usługi:** Zarządzanie użytkownikami

**Możliwość produktu:** Katalogi

Teraz można wykonywać działania zbiorcze dla użytkowników i grup w usłudze Azure AD, przekazując plik CSV w środowisku portalu administratora usługi Azure AD. Możesz tworzyć użytkowników, usuwać użytkowników i zapraszać użytkowników-Gości. I można dodawać i usuwać członków z grupy.

Możesz również pobrać listy zasobów usługi Azure AD ze środowiska portalu administracyjnego usługi Azure AD. Możesz pobrać listę użytkowników w katalogu, listę grup w katalogu i członków konkretnej grupy.

Aby uzyskać więcej informacji, zapoznaj się z następującymi informacjami:

- [Tworzenie użytkowników](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-add) lub [Zapraszanie użytkowników-Gości](https://docs.microsoft.com/azure/active-directory/b2b/tutorial-bulk-invite)
- [Usuwanie użytkowników](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-delete) lub [Przywracanie usuniętych użytkowników](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-restore)
- [Pobierz listę użytkowników](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-download) lub [Pobierz listę grup](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)
- [Dodawanie (importowanie) członków](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members) lub [usuwanie członków](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members) lub [Pobieranie listy członków](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members) grupy

---

### <a name="my-staff-delegated-user-management"></a>Moi personel delegowany do zarządzania użytkownikami

**Typ:** Nowa funkcja

**Kategoria usługi:** Zarządzanie użytkownikami

**Możliwość produktu:**

Mój personel umożliwia menedżerom Firstline, takim jak kierownik sklepu, zapewnienie im dostępu do swoich kont usługi Azure AD. Zamiast polegać na centralnej pomocy technicznej, organizacje mogą delegować typowe zadania, takie jak resetowanie haseł lub zmiana numerów telefonów, do Firstline Manager. W przypadku mojego personelu użytkownik, który nie może uzyskać dostępu do swojego konta, może ponownie uzyskać dostęp w zaledwie kilka kliknięć, bez pomocy technicznej ani personelu działu IT. Aby uzyskać więcej informacji, zobacz [Zarządzanie użytkownikami za pomocą mojego personelu (wersja zapoznawcza)](https://aka.ms/MyStaffAdminDocs) i [delegowanie zarządzania użytkownikami za pomocą mojego personelu (wersja zapoznawcza)](https://aka.ms/MyStaffUserDocs).

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>Uaktualnione środowisko użytkownika końcowego w przeglądach dostępu

**Typ:** Zmieniono funkcję

**Kategoria usługi:** Przeglądy dostępu

**Możliwość produktu:** Zarządzanie tożsamościami

Zaktualizowaliśmy środowisko recenzenta dla przeglądów dostępu do usługi Azure AD w portalu Moje aplikacje. Na koniec kwietnia recenzentów, którzy zalogowali się do środowiska recenzenta przeglądów dostępu usługi Azure AD zobaczyli transparent, który umożliwi im wypróbowanie zaktualizowanego środowiska w ramach dostępu. Należy pamiętać, że zaktualizowane środowisko przeglądów dostępu oferuje te same funkcje, co w przypadku bieżącego środowiska, ale udoskonalony interfejs użytkownika na nowe możliwości pozwalające wydajnie pracować nad użytkownikami. [Więcej informacji o zaktualizowanym doświadczeniu można znaleźć tutaj](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review). Ta Publiczna wersja zapoznawcza będzie Ostatnia do końca lipca 2020. Na koniec lipca Recenzenci, którzy nie zostali w trakcie korzystania z wersji zapoznawczej, zostaną automatycznie przekierowani do mojego dostępu w celu przeprowadzenia przeglądów dostępu. Jeśli chcesz, aby recenzenci mogli trwale przełączać się do funkcji wersji zapoznawczej w moim dostępie, wprowadź [tutaj żądanie](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Funkcja aprowizacji użytkowników przychodzących i zapisywania zwrotnego dla aplikacji w usłudze Workday obsługuje teraz najnowsze wersje interfejsu API usług sieci Web dla systemu Workday

**Typ:** Zmieniono funkcję

**Kategoria usługi:** Inicjowanie obsługi aplikacji

**Możliwość produktu:** 

W oparciu o opinie klientów Zaktualizowaliśmy teraz aplikacje przychodzącej obsługi administracyjnej użytkowników i zapisywania zwrotnego dla usługi Workday w galerii aplikacji dla przedsiębiorstw, które obsługują najnowsze wersje interfejsu API usług sieci Web (WWS). Dzięki tej zmianie klienci mogą określić wersję interfejsu API WWS, która ma być używana w parametrach połączenia. Dzięki temu klienci mogą pobrać więcej atrybutów HR dostępnych w wersjach dnia roboczego. Aplikacja zapisywania zwrotnego w programie Workday używa teraz zalecanej usługi sieci Web Change_Work_Contact_Info Workday do pokonania ograniczeń Maintain_Contact_Info.

Jeśli żadna wersja nie zostanie określona w parametrach połączenia, domyślnie aplikacje do inicjowania obsługi przychodzącej z usługi Workday będą nadal używać WWS v 21.1 w celu przełączenia się do najnowszych interfejsów API platformy Workday na potrzeby inicjowania obsługi klienta przychodzącego, a klienci muszą zaktualizować parametry połączenia zgodnie z opisem [w samouczku](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) , a także zaktualizować wyrażenia XPath używane dla atrybutów produktu Workday, zgodnie z opisem w [podręczniku odwołania do atrybutu](https://docs.microsoft.com/azure/active-directory/app-provisioning/workday-attribute-reference#xpath-values-for-workday-web-services-wws-api-v30) 

Aby można było użyć nowego interfejsu API do zapisywania zwrotnego, nie ma żadnych zmian wymaganych w aplikacji do inicjowania obsługi funkcji zapisywania zwrotnego dla produktu Workday. Na stronie Workday upewnij się, że konto użytkownika integracji z systemem Workday (ISU) ma uprawnienia do wywołania procesu biznesowego Change_Work_Contact zgodnie z opisem w sekcji samouczek, [Konfigurowanie uprawnień zasad zabezpieczeń procesów firmy](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#configuring-business-process-security-policy-permissions). 

Zaktualizowaliśmy [Przewodnik samouczka](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) , aby odzwierciedlić obsługę nowej wersji interfejsu API.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Użytkownicy z domyślną rolą dostępu są teraz w zakresie aprowizacji

**Typ:** Zmieniono funkcję

**Kategoria usługi:** Inicjowanie obsługi aplikacji

**Możliwość produktu:** Zarządzanie cyklem życia tożsamości

W przeszłości użytkownicy z domyślną rolą dostępu znajdują się poza zakresem aprowizacji. Otrzymaliśmy Opinie, dla których klienci chcą, aby użytkownicy z tą rolą mieli zasięg w zakresie aprowizacji. Od 16 kwietnia 2020 wszystkie nowe konfiguracje aprowizacji umożliwiają zainicjowanie obsługi administracyjnej użytkowników z domyślną rolą dostępu. Stopniowo zmienimy zachowanie istniejących konfiguracji aprowizacji, aby obsługiwać użytkowników aprowizacji z tą rolą. [Dowiedz się więcej.](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)

---

### <a name="updated-provisioning-ui"></a>Zaktualizowany interfejs użytkownika aprowizacji

**Typ:** Zmieniono funkcję

**Kategoria usługi:** Inicjowanie obsługi aplikacji

**Możliwość produktu:** Zarządzanie cyklem życia tożsamości

Nasze środowisko aprowizacji zostało odświeżone, aby utworzyć bardziej ukierunkowany widok zarządzania. Po przejściu do bloku aprowizacji dla aplikacji dla przedsiębiorstw, która została już skonfigurowana, można łatwo monitorować postęp aprowizacji i zarządzać akcjami, takimi jak uruchamianie, zatrzymywanie i ponowne uruchamianie aprowizacji. [Dowiedz się więcej.](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>Sprawdzanie poprawności reguły grupy dynamicznej jest teraz dostępne w publicznej wersji zapoznawczej

**Typ:** Zmieniono funkcję

**Kategoria usługi:** Zarządzanie grupami

**Możliwość produktu:** Społeczności

Azure Active Directory (Azure AD) zapewnia teraz metodę weryfikacji reguł grupy dynamicznej. Na karcie **Sprawdzanie poprawności reguł** można sprawdzić poprawność reguły dynamicznej względem przykładowych członków grupy, aby potwierdzić, że reguła działa zgodnie z oczekiwaniami. Podczas tworzenia lub aktualizowania zasad grupy dynamicznej Administratorzy chcą wiedzieć, czy użytkownik lub urządzenie będą członkiem grupy. Pomaga to w ocenie, czy użytkownik lub urządzenie spełnia kryteria reguły i pomoc w rozwiązywaniu problemów, gdy członkostwo nie jest oczekiwane.

Aby uzyskać więcej informacji, zobacz [Weryfikowanie reguły członkostwa w grupie dynamicznej (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-rule-validation).

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Ocena bezpiecznego stanu — aktualizacje zabezpieczeń i akcje poprawy usługi MFA

**Typ:** Zmieniono funkcję

**Kategoria usługi:** NIE DOTYCZY

**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

**Obsługa domyślnych ustawień zabezpieczeń dla akcji poprawy usługi Azure AD:** Firma Microsoft Security Score będzie aktualizować akcje naprawcze w celu obsługi [ustawień domyślnych zabezpieczeń w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults), co ułatwia ochronę organizacji przy użyciu wstępnie skonfigurowanych ustawień zabezpieczeń dla typowych ataków. Będzie to miało wpływ na następujące akcje ulepszania:

- Upewnij się, że wszyscy użytkownicy mogą ukończyć uwierzytelnianie wieloskładnikowe w celu zapewnienia bezpiecznego dostępu
- Wymagaj uwierzytelniania wieloskładnikowego dla ról administracyjnych
- Włącz zasady blokujące starsze uwierzytelnianie
 
**Aktualizacje akcji poprawy usługi MFA:** Aby odzwierciedlić potrzebę firm, aby zapewnić najwyższy poziom bezpieczeństwa podczas stosowania zasad, które współpracują z firmą, firma Microsoft Security Score usunęła trzy akcje poprawy wyśrodkowane przez uwierzytelnianie wieloskładnikowe i dodaje dwa.

Usunięte akcje ulepszania:

- Rejestrowanie wszystkich użytkowników do uwierzytelniania wieloskładnikowego
- Wymaganie uwierzytelniania wieloskładnikowego dla wszystkich użytkowników
- Wymagaj uwierzytelniania wieloskładnikowego dla ról uprzywilejowanych usługi Azure AD

Dodano akcje ulepszania:

- Upewnij się, że wszyscy użytkownicy mogą ukończyć uwierzytelnianie wieloskładnikowe w celu zapewnienia bezpiecznego dostępu
- Wymagaj uwierzytelniania wieloskładnikowego dla ról administracyjnych

Te nowe akcje poprawy wymagają zarejestrowania użytkowników lub administratorów na potrzeby uwierzytelniania wieloskładnikowego (MFA) w katalogu i ustanowienia odpowiedniego zestawu zasad pasujących do potrzeb organizacji. Głównym celem jest zapewnienie elastyczności, dzięki czemu wszyscy użytkownicy i Administratorzy mogą uwierzytelniać się przy użyciu wielu czynników lub z zapytaniem do weryfikacji tożsamości opartej na ryzyku. Może to mieć formę istnienia wielu zasad, które stosują decyzje w zakresie lub ustawienia domyślne zabezpieczeń (od 16 marca), dzięki którym firma Microsoft zdecyduje się, kiedy należy zakwestionować użytkowników usługi MFA. [Przeczytaj więcej na temat Nowości w zabezpieczeniach firmy Microsoft](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new).

---

## <a name="march-2020"></a>Marzec 2020 r.

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Niezarządzane konta Azure Active Directory w aktualizacji B2B dla marca 2021

**Typ:** Planowanie zmiany  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C
 
**Począwszy od 31 marca 2021**, firma Microsoft nie będzie już obsługiwać wykupu zaproszeń przez tworzenie niezarządzanych kont Azure Active Directory (Azure AD) i dzierżawców na potrzeby scenariuszy współpracy B2B. Zachęcamy do tego, abyśmy mogli korzystać z [uwierzytelniania jednorazowego kodu dostępu za pośrednictwem poczty e-mail](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode).

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Użytkownicy z domyślną rolą dostępu będą znajdować się w zakresie aprowizacji

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
W przeszłości użytkownicy z domyślną rolą dostępu znajdują się poza zakresem aprowizacji. Otrzymaliśmy Opinie, dla których klienci chcą, aby użytkownicy z tą rolą mieli zasięg w zakresie aprowizacji. Pracujemy nad wdrożeniem zmiany, dzięki czemu wszystkie nowe konfiguracje aprowizacji będą zezwalały użytkownikom z domyślną rolą dostępu. Stopniowo zmieniamy zachowanie istniejących konfiguracji aprowizacji, aby obsługiwać użytkowników aprowizacji z tą rolą. Nie jest wymagane wykonanie jakiejkolwiek czynności przez klienta. Po wprowadzeniu tej zmiany wyślemy aktualizację do naszej [dokumentacji](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) .

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Współpraca B2B w usłudze Azure AD będzie dostępna w Microsoft Azure obsługiwanych przez firmę 21Vianet (Azure Chiny 21Vianet)

**Typ:** Planowanie zmiany  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C
 
Możliwości współpracy B2B usługi Azure AD będą dostępne w Microsoft Azure obsługiwanych przez firmę 21Vianet (platformy Azure z Chin), umożliwiając użytkownikom w Chinach firmy 21Vianet współdziałanie z użytkownikami w innych dzierżawach usługi Azure Chiny. [Dowiedz się więcej o współpracy B2B usługi Azure AD](https://docs.microsoft.com/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Przeprojektowana wiadomość e-mail z zaproszeniem do współpracy B2B usługi Azure AD

**Typ:** Planowanie zmiany  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C
 
[Wiadomości e-mail](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) wysyłane przez usługę zaproszeń do współpracy B2B usługi Azure AD w celu zapraszania użytkowników do katalogu zostaną przeprojektowane w celu uzyskania informacji o zaproszeniach i kolejnych krokach użytkownika.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Zmiany zasad HomeRealmDiscovery będą widoczne w dziennikach inspekcji

**Typ:** FIXED  
**Kategoria usługi:** Wizyjn  
**Możliwość produktu:** Monitorowanie & raportowania
 
Rozwiązano problem polegający na tym, że zmiany [zasad HomeRealmDiscovery](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) nie zostały uwzględnione w dziennikach inspekcji. Teraz będzie można zobaczyć, kiedy i w jaki sposób zasady zostały zmienione. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — marzec 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W marcu 2020 dodaliśmy do galerii aplikacji 51 te nowe aplikacje z obsługą Federacji: 

[Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect), [Zoho jeden Chiny](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co aplikacji SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial) [iPoint Service Provider](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial), [Node Insight](https://admin.nodeinsight.com/AADLogin.aspx) [Campus Café](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial) [contexxt.AI Sphere](https://contexxt-sphere.com/login), [mądry przez Invictus](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial), [Flara cyfrowa](https://spark-dev.pixelnebula.com/login),, wgląd [w chmurę dla inżynierów](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial), [Logz.io](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial), [SpectrumU](https://bizzcontact.app/), [BizzContact rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial), [Elqano](http://www.signshare.com/), [MarketSignShare Learning Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial),, CrossKnowledge, COMPAS [Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial) [żebra](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial) [A/S FCM Mobile, Byggeweb](https://apps.apple.com/us/app/docia/id529058757) [,](https://demo.asterapp.io/login) [GoLinks](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial), [usługi Datadog](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial)Planview Enterprise one [platforma](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial) [IP platform,](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial)InVision [,](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial)usługi PIPEDRIVE [,](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial)Zaprezentuj [warsztat,](https://app.showcaseworkshop.com/)Greenlight [Integration platform,](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial)Greenlight [zgodnego dostępu,](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial)Grok [uczenie](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial), Miradore [online,](https://login.online.miradore.com/)Khoros [opieki,](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial)AskYourTeam [,](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial)TruNarrative [,](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial)Smartwaiver [,](https://www.smartwaiver.com/m/user/sw_login.php?wms_login)Bizagi [Studio for Digital Process Automation,](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial)insuiteX [,](https://www.insuite.jp/)Sybo [,](https://www.systexsoftware.com.tw/)Britive [,](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial)WhosOffice [,](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial)E [-dni,](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial)Kollective [SDN,](https://portal.kollective.app/login) [WatchTeams](https://www.devfinition.com/)Witivio [,](https://app.witivio.com/) [PlayVox](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial) [,](https://my.playvox.com/login)360 [Netvision Compas](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial) [Skills Workflow](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial) [Korn Ferry 360](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial) [Catchpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Współpraca B2B w usłudze Azure AD jest dostępna w dzierżawach Azure Government

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C
 
Funkcje współpracy B2B usługi Azure AD są teraz dostępne między niektórymi dzierżawami Azure Government.  Aby dowiedzieć się, czy Dzierżawca może korzystać z tych możliwości, postępuj zgodnie z instrukcjami w temacie [Jak mogę sprawdzić, czy w mojej dzierżawie platformy Azure USA jest dostępna współpraca B2B?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure Monitor integracja z usługą Azure Logs jest teraz dostępna w Azure Government

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania
 
Azure Monitor integracja z dziennikami usługi Azure AD jest teraz dostępna w programie Azure Government. Dzienniki usługi Azure AD (inspekcja i dzienniki logowania) można kierować do konta magazynu, centrum zdarzeń i Log Analytics. Zapoznaj się ze [szczegółową dokumentacją](https://aka.ms/aadlogsinamd) , [a także planami wdrażania raportowania i monitorowania](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) scenariuszy usługi Azure AD.

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
 
Usługa Azure AD Provisioning oferuje bogaty zestaw funkcji konfiguracji. Klienci muszą mieć możliwość zapisania swojej konfiguracji, aby mogły odwoływać się do niej później lub przywracać do znanej dobrej wersji. Dodaliśmy możliwość pobrania konfiguracji aprowizacji jako pliku JSON i przekazania jej w razie potrzeby. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (Samoobsługowe resetowanie hasła) teraz wymaga dwóch bram dla administratorów Microsoft Azure obsługiwanych przez firmę 21Vianet (Azure Chiny 21Vianet) 

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Samoobsługowe resetowanie hasła  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Wcześniej w Microsoft Azure obsługiwane przez firmę 21Vianet (Azure Chiny 21Vianet) Administratorzy korzystający z funkcji samoobsługowego resetowania hasła (SSPR) do resetowania własnych haseł potrzebują tylko jednego "bramy" (wyzwanie), aby potwierdzić swoją tożsamość. W publicznych i innych chmurach narodowych Administratorzy zwykle muszą użyć dwóch bram, aby potwierdzić swoją tożsamość podczas korzystania z usługi SSPR. Ale ponieważ nie obsługujemy wiadomości SMS lub połączeń telefonicznych na platformie 21Vianet firmy Microsoft, zezwalamy na Resetowanie hasła przez administratorów.

Tworzymy SSPR funkcji między platformą Azure Chiny 21Vianet a chmurą publiczną. W przyszłości Administratorzy muszą używać dwóch bram podczas korzystania z usługi SSPR. Obsługa wiadomości SMS, połączeń telefonicznych i powiadomień aplikacji uwierzytelniania oraz kodów. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>Długość hasła jest ograniczona do 256 znaków

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Aby zapewnić niezawodność usługi Azure AD, hasła użytkowników mają teraz ograniczoną długość do 256 znaków. Użytkownicy z hasłami dłużej niż zostanie wyświetlony monit o zmianę hasła podczas kolejnego logowania przez skontaktowanie się z administratorem lub funkcją samoobsługowego resetowania hasła.

Ta zmiana została włączona 13 marca 2020, at od 10:00 PST (18:00 UTC), a błąd to AADSTS 50052, InvalidPasswordExceedsMaxLength. Aby uzyskać więcej informacji, zobacz [powiadomienie o zmianie](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters) .

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Dzienniki logowania usługi Azure AD są teraz dostępne dla wszystkich bezpłatnych dzierżawców za pomocą Azure Portal

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania
 
Od tej pory Klienci, którzy mają bezpłatnych dzierżawców, mogą uzyskać dostęp do [dzienników logowania usługi Azure AD z Azure Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) przez maksymalnie 7 dni. Wcześniej dzienniki logowania były dostępne tylko dla klientów z licencjami na Azure Active Directory — wersja Premium. W przypadku tej zmiany wszyscy dzierżawcy będą mogli uzyskiwać dostęp do tych dzienników za pomocą portalu.

> [!NOTE]
> Klienci nadal potrzebują licencji Premium (Azure Active Directory — wersja Premium P1 lub P2), aby uzyskać dostęp do dzienników logowania za pomocą Microsoft Graph API i Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Wycofanie opcji grup obejmujących cały katalog z ustawień ogólnych grup na Azure Portal

**Typ:** Przestarzałe  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności

Aby zapewnić klientom bardziej elastyczny sposób tworzenia grup obejmujących katalogi, które najlepiej odpowiadają potrzebom, zamienisz opcję **grupy w poziomie katalogów** z **Groups**  >  ustawień**ogólnych** grupy w Azure Portal z linkiem do [dokumentacji grupy dynamicznej](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership). Ulepszono nasze dokumenty w celu uwzględnienia dodatkowych instrukcji, aby administratorzy mogli tworzyć wszystkie grupy użytkowników, które obejmują lub wykluczają użytkowników-Gości.

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
 
Uaktualnij swoją organizację do nowego portalu Moje aplikacje, który jest teraz ogólnie dostępny! Więcej informacji na temat nowego portalu i kolekcji znajduje się w [sekcji Tworzenie kolekcji w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Nazwy obszarów roboczych w usłudze Azure AD zostały zmienione na Kolekcje

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Moje aplikacje   
**Możliwość produktu:** Środowiska użytkownika końcowego
 
Obszary robocze, Administratorzy filtrów mogą konfigurować, aby organizować aplikacje użytkowników, będą teraz określane jako kolekcje. Więcej informacji na temat sposobu ich konfigurowania można znaleźć w [sekcji Tworzenie kolekcji w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C Rejestracja i logowanie przy użyciu zasad niestandardowych (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C
 
Dzięki rejestrowaniu i rejestrowaniu numerów telefonów deweloperzy i przedsiębiorstwa mogą umożliwić klientom rejestrowanie się i logowanie przy użyciu hasła jednorazowego wysyłanego do numeru telefonu użytkownika za pośrednictwem wiadomości SMS. Ta funkcja umożliwia także zmianę numeru telefonu przez klienta w przypadku utraty dostępu do telefonu. Korzystając z możliwości zasad niestandardowych, rejestracja i logowanie za pomocą telefonu umożliwia deweloperom i przedsiębiorstwom komunikowanie się z nimi przez dostosowanie strony. Dowiedz się [, jak skonfigurować rejestrowanie i logowanie za pomocą zasad niestandardowych w Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Nowe łączniki aprowizacji w galerii aplikacji usługi Azure AD — styczeń 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzacja aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — styczeń 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W styczniu 2020 dodaliśmy do galerii aplikacji 33 te nowe aplikacje z obsługą Federacji: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [szybka granica w chmurze](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [odstrzelone](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial), [LeaveBot](https://leavebot.io/#home), [datacamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [SmartWork](https://www.intumit.com/english/SmartWork.html) [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial), [dotcom-monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla Oracle E-Business Suite — EBS, PeopleSoft i JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [Hostowana usługa MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [yuhu Property Management Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), Enterprise-and- [Work](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB for Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe SAML2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxients Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [helpshift,](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial) [PortalTalk 365](https://www.portaltalk.com/) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial) [Przegląd](https://portal.coreview.com/), [squelch Cloud](https://laxmi.squelch.io/login) [, PingFlow,](https://app-staging.pingview.io/) [PrinterLogic](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial), [Sandwai](https://app.sandwai.com/) [SaaS, Taskize](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial) [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="two-new-identity-protection-detections"></a>Dwa nowe wykrywania ochrony tożsamości

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Dodaliśmy dwa nowe typy wykrywania połączonego logowania do ochrony tożsamości: podejrzane reguły manipulowania skrzynką odbiorczą i niemożliwa podróż. Te wykrywania w trybie offline są wykrywane przez Microsoft Cloud App Security (MCAS) i wpływają na ryzyko związane z logowaniem w usłudze Identity Protection. Aby uzyskać więcej informacji na temat tych wykryć, zobacz [typy ryzyka logowania](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Istotna zmiana: fragmenty identyfikatorów URI nie będą przenoszone przy użyciu przekierowania logowania

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Od 8 lutego 2020, gdy żądanie jest wysyłane do login.microsoftonline.com w celu zalogowania użytkownika, usługa dołączy pusty fragment do żądania.  Pozwala to zapobiec atakom klasy w celu przekierowania przez zagwarantowanie, że przeglądarka wymaże wszystkie istniejące fragmenty w żądaniu. Żadna aplikacja nie powinna mieć zależności dotyczącej tego zachowania. Aby uzyskać więcej informacji, zobacz istotne [zmiany](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) w dokumentacji platformy tożsamości firmy Microsoft.

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

Aby uzyskać więcej informacji, zobacz temat [niestandardowa Weryfikacja poczty e-mail w Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-email).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Zastąpienie zasad odniesienia przy użyciu domyślnych ustawień zabezpieczeń

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

W ramach modelu bezpiecznego i domyślnego uwierzytelniania usuwamy istniejące zasady ochrony linii bazowej ze wszystkich dzierżawców. To usunięcie jest przeznaczone do ukończenia na koniec lutego. Zastąpienie tych zasad ochrony linii bazowej jest ustawieniami domyślnymi zabezpieczeń. Jeśli używasz zasad ochrony linii bazowej, musisz zaplanować przejście na nowe zasady ustawień zabezpieczeń lub dostęp warunkowy. Jeśli te zasady nie są używane, nie ma żadnych działań do wykonania.

Aby uzyskać więcej informacji na temat nowych domyślnych ustawień zabezpieczeń, zobacz [co to są ustawienia domyślne zabezpieczeń?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) Aby uzyskać więcej informacji na temat zasad dostępu warunkowego, zobacz [typowe zasady dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common).

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

Aby uzyskać więcej informacji, zobacz temat [Używanie raportu działania aplikacji AD FS do migrowania aplikacji do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Nowy przepływ pracy do żądania zgody administratora (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Access Control

Nowy przepływ pracy zgody administratora zapewnia administratorom możliwość udzielenia dostępu do aplikacji, które wymagają zatwierdzenia przez administratora. Jeśli użytkownik próbuje uzyskać dostęp do aplikacji, ale nie może zapewnić zgody, może teraz wysłać żądanie zatwierdzenia przez administratora. Żądanie jest wysyłane pocztą e-mail i umieszczane w kolejce, która jest dostępna w Azure Portal, do wszystkich administratorów, którzy zostali wyznaczeni jako recenzenci. Gdy recenzent podejmuje działania dotyczące oczekujących żądań, żądający użytkowników są powiadamiani o tej akcji.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie przepływu pracy zgody administratora (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nowe środowisko konfiguracji aplikacja usługi Azure AD rejestracji tokenu do zarządzania opcjonalnymi oświadczeniami (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Środowisko deweloperskie

W bloku **Konfiguracja nowego tokenu aplikacja usługi Azure AD rejestracji** na Azure Portal teraz są wyświetlane aplikacje dla deweloperów aplikacji, która jest dynamiczną listą opcjonalnych oświadczeń dla aplikacji. To nowe środowisko pomaga usprawnić migracje aplikacji usługi Azure AD i zminimalizować opcjonalne konfiguracje oświadczeń.

Aby uzyskać więcej informacji, zobacz [dostarczanie opcjonalnych oświadczeń do aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Nowy przepływ pracy zatwierdzania dwuetapowego w usłudze Azure AD uprawnia do zarządzania (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Zarządzanie prawami

Wprowadziliśmy Nowy dwuetapowy przepływ pracy zatwierdzania, który pozwala wymagać od dwóch osób zatwierdzających zatwierdzenie żądania użytkownika do pakietu dostępu. Na przykład można ustawić go tak, aby Menedżer żądającego użytkownika musiał najpierw ją zatwierdzić, a następnie można wymagać od właściciela zasobu zatwierdzić. Jeśli jedna z osób zatwierdzających nie zatwierdzi, dostęp nie zostanie udzielony.

Aby uzyskać więcej informacji, zobacz temat [Zmiana ustawień żądania i zatwierdzania dla pakietu dostępu w usłudze Azure AD uprawnienia zarządzania](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Aktualizacje strony Moje aplikacje oraz nowe obszary robocze (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwości produktu:** integracja innej firmy

Teraz możesz dostosować sposób wyświetlania przez użytkowników w organizacji i uzyskiwania dostępu do odświeżonych funkcji Moje aplikacje. To nowe środowisko obejmuje również nowe funkcje obszarów roboczych, które ułatwiają użytkownikom znajdowanie i organizowanie aplikacji.

Aby uzyskać więcej informacji na temat nowych funkcji Moje aplikacje i tworzenia obszarów roboczych, zobacz [Tworzenie obszarów roboczych w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Obsługa identyfikatorów firmy Google Social dla współpracy B2B usługi Azure AD (ogólna dostępność)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Nowe wsparcie w zakresie korzystania z identyfikatorów usługi Google Social (konta w usłudze Gmail) w usłudze Azure AD pomaga uprościć współpracę dla użytkowników i partnerów. Nie jest już konieczne, aby partnerzy mogli tworzyć nowe konta specyficzne dla firmy Microsoft i zarządzać nimi. Usługi Microsoft Teams są teraz w pełni obsługiwane przez użytkowników Google na wszystkich klientach i we wszystkich punktach końcowych uwierzytelniania związanych z dzierżawcami.

Aby uzyskać więcej informacji, zobacz temat [Dodawanie usługi Google jako dostawcy tożsamości dla użytkowników-Gości B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Microsoft Edge mobile support dla dostępu warunkowego i logowania jednokrotnego (ogólnie dostępna)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Usługa Azure AD dla przeglądarki Microsoft Edge w systemach iOS i Android obsługuje teraz Logowanie jednokrotne usługi Azure AD i dostęp warunkowy:

- Logowanie jednokrotne **w przeglądarce Microsoft Edge:** Logowanie jednokrotne jest teraz dostępne na natywnych klientach (takich jak Microsoft Outlook i Microsoft Edge) dla wszystkich aplikacji połączonych z usługą Azure AD.

- **Dostęp warunkowy do programu Microsoft Edge:** Korzystając z zasad dostępu warunkowego opartego na aplikacji, użytkownicy muszą używać przeglądarek chronionych przez Microsoft Intune, takich jak Microsoft Edge.

Aby uzyskać więcej informacji na temat dostępu warunkowego i logowania jednokrotnego przy użyciu przeglądarki Microsoft Edge, zobacz artykuł [Obsługa urządzeń przenośnych w usłudze Microsoft Edge Mobile dla dostępu warunkowego i logowania](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) jednokrotnego. Aby uzyskać więcej informacji o sposobie konfigurowania aplikacji klienckich przy użyciu [dostępu warunkowego opartego na aplikacji](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) lub [dostępu warunkowego opartego na urządzeniach](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices), zobacz [Zarządzanie dostępem do sieci Web za pomocą przeglądarki Microsoft Intuneej chronionej przez zasady](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Zarządzanie prawami w usłudze Azure AD (ogólna dostępność)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Zarządzanie prawami

Zarządzanie prawami w usłudze Azure AD to nowa funkcja zarządzania tożsamościami, która pomaga organizacjom zarządzać cyklem życia tożsamości i dostępu na dużą skalę. Ta nowa funkcja pomaga zautomatyzować przepływy pracy żądań dostępu, przypisań, przeglądów i wygaśnięcia między grupami, aplikacjami i witrynami usługi SharePoint Online.

Korzystając z usługi Azure AD uprawnia do zarządzania, możesz wydajnie zarządzać dostępem dla pracowników, a także dla użytkowników spoza organizacji, którzy potrzebują dostępu do tych zasobów.

Aby uzyskać więcej informacji, zobacz [co to jest zarządzanie prawami w usłudze Azure AD?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatyzowanie aprowizacji kont użytkowników dla tych nowo obsługiwanych aplikacji SaaS

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy  

Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

[Usługa SAP Cloud Platform Authentication Identity](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [OfficeSpace Software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [Priority Matrix](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzacja aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — listopad 2019

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W listopadzie 2019 dodaliśmy następujące 21 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial) [HootSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [Blue Access (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [Bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), NegometrixPortal — Logowanie jednokrotne [(SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [Fokoe](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), Qmarkets [pomysł & zarządzanie innowacyjnością](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), Netskopee [uwierzytelnianie użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), [uniFLOW online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial), [JISC student głosujących Registration](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Nowa i ulepszona Galeria aplikacji usługi Azure AD

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ

Zaktualizowaliśmy galerię aplikacji usługi Azure AD, aby ułatwić znajdowanie wstępnie zintegrowanych aplikacji obsługujących obsługę administracyjną, OpenID Connect Connect i SAML w dzierżawie Azure Active Directory.

Aby uzyskać więcej informacji, zobacz [Dodawanie aplikacji do dzierżawy Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Zwiększono limit długości definicji roli aplikacji z 120 do 240 znaków

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ

Firma Microsoft wysłuchuje od klientów, że długość limitu dla wartości definicji roli aplikacji w niektórych aplikacjach i usługach jest za mała o 120 znaków. W odpowiedzi Zwiększono maksymalną długość definicji wartości roli do 240 znaków.

Aby uzyskać więcej informacji o korzystaniu z definicji ról specyficznych dla aplikacji, zobacz [Dodawanie ról aplikacji w aplikacji i odbieranie ich w tokenie](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

---
