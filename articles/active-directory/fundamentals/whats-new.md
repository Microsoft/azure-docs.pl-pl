---
title: Co nowego Informacje o wersji — Azure Active Directory | Microsoft Docs
description: Dowiedz się, co nowego w programie Azure Active Directory; takie jak Najnowsze informacje o wersji, znane problemy, poprawki błędów, przestarzałe funkcje i nadchodzące zmiany.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 1/29/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c835ddcee332499ef7853d0d2fa4fcdbcf8a1cf
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988869"
---
# <a name="whats-new-in-azure-active-directory"></a>Co nowego w Azure Active Directory?

>Otrzymuj powiadomienia o tym, kiedy należy ponownie odwiedzić Tę stronę pod kątem aktualizacji przez skopiowanie i wklejenie tego adresu URL: do czytnika kanału informacyjnego kanału informacyjnego `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` ![ RSS ](./media/whats-new/feed-icon-16x16.png) .

Usługa Azure AD otrzymuje ulepszenia na bieżąco. Aby zachować aktualność w zakresie najnowszych zmian, ten artykuł zawiera informacje na temat:

- Najnowsze wersje
- Znane problemy
- Poprawki błędów
- Funkcje uznane za przestarzałe
- Plany zmian

Ta strona jest aktualizowana co miesiąc, dlatego należy ją regularnie odwiedzać. Jeśli szukasz elementów starszych niż sześć miesięcy, możesz je znaleźć w archiwum, aby poznać nowości [w Azure Active Directory](whats-new-archive.md).

---
## <a name="january-2021"></a>Styczeń 2021 r.

### <a name="secret-token-will-be-a-mandatory-field-when-configuring-provisioning"></a>W przypadku konfigurowania aprowizacji token tajny będzie obowiązkowy.

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości

W przeszłości pole tokenu tajnego może być przechowywane puste podczas konfigurowania aprowizacji w aplikacji niestandardowej/BYOA. Ta funkcja była przeznaczona wyłącznie do testowania. Zaktualizujemy interfejs użytkownika, aby pole było wymagane. 

Klienci mogą obejść ten wymóg na potrzeby testowania przy użyciu flagi funkcji w adresie URL przeglądarki. [Dowiedz się więcej](../app-provisioning/use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery).
 
---

### <a name="public-preview---customize-and-configure-android-shared-devices-for-firstline-workers-at-scale"></a>Publiczna wersja zapoznawcza — Dostosowywanie i Konfigurowanie urządzeń udostępnionych z systemem Android dla procesów roboczych Firstline na dużą skalę

**Typ:** Nowa funkcja  
**Kategoria usługi:** Rejestrowanie urządzeń i zarządzanie nimi  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Zespoły usługi Azure AD i Microsoft Endpoint Manager łączą się z możliwością dostosowywania, skalowania i zabezpieczania urządzeń roboczych Firstline.

Poniższe funkcje w wersji zapoznawczej umożliwią:
- Udostępnianie udostępnionych urządzeń z systemem Android na dużą skalę przy użyciu programu Microsoft Endpoint Manager
- Zabezpiecz swój dostęp dla pracowników przenoszonych przy użyciu dostępu warunkowego opartego na urządzeniach
- Dostosuj środowisko logowania dla pracowników przesunięć z zarządzanym ekranem głównym

Aby dowiedzieć się więcej, zapoznaj się z tematem [Dostosowywanie i Konfigurowanie udostępnionych urządzeń dla Firstlineych procesów roboczych na dużą skalę](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-and-configure-shared-devices-for-firstline-workers-at/ba-p/1751708).

---

### <a name="public-preview---provisioning-logs-can-now-be-downloaded-as-a-csv-or-json"></a>Publiczna wersja zapoznawcza — dzienniki aprowizacji można teraz pobrać jako woluminy CSV lub JSON

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości

Klienci mogą pobrać dzienniki aprowizacji jako plik CSV lub JSON za pośrednictwem interfejsu użytkownika i interfejsu API programu Graph. Aby dowiedzieć się więcej, zapoznaj się z [raportami aprowizacji w portalu Azure Active Directory](../reports-monitoring/concept-provisioning-logs.md).

---

### <a name="public-preview---assign-cloud-groups-to-azure-ad-custom-roles-and-admin-unit-scoped-roles"></a>Publiczna wersja zapoznawcza — przypisywanie grup w chmurze do ról niestandardowych i ról zakresu jednostki administracyjnej usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** RBAC  
**Możliwość produktu:** Access Control
 
Klienci mogą przypisywać grupy chmury do ról niestandardowych usługi Azure AD lub roli o zakresie jednostki administracyjnej. Aby dowiedzieć się, jak używać tej funkcji, zapoznaj się z tematem [Używanie grup w chmurze do zarządzania przypisaniami ról w Azure Active Directory](../roles/groups-concept.md).

---

### <a name="general-availability---azure-ad-connect-cloud-sync-previously-known-as-cloud-provisioning"></a>Ogólna dostępność — synchronizacja z chmurą Azure AD Connect (wcześniej znana jako aprowizacji w chmurze)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Azure AD Connect synchronizacji w chmurze  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
Azure AD Connect synchronizacja z chmurą jest teraz ogólnie dostępna dla wszystkich klientów.

