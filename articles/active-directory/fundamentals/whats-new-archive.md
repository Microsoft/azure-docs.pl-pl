---
title: Archiwum nowości w programie Azure Active Directory? | Microsoft Docs
description: Informacje o nowościach w sekcji Przegląd tego zestawu zawartości zawierają działania z 6 miesięcy. Po upływie 6 miesięcy elementy są usuwane z artykułu głównego i umieszczane w tym artykule archiwum.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 3/31/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca7fda6345356568d512b396c412603cf7d837f7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532404"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Archiwum nowości w programie Azure Active Directory?

Podstawowy artykuł [Co nowego w programie Azure Active Directory?](whats-new.md) zawiera aktualizacje z ostatnich sześciu miesięcy, natomiast ten artykuł zawiera wszystkie starsze informacje.

Co nowego w programie Azure Active Directory? Informacje o wersji zawierają informacje o:

- Najnowsze wersje
- Znane problemy
- Poprawki błędów
- Funkcje uznane za przestarzałe
- Plany zmian

---

## <a name="september-2020"></a>Wrzesień 2020

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Nowe łączniki aprowizowania w galerii aplikacji usługi Azure AD — wrzesień 2020 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwości produktu:** integracja z produktami innych firm
 
Teraz możesz zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla tych nowo zintegrowanych aplikacji:

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [Chmura SAP Analytics](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Świadomość zabezpieczeń webroot](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu zautomatyzowanej aprowzowania kont użytkowników, zobacz [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Automatyzacja aprowzowania użytkowników w aplikacjach SaaS przy użyciu usługi Azure AD).
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>Odświeżanie publicznej wersji zapoznawczej aprowowania w chmurze

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Możliwość produktu do aprowowania w **chmurze usługi** Azure AD: zarządzanie cyklem życia tożsamości
 
Azure AD Connect aprowizowania w chmurze w publicznej wersji zapoznawczej zawiera dwa istotne ulepszenia opracowane na podstawie opinii klientów: 

- Środowisko mapowania atrybutów za pośrednictwem Azure Portal

    Dzięki tej funkcji administratorzy IT mogą mapować atrybuty użytkowników, grup lub kontaktów z usługi AD do usługi Azure AD przy użyciu różnych typów mapowań obecnych obecnie. Mapowanie atrybutów to funkcja używana do standaryzacji wartości atrybutów, które przepływa z usługi Active Directory do Azure Active Directory. Można określić, czy bezpośrednio mapować wartość atrybutu, jaka jest z usługi AD do usługi Azure AD, czy użyć wyrażeń do przekształcania wartości atrybutów podczas aprowizowania użytkowników. [Dowiedz się więcej](../cloud-sync/how-to-attribute-mapping.md)

- Aprowizowanie na żądanie lub środowisko użytkownika testowego

    Po zakończeniu konfigurowania konfiguracji warto sprawdzić, czy przekształcenie użytkownika działa zgodnie z oczekiwaniami, przed zastosowaniem go do wszystkich użytkowników w zakresie. Dzięki aprowizowaniu na żądanie administratorzy IT mogą wprowadzić nazwę wyróżniającą użytkownika usługi AD i sprawdzić, czy jest on synchronizowany zgodnie z oczekiwaniami. Aprowizowanie na żądanie zapewnia doskonały sposób zapewnienia, że mapowania atrybutów, które wcześniej działały zgodnie z oczekiwaniami. [Więcej informacji](../cloud-sync/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Inspekcja odzyskiwania funkcji BitLocker w usłudze Azure AD — publiczna wersja zapoznawcza

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie dostępem do urządzeń  
**Możliwości produktu:** Zarządzanie cyklem życia urządzenia
 
Gdy administratorzy IT lub użytkownicy końcowi odczytują klucz odzyskiwania funkcji BitLocker, do którego mają dostęp, Azure Active Directory teraz generuje dziennik inspekcji, który przechwytuje, kto uzyskał dostęp do klucza odzyskiwania. Ta sama inspekcja zawiera szczegółowe informacje o urządzeniu, z które został skojarzony klucz funkcji BitLocker.

Użytkownicy końcowi mogą [uzyskać dostęp do swoich kluczy odzyskiwania za pośrednictwem konta .](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key) Administratorzy IT mogą uzyskać dostęp do kluczy odzyskiwania za pośrednictwem interfejsu API klucza odzyskiwania funkcji [BitLocker w wersji beta](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta&preserve-view=true) lub za pośrednictwem portalu usługi Azure AD. Aby dowiedzieć się więcej, zobacz [Wyświetlanie lub kopiowanie kluczy funkcji BitLocker w portalu usługi Azure AD.](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys)

---

### <a name="teams-devices-administrator-built-in-role"></a>Wbudowana rola administratora urządzeń usługi Teams

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Rbac  
**Możliwości produktu:** Access Control
 
Użytkownicy z rolą [administratora urządzeń usługi Teams](../roles/permissions-reference.md#teams-devices-administrator) mogą zarządzać urządzeniami [certyfikowanymi przez](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) usługę Teams z centrum administracyjnego usługi Teams. 

Ta rola umożliwia użytkownikowi wyświetlanie wszystkich urządzeń na pierwszy rzut oka z możliwością wyszukiwania i filtrowania urządzeń. Użytkownik może również sprawdzić szczegóły każdego urządzenia, w tym zalogowanego konta oraz marki i modelu urządzenia. Użytkownik może zmienić ustawienia na urządzeniu i zaktualizować wersje oprogramowania. Ta rola nie udziela uprawnień do sprawdzania aktywności aplikacji Teams i wywołania jakości urządzenia.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>Zaawansowane możliwości zapytań dla obiektów katalogu

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** MS Graph  
**Możliwości produktu:** Środowisko dewelopera
 
Wszystkie nowe możliwości zapytań wprowadzone dla obiektów katalogu w interfejsach API usługi Azure AD są teraz dostępne w punkcie końcowym w wersji 1.0 i gotowe do produkcji. Deweloperzy mogą liczyć, wyszukiwać, filtrować i sortować obiekty katalogu oraz powiązane linki przy użyciu standardowych operatorów OData.

Aby dowiedzieć się więcej, zapoznaj się z [dokumentacją tutaj.](https://aka.ms/BlogPostMezzoGA)Możesz również wysłać opinię za pomocą tej [krótkiej ankiety.](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u)
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Publiczna wersja zapoznawcza: ciągła ocena dostępu dla dzierżawców, którzy skonfigurowali zasady dostępu warunkowego

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (identyfikatory logowania)  
**Możliwości produktu:** Identity Security & Protection
 
Ciągła ocena dostępu jest teraz dostępna w publicznej wersji zapoznawczej dla dzierżaw usługi Azure AD z zasadami dostępu warunkowego. W przypadku środowiska CAE krytyczne zdarzenia zabezpieczeń i zasady są oceniane w czasie rzeczywistym. Obejmuje to wyłączenie konta, zresetowanie hasła i zmianę lokalizacji. Aby dowiedzieć się więcej, zobacz [Ciągła ocena dostępu](../conditional-access/concept-continuous-access-evaluation.md).

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Publiczna wersja zapoznawcza: zadawanie użytkownikom żądających pakietu dostępu dodatkowych pytań w celu usprawnienia decyzji dotyczących zatwierdzenia

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie dostępem użytkowników  
**Możliwości produktu:** Zarządzanie uprawnieniami
 
Administratorzy mogą teraz wymagać, aby użytkownicy żądający pakietu dostępu odpowiadali na dodatkowe pytania poza uzasadnieniem biznesowym w portalu internetowym zarządzania uprawnieniami Mój dostęp Azure AD. Odpowiedzi użytkowników będą następnie widoczne dla osób zatwierdzających, aby ułatwić im podejmowanie bardziej precyzyjnej decyzji o zatwierdzeniu dostępu. Aby dowiedzieć się więcej, zobacz [Zbieranie dodatkowych informacji o żądaniu w celu zatwierdzenia (wersja zapoznawcza).](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview)
 
---

### <a name="public-preview-enhanced-user-management"></a>Publiczna wersja zapoznawcza: ulepszone zarządzanie użytkownikami

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie użytkownikami  
**Możliwości produktu:** Zarządzanie użytkownikami
 

Portal usługi Azure AD został zaktualizowany, aby ułatwić znajdowanie użytkowników na stronach Wszyscy użytkownicy i Usunięci użytkownicy. Zmiany w wersji zapoznawczej obejmują: 
- Bardziej widoczne właściwości użytkownika, w tym identyfikator obiektu, stan synchronizacji katalogu, typ tworzenia i wystawca tożsamości.
- Wyszukiwanie umożliwia teraz połączone wyszukiwanie nazw, wiadomości e-mail i identyfikatorów obiektów.
- Ulepszone filtrowanie według typu użytkownika (członka, gościa i brak), stanu synchronizacji katalogu, typu tworzenia, nazwy firmy i nazwy domeny.
- Nowe możliwości sortowania właściwości, takich jak nazwa, główna nazwa użytkownika i data usunięcia.
- Nowa łączna liczba użytkowników będzie aktualizowana przy użyciu dowolnych wyszukiwań lub filtrów.

Aby uzyskać więcej informacji, zobacz [Ulepszenia zarządzania użytkownikami (wersja zapoznawcza) w Azure Active Directory](../enterprise-users/users-search-enhanced.md).

---

### <a name="new-notes-field-for-enterprise-applications"></a>Nowe pole notatek dla aplikacji dla przedsiębiorstw

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Możliwość produktu **Enterprise Apps: logowanie** jednokrotne

Do aplikacji dla przedsiębiorstw można dodawać bezpłatne notatki tekstowe. Możesz dodać wszelkie istotne informacje, które ułatwiają zarządzanie aplikacjami w obszarze Aplikacje dla przedsiębiorstw. Aby uzyskać więcej informacji, zobacz Szybki start: konfigurowanie właściwości aplikacji w dzierżawie [usługi Azure Active Directory (Azure AD).](../manage-apps/add-application-portal-configure.md) 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — wrzesień 2020 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja z produktami innych firm

We wrześniu 2020 r. dodaliśmy następujące 34 nowe aplikacje w galerii aplikacji z obsługą federacji:

[VMware Horizon]()— Unified Access Gateway , Pulse Secure [PCS,](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md) [Inventory360,](../saas-apps/pulse-secure-pcs-tutorial.md) [Frontitude,](https://services.enteksystems.de/sso/microsoft/signup) [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_Server](https://zaas.zenmutech.com/user/signin), [HashData for Business,](https://hashdata.app/login.xhtml) [SecureLogin,](https://securelogin.securelogin.nu/sso/azure/login) [CyberSolutions MAILBASERS/CMSS,](../saas-apps/cybersolutions-mailbase-tutorial.md) [CyberSolutions CYBERMAIL](../saas-apps/cybersolutions-cybermail-tutorial.md)W,KsleCMMS [,Cznit Inc,](../saas-apps/glint-inc-tutorial.md) [zeroheight,](../saas-apps/zeroheight-tutorial.md) [Gender Fitness,](https://app.genderfitness.com/) [Coeo Portal](https://my.coeo.com/), [Gramatyka,](../saas-apps/grammarly-tutorial.md) [Fivetran,](../saas-apps/fivetran-tutorial.md) [Kukuus,](../saas-apps/kumolus-tutorial.md) [RSA I Suite,](../saas-apps/rsa-archer-suite-tutorial.md) [TeamzSkill](../saas-apps/teamzskill-tutorial.md), [raumfürraum,](../saas-apps/raumfurraum-tutorial.md) [Saviynt,](../saas-apps/saviynt-tutorial.md) [BizMerlinHR,](https://marketplace.bizmerlin.net/bmone/signup)Mobile [Locker,](../saas-apps/mobile-locker-tutorial.md) [Zengine,](../saas-apps/zengine-tutorial.md) [CloudCADI,](https://app.cloudcadi.com/login) [Simfoni Analytics,](https://simfonianalytics.com/accounts/microsoft/login/) [Priva Identity & Access Management,](https://my.priva.com/)Oracle [Pro,](https://www.gonitro.com/nps/product-details/downloads) [Eventfinity,](../saas-apps/eventfinity-tutorial.md) [Fexa,](../saas-apps/fexa-tutorial.md)Secured Signing Enterprise Portal , [Secured Signing Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal) [AAD Setup](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [Wistac Online,](https://wisteconline.com/auth/oidc) [Oracle PeopleSoft — Protected by F5 BIG-IP APM](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md) [](https://auth.limblecmms.com/)

Dokumentację wszystkich aplikacji można również znaleźć tutaj: https://aka.ms/AppsTutorial .

Aby uzyskać listę aplikacji w galerii aplikacji usługi Azure AD, przeczytaj szczegółowe informacje tutaj: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Nowa rola delegowania w zarządzaniu uprawnieniami w usłudze Azure AD: Menedżer przypisań pakietów dostępu

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie dostępem użytkowników  
**Możliwości produktu:** Zarządzanie uprawnieniami
 
Dodano nową rolę Menedżera przypisań pakietów dostępu do zarządzania uprawnieniami usługi Azure AD w celu zapewnienia szczegółowych uprawnień do zarządzania przypisaniami. Teraz można delegować zadania do użytkownika w tej roli, który może delegować zarządzanie przypisaniami pakietu dostępu do właściciela firmy. Jednak Menedżer przypisania pakietów dostępu nie może zmienić zasad pakietów dostępu ani innych właściwości, które są ustawiane przez administratorów. 

Dzięki tej nowej roli można korzystać z najmniejszych uprawnień wymaganych do delegowania zarządzania przypisaniami i utrzymania kontroli administracyjnej nad wszystkimi innymi konfiguracjami pakietów dostępu. Aby dowiedzieć się więcej, zobacz [Role zarządzania uprawnieniami](../governance/entitlement-management-delegate.md#entitlement-management-roles).
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Zmiany Privileged Identity Management dołączania do firmy

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Privileged Identity Management  
**Możliwości produktu:** Privileged Identity Management
 
Wcześniej dołączanie do usługi Privileged Identity Management (PIM) wymagało zgody użytkownika i przepływu dołączania w bloku usługi PIM, który obejmował rejestrację w usłudze Azure AD MFA. Dzięki ostatniej integracji środowiska usługi PIM z blokami ról i administratorów usługi Azure AD usuwamy to środowisko. Każda dzierżawa z ważną licencją P2 zostanie automatycznie doowana do usługi PIM.

Dołączanie do usługi PIM nie ma bezpośredniego negatywnego wpływu na dzierżawę. Można oczekiwać następujących zmian:
- Dodatkowe opcje przypisania, takie jak aktywne lub kwalifikujące się wraz z czasem rozpoczęcia i zakończenia podczas przypisywania w bloku role i administratorzy usługi PIM lub Azure AD. 
- Dodatkowe mechanizmy zakresu, takie jak jednostki administracyjne i role niestandardowe, wprowadzone bezpośrednio do funkcji przypisywania. 
- Jeśli jesteś administratorem globalnym lub administratorem ról uprzywilejowanych, możesz zacząć otrzymywać kilka dodatkowych wiadomości e-mail, takich jak cotygodniowe podsumowanie usługi PIM. 
- W dzienniku inspekcji może być również widać jednostkę usługi ms-pim powiązaną z przypisaniem roli. Ta oczekiwana zmiana nie powinna mieć wpływu na zwykły przepływ pracy.

 Aby uzyskać więcej informacji, zobacz [Rozpoczynanie korzystania z Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Zarządzanie uprawnieniami w usłudze Azure AD: w okienku Wybieranie zasobów pakietu dostępu są teraz domyślnie dostępne zasoby aktualnie w wybranym wykazie

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie dostępem użytkowników  
**Możliwości produktu:** Zarządzanie uprawnieniami
 

W przepływie tworzenia pakietu dostępu na karcie Role zasobów zmienia się zachowanie okienka Wybierz. Obecnie domyślne zachowanie to wyświetlanie wszystkich zasobów należących do użytkownika i zasobów dodanych do wybranego katalogu. 

To środowisko zostanie zmienione tak, aby domyślnie wyświetlać tylko zasoby aktualnie dodane w wykazie, dzięki czemu użytkownicy mogą łatwo wybierać zasoby z wykazu. Aktualizacja ta pomoże wykryć zasoby, które można dodać do pakietów dostępu, i zmniejszy ryzyko przypadkowego dodania zasobów należących do użytkownika, który nie należy do katalogu. Aby dowiedzieć się więcej, zobacz Create a new access package in Azure AD entitlement management (Tworzenie nowego pakietu [dostępu w zarządzaniu uprawnieniami w usłudze Azure AD).](../governance/entitlement-management-access-package-create.md#resource-roles)
 
---

## <a name="august-2020"></a>Sierpień 2020 r. 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Aktualizacje wymagań dotyczących zapory Serwer Multi-Factor Authentication Azure

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Mfa  
**Możliwości produktu:** Identity Security & Protection
 
Od 1 października 2020 r. Azure MFA Server wymagania dotyczące zapory będą wymagać dodatkowych zakresów adresów IP.

Jeśli w organizacji masz reguły zapory dla ruchu wychodzącego, zaktualizuj te reguły, aby serwery MFA mogą komunikować się ze wszystkimi niezbędnymi zakresami adresów IP. Zakresy adresów IP są udokumentowane w wymaganiach [dotyczących zapory Serwer Multi-Factor Authentication Azure.](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements)

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Nadchodzące zmiany w środowisku użytkownika w zabezpieczonych ocenach tożsamości

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Możliwość produktu **Identity Protection:** Identity Security & Protection

Aktualizujemy portal Identity Secure Score, aby dopasować go do zmian wprowadzonych w nowej wersji bezpiecznego wyniku [firmy](/microsoft-365/security/mtp/microsoft-secure-score-whats-new)Microsoft. 

Wersja zapoznawcza ze zmianami będzie dostępna na początku września. Zmiany w wersji zapoznawczej obejmują:
- Nazwa "Identity Secure Score" (Wynik bezpieczeństwa tożsamości) została zmieniona na "Secure Score for Identity" (Bezpieczny wynik dla tożsamości) w celu dostosowania marki do wyniku bezpieczeństwa firmy Microsoft
- Punkty znormalizowane do standardowej skali i raportowane w procentach zamiast punktów

W tej wersji zapoznawczej klienci mogą przełączać się między istniejącym i nowym środowiskom. Ta wersja zapoznawcza będzie trwała do końca listopada 2020 r. Po wersji zapoznawczej klienci będą automatycznie kierowani do nowego środowiska użytkownika.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Nowe ograniczone uprawnienia dostępu gościa w usłudze Azure AD — publiczna wersja zapoznawcza

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Access Control   
**Możliwości produktu:** Zarządzanie użytkownikami

Zaktualizowaliśmy uprawnienia na poziomie katalogu dla użytkowników-gości. Te uprawnienia umożliwiają administratorom wymaganie dodatkowych ograniczeń i kontroli dostępu użytkowników-gości zewnętrznych. Administratorzy mogą teraz dodawać dodatkowe ograniczenia dotyczące dostępu gości zewnętrznych do informacji o profilu i członkostwie użytkowników i grup. Dzięki tej funkcji publicznej wersji zapoznawczej klienci mogą zarządzać dostępem użytkowników zewnętrznych na dużą skalę, zaciemniając członkostwa w grupach, w tym ograniczając możliwość wyświetlania członkostwa w grupach, w których się znajdują.

Aby dowiedzieć się więcej, zobacz [Ograniczone uprawnienia dostępu gościa](../enterprise-users/users-restrict-guest-permissions.md) [i Domyślne uprawnienia użytkowników.](./users-default-permissions.md)
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Ogólna dostępność zapytań różnicowych dla jednostki usługi

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** MS Graph  
**Możliwości produktu:** Środowisko dewelopera
 
Microsoft Graph delta query obsługuje teraz typ zasobu w wersji 1.0:
- Jednostka usługi

Teraz klienci mogą efektywnie śledzić zmiany w tych zasobach i zapewnia najlepsze rozwiązanie do synchronizowania zmian w tych zasobach z lokalnym magazynem danych. Aby dowiedzieć się, jak skonfigurować te zasoby w zapytaniu, zobacz Use delta query to track changes in Microsoft Graph data (Używanie zapytania różnicowego do śledzenia zmian [Microsoft Graph danych).](/graph/delta-query-overview)
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Ogólna dostępność zapytań różnicowych dla oAuth2PermissionGrant

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** MS Graph  
**Możliwości produktu:** Środowisko dewelopera

Microsoft Graph delta query obsługuje teraz typ zasobu w wersji 1.0:
- OAuth2PermissionGrant

Klienci mogą teraz efektywnie śledzić zmiany w tych zasobach i zapewnia najlepsze rozwiązanie do synchronizowania zmian w tych zasobach z lokalnym magazynem danych. Aby dowiedzieć się, jak skonfigurować te zasoby w zapytaniu, zobacz Use delta query to track changes in Microsoft Graph data (Używanie zapytania różnicowego do śledzenia zmian [Microsoft Graph danych).](/graph/delta-query-overview)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — sierpień 2020 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja z produktami innych firm

W sierpniu 2020 r. dodaliśmy następujące 25 nowych aplikacji w galerii aplikacji z obsługą federacji:

[Backup365,](https://portal.backup365.io/login) [Soapbox,](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2) [Sis - Sis ,](https://almau.getalma.com/) [Enlyft Dynamics 365 Connector,](http://enlyft.com/) [Serraview Space Utilization Software Solutions,](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md) [Uniq,](https://web.uniq.app/) [Visibly,](../saas-apps/visibly-tutorial.md) [Zylo,](../saas-apps/zylo-tutorial.md) [Edmentum — Courseware Assessments Exact Path,](https://auth.edmentum.com/elf/login) [CyberLAB,](https://cyberlab.evolvesecurity.com/#/welcome) [Altamira HRM,](../saas-apps/altamira-hrm-tutorial.md) [Wire Wander,](../saas-apps/wirewheel-tutorial.md) [Zix Compliance and Capture,](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common) [Greenlight Enterprise Business Controls Platform,](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md) [Genetec,](https://www.clearance.network/) [iSAMS,](../saas-apps/isams-tutorial.md) [VeraSMART,](../saas-apps/verasmart-tutorial.md) [Amiko,](https://amiko.web.rivero.app/) [Twingate,](https://auth.twingate.com/signup) [Funnel Leasing,](https://nestiolistings.com/sso/oidc/azure/authorize/) [Scalefusion,](https://scalefusion.com/users/sign_in/) [Bpanda,](https://goto.bpanda.com/login) [Vivun Calendar Connect,](https://app.vivun.com/dashboard/calendar/connect) [FortiGate SSL VPN,](../saas-apps/fortigate-ssl-vpn-tutorial.md) [Wandera End User](https://www.wandera.com/)

Dokumentację wszystkich aplikacji można również znaleźć tutaj https://aka.ms/AppsTutorial

Aby uzyskać listę aplikacji w galerii aplikacji usługi Azure AD, przeczytaj szczegółowe informacje tutaj https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Lasy zasobów są teraz dostępne dla Azure AD DS 

**Wpisz:** Nowa kategoria **usługi funkcji:** Azure AD Domain Services   
**Możliwości produktu:** Azure AD Domain Services
 
Funkcje lasów zasobów w Azure AD Domain Services są teraz ogólnie dostępne. Teraz możesz włączyć autoryzację bez synchronizacji skrótów haseł, aby używać Azure AD Domain Services, w tym autoryzacji kart inteligentnych. Aby dowiedzieć się więcej, zobacz [Replica sets concepts and features for Azure Active Directory Domain Services (preview) (Pojęcia](../../active-directory-domain-services/concepts-replica-sets.md)i funkcje zestawów replik dla Azure Active Directory Domain Services wersji zapoznawczej).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>Regionalna obsługa replik dla Azure AD DS zarządzanych jest teraz dostępna

**Wpisz:** Nowa funkcja   
**Kategoria usługi:** Azure AD Domain Services  
**Możliwości produktu:** Azure AD Domain Services
 
Domenę zarządzaną można rozwinąć, aby mieć więcej niż jeden zestaw replik na dzierżawę usługi Azure AD. Zestawy replik można dodać do dowolnej równorzędnej sieci wirtualnej w dowolnym regionie platformy Azure, który obsługuje Azure AD Domain Services. Dodatkowe zestawy replik w różnych regionach świadczenia usługi Azure zapewniają geograficzne odzyskiwanie po awarii dla starszych aplikacji, jeśli region świadczenia usługi Azure przejdzie w tryb offline. Aby dowiedzieć się więcej, zobacz [Replica sets concepts and features for Azure Active Directory Domain Services (preview) (Pojęcia](../../active-directory-domain-services/concepts-replica-sets.md)i funkcje zestawów replik dla Azure Active Directory Domain Services wersji zapoznawczej).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Ogólna dostępność usługi Azure AD My Sign-Ins

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (identyfikatory logowania)  
**Możliwości produktu:** Środowisko użytkownika końcowego
 
Azure AD My Sign-Ins to nowa funkcja, która umożliwia użytkownikom korporacyjnym przeglądanie historii logowania w celu sprawdzenia pod uwagę wszelkich nietypowych działań. Ponadto ta funkcja umożliwia użytkownikom końcowych zgłaszanie raportów "To nie było ja" lub "To było ja" w przypadku podejrzanych działań. Aby dowiedzieć się więcej na temat korzystania z tej funkcji, zobacz Wyświetlanie i przeszukiwanie ostatnich działań logowania na stronie [Moje Sign-Ins.](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity)
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>Aprowizowanie użytkowników oparte na usłudze SAP SuccessFactors HR w usłudze Azure AD jest teraz ogólnie dostępne

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
Teraz możesz zintegrować rozwiązanie SAP SuccessFactors jako autorytatywne źródło tożsamości z usługą Azure AD i zautomatyzować cały cykl życia tożsamości przy użyciu zdarzeń kadrowych, takich jak nowe zatrudnienie i zakończenia pracy, aby zatrzymać aprowizowanie i cofanie aprowzowania kont w usłudze Azure AD. 

Aby dowiedzieć się więcej o tym, jak skonfigurować aprowizowanie ruchu przychodzącego rozwiązania SAP SuccessFactors do usługi Azure AD, zapoznaj się z samouczkiem Konfigurowanie rozwiązania SAP SuccessFactors do aprowizowania użytkowników [w usłudze Active Directory.](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Obsługa niestandardowego programu Open ID Connect interfejs Graph API MS dla Azure AD B2C

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2C — zarządzanie tożsamościami konsumentów  
**Możliwości produktu:** B2B/B2C
 
Wcześniej można było dodawać niestandardowych dostawców open ID Connect lub zarządzać nimi tylko za pośrednictwem Azure Portal. Teraz klienci Azure AD B2C dodawać je i zarządzać nimi za pośrednictwem Microsoft Graph API w wersji beta. Aby dowiedzieć się, jak skonfigurować ten zasób przy użyciu interfejsów API, zobacz [identityProvider resource type (Typ zasobu dostawcy tożsamości).](/graph/api/resources/identityprovider?view=graph-rest-beta&preserve-view=true)
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Przypisywanie wbudowanych ról usługi Azure AD do grup w chmurze

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Role usługi Azure AD  
**Możliwości produktu:** Access Control

Ta nowa funkcja umożliwia teraz przypisywanie wbudowanych ról usługi Azure AD do grup w chmurze. Możesz na przykład przypisać rolę administratora programu SharePoint do Contoso_SharePoint_Admins grupy. Możesz również użyć funkcji PIM, aby ustawić grupę jako uprawnionego członka roli, zamiast udzielać stałego dostępu. Aby dowiedzieć się, jak skonfigurować tę funkcję, zobacz Używanie grup w chmurze do zarządzania przypisaniami ról w [programie Azure Active Directory (wersja zapoznawcza).](../roles/groups-concept.md)
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Wbudowana rola lidera biznesowego usługi Insights jest teraz dostępna

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Role usługi Azure AD  
**Możliwości produktu:** Access Control
 
Użytkownicy z rolą lidera biznesowego usługi Insights mogą uzyskać dostęp do zestawu pulpitów nawigacyjnych i szczegółowych informacji za pośrednictwem aplikacji [M365 Insights.](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics) Obejmuje to pełny dostęp do wszystkich pulpitów nawigacyjnych oraz przedstawianych szczegółowych informacji i funkcji eksploracji danych. Jednak użytkownicy w tej roli nie mają dostępu do ustawień konfiguracji produktu, za co odpowiada rola administratora usługi Insights. Aby dowiedzieć się więcej na temat tej roli, zobacz [Uprawnienia ról administratorów w Azure Active Directory](../roles/permissions-reference.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Wbudowana rola administratora usługi Insights jest teraz dostępna

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Role usługi Azure AD  
**Możliwości produktu:** Access Control
 
Użytkownicy z rolą administratora usługi Insights mogą uzyskać dostęp do pełnego zestawu funkcji administracyjnych w aplikacji [M365 Insights.](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics) Użytkownik w tej roli może odczytywać informacje o katalogu, monitorować kondycję usługi, bilety pomocy technicznej plików i uzyskać dostęp do aspektów ustawień administratora usługi Insights. Aby dowiedzieć się więcej na temat tej roli, zobacz [Uprawnienia ról administratorów w Azure Active Directory](../roles/permissions-reference.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>Administrator aplikacji i administrator aplikacji w chmurze mogą zarządzać właściwościami rozszerzenia aplikacji

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Role usługi Azure AD  
**Możliwości produktu:** Access Control
 
Wcześniej tylko administrator globalny mógł zarządzać [właściwością rozszerzenia](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http&preserve-view=true). Teraz ta funkcja jest teraz w stanie również dla administratora aplikacji i administratora aplikacji w chmurze.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>Poprawka 4.6.263.0 i łączniki 1.1.1301.0 w programie MIM 2016 SP2

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Microsoft Identity Manager  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości

Pakiet zbiorczy poprawek [(kompilacja 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) jest dostępny dla wersji Microsoft Identity Manager (MIM) 2016 z dodatkiem Service Pack 2 (SP2). Ten pakiet zbiorczy zawiera aktualizacje dla składników zarządzanie certyfikatami w usłudze MIM, Menedżera synchronizacji programu MIM i usługi PAM. Ponadto kompilacja 1.1.1301.0 łączników ogólnych programu MIM zawiera aktualizacje łącznika programu Graph.

---

## <a name="july-2020"></a>Lipiec 2020 r.

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>Jako administrator IT chcę kierować aplikacje klienckie przy użyciu dostępu warunkowego

**Wpisz:** Planowanie zmian   
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Identity Security & Protection
 
Po wydaniu warunku aplikacji klienckich w wersji gachodniej w trybie dostępu warunkowego nowe zasady będą teraz domyślnie stosowane do wszystkich aplikacji klienckich. Dotyczy to również starszych klientów uwierzytelniania. Istniejące zasady pozostaną niezmienione, ale przełącznik Konfiguruj *tak/nie* zostanie usunięty z istniejących zasad, aby łatwo zobaczyć, do których aplikacji klienckich są stosowane zasady. 

Podczas tworzenia nowych zasad pamiętaj o wykluczeniu użytkowników i kont usług, które nadal będą korzystać ze starszego uwierzytelniania. Jeśli tego nie zimkniesz, zostaną one zablokowane. [Dowiedz się więcej](../conditional-access/concept-conditional-access-conditions.md).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Nadchodzące poprawki zgodności ze standardem SCIM

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
Usługa aprowizowania Azure AD korzysta ze standardu SCIM do integracji z aplikacjami. Implementacja standardu SCIM ewoluuje i oczekujemy wprowadzenia zmian w naszym zachowaniu w zakresie sposobu wykonywania operacji PATCH, a także ustawienia właściwości "aktywnej" dla zasobu. [Dowiedz się więcej](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>Ustawienie właściciela grupy w portalu administracyjnym platformy Azure zostanie zmienione

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Zarządzanie grupą  
**Możliwości produktu:** Współpracy

Ustawienia właściciela na stronie ustawienia ogólne grupy można skonfigurować tak, aby ograniczyć uprawnienia do przypisywania właściciela do ograniczonej grupy użytkowników w portalu administracyjnym platformy Azure i Panel dostępu. Wkrótce będziemy mieć możliwość przypisywania uprawnień właściciela grupy nie tylko w tych dwóch portalach środowiska użytkownika, ale także wymuszania zasad na za zaplecza w celu zapewnienia spójnego zachowania między punktami końcowymi, takimi jak program PowerShell i Microsoft Graph. 

W ciągu następnych kilku miesięcy zaczniemy wyłączać bieżące ustawienie dla klientów, którzy go nie będą używać. W ciągu następnych kilku miesięcy będzie dostępna opcja ustawienia zakresu użytkowników dla uprawnień właściciela grupy. Aby uzyskać wskazówki dotyczące aktualizowania ustawień grupy, zobacz Edytowanie informacji o grupie przy użyciu [Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Azure Active Directory usługi rejestracji kończy obsługę TLS 1.0 i 1.1

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Rejestracja urządzeń i zarządzanie nimi  
**Możliwości produktu:** Platformy

Zabezpieczenia warstwy transportowej (TLS) 1.2 oraz serwery aktualizacji i klienci wkrótce będą komunikować się z Azure Active Directory Device Registration Service. Obsługa TLS 1.0 i 1.1 komunikacji z usługą rejestracji urządzeń w usłudze Azure AD zostanie wycofana:
- 31 sierpnia 2020 r. we wszystkich suwerennych chmurach (GCC High, DoD itp.)
- 30 października 2020 r. we wszystkich chmurach komercyjnych

[Dowiedz się więcej](../devices/reference-device-registration-tls-1-2.md) o wersji TLS 1.2 dla usługi rejestracji w usłudze Azure AD.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Windows Hello dla firm logowania widoczne w dziennikach logowania usługi Azure AD

**Wpisz:** Stałe  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania
 
Windows Hello dla firm użytkownicy końcowi mogą logować się do maszyn z systemem Windows za pomocą gestu (takiego jak numer PIN lub dane biometryczne). Administratorzy usługi Azure AD mogą chcieć odróżnić logowania Windows Hello dla firm logowania systemu Windows w ramach podróży organizacji do uwierzytelniania bez hasła. 

Administratorzy mogą teraz sprawdzić, czy uwierzytelnianie systemu Windows było używane przez usługę Windows Hello dla firm, sprawdzając na karcie Szczegóły uwierzytelniania zdarzenie logowania systemu Windows w bloku Azure AD Sign-Ins w Azure Portal. Windows Hello dla firm uwierzytelnienia obejmują "WindowsHelloForBusiness" w polu Metoda uwierzytelniania. Aby uzyskać więcej informacji na temat interpretowania dzienników Sign-In, zobacz [dokumentację dzienników logowania](../reports-monitoring/concept-sign-ins.md).
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Poprawki dotyczące poprawy wydajności i zachowania usuwania grup

**Wpisz:** Stałe  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
Wcześniej, gdy grupa została zmieniona z "w zakresie" na "poza zakresem", a administrator kliknął pozycję Uruchom ponownie przed ukończeniem zmiany, obiekt grupy nie był usuwany. Teraz obiekt grupy zostanie usunięty z aplikacji docelowej, gdy wykracza poza zakres (wyłączony, usunięty, nieprzypisane lub nie przejdzie przez filtr zakresu). [Dowiedz się więcej](../app-provisioning/how-provisioning-works.md#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Publiczna wersja zapoznawcza: administratorzy mogą teraz dodawać niestandardową zawartość w wiadomości e-mail do recenzentów podczas tworzenia przeglądu dostępu

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Przeglądy dostępu  
**Możliwości produktu:** Nadzór nad tożsamościami
 
Po utworzeniu nowego przeglądu dostępu recenzent otrzymuje wiadomość e-mail z prośbą o ukończenie przeglądu dostępu. Wielu naszych klientów prosiło o możliwość dodania do wiadomości e-mail niestandardowej zawartości, takiej jak informacje kontaktowe lub inna dodatkowa zawartość dodatkowa, która będzie kierować recenzentem. 

Teraz, gdy jest dostępna w publicznej wersji zapoznawczej, administratorzy mogą określić niestandardową zawartość w wiadomościach e-mail wysyłanych do recenzentów, dodając zawartość w sekcji "zaawansowane" w sekcji Przeglądy dostępu w usłudze Azure AD. Aby uzyskać wskazówki dotyczące tworzenia przeglądów dostępu, zobacz Tworzenie przeglądu dostępu grup i aplikacji [w przeglądach dostępu usługi Azure AD.](../governance/create-access-review.md)
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Dostępny przepływ kodu autoryzacji dla aplikacji jednostronicowych

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (identyfikatory logowania)  
**Możliwości produktu:** Środowisko dewelopera
 
Ze względu na ograniczenia dotyczące plików cookie innych firm w nowoczesnych przeglądarkach, takich jak Safari ITP, firmy SPA będą musiały używać przepływu kodu autoryzacji zamiast niejawnego przepływu do obsługi logowania jednokrotnego, a program MSAL.js v 2.x będzie teraz obsługiwać przepływ kodu autoryzacji. 

Istnieją odpowiednie aktualizacje do Azure Portal więc można zaktualizować SPA jako typ "spa" i użyj przepływu kodu uwierzytelniania. Aby [uzyskać dalsze wskazówki, zobacz](../develop/quickstart-v2-javascript-auth-code.md) Logowanie użytkowników i uzyskiwanie tokenu dostępu w aplikacji SPA w języku JavaScript przy użyciu przepływu kodu uwierzytelniania.
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Usługa Azure AD serwer proxy aplikacji obsługuje teraz klienta Usługi pulpitu zdalnego web client

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwości produktu:** Access Control

Usługa Azure AD serwer proxy aplikacji obsługuje teraz klienta Usługi pulpitu zdalnego sieci Web (RDS). Klient sieci Web usług pulpitu zdalnego umożliwia użytkownikom dostęp do infrastruktury Pulpit zdalny za pośrednictwem dowolnej przeglądarki z możliwością obsługi HTLM5, takiej jak Microsoft Edge, Internet Explorer 11, Google Chrome itp. Użytkownicy mogą wchodzić w interakcje ze zdalnymi aplikacjami lub pulpitami, tak jak z urządzeniem lokalnym z dowolnego miejsca. Za pomocą usługi Azure AD serwer proxy aplikacji można zwiększyć bezpieczeństwo wdrożenia usług pulpitu zdalnego przez wymuszenie wstępnego uwierzytelniania i zasad dostępu warunkowego dla wszystkich typów rozbudowanych aplikacji klienckich. Aby uzyskać wskazówki, zobacz Publish Pulpit zdalny with Azure AD serwer proxy aplikacji (Publikowanie aplikacji za [pomocą usługi Azure AD serwer proxy aplikacji).](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>Następna generacja Azure AD B2C przepływów użytkowników w publicznej wersji zapoznawczej

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2C — zarządzanie tożsamościami konsumentów  
**Możliwości produktu:** B2B/B2C
 
Uproszczone środowisko przepływu użytkownika zapewnia parzystość funkcji z funkcjami w wersji zapoznawczej i jest home dla wszystkich nowych funkcji. Użytkownicy będą mogli włączyć nowe funkcje w ramach tego samego przepływu użytkownika, co zmniejsza potrzebę tworzenia wielu wersji przy każdej nowej wersji funkcji. Na koniec nowe, przyjazne dla użytkownika interfejs użytkownika upraszcza wybór i tworzenie przepływów użytkownika. Wypróbuj to teraz, [tworząc przepływ użytkownika](../../active-directory-b2c/tutorial-create-user-flows.md). 

Aby uzyskać więcej informacji na temat przepływów użytkowników, zobacz [Wersje przepływu użytkownika w Azure Active Directory B2C](../../active-directory-b2c/user-flow-versions.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — lipiec 2020 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja z produktami innych firm
 
W lipcu 2020 r. dodaliśmy następujące 55 nowych aplikacji w galerii aplikacji z obsługą federacji:

[Clap Your Hands](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor Vault](https://inexto.com/inexto-suite/inextor), [Beekast,](https://my.beekast.com/) [Templafy OpenID Connect,](https://app.templafy.com/) [PeterConnects,](https://msteams.peterconnects.com/) [AlohaCloud,](https://appfusions.alohacloud.com/auth) [Control Tower,](https://bpm.tnxcorp.com/sso/microsoft) [Cocoom](https://start.cocoom.com/), [CONSTRUCTION Construction Cloud](https://sso.coinsconstructioncloud.com/#login/), [Medxnote MT,](https://task.teamsmain.medx.im/authorization) [Reflekt,](https://reflekt.konsolute.com/login) [Rever,](https://app.reverscore.net/access) [MyCompanyArchive](https://login.mycompanyarchive.com/), [GReminders,](https://app.greminders.com/o365-oauth) [Titanfile,](../saas-apps/titanfile-tutorial.md) [Wootric,](../saas-apps/wootric-tutorial.md) [SolarWinds Orion,](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US)  [OpenText Directory Services,](../saas-apps/opentext-directory-services-tutorial.md) [Datasite,](../saas-apps/datasite-tutorial.md) [BlogIn](../saas-apps/blogin-tutorial.md), [IntSights,](../saas-apps/intsights-tutorial.md) [kpifire,](../saas-apps/kpifire-tutorial.md) [Textline,](../saas-apps/textline-tutorial.md) [Cloud Academy - SSO,](../saas-apps/cloud-academy-sso-tutorial.md) [Community Spark,](../saas-apps/community-spark-tutorial.md) [Chatwork,](../saas-apps/chatwork-tutorial.md) [CloudSign,](../saas-apps/cloudsign-tutorial.md) [C3M Cloud Control,](../saas-apps/c3m-cloud-control-tutorial.md) [SmartHR](https://smarthr.jp/), [NumlyEngage™](../saas-apps/numlyengage-tutorial.md), [Data Hub Single Sign-On,](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md) [Egress,](../saas-apps/egress-tutorial.md) [SendSafely,](../saas-apps/sendsafely-tutorial.md) [Eletive,](https://app.eletive.com/) [Right-Hand Cybersecurity ADI,](https://right-hand.ai/) [Fyde Enterprise Authentication,](https://enterprise.fyde.com/) [Verme,](../saas-apps/verme-tutorial.md) [Lenses.io,](../saas-apps/lensesio-tutorial.md) [Momenta,](../saas-apps/momenta-tutorial.md) [Uprise,](https://app.uprise.co/sign-in) [Q,](https://q.moduleq.com/login) [CloudCords,](../saas-apps/cloudcords-tutorial.md) [TellMe Bot,](https://tellme365liteweb.azurewebsites.net/) [Inspire,](https://app.inspiresoftware.com/) [Maverics Identity Orchestrator SAML Connector,](https://www.strata.io/identity-fabric/) [Smart school Management System,](https://smartschoolz.com/login) [Zepto — Intelligent timekeeping](https://user.zepto-ai.com/signin), [Studi.ly,](https://studi.ly/) [Trackplan,](http://www.trackplanfm.com/) [Skedda,](../saas-apps/skedda-tutorial.md) [WhosOnLocation,](../saas-apps/whos-on-location-tutorial.md) [Coggle,Gent](../saas-apps/coggle-tutorial.md) [LoadMaster,](https://kemptechnologies.com/cloud-load-balancer/) [BrowserStack Single Sign-on](../saas-apps/browserstack-single-sign-on-tutorial.md)

Dokumentację wszystkich aplikacji można również znaleźć tutaj https://aka.ms/AppsTutorial

Aby uzyskać listę aplikacji w galerii aplikacji usługi Azure AD, przeczytaj szczegółowe informacje tutaj https://aka.ms/AzureADAppRequest

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Wyświetlanie przypisań ról we wszystkich zakresach i możliwość pobierania ich do pliku CSV

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Role usługi Azure AD  
**Możliwości produktu:** Access Control
 
Teraz możesz wyświetlać przypisania ról we wszystkich zakresach dla roli na karcie "Role i administratorzy" w portalu usługi Azure AD. Możesz również pobrać te przypisania ról dla każdej roli do pliku CSV. Aby uzyskać wskazówki dotyczące wyświetlania i dodawania przypisań ról, [zobacz Wyświetlanie i przypisywanie ról administratorów w Azure Active Directory](../roles/manage-roles-portal.md).
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Obsługa tworzenia oprogramowania w usłudze Azure Multi-Factor Authentication (Azure MFA SDK)

**Wpisz:** Przestarzałe  
**Kategoria usługi:** Mfa  
**Możliwości produktu:** Identity Security & Protection
 
Program Azure Multi-Factor Authentication Software Development (Azure MFA SDK) zakończył się 14 listopada 2018 r., zgodnie z pierwszą zapowiedzią w listopadzie 2017 r. Firma Microsoft będzie zamykać usługę SDK od 30 września 2020 r. Wszystkie wywołania wykonane do zestawu SDK nie powiodą się.

Jeśli Twoja organizacja korzysta z zestawu Azure MFA SDK, musisz przeprowadzić migrację do 30 września 2020 r.:
- Zestaw SDK usługi Azure MFA dla programu MIM: w przypadku używania zestawu SDK z programem MIM należy przeprowadzić migrację do usługi Azure MFA Server i aktywować usługę Privileged Access Management (PAM) zgodnie z tymi [instrukcjami.](/microsoft-identity-manager/working-with-mfaserver-for-mim)   
- Zestaw SDK usługi Azure MFA dla niestandardowych aplikacji: rozważ zintegrowanie aplikacji z usługą Azure AD i użycie dostępu warunkowego w celu wymuszenia uwierzytelniania wieloskładnikowego. Aby rozpocząć, przejrzyj tę [stronę.](../manage-apps/plan-an-application-integration.md) 

---

## <a name="june-2020"></a>Czerwiec 2020 r. 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Warunek ryzyka związanego z użytkownikiem w zasadach dostępu warunkowego

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Identity Security & Protection
 

Obsługa ryzyka związanego z użytkownikiem w zasadach dostępu warunkowego usługi Azure AD umożliwia tworzenie wielu zasad opartych na ryzyku użytkowników. Różne minimalne poziomy ryzyka użytkownika mogą być wymagane dla różnych użytkowników i aplikacji. Na podstawie ryzyka związanego z użytkownikiem można tworzyć zasady blokujące dostęp, wymagające uwierzytelniania wieloskładnikowego, bezpiecznej zmiany hasła lub przekierowywania do usługi Microsoft Cloud App Security w celu wymuszenia zasad sesji, takich jak dodatkowa inspekcja.

Warunek ryzyka użytkownika wymaga Azure AD — wersja Premium P2, ponieważ korzysta z usługi Azure Identity Protection, która jest ofertą P2. Aby uzyskać więcej informacji na temat dostępu warunkowego, zapoznaj się z dokumentacją [dostępu warunkowego usługi Azure AD.](../conditional-access/index.yml)

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>Logowanie jednokrotne SAML obsługuje teraz aplikacje, które wymagają ustawienia SPNameQualifier na żądanie

**Wpisz:** Stałe  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej
 
Niektóre aplikacje SAML wymagają zwrócenia nazwy SPNameQualifier w temacie asercji na żądanie. Teraz usługa Azure AD odpowiada prawidłowo, gdy w zasadach NameID żądania jest żądany parametr SPNameQualifier. Działa to również w przypadku logowania inicjowanego przez spedycyjną platformę , a logowanie inicjowane przez idP będzie zgodne z tym procesem.  Aby dowiedzieć się więcej na temat protokołu SAML w Azure Active Directory, zobacz Single Sign-On SAML protocol (Protokół SAML Sign-On [saml).](../develop/single-sign-on-saml-protocol.md)

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Usługa Azure AD B2B Collaboration obsługuje zapraszanie użytkowników msa i Google w Azure Government dzierżawach

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C
 

Azure Government dzierżawców korzystających z funkcji współpracy B2B mogą teraz zapraszać użytkowników, którzy mają konto Microsoft lub Google. Aby dowiedzieć się, czy dzierżawca może korzystać z tych funkcji, postępuj zgodnie z instrukcjami w tesłudze Jak sprawdzić, czy współpraca [B2B](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant) jest dostępna w dzierżawie platformy Azure dla instytucji rządowych USA?

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>Obiekt użytkownika w programie MS Graph w wersji 1 zawiera teraz właściwości externalUserState i externalUserStateChangedDateTime

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C
 

Właściwości externalUserState i externalUserStateChangedDateTime mogą służyć do znalezienia zaproszonych gości B2B, którzy jeszcze nie zaakceptowali zaproszeń, a także do automatyzacji kompilacji, takiej jak usuwanie użytkowników, którzy nie zaakceptowali zaproszeń po upływie kilku dni. Te właściwości są teraz dostępne w programie MS Graph w wersji 1. Aby uzyskać wskazówki dotyczące używania tych właściwości, zobacz [User resource type (Typ zasobu użytkownika).](/graph/api/resources/user)
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Zarządzanie sesjami uwierzytelniania w dostępie warunkowym usługi Azure AD jest teraz ogólnie dostępne

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Identity Security & Protection
 
Funkcje zarządzania sesjami uwierzytelniania umożliwiają skonfigurowanie, jak często użytkownicy muszą poświadczeń logowania oraz czy po zamknięciu i ponownym otwarciu przeglądarek muszą podać poświadczenia, aby zapewnić większe bezpieczeństwo i elastyczność w środowisku.
 
Ponadto zarządzanie sesjami uwierzytelniania było stosowane tylko do uwierzytelniania First Factor Authentication na urządzeniach przyłączone do usługi Azure AD, przyłączone hybrydowo do usługi Azure AD i zarejestrowane w usłudze Azure AD. Teraz zarządzanie sesją uwierzytelniania będzie również stosowane do uwierzytelniania wieloskładnikowego. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zarządzania sesją uwierzytelniania przy użyciu dostępu warunkowego.](../conditional-access/howto-conditional-access-session-lifetime.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — czerwiec 2020 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja z produktami innych firm
 
W czerwcu 2020 r. dodaliśmy następujące 29 nowych aplikacji w galerii aplikacji z obsługą federacji:

[Shopify Plus,](../saas-apps/shopify-plus-tutorial.md) [Ekarda,](../saas-apps/ekarda-tutorial.md) [MailGates,](../saas-apps/mailgates-tutorial.md) [DosyćTDP,](../saas-apps/bullseyetdp-tutorial.md) [Raketa,](../saas-apps/raketa-tutorial.md) [Segment,](../saas-apps/segment-tutorial.md) [Ai Auditor,](https://www.mindbridge.ai/products/ai-auditor/) [Pobuca Connect](https://app.pobu.ca/), [Proto.io](../saas-apps/proto.io-tutorial.md), [Gatekeeper,](https://www.gatekeeperhq.com/) [Hub Planner](../saas-apps/hub-planner-tutorial.md), [Ansira-Partner Go-to-Market Toolbox,](https://ansira.com/technology/channel-engagement) [IBM Digital Business Automation on Cloud](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md), [Kisi Physical Security,](../saas-apps/kisi-physical-security-tutorial.md) [ViewpointOne,](https://team.viewpoint.com/) [IntelligenceBank](../saas-apps/intelligencebank-tutorial.md), [pmetrics,](../saas-apps/pymetrics-tutorial.md) [Zero,](https://www.teamzero.com/) [InStation,](https://instation.invillia.com/) [edX for Business SAML 2.0 Integration](../saas-apps/edx-for-business-saml-integration-tutorial.md), [MOOC Office 365,](https://mooc.office365-training.com/en/) [SmartKargo,](../saas-apps/smartkargo-tutorial.md) [PKIsigning platform,](https://platform.pkisigning.nl/) [SiteIntel,](../saas-apps/siteintel-tutorial.md) [Field iD,](../saas-apps/field-id-tutorial.md) [Curricula SAML,](../saas-apps/curricula-saml-tutorial.md) [Perforce Helix Core - Helix Authentication Service,](../saas-apps/perforce-helix-core-tutorial.md) [MyCompliance Cloud,](https://cloud.metacompliance.com/) [Smallstep SSH](https://smallstep.com/sso-ssh/)  

Dokumentację wszystkich aplikacji można również znaleźć https://aka.ms/AppsTutorial tutaj. Aby uzyskać listę aplikacji w galerii aplikacji usługi Azure AD, przeczytaj szczegółowe informacje tutaj: https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>Łączniki interfejsu API External Identities samoobsługowej rejestracji są teraz dostępne w publicznej wersji zapoznawczej

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C
 
External Identities API umożliwiają korzystanie z internetowych interfejsów API w celu integracji samoobsługowej rejestracji z zewnętrznymi systemami w chmurze. Oznacza to, że teraz można wywoływać internetowe interfejsy API jako określone kroki w przepływie rejestracji w celu wyzwolenia niestandardowych przepływów pracy opartych na chmurze. Łączniki interfejsu API mogą na przykład umożliwiać:

- Integracja z niestandardowymi przepływami pracy zatwierdzania.
- Wykonywanie weryfikacji tożsamości
- Weryfikowanie danych wejściowych użytkownika
- Zastępowanie atrybutów użytkownika
- Uruchamianie niestandardowej logiki biznesowej

Aby uzyskać więcej informacji na temat wszystkich możliwych funkcji łączników interfejsu [API,](../external-identities/api-connectors-overview.md)zobacz Dostosowywanie i rozszerzanie możliwości samoobsługowej rejestracji przy użyciu łączników interfejsu API lub Dostosowywanie samoobsługowej rejestracji za pomocą integracji interfejsu [External Identities](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin)API sieci Web.
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>Aprowizuj na żądanie i dostąp użytkowników do aplikacji w ciągu kilku sekund

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
Usługa aprowizowania Usługi Azure AD działa obecnie cyklicznie. Usługa jest uruchamiana co 40 minut. Funkcja [aprowizowania na](https://aka.ms/provisionondemand) żądanie umożliwia wybór użytkownika i aprowizowanie go w ciągu kilku sekund. Ta funkcja umożliwia szybkie rozwiązywanie problemów z aprowizowaniem bez konieczności ponownego uruchamiania w celu wymusenia ponownego uruchomienia cyklu aprowowania. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Nowe uprawnienie do korzystania z zarządzania uprawnieniami usługi Azure AD w programie Graph

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Zarządzanie uprawnieniami
 
Nowe uprawnienia delegowane EntitlementManagement.Read.All są teraz dostępne do użycia z uprawnieniami interfejs API Zarządzanie w Microsoft Graph beta. Aby dowiedzieć się więcej na temat dostępnych interfejsów API, zobacz Praca z interfejsem API zarządzania [uprawnieniami usługi Azure AD.](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta&preserve-view=true)

---

### <a name="identity-protection-apis-available-in-v10"></a>Interfejsy API ochrony tożsamości dostępne w wersji 1.0

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Identity Protection  
**Możliwości produktu:** Identity Security & Protection
 
RyzykowniUżytkownicy i ryzykoWydajne Microsoft Graph API są teraz ogólnie dostępne. Teraz, gdy są one dostępne w punkcie końcowym w wersji 1.0, zachęcamy do korzystania z nich w środowisku produkcyjnym. Aby uzyskać więcej informacji, zapoznaj się z [Microsoft Graph docs.](/graph/api/resources/identityprotectionroot)
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Etykiety czułości do stosowania zasad do Microsoft 365 są teraz ogólnie dostępne

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupą  
**Możliwości produktu:** Współpracy
 

Teraz możesz tworzyć etykiety poufności i używać ich do stosowania zasad do grup Microsoft 365, w tym zasad prywatności (publicznych lub prywatnych) i zewnętrznych zasad dostępu użytkowników. Możesz utworzyć etykietę z zasadami ochrony prywatności prywatnymi i zewnętrznymi zasadami dostępu użytkowników, aby nie zezwalać na dodawanie użytkowników-gości. Gdy użytkownik stosuje tę etykietę do grupy, grupa będzie prywatna i żaden użytkownik-gość nie będzie mógł zostać dodany do grupy. 

Etykiety czułości są ważne, aby chronić dane krytyczne dla działalności firmy i umożliwiać zarządzanie grupami na dużą skalę w sposób zgodny i bezpieczny. Aby uzyskać wskazówki dotyczące używania etykiet czułości, zobacz Assign sensitivity labels to Microsoft 365 groups in Azure Active Directory (preview) (Przypisywanie etykiet czułości [do Azure Active Directory (wersja zapoznawcza).](../enterprise-users/groups-assign-sensitivity-labels.md)
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Aktualizacje pomocy technicznej dotyczące Microsoft Identity Manager dla Azure AD — wersja Premium klientów

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Microsoft Identity Manager  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
pomoc techniczna platformy Azure jest teraz dostępna dla składników integracji usługi Azure AD w wersji Microsoft Identity Manager 2016 do końca okresu rozszerzonej pomocy technicznej dla Microsoft Identity Manager 2016 r. Przeczytaj więcej na [temat aktualizacji pomocy technicznej dla Azure AD — wersja Premium korzystających z Microsoft Identity Manager](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>Użycie warunków członkostwa w grupie w konfiguracji oświadczeń logowania jednokrotnego jest zwiększone

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej
 
Wcześniej liczba grup, których można było użyć podczas warunkowej zmiany oświadczeń na podstawie członkostwa w grupie w ramach dowolnej konfiguracji pojedynczej aplikacji, była ograniczona do 10. Użycie warunków członkostwa w grupie w konfiguracji oświadczeń logowania jednokrotnego zostało teraz zwiększone do maksymalnie 50 grup. Aby uzyskać więcej informacji na temat konfigurowania oświadczeń, zapoznaj się z konfiguracją [oświadczeń logowania jednokrotnego](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions)aplikacji dla przedsiębiorstw. 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Włączanie podstawowego formatowania w składniku Tekst strony logowania w znakowanie firmowe.

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnienia (identyfikatory logowania)  
**Możliwości produktu:** Uwierzytelnianie użytkownika
 
Funkcja znakowania firmowego w usłudze Azure AD/Microsoft 365 została zaktualizowana, aby umożliwić klientowi dodawanie hiperlinków i prostego formatowania, w tym pogrubienia czcionki, podkreślenia i kursyw. Aby uzyskać wskazówki dotyczące korzystania z tej funkcji, zobacz Dodawanie [brandingu do](./customize-branding.md)strony logowania Azure Active Directory organizacji.

---

### <a name="provisioning-performance-improvements"></a>Ulepszenia wydajności aprowingu

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
Usługa aprowizowania została zaktualizowana w celu skrócenia czasu ukończenia cyklu [przyrostowego.](../app-provisioning/how-provisioning-works.md#incremental-cycles) Oznacza to, że użytkownicy i grupy będą aprowizowane w swoich aplikacjach szybciej niż wcześniej. Wszystkie nowe zadania aprowowania utworzone po 10.06.2020 r. będą automatycznie korzystać z ulepszeń wydajności. Wszystkie aplikacje skonfigurowane do aprowizowania przed 10.06.2020 r. będą wymagać ponownego uruchomienia raz po 10.06.2020 r., aby skorzystać z ulepszeń wydajności. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Announcing the deprecation of ADAL and MS Graph Parity

**Wpisz:** Przestarzałe  
**Kategoria usługi:** N/a  
**Możliwości produktu:** Zarządzanie cyklem życia urządzeń

Teraz, gdy biblioteki uwierzytelniania firmy Microsoft (MSAL) są dostępne, nie będziemy już dodawać nowych funkcji do bibliotek Azure Active Directory Authentication Libraries (ADAL) i zakończymy poprawki zabezpieczeń 30 czerwca 2022 r. Aby uzyskać więcej informacji na temat migracji do biblioteki MSAL, zobacz [Migrowanie aplikacji do biblioteki Microsoft Authentication Library (MSAL).](../develop/msal-migration.md)

Ponadto zakończyliśmy pracę nad tym, aby udostępnić wszystkie funkcje programu Azure AD Graph za pośrednictwem programu MS Graph. Dlatego interfejsy API programu Graph usługi Azure AD będą otrzymywać tylko poprawki błędów i poprawki zabezpieczeń do 30 czerwca 2022 r. Aby uzyskać więcej informacji, zobacz Update your applications to use Microsoft Authentication Library and Microsoft Graph API (Aktualizowanie aplikacji w [celu używania biblioteki uwierzytelniania](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363) firmy Microsoft i Microsoft Graph API)
 
---
## <a name="may-2020"></a>Maj 2020 r.

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>Wycofanie właściwości w interfejsach API signIns, riskyUsers i riskDetections

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Identity Protection  
**Możliwości produktu:** Identity Security & Protection

Obecnie typy wyliczane są używane do reprezentowania właściwości riskType zarówno w interfejsie API riskDetections, jak i riskyUserHistoryItem (w wersji zapoznawczej). Typy wyliczane są również używane dla właściwości riskEventTypes w interfejsie API signIns. W przyszłości te właściwości będą reprezentowane jako ciągi. 

Klienci powinni przejść do właściwości riskEventType w interfejsie API riskDetections i riskyUserHistoryItem oraz do właściwości riskEventTypes_v2 w interfejsie API logowania w wersji beta do 9 września 2020 r. W tym dniu wycofamy bieżące właściwości riskType i riskEventTypes. Aby uzyskać więcej informacji, zapoznaj się z [tematami Changes to risk event properties (Zmiany właściwości](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/)zdarzenia ryzyka) i Identity Protection APIs on Microsoft Graph (

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Cofanie pracy z właściwością riskEventTypes w interfejsie API signIns w wersji 1.0 na Microsoft Graph

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Identity Security & Protection

Typy wyliczane zostaną przełączone na typy ciągów podczas reprezentowania właściwości zdarzenia o Microsoft Graph września 2020 r. Oprócz wpływu na interfejsy API w wersji zapoznawczej ta zmiana będzie również mieć wpływ na interfejs API logowania w środowisku produkcyjnym.

Wprowadziliśmy nową właściwość riskEventsTypes_v2 (ciąg) do interfejsu API signIns w wersji 1.0. Bieżąca właściwość riskEventTypes (enum) zostanie wycofana 11 czerwca 2022 r. zgodnie z naszymi zasadami Microsoft Graph wycofywania. Klienci powinni przejść do właściwości riskEventTypes_v2 w interfejsie API logowania usługi SignIns w wersji 1.0 do 11 czerwca 2022 r. Aby uzyskać więcej informacji, zapoznaj się [z tematem Deprecation of riskEventTypes property in signIns v1.0 API on Microsoft Graph (Deprecation of riskEventTypes property in signIns v1.0 API on Microsoft Graph](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//)(

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>Nadchodzące zmiany w powiadomieniach e-mail usługi MFA

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Mfa  
**Możliwości produktu:** Identity Security & Protection
 

Wprowadzamy następujące zmiany w powiadomieniach e-mail dotyczących uwierzytelniania wieloskładnikowego w chmurze:

Powiadomienia e-mail będą wysyłane z następującego adresu: azure-noreply@microsoft.com i msonlineservicesteam@microsoftonline.com . Aktualizujemy zawartość wiadomości e-mail z alertami o oszustwie, aby lepiej wskazać wymagane kroki odblokowania zastosowań.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Nowa rejestracja samoobsługowa dla użytkowników w domenach federowanych, którzy nie mogą uzyskać dostępu do aplikacji Microsoft Teams, ponieważ nie są synchronizowani z Azure Active Directory.

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Uwierzytelnienia (identyfikatory logowania)  
**Możliwości produktu:** Uwierzytelnianie użytkowników
 

Obecnie użytkownicy, którzy znajdują się w domenach federowanych w usłudze Azure AD, ale nie są synchronizowani z dzierżawą, nie mogą uzyskać dostępu do aplikacji Teams. Począwszy od końca czerwca, ta nowa funkcja umożliwi im to przez rozszerzenie istniejącej funkcji rejestracji zweryfikowanych pocztą e-mail. Pozwoli to użytkownikom, którzy mogą logować się do federowego identyfikatora tożsamości, ale którzy nie mają jeszcze obiektu użytkownika w identyfikatorze platformy Azure, na automatyczne utworzeniu obiektu użytkownika i uwierzytelnieniu go w usłudze Teams. Ich obiekt użytkownika zostanie oznaczony jako "rejestracja samoobsługowa". Jest to rozszerzenie istniejącej możliwości samodzielnego rejestracji zweryfikowanego za pomocą poczty e-mail, która może być kontrolowana przez użytkowników w domenach zarządzanych przy użyciu tej samej flagi. Ta zmiana zostanie ukończona w ciągu następnych dwóch miesięcy. Aktualizacje dokumentacji można znaleźć [tutaj.](../enterprise-users/directory-self-service-signup.md)
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Nadchodząca poprawka: dokument odnajdywania OIDC dla chmury Azure Government jest aktualizowany w celu odwołania się do poprawnych punktów końcowych programu Graph.

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Suwerenne chmury  
**Możliwości produktu:** Uwierzytelnianie użytkowników
 
Od czerwca dokument odnajdywania OIDC platformy tożsamości firmy Microsoft i protokołu [OpenID Connect](../develop/v2-protocols-oidc.md) w punkcie końcowym chmury usługi [](/graph/deployments) [Azure Government](../develop/authentication-national-cloud.md) (login.microsoftonline.us) rozpocznie zwracanie poprawnego punktu końcowego wykresu chmury krajowej ( lub , na podstawie podanej https://graph.microsoft.us https://dod-graph.microsoft.us) dzierżawy.  Obecnie udostępnia niepoprawne pole "graph.microsoft.com" punktu końcowego msgraph_host Graph.  

Ta poprawka błędu będzie wprowadzana stopniowo przez około 2 miesiące.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government użytkownicy nie będą już mogli logować się do login.microsoftonline.com

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Suwerenne chmury  
**Możliwości produktu:** Uwierzytelnianie użytkowników
 
1 czerwca 2018 r. oficjalny urząd Azure Active Directory (Azure AD) dla usługi Azure Government został zmieniony z https://login-us.microsoftonline.com na https://login.microsoftonline.us . Jeśli jesteś właścicielem aplikacji w dzierżawie Azure Government, musisz zaktualizować aplikację, aby logować użytkowników w punkcie końcowym us.

Od 5 maja usługa Azure AD zacznie wymuszać zmianę punktu końcowego, blokując użytkownikom Azure Government logowanie się do aplikacji hostowanych w dzierżawach usługi Azure Government przy użyciu publicznego punktu końcowego (microsoftonline.com). Aplikacje, których to problem, zaczną widzieć błąd AADSTS900439 — USGClientNotSupportedOnPublicEndpoint. 

Ta zmiana zostanie stopniowo wdróżna, a wymuszanie będzie ukończone we wszystkich aplikacjach w czerwcu 2020 r. Aby uzyskać więcej informacji, zobacz wpis [w Azure Government blogu](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/).

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>Żądanie wylogowywu pojedynczego saml teraz wysyła nameID w poprawnym formacie

**Wpisz:** Stałe  
**Kategoria usługi:** Uwierzytelnienia (identyfikatory logowania)  
**Możliwości produktu:** Uwierzytelnianie użytkowników
 
Gdy użytkownik kliknie wyloguj się (np. w portalu MyApps), usługa Azure AD wysyła komunikat saml pojedynczego wylogowania do każdej aplikacji, która jest aktywna w sesji użytkownika i ma skonfigurowany adres URL wylogowy. Te komunikaty zawierają nameID w formacie trwałym.

Jeśli oryginalny token logowania SAML użył innego formatu dla nameID (np. adresu e-mail/nazwy UPN), aplikacja SAML nie może skorelować wartości NameID w komunikacie wylogowania z istniejącą sesją (ponieważ identyfikatory NameID używane w obu komunikatach są różne), co spowodowało odrzucenie komunikatu wylogowania przez aplikację SAML i zalogowanie użytkownika. Ta poprawka sprawia, że komunikat wylogowania jest zgodny z nameID skonfigurowanym dla aplikacji.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>Rola administratora tożsamości hybrydowej jest teraz dostępna z aprowizowania w chmurze

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aprowizowanie w chmurze w usłudze Azure AD  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
Administratorzy IT mogą rozpocząć korzystanie z nowej roli "Administrator hybrydowy" jako roli o najmniejszych uprawnieniach do konfigurowania Azure AD Connect w chmurze. Dzięki tej nowej roli nie trzeba już używać roli administratora globalnego do konfigurowania i konfigurowania aprowowania w chmurze. [Dowiedz się więcej](../roles/delegate-by-task.md#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — maj 2020 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja z produktami innych firm
 
W maju 2020 r. dodaliśmy następujące 36 nowych aplikacji w galerii aplikacji z obsługą federacji:

[Moula,](https://moula.com.au/pay/merchants) [Surveypal,](https://www.surveypal.com/app) [Kbot365,](https://www.konverso.ai/virtual-assistant-digital-workplace/) [TackleBox,](http://www.tacklebox.app/) [Powell Teams,](https://powell-software.com/en/powell-teams-en/) [Talentsoft Assistant,](https://msteams.talent-soft.com/) [ASC Recording Insights,](https://teams.asc-recording.app/product) [GO1,](https://www.go1.com/) [B-Engaged,](https://b-engaged.se/) [Competella Contact Center Workgroup,](http://www.competella.com/) [Asite,](http://www.asite.com/) [ImageSoft Identity,](https://identity.imagesoftinc.com/) [My IBISWorld,](https://identity.imagesoftinc.com/) [insuite,](../saas-apps/insuite-tutorial.md) [Change Process Management,](../saas-apps/change-process-management-tutorial.md) [Cyara CX Assurance Platform,](../saas-apps/cyara-cx-assurance-platform-tutorial.md) [Smart Global Governance,](../saas-apps/smart-global-governance-tutorial.md) [Prezi,](../saas-apps/prezi-tutorial.md) [Mapbox,](../saas-apps/mapbox-tutorial.md) [Datava Enterprise Service Platform,](../saas-apps/datava-enterprise-service-platform-tutorial.md) [Whimsical,](../saas-apps/whimsical-tutorial.md) [Trelica,](../saas-apps/trelica-tutorial.md) [EasySSO for Confluence,](../saas-apps/easysso-for-confluence-tutorial.md) [EasySSO for BitBucket,](../saas-apps/easysso-for-bitbucket-tutorial.md) [EasySSO for Bamboo,](../saas-apps/easysso-for-bamboo-tutorial.md) [Torii,](../saas-apps/torii-tutorial.md) [Astockd Cloud,](../saas-apps/axiad-cloud-tutorial.md) [Humanage,](../saas-apps/humanage-tutorial.md) [ColorTokens ZTNA,](../saas-apps/colortokens-ztna-tutorial.md) [CCH Tagetow](../saas-apps/cch-tagetik-tutorial.md), [ShareVault,](../saas-apps/sharevault-tutorial.md) [Vyond,](../saas-apps/vyond-tutorial.md) [TextExpander,](../saas-apps/textexpander-tutorial.md) [Anyone Home CRM,](../saas-apps/anyone-home-crm-tutorial.md) [askSpoke,](../saas-apps/askspoke-tutorial.md) [ice Contact Center](../saas-apps/ice-contact-center-tutorial.md)

Dokumentację wszystkich aplikacji można również znaleźć https://aka.ms/AppsTutorial tutaj.

Aby uzyskać listę aplikacji w galerii aplikacji usługi Azure AD, przeczytaj szczegółowe informacje https://aka.ms/AzureADAppRequest tutaj.

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Tryb tylko do raportu dla dostępu warunkowego jest teraz ogólnie dostępny

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Identity Security & Protection

[Tryb tylko do raportu dla dostępu warunkowego usługi Azure AD](../conditional-access/concept-conditional-access-report-only.md) umożliwia ocenę wyniku zasad bez wymuszania kontroli dostępu. Możesz przetestować zasady tylko do raportów w całej organizacji i zrozumieć ich wpływ przed ich włączeniem, dzięki czemu wdrażanie będzie bezpieczniejsze i łatwiejsze. W ciągu ostatnich kilku miesięcy widzieliśmy silne wdrożenie trybu tylko do raportów — ponad 26 milionów użytkowników jest już w zakresie zasad tylko do raportów. Wraz z tym ogłoszeniem nowe zasady dostępu warunkowego usługi Azure AD zostaną domyślnie utworzone w trybie tylko do raportu. Oznacza to, że możesz monitorować wpływ zasad od momentu ich utworzenia. Osoby, które korzystają z interfejsów API programu MS Graph, mogą również programowo zarządzać zasadami [tylko](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true) do raportów. 

---

### <a name="self-service-sign-up-for-guest-users"></a>Rejestracja samoobsługowa dla użytkowników-gości

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C
 
Dzięki External Identities usłudze Azure AD możesz zezwolić osobom spoza organizacji na dostęp do Twoich aplikacji i zasobów, jednocześnie umożliwiając im logowanie się przy użyciu dowolnej preferowanej tożsamości. Podczas udostępniania aplikacji użytkownikom zewnętrznym możesz nie zawsze z wyprzedzeniem wiedzieć, kto będzie potrzebować dostępu do aplikacji. Dzięki [rejestracji samoobsługowej](../external-identities/self-service-sign-up-overview.md)można umożliwić użytkownikom-gościom rejestracja i uzyskanie konta gościa dla aplikacji biznesowych (LOB). Przepływ rejestracji można utworzyć i dostosować w celu obsługi usługi Azure AD i tożsamości społecznościowych. Możesz również zbierać dodatkowe informacje o użytkowniku podczas rejestracji.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Szczegółowe informacje o dostępie warunkowym i skoroszyt raportowania są ogólnie dostępne

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Identity Security & Protection

Skoroszyt [analizy i raportowania zapewnia](../conditional-access/howto-conditional-access-insights-reporting.md) administratorom widok podsumowania dostępu warunkowego usługi Azure AD w dzierżawie. Dzięki możliwości wybierania poszczególnych zasad administratorzy mogą lepiej zrozumieć działania poszczególnych zasad i monitorować wszelkie zmiany w czasie rzeczywistym. Skoroszyt przesyła strumieniowo dane Azure Monitor, które można skonfigurować w ciągu kilku [minut, zgodnie z tymi instrukcjami.](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) Aby pulpit nawigacyjny był bardziej odnajdywalny, przenieśliśmy go na nową kartę szczegółowych informacji i raportowania w menu dostępu warunkowego usługi Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Blok Szczegóły zasad dla dostępu warunkowego jest w publicznej wersji zapoznawczej

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Identity Security & Protection

Nowy blok [szczegółów zasad zawiera](../conditional-access/troubleshoot-conditional-access.md) przypisania, warunki i mechanizmy kontroli spełnione podczas oceny zasad dostępu warunkowego. Dostęp do bloku można uzyskać, wybierając wiersz na kartach Dostęp warunkowy lub Tylko raport w szczegółach logowania.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Nowe możliwości zapytań dla obiektów katalogu w usłudze Microsoft Graph są dostępne w publicznej wersji zapoznawczej

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Możliwości produktu MS **Graph:** środowisko dewelopera

Wprowadzono nowe możliwości dla interfejsów API Microsoft Graph Directory Objects, umożliwiając operacje Count, Search, Filter i Sort. Dzięki temu deweloperzy będą mieć możliwość szybkiego wykonywania zapytań o obiekty katalogu bez obejść, takich jak filtrowanie i sortowanie w pamięci. Dowiedz się więcej z tego [wpisu w blogu.](https://aka.ms/CountFilterMSGraphAAD)

Obecnie jesteśmy w publicznej wersji zapoznawczej i szukamy opinii. Wyślij swoje komentarze za pomocą tej [krótkiej ankiety](https://aka.ms/MsGraphAADSurveyDocs).

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Konfigurowanie logowania pojedynczego opartego na saml przy użyciu interfejsu API Microsoft Graph API (beta)

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej
 
Obsługa tworzenia i konfigurowania aplikacji z galerii usługi Azure AD przy użyciu interfejsów API programu MS Graph w wersji beta jest teraz dostępna. Jeśli musisz skonfigurować logowanie pojedynczej oparte na saml dla wielu wystąpień aplikacji, zaoszczędź czas przy użyciu interfejsów API języka Microsoft Graph, aby zautomatyzować konfigurację logowania pojedynczego opartego na [saml.](/graph/application-saml-sso-configure-api)
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Nowe łączniki aprowizowania w galerii aplikacji usługi Azure AD — maj 2020 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwości produktu:** integracja z produktami innych firm
 
Teraz możesz zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla tych nowo zintegrowanych aplikacji:

* [8x8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [New Relic by Organization](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-infrastructure-console-provisioning-tutorial.md)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu zautomatyzowanego aprowizowania kont użytkowników, zobacz [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Automatyzowanie aprowzowania użytkowników w aplikacjach SaaS za pomocą usługi Azure AD).

---

### <a name="saml-token-encryption-is-generally-available"></a>Szyfrowanie tokenów SAML jest ogólnie dostępne

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej
 
[Szyfrowanie tokenów SAML](../manage-apps/howto-saml-token-encryption.md) umożliwia skonfigurowanie aplikacji do odbierania zaszyfrowanych asercji SAML. Ta funkcja jest teraz ogólnie dostępna we wszystkich chmurach.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Oświadczenia nazw grup w tokenach aplikacji są ogólnie dostępne

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej
 
Oświadczenia grupy wystawione w tokenie można teraz ograniczyć tylko do grup przypisanych do aplikacji.  Jest to szczególnie ważne, gdy użytkownicy są członkami dużej liczby grup i istnieje ryzyko przekroczenia limitów rozmiaru tokenu. Dzięki tej nowej możliwości możliwość dodawania nazw grup do [tokenów](../hybrid/how-to-connect-fed-group-claims.md) jest ogólnie dostępna.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Zapis zwrotny produktu Workday obsługuje teraz ustawianie atrybutów numeru telefonu służbowego

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
Ulepszyliśmy aplikację aprowizowania zwrotnego produktu Workday, aby teraz obsługiła zapis zwrotny dla służbowego numeru telefonu i atrybutów numeru mobilnego. Oprócz poczty e-mail i nazwy użytkownika możesz teraz skonfigurować aplikację aprowicyjną Workday Writeback, aby przepływała wartości numerów telefonów z usługi Azure AD do aplikacji Workday. Aby uzyskać więcej informacji na temat konfigurowania zapisu zwrotnego numerów telefonów, zapoznaj się z samouczkiem [aplikacji Workday Writeback.](../saas-apps/workday-writeback-tutorial.md) 

---

### <a name="publisher-verification-preview"></a>Weryfikacja wydawcy (wersja zapoznawcza)

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Środowisko dewelopera
 
Weryfikacja wydawcy (wersja zapoznawcza) pomaga administratorom i użytkownikom końcowych zrozumieć autentyczność deweloperów aplikacji zintegrowanych z platformą tożsamości firmy Microsoft. Aby uzyskać szczegółowe informacje, zobacz [Weryfikacja wydawcy (wersja zapoznawcza).](../develop/publisher-verification-overview.md)
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Przepływ kodu autoryzacji dla aplikacji jednostronicowych

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Możliwość **produktu uwierzytelniania:** Środowisko dewelopera

Ze względu na ograniczenia dotyczące plików cookie innych firm w nowoczesnych przeglądarkach, takich jak [Safari ITP,](../develop/reference-third-party-cookies-spas.md)do obsługi logowania jednokrotnego będą musiały używać przepływu kodu autoryzacji, a nie niejawnego przepływu; MSAL.js 2.x będzie teraz obsługiwać przepływ kodu autoryzacji. Istnieje jako odpowiednie aktualizacje Azure Portal, aby można było zaktualizować SPA jako typ "spa" i użyj przepływu kodu uwierzytelniania. Aby uzyskać wskazówki, zapoznaj się z tematem Szybki start: logowanie użytkowników i uzyskiwanie tokenu dostępu w aplikacji SPA w języku JavaScript przy użyciu [przepływu kodu uwierzytelniania.](../develop/quickstart-v2-javascript-auth-code.md)

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Ulepszone filtrowanie urządzeń jest dostępne w publicznej wersji zapoznawczej

**Wpisz:** Zmieniono funkcję   
**Kategoria usługi:** Zarządzanie urządzeniami **produktu: Zarządzanie** cyklem życia urządzenia
 
Wcześniej jedynymi filtrami, których można było użyć, były "Włączone" i "Data działania". Teraz możesz filtrować [listę urządzeń według](../devices/device-management-azure-portal.md#device-list-filtering-preview)większej liczby właściwości, w tym typu systemu operacyjnego, typu sprzężenia, zgodności i innych. Te dodatki powinny uprościć lokalizowanie określonego urządzenia.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>Nowe środowisko Rejestracje aplikacji dla Azure AD B2C jest teraz ogólnie dostępne

**Wpisz:** Zmieniono funkcję   
**Kategoria usługi:** B2C — zarządzanie tożsamościami konsumentów  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
Nowe środowisko Rejestracje aplikacji dla Azure AD B2C jest teraz ogólnie dostępne. 

Wcześniej trzeba było zarządzać aplikacjami B2C dostępnymi dla konsumentów niezależnie od pozostałych aplikacji przy użyciu starszej wersji funkcji "Aplikacje". Oznaczało to różne środowisko tworzenia aplikacji w różnych miejscach na platformie Azure.

Nowe środowisko pokazuje wszystkie rejestracje aplikacji B2C i rejestracje aplikacji usługi Azure AD w jednym miejscu i zapewnia spójny sposób zarządzania nimi. Niezależnie od tego, czy musisz zarządzać aplikacją skierowaną do klienta, czy aplikacją, która ma dostęp do usługi Microsoft Graph, aby programowo zarządzać zasobami Azure AD B2C, musisz nauczyć się tylko jednego sposobu, aby coś zrobić.

Nowe środowisko można uzyskać, przechodząc do Azure AD B2C i wybierając blok Rejestracje aplikacji. Środowisko jest również dostępne z Azure Active Directory usługi.

Środowisko Azure AD B2C Rejestracje aplikacji jest oparte na [](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) ogólnym doświadczeniu rejestracji aplikacji dla dzierżaw usługi Azure AD, ale jest dostosowane do Azure AD B2C. Starsze środowisko "Aplikacje" zostanie wycofane w przyszłości.

Aby uzyskać więcej informacji, odwiedź stronę The New app registration experience for Azure AD B2C (Nowe środowisko rejestracji [aplikacji dla Azure AD B2C).](../../active-directory-b2c/app-registrations-training-guide.md)

---
## <a name="april-2020"></a>Kwiecień 2020 r.

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>Połączone środowisko rejestracji informacji zabezpieczających jest teraz ogólnie dostępne

**Wpisz:** Nowa funkcja

**Kategoria usługi:** Uwierzytelnienia (identyfikatory logowania)

**Możliwości produktu:** Identity Security & Protection

Połączone środowisko rejestracji dla usługi Multi-Factor Authentication (MFA) i resetowania Self-Service hasła (SSPR) jest teraz ogólnie dostępne. To nowe środowisko rejestracji umożliwia użytkownikom rejestrowanie się w celu uwierzytelniania wieloskładnikowego i usługi SSPR w jednym procesie krok po kroku. Podczas wdrażania nowego rozwiązania dla organizacji użytkownicy mogą rejestrować się w krótszym czasie i z mniejszą liczbą problemów. Zapoznaj się z wpisem w blogu [tutaj.](https://bit.ly/3etiRyQ)

---

### <a name="continuous-access-evaluation"></a>Ciągła weryfikacja dostępu

**Wpisz:** Nowa funkcja

**Kategoria usługi:** Uwierzytelnienia (identyfikatory logowania)

**Możliwości produktu:** Identity Security & Protection

Ciągła weryfikacja dostępu to nowa funkcja zabezpieczeń, która umożliwia wymuszanie zasad prawie w czasie rzeczywistym na jednostki zależne korzystające z tokenów dostępu usługi Azure AD w przypadku zdarzeń w usłudze Azure AD (takich jak usunięcie konta użytkownika). Najpierw wprowadzamy tę funkcję dla klientów aplikacji Teams i Outlook. Aby uzyskać więcej informacji, przeczytaj nasz [blog i](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)  [dokumentację](../conditional-access/concept-continuous-access-evaluation.md).

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>Logowanie za pomocą wiadomości SMS: pracownicy pierwszej linii mogą logować się do aplikacji usługi Azure AD przy użyciu numeru telefonu i hasła

**Wpisz:** Nowa funkcja

**Kategoria usługi:** Uwierzytelnienia (identyfikatory logowania)

**Możliwości produktu:** Uwierzytelnianie użytkowników

Pakiet Office uruchamia serię aplikacji biznesowych przeznaczonych dla urządzeń przenośnych przeznaczonych dla nietradycyjnych organizacji oraz dla pracowników w dużych organizacjach, które nie używają poczty e-mail jako podstawowej metody komunikacji. Te aplikacje są kierowane do pracowników pracujących przy frontonie, pracowników bez działu pomocy technicznej, agentów terenowych lub pracowników sklepów detalicznych, którzy mogą nie otrzymać adresu e-mail od pracodawcy, mieć dostęp do komputera lub działu IT. Ten projekt umożliwia tym pracownikom logowanie się do aplikacji biznesowych przez wprowadzenie numeru telefonu i obejściu kodu. Aby uzyskać więcej informacji, zobacz dokumentację [dla administratorów](../authentication/howto-authentication-sms-signin.md) i [dokumentację użytkownika końcowego](../user-help/sms-sign-in-explainer.md).

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Zapraszanie użytkowników wewnętrznych do korzystania ze współpracy B2B

**Wpisz:** Nowa funkcja

**Kategoria usługi:** B2b

**Możliwości produktu:**

Rozszerzamy możliwość zapraszania B2B, aby umożliwić zapraszanie istniejących kont wewnętrznych do korzystania z poświadczeń współpracy B2B w przyszłości. W tym celu oprócz typowych parametrów, takich jak zaproszony adres e-mail, obiekt użytkownika jest przekazywania do interfejsu API zapraszania. Identyfikator obiektu użytkownika, nazwa UPN, członkostwo w grupie, przypisanie aplikacji itp. pozostaną nienaruszone, ale w przyszłości będą one używać usługi B2B do uwierzytelniania przy użyciu poświadczeń dzierżawy głównej, a nie poświadczeń wewnętrznych, których używali przed zaproszeniem. Aby uzyskać szczegółowe informacje, zobacz [dokumentację](../external-identities/invite-internal-users.md).

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Tryb dostępu warunkowego tylko do raportu jest teraz ogólnie dostępny

**Wpisz:** Nowa funkcja

**Kategoria usługi:** Dostęp warunkowy

**Możliwości produktu:** Identity Security & Protection

[Tryb tylko do raportu dla dostępu warunkowego usługi Azure AD](../conditional-access/concept-conditional-access-report-only.md) umożliwia ocenę wyniku zasad bez wymuszania kontroli dostępu. Możesz przetestować zasady tylko do raportów w całej organizacji i zrozumieć ich wpływ przed ich włączeniem, co czyni wdrożenie bezpieczniejszym i łatwiejszym. W ciągu ostatnich kilku miesięcy widzieliśmy silne wdrożenie trybu tylko do raportów, w przypadku których ponad 26 milionów użytkowników jest już w zakresie zasad tylko do raportów. Dzięki temu anonsowi nowe zasady dostępu warunkowego usługi Azure AD zostaną domyślnie utworzone w trybie tylko do raportów. Oznacza to, że możesz monitorować wpływ zasad od momentu ich utworzenia. Ponadto użytkownikom, którzy korzystają z interfejsów API programu MS Graph, można programowo zarządzać zasadami [tylko do raportów.](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true) 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Szczegółowe informacje dotyczące dostępu warunkowego i skoroszyt raportowania są ogólnie dostępne

**Wpisz:** Nowa funkcja

**Kategoria usługi:** Dostęp warunkowy

**Możliwości produktu:** Identity Security & Protection

Skoroszyt analizy dostępu [warunkowego i raportowania](../conditional-access/howto-conditional-access-insights-reporting.md) zapewnia administratorom widok podsumowania dostępu warunkowego usługi Azure AD w dzierżawie. Dzięki możliwości wybierania poszczególnych zasad administratorzy mogą lepiej zrozumieć działania poszczególnych zasad i monitorować wszelkie zmiany w czasie rzeczywistym. Skoroszyt przesyła strumieniowo dane Azure Monitor, które można skonfigurować w ciągu kilku [minut, zgodnie z tymi instrukcjami.](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) Aby pulpit nawigacyjny był bardziej odnajdywalny, przenieśliśmy go na nową kartę szczegółowych informacji i raportowania w menu dostępu warunkowego usługi Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Blok Szczegóły zasad dla dostępu warunkowego jest w publicznej wersji zapoznawczej

**Wpisz:** Nowa funkcja

**Kategoria usługi:** Dostęp warunkowy

**Możliwości produktu:** Identity Security & Protection

W bloku [szczegółów nowych zasad są](../conditional-access/troubleshoot-conditional-access.md) wyświetlane przypisania, warunki i kontrolki, które zostały spełnione podczas oceny zasad dostępu warunkowego. Dostęp do bloku można uzyskać,  wybierając wiersz na kartach Dostęp warunkowy lub Tylko **raport** szczegółów logowania.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Nowe aplikacje federowane dostępne w galerii aplikacja usługi Azure AD — kwiecień 2020 r.

**Wpisz:** Nowa funkcja

**Kategoria usługi:** Aplikacje dla przedsiębiorstw

**Możliwości produktu:** integracja z produktami innych firm

W kwietniu 2020 r. dodaliśmy te 31 nowych aplikacji z obsługą federacji do galerii aplikacji: 

[SincroPool Apps](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [Float](../saas-apps/float-tutorial.md), [LMS365](https://lms.365.systems/), [IWT Procurement Suite](../saas-apps/iwt-procurement-suite-tutorial.md), [Lunni,](https://lunni.fi/) [EasySSO for Jira,](../saas-apps/easysso-for-jira-tutorial.md) [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki Dashboard](../saas-apps/meraki-dashboard-tutorial.md), Microsoft 365 [Mover](https://app.mover.io/login), [Speaker Engage](https://speakerengage.com/login.php), Przekrzywile , [Ally,](../saas-apps/ally-tutorial.md) [PrzełęczFlow,](https://app.dutyflow.nl/) [AlertMedia,](../saas-apps/alertmedia-tutorial.md) [gr8 People](../saas-apps/gr8-people-tutorial.md), [Pendo,](../saas-apps/pendo-tutorial.md) [HighGround,](../saas-apps/highground-tutorial.md) [Synchroniz](../saas-apps/harmony-tutorial.md), [Timetabling Solutions,](../saas-apps/timetabling-solutions-tutorial.md) [SynchroNet CLICK,](../saas-apps/synchronet-click-tutorial.md) [empower](https://www.made-in-office.com/en/), [Fortes Change Cloud,](../saas-apps/fortes-change-cloud-tutorial.md) [Litmus,](../saas-apps/litmus-tutorial.md) [GroupTalk,](https://recorder.grouptalk.com/) [Frontify,](../saas-apps/frontify-tutorial.md) [MongoDB Cloud,](../saas-apps/mongodb-cloud-tutorial.md) [TickitLMS Learn](../saas-apps/tickitlms-learn-tutorial.md), [COCO,](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/)Grupowanie Productivity [Suite,](../saas-apps/nitro-productivity-suite-tutorial.md) [Trend Micro Web Security (TMWS)](../saas-apps/trend-micro-tutorial.md) [](../saas-apps/honestly-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z](../saas-apps/tutorial-list.md)Azure Active Directory . Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy aplikacji w [galerii Azure Active Directory aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Microsoft Graph zapytań różnicowych dla protokołu oAuth2PermissionGrant dostępnego w publicznej wersji zapoznawczej

**Wpisz:** Nowa funkcja

**Kategoria usługi:** MS Graph

**Możliwości produktu:** Środowisko dewelopera

Zapytanie różnicowe oAuth2PermissionGrant jest dostępne w publicznej wersji zapoznawczej! Teraz możesz śledzić zmiany bez konieczności ciągłego sondowania Microsoft Graph. [Dowiedz się więcej.](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Microsoft Graph zapytań różnicowych dla kontaktu organizacyjnego jest ogólnie dostępna

**Wpisz:** Nowa funkcja

**Kategoria usługi:** MS Graph

**Możliwości produktu:** Środowisko dewelopera

Zapytanie różnicowe dla kontaktów organizacyjnych jest ogólnie dostępne! Teraz możesz śledzić zmiany w aplikacjach produkcyjnych bez konieczności ciągłego sondowania Microsoft Graph. Zastąp istniejący kod, który stale sonduje orgContact danych przez zapytanie różnicowe, aby znacznie zwiększyć wydajność. [Dowiedz się więcej.](/graph/api/orgcontact-delta?tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Microsoft Graph zapytań różnicowych dla ogólnie dostępnej aplikacji

**Wpisz:** Nowa funkcja

**Kategoria usługi:** MS Graph

**Możliwości produktu:** Środowisko dewelopera

Zapytanie różnicowe dla aplikacji jest ogólnie dostępne! Teraz możesz śledzić zmiany w aplikacjach produkcyjnych bez konieczności ciągłego sondowania Microsoft Graph. Zastąp istniejący kod, który stale sonduje dane aplikacji za pomocą zapytania różnicowego w celu znacznego zwiększenia wydajności. [Dowiedz się więcej.](/graph/api/application-delta)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Microsoft Graph zapytań różnicowych dla jednostek administracyjnych dostępnych w publicznej wersji zapoznawczej

**Wpisz:** Nowa funkcja

**Kategoria usługi:** MS Graph

**Możliwości produktu:** Zapytanie różnicowe na platformie Developer Experience dla jednostek administracyjnych jest dostępne w publicznej wersji zapoznawczej! Teraz możesz śledzić zmiany bez konieczności ciągłego sondowania Microsoft Graph. [Dowiedz się więcej.](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Zarządzanie numerami telefonów uwierzytelniania i nie tylko w nowych interfejsach API Microsoft Graph beta

**Wpisz:** Nowa funkcja

**Kategoria usługi:** MS Graph

**Możliwości produktu:** Środowisko dewelopera

Te interfejsy API są kluczowym narzędziem do zarządzania metodami uwierzytelniania użytkowników. Teraz możesz programowo wstępnie zarejestrować wystawców uwierzytelnień używanych do uwierzytelniania MFA i samoobsługowego resetowania hasła (SSPR, self-service password reset) i zarządzać nimi. Była to jedna z najbardziej żądanych funkcji w usłudze Azure AD MFA, funkcji SSPR i Microsoft Graph przestrzeniach. Nowe interfejsy API, które wydaliśmy w tej fali, dają możliwość wykonywania:

- Odczytywanie, dodawanie, aktualizowanie i usuwanie telefonów uwierzytelniania użytkownika
- Resetowanie hasła użytkownika
- Włączanie i wyłączanie logowania za pomocą wiadomości SMS

Aby uzyskać więcej informacji, zobacz Omówienie interfejsu [API metod uwierzytelniania usługi Azure AD.](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)

---

### <a name="administrative-units-public-preview"></a>Publiczna wersja zapoznawcza jednostek administracyjnych

**Wpisz:** Nowa funkcja

**Kategoria usługi:** Role usługi Azure AD

**Możliwości produktu:** Access Control

Jednostki administracyjne umożliwiają przyznawanie uprawnień administratora, które są ograniczone do działu, regionu lub innego segmentu organizacji, który zdefiniujesz. Za pomocą jednostek administracyjnych można delegować uprawnienia do administratorów regionalnych lub ustawiać zasady na poziomie szczegółowym. Na przykład administrator konta użytkownika może aktualizować informacje o profilu, resetować hasła i przypisywać licencje użytkownikom tylko w swojej jednostce administracyjnej.

Korzystając z jednostek administracyjnych, administrator centralny może:

- Tworzenie jednostki administracyjnej do zdecentralizowanego zarządzania zasobami
- Przypisywanie roli z uprawnieniami administracyjnymi tylko dla użytkowników usługi Azure AD w jednostce administracyjnej
- Wypełnij jednostki administracyjne użytkownikami i grupami zgodnie z potrzebami

Aby uzyskać więcej informacji, zobacz [Zarządzanie jednostkami administracyjnymi w Azure Active Directory (wersja zapoznawcza).](../roles/administrative-units.md)

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Wbudowane role administratora i technika drukarek

**Wpisz:** Nowa funkcja

**Kategoria usługi:** Role usługi Azure AD

**Możliwości produktu:** Access Control

**Administrator drukarek:** użytkownicy z tą rolą mogą rejestrować drukarki i zarządzać wszystkimi aspektami wszystkich konfiguracji drukarek w rozwiązaniu Microsoft Drukowanie uniwersalne, w tym ustawieniami łącznika Drukowanie uniwersalne. Mogą wyrazić zgodę na wszystkie delegowane żądania uprawnień drukowania. Administratorzy drukarek mają również dostęp do raportów drukowania. 

**Technik drukarek:** użytkownicy z tą rolą mogą rejestrować drukarki i zarządzać stanem drukarek w rozwiązaniu Drukowanie uniwersalne Microsoft. Mogą również odczytywać wszystkie informacje o łączniku. Kluczowe zadania, których nie może wykonać technik drukarek, to ustawienie uprawnień użytkownika do drukarek i udostępniania drukarek. [Dowiedz się więcej.](../roles/permissions-reference.md#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Wbudowana rola administratora tożsamości hybrydowej

**Wpisz:** Nowa funkcja

**Kategoria usługi:** Role usługi Azure AD

**Możliwości produktu:** Access Control

Użytkownicy w tej roli mogą włączać i konfigurować usługi i ustawienia związane z włączaniem tożsamości hybrydowej w usłudze Azure AD oraz zarządzać nimi. Ta rola daje możliwość skonfigurowania usługi Azure AD do jednej z trzech obsługiwanych metod uwierzytelniania&#8212;synchronizacja skrótów haseł, uwierzytelnianie pass-through (PTA) lub federacja (AD FS lub dostawca federowania innej firmy)&#8212;oraz wdrażanie powiązanej infrastruktury lokalnej w celu ich włączenia. Infrastruktura lokalna obejmuje aprowizowanie i agentów PTA. Ta rola daje możliwość włączenia bezproblemowego logowania jednokrotnego (S-SSO, Seamless Single Sign-On) w celu umożliwienia bezproblemowego uwierzytelniania na urządzeniach innych niż Windows 10 lub komputerach z systemem innych niż Windows Server 2016. Ponadto ta rola daje możliwość wyświetlania dzienników logowania oraz uzyskiwania dostępu do kondycji i analizy na potrzeby monitorowania i rozwiązywania problemów. [Dowiedz się więcej.](../roles/permissions-reference.md#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Wbudowana rola administratora sieci

**Wpisz:** Nowa funkcja

**Kategoria usługi:** Role usługi Azure AD

**Możliwości produktu:** Access Control

Użytkownicy z tą rolą mogą przeglądać zalecenia firmy Microsoft dotyczące architektury obwodowej sieci, które są oparte na telemetrii sieci z lokalizacji użytkowników. Wydajność sieci dla Microsoft 365 opiera się na dokładnej architekturze obwodowej sieci klienta przedsiębiorstwa, która jest zazwyczaj specyficzna dla lokalizacji użytkownika. Ta rola umożliwia edytowanie odnalezionych lokalizacji użytkowników i konfigurowanie parametrów sieci dla tych lokalizacji w celu ułatwienia ulepszonych pomiarów telemetrii i rekomendacji dotyczących projektowania. [Dowiedz się więcej.](../roles/permissions-reference.md#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Działanie zbiorcze i pobieranie w portalu administracyjnym usługi Azure AD

**Wpisz:** Nowa funkcja

**Kategoria usługi:** Zarządzanie użytkownikami

**Możliwości produktu:** Katalogu

Teraz możesz wykonywać zbiorcze działania na użytkownikach i grupach w usłudze Azure AD, przesyłając plik CSV w portalu administracyjnym usługi Azure AD. Możesz tworzyć użytkowników, usuwać ich i zapraszać użytkowników-gości. Możesz również dodawać i usuwać członków z grupy.

Możesz również pobrać listy zasobów usługi Azure AD ze strony portalu administracyjnego usługi Azure AD. Możesz pobrać listę użytkowników w katalogu, listę grup w katalogu i członków określonej grupy.

Aby uzyskać więcej informacji, zapoznaj się z następującymi tematami:

- [Tworzenie użytkowników lub](../enterprise-users/users-bulk-add.md) [zapraszanie użytkowników-gości](../external-identities/tutorial-bulk-invite.md)
- [Usuwanie użytkowników lub](../enterprise-users/users-bulk-delete.md) [przywracanie usuniętych użytkowników](../enterprise-users/users-bulk-restore.md)
- [Pobieranie listy użytkowników lub](../enterprise-users/users-bulk-download.md) [pobieranie listy grup](../enterprise-users/groups-bulk-download.md)
- [Dodawanie (importowanie) członków](../enterprise-users/groups-bulk-import-members.md) lub [usuwanie członków](../enterprise-users/groups-bulk-remove-members.md) lub Pobieranie [listy członków](../enterprise-users/groups-bulk-download-members.md) dla grupy

---

### <a name="my-staff-delegated-user-management"></a>Mój personel zarządzania użytkownikami delegowanymi

**Wpisz:** Nowa funkcja

**Kategoria usługi:** Zarządzanie użytkownikami

**Możliwości produktu:**

Mój personel umożliwia menedżerom firstline, takim jak menedżer sklepu, zapewnienie, że członkowie personelu mogą uzyskać dostęp do kont usługi Azure AD. Zamiast polegać na centralnej pomocy technicznej, organizacje mogą delegować typowe zadania, takie jak resetowanie haseł lub zmienianie numerów telefonów, do menedżera firstline. Dzięki Mój personel użytkownik, który nie może uzyskać dostępu do swojego konta, może ponownie uzyskać dostęp za pomocą zaledwie kilku kliknięć, bez potrzeby pomocy technicznej ani personelu IT. Aby uzyskać więcej informacji, zobacz Zarządzanie użytkownikami za pomocą programu [Mój personel (wersja zapoznawcza)](../roles/my-staff-configure.md) i Delegowanie zarządzania użytkownikami za [pomocą programu Mój personel (wersja zapoznawcza).](../user-help/my-staff-team-manager.md)

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>Uaktualnione środowisko użytkownika końcowego podczas przeglądów dostępu

**Wpisz:** Zmieniono funkcję

**Kategoria usługi:** Przeglądy dostępu

**Możliwości produktu:** Nadzór nad tożsamościami

Zaktualizowaliśmy środowisko recenzenta dla przeglądów dostępu usługi Azure AD w Moje aplikacje portal. Pod koniec kwietnia recenzentzy, którzy są zalogowani do obsługi recenzentów dostępu usługi Azure AD, zobaczą baner, który umożliwi im wypróbowanie zaktualizowanego Mój dostęp. Należy pamiętać, że zaktualizowane środowisko przeglądów dostępu oferuje te same funkcje co bieżące środowisko, ale z ulepszonym interfejsem użytkownika i nowymi możliwościami, aby umożliwić użytkownikom wydajną pracę. [Więcej informacji na temat zaktualizowanego środowisko można znaleźć tutaj.](../governance/perform-access-review.md) Ta publiczna wersja zapoznawcza będzie trwała do końca lipca 2020 r. Pod koniec lipca recenzentzy, którzy nie wybrali wersji zapoznawczej, zostaną automatycznie kierowani do Mój dostęp przeprowadzania przeglądów dostępu. Jeśli chcesz, aby recenzentzy na stałe przeszli do wersji zapoznawczej na platformie Mój dostęp teraz, prosimy o zgłoszenie [w tym miejscu.](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Aplikacje do aprowizowania i zapisu zwrotnego użytkowników w aplikacji Workday dla ruchu przychodzącego obsługują teraz najnowsze wersje interfejsu API usług internetowych workday

**Wpisz:** Zmieniono funkcję

**Kategoria usługi:** Aprowizowanie aplikacji

**Możliwości produktu:** 

W oparciu o opinie klientów zaktualizowaliśmy aplikacje do aprowowania i zapisu zwrotnego dla użytkowników przychodzących workday w galerii aplikacji przedsiębiorstwa w celu obsługi najnowszych wersji interfejsu API usług internetowych workday (WWS). Dzięki tej zmianie klienci mogą określić wersję interfejsu API usług WWS, która ma być używania w ciągu połączenia. Dzięki temu klienci mogą pobrać więcej atrybutów kadr dostępnych w wersjach produktu Workday. Aplikacja Workday Writeback używa teraz zalecanej usługi internetowej Change_Work_Contact_Info Workday, aby pokonać ograniczenia Maintain_Contact_Info.

Jeśli w ciągu połączenia nie zostanie określona żadna wersja, domyślnie aplikacje do aprowizowania ruchu przychodzącego produktu Workday będą nadal używać usług WWS w wersji 21.1. Aby przełączyć się do najnowszych interfejsów API produktu Workday na potrzeby aprowizowania użytkowników przychodzących, klienci muszą zaktualizować ciągi połączenia zgodnie z dokumentacją w samouczku, a także zaktualizować elementy XPATH używane dla atrybutów produktu Workday zgodnie z dokumentacją w przewodniku informacyjnym po atrybutach produktu [Workday.](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30) [](../saas-apps/workday-inbound-tutorial.md#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) 

Aby korzystać z nowego interfejsu API na potrzeby zapisu zwrotnego, nie ma żadnych zmian wymaganych w aplikacji do aprowowania funkcji Writeback w programie Workday. Po stronie aplikacji Workday upewnij się, że konto użytkownika systemu integracji (ISU) firmy Workday ma uprawnienia do wywoływania procesu biznesowego usługi Change_Work_Contact zgodnie z dokumentacją w sekcji samouczka Konfigurowanie uprawnień zasad zabezpieczeń procesów [biznesowych.](../saas-apps/workday-inbound-tutorial.md#configuring-business-process-security-policy-permissions) 

Zaktualizowaliśmy nasz przewodnik [samouczka, aby](../saas-apps/workday-inbound-tutorial.md) odzwierciedlić obsługę nowej wersji interfejsu API.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Użytkownicy z domyślną rolą dostępu są teraz w zakresie aprowizowania

**Wpisz:** Zmieniono funkcję

**Kategoria usługi:** Aprowizowanie aplikacji

**Możliwości produktu:** Zarządzanie cyklem życia tożsamości

W przeszłości użytkownicy z domyślną rolą dostępu nie mieli zakresu aprowizowania. Słyszeliśmy opinie, że klienci chcą, aby użytkownicy z tą rolą w zakresie aprowizowania. Od 16 kwietnia 2020 r. wszystkie nowe konfiguracje aprowowania umożliwiają aprowizowanie użytkowników z domyślną rolą dostępu. Stopniowo zmieniamy zachowanie istniejących konfiguracji aprowizowania, aby obsługiwać aprowizowanie użytkowników z tą rolą. [Dowiedz się więcej.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

---

### <a name="updated-provisioning-ui"></a>Zaktualizowany interfejs użytkownika aprowowania

**Wpisz:** Zmieniono funkcję

**Kategoria usługi:** Aprowizowanie aplikacji

**Możliwości produktu:** Zarządzanie cyklem życia tożsamości

Odświeżyliśmy środowisko aprowizowania, aby utworzyć bardziej skoncentrowany widok zarządzania. Po przechodzeniu do bloku aprowizowania dla aplikacji przedsiębiorstwa, która została już skonfigurowana, będzie można łatwo monitorować postęp aprowizowania i zarządzać akcjami, takimi jak uruchamianie, zatrzymywanie i ponowne uruchamianie aprowowania. [Dowiedz się więcej.](../app-provisioning/configure-automatic-user-provisioning-portal.md)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>Walidacja reguły grupy dynamicznej jest teraz dostępna w publicznej wersji zapoznawczej

**Wpisz:** Zmieniono funkcję

**Kategoria usługi:** Zarządzanie grupą

**Możliwości produktu:** Współpracy

Azure Active Directory (Azure AD) udostępnia teraz metody weryfikowania reguł grupy dynamicznej. Na karcie **Weryfikuj** reguły możesz zweryfikować regułę dynamiczną względem przykładowych elementów członkowskich grupy, aby upewnić się, że reguła działa zgodnie z oczekiwaniami. Podczas tworzenia lub aktualizowania reguł grupy dynamicznej administratorzy chcą wiedzieć, czy użytkownik lub urządzenie będzie członkiem grupy. Pomaga to ocenić, czy użytkownik lub urządzenie spełnia kryteria reguły i pomaga w rozwiązywaniu problemów, gdy członkostwo nie jest oczekiwane.

Aby uzyskać więcej informacji, zobacz [Weryfikowanie reguły dynamicznego członkostwa w grupie (wersja zapoznawcza).](../enterprise-users/groups-dynamic-rule-validation.md)

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Wynik bezpieczeństwa tożsamości — wartości domyślne zabezpieczeń i aktualizacje akcji poprawy usługi MFA

**Wpisz:** Zmieniono funkcję

**Kategoria usługi:** N/a

**Możliwości produktu:** Identity Security & Protection

**Obsługa domyślnych ustawień zabezpieczeń dla akcji ulepszania usługi Azure AD:** Ocena bezpieczeństwa firmy Microsoft będzie aktualizować akcje ulepszeń w celu obsługi wartości domyślnych zabezpieczeń w usłudze [Azure AD,](./concept-fundamentals-security-defaults.md)co ułatwia ochronę organizacji przy użyciu wstępnie skonfigurowanych ustawień zabezpieczeń dla typowych ataków. Będzie to miało wpływ na następujące akcje poprawy:

- Upewnij się, że wszyscy użytkownicy mogą ukończyć uwierzytelnianie wieloskładnikowe w celu zapewnienia bezpiecznego dostępu
- Wymaganie uwierzytelniania wieloskładnikowego dla ról administracyjnych
- Włączanie zasad w celu blokowania starszego uwierzytelniania
 
**Aktualizacje akcji poprawy usługi MFA:** Aby odzwierciedlić potrzebę zapewnienia przez firmy najwyższych zabezpieczeń podczas stosowania zasad, które działają w firmie, funkcja Microsoft Secure Score usunęła trzy akcje poprawy dotyczące uwierzytelniania wieloskładnikowego i dodała dwa.

Usunięto akcje ulepszeń:

- Rejestrowanie wszystkich użytkowników w celu uwierzytelniania wieloskładnikowego
- Wymaganie uwierzytelniania wieloskładnikowego dla wszystkich użytkowników
- Wymaganie uwierzytelniania wieloskładnikowego dla ról uprzywilejowanych usługi Azure AD

Dodano akcje ulepszeń:

- Upewnij się, że wszyscy użytkownicy mogą ukończyć uwierzytelnianie wieloskładnikowe w celu zapewnienia bezpiecznego dostępu
- Wymaganie uwierzytelniania wieloskładnikowego dla ról administracyjnych

Te nowe akcje poprawy wymagają zarejestrowania użytkowników lub administratorów na potrzeby uwierzytelniania wieloskładnikowego (MFA) w katalogu i ustanowienia odpowiedniego zestawu zasad, które pasują do potrzeb organizacji. Głównym celem jest zapewnienie elastyczności przy jednoczesnym zapewnieniu, że wszyscy użytkownicy i administratorzy mogą uwierzytelniać się przy użyciu wielu czynników lub monitów o weryfikację tożsamości na podstawie ryzyka. Może to mieć formę posiadania wielu zasad, które stosują decyzje w zakresie, lub ustawiania wartości domyślnych zabezpieczeń (od 16 marca), które umożliwiają firmie Microsoft podjęcie decyzji o tym, kiedy należy zaaplikować użytkowników do uwierzytelniania wieloskładnikowego. [Przeczytaj więcej na temat nowości w programie Microsoft Secure Score.](/microsoft-365/security/mtp/microsoft-secure-score#whats-new)

---

## <a name="march-2020"></a>Marzec 2020 r.

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march--2021"></a>Konta niezamanageowane Azure Active Directory aktualizacji B2B z marca 2021 r.

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C
 
Począwszy **od 31 marca 2021** r. firma Microsoft nie będzie już obsługiwać realizacji zaproszeń, tworząc konta i dzierżawy niezamężne usługi Azure Active Directory (Azure AD) na potrzeby scenariuszy współpracy B2B. W ramach przygotowań do tego celu zachęcamy do rezygnacji z uwierzytelniania za pomocą jednego kodu dostępu w [wiadomości e-mail.](../external-identities/one-time-passcode.md)

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Użytkownicy z domyślną rolą dostępu będą w zakresie aprowizowania

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
W przeszłości użytkownicy z domyślną rolą dostępu nie mieli zakresu aprowizowania. Słyszeliśmy opinie, że klienci chcą, aby użytkownicy z tą rolą w zakresie aprowiwizowania ujmowaliśmy w zakres. Pracujemy nad wdrożeniem zmiany w taki sposób, aby wszystkie nowe konfiguracje aprowiwizowania umożliwiały aprowizowanie użytkowników z domyślną rolą dostępu. Stopniowo zmieniamy zachowanie istniejących konfiguracji aprowizowania, aby obsługiwać aprowizowanie użytkowników z tą rolą. Nie jest wymagana żadna akcja klienta. Gdy ta zmiana zostanie w miejscu, [opublikujemy](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md) aktualizację naszej dokumentacji.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Współpraca B2B w usłudze Azure AD będzie dostępna w Microsoft Azure obsługiwanych przez dzierżawców 21Vianet (Azure (Chiny) — 21Vianet)

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C
 
Możliwości współpracy B2B usługi Azure AD zostaną udostępnione w usłudze Microsoft Azure obsługiwanej przez dzierżawy 21Vianet (Azure (Chiny) — 21Vianet), dzięki czemu użytkownicy w dzierżawie usługi Azure (Chiny) — 21Vianet mogą bezproblemowo współpracować z użytkownikami w innych dzierżawach Azure (Chiny) — 21Vianet. [Dowiedz się więcej o współpracy B2B w](/azure/active-directory/b2b/)usłudze Azure AD.

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Przeprojektowanie wiadomości e-B2B Collaboration z zaproszeniem do usługi Azure AD

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C
 
Wiadomości [e-mail](../external-identities/invitation-email-elements.md) wysyłane przez usługę zaproszenia do współpracy B2B w usłudze Azure AD w celu zapraszania użytkowników do katalogu zostaną przeprojektowane w celu bardziej czytelnego zapoznania się z informacjami o zaproszeniu i następnymi krokami użytkownika.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Zmiany zasad HomeRealmDiscovery pojawią się w dziennikach inspekcji

**Wpisz:** Stałe  
**Kategoria usługi:** Inspekcji  
**Możliwości produktu:** Monitorowanie & raportów
 
Usunięto usterkę, która nie uwzględniała zmian w zasadach [HomeRealmDiscovery](../manage-apps/configure-authentication-for-federated-users-portal.md) w dziennikach inspekcji. Teraz będzie można zobaczyć, kiedy i w jaki sposób zasady zostały zmienione oraz przez kogo. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Nowe aplikacje federowane dostępne w galerii aplikacja usługi Azure AD — marzec 2020 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja z produktami innych firm
 
W marcu 2020 r. dodaliśmy te 51 nowych aplikacji z obsługą federacji do galerii aplikacji: 

[Cisco AnyConnect,](../saas-apps/cisco-anyconnect.md) [Zoho One China](../saas-apps/zoho-one-china-tutorial.md), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co SAML App,](../saas-apps/profitco-saml-app-tutorial.md) [iPoint Service Provider](../saas-apps/ipoint-service-provider-tutorial.md), contexxt.ai [SPHERE,](https://contexxt-sphere.com/login)Nawiąż wiedzę [invictus,](../saas-apps/wisdom-by-invictus-tutorial.md) [Digital Signage](https://spark-dev.pixelnebula.com/login), Logz.io — Cloud [Observability for Engineers,](../saas-apps/logzio-cloud-observability-for-engineers-tutorial.md) [SpectrumU,](../saas-apps/spectrumu-tutorial.md) [BviContact](https://bizzcontact.app/), [Elqano SSO,](../saas-apps/elqano-sso-tutorial.md) [MarketSignShare,](http://www.signshare.com/) [CrossKnowledge Learning Suite,](../saas-apps/crossknowledge-learning-suite-tutorial.md) [Netvision Compas,](../saas-apps/netvision-compas-tutorial.md) [FCM HUB,](../saas-apps/fcm-hub-tutorial.md) [FAQ A/S Byggeweb Mobile,](https://apps.apple.com/us/app/docia/id529058757) [GoLinks,](../saas-apps/golinks-tutorial.md) [Datadog,](../saas-apps/datadog-tutorial.md) [Zscaler B2B User Portal,](../saas-apps/zscaler-b2b-user-portal-tutorial.md) [LIFT,](../saas-apps/lift-tutorial.md) [Planview Enterprise One](../saas-apps/planview-enterprise-one-tutorial.md), [WatchTeams,](https://www.devfinition.com/) [Aster,](https://demo.asterapp.io/login) [Skills Workflow,](../saas-apps/skills-workflow-tutorial.md) [Node Insight,](https://admin.nodeinsight.com/AADLogin.aspx) [IP Platform,](../saas-apps/ip-platform-tutorial.md) [InVision,](../saas-apps/invision-tutorial.md) [Pipedrive,](../saas-apps/pipedrive-tutorial.md) [Showcase Workshop,](https://app.showcaseworkshop.com/) [Greenlight Integration Platform, Greenlight](../saas-apps/greenlight-integration-platform-tutorial.md) [Compliant Access Management,](../saas-apps/greenlight-compliant-access-management-tutorial.md) [Grok](../saas-apps/grok-learning-tutorial.md)Learning,Teraskie [Online,](https://login.online.miradore.com/) [Dostoja Care,](../saas-apps/khoros-care-tutorial.md) [AskYourTeam,](../saas-apps/askyourteam-tutorial.md) [TruNarrative,](../saas-apps/trunarrative-tutorial.md) [Smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login), [Bizagi Studio for Digital Process Automation](../saas-apps/bizagi-studio-for-digital-process-automation-tutorial.md), [insuiteX](https://www.insuite.jp/), [sybo,](https://www.systexsoftware.com.tw/) [Britive,](../saas-apps/britive-tutorial.md) [WhosOffice,](../saas-apps/whosoffice-tutorial.md) [E-days,](../saas-apps/e-days-tutorial.md) [Kollective SDN,](https://portal.kollective.app/login) [Witivio,](https://app.witivio.com/) [Playvox,](https://my.playvox.com/login) [Korn Ferry 360,](../saas-apps/korn-ferry-360-tutorial.md) [Campus Campus Campus,](../saas-apps/campus-cafe-tutorial.md) [Catchpoint,](../saas-apps/catchpoint-tutorial.md) [Code42](../saas-apps/code42-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z](../saas-apps/tutorial-list.md)Azure Active Directory . Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy [aplikacji w galerii Azure Active Directory aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Usługa Azure AD B2B Collaboration dostępna w Azure Government dzierżaw

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C
 
Funkcje współpracy B2B w usłudze Azure AD są teraz dostępne między Azure Government dzierżawami.  Aby dowiedzieć się, czy dzierżawca może korzystać z tych funkcji, postępuj zgodnie z instrukcjami w te tematach Jak sprawdzić, czy współpraca [B2B](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)jest dostępna w mojej dzierżawie platformy Azure dla instytucji rządowych USA? .

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure Monitor dla usługi Azure Logs jest teraz dostępna w usłudze Azure Government

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportów
 
Azure Monitor z dziennikami usługi Azure AD jest teraz dostępna w Azure Government. Dzienniki usługi Azure AD (dzienniki inspekcji i logowania) można przekierowyć do konta magazynu, centrum zdarzeń i usługi Log Analytics. Zapoznaj się ze [szczegółową dokumentacją](../reports-monitoring/concept-activity-logs-azure-monitor.md) oraz [planami wdrażania na temat raportowania i monitorowania scenariuszy](../reports-monitoring/plan-monitoring-and-reporting.md) usługi Azure AD.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Odświeżanie ochrony tożsamości w Azure Government

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Identity Protection  
**Możliwości produktu:** Identity Security & Protection

Z przyjemnością informujemy, że teraz udostępniliśmy odświeżone środowisko Azure AD Identity Protection [](../identity-protection/overview-identity-protection.md)   witrynie [Microsoft Azure Government Portal.](https://portal.azure.us/) Aby uzyskać więcej informacji, zobacz nasz [wpis w blogu z ogłoszeniem](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Odzyskiwanie po awarii: pobieranie i przechowywanie konfiguracji aprowacji

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
Usługa aprowizowania Usługi Azure AD zapewnia bogaty zestaw funkcji konfiguracji. Klienci muszą mieć możliwość zapisania konfiguracji, aby mogli odwoływać się do jej później lub wrócić do znanej dobrej wersji. Dodaliśmy możliwość pobrania konfiguracji aprowizowania jako pliku JSON i przekazania jej w razie potrzeby. [Dowiedz się więcej](../app-provisioning/export-import-provisioning-configuration.md).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Samoobsługowe resetowanie hasła (samoobsługowe resetowanie hasła) wymaga teraz dwóch bram dla administratorów w Microsoft Azure obsługiwanych przez firmę 21Vianet (Azure (Chiny) — 21Vianet) 

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi: Self-Service** resetowania hasła  
**Możliwości produktu:** Identity Security & Protection
 
Wcześniej w usłudze Microsoft Azure obsługiwanej przez firmę 21Vianet (Azure (Chiny) — 21Vianet) administratorzy korzystający z samoobsługowego resetowania hasła (SSPR, self-service password reset) w celu resetowania własnych haseł potrzebowali tylko jednej "bramy" (wyzwania), aby potwierdzić swoją tożsamość. W chmurach publicznych i innych chmurach krajowych administratorzy zwykle muszą użyć dwóch bram, aby potwierdzić swoją tożsamość podczas korzystania z funkcji SSPR. Jednak ponieważ nie obsługiliśmy wiadomości SMS ani połączeń telefonicznych w Azure (Chiny) — 21Vianet, zezwoliliśmy administratorom na resetowanie hasła z jedną bramą.

Tworzymy parzystość funkcji SSPR między Azure (Chiny) — 21Vianet a chmurą publiczną. W przyszłości administratorzy muszą używać dwóch bram podczas korzystania z funkcji SSPR. Obsługiwane będą wiadomości SMS, połączenia telefoniczne oraz powiadomienia i kody aplikacji Authenticator. [Dowiedz się więcej](../authentication/concept-sspr-policy.md#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>Długość hasła jest ograniczona do 256 znaków

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnienia (identyfikatory logowania)  
**Możliwości produktu:** Uwierzytelnianie użytkownika
 
Aby zapewnić niezawodność usługi Azure AD, hasła użytkowników są teraz ograniczone do 256 znaków. Użytkownicy z hasłami dłuższymi niż to będą proszeni o zmianę hasła podczas kolejnych logowań, kontaktując się z administratorem lub za pomocą funkcji samoobsługowego resetowania hasła.

Ta zmiana została włączona 13 marca 2020 r. o godzinie 10:00 PST (18:00 UTC), a błąd to AADSTS 50052, InvalidPasswordExceedsMaxLength. Aby uzyskać [więcej informacji,](../develop/reference-breaking-changes.md#user-passwords-will-be-restricted-to-256-characters) zobacz powiadomienie o zmianie istotnej.

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Dzienniki logowania usługi Azure AD są teraz dostępne dla wszystkich bezpłatnych dzierżawców za pośrednictwem Azure Portal

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportów
 
Od teraz klienci, którzy mają bezpłatne dzierżawy, mogą uzyskać dostęp do dzienników logowania usługi [Azure AD](../reports-monitoring/concept-sign-ins.md) z witryny Azure Portal przez maksymalnie 7 dni. Wcześniej dzienniki logowania były dostępne tylko dla klientów z Azure Active Directory — wersja Premium licencji. Dzięki tej zmianie wszyscy dzierżawcy mogą uzyskać dostęp do tych dzienników za pośrednictwem portalu.

> [!NOTE]
> Klienci nadal potrzebują licencji Premium (Azure Active Directory — wersja Premium P1 lub P2), aby uzyskać dostęp do dzienników logowania za pośrednictwem Microsoft Graph API i Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Opcja cofania wsparcia dla grup dla całego katalogu w ustawieniach ogólnych grup na Azure Portal

**Wpisz:** Przestarzałe  
**Kategoria usługi:** Zarządzanie grupą  
**Możliwości produktu:** Współpracy

Aby zapewnić klientom bardziej elastyczny sposób tworzenia grup w całym katalogu, które najlepiej  spełniają ich potrzeby, zastąpiliśmy opcję Grupy dla całego katalogu w ustawieniach Ogólne grupy w usłudze Azure Portal linkiem do dokumentacji grup  >   [dynamicznych.](../enterprise-users/groups-dynamic-membership.md) Ulepszyliśmy naszą dokumentację tak, aby zawierała więcej instrukcji, dzięki czemu administratorzy mogą tworzyć grupy wszystkich użytkowników, które obejmują lub wykluczają użytkowników-gości.

---

## <a name="february-2020"></a>Luty 2020 r.

### <a name="upcoming-changes-to-custom-controls"></a>Nadchodzące zmiany w kontrolkach niestandardowych

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Mfa  
**Możliwości produktu:** Identity Security & Protection
 
Planujemy zastąpienie bieżącej wersji zapoznawczej kontrolek niestandardowych podejściem, które umożliwia bezproblemową współpracę możliwości uwierzytelniania dostarczanych przez partnerów z Azure Active Directory użytkownikami i użytkownikami. Obecnie rozwiązania uwierzytelniania wieloskładnikowego partnerów mają następujące ograniczenia: działają tylko po wprowadzeniu hasła; nie służą one jako uwierzytelnianie wieloskładnikowe do uwierzytelniania krokowego w innych kluczowych scenariuszach; i nie integrują się z funkcjami zarządzania poświadczeniami użytkownika końcowego ani poświadczeniami administracyjnymi. Nowa implementacja umożliwi stosowanie czynników uwierzytelniania dostarczanych przez partnerów wraz z wbudowanymi czynnikami dla kluczowych scenariuszy, takich jak rejestracja, użycie, oświadczenia usługi MFA, uwierzytelnianie krok po kroku, raportowanie i rejestrowanie. 

Kontrolki niestandardowe będą nadal obsługiwane w wersji zapoznawczej wraz z nowym projektem, dopóki nie osiągnie ogólnej dostępności. W tym momencie zapewnimy klientom czas na migrację do nowego projektu. Ze względu na ograniczenia bieżącego podejścia nie będziemy dołączać nowych dostawców, dopóki nowy projekt nie będzie dostępny. Ściśle współpracujemy z klientami i dostawcami i będziemy przekazywać informacje o osi czasu, gdy będziemy się zbliżać. [Dowiedz się więcej](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Ocena bezpieczeństwa tożsamości — aktualizacje akcji ulepszania usługi MFA

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Mfa  
**Możliwości produktu:** Identity Security & Protection
 
Aby odzwierciedlić potrzebę zapewnienia przez firmy najwyższych zabezpieczeń podczas stosowania zasad, które działają w firmie, funkcja Microsoft Secure Score usuwa trzy akcje ulepszeń dotyczące uwierzytelniania wieloskładnikowego (MFA) i dodaje dwa.

Zostaną usunięte następujące akcje ulepszeń:

- Rejestrowanie wszystkich użytkowników w celu uwierzytelniania wieloskładnikowego
- Wymaganie uwierzytelniania wieloskładnikowego dla wszystkich użytkowników
- Wymaganie uwierzytelniania wieloskładnikowego dla ról uprzywilejowanych usługi Azure AD

Zostaną dodane następujące akcje poprawy:

- Upewnij się, że wszyscy użytkownicy mogą ukończyć uwierzytelniania wieloskładnikowego w celu zapewnienia bezpiecznego dostępu
- Wymaganie uwierzytelniania wieloskładnikowego dla ról administracyjnych

Te nowe akcje ulepszeń będą wymagały zarejestrowania użytkowników lub administratorów w celu uwierzytelniania wieloskładnikowego w katalogu i ustanowienia odpowiedniego zestawu zasad, które pasują do potrzeb organizacji. Głównym celem jest zapewnienie elastyczności przy jednoczesnym zapewnieniu wszystkim użytkownikom i administratorom możliwości uwierzytelniania przy użyciu wielu czynników lub monitów o weryfikację tożsamości na podstawie ryzyka. Może to mieć postać ustawiania wartości domyślnych zabezpieczeń, które umożliwiają firmie Microsoft podjęcie decyzji o tym, kiedy należy zakwestionować użytkowników w celu uwierzytelniania wieloskładnikowego, lub mieć wiele zasad, które stosują decyzje o zakresie. W ramach tych aktualizacji akcji poprawy zasady ochrony punktu odniesienia nie będą już uwzględniane w obliczeniach oceniania. [Przeczytaj więcej na temat nadchodzących wyników bezpieczeństwa firmy Microsoft.](/microsoft-365/security/mtp/microsoft-secure-score-whats-coming)

---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD Domain Services SKU

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Azure AD Domain Services  
**Możliwości produktu:** Azure AD Domain Services
 
Słyszeliśmy opinie, że Azure AD Domain Services chcą większej elastyczności podczas wybierania poziomów wydajności dla swoich wystąpień. Od 1 lutego 2020 r. przeszliśmy z modelu dynamicznego (w którym usługa Azure AD określa wydajność i warstwę cenową na podstawie liczby obiektów) na model samodzielnego wyboru. Teraz klienci mogą wybrać warstwę wydajności, która odpowiada ich środowisku. Ta zmiana umożliwia również włączenie nowych scenariuszy, takich jak lasy zasobów i funkcje Premium, takie jak codzienne kopie zapasowe. Liczba obiektów jest teraz nieograniczona dla wszystkich jednostki SKU, ale będziemy nadal oferować sugestie dotyczące liczby obiektów dla każdej warstwy.

**Nie jest wymagana żadna natychmiastowa akcja klienta.** W przypadku istniejących klientów nowa warstwa domyślna jest określana w warstwie dynamicznej, która była używana 1 lutego 2020 r. Ta zmiana nie ma wpływu na cenę ani wydajność. W przyszłości Azure AD DS będą musieli ocenić wymagania dotyczące wydajności w przypadku zmiany charakterystyki rozmiaru katalogu i obciążenia. Przełączanie między warstwami usług nadal będzie operacją bez przestojów i nie będziemy już automatycznie przenosić klientów do nowych warstw w oparciu o rozwój ich katalogu. Ponadto nie będzie wzrostu cen, a nowe ceny będą zgodne z naszym bieżącym modelem rozliczeń. Aby uzyskać więcej informacji, zobacz [dokumentację Azure AD DS jednostki SKU](../../active-directory-domain-services/administration-concepts.md#azure-ad-ds-skus) i stronę [Azure AD Domain Services cennika.](https://azure.microsoft.com/pricing/details/active-directory-ds/)

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Nowe aplikacje federowane dostępne w galerii aplikacja usługi Azure AD — luty 2020 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja z produktami innych firm
 
W lutym 2020 r. dodaliśmy te 31 nowych aplikacji z obsługą federacji do galerii aplikacji: 

[IamIP Patent Platform,](../saas-apps/iamip-patent-platform-tutorial.md) [Experience Cloud,](../saas-apps/experience-cloud-tutorial.md) [NS1 SSO for Azure,](../saas-apps/ns1-sso-azure-tutorial.md) [Barracuda Email Security Service,](https://ess.barracudanetworks.com/sso/azure) [ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad), [In Case of Crisis - Online Portal,](../saas-apps/in-case-of-crisis-online-portal-tutorial.md) [BIC Cloud Design,](../saas-apps/bic-cloud-design-tutorial.md) [Beekeeper Azure AD Data Connector,](../saas-apps/beekeeper-azure-ad-data-connector-tutorial.md) [Korn Ferry Assessments,](https://www.kornferry.com/solutions/kf-digital/kf-assess) [Verkada Command,](../saas-apps/verkada-command-tutorial.md) [Splashtop,](../saas-apps/splashtop-tutorial.md) [Syxsense,](../saas-apps/syxsense-tutorial.md) [EAB Navigate](../saas-apps/eab-navigate-tutorial.md), New Relic [(wersja ograniczona),](../saas-apps/new-relic-limited-release-tutorial.md) [Thulium,](https://admin.thulium.com/login/instance) [Ticket Manager,](../saas-apps/ticketmanager-tutorial.md)Template [Chooser for Teams](https://links.officeatwork.com/templatechooser-download-teams), [Beesy,](https://www.beesy.me/index.php/site/login) [Health Support System,](../saas-apps/health-support-system-tutorial.md) [MURAL,](https://app.mural.co/signup) [Hive,](../saas-apps/hive-tutorial.md) [LavaDo,](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview) [Wakelet,](https://wakelet.com/login) [Firmex VDR,](../saas-apps/firmex-vdr-tutorial.md) [ThingLink for Teachers and Schools](https://www.thinglink.com/), [Coda,](../saas-apps/coda-tutorial.md) [NearpodApp,](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product) [WEDO,](../saas-apps/wedo-tutorial.md) [InvitePeople,](https://invitepeople.com/login) [Reprints Desk - Article Galaxy,](../saas-apps/reprints-desk-article-galaxy-tutorial.md) [TeamViewer](../saas-apps/teamviewer-tutorial.md)

 
Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z](../saas-apps/tutorial-list.md)Azure Active Directory . Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy aplikacji w [galerii Azure Active Directory aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nowe łączniki aprowizowania w galerii aplikacji usługi Azure AD — luty 2020 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja z produktami innych firm
 
Teraz możesz zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla tych nowo zintegrowanych aplikacji:

- [Mixpanel](../saas-apps/mixpanel-provisioning-tutorial.md)
- [TeamViewer](../saas-apps/teamviewer-provisioning-tutorial.md)
- [Azure Databricks](/azure/databricks/administration-guide/users-groups/scim/aad)
- [PureCloud by Genesys](../saas-apps/purecloud-by-genesys-provisioning-tutorial.md)
- [Zapier](../saas-apps/zapier-provisioning-tutorial.md)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu zautomatyzowanego aprowizowania kont użytkowników, zobacz [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Automatyzowanie aprowzowania użytkowników w aplikacjach SaaS za pomocą usługi Azure AD).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Obsługa kluczy zabezpieczeń FIDO2 w środowiskach hybrydowych w usłudze Azure AD

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (identyfikatory logowania)  
**Możliwości produktu:** Uwierzytelnianie użytkownika
 
Ogłaszamy publiczną obsługę kluczy zabezpieczeń FIDO2 w środowiskach hybrydowych w usłudze Azure AD w wersji zapoznawczej. Użytkownicy mogą teraz używać kluczy zabezpieczeń FIDO2, aby logować się na swoich urządzeniach hybrydowych przyłączonych do usługi Azure AD Windows 10 i bezproblemowo logować się do zasobów lokalnych i w chmurze. Obsługa środowisk hybrydowych była najczęściej żądaną funkcją od naszych klientów bez hasła, ponieważ początkowo uruchomiliśmy publiczną podgląd pomocy technicznej FIDO2 na urządzeniach przyłączonych do usługi Azure AD. Uwierzytelnianie bez hasła przy użyciu zaawansowanych technologii, takich jak kryptografia biometryczna i kryptografia klucza publicznego/prywatnego, zapewnia wygodę i łatwość użycia przy jednoczesnym zachowaniu bezpieczeństwa. W tej publicznej wersji zapoznawczej można teraz używać nowoczesnego uwierzytelniania, takiego jak klucze zabezpieczeń FIDO2, w celu uzyskania dostępu do tradycyjnych zasobów usługi Active Directory. Aby uzyskać więcej informacji, przejdź do [tematu SSO to on-premises resources (Logowanie jednokrotne do zasobów lokalnych).](../authentication/howto-authentication-passwordless-security-key-on-premises.md) 

Aby rozpocząć pracę, odwiedź stronę Włączanie kluczy zabezpieczeń [FIDO2](../authentication/howto-authentication-passwordless-security-key.md) dla dzierżawy, aby uzyskać instrukcje krok po kroku. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>Nowe środowisko Moje konto jest teraz ogólnie dostępne

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Mój profil/konto  
**Możliwości produktu:** Środowisko użytkownika końcowego
 
Moje konto, pierwsze miejsca do obsługi wszystkich potrzeb w zakresie zarządzania kontami użytkowników końcowych, jest teraz ogólnie dostępne. Użytkownicy końcowi mogą uzyskać dostęp do tej nowej witryny za pośrednictwem adresu URL lub nagłówka nowego Moje aplikacje użytkownika. Dowiedz się więcej o wszystkich możliwościach samoobsługi dostępnych w nowych możliwościach na stronie [Omówienie portalu Moje konto.](../user-help/my-account-portal-overview.md)

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Aktualizowanie adresu URL witryny konta do myaccount.microsoft.com

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Mój profil/konto  
**Możliwości produktu:** Środowisko użytkownika końcowego
 
Nowe środowisko użytkownika końcowego Moje konto będzie aktualizować swój adres URL `https://myaccount.microsoft.com` do w następnym miesiącu. Aby uzyskać więcej informacji na temat obsługi i wszystkich funkcji samoobsługi konta, które oferuje użytkownikom, zobacz Pomoc [w portalu Moje konto.](../user-help/my-account-portal-overview.md)

---

## <a name="january-2020"></a>Styczeń 2020 r.
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Nowy portal Moje aplikacje jest teraz ogólnie dostępny

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Moje aplikacje  
**Możliwości produktu:** Środowisko użytkownika końcowego
 
Uaktualnij organizację do nowego portalu Moje aplikacje, który jest teraz ogólnie dostępny. Aby uzyskać więcej informacji na temat nowego portalu i kolekcji, zobacz [Tworzenie kolekcji w Moje aplikacje portalu.](../manage-apps/access-panel-collections.md)

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Nazwy obszarów roboczych w usłudze Azure AD zostały zmienione na kolekcje

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Moje aplikacje   
**Możliwości produktu:** Środowisko użytkownika końcowego
 
Obszary robocze, które administratorzy filtrów mogą skonfigurować w celu organizowania aplikacji użytkowników, będą teraz nazywane kolekcjami. Aby uzyskać więcej informacji na temat sposobu ich konfigurowania, zobacz Create collections on the Moje aplikacje portal (Tworzenie [kolekcji w Moje aplikacje portal).](../manage-apps/access-panel-collections.md)

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C rejestracji i logowania za pomocą zasad niestandardowych (publiczna wersja zapoznawcza)

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2C — zarządzanie tożsamościami konsumentów  
**Możliwości produktu:** B2B/B2C
 
Dzięki funkcji rejestracji i logowania przy użyciu numeru telefonu deweloperzy i przedsiębiorstwa mogą zezwolić swoim klientom na rejestracja i logowanie się przy użyciu hasła raz wysłanego na numer telefonu użytkownika za pośrednictwem wiadomości SMS. Ta funkcja umożliwia również klientowi zmianę numeru telefonu w przypadku utraty dostępu do telefonu. Dzięki możliwościom zasad niestandardowych oraz rejestracji i logowania za pomocą telefonu deweloperzy i przedsiębiorstwa mogą komunikować swoją markę za pośrednictwem dostosowywania strony. Dowiedz się, jak [skonfigurować logowanie](../../active-directory-b2c/phone-authentication-user-flows.md)i logowanie za pomocą zasad niestandardowych na stronie Azure AD B2C .
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Nowe łączniki aprowizowania w galerii aplikacji usługi Azure AD — styczeń 2020 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja z produktami innych firm
 
Teraz możesz zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla tych nowo zintegrowanych aplikacji:

- [Promapp]( ../saas-apps/promapp-provisioning-tutorial.md)
- [Zscaler Private Access](../saas-apps/zscaler-private-access-provisioning-tutorial.md)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu zautomatyzowanego aprowizowania kont użytkowników, zobacz [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Automatyzowanie aprowzowania użytkowników w aplikacjach SaaS za pomocą usługi Azure AD).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nowe aplikacje federowane dostępne w galerii aplikacja usługi Azure AD — styczeń 2020 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja z produktami innych firm
 
W styczniu 2020 r. dodaliśmy te 33 nowe aplikacje z obsługą federacji do galerii aplikacji: 

[JOSA,](../saas-apps/josa-tutorial.md) [Fastly Edge Cloud,](../saas-apps/fastly-edge-cloud-tutorial.md) [Terraform Enterprise,](../saas-apps/terraform-enterprise-tutorial.md) [Spintr SSO,](../saas-apps/spintr-sso-tutorial.md) [Abibot Netlogistik,](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik) [SkyKick,](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access) [Upshotly,](../saas-apps/upshotly-tutorial.md) [LeaveBot,](https://appsource.microsoft.com/en-us/product/office/WA200001175) [DataCamp,](../saas-apps/datacamp-tutorial.md) [TripActions,](../saas-apps/tripactions-tutorial.md) [SmartWork,](https://www.intumit.com/teams-smartwork/) [Dotcom-Monitor,](../saas-apps/dotcom-monitor-tutorial.md) [SSNION — Azure AD SSO Gateway for Oracle E-Business Suite — EBS, PeopleSoft, i JDE](../saas-apps/ssogen-tutorial.md), [Hosted MyCirqa SSO,](../saas-apps/hosted-mycirqa-sso-tutorial.md) [Yuhu Property Management Platform,](../saas-apps/yuhu-property-management-platform-tutorial.md) [LumApps,](https://sites.lumapps.com/login) [Upwork Enterprise,](../saas-apps/upwork-enterprise-tutorial.md) [Talentsoft,](../saas-apps/talentsoft-tutorial.md) [SmartDB for Microsoft Teams,](http://teams.smartdb.jp/login/) [PressPage,](../saas-apps/presspage-tutorial.md) [ContractSafe Saml2 SSO,](../saas-apps/contractsafe-saml2-sso-tutorial.md) [Maxient Conduct Manager Software,](../saas-apps/maxient-conduct-manager-software-tutorial.md) [Helpshift](../saas-apps/helpshift-tutorial.md), [PortalTalk 365,](https://www.portaltalk.com/) [CoreView,](https://portal.coreview.com/)Squelch Cloud Office365 Connector, [PingFlow Authentication,](https://app-staging.pingview.io/) [ PrinterLogic SaaS,](../saas-apps/printerlogic-saas-tutorial.md) [Taskize Connect,](../saas-apps/taskize-connect-tutorial.md) [Sandwai,](https://app.sandwai.com/) [EZRentOut,](../saas-apps/ezrentout-tutorial.md) [AssetSonar,](../saas-apps/assetsonar-tutorial.md) [Akari Asystent wirtualny](https://akari.io/akari-virtual-assistant/)

Aby uzyskać więcej informacji na temat aplikacji, zobacz Integracja aplikacji [SaaS z](../saas-apps/tutorial-list.md)Azure Active Directory . Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy aplikacji w galerii Azure Active Directory [aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="two-new-identity-protection-detections"></a>Dwa nowe wykrycia ochrony tożsamości

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Identity Protection  
**Możliwości produktu:** Identity Security & Protection
 
Dodaliśmy dwa nowe typy wykrywania połączonego logowania do usługi Identity Protection: Podejrzane reguły obsługi skrzynki odbiorczej i Niemożliwa podróż. Te wykrycia w trybie offline są wykrywane przez Microsoft Cloud App Security (MCAS) i wpływają na ryzyko związane z użytkownikiem i logowaniem w u programie Identity Protection. Aby uzyskać więcej informacji na temat tych wykrywania, zobacz nasze typy ryzyka [związanego z logowaniem](../identity-protection/concept-identity-protection-risks.md#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Zmiana przerywana: Fragmenty identyfikatora URI nie będą przekierowywani przez przekierowanie logowania

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnienia (identyfikatory logowania)  
**Możliwości produktu:** Uwierzytelnianie użytkowników
 
Począwszy od 8 lutego 2020 r., gdy żądanie jest wysyłane do usługi login.microsoftonline.com w celu zalogowania użytkownika, usługa dołącza pusty fragment do żądania.  Zapobiega to atakom typu przekierowania przez upewnienie się, że przeglądarka wyczyści dowolny istniejący fragment w żądaniu. Żadna aplikacja nie powinna mieć zależności od tego zachowania. Aby uzyskać więcej informacji, zobacz [Istotne zmiany w](../develop/reference-breaking-changes.md#february-2020) dokumentacji platformy tożsamości firmy Microsoft.

---

## <a name="december-2019"></a>Grudzień 2019 r.

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integrowanie aprowizowania rozwiązania SAP SuccessFactors z usługą Azure AD i lokalną usługą AD (publiczna wersja zapoznawcza)

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości

Teraz możesz zintegrować rozwiązanie SAP SuccessFactors jako autorytatywne źródło tożsamości w usłudze Azure AD. Ta integracja pomaga zautomatyzować cały cykl życia tożsamości, w tym używanie zdarzeń opartych na kadrach, takich jak nowi zatrudnienie lub zakończenia, w celu kontrolowania aprowzowania kont usługi Azure AD.

Aby uzyskać więcej informacji na temat konfigurowania aprowizowania ruchu przychodzącego rozwiązania SAP SuccessFactors w usłudze Azure AD, zobacz samouczek Konfigurowanie automatycznej aprowyzacji rozwiązania [SAP SuccessFactors.](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Obsługa dostosowanych wiadomości e-mail w Azure AD B2C (publiczna wersja zapoznawcza)

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2C — zarządzanie tożsamościami konsumentów  
**Możliwości produktu:** B2B/B2C

Teraz możesz używać Azure AD B2C do tworzenia dostosowanych wiadomości e-mail, gdy użytkownicy zarejestrują się, aby korzystać z aplikacji. Korzystając z funkcji DisplayControls (obecnie w wersji zapoznawczej) i dostawcy poczty e-mail innej firmy (takiego jak [SendGrid,](https://sendgrid.com/) [SparkPost](https://sparkpost.com/)lub niestandardowy interfejs API REST), możesz użyć własnego szablonu poczty e-mail,  adresu od i tekstu tematu, a także obsługiwać ustawienia lokalizacji i niestandardowe hasła jednorazowego (OTP).

Aby uzyskać więcej informacji, zobacz [Niestandardowa weryfikacja wiadomości e-mail w Azure Active Directory B2C](../../active-directory-b2c/custom-email-sendgrid.md).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Zastępowanie zasad bazowych wartościami domyślnymi zabezpieczeń

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Zabezpieczenia i ochrona tożsamości

W ramach modelu bezpiecznego domyślnie uwierzytelniania usuwamy istniejące zasady ochrony linii bazowej ze wszystkich dzierżaw. To usunięcie zostanie ukończone pod koniec lutego. Zastąpienie tych zasad ochrony punktów odniesienia to wartości domyślne zabezpieczeń. Jeśli używasz zasad ochrony punktu odniesienia, musisz zaplanować przejście do nowych zasad domyślnych zabezpieczeń lub dostępu warunkowego. Jeśli te zasady nie zostały użyte, nie trzeba nic zrobić.

Aby uzyskać więcej informacji na temat nowych wartości domyślnych zabezpieczeń, zobacz [Co to są ustawienia domyślne zabezpieczeń?](./concept-fundamentals-security-defaults.md) Aby uzyskać więcej informacji na temat zasad dostępu warunkowego, zobacz [Typowe zasady dostępu warunkowego.](../conditional-access/concept-conditional-access-policy-common.md)

---

## <a name="november-2019"></a>Listopad 2019 r.

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Obsługa atrybutu SameSite i przeglądarki Chrome 80

**Wpisz:** Planowanie zmian  
**Kategoria usługi:** Uwierzytelnienia (identyfikatory logowania)  
**Możliwości produktu:** Uwierzytelnianie użytkowników

W ramach modelu bezpiecznych domyślnie plików cookie przeglądarka Chrome 80 zmienia sposób traktowania plików cookie bez `SameSite` atrybutu. Każdy plik cookie, który nie określa atrybutu, będzie traktowany tak, jakby był ustawiony na wartość , co spowoduje zablokowanie przez przeglądarkę Chrome niektórych scenariuszy udostępniania plików cookie między domenami, od których może zależeć `SameSite` `SameSite=Lax` aplikacja. Aby zachować starsze zachowanie przeglądarki Chrome, można użyć atrybutu i dodać dodatkowy atrybut, dzięki czemu pliki cookie między witrynami będą dostępne tylko za pośrednictwem `SameSite=None` `Secure` połączeń HTTPS. Przeglądarka Chrome ma ukończyć tę zmianę do 4 lutego 2020 r.

Zalecamy wszystkim naszym deweloperom testowanie aplikacji przy użyciu tych wskazówek:

- Ustaw wartość domyślną ustawienia **Użyj bezpiecznego pliku cookie** na **Tak.**

- Ustaw wartość domyślną **atrybutu SameSite** na **Brak.**

- Dodaj dodatkowy atrybut `SameSite` **Secure**.

Aby uzyskać więcej informacji, zobacz Nadchodzące zmiany plików [cookie SameSite](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) w usługach ASP.NET i ASP.NET Core oraz Potencjalne zakłócenia w witrynach internetowych klientów oraz produktach i usługach firmy Microsoft w przeglądarce Chrome w wersji [79 i nowszych.](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Nowa poprawka dla Microsoft Identity Manager (MIM) 2016 z dodatkiem Service Pack 2 (SP2)

**Wpisz:** Stałe  
**Kategoria usługi:** Microsoft Identity Manager  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości

Pakiet zbiorczy poprawek (kompilacja 4.6.34.0) jest dostępny dla wersji Microsoft Identity Manager (MIM) 2016 z dodatkiem Service Pack 2 (SP2). Ten pakiet zbiorczy rozwiązuje problemy i dodaje ulepszenia opisane w sekcji "Problemy rozwiązane i ulepszenia dodane w tej aktualizacji".

Aby uzyskać więcej informacji i pobrać pakiet poprawek, zobacz [Microsoft Identity Manager 2016 Service Pack 2 (kompilacja 4.6.34.0)](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r)Pakiet zbiorczy aktualizacji jest dostępny.

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Nowy AD FS aktywności aplikacji ułatwiający migrację aplikacji do usługi Azure AD (publiczna wersja zapoznawcza)

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej

Użyj nowego Active Directory Federation Services aplikacji (AD FS) w witrynie Azure Portal, aby określić, które aplikacje można migrować do usługi Azure AD. Raport ocenia wszystkie aplikacje AD FS pod kątem zgodności z usługą Azure AD, sprawdza wszelkie problemy i zapewnia wskazówki dotyczące przygotowywania poszczególnych aplikacji do migracji.

Aby uzyskać więcej informacji, zobacz [Use the AD FS application activity report to migrate applications to Azure AD](../manage-apps/migrate-adfs-application-activity.md)(Migrowanie aplikacji do usługi Azure AD przy użyciu raportu aktywności aplikacji).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Nowy przepływ pracy dla użytkowników w celu zażądania zgody administratora (publiczna wersja zapoznawcza)

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Access Control

Nowy przepływ pracy zgody administratora umożliwia administratorom udzielenie dostępu do aplikacji, które wymagają zatwierdzenia przez administratora. Jeśli użytkownik spróbuje uzyskać dostęp do aplikacji, ale nie może wyrazić zgody, może teraz wysłać żądanie zatwierdzenia przez administratora. Żądanie jest wysyłane pocztą e-mail i umieszczane w kolejce dostępnej z Azure Portal do wszystkich administratorów wyznaczonych jako recenzentzy. Gdy recenzent podejmuje działania w przypadku oczekującego żądania, użytkownicy żądający są powiadomieni o akcji.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie przepływu pracy wyrażania zgody przez administratora (wersja zapoznawcza).](../manage-apps/configure-admin-consent-workflow.md)

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nowe aplikacja usługi Azure AD konfiguracji tokenu rejestracji aplikacji do zarządzania opcjonalnymi oświadczeniami (publiczna wersja zapoznawcza)

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Środowisko dewelopera

Nowy blok **aplikacja usługi Azure AD konfiguracji tokenu** rejestracji aplikacji Azure Portal teraz pokazuje deweloperom aplikacji dynamiczną listę opcjonalnych oświadczeń dla swoich aplikacji. To nowe środowisko pomaga usprawnić migracje aplikacji usługi Azure AD i zminimalizować błędy konfiguracji opcjonalnych oświadczeń.

Aby uzyskać więcej informacji, zobacz [Provide optional claims to your Azure AD app (Zapewnianie opcjonalnych oświadczeń w aplikacji usługi Azure AD).](../develop/active-directory-optional-claims.md)

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Nowy dwuetapowy przepływ pracy zatwierdzania w usłudze Azure AD entitlement management (publiczna wersja zapoznawcza)

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Zarządzanie uprawnieniami

Wprowadziliśmy nowy dwuetapowy przepływ pracy zatwierdzania, który umożliwia wymaganie od dwóch osób zatwierdzających zatwierdzenia żądania użytkownika do pakietu dostępu. Można go na przykład ustawić tak, aby menedżer żądający użytkownika musiał najpierw zatwierdzić, a następnie można również wymagać zatwierdzenia przez właściciela zasobu. Jeśli jedna z osób zatwierdzających nie zatwierdzi, dostęp nie zostanie udzielony.

Aby uzyskać więcej informacji, zobacz [Zmienianie ustawień żądania i zatwierdzania dla pakietu dostępu w usłudze Azure AD entitlement management](../governance/entitlement-management-access-package-request-policy.md).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Aktualizacje strony Moje aplikacje obszarów roboczych wraz z nowymi obszarami roboczymi (publiczna wersja zapoznawcza)

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwości produktu:** integracja z produktami innych firm

Teraz możesz dostosować sposób wyświetlania i uzyskiwania dostępu do odświeżanych danych przez użytkowników w Moje aplikacje organizacji. To nowe środowisko obejmuje również nową funkcję obszarów roboczych, która ułatwia użytkownikom znajdowanie i organizowanie aplikacji.

Aby uzyskać więcej informacji na temat nowego środowiska Moje aplikacje i tworzenia obszarów roboczych, zobacz Tworzenie obszarów roboczych w [Moje aplikacje portalu.](../manage-apps/access-panel-collections.md)

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Obsługa identyfikatorów społecznościowych Google na rzecz współpracy B2B w usłudze Azure AD (ogólna dostępność)

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** B2b  
**Możliwości produktu:** Uwierzytelnianie użytkowników

Nowa obsługa identyfikatorów społecznościowych Google (kont Gmail) w usłudze Azure AD ułatwia współpracę użytkownikom i partnerom. Partnerzy nie muszą już tworzyć nowego konta specyficznego dla firmy Microsoft ani zarządzać nimi. Aplikacja Microsoft Teams teraz w pełni obsługuje użytkowników google na wszystkich klientach oraz w wspólnych punktach końcowych uwierzytelniania związanych z dzierżawą.

Aby uzyskać więcej informacji, zobacz Add Google as an identity provider for B2B guest users (Dodawanie firmy Google jako [dostawcy tożsamości dla użytkowników-gości B2B).](../external-identities/google-federation.md)

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Microsoft Edge Mobile na temat dostępu warunkowego i logowania pojedynczego (ogólna dostępność)

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Identity Security & Protection

Usługa Azure AD dla Microsoft Edge w systemach iOS i Android obsługuje teraz usługę Azure AD z Sign-On dostępem warunkowym:

- **Microsoft Edge logowania jednokrotnego:** Logowanie pojedyncze jest teraz dostępne na klientach natywnych (takich jak Microsoft Outlook i Microsoft Edge) dla wszystkich aplikacji połączonych z usługą Azure AD.

- **Microsoft Edge warunkowego:** Za pomocą zasad dostępu warunkowego opartego na aplikacji użytkownicy muszą używać Microsoft Intune chronionych przeglądarkami, takimi jak Microsoft Edge.

Aby uzyskać więcej informacji na temat dostępu warunkowego i logowania jednokrotnego za pomocą usługi Microsoft Edge, zobacz wpis w blogu [Microsoft Edge Mobile Support for Conditional Access and Single Sign-on Now Generally Available](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) (Obsługa aplikacji mobilnych dla dostępu warunkowego i logowania jednokrotnego teraz ogólnie dostępne). Aby uzyskać więcej informacji na temat [](../conditional-access/app-based-conditional-access.md) sposobu konfiguracji aplikacji klienckich przy użyciu dostępu warunkowego opartego na aplikacji lub dostępu warunkowego opartego na urządzeniach, [](../conditional-access/require-managed-devices.md)zobacz Zarządzanie dostępem do Internetu przy użyciu przeglądarki chronionej przez zasady usługi [Microsoft Intune.](/intune/apps/app-configuration-managed-browser)

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Zarządzanie uprawnieniami usługi Azure AD (ogólna dostępność)

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Zarządzanie uprawnieniami

Zarządzanie uprawnieniami usługi Azure AD to nowa funkcja zarządzania tożsamościami, która ułatwia organizacjom zarządzanie cyklem życia tożsamości i dostępu na dużą skalę. Ta nowa funkcja ułatwia automatyzowanie przepływów pracy żądań dostępu, przypisań dostępu, przeglądów i wygaśnięcia w grupach, aplikacjach i witrynach usługi SharePoint Online.

Dzięki zarządzaniu uprawnieniami usługi Azure AD możesz wydajniej zarządzać dostępem zarówno dla pracowników, jak i dla użytkowników spoza organizacji, którzy potrzebują dostępu do tych zasobów.

Aby uzyskać więcej informacji, zobacz [Co to jest zarządzanie uprawnieniami usługi Azure AD?](../governance/entitlement-management-overview.md#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatyzowanie aprowzowania kont użytkowników dla tych nowo obsługiwanych aplikacji SaaS

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja z produktami innych firm  

Teraz możesz zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla tych nowo zintegrowanych aplikacji:

[SAP Cloud Platform Identity Authentication Service,](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md) [RingCentral,](../saas-apps/ringcentral-provisioning-tutorial.md) [SpaceIQ,](../saas-apps/spaceiq-provisioning-tutorial.md) [Miro,](../saas-apps/miro-provisioning-tutorial.md) [Cloudgate,](../saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial.md) [Infor CloudSuite,](../saas-apps/infor-cloudsuite-provisioning-tutorial.md) [OfficeSpace Software,](../saas-apps/officespace-software-provisioning-tutorial.md) [Priority Matrix](../saas-apps/priority-matrix-provisioning-tutorial.md)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu zautomatyzowanego aprowizowania kont użytkowników, zobacz [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Automatyzowanie aprowzowania użytkowników w aplikacjach SaaS za pomocą usługi Azure AD).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Nowe aplikacje federowane dostępne w galerii aplikacja usługi Azure AD — listopad 2019 r.

**Wpisz:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja z produktami innych firm

W listopadzie 2019 r. dodaliśmy do galerii aplikacji te 21 nowych aplikacji z obsługą federacji:

[Airtable,](../saas-apps/airtable-tutorial.md) [Hootsuite,](../saas-apps/hootsuite-tutorial.md) [Blue Access for Members (BAM)](../saas-apps/blue-access-for-members-tutorial.md), [Bitly,](../saas-apps/bitly-tutorial.md) [Riva,](../saas-apps/riva-tutorial.md) [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal Single Sign On (SSO)](../saas-apps/negometrixportal-tutorial.md), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](../saas-apps/motus-tutorial.md), [MyAryaka](../saas-apps/myaryaka-tutorial.md), [BlueMail,](https://loginself1.bluemail.me/) [Beedle,](https://teams-web.beedle.co/#/) [Visma](../saas-apps/visma-tutorial.md), [OneDesk](../saas-apps/onedesk-tutorial.md), [Foko Retail,](../saas-apps/foko-retail-tutorial.md) [Qmarkets Idea & Innovation Management](../saas-apps/qmarkets-idea-innovation-management-tutorial.md), [Netskope User Authentication,](../saas-apps/netskope-user-authentication-tutorial.md) [uniFLOW Online,](../saas-apps/uniflow-online-tutorial.md) [Claromentis,](../saas-apps/claromentis-tutorial.md) [Jisc Student Voter Registration](../saas-apps/jisc-student-voter-registration-tutorial.md), [e4enable](https://portal.e4enable.com/)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z](../saas-apps/tutorial-list.md)Azure Active Directory . Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy [aplikacji w galerii Azure Active Directory aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Nowa i ulepszona galeria aplikacji usługi Azure AD

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej

Zaktualizowaliśmy galerię aplikacji usługi Azure AD, aby ułatwić znajdowanie wstępnie zintegrowanych aplikacji, które obsługują aprowizowanie, obsługę OpenID Connect i saml w dzierżawie Azure Active Directory dzierżawie.

Aby uzyskać więcej informacji, [zobacz Dodawanie aplikacji do dzierżawy Azure Active Directory dzierżawy.](../manage-apps/add-application-portal.md)

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Zwiększono limit długości definicji roli aplikacji ze 120 do 240 znaków

**Wpisz:** Zmieniono funkcję  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej

Słyszeliśmy od klientów, że limit długości wartości definicji roli aplikacji w niektórych aplikacjach i usługach jest zbyt krótki i wynosi 120 znaków. W odpowiedzi zwiększyliśmy maksymalną długość definicji wartości roli do 240 znaków.

Aby uzyskać więcej informacji na temat używania definicji ról specyficznych dla aplikacji, zobacz Dodawanie ról aplikacji w aplikacji i [odbieranie ich w tokenie](../develop/howto-add-app-roles-in-azure-ad-apps.md).

---

## <a name="october-2019"></a>Październik 2019 r.

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Cofanie wersji interfejsu API identityRiskEvent na Azure AD Identity Protection wykrywania ryzyka

**Wpisz:** Planowanie zmiany Kategoria **usługi:** Możliwość produktu Identity **Protection:** Identity Security & Protection

W odpowiedzi na opinie deweloperów subskrybenci Azure AD — wersja Premium P2 mogą teraz wykonywać złożone zapytania dotyczące danych wykrywania ryzyka usługi Azure AD Identity Protection przy użyciu nowego interfejsu API riskDetection dla Microsoft Graph. Istniejąca wersja beta interfejsu API [identityRiskEvent](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true) przestanie zwracać dane około **10 stycznia 2020 r.** Jeśli Twoja organizacja korzysta z interfejsu API identityRiskEvent, należy przejść do nowego interfejsu API riskDetection.

Aby uzyskać więcej informacji na temat nowego interfejsu API riskDetection, zobacz dokumentację interfejsu API wykrywania [ryzyka](/graph/api/resources/riskdetection).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>serwer proxy aplikacji dla atrybutu SameSite i przeglądarki Chrome 80

**Wpisz:** Planowanie zmiany kategorii **Usługi: Możliwość** produktu serwera proxy **aplikacji:** Access Control

Kilka tygodni przed wydaniem przeglądarki Chrome 80 planujemy zaktualizować sposób, w jaki pliki serwer proxy aplikacji traktują **atrybut SameSite.** Po wydaniu przeglądarki Chrome 80 każdy plik cookie, który nie określa **atrybutu SameSite,** będzie traktowany tak, jakby był ustawiony na `SameSite=Lax` wartość .

Aby uniknąć potencjalnie negatywnego wpływu tej zmiany, aktualizujemy pliki cookie dostępu serwer proxy aplikacji sesji przez:

- Ustawienie wartości domyślnej dla ustawienia **Użyj bezpiecznego pliku cookie** na **Tak.**

- Ustawienie wartości domyślnej atrybutu **SameSite** na **Brak.**

    >[!NOTE]
    > serwer proxy aplikacji plików cookie dostępu zawsze były przesyłane wyłącznie za pośrednictwem bezpiecznych kanałów. Te zmiany dotyczą tylko plików cookie sesji.

Aby uzyskać więcej informacji na temat serwer proxy aplikacji plików cookie, zobacz Ustawienia plików cookie na temat uzyskiwania dostępu do aplikacji lokalnych w [programie Azure Active Directory](../manage-apps/application-proxy-configure-cookie-settings.md).

---

### <a name="app-registrations-legacy-and-app-management-in-the-application-registration-portal-appsdevmicrosoftcom-is-no-longer-available"></a>Rejestracje aplikacji (starsza wersja) i zarządzanie aplikacją w portalu rejestracji aplikacji (apps.dev.microsoft.com) nie są już dostępne

**Wpisz:** Planowanie zmiany kategorii **usługi:** Możliwość produktu nie **obejmuje:** środowisko dewelopera

Użytkownicy z kontami usługi Azure AD nie mogą już rejestrować aplikacji ani zarządzać nimi za pomocą portalu rejestracji aplikacji (apps.dev.microsoft.com) ani rejestrować aplikacji i zarządzać nimi w usłudze Rejestracje aplikacji (starszej wersji) w Azure Portal.

Aby dowiedzieć się więcej na temat nowego Rejestracje aplikacji, zobacz Rejestracje aplikacji [w przewodniku szkoleniowym](../develop/quickstart-register-app.md)Azure Portal usługi .

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Użytkownicy nie muszą już ponownie rejestrować się podczas migracji z uwierzytelniania wieloskładnikowego na użytkownika do usługi MFA opartej na dostępie warunkowym

**Wpisz:** Stała **kategoria usługi: Możliwość** produktu MFA: Identity Security & Protection 

Rozwiązaliśmy znany problem, który dotyczył sytuacji, w której użytkownicy byli zobowiązani do ponownego zarejestrowania się, jeśli zostali wyłączeni dla usługi Multi-Factor Authentication (MFA) dla uwierzytelniania wieloskładnikowego (MFA), a następnie włączyli usługę MFA za pomocą zasad dostępu warunkowego.

Aby wymagać od użytkowników ponownej rejestracji, możesz wybrać opcję Wymagane ponowne zarejestrowanie usługi **MFA** w metodach uwierzytelniania użytkownika w portalu usługi Azure AD. Aby uzyskać więcej informacji na temat migrowania użytkowników z uwierzytelniania wieloskładnikowego na użytkownika do usługi MFA opartej na dostępie warunkowym, zobacz Konwertowanie użytkowników z usługi MFA na użytkownika na usługę MFA opartą na [dostępie warunkowym.](../authentication/howto-mfa-getstarted.md#convert-users-from-per-user-mfa-to-conditional-access-based-mfa)

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nowe możliwości przekształcania i wysyłania oświadczeń w tokenie SAML

**Wpisz:** Nowa kategoria usługi **funkcji: Możliwość** produktu **Enterprise Apps:** logowanie jednokrotne

Dodaliśmy dodatkowe funkcje, które ułatwiają dostosowywanie i wysyłanie oświadczeń w tokenie SAML. Te nowe możliwości obejmują:

- Dodatkowe funkcje przekształcania oświadczeń, które pomagają zmodyfikować wartość wysyłaną w oświadczenie.

- Możliwość zastosowania wielu przekształceń do pojedynczego oświadczenia.

- Możliwość określenia źródła oświadczenia na podstawie typu użytkownika i grupy, do której należy użytkownik.

Aby uzyskać szczegółowe informacje o tych nowych możliwościach, w tym o tym, jak ich używać, zobacz Dostosowywanie oświadczeń wystawionych w [tokenie SAML dla aplikacji dla przedsiębiorstw.](../develop/active-directory-saml-claims-customization.md)

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nowa strona Moje logowania dla użytkowników końcowych w usłudze Azure AD

**Wpisz:** Nowa kategoria **usługi funkcji:** Uwierzytelnianie (identyfikatory logowania) **Możliwość produktu:** Monitorowanie & raportowania

Dodaliśmy nową  stronę Moje logowania ( , aby użytkownicy twojej organizacji przeglądali ostatnio historię logowania w celu sprawdzenia, czy nie ma żadnych https://mysignins.microsoft.com) nietypowych działań. Ta nowa strona umożliwia użytkownikom wyświetlanie:

- Jeśli ktoś próbuje odgadnąć hasło.

- Jeśli osoba atakująca pomyślnie zalogowała się na swoje konto i z jakiej lokalizacji.

- Aplikacje, do których osoba atakująca próbowała uzyskać dostęp.

Aby uzyskać więcej informacji, zobacz [blog Users can now check their sign-in history for unusual activity](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) (Użytkownicy mogą teraz sprawdzać historię logowania w przypadku nietypowych działań).

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migracja Azure AD Domain Services (Azure AD DS) z klasycznych do Azure Resource Manager wirtualnych

**Wpisz:** Nowa kategoria **usługi funkcji:** Azure AD Domain Services **Produkt:** Azure AD Domain Services

Dla naszych klientów, którzy utknęli w klasycznych sieciach wirtualnych — mamy dla Ciebie świetną wiadomość! Teraz można przeprowadzić migrację tylko raz z klasycznej sieci wirtualnej do istniejącej Resource Manager wirtualnej. Po przeniesieniu do Resource Manager wirtualnej będzie można korzystać z dodatkowych i uaktualnionych funkcji, takich jak precyzyjne zasady haseł, powiadomienia e-mail i dzienniki inspekcji.

Aby uzyskać więcej informacji, zobacz Wersja zapoznawcza — [Azure AD Domain Services z klasycznego](../../active-directory-domain-services/migrate-from-classic-vnet.md)modelu sieci wirtualnej do usługi Resource Manager .

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Aktualizacje układu Azure AD B2C strony

**Wpisz:** Nowa kategoria **usługi funkcji:** B2C — funkcja produktu do zarządzania tożsamościami **konsumentów:** B2B/B2C

Wprowadziliśmy kilka nowych zmian w wersji 1.2.0 kontraktu strony dla Azure AD B2C. W tej zaktualizowanej wersji można teraz kontrolować kolejność ładowania elementów, co może również pomóc zatrzymać migawkę, która występuje po załadowaniu arkusza stylów (CSS).

Aby uzyskać pełną listę zmian wprowadzonych w kontrakcie strony, zobacz [dziennik zmian wersji](../../active-directory-b2c/page-layout.md#other-pages-providerselection-claimsconsent-unifiedssd).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Aktualizacja strony Moje aplikacje obszaru roboczego wraz z nowymi obszarami roboczymi (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria **usługi feature:** Moje aplikacje **Możliwości produktu:** Access Control

Teraz możesz dostosować sposób wyświetlania i uzyskiwania dostępu do zupełnie nowego środowiska usługi Moje aplikacje przez użytkowników w organizacji, w tym za pomocą nowej funkcji obszarów roboczych, aby ułatwić im znajdowanie aplikacji. Nowe funkcje obszarów roboczych pełnią rolę filtru dla aplikacji, do których użytkownicy w organizacji mają już dostęp.

Aby uzyskać więcej informacji na temat Moje aplikacje środowiska pracy i tworzenia obszarów roboczych, zobacz Tworzenie obszarów roboczych w [portalu Moje aplikacje (wersja zapoznawcza).](../manage-apps/access-panel-collections.md)

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Obsługa miesięcznego aktywnego modelu rozliczeń opartego na użytkowniku (ogólna dostępność)

**Wpisz:** Nowa kategoria **usługi funkcji:** B2C — możliwość produktu do zarządzania tożsamościami **konsumentów:** B2B/B2C

Azure AD B2C obsługuje teraz comiesięczne rozliczanie aktywnych użytkowników (MAU). Rozliczanie za pomocą usługi MAU jest oparte na liczbie unikatowych użytkowników z aktywnością uwierzytelniania w miesiącu kalendarzowym. Istniejący klienci mogą w dowolnym momencie przełączyć się na tę nową metodę rozliczeń.

Od 1 listopada 2019 r. wszyscy nowi klienci będą automatycznie rozliczani przy użyciu tej metody. Ta metoda rozliczeń przynosi korzyści klientom dzięki korzyściom kosztom i możliwości planowania z wyprzedzeniem.

Aby uzyskać więcej informacji, zobacz [Upgrade to monthly active users billing model (Uaktualnianie do modelu rozliczeń dla aktywnych użytkowników miesięcznie).](../../active-directory-b2c/billing.md#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nowe aplikacje federowane dostępne w galerii aplikacja usługi Azure AD — październik 2019 r.

**Wpisz:** Nowa kategoria **usługi funkcji: Możliwość** produktu Enterprise **Apps:** integracja z firmami 3

W październiku 2019 r. dodaliśmy te 35 nowych aplikacji z obsługą federacji do galerii aplikacji:

[In Case of Crisis – Mobile](../saas-apps/in-case-of-crisis-mobile-tutorial.md), [Juno Journey](../saas-apps/juno-journey-tutorial.md), [ExponentHR,](../saas-apps/exponenthr-tutorial.md) [Tact,](https://www.tact.ai/products/tact-assistant) [OpusCapita Cash Management,](https://appsource.microsoft.com/product/web-apps/opuscapitagroupoy-1036255.opuscapita-cm) [Salestim,](https://www.salestim.com/) [Learnster,](../saas-apps/learnster-tutorial.md) [Dynatrace,](../saas-apps/dynatrace-tutorial.md) [HunchBuzz,](https://login.hunchbuzz.com/integrations/azure/process) [Freshworks,](../saas-apps/freshworks-tutorial.md) [eCorcor ,](../saas-apps/ecornell-tutorial.md) [ShipHazmat,](../saas-apps/shiphazmat-tutorial.md) [Netskope Cloud Security,](../saas-apps/netskope-cloud-security-tutorial.md) [Contentful,](../saas-apps/contentful-tutorial.md) [Bindtuning,](https://bindtuning.com/login) [HireVue Coordinate – Europe,](https://www.hirevue.com/) [HireVue Coordinate - USOnly, HireVue](https://www.hirevue.com/) [Coordinate - US,](https://www.hirevue.com/) [PatchyParrot Knowledge Box,](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup) [Cloudmore](../saas-apps/cloudmore-tutorial.md), [Visit.org](../saas-apps/visitorg-tutorial.md), [Cambium Xirrus EasyPass Portal,](https://login.xirrus.com/azure-signup) [Paylocity,](../saas-apps/paylocity-tutorial.md) [Mail Luck!](../saas-apps/mail-luck-tutorial.md), [Teamie,](https://theteamie.com/) [Velocity for Teams,](https://velocity.peakup.org/teams/login) [SIGNL4,](https://account.signl4.com/manage) [EAB Navigate IMPL,](../saas-apps/eab-navigate-impl-tutorial.md) [ScreenMeet,](https://console.screenmeet.com/) [Point ,](https://pi.ompnt.com/) [Speaking Email for Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [Speaking Email for Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO,](../saas-apps/exactcare-sso-tutorial.md) [iHealthHome Care Navigation System](https://ihealthnav.com/account/signin), [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z](../saas-apps/tutorial-list.md)Azure Active Directory . Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy aplikacji w galerii Azure Active Directory [aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Skonsolidowany element menu Zabezpieczenia w portalu usługi Azure AD

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Identity Protection **Product capability:** Identity Security & Protection

Teraz możesz uzyskać dostęp do wszystkich dostępnych funkcji zabezpieczeń usługi Azure  AD z nowego elementu **menu** Zabezpieczenia oraz z paska wyszukiwania w Azure Portal. Ponadto nowa strona  docelowa Zabezpieczenia o nazwie Zabezpieczenia — wprowadzenie będzie zawierać linki do naszej publicznej dokumentacji, wskazówek dotyczących zabezpieczeń i przewodników wdrażania.

Nowe menu **Zabezpieczenia** zawiera:

- Dostęp warunkowy
- Identity Protection
- Security Center
- Ocena bezpieczeństwa tożsamości
- Metody uwierzytelniania
- Uwierzytelnianie wieloskładnikowe
- Raporty ryzyka — ryzykowni użytkownicy, ryzykowne logowania, wykrycia ryzyka
- I nie tylko...

Aby uzyskać więcej informacji, zobacz [Zabezpieczenia — wprowadzenie.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted)

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Rozszerzono zasady wygasania grup usługi Office 365 przy użyciu funkcji autorenewal

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Możliwości **produktu do zarządzania grupą:** Zarządzanie cyklem życia tożsamości

Zasady wygasania grup usługi Office 365 zostały rozszerzone o automatyczne odnawianie grup, które są aktywnie wykorzystywane przez ich członków. Grupy będą automatycznie dostępne na podstawie aktywności użytkowników we wszystkich aplikacjach usługi Office 365, w tym Outlook, SharePoint i Teams.

To ulepszenie pomaga zmniejszyć liczbę powiadomień o wygaśnięciu grupy i pomaga zapewnić, że aktywne grupy będą nadal dostępne. Jeśli masz już aktywne zasady wygasania dla grup usługi Office 365, nie musisz nic robić, aby włączyć tę nową funkcję.

Aby uzyskać więcej informacji, [zobacz Konfigurowanie zasad wygasania dla grup usługi Office 365.](../enterprise-users/groups-lifecycle.md)

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Zaktualizowane Azure AD Domain Services tworzenia Azure AD DS (Azure AD DS)

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Azure AD Domain Services **Możliwości produktu:** Azure AD Domain Services

Zaktualizowaliśmy środowisko Azure AD Domain Services (Azure AD DS), aby uwzględnić nowe i ulepszone środowisko tworzenia, co ułatwia tworzenie domeny zarządzanej za pomocą zaledwie trzech kliknięć. Ponadto można teraz przekazywać i wdrażać Azure AD DS z szablonu.

Aby uzyskać więcej informacji, [zobacz Samouczek: tworzenie i konfigurowanie Azure Active Directory Domain Services wystąpienia .](../../active-directory-domain-services/tutorial-create-instance.md)

---

## <a name="september-2019"></a>Wrzesień 2019 r.

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Planowanie zmian: cofanie Power BI pakietów zawartości

**Wpisz:** Planowanie zmiany kategorii **usługi:** Możliwości **produktu raportowania:** monitorowanie & raportowania

Od 1 października 2019 r. Power BI przestarzałe wszystkie pakiety zawartości, w tym pakiet zawartości azure AD Power BI zawartości. Alternatywą dla tego pakietu zawartości jest użycie skoroszytów usługi Azure AD w celu uzyskania wglądu w usługi związane z usługą Azure AD. Dostępne są dodatkowe skoroszyty, w tym skoroszyty dotyczące zasad dostępu warunkowego w trybie tylko do raportu, szczegółowe informacje oparte na zgody aplikacji i inne.

Aby uzyskać więcej informacji na temat skoroszytów, zobacz How to use Azure Monitor workbooks for Azure Active Directory reports (Jak używać [skoroszytów Azure Active Directory raportów).](../reports-monitoring/howto-use-azure-monitor-workbooks.md) Aby uzyskać więcej informacji na temat cofania pracy pakietów zawartości, zobacz wpis w blogu [Announcing Power BI template apps general availability](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) (Ogłaszanie ogólnej dostępności aplikacji szablonu).

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Mój profil zmienia nazwy i integruje się ze stroną Microsoft Office konta

**Wpisz:** Planowanie zmiany kategorii **usługi:** Możliwość produktu Mój **profil/konto:** Współpraca

Od października środowisko Mój profil stanie się kontem. W ramach tej zmiany, wszędzie tam, gdzie obecnie jest dostępna, mój **profil** zmieni się na **Moje konto.** Oprócz zmiany nazewnictwa i pewnych ulepszeń projektowych zaktualizowane środowisko oferuje dodatkową integrację ze stroną Microsoft Office konta. W szczególności będzie można uzyskać dostęp do instalacji  i subskrypcji pakietu Office ze strony Konto przeglądowe wraz z preferencjami kontaktu dotyczącymi pakietu Office ze **strony Prywatność.**

Aby uzyskać więcej informacji na temat funkcji Mój profil (wersja zapoznawcza), zobacz Omówienie portalu Mój [profil (wersja zapoznawcza).](../user-help/my-account-portal-overview.md)

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Zbiorcze zarządzanie grupami i członkami przy użyciu plików CSV w portalu usługi Azure AD (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria **usługi funkcji:** Możliwość produktu do zarządzania **grupą:** Współpraca

Z zadowoleniem ogłaszamy publiczną dostępność wersji zapoznawczej funkcji zbiorczego zarządzania grupą w portalu usługi Azure AD. Teraz możesz używać pliku CSV i portalu usługi Azure AD do zarządzania grupami i listami członków, w tym:

- Dodawanie lub usuwanie członków z grupy.

- Pobieranie listy grup z katalogu.

- Pobieranie listy członków grupy dla określonej grupy.

Aby uzyskać więcej informacji, zobacz [Zbiorcze](../enterprise-users/groups-bulk-import-members.md)dodawanie członków, Zbiorcze usuwanie [elementów członkowskich,](../enterprise-users/groups-bulk-remove-members.md)Lista członków pobierania [zbiorczego](../enterprise-users/groups-bulk-download-members.md)i [Lista grup pobierania zbiorczego.](../enterprise-users/groups-bulk-download.md)

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Dynamiczna zgoda jest teraz obsługiwana za pośrednictwem nowego punktu końcowego zgody administratora

**Wpisz:** Nowa kategoria **usługi feature:** Authentications (Logins) **Product capability:** User Authentication (Uwierzytelnianie użytkownika)

Utworzono nowy punkt końcowy zgody administratora do obsługi dynamicznej zgody, który jest przydatny w przypadku aplikacji, które chcą używać dynamicznego modelu wyrażania zgody na platformie Microsoft Identity.

Aby uzyskać więcej informacji na temat korzystania z tego nowego punktu końcowego, zobacz [Używanie punktu końcowego zgody administratora](../develop/v2-admin-consent.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Nowe aplikacje federowane dostępne w galerii aplikacja usługi Azure AD — wrzesień 2019 r.

**Wpisz:** Nowa kategoria **usługi feature: Możliwości** **produktu Aplikacje dla przedsiębiorstw:** Integracja z firmami trzecimi

We wrześniu 2019 r. dodaliśmy te 29 nowych aplikacji z obsługą federacji do galerii aplikacji:

[ScheduleLook,](https://schedulelook.bbsonlineservices.net/) [MS Azure SSO Access for Ethidex Compliance Office &trade; —](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md)logowanie jednokrotne, [iServer Portal,](../saas-apps/iserver-portal-tutorial.md) [SKYSITE,](../saas-apps/skysite-tutorial.md) [Concur Travel and Expense,](../saas-apps/concur-travel-and-expense-tutorial.md) [WorkBoard,](../saas-apps/workboard-tutorial.md) `https://apps.yeeflow.com/` , ARC [Facilities,](../saas-apps/arc-facilities-tutorial.md) [Luware Stratus Team,](https://stratus.emea.luware.cloud/login) [Wide Ideas,](https://wideideas.online/wideideas/) [Prisma Cloud,](../saas-apps/prisma-cloud-tutorial.md) [JDLT Client Hub,](https://clients.jdlt.co.uk/login) [RENRAKU,](../saas-apps/renraku-tutorial.md) [SealPath Secure Browser,](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive) [Prisma Cloud,](../saas-apps/prisma-cloud-tutorial.md) `https://app.penneo.com/` , , `https://app.testhtm.com/settings/email-integration` [Cintoo Cloud,](https://aec.cintoo.com/login) [Whitesource,](../saas-apps/whitesource-tutorial.md) [Hosted Heritage Online SSO,](../saas-apps/hosted-heritage-online-sso-tutorial.md) [IDC,Hr](../saas-apps/idc-tutorial.md), [BIS,](../saas-apps/bis-tutorial.md) [Coo Kai Team Build](https://ms-contacts.coo-kai.jp/), [Sonarqube,](../saas-apps/sonarqube-tutorial.md) [Adobe Identity Management,](../saas-apps/tutorial-list.md)Discovery Benefits [SSO](../saas-apps/discovery-benefits-sso-tutorial.md), [Amelio](https://app.amelio.co/), [](../saas-apps/cakehr-tutorial.md)`https://itask.yipinapp.com/`

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z Azure Active Directory](../saas-apps/tutorial-list.md). Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy aplikacji w galerii Azure Active Directory [aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-azure-ad-global-reader-role"></a>Nowa rola czytelnika globalnego usługi Azure AD

**Wpisz:** Nowa kategoria **usługi funkcji:** Role usługi Azure AD **Możliwość produktu:** Access Control

Od 24 września 2019 r. zaczniemy wprowadzać nową rolę Azure Active Directory (AD) o nazwie Czytelnik globalny. To uruchomienie rozpocznie się od klientów produkcyjnych i globalnych chmur (GCC), a w październiku zakończy się na całym świecie.

Rola Czytelnik globalny jest odpowiednikiem tylko do odczytu dla administratora globalnego. Użytkownicy w tej roli mogą odczytywać ustawienia i informacje administracyjne Microsoft 365 usługach, ale nie mogą podjąć akcji zarządzania. Utworzono rolę Czytelnik globalny, aby zmniejszyć liczbę administratorów globalnych w organizacji. Ponieważ konta administratorów globalnych są zaawansowane i narażone na ataki, zalecamy, aby mieć mniej niż pięciu administratorów globalnych. Zalecamy używanie roli czytelnika globalnego do planowania, inspekcji lub badań. Zalecamy również używanie roli Czytelnik globalny w połączeniu z innymi ograniczonymi rolami administratora, np. administratorem programu Exchange, aby ułatwić pracę bez konieczności korzystania z roli administratora globalnego.

Rola Czytelnik globalny współpracuje z nowym centrum administracyjnym usługi Microsoft 365, Centrum administracyjnym programu Exchange, Centrum administracyjnym usługi Teams, Security Center, Centrum zgodności, Centrum administracyjnym usługi Azure AD i centrum Zarządzanie urządzeniami administracyjnego programu .

>[!NOTE]
> Na początku publicznej wersji zapoznawczej rola Czytelnik globalny nie będzie działać z: SharePoint, Privileged Access Management, Skrytka klienta, etykietami czułości, cyklem życia zespołów, analizą połączeń telefonicznych w usłudze Teams Reporting &, usługą Teams IP Phone Zarządzanie urządzeniami i katalogiem aplikacji usługi Teams.

Aby uzyskać więcej informacji, zobacz [Uprawnienia roli administratora w Azure Active Directory](../roles/permissions-reference.md).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Uzyskiwanie dostępu do lokalnego serwera raportów z aplikacji Power BI Mobile przy użyciu Azure Active Directory serwer proxy aplikacji

**Wpisz:** Nowa kategoria **usługi funkcji:** Możliwość produktu serwera proxy **aplikacji:** Access Control

Nowa integracja między aplikacją mobilną Power BI i usługą Azure AD serwer proxy aplikacji umożliwia bezpieczne logowanie się do aplikacji mobilnej Power BI i wyświetlanie dowolnych raportów organizacji hostowanych w środowisku Serwer raportów usługi Power BI.

Aby uzyskać informacje o aplikacji Power BI Mobile, w tym o tym, gdzie pobrać aplikację, zobacz [witrynę Power BI aplikacji.](https://powerbi.microsoft.com/mobile/) Aby uzyskać więcej informacji na temat sposobu skonfigurowania aplikacji mobilnej Power BI za pomocą usługi Azure AD serwer proxy aplikacji, zobacz Włączanie dostępu zdalnego do aplikacji Power BI Mobile za pomocą usługi [Azure AD serwer proxy aplikacji](../manage-apps/application-proxy-integrate-with-power-bi.md).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Dostępna jest nowa wersja modułu AzureADPreview programu PowerShell

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Inne **możliwości produktu:** Katalog

Do modułu AzureADPreview dodano nowe polecenia cmdlet, aby ułatwić definiowanie i przypisywanie ról niestandardowych w usłudze Azure AD, w tym:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nowa wersja Azure AD Connect

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Inne **możliwości produktu:** Katalog

Wydaliśmy zaktualizowaną wersję usługi Azure AD Connect dla klientów z automatycznym uaktualnianiem. Ta nowa wersja zawiera kilka nowych funkcji, ulepszeń i poprawek błędów. Aby uzyskać więcej informacji na temat tej nowej wersji, [zobacz Azure AD Connect: Historia wersji.](../hybrid/reference-connect-version-history.md#14250)

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Serwer usługi Azure Multi-Factor Authentication (MFA) w wersji 8.0.2 jest teraz dostępny

**Wpisz:** Stała **kategoria usługi: Możliwość** produktu MFA: Identity Security & Protection 

Jeśli jesteś istniejącym klientem, który aktywował serwer MFA przed 1 lipca 2019 r., możesz teraz pobrać najnowszą wersję serwera MFA (wersja 8.0.2). W tej nowej wersji:

- Rozwiązano problem, który był taki, że gdy synchronizacja usługi Azure AD zmieniała użytkownika z Wyłączone na Włączone, do użytkownika jest wysyłana wiadomość e-mail.

- Rozwiązano problem, który pozwalał klientom na pomyślne uaktualnienie przy jednoczesnym kontynuowaniu korzystania z funkcji Tagi.

- Dodano kod kraju w poszczególnych krajach (+383).

- Dodano rejestrowanie inspekcji z obejściem raz do pliku MultiFactorAuthSvc.log.

- Zwiększona wydajność zestawu SDK usługi sieci Web.

- Usunięto inne drobne usterki.

Od 1 lipca 2019 r. firma Microsoft przestała oferować serwer MFA dla nowych wdrożeń. Nowi klienci, którzy wymagają uwierzytelniania wieloskładnikowego, powinni używać usługi Azure AD Multi-Factor Authentication opartej na chmurze. Aby uzyskać więcej informacji, zobacz [Planning a cloud-based Azure AD Multi-Factor Authentication deployment](../authentication/howto-mfa-getstarted.md)(Planowanie wdrożenia usługi Azure AD Multi-Factor Authentication w chmurze).

---

## <a name="august-2019"></a>Sierpień 2019 r.

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Rozszerzone wyszukiwanie, filtrowanie i sortowanie dla grup jest dostępne w portalu usługi Azure AD (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria **usługi funkcji:** Możliwość produktu do zarządzania **grupą:** Współpraca

Z zadowoleniem ogłaszamy publiczną dostępność wersji zapoznawczej rozszerzonych funkcji związanych z grupami w portalu usługi Azure AD. Te ulepszenia ułatwiają lepsze zarządzanie grupami i listami członków, zapewniając:

- Zaawansowane możliwości wyszukiwania, takie jak wyszukiwanie podciągów na listach grup.
- Zaawansowane opcje filtrowania i sortowania na listach członków i właścicieli.
- Nowe możliwości wyszukiwania list członków i właścicieli.
- Dokładniejsze liczby grup dla dużych grup.

Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami w Azure Portal](./active-directory-groups-members-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Nowe role niestandardowe są dostępne do zarządzania rejestracją aplikacji (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria **usługi feature:** Role usługi Azure AD **Możliwość produktu:** Access Control

Role niestandardowe (dostępne w ramach subskrypcji P1 lub P2 usługi Azure AD) mogą teraz pomóc w zapewnianiu szczegółowego dostępu, umożliwiając tworzenie definicji ról z określonymi uprawnieniami, a następnie przypisywanie tych ról do określonych zasobów. Obecnie role niestandardowe tworzy się przy użyciu uprawnień do zarządzania rejestracjami aplikacji, a następnie przypisywania roli do określonej aplikacji. Aby uzyskać więcej informacji na temat ról niestandardowych, zobacz Role administratora niestandardowego [w Azure Active Directory (wersja zapoznawcza).](../roles/custom-overview.md)

Jeśli potrzebujesz dodatkowych obsługiwanych uprawnień lub zasobów, których obecnie nie widzisz, możesz wysłać opinię do naszej witryny opinii o platformie [Azure,](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) a my dodamy Twoje żądanie do naszego aktualizowanego mapy.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Nowe dzienniki aprowizowania mogą pomóc w monitorowaniu i rozwiązywaniu problemów z wdrożeniem aprowowania aplikacji (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria usługi **feature:** App Provisioning Product capability: Identity Lifecycle Management (Nowa kategoria usługi feature: App Provisioning **Product capability:** Identity Lifecycle Management)

Dostępne są nowe dzienniki inicjowania obsługi administracyjnej, które ułatwiają monitorowanie i rozwiązywanie problemów z wdrożeniem aprowowania użytkowników i grup. Te nowe pliki dziennika zawierają następujące informacje:

- Jakie grupy zostały pomyślnie utworzone w [usłudze ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)
- Jakie role zostały zaimportowane z programu [AWS Single-Account Access](../saas-apps/amazon-web-service-tutorial.md#configure-and-test-azure-ad-sso-for-aws-single-account-access)
- Którzy pracownicy nie zostali zaimportowani z produktów [Workday](../saas-apps/workday-inbound-tutorial.md)

Aby uzyskać więcej informacji, zobacz [Provisioning reports in the Azure Active Directory portal (preview)](../reports-monitoring/concept-provisioning-logs.md)(Aprowizowanie raportów w portalu Azure Active Directory w wersji zapoznawczej).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nowe raporty zabezpieczeń dla wszystkich administratorów usługi Azure AD (ogólna dostępność)

**Wpisz:** Nowa kategoria **usługi feature:** Identity Protection **Product capability:** Identity Security & Protection

Domyślnie wszyscy administratorzy usługi Azure AD wkrótce będą mogli uzyskać dostęp do nowoczesnych raportów zabezpieczeń w usłudze Azure AD. Do końca września będzie można wrócić do starych raportów za pomocą baneru w górnej części nowoczesnych raportów dotyczących zabezpieczeń.

Nowoczesne raporty o zabezpieczeniach zapewniają dodatkowe możliwości ze starszych wersji, w tym:

- Zaawansowane filtrowanie i sortowanie
- Akcje zbiorcze, takie jak odrzucanie ryzyka związanego z użytkownikiem
- Potwierdzenie naruszonych lub bezpiecznych jednostek
- Stan ryzyka obejmujący: ryzyko, odrzucenie, skorygowanie i potwierdzenie naruszenia
- Nowe wykrycia związane z ryzykiem (dostępne dla Azure AD — wersja Premium subskrybentów)

Aby uzyskać więcej informacji, zobacz [Ryzykownych użytkowników,](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users) [Ryzykowne logowania](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins)i [Wykrycia ryzyka.](../identity-protection/howto-identity-protection-investigate-risk.md#risk-detections)

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Tożsamość zarządzana przypisana przez użytkownika jest dostępna dla Virtual Machines i Virtual Machine Scale Sets (ogólna dostępność)

**Wpisz:** Nowa kategoria usługi **funkcji: Tożsamości** zarządzane dla zasobów platformy Azure **Możliwość produktu:** Środowisko dewelopera

Tożsamości zarządzane przypisane przez użytkownika są teraz ogólnie dostępne dla Virtual Machines i Virtual Machine Scale Sets. W ramach tego zadania platforma Azure może utworzyć tożsamość w dzierżawie usługi Azure AD, która jest zaufana przez używaną subskrypcję i może zostać przypisana do co najmniej jednego wystąpienia usługi platformy Azure. Aby uzyskać więcej informacji na temat tożsamości zarządzanych przypisanych przez użytkownika, zobacz Co to są [tożsamości zarządzane dla zasobów platformy Azure?](../managed-identities-azure-resources/overview.md).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Użytkownicy mogą resetować swoje hasła przy użyciu aplikacji mobilnej lub tokenu sprzętowego (ogólna dostępność)

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Funkcja samoobsługowego resetowania **hasła Produkt:** Uwierzytelnianie użytkowników

Użytkownicy, którzy zarejestrują aplikację mobilną w organizacji, mogą teraz zresetować własne hasło, zatwierdzając powiadomienie z aplikacji Microsoft Authenticator lub wprowadzając kod z aplikacji mobilnej lub tokenu sprzętowego.

Aby uzyskać więcej informacji, zobacz [Jak to działa: samoobsługowe resetowanie haseł w usłudze Azure AD.](../authentication/concept-sspr-howitworks.md) Aby uzyskać więcej informacji na temat obsługi użytkownika, zobacz [Resetowanie własnego hasła służbowego — omówienie](../user-help/active-directory-passwords-reset-register.md).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignoruje MSAL.NET pamięci podręcznej dla scenariuszy "w imieniu"

**Wpisz:** Naprawiono **kategorię usługi:** Możliwość produktu Authentications (Logins):  User Authentication (Uwierzytelnianie użytkownika)

Począwszy od biblioteki uwierzytelniania usługi Azure AD (ADAL.NET) w wersji 5.0.0-preview, deweloperzy aplikacji muszą [serializować](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api)jedną pamięć podręczną na konto dla aplikacji internetowych i internetowych interfejsów API. W przeciwnym razie niektóre scenariusze korzystające z przepływu "w [imieniu"](../develop/scenario-web-api-call-api-app-configuration.md?tabs=java) dla języka Java wraz z pewnymi konkretnymi przypadkami użycia programu mogą spowodować `UserAssertion` podniesienie uprawnień. Aby uniknąć tej luki w zabezpieczeniach, ADAL.NET teraz ignoruje bibliotekę uwierzytelniania firmy Microsoft dla udostępnionej pamięci podręcznej dotnet (MSAL.NET) dla scenariuszy w imieniu użytkownika.

Aby uzyskać więcej informacji na temat tego problemu, zobacz Azure Active Directory luka w zabezpieczeniach podniesienia uprawnień [w bibliotece uwierzytelniania](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nowe aplikacje federowane dostępne w galerii aplikacja usługi Azure AD — sierpień 2019 r.

**Wpisz:** Nowa kategoria **usługi feature: Możliwości** **produktu Aplikacje dla przedsiębiorstw:** Integracja z firmami trzecimi

W sierpniu 2019 r. dodaliśmy te 26 nowych aplikacji z obsługą federacji do galerii aplikacji:

[Platform](../saas-apps/civic-platform-tutorial.md), [Amazon Business,](../saas-apps/amazon-business-tutorial.md) [ProNovos Ops Manager](../saas-apps/pronovos-ops-manager-tutorial.md), [Cognidox,](../saas-apps/cognidox-tutorial.md) [Viareport's Inativ Portal (Europe)](../saas-apps/viareports-inativ-portal-europe-tutorial.md), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin, Academy](../saas-apps/robin-tutorial.md) [Wadze,](../saas-apps/academy-attendance-tutorial.md) [Priority Matrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace,](https://cousto.platformers.be/account/login) [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint Backup](../saas-apps/carbonite-endpoint-backup-tutorial.md), [CPQSync firmy Cincom,](../saas-apps/cpqsync-by-cincom-tutorial.md) [Chargebee,](../saas-apps/chargebee-tutorial.md) [deliver.media &trade; Portal,](https://portal.deliver.media) [Frontline Education,](../saas-apps/frontline-education-tutorial.md) [F5,](https://www.f5.com/products/security/access-policy-manager) [stashcat AD Connect,](https://www.stashcat.com) [Blink,](../saas-apps/blink-tutorial.md) [Vocoli,](../saas-apps/vocoli-tutorial.md) [ProNovos Analytics,](../saas-apps/pronovos-analytics-tutorial.md) [Sigstr,](../saas-apps/sigstr-tutorial.md) [Darwinbox,](../saas-apps/darwinbox-tutorial.md) [Watch by Colors,](../saas-apps/watch-by-colors-tutorial.md) [Harness,](../saas-apps/harness-tutorial.md) [EAB Navigate Strategic Care](../saas-apps/eab-navigate-strategic-care-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z](../saas-apps/tutorial-list.md)Azure Active Directory . Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy [aplikacji w galerii Azure Active Directory aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Dostępne są nowe wersje modułów AzureAD PowerShell i AzureADPreview PowerShell

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Inne **możliwości produktu:** Katalog

Dostępne są nowe aktualizacje modułów azureAD i AzureAD (wersja zapoznawcza programu PowerShell):

- Dodano `-Filter` nowy parametr do `Get-AzureADDirectoryRole` parametru w module AzureAD. Ten parametr ułatwia filtrowanie ról katalogu zwracanych przez polecenie cmdlet .
- Do modułu AzureADPreview dodano nowe polecenia cmdlet, aby ułatwić definiowanie i przypisywanie ról niestandardowych w usłudze Azure AD, w tym:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Ulepszenia interfejsu użytkownika konstruktora reguł grupy dynamicznej w Azure Portal

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Możliwość **produktu do zarządzania grupą:** Współpraca

Wprowadzono pewne ulepszenia interfejsu użytkownika w konstruktorze reguł grupy dynamicznej, które są dostępne w Azure Portal, aby ułatwić konfigurowanie nowej reguły lub zmianę istniejących reguł. To ulepszenie projektu umożliwia tworzenie reguł z maksymalnie pięcioma wyrażeniami, a nie tylko jednym. Zaktualizowaliśmy również listę właściwości urządzenia, aby usunąć przestarzałe właściwości urządzenia.

Aby uzyskać więcej informacji, zobacz [Zarządzanie regułami członkostwa dynamicznego.](../enterprise-users/groups-dynamic-membership.md)

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nowe Microsoft Graph aplikacji dostępne do użycia z przeglądami dostępu

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Przeglądy dostępu **Możliwość produktu:** Nadzór nad tożsamościami

Wprowadziliśmy nowe uprawnienie aplikacji Microsoft Graph, które umożliwia aplikacjom automatyczne tworzenie i pobieranie przeglądów dostępu dla członkostwa w grupach i `AccessReview.ReadWrite.Membership` przypisań aplikacji. To uprawnienie może być używane przez zaplanowane zadania lub w ramach automatyzacji, bez konieczności zalogowania się w kontekście użytkownika.

Aby uzyskać więcej informacji, zobacz przykładowy sposób tworzenia przeglądów dostępu usługi [Azure AD przy użyciu Microsoft Graph aplikacji za pomocą bloga programu PowerShell.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241)

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Dzienniki aktywności usługi Azure AD są teraz dostępne dla wystąpień chmury dla instytucji rządowych w Azure Monitor

**Wpisz:** Zmieniono **kategorię usługi funkcji:** **Raportowanie możliwości produktu:** Monitorowanie & raportowania

Z przyjemnością ogłaszamy, że dzienniki aktywności usługi Azure AD są teraz dostępne dla wystąpień chmury dla instytucji rządowych w Azure Monitor. Teraz możesz wysyłać dzienniki usługi Azure AD do konta magazynu lub do centrum zdarzeń w celu integracji z narzędziami SIEM, np. [Sumologic,](../reports-monitoring/howto-integrate-activity-logs-with-sumologic.md) [Splunk](../reports-monitoring/howto-integrate-activity-logs-with-splunk.md)i [ArcSight.](../reports-monitoring/howto-integrate-activity-logs-with-arcsight.md)

Aby uzyskać więcej informacji na temat konfigurowania Azure Monitor, zobacz [Dzienniki aktywności usługi Azure AD w usłudze Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Aktualizowanie użytkowników do nowego, ulepszonego systemu informacji zabezpieczających

**Wpisz:** Zmieniono **kategorię usługi funkcji:**  Uwierzytelnianie (identyfikatory logowania) **Możliwość produktu:** Uwierzytelnianie użytkownika

25 września 2019 r. wyłączymy stare środowisko informacji zabezpieczających bez rozszerzonych informacji zabezpieczających w celu rejestrowania informacji zabezpieczających użytkowników i zarządzania nimi oraz włączania tylko nowej, rozszerzonej [wersji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). Oznacza to, że użytkownicy nie będą już mogli korzystać ze starego środowisko.

Aby uzyskać więcej informacji na temat ulepszonego interfejsu informacji zabezpieczających, zobacz [dokumentację dla](../authentication/concept-registration-mfa-sspr-combined.md) administratorów i [dokumentację użytkownika](../user-help/security-info-setup-signin.md).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Aby włączyć to nowe środowisko, musisz:

1. Zaloguj się do Azure Portal jako administrator globalny lub administrator użytkowników.

2. Przejdź do **Azure Active Directory > Ustawienia użytkownika i > Zarządzaj ustawieniami funkcji panelu dostępu w wersji zapoznawczej.**

3. W  obszarze Użytkownicy mogą używać funkcji w wersji zapoznawczej do rejestrowania informacji zabezpieczających i  zarządzania nimi — rozszerzony obszar wybierz pozycję **Wybrane,** a następnie wybierz grupę użytkowników lub wybierz pozycję Wszyscy, aby włączyć tę funkcję dla wszystkich użytkowników w dzierżawie.

4. W obszarze **Użytkownicy mogą używać funkcji w wersji zapoznawczej do rejestrowania **informacji* zabezpieczeń i zarządzania nimi wybierz pozycję **Brak.**

5. Zapisz ustawienia.

    Po zapisaniu ustawień nie będziesz już mieć dostępu do starego środowisko informacji zabezpieczających.

>[!Important]
>Jeśli te kroki nie zostaną ukończone przed 25 września 2019 r., dzierżawa usługi Azure Active Directory zostanie automatycznie włączona w rozszerzonym trybie. Jeśli masz pytania, skontaktuj się z nami pod adres registrationpreview@microsoft.com .

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Żądania uwierzytelniania przy użyciu identyfikatorów logowania POST będą dokładniej weryfikowane

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Możliwości produktu Authentications (Logins):  Standards

Od 2 września 2019 r. żądania uwierzytelniania przy użyciu metody POST będą bardziej ściśle weryfikowane pod względem standardów HTTP. W szczególności spacje i cudzysłowy (") nie będą już usuwane z wartości formularza żądania. Te zmiany nie powinny przerwać działania istniejących klientów i pomagają zapewnić niezawodną obsługę żądań wysyłanych do usługi Azure AD za każdym razem.

Aby uzyskać więcej informacji, zobacz uwagi dotyczące zmian [przerywania usługi Azure AD.](../develop/reference-breaking-changes.md#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored)

---

## <a name="july-2019"></a>Lipiec 2019 r.

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Planowanie zmian: serwer proxy aplikacji aktualizacji usługi do obsługi tylko TLS 1.2

**Wpisz:** Planowanie zmiany kategorii **Usługi: Możliwość** produktu serwera proxy **aplikacji:** Access Control

Aby zapewnić najwyższe szyfrowanie, zaczniemy ograniczać dostęp serwer proxy aplikacji tylko do protokołów TLS 1.2. To ograniczenie będzie początkowo dostępne dla klientów, którzy już korzystali z protokołów TLS 1.2, więc nie będzie to miało wpływu. Zakończenie pracy protokołów TLS 1.0 i TLS 1.1 zakończy się 31 sierpnia 2019 r. Klienci nadal korzystający z rozwiązań TLS 1.0 i TLS 1.1 otrzymają powiadomienie zaawansowane, aby przygotować się do tej zmiany.

Aby zachować połączenie z usługą serwer proxy aplikacji w ramach tej zmiany, zalecamy zaktualizowanie kombinacji klient-serwer i serwer przeglądarki w celu korzystania z wersji TLS 1.2. Zalecamy również, aby uwzględnić wszystkie systemy klienckie używane przez pracowników do uzyskiwania dostępu do aplikacji opublikowanych za pośrednictwem serwer proxy aplikacji usługi.

Aby uzyskać więcej informacji, zobacz [Add an on-premises application for remote access through serwer proxy aplikacji in Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Planowanie zmian: w galerii aplikacji będą dostępne aktualizacje projektu

**Wpisz:** Planowanie zmiany Kategoria **usługi: Możliwość** produktu Enterprise **Apps:** Logowanie jednokrotne

W projekcie dodaj z obszaru  galerii bloku Dodawanie aplikacji zostaną wprowadzone **nowe zmiany interfejsu** użytkownika. Te zmiany ułatwiają znajdowanie aplikacji, które obsługują automatyczną aprowizowanie, OpenID Connect, SAML (SAML) i logowanie jednokrotne (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Planowanie zmian: usunięcie adresu IP serwera MFA z adresu IP usługi Office 365

**Wpisz:** Planowanie zmiany kategorii **usługi:** Możliwość produktu **MFA:** Identity Security & Protection

Usuwamy adres IP serwera MFA z adresu IP i adresu URL usługi sieci Web usługi [Office 365.](/office365/enterprise/office-365-ip-web-service) Jeśli obecnie używasz tych stron do aktualizowania ustawień zapory, upewnij się, że uwzględniasz również listę adresów IP udokumentowaną w sekcji Wymagania dotyczące zapory usługi **Azure Serwer Multi-Factor Authentication** artykułu Wprowadzenie do usługi [Azure Serwer Multi-Factor Authentication.](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements)

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Tokeny tylko dla aplikacji wymagają teraz istnienia aplikacji klienckiej w dzierżawie zasobów

**Wpisz:** Naprawiono **kategorię usługi:** Możliwość produktu Authentications (Logins):  User Authentication (Uwierzytelnianie użytkownika)

26 lipca 2019 r. zmieniliśmy sposób, w jaki zapewniamy tokeny tylko dla aplikacji za pośrednictwem udzielania [poświadczeń klienta](../azuread-dev/v1-oauth2-client-creds-grant-flow.md). Wcześniej aplikacje mogły uzyskać tokeny do wywołania innych aplikacji, niezależnie od tego, czy aplikacja kliency była w dzierżawie. Zaktualizowaliśmy to zachowanie, dzięki czemu zasoby pojedynczej dzierżawy, czasami nazywane internetowymi interfejsami API, mogą być wywoływane tylko przez aplikacje klienckie istniejące w dzierżawie zasobów.

Jeśli Twoja aplikacja nie znajduje się w dzierżawie zasobów, zostanie wyświetlony komunikat o błędzie: Aby rozwiązać ten problem, musisz utworzyć jednostkę usługi aplikacji klienta w dzierżawie przy użyciu punktu końcowego zgody administratora lub programu PowerShell, co gwarantuje, że dzierżawa nada aplikacji uprawnienia do działania w `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` dzierżawie. [](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint) [](../develop/howto-authenticate-service-principal-powershell.md)

Aby uzyskać więcej informacji, zobacz [Co nowego w uwierzytelnianiu?](../develop/reference-breaking-changes.md#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Istniejąca zgoda między klientem a interfejsem API nadal nie jest wymagana. Aplikacje powinny nadal przeprowadzać własne kontrole autoryzacji.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nowe logowanie bez hasła do usługi Azure AD przy użyciu kluczy zabezpieczeń FIDO2

**Wpisz:** Nowa kategoria **usługi feature:** Authentications (Logins) **Product capability:** User Authentication (Uwierzytelnianie użytkownika)

Klienci usługi Azure AD mogą teraz ustawiać zasady zarządzania kluczami zabezpieczeń FIDO2 dla użytkowników i grup w organizacji. Użytkownicy końcowi mogą również samodzielnie rejestrować swoje klucze zabezpieczeń, używać kluczy do logowania się do kont Microsoft w witrynach internetowych na urządzeniach z możliwością fido, a także logować się do urządzeń przyłączonych do usługi Azure AD Windows 10 internetowych.

Aby uzyskać więcej informacji, zobacz Włączanie logowania bez hasła w usłudze [Azure AD (wersja zapoznawcza),](../authentication/concept-authentication-passwordless.md) aby uzyskać informacje związane z administratorem, i Konfigurowanie informacji zabezpieczających w celu używania klucza zabezpieczeń [(wersja zapoznawcza)](../user-help/security-info-setup-security-key.md) w celu wyświetlania informacji dotyczących użytkowników końcowych.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nowe aplikacje federowane dostępne w galerii aplikacja usługi Azure AD — lipiec 2019 r.

**Wpisz:** Nowa kategoria **usługi funkcji: Możliwość** produktu Enterprise **Apps:** integracja z firmami 3

W lipcu 2019 r. dodaliśmy te 18 nowych aplikacji z obsługą federacji do galerii aplikacji:

[Ungerboeck Software,](../saas-apps/ungerboeck-software-tutorial.md) [Bright Pattern Omnichannel Contact Center,](../saas-apps/bright-pattern-omnichannel-contact-center-tutorial.md) [Clever Cheny,](../saas-apps/clever-nelly-tutorial.md) [AcquireIO,](../saas-apps/acquireio-tutorial.md) [Looop,](https://www.looop.co/schedule-a-demo/) [productboard,](../saas-apps/productboard-tutorial.md) [MS Azure SSO Access for Ethidex Compliance Office &trade; ](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md), [Hype,](../saas-apps/hype-tutorial.md) [Abstract](../saas-apps/abstract-tutorial.md), [Ascentis,](../saas-apps/ascentis-tutorial.md) [Flipsnack,](https://www.flipsnack.com/accounts/sign-in-sso.html) [Wandera,](../saas-apps/wandera-tutorial.md) [TwineSocial,](https://twinesocial.com/) [Kallidus,](../saas-apps/kallidus-tutorial.md) [HyperAnna,](../saas-apps/hyperanna-tutorial.md) [TunID WasteWitness,](https://pharmid.com/) [i2B Connect,](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/) [JFrog Artifactory](../saas-apps/jfrog-artifactory-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz Integracja aplikacji [SaaS z](../saas-apps/tutorial-list.md)Azure Active Directory . Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy aplikacji w galerii Azure Active Directory [aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatyzowanie aprowzowania kont użytkowników dla tych nowo obsługiwanych aplikacji SaaS

**Wpisz:** Nowa kategoria usługi **funkcji: Możliwość** produktu Aplikacje **dla przedsiębiorstw:** Monitorowanie & raportowania

Teraz możesz zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla tych nowo zintegrowanych aplikacji:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federated Directory](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu zautomatyzowanego aprowizowania kont użytkowników, zobacz Automatyzowanie aprowzowania użytkowników w aplikacjach [SaaS](../app-provisioning/user-provisioning.md) za pomocą usługi Azure AD

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nowy Azure AD Domain Services usługi dla sieciowej grupy zabezpieczeń

**Wpisz:** Nowa kategoria **usługi funkcji:** Azure AD Domain Services **product capability:** Azure AD Domain Services

Jeśli nie musisz zarządzać długimi listami adresów IP i zakresów, możesz użyć nowego tagu usługi **sieciowej AzureActiveDirectoryDomainServices** w sieciowej grupie zabezpieczeń platformy Azure, aby zabezpieczyć ruch przychodzący do podsieci sieci wirtualnej Azure AD Domain Services.

Aby uzyskać więcej informacji na temat tego nowego tagu usługi, zobacz [Sieciowe grupy zabezpieczeń dla Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nowe inspekcje zabezpieczeń dla Azure AD Domain Services (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria **usługi funkcji:** Azure AD Domain Services **Możliwości produktu:** Azure AD Domain Services

Z zadowoleniem ogłaszamy wydanie inspekcji zabezpieczeń usługi Azure AD Domain Service w publicznej wersji zapoznawczej. Inspekcja zabezpieczeń pomaga zapewnić krytyczny wgląd w usługi uwierzytelniania dzięki przesyłaniu strumieniowemu zdarzeń inspekcji zabezpieczeń do zasobów docelowych, w tym usługi Azure Storage, obszarów roboczych usługi Azure Log Analytics i usługi Azure Event Hub, przy użyciu portalu usługi Azure AD Domain Service.

Aby uzyskać więcej informacji, zobacz [Włączanie inspekcji zabezpieczeń dla Azure AD Domain Services (wersja zapoznawcza).](../../active-directory-domain-services/security-audit-events.md)

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nowe metody uwierzytelniania i & szczegółowych informacji (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria usługi **funkcji: Funkcja** samoobsługowego resetowania hasła **Produkt:** Monitorowanie & raportowania

Nowe raporty analizy użycia metod uwierzytelniania & pomagają zrozumieć, jak funkcje, takie jak usługa Azure AD Multi-Factor Authentication i samoobsługowe resetowanie hasła, są rejestrowane i używane w organizacji, w tym liczbę zarejestrowanych użytkowników dla każdej funkcji, jak często samoobsługowe resetowanie haseł jest używane do resetowania haseł oraz jaka jest metoda resetowania.

Aby uzyskać więcej informacji, zobacz [Authentication methods usage & insights (preview) (Szczegółowe informacje o metodach uwierzytelniania w wersji zapoznawczej).](../authentication/howto-authentication-methods-activity.md)

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Nowe raporty zabezpieczeń są dostępne dla wszystkich administratorów usługi Azure AD (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria **usługi feature:** Identity Protection **Product capability:** Identity Security & Protection

Wszyscy administratorzy usługi Azure AD mogą teraz wybrać transparent w  górnej części istniejących raportów zabezpieczeń, takich jak raport  Użytkownicy oflagowany w związku z ryzykiem, aby rozpocząć korzystanie z nowego rozwiązania zabezpieczeń, jak pokazano w raportach ryzykownych użytkowników i ryzykownych **logowaniach.** Wraz z czasem wszystkie raporty o zabezpieczeniach będą przechodzić ze starszych wersji do nowych, a nowe raporty zapewniają następujące dodatkowe możliwości:

- Zaawansowane filtrowanie i sortowanie

- Akcje zbiorcze, takie jak odrzucanie ryzyka związanego z użytkownikiem

- Potwierdzenie naruszonych lub bezpiecznych jednostek

- Stan ryzyka obejmujący: Zagrożone, Odrzucone, Skorygowane i Potwierdzone naruszone bezpieczeństwo

Aby uzyskać więcej informacji, zobacz [Raport ryzykownych użytkowników](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users) i [Raport ryzykownych logów.](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins)

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nowe inspekcje zabezpieczeń dla Azure AD Domain Services (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria **usługi funkcji:** Azure AD Domain Services **Produkt:** Azure AD Domain Services

Z zadowoleniem ogłaszamy wydanie inspekcji zabezpieczeń usługi Azure AD Domain Service w publicznej wersji zapoznawczej. Inspekcja zabezpieczeń pomaga zapewnić krytyczny wgląd w usługi uwierzytelniania przez przesyłanie strumieniowe zdarzeń inspekcji zabezpieczeń do zasobów docelowych, w tym do usługi Azure Storage, obszarów roboczych usługi Azure Log Analytics i usługi Azure Event Hub, przy użyciu portalu usługi Azure AD Domain Service.

Aby uzyskać więcej informacji, zobacz Włączanie inspekcji zabezpieczeń dla [Azure AD Domain Services (wersja zapoznawcza).](../../active-directory-domain-services/security-audit-events.md)

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nowa federacja bezpośrednia B2B przy użyciu technologii SAML/WS-Fed (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria **usługi funkcji:** Możliwość produktu B2B: B2B/B2C 

Federacja bezpośrednia ułatwia współpracę z partnerami, których rozwiązaniem do zarządzania tożsamościami IT nie jest usługa Azure AD, dzięki pracy z systemami tożsamości, które obsługują standardy SAML lub WS-Fed. Po skonfigurowaniu bezpośredniej relacji federacji z partnerem każdy nowy użytkownik-gość zapraszany z tej domeny może współpracować z Toem przy użyciu istniejącego konta organizacyjnego, dzięki czemu środowisko użytkownika dla gości będzie bardziej bezproblemowe.

Aby uzyskać więcej informacji, zobacz [Federacja bezpośrednia z AD FS i dostawcami innych firm dla użytkowników-gości (wersja zapoznawcza).](../external-identities/direct-federation.md)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Zautomatyzuj aprowizowanie kont użytkowników dla tych nowo obsługiwanych aplikacji SaaS

**Wpisz:** Nowa kategoria usługi **feature: Możliwości** **produktu Aplikacje dla przedsiębiorstw:** Monitorowanie & raportowania

Teraz możesz zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla tych nowo zintegrowanych aplikacji:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federated Directory](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu zautomatyzowanego aprowizowania kont użytkowników, zobacz [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Automatyzowanie aprowzowania użytkowników w aplikacjach SaaS za pomocą usługi Azure AD).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nowe sprawdzanie zduplikowanych nazw grup w portalu usługi Azure AD

**Wpisz:** Nowa kategoria usługi **funkcji:** Możliwość produktu **do zarządzania grupą:** Współpraca

Teraz podczas tworzenia lub aktualizowania nazwy grupy w portalu usługi Azure AD wykonamy sprawdzenie, czy duplikowana jest nazwa istniejącej grupy w zasobie. Jeśli ustalimy, że nazwa jest już używana przez inną grupę, zostaniesz poproszony o zmodyfikowanie swojej nazwy.

Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami w portalu usługi Azure AD.](./active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Usługa Azure AD obsługuje teraz statyczne parametry zapytania w interfejsach URI odpowiedzi (przekierowania)

**Wpisz:** Nowa kategoria **usługi feature:** Authentications (Logins) **Product capability:** User Authentication (Uwierzytelnianie użytkownika)

Aplikacje usługi Azure AD mogą teraz rejestrować i używać interfejsów URI odpowiedzi (przekierowania) z parametrami zapytania statycznego (na przykład ) dla żądań `https://contoso.com/oauth2?idp=microsoft` OAuth 2.0. Parametr zapytania statycznego może być zgodny z ciągami dla adresów URI odpowiedzi, podobnie jak każda inna część adresu URI odpowiedzi. Jeśli nie ma zarejestrowanego ciągu, który pasuje do zdekodowania adresu URL redirect-uri, żądanie zostanie odrzucone. Jeśli identyfikator URI odpowiedzi zostanie znaleziony, cały ciąg jest używany do przekierowywania użytkownika, łącznie z parametrem zapytania statycznego.

Dynamiczne adresy URL odpowiedzi są nadal zabronione, ponieważ stanowią zagrożenie bezpieczeństwa i nie mogą służyć do przechowywania informacji o stanie w żądaniu uwierzytelnienia. W tym celu użyj `state` parametru .

Obecnie ekrany rejestracji aplikacji w Azure Portal nadal blokują parametry zapytania. Można jednak ręcznie edytować manifest aplikacji, aby dodać i przetestować parametry zapytania w aplikacji. Aby uzyskać więcej informacji, zobacz [Co nowego w uwierzytelnianiu?](../develop/reference-breaking-changes.md#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Dzienniki aktywności (interfejsy API programu MS Graph) dla usługi Azure AD są teraz dostępne za pośrednictwem poleceń cmdlet programu PowerShell

**Wpisz:** Nowa kategoria **usługi funkcji:** **Raportowanie możliwości produktu:** Monitorowanie & raportowania

Z przyjemnością ogłaszamy, że dzienniki aktywności usługi Azure AD (raporty inspekcji i logowania) są teraz dostępne za pośrednictwem modułu Azure AD PowerShell. Wcześniej można było tworzyć własne skrypty przy użyciu interfejs Graph API MS, a teraz rozszerzyliśmy tę możliwość o polecenia cmdlet programu PowerShell.

Aby uzyskać więcej informacji na temat używania tych poleceń cmdlet, zobacz [Polecenia cmdlet](../reports-monitoring/reference-powershell-reporting.md)programu PowerShell usługi Azure AD do raportowania .

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Zaktualizowano kontrolki filtru dla dzienników inspekcji i logowania w usłudze Azure AD

**Wpisz:** Zmieniono **kategorię usługi funkcji:** **Raportowanie możliwości produktu:** Monitorowanie & raportowania

Zaktualizowaliśmy raporty dzienników inspekcji i logowania, dzięki czemu można teraz stosować różne filtry bez konieczności dodawania ich jako kolumn na ekranach raportu. Ponadto możesz teraz zdecydować, ile filtrów chcesz wyświetlić na ekranie. Wszystkie te aktualizacje współpracują ze sobą, aby raporty były łatwiejsze do odczytania i bardziej w zakresie twoich potrzeb.

Aby uzyskać więcej informacji na temat tych aktualizacji, zobacz [Filtrowanie dzienników inspekcji](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) i [Filtrowanie działań logowania.](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities)

---

## <a name="june-2019"></a>Czerwiec 2019 r.

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nowy interfejs API riskDetections dla Microsoft Graph (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria **usługi funkcji:** Identity Protection **Product capability:** Identity Security & Protection

Z zadowoleniem ogłaszamy nowy interfejs API riskDetections dla Microsoft Graph jest teraz w publicznej wersji zapoznawczej. Za pomocą tego nowego interfejsu API można wyświetlić listę wykrywania ryzyka związanego z logowaniem i użytkownikiem usługi Identity Protection w organizacji. Ten interfejs API umożliwia również wydajniejsze wykonywanie zapytań dotyczących wykrywania ryzyka, w tym szczegółowe informacje o typie wykrywania, stanie, poziomie i innych.

Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API wykrywania ryzyka](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — czerwiec 2019 r.

**Wpisz:** Nowa kategoria **usługi feature: Możliwości** **produktu Aplikacje dla przedsiębiorstw:** Integracja z firmami trzecimi

W czerwcu 2019 r. dodaliśmy te 22 nowe aplikacje z obsługą federacji do galerii aplikacji:

[Azure AD SAML Toolkit,](../saas-apps/saml-toolkit-tutorial.md) [Otsuka Shokai (塚商会 <2> <1>)](../saas-apps/otsuka-shokai-tutorial.md), [ANAQUA,](../saas-apps/anaqua-tutorial.md) [Azure VPN Client](https://portal.azure.com/), [ExpenseIn,](../saas-apps/expensein-tutorial.md) [Helper Helper](../saas-apps/helper-helper-tutorial.md), [Costpoint,](../saas-apps/costpoint-tutorial.md) [GlobalOne,](../saas-apps/globalone-tutorial.md) [Doce-System In-Car Office,](https://me.secure.mercedes-benz.com/) [Skore,](https://app.justskore.it/) [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-tutorial.md), [CyberArk SAML Authentication,](../saas-apps/cyberark-saml-authentication-tutorial.md) [Scrible Edu,](https://www.scrible.com/sign-in/#/create-account) [PandaDoc,](../saas-apps/pandadoc-tutorial.md) [Perceptyx,](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial) [Proptimise OS,](https://proptimise.co.uk/) [Vtiger CRM (SAML),](../saas-apps/vtiger-crm-saml-tutorial.md)Oracle Access Manager for Oracle Retail Merchandising, Oracle Access Manager for Oracle E-Business Suite, Oracle IDCS for E-Business Suite, Oracle IDCS for PeopleSoft, Oracle IDCS for JD Edwards

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z](../saas-apps/tutorial-list.md)Azure Active Directory . Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy [aplikacji w galerii Azure Active Directory aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatyzowanie aprowzowania kont użytkowników dla tych nowo obsługiwanych aplikacji SaaS

**Wpisz:** Nowa kategoria usługi **feature: Możliwości** **produktu Aplikacje dla przedsiębiorstw:** Monitorowanie & raportowania

Teraz możesz zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla tych nowo zintegrowanych aplikacji:

- [Powiększenie](../saas-apps/zoom-provisioning-tutorial.md)

- [Envoy](../saas-apps/envoy-provisioning-tutorial.md)

- [Proxyclick](../saas-apps/proxyclick-provisioning-tutorial.md)

- [4me](../saas-apps/4me-provisioning-tutorial.md)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu zautomatyzowanej aprowzowania kont użytkowników, zobacz [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md) (Automatyzowanie aprowizowania użytkowników w aplikacjach SaaS za pomocą usługi Azure AD)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Wyświetlanie postępu usługi aprowizowania usługi Azure AD w czasie rzeczywistym

**Wpisz:** Zmieniono **kategorię usługi feature:** App Provisioning **Product capability:** Identity Lifecycle Management

Zaktualizowaliśmy środowisko aprowizowania usługi Azure AD, aby uwzględnić nowy pasek postępu, który pokazuje, jak daleko znajdujesz się w procesie aprowacji użytkowników. To zaktualizowane środowisko zawiera również informacje o liczbie użytkowników aprowowanych w bieżącym cyklu, a także liczbie użytkowników aprowowanych do tej pory.

Aby uzyskać więcej informacji, [zobacz Sprawdzanie stanu aprowizowania użytkowników.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Znakowanie firmowe jest teraz wyświetlane na ekranach wylogowania i błędów

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Uwierzytelnianie (identyfikatory logowania) **Możliwość produktu:** Uwierzytelnianie użytkownika

Zaktualizowaliśmy usługę Azure AD, aby znakowanie firmowe było teraz wyświetlane na ekranach wylogowania i błędu, a także na stronie logowania. Nie musisz nic robić, aby włączyć tę funkcję. Usługa Azure AD po prostu  używa zasobów, które zostały już ustawione w obszarze brandingu Azure Portal.

Aby uzyskać więcej informacji na temat konfigurowania brandingu firmowego, zobacz Dodawanie brandowania do stron Azure Active Directory [organizacji.](./customize-branding.md)

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Serwer usługi Azure Multi-Factor Authentication (MFA) nie jest już dostępny dla nowych wdrożeń

**Wpisz:** Przestarzała **kategoria usługi:** Możliwość produktu MFA: Identity Security & Protection 

Od 1 lipca 2019 r. firma Microsoft nie będzie już oferować serwera MFA dla nowych wdrożeń. Nowi klienci, którzy chcą wymagać uwierzytelniania wieloskładnikowego w swojej organizacji, muszą teraz używać usługi Azure AD Multi-Factor Authentication opartej na chmurze. Klienci, którzy aktywowali serwer MFA przed 1 lipca, nie zobaczą zmiany. Nadal będzie można pobierać najnowszą wersję, pobierać przyszłe aktualizacje i generować poświadczenia aktywacji.

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Serwer Multi-Factor Authentication.](../authentication/howto-mfaserver-deploy.md) Aby uzyskać więcej informacji na temat usługi Azure AD Multi-Factor Authentication opartej na [chmurze, zobacz Planning a cloud-based Azure AD Multi-Factor Authentication deployment](../authentication/howto-mfa-getstarted.md)(Planowanie wdrożenia usługi Azure AD Multi-Factor Authentication opartego na chmurze).

---

## <a name="may-2019"></a>Maj 2019 r.

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Zmiana usługi: Przyszła obsługa tylko protokołów TLS 1.2 w serwer proxy aplikacji usługi

**Wpisz:** Planowanie zmiany kategorii **Usługi: Możliwość** produktu serwera proxy **aplikacji:** Access Control

Aby zapewnić najlepsze w swojej klasie szyfrowanie dla naszych klientów, ograniczamy dostęp tylko do protokołów TLS 1.2 w serwer proxy aplikacji usługi. Ta zmiana jest stopniowo wprowadzana dla klientów, którzy już korzystali tylko z protokołów TLS 1.2, więc nie powinny zostać wprowadzone żadne zmiany.

Okres świadczenia usług TLS 1.0 i TLS 1.1 nastąpi 31 sierpnia 2019 r., ale udostępnimy dodatkowe powiadomienie zaawansowane, aby mieć czas na przygotowanie się do tej zmiany. Aby przygotować się do tej zmiany, upewnij się, że kombinacje klient-serwer i serwer przeglądarki, w tym wszystkich klientów, których użytkownicy używają do uzyskiwania dostępu do aplikacji opublikowanych za pośrednictwem usługi serwer proxy aplikacji, zostały zaktualizowane w celu używania protokołu TLS 1.2 w celu utrzymania połączenia z usługą serwer proxy aplikacji. Aby uzyskać więcej informacji, zobacz [Add an on-premises application for remote access through serwer proxy aplikacji in Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md#prerequisites).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Wyświetlanie danych logowania związanych z aplikacją przy użyciu raportu użycia i szczegółowych informacji

**Wpisz:** Nowa kategoria usługi **feature: Możliwości** **produktu Aplikacje dla przedsiębiorstw:** Monitorowanie & raportowania

Teraz możesz użyć raportu użycia i szczegółowych  informacji znajdującego się w obszarze Aplikacje dla przedsiębiorstw usługi Azure Portal, aby uzyskać zorientowany na aplikację widok danych logowania, w tym informacje dotyczące:

- Najocej używane aplikacje dla organizacji

- Aplikacje z najczęściej nieudanym logowaniem

- Najważniejsze błędy logowania dla każdej aplikacji

Aby uzyskać więcej informacji na temat tej funkcji, zobacz Raport użycia [i szczegółowych informacji w Azure Active Directory portal](../reports-monitoring/concept-usage-insights-report.md)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatyzowanie aprowizowania użytkowników w aplikacjach w chmurze przy użyciu usługi Azure AD

**Wpisz:** Nowa kategoria usługi **feature: Możliwości** **produktu Aplikacje dla przedsiębiorstw:** Monitorowanie & raportowania

Postępuj zgodnie z tymi nowymi samouczkami, aby zautomatyzować tworzenie, usuwanie i aktualizowanie kont użytkowników dla następujących aplikacji w chmurze przy użyciu usługi Azure AD Provisioning Service:

- [Comeet](../saas-apps/comeet-recruiting-software-provisioning-tutorial.md)

- [DynamicSignal](../saas-apps/dynamic-signal-provisioning-tutorial.md)

- [KeeperSecurity](../saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial.md)

Możesz również wykonać kroki tego nowego samouczka [usługi Dropbox,](../saas-apps/dropboxforbusiness-provisioning-tutorial.md)który zawiera informacje na temat sposobu aprowizowania obiektów grupy.

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację za pomocą zautomatyzowanego aprowizowania kont użytkowników, zobacz [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Automatyzowanie aprowzowania użytkowników w aplikacjach SaaS za pomocą usługi Azure AD).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Ocena bezpieczeństwa tożsamości jest teraz dostępna w usłudze Azure AD (ogólna dostępność)

**Wpisz:** Nowa kategoria **usługi funkcji:** Możliwość produktu nie **obejmuje:** Identity Security & Protection

Teraz możesz monitorować i poprawiać stan zabezpieczeń tożsamości przy użyciu funkcji bezpiecznego oceny tożsamości w usłudze Azure AD. Funkcja wskaźników bezpieczeństwa tożsamości używa jednego pulpitu nawigacyjnego, aby ułatwić:

- Obiektywnie zmierz poziom zabezpieczeń tożsamości na podstawie wyniku z wartości od 1 do 223.

- Planowanie ulepszeń zabezpieczeń tożsamości

- Przegląd powodzenia ulepszeń zabezpieczeń

Aby uzyskać więcej informacji na temat funkcji oceny zabezpieczeń tożsamości, zobacz [What is the identity secure score in Azure Active Directory? (Jaki](./identity-secure-score.md)jest wynik bezpieczeństwa tożsamości w Azure Active Directory?).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nowe Rejestracje aplikacji jest teraz dostępne (ogólna dostępność)

**Wpisz:** Nowa kategoria **usługi funkcji:** Możliwości produktu Authentications (Logins) **Product:** Developer Experience

Nowe środowisko [Rejestracje aplikacji](https://aka.ms/appregistrations) jest teraz ogólnie dostępne. To nowe środowisko obejmuje wszystkie najważniejsze funkcje, które znasz z witryny Azure Portal i portalu rejestracji aplikacji, i ulepsza je za pomocą:

- **Lepsze zarządzanie aplikacją.** Zamiast widzieć aplikacje w różnych portalach, możesz teraz wyświetlić wszystkie aplikacje w jednej lokalizacji.

- **Uproszczona rejestracja aplikacji.** Od ulepszonego środowisko nawigacji po przerejestrowane środowisko wyboru uprawnień , teraz można łatwiej rejestrować aplikacje i zarządzać nimi.

- **Bardziej szczegółowe informacje.** Możesz znaleźć więcej szczegółów na temat aplikacji, w tym przewodniki Szybki start i inne.

Aby uzyskać więcej informacji, zobacz [Platforma tożsamości firmy Microsoft,](../develop/index.yml) a [środowisko Rejestracje aplikacji jest teraz ogólnie dostępne.](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) ogłoszenie w blogu.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nowe możliwości dostępne w interfejsie API ryzykownych użytkowników dla usługi Identity Protection

**Wpisz:** Nowa kategoria **usługi feature:** Identity Protection **Product capability:** Identity Security & Protection

Z zadowoleniem ogłaszamy, że możesz teraz używać interfejsu API ryzykownych użytkowników do pobierania historii ryzyka użytkowników, odrzucania ryzykownych użytkowników i potwierdzania, że użytkownicy są złamani. Ta zmiana pomaga wydajniej aktualizować stan ryzyka użytkowników i rozumiać ich historię ryzyka.

Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API ryzykownych użytkowników.](/graph/api/resources/riskyuser?view=graph-rest-beta&preserve-view=true)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — maj 2019 r.

**Wpisz:** Nowa kategoria **usługi feature: Możliwości** **produktu Aplikacje dla przedsiębiorstw:** integracja z firmami trzecimi

W maju 2019 r. dodaliśmy te 21 nowych aplikacji z obsługą federacji do galerii aplikacji:

[Freedcamp,](../saas-apps/freedcamp-tutorial.md) [Real Links](../saas-apps/real-links-tutorial.md), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign,](../saas-apps/simple-sign-tutorial.md) [Braze,](../saas-apps/braze-tutorial.md) [Displayr,](../saas-apps/displayr-tutorial.md) [Templafy,](../saas-apps/templafy-tutorial.md) [Marketo Sales Engage](https://toutapp.com/login), [ACLP,](../saas-apps/aclp-tutorial.md) [OutSystems,](../saas-apps/outsystems-tutorial.md) [Meta4 Global HR,](../saas-apps/meta4-global-hr-tutorial.md) [Quantum Workplace,](../saas-apps/quantum-workplace-tutorial.md) [Cobalt,](../saas-apps/cobalt-tutorial.md) [webMethods API Cloud,](../saas-apps/webmethods-integration-cloud-tutorial.md) [RedFlag,](https://pocketstop.com/redflag/) [Whatfix](../saas-apps/whatfix-tutorial.md), [Control](../saas-apps/control-tutorial.md), [JOBHUB](../saas-apps/jobhub-tutorial.md), [HDDGOV,](../saas-apps/neogov-tutorial.md) [Foodee,](../saas-apps/foodee-tutorial.md) [MyVR](../saas-apps/myvr-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z](../saas-apps/tutorial-list.md)Azure Active Directory . Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy [aplikacji w galerii Azure Active Directory aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Ulepszone środowisko tworzenia grup i zarządzania nimi w portalu usługi Azure AD

**Wpisz:** Nowa kategoria **usługi funkcji:** Możliwość produktu do zarządzania **grupą:** Współpraca

Ulepszyliśmy środowisko związane z grupami w portalu usługi Azure AD. Te ulepszenia umożliwiają administratorom lepsze zarządzanie listami grup, listami członków i zapewnianie dodatkowych opcji tworzenia.

Wprowadzono następujące ulepszenia:

- Filtrowanie podstawowe według typu członkostwa i typu grupy.

- Dodanie nowych kolumn, takich jak Źródło i Adres e-mail.

- Możliwość wielokrotnego wyboru grup, członków i list właścicieli w celu łatwego usuwania.

- Możliwość wyboru adresu e-mail i dodawania właścicieli podczas tworzenia grupy.

Aby uzyskać więcej informacji, zobacz [Tworzenie podstawowej grupy i dodawanie członków w usłudze Azure Active Directory](./active-directory-groups-create-azure-portal.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Konfigurowanie zasad nazewnictwa dla grup usługi Office 365 w portalu usługi Azure AD (ogólna dostępność)

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Możliwość **produktu do zarządzania grupą:** Współpraca

Administratorzy mogą teraz konfigurować zasady nazewnictwa dla grup usługi Office 365 przy użyciu portalu usługi Azure AD. Ta zmiana pomaga wymusić spójne konwencje nazewnictwa dla grup usługi Office 365 utworzonych lub edytowanych przez użytkowników w organizacji.

Zasady nazewnictwa dla grup usługi Office 365 można skonfigurować na dwa różne sposoby:

- Zdefiniuj prefiksy lub sufiksy, które są automatycznie dodawane do nazwy grupy.

- Przekaż dostosowany zestaw zablokowanych słów dla organizacji, które nie są dozwolone w nazwach grup (na przykład "DYREKTOR NACZELNY, Lista płac, KADR").

Aby uzyskać więcej informacji, zobacz [Wymuszanie zasad nazewnictwa dla grup usługi Office 365.](../enterprise-users/groups-naming-policy.md)

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph api są teraz dostępne dla dzienników aktywności usługi Azure AD (ogólna dostępność)

**Wpisz:** Zmieniono **kategorię usługi funkcji:** **Raportowanie możliwości produktu:** Monitorowanie & raportowania

Z przyjemnością ogłaszamy ogólną dostępność obsługi punktów końcowych Microsoft Graph API dla dzienników aktywności usługi Azure AD. W tej wersji można teraz używać wersji 1.0 zarówno dzienników inspekcji usługi Azure AD, jak i interfejsów API dzienników logowania.

Aby uzyskać więcej informacji, zobacz Omówienie interfejsu API dziennika inspekcji [usługi Azure AD.](/graph/api/resources/azure-ad-auditlog-overview)

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Administratorzy mogą teraz używać dostępu warunkowego w połączonym procesie rejestracji (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria usługi **funkcji:** Możliwości produktu dostępu **warunkowego:** Identity Security & Protection

Administratorzy mogą teraz tworzyć zasady dostępu warunkowego do użycia przez połączoną stronę rejestracji. Obejmuje to stosowanie zasad w celu umożliwienia rejestracji, jeśli:

- Użytkownicy znajdują się w zaufanej sieci.

- Użytkownicy są na niskim poziomie ryzyka związanego z logowaniem.

- Użytkownicy znajdują się na urządzeniu zarządzanym.

- Użytkownicy zgadzają się na warunki użytkowania organizacji.

Aby uzyskać więcej informacji na temat dostępu warunkowego i resetowania haseł, zobacz wpis w blogu Dostęp warunkowy dla połączonego uwierzytelniania wieloskładnikowego i rejestracji [resetowania haseł usługi Azure AD.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348) Aby uzyskać więcej informacji na temat zasad dostępu warunkowego dla połączonego procesu rejestracji, zobacz [Zasady dostępu warunkowego dla połączonej rejestracji.](../authentication/howto-registration-mfa-sspr-combined.md#conditional-access-policies-for-combined-registration) Aby uzyskać więcej informacji na temat funkcji warunków użytkowania usługi Azure AD, [zobacz Azure Active Directory funkcji warunków użytkowania.](../conditional-access/terms-of-use.md)

---

## <a name="april-2019"></a>Kwiecień 2019 r.

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Nowe Analiza zagrożeń w usłudze Azure AD jest teraz dostępne w ramach Azure AD Identity Protection

**Wpisz:** Nowa kategoria **usługi feature:** Azure AD Identity Protection **Product capability:** Identity Security & Protection

Analiza zagrożeń w usłudze Azure AD jest teraz dostępne w ramach zaktualizowanej funkcji Azure AD Identity Protection aplikacji. Ta nowa funkcja pomaga wskazać nietypową aktywność użytkownika dla określonego użytkownika lub działania, które są zgodne ze znanymi wzorcami ataków opartymi na wewnętrznych i zewnętrznych źródłach analizy zagrożeń firmy Microsoft.

Aby uzyskać więcej informacji na temat odświeżonej wersji usługi Azure AD Identity Protection, zobacz blog Cztery najważniejsze rozszerzenia usługi [Azure AD Identity Protection](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) są teraz dostępne w publicznej wersji zapoznawczej oraz Co to jest Azure Active Directory Identity Protection [(odświeżone)?](../identity-protection/overview-identity-protection.md) (Jak działa usługa Azure RMS). Aby uzyskać więcej informacji na Analiza zagrożeń w usłudze Azure AD wykrywania zagrożeń, zobacz artykuł [Azure Active Directory Identity Protection wykrywanie ryzyka.](../identity-protection/concept-identity-protection-risks.md)

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Zarządzanie uprawnieniami usługi Azure AD jest teraz dostępne (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria usługi **funkcji: Możliwość** produktu identity **governance:** nadzór nad tożsamościami

Zarządzanie uprawnieniami usługi Azure AD, obecnie w publicznej wersji zapoznawczej, ułatwia klientom delegowanie zarządzania pakietami dostępu, co definiuje, jak pracownicy i partnerzy biznesowi mogą żądać dostępu, kto musi zatwierdzić i jak długo mają dostęp. Pakiety dostępu mogą zarządzać członkostwem w grupach usług Azure AD i Office 365, przypisaniami ról w aplikacjach dla przedsiębiorstw oraz przypisaniami ról dla witryn usługi SharePoint Online. Więcej informacji na temat zarządzania uprawnieniami można uzyskać w [te tematach dotyczących zarządzania uprawnieniami w usłudze Azure AD.](../governance/entitlement-management-overview.md) Aby dowiedzieć się więcej na temat różnych Azure AD Identity Governance, w tym Privileged Identity Management, przeglądów dostępu i warunków użytkowania, zobacz Co to jest [Azure AD Identity Governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Konfigurowanie zasad nazewnictwa dla grup usługi Office 365 w portalu usługi Azure AD (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria **usługi funkcji:** Możliwość produktu do zarządzania **grupą:** Współpraca

Administratorzy mogą teraz konfigurować zasady nazewnictwa dla grup usługi Office 365 przy użyciu portalu usługi Azure AD. Ta zmiana pomaga wymusić spójne konwencje nazewnictwa dla grup usługi Office 365 utworzonych lub edytowanych przez użytkowników w organizacji.

Zasady nazewnictwa dla grup usługi Office 365 można skonfigurować na dwa różne sposoby:

- Zdefiniuj prefiksy lub sufiksy, które są automatycznie dodawane do nazwy grupy.

- Przekaż dostosowany zestaw zablokowanych słów dla organizacji, które nie są dozwolone w nazwach grup (na przykład "DYREKTOR NACZELNY, Lista płac, KADR").

Aby uzyskać więcej informacji, zobacz [Wymuszanie zasad nazewnictwa dla grup usługi Office 365.](../enterprise-users/groups-naming-policy.md)

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Dzienniki aktywności usługi Azure AD są teraz dostępne w Azure Monitor (ogólna dostępność)

**Wpisz:** Nowa kategoria **usługi feature:** Reporting **Product capability:** Monitoring & Reporting

Aby ułatwić odpowiedź na twoją opinię na temat wizualizacji za pomocą dzienników aktywności usługi Azure AD, wprowadzamy nową funkcję szczegółowych informacji w usłudze Log Analytics. Ta funkcja ułatwia uzyskanie szczegółowych informacji o zasobach usługi Azure AD przy użyciu naszych szablonów interaktywnych nazywanych skoroszytami. Te wstępnie sbudowaną skoroszyty mogą zawierać szczegółowe informacje dla aplikacji lub użytkowników oraz obejmują:

- **Logowania.** Zawiera szczegółowe informacje dotyczące aplikacji i użytkowników, w tym lokalizację logowania, system operacyjny lub klienta i wersję przeglądarki oraz liczbę pomyślnych lub nieudanych logowania.

- **Starsze uwierzytelnianie i dostęp warunkowy.** Zawiera szczegółowe informacje dotyczące aplikacji i użytkowników korzystających ze starszego uwierzytelniania, w tym użycie uwierzytelniania wieloskładnikowego wyzwalane przez zasady dostępu warunkowego, aplikacje korzystające z zasad dostępu warunkowego i tak dalej.

- **Analiza niepowodzeń logowania.** Pomaga określić, czy błędy logowania występują z powodu działania użytkownika, problemów z zasadami lub infrastruktury.

- **Raporty niestandardowe.** Możesz tworzyć nowe lub edytować istniejące skoroszyty, aby pomóc w dostosowywaniu funkcji szczegółowych informacji w organizacji.

Aby uzyskać więcej informacji, zobacz How to use Azure Monitor workbooks for Azure Active Directory reports (Jak używać [skoroszytów Azure Active Directory raportach).](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — kwiecień 2019 r.

**Wpisz:** Nowa kategoria **usługi feature: Możliwości** **produktu Aplikacje dla przedsiębiorstw:** integracja z firmami trzecimi

W kwietniu 2019 r. dodaliśmy te 21 nowych aplikacji z obsługą federacji do galerii aplikacji:

[SAP Fiori,](../saas-apps/sap-fiori-tutorial.md) [HRworks Single Sign-On](../saas-apps/hrworks-single-sign-on-tutorial.md), [Percolate,](../saas-apps/percolate-tutorial.md) [MobiControl,](../saas-apps/mobicontrol-tutorial.md) [Citrix NetScaler,](../saas-apps/citrix-netscaler-tutorial.md) [Shibumi,](../saas-apps/shibumi-tutorial.md) [Benchling](../saas-apps/benchling-tutorial.md), [MileIQ,](https://mileiq.onelink.me/991934284/7e980085) [PageDNA,](../saas-apps/pagedna-tutorial.md) [EduBrite LMS,](../saas-apps/edubrite-lms-tutorial.md) [RStudio Connect,](../saas-apps/rstudio-connect-tutorial.md) [AMMS,](../saas-apps/amms-tutorial.md) [Mitel Connect,](../saas-apps/mitel-connect-tutorial.md) [Alibaba Cloud (logowanie](../saas-apps/alibaba-cloud-service-role-based-sso-tutorial.md)jednokrotne oparte na rolach) , [Certent Enterprise Management,](../saas-apps/certent-equity-management-tutorial.md) [Sectigo Certificate Manager,](../saas-apps/sectigo-certificate-manager-tutorial.md) [GreenOrbit,](../saas-apps/greenorbit-tutorial.md) [Workgrid](../saas-apps/workgrid-tutorial.md), [monday.com](../saas-apps/mondaycom-tutorial.md), [SurveyMonkey Enterprise](../saas-apps/surveymonkey-enterprise-tutorial.md), [Indiggo](https://indiggolead.com/)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z](../saas-apps/tutorial-list.md)Azure Active Directory . Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy aplikacji w [galerii Azure Active Directory aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nowa opcja częstotliwości przeglądów dostępu i wybór wielu ról

**Wpisz:** Nowa kategoria usługi **funkcji: Przeglądy** dostępu **Możliwość produktu:** Nadzór nad tożsamościami

Nowe aktualizacje w przeglądach dostępu usługi Azure AD umożliwiają:

- Zmień częstotliwość przeglądów dostępu na półroczne, a także istniejące opcje tygodniowe, miesięczne, kwartalne i roczne.

- Podczas tworzenia pojedynczego przeglądu dostępu wybierz wiele ról zasobów usługi Azure AD i platformy Azure. W takiej sytuacji wszystkie role są ustawiane przy użyciu tych samych ustawień, a wszyscy recenzentzy są powiadamiani w tym samym czasie.

Aby uzyskać więcej informacji na temat tworzenia przeglądu dostępu, zobacz Create an access review of groups or applications in Azure AD access reviews (Tworzenie przeglądu dostępu grup lub aplikacji w przeglądach [dostępu usługi Azure AD).](../governance/create-access-review.md)

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect systemu alertów e-mail są przejściowe i wysyłają nowe informacje o nadawcy wiadomości e-mail do niektórych klientów

**Wpisz:** Zmieniono **kategorię usługi funkcji:** AD Sync **Produkt:** Platforma

Azure AD Connect jest w trakcie przechodzenia systemów alertów e-mail, potencjalnie pokazując niektórym klientom nowego nadawcę wiadomości e-mail. Aby rozwiązać ten problemy, należy dodać element do listy zezwalania w organizacji lub nie będzie można nadal otrzymywać ważnych alertów z usługi Office 365, platformy Azure ani usług `azure-noreply@microsoft.com` synchronizacji.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Zmiany sufiksu nazwy UPN są teraz pomyślne między domenami federskimi w Azure AD Connect

**Wpisz:** Stała **kategoria usługi:** AD Sync **Produkt:** Platforma

Teraz można pomyślnie zmienić sufiks nazwy UPN użytkownika z jednej domeny federskiej na inną domenę federacyjną w Azure AD Connect. Ta poprawka oznacza, że podczas cyklu synchronizacji nie powinien już pojawiać się komunikat o błędzie FederatedDomainChangeError lub otrzymywać wiadomości e-mail z powiadomieniem "Nie można zaktualizować tego obiektu w programie Azure Active Directory, ponieważ atrybut [FederatedUser.UserPrincipalName] jest nieprawidłowy. Zaktualizuj wartość w lokalnych usługach katalogowych".

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z błędami podczas synchronizacji.](../hybrid/tshoot-connect-sync-errors.md#federateddomainchangeerror)

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Zwiększone zabezpieczenia przy użyciu zasad dostępu warunkowego opartego na ochronie aplikacji w usłudze Azure AD (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria usługi **funkcji:** Możliwości produktu dostępu **warunkowego:** Identity Security & Protection

Ochrona aplikacji warunkowy jest teraz dostępny przy użyciu zasad **Wymagaj ochrony** aplikacji. Te nowe zasady pomagają zwiększyć bezpieczeństwo organizacji, zapobiegając:

- Użytkownicy uzyskujący dostęp do aplikacji bez Microsoft Intune licencji.

- Użytkownicy nie mogą uzyskać Microsoft Intune ochrony aplikacji.

- Użytkownicy uzyskujący dostęp do aplikacji bez skonfigurowanych zasad Microsoft Intune ochrony aplikacji.

Aby uzyskać więcej informacji, zobacz How to Require app protection policy for cloud app access with Conditional Access (Jak wymagać zasad ochrony aplikacji w [celu uzyskania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego).](../conditional-access/app-protection-based-conditional-access.md)

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nowa obsługa logowania pojedynczego usługi Azure AD i dostępu warunkowego w usłudze Microsoft Edge (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria usługi **funkcji:** Możliwości produktu dostępu **warunkowego:** Identity Security & Protection

Ulepszyliśmy obsługę usługi Azure AD dla aplikacji Microsoft Edge, w tym zapewniamy nową obsługę logowania pojedynczego usługi Azure AD i dostępu warunkowego. Jeśli wcześniej były używane Microsoft Intune Managed Browser, możesz teraz użyć Microsoft Edge zamiast tego.

Aby uzyskać więcej informacji na temat konfigurowania urządzeń i aplikacji oraz zarządzania nimi przy użyciu dostępu warunkowego, zobacz Require managed devices [for cloud app access with Conditional Access (Wymaganie](../conditional-access/require-managed-devices.md) urządzeń zarządzanych w celu uzyskania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego do aplikacji w chmurze) i Require approved client apps for cloud app access with Conditional Access (Wymaganie zatwierdzonych aplikacji klienckich w celu uzyskania dostępu do aplikacji w chmurze przy użyciu dostępu [warunkowego).](../conditional-access/app-based-conditional-access.md) Aby uzyskać więcej informacji na temat zarządzania dostępem przy użyciu zasad Microsoft Edge z zasadami Microsoft Intune, zobacz Zarządzanie dostępem do Internetu przy użyciu przeglądarki chronionej przez zasady Microsoft Intune sieci [.](/intune/app-configuration-managed-browser)

---

## <a name="march-2019"></a>Marzec 2019 r.

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Identity Experience Framework i obsługa zasad niestandardowych w Azure Active Directory B2C jest teraz dostępna (GA)

**Wpisz:** Nowa kategoria **usługi funkcji:** B2C — możliwość produktu do zarządzania tożsamościami **konsumentów:** B2B/B2C

Teraz możesz tworzyć zasady niestandardowe w usłudze Azure AD B2C, w tym następujące zadania, które są obsługiwane na dużą skalę i w ramach naszej umowy SLA platformy Azure:

- Tworzenie i przekazywanie niestandardowych podróży użytkowników uwierzytelniania przy użyciu zasad niestandardowych.

- Opis podróży użytkownika krok po kroku jako wymiany między dostawcami oświadczeń.

- Definiowanie rozgałęzień warunkowych w podróżach użytkownika.

- Przekształcanie i mapowanie oświadczeń do użycia w decyzjach i komunikacji w czasie rzeczywistym.

- Użyj usług z obsługą interfejsu API REST w niestandardowych podróżach użytkowników uwierzytelniania. Na przykład w przypadku dostawców poczty e-mail, crmów i własnościowych systemów autoryzacji.

- Federuj z dostawcami tożsamości, którzy są zgodni z protokołem OpenIDConnect. Na przykład w przypadku wielodostępnych usług Azure AD, dostawców kont społecznościowych lub dostawców weryfikacji dwuskładnikowej.

Aby uzyskać więcej informacji na temat tworzenia zasad niestandardowych, zobacz Developer notes for custom policies in Azure Active Directory B2C (Uwagi dla deweloperów dotyczące zasad niestandardowych w aplikacji [Azure Active Directory B2C)](../../active-directory-b2c/custom-policy-developer-notes.md) i przeczytaj wpis [w blogu Użytkownika Alex Simon,](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791)w tym analizy przypadków.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — marzec 2019 r.

**Wpisz:** Nowa kategoria **usługi feature: Możliwości** **produktu Aplikacje dla przedsiębiorstw:** integracja z firmami trzecimi

W marcu 2019 r. dodaliśmy te 14 nowych aplikacji z obsługą federacji do galerii aplikacji:

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/), [ElementusFlow,](https://office365.cloudapp.mediusflow.com/) [ePlatform,](../saas-apps/eplatform-tutorial.md) [Fulcrum,](../saas-apps/fulcrum-tutorial.md) [ExcelityGlobal,](../saas-apps/excelityglobal-tutorial.md) [Explanation-Based Auditing System](../saas-apps/explanation-based-auditing-system-tutorial.md), [Lean,](../saas-apps/lean-tutorial.md) [Powerschool Performance Matters](../saas-apps/powerschool-performance-matters-tutorial.md), [Cinode,](https://cinode.com/) [Iris Intranet,](../saas-apps/iris-intranet-tutorial.md) [Empactis,](../saas-apps/empactis-tutorial.md) [SmartDraw,](../saas-apps/smartdraw-tutorial.md) [Confirmit Horizons,](../saas-apps/confirmit-horizons-tutorial.md) [TAS](../saas-apps/tas-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z](../saas-apps/tutorial-list.md)Azure Active Directory . Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy [aplikacji w galerii Azure Active Directory aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nowe łączniki aprowizowania usług Zscaler i Atlassian w galerii usługi Azure AD — marzec 2019 r.

**Wpisz:** Nowa kategoria **usługi feature:** App Provisioning **Product capability:** 3rd Party Integration

Zautomatyzuj tworzenie, aktualizowanie i usuwanie kont użytkowników dla następujących aplikacji:

[Zscaler,](../saas-apps/zscaler-provisioning-tutorial.md) [Zscaler Beta,](../saas-apps/zscaler-beta-provisioning-tutorial.md) [Zscaler One,](../saas-apps/zscaler-one-provisioning-tutorial.md) [Zscaler Two,](../saas-apps/zscaler-two-provisioning-tutorial.md) [Zscaler Three,](../saas-apps/zscaler-three-provisioning-tutorial.md) [Zscaler ZSCloud,](../saas-apps/zscaler-zscloud-provisioning-tutorial.md) [Atlassian Cloud](../saas-apps/atlassian-cloud-provisioning-tutorial.md)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację za pomocą automatycznej aprowzowania kont użytkowników, zobacz [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Automatyzowanie aprowzowania użytkowników w aplikacjach SaaS za pomocą usługi Azure AD).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Przywracanie usuniętych grup usługi Office 365 i zarządzanie nimi w portalu usługi Azure AD

**Wpisz:** Nowa kategoria usługi **funkcji:** Możliwość produktu **do zarządzania grupą:** Współpraca

Teraz możesz wyświetlać usunięte grupy usługi Office 365 i zarządzać nimi w portalu usługi Azure AD. Dzięki tej zmianie można zobaczyć, które grupy można przywrócić, a także trwale usunąć wszystkie grupy, które nie są wymagane przez organizację.

Aby uzyskać więcej informacji, zobacz [Przywracanie wygasłych lub usuniętych grup.](../enterprise-users/groups-restore-deleted.md#view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore)

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Logowanie pojedyncze jest teraz dostępne dla aplikacji lokalnych zabezpieczonych za pomocą saml usługi Azure AD za pośrednictwem serwer proxy aplikacji (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria **usługi funkcji: Możliwość** produktu serwera proxy **aplikacji:** Access Control

Teraz możesz udostępnić środowisko logowania jednokrotnego (SSO) dla lokalnych aplikacji uwierzytelnionych za pomocą saml, a także dostęp zdalny do tych aplikacji za pośrednictwem serwer proxy aplikacji. Aby uzyskać więcej informacji na temat sposobu skonfigurowania logowania jednokrotnego SAML przy użyciu aplikacji lokalnych, zobacz Logowanie jednokrotne SAML dla aplikacji lokalnych przy użyciu programu [serwer proxy aplikacji (wersja zapoznawcza).](../manage-apps/application-proxy-configure-single-sign-on-on-premises-apps.md)

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Aplikacje klienckie w pętlach żądań zostaną przerwane, aby zwiększyć niezawodność i środowisko użytkownika

**Wpisz:** Nowa kategoria **usługi funkcji:** Uwierzytelnianie (identyfikatory logowania) **Możliwość produktu:** Uwierzytelnianie użytkownika

Aplikacje klienckie mogą niepoprawnie wysyłać setki tych samych żądań logowania w krótkim czasie. Te żądania, niezależnie od tego, czy są pomyślne, czy nie, przyczyniają się do niskiej jakości obsługi użytkownika i zwiększonego obciążenia dla dostawcy tożsamości, zwiększając opóźnienia dla wszystkich użytkowników i zmniejszając dostępność dostawcy tożsamości.

Ta aktualizacja powoduje wysłanie błędu: do aplikacji klienckich, które wielokrotnie wysyłają zduplikowane żądania, poza `invalid_grant` `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` zakresem normalnego działania. Aplikacje klienckie, które napotkają ten problem, powinny wyświetlać interakcyjny monit, wymagając od użytkownika ponownego zalogowania się. Aby uzyskać więcej informacji na temat tej zmiany i sposobu naprawiania aplikacji w przypadku wystąpienia tego błędu, zobacz Co nowego [w przypadku uwierzytelniania?](../develop/reference-breaking-changes.md#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nowe środowisko użytkownika dzienników inspekcji jest teraz dostępne

**Wpisz:** Zmieniono **kategorię usługi funkcji:** **Raportowanie możliwości produktu:** Monitorowanie & raportowania

Utworzono nową stronę dzienników **inspekcji** usługi Azure AD, aby zwiększyć czytelność i sposób wyszukiwania informacji. Aby wyświetlić nową **stronę Dzienniki inspekcji,** wybierz pozycję **Dzienniki inspekcji** w sekcji **Aktywność** usługi Azure AD.

![Nowa strona dzienników inspekcji z przykładami informacji](media/whats-new/audit-logs-page.png)

Aby uzyskać więcej informacji na temat nowej **strony Dzienniki inspekcji,** zobacz Raporty dotyczące [inspekcji w portalu Azure Active Directory inspekcji.](../reports-monitoring/concept-audit-logs.md#audit-logs)

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nowe ostrzeżenia i wskazówki, które ułatwiają zapobieganie przypadkowej blokadzie administratora przed błędnie skonfigurowanymi zasadami dostępu warunkowego

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Możliwości produktu dostępu **warunkowego:** Identity Security & Protection

Aby zapobiec przypadkowemu zablokowaniu się własnych dzierżaw przez administratorów za pośrednictwem nieprawidłowo skonfigurowanych zasad dostępu warunkowego, w przewodniku utworzono nowe ostrzeżenia i zaktualizowane Azure Portal. Aby uzyskać więcej informacji na temat nowych wskazówek, zobacz What are service dependencies in Azure Active Directory Conditional Access (Czym są zależności usług [w usłudze Azure Active Directory warunkowego).](../conditional-access/service-dependencies.md)

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Ulepszone warunki użytkowania dla użytkowników końcowych na urządzeniach przenośnych

**Wpisz:** Zmieniono **kategorię usługi feature:** Warunki użytkowania **Product capability: Governance (Możliwości produktu:** ład)

Zaktualizowaliśmy nasze istniejące warunki użytkowania, aby ulepszyć sposób przeglądania i wyrażania zgody na warunki użytkowania na urządzeniu przenośnym. Teraz możesz powiększać i pomniejszać, wracać, pobierać informacje i wybierać hiperlinki. Aby uzyskać więcej informacji na temat zaktualizowanych warunków użytkowania, [zobacz Azure Active Directory warunki użytkowania funkcji](../conditional-access/terms-of-use.md#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Dostępne nowe środowisko pobierania dzienników aktywności usługi Azure AD

**Wpisz:** Zmieniono **kategorię usługi feature:** Reporting **Product capability:** Monitoring & Reporting

Teraz możesz pobrać dużą ilość dzienników aktywności bezpośrednio z Azure Portal. Ta aktualizacja umożliwia:

- Pobierz maksymalnie 250 000 wierszy.

- Otrzymuj powiadomienia po zakończeniu pobierania.

- Dostosuj nazwę pliku.

- Określ format danych wyjściowych w formacie JSON lub CSV.

Aby uzyskać więcej informacji na temat tej funkcji, zobacz [Szybki start: pobieranie raportu z inspekcji przy użyciu Azure Portal](../reports-monitoring/quickstart-download-audit-report.md)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Zmiana przerywana: Aktualizacje oceny warunków przez program Exchange ActiveSync (EAS)

**Wpisz:** Planowanie zmiany kategorii **Usługi:** Możliwość produktu dostępu **warunkowego:** Access Control

Trwa proces aktualizowania sposobu, w jaki program Exchange ActiveSync (EAS) ocenia następujące warunki:

- Lokalizacja użytkownika na podstawie kraju, regionu lub adresu IP

- Ryzyko związane z logowaniem

- Platforma urządzeń

Jeśli te warunki były wcześniej używane w zasadach dostępu warunkowego, należy pamiętać, że zachowanie warunku może ulec zmianie. Jeśli na przykład wcześniej warunek lokalizacji użytkownika był używany w zasadach, może się okazać, że zasady są teraz pomijane na podstawie lokalizacji użytkownika.

---

## <a name="february-2019"></a>Luty 2019 r.

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurowalne szyfrowanie tokenów SAML usługi Azure AD (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria usługi **funkcji: Możliwość** produktu Enterprise **Apps:** logowanie jednokrotne

Teraz można skonfigurować dowolną obsługiwaną aplikację SAML do odbierania zaszyfrowanych tokenów SAML. W przypadku skonfigurowania i użycia z aplikacją usługa Azure AD szyfruje emitowane asercji SAML przy użyciu klucza publicznego uzyskanego z certyfikatu przechowywanego w usłudze Azure AD.

Aby uzyskać więcej informacji na temat konfigurowania szyfrowania tokenu SAML, zobacz [Konfigurowanie szyfrowania tokenu SAML usługi Azure AD.](../manage-apps/howto-saml-token-encryption.md)

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Tworzenie przeglądu dostępu dla grup lub aplikacji przy użyciu przeglądów dostępu usługi Azure AD

**Wpisz:** Nowa kategoria usługi **funkcji: Przeglądy** dostępu **Możliwość produktu:** Ład

Teraz możesz uwzględnić wiele grup lub aplikacji w jednym przeglądzie dostępu usługi Azure AD dla członkostwa w grupie lub przypisania aplikacji. Przeglądy dostępu z wieloma grupami lub aplikacjami są ustawiane przy użyciu tych samych ustawień, a wszyscy uwzględnioni recenzentzy są jednocześnie powiadamiani.

Aby uzyskać więcej informacji na temat tworzenia przeglądu dostępu przy użyciu przeglądów dostępu usługi Azure AD, zobacz Tworzenie przeglądu dostępu grup lub aplikacji w [przeglądach dostępu usługi Azure AD](../governance/create-access-review.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — luty 2019 r.

**Wpisz:** Nowa kategoria **usługi funkcji: Możliwość** produktu Enterprise **Apps:** integracja z firmami 3

W lutym 2019 r. dodaliśmy te 27 nowych aplikacji z obsługą federacji do galerii aplikacji:

[Euromonitor Passport,](../saas-apps/euromonitor-passport-tutorial.md) [MindTickle,](../saas-apps/mindtickle-tutorial.md) [FAT FINGER,](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7) [AirStack,](../saas-apps/airstack-tutorial.md) [Oracle Fusion ERP,](../saas-apps/oracle-fusion-erp-tutorial.md) [IDrive,](../saas-apps/idrive-tutorial.md) [Skyward Qmlativ,](../saas-apps/skyward-qmlativ-tutorial.md) [Brightidea,](../saas-apps/brightidea-tutorial.md) [AlertOps,](../saas-apps/alertops-tutorial.md) [Soloinsight-CloudGate SSO,](../saas-apps/soloinsight-cloudgate-sso-tutorial.md)Kliknięcie uprawnień, [Brandfolder,](../saas-apps/brandfolder-tutorial.md) [StoregateSmartFile,](../saas-apps/smartfile-tutorial.md) [Pexip,](../saas-apps/pexip-tutorial.md) [Stormboard,](../saas-apps/stormboard-tutorial.md) [Seismic,](../saas-apps/seismic-tutorial.md) [Share A Dream,](https://www.shareadream.org/how-it-works) [Bugsnag,](../saas-apps/bugsnag-tutorial.md) [webMethods Integration Cloud,](../saas-apps/webmethods-integration-cloud-tutorial.md) [Knowledge Anywhere LMS,](../saas-apps/knowledge-anywhere-lms-tutorial.md) [OU Campus,](../saas-apps/ou-campus-tutorial.md) [Periscope Data,](../saas-apps/periscope-data-tutorial.md) [Netop Portal](../saas-apps/netop-portal-tutorial.md), [smartvid.io,](../saas-apps/smartvid.io-tutorial.md) [PureCloud by Genesys,](../saas-apps/purecloud-by-genesys-tutorial.md) [ClickUp Productivity Platform](../saas-apps/clickup-productivity-platform-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z](../saas-apps/tutorial-list.md)Azure Active Directory . Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy [aplikacji w galerii Azure Active Directory aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="enhanced-combined-mfasspr-registration"></a>Rozszerzona połączona rejestracja uwierzytelniania wieloskładnikowego/SSPR

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Funkcja produktu samoobsługowego resetowania **hasła:** Uwierzytelnianie użytkownika

W odpowiedzi na opinie klientów ulepszyliśmy połączone środowisko podglądu rejestracji uwierzytelniania wieloskładnikowego/SSPR, pomagając użytkownikom szybciej rejestrować swoje informacje zabezpieczające na potrzeby uwierzytelniania wieloskładnikowego i usługi SSPR.

**Aby włączyć obecnie ulepszone środowisko dla użytkowników, wykonaj następujące kroki:**

1. Jako administrator globalny lub administrator użytkowników zaloguj się do witryny Azure Portal i przejdź do Azure Active Directory > Ustawienia użytkownika > Zarządzaj ustawieniami funkcji panelu dostępu w wersji **zapoznawczej.**

2. W opcji **Użytkownicy,** którzy mogą używać funkcji w wersji zapoznawczej do rejestrowania informacji  zabezpieczających i zarządzania nimi — odświeżanie wybierz włączenie funkcji dla wybranej grupy użytkowników lub dla **opcji Wszyscy użytkownicy.**

W ciągu następnych kilku tygodni usuniemy możliwość wyłączenia starej połączonej wersji zapoznawczej rejestracji uwierzytelniania MFA/SSPR dla dzierżaw, które nie zostały jeszcze włączone.

**Aby sprawdzić, czy kontrolka zostanie usunięta dla dzierżawy, wykonaj następujące kroki:**

1. Jako administrator globalny lub administrator użytkowników zaloguj się do witryny Azure Portal i przejdź do Azure Active Directory > Ustawienia użytkownika > Zarządzaj ustawieniami funkcji panelu dostępu w wersji **zapoznawczej.**

2. Jeśli opcja **Użytkownicy, którzy mogą używać** funkcji w wersji zapoznawczej do rejestrowania informacji zabezpieczających i zarządzania nimi jest ustawiona na wartość **Brak,** opcja zostanie usunięta z dzierżawy.

Niezależnie od tego, czy wcześniej włączono stare połączone środowisko podglądu rejestracji uwierzytelniania wieloskładnikowego/SSPR dla użytkowników, stare środowisko zostanie wyłączone w przyszłości. W związku z tym zdecydowanie zalecamy jak najszybciej przejście do nowego, ulepszonego środowisko.

Aby uzyskać więcej informacji na temat ulepszonego procesu rejestracji, zobacz Ulepszenia usługi Azure AD w zakresie rejestracji połączonej z usługą Azure AD i [resetowania hasła.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Zaktualizowane środowisko zarządzania zasadami dla przepływów użytkownika

**Wpisz:** Zmieniono **kategorię usługi funkcji:** B2C — możliwość produktu do zarządzania tożsamościami **konsumentów:** B2B/B2C

Zaktualizowaliśmy proces tworzenia zasad i zarządzania nimi dla przepływów użytkowników (wcześniej znanych jako wbudowane zasady) w łatwiejszy sposób. To nowe środowisko jest teraz ustawieniem domyślnym dla wszystkich dzierżaw usługi Azure AD.

Możesz przekazać dodatkowe opinie i sugestie, korzystając z  ikon uśmiechu lub frustrowania w obszarze Wyślij do nas opinię w górnej części ekranu portalu.

Aby uzyskać więcej informacji na temat nowego środowiska zarządzania zasadami, zobacz blog Azure AD B2C ma teraz dostosowywanie języka [JavaScript](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) i wiele innych nowych funkcji.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Wybierz wersje określonych elementów strony udostępniane przez Azure AD B2C

**Wpisz:** Nowa kategoria **usługi funkcji:** B2C — możliwość produktu do zarządzania tożsamościami **konsumentów:** B2B/B2C

Teraz możesz wybrać określoną wersję elementów strony dostarczanych przez Azure AD B2C. Wybierając określoną wersję, możesz przetestować aktualizacje, zanim pojawią się one na stronie i będzie można uzyskać przewidywalne zachowanie. Ponadto możesz teraz zdecydować się na wymuszanie określonych wersji stron w celu umożliwienia dostosowywania języka JavaScript. Aby włączyć tę funkcję, przejdź do **strony Właściwości** w przepływach użytkownika.

Aby uzyskać więcej informacji na temat wybierania określonych wersji elementów strony, zobacz blog Azure AD B2C teraz dostosowywanie języka [JavaScript](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) i wiele innych nowych funkcji.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurowalne wymagania dotyczące hasła użytkownika końcowego dla usługi B2C (GA)

**Wpisz:** Nowa kategoria **usługi funkcji:** B2C — możliwość produktu do zarządzania tożsamościami **konsumentów:** B2B/B2C

Teraz możesz skonfigurować złożoność hasła organizacji dla użytkowników końcowych, zamiast korzystać z natywnych zasad haseł usługi Azure AD. W **bloku** Właściwości przepływów użytkownika (wcześniej nazywanych zasadami wbudowanymi) możesz  wybrać złożoność hasła Proste  lub Silne albo utworzyć niestandardowy zestaw wymagań. 

Aby uzyskać więcej informacji na temat konfiguracji wymagań dotyczących złożoności hasła, zobacz [Configure complexity requirements for passwords in Azure Active Directory B2C](../../active-directory-b2c/password-complexity.md).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nowe szablony domyślne dla niestandardowych uwierzytelniania znakowanych marką

**Wpisz:** Nowa kategoria **usługi funkcji:** B2C — funkcja produktu do zarządzania tożsamościami **konsumentów:** B2B/B2C

Możesz użyć naszych nowych szablonów domyślnych znajdujących się w bloku Układy strony przepływów użytkownika (wcześniej znanych jako wbudowane zasady), aby utworzyć niestandardowe środowisko uwierzytelniania firmowego dla użytkowników. 

Aby uzyskać więcej informacji na temat korzystania z szablonów, zobacz Azure AD B2C ma teraz dostosowywanie [języka JavaScript i wiele innych nowych funkcji.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)

---

## <a name="january-2019"></a>Styczeń 2019 r.

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Współpraca B2B w usłudze Active Directory przy użyciu uwierzytelniania za pomocą jednego kodu dostępu (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria **usługi funkcji:** Możliwość produktu B2B: B2B/B2C 

Wprowadziliśmy jednorazowe uwierzytelnianie za pomocą kodu dostępu (OTP) dla użytkowników-gości B2B, których nie można uwierzytelnić za pomocą innych metod, takich jak usługa Azure AD, usługa konto Microsoft (MSA) lub federacja Google. Ta nowa metoda uwierzytelniania oznacza, że użytkownicy-goście nie muszą tworzyć nowej konto Microsoft. Zamiast tego podczas realizacji zaproszenia lub uzyskania dostępu do udostępnionego zasobu użytkownik-gość może zażądać tymczasowego kodu do wysłania na adres e-mail. Przy użyciu tego kodu tymczasowego użytkownik-gość może kontynuować logowanie.

Aby uzyskać więcej informacji, zobacz Email [one-time passcode authentication (preview) (E-mail one-time passcode authentication (preview)](../external-identities/one-time-passcode.md) (Uwierzytelnianie za pomocą kodu dostępu w wiadomości e-mail (wersja zapoznawcza) i blog . Usługa Azure AD sprawia, że udostępnianie i współpraca są bezproblemowe dla każdego użytkownika [z dowolnym kontem.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949)

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nowe ustawienia plików cookie serwer proxy aplikacji Azure AD

**Wpisz:** Nowa kategoria **usługi funkcji: Możliwość** produktu serwera proxy **aplikacji:** Access Control

Wprowadziliśmy trzy nowe ustawienia plików cookie dostępne dla aplikacji publikowanych za pośrednictwem serwer proxy aplikacji:

- **Użyj HTTP-Only cookie.** Ustawia **flagę HTTPOnly** na serwer proxy aplikacji plików cookie dostępu i sesji. Włączenie tego ustawienia zapewnia dodatkowe korzyści w zakresie zabezpieczeń, takie jak zapobieganie kopiowaniu lub modyfikowaniu plików cookie za pomocą skryptów po stronie klienta. Zalecamy włączenie tej flagi (wybierz pozycję **Tak),** aby uzyskać dodatkowe korzyści.

- **Użyj bezpiecznego pliku cookie.** Ustawia **flagę Secure** na serwer proxy aplikacji plików cookie dostępu i sesji. Włączenie tego ustawienia zapewnia dodatkowe korzyści w zakresie zabezpieczeń, ponieważ pliki cookie są przesyłane tylko za pośrednictwem bezpiecznych kanałów protokołu TLS, takich jak protokół HTTPS. Zalecamy włączenie tej flagi (wybierz pozycję **Tak),** aby uzyskać dodatkowe korzyści.

- **Użyj trwałego pliku cookie.** Zapobiega wygaśnięciu plików cookie dostępu po zamknięciu przeglądarki internetowej. Te pliki cookie są ostatnie przez cały okres istnienia tokenu dostępu. Pliki cookie są jednak resetowane po osiągnięciu czasu wygaśnięcia lub ręcznym usunięciu pliku cookie przez użytkownika. Zalecamy zachowanie ustawienia domyślnego **Nie**. Ustawienie to jest włączone tylko dla starszych aplikacji, które nie współdzielą plików cookie między procesami.

Aby uzyskać więcej informacji na temat nowych plików cookie, zobacz Ustawienia plików cookie na temat uzyskiwania dostępu do aplikacji [lokalnych w Azure Active Directory](../manage-apps/application-proxy-configure-cookie-settings.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — styczeń 2019 r.

**Wpisz:** Nowa kategoria **usługi feature: Możliwości** **produktu Aplikacje dla przedsiębiorstw:** integracja z firmami trzecimi

W styczniu 2019 r. dodaliśmy te 35 nowych aplikacji z obsługą federacji do galerii aplikacji:

[Firstbird,](../saas-apps/firstbird-tutorial.md) [Folloze,](../saas-apps/folloze-tutorial.md) [Talent Palette](../saas-apps/talent-palette-tutorial.md), [Infor CloudSuite,](../saas-apps/infor-cloud-suite-tutorial.md) [Cisco Umbrella,](../saas-apps/cisco-umbrella-tutorial.md) [Zscaler Internet Access Administrator,](../saas-apps/zscaler-internet-access-administrator-tutorial.md) [Expiration Reminder,](../saas-apps/expiration-reminder-tutorial.md) [InstaVR Viewer](../saas-apps/instavr-viewer-tutorial.md), [CorpTax](../saas-apps/corptax-tutorial.md), [Verb](https://app.verb.net/login), [OpenLattice,](https://openlattice.com/agora) [TheOrgWiki,](https://www.theorgwiki.com/signup) [Pavaso Digital Close,](../saas-apps/pavaso-digital-close-tutorial.md) [GoodPractice Toolkit](../saas-apps/goodpractice-toolkit-tutorial.md), [Cloud Service PICCO,](../saas-apps/cloud-service-picco-tutorial.md) [AuditBoard,](../saas-apps/auditboard-tutorial.md) [iProva,](../saas-apps/iprova-tutorial.md) [Workable](../saas-apps/workable-tutorial.md), [CallPlease,](https://webapp.callplease.com/create-account/create-account.html) [GTNexus SSO System,](../saas-apps/gtnexus-sso-module-tutorial.md) [CBRE ServiceInsight,](../saas-apps/cbre-serviceinsight-tutorial.md) [Deskradar,](../saas-apps/deskradar-tutorial.md) [Coralogixv,](../saas-apps/coralogix-tutorial.md) [Signagelive,](../saas-apps/signagelive-tutorial.md) [ARES for Enterprise,](../saas-apps/ares-for-enterprise-tutorial.md) [K2 for Office 365,](https://www.k2.com/O365) [Xledger,](https://www.xledger.net/) [iDiD Manager,](../saas-apps/idid-manager-tutorial.md) [HighGear,](../saas-apps/highgear-tutorial.md) [Visitly,](../saas-apps/visitly-tutorial.md) [Korn Ferry ALP,](../saas-apps/korn-ferry-alp-tutorial.md) [Acadia,](../saas-apps/acadia-tutorial.md) [Adoddle cSaas Platform](../saas-apps/adoddle-csaas-platform-tutorial.md)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z Azure Active Directory](../saas-apps/tutorial-list.md). Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy [aplikacji w galerii Azure Active Directory aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nowe Azure AD Identity Protection wersji zapoznawczej (publiczna wersja zapoznawcza)

**Wpisz:** Zmieniono **kategorię usługi feature:** Identity Protection **Product capability:** Identity Security & Protection

Z przyjemnością ogłaszamy, że dodaliśmy następujące ulepszenia do oferty publicznej Azure AD Identity Protection wersji zapoznawczej, w tym:

- Zaktualizowany i bardziej zintegrowany interfejs użytkownika

- Dodatkowe interfejsy API

- Ulepszona ocena ryzyka za pomocą uczenia maszynowego

- Dopasowanie całego produktu do ryzykownych użytkowników i ryzykownych logie

Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [Co to jest Azure Active Directory Identity Protection (odświeżone)?](../identity-protection/overview-identity-protection.md) aby dowiedzieć się więcej i podzielić się swoimi przemyśleniami za pośrednictwem monitów wyświetlanych w produkcie.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nowa Blokada aplikacji dla aplikacji Microsoft Authenticator na urządzeniach z systemami iOS i Android

**Wpisz:** Nowa kategoria **usługi feature: Microsoft Authenticator** App **Product:** Identity Security & Protection

Aby zapewnić większe bezpieczeństwo kodów dostępu, informacji o aplikacji i ustawień aplikacji, możesz włączyć funkcję Blokada aplikacji aplikacji Microsoft Authenticator aplikacji. Włączenie Blokada aplikacji oznacza, że przy każdym otwarciu aplikacji Microsoft Authenticator zostaniesz poproszony o uwierzytelnienie przy użyciu numeru PIN lub danych biometrycznych.

Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące Microsoft Authenticator aplikacji.](../user-help/user-help-auth-app-faq.md)

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Ulepszone Azure AD Privileged Identity Management eksportu danych (PIM)

**Wpisz:** Nowa kategoria **usługi funkcji:** Privileged Identity Management **Produkt:** Privileged Identity Management

Privileged Identity Management (PIM) mogą teraz eksportować wszystkie aktywne i kwalifikujące się przypisania ról dla określonego zasobu, w tym przypisania ról dla wszystkich zasobów podrzędnych. Wcześniej administratorzy musieli uzyskać pełną listę przypisań ról dla subskrypcji i musieli eksportować przypisania ról dla każdego określonego zasobu.

Aby uzyskać więcej informacji, zobacz [Wyświetlanie historii aktywności i inspekcji dla ról zasobów platformy Azure w usłudze PIM.](../privileged-identity-management/azure-pim-resource-rbac.md)

---

## <a name="novemberdecember-2018"></a>Listopad/grudzień 2018 r.

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Użytkownicy usunięci z zakresu synchronizacji nie są już przełączani na konta tylko w chmurze

**Wpisz:** Stała **kategoria usługi:** Możliwość **produktu do zarządzania użytkownikami:** Katalog

>[!Important]
>Słyszeliśmy i rozumieliśmy frustrację z powodu tej poprawki. W związku z tym ta zmiana była przywracana do czasu, gdy możemy ułatwić zaimplementowanie poprawki w organizacji.

Naprawiliśmy usterkę, w której flaga DirSyncEnabled użytkownika była błędnie przełączana na wartość **False,** gdy obiekt Active Directory Domain Services (AD DS) został wykluczony z zakresu synchronizacji, a następnie przeniesiony do usługi Kosz w usłudze Azure AD w następnym cyklu synchronizacji. W wyniku tej poprawki, jeśli użytkownik zostanie wykluczony z zakresu synchronizacji, a następnie przywrócony z usługi Azure AD Kosz, konto użytkownika pozostanie zsynchronizowane z lokalną usługą AD zgodnie z oczekiwaniami i nie będzie można nim zarządzać w chmurze, ponieważ jego źródło urzędu (SoA) pozostaje jako lokalna usługa AD.

Przed tą poprawą był problem, który dotyczył przełączania flagi DirSyncEnabled na wartość False. Dało to błędne wrażenie, że te konta zostały przekonwertowane na obiekty tylko w chmurze i że konta mogą być zarządzane w chmurze. Jednak konta nadal zachowywały swoje soa jako lokalne i wszystkie zsynchronizowane właściwości (atrybuty w tle) pochodzące z lokalnej usługi AD. Ten warunek spowodował wiele problemów w usłudze Azure AD i innych obciążeniach w chmurze (takich jak usługa Exchange Online), które powinny traktować te konta jako zsynchronizowane z usługi AD, ale teraz zachowują się jak konta tylko w chmurze.

Obecnie jedynym sposobem, aby naprawdę przekonwertować konto synchronizowane z usługi AD na konto tylko w chmurze, jest wyłączenie programu DirSync na poziomie dzierżawy, co wyzwala operację zaplecza w celu przeniesienia soA. Ten typ zmiany SoA wymaga (ale nie jest ograniczony do) czyszczenia wszystkich atrybutów powiązanych ze środowisku lokalnym (takich jak lastDirSyncTime i atrybuty w tle) i wysyłania sygnału do innych obciążeń w chmurze, aby jego odpowiedni obiekt został również przekonwertowany na konto tylko w chmurze.

W związku z tym ta poprawka zapobiega bezpośrednim aktualizacjom atrybutu ImmutableID użytkownika zsynchronizowanego z usługą AD, co w niektórych scenariuszach w przeszłości było wymagane. Zgodnie z projektem niezmienny identyfikator obiektu w usłudze Azure AD, jak sama nazwa wskazuje, jest niezmienny. Nowe funkcje zaimplementowane w Azure AD Connect Health i Azure AD Connect synchronizacji są dostępne do obsługi takich scenariuszy:

- **Aktualizacja immutableID na dużą skalę dla wielu użytkowników w podejściu etapowym**

  Na przykład należy wykonać długotrwałą migrację AD DS między lasami. Rozwiązanie: użyj usługi Azure AD Connect  do skonfigurowania zakotwiczenia źródła i podczas migracji użytkownika skopiuj istniejące wartości ImmutableID z usługi Azure AD do atrybutu ms-DS-Consistency-Guid użytkownika lokalnego usługi AD DS nowego lasu. Aby uzyskać więcej informacji, zobacz [Using ms-DS-ConsistencyGuid as sourceAnchor (Używanie ms-DS-ConsistencyGuid jako sourceAnchor).](../hybrid/plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)

- **Aktualizacje immutableID na dużą skalę dla wielu użytkowników w jednym zdjęcie**

  Na przykład podczas implementowania Azure AD Connect popełnisz błąd, a teraz musisz zmienić atrybut SourceAnchor. Rozwiązanie: Wyłącz narzędzie DirSync na poziomie dzierżawy i wyczyść wszystkie nieprawidłowe wartości ImmutableID. Aby uzyskać więcej informacji, zobacz [Wyłączanie synchronizacji katalogów dla usługi Office 365.](/office365/enterprise/turn-off-directory-synchronization)

- **Ponowne dopasowywczanie użytkownika lokalnego z istniejącym użytkownikiem w usłudze Azure AD** Na przykład użytkownik, który został ponownie utworzony w usłudze AD DS, wygeneruje duplikat na koncie usługi Azure AD zamiast dopasowywaną do istniejącego konta usługi Azure AD (obiekt oddzielony). Rozwiązanie: użyj Azure AD Connect Health w Azure Portal, aby ponownie zamapować zakotwiczenie źródła/immutableID. Aby uzyskać więcej informacji, zobacz [Scenariusz obiektu oddzielonego](../hybrid/how-to-connect-health-diagnose-sync-errors.md#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Zmiana przerywana: aktualizacje schematu dzienników inspekcji i logowania za pośrednictwem Azure Monitor

**Wpisz:** Zmieniono **kategorię usługi funkcji:** **Raportowanie możliwości produktu:** Monitorowanie & raportowania

Obecnie publikujemy strumienie dzienników inspekcji i logowania za pośrednictwem usługi Azure Monitor, dzięki czemu można bezproblemowo zintegrować pliki dziennika z narzędziami SIEM lub usługą Log Analytics. Na podstawie opinii użytkowników i w ramach przygotowania do ogłoszenia o ogólnej dostępności tej funkcji wprowadzamy następujące zmiany w schemacie. Te zmiany schematu i powiązane z nimi aktualizacje dokumentacji zostaną wprowadzone w pierwszym tygodniu stycznia.

#### <a name="new-fields-in-the-audit-schema"></a>Nowe pola w schemacie inspekcji
Dodajemy nowe pole **Typ operacji,** aby podać typ operacji wykonywanej na zasobie. Na przykład **dodaj**, **zaktualizuj** lub **usuń**.

#### <a name="changed-fields-in-the-audit-schema"></a>Zmieniono pola w schemacie inspekcji
Następujące pola zmieniają się w schemacie inspekcji:

|Nazwa pola|Co się zmieniło|Stare wartości|Nowe wartości|
|----------|------------|----------|----------|
|Kategoria|To było **pole Nazwa** usługi. Teraz jest to pole **Kategorie** inspekcji. **Nazwa usługi** została zmieniona na **pole loggedByService.**|<ul><li>Aprowizacja kont</li><li>Katalog podstawowy</li><li>Samoobsługowe resetowanie hasła</li></ul>|<ul><li>Zarządzanie użytkownikami</li><li>Zarządzanie grupami</li><li>Zarządzanie aplikacją</li></ul>|
|targetResources|Zawiera **targetResourceType** na najwyższym poziomie.|&nbsp;|<ul><li>Zasady</li><li>Aplikacja</li><li>Użytkownik</li><li>Group (Grupa)</li></ul>|
|loggedByService|Zawiera nazwę usługi, która wygenerowała dziennik inspekcji.|Zero|<ul><li>Aprowizacja kont</li><li>Katalog podstawowy</li><li>Samoobsługowe resetowanie haseł</li></ul>|
|Wynik|Zawiera wynik dzienników inspekcji. Wcześniej ta wartość była wyliczana, ale teraz pokazujemy wartość rzeczywistą.|<ul><li>0</li><li>1</li></ul>|<ul><li>Powodzenie</li><li>Niepowodzenie</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Zmieniono pola w schemacie logowania
Następujące pola zmieniają się w schemacie logowania:

|Nazwa pola|Co się zmieniło|Stare wartości|Nowe wartości|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|To było pole **conditionalaccessPolicies.** Jest to teraz pole **appliedConditionalAccessPolicies.**|Bez zmian|Bez zmian|
|conditionalAccessStatus|Zawiera wynik stanu zasad dostępu warunkowego podczas logowania. Wcześniej ta wartość była wyliczana, ale teraz pokazujemy wartość rzeczywistą.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Powodzenie</li><li>Niepowodzenie</li><li>Nie zastosowano</li><li>Disabled</li></ul>|
|appliedConditionalAccessPolicies: wynik|Zawiera wynik stanu poszczególnych zasad dostępu warunkowego podczas logowania. Wcześniej ta wartość była wyliczana, ale teraz pokazujemy wartość rzeczywistą.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Powodzenie</li><li>Niepowodzenie</li><li>Nie zastosowano</li><li>Disabled</li></ul>|

Aby uzyskać więcej informacji na temat schematu, zobacz Interpretowanie schematu dzienników inspekcji usługi [Azure AD w Azure Monitor (wersja zapoznawcza)](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Ulepszenia ochrony tożsamości dla nadzorowanego modelu uczenia maszynowego i aparatu oceny ryzyka

**Wpisz:** Zmieniono **kategorię usługi feature:** Identity Protection **Product capability:** Risk Scores (Możliwość produktu Identity Protection: oceny ryzyka)

Ulepszenia aparatu oceny ryzyka związanego z użytkownikiem i logowaniem związanego z usługą Identity Protection mogą pomóc zwiększyć dokładność i pokrycie ryzyka związanego z użytkownikiem. Administratorzy mogą zauważyć, że poziom ryzyka użytkownika nie jest już bezpośrednio powiązany z poziomem ryzyka określonych wykrywania oraz że zwiększa się liczba i poziom ryzykownych zdarzeń logowania.

Wykrycia ryzyka są teraz oceniane przez nadzorowany model uczenia maszynowego, który oblicza ryzyko użytkownika przy użyciu dodatkowych funkcji logowania użytkownika i wzorca wykrywania. Na podstawie tego modelu administrator może znaleźć użytkowników o wysokim ryzyku, nawet jeśli wykrycia skojarzone z tym użytkownikiem są o niskim lub średnim ryzyku.

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Administratorzy mogą resetować własne hasło przy użyciu Microsoft Authenticator aplikacji (publiczna wersja zapoznawcza)

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Funkcja produktu samoobsługowego resetowania **hasła:** Uwierzytelnianie użytkowników

Administratorzy usługi Azure AD mogą teraz resetować własne hasło przy użyciu powiadomień aplikacji Microsoft Authenticator lub kodu z dowolnej aplikacji do uwierzytelniania mobilnego lub tokenu sprzętowego. Aby zresetować własne hasło, administratorzy będą teraz mogli używać dwóch z następujących metod:

- Microsoft Authenticator powiadomienia aplikacji

- Inna mobilna aplikacja wystawcy uwierzytelniania / Kod tokenu sprzętowego

- E-mail

- Połączenie telefoniczne

- Wiadomość SMS

Aby uzyskać więcej informacji na temat resetowania haseł za pomocą aplikacji Microsoft Authenticator, zobacz Samoobsługowe resetowanie haseł w usłudze Azure AD — aplikacja mobilna i [samoobsługowe resetowanie hasła (wersja zapoznawcza)](../authentication/concept-sspr-howitworks.md#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nowa rola administratora urządzeń w chmurze usługi Azure AD (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria usługi **funkcji:** Rejestracja urządzeń i zarządzanie **Produkt możliwości:** Kontrola dostępu

Administratorzy mogą przypisywać użytkowników do nowej roli administratora urządzeń w chmurze w celu wykonywania zadań administratora urządzeń w chmurze. Użytkownicy z przypisaną rolą Administratorzy urządzeń w chmurze mogą włączać, wyłączać i usuwać urządzenia w usłudze Azure AD oraz odczytywać klucze funkcji BitLocker Windows 10 (jeśli są obecne) w usłudze Azure Portal.

Aby uzyskać więcej informacji na temat ról i uprawnień, zobacz [Przypisywanie ról administratorów w Azure Active Directory](../roles/permissions-reference.md)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Zarządzanie urządzeniami przy użyciu nowego znacznika czasu aktywności w usłudze Azure AD (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria usługi **funkcji:** Możliwość rejestracji urządzeń i zarządzania **produktem: Zarządzanie** cyklem życia urządzenia

Zdajemy sobie sprawę, że z czasem należy odświeżyć i wycofać urządzenia organizacji w usłudze Azure AD, aby uniknąć nieaktywnego urządzenia w środowisku. Aby ułatwić ten proces, usługa Azure AD aktualizuje urządzenia przy użyciu nowego znacznika czasu aktywności, co ułatwia zarządzanie cyklem życia urządzenia.

Aby uzyskać więcej informacji na temat sposobu uzyskania i używania tego znacznika czasu, zobacz Jak zarządzać nieaktywne [urządzenia w usłudze Azure AD](../devices/manage-stale-devices.md)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Administratorzy mogą wymagać od użytkowników zaakceptowania warunków użytkowania na każdym urządzeniu

**Wpisz:** Nowa kategoria **usługi funkcji: Warunki użytkowania** **Produkt: Ład**

Administratorzy mogą teraz  włączyć opcję Wymagaj zgody od użytkowników na każdym urządzeniu, aby wymagać od użytkowników zaakceptowania warunków użytkowania na każdym urządzeniu, z których korzystają w dzierżawie.

Aby uzyskać więcej informacji, zobacz sekcję Warunki użytkowania 1 urządzenia [w te Azure Active Directory warunki użytkowania.](../conditional-access/terms-of-use.md#per-device-terms-of-use)

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Administratorzy mogą skonfigurować warunki użytkowania tak, aby wygasały zgodnie z harmonogramem cyklicznym

**Wpisz:** Nowa kategoria **usługi funkcji: Warunki użytkowania** **Produkt: Ład**


Administratorzy mogą teraz włączyć **opcję** Wygasanie zgody, aby warunki użytkowania wygasały dla wszystkich użytkowników zgodnie z określonym harmonogramem cyklicznym. Harmonogram może być coroczny, co dwa roku, co kwartał lub co miesiąc. Po wygaśnięciu warunków użytkowania użytkownicy muszą ponownie to zrobić.

Aby uzyskać więcej informacji, zobacz sekcję Dodawanie warunków użytkowania w [te Azure Active Directory warunki użytkowania.](../conditional-access/terms-of-use.md#add-terms-of-use)

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Administratorzy mogą skonfigurować warunki użytkowania tak, aby wygasały zgodnie z harmonogramem każdego użytkownika

**Wpisz:** Nowa kategoria **usługi funkcji: Warunki użytkowania** **produkt: ład**

Administratorzy mogą teraz określić czas trwania, przez który użytkownik musi ponownie stosować warunki użytkowania. Administratorzy mogą na przykład określić, że użytkownicy muszą ponownie stosować warunki użytkowania co 90 dni.

Aby uzyskać więcej informacji, zobacz sekcję Dodawanie warunków użytkowania w [te Azure Active Directory warunki użytkowania.](../conditional-access/terms-of-use.md#add-terms-of-use)

---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nowe Azure AD Privileged Identity Management e-mail (PIM) dla ról Azure Active Directory użytkowników

**Wpisz:** Nowa kategoria **usługi funkcji:** Privileged Identity Management **Możliwości produktu:** Privileged Identity Management

Klienci korzystający Azure AD Privileged Identity Management (PIM) mogą teraz otrzymywać cotygodniową wiadomość e-mail z podsumowaniem, w tym następujące informacje z ostatnich siedmiu dni:

- Omówienie najbardziej kwalifikujących się i trwałych przypisań ról

- Liczba użytkowników aktywjących role

- Liczba użytkowników przypisanych do ról w uciekierce PIM

- Liczba użytkowników przypisanych do ról poza pim

- Liczba użytkowników"stała się trwała" w uciekierce PIM

Aby uzyskać więcej informacji na temat usługi PIM i dostępnych powiadomień e-mail, zobacz [Powiadomienia e-mail w u usługi PIM.](../privileged-identity-management/pim-email-notifications.md)

---

### <a name="group-based-licensing-is-now-generally-available"></a>Licencjonowanie oparte na grupach jest teraz ogólnie dostępne

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Inne **możliwości produktu:** Katalog

Licencjonowanie oparte na grupach nie jest dostępne w publicznej wersji zapoznawczej i jest teraz ogólnie dostępne. W tej wersji ogólnej ta funkcja jest teraz bardziej skalowalna i umożliwia ponowne przetwarzanie przypisań licencjonowania opartego na grupach dla jednego użytkownika oraz możliwość korzystania z licencjonowania opartego na grupach z licencjami E3/A3 usługi Office 365.

Aby uzyskać więcej informacji na temat licencjonowania opartego na grupach, zobacz Co to jest licencjonowanie oparte na grupach [w Azure Active Directory?](./active-directory-licensing-whatis-azure-portal.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — listopad 2018 r.

**Wpisz:** Nowa kategoria **usługi feature: Możliwości** **produktu Aplikacje dla przedsiębiorstw:** integracja z firmami trzecimi

W listopadzie 2018 r. dodaliśmy te 26 nowych aplikacji z obsługą federacji do galerii aplikacji:

[CoreStack,](https://cloud.corestack.io/site/login) [HubSpot,](../saas-apps/hubspot-tutorial.md) [GetThere,](../saas-apps/getthere-tutorial.md) [Gra-Pe,](../saas-apps/grape-tutorial.md) [eHour,](https://getehour.com/try-now) [Consent2Go,](../saas-apps/consent2go-tutorial.md) [Appinux,](../saas-apps/appinux-tutorial.md) [DriveDollar,](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview) [Useall,](../saas-apps/useall-tutorial.md) [Infinite Campus,](../saas-apps/infinitecampus-tutorial.md) [Alaya,](https://alayagood.com) [HeyBuddy,](../saas-apps/heybuddy-tutorial.md) [Wrike SAML,](../saas-apps/wrike-tutorial.md) [Drift,](../saas-apps/drift-tutorial.md) [Zenegy for Business Central 365,](https://accounting.zenegy.com/) [Everbridge Member Portal,](../saas-apps/everbridge-tutorial.md) [IDEO,](https://profile.ideo.com/users/sign_up) [Ivanti Service Manager (ISM)](../saas-apps/ivanti-service-manager-tutorial.md), [Peakon,](../saas-apps/peakon-tutorial.md) [Allbound SSO,](../saas-apps/allbound-sso-tutorial.md) [Plex Apps - Classic Test,](https://test.plexonline.com/signon) [Plex Apps – Classic,](https://www.plexonline.com/signon) [Plex Apps - UX Test,](https://test.cloud.plex.com/sso) [Plex Apps – UX,](https://cloud.plex.com/sso) [Plex Apps – IAM,](https://accounts.plex.com/) [MX - Childcare Records, Plex, & Financial Tracking System](https://getcrafts.ca/craftsregistration)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z](../saas-apps/tutorial-list.md)Azure Active Directory . Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy aplikacji w galerii Azure Active Directory [aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

## <a name="october-2018"></a>Październik 2018 r.

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Dzienniki usługi Azure AD działają teraz z usługą Azure Log Analytics (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria **usługi funkcji:** **Raportowanie możliwości produktu:** Monitorowanie & raportowania

Z przyjemnością ogłaszamy, że możesz teraz przesyłać dzienniki usługi Azure AD do usługi Azure Log Analytics! Ta najbardziej żądana funkcja zapewnia jeszcze lepszy dostęp do analiz biznesowych, operacyjnych i zabezpieczeń, a także ułatwia monitorowanie infrastruktury. Aby uzyskać więcej informacji, zobacz blog Azure Active Directory Activity logs in Azure Log Analytics now available (Dzienniki aktywności w [usłudze Azure Log Analytics są teraz dostępne).](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — październik 2018 r.

**Wpisz:** Nowa kategoria **usługi funkcji: Możliwość** produktu Enterprise **Apps:** integracja z firmami 3

W październiku 2018 r. dodaliśmy te 14 nowych aplikacji z obsługą federacji do galerii aplikacji:

[My Award Points,](../saas-apps/myawardpoints-tutorial.md) [Vibe HCM,](../saas-apps/vibehcm-tutorial.md)ambyint, [MyWorkDrive,](../saas-apps/myworkdrive-tutorial.md) [BorrowBox,](../saas-apps/borrowbox-tutorial.md)Dialpad, [ON24 Virtual Environment,](../saas-apps/on24-tutorial.md) [RingCentral,](../saas-apps/ringcentral-tutorial.md) [Zscaler Three,](../saas-apps/zscaler-three-tutorial.md) [Phraseanet,](../saas-apps/phraseanet-tutorial.md) [Appraisd,](../saas-apps/appraisd-tutorial.md) [Workspot Control,](../saas-apps/workspotcontrol-tutorial.md) [Shuccho Navi,](../saas-apps/shucchonavi-tutorial.md) [Glassfrog](../saas-apps/glassfrog-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z](../saas-apps/tutorial-list.md)Azure Active Directory . Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy [aplikacji w galerii Azure Active Directory aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services e-mail

**Wpisz:** Nowa kategoria **usługi funkcji:** Azure AD Domain Services **Możliwości produktu:** Azure AD Domain Services

Azure AD Domain Services alerty na Azure Portal o błędach konfiguracji lub problemach z domeną zarządzaną. Te alerty obejmują przewodniki krok po kroku, dzięki czemu możesz spróbować rozwiązać problemy bez konieczności kontaktowania się z pomocą techniczną.

Od października będzie można dostosowywać ustawienia powiadomień dla domeny zarządzanej, dzięki czemu w przypadku wystąpienia nowych alertów do wyznaczonej grupy osób będzie wysyłana wiadomość e-mail, co eliminuje konieczność ciągłego sprawdzania portalu pod potrzeby aktualizacji.

Aby uzyskać więcej informacji, zobacz [Ustawienia powiadomień w Azure AD Domain Services](../../active-directory-domain-services/notifications.md).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Portal usługi Azure AD obsługuje usuwanie domen niestandardowych za pomocą interfejsu API domeny ForceDelete

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Możliwości **produktu do zarządzania katalogami:** Katalog

Z zadowoleniem ogłaszamy, że możesz teraz używać interfejsu API domeny ForceDelete do usuwania niestandardowych nazw domen przez asynchroniczne zmienianie nazw odwołań, takich jak użytkownicy, grupy i aplikacje, z niestandardowej nazwy domeny (contoso.com) z powrotem do początkowej domyślnej nazwy domeny (contoso.onmicrosoft.com).

Ta zmiana ułatwia szybkie usuwanie niestandardowych nazw domen, jeśli organizacja nie używa już tej nazwy lub jeśli musisz używać nazwy domeny z inną usługą Azure AD.

Aby uzyskać więcej informacji, zobacz [Usuwanie niestandardowej nazwy domeny](../enterprise-users/domains-manage.md#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Wrzesień 2018 r.

### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Zaktualizowane uprawnienia roli administratora dla grup dynamicznych

**Wpisz:** Naprawiono **kategorię usługi:** Możliwość **produktu do zarządzania grupą:** Współpraca

Rozwiązaliśmy problem, dzięki czemu określone role administratora mogą teraz tworzyć i aktualizować reguły członkostwa dynamicznego bez konieczności być właścicielem grupy.

Role są:

- Administrator globalny

- Administrator usługi Intune

- Administrator użytkowników

Aby uzyskać więcej informacji, zobacz [Tworzenie grupy dynamicznej i sprawdzanie stanu](../enterprise-users/groups-create-rule.md)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Uproszczone ustawienia Sign-On jednokrotnego (SSO) dla niektórych aplikacji innych firm

**Wpisz:** Nowa kategoria usługi **funkcji: Możliwość** produktu Enterprise **Apps:** logowanie jednokrotne

Zdajemy sobie sprawę, że skonfigurowanie usługi Single Sign-On (SSO) dla aplikacji Typu oprogramowanie jako usługa (SaaS) może być trudne ze względu na unikatowy charakter każdej konfiguracji aplikacji. Zbudowaliśmy uproszczone środowisko konfiguracji do automatycznego wypełniania ustawień konfiguracji logowania jednokrotnego dla następujących aplikacji SaaS innych firm:

- Zendesk

- ArcGis Online

- Jamf Pro

Aby rozpocząć korzystanie z tego jednego kliknięcia, przejdź do **Azure Portal** konfiguracji logowania jednokrotnego  >   dla aplikacji. Aby uzyskać więcej informacji, zobacz [Integracja aplikacji SaaS z Azure Active Directory](../saas-apps/tutorial-list.md)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory — gdzie znajdują się Twoje dane? Strona

**Wpisz:** Nowa kategoria **usługi funkcji:** Inne **możliwości produktu:** GoLocal

Wybierz region firmy na stronie Azure Active Directory **—** Gdzie znajdują się Twoje dane, aby wyświetlić centrum danych platformy Azure, w którym znajdują się dane usługi Azure AD w spoczynku dla wszystkich usług Azure AD. Informacje można filtrować według określonych usług Azure AD dla regionu firmy.

Aby uzyskać dostęp do tej funkcji i uzyskać więcej informacji, [zobacz Azure Active Directory — gdzie znajdują się twoje dane.](https://aka.ms/AADDataMap)

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Nowy plan wdrażania dostępny dla panelu Moje aplikacje Access

**Wpisz:** Nowa kategoria **usługi funkcji: Moje aplikacje** **produkt:** logowanie jednokrotne

Zapoznaj się z nowym planem wdrażania dostępnym na panelu Moje aplikacje Access ( https://aka.ms/deploymentplans) .
Panel Moje aplikacje Access zapewnia użytkownikom jedno miejsce, w którym można znaleźć aplikacje i uzyskać do nich dostęp. Ten portal udostępnia również użytkownikom możliwości samoobsługi, takie jak żądanie dostępu do aplikacji i grup lub zarządzanie dostępem do tych zasobów w imieniu innych osób.

Aby uzyskać więcej informacji, [zobacz Co to jest Moje aplikacje Portal?](../user-help/my-apps-portal-end-user-access.md)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nowa karta Rozwiązywanie problemów i pomoc techniczna na stronie Dzienniki logowania Azure Portal

**Wpisz:** Nowa kategoria usługi **feature:** Reporting **Product capability:** Monitoring & Reporting

Nowa karta **Rozwiązywanie** problemów i  pomoc techniczna na stronie Logowania w aplikacji Azure Portal ma pomóc administratorom i inżynierom pomocy technicznej w rozwiązywaniu problemów związanych z logowaniem do usługi Azure AD. Ta nowa karta zawiera kod błędu, komunikat o błędzie i zalecenia dotyczące korygowania (jeśli są) pomocne w rozwiązaniu problemu. Jeśli nie możesz rozwiązać problemu, oferujemy również nowy sposób tworzenia biletu  pomocy technicznej przy użyciu funkcji  kopiowania do schowka, która wypełnia pola Identyfikator żądania i Data **(UTC)** dla pliku dziennika w bilecie pomocy technicznej.

![Dzienniki logowania z nową kartą](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Rozszerzona obsługa niestandardowych właściwości rozszerzenia używanych do tworzenia reguł członkostwa dynamicznego

**Wpisz:** Zmieniono **kategorię usługi feature:** Group Management **Product capability:** Collaboration (Możliwość produktu do zarządzania grupą: Współpraca)

Dzięki tej aktualizacji można teraz  kliknąć link Pobierz właściwości rozszerzenia niestandardowego w konstruktorze reguł dynamicznej grupy użytkowników, wprowadzić unikatowy identyfikator aplikacji i otrzymać pełną listę właściwości rozszerzenia niestandardowego do użycia podczas tworzenia reguły członkostwa dynamicznego dla użytkowników. Tę listę można również odświeżyć w celu uzyskania nowych właściwości rozszerzenia niestandardowego dla tej aplikacji.

Aby uzyskać więcej informacji na temat używania niestandardowych właściwości rozszerzenia dla reguł członkostwa dynamicznego, zobacz [Właściwości rozszerzenia i niestandardowe właściwości rozszerzenia](../enterprise-users/groups-dynamic-membership.md#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie dla dostępu warunkowego opartego na aplikacji usługi Azure AD

**Wpisz:** Planowanie zmiany Kategoria **usługi: Możliwość** produktu dostępu **warunkowego: Zabezpieczenia** i ochrona tożsamości

Na liście zatwierdzonych aplikacji klienckich znajdują się [następujące aplikacje:](../conditional-access/concept-conditional-access-conditions.md#client-apps)

- Microsoft To-Do

- Usługa Microsoft Stream

Aby uzyskać więcej informacji, zobacz:

- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nowa obsługa resetowania Self-Service hasła z ekranu blokady systemu Windows 7/8/8.1

**Wpisz:** Nowa kategoria **usługi funkcji:** SSPR **Product capability:** User Authentication (Funkcja samoobsługowego uwierzytelniania: uwierzytelnianie użytkowników)

Po skonfigurowaniu tej nowej funkcji użytkownicy zobaczą link do  resetowania hasła na ekranie blokady urządzenia z systemem Windows 7, Windows 8 lub Windows 8.1. Kliknięcie tego linku powoduje, że użytkownik przechodzi przez ten sam przepływ resetowania hasła co w przeglądarce internetowej.

Aby uzyskać więcej informacji, zobacz [How to enable password reset from Windows 7, 8, and 8.1 (Jak](../authentication/howto-sspr-windows.md) włączyć resetowanie haseł z systemów Windows 7, 8 i 8.1)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Powiadomienie o zmianie: Kody autoryzacji nie będą już dostępne do ponownego użycia

**Wpisz:** Planowanie zmiany kategorii **usługi:** Możliwość produktu Authentications (Logins) **Product:** User Authentication (Uwierzytelnianie użytkownika)

Od 15 listopada 2018 r. usługa Azure AD przestanie akceptować wcześniej używane kody uwierzytelniania dla aplikacji. Ta zmiana zabezpieczeń pomaga zapewnić, że usługa Azure AD będzie zgodne ze specyfikacją protokołu OAuth i będzie wymuszana zarówno w punktach końcowych w wersji 1, jak i 2.

Jeśli aplikacja ponownie używa kodów autoryzacji w celu uzyskania tokenów dla wielu zasobów, zalecamy użycie kodu w celu uzyskania tokenu odświeżania, a następnie użycie tego tokenu odświeżania w celu uzyskania dodatkowych tokenów dla innych zasobów. Kody autoryzacji mogą być używane tylko raz, ale tokeny odświeżania mogą być używane wiele razy w wielu zasobach. Aplikacja, która próbuje ponownie użyć kodu uwierzytelniania podczas przepływu kodu OAuth, otrzyma invalid_grant błąd.

Aby uzyskać informacje na temat zmian związanych z protokołami, zobacz pełną listę nowości dotyczących [uwierzytelniania.](../develop/reference-breaking-changes.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — wrzesień 2018 r.

**Wpisz:** Nowa kategoria **usługi funkcji: Możliwość** produktu Enterprise **Apps:** integracja z firmami 3

We wrześniu 2018 r. dodaliśmy te 16 nowych aplikacji z obsługą federacji do galerii aplikacji:

[Uberflip](../saas-apps/uberflip-tutorial.md), [Comeet Recruiting Software,](../saas-apps/comeetrecruitingsoftware-tutorial.md) [Workteam,](../saas-apps/workteam-tutorial.md) [ArcGIS Enterprise,](../saas-apps/arcgisenterprise-tutorial.md) [Nuclino,](../saas-apps/nuclino-tutorial.md) [JDA Cloud,](../saas-apps/jdacloud-tutorial.md) [Snowflake,](../saas-apps/snowflake-tutorial.md)NavigoCloud, [Figma](../saas-apps/figma-tutorial.md), join.me, [ZephyrSSO,](../saas-apps/zephyrsso-tutorial.md) [Silverback,](../saas-apps/silverback-tutorial.md)Riverbed Xirrus EasyPass, [Rackspace SSO,](../saas-apps/rackspacesso-tutorial.md)Enlyft SSO for Azure, SurveyMonkey, [Convene,](../saas-apps/convene-tutorial.md) [dmarcian](../saas-apps/dmarcian-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z Azure Active Directory](../saas-apps/tutorial-list.md). Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy [aplikacji w galerii Azure Active Directory aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="support-for-additional-claims-transformations-methods"></a>Obsługa dodatkowych metod przekształcania oświadczeń

**Wpisz:** Nowa kategoria usługi **feature: Enterprise** Apps **Product capability:** SSO

Wprowadziliśmy nowe metody przekształcania uwierzytelniania, ToLower() i ToUpper(), które mogą być stosowane do tokenów SAML ze strony saml single **Sign-On Configuration** (Konfiguracja konfiguracji pojedynczej opartej na Sign-On SAML).

Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw w usłudze Azure AD](../develop/active-directory-saml-claims-customization.md)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Zaktualizowany interfejs użytkownika konfiguracji aplikacji oparty na saml (wersja zapoznawcza)

**Wpisz:** Zmieniono **kategorię usługi feature:** Enterprise Apps **Product capability:** SSO (Możliwość produktu Aplikacji dla przedsiębiorstw: logowanie jednokrotne)

W ramach zaktualizowanego interfejsu użytkownika konfiguracji aplikacji opartej na saml otrzymasz:

- Zaktualizowane środowisko instruktażowe do konfigurowania aplikacji opartych na saml.

- Więcej informacji o tym, czego brakuje lub które są nieprawidłowe w konfiguracji.

- Możliwość dodawania wielu adresów e-mail w celu powiadamiania o certyfikacie wygaśnięcia.

- Nowe metody przekształcania oświadczenia, ToLower() i ToUpper() i inne.

- Sposób przekazywania własnego certyfikatu podpisywania tokenu dla aplikacji przedsiębiorstwa.

- Sposób ustawienia formatu NameID dla aplikacji SAML oraz sposób ustawienia wartości NameID jako rozszerzeń katalogów.

Aby włączyć ten zaktualizowany widok, kliknij link **Wypróbuj** nasze nowe środowisko w górnej części strony **Logowanie pojedyncze.** Aby uzyskać więcej informacji, zobacz [Tutorial: Configure SAML-based single sign-on for an application with Azure Active Directory](../manage-apps/view-applications-portal.md).

---

## <a name="august-2018"></a>Sierpień 2018 r.

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Zmiany Azure Active Directory zakresów adresów IP

**Wpisz:** Planowanie zmiany **Kategoria usługi: Inne** możliwości **produktu:** Platforma

Wprowadzamy większe zakresy adresów IP do usługi Azure AD, co oznacza, że jeśli skonfigurowano zakresy adresów IP usługi Azure AD dla zapór, routerów lub sieciowych grup zabezpieczeń, należy je zaktualizować. Aktualizujemy tę aktualizację, dzięki czemu nie trzeba będzie ponownie zmieniać konfiguracji zakresu adresów IP zapory, routera ani sieciowych grup zabezpieczeń, gdy usługa Azure AD doda nowe punkty końcowe.

Ruch sieciowy będzie przenoszący się do tych nowych zakresów w ciągu najbliższych dwóch miesięcy. Aby kontynuować nieprzerwaną usługę, należy dodać te zaktualizowane wartości do adresów IP przed 10 września 2018 r.:

- 20.190.128.0/18

- 40.126.0.0/18

Zdecydowanie zalecamy, aby nie usuwać starych zakresów adresów IP, dopóki cały ruch sieciowy nie zostanie przeniesiony do nowych zakresów. Aby uzyskać informacje o przenoszeniu i dowiedzieć się, kiedy można usunąć stare zakresy, zobacz Zakresy adresów URL i IP usługi [Office 365.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Powiadomienie o zmianie: Kody autoryzacji nie będą już dostępne do ponownego użycia

**Wpisz:** Planowanie zmiany kategorii **usługi:** Możliwość produktu Authentications (Logins) **Product:** User Authentication (Uwierzytelnianie użytkownika)

Od 15 listopada 2018 r. usługa Azure AD przestanie akceptować wcześniej używane kody uwierzytelniania dla aplikacji. Ta zmiana zabezpieczeń pomaga zapewnić, że usługa Azure AD będzie zgodne ze specyfikacją protokołu OAuth i będzie wymuszana zarówno w punktach końcowych w wersji 1, jak i 2.

Jeśli aplikacja ponownie używa kodów autoryzacji w celu uzyskania tokenów dla wielu zasobów, zalecamy użycie kodu w celu uzyskania tokenu odświeżania, a następnie użycie tego tokenu odświeżania w celu uzyskania dodatkowych tokenów dla innych zasobów. Kody autoryzacji mogą być używane tylko raz, ale tokeny odświeżania mogą być używane wiele razy w wielu zasobach. W aplikacji, która próbuje ponownie użyć kodu uwierzytelniania podczas przepływu kodu OAuth, zostanie invalid_grant błąd.

Aby uzyskać informacje na temat zmian związanych z protokołami, zobacz pełną listę nowości [dotyczących uwierzytelniania.](../develop/reference-breaking-changes.md)

---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Zbieżne zarządzanie informacjami zabezpieczającymi na temat haseł samoobsługowych (SSPR) i uwierzytelniania wieloskładnikowego (MFA)

**Wpisz:** Nowa kategoria **usługi feature:** SSPR Product capability: User Authentication (Możliwość produktu samoobsługowego **samoobsługowego samoobsługowego przetwarzania danych:** uwierzytelnianie użytkowników)

Ta nowa funkcja ułatwia użytkownikom zarządzanie informacjami zabezpieczającymi (takimi jak numer telefonu, aplikacja mobilna i tak dalej) dla funkcji SSPR i MFA w jednej lokalizacji i w jednym miejscu. w porównaniu do wcześniej, gdzie zostało to zrobione w dwóch różnych lokalizacjach.

To zbieżne środowisko działa również w przypadku osób korzystających z funkcji SSPR lub MFA. Ponadto, jeśli Twoja organizacja nie wymusza rejestracji uwierzytelniania wieloskładnikowego lub SSPR, osoby nadal mogą rejestrować dowolne metody informacji zabezpieczających uwierzytelniania wieloskładnikowego lub SSPR dozwolone przez organizację z Moje aplikacje Portal.

Jest to publiczna wersja zapoznawcza z rezygnacją. Administratorzy mogą włączyć nowe środowisko (w razie potrzeby) dla wybranej grupy lub dla wszystkich użytkowników w dzierżawie. Aby uzyskać więcej informacji na temat zbieżności, zobacz blog [konwergentnej obsługi](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nowe HTTP-Only plików cookie w aplikacjach serwera proxy aplikacji usługi Azure AD

**Wpisz:** Nowa kategoria **usługi funkcji:** Możliwość produktu serwera proxy **aplikacji:** Access Control

W aplikacjach mobilnych istnieje nowe ustawienie o nazwie **Pliki cookie** tylko serwer proxy aplikacji HTTP. To ustawienie pomaga zapewnić dodatkowe zabezpieczenia, uwzględniając flagę HTTPOnly w nagłówku odpowiedzi HTTP dla plików cookie dostępu i sesji programu serwer proxy aplikacji, zatrzymując dostęp do pliku cookie ze skryptu po stronie klienta i uniemożliwiając dalsze akcje, takie jak kopiowanie lub modyfikowanie pliku cookie. Chociaż ta flaga nie była wcześniej używana, pliki cookie zawsze były szyfrowane i przesyłane przy użyciu połączenia TLS, aby ułatwić ochronę przed nieprawidłowymi modyfikacjami.

To ustawienie nie jest zgodne z aplikacjami korzystającymi z kontrolek ActiveX, takimi jak Pulpit zdalny. W takiej sytuacji zalecamy wyłączenie tego ustawienia.

Aby uzyskać więcej informacji na temat ustawienia HTTP-Only Plików cookie, zobacz [Publikowanie aplikacji przy użyciu usługi Azure AD serwer proxy aplikacji.](../manage-apps/application-proxy-add-on-premises-application.md)

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) dla zasobów platformy Azure obsługuje typy zasobów grupy zarządzania

**Wpisz:** Nowa kategoria **usługi funkcji:** Privileged Identity Management **Produkt:** Privileged Identity Management

Ustawienia aktywacji just in time i przypisywania można teraz stosować do typów zasobów grupy zarządzania, podobnie jak w przypadku subskrypcji, grup zasobów i zasobów (takich jak maszyny wirtualne, App Services i inne). Ponadto każda osoba z rolą, która zapewnia administratorowi dostęp do grupy zarządzania, może odnajdywać ten zasób i zarządzać nim w uściśli pim.

Aby uzyskać więcej informacji na temat usługi PIM i zasobów platformy Azure, zobacz [Discover and manage Azure resources by using Privileged Identity Management (Odnajdywanie zasobów](../privileged-identity-management/pim-resource-roles-discover-resources.md) platformy Azure i zarządzanie nimi przy użyciu usługi Privileged Identity Management

---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Dostęp do aplikacji (wersja zapoznawcza) zapewnia szybszy dostęp do portalu usługi Azure AD

**Wpisz:** Nowa kategoria **usługi funkcji:** Privileged Identity Management **Produkt:** Privileged Identity Management

Obecnie aktywowanie roli przy użyciu usługi PIM może potrwać ponad 10 minut, aby uprawnienia zostały wprowadzone. Jeśli zdecydujesz się na korzystanie z dostępu do aplikacji, który jest obecnie dostępny w publicznej wersji zapoznawczej, administratorzy będą mieć dostęp do portalu usługi Azure AD zaraz po zakończeniu żądania aktywacji.

Obecnie dostęp do aplikacji obsługuje tylko środowisko portalu usługi Azure AD i zasoby platformy Azure. Aby uzyskać więcej informacji na temat dostępu do aplikacji i pim, zobacz [Co to jest Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — sierpień 2018 r.

**Wpisz:** Nowa kategoria **usługi funkcji: Możliwość** produktu Enterprise **Apps:** integracja z firmami 3

W sierpniu 2018 r. dodaliśmy te 16 nowych aplikacji z obsługą federacji do galerii aplikacji:

[Hornbill,](../saas-apps/hornbill-tutorial.md) [Bridgeline Unbound,](../saas-apps/bridgelineunbound-tutorial.md) [Walenter Labs](../saas-apps/saucelabs-mobileandwebtesting-tutorial.md)— testy mobilne i internetowe, [Meta Networks Connector,](../saas-apps/metanetworksconnector-tutorial.md)Way We [Do](../saas-apps/waywedo-tutorial.md), [Spotinst,](../saas-apps/spotinst-tutorial.md) [ProMaster (by Inlogik),](../saas-apps/promaster-tutorial.md)SchoolBooking, [4me,](../saas-apps/4me-tutorial.md) [Dovetale,](../saas-apps/dossier-tutorial.md) [N2F — raporty](../saas-apps/n2f-expensereports-tutorial.md)wydatków, [Comm100 Live Chat,](../saas-apps/comm100livechat-tutorial.md) [SafeConnect,](../saas-apps/safeconnect-tutorial.md) [ZenQMS,](../saas-apps/zenqms-tutorial.md) [eLuminate,](../saas-apps/eluminate-tutorial.md) [Dovetale.](../saas-apps/dovetale-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z](../saas-apps/tutorial-list.md)Azure Active Directory . Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy [aplikacji w galerii Azure Active Directory aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Natywna obsługa tableau jest teraz dostępna w usłudze Azure AD serwer proxy aplikacji

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Możliwość produktu serwera proxy **aplikacji:** Access Control

Dzięki naszej aktualizacji z serwera OpenID Connect do protokołu OAuth 2.0 Code Grant dla naszego protokołu uwierzytelniania wstępnego nie trzeba już robić żadnych dodatkowych konfiguracji, aby używać tableau z serwer proxy aplikacji. Ta zmiana protokołu pomaga również serwer proxy aplikacji lepszą obsługę bardziej nowoczesnych aplikacji przy użyciu tylko przekierowań HTTP, które są często obsługiwane w tagach JavaScript i HTML.

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nowa obsługa dodawania firmy Google jako dostawcy tożsamości dla użytkowników-gości B2B w usłudze Azure Active Directory (wersja zapoznawcza)

**Wpisz:** Nowa kategoria **usługi funkcji:** Możliwość produktu B2B: B2B/B2C 

Dzięki skonfigurowaniu federacji z firmą Google w organizacji można pozwolić zaproszonym użytkownikom usługi Gmail na logowanie się do udostępnionych aplikacji i zasobów przy użyciu istniejącego konta Google bez konieczności tworzenia osobistego konta Microsoft (MSA) lub konta usługi Azure AD.

Jest to publiczna wersja zapoznawcza z rezygnacją. Aby uzyskać więcej informacji na temat federacji z usługą Google, zobacz Add Google as an identity provider for B2B guest users (Dodawanie firmy Google jako dostawcy [tożsamości dla użytkowników-gości B2B).](../external-identities/google-federation.md)

---

## <a name="july-2018"></a>Lipiec 2018 r.

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Ulepszenia powiadomień e-Azure Active Directory e-mail

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Inne **możliwości produktu: Zarządzanie** cyklem życia tożsamości

Azure Active Directory e-mail (Azure AD) mają teraz zaktualizowany projekt, a także zmiany adresu e-mail nadawcy i nazwy wyświetlanej nadawcy, gdy są wysyłane z następujących usług:

- Przeglądy dostępu do usługi Azure AD
- Azure AD Connect Health
- Usługa Azure AD Identity Protection
- Azure AD Privileged Identity Management
- Powiadomienia o wygasaniu certyfikatów aplikacji dla przedsiębiorstw
- Powiadomienia usługi App Provisioning Service dla przedsiębiorstw

Powiadomienia e-mail będą wysyłane z następującego adresu e-mail i nazwy wyświetlanej:

- Adres e-mail: azure-noreply@microsoft.com
- Nazwa wyświetlana: Microsoft Azure

Aby uzyskać przykład niektórych nowych projektów wiadomości e-mail i więcej informacji, zobacz Powiadomienia e-mail w [usłudze Azure AD PIM.](../privileged-identity-management/pim-email-notifications.md)

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Dzienniki aktywności usługi Azure AD są teraz dostępne za pośrednictwem Azure Monitor

**Wpisz:** Nowa kategoria **usługi funkcji:** **Raportowanie możliwości produktu:** Monitorowanie & raportowania

Dzienniki aktywności usługi Azure AD są teraz dostępne w publicznej wersji zapoznawczej dla Azure Monitor (usługi monitorowania na całej platformie Azure). Azure Monitor oferuje długoterminowe przechowywanie i bezproblemową integrację, a także następujące ulepszenia:

- Długoterminowe przechowywanie przez kierowanie plików dziennika do własnego konta usługi Azure Storage.

- Bezproblemowa integracja rozwiązania SIEM bez konieczności pisania lub obsługi skryptów niestandardowych.

- Bezproblemowa integracja z własnymi niestandardowymi rozwiązaniami, narzędziami analitycznymi lub rozwiązaniami do zarządzania zdarzeniami.

Aby uzyskać więcej informacji na temat tych nowych możliwości, zobacz nasz blog Dzienniki aktywności usługi [Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) w usłudze Azure Monitor Diagnostics jest teraz w publicznej wersji zapoznawczej, a nasza dokumentacja zawiera dzienniki aktywności usługi Azure Active Directory w usłudze Azure Monitor [(wersja zapoznawcza).](../reports-monitoring/concept-activity-logs-azure-monitor.md)

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informacje o dostępie warunkowym dodane do raportu logowania usługi Azure AD

**Wpisz:** Nowa kategoria **usługi funkcji: Raportowanie** **możliwości produktu:** Identity Security & Protection

Ta aktualizacja pozwala zobaczyć, które zasady są oceniane, gdy użytkownik się do nich zaimknie wraz z wynikiem zasad. Ponadto raport zawiera teraz typ aplikacji klienckiej używany przez użytkownika, dzięki czemu można zidentyfikować starszy ruch protokołu. Wpisy raportu można teraz również wyszukać identyfikator korelacji, który można znaleźć w komunikacie o błędzie dla użytkownika i który może służyć do identyfikowania i rozwiązywania problemów z pasującymi żądaniami logowania.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Wyświetlanie starszych uwierzytelnień za pomocą dzienników aktywności logowania

**Wpisz:** Nowa kategoria usługi **feature:** Reporting **Product capability:** Monitoring & Reporting

Wraz z wprowadzeniem pola **Aplikacja klienca** w dziennikach aktywności logowania klienci mogą teraz zobaczyć użytkowników korzystających ze starszych uwierzytelnień. Klienci będą mogli uzyskać dostęp do tych informacji przy użyciu interfejsu API logowania w usłudze Microsoft Graph lub  za pośrednictwem dzienników aktywności logowania w portalu usługi Azure AD, w którym można filtrować starsze uwierzytelnienia przy użyciu kontrolki Aplikacja klienta. Zapoznaj się z dokumentacją, aby uzyskać więcej informacji.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — lipiec 2018 r.

**Wpisz:** Nowa kategoria **usługi feature: Możliwości** **produktu Aplikacje dla przedsiębiorstw:** Integracja z firmami trzecimi

W lipcu 2018 r. dodaliśmy te 16 nowych aplikacji z obsługą federacji do galerii aplikacji:

[Innovation Hub,](../saas-apps/innovationhub-tutorial.md)Przestępne, [Certain Admin SSO,](../saas-apps/certainadminsso-tutorial.md)PSUC Staging, [iPass SmartConnect,](../saas-apps/ipasssmartconnect-tutorial.md) [Screencast-O-Matic,](../saas-apps/screencast-tutorial.md)PowerSchool Unified Classroom, [Eli Onboarding,](../saas-apps/elionboarding-tutorial.md) [Bomgar Remote Support,](../saas-apps/bomgarremotesupport-tutorial.md) [Nimblex,](../saas-apps/nimblex-tutorial.md) [Imagineer WebVision,](../saas-apps/imagineerwebvision-tutorial.md) [Insight4GRC,](../saas-apps/insight4grc-tutorial.md) [SecureW2 JoinNow Connector,](../saas-apps/securejoinnow-tutorial.md) [Kanbanize,](../saas-apps/kanbanize-tutorial.md) [SmartLPA,](../saas-apps/smartlpa-tutorial.md) [Skills Base](../saas-apps/skillsbase-tutorial.md) [](../saas-apps/leapsome-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z Azure Active Directory](../saas-apps/tutorial-list.md). Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy [aplikacji w galerii Azure Active Directory aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Nowe integracje aplikacji SaaS aprowingu użytkowników — lipiec 2018 r.

**Wpisz:** Nowa kategoria **usługi feature:** App Provisioning **Product capability:** 3rd party Integration

Usługa Azure AD umożliwia automatyzację tworzenia, konserwacji i usuwania tożsamości użytkowników w aplikacjach SaaS, takich jak Dropbox, Salesforce, ServiceNow i nie tylko. W lipcu 2018 r. dodaliśmy obsługę aprowizowania użytkowników dla następujących aplikacji w galerii aplikacji usługi Azure AD:

- [Cisco WebEx](../saas-apps/cisco-webex-provisioning-tutorial.md)

- [Bonusly](../saas-apps/bonusly-provisioning-tutorial.md)

Aby uzyskać listę wszystkich aplikacji, które obsługują aprowizowanie użytkowników w galerii usługi Azure AD, zobacz Integracja aplikacji [SaaS](../saas-apps/tutorial-list.md)z Azure Active Directory .

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health for Sync — łatwiejszy sposób naprawiania oddzielonych i zduplikowanych błędów synchronizacji atrybutów

**Wpisz:** Nowa kategoria **usługi funkcji:** Możliwość produktu AD **Connect:** monitorowanie & raportowania

Azure AD Connect Health wprowadzono samoobsługowe korygowanie, które pomaga wyróżniać i naprawiać błędy synchronizacji. Ta funkcja rozwiązuje problemy z błędami synchronizacji zduplikowanych atrybutów i naprawia obiekty oddzielone od usługi Azure AD. Ta diagnostyka ma następujące korzyści:

- Zawęża błędy synchronizacji zduplikowanych atrybutów, zapewniając określone poprawki

- Stosuje poprawkę dla dedykowanych scenariuszy usługi Azure AD, która rozwiązuje błędy w jednym kroku

- Aby włączyć tę funkcję i korzystać z tej funkcji, nie jest wymagane żadne uaktualnienie ani konfiguracja

Aby uzyskać więcej informacji, zobacz [Diagnozowanie i korygowanie błędów synchronizacji zduplikowanych atrybutów](../hybrid/how-to-connect-health-diagnose-sync-errors.md)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Visual updates to the Azure AD and MSA sign-in experiences (Wizualne aktualizacje funkcji logowania usługi Azure AD i msA)

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Możliwość produktu Azure **AD:** Uwierzytelnianie użytkowników

Zaktualizowaliśmy interfejs użytkownika dla logowania Usługi online firmy Microsoft, na przykład dla usługi Office 365 i platformy Azure. Ta zmiana sprawia, że ekrany są mniej zaśmiecone i prostsze. Aby uzyskać więcej informacji na temat tej zmiany, zobacz blog [Upcoming improvements to the Azure AD sign-in experience](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) (Nadchodzące ulepszenia w funkcji logowania w usłudze Azure AD).

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nowa wersja Azure AD Connect — lipiec 2018 r.

**Wpisz:** Zmieniono **kategorię usługi feature:** App Provisioning **Product capability:** Identity Lifecycle Management

Najnowsza wersja Azure AD Connect obejmuje:

- Poprawki błędów i aktualizacje możliwości obsługi

- Ogólna dostępność integracji Ping-Federate danych

- Aktualizacje najnowszego klienta SQL 2012

Aby uzyskać więcej informacji na temat tej aktualizacji, [zobacz Azure AD Connect: Historia wersji](../hybrid/reference-connect-version-history.md)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Aktualizacje interfejsu użytkownika końcowego warunków użytkowania

**Wpisz:** Zmieniono **kategorię usługi feature:** Warunki użytkowania **Product capability: Governance (Możliwości produktu:** ład)

Aktualizujemy ciąg akceptacji w interfejsie użytkownika końcowego warunków.

**Bieżący tekst.** Aby uzyskać dostęp do zasobów [tenantName], należy zaakceptować warunki użytkowania.<br>**Nowy tekst.** Aby uzyskać dostęp do zasobu [tenantName], należy przeczytać warunki użytkowania.

**Bieżący tekst:** Zaakceptowanie oznacza akceptację wszystkich powyższych warunków użytkowania.<br>**Nowy tekst:** Kliknij przycisk Akceptuj, aby potwierdzić, że przeczytaliśmy i rozumiesz warunki użytkowania.

---

### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Uwierzytelnianie pass-through obsługuje starsze protokoły i aplikacje

**Wpisz:** Zmieniono **kategorię usługi feature:** Authentications (Logins) **Product capability:** User Authentication (Uwierzytelnianie użytkownika)

Uwierzytelnianie pass-through obsługuje teraz starsze protokoły i aplikacje. Następujące ograniczenia są teraz w pełni obsługiwane:

- Logowania użytkowników do starszych aplikacji klienckich pakietu Office, pakietu Office 2010 i pakietu Office 2013, bez konieczności nowoczesnego uwierzytelniania.

- Dostęp do udostępniania kalendarza i informacji o bezpłatnych/zajętych środowiskach hybrydowych programu Exchange tylko w psłudze Office 2010.

- Logowania użytkowników do aplikacji klienckich Skype dla firm bez konieczności nowoczesnego uwierzytelniania.

- Użytkownik loguje się do programu PowerShell w wersji 1.0.

- Program Apple Device Enrollment Program (Apple DEP) przy użyciu Asystenta ustawień systemu iOS.

---

### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Zarządzanie zbieżnymi informacjami zabezpieczającymi na temat samoobsługowego resetowania hasła i uwierzytelniania wieloskładnikowego

**Wpisz:** Nowa kategoria **usługi funkcji:** SSPR **Product capability:** User Authentication (Funkcja samoobsługowego uwierzytelniania: uwierzytelnianie użytkowników)

Ta nowa funkcja umożliwia użytkownikom zarządzanie informacjami zabezpieczającymi (na przykład numerem telefonu, adresem e-mail, aplikacją mobilną itp.) na temat samoobsługowego resetowania hasła (SSPR) i uwierzytelniania wieloskładnikowego (MFA) w jednym przypadku. Użytkownicy nie będą już muszą rejestrować tych samych informacji zabezpieczających dla usługi SSPR i MFA w dwóch różnych możliwościach. To nowe środowisko dotyczy również użytkowników, którzy mają usługę SSPR lub MFA.

Jeśli organizacja nie wymusza rejestracji uwierzytelniania wieloskładnikowego lub funkcji SSPR, użytkownicy mogą rejestrować swoje informacje zabezpieczające za **pośrednictwem Moje aplikacje** portal. W tym miejscu użytkownicy mogą rejestrować dowolne metody włączone dla uwierzytelniania wieloskładnikowego lub funkcji SSPR.

Jest to ogólna publiczna wersja zapoznawcza. Administratorzy mogą włączyć nowe środowisko (w razie potrzeby) dla wybranej grupy użytkowników lub wszystkich użytkowników w dzierżawie.

---

### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Użyj aplikacji Microsoft Authenticator, aby zweryfikować swoją tożsamość podczas resetowania hasła

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Funkcja SSPR **Product:** Uwierzytelnianie użytkowników

Ta funkcja umożliwia użytkownikom niebędącym administratorami weryfikowanie tożsamości podczas resetowania hasła przy użyciu powiadomienia lub kodu z Microsoft Authenticator (lub dowolnej innej aplikacji wystawcy uwierzytelnienia). Gdy administratorzy włączą tę metodę samoobsługowego resetowania hasła, użytkownicy, którzy zarejestrują aplikację mobilną za pośrednictwem usługi aka.ms/mfasetup lub aka.ms/setupsecurityinfo, mogą używać swojej aplikacji mobilnej jako metody weryfikacji podczas resetowania hasła.

Powiadomienie aplikacji mobilnej można włączona tylko w ramach zasad, które wymagają dwóch metod resetowania hasła.

---

## <a name="june-2018"></a>Czerwiec 2018 r.

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Powiadomienie o zmianie: Poprawka zabezpieczeń delegowanego przepływu autoryzacji dla aplikacji korzystających z interfejsu API dzienników aktywności usługi Azure AD

**Wpisz:** Planowanie zmiany kategorii **usługi:** **Raportowanie możliwości produktu:** Monitorowanie & raportowania

Ze względu na silniejsze wymuszanie zabezpieczeń musieliśmy zmienić uprawnienia dla aplikacji, które używają delegowanego przepływu autoryzacji, aby uzyskać dostęp do interfejsów API dzienników aktywności [usługi Azure AD.](../reports-monitoring/concept-reporting-api.md) Ta zmiana nastąpi do **26 czerwca 2018 r.**.

Jeśli którakolwiek z Twoich aplikacji korzysta z interfejsów API dziennika aktywności usługi Azure AD, wykonaj następujące kroki, aby upewnić się, że aplikacja nie przerwie działania po zmianie.

**Aby zaktualizować uprawnienia aplikacji**

1. Zaloguj się do usługi Azure Portal, wybierz **pozycję Azure Active Directory**, a następnie wybierz **pozycję Rejestracje aplikacji.**
2. Wybierz aplikację, która korzysta z interfejsu API dzienników aktywności usługi Azure AD, wybierz pozycję **Ustawienia,** wybierz pozycję Wymagane **uprawnienia,** a następnie wybierz interfejs API Azure Active Directory **Windows.**
3. W obszarze **Uprawnienia delegowane** bloku Włącz **dostęp** zaznacz pole wyboru obok pola Odczyt danych **katalogu,** a następnie wybierz pozycję **Zapisz.**
4. Wybierz **pozycję U przyznaj** uprawnienia, a następnie wybierz pozycję **Tak.**

    >[!Note]
    >Musisz być użytkownik administrator globalny, aby udzielić uprawnień aplikacji.

Aby uzyskać więcej informacji, zobacz obszar [Udzielanie uprawnień](../reports-monitoring/howto-configure-prerequisites-for-reporting-api.md#grant-permissions) w artykule Wymagania wstępne dotyczące uzyskiwania dostępu do interfejsu API raportowania usługi Azure AD.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Konfigurowanie ustawień zabezpieczeń TLS w celu nawiązania połączenia z usługami Azure AD w celu PCI DSS zgodności

**Wpisz:** Nowa kategoria **usługi funkcji:** Możliwość produktu nienadmierowego: Platforma 

Transport Layer Security (TLS) to protokół, który zapewnia prywatność i integralność danych między dwiema komunikując się aplikacjami i jest najczęściej używanym protokołem zabezpieczeń.

Komisja [PCI Security Standards Standards (PCI Security Standards)](https://www.pcisecuritystandards.org/) ustaliła, że wczesne wersje protokołów TLS i Secure Sockets Layer (SSL) muszą zostać wyłączone na rzecz włączenia nowych i bezpieczniejszych protokołów aplikacji ze zgodnością począwszy od 30 czerwca **2018** r. Ta zmiana oznacza, że jeśli łączysz się z usługami Azure AD i PCI DSS zgodność, musisz wyłączyć TLS 1.0. Dostępnych jest wiele wersji TLS, ale TLS 1.2 jest najnowszą wersją dostępną dla Azure Active Directory Services. Zdecydowanie zalecamy przejście bezpośrednio do usługi TLS 1.2 w przypadku kombinacji klient/serwer oraz przeglądarka/serwer.

Przeglądarki z nową datą mogą nie obsługiwać nowszej wersji TLS, takiej jak TLS 1.2. Aby sprawdzić, które wersje protokołu TLS są obsługiwane przez przeglądarkę, przejdź do witryny [Qualys SSL Labs](https://www.ssllabs.com/) i kliknij **przycisk Testuj przeglądarkę.** Zalecamy uaktualnienie do najnowszej wersji przeglądarki internetowej i najlepiej włączyć tylko wersję TLS 1.2.

**Aby włączyć obsługę TLS 1.2, za pomocą przeglądarki**

- **Microsoft Edge i Internet Explorer (obie są ustawiane przy użyciu Internet Explorer)**

    1. Otwórz Internet Explorer wybierz pozycję **Narzędzia**  >  **Opcje internetowe**  >  **Zaawansowane.**
    2. W obszarze **Zabezpieczenia** wybierz **pozycję TLS 1.2,** a następnie wybierz przycisk **OK.**
    3. Zamknij wszystkie okna przeglądarki i uruchom ponownie Internet Explorer.

- **Google Chrome**

    1. Otwórz przeglądarkę Google Chrome, *wpisz chrome://settings/* na pasku adresu i naciśnij **klawisz Enter.**
    2. Rozwiń **pozycję Opcje** zaawansowane, przejdź do obszaru **System** i wybierz pozycję Otwórz ustawienia **serwera proxy.**
    3. W polu Właściwości **internetowe** wybierz kartę **Zaawansowane,** przejdź do obszaru Zabezpieczenia, wybierz pozycję **Użyj TLS 1.2,** a następnie wybierz przycisk **OK.** 
    4. Zamknij wszystkie okna przeglądarki i uruchom ponownie przeglądarkę Google Chrome.

- **Mozilla Firefox**

    1. Otwórz przeglądarkę Firefox, wpisz *about:config* na pasku adresu, a następnie naciśnij **klawisz Enter.**
    2. Wyszukaj termin *TLS,* a następnie wybierz wpis **security.tls.version.max.**
    3. Ustaw wartość na **3,** aby wymusić korzystanie z przeglądarki do wersji TLS 1.2, a następnie wybierz przycisk **OK.**

        >[!NOTE]
        >Przeglądarka Firefox w wersji 60.0 obsługuje zabezpieczenia TLS 1.3, więc można również ustawić wartość security.tls.version.max **na 4**.

    4. Zamknij wszystkie okna przeglądarki i uruchom ponownie przeglądarkę Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — czerwiec 2018 r.

**Wpisz:** Nowa kategoria **usługi feature: Możliwości** **produktu Aplikacje dla przedsiębiorstw:** Integracja z firmami trzecimi

W czerwcu 2018 r. dodaliśmy te 15 nowych aplikacji z obsługą federacji do galerii aplikacji:

[Skytap,](../saas-apps/skytap-tutorial.md) [settling music](../saas-apps/settlingmusic-tutorial.md), [SAML 1.1 Token enabled LOB App,](../saas-apps/saml-tutorial.md) [Supermood,](../saas-apps/supermood-tutorial.md) [Autotask,](../saas-apps/autotaskendpointbackup-tutorial.md) [Endpoint Backup,](../saas-apps/autotaskendpointbackup-tutorial.md) [Skyhigh Networks,](../saas-apps/skyhighnetworks-tutorial.md)Smartway2, [TonicDM,](../saas-apps/tonicdm-tutorial.md) [Moconavi,](../saas-apps/moconavi-tutorial.md) [Zoho One,](../saas-apps/zohoone-tutorial.md) [SharePoint on-premises](../saas-apps/sharepoint-on-premises-tutorial.md), [ForeSee CX Suite](../saas-apps/foreseecxsuite-tutorial.md), [Vidyard,](../saas-apps/vidyard-tutorial.md) [Ichx](../saas-apps/chronicx-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z Azure Active Directory](../saas-apps/tutorial-list.md). Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy [aplikacji w galerii Azure Active Directory aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Usługa Azure AD Password Protection jest dostępna w publicznej wersji zapoznawczej

**Wpisz:** Nowa kategoria usługi **feature:** Identity Protection **Product capability:** User Authentication (Możliwość produktu Identity Protection: Uwierzytelnianie użytkowników)

Użyj usługi Azure AD Password Protection, aby wyeliminować łatwe do odgadnięcia hasła ze środowiska. Eliminowanie tych haseł pomaga zmniejszyć ryzyko naruszenia bezpieczeństwa przed atakiem typu rozsyłania hasła.

W szczególności usługa Azure AD Password Protection ułatwia:

- Ochrona kont organizacji zarówno w usłudze Azure AD, jak i Windows Server Active Directory (AD).
- Umożliwia użytkownikom używanie haseł na liście ponad 500 najczęściej używanych haseł oraz ponad miliona odmian podstawienia tych haseł.
- Administrowanie ochroną haseł w usłudze Azure AD z jednej lokalizacji w portalu usługi Azure AD dla usługi Azure AD i aplikacji Windows Server AD.

Aby uzyskać więcej informacji na temat ochrony haseł w usłudze Azure AD, zobacz Eliminowanie złych [haseł w organizacji.](../authentication/concept-password-ban-bad.md)

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nowy szablon zasad dostępu warunkowego "wszyscy goście" utworzony podczas tworzenia warunków użytkowania

**Wpisz:** Nowa kategoria **usługi funkcji: Warunki użytkowania** **Produkt: Ład**

Podczas tworzenia warunków użytkowania tworzony jest również nowy szablon zasad dostępu warunkowego dla "wszystkich gości" i "wszystkich aplikacji". Ten nowy szablon zasad stosuje nowo utworzoną w tym celu, usprawniając proces tworzenia i wymuszania dla gości.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory Warunki użytkowania funkcji](../conditional-access/terms-of-use.md).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nowy "niestandardowy" szablon zasad dostępu warunkowego utworzony podczas tworzenia warunków użytkowania

**Wpisz:** Nowa kategoria **usługi funkcji: Warunki użytkowania** **Produkt: Ład**

Podczas tworzenia warunków użytkowania jest również tworzony nowy szablon zasad dostępu warunkowego "niestandardowe". Ten nowy szablon zasad umożliwia utworzenie warunków umowy, a następnie natychmiastowe przejście do bloku tworzenia zasad dostępu warunkowego bez konieczności ręcznego nawigowania po portalu.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory Warunki użytkowania funkcji](../conditional-access/terms-of-use.md).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-ad-multi-factor-authentication"></a>Nowe i kompleksowe wskazówki dotyczące wdrażania usługi Azure AD Multi-Factor Authentication

**Wpisz:** Nowa kategoria **usługi funkcji: Inne** **możliwości produktu:** Identity Security & Protection

Wydaliśmy nowe szczegółowe wskazówki dotyczące sposobu wdrażania usługi Azure AD Multi-Factor Authentication (MFA) w organizacji.

Aby wyświetlić przewodnik wdrażania usługi MFA, przejdź do repozytorium [Przewodniki](./active-directory-deployment-plans.md) wdrażania tożsamości w witrynie GitHub. Aby przekazać opinię na temat przewodników wdrażania, użyj formularza [Opinia o planie wdrożenia.](https://aka.ms/deploymentplanfeedback) Jeśli masz pytania dotyczące przewodników wdrażania, skontaktuj się z nami pod [adres IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Delegowane role zarządzania aplikacją usługi Azure AD są dostępne w publicznej wersji zapoznawczej

**Wpisz:** Nowa kategoria **usługi funkcji: Możliwość** **produktu Enterprise Apps:** Access Control

Administratorzy mogą teraz delegować zadania zarządzania aplikacją bez przypisywania roli administratora globalnego. Nowe role i możliwości to:

- **Nowe standardowe role administratora usługi Azure AD:**

    - **Administrator aplikacji.** Daje możliwość zarządzania wszystkimi aspektami wszystkich aplikacji, w tym rejestracją, ustawieniami logowania jednokrotnego, przypisaniami i licencjonowaniem aplikacji, ustawieniami serwera proxy aplikacji oraz zgodą (z wyjątkiem zasobów usługi Azure AD).

    - **Administrator aplikacji w chmurze.** Przyznaje wszystkie możliwości administratora aplikacji, z wyjątkiem serwera proxy aplikacji, ponieważ nie zapewnia on dostępu lokalnego.

    - **Deweloper aplikacji.** Daje możliwość tworzenia rejestracji aplikacji, nawet  jeśli opcja zezwalania użytkownikom na rejestrowanie aplikacji jest wyłączona.

- **Własność (konfigurowanie rejestracji dla aplikacji i aplikacji dla przedsiębiorstwo, podobnie jak w przypadku procesu własności grupy:**

    - **Właściciel rejestracji aplikacji.** Daje możliwość zarządzania wszystkimi aspektami rejestracji posiadanych aplikacji, w tym manifestem aplikacji i dodawaniem kolejnych właścicieli.

    - **Właściciel aplikacji przedsiębiorstwa.** Daje możliwość zarządzania wieloma aspektami posiadanych aplikacji przedsiębiorstwa, w tym ustawieniami logowania jednokrotnego, przypisaniami aplikacji i zgodą (z wyjątkiem zasobów usługi Azure AD).

Aby uzyskać więcej informacji na temat publicznej wersji zapoznawczej, zobacz [Azure AD delegated application management roles are in public preview! (Role zarządzania delegowanymi usługami Azure AD są dostępne w publicznej wersji zapoznawczej).](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) Blog. Aby uzyskać więcej informacji na temat ról i uprawnień, zobacz [Przypisywanie ról administratorów w Azure Active Directory](../roles/permissions-reference.md).

---

## <a name="may-2018"></a>Maj 2018 r.

### <a name="expressroute-support-changes"></a>Zmiany obsługi expressroute

**Wpisz:** Planowanie zmiany kategorii **usługi:** Możliwości produktu Authentications (Logins) **:** Platforma

Oferta oprogramowania jako usługi, na przykład Azure Active Directory (Azure AD), została zaprojektowana tak, aby działała najlepiej bezpośrednio przez Internet, bez konieczności użycia usługi ExpressRoute ani innych tuneli prywatnej sieci VPN. W związku z tym 1 sierpnia **2018** r. przestaniemy obsługiwać usługę ExpressRoute dla usług Azure AD przy użyciu publicznej komunikacji równorzędnej Azure i społeczności platformy Azure w komunikacji równorzędnej firmy Microsoft. Wszystkie usługi, na które ta zmiana ma wpływ, mogą zauważyć stopniowe przejście ruchu usługi Azure AD z usługi ExpressRoute do Internetu.

Chociaż zmieniamy nasze wsparcie, wiemy również, że nadal istnieją sytuacje, w których może być konieczne użycie dedykowanego zestawu obwodów dla ruchu uwierzytelniania. W związku z tym usługa Azure AD będzie nadal obsługiwać ograniczenia zakresu adresów IP dla dzierżawy przy użyciu usługi ExpressRoute i usług już w ramach komunikacji równorzędnej firmy Microsoft ze społecznością "Inne usługi Office 365 Online". Jeśli twoje usługi będą miały wpływ, ale potrzebujesz usługi ExpressRoute, musisz wykonać następujące czynności:

- **Jeśli używasz publicznej komunikacji równorzędnej Azure.** Przejdź do komunikacji równorzędnej firmy Microsoft i zarejestruj się w społeczności innych usług **Office 365 Online (12076:5100).** Aby uzyskać więcej informacji na temat sposobu przechodzenia z publicznej komunikacji równorzędnej Azure do komunikacji równorzędnej firmy Microsoft, zobacz artykuł [Move a public peering to Microsoft peering](../../expressroute/how-to-move-peering.md) (Przenoszenie publicznej komunikacji równorzędnej do komunikacji równorzędnej firmy Microsoft).

- **Jeśli używasz komunikacji równorzędnej firmy Microsoft.** Zarejestruj się w **społeczności Innych usług Office 365 Online (12076:5100).** Aby uzyskać więcej informacji na temat wymagań dotyczących routingu, zobacz sekcję [Support for BGP communities](../../expressroute/expressroute-routing.md#bgp) (Obsługa społeczności BGP) artykułu ExpressRoute routing requirements (Wymagania dotyczące routingu usługi ExpressRoute).

Jeśli musisz nadal korzystać z dedykowanych obwodów, musisz porozmawiać z zespołem ds. kont Microsoft, aby dowiedzieć się, jak uzyskać autoryzację w celu korzystania ze społeczności Inne usługi **Office 365 Online (12076:5100).** Zarządzana przez pakiet MS Office tablica przeglądu zweryfikuje, czy potrzebujesz tych obwodów, i upewnij się, że rozumiesz konsekwencje techniczne ich utrzymania. Nieautoryzowane subskrypcje próbujące utworzyć filtry tras dla usługi Office 365 otrzymają komunikat o błędzie.

---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph API dla scenariuszy administracyjnych dla tou

**Wpisz:** Nowa kategoria **usługi funkcji: Warunki użytkowania** **Produkt: Środowisko** dewelopera

Dodaliśmy interfejsy API Microsoft Graph do administrowania warunkami użytkowania usługi Azure AD. Możesz tworzyć, aktualizować i usuwać obiekty warunków użytkowania.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Dodawanie wielodostępowego punktu końcowego usługi Azure AD jako dostawcy tożsamości w usłudze Azure AD B2C

**Wpisz:** Nowa kategoria **usługi funkcji:** B2C — możliwość produktu do zarządzania tożsamościami **konsumentów:** B2B/B2C

Za pomocą zasad niestandardowych można teraz dodać wspólny punkt końcowy usługi Azure AD jako dostawcę tożsamości w Azure AD B2C. Dzięki temu można mieć pojedynczy punkt wejścia dla wszystkich użytkowników usługi Azure AD, którzy logują się do aplikacji. Aby uzyskać więcej informacji, zobacz Azure Active Directory B2C: Allow users to sign in to a multi-tenant Azure AD identity provider using custom policies (Logowanie użytkowników do dostawcy tożsamości usługi Azure AD z wieloma [dzierżawami przy użyciu zasad niestandardowych).](../../active-directory-b2c/identity-provider-azure-ad-multi-tenant.md)

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Użyj wewnętrznych adresów URL, aby uzyskać dostęp do aplikacji z dowolnego miejsca Moje aplikacje naszym rozszerzeniem logowania i rozszerzeniem logowania usługi Azure AD serwer proxy aplikacji

**Wpisz:** Nowa kategoria **usługi feature:** Moje aplikacje Product capability: SSO (Nowa kategoria usługi funkcji: **Moje aplikacje product capability:** SSO (Możliwość logowania jednokrotnego)

Użytkownicy mogą teraz uzyskać dostęp do aplikacji za pośrednictwem wewnętrznych adresów URL nawet spoza sieci firmowej przy użyciu Moje aplikacje bezpiecznego logowania dla usługi Azure AD. Będzie to działać z dowolną aplikacją opublikowaną przy użyciu usługi Azure AD serwer proxy aplikacji w dowolnej przeglądarce, w Panel dostępu zainstalowane rozszerzenie przeglądarki. Funkcja przekierowywania adresów URL jest włączana automatycznie, gdy użytkownik zaloguje się do rozszerzenia. Rozszerzenie jest dostępne do pobrania w [programach Microsoft Edge,](https://go.microsoft.com/fwlink/?linkid=845176) [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)i [Firefox.](https://go.microsoft.com/fwlink/?linkid=866366)

---

### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory — dane klientów w Europie

**Wpisz:** Nowa kategoria **usługi funkcji:** Inne **możliwości produktu:** GoLocal

Klienci w Europie wymagają, aby ich dane zostały w Europie i nie były replikowane poza europejskiego centrum danych w celu zachowania poufności i przepisów prawa europejskiego. Ten [artykuł](./active-directory-data-storage-eu.md) zawiera szczegółowe informacje dotyczące tożsamości, które będą przechowywane w Europie, a także szczegółowe informacje o informacjach, które będą przechowywane poza europejskich centrami danych.

---

### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nowe integracje aplikacji SaaS aprowingu użytkowników — maj 2018 r.

**Wpisz:** Nowa kategoria **usługi feature:** App Provisioning **Product capability:** 3rd Party Integration

Usługa Azure AD umożliwia automatyzację tworzenia, konserwacji i usuwania tożsamości użytkowników w aplikacjach SaaS, takich jak Dropbox, Salesforce, ServiceNow i nie tylko. W maju 2018 r. dodaliśmy obsługę aprowizowania użytkowników dla następujących aplikacji w galerii aplikacji usługi Azure AD:

- [BlueJeans](../saas-apps/bluejeans-provisioning-tutorial.md)

- [Cornerstone OnDemand](../saas-apps/cornerstone-ondemand-provisioning-tutorial.md)

- [Zendesk](../saas-apps/zendesk-provisioning-tutorial.md)

Aby uzyskać listę wszystkich aplikacji, które obsługują aprowizowanie użytkowników w galerii usługi Azure AD, zobacz [https://aka.ms/appstutorial](../saas-apps/tutorial-list.md) .

---

### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Przeglądy dostępu grup i dostępu do aplikacji w usłudze Azure AD teraz zapewniają cykliczne przeglądy

**Wpisz:** Nowa kategoria usługi **funkcji: Przeglądy** dostępu **Możliwość produktu:** Ład

Przegląd dostępu grup i aplikacji jest teraz ogólnie dostępny w ramach Azure AD — wersja Premium P2.  Administratorzy będą mogli skonfigurować przeglądy dostępu dla członkostwa w grupach i przypisań aplikacji, aby automatycznie powtarzały się w regularnych odstępach czasu, na przykład co miesiąc lub co kwartał.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Dzienniki aktywności usługi Azure AD (logowania i inspekcja) są teraz dostępne za pośrednictwem programu MS Graph

**Wpisz:** Nowa kategoria **usługi funkcji:** **Raportowanie możliwości produktu:** Monitorowanie & raportowania

Dzienniki aktywności usługi Azure AD, w tym dzienniki logowania i inspekcji, są teraz dostępne za pośrednictwem Microsoft Graph API. Aby uzyskać dostęp do tych dzienników, za pośrednictwem interfejsu API Microsoft Graph zostały ujawnione dwa punkty końcowe. Zapoznaj się z [naszymi dokumentami,](../reports-monitoring/concept-reporting-api.md) aby uzyskać dostęp programowy do interfejsów API raportowania usługi Azure AD, aby rozpocząć pracę.

---

### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Ulepszenia funkcji realizacji B2B i opuszczenie organizacji

**Wpisz:** Nowa kategoria **usługi funkcji:** Możliwość produktu B2B: B2B/B2C 

**Realizacja just in time:** Gdy udostępnisz zasób użytkownikowi-gościowi przy użyciu interfejsu API B2B — nie musisz wysyłać wiadomości e-mail z zaproszeniem specjalnym. W większości przypadków użytkownik-gość może uzyskać dostęp do zasobu i zostanie on przejmyny przez środowisko realizacji dokładnie na czas. Brak wpływu z powodu nieodebranych wiadomości e-mail. Nie musisz już pytać użytkowników-gości : "Czy kliknąno link realizacji wysłany przez system?". Oznacza to, że gdy usługa SPO używa menedżera zaproszeń — załączniki w chmurze mogą mieć ten sam kanoniczny adres URL dla wszystkich użytkowników — wewnętrznych i zewnętrznych — w dowolnym stanie realizacji.

**Nowoczesne środowisko realizacji:** Koniec ze stroną docelową realizacji ekranu podzielonego. Użytkownicy będą widzieć nowoczesne środowisko wyrażania zgody z oświadczeniem o ochronie prywatności organizacji zapraszania, podobnie jak w przypadku aplikacji innych firm.

**Użytkownicy-goście mogą opuścić organizacji:** Po zakończeniu relacji użytkownika z organizacją może on samodzielnie opuścić organizację. Koniec z wywoływaniem administratora organizacji zapraszania w celu "usunięcia" i nie trzeba już bierania biletów pomocy technicznej.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD — maj 2018 r.

**Wpisz:** Nowa kategoria **usługi feature: Możliwości** **produktu Aplikacje dla przedsiębiorstw:** Integracja z firmami trzecimi

W maju 2018 r. dodaliśmy te 18 nowych aplikacji z obsługą federacji do naszej galerii aplikacji:

[AwardSpring,](../saas-apps/awardspring-tutorial.md)Infogix Data3Sixty Govern, [Yodeck,](../saas-apps/infogix-tutorial.md) [Jamf Pro,](../saas-apps/jamfprosamlconnector-tutorial.md) [KnowledgeOwl,](../saas-apps/knowledgeowl-tutorial.md) [Envi MMIS,](../saas-apps/envimmis-tutorial.md) [LaunchDarkly,](../saas-apps/launchdarkly-tutorial.md) [Adobe Captivate Prime,](../saas-apps/adobecaptivateprime-tutorial.md) [Montage Online](../saas-apps/montageonline-tutorial.md), [odtądびポkazyka,](../saas-apps/manabipocket-tutorial.md)OpenReel, [Arc Publishing — SSO,](../saas-apps/arc-tutorial.md) [PlanGrid,](../saas-apps/plangrid-tutorial.md) [iWellnessNow,](../saas-apps/iwellnessnow-tutorial.md) [Proxyclick,](../saas-apps/proxyclick-tutorial.md) [Riskware,](../saas-apps/riskware-tutorial.md) [Flock,](../saas-apps/flock-tutorial.md) [Reviewsnap](../saas-apps/reviewsnap-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z Azure Active Directory](../saas-apps/tutorial-list.md).

Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy [aplikacji w galerii Azure Active Directory aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nowe przewodniki krok po kroku dotyczące wdrażania dla Azure Active Directory

**Wpisz:** Nowa kategoria **usługi funkcji:** Inne **możliwości produktu:** Katalog

Nowe, szczegółowe wskazówki dotyczące wdrażania usługi Azure Active Directory (Azure AD), w tym samoobsługowego resetowania hasła( SSPR), logowania jednokrotnego, dostępu warunkowego, serwera proxy aplikacji, aprowizowania użytkowników, Active Directory Federation Services (ADFS) do uwierzytelniania pass-through (PTA) i synchronizacji skrótów haseł (PHS) w usługach ADFS.

Aby wyświetlić przewodniki wdrażania, przejdź do repozytorium [Przewodniki](./active-directory-deployment-plans.md) wdrażania tożsamości w witrynie GitHub. Aby przekazać opinię na temat przewodników wdrażania, użyj formularza [Opinii o planie wdrożenia.](https://aka.ms/deploymentplanfeedback) Jeśli masz pytania dotyczące przewodników wdrażania, skontaktuj się z nami pod [adres IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Wyszukiwanie aplikacji dla przedsiębiorstw — ładowanie większej liczby aplikacji

**Wpisz:** Nowa kategoria usługi **funkcji: Możliwość** produktu Enterprise **Apps:** logowanie jednokrotne

Masz problem ze znalezieniem aplikacji/jednostki usługi? Dodaliśmy możliwość ładowania większej liczby aplikacji na liście wszystkich aplikacji dla przedsiębiorstw. Domyślnie wyświetlane jest 20 aplikacji. Teraz możesz kliknąć pozycję **Załaduj więcej,** aby wyświetlić dodatkowe aplikacje.

---

### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Majowa wersja programu AADConnect zawiera publiczną wersję zapoznawczą integracji z usługą PingFederate, ważne aktualizacje zabezpieczeń, wiele poprawek błędów i nowe narzędzia do rozwiązywania problemów.

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Możliwość produktu AD **Connect:** Zarządzanie cyklem życia tożsamości

Majowa wersja programu AADConnect zawiera publiczną wersję zapoznawczą integracji z usługą PingFederate, ważne aktualizacje zabezpieczeń, wiele poprawek błędów i nowe narzędzia do rozwiązywania problemów. Informacje o wersji można znaleźć [tutaj.](../hybrid/reference-connect-version-history.md)

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Przeglądy dostępu w usłudze Azure AD: automatyczne stosowanie

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Przeglądy dostępu **Możliwość produktu:** Ład

Przeglądy dostępu grup i aplikacji są teraz ogólnie dostępne w ramach Azure AD — wersja Premium P2. Administrator może skonfigurować, aby automatycznie stosować zmiany recenzenta do tej grupy lub aplikacji po zakończeniu przeglądu dostępu. Administrator może również określić, co się stanie z dalszym dostępem użytkownika, jeśli recenzent nie odpowiedział, nie usuną dostępu, nie zachowają dostępu lub nie skorzystają z zaleceń systemowych.

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Tokenów identyfikatorów nie można już zwracać przy użyciu zapytania response_mode dla nowych aplikacji.

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Uwierzytelnianie (identyfikatory logowania) **Możliwość produktu:** Uwierzytelnianie użytkownika

Aplikacje utworzone 25 kwietnia 2018 r. lub później nie będą już mogły zażądać żądania id_token **przy** użyciu zapytania **response_mode.**  Dzięki temu usługa Azure AD jest w tekście ze specyfikacją OIDC i pomaga zmniejszyć powierzchnię ataków na aplikacje.  Aplikacje utworzone przed 25 kwietnia 2018 r.  nie mają zablokowanej funkcji response_mode z response_type o **id_token**.  Zwracany błąd podczas żądania id_token z usługi Azure AD to **AADSTS70007: "query"** nie jest obsługiwaną wartością "response_mode" podczas żądania tokenu .

Fragment **i** **form_post** response_modes nadal działają — podczas tworzenia nowych obiektów aplikacji (na przykład w przypadku użycia serwera proxy aplikacji) należy użyć jednego z tych response_modes przed utworzeniem nowej aplikacji.

---

## <a name="april-2018"></a>Kwiecień 2018 r.

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C dostępu są dostępne

**Wpisz:** Nowa kategoria **usługi funkcji:** B2C — funkcja produktu do zarządzania tożsamościami **konsumentów:** B2B/B2C

Teraz możesz uzyskać dostęp do internetowych interfejsów API zabezpieczonych przez Azure AD B2C przy użyciu tokenów dostępu. Funkcja jest przenosząca z publicznej wersji zapoznawczej do wersji ogólnodostępnych. Ulepszono środowisko interfejsu użytkownika Azure AD B2C aplikacji i internetowych interfejsów API, a wprowadzono inne drobne ulepszenia.

Aby uzyskać więcej informacji, [zobacz Azure AD B2C: Żądanie tokenów dostępu.](../../active-directory-b2c/access-tokens.md)

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testowanie konfiguracji logowania pojedynczego dla aplikacji opartych na saml

**Wpisz:** Nowa kategoria usługi **feature: Enterprise** Apps **Product capability:** SSO

Podczas konfigurowania aplikacji logowania jednokrotnego opartego na saml można przetestować integrację na stronie konfiguracji. Jeśli podczas logowania wystąpi błąd, możesz podać ten błąd w środowisko testowania, a usługa Azure AD zapewni ci kroki rozwiązywania konkretnego problemu.

Aby uzyskać więcej informacji, zobacz:

- [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji Azure Active Directory](../manage-apps/view-applications-portal.md)
- [How to debug SAML-based single sign-on to applications in Azure Active Directory](../manage-apps/debug-saml-sso-issues.md)

---

### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Warunki użytkowania usługi Azure AD mają teraz raportowanie na użytkownika

**Wpisz:** Nowa kategoria **usługi funkcji:** Warunki użytkowania **Produkt: Zgodność**

Administratorzy mogą teraz wybrać dany okres ważności i wyświetlić wszystkich użytkowników, którzy wyraziły zgodę na ten okres oraz datę/godzinę jego zakończenia.

Aby uzyskać więcej informacji, zobacz funkcję [warunków użytkowania usługi Azure AD.](../conditional-access/terms-of-use.md)

---

### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: ryzykowny adres IP AD FS ochrony przed blokadą ekstranetu

**Wpisz:** Nowa kategoria **usługi funkcji: Inne** **możliwości produktu:** Monitorowanie & raportowania

Program Connect Health obsługuje teraz możliwość wykrywania adresów IP, które przekraczają próg nieudanych logowań U/P co godzinę lub codziennie. Możliwości zapewniane przez tę funkcję są:

- Kompleksowy raport przedstawiający adres IP i liczbę nieudanych logowań generowanych co godzinę/codziennie z dostosowywalnym progiem.
- Alerty oparte na wiadomościach e-mail pokazujące, kiedy określony adres IP przekroczył próg nieudanych logowań U/P co godzinę/codziennie.
- Opcja pobierania do szczegółowej analizy danych

Aby uzyskać więcej informacji, zobacz [Raport ryzykownych adresów IP.](../hybrid/how-to-connect-health-adfs.md)

---

### <a name="easy-app-config-with-metadata-file-or-url"></a>Łatwa konfiguracja aplikacji z plikiem metadanych lub adresem URL

**Wpisz:** Nowa kategoria usługi **funkcji: Możliwość** produktu Enterprise **Apps:** logowanie jednokrotne

Na stronie Aplikacje dla przedsiębiorstw administratorzy mogą przekazać plik metadanych SAML w celu skonfigurowania logowania opartego na saml dla galerii usługi Azure AD i aplikacji spoza galerii.

Ponadto możesz użyć adresu URL metadanych federacji aplikacji usługi Azure AD, aby skonfigurować logowanie jednokrotne z aplikacją docelową.

Aby uzyskać więcej informacji, zobacz [Configuring single sign-on to applications that](../manage-apps/view-applications-portal.md)are not in the Azure Active Directory application gallery (Konfigurowanie logowania Azure Active Directory aplikacji).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Usługa Azure AD Warunki użytkowania teraz ogólnie dostępna

**Wpisz:** Nowa kategoria **usługi funkcji: Warunki użytkowania** **Produkt:** Zgodność


Warunki użytkowania usługi Azure AD zostały przeniesione z publicznej wersji zapoznawczej do ogólnie dostępnej.

Aby uzyskać więcej informacji, zobacz funkcję [warunków użytkowania usługi Azure AD.](../conditional-access/terms-of-use.md)

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Zezwalanie na zaproszenia dla użytkowników B2B z określonych organizacji i blokowanie ich

**Wpisz:** Nowa kategoria **usługi funkcji:** Możliwość produktu B2B: B2B/B2C 


Teraz możesz określić, z którymi organizacjami partnerskimi chcesz się podzielić i z którymi chcesz współpracować w usłudze Azure AD B2B Collaboration. W tym celu można utworzyć listę określonych domen zezwalania lub odmowy. Gdy domena zostanie zablokowana przy użyciu tych możliwości, pracownicy nie mogą już wysyłać zaproszeń do osób w tej domenie.

Ułatwia to kontrolowanie dostępu do zasobów przy jednoczesnym umożliwieniu bezproblemowego korzystania z aplikacji dla zatwierdzonych użytkowników.

Ta B2B Collaboration jest dostępna dla wszystkich klientów usługi Azure Active Directory i może być używana w połączeniu z funkcjami usługi Azure AD — wersja Premium, takich jak dostęp warunkowy i ochrona tożsamości, aby uzyskać bardziej szczegółową kontrolę nad tym, kiedy i jak zewnętrzni użytkownicy biznesowi logują się i uzyskują dostęp.

Aby uzyskać więcej informacji, zobacz Zezwalanie na zaproszenia dla użytkowników [B2B](../external-identities/allow-deny-list.md)z określonych organizacji lub blokowanie ich.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD

**Wpisz:** Nowa kategoria **usługi feature: Możliwości** **produktu Aplikacje dla przedsiębiorstw:** Integracja z firmami trzecimi

W kwietniu 2018 r. dodaliśmy te 13 nowych aplikacji z obsługą federacji do naszej galerii aplikacji:

Criterion HCM, [FiscalNote](../saas-apps/fiscalnote-tutorial.md), [Secret Server (On-Premises)](../saas-apps/secretserver-on-premises-tutorial.md), [Dynamic Signal,](../saas-apps/dynamicsignal-tutorial.md) [mindWireless,](../saas-apps/mindwireless-tutorial.md) [OrgChart Now](../saas-apps/orgchartnow-tutorial.md), [Ziflow,](../saas-apps/ziflow-tutorial.md) [AppNeta monitor wydajności,](../saas-apps/appneta-tutorial.md) [Elium,](../saas-apps/elium-tutorial.md) [Fluxx Labs,](../saas-apps/fluxxlabs-tutorial.md) [Cisco Cloud,](../saas-apps/ciscocloud-tutorial.md)Shelf, [SafetyNet](../saas-apps/safetynet-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z](../saas-apps/tutorial-list.md)Azure Active Directory .

Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy [aplikacji w galerii Azure Active Directory aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Udzielanie użytkownikom B2B w usłudze Azure AD dostępu do aplikacji lokalnych (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria **usługi funkcji:** Możliwość produktu B2B: B2B/B2C 

Jako organizacja, która korzysta z funkcji współpracy B2B usługi Azure Active Directory (Azure AD) w celu zapraszania użytkowników-gości z organizacji partnerskich do usługi Azure AD, możesz teraz zapewnić tym użytkownikom B2B dostęp do aplikacji lokalnych. Te aplikacje lokalne mogą używać uwierzytelniania opartego na saml lub funkcji Integrated Windows Authentication (IWA) z ograniczonym delegowaniem Kerberos (KCD).

Aby uzyskać więcej informacji, zobacz [Grant B2B users in Azure AD access to your on-premises applications](../external-identities/hybrid-cloud-to-on-premises.md)(Udzielanie użytkownikom B2B w usłudze Azure AD dostępu do aplikacji lokalnych).

---

### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Pobierz samouczki integracji logowania jednokrotnego z Azure Marketplace

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Inne **możliwości produktu:** integracja z innymi firmami

Jeśli aplikacja wymieniona na liście w aplikacji [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) logowaniem pojedynczym  opartym na saml, kliknięcie przycisku Pobierz teraz udostępnia samouczek integracji skojarzony z tą aplikacją.

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Szybsza wydajność automatycznego aprowizowania użytkowników w usłudze Azure AD w aplikacjach SaaS

**Wpisz:** Zmieniono **kategorię usługi feature:** App Provisioning **Product capability:** 3rd Party Integration

Wcześniej klienci korzystający z łączników aprowizowania użytkowników usługi Azure Active Directory dla aplikacji SaaS (na przykład Salesforce, ServiceNow i Box) mogli odczuwać spadek wydajności, jeśli ich dzierżawy usługi Azure AD zawierały ponad 100 000 połączonych użytkowników i grup, a także używali przypisań użytkowników i grup w celu określenia, którzy użytkownicy powinni być aprowieni.

2 kwietnia 2018 r. w usłudze aprowizowania usługi Azure AD wdrożono znaczące ulepszenia wydajności, które znacznie skracają czas potrzebny do przeprowadzenia synchronizacji początkowej między Azure Active Directory a docelowymi aplikacjami SaaS.

W związku z tym wielu klientów, którzy mieli początkowe synchronizacje z aplikacjami, które trwały wiele dni lub które nigdy nie zostały ukończone, są teraz kończenie w ciągu kilku minut lub godzin.

Aby uzyskać więcej informacji, zobacz [Co się dzieje podczas aprowizowania?](../..//active-directory/app-provisioning/how-provisioning-works.md)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Samoobsługowe resetowanie hasła z Windows 10 ekranu blokady dla maszyn przyłączone do hybrydowej usługi Azure AD

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Funkcja samoobsługowego resetowania **hasła Produkt:** Uwierzytelnianie użytkowników

Zaktualizowaliśmy funkcję Windows 10 SSPR, aby uwzględniała obsługę maszyn, które są przyłączone do hybrydowej usługi Azure AD. Ta funkcja jest dostępna w Windows 10 RS4 umożliwia użytkownikom resetowanie hasła z ekranu blokady Windows 10 komputera. Użytkownicy, którzy są włączoni i zarejestrowani w celu samoobsługowego resetowania hasła, mogą korzystać z tej funkcji.

Aby uzyskać więcej informacji, zobacz [Azure AD password reset from the login screen (Resetowanie](../authentication/howto-sspr-windows.md)hasła usługi Azure AD z ekranu logowania).

---

## <a name="march-2018"></a>Marzec 2018 r.

### <a name="certificate-expire-notification"></a>Powiadomienie o wygaśnięciu certyfikatu

**Wpisz:** Stała **kategoria usługi: Możliwość** produktu Enterprise **Apps:** logowanie jednokrotne

Usługa Azure AD wysyła powiadomienie, gdy certyfikat galerii lub aplikacji spoza galerii wygaśnie.

Niektórzy użytkownicy nie osyłali powiadomień dla aplikacji dla przedsiębiorstw skonfigurowanych do logowania pojedynczego opartego na technologii SAML. Ten problem został rozwiązany. Usługa Azure AD wysyła powiadomienie o certyfikatach wygasających w ciągu 7, 30 i 60 dni. To zdarzenie można zobaczyć w dziennikach inspekcji.

Aby uzyskać więcej informacji, zobacz:

- [Manage Certificates for federated single sign-on in Azure Active Directory](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
- [Raporty dotyczące inspekcji w portalu usługi Azure Active Directory](../reports-monitoring/concept-audit-logs.md)

---

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Dostawcy tożsamości usług Twitter i GitHub w Azure AD B2C

**Wpisz:** Nowa kategoria **usługi funkcji:** B2C — funkcja produktu do zarządzania tożsamościami **konsumentów:** B2B/B2C

Teraz możesz dodać usługę Twitter lub GitHub jako dostawcę tożsamości w usłudze Azure AD B2C. Serwis Twitter jest przenoszący z publicznej wersji zapoznawczej do wersji ogólnodostępnych. Usługa GitHub jest wydana w publicznej wersji zapoznawczej.

Aby uzyskać więcej informacji, zobacz [Co to jest współpraca B2B w](../external-identities/what-is-b2b.md)usłudze Azure AD? .

---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Ograniczanie dostępu do przeglądarki przy Intune Managed Browser z dostępem warunkowym opartym na aplikacji usługi Azure AD dla systemów iOS i Android

**Wpisz:** Nowa kategoria usługi **funkcji:** Możliwości produktu dostępu **warunkowego:** Identity Security & Protection

**Teraz w publicznej wersji zapoznawczej!**

**Intune Managed Browser logowania jednokrotnego:** Pracownicy mogą używać logowania pojedynczego na klientach natywnych (takich jak Microsoft Outlook) i Intune Managed Browser dla wszystkich aplikacji połączonych z usługą Azure AD.

**Intune Managed Browser warunkowego:** Teraz możesz wymagać od pracowników używania przeglądarki zarządzanej przez usługę Intune przy użyciu zasad dostępu warunkowego opartego na aplikacji.

Przeczytaj więcej na ten temat w naszym [wpisie w blogu](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Aby uzyskać więcej informacji, zobacz:

- [Konfigurowanie dostępu warunkowego opartego na aplikacji](../conditional-access/app-based-conditional-access.md)

- [Konfigurowanie zasad programu Managed Browser](/mem/intune/apps/manage-microsoft-edge)

---

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Polecenia cmdlet serwera proxy aplikacji w module ga-ga programu PowerShell

**Wpisz:** Nowa kategoria **usługi funkcji: Możliwość** produktu serwera proxy **aplikacji:** Access Control

Obsługa poleceń cmdlet serwer proxy aplikacji jest teraz w module Ga Module programu PowerShell! Wymaga to, aby być na bieżąco z modułami programu PowerShell — jeśli staniesz się dłużej niż rok wcześniej, niektóre polecenia cmdlet mogą przestać działać.

Aby uzyskać więcej informacji, zobacz [AzureAD](/powershell/module/Azuread/).

---

### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Klienci natywni usługi Office 365 są obsługiwani przez bezproblemowe logowanie jednokrotne przy użyciu protokołu nieinterakcyjnego

**Wpisz:** Nowa kategoria **usługi funkcji:** Uwierzytelnianie (identyfikatory logowania) **Możliwość produktu:** Uwierzytelnianie użytkownika

Użytkownik korzystający z klientów natywnych usługi Office 365 (w wersji 16.0.8730.xxxx lub nowsza) może korzystać z dyskretnego logowania przy użyciu bezproblemowego logowania jednokrotnego. Ta obsługa jest zapewniana przez dodanie protokołu nieinterakcyjnego (WS-Trust) do usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Jak działa logowanie na kliencie natywnym przy użyciu bezproblemowego logowania jednokrotnego?](../hybrid/how-to-connect-sso-how-it-works.md#how-does-sign-in-on-a-native-client-with-seamless-sso-work)

---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Użytkownicy mogą uzyskać środowisko logowania dyskretnego z bezproblemowym logowaniem jednokrotnym, jeśli aplikacja wysyła żądania logowania do punktów końcowych dzierżawy usługi Azure AD

**Wpisz:** Nowa kategoria **usługi funkcji:** Uwierzytelnianie (identyfikatory logowania) **Możliwość produktu:** Uwierzytelnianie użytkownika

Użytkownicy mogą uzyskać środowisko logowania dyskretnego z bezproblemowym logowaniem jednokrotnym, jeśli aplikacja (na przykład ) wysyła żądania logowania do punktów końcowych dzierżawy usługi Azure AD — czyli lub — zamiast wspólnego punktu końcowego usługi `https://contoso.sharepoint.com` `https://login.microsoftonline.com/contoso.com/<..>` Azure AD ( `https://login.microsoftonline.com/<tenant_ID>/<..>` `https://login.microsoftonline.com/common/<...>` ).

Aby uzyskać więcej informacji, [zobacz Azure Active Directory Seamless Single Sign-On (Bezproblemowe logowanie pojedyncze).](../hybrid/how-to-connect-sso.md)

---

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Należy dodać tylko jeden adres URL usługi Azure AD, a nie dwa adresy URL wcześniej, do ustawień strefy intranetowej użytkowników, aby wdać się w bezproblemowe logowanie jednokrotne

**Wpisz:** Nowa kategoria **usługi feature:** Authentications (Logins) **Product capability:** User Authentication (Uwierzytelnianie użytkownika)

Aby można było korzystać z bezproblemowego logowania jednokrotnego dla użytkowników, należy dodać tylko jeden adres URL usługi Azure AD do ustawień strefy intranetowej użytkowników przy użyciu zasad grupy w usłudze Active Directory: `https://autologon.microsoftazuread-sso.com` . Wcześniej klienci byli zobowiązani do dodania dwóch adresów URL.

Aby uzyskać więcej informacji, [zobacz Azure Active Directory Seamless Single Sign-On (Bezproblemowe logowanie pojedynczej).](../hybrid/how-to-connect-sso.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD

**Wpisz:** Nowa kategoria **usługi feature: Możliwości** **produktu Aplikacje dla przedsiębiorstw:** Integracja z firmami trzecimi

W marcu 2018 r. dodaliśmy te 15 nowych aplikacji z obsługą federacji do naszej galerii aplikacji:

[Boxcryptor,](../saas-apps/boxcryptor-tutorial.md) [CylancePROTECT,](../saas-apps/cylanceprotect-tutorial.md)Wrike, [SignalFx,](../saas-apps/signalfx-tutorial.md)Assistant by FirstAgenda, [Domyślnie,](../saas-apps/yardione-tutorial.md)Vtiger CRM, inwink, [Amplitude,](../saas-apps/amplitude-tutorial.md) [Spacio,](../saas-apps/spacio-tutorial.md) [ContractWorks,](../saas-apps/contractworks-tutorial.md) [Bersin,](../saas-apps/bersin-tutorial.md) [Mercell,](../saas-apps/mercell-tutorial.md) [Trisotech Digital Enterprise Server,](../saas-apps/trisotechdigitalenterpriseserver-tutorial.md) [Qumu Cloud.](../saas-apps/qumucloud-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z](../saas-apps/tutorial-list.md)Azure Active Directory .

Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy [aplikacji w galerii Azure Active Directory aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="pim-for-azure-resources-is-generally-available"></a>Usługa PIM dla zasobów platformy Azure jest ogólnie dostępna

**Wpisz:** Nowa kategoria **usługi feature:** Privileged Identity Management **Możliwości produktu:** Privileged Identity Management

Jeśli używasz usługi Azure AD Privileged Identity Management dla ról katalogu, możesz teraz używać ograniczonych przez czas funkcji dostępu i przypisywania usługi PIM dla ról zasobów platformy Azure, takich jak subskrypcje, grupy zasobów, Virtual Machines i inne zasoby obsługiwane przez usługę Azure Resource Manager. Wymuszanie uwierzytelniania wieloskładnikowego podczas aktywowania ról just in time i planowanie aktywacji w koordynacji z zatwierdzonymi oknami zmian. Ponadto w tej wersji dodano ulepszenia niedostępne w publicznej wersji zapoznawczej, w tym zaktualizowany interfejs użytkownika, przepływy pracy zatwierdzania oraz możliwość rozszerzania wkrótce wygasających ról i odnawiania wygasłych ról.

Aby uzyskać więcej informacji, zobacz [PIM for Azure resources (Preview) (Usługa PIM dla zasobów platformy Azure (wersja zapoznawcza)](../privileged-identity-management/azure-pim-resource-rbac.md)

---

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Dodawanie opcjonalnych oświadczeń do tokenów aplikacji (publiczna wersja zapoznawcza)

**Wpisz:** Nowa kategoria **usługi funkcji:** Uwierzytelnianie (identyfikatory logowania) **Możliwość produktu:** Uwierzytelnianie użytkownika

Aplikacja usługi Azure AD może teraz żądać oświadczeń niestandardowych lub opcjonalnych w tokenach JWTs lub SAML.  Są to oświadczenia dotyczące użytkownika lub dzierżawy, które nie są domyślnie uwzględniane w tokenie ze względu na ograniczenia dotyczące rozmiaru lub stosowania.  Ta funkcja jest obecnie dostępna w publicznej wersji zapoznawczej dla aplikacji usługi Azure AD w punktach końcowych w wersji 1.0 i 2.0.  Zapoznaj się z dokumentacją, aby uzyskać informacje na temat oświadczeń, które można dodać, oraz sposobu edytowania manifestu aplikacji w celu żądania ich.

Aby uzyskać więcej informacji, zobacz [Opcjonalne oświadczenia w usłudze Azure AD.](../develop/active-directory-optional-claims.md)

---

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Usługa Azure AD obsługuje pkce w celu zapewnienia bezpieczniejszych przepływów OAuth

**Wpisz:** Nowa kategoria **usługi funkcji:** Uwierzytelnianie (identyfikatory logowania) **Możliwość produktu:** Uwierzytelnianie użytkownika

Dokumenty usługi Azure AD zostały zaktualizowane w celu zanotowania obsługi protokołu PKCE, co umożliwia bezpieczniejszą komunikację podczas przepływu udzielania kodu autoryzacji OAuth 2.0.  Zarówno S256, jak i code_challenges są obsługiwane w punktach końcowych w wersji 1.0 i 2.0.

Aby uzyskać więcej informacji, zobacz [Request an authorization code (Żądanie kodu autoryzacji).](../develop/v2-oauth2-auth-code-flow.md#request-an-authorization-code)

---

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Obsługa aprowizowania wszystkich wartości atrybutów użytkownika dostępnych w interfejsie API Get_Workers Workday

**Wpisz:** Nowa kategoria **usługi feature:** App Provisioning **Product capability:** 3rd Party Integration

Publiczna wersja zapoznawcza aprowizowania ruchu przychodzącego z produktu Workday do usług Active Directory i Azure AD obsługuje teraz wyodrębnianie i aprowizowanie wszystkich wartości atrybutów dostępnych w interfejsie API usługi Workday Get_Workers API. Powoduje to dodanie obsługi setek dodatkowych atrybutów standardowych i niestandardowych wykraczających poza te, które są dostarczane z początkową wersją łącznika inicjowania obsługi ruchu przychodzącego produktu Workday.

Aby uzyskać więcej informacji, zobacz: Dostosowywanie listy atrybutów użytkownika [produktu Workday](../saas-apps/workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Zmiana członkostwa w grupie z dynamicznej na statyczną i odwrotnie

**Wpisz:** Nowa kategoria usługi **funkcji:** Możliwość produktu **do zarządzania grupą:** Współpraca

Istnieje możliwość zmiany sposobu zarządzania członkostwem w grupie. Jest to przydatne, gdy chcesz zachować tę samą nazwę i identyfikator grupy w systemie, więc wszystkie istniejące odwołania do grupy są nadal prawidłowe. Utworzenie nowej grupy wymaga zaktualizowania tych odwołań.
Zaktualizowaliśmy centrum administracyjne usługi Azure AD w celu obsługi tej funkcji. Teraz klienci mogą konwertować istniejące grupy z członkostwa dynamicznego na przypisane członkostwo i na odwrót. Istniejące polecenia cmdlet programu PowerShell również są nadal dostępne.

Aby uzyskać więcej informacji, zobacz [Reguły członkostwa dynamicznego dla grup w Azure Active Directory](../enterprise-users/groups-dynamic-membership.md)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Ulepszone zachowanie wylogowywu przy użyciu bezproblemowego logowania jednokrotnego

**Wpisz:** Zmieniono **kategorię usługi feature:** Authentications (Logins) **Product capability:** User Authentication (Uwierzytelnianie użytkownika)

Wcześniej, nawet jeśli użytkownicy jawnie wy zalogowali się z aplikacji zabezpieczonej przez usługę Azure AD, zostaliby automatycznie zalogowani ponownie przy użyciu bezproblemowego logowania jednokrotnego, gdyby próbowali ponownie uzyskać dostęp do aplikacji usługi Azure AD w sieci firmowej z urządzeń przyłączonych do domeny. Dzięki tej zmianie jest obsługiwane wylogowanie.  Dzięki temu użytkownicy mogą wybrać to samo lub inne konto usługi Azure AD, za pomocą których będą logować się ponownie, zamiast automatycznie logować się przy użyciu bezproblemowego logowania jednokrotnego.

Aby uzyskać więcej informacji, [zobacz Azure Active Directory Seamless Single Sign-On (Bezproblemowe logowanie pojedynczej)](../hybrid/how-to-connect-sso.md)

---

### <a name="application-proxy-connector-version-154020-released"></a>serwer proxy aplikacji Connector w wersji 1.5.402.0

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Możliwość produktu serwera proxy **aplikacji:** Identity Security & Protection

Ta wersja łącznika jest stopniowo wprowadzana do listopada. Ta nowa wersja łącznika obejmuje następujące zmiany:

- Łącznik ustawia teraz pliki cookie na poziomie domeny zamiast poziomu poddomeny. Zapewnia to płynniejsze logowanie jednokrotne i pozwala uniknąć nadmiarowych monitów o uwierzytelnienie.
- Obsługa fragmentowanych żądań kodowania
- Ulepszone monitorowanie kondycji łącznika
- Kilka poprawek błędów i ulepszeń stabilności

Aby uzyskać więcej informacji, zobacz Understand Azure AD serwer proxy aplikacji connectors (Informacje na temat [łączników usługi Azure AD serwer proxy aplikacji).](../manage-apps/application-proxy-connectors.md)

---

## <a name="february-2018"></a>Luty 2018 r.

### <a name="improved-navigation-for-managing-users-and-groups"></a>Ulepszona nawigacja do zarządzania użytkownikami i grupami

**Wpisz:** Planowanie zmiany **Kategoria usługi:** Możliwość produktu do zarządzania **katalogami:** Katalog

Usprawniono środowisko nawigacji do zarządzania użytkownikami i grupami. Teraz możesz przejść z przeglądu katalogu bezpośrednio do listy wszystkich użytkowników, co zapewnia łatwiejszy dostęp do listy usuniętych użytkowników. Możesz również przejść z przeglądu katalogu bezpośrednio do listy wszystkich grup, aby mieć łatwiejszy dostęp do ustawień zarządzania grupami. Na stronie przeglądu katalogu można również wyszukać użytkownika, grupę, aplikację dla przedsiębiorstw lub rejestrację aplikacji.

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Dostępność raportów logowania i inspekcji w usługach Microsoft Azure obsługiwanych przez firmę 21Vianet (Azure (Chiny) — 21Vianet)

**Wpisz:** Nowa kategoria **usługi funkcji: Azure Stack** **Produkt: Monitorowanie** & raportowania

Raporty dziennika aktywności usługi Azure AD są teraz dostępne w Microsoft Azure obsługiwanych przez wystąpienia 21Vianet (Azure (Chiny) — 21Vianet). Uwzględniane są następujące dzienniki:

- **Dzienniki aktywności logowania —**  obejmuje wszystkie dzienniki logowania skojarzone z dzierżawą.

- **Dzienniki inspekcji haseł samoobsługi** — obejmuje wszystkie dzienniki inspekcji samoobsługowego resetowania hasła.

- **Dzienniki inspekcji zarządzania katalogami** — obejmuje wszystkie dzienniki inspekcji związane z zarządzaniem katalogami, takie jak zarządzanie użytkownikami, zarządzanie aplikacją i inne.

Za pomocą tych dzienników można uzyskać wgląd w sposób działania środowiska. Na podstawie udostępnionych danych można:

- Określ sposób korzystania z aplikacji i usług przez użytkowników.

- Rozwiązywanie problemów uniemożliwiających użytkownikom pracę.

Aby uzyskać więcej informacji na temat korzystania z tych raportów, zobacz [Azure Active Directory raporty.](../reports-monitoring/overview-reports.md)

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Używanie roli "Czytelnik raportów" (bez roli administratora) do wyświetlania raportów aktywności usługi Azure AD

**Wpisz:** Nowa kategoria usługi **feature:** Reporting **Product capability:** Monitoring & Reporting

W ramach opinii klientów w celu umożliwienia użytkownikom niebędącym administratorami dostępu do dzienników aktywności usługi Azure AD włączyliśmy możliwość uzyskiwania dostępu do dzienników aktywności w usłudze Azure Portal przez użytkowników pełniący rolę "Czytelnik raportów", a także do korzystania z interfejsu API Microsoft Graph.

Aby uzyskać więcej informacji na temat korzystania z tych raportów, zobacz [Azure Active Directory raportowania](../reports-monitoring/overview-reports.md).

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Oświadczenie EmployeeID dostępne jako atrybut użytkownika i identyfikator użytkownika

**Wpisz:** Nowa kategoria usługi **funkcji: Możliwość** produktu **Enterprise Apps:** logowanie jednokrotne

Możesz skonfigurować element **EmployeeID** jako identyfikator użytkownika i atrybut użytkownika dla użytkowników członkowskich i gości B2B w aplikacjach logowania opartych na saml z interfejsu użytkownika aplikacji przedsiębiorstwa.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML](../develop/active-directory-saml-claims-customization.md)dla aplikacji dla przedsiębiorstw w Azure Active Directory .

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Uproszczone zarządzanie aplikacją przy użyciu symboli wieloznacznych w usłudze Azure AD serwer proxy aplikacji

**Wpisz:** Nowa kategoria usługi **funkcji: Możliwość** produktu serwera proxy **aplikacji: Uwierzytelnianie** użytkownika

Aby ułatwić wdrażanie aplikacji i zmniejszyć koszty administracyjne, teraz obsługujemy możliwość publikowania aplikacji przy użyciu symboli wieloznacznych. Aby opublikować aplikację z symbolami wieloznacznymi, możesz skorzystać ze standardowego przepływu publikowania aplikacji, ale użyć symbolu wieloznacznego w wewnętrznych i zewnętrznych adresach URL.

Aby uzyskać więcej informacji, zobacz [Aplikacje z symbolami wieloznacznymi na serwerze Azure Active Directory proxy aplikacji](../manage-apps/application-proxy-wildcard.md)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nowe polecenia cmdlet do obsługi konfiguracji serwer proxy aplikacji

**Wpisz:** Nowa kategoria usługi **funkcji: Możliwość** produktu serwera proxy **aplikacji:** Platforma

Najnowsza wersja modułu AzureAD PowerShell (wersja zapoznawcza) zawiera nowe polecenia cmdlet, które umożliwiają klientom konfigurowanie aplikacji serwer proxy aplikacji przy użyciu programu PowerShell.

Nowe polecenia cmdlet to:

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

**Wpisz:** Nowa kategoria **usługi funkcji: Możliwość** produktu serwera proxy **aplikacji:** Platforma

Najnowsza wersja modułu AzureAD PowerShell zawiera polecenia cmdlet do zarządzania grupami w usłudze Azure AD. Te polecenia cmdlet były wcześniej dostępne w module AzureADPreview i teraz zostały dodane do modułu AzureAD

Polecenia cmdlet grupy, które są teraz dostępne ogólnie, to:

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

**Wpisz:** Nowa kategoria **usługi funkcji:** AD Sync **możliwości produktu:** Platforma

Azure AD Connect jest preferowanym narzędziem do synchronizowania danych między usługą Azure AD i lokalnymi źródłami danych, w tym Windows Server Active Directory i LDAP.

>[!Important]
>Ta kompilacja wprowadza zmiany w regułach schematu i synchronizacji. Usługa Azure AD Connect Synchronization Service wyzwala kroki pełnego importowania i pełnej synchronizacji po uaktualnieniu. Aby uzyskać informacje na temat zmiany tego zachowania, zobacz [Jak odroczyć pełną synchronizację po uaktualnieniu.](../hybrid/how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade)

Ta wersja ma następujące aktualizacje i zmiany:

**Rozwiązano problemy**

- Naprawiono okno chronometrażu dla zadań w tle dla strony filtrowania partycji podczas przełączania do następnej strony.

- Usunięto usterkę, która powodowała naruszenie zasad dostępu podczas akcji niestandardowej ConfigDB.

- Usunięto usterkę w celu odzyskania danych po przechyłce czasu połączenia SQL.

- Usunięto usterkę, która w przypadku certyfikatów z symbolami wieloznacznymi sieci SAN nie powiodła się przed sprawdzeniem.

- Usunięto usterkę, która miiserver.exe podczas eksportowania łącznika usługi Azure AD.

- Usunięto usterkę, która powodowała, że próba zalogowania się z błędem hasła na kontrolerze domeny podczas uruchamiania powodowała zmianę konfiguracji przez kreatora programu Azure AD Connect

**Nowe funkcje i ulepszenia**

- Telemetria aplikacji — administratorzy mogą włączać/wyłączać tę klasę danych.

- Dane usługi Azure AD Health — administratorzy muszą odwiedzić portal kondycji, aby kontrolować swoje ustawienia kondycji. Po zmianie zasad usługi agenci będą je odczytywać i wymuszać.

- Dodano akcje konfiguracji zapisu zwrotnego urządzeń i pasek postępu dla inicjowania strony.

- Ulepszono ogólną diagnostykę za pomocą raportu HTML i pełnego zbierania danych w ZIP-Text /HTML.

- Poprawiono niezawodność automatycznego uaktualniania i dodano dodatkowe dane telemetryczne w celu zapewnienia, że można określić kondycję serwera.

- Ogranicz uprawnienia dostępne dla uprzywilejowanych kont na koncie łącznika usługi AD. W przypadku nowych instalacji kreator ogranicza uprawnienia, które uprzywilejowane konta mają na koncie MSOL po utworzeniu konta MSOL. Zmiany mają wpływ na instalacje ekspresowe i instalacje niestandardowe przy użyciu konta automatycznego tworzenia.

- Zmieniono instalator tak, aby nie wymagał uprawnień administratora systemu podczas czystej instalacji programu AADConnect.

- Nowe narzędzie do rozwiązywania problemów z synchronizacją określonego obiektu. Obecnie narzędzie sprawdza następujące elementy:

    - Niezgodność userPrincipalName między zsynchronizowanym obiektem użytkownika a kontem użytkownika w dzierżawie usługi Azure AD.

    - Jeśli obiekt jest filtrowany z synchronizacji z powodu filtrowania domeny

    - Jeśli obiekt jest filtrowany z synchronizacji z powodu filtrowania jednostek organizacyjnych

- Nowe narzędzie do synchronizowania bieżącego skrótu hasła przechowywanego w lokalna usługa Active Directory dla określonego konta użytkownika. Narzędzie nie wymaga zmiany hasła.

---

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Aplikacje obsługujące Intune App Protection dodane do użytku z dostępem warunkowym opartym na aplikacji usługi Azure AD

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Możliwości produktu dostępu **warunkowego:** Identity Security & Protection

Dodaliśmy więcej aplikacji, które obsługują dostęp warunkowy oparty na aplikacji. Teraz możesz uzyskać dostęp do usługi Office 365 i innych aplikacji w chmurze połączonych z usługą Azure AD przy użyciu tych zatwierdzonych aplikacji klienckich.

Do końca lutego zostaną dodane następujące aplikacje:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Aby uzyskać więcej informacji, zobacz:

- [Wymaganie zatwierdzonej aplikacji klienckiej](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Warunki użytkowania aktualizacji do obsługi urządzeń przenośnych

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Warunki użytkowania **Produkt:** Zgodność

Po wyświetleniu warunków użytkowania możesz kliknąć pozycję Masz problemy z **wyświetlaniem? Kliknij tutaj**. Kliknięcie tego linku powoduje natywne otwarcie warunków użytkowania na urządzeniu. Niezależnie od rozmiaru czcionki w dokumencie lub rozmiaru ekranu urządzenia można powiększać i odczytywać dokument zgodnie z potrzebami.

---

## <a name="january-2018"></a>Styczeń 2018 r.

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe aplikacje federowane dostępne w galerii aplikacji usługi Azure AD

**Wpisz:** Nowa kategoria **usługi funkcji: Możliwość** produktu Enterprise **Apps:** integracja z firmami 3

W styczniu 2018 r. w galerii aplikacji dodano następujące nowe aplikacje z obsługą federacji:

[IBM OpenPages,](../saas-apps/ibmopenpages-tutorial.md) [OneTrust Privacy Management Software,](../saas-apps/onetrust-tutorial.md) [Dealpath,](../saas-apps/dealpath-tutorial.md)[IdioRisk Federated Directory i [Fidelity NetBenefits.](../saas-apps/fidelitynetbenefits-tutorial.md)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z](../saas-apps/tutorial-list.md)Azure Active Directory .

Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy aplikacji w [galerii Azure Active Directory aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="sign-in-with-additional-risk-detected"></a>Logowanie z wykrytym dodatkowym ryzykiem

**Wpisz:** Nowa kategoria **usługi funkcji:** Identity Protection **Product capability:** Identity Security & Protection

Szczegółowe informacje o wykrytym wykryciu ryzyka są powiązane z subskrypcją usługi Azure AD. W przypadku Azure AD — wersja Premium P2 można uzyskać najbardziej szczegółowe informacje o wszystkich podstawowych wykryciach.

W przypadku Azure AD — wersja Premium P1 wykrycia, które nie są objęte licencją, są wyświetlane jako logowanie wykrywania ryzyka z wykrytym dodatkowym ryzykiem.

Aby uzyskać więcej informacji, [zobacz Azure Active Directory wykrywanie ryzyka.](../identity-protection/overview-identity-protection.md)

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Ukrywanie aplikacji usługi Office 365 przed panelami dostępu użytkowników końcowych

**Wpisz:** Nowa kategoria **usługi funkcji: Moje aplikacje** **produkt:** logowanie jednokrotne

Teraz możesz lepiej zarządzać wyświetlaniem aplikacji usługi Office 365 w panelach dostępu użytkownika za pomocą nowego ustawienia użytkownika. Ta opcja jest przydatna do zmniejszania liczby aplikacji w panelach dostępu użytkownika, jeśli wolisz wyświetlać tylko aplikacje pakietu Office w portalu pakietu Office. To ustawienie znajduje  się w ustawieniach użytkownika i ma etykietę Użytkownicy mogą zobaczyć tylko aplikacje **usługi Office 365 w portalu usługi Office 365.**

Aby uzyskać więcej informacji, zobacz [Ukrywanie aplikacji w interfejsie użytkownika](../manage-apps/hide-application-from-user-portal.md)w Azure Active Directory .

---

### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Bezproblemowe logowanie do aplikacji z włączoną obsługą logowania jednokrotnego haseł bezpośrednio z adresu URL aplikacji

**Wpisz:** Nowa kategoria **usługi funkcji:** Moje aplikacje **produktu:** logowanie jednokrotne

Rozszerzenie Moje aplikacje jest teraz dostępne za pośrednictwem wygodnego narzędzia, które Moje aplikacje możliwość logowania Moje aplikacje jako skrót w przeglądarce. Po zainstalowaniu użytkownik zobaczy w przeglądarce ikonę gofra, która zapewnia szybki dostęp do aplikacji. Użytkownicy mogą teraz korzystać z:

- Możliwość bezpośredniego logowania się do aplikacji opartych na logować się przy użyciu hasła ze strony logowania aplikacji
- Uruchamianie dowolnej aplikacji przy użyciu funkcji szybkiego wyszukiwania
- Skróty do ostatnio używanych aplikacji z rozszerzenia
- Rozszerzenie jest dostępne dla Microsoft Edge, Chrome i Firefox.

Aby uzyskać więcej informacji, [zobacz Moje aplikacje Secure Sign-in Extension](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)(Rozszerzenie bezpiecznego logowania).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Środowisko administracyjne usługi Azure AD w klasycznym portalu Azure zostało wycofane

**Wpisz:** Przestarzała **kategoria usługi:** Możliwości produktu Azure **AD:** Katalog

Od 8 stycznia 2018 r. środowisko administracyjne usługi Azure AD w klasycznym portalu Azure zostało wycofane. Miało to miejsce w połączeniu z wycofaniem samej klasycznej witryny Azure Portal. W przyszłości należy używać centrum administracyjnego [usługi Azure AD](https://aad.portal.azure.com) do całej administracji opartej na portalu usługi Azure AD.

---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Portal internetowy PhoneFactor został wycofany

**Wpisz:** Przestarzała **kategoria usługi:** Możliwości produktu Azure **AD:** Katalog

Od 8 stycznia 2018 r. portal internetowy PhoneFactor został wycofany. Ten portal był używany do administrowania serwerem MFA, ale te funkcje zostały przeniesione do Azure Portal na portal.azure.com.

Konfiguracja usługi MFA znajduje się w: **Azure Active Directory \> MFA Server**

---

### <a name="deprecate-azure-ad-reports"></a>Wycofaj raporty usługi Azure AD

**Wpisz:** Przestarzała **kategoria usługi:** **Raportowanie możliwości produktu:** Zarządzanie cyklem życia tożsamości


Po ogólnym dostępności nowej konsoli administracyjnej usługi Azure Active Directory i nowych interfejsów API, które są teraz dostępne dla raportów dotyczących działań i zabezpieczeń, interfejsy API raportów w obszarze punktu końcowego "/reports" zostały wycofane z końcem 31 grudnia 2017 r.

**Co jest dostępne?**

W ramach przejścia do nowej konsoli administracyjnej zostały udostępnione 2 nowe interfejsy API do pobierania dzienników aktywności usługi Azure AD. Nowy zestaw interfejsów API zapewnia bardziej rozbudowane funkcje filtrowania i sortowania, a także zapewnia bardziej rozbudowane działania inspekcji i logowania. Dane wcześniej dostępne za pośrednictwem raportów zabezpieczeń są teraz dostępne za pośrednictwem interfejsu API wykrywania ryzyka usługi Identity Protection w Microsoft Graph.

Aby uzyskać więcej informacji, zobacz:

- [Wprowadzenie do interfejsu API Azure Active Directory raportowania](../reports-monitoring/concept-reporting-api.md)

- [Wprowadzenie do Azure Active Directory Identity Protection i Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md)

---

## <a name="december-2017"></a>Grudzień 2017

### <a name="terms-of-use-in-the-access-panel"></a>Warunki użytkowania w Panel dostępu

**Wpisz:** Nowa kategoria **usługi funkcji: Warunki użytkowania** **Produkt:** Zgodność

Teraz możesz przejść do strony Panel dostępu i wyświetlić wcześniej zaakceptowane warunki użytkowania.

Wykonaj następujące kroki:

1. Przejdź do [portalu MyApps i](https://myapps.microsoft.com)zaloguj się.

2. W prawym górnym rogu wybierz swoją nazwę, a następnie wybierz **pozycję Profil** z listy.

3. W swoim **profilu wybierz** pozycję Przejrzyj **warunki użytkowania.**

4. Teraz możesz przejrzeć zaakceptowane warunki użytkowania.

Aby uzyskać więcej informacji, zobacz temat [Funkcja warunków użytkowania usługi Azure AD (wersja zapoznawcza).](../conditional-access/terms-of-use.md)

---

### <a name="new-azure-ad-sign-in-experience"></a>Nowe środowisko logowania w usłudze Azure AD

**Wpisz:** Nowa kategoria **usługi feature:** Możliwość produktu Azure **AD: Uwierzytelnianie** użytkowników

Interfejsy użytkownika usług Azure AD i konto Microsoft tożsamości zostały przeprojektowane tak, aby miały spójny wygląd i sposób pracy. Ponadto strona logowania usługi Azure AD najpierw zbiera nazwę użytkownika, a następnie poświadczenia na drugim ekranie.

Aby uzyskać więcej informacji, zobacz [The new Azure AD sign-in experience is now in public preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)(Nowe środowisko logowania usługi Azure AD jest teraz dostępne w publicznej wersji zapoznawczej).

---

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Mniej monitów logowania: nowe środowisko "nie wyloguj mnie" do logowania w usłudze Azure AD

**Wpisz:** Nowa kategoria usługi **feature:** Możliwości produktu Azure **AD: Uwierzytelnianie** użytkowników

Pole **wyboru Nie wyloguj** mnie na stronie logowania usługi Azure AD zostało zastąpione nowym monitem, który jest wyświetlany po pomyślnym uwierzytelnieniu.

Jeśli odpowiesz **tak** na ten monit, usługa wyświetli token odświeżania trwałego. To zachowanie jest takie samo jak w przypadku wyboru pola wyboru **Nie wyewidencjojuj** mnie w starym. W przypadku dzierżaw federowanych ten monit jest wyświetlany po pomyślnym uwierzytelnieniu w usłudze federacji.

Aby uzyskać więcej informacji, zobacz [Fewer sign-in prompts: The new "keep me signed in" experience for Azure AD is in preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/)(Mniej monitów o logowanie: nowe środowisko "nie wyloguj mnie" dla usługi Azure AD jest w wersji zapoznawczej).

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Dodaj konfigurację, aby wymagać rozszerzenia warunków użytkowania przed zaakceptowaniem

**Wpisz:** Nowa kategoria **usługi funkcji:** Warunki użytkowania **Produkt: Zgodność**

Opcja dla administratorów wymaga od użytkowników rozszerzenia warunków użytkowania przed zaakceptowaniem warunków.

Wybierz pozycję **Wł.** lub **Wył.,** aby wymagać od użytkowników rozwinięcia warunków użytkowania. Ustawienie **Wł.** wymaga od użytkowników wyświetlenia warunków użytkowania przed ich zaakceptowaniem.

Aby uzyskać więcej informacji, zobacz funkcja [warunków użytkowania usługi Azure AD (wersja zapoznawcza).](../conditional-access/terms-of-use.md)

---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Aktywacja w zakresie dla kwalifikujących się przypisań ról

**Wpisz:** Nowa kategoria **usługi feature:** Privileged Identity Management **Możliwości produktu:** Privileged Identity Management

Aktywacji w zakresie można użyć do aktywowania kwalifikujących się przypisań ról zasobów platformy Azure z mniejszą autonomią niż oryginalne ustawienia domyślne przypisania. Przykładem może być przypisanie Cię jako właściciela subskrypcji w dzierżawie. W przypadku aktywacji w zakresie można aktywować rolę właściciela dla maksymalnie pięciu zasobów zawartych w ramach subskrypcji (takich jak grupy zasobów i maszyny wirtualne). Określanie zakresu aktywacji może zmniejszyć możliwość wprowadzenia niepożądanych zmian w krytycznych zasobach platformy Azure.

Aby uzyskać więcej informacji, [zobacz Co to jest Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md).

---

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nowe aplikacje federowane w galerii aplikacji usługi Azure AD

**Wpisz:** Nowa kategoria **usługi funkcji: Aplikacje** dla przedsiębiorstw **Możliwość produktu:** Integracja z firmami trzecimi

W grudniu 2017 r. dodaliśmy te nowe aplikacje z obsługą federacji do galerii aplikacji:

[Accredible,](../saas-apps/accredible-tutorial.md)Adobe Experience Manager, [EFI Digital StoreFront,](../saas-apps/efidigitalstorefront-tutorial.md) [Communifire](../saas-apps/communifire-tutorial.md) CybSafe, [FactSet,](../saas-apps/factset-tutorial.md) [IMAGE WORKS,](../saas-apps/imageworks-tutorial.md) [MOBI,](../saas-apps/mobi-tutorial.md) [MobileIron Azure AD integration](../saas-apps/mobileiron-tutorial.md), [Reflektive,](../saas-apps/reflektive-tutorial.md) [SAML SSO for Bamboo by resolution GmbH,](../saas-apps/bamboo-tutorial.md) [SAML SSO for Bitbucket by resolution GmbH,](../saas-apps/bitbucket-tutorial.md) [Vodeclic,](../saas-apps/vodeclic-tutorial.md)WebHR, Zenegy Azure AD Integration.

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z](../saas-apps/tutorial-list.md)Azure Active Directory .

Aby uzyskać więcej informacji na temat listy aplikacji w galerii aplikacji usługi Azure AD, zobacz Wyświetlanie listy aplikacji w galerii Azure Active Directory [aplikacji.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Przepływy pracy zatwierdzania dla ról katalogu usługi Azure AD

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Privileged Identity Management **Produkt:** Privileged Identity Management

Przepływ pracy zatwierdzania ról katalogu usługi Azure AD jest ogólnie dostępny.

Dzięki przepływowi pracy zatwierdzania administratorzy ról uprzywilejowanych mogą wymagać od członków kwalifikujących się ról żądania aktywacji roli przed użyciem roli uprzywilejowanej. Wielu użytkowników i grup może delegować obowiązki zatwierdzania. Uprawnieni członkowie roli otrzymują powiadomienia po zakończeniu zatwierdzania, gdy ich rola jest aktywna.

---

### <a name="pass-through-authentication-skype-for-business-support"></a>Uwierzytelnianie pass-through: pomoc techniczna programu Skype dla firm

**Wpisz:** Zmieniono **kategorię usługi feature:** Authentications (Logins) **Product capability:** User authentication (Uwierzytelnianie użytkowników)

Uwierzytelnianie pass-through obsługuje teraz logowania użytkowników do aplikacji klienckich Skype dla firm, które obsługują nowoczesne uwierzytelnianie, w tym topologie online i hybrydowe.

Aby uzyskać więcej informacji, zobacz [Topologie programu Skype dla firm obsługiwane przy użyciu nowoczesnego uwierzytelniania.](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)

---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Aktualizacje kontroli Azure AD Privileged Identity Management kontroli RBAC platformy Azure (wersja zapoznawcza)

**Wpisz:** Zmieniono **kategorię usługi feature:** Privileged Identity Management **Możliwości produktu:** Privileged Identity Management

Dzięki odświeżeniu publicznej wersji zapoznawczej usługi Azure AD Privileged Identity Management (PIM) dla kontroli dostępu na podstawie ról (RBAC) platformy Azure możesz teraz:

* Użyj funkcji Just Enough Administration.
* Wymaganie zatwierdzenia w celu aktywowania ról zasobów.
* Zaplanuj przyszłą aktywację roli, która wymaga zatwierdzenia ról zarówno usługi Azure AD, jak i platformy Azure.

Aby uzyskać więcej informacji, zobacz [Privileged Identity Management zasobów platformy Azure (wersja zapoznawcza).](../privileged-identity-management/azure-pim-resource-rbac.md)

---

## <a name="november-2017"></a>Listopad 2017

### <a name="access-control-service-retirement"></a>Access Control wycofanie usługi

**Wpisz:** Planowanie zmian w **kategorii Usługa:** Access Control usługi **Możliwości produktu:** Access Control service

Azure Active Directory Access Control (znana także jako usługa Access Control) zostanie wycofana pod koniec 2018 r. Więcej informacji, które zawierają szczegółowy harmonogram i wskazówki dotyczące migracji wysokiego poziomu, zostanie podanych w ciągu następnych kilku tygodni. Na tej stronie możesz pozostawić komentarze z pytaniami Access Control usługi, a członek zespołu odpowie na nie.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Ograniczanie dostępu przeglądarki do Intune Managed Browser

**Wpisz:** Planowanie zmiany kategorii **usługi: Możliwości** produktu dostępu **warunkowego:** Zabezpieczenia i ochrona tożsamości

Możesz ograniczyć dostęp przeglądarki do usługi Office 365 i innych aplikacji w chmurze połączonych z usługą Azure AD, używając Intune Managed Browser jako zatwierdzonej aplikacji.

Teraz można skonfigurować następujący warunek dla dostępu warunkowego opartego na aplikacji:

**Aplikacje klienckie:** Przeglądarka

**Jaki jest efekt zmiany?**

Obecnie dostęp jest blokowany w przypadku użycia tego warunku. Gdy wersja zapoznawcza jest dostępna, cały dostęp będzie wymagał użycia aplikacji Managed Browser.

Poszukaj tej możliwości i dodatkowych informacji w kolejnych blogach i informacjach o wersji.

Aby uzyskać więcej informacji, zobacz [Dostęp warunkowy w usłudze Azure AD.](../conditional-access/overview.md)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie dla dostępu warunkowego opartego na aplikacji usługi Azure AD

**Wpisz:** Planowanie zmiany Kategoria **usługi: Możliwość** produktu dostępu **warunkowego: Zabezpieczenia** i ochrona tożsamości

Na liście zatwierdzonych aplikacji klienckich znajdują się [następujące aplikacje:](../conditional-access/concept-conditional-access-conditions.md#client-apps)

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Aby uzyskać więcej informacji, zobacz:

- [Wymagania dotyczące zatwierdzonej aplikacji klienckiej](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Obsługa warunków użytkowania dla wielu języków

**Wpisz:** Nowa kategoria **usługi funkcji: Warunki użytkowania** **Produkt:** Zgodność

Administratorzy mogą teraz tworzyć nowe warunki użytkowania, które zawierają wiele dokumentów PDF. Te dokumenty PDF można otagować za pomocą odpowiedniego języka. Użytkownicy są pokazywani w formacie PDF z pasującymi językami na podstawie ich preferencji. Jeśli nie ma dopasowania, zostanie pokazany język domyślny.

---

### <a name="real-time-password-writeback-client-status"></a>Stan klienta zapisu zwrotnego haseł w czasie rzeczywistym

**Wpisz:** Nowa kategoria **usługi funkcji:** Samoobsługowe resetowanie hasła **Możliwość produktu:** Uwierzytelnianie użytkowników

Teraz możesz przejrzeć stan lokalnego klienta zapisu zwrotnego haseł. Ta opcja jest dostępna w **sekcji Integracja lokalna** na [stronie Resetowanie](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) hasła.

Jeśli występują problemy z połączeniem z lokalnym klientem zapisu zwrotnego, zostanie wyświetlony komunikat o błędzie z:

- Informacje o tym, dlaczego nie można nawiązać połączenia z lokalnym klientem zapisu zwrotnego.
- Link do dokumentacji, która pomaga w rozwiązaniu problemu.

Aby uzyskać więcej informacji, zobacz [integracja lokalna](../authentication/concept-sspr-howitworks.md#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Dostęp warunkowy oparty na aplikacji usługi Azure AD

**Wpisz:** Nowa kategoria usługi **feature:** Możliwości produktu Azure **AD: Zabezpieczenia** i ochrona tożsamości

Teraz można ograniczyć dostęp do usługi Office 365 i [](../conditional-access/concept-conditional-access-conditions.md#client-apps) innych aplikacji w chmurze połączonych z usługą Azure AD do zatwierdzonych aplikacji klienckich, które obsługują zasady ochrony aplikacji usługi Intune, przy użyciu dostępu warunkowego opartego na aplikacji [usługi Azure AD.](../conditional-access/app-based-conditional-access.md) Zasady ochrony aplikacji usługi Intune służą do konfigurowania i ochrony danych firmy w tych aplikacjach klienckich.

Połączenie [opartego na aplikacji z](../conditional-access/app-based-conditional-access.md) [zasadami dostępu](../conditional-access/require-managed-devices.md) warunkowego opartego na urządzeniach zapewnia elastyczność ochrony danych dla urządzeń osobistych i firmowych.

Następujące warunki i kontrolki są teraz dostępne do użycia z dostępem warunkowym opartym na aplikacji:

**Obsługiwany warunek platformy**

- iOS
- Android

**Warunek aplikacji klienckich**

- Aplikacje mobilne i klienci desktopowi

**Kontrola dostępu**

- Wymaganie zatwierdzonej aplikacji klienckiej

Aby uzyskać więcej informacji, zobacz Dostęp warunkowy oparty na aplikacji [usługi Azure AD.](../conditional-access/app-based-conditional-access.md)

---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Zarządzanie urządzeniami usługi Azure AD w Azure Portal

**Wpisz:** Nowa kategoria usługi **funkcji: Rejestracja** urządzeń i zarządzanie **możliwością produktu: Zabezpieczenia** i ochrona tożsamości

W jednym miejscu można teraz znaleźć wszystkie urządzenia połączone z usługą Azure AD i działania związane z urządzeniami. Dostępne jest nowe środowisko administracyjne do zarządzania wszystkimi tożsamościami i ustawieniami urządzeń w Azure Portal. W tej wersji można:

- Wyświetl wszystko urządzenia, które są dostępne dla dostępu warunkowego w usłudze Azure AD.
- Wyświetl właściwości, które obejmują urządzenia przyłączone hybrydowo do usługi Azure AD.
- Znajdź klucze funkcji BitLocker dla urządzeń przyłączone do usługi Azure AD, zarządzaj urządzeniem za pomocą usługi Intune i nie tylko.
- Zarządzanie ustawieniami związanymi z urządzeniami w usłudze Azure AD.

Aby uzyskać więcej informacji, zobacz [Zarządzanie urządzeniami przy użyciu Azure Portal](../devices/device-management-azure-portal.md).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Obsługa systemu macOS jako platformy urządzeń dla dostępu warunkowego usługi Azure AD

**Wpisz:** Nowa kategoria usługi **funkcji: Możliwość** produktu dostępu **warunkowego:** Bezpieczeństwo i ochrona tożsamości

Teraz możesz dołączyć (lub wykluczyć) system macOS jako warunek platformy urządzenia w zasadach dostępu warunkowego usługi Azure AD. Dodanie systemu macOS do obsługiwanych platform urządzeń umożliwia:

- **Rejestrowanie urządzeń z systemem macOS i zarządzanie nimi przy użyciu usługi Intune.** Podobnie jak w przypadku innych platform, takich jak iOS i Android, aplikacja Portal firmy jest dostępna dla systemu macOS w celu ujednoliconego rejestrowania. Nowa aplikacja Portal firmy dla systemu macOS umożliwia zarejestrowanie urządzenia w usłudze Intune i zarejestrowanie go w usłudze Azure AD.
- **Upewnij się, że urządzenia z systemem macOS są zgodne z zasadami zgodności organizacji zdefiniowanymi w usłudze Intune.** W usłudze Intune Azure Portal można teraz skonfigurować zasady zgodności dla urządzeń z systemem macOS.
- **Ogranicz dostęp do aplikacji w usłudze Azure AD tylko do zgodnych urządzeń z systemem macOS.** Tworzenie zasad dostępu warunkowego ma system macOS jako oddzielną opcję platformy urządzenia. Teraz możesz ować zasady dostępu warunkowego specyficzne dla systemu macOS dla aplikacji docelowej ustawionej na platformie Azure.

Aby uzyskać więcej informacji, zobacz:

- [Tworzenie zasad zgodności urządzeń dla urządzeń z systemem macOS w usłudze Intune](/mem/intune/protect/compliance-policy-create-mac-os)
- [Dostęp warunkowy w usłudze Azure Active Directory](../conditional-access/overview.md)

---

### <a name="network-policy-server-extension-for-azure-ad-multi-factor-authentication"></a>Rozszerzenie serwera zasad sieciowych dla usługi Azure AD Multi-Factor Authentication

**Wpisz:** Nowa kategoria **usługi funkcji: Możliwość**  uwierzytelniania wieloskładnikowego **Produkt: Uwierzytelnianie** użytkowników

Rozszerzenie serwera zasad sieciowych dla usługi Azure AD Multi-Factor Authentication dodaje możliwości usługi Multi-Factor Authentication oparte na chmurze do infrastruktury uwierzytelniania przy użyciu istniejących serwerów. Za pomocą rozszerzenia Serwer zasad sieciowych możesz dodać weryfikację połączenia telefonicznego, wiadomości SMS lub aplikacji telefonicznej do istniejącego przepływu uwierzytelniania. Nie trzeba instalować, konfigurować i konserwować nowych serwerów.

To rozszerzenie zostało utworzone dla organizacji, które chcą chronić połączenia wirtualnej sieci prywatnej bez wdrażania usługi Azure Serwer Multi-Factor Authentication. Rozszerzenie serwera zasad sieciowych działa jako karta między usługą RADIUS a opartym na chmurze uwierzytelnianiem wieloskładnikowym usługi Azure AD w celu zapewnienia drugiego czynnika uwierzytelniania dla użytkowników federowanych lub zsynchronizowanych.

Aby uzyskać więcej informacji, zobacz [Integrowanie istniejącej infrastruktury serwera zasad sieciowych z usługą Azure AD Multi-Factor Authentication.](../authentication/howto-mfa-nps-extension.md)

---

### <a name="restore-or-permanently-remove-deleted-users"></a>Przywracanie lub trwałe usuwanie usuniętych użytkowników

**Wpisz:** Nowa kategoria **usługi funkcji:** Możliwość **zarządzania użytkownikami Produkt:** Katalog

W centrum administracyjnym usługi Azure AD możesz teraz:

- Przywracanie usuniętego użytkownika.
- Trwałe usunięcie użytkownika.

**Aby ją wypróbować:**

1. W centrum administracyjnym usługi Azure AD wybierz pozycję [Wszyscy użytkownicy](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) w **sekcji** Zarządzanie.

2. Z listy **Pokaż** wybierz pozycję **Ostatnio usunięci użytkownicy.**

3. Wybierz co najmniej jednego ostatnio usuniętego użytkownika, a następnie przywróć go lub trwale usuń.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie dla dostępu warunkowego opartego na aplikacji usługi Azure AD

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Możliwości produktu dostępu **warunkowego:** Zabezpieczenia i ochrona tożsamości

Do listy zatwierdzonych aplikacji klienckich zostały [dodane następujące aplikacje:](../conditional-access/concept-conditional-access-conditions.md#client-apps)

- Microsoft Planner
- Azure Information Protection

Aby uzyskać więcej informacji, zobacz:

- [Wymaganie zatwierdzonej aplikacji klienckiej](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Używanie wartości "OR" między kontrolkami w zasadach dostępu warunkowego

**Wpisz:** Zmieniono **kategorię usługi funkcji:** Możliwości produktu dostępu **warunkowego:** Zabezpieczenia i ochrona tożsamości

Teraz możesz użyć opcji "OR" (wymagaj jednej z wybranych kontrolek) dla kontroli dostępu warunkowego. Za pomocą tej funkcji można tworzyć zasady z wartością "OR" między kontrolami dostępu. Za pomocą tej funkcji można na przykład utworzyć zasady, które wymagają od użytkownika zalogowania się przy użyciu uwierzytelniania wieloskładnikowego "OR" w celu korzystania ze zgodnego urządzenia.

Aby uzyskać więcej informacji, zobacz Controls in Azure AD Conditional Access (Mechanizmy [kontroli w dostępie warunkowym usługi Azure AD).](../conditional-access/controls.md)

---

### <a name="aggregation-of-real-time-risk-detections"></a>Agregacja wykrywania ryzyka w czasie rzeczywistym

**Wpisz:** Zmieniono **kategorię usługi feature:** Identity protection **Product capability:** Identity security and protection

W Azure AD Identity Protection wszystkie wykrycia ryzyka w czasie rzeczywistym pochodzące z tego samego adresu IP w danym dniu są teraz agregowane dla każdego typu wykrywania ryzyka. Ta zmiana ogranicza ilość wykrywanych zagrożeń bez żadnych zmian w zabezpieczeniach użytkowników.

Podstawowe wykrywanie w czasie rzeczywistym działa za każdym razem, gdy użytkownik się do nich insyguje. Jeśli masz zasady zabezpieczeń dotyczące ryzyka logowania ustawione na usługę Multi-Factor Authentication lub blokujesz dostęp, są one nadal wyzwalane podczas każdego ryzykownych logowania.

---

## <a name="october-2017"></a>Październik 2017

### <a name="deprecate-azure-ad-reports"></a>Wycofaj raporty usługi Azure AD

**Wpisz:** Planowanie zmiany kategoria **usługi:** Możliwości **produktu raportowania:** Zarządzanie cyklem życia tożsamości

Ten Azure Portal zapewnia:

- Nowa konsola administracyjna usługi Azure AD.
- Nowe interfejsy API dla raportów aktywności i zabezpieczeń.

Ze względu na te nowe możliwości interfejsy API raportów w punkcie końcowym /reports zostały wycofane 10 grudnia 2017 r.

---

### <a name="automatic-sign-in-field-detection"></a>Automatyczne wykrywanie pól logowania

**Wpisz:** Naprawiono **kategorię usługi:** Moje aplikacje **product capability:** Single sign-on (Stała kategoria usługi: Moje aplikacje product: Single sign-on (Możliwość produktu: logowanie pojedyncze)

Usługa Azure AD obsługuje automatyczne wykrywanie pól logowania dla aplikacji, które renderują pole nazwy użytkownika i hasła HTML. Te kroki opisano w te [tematach Jak automatycznie przechwytywać](../manage-apps/troubleshoot-password-based-sso.md#manually-capture-sign-in-fields-for-an-app)pola logowania dla aplikacji. Tę możliwość można znaleźć, dodając *aplikację* spoza galerii na stronie **Aplikacje** dla przedsiębiorstw w [Azure Portal](https://aad.portal.azure.com). Ponadto możesz skonfigurować tryb  logowania pojedynczego w tej nowej aplikacji do logowania pojedynczego opartego na hasłach, wprowadzić internetowy adres URL, a następnie zapisać stronę.

Z powodu problemu z usługą ta funkcja została tymczasowo wyłączona. Problem został rozwiązany, a automatyczne wykrywanie pól logowania jest ponownie dostępne.

---

### <a name="new-multi-factor-authentication-features"></a>Nowe funkcje usługi Multi-Factor Authentication

**Wpisz:** Nowa kategoria **usługi feature: Multi-Factor** Authentication Product capability: Identity security and protection (Możliwość produktu uwierzytelniania wieloskładnikowego: zabezpieczenia i ochrona tożsamości) 

Uwierzytelnianie wieloskładnikowe (MFA) jest istotną częścią ochrony organizacji. Aby poświadczenia były bardziej adaptacyjne i bardziej bezproblemowe, dodano następujące funkcje:

- Wyniki testu wieloskładnikowego są bezpośrednio zintegrowane z raportem logowania usługi Azure AD, który obejmuje programowy dostęp do wyników uwierzytelniania wieloskładnikowego.
- Konfiguracja uwierzytelniania wieloskładnikowego jest bardziej zintegrowana ze środowiskom konfiguracji usługi Azure AD w Azure Portal.

W tej publicznej wersji zapoznawczej zarządzanie usługą MFA i raportowanie są zintegrowaną częścią podstawowego rozwiązania konfiguracji usługi Azure AD. Teraz możesz zarządzać funkcjami portalu zarządzania usługi MFA w ramach funkcji usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [temat Reference for MFA reporting (Informacje dotyczące raportowania usługi MFA) w Azure Portal](../authentication/howto-mfa-reporting.md).

---

### <a name="terms-of-use"></a>Warunki użytkowania

**Wpisz:** Nowa kategoria **usługi feature:** Warunki użytkowania **Produkt:** Zgodność

Warunków użytkowania usługi Azure AD można użyć do przedstawienia użytkownikom informacji, takich jak odpowiednie zastrzeżenia do wymagań prawnych lub wymagań dotyczących zgodności.

Warunków użytkowania usługi Azure AD można używać w następujących scenariuszach:

- Ogólne warunki użytkowania dla wszystkich użytkowników w organizacji
- Określone warunki użytkowania na podstawie atrybutów użytkownika (na przykład lekarze i pracownicy wewnętrzni lub pracownicy wewnętrzni i międzynarodowi) wykonywane przez grupy dynamiczne
- Określone warunki użytkowania dotyczące uzyskiwania dostępu do aplikacji biznesowych o dużym wpływie, takich jak Salesforce

Aby uzyskać więcej informacji, zobacz [Warunki użytkowania usługi Azure AD.](../conditional-access/terms-of-use.md)

---

### <a name="enhancements-to-privileged-identity-management"></a>Ulepszenia Privileged Identity Management

**Wpisz:** Nowa kategoria **usługi funkcji:** Privileged Identity Management **Produkt:** Privileged Identity Management

Dzięki Azure AD Privileged Identity Management możesz kontrolować i monitorować dostęp do zasobów platformy Azure (wersja zapoznawcza) w organizacji, aby:

- Subskrypcje
- Grupy zasobów
- Maszyny wirtualne

Wszystkie zasoby w Azure Portal, które korzystają z funkcji RBAC platformy Azure, mogą korzystać ze wszystkich możliwości zarządzania zabezpieczeniami i cyklem życia, które Azure AD Privileged Identity Management oferuje.

Aby uzyskać więcej informacji, zobacz [Privileged Identity Management zasobów platformy Azure.](../privileged-identity-management/azure-pim-resource-rbac.md)

---

### <a name="access-reviews"></a>Przeglądy dostępu

**Wpisz:** Nowa kategoria **usługi funkcji: Przeglądy** dostępu **Możliwości produktu:** Zgodność

Organizacje mogą używać przeglądów dostępu (wersja zapoznawcza) do efektywnego zarządzania członkostwem w grupach i dostępem do aplikacji dla przedsiębiorstw:

- Przeprowadzając przeglądy dostępu, można ponownie certyfikować dostęp użytkowników-gości do aplikacji i ich członkostwo w grupach. Recenzentzy mogą efektywnie decydować, czy zezwolić gościom na dalszy dostęp na podstawie szczegółowych informacji zapewnianych przez przeglądy dostępu.
- Przeprowadzając przeglądy dostępu, można ponownie certyfikować dostęp do aplikacji i członkostwo w grupach pracowników.

Kontrole z przeglądem dostępu można łączyć w programy odpowiednie dla danej organizacji, aby śledzić przeglądy dla aplikacji dotyczących zgodności lub aplikacji narażonych na ryzyko.

Aby uzyskać więcej informacji, zobacz [Przeglądy dostępu w usłudze Azure AD.](../governance/access-reviews-overview.md)

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Ukrywanie aplikacji innych firm w Moje aplikacje uruchamiania aplikacji usługi Office 365

**Wpisz:** Nowa kategoria **usługi feature:** Moje aplikacje product capability: Single sign-on (Nowa kategoria usługi funkcji: **Moje aplikacje product capability:** Single sign-on (Możliwość produktu: logowanie pojedyncze)

Teraz możesz lepiej zarządzać aplikacjami, które są wyświetlane w portalach użytkowników, za pomocą nowej właściwości **ukryj** aplikację. Możesz ukryć aplikacje, aby pomóc w przypadkach, w których kafelki aplikacji są wyświetlane dla usług back-end lub zduplikowanych kafelków i zaśmiecania uruchamiania aplikacji użytkowników. Przełącznik znajduje się w sekcji **Właściwości** aplikacji innej firmy i ma etykietę **Widoczne dla użytkownika?** Aplikację można również ukryć programowo za pomocą programu PowerShell.

Aby uzyskać więcej informacji, zobacz Ukrywanie aplikacji [innej firmy w interfejsie użytkownika w usłudze Azure AD.](../manage-apps/hide-application-from-user-portal.md)


**Co jest dostępne?**

 W ramach przejścia do nowej konsoli administracyjnej dostępne są dwa nowe interfejsy API do pobierania dzienników aktywności usługi Azure AD. Nowy zestaw interfejsów API zapewnia bardziej rozbudowane funkcje filtrowania i sortowania, a także oferuje bardziej rozbudowane działania inspekcji i logowania. Dane wcześniej dostępne za pośrednictwem raportów zabezpieczeń są teraz dostępne za pośrednictwem interfejsu API wykrywania ryzyka usługi Identity Protection w Microsoft Graph.


## <a name="september-2017"></a>Wrzesień 2017

### <a name="hotfix-for-identity-manager"></a>Poprawka dla programu Identity Manager

**Wpisz:** Zmieniono **kategorię usługi feature:** Identity Manager **Product capability:** Identity lifecycle management (Możliwość produktu Identity Manager: zarządzanie cyklem życia tożsamości)

Pakiet zbiorczy poprawek (kompilacja 4.4.1642.0) jest dostępny od 25 września 2017 r. dla programu Identity Manager 2016 z dodatkiem Service Pack 1. Ten pakiet zbiorczy:

- Rozwiązuje problemy i dodaje ulepszenia.
- Jest aktualizacją zbiorczą, która zastępuje wszystkie aktualizacje dodatku Service Pack 1 programu Identity Manager 2016 aż do kompilacji 4.4.1459.0 dla programu Identity Manager 2016.
- Wymaga programu Identity Manager 2016 w kompilacji 4.4.1302.0.

Aby uzyskać więcej informacji, zobacz Pakiet zbiorczy poprawek [(kompilacja 4.4.1642.0)](https://support.microsoft.com/help/4021562)jest dostępny dla programu Identity Manager 2016 z dodatkiem Service Pack 1.

---