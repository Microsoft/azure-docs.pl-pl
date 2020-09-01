---
title: Co nowego? Informacje o wersji — Azure Active Directory | Microsoft Docs
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
ms.date: 08/31/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d8ce5f7ec59df16b71a1fbaf51950ee4c31872a
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268948"
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

## <a name="august-2020"></a>Sierpień 2020 r. 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Aktualizacje wymagań zapory Serwer Multi-Factor Authentication platformy Azure

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Funkcja  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Od 1 października 2020 wymagania dotyczące zapory serwera usługi Azure MFA będą wymagały dodatkowych zakresów adresów IP.

Jeśli w organizacji istnieją reguły zapory dla ruchu wychodzącego, zaktualizuj reguły tak, aby serwery usługi MFA mogły komunikować się ze wszystkimi wymaganymi zakresami adresów IP. Zakresy adresów IP są udokumentowane w [wymaganiach dotyczących zapory serwer Multi-Factor Authentication platformy Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Nadchodzące zmiany środowiska użytkownika w celu uzyskania bezpiecznego wyniku tożsamości

**Typ:** Planowanie zmiany  
**Kategoria usługi:** **Możliwości produktu** do ochrony tożsamości: ochrona tożsamości & zabezpieczenia

Aktualizujesz Portal bezpiecznego oceniania tożsamości, aby wyrównać zmiany wprowadzone w [nowej wersji](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-new?view=o365-worldwide)Microsoft Secure Score. 

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

Aby dowiedzieć się więcej, zobacz uprawnienia [dostępu gościa z ograniczeniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-restrict-guest-permissions) i [użytkowników](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions).
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Ogólna dostępność zapytań różnicowych dla jednostek usługi

**Typ:** Nowa funkcja  
**Kategoria usługi:** Program MS Graph  
**Możliwość produktu:** Środowisko deweloperskie
 
Microsoft Graph zapytanie różnicowe obsługuje teraz typ zasobu w wersji 1.0:
- Jednostka usługi

Teraz klienci mogą efektywnie śledzić zmiany do tych zasobów i zapewniać najlepsze rozwiązanie do synchronizowania zmian tych zasobów z lokalnym magazynem danych. Aby dowiedzieć się, jak skonfigurować te zasoby w zapytaniu, zobacz [Używanie zapytania różnicowego do śledzenia zmian w danych Microsoft Graph](https://docs.microsoft.com/graph/delta-query-overview).
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Ogólna dostępność zapytań różnicowych dla element oauth2permissiongrant

**Typ:** Nowa funkcja  
**Kategoria usługi:** Program MS Graph  
**Możliwość produktu:** Środowisko deweloperskie

Microsoft Graph zapytanie różnicowe obsługuje teraz typ zasobu w wersji 1.0:
- Element oauth2permissiongrant

Klienci mogą teraz śledzić zmiany do tych zasobów wydajnie i zapewniać najlepsze rozwiązanie do synchronizowania zmian tych zasobów z lokalnym magazynem danych. Aby dowiedzieć się, jak skonfigurować te zasoby w zapytaniu, zobacz [Używanie zapytania różnicowego do śledzenia zmian w danych Microsoft Graph](https://docs.microsoft.com/graph/delta-query-overview).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — sierpień 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W sierpniu 2020 dodaliśmy następujące 26 nowych aplikacji w galerii aplikacji z obsługą Federacji:

[Backup365](https://portal.backup365.io/login), [Soapbox](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [Alma SIS](https://almau.getalma.com/), [Enlyft Dynamics 365 łącznik](http://enlyft.com/), [Serraview rozwiązania oprogramowania](https://docs.microsoft.com/azure/active-directory/saas-apps/serraview-space-utilization-software-solutions-tutorial), [uniq](https://web.uniq.app/), w [widoczny](https://docs.microsoft.com/azure/active-directory/saas-apps/visibly-tutorial), [Zylo](https://docs.microsoft.com/azure/active-directory/saas-apps/zylo-tutorial), [Edmentum-kursowe oceny, dokładne ścieżki](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Altamira HRM](https://docs.microsoft.com/azure/active-directory/saas-apps/altamira-hrm-tutorial), [WireWheel](https://docs.microsoft.com/azure/active-directory/saas-apps/wirewheel-tutorial), [ZIX zgodność i przechwytywanie](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), [Greenlight Enterprise Controls platform](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-enterprise-business-controls-platform-tutorial), [Usługa Genetec zasięg](https://www.clearance.network/), [ISAMs](https://docs.microsoft.com/azure/active-directory/saas-apps/isams-tutorial), [VeraSMART](https://docs.microsoft.com/azure/active-directory/saas-apps/verasmart-tutorial), [Amiko](https://amiko.web.rivero.app/), twingate [,](https://auth.twingate.com/signup) [dzierżawa lejka](https://nestiolistings.com/sso/oidc/azure/authorize/), [Scalefusion](https://scalefusion.com/users/sign_in/), [Bpanda](https://goto.bpanda.com/login), [Vivun](https://app.vivun.com/dashboard/calendar/connect) [FortiGate SSL VPN](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial) [, RStudio](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-server-pro-tutorial) [Wandera End User](https://www.wandera.com/)

W tym miejscu możesz również znaleźć dokumentację dotyczącą wszystkich aplikacji https://aka.ms/AppsTutorial

Aby wyświetlić listę aplikacji w galerii aplikacji usługi Azure AD, zapoznaj się ze szczegółowymi informacjami w tym miejscu https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Dostępne są teraz lasy zasobów dla platformy Azure AD DS 

**Typ:** Nowa **Kategoria usługi funkcji:** Azure AD Domain Services   
**Możliwość produktu:** Azure AD Domain Services
 
Możliwości lasów zasobów w Azure AD Domain Services są teraz ogólnie dostępne. Teraz można włączyć autoryzację bez synchronizacji skrótów haseł do używania Azure AD Domain Services, w tym autoryzacji karty inteligentnej. Aby dowiedzieć się więcej, zobacz [pojęcia i funkcje zestawu replik dla Azure Active Directory Domain Services (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory-domain-services/concepts-replica-sets).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>Obsługa replik regionalnych dla AD DS domen zarządzanych przez platformę Azure jest teraz dostępna

**Typ:** Nowa funkcja   
**Kategoria usługi:** Azure AD Domain Services  
**Możliwość produktu:** Azure AD Domain Services
 
Można rozszerzyć domenę zarządzaną, aby mieć więcej niż jeden zbiór replik dla dzierżawy usługi Azure AD. Zestawy replik można dodawać do dowolnej komunikacji równorzędnej sieci wirtualnej w dowolnym regionie świadczenia usługi Azure, który obsługuje Azure AD Domain Services. Dodatkowe zestawy replik w różnych regionach platformy Azure zapewniają geograficzne odzyskiwanie po awarii dla starszych aplikacji, jeśli region platformy Azure przejdzie w tryb offline. Aby dowiedzieć się więcej, zobacz [pojęcia i funkcje zestawu replik dla Azure Active Directory Domain Services (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory-domain-services/concepts-replica-sets).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Ogólna dostępność usługi Azure AD moje logowania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Środowiska użytkownika końcowego
 
Moje logowania w usłudze Azure AD to nowa funkcja umożliwiająca użytkownikom korporacyjnym przeglądanie historii logowania w celu sprawdzenia, czy nie występują żadne nietypowe działania. Ponadto ta funkcja umożliwia użytkownikom końcowym zgłaszanie "tego nie jestem" lub "to ja mnie" w podejrzanych działaniach. Aby dowiedzieć się więcej na temat korzystania z tej funkcji, zobacz [Wyświetlanie i wyszukiwanie ostatnich działań związanych z logowaniem ze strony Moje logowania](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-sign-ins-page#confirm-unusual-activity).
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>Inicjowanie obsługi administracyjnej użytkowników w usłudze SAP SuccessFactors w usłudze Azure AD jest teraz ogólnie dostępne

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
Teraz możesz zintegrować rozwiązanie SAP SuccessFactors jako autorytatywne Źródło tożsamości z usługą Azure AD i zautomatyzować kompleksowy cykl życia tożsamości przy użyciu wydarzeń HR, takich jak nowe zatrudnienie i zakończenia, aby zapewnić możliwość aprowizacji i nieaprowizacji kont w usłudze Azure AD. 

Aby dowiedzieć się więcej o sposobie konfigurowania inicjowania obsługi przychodzącej SAP SuccessFactors w usłudze Azure AD, zapoznaj się z samouczkiem [Konfigurowanie oprogramowania SAP SuccessFactors w celu Active Directory aprowizacji użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-successfactors-inbound-provisioning-tutorial).
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Niestandardowa nazwa Open ID Connect interfejs API programu Graph support dla Azure AD B2C

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C
 
Wcześniej niestandardowe dostawcy połączenia Open ID mogą być dodawane lub zarządzane tylko za pomocą Azure Portal. Teraz Azure AD B2C klienci mogą także dodawać je i zarządzać nimi za pomocą wersji Microsoft Graph API wersja beta. Aby dowiedzieć się, jak skonfigurować ten zasób przy użyciu interfejsów API, zobacz [identityProvider Resource Type](https://docs.microsoft.com/graph/api/resources/identityprovider?view=graph-rest-beta).
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Przypisywanie wbudowanych ról usługi Azure AD do grup chmur

**Typ:** Nowa funkcja  
**Kategoria usługi:** Role usługi Azure AD  
**Możliwość produktu:** Access Control

Teraz możesz przypisywać wbudowane role usługi Azure AD do grup w chmurze przy użyciu tej nowej funkcji. Można na przykład przypisać rolę administratora programu SharePoint do grupy Contoso_SharePoint_Admins. Możesz również użyć programu PIM, aby grupa była uprawnionym członkiem roli, zamiast udzielać stałego dostępu. Aby dowiedzieć się, jak skonfigurować tę funkcję, zobacz temat [Używanie grup w chmurze do zarządzania przypisaniami ról w Azure Active Directory (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-groups-concept).
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Wbudowana rola lidera biznesowego usługi Insights jest teraz dostępna

**Typ:** Nowa funkcja  
**Kategoria usługi:** Role usługi Azure AD  
**Możliwość produktu:** Access Control
 
Użytkownicy w roli lidera usługi Insights mogą uzyskać dostęp do zestawu pulpitów nawigacyjnych i szczegółowych informacji za pośrednictwem [aplikacji M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Obejmuje to pełny dostęp do wszystkich pulpitów nawigacyjnych oraz przedstawia szczegółowe informacje i funkcje eksploracji danych. Jednak użytkownicy w tej roli nie mają dostępu do ustawień konfiguracji produktu, które są odpowiedzialne za rolę administratora usługi Insights. Aby dowiedzieć się więcej na temat tej roli, zobacz [uprawnienia roli administrator w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Wbudowana rola administratora usługi Insights jest teraz dostępna

**Typ:** Nowa funkcja  
**Kategoria usługi:** Role usługi Azure AD  
**Możliwość produktu:** Access Control
 
Użytkownicy w roli administratora usługi Insights mogą uzyskać dostęp do pełnego zestawu funkcji administracyjnych w [aplikacji M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Użytkownik w tej roli może odczytywać informacje o katalogu, monitorować kondycję usługi, bilety pomocy technicznej plików i uzyskiwać dostęp do aspektów ustawień administratora usługi Insights. Aby dowiedzieć się więcej na temat tej roli, zobacz [uprawnienia roli administrator w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>Administrator aplikacji i administrator aplikacji w chmurze mogą zarządzać właściwościami rozszerzenia aplikacji

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Role usługi Azure AD  
**Możliwość produktu:** Access Control
 
Wcześniej tylko administrator globalny mógł zarządzać [właściwością rozszerzenia](https://docs.microsoft.com/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http). Teraz udostępniamy tę funkcję administratorowi aplikacji i administratorowi aplikacji w chmurze.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>Poprawka do programu MIM 2016 SP2 4.6.263.0 i łączniki 1.1.1301.0

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Microsoft Identity Manager  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości

[Pakiet zbiorczy poprawek (build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) jest dostępny dla Microsoft Identity Manager (MIM) 2016 z dodatkiem Service Pack 2 (SP2). Ten pakiet zbiorczy zawiera aktualizacje zarządzanie certyfikatami w usłudze MIM, Menedżera synchronizacji programu MIM i składników usługi PAM. Ponadto 1.1.1301.0E łączniki ogólne programu MIM zawierają aktualizacje łącznika programu Graph.

---
 
## <a name="july-2020"></a>Lipiec 2020 r.

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>Jako administrator IT chcę dokierować aplikacje klienckie przy użyciu dostępu warunkowego

**Typ:** Planowanie zmiany   
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
W przypadku wersji "wydanie" aplikacji klienckich w przypadku dostępu warunkowego nowe zasady będą teraz stosowane domyślnie dla wszystkich aplikacji klienckich. Obejmuje to starszych klientów uwierzytelniania. Istniejące zasady pozostaną bez zmian, ale przełącznik *Konfiguruj tak/nie* zostanie usunięty z istniejących zasad, aby łatwo sprawdzić, które aplikacje klienckie są stosowane przez zasady. 

Podczas tworzenia nowych zasad upewnij się, że wykluczono użytkowników i konta usług, które nadal korzystają ze starszego uwierzytelniania. Jeśli tego nie zrobisz, zostaną zablokowane. [Dowiedz się więcej](https://aka.ms/caclientapps).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Nadchodzące poprawki zgodności Standard scim

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
Usługa Azure AD Provisioning korzysta ze standardu Standard scim na potrzeby integracji z aplikacjami. Nasza implementacja standardu Standard scim jest zmieniana i oczekujemy, że wprowadzimy zmiany w naszym zachowaniu, a także ustawimy Właściwość "Active" w zasobie. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-scim-compatibility).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>Ustawienie właściciela grupy w portalu administracyjnym platformy Azure zostanie zmienione

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności

Ustawienia właściciela na stronie Ustawienia ogólne grup można skonfigurować w taki sposób, aby ograniczyć uprawnienia przypisania właściciela do ograniczonej grupy użytkowników w portalu administracyjnym platformy Azure i w panelu dostępu. Wkrótce będziemy mogli przypisywać uprawnienia właściciela grupy nie tylko w tych dwóch portalach środowiska użytkownika, ale również wymuszać zasady w zapleczu, aby zapewnić spójne zachowanie między punktami końcowymi, takimi jak PowerShell i Microsoft Graph. 

Rozpocznie się wyłączanie bieżącego ustawienia dla klientów, którzy nie korzystają z tej usługi, i udostępnienie opcji określania zakresu użytkowników dla uprawnienia właściciela grupy w ciągu następnych kilku miesięcy. Aby uzyskać wskazówki dotyczące aktualizowania ustawień grupy, zobacz Edytowanie informacji o grupie przy użyciu [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Usługa rejestracji Azure Active Directory kończy obsługę protokołów TLS 1,0 i 1,1

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Rejestrowanie urządzeń i zarządzanie nimi  
**Możliwość produktu:** Platformach

Protokół TLS (Transport Layer Security) 1,2 i serwery i klienci aktualizacji wkrótce będą komunikować się z usługą Azure Active Directory Device Registration. Obsługa protokołu TLS 1,0 i 1,1 do komunikacji z usługą rejestracji urządzeń w usłudze Azure AD zostanie wycofana:
- 31 sierpnia 2020, we wszystkich w chmurach suwerennych (w serwisie szybkim, DoD itp.)
- 30 października 2020, we wszystkich chmurach komercyjnych

[Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/devices/reference-device-registration-tls-1-2) o protokole TLS 1,2 dla usługi rejestracji w usłudze Azure AD.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Logowania do usługi Windows Hello dla firm widoczne w dziennikach logowania w usłudze Azure AD

**Typ:** FIXED  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania
 
Funkcja Windows Hello dla firm umożliwia użytkownikom końcowym logowanie się do maszyn z systemem Windows przy użyciu gestu (takiego jak numer PIN lub biometryczna). Administratorzy usługi Azure AD mogą chcieć odróżnić logowania w usłudze Windows Hello dla firm z innych logowań systemu Windows w ramach podróży organizacji do uwierzytelniania bezhaseł. 

Administratorzy mogą teraz sprawdzić, czy uwierzytelnianie systemu Windows korzysta z usługi Windows Hello dla firm, sprawdzając kartę Szczegóły uwierzytelniania dla zdarzenia logowania systemu Windows w bloku logowania usługi Azure AD w witrynie Azure Portal. Uwierzytelnianie usługi Windows Hello dla firm będzie zawierać ciąg "WindowsHelloForBusiness" w polu Metoda uwierzytelniania. Aby uzyskać więcej informacji na temat interpretacji dzienników logowania, zapoznaj się z [dokumentacją logowania](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins).
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Poprawki dotyczące zachowania usuwania grup i ulepszeń wydajności

**Typ:** FIXED  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
Wcześniej, gdy grupa została zmieniona z "z zakresu" na "poza zakresem", a administrator kliknął ponowne uruchomienie przed zakończeniem zmiany, obiekt grupy nie został usunięty. Teraz obiekt grupy zostanie usunięty z aplikacji docelowej, gdy wyjdzie poza zakres (wyłączone, usunięte, nieprzypisane lub nie przeszedł filtru zakresu). [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Publiczna wersja zapoznawcza: Administratorzy mogą teraz dodawać zawartość niestandardową do recenzentów podczas tworzenia przeglądu dostępu

**Typ:** Nowa funkcja  
**Kategoria usługi:** Przeglądy dostępu  
**Możliwość produktu:** Zarządzanie tożsamościami
 
Po utworzeniu nowego przeglądu dostępu recenzent otrzyma wiadomość e-mail z prośbą o przeprowadzenie przeglądu dostępu. Wielu naszych klientów poprosiło o możliwość dodania do wiadomości e-mail niestandardowej zawartości, takiej jak informacje kontaktowe, lub innej dodatkowej zawartości pomocniczej, która będzie przewodnikiem recenzenta. 

Teraz dostępne w publicznej wersji zapoznawczej Administratorzy mogą określać zawartość niestandardową w wiadomościach e-mail wysyłanych do recenzentów, dodając zawartość w sekcji "Zaawansowane" przeglądów dostępu do usługi Azure AD. Aby uzyskać wskazówki dotyczące tworzenia przeglądów dostępu, zobacz temat [Tworzenie przeglądu dostępu do grup i aplikacji w przeglądach dostępu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Przepływ kodu autoryzacji dla aplikacji jednostronicowych

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Środowisko deweloperskie
 
Ze względu na to, że istnieją ograniczenia dotyczące plików cookie innych firm, takie jak Safari ITP, Usługa aplikacji jednostronicowych będzie musiała używać przepływu kodu autoryzacji zamiast przepływu niejawnego do obsługi logowania jednokrotnego, a MSAL.js v 2. x będzie teraz obsługiwać przepływ kodu autoryzacji. 

Istnieją odpowiednie aktualizacje Azure Portal, aby można było zaktualizować SPA w taki sposób, aby były typu "Spa" i używają przepływu kodu uwierzytelniania. Więcej wskazówek można znaleźć w temacie [Logowanie użytkowników i uzyskiwanie tokenu dostępu w JavaScript Spa przy użyciu przepływu kodu uwierzytelniania](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript-auth-code) .
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Usługa Azure serwer proxy aplikacji usługi Azure AD obsługuje teraz klienta sieci Web Usługi pulpitu zdalnego

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control

Usługa Azure serwer proxy aplikacji usługi Azure AD obsługuje teraz klienta sieci Web Usługi pulpitu zdalnego (RDS). Klient sieci Web usług pulpitu zdalnego umożliwia użytkownikom dostęp do infrastruktury Pulpit zdalny za pomocą dowolnej przeglądarki z obsługą HTLM5, takiej jak Microsoft Edge, Internet Explorer 11, Google Chrome itd. Użytkownicy mogą korzystać z aplikacji zdalnych lub pulpitów, takich jak w przypadku urządzeń lokalnych, z dowolnego miejsca. Za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD można zwiększyć bezpieczeństwo wdrożenia usług pulpitu zdalnego, wymuszając uwierzytelnianie wstępne i zasady dostępu warunkowego dla wszystkich typów rozbudowanych aplikacji klienckich. Aby uzyskać wskazówki, zobacz temat [publikowanie pulpit zdalny przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-remote-desktop-services).
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>Kolejna generacja Azure AD B2C przepływy użytkownika w publicznej wersji zapoznawczej

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C
 
Uproszczone środowisko przepływu użytkownika oferuje dostępność funkcji w wersji zapoznawczej i jest domem dla wszystkich nowych funkcji. Użytkownicy będą mogli włączyć nowe funkcje w tym samym przepływie użytkownika, co zmniejsza konieczność tworzenia wielu wersji z każdą nową wersją funkcji. Nowe, przyjazne dla użytkownika środowisko upraszcza wybór i Tworzenie przepływów użytkowników. Wypróbuj teraz, [tworząc przepływ użytkownika](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows). 

Aby uzyskać więcej informacji o przepływach użytkowników, zobacz [wersje przepływu użytkowników w Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/user-flow-versions#:~:text=%20%20%20%20User%20flow%20%20%2caccount.%20Usi%20...%20%201%20more%20rows%20).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — lipiec 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W lipcu 2020 dodaliśmy 55 następujące nowe aplikacje w galerii aplikacji z obsługą Federacji:

[CLAP swoje ręce](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor](https://inexto.com/inexto-suite/inextor), [Beekast](https://my.beekast.com/), [Templafy OpenID Connect Connect](https://app.templafy.com/), [PeterConnects recepcjonista](https://msteams.peterconnects.com/), [ALOHACLOUD](https://appfusions.alohacloud.com/auth), [Control Tower](https://bpm.tnxcorp.com/sso/microsoft), [Cocoom](https://start.cocoom.com/), [monety konstrukcyjnej chmury](https://sso.coinsconstructioncloud.com/#login/), [Medxnote MT](https://task.teamsmain.medx.im/authorization), [Reflekt](https://reflekt.konsolute.com/login), [Rever](https://app.reverscore.net/access), [MyCompanyArchive](https://login.mycompanyarchive.com/), [GReminders](https://app.greminders.com/o365-oauth), [Titanfile](https://docs.microsoft.com/azure/active-directory/saas-apps/titanfile-tutorial), [Wootric,](https://docs.microsoft.com/azure/active-directory/saas-apps/wootric-tutorial)SolarWinds [Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US), [OpenText Directory Services](https://docs.microsoft.com/azure/active-directory/saas-apps/opentext-directory-services-tutorial), [DataSite](https://docs.microsoft.com/azure/active-directory/saas-apps/datasite-tutorial), [blog](https://docs.microsoft.com/azure/active-directory/saas-apps/blogin-tutorial), [IntSights](https://docs.microsoft.com/azure/active-directory/saas-apps/intsights-tutorial), kpifire [,](https://docs.microsoft.com/azure/active-directory/saas-apps/kpifire-tutorial) [TextLine](https://docs.microsoft.com/azure/active-directory/saas-apps/textline-tutorial), [Cloud Academy — SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-academy-sso-tutorial), [Community Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/community-spark-tutorial), [ChatWork](https://docs.microsoft.com/azure/active-directory/saas-apps/chatwork-tutorial), [CloudSign](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudsign-tutorial), [C3M Cloud Control](https://docs.microsoft.com/azure/active-directory/saas-apps/c3m-cloud-control-tutorial), [SmartHR](https://smarthr.jp/), [NumlyEngage™](https://docs.microsoft.com/azure/active-directory/saas-apps/numlyengage-tutorial), [Michigan Logowanie jednokrotne w centrum danych, ruch wychodzący](https://docs.microsoft.com/azure/active-directory/saas-apps/michigan-data-hub-single-sign-on-tutorial), [SendSafely](https://docs.microsoft.com/azure/active-directory/saas-apps/sendsafely-tutorial) [, Eletive,](https://app.eletive.com/), [prawo cyberbezpieczeństwa ADI](https://right-hand.ai/), [Fyde Enterprise Authentication](https://enterprise.fyde.com/), [Verme](https://docs.microsoft.com/azure/active-directory/saas-apps/verme-tutorial), [lenses.IO](https://docs.microsoft.com/azure/active-directory/saas-apps/lensesio-tutorial) [,,](https://docs.microsoft.com/azure/active-directory/saas-apps/momenta-tutorial) [wzrost](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [CloudCords](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudcords-tutorial), [tellme bot](https://tellme365liteweb.azurewebsites.net/), [pobudzanie](https://app.inspiresoftware.com/), [Maverics tożsamość, łącznik SAML](https://www.strata.io/identity-fabric/), [Smartschool (system zarządzania szkołą)](https://smart-schoolapp.com/frmLoginForm), [Zepto-Intelligent dokładnych](https://user.zepto-ai.com/signin), [Studi.ly](https://studi.ly/), [Trackplan](http://www.trackplanfm.com/), [Skedda](https://docs.microsoft.com/azure/active-directory/saas-apps/skedda-tutorial), [WhosOnLocation](https://docs.microsoft.com/azure/active-directory/saas-apps/whos-on-location-tutorial), [Coggle](https://docs.microsoft.com/azure/active-directory/saas-apps/coggle-tutorial), [Kemp LoadMaster](https://kemptechnologies.com/cloud-load-balancer/), [BrowserStack Logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/saas-apps/browserstack-single-sign-on-tutorial) [Egress](https://docs.microsoft.com/azure/active-directory/saas-apps/egress-tutorial)

W tym miejscu możesz również znaleźć dokumentację dotyczącą wszystkich aplikacji https://aka.ms/AppsTutorial

Aby wyświetlić listę aplikacji w galerii aplikacji usługi Azure AD, zapoznaj się ze szczegółowymi informacjami w tym miejscu https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Nowe łączniki aprowizacji w galerii aplikacji usługi Azure AD — lipiec 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwości produktu:** integracja innej firmy

Teraz możesz zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanej aplikacji w [serwisie LinkedIn](https://docs.microsoft.com/azure/active-directory/saas-apps/linkedin-learning-provisioning-tutorial).

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzacja aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Wyświetlanie przypisań ról we wszystkich zakresach i możliwość pobierania ich do pliku CSV

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Role usługi Azure AD  
**Możliwość produktu:** Access Control
 
Można teraz wyświetlać przypisania ról we wszystkich zakresach dla roli na karcie "role i Administratorzy" w portalu usługi Azure AD. Możesz również pobrać te przypisania ról dla każdej roli do pliku CSV. Aby uzyskać wskazówki dotyczące wyświetlania i dodawania przypisań ról, zobacz [Wyświetlanie i przypisywanie ról administratorów w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Wdrażanie oprogramowania platformy Azure Multi-Factor Authentication (zestaw SDK usługi Azure MFA)

**Typ:** Przestarzałe  
**Kategoria usługi:** Funkcja  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Na platformie Azure Multi-Factor Authentication Software Development (Azure MFA SDK) osiągnięto koniec okresu istnienia 14 listopada 2018, zgodnie z pierwszym ogłoszeniem w listopadzie 2017. Firma Microsoft będzie zamykać usługę SDK obowiązującą 30 września 2020. Wszystkie wywołania w zestawie SDK zakończą się niepowodzeniem.

Jeśli Twoja organizacja korzysta z zestawu Azure MFA SDK, musisz przeprowadzić migrację do 30 września 2020:
- Zestaw SDK usługi Azure MFA dla programu MIM: Jeśli korzystasz z zestawu SDK z programem MIM, należy przeprowadzić migrację do serwera usługi Azure MFA i aktywować Privileged Access Management (PAM), postępując zgodnie z tymi [instrukcjami](https://docs.microsoft.com/microsoft-identity-manager/working-with-mfaserver-for-mim).   
- Zestaw SDK usługi Azure MFA dla aplikacji niestandardowych: Rozważ integrację aplikacji z usługą Azure AD i używanie dostępu warunkowego w celu wymuszania uwierzytelniania MFA. Aby rozpocząć, Przejrzyj tę [stronę](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration). 

---

## <a name="june-2020"></a>Czerwiec 2020 r. 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Warunek ryzyka użytkownika w zasadach dostępu warunkowego

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 

Obsługa ryzyka użytkownika w zasadach dostępu warunkowego usługi Azure AD umożliwia tworzenie wielu zasad opartych na ryzyku użytkownika. Dla różnych użytkowników i aplikacji mogą być wymagane różne minimalne poziomy ryzyka użytkownika. Na podstawie ryzyka związanego z użytkownikiem można utworzyć zasady blokujące dostęp, wymagać uwierzytelniania wieloskładnikowego, bezpiecznej zmiany hasła lub przekierować do Microsoft Cloud App Security, aby wymusić zasady sesji, takie jak dodatkowe inspekcje.

Warunek ryzyka użytkownika wymaga Azure AD — wersja Premium P2, ponieważ używa usługi Azure Identity Protection, która jest ofertą P2. Więcej informacji o dostępie warunkowym można znaleźć w [dokumentacji dostępu warunkowego usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/).

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>Logowanie jednokrotne w protokole SAML obsługuje teraz aplikacje wymagające SPNameQualifier do ustawienia na żądanie

**Typ:** FIXED  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ
 
Niektóre aplikacje SAML wymagają zwracania SPNameQualifier w temacie potwierdzenia, gdy jest to wymagane. Teraz usługa Azure AD reaguje prawidłowo, gdy żądanie SPNameQualifier jest wymagane w zasadach NameID. Działa to również w przypadku zainicjowanego logowania do programu SP i zainicjowano Logowanie przy użyciu usługi dostawcy tożsamości.  Aby dowiedzieć się więcej na temat protokołu SAML w Azure Active Directory, zobacz Logowanie jednokrotne [protokołu SAML](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol).

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Współpraca B2B w usłudze Azure AD obsługuje zapraszanie kont MSA i użytkowników Google w dzierżawach Azure Government

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C
 

Azure Government dzierżawców korzystających z funkcji współpracy B2B może teraz zapraszać użytkowników, którzy mają konto Microsoft lub Google. Aby dowiedzieć się, czy Dzierżawca może korzystać z tych możliwości, postępuj zgodnie z instrukcjami w temacie [Jak mogę sprawdzić, czy współpraca B2B jest dostępna w mojej dzierżawie platformy Azure dla instytucji rządowych?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>Obiekt użytkownika w programie Microsoft Graph w wersji 1 zawiera teraz właściwości externalUserState i externalUserStateChangedDateTime

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C
 

Właściwości externalUserState i externalUserStateChangedDateTime mogą służyć do znajdowania zaproszonych gości B2B, którzy nie zaakceptowali jeszcze zaproszeń, a także do automatyzacji kompilacji, takich jak usuwanie użytkowników, którzy nie zaakceptowali zaproszeń po upływie kilku dni. Te właściwości są teraz dostępne w programie MS Graph v1. Aby uzyskać wskazówki dotyczące używania tych właściwości, zapoznaj się z tematem [Typ zasobu użytkownika](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0).
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Zarządzanie sesjami uwierzytelniania w usłudze Azure AD dostęp warunkowy jest teraz ogólnie dostępne

**Typ:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Funkcje zarządzania sesjami uwierzytelniania umożliwiają skonfigurowanie, jak często użytkownicy muszą podać poświadczenia logowania, oraz określić, czy należy podać poświadczenia po zamknięciu i ponownym otwarciu przeglądarki, aby zapewnić lepsze zabezpieczenia i elastyczność w danym środowisku.
 
Ponadto zarządzanie sesjami uwierzytelniania stosowane tylko do uwierzytelniania pierwszego czynnika w usłudze Azure AD przyłączone do usługi Azure AD, dołączone do hybrydowego urządzenia z usługą Active Directory i zarejestrowane w usłudze Azure AD. Teraz zarządzanie sesjami uwierzytelniania będzie również dotyczyło uwierzytelniania MFA. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zarządzania sesją uwierzytelniania przy użyciu dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — Czerwiec 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W czerwcu 2020 dodaliśmy następujące 29 nowych aplikacji w galerii aplikacji z obsługą Federacji:

[Shopify Plus](https://docs.microsoft.com/azure/active-directory/saas-apps/shopify-plus-tutorial), [Ekarda](https://docs.microsoft.com/azure/active-directory/saas-apps/ekarda-tutorial), [MailGates](https://docs.microsoft.com/azure/active-directory/saas-apps/mailgates-tutorial), [BullseyeTDP](https://docs.microsoft.com/azure/active-directory/saas-apps/bullseyetdp-tutorial), [Raketa](https://docs.microsoft.com/azure/active-directory/saas-apps/raketa-tutorial), [segment](https://docs.microsoft.com/azure/active-directory/saas-apps/segment-tutorial), [audytor AI](https://www.mindbridge.ai/products/ai-auditor/), [Pobuca Connect](https://app.pobu.ca/), [proto.IO](https://docs.microsoft.com/azure/active-directory/saas-apps/proto.io-tutorial), [strażnik](https://www.gatekeeperhq.com/), [planista centrum](https://docs.microsoft.com/azure/active-directory/saas-apps/hub-planner-tutorial), [zestaw narzędzi do rynkowych partnerów](https://ansira.com/technology/channel-engagement)firmy Microsoft, usługa [IBM Digital Business Automation w chmurze](https://docs.microsoft.com/azure/active-directory/saas-apps/ibm-digital-business-automation-on-cloud-tutorial), [kisi zabezpieczenia fizyczne](https://docs.microsoft.com/azure/active-directory/saas-apps/kisi-physical-security-tutorial), [ViewpointOne](https://team.viewpoint.com/), [INTELLIGENCEBANK](https://docs.microsoft.com/azure/active-directory/saas-apps/intelligencebank-tutorial), [pymetrics](https://docs.microsoft.com/azure/active-directory/saas-apps/pymetrics-tutorial), [zero](https://www.teamzero.com/), [instation](https://instation.invillia.com/), [EDX for Business SAML 2,0 Integration](https://docs.microsoft.com/azure/active-directory/saas-apps/edx-for-business-saml-integration-tutorial), [MOOC Office 365](https://mooc.office365-training.com/en/), [SmartKargo](https://docs.microsoft.com/azure/active-directory/saas-apps/smartkargo-tutorial), [PKIsigning platform](https://platform.pkisigning.nl/), [SiteIntel](https://docs.microsoft.com/azure/active-directory/saas-apps/siteintel-tutorial) [,](https://cloud.metacompliance.com/) [Identyfikator pola](https://docs.microsoft.com/azure/active-directory/saas-apps/field-id-tutorial), [siebie zajęć SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/curricula-saml-tutorial) [,](https://smallstep.com/sso-ssh/) [Perforce Helix](https://docs.microsoft.com/azure/active-directory/saas-apps/perforce-helix-core-tutorial)  

W tym miejscu możesz również znaleźć dokumentację dotyczącą wszystkich aplikacji https://aka.ms/AppsTutorial . Aby wyświetlić listę aplikacji w galerii aplikacji usługi Azure AD, zapoznaj się ze szczegółowymi informacjami tutaj: https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>Łączniki interfejsu API do samoobsługowego rejestrowania tożsamości zewnętrznych są teraz dostępne w publicznej wersji zapoznawczej

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C
 
Łączniki interfejsu API tożsamości zewnętrznych umożliwiają korzystanie z interfejsów API sieci Web w celu zintegrowania rejestracji samoobsługowej z zewnętrznymi systemami w chmurze. Oznacza to, że można teraz wywoływać interfejsy API sieci Web jako określone kroki przepływu rejestracji, aby wyzwolić niestandardowe przepływy pracy oparte na chmurze. Łączniki interfejsu API można na przykład używać do następujących celów:

- Integruj z niestandardowymi przepływami pracy zatwierdzania.
- Wykonywanie weryfikacji tożsamości
- Weryfikowanie danych wejściowych użytkownika
- Zastąp atrybuty użytkownika
- Uruchom niestandardową logikę biznesową

Aby uzyskać więcej informacji o wszystkich doświadczeniach dostępnych w przypadku łączników interfejsu API, zobacz [Używanie łączników interfejsu API do dostosowywania i zwiększania rejestracji samoobsługowej](https://docs.microsoft.com/azure/active-directory/b2b/api-connectors-overview)lub [Dostosowywanie rejestracji tożsamości zewnętrznych w ramach integracji z interfejsem API sieci Web](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin).
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>Zapewnij użytkownikom dostęp do aplikacji w ciągu kilku sekund

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
Usługa Azure AD Provisioning obecnie działa cyklicznie. Usługa jest uruchamiana co 40 min. [Funkcja aprowizacji na żądanie](https://aka.ms/provisionondemand) umożliwia wybranie użytkownika i dostarczenie ich w ciągu kilku sekund. Ta funkcja umożliwia szybkie rozwiązywanie problemów z inicjowaniem obsługi bez konieczności ponownego uruchomienia w celu wymuszenia ponownego uruchomienia cyklu aprowizacji. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Nowe uprawnienie do korzystania z funkcji zarządzania prawami usługi Azure AD w programie Graph

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Zarządzanie prawami
 
Nowe delegowane uprawnienie EntitlementManagement. Read. All jest teraz dostępne do użycia z interfejsem API zarządzania uprawnieniami w Microsoft Graph wersji beta. Aby dowiedzieć się więcej na temat dostępnych interfejsów API, zobacz [Praca z interfejsem API zarządzania uprawnieniami usługi Azure AD](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta).

---

### <a name="identity-protection-apis-available-in-v10"></a>Interfejsy API ochrony tożsamości dostępne w wersji 1.0

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Interfejsy API Microsoft Graph riskyUsers i riskDetections są teraz ogólnie dostępne. Teraz, gdy są one dostępne w punkcie końcowym 1.0, Zapraszamy do korzystania z nich w środowisku produkcyjnym. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/identityprotectionroot?view=graph-rest-1.0).
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Etykiety czułości do stosowania zasad do grup Microsoft 365 są teraz ogólnie dostępne

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności
 

Teraz można tworzyć etykiety czułości i używać ustawień etykiet do stosowania zasad do Microsoft 365 grup, w tym ochrony prywatności (Public lub private) i zewnętrznych zasad dostępu użytkowników. Aby nie zezwalać na dodawanie użytkowników-Gości, można utworzyć etykietę z zasadami ochrony prywatności. Gdy użytkownik zastosuje tę etykietę do grupy, grupa będzie prywatna i nie będzie można dodawać do niej żadnych użytkowników-Gości. 

Etykiety czułości są ważne do ochrony danych o kluczowym znaczeniu dla firmy i umożliwiają zarządzanie grupami na dużą skalę w sposób zgodny i bezpieczny. Aby uzyskać wskazówki dotyczące używania etykiet czułości, zapoznaj się z tematem [przypisywanie etykiet czułości do grup pakietu Office 365 w Azure Active Directory (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-assign-sensitivity-labels).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Aktualizacje do obsługi Microsoft Identity Manager dla klientów Azure AD — wersja Premium

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Microsoft Identity Manager  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
Pomoc techniczna platformy Azure jest teraz dostępna dla składników integracji usługi Azure AD Microsoft Identity Manager 2016, do końca rozszerzonej pomocy technicznej dla Microsoft Identity Manager 2016. Zapoznaj się z artykułem [support Update for Azure AD — wersja Premium klienci korzystający z Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>Zwiększenie użycia warunków członkostwa w grupie w konfiguracji oświadczeń rejestracji jednokrotnej

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ
 
Wcześniej liczba grup, których można użyć w przypadku warunkowego zmiany oświadczeń na podstawie przynależności do grupy w ramach jednej konfiguracji aplikacji, została ograniczona do 10. Użycie warunków członkostwa w grupie w konfiguracji oświadczeń logowania jednokrotnego została teraz zwiększone do maksymalnie 50 grup. Aby uzyskać więcej informacji na temat konfigurowania oświadczeń, zapoznaj się z tematem [Konfiguracja oświadczeń aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#emitting-claims-based-on-conditions). 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Włączenie podstawowego formatowania składnika tekst strony logowania w znakach firmowych.

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Funkcja znakowania firmowego w środowisku logowania usługi Azure AD/Microsoft 365 została zaktualizowana, aby umożliwić klientowi Dodawanie hiperłączy oraz proste formatowanie, w tym pogrubienie czcionki, podkreślenie i kursywę. Aby uzyskać wskazówki dotyczące korzystania z tej funkcji, zobacz [Dodawanie znakowania do strony logowania Azure Active Directory w organizacji](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="provisioning-performance-improvements"></a>Ulepszenia wydajności aprowizacji

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
Usługa aprowizacji została zaktualizowana, aby skrócić czas trwania [cyklu przyrostowego](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#incremental-cycles) . Oznacza to, że użytkownicy i grupy będą obsługiwani do aplikacji szybciej niż wcześniej. Wszystkie nowe zadania aprowizacji utworzone po 6/10/2020 będą automatycznie korzystać z ulepszeń wydajności. Wszystkie aplikacje skonfigurowane do obsługi administracyjnej przed 6/10/2020 będą musiały zostać ponownie uruchomione po 6/10/2020, aby wykorzystać ulepszenia wydajności. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Zapowiedź zgodności biblioteki ADAL i programu MS Graph

**Typ:** Przestarzałe  
**Kategoria usługi:** NIE DOTYCZY  
**Możliwość produktu:** Zarządzanie cyklem życia urządzeń

Teraz, Azure Active Directory gdy biblioteki uwierzytelniania firmy Microsoft (MSAL) są dostępne, nie będziemy już dodawać nowych funkcji do bibliotek Authentication Library (ADAL) i będą one kończyć się poprawkami zabezpieczeń 30 czerwca 2022. Aby uzyskać więcej informacji na temat migracji do MSAL, zobacz [Migrowanie aplikacji do biblioteki uwierzytelniania firmy Microsoft (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-migration).

Ponadto firma Microsoft zakończyła pracę w celu udostępnienia wszystkich funkcji grafów usługi Azure AD za pomocą programu MS Graph. W związku z tym interfejsy API programu Azure AD Graph będą odbierać wyłącznie poprawka i poprawki zabezpieczeń do 30 czerwca 2022. Aby uzyskać więcej informacji, zobacz [aktualizowanie aplikacji do korzystania z biblioteki uwierzytelniania firmy Microsoft i interfejsu API Microsoft Graph](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
 
---
 
## <a name="may-2020"></a>Maj 2020 r.

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>Wycofanie właściwości w interfejsach API signIns, riskyUsers i riskDetections

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Obecnie wyliczane typy są używane do reprezentowania właściwości ryzyka w interfejsie API riskDetections i riskyUserHistoryItem (w wersji zapoznawczej). Typy wyliczeniowe są również używane dla właściwości riskEventTypes w interfejsie API signIns. Dzięki temu będą one reprezentować te właściwości jako ciągi. 

Klienci powinni przejść do właściwości riskEventType w interfejsie API beta riskDetections i riskyUserHistoryItem oraz do riskEventTypes_v2 właściwości w interfejsie API signIns beta do 9 września 2020. W tej chwili będziemy wycofywane bieżące właściwości ryzyka i riskEventTypes. Aby uzyskać więcej informacji, zobacz [zmiany właściwości zdarzeń ryzyka i interfejsów API ochrony tożsamości na Microsoft Graph](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/).

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Przestarzała Właściwość riskEventTypes w interfejsie API signIns v 1.0 na Microsoft Graph

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Typy wyliczeniowe będą przełączane do typów ciągów podczas reprezentowania właściwości zdarzenia ryzyka w Microsoft Graph września 2020. Ta zmiana nie będzie miała wpływu na interfejsy API w wersji zapoznawczej, ale również ma wpływ na interfejs API signIns w środowisku produkcyjnym.

Wprowadzono nową właściwość riskEventsTypes_v2 (String) do interfejsu API signIns 1.0. Bieżąca Właściwość riskEventTypes (enum) zostanie wycofana w dniu 11 czerwca 2022 zgodnie z naszymi zasadami dotyczącymi przestarzałych Microsoft Graph. Klienci powinni przejść do właściwości riskEventTypes_v2 w interfejsie API signIns v 1.0 do 11 czerwca 2022. Aby uzyskać więcej informacji, zapoznaj się z tematem [przestarzałe właściwości riskEventTypes w interfejsie API signIns v 1.0 na Microsoft Graph](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//).

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>Nadchodzące zmiany powiadomień e-mail usługi MFA

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Funkcja  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 

Wprowadzamy następujące zmiany powiadomień e-mail dotyczących usługi MFA w chmurze:

Powiadomienia E-mail będą wysyłane z następującego adresu: azure-noreply@microsoft.com i msonlineservicesteam@microsoftonline.com . Aktualizujemy zawartość wiadomości e-mail z alertami o oszustwie, aby lepiej wskazać wymagane kroki w celu odblokowania użycia.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Nowe konto samoobsługi dla użytkowników w domenach federacyjnych, które nie mogą uzyskać dostępu do programu Microsoft Teams, ponieważ nie są synchronizowane z usługą Azure Active Directory.

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 

Obecnie użytkownicy, którzy znajdują się w domenach federacyjnych w usłudze Azure AD, ale nie są synchronizowani do dzierżawy, nie mogą uzyskać dostępu do zespołów. Począwszy od końca czerwca, ta nowa funkcja umożliwi im to przez rozszerzenie istniejącej, zweryfikowanej funkcji rejestracji w wiadomości e-mail. Pozwoli to użytkownikom, którzy mogą zalogować się do dostawcy tożsamości federacyjnego, ale którzy nie mają jeszcze obiektu użytkownika w usłudze Azure ID, aby obiekt użytkownika został utworzony automatycznie i być uwierzytelniany dla zespołów. Ich obiekt użytkownika zostanie oznaczony jako "samoobsługowe rejestrowanie się". Jest to rozszerzenie istniejącej możliwości do zweryfikowania się samoobsługowego rejestrowania wiadomości e-mail przez użytkowników w domenach zarządzanych, które można kontrolować przy użyciu tej samej flagi. Ta zmiana zostanie ukończona w ciągu następnych dwóch miesięcy. [Tutaj](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup)Obejrzyj aktualizacje dokumentacji.
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Nadchodząca Poprawka: dokument odnajdywania OIDC dla chmury Azure Government jest aktualizowany w celu odwoływania się do prawidłowych punktów końcowych grafu.

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Suwerenne chmury  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Począwszy od czerwca, OIDC dokument odnajdywania [Microsoft Identity platform i OpenID Connect Connect](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) w punkcie końcowym usługi [Azure Government Cloud](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) (login.microsoftonline.us), zacznie zwracać prawidłowy Narodowy punkt końcowy [grafu w chmurze](https://docs.microsoft.com/graph/deployments) ( https://graph.microsoft.us lub https://dod-graph.microsoft.us) na podstawie podanej dzierżawy.  W tej chwili jest dostępne nieprawidłowe pole punktu końcowego grafu (graph.microsoft.com) "msgraph_host".  

Ta poprawka błędu zostanie przeprowadzona stopniowo przez około 2 miesiące.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government użytkownicy nie będą już mogli logować się w usłudze login.microsoftonline.com

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Suwerenne chmury  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
1 czerwca 2018 Urząd oficjalnych Azure Active Directory (AAD) dla Azure Government zmieniony z https://login-us.microsoftonline.com na https://login.microsoftonline.us . Jeśli jesteś właocicielem aplikacji w ramach dzierżawy Azure Government, musisz zaktualizować aplikację, aby zalogować użytkowników w punkcie końcowym. us.

Począwszy od 5 maja, usługa Azure AD zacznie wymuszać zmianę punktu końcowego, blokując Azure Government użytkownikom rejestrowanie się w aplikacjach hostowanych w Azure Government dzierżawach przy użyciu publicznego punktu końcowego (microsoftonline.com). Aplikacje, których dotyczy problem, rozpoczną wyświetlanie błędu AADSTS900439-USGClientNotSupportedOnPublicEndpoint. 

Nastąpi stopniowe wprowadzanie tej zmiany z oczekiwaną ukończeniem wymuszania we wszystkich aplikacjach z czerwca 2020. Aby uzyskać więcej informacji, zobacz [wpis w blogu Azure Government](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/).

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>Żądanie wylogowania jednokrotnego SAML teraz wysyła NameID w prawidłowym formacie

**Typ:** FIXED  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Gdy użytkownik kliknie opcję wylogowania (np. w portalu aplikacji), usługa Azure AD wyśle komunikat o wylogowaniu jednokrotnym SAML do każdej aplikacji, która jest aktywna w sesji użytkownika i ma skonfigurowany adres URL wylogowywania. Te komunikaty zawierają NameID w formacie trwałym.

Jeśli oryginalny token logowania przy użyciu protokołu SAML używał innego formatu dla NameID (np. poczty e-mail/UPN), aplikacja SAML nie może skorelować NameID w komunikacie o wylogowaniu do istniejącej sesji (ponieważ NameIDs użyta w obu komunikatach jest inna), co spowodowało odrzucenie komunikatu o wylogowaniu przez aplikację SAML i zalogowanie użytkownika. Ta poprawka powoduje, że komunikat o wylogowaniu jest spójny z NameID skonfigurowanym dla aplikacji.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>Rola administratora tożsamości hybrydowej jest teraz dostępna z obsługą chmury

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi chmury usługi Azure AD  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
Administratorzy IT mogą rozpocząć korzystanie z nowej roli "Administrator hybrydowy" jako najmniejszej roli uprzywilejowanej na potrzeby konfigurowania aprowizacji usługi Azure ADConnect Cloud. W przypadku tej nowej roli nie trzeba już używać roli administratora globalnego do konfigurowania i konfigurowania aprowizacji w chmurze. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-by-task#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — maj 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W maju 2020 dodaliśmy 36 następujące nowe aplikacje w galerii aplikacji z obsługą Federacji:

[Moula](https://moula.com.au/pay/merchants), [Surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox](http://www.tacklebox.app/), [Powell Teams](https://powell-software.com/en/powell-teams-en/), [Talentsoft Assistant](https://msteams.talent-soft.com/), [rejestrowanie szczegółowych](https://teams.asc-recording.app/product)informacji, [askSpoke](https://docs.microsoft.com/azure/active-directory/saas-apps/askspoke-tutorial) [GO1](https://www.go1.com/), [B-zaangażowanych](https://b-engaged.se/), [Competella Center contacter](http://www.competella.com/), [Asite](http://www.asite.com/), [ImageSoft Identity](https://identity.imagesoftinc.com/) [My IBISWorld](https://identity.imagesoftinc.com/), [insuiteal](https://docs.microsoft.com/azure/active-directory/saas-apps/insuite-tutorial), [Change Process Management](https://docs.microsoft.com/azure/active-directory/saas-apps/change-process-management-tutorial), [Cyaraa](https://docs.microsoft.com/azure/active-directory/saas-apps/cyara-cx-assurance-platform-tutorial) [Prezi](https://docs.microsoft.com/azure/active-directory/saas-apps/prezi-tutorial), [Anyone Home CRM](https://docs.microsoft.com/azure/active-directory/saas-apps/anyone-home-crm-tutorial) [Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/axiad-cloud-tutorial) [Globally](https://docs.microsoft.com/azure/active-directory/saas-apps/smart-global-governance-tutorial), [MapBox](https://docs.microsoft.com/azure/active-directory/saas-apps/mapbox-tutorial), [Datava Enterprise Service Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/datava-enterprise-service-platform-tutorial), [Whimsical](https://docs.microsoft.com/azure/active-directory/saas-apps/whimsical-tutorial), [Trelica](https://docs.microsoft.com/azure/active-directory/saas-apps/trelica-tutorial), [EasySSO dla Confluence](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-confluence-tutorial), [EasySSO dla BitBucket](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bitbucket-tutorial), [EasySSO](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bamboo-tutorial), [Bamboo, Torii, Axiad](https://docs.microsoft.com/azure/active-directory/saas-apps/torii-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/vyond-tutorial) [ColorTokens, ZTNA](https://docs.microsoft.com/azure/active-directory/saas-apps/humanage-tutorial) [CCH,](https://docs.microsoft.com/azure/active-directory/saas-apps/colortokens-ztna-tutorial) [Tagetik ShareVault](https://docs.microsoft.com/azure/active-directory/saas-apps/cch-tagetik-tutorial) [, Vyond](https://docs.microsoft.com/azure/active-directory/saas-apps/sharevault-tutorial) [TextExpander](https://docs.microsoft.com/azure/active-directory/saas-apps/textexpander-tutorial) [ice Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/ice-contact-center-tutorial)

W tym miejscu możesz również znaleźć dokumentację dotyczącą wszystkich aplikacji https://aka.ms/AppsTutorial .

Aby wyświetlić listę aplikacji w galerii aplikacji usługi Azure AD, zapoznaj się ze szczegółowymi informacjami w tym miejscu https://aka.ms/AzureADAppRequest .

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Tryb tylko do raportowania dla dostępu warunkowego jest teraz ogólnie dostępny

**Typ:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

[Tryb tylko do raportowania dla dostępu warunkowego usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) pozwala oszacować wynik zasad bez wymuszania kontroli dostępu. Można testować zasady dotyczące tylko raportów w organizacji i zrozumieć ich wpływ przed ich włączeniem, zapewniając bezpieczniejsze i łatwiejsze wdrażanie. W ciągu ostatnich kilku miesięcy zaobserwowano silne wdrożenie trybu tylko do raportowania — ponad 26M użytkownicy są już w zasięgu zasad opartych na raportach. Dzięki ogłoszeniu dzisiaj nowe zasady dostępu warunkowego usługi Azure AD zostaną domyślnie utworzone w trybie tylko do raportowania. Oznacza to, że można monitorować wpływ zasad od momentu ich tworzenia. W przypadku użytkowników korzystających z interfejsów API programu MS Graph można również [zarządzać zasadami tylko do raportowania](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) . 

---

### <a name="self-service-sign-up-for-guest-users"></a>Rejestracja samoobsługowa dla użytkowników-Gości

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C
 
Tożsamości zewnętrzne w usłudze Azure AD umożliwiają osobom spoza organizacji dostęp do Twoich aplikacji i zasobów przy użyciu dowolnej tożsamości, którą preferują. W przypadku udostępniania aplikacji użytkownikom zewnętrznym użytkownik może nie zawsze z góry znać, kto będzie potrzebować dostępu do aplikacji. Korzystając z samoobsługowego [rejestrowania](https://docs.microsoft.com/azure/active-directory/b2b/self-service-sign-up-overview), możesz umożliwić użytkownikom-Gościom rejestrowanie się i uzyskiwanie konta gościa dla aplikacji biznesowych. Przepływ rejestracji można utworzyć i dostosować do obsługi tożsamości usług Azure AD i społecznościowych. Możesz również zbierać dodatkowe informacje o użytkowniku podczas tworzenia konta.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Szczegółowe informacje o dostępie warunkowym i skoroszycie raportów są ogólnie dostępne

**Typ:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

[Skoroszyt usługi Insights i raportowanie](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) zapewnia administratorom Widok podsumowania dostępu warunkowego w usłudze Azure AD w swojej dzierżawie. Korzystając z możliwości wybierania poszczególnych zasad, Administratorzy mogą lepiej zrozumieć, jakie zasady są wykonywane i monitorują zmiany w czasie rzeczywistym. Skoroszyt strumieniuje dane przechowywane w Azure Monitor, które można skonfigurować w kilka minut, postępując zgodnie z [tymi instrukcjami](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics). Aby pulpit nawigacyjny był bardziej wykrywalny, został przeniesiony na nowe informacje i kartę Raportowanie w menu dostęp warunkowy usługi Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Blok Szczegóły zasad dostępu warunkowego jest w publicznej wersji zapoznawczej

**Typ:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

W [bloku szczegóły nowego zasad](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) są wyświetlane przydziały, warunki i kontrolki spełnione podczas obliczania zasad dostępu warunkowego. Możesz uzyskać dostęp do bloku, zaznaczając wiersz na kartach dostęp warunkowy lub tylko raport w obszarze szczegóły logowania.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Nowe możliwości zapytania dotyczące obiektów katalogu w Microsoft Graph są w publicznej wersji zapoznawczej

**Typ:** Nowa funkcja  
**Kategoria usługi:** **Funkcja produktu** MS Graph: środowisko programistyczne

Wprowadzane są nowe możliwości dla Microsoft Graph interfejsów API obiektów usługi Active Directory, Włączanie operacji count, Search, Filter i Sort. Dzięki temu deweloperzy mogą szybko wykonywać zapytania dotyczące obiektów katalogu bez obejścia takich rozwiązań, jak filtrowanie i sortowanie w pamięci. Dowiedz się więcej w tym [wpisie w blogu](https://aka.ms/CountFilterMSGraphAAD).

Obecnie w publicznej wersji zapoznawczej szukasz informacji zwrotnych. Wyślij komentarze do tej [krótkiej ankiety](https://aka.ms/MsGraphAADSurveyDocs).

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Konfigurowanie logowania jednokrotnego opartego na protokole SAML przy użyciu interfejsu API Microsoft Graph (beta)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ
 
Obsługa tworzenia i konfigurowania aplikacji z galerii usługi Azure AD przy użyciu interfejsów API programu MS Graph w wersji beta jest teraz dostępna. Jeśli konieczne jest skonfigurowanie logowania jednokrotnego opartego na protokole SAML dla wielu wystąpień aplikacji, należy zaoszczędzić czas przy użyciu Microsoft Graph interfejsów API, aby [zautomatyzować konfigurację logowania jednokrotnego opartego na protokole SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Nowe łączniki aprowizacji w galerii aplikacji usługi Azure AD — maj 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwości produktu:** integracja innej firmy
 
Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

* [8x8](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8-provisioning-tutorial)
* [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-provisioning-tutorial)
* [MediusFlow](https://docs.microsoft.com/azure/active-directory/saas-apps/mediusflow-provisioning-tutorial)
* [New Relic by Organization](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-by-organization-provisioning-tutorial)
* [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzacja aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="saml-token-encryption-is-generally-available"></a>Szyfrowanie tokenów SAML jest ogólnie dostępne

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ
 
[Szyfrowanie tokenów SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption) umożliwia aplikacjom Konfigurowanie do odbierania szyfrowanych zatwierdzeń SAML. Ta funkcja jest teraz ogólnie dostępna we wszystkich chmurach.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Oświadczenia nazw grup w tokenach aplikacji są ogólnie dostępne

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ
 
Oświadczenia grupy wystawione w tokenie mogą teraz być ograniczone do tylko tych grup przypisanych do aplikacji.  Jest to szczególnie ważne, gdy użytkownicy są członkami dużej liczby grup, a wystąpiło ryzyko przekroczenia limitów rozmiaru tokenu. Dzięki tej nowej funkcji możliwość [dodawania nazw grup do tokenów](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims) jest ogólnie dostępna.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Funkcja zapisywania zwrotnego dla produktu Workday obsługuje teraz ustawianie atrybutów numeru telefonu służbowego

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
Ulepszono aplikację obsługi funkcji zapisywania zwrotnego dla produktu Workday, która obsługuje teraz Stornowanie atrybutów numeru telefonu służbowego i numeru komórkowego. Oprócz poczty e-mail i nazwy użytkownika można teraz skonfigurować aplikację do obsługi funkcji zapisywania zwrotnego dla programu Workday, aby przepływać wartości numerów telefonów z usługi Azure AD do produktu Workday. Aby uzyskać więcej informacji na temat konfigurowania zapisywania zwrotnego numerów telefonów, zapoznaj się z samouczkiem dotyczącym aplikacji [zapisywania zwrotnego dla produktu Workday](https://aka.ms/WorkdayWriteback) . 

---

### <a name="publisher-verification-preview"></a>Weryfikacja wydawcy (wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Środowisko deweloperskie
 
Weryfikacja wydawcy (wersja zapoznawcza) pomaga administratorom i użytkownikom końcowym zrozumieć autentyczność deweloperów aplikacji integrujących się z platformą tożsamości firmy Microsoft. Aby uzyskać szczegółowe informacje, zobacz [weryfikacja wydawcy (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/develop/publisher-verification-overview).
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Przepływ kodu autoryzacji dla aplikacji jednostronicowych

**Typ:** Zmieniono **kategorię usługi funkcji:** uwierzytelnianie **możliwości produktu:** środowisko programistyczne

Ze względu na to, że istnieją [ograniczenia dotyczące plików cookie innych firm, takie jak Safari itp](https://docs.microsoft.com/azure/active-directory/develop/reference-third-party-cookies-spas), Usługa aplikacji jednostronicowych będzie musiała używać przepływu kodu autoryzacji zamiast przepływu niejawnego do obsługi logowania jednokrotnego. MSAL.js v 2. x będzie teraz obsługiwał przepływ kodu autoryzacji. Istnieją odpowiednie aktualizacje Azure Portal, aby można było zaktualizować SPA w taki sposób, aby były typu "Spa" i używają przepływu kodu uwierzytelniania. Aby uzyskać wskazówki, zobacz [Przewodnik Szybki Start: Logowanie użytkowników i uzyskiwanie tokenu dostępu w usłudze JavaScript Spa przy użyciu przepływu kodu uwierzytelniania](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript-auth-code).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Ulepszone filtrowanie urządzeń jest w publicznej wersji zapoznawczej

**Typ:** Zmieniono funkcję   
**Kategoria usługi:** **Możliwości produktu** zarządzanie urządzeniami: Zarządzanie cyklem życia urządzeń
 
Wcześniej jedyne filtry, których można użyć, to "Enabled" i "Data działania". Teraz można [filtrować listę urządzeń, aby uzyskać więcej właściwości, w](https://docs.microsoft.com/azure/active-directory/devices/device-management-azure-portal#device-list-filtering-preview)tym typ systemu operacyjnego, typ sprzężenia, zgodność i inne. Te dodatki powinny uprościć lokalizowanie konkretnego urządzenia.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>Nowe Rejestracje aplikacji środowisko Azure AD B2C jest teraz ogólnie dostępne

**Typ:** Zmieniono funkcję   
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
Nowe Rejestracje aplikacji środowisko Azure AD B2C jest teraz ogólnie dostępne. 

Wcześniej trzeba było zarządzać aplikacjami dostępnymi dla użytkowników B2C niezależnie od reszty aplikacji przy użyciu starszego środowiska "aplikacje". Są to różne środowiska tworzenia aplikacji w różnych miejscach na platformie Azure.

Nowe środowisko przedstawia wszystkie rejestracje aplikacji B2C i rejestracje aplikacji usługi Azure AD w jednym miejscu i zapewnia spójny sposób zarządzania nimi. Bez względu na to, czy chcesz zarządzać aplikacją dostępną dla klienta, czy aplikacją, która jest w Microsoft Graph, aby programowo zarządzać zasobami Azure AD B2C, musisz tylko poznać jeden ze sposobów.

Aby uzyskać dostęp do nowego środowiska, przejdź do usługi Azure AD B2C i wybierz blok Rejestracje aplikacji. Środowisko jest również dostępne w usłudze Azure Active Directory.

Środowisko Rejestracje aplikacji Azure AD B2C jest oparte na ogólnej [rejestracji aplikacji](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) dla dzierżawców usługi Azure AD, ale jest dostosowany do Azure AD B2C. Starsze środowisko "aplikacje" będzie przestarzałe w przyszłości.

Aby uzyskać więcej informacji, odwiedź [nowe środowisko rejestracji aplikacji dla Azure AD B2C](https://aka.ms/b2cappregtraining).

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

Ciągła ocena dostępu to nowa funkcja zabezpieczeń, która umożliwia niemal w czasie rzeczywistym egzekwowanie zasad dotyczących jednostek uzależnionych korzystających z tokenów dostępu usługi Azure AD w przypadku wystąpienia zdarzeń w usłudze Azure AD (takich jak usuwanie konta użytkownika). Ta funkcja jest najpierw wycofywana dla zespołów i klientów programu Outlook. Aby uzyskać więcej informacji, Przeczytaj nasz [blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) i  [dokumentację](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation).

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

**Kategoria usługi:** Role usługi Azure AD

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

**Kategoria usługi:** Role usługi Azure AD

**Możliwość produktu:** Access Control

**Administrator drukarki**: Użytkownicy z tą rolą mogą rejestrować drukarki i zarządzać wszystkimi aspektami wszystkich konfiguracji drukarek w rozwiązaniu uniwersalnego drukowania firmy Microsoft, w tym ustawieniami uniwersalnego łącznika wydruku. Mogą oni wyrazić zgodę na wszystkie delegowane żądania uprawnień do drukowania. Administratorzy drukarek mają również dostęp do raportów drukowania. 

**Technika drukowania**: Użytkownicy z tą rolą mogą rejestrować drukarki i zarządzać stanem drukarki w uniwersalnym rozwiązaniu firmy Microsoft. Mogą również odczytywać wszystkie informacje o łączniku. Najważniejsze zadania nie można ustawić uprawnień użytkowników na drukarkach i udostępniających drukarki. [Dowiedz się więcej.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Wbudowana rola administratora tożsamości hybrydowej

**Typ:** Nowa funkcja

**Kategoria usługi:** Role usługi Azure AD

**Możliwość produktu:** Access Control

Użytkownicy w tej roli mogą włączać i konfigurować usługi oraz ustawienia związane z włączaniem tożsamości hybrydowej w usłudze Azure AD oraz zarządzać nimi. Ta rola zapewnia możliwość skonfigurowania usługi Azure AD na jednej z trzech obsługiwanych metod uwierzytelniania&#8212;synchronizacji skrótów haseł (PHS), uwierzytelniania przekazywanego (PTA) lub Federacji (AD FS lub dostawcy Federacji innej firmy) &#8212;oraz do wdrożenia powiązanej infrastruktury lokalnej w celu jej włączenia. Infrastruktura lokalna obejmuje agentów aprowizacji i PTA. Ta rola zapewnia możliwość włączenia bezproblemowego logowania jednokrotnego (SSO) w celu zapewnienia bezproblemowego uwierzytelniania na urządzeniach z systemem innym niż Windows 10 lub komputerach z systemem innym niż Windows Server 2016. Ponadto ta rola zapewnia możliwość wyświetlania dzienników logowania i uzyskiwania dostępu do kondycji i analizy w celu monitorowania i rozwiązywania problemów. [Dowiedz się więcej.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Wbudowana rola administratora sieci

**Typ:** Nowa funkcja

**Kategoria usługi:** Role usługi Azure AD

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