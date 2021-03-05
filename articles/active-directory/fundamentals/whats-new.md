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
ms.date: 3/4/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9abed17f5a3d23f811c7cec0d4fd31e4433f651d
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177027"
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
## <a name="february-2021"></a>Luty 2021 r.

### <a name="email-one-time-passcode-authentication-on-by-default-starting-october-2021"></a>Wysyłaj pocztą e-mail jednorazowe uwierzytelnianie kodu dostępu, rozpoczynając od października 2021

**Typ:** Planowanie zmiany  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C
 

Od 31 października 2021 Microsoft Azure Active Directory [jednorazowe uwierzytelnianie kodu dostępu w wiadomości e-mail](../external-identities/one-time-passcode.md) stanie się domyślną metodą zapraszania kont i dzierżawców na potrzeby scenariuszy współpracy B2B. W tej chwili firma Microsoft nie będzie już zezwalać na umorzenie zaproszeń przy użyciu niezarządzanych kont Azure Active Directory. 

---

### <a name="unrequested-but-consented-permissions-will-no-longer-be-added-to-tokens-if-they-would-trigger-conditional-access"></a>Niewymagane, ale uprawnienia, które zostały wysłane, nie będą już dodawane do tokenów, jeśli spowodują wyzwolenie dostępu warunkowego

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Platformach
 
Obecnie w aplikacjach korzystających z [uprawnień dynamicznych](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent) są nadawane wszystkie uprawnienia, które są do niego wysyłane. Obejmuje to aplikacje, które nie są żądane, a nawet wtedy, gdy wyzwalają dostęp warunkowy. Na przykład może to spowodować, że aplikacja żąda tylko `user.read` , że ma również zgodę na `files.read` przekazanie dostępu warunkowego przypisanego do `files.read` uprawnienia. 

