---
title: Co nowego Informacje o wersji — Azure Active Directory | Microsoft Docs
description: Dowiedz się, co nowego w Azure Active Directory; na przykład najnowsze informacje o wersji, znane problemy, poprawki usterek, przestarzałe funkcje i nadchodzące zmiany.
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
ms.date: 3/31/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81a909d946b55ee8b06d68aa8bee53bc50d2190e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532296"
---
# <a name="whats-new-in-azure-active-directory"></a>Co nowego w programie Azure Active Directory?

>Otrzymuj powiadomienia o tym, kiedy ponownie chcieć na tę stronę w celu uzyskania aktualizacji, kopiując i wklejając ten adres URL: w czytniku źródła danych RSS ikona czytnika `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` ![ ](./media/whats-new/feed-icon-16x16.png) kanału informacyjnego.

Usługa Azure AD na bieżąco otrzymuje ulepszenia. Aby być na bieżąco z najnowszymi zmianami, ten artykuł zawiera informacje na temat:

- Najnowsze wersje
- Znane problemy
- Poprawki błędów
- Funkcje uznane za przestarzałe
- Plany zmian

Ta strona jest aktualizowana co miesiąc, więc regularnie ją odwiedzaj. Jeśli szukasz elementów starszych niż sześć miesięcy, możesz je znaleźć w archiwum co nowego w u [Azure Active Directory](whats-new-archive.md).

---

## <a name="march-2021"></a>Marzec 2021 r.

### <a name="guidance-on-how-to-enable-support-for-tls-12-in-your-environment-in-preparation-for-upcoming-azure-ad-tls-1011-deprecation"></a>Wskazówki dotyczące włączania obsługi wersji TLS 1.2 w środowisku w ramach przygotowań do nadchodzącego cofania obsługi wersji 1.0/1.1 usługi Azure AD

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** N/a  
**Możliwości produktu:** Standardów

Azure Active Directory 30 czerwca 2021 r. następujące protokoły Azure Active Directory regionach na całym świecie:


- TLS 1.0
- TLS 1.1
- Zestaw szyfrowania 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Środowiska, których dotyczy problem, obejmują:

- Komercyjna chmura platformy Azure
- Office 365 GCC i WW

Aby uzyskać dodatkowe wskazówki, zapoznaj się z tematem [Enable support for TLS 1.2 in your environment for Azure AD TLS 1.1 and 1.0 deprecation](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment)(Włączanie obsługi dla wersji TLS 1.2 w środowisku dla wersji 1.1 i 1.0 usługi Azure AD w celu cofania obsługi).

---

### <a name="public-preview---azure-ad-entitlement-management-now-supports-multi-geo-sharepoint-online"></a>Publiczna wersja zapoznawcza — zarządzanie uprawnieniami usługi Azure AD obsługuje teraz usługę SharePoint Online z wieloma obszarami geograficznymi

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Zarządzanie uprawnieniami
 
W przypadku organizacji korzystających z usługi SharePoint Online z wieloma obszarami geograficznymi można teraz dołączać witryny z określonych środowisk z wieloma obszarami geograficznymi do pakietów dostępu do zarządzania uprawnieniami. [Dowiedz się więcej](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site-preview).

---

### <a name="public-preview---restore-deleted-apps-from-app-registrations"></a>Publiczna wersja zapoznawcza — przywracanie usuniętych aplikacji z Rejestracje aplikacji

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Środowisko dewelopera
 
Klienci mogą teraz wyświetlać, przywracać i trwale usuwać usunięte rejestracje aplikacji z Azure Portal. Dotyczy to tylko aplikacji skojarzonych z katalogiem, a nie aplikacji z katalogu osobistego konto Microsoft. [Dowiedz się więcej](../develop/quickstart-restore-app.md).
 
---

### <a name="public-preview---new-user-action-in-conditional-access-for-registering-or-joining-devices"></a>Publiczna wersja zapoznawcza — nowa "akcja użytkownika" w dostępie warunkowym do rejestrowania lub dołączania urządzeń

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Identity Security & Protection
 
 Dostępna jest nowa akcja użytkownika o nazwie "Rejestrowanie lub dołączanie urządzeń" w ramach dostępu warunkowego. Ta akcja użytkownika umożliwia kontrolowanie zasad uwierzytelniania wieloskładnikowego (MFA) na rzecz rejestracji urządzeń w usłudze Azure AD. 

Obecnie ta akcja użytkownika umożliwia włączenie uwierzytelniania wieloskładnikowego jako kontrolki tylko wtedy, gdy użytkownicy rejestrują urządzenia lub dołączają je do usługi Azure AD. Inne kontrolki, które są zależne od rejestracji urządzeń w usłudze Azure AD lub nie mają zastosowania, są wyłączone za pomocą tej akcji użytkownika. [Dowiedz się więcej](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions). 
 
---

### <a name="public-preview---optimize-connector-groups-to-use-the-closest-application-proxy-cloud-service"></a>Publiczna wersja zapoznawcza — optymalizowanie grup łączników w celu używania najbliższej serwer proxy aplikacji w chmurze

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwości produktu:** Access Control
 
Dzięki tej nowej możliwości grupy łączników można przypisać do najbliższej regionalnej serwer proxy aplikacji, w których jest hostowana aplikacja. Może to poprawić wydajność aplikacji w scenariuszach, w których aplikacje są hostowane w regionach innych niż region dzierżawy macierzyskiej. [Dowiedz się więcej](../manage-apps/application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview). 
 
---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>Publiczna wersja zapoznawcza — External Identities Self-Service konta usługi AAD przy użyciu poczty e-mail One-Time kont kodu dostępu

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C

Użytkownicy zewnętrzni będą teraz mogli używać kont One-Time e-mail w celu logowania się do aplikacji LOB i aplikacji LOB usługi Azure AD. [Dowiedz się więcej](../external-identities/one-time-passcode.md).

---

### <a name="public-preview---availability-of-ad-fs-sign-ins-in-azure-ad"></a>Publiczna wersja zapoznawcza — dostępność AD FS Sign-Ins usłudze Azure AD

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (identyfikatory logowania)  
**Możliwości produktu:** Monitorowanie & raportowania
 
AD FS logowania można teraz zintegrować z raportowaniem aktywności usługi Azure AD, zapewniając ujednolicony widok infrastruktury tożsamości hybrydowej. Korzystając z raportu usługi Azure AD Sign-Ins, usługi Log Analytics i skoroszytów usługi Azure Monitor, można przeprowadzić szczegółową analizę scenariuszy logowania usług AAD i AD FS, takich jak blokada konta usługi AD FS, próby użycia złych haseł i skoki nieoczekiwanych prób logowania.

Aby dowiedzieć się więcej, [odwiedź AD FS logowania w usłudze Azure AD](../hybrid/how-to-connect-health-ad-fs-sign-in.md)za pomocą programu Connect Health.

---

### <a name="general-availability---staged-rollout-to-cloud-authentication"></a>Ogólna dostępność — etapowe uwierzytelnianie w chmurze

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** AD Connect  
**Możliwości produktu:** Uwierzytelnianie użytkowników
 
Etapowe uwierzytelnianie w chmurze jest teraz ogólnie dostępne. Funkcja etapowego wycofywania umożliwia selektywne testowanie grup użytkowników przy użyciu metod uwierzytelniania w chmurze, takich jak uwierzytelnianie passthrough (PTA) lub synchronizacja skrótów haseł (PHS). W międzyczasie wszyscy inni użytkownicy w domenach federowanych nadal używają usług federowanych, takich jak AD FS lub innych usług federowanych, do uwierzytelniania użytkowników. [Dowiedz się więcej](../hybrid/how-to-connect-staged-rollout.md).

---

### <a name="general-availability---user-type-attribute-can-now-be-updated-in-the-azure-admin-portal"></a>Ogólna dostępność — atrybut typu użytkownika można teraz zaktualizować w portalu administracyjnym platformy Azure

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Środowisko użytkownika i zarządzanie  
**Możliwości produktu:** Zarządzanie użytkownikami
 
Klienci mogą teraz aktualizować typ użytkownika użytkowników usługi Azure AD podczas aktualizowania informacji o profilu użytkownika z portalu administracyjnego platformy Azure. Typ użytkownika można również zaktualizować z Microsoft Graph. Aby dowiedzieć się więcej, zobacz [Dodawanie lub aktualizowanie informacji o profilu użytkownika.](active-directory-users-profile-azure-portal.md)
 
---

### <a name="general-availability---replica-sets-for-azure-active-directory-domain-services"></a>Ogólna dostępność — zestawy replik dla Azure Active Directory Domain Services

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Azure AD Domain Services  
**Możliwości produktu:** Azure AD Domain Services
 
Możliwości zestawów replik w Azure AD DS są teraz ogólnie dostępne. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory-domain-services/concepts-replica-sets).
 
---

### <a name="general-availability---collaborate-with-your-partners-using-email-one-time-passcode-in-the-azure-government-cloud"></a>Ogólna dostępność — współpraca z partnerami przy użyciu kodu dostępu One-Time e-mail w Azure Government chmury

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C
 