Usługa Azure AD Connect Cloud przenosi silną transformację logiki transformacji do chmury i zmniejsza jej wartość lokalną. Ponadto dostępnych jest wiele lekkich wdrożeń agentów do uzyskania wyższej dostępności synchronizacji. [Dowiedz się więcej](https://aka.ms/cloudsyncGA).
 
---
### <a name="general-availability---attack-simulation-administrator-and-attack-payload-author-built-in-roles"></a>Ogólna dostępność — administrator symulacji ataku oraz wbudowane role autora autor

**Typ:** Nowa funkcja  
**Kategoria usługi:** RBAC  
**Możliwość produktu:** Access Control
 
Dwie nowe role w Role-Based Access Control są dostępne do przypisywania do użytkowników, osoby z uprawnieniami administratora i rozładunku ataku. 

Użytkownicy w roli [administratora symulacji ataku](../roles/permissions-reference.md#attack-simulation-administrator) mają dostęp do wszystkich symulacji w dzierżawie i mogą:
- Twórz wszystkie aspekty tworzenia symulacji ataków i zarządzaj nimi
- Uruchamianie/planowanie symulacji
-  Przejrzyj wyniki symulacji. 

Użytkownicy w ramach roli [autor ładunku atakujący](../roles/permissions-reference.md#attack-payload-author) mogą tworzyć ładunki ataków, ale nie są faktycznie uruchamiane ani zaplanowani. Ładunki ataków są następnie dostępne dla wszystkich administratorów w dzierżawie, którzy mogą ich używać do tworzenia symulacji.

---

### <a name="general-availability---usage-summary-reports-reader-built-in-role"></a>Rola wbudowana — zestawienie raportów podsumowania użycia

**Typ:** Nowa funkcja  
**Kategoria usługi:** RBAC  
**Możliwość produktu:** Access Control
 
Użytkownicy z rolą czytelnik raportów podsumowujących użycia mogą uzyskać dostęp do zagregowanych danych poziomu dzierżawy i skojarzonych z nimi szczegółowych informacji w centrum administracyjnym Microsoft 365 w celu użycia i oceny wydajności. Nie mogą jednak uzyskać dostępu do żadnych szczegółów na poziomie użytkownika ani szczegółowych informacji. 

W centrum administracyjnym Microsoft 365 dla dwóch raportów rozróżniamy zagregowane dane na poziomie dzierżawy i szczegóły na poziomie użytkownika. Ta rola dodaje dodatkową warstwę ochrony do poszczególnych użytkowników, których dane są identyfikowalne. [Dowiedz się więcej](../roles/permissions-reference.md#usage-summary-reports-reader).

---

### <a name="general-availability---require-app-protection-policy-grant-in-azure-ad-conditional-access"></a>Ogólna dostępność — Wymagaj przydzielenia zasad ochrony aplikacji w usłudze Azure AD dostęp warunkowy

**Typ:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Uprawnienie dostępu warunkowego usługi Azure AD dla "Wymagaj zasad ochrony aplikacji" ma teraz wartość GA. 

Zasady te zapewniają następujące możliwości:
- Zezwala na dostęp tylko w przypadku korzystania z aplikacji mobilnej, która obsługuje ochronę aplikacji usługi Intune
- Zezwala na dostęp tylko wtedy, gdy użytkownik ma zasady ochrony aplikacji usługi Intune dostarczone do aplikacji mobilnej

Dowiedz się więcej na temat sposobu konfigurowania zasad dostępu warunkowego dla ochrony aplikacji w [tym miejscu](../conditional-access/app-protection-based-conditional-access.md).
 
---

### <a name="general-availability---email-one-time-passcode"></a>Ogólna dostępność — kod dostępu One-Time E-mail

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C
 
Uwierzytelnianie OTP poczty e-mail pozwala organizacjom na całym świecie współpracować z innymi osobami, wysyłając link lub zaproszenie za pośrednictwem poczty e-mail. Zaproszeni użytkownicy mogą weryfikować swoją tożsamość za pomocą jednorazowego kodu dostępu wysyłanego do poczty e-mail w celu uzyskania dostępu do zasobów partnera. [Dowiedz się więcej](../external-identities/one-time-passcode.md). 
 
---

 ### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2021"></a>Nowe łączniki aprowizacji w galerii aplikacji usługi Azure AD — styczeń 2021

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwości produktu:** integracja innej firmy
 
Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:
- [Fortes Change Cloud](../saas-apps/fortes-change-cloud-provisioning-tutorial.md)
- [Gtmhub](../saas-apps/gtmhub-provisioning-tutorial.md)
- [monday.com](../saas-apps/mondaycom-provisioning-tutorial.md)
- [Splashtop](../saas-apps/splashtop-provisioning-tutorial.md)
- [Templafy OpenID Connect Connect](../saas-apps/templafy-openid-connect-provisioning-tutorial.md)
- [WEDO](../saas-apps/wedo-provisioning-tutorial.md)

Aby uzyskać więcej informacji, zobacz [co to jest zautomatyzowane Inicjowanie obsługi użytkowników aplikacji SaaS w usłudze Azure AD?](../app-provisioning/user-provisioning.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---january-2021"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — styczeń 2021

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W styczniu 2021 dodaliśmy następujące 29 nowych aplikacji w galerii aplikacji z obsługą Federacji:

[mySCView](https://dev.myscview.com/), [Talentech](https://talentech.com/contact/), [Bipsync](https://www.bipsync.com/), [OroTimesheet](https://app.orotimesheet.com/login.php), [Mio](https://app.m.io/auth/install/microsoft?scopetype=hub), [Sovelto Easy](https://login.soveltoeasy.fi/), [Supportbench](https://account.supportbench.net/agent/login/),[Bienvenuee](https://formation.bienvenue.pro/login), [AIDAa](https://aidaforparents.com/login/organizations), [Międzynarodowa pomoc techniczną sos](../saas-apps/international-sos-assistance-products-tutorial.md), [NAVEX jeden](../saas-apps/navex-one-tutorial.md), [LabLog](../saas-apps/lablog-tutorial.md), [Oktopost SAML](../saas-apps/oktopost-saml-tutorial.md), [EPHOTOa](../saas-apps/ephoto-dam-tutorial.md), [pojęcie](../saas-apps/notion-tutorial.md), [Syndio](../saas-apps/syndio-tutorial.md), [Yello Enterprise](../saas-apps/yello-enterprise-tutorial.md), [TimeClock 365 SAML](../saas-apps/timeclock-365-saml-tutorial.md), [Nalco E-Data](https://www.ecolab.com/), [](https://app.vacancy-filler.co.uk/VFMVC/Account/Login)Synerisee [, Imperva](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md) [Experience](../saas-apps/perimeter-81-tutorial.md) [,](../saas-apps/aruba-user-experience-insight-tutorial.md) [Illusive,](../saas-apps/imperva-data-security-tutorial.md) [sPlan,](../saas-apps/contentsquare-sso-tutorial.md) [,](../saas-apps/illusive-networks-tutorial.md),, [w](../saas-apps/proware-tutorial.md)wersji [](../saas-apps/splan-visitor-tutorial.md)81 [Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/burp-suite-enterprise-edition-tutorial)

W tym miejscu możesz również znaleźć dokumentację dotyczącą wszystkich aplikacji https://aka.ms/AppsTutorial

Aby wyświetlić listę aplikacji w galerii aplikacji usługi Azure AD, zapoznaj się ze szczegółowymi informacjami w tym miejscu https://aka.ms/AzureADAppRequest 

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Publiczna wersja zapoznawcza — Menedżer drugiego poziomu można ustawić jako alternatywną osobę zatwierdzającą

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie dostępem użytkowników  
**Możliwość produktu:** Zarządzanie prawami
 
Dodatkowa opcja w przypadku wybrania opcji osoby zatwierdzające jest teraz dostępna w usłudze zarządzania prawami. Jeśli wybierzesz opcję "Menedżer jako osoba zatwierdzająca" dla pierwszej osoby zatwierdzającej, będziesz mieć inną opcję "Menedżer drugiego poziomu jako alternatywny osoba zatwierdzająca", dostępną do wyboru w polu alternatywna osoba zatwierdzająca. W przypadku wybrania tej opcji należy dodać osobę zatwierdzającą rezerwę do przesyłania żądania do programu w przypadku, gdy system nie może odnaleźć Menedżera drugiego poziomu. [Dowiedz się więcej](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="general-availability---navigate-to-teams-directly-from-my-access-portal"></a>Ogólna dostępność — bezpośrednie nawigowanie do zespołów z portalu dostępu

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie dostępem użytkowników  
**Możliwość produktu:** Zarządzanie prawami
 
Teraz możesz uruchamiać zespoły bezpośrednio z portalu My Access. 

W tym celu zaloguj się do mojego dostępu ( https://myaccess.microsoft.com/) , przejdź do pozycji "pakiety dostępu", a następnie przejdź do karty "aktywne", aby wyświetlić wszystkie pakiety dostępu, do których masz już dostęp. Po rozwinięciu wybranego pakietu dostępu i umieszczeniu na nim wskaźnika myszy można uruchomić go, klikając przycisk "Otwórz". [Dowiedz się więcej](../governance/entitlement-management-request-access.md).
 
---

### <a name="improved-logging--end-user-prompts-for-risky-guest-users"></a>Ulepszone rejestrowanie & End-User monitowani o ryzykownych użytkowników-Gości

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 

Informacje o rejestrowaniu i End-Userniu dla ryzykownych użytkowników-Gości zostały zaktualizowane. Dowiedz się więcej na temat [ochrony tożsamości i użytkowników B2B](../identity-protection/concept-identity-protection-b2b.md).
 
---
 
## <a name="december-2020"></a>Grudzień 2020 r.

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>Publiczna wersja zapoznawcza — Azure AD B2C Rejestracja i logowanie za pomocą zasad wbudowanych

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C
 
B2C i logowanie za pomocą wbudowanych zasad pozwala administratorom IT i deweloperom organizacji zezwolić użytkownikom końcowym na logowanie się i rejestrowanie się przy użyciu numeru telefonu w przepływach użytkownika. Aby dowiedzieć się więcej, zapoznaj się z artykułem [Konfigurowanie rejestrowania i logowania w usłudze Flow dla przepływów użytkowników (wersja zapoznawcza)](../../active-directory-b2c/phone-authentication-user-flows.md) .

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>Ogólna dostępność — domyślnie włączone domyślnie dla wszystkich nowych dzierżawców

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Aby chronić konta użytkowników, wszystkie nowe dzierżawy utworzone w dniu lub po 12 listopada 2020 będą mieć włączone wartości domyślne zabezpieczeń. Domyślne ustawienia zabezpieczeń wymuszają wiele zasad, w tym:
- Wymaga, aby wszyscy użytkownicy i Administratorzy rejestrowali usługi MFA przy użyciu aplikacji Microsoft Authenticator
- Wymaga ról administratora krytycznego do korzystania z usługi MFA za każdym razem, gdy logują się. W razie potrzeby wszyscy inni użytkownicy będą monitowani o uwierzytelnianie MFA. 
- Starsze uwierzytelnianie będzie blokowane w całej dzierżawie. 

Aby uzyskać więcej informacji, zobacz [co to są ustawienia domyślne zabezpieczeń?](../fundamentals/concept-fundamentals-security-defaults.md)

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>Ogólna dostępność — Obsługa grup mających do 250 000 członków w AADConnect

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Połączenie AD  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
Firma Microsoft wdrożyła nowy punkt końcowy (API) dla Azure AD Connect, który poprawia wydajność operacji usługi synchronizacji do Azure Active Directory. Gdy korzystasz z nowego [punktu końcowego w wersji 2](../hybrid/how-to-connect-sync-endpoint-api-v2.md), zobaczysz zauważalny wzrost wydajności dotyczący eksportowania i importowania do usługi Azure AD. Ten nowy punkt końcowy obsługuje następujące scenariusze:

- Synchronizowanie grup z elementami członkowskimi do 250 000
- Wzrost wydajności dotyczący eksportowania i importowania do usługi Azure AD

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>Ogólna dostępność — Zarządzanie prawami dostępnymi dla dzierżawców w chmurze platformy Azure w Chinach

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie dostępem użytkowników  
**Możliwość produktu:** Zarządzanie prawami
 

Możliwości zarządzania uprawnieniami są teraz dostępne dla wszystkich dzierżawców w chmurze platformy Azure w Chinach. Aby uzyskać więcej informacji, odwiedź naszą witrynę z [dokumentacją dotyczącą zarządzania tożsamościami](https://docs.azure.cn/zh-cn/active-directory/governance/) .

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Nowe łączniki aprowizacji w galerii aplikacji usługi Azure AD — grudzień 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwości produktu:** integracja innej firmy

Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

- [Bizagi Studio for Digital Process Automation](../saas-apps/bizagi-studio-for-digital-process-automation-provisioning-tutorial.md)
- [CybSafe](../saas-apps/cybsafe-provisioning-tutorial.md)
- [GroupTalk](../saas-apps/grouptalk-provisioning-tutorial.md)
- [PaperCut Cloud Print Management](../saas-apps/papercut-cloud-print-management-provisioning-tutorial.md)
- [Parsable](../saas-apps/parsable-provisioning-tutorial.md)
- [Shopify Plus](../saas-apps/shopify-plus-provisioning-tutorial.md)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzacja aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — grudzień 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W grudniu 2020 dodaliśmy następujące 18 nowych aplikacji w galerii aplikacji z obsługą Federacji:

[AwareGo](../saas-apps/awarego-tutorial.md), [HowNow Logowanie jednokrotne](https://gethownow.com/), [ZyLAB jedno](https://www.zylab.com/en/product/legal-hold)Zarząd prawny, [poradnik](http://www.guider-ai.com/), [Softcrisis](https://www.softcrisis.se/sv/), [PIMS 365](http://www.omega365.com/pims), [InformaCast](../saas-apps/informacast-tutorial.md), [RetrieverMediaDatabase](../saas-apps/retrievermediadatabase-tutorial.md) [](../saas-apps/kfadvance-tutorial.md) , [Vonage](../saas-apps/vonage-tutorial.md), [Liczba mnie na pulpicie nawigacyjnym operacje](../saas-apps/count-me-in-operations-dashboard-tutorial.md), [ProProfs Knowledge Base](../saas-apps/proprofs-knowledge-base-tutorial.md), RightCrowd [Management](../saas-apps/rightcrowd-workforce-management-tutorial.md) [, JLL](../saas-apps/equinix-federation-app-tutorial.md) [TRIRIGA](../saas-apps/jll-tririga-tutorial.md), [Shutterstock](../saas-apps/shutterstock-tutorial.md), [FortiWeb](../saas-apps/linkedin-talent-solutions-tutorial.md) [](../saas-apps/linkedin-talent-solutions-tutorial.md)

W tym miejscu możesz również znaleźć dokumentację dotyczącą wszystkich aplikacji https://aka.ms/AppsTutorial

Aby wyświetlić listę aplikacji w galerii aplikacji usługi Azure AD, zapoznaj się ze szczegółowymi informacjami w tym miejscu https://aka.ms/AzureADAppRequest

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>Przejdź do zespołów bezpośrednio z portalu dostępu

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** **Możliwości produktu** zarządzanie dostępem użytkowników: Zarządzanie prawami

Teraz możesz uruchamiać zespoły bezpośrednio z portalu dostępu. Aby to zrobić, zaloguj się do [mojego dostępu](https://myaccess.microsoft.com/), przejdź do pozycji **dostęp do pakietów**, a następnie przejdź do karty **aktywne** , aby wyświetlić wszystkie pakiety dostępu, do których masz już dostęp. Po rozwinięciu pakietu dostępu i umieszczeniu go na zespołach można uruchomić go, klikając przycisk **Otwórz** . 

Aby dowiedzieć się więcej na temat korzystania z portalu dostępu, przejdź do strony [żądania dostępu do pakietu dostępu w usłudze Azure AD uprawnienia do zarządzania](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal).

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Publiczna wersja zapoznawcza — Menedżer drugiego poziomu można ustawić jako alternatywną osobę zatwierdzającą

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie dostępem użytkowników  
**Możliwość produktu:** Zarządzanie prawami

Dodatkowa opcja jest teraz dostępna w procesie zatwierdzania w usłudze zarządzania uprawnieniami. Jeśli wybierzesz pozycję Menedżer jako osoba zatwierdzająca dla pierwszej osoby zatwierdzającej, będziesz mieć kolejną opcję, drugi poziom jako osobę zatwierdzającą, dostępną do wyboru w polu alternatywna osoba zatwierdzająca. Po wybraniu tej opcji należy dodać osobę zatwierdzającą rezerwę do przesyłania żądania do programu w przypadku, gdy system nie może odnaleźć Menedżera drugiego poziomu.

Aby uzyskać więcej informacji, przejdź do [pozycji Zmień ustawienia zatwierdzania dla pakietu dostępu w usłudze Azure AD uprawnienia zarządzania](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).

--- 

## <a name="november-2020"></a>Listopad 2020 r.

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Azure Active Directory TLS 1,0, TLS 1,1 i zaniechania algorytmu 3DES

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Wszystkie aplikacje usługi Azure AD  
**Możliwość produktu:** Norm

Azure Active Directory spowoduje wycofanie następujących protokołów w Azure Active Directory regionach ogólnoświatowych do 30 czerwca 2021:

- TLS 1.0
- TLS 1.1
- mechanizm szyfrowania 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Narażone są następujące środowiska:
- Chmura komercyjna Azure
- Office 365 w zatoce i WW

Powiązane anonse wszystkie kombinacje klient-serwer i przeglądarka-serwer powinny używać protokołu TLS 1,2 i nowoczesnych mechanizmów szyfrowania, aby zachować bezpieczne połączenie z Azure Active Directory dla platformy Azure, pakietu Office 365 i usług Microsoft 365. Jest to zmiana związana z [Azure Active Directory TLS 1,0 & 1,1 oraz zaniechania pakietu szyfrowania 3DES w US gov chmurze](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — listopad 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W listopadzie 2020 dodaliśmy 52 następujące nowe aplikacje w galerii aplikacji z obsługą Federacji:

[Podróże & zarządzanie kosztami](https://app.expenseonce.com/Account/Login), [Tribeloo](../saas-apps/tribeloo-tutorial.md), [Itslearning wybór plików](https://pmteam.itslearning.com/), [Kontrola kryzysowa](../saas-apps/crises-control-tutorial.md), [CourtAlert](https://www.courtalert.com/), [StealthMail](https://stealthmail.com/), [wyspa Edmentum-badawcza](https://app.studyisland.com/cfw/login/), [Menedżer ryzyka wirtualnego](../saas-apps/virtual-risk-manager-tutorial.md), [timu](../saas-apps/timu-tutorial.md), [analityka analityczna, platforma](../saas-apps/looker-analytics-platform-tutorial.md) [](https://gradle.com/) [Talview-Rekrutacja](https://recruit.talview.com/login), translator czasu [](../saas-apps/hownow-webapp-sso-tutorial.md) [rzeczywistego,](../saas-apps/appian-tutorial.md) [Klaxoon](https://access.klaxoon.com/login), [podbean](../saas-apps/podbean-tutorial.md), [zcal](https://zcal.co/signup), [](https://cloud.relations-crm.com/) [](https://portal.brightbooking.eu/) [](../saas-apps/panorays-tutorial.md) [](../saas-apps/coupa-risk-assess-tutorial.md) [](https://portal.builterra.com/) [wydatekmanager](https://www.edriving.com/), platforma platformy [, środowisko](../saas-apps/netsparker-enterprise-tutorial.md) [uruchomieniowe EN-trak,](https://portal.en-trak.app/)( [wszystkie produkty),](../saas-apps/lucid-tutorial.md) [Appian, Panorays](https://my.evacheckin.com/organization) [Builterra,](../saas-apps/sailpoint-identitynow-tutorial.md)Resource[Central,](../saas-apps/resource-central-tutorial.md)UiPathStudioO365App [,](https://www.uipath.com/product/platform)Jedox [,](../saas-apps/jedox-tutorial.md)Cequence [Application Security,](../saas-apps/cequence-application-security-tutorial.md)PerimeterX [,](../saas-apps/perimeterx-tutorial.md)TrendMiner [,](../saas-apps/trendminer-tutorial.md)LEXION [,](../saas-apps/lexion-tutorial.md) [Working](../saas-apps/workware-tutorial.md) [, ProdPad, AWS](../saas-apps/prodpad-tutorial.md) [ClientVPN,](../saas-apps/aws-clientvpn-tutorial.md)AppSec [Flow — SSO,](../saas-apps/appsec-flow-sso-tutorial.md)Luum [,](../saas-apps/luum-tutorial.md)transport [,](https://www.gpcsl.com/freight.html)Terraform Cloud [,](../saas-apps/terraform-cloud-tutorial.md)natura [Research,](../saas-apps/nature-research-tutorial.md)Play [Signing](https://login.playsignage.com/login) [, RemotePC, Prolorus,](../saas-apps/remotepc-tutorial.md) [, Hirebridge](../saas-apps/hirebridge-ats-tutorial.md) [ATS](https://www.teamgage.com/Account/ExternalLoginAzure) [, Teamgage®](../saas-apps/roadmunk-tutorial.md) [](https://api.expense-manager.com/) [](../saas-apps/prolorus-tutorial.md) [](../saas-apps/procaire-tutorial.md)

W tym miejscu możesz również znaleźć dokumentację dotyczącą wszystkich aplikacji https://aka.ms/AppsTutorial

Aby wyświetlić listę aplikacji w galerii aplikacji usługi Azure AD, zapoznaj się ze szczegółowymi informacjami w tym miejscu https://aka.ms/AzureADAppRequest

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>Publiczna wersja zapoznawcza — role niestandardowe dla aplikacji dla przedsiębiorstw

**Typ:** Nowa funkcja  
**Kategoria usługi:** RBAC  
**Możliwość produktu:** Access Control
 
 [Niestandardowe role RBAC na potrzeby delegowania zarządzania aplikacjami przedsiębiorstwa](../roles/custom-available-permissions.md) są teraz dostępne w publicznej wersji zapoznawczej. Te nowe uprawnienia są kompilowane na rolach niestandardowych na potrzeby zarządzania rejestracją aplikacji, co umożliwia precyzyjne sterowanie dostępem administratorów. Z upływem czasu zostaną wydane dodatkowe uprawnienia do delegowania zarządzania usługą Azure AD.

Niektóre typowe scenariusze delegowania:
- przypisanie użytkowników i grup, które mogą uzyskiwać dostęp do aplikacji logowania jednokrotnego opartego na protokole SAML
- Tworzenie aplikacji w galerii usługi Azure AD
- aktualizowanie i odczytywanie podstawowych konfiguracji języka SAML dla aplikacji logowania jednokrotnego opartego na protokole SAML
- Zarządzanie certyfikatami podpisywania dla aplikacji logowania jednokrotnego opartego na protokole SAML
- Aktualizacja wygasłych certyfikatów logowania adresy e-mail dla aplikacji logowania jednokrotnego opartego na protokole SAML
- Aktualizacja sygnatury tokenu języka SAML i algorytmu logowania dla aplikacji logowania jednokrotnego opartego na protokole SAML
- Tworzenie, usuwanie i aktualizowanie atrybutów użytkownika i oświadczeń dla aplikacji logowania jednokrotnego opartego na protokole SAML
- możliwość włączania, wyłączania i ponownego uruchamiania zadań aprowizacji
- aktualizacje mapowania atrybutów
- możliwość odczytu ustawień aprowizacji skojarzonych z obiektem
- możliwość odczytywania ustawień aprowizacji skojarzonych z jednostką usługi
- możliwość autoryzacji dostępu do aplikacji w celu aprowizacji

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>Publiczna wersja zapoznawcza — usługa Azure serwer proxy aplikacji usługi Azure AD natywnie obsługuje dostęp za pomocą logowania jednokrotnego do aplikacji, które używają nagłówków do uwierzytelniania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control
 
Serwer proxy aplikacji usługi Azure Active Directory (Azure AD) natywnie obsługuje dostęp za pomocą logowania jednokrotnego do aplikacji, które używają nagłówków do uwierzytelniania. Możesz skonfigurować wartości nagłówka wymagane przez aplikację w usłudze Azure AD. Wartości nagłówka zostaną wysłane do aplikacji za pośrednictwem serwera proxy aplikacji. Aby dowiedzieć się więcej, zobacz [Logowanie jednokrotne oparte na nagłówkach dla aplikacji lokalnych przy użyciu aplikacja usługi Azure AD proxy](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>Ogólna dostępność — Azure AD B2C Rejestracja i logowanie za pomocą zasad niestandardowych

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C

Dzięki rejestrowaniu i rejestrowaniu numerów telefonów deweloperzy i przedsiębiorstwa mogą umożliwić klientom rejestrowanie się i logowanie przy użyciu hasła jednorazowego wysyłanego do numeru telefonu użytkownika za pośrednictwem wiadomości SMS. Ta funkcja umożliwia także zmianę numeru telefonu przez klienta w przypadku utraty dostępu do telefonu. Korzystając z możliwości zasad niestandardowych, zezwól programistom i przedsiębiorstwom na komunikowanie się z nimi za pomocą dostosowywania strony. Dowiedz się [, jak skonfigurować rejestrowanie i logowanie za pomocą zasad niestandardowych w Azure AD B2C](../../active-directory-b2c/phone-authentication.md).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Nowe łączniki aprowizacji w galerii aplikacji usługi Azure AD — listopad 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwości produktu:** integracja innej firmy
 
Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

- [Zarządzanie tożsamościami Adobe](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blog](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Contentful](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [Kółko i krzyżyk-TAC Mobile](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

Aby uzyskać więcej informacji, zobacz [Automatyzowanie aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>Publiczna wersja zapoznawcza — Sign-In E-mail z możliwością wdrażania w ramach przygotowania etapowego

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Administratorzy dzierżawy mogą teraz używać wdrożenia etapowego do wdrażania Sign-In poczty E-mail przy użyciu usługi ProxyAddresses w określonych grupach usługi Azure AD. Może to pomóc podczas wypróbowania funkcji przed wdrożeniem jej w całej dzierżawie za pośrednictwem zasad odnajdywania obszaru głównego. Instrukcje dotyczące wdrażania Sign-In poczty E-mail przy użyciu usługi ProxyAddresses w ramach wdrożenia etapowego znajdują się w [dokumentacji](../authentication/howto-authentication-use-email-signin.md).
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>Ograniczona wersja zapoznawcza — Diagnostyka logowania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania
 
W początkowej wersji zapoznawczej diagnostyki logowania Administratorzy mogą teraz przeglądać logowania użytkowników. Administratorzy mogą otrzymywać kontekstowe, specyficzne i szczegółowe informacje oraz wskazówki dotyczące tego, co się stało podczas logowania i rozwiązywania problemów. Diagnostyka jest dostępna zarówno na poziomie usługi Azure AD, jak i w obszarze diagnozowanie i rozwiązywanie dostępu warunkowego. Scenariusze diagnostyczne omówione w tej wersji są dostęp warunkowy, Multi-Factor Authentication i logowania jednokrotnego.

Aby uzyskać więcej informacji, zobacz [co to jest Diagnostyka logowania w usłudze Azure AD?](../reports-monitoring/overview-sign-in-diagnostics.md).
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>Ulepszone nieznane właściwości logowania

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

  Nieznane wykrycia właściwości logowania zostały zaktualizowane. Klienci mogą zauważyć więcej nieznanych wykrywania właściwości logowania o wysokim ryzyku. Aby uzyskać więcej informacji, zobacz [co to jest ryzyko?](../identity-protection/concept-identity-protection-risks.md)
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>Udostępniono już publiczną wersję zapoznawczą agenta aprowizacji w chmurze (wersja: 1.1.281.0)

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Inicjowanie obsługi chmury usługi Azure AD  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
Agent aprowizacji w chmurze został opublikowany w publicznej wersji zapoznawczej i jest teraz dostępny w portalu. Ta wersja zawiera kilka ulepszeń, w tym obsługę GMSA dla domen, która zapewnia lepsze zabezpieczenia, ulepszone cykle początkowej synchronizacji i obsługę dużych grup. Aby uzyskać więcej informacji, zapoznaj się z [historią](../app-provisioning/provisioning-agent-release-version-history.md) wersji wydania. 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>Punkt końcowy interfejsu API odzyskiwania funkcji BitLocker w obszarze/informationProtection

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie dostępem do urządzeń  
**Możliwość produktu:** Zarządzanie cyklem życia urządzeń
 
Wcześniej można było odzyskać klucze funkcji BitLocker za pośrednictwem punktu końcowego/BitLocker. Ostatecznie wycofamy ten punkt końcowy, a klienci powinni zacząć korzystać z interfejsu API, który teraz znajduje się w obszarze/informationProtection. 

Aby odzwierciedlić te zmiany, zobacz [interfejs API odzyskiwania funkcji BitLocker](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) dla aktualizacji dokumentacji.

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>Ogólna dostępność obsługi serwera proxy aplikacji dla klienta sieci Web Usługi pulpitu zdalnego HTML5

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control
 
Usługa Azure serwer proxy aplikacji usługi Azure AD support dla Usługi pulpitu zdalnego (RDS) klienta sieci Web jest teraz ogólnie dostępna. Klient sieci Web usług pulpitu zdalnego umożliwia użytkownikom dostęp do infrastruktury Pulpit zdalny za pomocą dowolnej przeglądarki z obsługą HTLM5, takiej jak Microsoft Edge, Internet Explorer 11, Google Chrome itd. Użytkownicy mogą korzystać z aplikacji zdalnych lub pulpitów, takich jak w przypadku urządzeń lokalnych, z dowolnego miejsca. 

Korzystając z usługi Azure serwer proxy aplikacji usługi Azure AD, można zwiększyć bezpieczeństwo wdrożenia usług pulpitu zdalnego, wymuszając uwierzytelnianie wstępne i zasady dostępu warunkowego dla wszystkich typów rozbudowanych aplikacji klienckich. Aby dowiedzieć się więcej, zobacz temat [publikowanie pulpit zdalny przy użyciu platformy Azure serwer proxy aplikacji usługi Azure AD](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>Nowa ulepszona usługa grupy dynamicznej jest w publicznej wersji zapoznawczej

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności
 
Rozszerzona usługa grupy dynamicznej jest teraz w publicznej wersji zapoznawczej. Nowi klienci, którzy tworzą grupy dynamiczne w swoich dzierżawcach, będą korzystać z nowej usługi. Czas wymagany do utworzenia grupy dynamicznej będzie proporcjonalny do rozmiaru tworzonej grupy, a nie od rozmiaru dzierżawy. Ta aktualizacja poprawi wydajność dużych dzierżawców znacznie znacząco podczas tworzenia mniejszych grup przez klientów. 

Nowa usługa ma również na celu zakończenie dodawania i usuwania członków z powodu zmian atrybutów w ciągu kilku minut. Ponadto pojedyncze błędy przetwarzania nie blokują przetwarzania dzierżawcy. Aby dowiedzieć się więcej na temat tworzenia grup dynamicznych, zapoznaj się z naszą [dokumentacją](../enterprise-users/groups-create-rule.md).
 
---
## <a name="october-2020"></a>Październik 2020 r.

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Lokalne agenci Hybrydowi usługi Azure AD, na które wpływają zmiany certyfikatu protokołu TLS platformy Azure

**Typ:** Planowanie zmiany  
**Kategoria usługi:** NIE DOTYCZY  
**Możliwość produktu:** Platformach

Firma Microsoft aktualizuje usługi platformy Azure, aby używać certyfikatów TLS z innego zestawu głównych urzędów certyfikacji (CA). Ta aktualizacja jest spowodowana przez bieżące certyfikaty urzędu certyfikacji, które nie są zgodne z jednym z wymagań linii bazowej urzędu certyfikacji/przeglądarki. Ta zmiana wpłynie na agentów hybrydowych usługi Azure AD zainstalowanych lokalnie, które mają zaostrzone środowiska ze stałą listą certyfikatów głównych i będą musiały zostać zaktualizowane, aby ufać nowym WYSTAWCOM certyfikatów.

Ta zmiana spowoduje zakłócenie działania usługi, jeśli nie zostanie ona natychmiast podjęta. Tacy agenci obejmują [Łączniki serwera proxy aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) dla dostępu zdalnego do lokalnych, [przekazujących agentów uwierzytelniania](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) , którzy umożliwiają użytkownikom logowanie się do aplikacji przy użyciu tych samych haseł, a także agentów usługi [Cloud Provisioning w wersji zapoznawczej](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) do usługi Azure AD Sync. 

Jeśli masz środowisko z regułami zapory ustawioną tak, aby zezwalać na połączenia wychodzące tylko do pobierania listy odwołania certyfikatów (CRL), musisz zezwolić na następujące listy CRL i adresy URL protokołu OCSP. Aby uzyskać szczegółowe informacje dotyczące zmiany i listy CRL oraz adresów URL protokołu OCSP, aby umożliwić dostęp do programu, zobacz  [zmiany certyfikatu protokołu TLS platformy Azure](../../security/fundamentals/tls-certificate-changes.md).

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>Zdarzenia aprowizacji zostaną usunięte z dzienników inspekcji i opublikowane wyłącznie w dziennikach aprowizacji

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania
 
Działanie [usługi Standard scim aprowizacji](../app-provisioning/user-provisioning.md) jest rejestrowane zarówno w dziennikach inspekcji, jak i w dziennikach aprowizacji. Obejmuje to działania, takie jak tworzenie użytkownika w usługi ServiceNow, Group in GSuite lub importuje rolę z AWS. W przyszłości te zdarzenia będą publikowane tylko w dziennikach aprowizacji. Ta zmiana jest implementowana w celu uniknięcia zduplikowanych zdarzeń w dziennikach i dodatkowych kosztów ponoszonych przez klientów korzystających z dzienników w usłudze log Analytics. 

Udostępnimy aktualizację, gdy data zostanie zakończona. To wycofanie nie jest planowane dla roku kalendarzowego 2020. 

> [!NOTE]
> Nie ma to wpływu na wszystkie zdarzenia w dziennikach inspekcji poza zdarzeniami synchronizacji emitowanymi przez usługę aprowizacji. Zdarzenia, takie jak tworzenie aplikacji, zasady dostępu warunkowego, użytkownik w katalogu itp., będą nadal emitowane w dziennikach inspekcji. [Dowiedz się więcej](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context).
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Lokalne agenci Hybrydowi usługi Azure AD wpływ na zmiany certyfikatu usługi Azure Transport Layer Security (TLS)

**Typ:** Planowanie zmiany  
**Kategoria usługi:** NIE DOTYCZY  
**Możliwość produktu:** Platformach
 
Firma Microsoft aktualizuje usługi platformy Azure, aby używać certyfikatów TLS z innego zestawu głównych urzędów certyfikacji (CA). Istnieje aktualizacja z powodu bieżących certyfikatów urzędu certyfikacji, która nie jest zgodna z wymaganiami linii bazowej urzędu certyfikacji/przeglądarki. Ta zmiana wpłynie na agentów hybrydowych usługi Azure AD zainstalowanych lokalnie, które mają zaostrzone środowiska ze stałą listą certyfikatów głównych. Te agenci należy zaktualizować, aby ufać nowym WYSTAWCOM certyfikatów.

Ta zmiana spowoduje zakłócenie działania usługi, jeśli nie zostanie ona natychmiast podjęta. Te agenci obejmują: 
- [Łączniki serwera proxy aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) dla dostępu zdalnego do lokalnego 
- [Przekazywanie agentów uwierzytelniania](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) umożliwiających użytkownikom logowanie się do aplikacji przy użyciu tych samych haseł
- Agenci usługi [Cloud Provisioning w wersji zapoznawczej](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) , którzy wykonują synchronizację z usługą Azure AD. 

Jeśli masz środowisko z regułami zapory ustawioną tak, aby zezwalać na połączenia wychodzące tylko do pobierania listy odwołania certyfikatów (CRL), musisz zezwolić na listę CRL i adresy URL protokołu OCSP. Aby uzyskać szczegółowe informacje dotyczące zmiany i listy CRL oraz adresów URL protokołu OCSP, aby umożliwić dostęp do programu, zobacz  [zmiany certyfikatu protokołu TLS platformy Azure](../../security/fundamentals/tls-certificate-changes.md).
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>Azure Active Directory TLS 1,0, TLS 1,1 i zaniechania algorytmu 3DES w chmurze US Gov

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Wszystkie aplikacje usługi Azure AD  
**Możliwość produktu:** Norm
 
Azure Active Directory spowoduje wycofanie następujących protokołów do 31 marca 2021:
- TLS 1.0
- TLS 1.1
- mechanizm szyfrowania 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Wszystkie kombinacje klient-serwer i przeglądarka-serwer powinny korzystać z protokołu TLS 1,2 i nowoczesnych mechanizmów szyfrowania w celu zapewnienia bezpiecznego połączenia z Azure Active Directoryami dla usług Azure, Office 365 i Microsoft 365.

Narażone są następujące środowiska:
- US Gov platformy Azure
- [Office 365 w zatoce High & DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)
 
---

### <a name="assign-applications-to-roles-on-au-and-object-scope"></a>Przypisywanie aplikacji do ról w ramach funkcji automatyczne i zakres obiektów

**Typ:** Nowa funkcja  
**Kategoria usługi:** RBAC  
**Możliwość produktu:** Access Control
 
Ta funkcja umożliwia przypisanie aplikacji (SPN) do roli administratora w zakresie jednostki administracyjnej. Aby dowiedzieć się więcej, zapoznaj się z tematem [Przypisywanie ról objętych zakresem do jednostki administracyjnej](../roles/admin-units-assign-roles.md).

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>Teraz można wyłączyć i usunąć użytkowników-Gości po odmowie dostępu do zasobu

**Typ:** Nowa funkcja  
**Kategoria usługi:** Przeglądy dostępu  
**Możliwość produktu:** Zarządzanie tożsamościami
 
Wyłączenie i usunięcie to zaawansowana kontrolka w przeglądach dostępu usługi Azure AD, która ułatwia organizacjom zarządzanie zewnętrznymi Gośćmi w grupach i aplikacjach. Jeśli Goście są odrzucani w przeglądzie dostępu, **disable i DELETE** będą automatycznie blokować Logowanie przez 30 dni. Po upływie 30 dni zostanie ono całkowicie usunięte z dzierżawy.

Aby uzyskać więcej informacji na temat tej funkcji, zobacz temat [wyłączanie i usuwanie tożsamości zewnętrznych przy użyciu przeglądów dostępu do usługi Azure AD (wersja zapoznawcza)](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews-preview).
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>Twórcy przeglądów dostępu mogą dodawać niestandardowe wiadomości e-mail do recenzentów

**Typ:** Nowa funkcja  
**Kategoria usługi:** Przeglądy dostępu  
**Możliwość produktu:** Zarządzanie tożsamościami
 
W przeglądach dostępu usługi Azure AD Administratorzy tworzący przeglądy mogą teraz napisać niestandardowy komunikat do recenzentów. Recenzenci będą widzieć komunikat w wiadomościach e-mail, które otrzymają monit o ukończenie przeglądu. Aby dowiedzieć się więcej na temat korzystania z tej funkcji, zobacz Krok 14 w sekcji [Tworzenie jednej lub więcej przeglądów dostępu](../governance/create-access-review.md#create-one-or-more-access-reviews) .

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Nowe łączniki aprowizacji w galerii aplikacji usługi Azure AD — październik 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwości produktu:** integracja innej firmy
 
Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [Global Relay Identity Sync](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzacja aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Asystent Integration dla Azure AD B2C

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C
 
Środowisko Integration Assistant (wersja zapoznawcza) jest teraz dostępne dla Azure AD B2C Rejestracje aplikacji. To środowisko pomaga w konfigurowaniu aplikacji dla typowych scenariuszy. Dowiedz się więcej o [najlepszych rozwiązaniach i zaleceniach dotyczących platformy tożsamości firmy Microsoft](../develop/identity-platform-integration-checklist.md).
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Wyświetl identyfikator szablonu roli w interfejsie użytkownika Azure Portal

**Typ:** Nowa funkcja  
**Kategoria usługi:** Role platformy Azure  
**Możliwość produktu:** Access Control
 

Teraz można wyświetlić identyfikator szablonu każdej roli usługi Azure AD w Azure Portal. W usłudze Azure AD wybierz pozycję  **Opis** wybranej roli. 

Zaleca się, aby klienci używali identyfikatorów szablonów ról w skryptach i kodzie programu PowerShell zamiast nazwy wyświetlanej. Identyfikator szablonu roli jest obsługiwany dla obiektów [directoryRoles](/graph/api/resources/directoryrole) i [definicji](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta) . Aby uzyskać więcej informacji na temat identyfikatorów szablonów ról, zobacz [identyfikatory szablonów ról](../roles/permissions-reference.md#role-template-ids).

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>Łączniki interfejsu API dla przepływów użytkowników rejestracji Azure AD B2C są teraz w publicznej wersji zapoznawczej

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C
 

Łączniki interfejsu API są teraz dostępne do użycia z Azure Active Directory B2C. Łączniki interfejsu API umożliwiają korzystanie z interfejsów API sieci Web w celu dostosowania przepływów użytkowników do rejestracji i integracji z zewnętrznymi systemami w chmurze. Łączników interfejsu API można używać do:

- Integracja z niestandardowymi przepływami pracy zatwierdzania
- Weryfikowanie danych wejściowych użytkownika
- Zastąp atrybuty użytkownika 
- Uruchom niestandardową logikę biznesową 

 Aby dowiedzieć się więcej, odwiedź stronę [Używanie łączników interfejsu API w celu dostosowania i rozbudowania](../../active-directory-b2c/api-connectors-overview.md) dokumentacji dotyczącej rejestracji.

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>Właściwość State dla połączonych organizacji w zarządzaniu prawami

**Typ:** Nowa funkcja  
**Kategoria usługi:** **Możliwości produktu** zarządzanie katalogami: Zarządzanie prawami
 

 Wszystkie połączone organizacje mają teraz dodatkową właściwość o nazwie "State". Stan kontroluje sposób używania połączonej organizacji w zasadach odwołujących się do "wszystkich skonfigurowanych połączonych organizacji". Wartość będzie równa "skonfigurowany" (oznacza to, że organizacja znajduje się w zakresie zasad, które używają klauzuli "All") lub "proponowane" (oznacza to, że organizacja nie znajduje się w zakresie).  

Ręcznie utworzone połączone organizacje będą mieć domyślne ustawienie "skonfigurowane". W tym czasie automatycznie utworzone przez program (utworzone za pomocą zasad zezwalających każdemu użytkownikowi z Internetu na żądanie dostępu) będą domyślnie "proponowane".  Wszystkie połączone organizacje utworzone przed Wrzesień 9 2020 zostaną ustawione na "skonfigurowane". Administratorzy mogą zaktualizować tę właściwość zgodnie z wymaganiami. [Dowiedz się więcej](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically).
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory tożsamości zewnętrzne mają teraz zaawansowane ustawienia zabezpieczeń Premium dla B2C

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C
 
Dostęp warunkowy oparty na ryzyku i funkcje wykrywania ryzyka programu Identity Protection są teraz dostępne w [Azure AD B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md). Dzięki tym zaawansowanym funkcjom zabezpieczeń klienci mogą teraz:
- Korzystaj z inteligentnego wglądu w dane, aby ocenić ryzyko związane z aplikacjami B2C i kontami użytkowników końcowych. Wykrycia obejmują nietypowe podróże, anonimowe adresy IP, adresy IP połączone przez złośliwe oprogramowanie oraz analizę zagrożeń usługi Azure AD. Dostępne są również raporty portalu i interfejsu API.
- Automatyczne rozwiązywanie zagrożeń przez skonfigurowanie adaptacyjnych zasad uwierzytelniania dla użytkowników B2C. Deweloperzy i Administratorzy aplikacji mogą ograniczyć ryzyko związane z ryzykiem w czasie rzeczywistym, wymagając uwierzytelniania wieloskładnikowego (MFA) lub blokując dostęp w zależności od wykrytego poziomu ryzyka użytkownika, z dodatkowymi kontrolkami dostępnymi na podstawie lokalizacji, grupy i aplikacji.
- Integracja z Azure AD B2C przepływami użytkowników i zasadami niestandardowymi. Warunki mogą być wyzwalane przez wbudowane przepływy użytkowników w Azure AD B2C lub mogą być włączone do zasad niestandardowych B2C. Podobnie jak w przypadku innych aspektów przepływu użytkownika programu B2C można dostosować obsługę komunikatów użytkownika końcowego. Dostosowanie jest uzależnione od rodzaju głosu, marki i środków zaradczych w organizacji.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — październik 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W październiku 2020 dodaliśmy następujące 27 nowych aplikacji w galerii aplikacji z obsługą Federacji:

[Sentry](../saas-apps/sentry-tutorial.md), [Bumblebee — produktywność superapp](https://app.yellowmessenger.com/user/login), [ABBYY FlexiCapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md), [EAComposer](../saas-apps/eacomposer-tutorial.md), integracja z [chmurą w chmurze dla platformy Azure](https://apps.mypurecloud.com/msteams-integration/), [Portal technologii strefy](https://portail.zonetechnologie.com/signin), [Genesys](../saas-apps/beautiful.ai-tutorial.md), [Beautiful.AI Access Broker](https://console.datawiza.com/), [Datawiza](https://app.zokri.com/), [ZOKRI](../saas-apps/checkproof-tutorial.md), [CheckProof](https://events.ecochallenge.org/users/login), [ecoChallenge.org](http://atspoke.com/login), spotkanie z [terminem](https://app.appointmentreminder.co.nz/account/login), [chmura. rynek](https://cloud.market/), [atSpoke](../saas-apps/travelperk-tutorial.md) [](../saas-apps/nimbus-tutorial.md), TravelPerk, [](../saas-apps/loop-flow-crm-tutorial.md)logowanie [](https://hrm.workstem.com/login) [](https://app.greetly.com/) [](../saas-apps/samsara-tutorial.md) [jednokrotne](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md) [Traffic Manager,](../saas-apps/web-cargo-air-tutorial.md) [OrgVitality,](../saas-apps/retail-zipline-tutorial.md) [Starmind,](../saas-apps/hoxhunt-tutorial.md) [Workstem](../saas-apps/mevisio-tutorial.md) [, zipline,](../saas-apps/starmind-tutorial.md)Hoxhunt [](../saas-apps/orgvitality-sso-tutorial.md)

W tym miejscu możesz również znaleźć dokumentację dotyczącą wszystkich aplikacji https://aka.ms/AppsTutorial

Aby wyświetlić listę aplikacji w galerii aplikacji usługi Azure AD, zapoznaj się ze szczegółowymi informacjami w tym miejscu https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>Dzienniki aprowizacji można teraz przesyłać strumieniowo do usługi log Analytics

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania
 

Opublikuj dzienniki aprowizacji w usłudze log Analytics, aby:
- Przechowywanie dzienników aprowizacji przez ponad 30 dni
- Definiowanie alertów niestandardowych i powiadomień
- Tworzenie pulpitów nawigacyjnych w celu wizualizacji dzienników
- Wykonywanie złożonych zapytań w celu przeanalizowania dzienników 

Aby dowiedzieć się, jak korzystać z tej funkcji, zobacz temat [Omówienie integracji aprowizacji z dziennikami Azure monitor](../app-provisioning/application-provisioning-log-analytics.md).
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>Dzienniki aprowizacji mogą być teraz wyświetlane przez właścicieli aplikacji

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania
 
Teraz można zezwolić właścicielom aplikacji na monitorowanie aktywności przez usługę aprowizacji i rozwiązywanie problemów bez podawania im uprzywilejowanych ról lub zwiększania wąskich gardeł. [Dowiedz się więcej](../reports-monitoring/concept-provisioning-logs.md).
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>Zmiana nazw ról Azure Active Directory

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Role platformy Azure  
**Możliwość produktu:** Access Control
 
Niektóre wbudowane role Azure Active Directory (AD) mają nazwy, które różnią się od tych, które są wyświetlane w Microsoft 365 centrum administracyjnym, portalu usługi Azure AD i Microsoft Graph. Niespójność może powodować problemy w zautomatyzowanych procesach. W przypadku tej aktualizacji zmieniamy nazwy 10 ról, aby były spójne. W poniższej tabeli znajdują się nowe nazwy ról:

![Tabela nowych nazw ról](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>Azure AD B2C obsługa przepływu kodu uwierzytelniania dla aplikacji jednostronicowych przy użyciu MSAL JS 2. x

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C
 
MSAL.js wersja 2. x obejmuje teraz obsługę przepływu kodu autoryzacji dla jednostronicowych aplikacji sieci Web (aplikacji jednostronicowych). Azure AD B2C będzie teraz obsługiwał użycie typu aplikacji SPA na Azure Portal i użycie przepływu kodu autoryzacji MSAL.js z PKCE dla aplikacji jednostronicowych. Pozwoli to aplikacji jednostronicowych przy użyciu Azure AD B2C do obsługi logowania jednokrotnego w nowszych przeglądarkach i przestrzegania nowszych zaleceń dotyczących protokołu uwierzytelniania. Rozpocznij pracę z [zarejestrowaniem aplikacji jednostronicowej (Spa) w](../../active-directory-b2c/tutorial-register-spa.md) samouczku Azure Active Directory B2C.

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>Aktualizacje do zapamiętania Multi-Factor Authentication (MFA) w ustawieniu zaufanego urządzenia

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Funkcja  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 

Niedawno Zaktualizowaliśmy [zapamiętanie Multi-Factor Authentication (MFA)](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) na funkcji zaufanego urządzenia, aby zwiększyć uwierzytelnianie przez maksymalnie 365 dni. Licencje na usługi Azure Active Directory (Azure AD) Premium mogą również używać [zasad częstotliwości logowania dostępu warunkowego](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) , które zapewniają większą elastyczność ustawień ponownego uwierzytelniania.

Aby zapewnić optymalne środowisko użytkownika, zalecamy użycie częstotliwości logowania dostępu warunkowego w celu przeciągnięcia okresów istnienia sesji na zaufanych urządzeniach, lokalizacjach lub sesjach niskiego ryzyka jako alternatywę dla opcji Zapamiętaj uwierzytelnianie wieloskładnikowe w przypadku ustawienia zaufanego urządzenia. Aby rozpocząć, zapoznaj się z [najnowszymi wskazówkami dotyczącymi optymalizacji środowiska ponownego uwierzytelniania](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

---

## <a name="september-2020"></a>Wrzesień 2020

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Nowe łączniki aprowizacji w galerii aplikacji usługi Azure AD — wrzesień 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwości produktu:** integracja innej firmy
 
Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

- [Wyciąg](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [Chmura SAP Analytics](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Świadomość zabezpieczeń w programie Webroot](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzacja aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](../app-provisioning/user-provisioning.md).
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>Odświeżanie publicznej wersji zapoznawczej w chmurze

**Typ:** Nowa funkcja  
**Kategoria usługi:** Funkcja aprowizacji w chmurze usługi Azure AD **:** Zarządzanie cyklem życia tożsamości
 
Azure AD Connect w chmurze Inicjowanie obsługi publicznej wersji zapoznawczej funkcje dwóch głównych ulepszeń opracowanych na podstawie opinii klientów: 

- Środowisko mapowania atrybutów za poorednictwem Azure Portal

    Ta funkcja umożliwia administratorom IT Mapowanie atrybutów użytkowników, grup lub kontaktów z usługi AD do usługi Azure AD przy użyciu różnych typów mapowania obecnych obecnie. Mapowanie atrybutu jest funkcją służącą do standaryzacji wartości atrybutów, które przepływą od Active Directory do Azure Active Directory. Jeden może określić, czy bezpośrednio mapować wartość atrybutu, ponieważ pochodzi ona z usługi AD do usługi Azure AD, lub używać wyrażeń do przekształcania wartości atrybutów podczas aprowizacji użytkowników. [Dowiedz się więcej](../cloud-provisioning/how-to-attribute-mapping.md)

- Inicjowanie obsługi administracyjnej na żądanie lub testowanie środowiska użytkownika

    Po skonfigurowaniu konfiguracji można sprawdzić, czy transformacja użytkownika działa zgodnie z oczekiwaniami przed zastosowaniem do wszystkich użytkowników w zakresie. W przypadku aprowizacji na żądanie Administratorzy IT mogą wprowadzić nazwę wyróżniającą (DN) użytkownika usługi AD i sprawdzić, czy są one zsynchronizowane zgodnie z oczekiwaniami. Inicjowanie obsługi na żądanie zapewnia doskonały sposób, aby zapewnić, że mapowania atrybutów wcześniej działały zgodnie z oczekiwaniami. [Więcej informacji](../cloud-provisioning/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Inspekcja odzyskiwania funkcji BitLocker w usłudze Azure AD — publiczna wersja zapoznawcza

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie dostępem do urządzeń  
**Możliwość produktu:** Zarządzanie cyklem życia urządzeń
 
Gdy Administratorzy IT lub Użytkownicy końcowi odczytują klucze odzyskiwania funkcji BitLocker, do których mają dostęp, Azure Active Directory teraz generuje dziennik inspekcji, który przechwytuje osoby, które uzyskały dostęp do klucza odzyskiwania. Ta sama Inspekcja zawiera szczegóły urządzenia, z którym skojarzony jest klucz funkcji BitLocker.

Użytkownicy końcowi mogą [uzyskiwać dostęp do swoich kluczy odzyskiwania za pośrednictwem mojego konta](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key). Administratorzy IT mogą uzyskać dostęp do kluczy odzyskiwania za pośrednictwem [interfejsu API odzyskiwania kluczy funkcji BitLocker w wersji beta](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) lub za pośrednictwem portalu usługi Azure AD. Aby dowiedzieć się więcej, zobacz [Wyświetlanie lub kopiowanie kluczy funkcji BitLocker w portalu usługi Azure AD](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys).

---

### <a name="teams-devices-administrator-built-in-role"></a>Wbudowana rola administratora urządzeń zespołów

**Typ:** Nowa funkcja  
**Kategoria usługi:** RBAC  
**Możliwość produktu:** Access Control
 
Użytkownicy z rolą [administrator urządzeń zespołów](../roles/permissions-reference.md#teams-devices-administrator) mogą zarządzać [urządzeniami certyfikowanymi przez zespoły](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) z poziomu Centrum administracyjnego zespołów. 

Ta rola umożliwia użytkownikowi wyświetlanie wszystkich urządzeń w jednym skrócie, z możliwością wyszukiwania i filtrowania urządzeń. Użytkownik może również sprawdzić szczegóły każdego urządzenia, w tym konto zalogowane oraz Marka i model urządzenia. Użytkownik może zmienić ustawienia na urządzeniu i zaktualizować wersje oprogramowania. Ta rola nie udziela uprawnień do sprawdzania aktywności zespołów i jakości wywołań urządzenia.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>Zaawansowane możliwości zapytań dla obiektów katalogu

**Typ:** Nowa funkcja  
**Kategoria usługi:** Program MS Graph  
**Możliwość produktu:** Środowisko deweloperskie
 
Wszystkie nowe możliwości zapytania wprowadzone dla obiektów katalogu w interfejsach API usługi Azure AD są teraz dostępne w punkcie końcowym v 1.0 i w środowisku produkcyjnym. Deweloperzy mogą liczyć, wyszukiwać, filtrować i sortować obiekty katalogu i powiązane linki przy użyciu standardowych operatorów OData.

Aby dowiedzieć się więcej, zapoznaj się z dokumentacją w [tym miejscu](https://aka.ms/BlogPostMezzoGA)i możesz wysłać opinię z tą [krótką ankietą](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u).
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Publiczna wersja zapoznawcza: ciągła ocena dostępu dla dzierżawców, którzy skonfigurowali zasady dostępu warunkowego

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Wersja ewaluacyjna ciągłego dostępu (CAE) jest teraz dostępna w publicznej wersji zapoznawczej dla dzierżawców usługi Azure AD z zasadami dostępu warunkowego. W przypadku CAE krytyczne zdarzenia zabezpieczeń i zasady są oceniane w czasie rzeczywistym. Obejmuje to wyłączenie konta, zresetowanie hasła oraz zmianę lokalizacji. Aby dowiedzieć się więcej, zobacz [ocenę dostępu ciągłego](../conditional-access/concept-continuous-access-evaluation.md).

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Publiczna wersja zapoznawcza: Poproś użytkowników o dodatkowe pytania dotyczące podejmowania decyzji o zatwierdzeniu

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie dostępem użytkowników  
**Możliwość produktu:** Zarządzanie prawami
 
Administratorzy mogą teraz wymagać, aby użytkownicy żądający pakietu dostępu mogli udzielić dodatkowych pytań poza justem biznesowym w portalu zarządzania prawami dostępu usługi Azure AD. Odpowiedzi użytkowników będą następnie widoczne dla osób zatwierdzających, aby ułatwić im podejmowanie decyzji o zatwierdzeniu dostępu. Aby dowiedzieć się więcej, zobacz [zbieranie dodatkowych informacji o żądającym zatwierdzenia (wersja zapoznawcza)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview).
 
---

### <a name="public-preview-enhanced-user-management"></a>Publiczna wersja zapoznawcza: Ulepszone zarządzanie użytkownikami

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie użytkownikami  
**Możliwość produktu:** Zarządzanie użytkownikami
 

Portal usługi Azure AD został zaktualizowany, aby ułatwić znajdowanie użytkowników na stronach wszyscy użytkownicy i usunięci użytkownicy. Zmiany w wersji zapoznawczej obejmują: 
- Bardziej widoczne właściwości użytkownika, w tym identyfikator obiektu, stan synchronizacji katalogu, typ tworzenia i wystawca tożsamości.
- Wyszukiwanie umożliwia teraz połączone wyszukiwanie nazw, wiadomości e-mail i identyfikatorów obiektów.
- Ulepszone filtrowanie według typu użytkownika (elementu członkowskiego, gościa i brak), stanu synchronizacji katalogów, typu tworzenia, nazwy firmy i nazwy domeny.
- Nowe możliwości sortowania dotyczące właściwości, takie jak nazwa, główna nazwa użytkownika i Data usunięcia.
- Nowa łączna liczba użytkowników, którzy aktualizujeją wszystkie wyszukiwania lub filtry.

Aby uzyskać więcej informacji, zobacz [ulepszenia zarządzania użytkownikami (wersja zapoznawcza) w Azure Active Directory](../enterprise-users/users-search-enhanced.md).

---

### <a name="new-notes-field-for-enterprise-applications"></a>Nowe pole notatek dla aplikacji dla przedsiębiorstw

**Typ:** Nowa funkcja  
**Kategoria usługi:** **Funkcja produktu** Enterprise Apps: Logowanie jednokrotne

Możesz dodawać bezpłatne notatki do aplikacji dla przedsiębiorstw. Możesz dodać wszelkie istotne informacje, które ułatwią menedżerom aplikacji w aplikacjach dla przedsiębiorstw. Aby uzyskać więcej informacji, zobacz [Szybki Start: Konfigurowanie właściwości aplikacji w dzierżawie usługi Azure Active Directory (Azure AD)](../manage-apps/add-application-portal-configure.md). 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — wrzesień 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

We wrześniu 2020 dodaliśmy 34 następujące nowe aplikacje w galerii aplikacji z obsługą Federacji:

[VMware Horizon — ujednolicona Brama dostępu](), [Pulse Secure PC](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md), [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [Frontitude](https://services.enteksystems.de/sso/microsoft/signup), [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_SERVER](https://zaas.zenmutech.com/user/signin), [HashData dla firm](https://hashdata.app/login.xhtml), [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login), [CyberSolutions MAILBASEΣ/CMS](../saas-apps/cybersolutions-mailbase-tutorial.md), [CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md), [LimbleCMMS](https://auth.limblecmms.com/), [Glint Inc](../saas-apps/glint-inc-tutorial.md), [zeroheight](../saas-apps/zeroheight-tutorial.md), [przydatności do płci](https://app.genderfitness.com/), [Portal Coeo](https://my.coeo.com/), [Gramatyka](../saas-apps/grammarly-tutorial.md), [Fivetran](../saas-apps/fivetran-tutorial.md), [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA Archer Suite](../saas-apps/rsa-archer-suite-tutorial.md), [TeamzSkill](../saas-apps/teamzskill-tutorial.md), [raumfürraum](../saas-apps/raumfurraum-tutorial.md), [Saviynt](../saas-apps/saviynt-tutorial.md), [BizMerlinHR](https://marketplace.bizmerlin.net/bmone/signup), [Mobile Locker](../saas-apps/mobile-locker-tutorial.md) [, Zengine,](../saas-apps/zengine-tutorial.md) [CloudCADI](https://app.cloudcadi.com/login), [Simfoni Analytics](https://simfonianalytics.com/accounts/microsoft/login/), [PRIVA Identity & Access Management](https://my.priva.com/) [,](https://wisteconline.com/auth/oidc) [Nitro Pro](https://www.gonitro.com/nps/product-details/downloads), [Eventfinity](../saas-apps/eventfinity-tutorial.md) [Fexa,](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md) [Wistec](../saas-apps/fexa-tutorial.md), [Secure Signing Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [Secure Signing Enterprise Portal Setup](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal)

Możesz również znaleźć dokumentację dotyczącą wszystkich aplikacji z tego miejsca: https://aka.ms/AppsTutorial .

Aby wyświetlić listę aplikacji w galerii aplikacji usługi Azure AD, zapoznaj się ze szczegółowymi informacjami tutaj: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Nowa rola delegowania w zarządzaniu prawami usługi Azure AD: Menedżer przypisań pakietów programu Access

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie dostępem użytkowników  
**Możliwość produktu:** Zarządzanie prawami
 
Do usługi zarządzania prawami dostępu w usłudze Azure AD dodano nową rolę Menedżera przypisania pakietu, która zapewnia szczegółowe uprawnienia do zarządzania przypisaniami. Teraz można delegować zadania do użytkownika w tej roli, który może delegować Zarządzanie przypisaniami pakietu dostępu do właściciela firmy. Jednak Menedżer przypisania pakietów dostępu nie może zmienić zasad dostępu lub innych właściwości ustawionych przez administratorów. 

Dzięki tej nowej roli można korzystać z najniższych uprawnień wymaganych do delegowania zarządzania przypisaniami i prowadzenia kontroli administracyjnej na wszystkich innych konfiguracjach pakietów dostępu. Aby dowiedzieć się więcej, zobacz [role zarządzania uprawnieniami](../governance/entitlement-management-delegate.md#entitlement-management-roles).
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Zmiany przepływu dołączania Privileged Identity Management

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management
 
Wcześniej podczas dołączania do Privileged Identity Management (PIM) wymagana jest zgoda użytkownika i przepływ dołączenia w bloku PIM, który obejmuje rejestrację w usłudze Azure AD MFA. Dzięki ostatniej integracji środowiska PIM z blokiem role i Administratorzy usługi Azure AD usuwamy to środowisko. Każda dzierżawa z ważną licencją P2 zostanie przełączona do aplikacji PIM.

Dołączanie do programu PIM nie ma żadnego bezpośredniego skutku dla dzierżawy. Można oczekiwać następujących zmian:
- Dodatkowe opcje przypisywania, takie jak Active a i czas zakończenia, są przypisywane w bloku role i Administratorzy usługi Azure AD. 
- Dodatkowe mechanizmy określania zakresu, takie jak jednostki administracyjne i role niestandardowe, wprowadzane bezpośrednio w środowisku przypisywania. 
- Jeśli jesteś administratorem globalnym lub administratorem roli uprzywilejowanej, możesz zacząć otrzymywać kilka dodatkowych wiadomości e-mail, takich jak PIM cotygodniowe podsumowanie. 
- Nazwa główna usługi MS-PIM może być również widoczna w dzienniku inspekcji związanym z przypisaniem roli. Ta oczekiwana zmiana nie powinna mieć wpływu na normalny przepływ pracy.

 Aby uzyskać więcej informacji, zobacz [Rozpoczynanie korzystania z Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Zarządzanie prawami usługi Azure AD: Wybierz okienko zasobów pakietu dostęp teraz wyświetla domyślnie zasoby obecnie znajdujące się w wybranym katalogu

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie dostępem użytkowników  
**Możliwość produktu:** Zarządzanie prawami
 

W przepływie tworzenia pakietu dostępu na karcie role zasobów jest zmieniane zachowanie okienka wybieranie. Obecnie domyślne zachowanie polega na pokazywania wszystkich zasobów należących do użytkownika i zasobów dodanych do wybranego katalogu. 

To środowisko zostanie zmienione w taki sposób, aby domyślnie wyświetlało tylko zasoby aktualnie dodane do katalogu, dzięki czemu użytkownicy będą mogli łatwo wybierać zasoby z wykazu. Aktualizacja ułatwi odnajdowanie zasobów do dodania do pakietów dostępu i zmniejsza ryzyko przypadkowego dodawania zasobów należących do użytkownika, które nie są częścią katalogu. Aby dowiedzieć się więcej, zobacz [Tworzenie nowego pakietu dostępu w usłudze Azure AD uprawnienia do zarządzania](../governance/entitlement-management-access-package-create.md#resource-roles).
 
---

## <a name="august-2020"></a>Sierpień 2020 r. 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Aktualizacje wymagań zapory Serwer Multi-Factor Authentication platformy Azure

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Funkcja  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Od 1 października 2020 wymagania dotyczące zapory serwera usługi Azure MFA będą wymagały dodatkowych zakresów adresów IP.

Jeśli w organizacji istnieją reguły zapory dla ruchu wychodzącego, zaktualizuj reguły tak, aby serwery usługi MFA mogły komunikować się ze wszystkimi wymaganymi zakresami adresów IP. Zakresy adresów IP są udokumentowane w [wymaganiach dotyczących zapory serwer Multi-Factor Authentication platformy Azure](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Nadchodzące zmiany środowiska użytkownika w celu uzyskania bezpiecznego wyniku tożsamości

**Typ:** Planowanie zmiany  
**Kategoria usługi:** **Możliwości produktu** do ochrony tożsamości: ochrona tożsamości & zabezpieczenia

Aktualizujesz Portal bezpiecznego oceniania tożsamości, aby wyrównać zmiany wprowadzone w [nowej wersji](/microsoft-365/security/mtp/microsoft-secure-score-whats-new)Microsoft Secure Score. 

Wersja zapoznawcza ze zmianami będzie dostępna na początku września. Zmiany w wersji zapoznawczej obejmują:
- "" Bezpieczna ocena tożsamości "została zmieniona na" bezpieczna ocena tożsamości "w celu wyrównania marki przy użyciu bezpiecznego wyniku firmy Microsoft
- Punkty znormalizowane do skali standardowej i raportowane jako wartości procentowe zamiast punktów

W tej wersji zapoznawczej klienci mogą przełączać się między istniejącym środowiskiem a nowym środowiskiem. Ta wersja zapoznawcza będzie Ostatnia do końca listopada 2020. Po zakończeniu wersji zapoznawczej klienci zostaną automatycznie przekierowani do nowego środowiska użytkownika.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Nowe uprawnienia dostępu gościa z ograniczeniami w usłudze Azure AD — publiczna wersja zapoznawcza

**Typ:** Nowa funkcja  
**Kategoria usługi:** Access Control   
**Możliwość produktu:** Zarządzanie użytkownikami

Zaktualizowaliśmy uprawnienia na poziomie katalogu dla użytkowników-Gości. Te uprawnienia umożliwiają administratorom wymaganie dodatkowych ograniczeń i kontroli dostępu użytkowników zewnętrznych. Administratorzy mogą teraz dodawać dodatkowe ograniczenia dotyczące dostępu do informacji o profilach i członkostwie użytkowników i grup. Dzięki tej publicznej funkcji w wersji zapoznawczej klienci mogą zarządzać dostępem użytkowników zewnętrznych w dużej skali przez zamieszanie członkostwa w grupach, w tym ograniczając użytkownikom-Gościom wyświetlanie członkostw grup, do których się znajdują.

Aby dowiedzieć się więcej, zobacz uprawnienia [dostępu gościa z ograniczeniami](../enterprise-users/users-restrict-guest-permissions.md) i [użytkowników](./users-default-permissions.md).
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Ogólna dostępność zapytań różnicowych dla jednostek usługi

**Typ:** Nowa funkcja  
**Kategoria usługi:** Program MS Graph  
**Możliwość produktu:** Środowisko deweloperskie
 
Microsoft Graph zapytanie różnicowe obsługuje teraz typ zasobu w wersji 1.0:
- Jednostka usługi

Teraz klienci mogą efektywnie śledzić zmiany do tych zasobów i zapewniać najlepsze rozwiązanie do synchronizowania zmian tych zasobów z lokalnym magazynem danych. Aby dowiedzieć się, jak skonfigurować te zasoby w zapytaniu, zobacz [Używanie zapytania różnicowego do śledzenia zmian w danych Microsoft Graph](/graph/delta-query-overview).
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Ogólna dostępność zapytań różnicowych dla element oauth2permissiongrant

**Typ:** Nowa funkcja  
**Kategoria usługi:** Program MS Graph  
**Możliwość produktu:** Środowisko deweloperskie

Microsoft Graph zapytanie różnicowe obsługuje teraz typ zasobu w wersji 1.0:
- Element oauth2permissiongrant

Klienci mogą teraz śledzić zmiany do tych zasobów wydajnie i zapewniać najlepsze rozwiązanie do synchronizowania zmian tych zasobów z lokalnym magazynem danych. Aby dowiedzieć się, jak skonfigurować te zasoby w zapytaniu, zobacz [Używanie zapytania różnicowego do śledzenia zmian w danych Microsoft Graph](/graph/delta-query-overview).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — sierpień 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W sierpniu 2020 dodaliśmy następujące 25 nowych aplikacji w galerii aplikacji z obsługą Federacji:

[Backup365](https://portal.backup365.io/login), [Soapbox](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [Alma SIS](https://almau.getalma.com/), [Łącznik Enlyft systemu Dynamics 365](http://enlyft.com/), [Serraview rozwiązania do wykorzystania miejsca](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), [uniq](https://web.uniq.app/) [, widoczne](../saas-apps/visibly-tutorial.md), [Zyloe](../saas-apps/zylo-tutorial.md), [Edmentum-kursowe oceny oprogramowania — dokładne ścieżki](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Altamira HRM](../saas-apps/altamira-hrm-tutorial.md) [](https://app.vivun.com/dashboard/calendar/connect), [WireWheel](../saas-apps/wirewheel-tutorial.md), [ZIX zgodności i przechwycenia](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), [](https://www.wandera.com/) [Greenlight Enterprise Controls platform](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md), [](https://goto.bpanda.com/login) [Usługa genetece](https://www.clearance.network/) [,](https://scalefusion.com/users/sign_in/) [ISAMs](../saas-apps/isams-tutorial.md) [, VeraSMART, Amiko](../saas-apps/verasmart-tutorial.md) [, twingate](https://amiko.web.rivero.app/) [, Scalefusion](https://auth.twingate.com/signup) [](https://nestiolistings.com/sso/oidc/azure/authorize/) [](../saas-apps/fortigate-ssl-vpn-tutorial.md)

W tym miejscu możesz również znaleźć dokumentację dotyczącą wszystkich aplikacji https://aka.ms/AppsTutorial

Aby wyświetlić listę aplikacji w galerii aplikacji usługi Azure AD, zapoznaj się ze szczegółowymi informacjami w tym miejscu https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Dostępne są teraz lasy zasobów dla platformy Azure AD DS 

**Typ:** Nowa **Kategoria usługi funkcji:** Azure AD Domain Services   
**Możliwość produktu:** Azure AD Domain Services
 
Możliwości lasów zasobów w Azure AD Domain Services są teraz ogólnie dostępne. Teraz można włączyć autoryzację bez synchronizacji skrótów haseł do używania Azure AD Domain Services, w tym autoryzacji karty inteligentnej. Aby dowiedzieć się więcej, zobacz [pojęcia i funkcje zestawu replik dla Azure Active Directory Domain Services (wersja zapoznawcza)](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>Obsługa replik regionalnych dla AD DS domen zarządzanych przez platformę Azure jest teraz dostępna

**Typ:** Nowa funkcja   
**Kategoria usługi:** Azure AD Domain Services  
**Możliwość produktu:** Azure AD Domain Services
 
Można rozszerzyć domenę zarządzaną, aby mieć więcej niż jeden zbiór replik dla dzierżawy usługi Azure AD. Zestawy replik można dodawać do dowolnej komunikacji równorzędnej sieci wirtualnej w dowolnym regionie świadczenia usługi Azure, który obsługuje Azure AD Domain Services. Dodatkowe zestawy replik w różnych regionach platformy Azure zapewniają geograficzne odzyskiwanie po awarii dla starszych aplikacji, jeśli region platformy Azure przejdzie w tryb offline. Aby dowiedzieć się więcej, zobacz [pojęcia i funkcje zestawu replik dla Azure Active Directory Domain Services (wersja zapoznawcza)](../../active-directory-domain-services/concepts-replica-sets.md).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Ogólna dostępność usługi Azure AD my Sign-Ins

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Środowiska użytkownika końcowego
 
Usługa Azure AD my Sign-Ins to nowa funkcja umożliwiająca użytkownikom korporacyjnym przeglądanie historii logowania w celu sprawdzenia, czy nie występują żadne nietypowe działania. Ponadto ta funkcja umożliwia użytkownikom końcowym zgłaszanie "tego nie jestem" lub "to jestem ja" w podejrzanych działaniach. Aby dowiedzieć się więcej na temat korzystania z tej funkcji, zobacz [Wyświetlanie i wyszukiwanie ostatnich działań związanych z logowaniem ze strony My Sign-Ins](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity).
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>Inicjowanie obsługi administracyjnej użytkowników w usłudze SAP SuccessFactors w usłudze Azure AD jest teraz ogólnie dostępne

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
Teraz możesz zintegrować rozwiązanie SAP SuccessFactors jako autorytatywne Źródło tożsamości z usługą Azure AD i zautomatyzować kompleksowy cykl życia tożsamości przy użyciu wydarzeń HR, takich jak nowe zatrudnienie i zakończenia, aby zapewnić możliwość aprowizacji i nieaprowizacji kont w usłudze Azure AD. 

Aby dowiedzieć się więcej o sposobie konfigurowania inicjowania obsługi przychodzącej SAP SuccessFactors w usłudze Azure AD, zapoznaj się z samouczkiem [Konfigurowanie oprogramowania SAP SuccessFactors w celu Active Directory aprowizacji użytkowników](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Niestandardowa nazwa Open ID Connect interfejs API programu Graph support dla Azure AD B2C

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C
 
Wcześniej niestandardowe dostawcy połączenia Open ID mogą być dodawane lub zarządzane tylko za pomocą Azure Portal. Teraz Azure AD B2C klienci mogą także dodawać je i zarządzać nimi za pomocą wersji Microsoft Graph API wersja beta. Aby dowiedzieć się, jak skonfigurować ten zasób przy użyciu interfejsów API, zobacz [identityProvider Resource Type](/graph/api/resources/identityprovider?view=graph-rest-beta).
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Przypisywanie wbudowanych ról usługi Azure AD do grup chmur

**Typ:** Nowa funkcja  
**Kategoria usługi:** Role usługi Azure AD  
**Możliwość produktu:** Access Control

Teraz możesz przypisywać wbudowane role usługi Azure AD do grup w chmurze przy użyciu tej nowej funkcji. Można na przykład przypisać rolę administratora programu SharePoint do grupy Contoso_SharePoint_Admins. Możesz również użyć programu PIM, aby grupa była uprawnionym członkiem roli, zamiast udzielać stałego dostępu. Aby dowiedzieć się, jak skonfigurować tę funkcję, zobacz temat [Używanie grup w chmurze do zarządzania przypisaniami ról w Azure Active Directory (wersja zapoznawcza)](../roles/groups-concept.md).
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Wbudowana rola lidera biznesowego usługi Insights jest teraz dostępna

**Typ:** Nowa funkcja  
**Kategoria usługi:** Role usługi Azure AD  
**Możliwość produktu:** Access Control
 
Użytkownicy w roli lidera usługi Insights mogą uzyskać dostęp do zestawu pulpitów nawigacyjnych i szczegółowych informacji za pośrednictwem [aplikacji M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Obejmuje to pełny dostęp do wszystkich pulpitów nawigacyjnych oraz przedstawia szczegółowe informacje i funkcje eksploracji danych. Jednak użytkownicy w tej roli nie mają dostępu do ustawień konfiguracji produktu, które są odpowiedzialne za rolę administratora usługi Insights. Aby dowiedzieć się więcej na temat tej roli, zobacz [uprawnienia roli administrator w Azure Active Directory](../roles/permissions-reference.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Wbudowana rola administratora usługi Insights jest teraz dostępna

**Typ:** Nowa funkcja  
**Kategoria usługi:** Role usługi Azure AD  
**Możliwość produktu:** Access Control
 
Użytkownicy w roli administratora usługi Insights mogą uzyskać dostęp do pełnego zestawu funkcji administracyjnych w [aplikacji M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Użytkownik w tej roli może odczytywać informacje o katalogu, monitorować kondycję usługi, bilety pomocy technicznej plików i uzyskiwać dostęp do aspektów ustawień administratora usługi Insights. Aby dowiedzieć się więcej na temat tej roli, zobacz [uprawnienia roli administrator w Azure Active Directory](../roles/permissions-reference.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>Administrator aplikacji i administrator aplikacji w chmurze mogą zarządzać właściwościami rozszerzenia aplikacji

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Role usługi Azure AD  
**Możliwość produktu:** Access Control
 
Wcześniej tylko administrator globalny mógł zarządzać [właściwością rozszerzenia](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http). Teraz udostępniamy tę funkcję administratorowi aplikacji i administratorowi aplikacji w chmurze.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>Poprawka do programu MIM 2016 SP2 4.6.263.0 i łączniki 1.1.1301.0

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Microsoft Identity Manager  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości

[Pakiet zbiorczy poprawek (build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) jest dostępny dla Microsoft Identity Manager (MIM) 2016 z dodatkiem Service Pack 2 (SP2). Ten pakiet zbiorczy zawiera aktualizacje zarządzanie certyfikatami w usłudze MIM, Menedżera synchronizacji programu MIM i składników usługi PAM. Ponadto 1.1.1301.0E łączniki ogólne programu MIM zawierają aktualizacje łącznika programu Graph.

---
 