Aby zmniejszyć liczbę niepotrzebnych monitów dostępu warunkowego, usługa Azure AD zmienia sposób, w jaki zakresy niewymagane są dostarczane do aplikacji. Aplikacje będą wyzwalać tylko dostęp warunkowy dla uprawnień, które jawnie zażądają. Aby uzyskać więcej informacji, przeczytaj artykuł [co nowego w uwierzytelnianiu](../develop/reference-breaking-changes.md#conditional-access-will-only-trigger-for-explicitly-requested-scopes).
 
---
 
### <a name="public-preview---use-a-temporary-access-pass-to-register-passwordless-credentials"></a>Publiczna wersja zapoznawcza — używanie tymczasowego dostępu do rejestrowania poświadczeń bezhasła

**Typ:** Nowa funkcja  
**Kategoria usługi:** Funkcja  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Przekazywanie dostępu tymczasowego to ograniczone przez czas kod dostępu, który służy jako silne poświadczenia i umożliwia dołączanie poświadczeń bezterminowych i odzyskiwania, gdy użytkownik stracił lub zapomniał silny czynnik uwierzytelniania (na przykład FIDO2 zabezpieczeń lub Microsoft Authenticator) i musi zalogować się, aby zarejestrować nowe metody silnego uwierzytelniania. [Dowiedz się więcej](../authentication/howto-authentication-temporary-access-pass.md).

---

### <a name="public-preview---keep-me-signed-in-kmsi-in-next-generation-of-user-flows"></a>Publiczna wersja zapoznawcza — nie wylogowuj mnie (KMSI) w następnej generacji przepływów użytkowników

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C

Kolejna generacja przepływów użytkowników usługi B2C obsługuje teraz funkcję [Keep ja in (KMSI)](https://docs.microsoft.com/azure/active-directory-b2c/session-behavior?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) , która umożliwia klientom zwiększenie okresu istnienia sesji dla użytkowników aplikacji internetowych i natywnych przy użyciu trwałego pliku cookie.  Funkcja utrzymuje aktywną sesję nawet wtedy, gdy użytkownik zamknie i ponownie otworzy przeglądarkę i zostanie odwołany, gdy użytkownik wyloguje się.

---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-msa-accounts"></a>Publiczna wersja zapoznawcza — tożsamości zewnętrzne Self-Service rejestracji w usłudze AAD przy użyciu kont MSA

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C
 
Użytkownicy zewnętrzni będą teraz mogli korzystać z kont Microsoft, aby zalogować się do usługi Azure AD i aplikacji biznesowych. [Dowiedz się więcej](../external-identities/self-service-sign-up-overview.md).

---

### <a name="public-preview---reset-redemption-status-for-a-guest-user"></a>Publiczna wersja zapoznawcza — resetowanie stanu wykupu dla użytkownika-gościa

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C
 
Klienci mogą teraz ponownie zapraszać istniejących użytkowników zewnętrznych, aby resetować stan ich wykupu, dzięki czemu konto użytkownika-gościa pozostanie bez utraty dostępu. [Dowiedz się więcej](../external-identities/reset-redemption-status.md).
 
---

### <a name="public-preview---synchronization-provisioning-apis-now-support-application-permissions"></a>Publiczna wersja zapoznawcza — interfejsy API/Synchronization (aprowizacji) obsługują teraz uprawnienia aplikacji

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
Klienci mogą teraz używać aplikacji Application. ReadWrite. ownedby jako uprawnienia aplikacji do wywoływania interfejsów API synchronizacji. Należy pamiętać, że jest to obsługiwane tylko w przypadku inicjowania obsługi administracyjnej z usługi Azure AD do aplikacji innych firm (na przykład AWS, kostki danych itp.). Nie jest to obecnie obsługiwane w przypadku udostępniania HR (Workday/SuccessFactors) ani synchronizacji z chmurą (AD do usługi Azure AD). [Dowiedz się więcej](https://docs.microsoft.com/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta).
 
---

### <a name="general-availability---authentication-policy-administrator-built-in-role"></a>Ogólna dostępność — wbudowana rola administratora zasad uwierzytelniania

**Typ:** Nowa funkcja  
**Kategoria usługi:** RBAC  
**Możliwość produktu:** Access Control
 
Użytkownicy z tą rolą mogą konfigurować zasady metod uwierzytelniania, ustawienia usługi MFA dla całej dzierżawy oraz zasady ochrony haseł. Ta rola przyznaje uprawnienia do zarządzania ustawieniami ochrony hasłem: inteligentne blokady konfiguracje i aktualizowanie listy niestandardowo zakazanych haseł. [Dowiedz się więcej](../roles/permissions-reference.md#authentication-policy-administrator).

---

### <a name="general-availability---user-collections-on-my-apps-are-available-now"></a>Ogólna dostępność — kolekcje użytkowników w moich aplikacjach są teraz dostępne!

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** Środowiska użytkownika końcowego
 
Użytkownicy mogą teraz tworzyć własne grupowania aplikacji w programie uruchamiania aplikacji Moje aplikacje. Mogą także zmieniać kolejność i ukrywać kolekcje udostępnione im przez administratora. [Dowiedz się więcej](../user-help/my-apps-portal-user-collections.md).

---

### <a name="general-availability---autofill-in-authenticator"></a>Ogólna dostępność — Autowypełnianie w wystawcy uwierzytelniania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacja Microsoft Authenticator  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Microsoft Authenticator zapewnia możliwości usługi uwierzytelniania wieloskładnikowego (MFA) i zarządzania kontami, a teraz spowoduje również Autowypełnianie haseł w witrynach i aplikacjach, które użytkownicy odwiedzają na urządzeniach przenośnych (iOS i Android). 

Aby użyć Autowypełniania na wystawcy uwierzytelnienia, użytkownicy muszą dodać własne konto Microsoft do uwierzytelniania i używać ich do synchronizowania haseł. Konta służbowe nie mogą być używane do synchronizacji haseł w tym momencie. [Dowiedz się więcej](../user-help/user-help-auth-app-faq.md#autofill-for-it-admins).

---

### <a name="general-availability---invite-internal-users-to-b2b-collaboration"></a>Ogólna dostępność — Zapraszanie użytkowników wewnętrznych do współpracy B2B

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C
 
Klienci mogą teraz zapraszać wewnętrznych Gości do korzystania z współpracy B2B zamiast wysyłać zaproszenia do istniejącego konta wewnętrznego. Umożliwia to klientom utrzymywanie identyfikatora obiektu użytkownika, nazwy UPN, członkostwa w grupach i przypisań aplikacji. [Dowiedz się więcej](../external-identities/invite-internal-users.md).

---

### <a name="general-availability---domain-name-administrator-built-in-role"></a>Ogólna dostępność — wbudowana rola administratora nazw domen

**Typ:** Nowa funkcja  
**Kategoria usługi:** RBAC  
**Możliwość produktu:** Access Control
 
Użytkownicy z tą rolą mogą zarządzać nazwami domen (odczyt, Dodawanie, weryfikowanie, aktualizowanie i usuwanie). Mogą również odczytywać informacje katalogu dotyczące użytkowników, grup i aplikacji, ponieważ te obiekty mają zależności domeny. 

W przypadku środowisk lokalnych użytkownicy z tą rolą mogą konfigurować nazwy domen dla Federacji, dzięki czemu skojarzeni użytkownicy są zawsze uwierzytelniani lokalnie. Ci użytkownicy mogą następnie zalogować się do usług opartych na usłudze Azure AD przy użyciu haseł lokalnych przy użyciu logowania jednokrotnego. Ustawienia Federacji należy synchronizować za pośrednictwem Azure AD Connect, aby użytkownicy mieli również uprawnienia do zarządzania Azure AD Connect. [Dowiedz się więcej](../roles/permissions-reference.md#domain-name-administrator).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---february-2021"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — luty 2021

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W lutym 2021 dodaliśmy 37 następujące nowe aplikacje w galerii aplikacji z obsługą Federacji:

[Pętla programu Messenger Extension](https://loopworks.com/loop-flow-messenger/), [Silverfort Azure AD adapter](http://www.silverfort.com/), [współpraca Learning](https://skilledtrades.interplaylearning.com/#login), [Nura Space](https://dashboard.nuraspace.com/login), [Yooz UE](https://eu1.getyooz.com/?kc_idp_hint=microsoft), [UXPressia](https://uxpressia.com/users/sign-in), [introDus pre-i](http://app.introdus.dk/login)dołączania platform, [Happybot](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=34353e1e-dfe5-4d2f-bb09-2a5e376270c8&response_type=code&redirect_uri=https://api.happyteams.io/microsoft/integrate&response_mode=query&scope=offline_access%20User.Read%20User.Read.All), [LeaksID](https://app.leaksid.com/), [ShiftWizard](http://www.shiftwizard.com/), [PingFlow SSO](https://app.pingview.io/), [Swiftlane](https://admin.swiftlane.com/login), [Quasydoc SSO](https://www.quasydoc.eu/login), [Fenwick Gold](https://businesscentral.dynamics.com/), [SeamlessDesk](https://www.seamlessdesk.com/login), Learnsoft, system [LMS & TMS](http://www.learnsoft.com/), [P-th +](https://p-th.jp/), [myViewBoard](https://api.myviewboard.com/auth/microsoft/), [Tartabit IoT Bridge](https://bridge-us.tartabit.com/), [AKASHI](../saas-apps/akashi-tutorial.md), [Watch](../saas-apps/rewatch-tutorial.md), [Zuddl](../saas-apps/zuddl-tutorial.md), Parkalot [-samochód — zarządzanie parkami](../saas-apps/parkalot-car-park-management-tutorial.md), [HSB ThoughtSpot](../saas-apps/hsb-thoughtspot-tutorial.md), [IBMid](../saas-apps/ibmid-tutorial.md), [SharingCloud](../saas-apps/sharingcloud-tutorial.md), [PoolParty — pakiet semantyczny](../saas-apps/poolparty-semantic-suite-tutorial.md), [GlobeSmart](../saas-apps/globesmart-tutorial.md), [Samsung KNOX i Business Services](../saas-apps/samsung-knox-and-business-services-tutorial.md), [Penji](../saas-apps/penji-tutorial.md), [Kendis-skalowanie platform Agile](../saas-apps/kendis-scaling-agile-platform-tutorial.md) [,](../saas-apps/klaxoon-saml-tutorial.md)Maptician, Olfeo [SaaS,](../saas-apps/olfeo-saas-tutorial.md) [Sigma Computing](../saas-apps/sigma-computing-tutorial.md) [,](../saas-apps/enablon-tutorial.md) [Platforma zarządzania uprawnieniami](../saas-apps/cloudknox-permissions-management-platform-tutorial.md) [CloudKnox](../saas-apps/maptician-tutorial.md)

Dokumentację dotyczącą wszystkich aplikacji można również znaleźć tutaj: https://aka.ms/AppsTutorial

Aby wyświetlić listę aplikacji w galerii aplikacji usługi Azure AD, zapoznaj się ze szczegółowymi informacjami w tym miejscu: https://aka.ms/AzureADAppRequest

--- 

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2021"></a>Nowe łączniki aprowizacji w galerii aplikacji usługi Azure AD — luty 2021

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwości produktu:** integracja innej firmy
 

Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

- [Atea](../saas-apps/atea-provisioning-tutorial.md)
- [Getabstract](../saas-apps/getabstract-provisioning-tutorial.md)
- [HelloID](../saas-apps/helloid-provisioning-tutorial.md)
- [Hoxhunt](../saas-apps/hoxhunt-provisioning-tutorial.md)
- [Iris Intranet](../saas-apps/iris-intranet-provisioning-tutorial.md)
- [Preciate](../saas-apps/preciate-provisioning-tutorial.md)

Aby uzyskać więcej informacji, zapoznaj się [z automatyzacją aprowizacji użytkowników, aby SaaS aplikacje za pomocą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="general-availability---10-azure-active-directory-roles-now-renamed"></a>Ogólna dostępność — 10 ról Azure Active Directory teraz zmieniono ich nazwy

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** RBAC  
**Możliwość produktu:** Access Control
 
10 wbudowanych ról usługi Azure AD zmieniono nazwy, aby były wyrównane do [Centrum administracyjnego Microsoft 365](https://docs.microsoft.com/microsoft-365/admin/microsoft-365-admin-center-preview), [portalu usługi Azure AD](https://portal.azure.com/)i [Microsoft Graph](https://developer.microsoft.com/graph/). Aby dowiedzieć się więcej o nowych rolach, zapoznaj się z [uprawnieniami roli administrator w Azure Active Directory](../roles/permissions-reference.md#all-roles).

![Tabela nowych nazw ról](media/whats-new/roles-table-rbac.png)

---

### <a name="new-company-branding-in-mfasspr-combined-registration"></a>Nowe oznakowanie firmy w ramach usługi MFA/SSPR połączonej rejestracji

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Środowisko użytkownika i zarządzanie  
**Możliwość produktu:** Środowiska użytkownika końcowego
 
W przeszłości logo firmy nie było używane na Azure Active Directory stronach logowania. Znakowanie firmowe znajduje się teraz w lewym górnym rogu usługi MFA/SSPR połączonej rejestracji. Znakowanie firmowe jest również zawarte na stronie Moje Sign-Ins i informacje zabezpieczające. [Dowiedz się więcej](../fundamentals/customize-branding.md).

---

### <a name="general-availability---second-level-manager-can-be-set-as-alternate-approver"></a>Ogólna dostępność — Menedżer drugiego poziomu można ustawić jako alternatywną osobę zatwierdzającą

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie dostępem użytkowników  
**Możliwość produktu:** Zarządzanie prawami
 
Dodatkowa opcja w przypadku wybrania opcji osoby zatwierdzające jest teraz dostępna w usłudze zarządzania prawami. Jeśli wybierzesz opcję "Menedżer jako osoba zatwierdzająca" dla pierwszej osoby zatwierdzającej, będziesz mieć inną opcję "Menedżer drugiego poziomu jako alternatywny osoba zatwierdzająca", dostępną do wyboru w polu alternatywna osoba zatwierdzająca. W przypadku wybrania tej opcji należy dodać osobę zatwierdzającą rezerwę do przesyłania żądania do programu w przypadku, gdy system nie może odnaleźć Menedżera drugiego poziomu. [Dowiedz się więcej](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).
 
---

### <a name="authentication-methods-activity-dashboard"></a>Pulpit nawigacyjny aktywności metod uwierzytelniania

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania
 

Odświeżony pulpit nawigacyjny działania metod uwierzytelniania zapewnia administratorom Omówienie rejestracji metod uwierzytelniania i działania użycia w ich dzierżawie. Raport zawiera podsumowanie liczby użytkowników zarejestrowanych dla każdej metody, a także metod, które są używane podczas logowania i resetowania hasła. [Dowiedz się więcej](../authentication/howto-authentication-methods-activity.md).
 
---

### <a name="refresh-and-session-token-lifetimes-configurability-in-configurable-token-lifetime-ctl-are-retired"></a>Okresy istnienia tokenu sesji odświeżania i określając w konfigurowalnym okresie istnienia tokenu (CTL) są wycofywane

**Typ:** Przestarzałe  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Okresy istnienia tokenu sesji odświeżania i określając na liście CTL zostały wycofane. Azure Active Directory nie będzie już przestrzegać konfiguracji odświeżania i tokenu sesji w istniejących zasadach. [Dowiedz się więcej](../develop/active-directory-configurable-token-lifetimes.md#token-lifetime-policies-for-refresh-tokens-and-session-tokens).
 
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

[mySCView](https://dev.myscview.com/), [Talentech](https://talentech.com/contact/), [Bipsync](https://www.bipsync.com/), [OroTimesheet](https://app.orotimesheet.com/login.php), [Mio](https://app.m.io/auth/install/microsoft?scopetype=hub), [Sovelto Easy](https://login.soveltoeasy.fi/), [Supportbench](https://account.supportbench.net/agent/login/),[Bienvenuee](https://formation.bienvenue.pro/login), [AIDAa](https://aidaforparents.com/login/organizations), [Międzynarodowa pomoc techniczną sos](../saas-apps/international-sos-assistance-products-tutorial.md), [NAVEX jeden](../saas-apps/navex-one-tutorial.md), [LabLog](../saas-apps/lablog-tutorial.md), [Oktopost SAML](../saas-apps/oktopost-saml-tutorial.md), [EPHOTOa](../saas-apps/ephoto-dam-tutorial.md), [pojęcie](../saas-apps/notion-tutorial.md), [Syndio](../saas-apps/syndio-tutorial.md), [Yello Enterprise](../saas-apps/yello-enterprise-tutorial.md), [TimeClock 365 SAML](../saas-apps/timeclock-365-saml-tutorial.md), [Nalco E-Data](https://www.ecolab.com/), [](https://app.vacancy-filler.co.uk/VFMVC/Account/Login)Synerisee [, Imperva](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md) [Experience](../saas-apps/perimeter-81-tutorial.md) [,](../saas-apps/aruba-user-experience-insight-tutorial.md) [Illusive,](../saas-apps/imperva-data-security-tutorial.md) [sPlan,](../saas-apps/contentsquare-sso-tutorial.md) [,](../saas-apps/illusive-networks-tutorial.md),, [w](../saas-apps/proware-tutorial.md)wersji [](../saas-apps/splan-visitor-tutorial.md)81 [Enterprise](../saas-apps/burp-suite-enterprise-edition-tutorial.md)

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

Dodatkowa opcja jest teraz dostępna w procesie zatwierdzania w usłudze zarządzania uprawnieniami. Jeśli wybierzesz pozycję Menedżer jako osoba zatwierdzająca dla pierwszej osoby zatwierdzającej, będziesz mieć kolejną opcję, drugi poziom, jako alternatywną osobę zatwierdzającą, dostępną do wyboru w polu alternatywny podmiot zatwierdzający. Po wybraniu tej opcji należy dodać osobę zatwierdzającą rezerwę do przesyłania żądania do programu w przypadku, gdy system nie może odnaleźć Menedżera drugiego poziomu.

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

Aby uzyskać więcej informacji na temat tej funkcji, zobacz temat [wyłączanie i usuwanie tożsamości zewnętrznych przy użyciu przeglądów dostępu do usługi Azure AD](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews).
 
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

Zaleca się, aby klienci używali identyfikatorów szablonów ról w skryptach i kodzie programu PowerShell zamiast nazwy wyświetlanej. Identyfikator szablonu roli jest obsługiwany dla obiektów [directoryRoles](/graph/api/resources/directoryrole) i [definicji](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta) . Aby uzyskać więcej informacji na temat identyfikatorów szablonów ról, zobacz [wbudowane role usługi Azure AD](../roles/permissions-reference.md).

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

    Ta funkcja umożliwia administratorom IT Mapowanie atrybutów użytkowników, grup lub kontaktów z usługi AD do usługi Azure AD przy użyciu różnych typów mapowania obecnych obecnie. Mapowanie atrybutu jest funkcją służącą do standaryzacji wartości atrybutów, które przepływą od Active Directory do Azure Active Directory. Jeden może określić, czy bezpośrednio mapować wartość atrybutu, ponieważ pochodzi ona z usługi AD do usługi Azure AD, lub używać wyrażeń do przekształcania wartości atrybutów podczas aprowizacji użytkowników. [Dowiedz się więcej](../cloud-sync/how-to-attribute-mapping.md)

- Inicjowanie obsługi administracyjnej na żądanie lub testowanie środowiska użytkownika

    Po skonfigurowaniu konfiguracji można sprawdzić, czy transformacja użytkownika działa zgodnie z oczekiwaniami przed zastosowaniem do wszystkich użytkowników w zakresie. W przypadku aprowizacji na żądanie Administratorzy IT mogą wprowadzić nazwę wyróżniającą (DN) użytkownika usługi AD i sprawdzić, czy są one zsynchronizowane zgodnie z oczekiwaniami. Inicjowanie obsługi na żądanie zapewnia doskonały sposób, aby zapewnić, że mapowania atrybutów wcześniej działały zgodnie z oczekiwaniami. [Więcej informacji](../cloud-sync/how-to-on-demand-provision.md)
 
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