Organizacje w chmurze Microsoft Azure Government mogą teraz umożliwiać swoim gościom realizowanie zaproszeń przy użyciu One-Time e-mail i kodu dostępu. Dzięki temu każdy użytkownik-gość bez kont usługi Azure AD, Microsoft lub Gmail w chmurze usługi Azure Government będzie nadal współpracować ze swoimi partnerami, żądając i wprowadzając tymczasowy kod w celu zalogowania się do udostępnionych zasobów. [Dowiedz się więcej](../external-identities/one-time-passcode.md#note-for-azure-us-government-customers).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---march-2021"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — marzec 2021 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja z produktami innych firm
 
W marcu 2021 r. dodaliśmy następujące 37 nowych aplikacji w galerii aplikacji z obsługą federacji:

[Sklep z](https://lcx.bambuser.com/)wideo na żywo, [DeepDyve Inc,](https://www.deepdyve.com/azure-sso) [Moqups,](../saas-apps/moqups-tutorial.md) [URZĄDZENIA DO](https://ricohspaces.app/welcome)MAGAZYNOWANIA Mobile, [Flipgrid,](https://auth.flipgrid.com/) [hCaptcha Enterprise,](../saas-apps/hcaptcha-enterprise-tutorial.md) [SchoolStream ASA,](https://jsd.schoolstreamk12.com/ASA/ASAlogin.aspx) [TransPerfect GlobalLink Dashboard,](../saas-apps/transperfect-globallink-dashboard-tutorial.md) [SimplificaCI,](https://app.simplificaci.com.br/) [Thrive LXP,](../saas-apps/thrive-lxp-tutorial.md) [Lexonis TalentScape,](../saas-apps/lexonis-talentscape-tutorial.md) [Exium,](../saas-apps/exium-tutorial.md) [Sapient,](../saas-apps/sapient-tutorial.md) [TrueChoice,](../saas-apps/truechoice-tutorial.md) [SPACESH Spaces](https://ricohspaces.app/welcome), [Patcha Cloud,](../saas-apps/learning-at-work-tutorial.md) [Acunetix 360](../saas-apps/acunetix-360-tutorial.md), [Exceed.ai](../saas-apps/exceed-ai-tutorial.md), GitHub Enterprise [Managed User](../saas-apps/github-enterprise-managed-user-tutorial.md), Enterprise Vault.cloud [for Outlook](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile%20User.Read&client_id=7176efe5-e954-4aed-b5c8-f5c85a980d3a&nonce=4b9e1981-1bcb-4938-a283-86f6931dc8cb), [Smartlook](../saas-apps/smartlook-tutorial.md), [Accenture Academy](../saas-apps/accenture-academy-tutorial.md), [Onshape,](../saas-apps/onshape-tutorial.md) [Tradeshift](../saas-apps/tradeshift-tutorial.md), [JuriBlox,](../saas-apps/juriblox-tutorial.md) [SecurityStudio](../saas-apps/securitystudio-tutorial.md), [ClicData,](https://app.clicdata.com/) [Evergreen,](../saas-apps/evergreen-tutorial.md) [Patchdeck,](https://patchdeck.com/ad_auth/authenticate/) [FAX. PLUS,](../saas-apps/fax.plus-tutorial.md) [ValidSign,](../saas-apps/validsign-tutorial.md) [AWS Single Sign-on,](../saas-apps/aws-single-sign-on-tutorial.md) [Nura Space,](https://dashboard.nuraspace.com/login) [Broadcom DX SaaS,](../saas-apps/broadcom-dx-saas-tutorial.md) [Interplay Learning,](https://skilledtrades.interplaylearning.com/#login) [SendPro Enterprise,](../saas-apps/sendpro-enterprise-tutorial.md) [FortiSASE SIA](../saas-apps/fortisase-sia-tutorial.md)

Dokumentację wszystkich aplikacji można również znaleźć tutaj: https://aka.ms/AppsTutorial

Aby uzyskać listę aplikacji w galerii aplikacji usługi Azure AD, przeczytaj szczegółowe informacje tutaj: https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---march-2021"></a>Nowe łączniki aprowizowania w galerii aplikacji usługi Azure AD — marzec 2021 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwości produktu:** integracja z produktami innych firm

Teraz możesz zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla tych nowo zintegrowanych aplikacji:

- [Logowanie pojedyncze na platformie AWS](../saas-apps/aws-single-sign-on-provisioning-tutorial.md)
- [Bpanda](../saas-apps/bpanda-provisioning-tutorial.md)
- [Britive](../saas-apps/britive-provisioning-tutorial.md)
- [GitHub Enterprise użytkownika zarządzanego](../saas-apps/github-enterprise-managed-user-provisioning-tutorial.md)
- [Grammarly](../saas-apps/grammarly-provisioning-tutorial.md)
- [LogicGate](../saas-apps/logicgate-provisioning-tutorial.md)
- [SecureLogin](../saas-apps/secure-login-provisioning-tutorial.md)
- [TravelPerk](../saas-apps/travelperk-provisioning-tutorial.md)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu zautomatyzowanego aprowizowania kont użytkowników, zobacz [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Automatyzacja aprowzowania użytkowników w aplikacjach SaaS przy użyciu usługi Azure AD).
 
---

### <a name="introducing-ms-graph-api-for-company-branding"></a>Wprowadzenie do brandowania interfejs Graph API MS

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** MS Graph  
**Możliwości produktu:** B2B/B2C

[Usługa MS interfejs Graph API](https://docs.microsoft.com/graph/api/resources/organizationalbrandingproperties)  znakowanie firmowe jest dostępna dla usługi Azure AD lub logowania Microsoft 365, aby umożliwić programowe zarządzanie parametrami znakowania.

---

### <a name="general-availability---header-based-authentication-sso-with-application-proxy"></a>Ogólna dostępność — logowanie jednokrotne do uwierzytelniania opartego na nagłówku z serwer proxy aplikacji

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwości produktu:** Access Control
 
Usługa Azure AD serwer proxy aplikacji natywną obsługę uwierzytelniania opartego na nagłówku jest teraz ogólnie dostępne. Dzięki tej funkcji można skonfigurować atrybuty użytkownika wymagane jako nagłówki HTTP dla aplikacji bez konieczności wdrażania dodatkowych składników. [Dowiedz się więcej](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md).

---

### <a name="two-way-sms-for-mfa-server-is-no-longer-supported"></a>Dwukierunkowa wiadomość SMS dla serwera MFA nie jest już obsługiwana

**Wpisz:** Przestarzałe  
**Kategoria usługi:** Mfa  
**Możliwości produktu:** Identity Security & Protection
 

Dwukierunkowa wiadomość SMS dla serwera MFA została pierwotnie wycofana w 2018 r. i nie będzie obsługiwana po 24 lutego 2021 r. Administratorzy powinni włączyć inną metodę dla użytkowników, którzy nadal używają jednokierunkowych wiadomości SMS.

Powiadomienia e-mail i powiadomienia Service Health Azure Portal zostały wysłane do administratorów, których to dotyczy, w dniach 8 grudnia 2020 r. i 28 stycznia 2021 r. Alerty zostały powiązane z subskrypcjami przy rolach właściciela, współwłaścicieli, administratora i administratora usługi. [Dowiedz się więcej](../authentication/how-to-authentication-two-way-sms-unsupported.md).
 
---
 
## <a name="february-2021"></a>Luty 2021 r.

### <a name="email-one-time-passcode-authentication-on-by-default-starting-october-2021"></a>Domyślnie od października 2021 r. jest włączone uwierzytelnianie za pomocą jednego kodu dostępu za pomocą poczty e-mail

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C
 

Od 31 października 2021 r. uwierzytelnianie za pomocą jednego kodu dostępu Microsoft Azure Active Directory [e-mail](../external-identities/one-time-passcode.md) będzie domyślną metodą zapraszania kont i dzierżaw dla scenariuszy współpracy B2B. W tej chwili firma Microsoft nie będzie już zezwalać na realizacja zaproszeń przy użyciu kont niezamówień Azure Active Directory kont. 

---

### <a name="unrequested-but-consented-permissions-will-no-longer-be-added-to-tokens-if-they-would-trigger-conditional-access"></a>Nieuzytelne, ale wyrażane zgody uprawnienia nie będą już dodawane do tokenów, jeśli wyzwoliłyby dostęp warunkowy

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Uwierzytelnienia (identyfikatory logowania)  
**Możliwości produktu:** Platformy
 
Obecnie aplikacje [korzystające z uprawnień dynamicznych](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent) mają nadane wszystkie uprawnienia, na które mają zgodę. Dotyczy to również aplikacji, które nie sąquested i nawet jeśli wyzwalają dostęp warunkowy. Na przykład może to spowodować, że aplikacja żąda tylko, która również ma zgodę dla usługi , może zostać wymuszona do przekazania dostępu warunkowego przypisanego `user.read` `files.read` do `files.read` uprawnienia. 

Aby zmniejszyć liczbę niepotrzebnych monitów o dostęp warunkowy, usługa Azure AD zmienia sposób, w jaki nieuzytelniane zakresy są udostępniane aplikacjom. Aplikacje będą wyzwalać tylko dostęp warunkowy dla uprawnień, których jawnie żądają. Aby uzyskać więcej informacji, [przeczytaj Co nowego w uwierzytelnianiu.](../develop/reference-breaking-changes.md#conditional-access-will-only-trigger-for-explicitly-requested-scopes)
 
---
 
### <a name="public-preview---use-a-temporary-access-pass-to-register-passwordless-credentials"></a>Publiczna wersja zapoznawcza — używanie Tymczasowy kod dostępu do rejestrowania poświadczeń bez hasła

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Mfa  
**Możliwości produktu:** Identity Security & Protection

Tymczasowy kod dostępu to ograniczony czasowo kod dostępu, który służy jako silne poświadczenia i umożliwia dołączanie poświadczeń bez hasła i odzyskiwania, gdy użytkownik utraci lub zapomni swój silny współczynnik uwierzytelniania (na przykład klucz zabezpieczeń FIDO2 lub Microsoft Authenticator) i musi się zalogować, aby zarejestrować nowe silne metody uwierzytelniania. [Dowiedz się więcej](../authentication/howto-authentication-temporary-access-pass.md).

---

### <a name="public-preview---keep-me-signed-in-kmsi-in-next-generation-of-user-flows"></a>Publiczna wersja zapoznawcza — nie wytrzymuj mnie w trybie zalogowania (KMSI) przy następnej generacji przepływów użytkownika

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2C — zarządzanie tożsamościami konsumentów  
**Możliwości produktu:** B2B/B2C

Następna generacja przepływów użytkowników B2C obsługuje teraz funkcję "nie wyślij mnie" [(KMSI, keep me signed in),](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) która umożliwia klientom wydłużenie okresu istnienia sesji dla użytkowników aplikacji internetowych i natywnych przy użyciu trwałego pliku cookie.  Funkcja utrzymuje aktywną sesję nawet wtedy, gdy użytkownik zamknie i ponownie otworzy przeglądarkę i zostanie odwołana, gdy użytkownik się wyczyszczy.

---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-msa-accounts"></a>Publiczna wersja zapoznawcza — External Identities Self-Service rejestracji w UAD przy użyciu kont MSA

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C
 
Użytkownicy zewnętrzni mogą teraz używać kont Microsoft do logowania się do aplikacji LOB i aplikacji LOB usługi Azure AD. [Dowiedz się więcej](../external-identities/self-service-sign-up-overview.md).

---

### <a name="public-preview---reset-redemption-status-for-a-guest-user"></a>Publiczna wersja zapoznawcza — resetowanie stanu realizacji dla użytkownika-gościa

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C
 
Klienci mogą teraz ponownie zaimprocować istniejących zewnętrznych użytkowników-gości w celu zresetowania ich stanu realizacji, co umożliwi kontu użytkownika-gościa pozostanie bez utraty dostępu. [Dowiedz się więcej](../external-identities/reset-redemption-status.md).
 
---

### <a name="public-preview---synchronization-provisioning-apis-now-support-application-permissions"></a>Publiczna wersja zapoznawcza — interfejsy API /synchronization (provisioning) obsługują teraz uprawnienia aplikacji

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
Klienci mogą teraz używać funkcji application.readwrite.ownedby jako uprawnienia aplikacji do wywołania interfejsów API synchronizacji. Pamiętaj, że jest to obsługiwane tylko w przypadku aprowizowania z usługi Azure AD do aplikacji innych firm (na przykład AWS, Data Bricks itp.). Obecnie nie jest ona obsługiwana w przypadku aprowyzacji hr (Workday/Successfactors) ani synchronizacji z chmury (z usługi AD do usługi Azure AD). [Dowiedz się więcej](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true).
 
---

### <a name="general-availability---authentication-policy-administrator-built-in-role"></a>Ogólna dostępność — wbudowana rola administratora zasad uwierzytelniania

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Rbac  
**Możliwości produktu:** Access Control
 
Użytkownicy z tą rolą mogą konfigurować zasady metod uwierzytelniania, ustawienia usługi MFA dla całej dzierżawy i zasady ochrony haseł. Ta rola przyznaje uprawnienia do zarządzania ustawieniami ochrony haseł: konfiguracjami inteligentnej blokady i aktualizowaniem niestandardowej listy zakazanych haseł. [Dowiedz się więcej](../roles/permissions-reference.md#authentication-policy-administrator).

---

### <a name="general-availability---user-collections-on-my-apps-are-available-now"></a>Ogólna dostępność — kolekcje użytkowników Moje aplikacje są teraz dostępne!

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwości produktu:** Środowisko użytkownika końcowego
 
Użytkownicy mogą teraz tworzyć własne grupy aplikacji w Moje aplikacje uruchamiania aplikacji. Mogą również zmieniać kolejność i ukrywać kolekcje udostępnione im przez administratora. [Dowiedz się więcej](../user-help/my-apps-portal-user-collections.md).

---

### <a name="general-availability---autofill-in-authenticator"></a>Ogólna dostępność — autowypełnianie w aplikacji Authenticator

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Microsoft Authenticator aplikacji  
**Możliwości produktu:** Identity Security & Protection
 
Microsoft Authenticator oferuje funkcje uwierzytelniania wieloskładnikowego (MFA) i zarządzania kontami, a teraz będzie również automatycznie wypełniać hasła w witrynach i aplikacjach odwiedzanych przez użytkowników na urządzeniach przenośnych (iOS i Android). 

Aby używać autowypełniania w aplikacji Authenticator, użytkownicy muszą dodać swoje osobiste konto Microsoft do aplikacji Authenticator i używać go do synchronizacji haseł. Obecnie do synchronizowania haseł nie można używać kont służbowych. [Dowiedz się więcej](../user-help/user-help-auth-app-faq.md#autofill-for-it-admins).

---

### <a name="general-availability---invite-internal-users-to-b2b-collaboration"></a>Ogólna dostępność — zapraszanie użytkowników wewnętrznych do współpracy B2B

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C
 
Klienci mogą teraz zapraszać gości wewnętrznych do korzystania ze współpracy B2B zamiast wysyłania zaproszenia do istniejącego konta wewnętrznego. Dzięki temu klienci mogą zachować identyfikator obiektu tego użytkownika, nazwę UPN, członkostwo w grupach i przypisania aplikacji. [Dowiedz się więcej](../external-identities/invite-internal-users.md).

---

### <a name="general-availability---domain-name-administrator-built-in-role"></a>Ogólna dostępność — wbudowana rola administratora nazw domen

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Rbac  
**Możliwości produktu:** Access Control
 
Użytkownicy z tą rolą mogą zarządzać nazwami domen (odczytywać, dodawać, weryfikować, aktualizować i usuwać). Mogą również odczytywać informacje o użytkownikach, grupach i aplikacjach, ponieważ te obiekty mają zależności domeny. 

W środowiskach lokalnych użytkownicy z tą rolą mogą konfigurować nazwy domen na potrzeby federacji, aby skojarzone z nimi użytkownicy były zawsze uwierzytelniani lokalnie. Ci użytkownicy mogą następnie logować się do usług opartych na usłudze Azure AD przy użyciu haseł lokalnych za pośrednictwem logowania pojedynczego. Ustawienia federacji muszą być synchronizowane za pośrednictwem Azure AD Connect, aby użytkownicy mieli również uprawnienia do zarządzania Azure AD Connect. [Dowiedz się więcej](../roles/permissions-reference.md#domain-name-administrator).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---february-2021"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — luty 2021 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja z produktami innych firm
 
W lutym 2021 r. dodaliśmy następujące 37 nowych aplikacji w galerii aplikacji z obsługą federacji:

[Loop Yahoo Extension](https://loopworks.com/loop-flow-messenger/), [Silverdesk Azure AD Adapter](http://www.silverfort.com/), [Interplay Learning](https://skilledtrades.interplaylearning.com/#login), [Nura Space](https://dashboard.nuraspace.com/login), [Yooz EU](https://eu1.getyooz.com/?kc_idp_hint=microsoft), [UXPressia](https://uxpressia.com/users/sign-in), [introDus Pre- and Onboarding Platform](http://app.introdus.dk/login), [Happybot](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=34353e1e-dfe5-4d2f-bb09-2a5e376270c8&response_type=code&redirect_uri=https://api.happyteams.io/microsoft/integrate&response_mode=query&scope=offline_access%20User.Read%20User.Read.All), [LeaksID](https://app.leaksid.com/), [ShiftWizard,](http://www.shiftwizard.com/) [PingFlow SSO](https://app.pingview.io/), [Swiftlane,](https://admin.swiftlane.com/login) [Quasydoc SSO](https://www.quasydoc.eu/login), [Fendesk Gold Account](https://businesscentral.dynamics.com/), [SeamlessDesk](https://www.seamlessdesk.com/login), [Learnsoft LMS & TMS](http://www.learnsoft.com/), [P-TH+](https://p-th.jp/), [myViewBoard](https://api.myviewboard.com/auth/microsoft/) [,Lane I Mostek](https://bridge-us.tartabit.com/)OT , [ASIAI](../saas-apps/akashi-tutorial.md), [Rewatch](../saas-apps/rewatch-tutorial.md), [Zuddl](../saas-apps/zuddl-tutorial.md), [Parkalot — zarządzanie parkiem](../saas-apps/parkalot-car-park-management-tutorial.md)samochodowym, [HSB ThoughtSpot,](../saas-apps/hsb-thoughtspot-tutorial.md) [IBMid,](../saas-apps/ibmid-tutorial.md) [SharingCloud,](../saas-apps/sharingcloud-tutorial.md) [PoolParty Semantic Suite,](../saas-apps/poolparty-semantic-suite-tutorial.md) [GlobeSmart,](../saas-apps/globesmart-tutorial.md)Samsung Knox i Business Services , [Penji,](../saas-apps/penji-tutorial.md) [Kendis- Scaling Agile Platform](../saas-apps/kendis-scaling-agile-platform-tutorial.md), [Maptician,](../saas-apps/maptician-tutorial.md) [Olfeo SAAS,](../saas-apps/olfeo-saas-tutorial.md) [Sigma Computing,](../saas-apps/sigma-computing-tutorial.md) [CloudKnox Permissions Management](../saas-apps/cloudknox-permissions-management-platform-tutorial.md) [Platform,](../saas-apps/samsung-knox-and-business-services-tutorial.md) [Saaxoon SAML,](../saas-apps/klaxoon-saml-tutorial.md) [Enablon](../saas-apps/enablon-tutorial.md)

Dokumentację wszystkich aplikacji można również znaleźć tutaj: https://aka.ms/AppsTutorial

Aby uzyskać listę aplikacji w galerii aplikacji usługi Azure AD, przeczytaj szczegółowe informacje tutaj: https://aka.ms/AzureADAppRequest

--- 

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2021"></a>Nowe łączniki aprowizowania w galerii aplikacji usługi Azure AD — luty 2021 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwości produktu:** integracja z produktami innych firm
 

Teraz możesz zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla tych nowo zintegrowanych aplikacji:

- [Atea](../saas-apps/atea-provisioning-tutorial.md)
- [Getabstract](../saas-apps/getabstract-provisioning-tutorial.md)
- [HelloID](../saas-apps/helloid-provisioning-tutorial.md)
- [Hoxhunt](../saas-apps/hoxhunt-provisioning-tutorial.md)
- [Iris Intranet](../saas-apps/iris-intranet-provisioning-tutorial.md)
- [Opady](../saas-apps/preciate-provisioning-tutorial.md)

Aby uzyskać więcej informacji, przeczytaj [Automate user provisioning to SaaS applications with Azure AD (Automatyzowanie aprowzowania użytkowników w aplikacjach SaaS za pomocą usługi Azure AD).](../app-provisioning/user-provisioning.md)

---

### <a name="general-availability---10-azure-active-directory-roles-now-renamed"></a>Ogólna dostępność — 10 ról Azure Active Directory ma teraz zmienioną nazwę

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Rbac  
**Możliwości produktu:** Access Control
 
Nazwy 10 wbudowanych ról usługi Azure AD zostały zmienione tak, aby były dopasowane do [centrum administracyjne platformy Microsoft 365,](/microsoft-365/admin/microsoft-365-admin-center-preview)portalu usługi [Azure AD](https://portal.azure.com/) [i Microsoft Graph](https://developer.microsoft.com/graph/). Aby dowiedzieć się więcej o nowych rolach, zapoznaj się z [tematem Uprawnienia ról administratorów w Azure Active Directory](../roles/permissions-reference.md#all-roles).

![Tabela przedstawiająca nazwy ról w programie MS interfejs Graph API i Azure Portal oraz proponowaną końcową nazwę interfejsu API, Azure Portal i komputerów Mac.](media/whats-new/roles-table-rbac.png)

---

### <a name="new-company-branding-in-mfasspr-combined-registration"></a>Znakowanie nowej firmy w połączonej rejestracji uwierzytelniania wieloskładnikowego/SSPR

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Środowisko użytkownika i zarządzanie  
**Możliwości produktu:** Środowisko użytkownika końcowego
 
W przeszłości logo firmy nie były używane na Azure Active Directory logowania. Znakowanie firmowe znajduje się teraz w lewym górnym rogu połączonej rejestracji uwierzytelniania wieloskładnikowego/SSPR. Znakowanie firmowe jest również zawarte na stronie Sign-Ins i informacje zabezpieczające. [Dowiedz się więcej](../fundamentals/customize-branding.md).

---

### <a name="general-availability---second-level-manager-can-be-set-as-alternate-approver"></a>Ogólna dostępność — menedżer drugiego poziomu może być ustawiony jako alternatywna osoba zatwierdzająca

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie dostępem użytkowników  
**Możliwości produktu:** Zarządzanie uprawnieniami
 
Dodatkowa opcja po wybraniu osób zatwierdzających jest teraz dostępna w zarządzaniu uprawnieniami. Jeśli wybierzesz opcję "Menedżer jako osoba zatwierdzająca" dla pierwszej osoby zatwierdzającej, będziesz mieć inną opcję "Menedżer drugiego poziomu jako alternatywna osoba zatwierdzająca", która będzie dostępna do wyboru w polu alternatywnej osoby zatwierdzającej. Jeśli wybierzesz tę opcję, musisz dodać rezerwowe osoby zatwierdzające, aby przesyłać dalej żądanie do w przypadku, gdy system nie może znaleźć menedżera drugiego poziomu. [Dowiedz się więcej](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).
 
---

### <a name="authentication-methods-activity-dashboard"></a>Pulpit nawigacyjny działania Metody uwierzytelniania

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportów
 

Odświeżony pulpit nawigacyjny działania Metody uwierzytelniania zawiera administratorom omówienie rejestracji metody uwierzytelniania i działań użycia w dzierżawie. Raport zawiera podsumowanie liczby użytkowników zarejestrowanych dla każdej metody, a także metod używanych podczas logowania i resetowania hasła. [Dowiedz się więcej](../authentication/howto-authentication-methods-activity.md).
 
---

### <a name="refresh-and-session-token-lifetimes-configurability-in-configurable-token-lifetime-ctl-are-retired"></a>Możliwość konfigurowania okresów istnienia tokenu odświeżania i sesji w konfigurowalnym okresie istnienia tokenu (CTL) została wycofana

**Wpisz:** Przestarzałe  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Uwierzytelnianie użytkowników
 
Możliwość konfigurowania okresów istnienia tokenów odświeżania i sesji w CTL została wycofana. Azure Active Directory już nie honoruje konfiguracji odświeżania i tokenu sesji w istniejących zasadach. [Dowiedz się więcej](../develop/active-directory-configurable-token-lifetimes.md#token-lifetime-policies-for-refresh-tokens-and-session-tokens).
 
---
 
## <a name="january-2021"></a>Styczeń 2021 r.

### <a name="secret-token-will-be-a-mandatory-field-when-configuring-provisioning"></a>Token tajny będzie polem obowiązkowym podczas konfigurowania aprowizowania

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości

W przeszłości pole tokenu tajnego mogło być puste podczas konfigurowania aprowizowania w aplikacji niestandardowej/BYOA. Ta funkcja została przeznaczona wyłącznie do testowania. Zaktualizujemy interfejs użytkownika, aby wymagać pola. 

Klienci mogą ominą to wymaganie do celów testowych, używając flagi funkcji w adresie URL przeglądarki. [Dowiedz się więcej](../app-provisioning/use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery).
 
---

### <a name="public-preview---customize-and-configure-android-shared-devices-for-frontline-workers-at-scale"></a>Publiczna wersja zapoznawcza — dostosowywanie i konfigurowanie urządzeń udostępnionych z systemem Android dla pracowników pracujących na pierwszej linii na dużą skalę

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Rejestracja urządzeń i zarządzanie nimi  
**Możliwości produktu:** Identity Security & Protection
 
Usługi Azure AD i zespoły Endpoint Manager Microsoft połączyły się, aby zapewnić możliwość dostosowywania, skalowania i zabezpieczania urządzeń procesów roboczych frontonu.

Następujące możliwości w wersji zapoznawczej umożliwiają:
- Aprowizowanie udostępnionych urządzeń z systemem Android na dużą skalę za pomocą usługi Microsoft Endpoint Manager
- Zabezpieczanie dostępu dla pracowników zmianowych przy użyciu dostępu warunkowego opartego na urządzeniach
- Dostosowywanie obsługi logowania dla pracowników zmianowych przy użyciu aplikacji Managed Home Screen

Aby dowiedzieć się więcej, zapoznaj się z [tematem Customize and configure shared devices for frontline workers at scale](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-and-configure-shared-devices-for-firstline-workers-at/ba-p/1751708)(Dostosowywanie i konfigurowanie urządzeń udostępnionych dla pracowników pracujących na pierwszej linii na dużą skalę).

---

### <a name="public-preview---provisioning-logs-can-now-be-downloaded-as-a-csv-or-json"></a>Publiczna wersja zapoznawcza — dzienniki aprowizowania można teraz pobrać w formacie CSV lub JSON

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości

Klienci mogą pobierać dzienniki aprowizowania jako plik CSV lub JSON za pośrednictwem interfejsu użytkownika i interfejsu API programu Graph. Aby dowiedzieć się więcej, zapoznaj się z [tematem Aprowizowanie raportów w Azure Active Directory portal.](../reports-monitoring/concept-provisioning-logs.md)

---

### <a name="public-preview---assign-cloud-groups-to-azure-ad-custom-roles-and-admin-unit-scoped-roles"></a>Publiczna wersja zapoznawcza — przypisywanie grup w chmurze do ról niestandardowych i ról o zakresie jednostki administracyjnej usługi Azure AD

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Rbac  
**Możliwości produktu:** Access Control
 
Klienci mogą przypisać grupę w chmurze do ról niestandardowych usługi Azure AD lub roli o zakresie jednostki administracyjnej. Aby dowiedzieć się, jak używać tej funkcji, zobacz [Use cloud groups to manage role assignments in Azure Active Directory](../roles/groups-concept.md)( Używanie grup w chmurze do zarządzania przypisaniami ról w usłudze Azure Active Directory .

---

### <a name="general-availability---azure-ad-connect-cloud-sync-previously-known-as-cloud-provisioning"></a>Ogólna dostępność — Azure AD Connect synchronizacja chmury (wcześniej znana jako aprownia w chmurze)

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Azure AD Connect synchronizacji chmury  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
Azure AD Connect jest teraz ogólnie dostępna dla wszystkich klientów.

Azure AD Connect przenosi duże obciążenia logiki przekształcania do chmury, co zmniejsza zużycie miejsca w środowisku lokalnym. Ponadto dostępnych jest wiele lekkich wdrożeń agentów w celu większej dostępności synchronizacji. [Dowiedz się więcej](https://aka.ms/cloudsyncGA).
 
---
### <a name="general-availability---attack-simulation-administrator-and-attack-payload-author-built-in-roles"></a>Ogólna dostępność — wbudowane role Administrator symulacji ataku i Ładunek ataku Autor

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Rbac  
**Możliwości produktu:** Access Control
 
Do użytkowników są Role-Based Access Control dostępne dwie nowe role: Administrator symulacji ataku i Autor ładunku ataku. 

Użytkownicy z rolą [Administrator symulacji ataku](../roles/permissions-reference.md#attack-simulation-administrator) mają dostęp do wszystkich symulacji w dzierżawie i mogą:
- tworzenie wszystkich aspektów tworzenia symulacji ataku i zarządzanie nimi
- uruchamianie/planowanie symulacji
-  przeglądanie wyników symulacji. 

Użytkownicy z rolą [Autora ładunku ataku](../roles/permissions-reference.md#attack-payload-author) mogą tworzyć ładunki ataków, ale nie mogą ich uruchamiać ani planować. Ładunki ataków są następnie dostępne dla wszystkich administratorów w dzierżawie, którzy mogą ich używać do tworzenia symulacji.

---

### <a name="general-availability---usage-summary-reports-reader-built-in-role"></a>Ogólna dostępność — wbudowana rola czytelnika raportów podsumowania użycia

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Rbac  
**Możliwości produktu:** Access Control
 
Użytkownicy z rolą Czytelnik raportów podsumowania użycia mogą uzyskać dostęp do zagregowanych danych na poziomie dzierżawy i skojarzonych szczegółowych informacji w centrum administracyjnym usługi Microsoft 365 na temat użycia i oceny produktywności. Nie mogą jednak uzyskać dostępu do szczegółów ani szczegółowych informacji na poziomie użytkownika. 

W centrum Microsoft 365 administracyjnego dla tych dwóch raportów rozróżniamy zagregowane dane na poziomie dzierżawy i szczegóły na poziomie użytkownika. Ta rola dodaje dodatkową warstwę ochrony do danych umożliwiających identyfikację poszczególnych użytkowników. [Dowiedz się więcej](../roles/permissions-reference.md#usage-summary-reports-reader).

---

### <a name="general-availability---require-app-protection-policy-grant-in-azure-ad-conditional-access"></a>Ogólna dostępność — wymaganie Ochrona aplikacji zasad w dostępie warunkowym usługi Azure AD

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Identity Security & Protection
 
Przyznawanie dostępu warunkowego w usłudze Azure AD dla ustawienia "Wymagaj zasad ochrony aplikacji" jest teraz dostępne w wersji GA. 

Zasady zapewniają następujące możliwości:
- Zezwala na dostęp tylko w przypadku korzystania z aplikacji mobilnej obsługującej usługę Intune Ochrona aplikacji
- Zezwala na dostęp tylko wtedy, gdy użytkownik ma zasady ochrony aplikacji usługi Intune dostarczone do aplikacji mobilnej

Dowiedz się więcej na temat sposobu skonfigurowania zasad dostępu warunkowego w celu ochrony aplikacji [tutaj.](../conditional-access/app-protection-based-conditional-access.md)
 
---

### <a name="general-availability---email-one-time-passcode"></a>Ogólna dostępność — kod dostępu One-Time e-mail

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C
 
E-mail OTP umożliwia organizacjom na całym świecie współpracę z innymi osobami przez wysłanie linku lub zaproszenia pocztą e-mail. Zaproszeni użytkownicy mogą zweryfikować swoją tożsamość przy użyciu kodów dostępu wysyłanych na adres e-mail w celu uzyskania dostępu do zasobów partnera. [Dowiedz się więcej](../external-identities/one-time-passcode.md). 
 
---

 ### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2021"></a>Nowe łączniki aprowizowania w galerii aplikacji usługi Azure AD — styczeń 2021 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwości produktu:** integracja z produktami innych firm
 
Teraz możesz zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla tych nowo zintegrowanych aplikacji:
- [Fortes Change Cloud](../saas-apps/fortes-change-cloud-provisioning-tutorial.md)
- [Gtmhub](../saas-apps/gtmhub-provisioning-tutorial.md)
- [monday.com](../saas-apps/mondaycom-provisioning-tutorial.md)
- [Splashtop](../saas-apps/splashtop-provisioning-tutorial.md)
- [Templafy OpenID Connect](../saas-apps/templafy-openid-connect-provisioning-tutorial.md)
- [WEDO](../saas-apps/wedo-provisioning-tutorial.md)

Aby uzyskać więcej informacji, zobacz [Co to jest automatyczna aprowzowanie użytkowników aplikacji SaaS w usłudze Azure AD?](../app-provisioning/user-provisioning.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---january-2021"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — styczeń 2021 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja z produktami innych firm

W styczniu 2021 r. dodaliśmy następujące 29 nowych aplikacji w galerii aplikacji z obsługą federacji:

[mySCView,](https://dev.myscview.com/) [Talentech,](https://talentech.com/contact/) [Bipsync,](https://www.bipsync.com/) [OroTimesheet,](https://app.orotimesheet.com/login.php) [Mio,](https://app.m.io/auth/install/microsoft?scopetype=hub) [Sovelto Easy,](https://login.soveltoeasy.fi/) [Supportbench,](https://account.supportbench.net/agent/login/)[Bienvenue,](https://formation.bienvenue.pro/login) [AIDA Healthcare SSO,](https://aidaforparents.com/login/organizations) [International SOS Assistance Products](../saas-apps/international-sos-assistance-products-tutorial.md), [NAVEX One,](../saas-apps/navex-one-tutorial.md) [LabLog,](../saas-apps/lablog-tutorial.md) [Oktopost SAML,](../saas-apps/oktopost-saml-tutorial.md) [EPHOTO DAM,](../saas-apps/ephoto-dam-tutorial.md) [Notion,](../saas-apps/notion-tutorial.md) [Syndio,](../saas-apps/syndio-tutorial.md) [Yello Enterprise,](../saas-apps/yello-enterprise-tutorial.md) [Timeclock 365 SAML,](../saas-apps/timeclock-365-saml-tutorial.md) [Nalco E-data, Nalco E-data,](https://www.ecolab.com/) [Ecosystem](https://app.vacancy-filler.co.uk/VFMVC/Account/Login), [Synerise AI Growth Ecosystem,](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md) [Imperva Data Security,](../saas-apps/imperva-data-security-tutorial.md) [Illusive Networks,](../saas-apps/illusive-networks-tutorial.md) [Proware,](../saas-apps/proware-tutorial.md) [Splan Visitor,](../saas-apps/splan-visitor-tutorial.md) [Alonea User Experience Insight,](../saas-apps/aruba-user-experience-insight-tutorial.md) [Contentsquare SSO,](../saas-apps/contentsquare-sso-tutorial.md) [Perimeter 81,](../saas-apps/perimeter-81-tutorial.md) [Burp Suite Enterprise Edition](../saas-apps/burp-suite-enterprise-edition-tutorial.md)

Dokumentację wszystkich aplikacji można również znaleźć tutaj https://aka.ms/AppsTutorial

Aby uzyskać listę aplikacji w galerii aplikacji usługi Azure AD, przeczytaj szczegółowe informacje tutaj https://aka.ms/AzureADAppRequest 

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Publiczna wersja zapoznawcza — menedżer drugiego poziomu może być ustawiony jako alternatywna osoba zatwierdzająca

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie dostępem użytkowników  
**Możliwości produktu:** Zarządzanie uprawnieniami
 
Dodatkowa opcja po wybraniu osób zatwierdzających jest teraz dostępna w zarządzaniu uprawnieniami. Jeśli wybierzesz opcję "Menedżer jako osoba zatwierdzająca" dla pierwszej osoby zatwierdzającej, będziesz mieć inną opcję "Menedżer drugiego poziomu jako alternatywna osoba zatwierdzająca", która będzie dostępna do wyboru w polu alternatywnej osoby zatwierdzającej. Jeśli wybierzesz tę opcję, musisz dodać osoby zatwierdzającej rezerwę, aby przesyłać dalej żądanie do programu w przypadku, gdy system nie może znaleźć menedżera drugiego poziomu. [Dowiedz się więcej](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="general-availability---navigate-to-teams-directly-from-my-access-portal"></a>Ogólna dostępność — przechodzenie do aplikacji Teams bezpośrednio Mój dostęp portalu

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie dostępem użytkowników  
**Możliwości produktu:** Zarządzanie uprawnieniami
 
Teraz możesz uruchomić aplikację Teams bezpośrednio z Mój dostęp portal. 

W tym celu zaloguj się do usługi Mój dostęp ( , przejdź do "Pakiety dostępu", a następnie przejdź do karty "Aktywne", aby wyświetlić wszystkie pakiety dostępu, do których https://myaccess.microsoft.com/) masz już dostęp. Po rozwinięciu wybranego pakietu dostępu i umieszczeniu wskaźnika myszy na aplikacji Teams możesz go uruchomić, klikając przycisk "Otwórz". [Dowiedz się więcej](../governance/entitlement-management-request-access.md).
 
---

### <a name="improved-logging--end-user-prompts-for-risky-guest-users"></a>Ulepszone rejestrowanie i & End-User monity dla ryzykownych użytkowników-gości

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Identity Protection  
**Możliwości produktu:** Identity Security & Protection
 

Zaktualizowano rejestrowanie i End-User monity dla ryzykownych użytkowników-gości. Dowiedz się więcej na temat użytkowników usługi Identity Protection i [B2B.](../identity-protection/concept-identity-protection-b2b.md)
 
---
 
## <a name="december-2020"></a>Grudzień 2020 r.

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>Publiczna wersja zapoznawcza — Azure AD B2C rejestracji i logowania za pomocą wbudowanych zasad

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2C — zarządzanie tożsamościami konsumentów  
**Możliwości produktu:** B2B/B2C
 
Rejestracja i logowanie na telefonie B2C przy użyciu wbudowanych zasad umożliwiają administratorom IT i deweloperom organizacji zezwalanie użytkownikom na logowanie się i logowanie przy użyciu numeru telefonu w przepływach użytkownika. Aby dowiedzieć się więcej, zobacz Konfigurowanie rejestracji i logowania za pomocą telefonu [dla przepływów użytkownika (wersja](../../active-directory-b2c/phone-authentication-user-flows.md) zapoznawcza).

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>Ogólna dostępność — domyślne ustawienia zabezpieczeń są teraz domyślnie włączone dla wszystkich nowych dzierżaw

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Identity Security & Protection
 
Aby chronić konta użytkowników, wszystkie nowe dzierżawy utworzone w dniu 12 listopada 2020 r. lub później będą mieć włączone wartości domyślne zabezpieczeń. Ustawienia domyślne zabezpieczeń wymuszają wiele zasad, w tym:
- Wymaga od wszystkich użytkowników i administratorów zarejestrowania się w celu uwierzytelniania wieloskładnikowego przy użyciu Microsoft Authenticator aplikacji
- Wymaga, aby krytyczne role administratora używały usługi MFA za każdym razem, gdy się logują. W razie potrzeby wszyscy inni użytkownicy będą monitować o uwierzytelniania wieloskładnikowego. 
- Starsze uwierzytelnianie zostanie zablokowane w całej dzierżawie. 

Aby uzyskać więcej informacji, [przeczytaj Co to są ustawienia domyślne zabezpieczeń?](../fundamentals/concept-fundamentals-security-defaults.md)

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>Ogólna dostępność — obsługa grup z maksymalnie 250 000 elementów członkowskich w programie AADConnect

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** AD Connect  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
Firma Microsoft wdrożyła nowy punkt końcowy (API) dla usługi Azure AD Connect, który zwiększa wydajność operacji usługi synchronizacji w celu Azure Active Directory. W przypadku korzystania z nowego punktu końcowego [w wersji 2](../hybrid/how-to-connect-sync-endpoint-api-v2.md)można zauważyć zauważalne wzrosty wydajności podczas eksportowania i importowania do usługi Azure AD. Ten nowy punkt końcowy obsługuje następujące scenariusze:

- Synchronizowanie grup z maksymalnie 250 000 członków
- Wzrost wydajności eksportu i importu do usługi Azure AD

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>Ogólna dostępność — zarządzanie uprawnieniami dostępne dla dzierżaw w chmurze Azure — Chiny

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie dostępem użytkowników  
**Możliwości produktu:** Zarządzanie uprawnieniami
 

Funkcje zarządzania uprawnieniami są teraz dostępne dla wszystkich dzierżaw w chmurze Azure w Chinach. Aby uzyskać więcej informacji, odwiedź naszą [witrynę dokumentacji dotyczącej nadzoru nad tożsamościami.](https://docs.azure.cn/zh-cn/active-directory/governance/)

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Nowe łączniki aprowizowania w galerii aplikacji usługi Azure AD — grudzień 2020 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwości produktu:** integracja z produktami innych firm

Teraz możesz zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla tych nowo zintegrowanych aplikacji:

- [Bizagi Studio for Digital Process Automation](../saas-apps/bizagi-studio-for-digital-process-automation-provisioning-tutorial.md)
- [CybSafe](../saas-apps/cybsafe-provisioning-tutorial.md)
- [GroupTalk](../saas-apps/grouptalk-provisioning-tutorial.md)
- [PaperCut Cloud Print Management](../saas-apps/papercut-cloud-print-management-provisioning-tutorial.md)
- [Parsable](../saas-apps/parsable-provisioning-tutorial.md)
- [Shopify Plus](../saas-apps/shopify-plus-provisioning-tutorial.md)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu zautomatyzowanej aprowzowania kont użytkowników, zobacz [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Automatyzacja aprowzowania użytkowników w aplikacjach SaaS przy użyciu usługi Azure AD).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — grudzień 2020 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja z produktami innych firm
 
W grudniu 2020 r. dodaliśmy następujące 18 nowych aplikacji w galerii aplikacji z obsługą federacji:

[AwareGo,](../saas-apps/awarego-tutorial.md) [HowNow SSO,](https://gethownow.com/) [ZyLAB ONE Legal Hold,](https://www.zylab.com/en/product/legal-hold) [Guider,](http://www.guider-ai.com/) [Softcrisis,](https://www.softcrisis.se/sv/) [Pims 365,](http://www.omega365.com/pims) [InformaCast,](../saas-apps/informacast-tutorial.md) [RetrieverMediaDatabase,](../saas-apps/retrievermediadatabase-tutorial.md) [ichage,](../saas-apps/vonage-tutorial.md) [Count Me In - Operations Dashboard,](../saas-apps/count-me-in-operations-dashboard-tutorial.md) [ProProfs Knowledge Base,](../saas-apps/proprofs-knowledge-base-tutorial.md) [RightCrowd Workforce Management](../saas-apps/rightcrowd-workforce-management-tutorial.md), [JLL TRIRIGA,Stock,](../saas-apps/jll-tririga-tutorial.md) [FortiWeb Web Application Firewall,](../saas-apps/linkedin-talent-solutions-tutorial.md) [LinkedIn Talent Solutions,](../saas-apps/linkedin-talent-solutions-tutorial.md) [Equinix Federation App](../saas-apps/equinix-federation-app-tutorial.md), [KFAdvance](../saas-apps/kfadvance-tutorial.md) [](../saas-apps/shutterstock-tutorial.md)

Dokumentację wszystkich aplikacji można również znaleźć tutaj https://aka.ms/AppsTutorial

Aby uzyskać listę aplikacji w galerii aplikacji usługi Azure AD, przeczytaj szczegółowe informacje tutaj https://aka.ms/AzureADAppRequest

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>Przejdź do aplikacji Teams bezpośrednio z Mój dostęp portal

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Możliwość produktu User Access **Management:** Zarządzanie uprawnieniami

Teraz możesz uruchomić aplikację Teams bezpośrednio z Mój dostęp Portal. W tym celu zaloguj się do usługi [Mój dostęp](https://myaccess.microsoft.com/), przejdź do menu Pakiety **dostępu,** a następnie przejdź do karty **Aktywne,** aby wyświetlić wszystkie pakiety dostępu, do których już masz dostęp. Po rozwinięciu pakietu dostępu i umieszczeniu wskaźnika myszy na aplikacji Teams możesz go uruchomić, klikając **przycisk** Otwórz. 

Aby dowiedzieć się więcej na temat korzystania z portalu Mój dostęp, przejdź do tematu Request access access package in Azure AD entitlement management (Żądanie dostępu do pakietu dostępu w usłudze [Azure AD entitlement Management).](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal)

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Publiczna wersja zapoznawcza — menedżer drugiego poziomu może być ustawiony jako alternatywna osoba zatwierdzająca

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie dostępem użytkowników  
**Możliwości produktu:** Zarządzanie uprawnieniami

W procesie zatwierdzania w zarządzaniu uprawnieniami jest teraz dostępna dodatkowa opcja. W przypadku wybrania menedżera jako osoby zatwierdzającej dla pierwszej osoby zatwierdzającej będziesz mieć inną opcję, Menedżer drugiego poziomu jako alternatywną osoba zatwierdzającą, do wyboru w polu alternatywnej osoby zatwierdzającej. Po wybraniu tej opcji należy dodać osoby zatwierdzającej rezerwę, aby przesyłać dalej żądanie do w przypadku, gdy system nie może znaleźć menedżera drugiego poziomu.

Aby uzyskać więcej informacji, przejdź do tematu Zmienianie ustawień [zatwierdzania dla pakietu dostępu w usłudze Azure AD entitlement management](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).

--- 

## <a name="november-2020"></a>Listopad 2020 r.

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Azure Active Directory 1.0, TLS 1.1 i 3DES

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Wszystkie aplikacje usługi Azure AD  
**Możliwości produktu:** Standardów

Azure Active Directory 30 czerwca 2021 r. następujące protokoły Azure Active Directory regionach na całym świecie:

- TLS 1.0
- TLS 1.1
- Zestaw szyfrowania 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Środowiska, których to dotyczy:
- Komercyjna chmura platformy Azure
- Office 365 GCC i WW

Powiązane ogłoszenie Wszystkie kombinacje klient-serwer i serwer przeglądarki powinny używać TLS 1.2 i nowoczesnych mechanizmów szyfrowania w celu utrzymania bezpiecznego połączenia z usługami Azure Active Directory dla platformy Azure, Office 365 i Microsoft 365. Ta zmiana jest powiązana z [Azure Active Directory TLS 1.0 & 1.1 i 3DES Cipher Suite Deprecation in US Gov Cloud (Deprecation](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud)in US Gov Cloud).

Aby uzyskać wskazówki dotyczące usuwania przestarzałych zależności protokołów, zapoznaj się z tematem Włączanie obsługi protokołu TLS 1.2 w środowisku dla wersji 1.1 i 1.0 usługi Azure AD w celu cofania obsługi protokołu [TLS 1.1 i 1.0.](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — listopad 2020 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja z produktami innych firm

W listopadzie 2020 r. dodaliśmy następujące 52 nowe aplikacje w galerii aplikacji z obsługą federacji:

[Travel & Expense Management](https://app.expenseonce.com/Account/Login), [Dobędzie,](../saas-apps/tribeloo-tutorial.md)Selektor [](../saas-apps/crises-control-tutorial.md)plików typu [Itslearning,](https://pmteam.itslearning.com/)Kontrola śledzenia, [CourtAlert,](https://www.courtalert.com/) [StealthMail,](https://stealthmail.com/) [Edmentum — Study Island,](https://app.studyisland.com/cfw/login/)Virtual Risk [Manager,](../saas-apps/virtual-risk-manager-tutorial.md) [TIMU,](../saas-apps/timu-tutorial.md) [Looker Analytics Platform,](../saas-apps/looker-analytics-platform-tutorial.md) [Talview - Recruit,](https://recruit.talview.com/login)Translator w czasie rzeczywistym Zaewidencjonu [, Podbean](../saas-apps/podbean-tutorial.md), [zcal](https://zcal.co/signup), [expensemanager](https://api.expense-manager.com/), [Netsparker Enterprise,](../saas-apps/netsparker-enterprise-tutorial.md) [En-trak Tenant Experience Platform](https://portal.en-trak.app/), [Appian,](../saas-apps/appian-tutorial.md) [Panorays,](../saas-apps/panorays-tutorial.md) [Builterra,](https://portal.builterra.com/) [CHECK-in EV](https://my.evacheckin.com/organization), [HowNow WebApp SSO](../saas-apps/hownow-webapp-sso-tutorial.md), [Coupa Risk Assess](../saas-apps/coupa-risk-assess-tutorial.md), [Lucid (wszystkie produkty),](../saas-apps/lucid-tutorial.md) [GoBright](https://portal.brightbooking.eu/), [SailPoint IdentityNow,](../saas-apps/sailpoint-identitynow-tutorial.md)Resource[Central,](../saas-apps/resource-central-tutorial.md) [UiPathStudioO365App,](https://www.uipath.com/product/platform) [Jedox,](../saas-apps/jedox-tutorial.md) [Cequence Application](../saas-apps/cequence-application-security-tutorial.md)Security, [PerimeterX,](../saas-apps/perimeterx-tutorial.md) [TrendMiner,](../saas-apps/trendminer-tutorial.md) [Lexion](../saas-apps/lexion-tutorial.md), [WorkWare,](../saas-apps/workware-tutorial.md) [ProdPad,](../saas-apps/prodpad-tutorial.md) [AWS ClientVPN,](../saas-apps/aws-clientvpn-tutorial.md) [AppSec Flow SSO,](../saas-apps/appsec-flow-sso-tutorial.md) [Luum,](../saas-apps/luum-tutorial.md)Freight [Measure](https://www.gpcsl.com/freight.html), [Terraform Cloud,](../saas-apps/terraform-cloud-tutorial.md)Nature [Research](../saas-apps/nature-research-tutorial.md), [Play Digital Signage](https://login.playsignage.com/login), [](https://access.klaxoon.com/login) [RemotePC,](../saas-apps/remotepc-tutorial.md) [Prolorus,](../saas-apps/prolorus-tutorial.md) [Hirebridge ATS,](../saas-apps/hirebridge-ats-tutorial.md) [Teamgage,](https://www.teamgage.com/Account/ExternalLoginAzure) [Roadmunk,](../saas-apps/roadmunk-tutorial.md) [<4> Software Relations CRM](https://cloud.relations-crm.com/), [Proctne,](../saas-apps/procaire-tutorial.md) [Mentor® by eDriving: Business,](https://www.edriving.com/) [Gradle Enterprise](https://gradle.com/)

Dokumentację wszystkich aplikacji można również znaleźć tutaj https://aka.ms/AppsTutorial

Aby uzyskać listę aplikacji w galerii aplikacji usługi Azure AD, przeczytaj szczegółowe informacje tutaj https://aka.ms/AzureADAppRequest

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>Publiczna wersja zapoznawcza — role niestandardowe dla aplikacji przedsiębiorstwa

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Rbac  
**Możliwości produktu:** Access Control
 
 [Niestandardowe role RBAC dla delegowanego zarządzania aplikacją przedsiębiorstwa](../roles/custom-available-permissions.md) są teraz dostępne w publicznej wersji zapoznawczej. Te nowe uprawnienia są kompilowane na podstawie ról niestandardowych do zarządzania rejestracją aplikacji, co umożliwia precyzyjne kontrolowanie dostępu administratorów. Z czasem zostaną wydane dodatkowe uprawnienia do delegowania zarządzania usługą Azure AD.

Niektóre typowe scenariusze delegowania:
- przypisanie użytkowników i grup, które mogą uzyskać dostęp do aplikacji logowania pojedynczego opartych na samL
- tworzenie aplikacji z galerii usługi Azure AD
- aktualizowanie i odczytywanie podstawowych konfiguracji saml dla aplikacji do logowania pojedynczego opartych na forach SAML
- zarządzanie certyfikatami podpisywania dla aplikacji logowania pojedynczego opartych na saml
- aktualizacja wygasających adresów e-mail powiadomień o certyfikatach logowania dla aplikacji do logowania się pojedynczego opartych na saml
- aktualizacja algorytmu logowania i podpisu tokenu SAML dla aplikacji logowania pojedynczego opartych na saml
- tworzenie, usuwanie i aktualizowanie atrybutów użytkownika i oświadczeń dla aplikacji do logowania pojedynczego opartych na forach SAML
- możliwość włączanie, wyłączanie i ponowne uruchamianie zadań aprowizowania
- aktualizacje mapowania atrybutów
- możliwość odczytywania ustawień aprowowania skojarzonych z obiektem
- możliwość odczytywania ustawień aprowowania skojarzonych z jednostką usługi
- możliwość autoryzowania dostępu do aplikacji w celu aprowizowania

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>Publiczna wersja zapoznawcza — usługa Azure AD serwer proxy aplikacji natywnie obsługuje dostęp za pomocą logowania pojedynczego do aplikacji, które używają nagłówków do uwierzytelniania

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwości produktu:** Access Control
 
Azure Active Directory (Azure AD) serwer proxy aplikacji natywnie obsługuje dostęp do logowania pojedynczego do aplikacji, które używają nagłówków do uwierzytelniania. W usłudze Azure AD można skonfigurować wartości nagłówka wymagane przez aplikację. Wartości nagłówka zostaną wysłane do aplikacji za pośrednictwem serwer proxy aplikacji. Aby dowiedzieć się więcej, zobacz Oparte na [nagłówku](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md) logowanie pojedyncze dla aplikacji lokalnych przy użyciu aplikacja usługi Azure AD proxy
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>Ogólna dostępność — Azure AD B2C rejestracji i logowania za pomocą zasad niestandardowych

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2C — zarządzanie tożsamościami konsumentów  
**Możliwości produktu:** B2B/B2C

Dzięki funkcji rejestracji i logowania przy użyciu numeru telefonu deweloperzy i przedsiębiorstwa mogą zezwolić swoim klientom na logowanie się i logowanie przy użyciu hasła raz wysłanego na numer telefonu użytkownika za pośrednictwem wiadomości SMS. Ta funkcja umożliwia również klientowi zmianę numeru telefonu w przypadku utraty dostępu do telefonu. Dzięki możliwościom zasad niestandardowych deweloperzy i przedsiębiorstwa mogą komunikować swoją markę za pośrednictwem dostosowywania strony. Dowiedz się, jak skonfigurować logowanie i logowanie za pomocą zasad niestandardowych na stronie [Azure AD B2C.](../../active-directory-b2c/phone-authentication-user-flows.md)
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Nowe łączniki aprowizowania w galerii aplikacji usługi Azure AD — listopad 2020 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwości produktu:** integracja z produktami innych firm
 
Teraz możesz zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla tych nowo zintegrowanych aplikacji:

- [Zarządzanie tożsamościami Adobe](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blogin](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Contentful](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [Tic - Tac Mobile](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

Aby uzyskać więcej informacji, zobacz [Automate user provisioning to SaaS applications with Azure AD (Automatyzowanie aprowzowania użytkowników w aplikacjach SaaS za pomocą usługi Azure AD).](../app-provisioning/user-provisioning.md)
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>Publiczna wersja zapoznawcza — Sign-In wiadomości e-mail z adresami ProxyAddresses można teraz wdrażać za pośrednictwem wdrożenia etapowego

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (identyfikatory logowania)  
**Możliwości produktu:** Uwierzytelnianie użytkowników
 
Administratorzy dzierżawy mogą teraz używać etapowego wdrażania do wdrażania adresów Sign-In e-mail z adresami ProxyAddresses w określonych grupach usługi Azure AD. Może to ułatwić wypróbowanie funkcji przed jej wdrożeniem w całej dzierżawie za pośrednictwem zasad odnajdywania obszarów domowych. Instrukcje dotyczące wdrażania adresów Sign-In e-mail za pomocą proxyAddresses za pośrednictwem wdrożenia etapowego znajdują się w [dokumentacji](../authentication/howto-authentication-use-email-signin.md).
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>Ograniczona wersja zapoznawcza — diagnostyka logowania

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania
 
W początkowej wersji zapoznawczej diagnostyki logowania administratorzy mogą teraz przeglądać logowania użytkowników. Administratorzy mogą otrzymywać kontekstowe, specyficzne i istotne szczegóły oraz wskazówki dotyczące tego, co się stało podczas logowania i jak rozwiązać problemy. Diagnostyka jest dostępna zarówno na poziomie usługi Azure AD, jak i w listach Diagnozowanie i rozwiązywanie problemów z dostępem warunkowym. Scenariusze diagnostyczne uwzględnione w tej wersji to dostęp warunkowy, uwierzytelnianie wieloskładnikowe i pomyślne logowanie.

Aby uzyskać więcej informacji, zobacz [Co to jest diagnostyka logowania w usłudze Azure AD?](../reports-monitoring/overview-sign-in-diagnostics.md).
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>Ulepszone nieznane właściwości logowania

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Identity Protection  
**Możliwości produktu:** Identity Security & Protection

  Nieznane właściwości logowania zostały zaktualizowane. Klienci mogą zauważyć bardziej ryzykowne wykrywanie nieznanych właściwości logowania. Aby uzyskać więcej informacji, zobacz [Co to jest ryzyko?](../identity-protection/concept-identity-protection-risks.md)
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>Odświeżanie agenta aprowowania w chmurze w publicznej wersji zapoznawczej jest teraz dostępne (wersja: 1.1.281.0)

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Aprowizowanie w chmurze w usłudze Azure AD  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
Agent aprowizowania w chmurze został opublikowany w publicznej wersji zapoznawczej i jest teraz dostępny za pośrednictwem portalu. Ta wersja zawiera kilka ulepszeń, w tym obsługę gmsa dla domen, które zapewniają lepsze zabezpieczenia, ulepszone cykle synchronizacji początkowej i obsługę dużych grup. Aby uzyskać więcej informacji, zapoznaj się [z historią](../app-provisioning/provisioning-agent-release-version-history.md) wersji. 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>Punkt końcowy interfejsu API klucza odzyskiwania funkcji BitLocker jest teraz w obszarze /informationProtection

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie dostępem do urządzeń  
**Możliwości produktu:** Zarządzanie cyklem życia urządzenia
 
Wcześniej można było odzyskać klucze funkcji BitLocker za pośrednictwem punktu końcowego /bitlocker. Ten punkt końcowy zostanie ostatecznie wycofany, a klienci powinni zacząć korzystać z interfejsu API, który znajduje się teraz w obszarze /informationProtection. 

Zobacz [Interfejs API odzyskiwania funkcji BitLocker,](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta&preserve-view=true) aby uzyskać informacje o aktualizacjach dokumentacji w celu odzwierciedlenia tych zmian.

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>Ogólna dostępność serwer proxy aplikacji dla klienta Usługi pulpitu zdalnego HTML5

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwości produktu:** Access Control
 
Usługa Azure AD serwer proxy aplikacji dla klienta Usługi pulpitu zdalnego sieci Web (RDS) jest teraz ogólnie dostępne. Klient sieci Web usług pulpitu zdalnego umożliwia użytkownikom dostęp do infrastruktury Pulpit zdalny za pośrednictwem dowolnej przeglądarki z możliwością obsługi HTLM5, takiej jak Microsoft Edge, Internet Explorer 11, Google Chrome i tak dalej. Użytkownicy mogą wchodzić w interakcje ze zdalnymi aplikacjami lub pulpitami, tak jak z urządzeniem lokalnym z dowolnego miejsca. 

Za pomocą usługi Azure AD serwer proxy aplikacji można zwiększyć bezpieczeństwo wdrożenia usług pulpitu zdalnego przez wymuszenie wstępnego uwierzytelniania i zasad dostępu warunkowego dla wszystkich typów rozbudowanych aplikacji klienckich. Aby dowiedzieć się więcej, zobacz [Publish Pulpit zdalny with Azure AD serwer proxy aplikacji (Publikowanie](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md) aplikacji za pomocą usługi Azure AD serwer proxy aplikacji
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>Nowa rozszerzona usługa grupy dynamicznej jest w publicznej wersji zapoznawczej

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie grupą  
**Możliwości produktu:** Współpracy
 
Rozszerzona dynamiczna usługa grupy jest teraz w publicznej wersji zapoznawczej. Nowi klienci, którzy tworzą grupy dynamiczne w swoich dzierżawach, będą korzystać z nowej usługi. Czas wymagany do utworzenia grupy dynamicznej będzie proporcjonalny do rozmiaru tworzonej grupy, a nie rozmiaru dzierżawy. Ta aktualizacja znacznie poprawi wydajność dużych dzierżaw, gdy klienci tworzą mniejsze grupy. 

Nowa usługa ma również na celu ukończenie dodatku i usuwania członków z powodu zmian atrybutów w ciągu kilku minut. Ponadto pojedyncze błędy przetwarzania nie blokują przetwarzania dzierżawy. Aby dowiedzieć się więcej na temat tworzenia grup dynamicznych, zobacz naszą [dokumentację](../enterprise-users/groups-create-rule.md).
 
---
## <a name="october-2020"></a>Październik 2020 r.

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Lokalni agenci hybrydowi usługi Azure AD, na które mają wpływ zmiany certyfikatu TLS platformy Azure

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** N/a  
**Możliwości produktu:** Platformy

Firma Microsoft aktualizuje usługi platformy Azure w celu używania certyfikatów TLS z innego zestawu głównych urzędów certyfikacji (CA). Ta aktualizacja jest spowodowana tym, że bieżące certyfikaty urzędu certyfikacji nie spełniają jednego z wymagań dotyczących punktów odniesienia dla urzędu certyfikacji/przeglądarki. Ta zmiana będzie mieć wpływ na agentów hybrydowych usługi Azure AD zainstalowanych lokalnie, którzy mają wzmocnione środowiska ze stałą listą certyfikatów głównych i muszą zostać zaktualizowani, aby ufać nowym wystawcom certyfikatów.

Ta zmiana spowoduje przerwy w działaniu usługi, jeśli nie podejmiesz natychmiastowej akcji. Agenci ci obejmują łączniki usługi serwer proxy aplikacji umożliwiające zdalny dostęp do lokalnych agentów uwierzytelniania [passthrough,](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) które umożliwiają użytkownikom logowanie się do aplikacji przy użyciu tych samych haseł, [oraz](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) agentów usługi [Cloud Provisioning w](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) wersji zapoznawczej, którzy wykonują synchronizację usługi AD z usługą Azure AD. 

Jeśli masz środowisko z regułami zapory ustawionymi tak, aby zezwalać na połączenia wychodzące tylko do pobierania określonych list odwołania certyfikatów (CRL), musisz zezwolić na następujące adresy URL CRL i OCSP. Aby uzyskać szczegółowe informacje na temat zmiany oraz adresów URL listy CRL i OCSP, do których należy włączyć dostęp, zobacz [Azure TLS certificate changes (Zmiany certyfikatów TLS platformy Azure).](../../security/fundamentals/tls-certificate-changes.md)

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>Zdarzenia aprowowania zostaną usunięte z dzienników inspekcji i opublikowane wyłącznie w dziennikach aprowrowi

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania
 
Działania usługi [aprowowania](../app-provisioning/user-provisioning.md) SCIM są rejestrowane zarówno w dziennikach inspekcji, jak i dziennikach aprowizowania. Obejmuje to działania, takie jak tworzenie użytkownika w usłudze ServiceNow, grupowanie w aplikacji GSuite lub importowanie roli z usług AWS. W przyszłości te zdarzenia będą publikowane tylko w dziennikach aprowizowania. Ta zmiana jest wdrażana w celu uniknięcia duplikowania zdarzeń w dziennikach i dodatkowych kosztów ponoszonych przez klientów korzystających z dzienników w analizie dzienników. 

Po zakończeniu daty udostępnimy aktualizację. To cofanie działania nie jest planowane w roku kalendarzowym 2020. 

> [!NOTE]
> Nie ma to wpływu na żadne zdarzenia w dziennikach inspekcji poza zdarzeniami synchronizacji emitowanych przez usługę aprowizowania. Zdarzenia, takie jak tworzenie aplikacji, zasady dostępu warunkowego, użytkownik w katalogu itp., będą nadal emitowane w dziennikach inspekcji. [Dowiedz się więcej](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context).
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Lokalni agenci hybrydowi usługi Azure AD, na które mają wpływ zmiany certyfikatu usługi Azure Transport Layer Security (TLS)

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** N/a  
**Możliwości produktu:** Platformy
 
Firma Microsoft aktualizuje usługi platformy Azure w celu używania certyfikatów TLS z innego zestawu głównych urzędów certyfikacji( CA). Aktualizacja zostanie w związku z bieżącymi certyfikatami urzędu certyfikacji, które nie spełniają jednego z wymagań dotyczących punktów odniesienia dla urzędu certyfikacji/przeglądarki. Ta zmiana będzie mieć wpływ na agentów hybrydowych usługi Azure AD zainstalowanych lokalnie, którzy mają wzmocnione środowiska ze stałą listą certyfikatów głównych. Tych agentów należy zaktualizować, aby ufać nowym wystawcom certyfikatów.

Ta zmiana spowoduje przerwy w działaniu usługi, jeśli nie podejmiesz natychmiastowej akcji. Do tych agentów należą: 
- [serwer proxy aplikacji łączników](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) dostępu zdalnego do lokalnego 
- [Agenci uwierzytelniania passthrough,](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) którzy umożliwiają użytkownikom logowanie się do aplikacji przy użyciu tych samych haseł
- [Agenci aprowności w chmurze w](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) wersji zapoznawczej, którzy synchronizują usługę AD z usługą Azure AD. 

Jeśli masz środowisko z regułami zapory ustawionymi tak, aby zezwalać na połączenia wychodzące tylko do określonego pobierania listy odwołania certyfikatów (CRL), musisz zezwolić na adresy URL list CRL i OCSP. Aby uzyskać szczegółowe informacje na temat zmiany oraz adresów URL list CRL i OCSP, do których należy włączyć dostęp, zobacz [Azure TLS certificate changes (Zmiany certyfikatów TLS platformy Azure).](../../security/fundamentals/tls-certificate-changes.md)
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>Azure Active Directory usług TLS 1.0, TLS 1.1 i 3DES w usłudze US Gov Cloud

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Wszystkie aplikacje usługi Azure AD  
**Możliwości produktu:** Standardów
 
Azure Active Directory 31 marca 2021 r. przestaną być używane następujące protokoły:
- TLS 1.0
- TLS 1.1
- Zestaw szyfrowania 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Wszystkie kombinacje klient-serwer i serwer przeglądarki powinny używać szyfrowania TLS 1.2 i nowoczesnych mechanizmów szyfrowania w celu utrzymania bezpiecznego połączenia z usługami Azure Active Directory dla platformy Azure, Office 365 i Microsoft 365.

Środowiska, których dotyczy problem, to:
- Azure US Gov
- [Office 365 GCC High & DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)

Aby uzyskać wskazówki dotyczące usuwania przestarzałych zależności protokołów, zapoznaj się z tematem Włączanie obsługi protokołu TLS 1.2 w środowisku dla wersji 1.1 i 1.0 usługi Azure AD w celu cofania obsługi protokołu [TLS 1.1 i 1.0.](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment)
 
---

### <a name="assign-applications-to-roles-on-administrative-unit-and-object-scope"></a>Przypisywanie aplikacji do ról w zakresie jednostki administracyjnej i obiektu

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Rbac  
**Możliwości produktu:** Access Control
 
Ta funkcja umożliwia przypisanie aplikacji (SPN) do roli administratora w zakresie jednostki administracyjnej. Aby dowiedzieć się więcej, zobacz [Przypisywanie ról o zakresie do jednostki administracyjnej.](../roles/admin-units-assign-roles.md)

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>Teraz możesz wyłączyć i usunąć użytkowników-gości, którzy odmówią im dostępu do zasobu

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Przeglądy dostępu  
**Możliwości produktu:** Nadzór nad tożsamościami
 
Wyłączanie i usuwanie to zaawansowana kontrolka w przeglądach dostępu usługi Azure AD, która pomaga organizacjom lepiej zarządzać gośćmi zewnętrznymi w grupach i aplikacjach. Jeśli goście zostaną odmówieni podczas przeglądu **dostępu,** wyłączenie i usunięcie spowoduje automatyczne zablokowanie im możliwości logowania się przez 30 dni. Po upływie 30 dni zostaną one całkowicie usunięte z dzierżawy.

Aby uzyskać więcej informacji na temat tej funkcji, zobacz Wyłączanie i usuwanie tożsamości zewnętrznych [za pomocą przeglądów dostępu usługi Azure AD.](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews)
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>Twórcy przeglądu dostępu mogą dodawać wiadomości niestandardowe w wiadomościach e-mail do recenzentów

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Przeglądy dostępu  
**Możliwości produktu:** Nadzór nad tożsamościami
 
W ramach przeglądów dostępu w usłudze Azure AD administratorzy tworzący przeglądy mogą teraz pisać niestandardowe wiadomości do recenzentów. Recenzentzy zobaczą w wiadomości e-mail wiadomość z monitem o ukończenie przeglądu. Aby dowiedzieć się więcej na temat korzystania z tej funkcji, zobacz krok 14 w sekcji [Tworzenie co najmniej jednego przeglądu](../governance/create-access-review.md#create-one-or-more-access-reviews) dostępu.

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Nowe łączniki aprowizowania w galerii aplikacji usługi Azure AD — październik 2020 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwości produktu:** integracja z produktami innych firm
 
Teraz możesz zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla tych nowo zintegrowanych aplikacji:

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [Global Relay Identity Sync](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu zautomatyzowanej aprowzowania kont użytkowników, zobacz [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Automatyzacja aprowzowania użytkowników w aplikacjach SaaS przy użyciu usługi Azure AD).
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Asystent integracji dla Azure AD B2C

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2C — zarządzanie tożsamościami konsumentów  
**Możliwości produktu:** B2B/B2C
 
Środowisko Asystent integracji (wersja zapoznawcza) jest teraz dostępne dla Azure AD B2C Rejestracje aplikacji. To środowisko pomaga w konfigurowaniu aplikacji dla typowych scenariuszy. Dowiedz się więcej o [najlepszych rozwiązaniach i zaleceniach dotyczących platformy tożsamości firmy Microsoft.](../develop/identity-platform-integration-checklist.md)
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Wyświetlanie identyfikatora szablonu roli w interfejsie Azure Portal użytkownika

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Role platformy Azure  
**Możliwości produktu:** Access Control
 

Teraz możesz wyświetlić identyfikator szablonu każdej roli usługi Azure AD w Azure Portal. W usłudze Azure AD  **wybierz opis** wybranej roli. 

Zaleca się, aby klienci używali identyfikatorów szablonów ról w skrypcie i kodzie programu PowerShell zamiast nazwy wyświetlanej. Identyfikator szablonu roli jest obsługiwany do użycia z [obiektami directoryRoles](/graph/api/resources/directoryrole) i [roleDefinition.](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta&preserve-view=true) Aby uzyskać więcej informacji na temat identyfikatorów szablonów ról, zobacz [Role wbudowane usługi Azure AD.](../roles/permissions-reference.md)

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>Łączniki interfejsu API Azure AD B2C przepływów użytkownika rejestracji są teraz dostępne w publicznej wersji zapoznawczej

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2C — zarządzanie tożsamościami konsumentów  
**Możliwości produktu:** B2B/B2C
 

Łączniki interfejsu API są teraz dostępne do użycia z Azure Active Directory B2C. Łączniki interfejsu API umożliwiają korzystanie z internetowych interfejsów API w celu dostosowywania przepływów użytkownika rejestracji i integracji z zewnętrznymi systemami w chmurze. Łączników interfejsu API można używać do:

- Integracja z niestandardowymi przepływami pracy zatwierdzania
- Weryfikowanie danych wejściowych użytkownika
- Zastępowanie atrybutów użytkownika 
- Uruchamianie niestandardowej logiki biznesowej 

 Aby dowiedzieć się więcej, odwiedź stronę Dostosowywanie [i rozszerzanie dokumentacji dotyczącej rejestracji](../../active-directory-b2c/api-connectors-overview.md) przy użyciu łączników interfejsu API.

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>Właściwość stanu dla połączonych organizacji w zarządzaniu uprawnieniami

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Możliwości produktu **do zarządzania katalogami:** Zarządzanie uprawnieniami
 

 Wszystkie połączone organizacje będą teraz mieć dodatkową właściwość o nazwie "Stan". Stan będzie kontrolować sposób, w jaki połączona organizacja będzie używana w zasadach, które odnoszą się do "wszystkich skonfigurowanych połączonych organizacji". Wartość będzie "skonfigurowana" (co oznacza, że organizacja znajduje się w zakresie zasad, które używają klauzuli "wszystkie") lub "proponowane" (co oznacza, że organizacja nie znajduje się w zakresie).  

Ręcznie utworzone połączone organizacje będą mieć ustawienie domyślne "skonfigurowane". W międzyczasie automatycznie utworzone (utworzone za pośrednictwem zasad, które zezwalają dowolneowi użytkownikowi z Internetu na żądanie dostępu) będą domyślnie wybierane jako "proponowane".  Wszystkie połączone organizacje utworzone przed 9 września 2020 r. zostaną ustawione na "skonfigurowane". Administratorzy mogą aktualizować tę właściwość zgodnie z potrzebami. [Dowiedz się więcej](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically).
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory External Identities zaawansowane ustawienia zabezpieczeń usługi B2C w chmurze Premium

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2C — zarządzanie tożsamościami konsumentów  
**Możliwości produktu:** B2B/B2C
 
Funkcje dostępu warunkowego opartego na ryzykach i wykrywania ryzyka w uchęce Identity Protection są teraz dostępne [w Azure AD B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md). Dzięki tym zaawansowanym funkcji zabezpieczeń klienci mogą teraz:
- Skorzystaj z inteligentnych szczegółowych informacji, aby ocenić ryzyko za pomocą aplikacji B2C i kont użytkowników końcowych. Wykrycia obejmują nietypowe podróże, anonimowe adresy IP, adresy IP połączone ze złośliwym oprogramowaniem i Analiza zagrożeń w usłudze Azure AD. Dostępne są również raporty oparte na portalu i interfejsie API.
- Automatycznie usuwaj zagrożenia, konfigurując adaptacyjne zasady uwierzytelniania dla użytkowników B2C. Deweloperzy aplikacji i administratorzy mogą ograniczyć ryzyko w czasie rzeczywistym, wymagając uwierzytelniania wieloskładnikowego (MFA) lub blokując dostęp w zależności od wykrytego poziomu ryzyka użytkownika, z dodatkowymi kontrolkami dostępnymi na podstawie lokalizacji, grupy i aplikacji.
- Integracja z Azure AD B2C przepływami użytkownika i zasadami niestandardowymi. Warunki mogą być wyzwalane z wbudowanych przepływów użytkownika w Azure AD B2C lub można je włączyć do zasad niestandardowych B2C. Podobnie jak w przypadku innych aspektów przepływu użytkownika B2C, komunikaty dotyczące obsługi użytkownika końcowego można dostosować. Dostosowywanie jest zgodne z głosem organizacji, marką i alternatywami ograniczania ryzyka.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — październik 2020 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja z produktami innych firm
 
W październiku 2020 r. dodaliśmy następujące 27 nowych aplikacji w galerii aplikacji z obsługą federacji:

[Sentry,](../saas-apps/sentry-tutorial.md) [Bumblebee — Productivity Superapp,](https://app.yellowmessenger.com/user/login) [ABBYY FlexiCapture Cloud,](../saas-apps/abbyy-flexicapture-cloud-tutorial.md) [EAComposer,](../saas-apps/eacomposer-tutorial.md) [Genesys Cloud Integration for Azure,](https://apps.mypurecloud.com/msteams-integration/) [Zone Technologies Portal](https://portail.zonetechnologie.com/signin), [Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md), [Datawiza Access Broker,](https://console.datawiza.com/) [ZOKRI,](https://app.zokri.com/) [CheckCompor,](../saas-apps/checkproof-tutorial.md) [Ecochallenge.org](https://events.ecochallenge.org/users/login), [atSpoke,](http://atspoke.com/login) [Appointment Reminder](https://app.appointmentreminder.co.nz/account/login), [Cloud.Market,](https://cloud.market/) [TravelPerk,](../saas-apps/travelperk-tutorial.md) [Greetly,](https://app.greetly.com/) [OrgVitality SSO,](../saas-apps/orgvitality-sso-tutorial.md) [Web Cargo Air,](../saas-apps/web-cargo-air-tutorial.md) [Loop Flow CRM,](../saas-apps/loop-flow-crm-tutorial.md) [Starmind,](../saas-apps/starmind-tutorial.md) [Workstem,](https://hrm.workstem.com/login) [Retail Zipline,](../saas-apps/retail-zipline-tutorial.md) [Hoxhunt,](../saas-apps/hoxhunt-tutorial.md) [MEVISIO,](../saas-apps/mevisio-tutorial.md) [Samsara,](../saas-apps/samsara-tutorial.md) [Nimbus,](../saas-apps/nimbus-tutorial.md) [Pulse Secure virtual Traffic Manager](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

Dokumentację wszystkich aplikacji można również znaleźć tutaj https://aka.ms/AppsTutorial

Aby uzyskać listę aplikacji w galerii aplikacji usługi Azure AD, przeczytaj szczegółowe informacje tutaj https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>Dzienniki aprowizowania można teraz przesyłać strumieniowo do usługi Log Analytics

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania
 

Opublikuj dzienniki aprowizowania do usługi Log Analytics w celu:
- Przechowywanie dzienników aprowizowania przez ponad 30 dni
- Definiowanie niestandardowych alertów i powiadomień
- Tworzenie pulpitów nawigacyjnych w celu wizualizacji dzienników
- Wykonywanie złożonych zapytań w celu analizowania dzienników 

Aby dowiedzieć się, jak korzystać z tej funkcji, zobacz [Understand how provisioning integrates with Azure Monitor logs](../app-provisioning/application-provisioning-log-analytics.md)(Opis integracji aprow Azure Monitor dziennikach).
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>Dzienniki aprowizowania mogą być teraz przeglądane przez właścicieli aplikacji

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania
 
Teraz możesz zezwolić właścicielom aplikacji na monitorowanie aktywności przez usługę aprowizowania i rozwiązywanie problemów bez dostarczania im uprzywilejowanej roli ani tworzenia wąskiego gardła dla it. [Dowiedz się więcej](../reports-monitoring/concept-provisioning-logs.md).
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>Zmiana nazwy 10 Azure Active Directory ról

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Role platformy Azure  
**Możliwości produktu:** Access Control
 
Niektóre Azure Active Directory wbudowane (AD) mają nazwy inne niż te, które są wyświetlane w usługach centrum administracyjne platformy Microsoft 365, Portalu usługi Azure AD i Microsoft Graph. Ta niespójność może powodować problemy w zautomatyzowanych procesach. W tej aktualizacji zmieniamy nazwy 10 ról, aby były spójne. W poniższej tabeli przedstawiono nowe nazwy ról:

![Tabela przedstawiająca nazwy ról w programie MS interfejs Graph API i Azure Portal oraz proponowaną nową nazwę roli w Centrum administracyjnym M365, Azure Portal i interfejsie API.](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>Azure AD B2C przepływu kodu uwierzytelniania dla aplikacji dla uwierzytelniania spA przy użyciu msal JS 2.x

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** B2C — zarządzanie tożsamościami konsumentów  
**Możliwości produktu:** B2B/B2C
 
MSAL.js wersji 2.x obejmuje teraz obsługę przepływu kodu autoryzacji dla jednostronicowych aplikacji internetowych (SPA). Azure AD B2C aplikacja będzie teraz obsługiwać użycie typu aplikacji SPA na stronie Azure Portal oraz przepływ kodu autoryzacji usługi MSAL.js z pkce dla aplikacji jednostronicowych. Umożliwi to użytkownikom spA korzystającym z Azure AD B2C zachowanie logowania jednokrotnego w przypadku nowszej przeglądarki i stosowanie się do nowszej rekomendacji dotyczących protokołu uwierzytelniania. Wprowadzenie do [rejestrowania aplikacji jednostronicowej (SPA)](../../active-directory-b2c/tutorial-register-spa.md) w Azure Active Directory B2C samouczku.

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>Aktualizacje ustawienia Zapamiętaj uwierzytelnianie wieloskładnikowe (MFA) na zaufanym urządzeniu

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Mfa  
**Możliwości produktu:** Identity Security & Protection
 

Niedawno zaktualizowaliśmy funkcję uwierzytelniania [wieloskładnikowego (MFA)](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) na zaufanym urządzeniu, aby rozszerzyć uwierzytelnianie do 365 dni. Azure Active Directory (Azure AD) Premium mogą również używać [](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) zasad Dostęp warunkowy — częstotliwość logowania, które zapewniają większą elastyczność ustawień ponownego uwierzytelniania.

Aby uzyskać optymalne środowisko użytkownika, zalecamy użycie częstotliwości logowania dostępu warunkowego w celu wydłużenia okresu istnienia sesji na zaufanych urządzeniach, lokalizacjach lub sesjach niskiego ryzyka jako alternatywy dla ustawienia pamiętaj uwierzytelniania wieloskładnikowego na zaufanym urządzeniu. Aby rozpocząć pracę, zapoznaj się z naszymi najnowszymi wskazówkami na temat optymalizacji obsługi [ponownego uwierzytelniania.](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)

---
