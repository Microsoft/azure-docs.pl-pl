---
title: Wbudowane role usługi Azure AD — Azure Active Directory
description: Opisuje Azure Active Directory wbudowanych ról i uprawnień.
services: active-directory
author: rolyon
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 04/20/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf614922503212488c822ac020960b0ddb99fc28
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780069"
---
# <a name="azure-ad-built-in-roles"></a>Role wbudowane usługi Azure AD

Jeśli Azure Active Directory (Azure AD) inny administrator lub inny administrator musi zarządzać zasobami usługi Azure AD, należy przypisać im rolę usługi Azure AD, która zapewnia wymagane uprawnienia. Można na przykład przypisywać role, aby umożliwić dodawanie lub zmienianie użytkowników, resetowanie haseł użytkowników, zarządzanie licencjami użytkowników lub zarządzanie nazwami domen.

W tym artykule wymieniono wbudowane role usługi Azure AD, które można przypisać w celu umożliwienia zarządzania zasobami usługi Azure AD. Aby uzyskać informacje na temat sposobu przypisywania ról, zobacz [Przypisywanie ról usługi Azure AD do użytkowników.](manage-roles-portal.md)

## <a name="all-roles"></a>Wszystkie role

> [!div class="mx-tableFixed"]
> | Rola | Opis | Identyfikator szablonu |
> | --- | --- | --- |
> | [Administrator aplikacji](#application-administrator) | Może tworzyć wszystkie aspekty rejestracji aplikacji i aplikacji przedsiębiorstwa oraz zarządzać nimi. | 9b895d92-2cd3-44c7-9d02-a6ac2d5ea5c3 |
> | [Deweloper aplikacji](#application-developer) | Może tworzyć rejestracje aplikacji niezależnie od ustawienia "Użytkownicy mogą rejestrować aplikacje". | cf1c38e5-3621-4004-a7cb-879624dced7c |
> | [Autor ładunku ataku](#attack-payload-author) | Może tworzyć ładunki ataków, które administrator może zainicjować później. | 9c6df0f2-1e7c-4dc3-b195-66dfbd24aa8f |
> | [Administrator symulacji ataku](#attack-simulation-administrator) | Może tworzyć wszystkie aspekty kampanii symulacji ataku i zarządzać nimi. | c430b396-e693-46cc-96f3-db01bf8bb62a |
> | [Administrator uwierzytelniania](#authentication-administrator) | Może wyświetlać, ustawiać i resetować informacje o metodzie uwierzytelniania dla dowolnego użytkownika bez uprawnień administratora. | c4e39bd9-1100-46d3-8c65-fb160da0071f |
> | [Administrator zasad uwierzytelniania](#authentication-policy-administrator) | Może tworzyć wszystkie aspekty metod uwierzytelniania i zasad ochrony haseł oraz zarządzać nimi. | 0526716b-113d-4c15-b2c8-68e3c22b9f80 |
> | [Administrator lokalny urządzenia przyłączonego do usługi Azure AD](#azure-ad-joined-device-local-administrator) | Użytkownicy przypisani do tej roli są dodawana do lokalnej grupy administratorów na urządzeniach przyłączonych do usługi Azure AD. | 9f06204d-73c1-4d4c-880a-6edb90606fd8 |
> | [Azure DevOps administratorem](#azure-devops-administrator) | Może zarządzać Azure DevOps i ustawieniami organizacji. | e3973bdf-4987-49ae-837a-ba8e231c7286 |
> | [Azure Information Protection administratora](#azure-information-protection-administrator) | Może zarządzać wszystkimi aspektami Azure Information Protection produktu. | 7495fdc4-34c4-4d15-a289-98788ce399fd |
> | [Administrator zestawu kluczy IEF B2C](#b2c-ief-keyset-administrator) | Może zarządzać wpisami tajnymi federacji i szyfrowania w Identity Experience Framework (IEF). | aaf43236-0c0d-4d5f-883a-6955382ac081 |
> | [Administrator zasad IEF B2C](#b2c-ief-policy-administrator) | Może tworzyć zasady struktury zaufania i zarządzać nimi w Identity Experience Framework (IEF). | 3edaf663-341e-4475-9f94-5c398ef6c070 |
> | [Administrator rozliczeń](#billing-administrator) | Może wykonywać typowe zadania związane z rozliczeniami, takie jak aktualizowanie informacji o płatności. | b0f54661-2d74-4c50-afa3-1ec803f12efe |
> | [Administrator aplikacji w chmurze](#cloud-application-administrator) | Może tworzyć wszystkie aspekty rejestracji aplikacji i aplikacji przedsiębiorstwa oraz zarządzać nimi z wyjątkiem serwera proxy aplikacji. | 158c047a-c907-4556-b7ef-446551a6b5f7 |
> | [Administrator urządzenia w chmurze](#cloud-device-administrator) | Ograniczony dostęp do zarządzania urządzeniami w usłudze Azure AD. | 7698a772-787b-4ac8-901f-60d6b08affd2 |
> | [Administrator zgodności](#compliance-administrator) | Może odczytywać konfigurację zgodności i raporty oraz zarządzać nimi w usłudze Azure AD i Microsoft 365. | 17315797-102d-40b4-93e0-432062ca18 |
> | [Administrator danych zgodności](#compliance-data-administrator) | Tworzy zawartość zgodności i zarządza nimi. | e6d1a23a-da11-4be4-9570-befc86d067a7 |
> | [Administrator dostępu warunkowego](#conditional-access-administrator) | Może zarządzać możliwościami dostępu warunkowego. | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9 |
> | [Osoba zatwierdzająca dostęp do skrytki klienta](#customer-lockbox-access-approver) | Może zatwierdzać żądania pomocy technicznej firmy Microsoft w celu uzyskania dostępu do danych organizacji klienta. | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91 |
> | [Desktop Analytics administratora](#desktop-analytics-administrator) | Może uzyskać dostęp do narzędzi i usług do zarządzania pulpitem oraz zarządzać nimi. | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4 |
> | [Czytniki katalogów](#directory-readers) | Może odczytywać podstawowe informacje o katalogu. Często używane do udzielania dostępu do odczytu katalogu aplikacjom i gościom. | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b |
> | [Konta synchronizacji katalogów](#directory-synchronization-accounts) | Używane tylko przez Azure AD Connect usługi. | d29b2b05-8046-44ba-8758-1e26182fcf32 |
> | [Twórcy katalogów](#directory-writers) | Może odczytywać i zapisywać podstawowe informacje o katalogu. Do udzielania dostępu do aplikacji, które nie są przeznaczone dla użytkowników. | 9360feb5-f418-4baa-8175-e2a00bac4301 |
> | [Administrator nazwy domeny](#domain-name-administrator) | Może zarządzać nazwami domen w chmurze i lokalnie. | 8329153b-31d0-4727-b945-745eb3bc5f31 |
> | [Dynamics 365 Administrator](#dynamics-365-administrator) | Może zarządzać wszystkimi aspektami produktu Dynamics 365. | 44367163-eba1-44c3-98af-f5787879f96a |
> | [Exchange Administrator](#exchange-administrator) | Może zarządzać wszystkimi aspektami produktu Exchange. | 29232cdf-9323-42fd-ade2-1d097af3e4de |
> | [Administrator adresata programu Exchange](#exchange-recipient-administrator) | Może tworzyć lub aktualizować adresatów usługi Exchange Online w organizacji usługi Exchange Online. | 31392ffb-586c-42d1-9346-e59415a2cc4e |
> | [Administrator przepływu użytkownika zewnętrznego identyfikatora](#external-id-user-flow-administrator) | Może tworzyć wszystkie aspekty przepływów użytkowników i zarządzać nimi. | 6e591065-9bad-43ed-90f3-e9424366d2f0 |
> | [Administrator atrybutów przepływu użytkownika zewnętrznego identyfikatora](#external-id-user-flow-attribute-administrator) | Może tworzyć schemat atrybutów dostępny dla wszystkich przepływów użytkownika i zarządzać nimi. | 0f971eea-41eb-4569-a71e-57bb8a3eff1e |
> | [Administrator zewnętrznego dostawcy tożsamości](#external-identity-provider-administrator) | Może konfigurować dostawców tożsamości do użycia w federacji bezpośredniej. | be2f45a1-457d-42af-a067-6ec1fa63bc45 |
> | [Administrator globalny](#global-administrator) | Może zarządzać wszystkimi aspektami usługi Azure AD i usługi firmy Microsoft które korzystają z tożsamości usługi Azure AD. | 62e90394-69f5-4237-9190-012177145e10 |
> | [Czytelnik globalny](#global-reader) | Może odczytywać wszystko, co administrator globalny może, ale nie może niczego aktualizować. | f2ef992c-3afb-46b9-b7cf-a126ee74c451 |
> | [Administrator grup](#groups-administrator) | Członkowie tej roli mogą tworzyć grupy i zarządzać nimi, tworzyć ustawienia grup i zarządzać nimi, takie jak zasady nazewnictwa i wygasania, a także wyświetlać działania grup i raporty inspekcji. | fdd7a751-b60b-444a-984c-02652fe8fa1c |
> | [Osoby zapraszane gości](#guest-inviter) | Może zapraszać użytkowników-gości niezależnie od ustawienia "Członkowie mogą zapraszać gości". | 95e79109-95c0-4d8e-aee3-d01accf2d47b |
> | [Administrator pomocy technicznej](#helpdesk-administrator) | Może resetować hasła dla użytkowników niebędących administratorami i administratorów pomocy technicznej. | 729827e3-9c14-49f7-bb1b-9608f156bbb8 |
> | [Administrator tożsamości hybrydowej](#hybrid-identity-administrator) | Może zarządzać aprowizowania usług AD w chmurze, Azure AD Connect i ustawień federacji. | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2 |
> | [Administrator szczegółowych informacji](#insights-administrator) | Ma dostęp administracyjny w aplikacji Microsoft 365 Insights. | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c |
> | [Insights Business Leader](#insights-business-leader) | Może wyświetlać i udostępniać pulpity nawigacyjne i szczegółowe informacje za pośrednictwem aplikacji M365 Insights. | 31e939ad-9672-4796-9c2e-873181342d2d |
> | [Intune Administrator](#intune-administrator) | Może zarządzać wszystkimi aspektami produktu Intune. | 3a2c62db-5318-420d-8d74-23affee5d9d5 |
> | [Kaizala Administrator](#kaizala-administrator) | Może zarządzać ustawieniami usługi Microsoft Kaizala. | 74ef975b-6605-40af-a5d2-b9539d836353 |
> | [Administrator wiedzy](#knowledge-administrator) | Może konfigurować wiedzę, uczenie się i inne inteligentne funkcje. | b5a8dcf3-09d5-43a9-a639-8e29ef291470 |
> | [Administrator licencji](#license-administrator) | Może zarządzać licencjami produktów dla użytkowników i grup. | 4d6ac14f-3453-41d0-bef9-a3e0c569773a |
> | [Centrum wiadomości czytelnika prywatności](#message-center-privacy-reader) | Może odczytywać tylko komunikaty i aktualizacje zabezpieczeń Centrum wiadomości Office 365. | ac16e43d-7b2d-40e0-ac05-243ff356ab5b |
> | [Centrum wiadomości czytelnika](#message-center-reader) | Może odczytywać wiadomości i aktualizacje dla swojej organizacji tylko w usłudze Office 365 Centrum wiadomości tylko. | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b |
> | [Nowoczesny użytkownik handlowy](#modern-commerce-user) | Może zarządzać zakupami komercyjnymi dla firmy, działu lub zespołu. | d24aef57-1500-4070-84db-2666f29cf966 |
> | [Administrator sieci](#network-administrator) | Może zarządzać lokalizacjami sieci i przeglądać szczegółowe informacje dotyczące projektowania sieci Microsoft 365 oprogramowania jako usługi. | d37c8bed-0711-4417-ba38-b4abe66ce4c2 |
> | [Administrator aplikacji pakietu Office](#office-apps-administrator) | Może zarządzać usługami w chmurze aplikacji pakietu Office, w tym zarządzaniem zasadami i ustawieniami, a także zarządzać możliwością wybierania, usuwania zaznaczenia i publikowania zawartości funkcji "co nowego" na urządzeniach użytkowników końcowych. | 2b745bdf-0803-4d80-aa65-822c4493daac |
> | [Pomoc techniczna dla partnerów w warstwie 1](#partner-tier1-support) | Nie używaj — nie jest przeznaczona do użytku ogólnego. | 4ba39ca4-527c-499a-b93d-d9b492c50246 |
> | [Pomoc techniczna dla partnerów w warstwie 2](#partner-tier2-support) | Nie używaj — nie jest przeznaczona do użytku ogólnego. | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8 |
> | [Administrator haseł](#password-administrator) | Może resetować hasła dla użytkowników niebędących administratorami i administratorów haseł. | 966707d0-3269-4727-9be2-8c3a10f19b9d |
> | [Power BI administratora](#power-bi-administrator) | Może zarządzać wszystkimi aspektami Power BI produktu. | a9ea8996-122f-4c74-9520-8edcd192826c |
> | [Power Platform administratora](#power-platform-administrator) | Może tworzyć wszystkie aspekty usług Microsoft Dynamics 365, PowerApps i Microsoft Flow oraz zarządzać nimi. | 11648597-926c-4cf3-9c36-bcebb0ba8dcc |
> | [Administrator drukarki](#printer-administrator) | Może zarządzać wszystkimi aspektami drukarek i łączników drukarek. | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f |
> | [Technik drukarek](#printer-technician) | Może rejestrować i wyrejestrować drukarki oraz aktualizować stan drukarki. | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477 |
> | [Administrator uwierzytelniania uprzywilejowanego](#privileged-authentication-administrator) | Może wyświetlać, ustawiać i resetować informacje o metodzie uwierzytelniania dla dowolnego użytkownika (administratora lub bez administratora). | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13 |
> | [Administrator ról uprzywilejowanych](#privileged-role-administrator) | Może zarządzać przypisaniami ról w usłudze Azure AD i wszystkimi aspektami Privileged Identity Management. | e8611ab8-c189-46e8-94e1-60213ab1f814 |
> | [Czytelnik raportów](#reports-reader) | Może odczytywać raporty logowania i inspekcji. | 4a5d8f65-41da-4de4-8968-e035b65339cf |
> | [Administrator wyszukiwania](#search-administrator) | Może tworzyć wszystkie aspekty ustawień usługi Microsoft Search i zarządzać nimi. | 0964bb5e-9bdb-4d7b-ac29-58e794862a40 |
> | [Edytor wyszukiwania](#search-editor) | Może tworzyć zawartość redakcyjną, taką jak zakładki, pytania i odpowiedzi, lokalizacje i plan podłóg, oraz zarządzać nimi. | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9 |
> | [Administrator zabezpieczeń](#security-administrator) | Może odczytywać informacje o zabezpieczeniach i raporty oraz zarządzać konfiguracją w usługach Azure AD i Office 365. | 194ae4cb-b126-40b2-bd5b-6091b380977d |
> | [Operator zabezpieczeń](#security-operator) | Tworzy zdarzenia zabezpieczeń i zarządza nimi. | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f |
> | [Czytelnik zabezpieczeń](#security-reader) | Może odczytywać informacje o zabezpieczeniach i raporty w usługach Azure AD i Office 365. | 5d6b6bb7-de71-4623-b4af-96380a352509 |
> | [Administrator pomocy technicznej usługi](#service-support-administrator) | Może odczytywać informacje o kondycji usługi i zarządzać biletami pomocy technicznej. | f023fd81-a637-4b56-95fd-791ac0226033 |
> | [SharePoint Administrator](#sharepoint-administrator) | Może zarządzać wszystkimi aspektami usługi SharePoint. | f28a1f50-f6e7-4571-818b-6a12f2af6b6c |
> | [Administrator programu Skype dla firm](#skype-for-business-administrator) | Może zarządzać wszystkimi aspektami produktu Skype dla firm. | 75941009-915a-4869-abe7-691bff18279e |
> | [Teams Administrator](#teams-administrator) | Może zarządzać usługą Microsoft Teams. | 69091246-20e8-4a56-aa4d-066075b2a7a8 |
> | [Administrator komunikacji usługi Teams](#teams-communications-administrator) | Może zarządzać funkcjami rozmów i spotkań w usłudze Microsoft Teams. | baf37b3a-610e-45da-9e62-d9d1e5e8914b |
> | [Inżynier pomocy technicznej ds. komunikacji usługi Teams](#teams-communications-support-engineer) | Może rozwiązywać problemy z komunikacją w aplikacji Teams przy użyciu zaawansowanych narzędzi. | f70938a0-fc10-4177-9e90-2178f8765737 |
> | [Specjalista ds. pomocy technicznej ds. komunikacji z zespołami](#teams-communications-support-specialist) | Może rozwiązywać problemy z komunikacją w aplikacji Teams przy użyciu podstawowych narzędzi. | fcf91098-03e3-41a9-b5ba-6f0ec8188a12 |
> | [Administrator urządzeń usługi Teams](#teams-devices-administrator) | Może wykonywać zadania związane z zarządzaniem na certyfikowanych urządzeniach usługi Teams. | 3d762c5a-1b6c-493f-843e-55a3b42923d4 |
> | [Czytelnik raportów podsumowania użycia](#usage-summary-reports-reader) | W analizie użycia i wyniku produktywności można Microsoft 365 tylko agregacje na poziomie dzierżawy. | 75934031-6c7e-415a-99d7-48dbd49e875e |
> | [Administrator użytkowników](#user-administrator) | Może zarządzać wszystkimi aspektami użytkowników i grup, w tym resetować hasła dla ograniczonych administratorów. | fe930be7-5e62-47db-91af-98c3a49a38b1 |

## <a name="application-administrator"></a>Administrator aplikacji

Użytkownicy w tej roli mogą tworzyć wszystkie aspekty aplikacji przedsiębiorstwa, rejestracji aplikacji i ustawień serwera proxy aplikacji oraz zarządzać nimi. Pamiętaj, że użytkownicy przypisani do tej roli nie są dodawana jako właściciele podczas tworzenia nowych rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

Ta rola daje również możliwość wyrażania zgody na uprawnienia delegowane i uprawnienia aplikacji, z wyjątkiem uprawnień aplikacji dla programu Microsoft Graph i programu Azure AD Graph.

> [!IMPORTANT]
> Ten wyjątek oznacza, że nadal  możesz wyrazić zgodę na uprawnienia aplikacji dla innych aplikacji (na przykład aplikacji firm innych niż Microsoft lub aplikacji, które zostały zarejestrowane). Nadal możesz _zażądać_ tych uprawnień w ramach rejestracji aplikacji, ale udzielenie _tych_ uprawnień (czyli wyrażenie zgody) wymaga bardziej uprzywilejowanego administratora, takiego jak administrator globalny.
>
>Ta rola daje możliwość zarządzania poświadczeniami aplikacji. Użytkownicy przypisani do tej roli mogą dodawać poświadczenia do aplikacji i używać tych poświadczeń do personifikacji tożsamości aplikacji. Jeśli tożsamości aplikacji udzielono dostępu do zasobu, takiego jak możliwość tworzenia lub aktualizowania użytkownika lub innych obiektów, użytkownik przypisany do tej roli może wykonywać te akcje podczas personifikacji aplikacji. Możliwość personifikacji tożsamości aplikacji może stanowić podniesienie uprawnień do tego, co użytkownik może zrobić za pomocą przypisań ról. Należy pamiętać, że przypisanie użytkownika do roli administratora aplikacji daje mu możliwość personifikacji tożsamości aplikacji.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/applications/create | Tworzenie wszystkich typów aplikacji |
> | microsoft.directory/applications/delete | Usuwanie wszystkich typów aplikacji |
> | microsoft.directory/applications/applicationProxy/read | Odczytywanie wszystkich właściwości serwera proxy aplikacji |
> | microsoft.directory/applications/applicationProxy/update | Aktualizowanie wszystkich właściwości serwera proxy aplikacji |
> | microsoft.directory/applications/applicationProxyAuthentication/update | Aktualizowanie uwierzytelniania we wszystkich typach aplikacji |
> | microsoft.directory/applications/applicationProxySslCertificate/update | Aktualizowanie ustawień certyfikatu SSL dla serwera proxy aplikacji |
> | microsoft.directory/applications/applicationProxyUrlSettings/update | Aktualizowanie ustawień adresu URL serwera proxy aplikacji |
> | microsoft.directory/applications/appRoles/update | Aktualizowanie właściwości appRoles we wszystkich typach aplikacji |
> | microsoft.directory/applications/audience/update | Aktualizowanie właściwości odbiorców dla aplikacji |
> | microsoft.directory/applications/authentication/update | Aktualizowanie uwierzytelniania we wszystkich typach aplikacji |
> | microsoft.directory/applications/basic/update | Aktualizowanie podstawowych właściwości aplikacji |
> | microsoft.directory/applications/credentials/update | Aktualizowanie poświadczeń aplikacji |
> | microsoft.directory/applications/owners/update | Aktualizowanie właścicieli aplikacji |
> | microsoft.directory/applications/permissions/update | Aktualizowanie ujawnionych uprawnień i wymaganych uprawnień we wszystkich typach aplikacji |
> | microsoft.directory/applications/policies/update | Aktualizowanie zasad aplikacji |
> | microsoft.directory/applications/verification/update | Aktualizowanie właściwości applicationsverification |
> | microsoft.directory/applications/synchronization/standard/read | Odczyt ustawień aprowizowania skojarzonych z obiektem aplikacji |
> | microsoft.directory/applicationTemplates/instantiate | Tworzenia wystąpienia aplikacji galerii na podstawie szablonów aplikacji |
> | microsoft.directory/auditLogs/allProperties/read | Odczytywanie wszystkich właściwości dzienników inspekcji, w tym właściwości uprzywilejowanych |
> | microsoft.directory/connectors/create | Tworzenie łączników serwera proxy aplikacji |
> | microsoft.directory/connectors/allProperties/read | Odczytywanie wszystkich właściwości łączników serwera proxy aplikacji |
> | microsoft.directory/connectorGroups/create | Tworzenie grup łączników serwera proxy aplikacji |
> | microsoft.directory/connectorGroups/delete | Usuwanie grup łączników serwera proxy aplikacji |
> | microsoft.directory/connectorGroups/allProperties/read | Odczytywanie wszystkich właściwości grup łączników serwera proxy aplikacji |
> | microsoft.directory/connectorGroups/allProperties/update | Aktualizowanie wszystkich właściwości grup łączników serwera proxy aplikacji |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Tworzenie i usuwanie uprawnień uwierzytelniania OAuth 2.0 oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/applicationPolicies/create | Tworzenie zasad aplikacji |
> | microsoft.directory/applicationPolicies/delete | Usuwanie zasad aplikacji |
> | microsoft.directory/applicationPolicies/standard/read | Odczytywanie standardowych właściwości zasad aplikacji |
> | microsoft.directory/applicationPolicies/owners/read | Odczytywanie właścicieli zasad aplikacji |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | Odczytywanie zasad aplikacji zastosowanych do listy obiektów |
> | microsoft.directory/applicationPolicies/basic/update | Aktualizowanie standardowych właściwości zasad aplikacji |
> | microsoft.directory/applicationPolicies/owners/update | Aktualizowanie właściwości właściciela zasad aplikacji |
> | microsoft.directory/provisioningLogs/allProperties/read | Odczytywanie wszystkich właściwości dzienników aprowizowania |
> | microsoft.directory/servicePrincipals/create | Tworzenie jednostek usługi |
> | microsoft.directory/servicePrincipals/delete | Usuwanie jednostki usługi |
> | microsoft.directory/servicePrincipals/disable | Wyłączanie jednostki usługi |
> | microsoft.directory/servicePrincipals/enable | Włączanie jednostek usługi |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Zarządzanie poświadczeniami logowania pojedynczego hasła dla jednostki usługi |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Zarządzanie wpisami tajnymi i poświadczeniami aprowiowania aplikacji |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Uruchamianie, ponowne uruchamianie i wstrzymywanie zadań synchronizacji aprowizacji aplikacji |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Tworzenie zadań synchronizacji i schematu aprowizacji aplikacji i zarządzanie nimi |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Odczytywanie poświadczeń logowania pojedynczego hasła dla jednostki usługi |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-application-admin | Przyznaj zgodę na uprawnienia aplikacji i uprawnienia delegowane w imieniu dowolnego użytkownika lub wszystkich użytkowników, z wyjątkiem uprawnień aplikacji dla programu Microsoft Graph i programu Azure AD Graph |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizowanie przypisań ról jednostki usługi |
> | microsoft.directory/servicePrincipals/audience/update | Aktualizowanie właściwości odbiorców w podmiotach zabezpieczeń usługi |
> | microsoft.directory/servicePrincipals/authentication/update | Aktualizowanie właściwości uwierzytelniania dla jednostki usługi |
> | microsoft.directory/servicePrincipals/basic/update | Aktualizowanie podstawowych właściwości jednostki usługi |
> | microsoft.directory/servicePrincipals/credentials/update | Aktualizowanie poświadczeń jednostki usługi |
> | microsoft.directory/servicePrincipals/owners/update | Aktualizowanie właścicieli jednostki usługi |
> | microsoft.directory/servicePrincipals/permissions/update | Aktualizowanie uprawnień jednostki usługi |
> | microsoft.directory/servicePrincipals/policies/update | Aktualizowanie zasad jednostki usługi |
> | microsoft.directory/servicePrincipals/tag/update | Aktualizowanie właściwości tagu dla jednostki usługi |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Odczyt ustawień aprowowania skojarzonych z jednostką usługi |
> | microsoft.directory/signInReports/allProperties/read | Odczytywanie wszystkich właściwości raportów logowania, w tym właściwości uprzywilejowanych |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="application-developer"></a>Deweloper aplikacji

Użytkownicy w tej roli mogą tworzyć rejestracje aplikacji, gdy ustawienie "Użytkownicy mogą rejestrować aplikacje" ma wartość Nie. Ta rola udziela również uprawnienia do wyrażania zgody we własnym imieniu, gdy ustawienie "Użytkownicy mogą wyrazić zgodę na dostęp aplikacji do danych firmy w ich imieniu" ma wartość Nie. Użytkownicy przypisani do tej roli są dodawana jako właściciele podczas tworzenia nowych rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/applications/createAsOwner | Utwórz wszystkie typy aplikacji, a twórca zostanie dodany jako pierwszy właściciel |
> | microsoft.directory/appRoleAssignments/createAsOwner | Tworzenie przypisań ról aplikacji z twórcą jako pierwszym właścicielem |
> | microsoft.directory/oAuth2PermissionGrants/createAsOwner | Tworzenie uprawnień uwierzytelniania OAuth 2.0 z twórcą jako pierwszym właścicielem |
> | microsoft.directory/servicePrincipals/createAsOwner | Tworzenie jednostki usługi z twórcą jako pierwszym właścicielem |

## <a name="attack-payload-author"></a>Autor ładunku ataku

Użytkownicy w tej roli mogą tworzyć ładunki ataków, ale nie mogą ich uruchamiać ani planować. Ładunki ataków są następnie dostępne dla wszystkich administratorów w dzierżawie, którzy mogą ich używać do tworzenia symulacji.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/ładunek/allProperties/allTasks | Tworzenie ładunków ataków i zarządzanie nimi w symulatorze ataków |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Przeczytaj raporty odpowiedzi symulacji ataku i powiązanego szkolenia |

## <a name="attack-simulation-administrator"></a>Administrator symulacji ataku

Użytkownicy w tej roli mogą tworzyć i zarządzać wszystkimi aspektami tworzenia symulacji ataku, uruchamiania/planowania symulacji oraz przeglądu wyników symulacji. Członkowie tej roli mają ten dostęp do wszystkich symulacji w dzierżawie.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/ładunek/allProperties/allTasks | Tworzenie ładunków ataków i zarządzanie nimi w symulatorze ataków |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Przeczytaj raporty odpowiedzi symulacji ataku i powiązanego trenowania |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | Tworzenie szablonów symulacji ataków i zarządzanie nimi w symulatorze ataków |

## <a name="authentication-administrator"></a>Administrator uwierzytelniania

Użytkownicy z tą rolą mogą ustawić lub zresetować dowolną metodę uwierzytelniania (w tym hasła) dla użytkowników niebędących administratorami i niektórych ról. Administratorzy uwierzytelniania mogą wymagać od użytkowników niebędących administratorami lub przypisanych do niektórych ról ponownego rejestrowania istniejących poświadczeń innych niż hasła (na przykład uwierzytelniania MFA lub FIDO), a także odwołać usługę **REMEMBER MFA** na urządzeniu, która wyświetla monit o uwierzytelnianie wieloskładnikowe przy następnym loganiu. Aby uzyskać listę ról, które administrator uwierzytelniania może odczytywać lub aktualizować metody uwierzytelniania, zobacz [Uprawnienia do resetowania hasła.](#password-reset-permissions)

Rola [administratora uwierzytelniania uprzywilejowanego](#privileged-authentication-administrator) ma uprawnienia do wymuszania ponownej rejestracji i uwierzytelniania wieloskładnikowego dla wszystkich użytkowników.

Rola [administratora zasad uwierzytelniania](#authentication-policy-administrator) ma uprawnienia do ustawienia zasad metody uwierzytelniania dzierżawy, które określają, które metody mogą być rejestrowane i stosowane przez poszczególnych użytkowników.

| Rola | Zarządzanie metodami uwierzytelniania użytkowników | Zarządzanie uwierzytelniania wieloskładnikowego na użytkownika | Zarządzanie ustawieniami usługi MFA | Zarządzanie zasadami metody uwierzytelniania | Zarządzanie zasadami ochrony haseł |
| ---- | ---- | ---- | ---- | ---- | ---- |
| administrator uwierzytelniania | Tak dla niektórych użytkowników (patrz powyżej) | Tak dla niektórych użytkowników (patrz powyżej) | Nie | Nie | Nie |
| Administrator uwierzytelniania uprzywilejowanego| Tak dla wszystkich użytkowników | Tak dla wszystkich użytkowników | Nie | Nie | Nie |
| Administrator zasad uwierzytelniania | Nie |Nie | Tak | Tak | Tak |

> [!IMPORTANT]
> Użytkownicy z tą rolą mogą zmieniać poświadczenia dla osób, które mogą mieć dostęp do informacji poufnych lub prywatnych albo do konfiguracji o krytycznym znaczeniu wewnątrz i na zewnątrz Azure Active Directory. Zmiana poświadczeń użytkownika może oznaczać możliwość założenia tożsamości i uprawnień tego użytkownika. Na przykład:
>
>* Rejestracja aplikacji i właściciele aplikacji dla przedsiębiorstw, którzy mogą zarządzać poświadczeniami posiadanych przez nich aplikacji. Te aplikacje mogą mieć uprzywilejowane uprawnienia w usłudze Azure AD i w innym miejscu nie są przyznawane administratorom uwierzytelniania. Za pomocą tej ścieżki administrator uwierzytelniania może przyjąć tożsamość właściciela aplikacji, a następnie dodatkowo założyć tożsamość aplikacji uprzywilejowanej, aktualizując poświadczenia aplikacji.
>* Właściciele subskrypcji platformy Azure, którzy mogą mieć dostęp do informacji poufnych lub prywatnych albo do konfiguracji o krytycznym znaczeniu na platformie Azure.
>* Grupy zabezpieczeń i Microsoft 365 grupy zabezpieczeń, którzy mogą zarządzać członkostwem w grupie. Te grupy mogą udzielić dostępu do informacji poufnych lub prywatnych albo do konfiguracji o krytycznym znaczeniu w usłudze Azure AD i w innych miejscach.
>* Administratorzy w innych usługach poza usługą Azure AD, takich jak Exchange Online, Centrum zabezpieczeń i zgodności pakietu Office oraz systemy zasobów ludzkich.
>* Osoby inne niż administratorzy, na przykład pracownicy kadry kierowniczej, prawni i pracownicy, którzy mogą mieć dostęp do poufnych lub prywatnych informacji.

> [!IMPORTANT]
> Ta rola nie może zarządzać ustawieniami usługi MFA w starszym portalu zarządzania uwierzytelniania wieloskładnikowego ani tokenach sprzętowych OATH. Te same funkcje można wykonać za pomocą polecenia [Set-MsolUser](/powershell/module/msonline/set-msoluser) modułu Azure AD Powershell.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/users/invalidateAllRefreshTokens | Wymuszanie wylogowania przez unieważnienie tokenów odświeżania użytkownika |
> | microsoft.directory/users/strongAuthentication/update | Aktualizowanie właściwości silnego uwierzytelniania dla użytkowników |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="authentication-policy-administrator"></a>Administrator zasad uwierzytelniania

Użytkownicy z tą rolą mogą konfigurować zasady metod uwierzytelniania, ustawienia usługi MFA dla całej dzierżawy i zasady ochrony haseł. Ta rola przyznaje uprawnienia do zarządzania ustawieniami ochrony haseł: konfiguracjami inteligentnej blokady i aktualizowaniem niestandardowych listy zakazanych haseł.

Role [administrator uwierzytelniania](#authentication-administrator) i [](#privileged-authentication-administrator) administrator uwierzytelniania uprzywilejowanego mają uprawnienia do zarządzania zarejestrowanymi metodami uwierzytelniania użytkowników i mogą wymuszać ponowną rejestrację i uwierzytelnianie wieloskładnikowe dla wszystkich użytkowników.

| Rola | Zarządzanie metodami uwierzytelniania użytkownika | Zarządzanie uwierzytelniania wieloskładnikowego na użytkownika | Zarządzanie ustawieniami usługi MFA | Zarządzanie zasadami metody uwierzytelniania | Zarządzanie zasadami ochrony haseł |
| ---- | ---- | ---- | ---- | ---- | ---- |
| administrator uwierzytelniania | Tak dla niektórych użytkowników (patrz powyżej) | Tak dla niektórych użytkowników (patrz powyżej) | Nie | Nie | Nie |
| Administrator uwierzytelniania uprzywilejowanego| Tak dla wszystkich użytkowników | Tak dla wszystkich użytkowników | Nie | Nie | Nie |
| Administrator zasad uwierzytelniania | Nie | Nie | Tak | Tak | Tak |

> [!IMPORTANT]
> Ta rola nie może zarządzać ustawieniami usługi MFA w portalu zarządzania usługi MFA w starszej wersji ani w tokenach sprzętowych OATH.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/organization/strongAuthentication/update | Aktualizowanie właściwości silnego uwierzytelniania organizacji |
> | microsoft.directory/userCredentialPolicies/create | Tworzenie zasad poświadczeń dla użytkowników |
> | microsoft.directory/userCredentialPolicies/delete | Usuwanie zasad poświadczeń dla użytkowników |
> | microsoft.directory/userCredentialPolicies/standard/read | Odczytywanie standardowych właściwości zasad poświadczeń dla użytkowników |
> | microsoft.directory/userCredentialPolicies/owners/read | Odczytywanie właścicieli zasad poświadczeń dla użytkowników |
> | microsoft.directory/userCredentialPolicies/policyAppliedTo/read | Odczyt policy.appliesTo link nawigacji |
> | microsoft.directory/userCredentialPolicies/basic/update | Aktualizowanie podstawowych zasad dla użytkowników |
> | microsoft.directory/userCredentialPolicies/owners/update | Aktualizowanie właścicieli zasad poświadczeń dla użytkowników |
> | microsoft.directory/userCredentialPolicies/tenantDefault/update | Zaktualizuj właściwość policy.isOrganizationDefault |

## <a name="azure-ad-joined-device-local-administrator"></a>Administrator lokalny urządzenia przyłączonego do usługi Azure AD

Ta rola jest dostępna do przypisania tylko jako dodatkowy administrator lokalny w [ustawieniach urządzenia.](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/) Użytkownicy z tą rolą stają się administratorami maszyn lokalnych na wszystkich Windows 10, które są przyłączone do Azure Active Directory. Nie mają możliwości zarządzania obiektami urządzeń w Azure Active Directory.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/groupSettings/standard/read | Odczytywanie podstawowych właściwości ustawień grupy |
> | microsoft.directory/groupSettingTemplates/standard/read | Odczytywanie podstawowych właściwości szablonów ustawień grupy |

## <a name="azure-devops-administrator"></a>Azure DevOps administratorem

Użytkownicy z tą rolą mogą zarządzać zasadami Azure DevOps, aby ograniczyć tworzenie nowych Azure DevOps do zestawu konfigurowalnych użytkowników lub grup. Użytkownicy w tej roli mogą zarządzać zasadami za pośrednictwem Azure DevOps organizacji, która jest zapasowa przez organizację usługi Azure AD firmy. Ta rola nie Azure DevOps uprawnień specyficznych dla użytkownika (na przykład administratorzy kolekcji projektów) w żadnej z organizacji Azure DevOps, których pomocą jest firmowa organizacja usługi Azure AD.

Wszystkie zasady Azure DevOps przedsiębiorstwa mogą być zarządzane przez użytkowników w tej roli.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.azure.devOps/allEntities/allTasks | Odczytywanie i konfigurowanie Azure DevOps |

## <a name="azure-information-protection-administrator"></a>Azure Information Protection administratora

Użytkownicy z tą rolą mają wszystkie uprawnienia w Azure Information Protection usługi. Ta rola umożliwia konfigurowanie etykiet dla zasad Azure Information Protection, zarządzanie szablonami ochrony i aktywowanie ochrony. Ta rola nie udziela żadnych uprawnień w Centrum ochrony tożsamości, Privileged Identity Management, Monitor Microsoft 365 Service Health ani Centrum zgodności zabezpieczeń & Office 365.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.azure.informationProtection/allEntities/allTasks | Zarządzanie wszystkimi aspektami Azure Information Protection |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="b2c-ief-keyset-administrator"></a>Administrator zestawu kluczy IEF B2C

Użytkownik może tworzyć klucze zasad i wpisy tajne oraz zarządzać nimi na potrzeby szyfrowania tokenów, podpisów tokenów oraz szyfrowania/odszyfrowywania uwierzytelniania. Dodając nowe klucze do istniejących kontenerów kluczy, ten ograniczony administrator może w razie potrzeby przerzucać wpisy tajne bez wpływu na istniejące aplikacje. Ten użytkownik może zobaczyć pełną zawartość tych wpisów tajnych i daty ich wygaśnięcia nawet po utworzeniu.

> [!IMPORTANT]
> Jest to wrażliwa rola. Rola administratora zestawu kluczy powinna być dokładnie poddana inspekcji i przypisana ostrożnie podczas produkcji i przedprodukcyjnie.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/b2cTrustFrameworkKeySet/allProperties/allTasks | Odczytywanie i aktualizowanie wszystkich właściwości zasad autoryzacji |

## <a name="b2c-ief-policy-administrator"></a>Administrator zasad IEF B2C

Użytkownicy w tej roli mają możliwość tworzenia, odczytywania, aktualizowania i usuwania wszystkich zasad niestandardowych w programie Azure AD B2C i w związku z tym mają pełną kontrolę nad Identity Experience Framework w odpowiedniej Azure AD B2C organizacji. Edytując zasady, ten użytkownik może ustanowić bezpośrednią federację z zewnętrznymi dostawcami tożsamości, zmienić schemat katalogu, zmienić całą zawartość dostępną dla użytkowników (HTML, CSS, JavaScript), zmienić wymagania w celu ukończenia uwierzytelniania, utworzyć nowych użytkowników, wysłać dane użytkowników do systemów zewnętrznych, w tym pełne migracje, a także edytować wszystkie informacje o użytkownikach, w tym poufne pola, takie jak hasła i numery telefonów. I odwrotnie, ta rola nie może zmieniać kluczy szyfrowania ani edytować wpisów tajnych używanych do federacji w organizacji.

> [!IMPORTANT]
> Administrator zasad IEF B2 jest bardzo wrażliwą rolą, którą należy przypisać w bardzo ograniczonym zakresie dla organizacji w środowisku produkcyjnym. Działania tych użytkowników powinny być ściśle inspekcji, szczególnie w przypadku organizacji w środowisku produkcyjnym.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/b2cTrustFrameworkPolicy/allProperties/allTasks | Odczytywanie i konfigurowanie zestawów kluczy w Azure Active Directory B2C |

## <a name="billing-administrator"></a>Administrator rozliczeń

Dokonuje zakupów, zarządza subskrypcjami, zarządza biletami pomocy technicznej i monitoruje kondycję usługi.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/organization/basic/update | Aktualizowanie podstawowych właściwości w organizacji |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.commerce.billing/allEntities/allTasks | Zarządzanie wszystkimi aspektami rozliczeń usługi Office 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="cloud-application-administrator"></a>Administrator aplikacji w chmurze

Użytkownicy w tej roli mają takie same uprawnienia jak rola administrator aplikacji, z wyłączeniem możliwości zarządzania serwerem proxy aplikacji. Ta rola daje możliwość tworzenia wszystkich aspektów aplikacji dla przedsiębiorstw i rejestracji aplikacji oraz zarządzania nimi. Użytkownicy przypisani do tej roli nie są dodawana jako właściciele podczas tworzenia nowych rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

Ta rola daje również możliwość wyrażania zgody na uprawnienia delegowane i uprawnienia aplikacji, z wyjątkiem uprawnień aplikacji dla programu Microsoft Graph i programu Azure AD Graph.

> [!IMPORTANT]
> Ten wyjątek oznacza, że nadal  możesz wyrazić zgodę na uprawnienia aplikacji dla innych aplikacji (na przykład aplikacji lub aplikacji firm innych niż Microsoft, które zostały zarejestrowane). Nadal możesz _zażądać_ tych uprawnień w ramach rejestracji aplikacji, ale udzielenie _tych_ uprawnień (czyli wyrażenie zgody) wymaga bardziej uprzywilejowanego administratora, takiego jak administrator globalny.
>
>Ta rola daje możliwość zarządzania poświadczeniami aplikacji. Użytkownicy przypisani do tej roli mogą dodawać poświadczenia do aplikacji i używać tych poświadczeń do personifikacji tożsamości aplikacji. Jeśli tożsamości aplikacji udzielono dostępu do zasobu, takiego jak możliwość tworzenia lub aktualizowania użytkownika lub innych obiektów, użytkownik przypisany do tej roli może wykonać te akcje podczas personifikacji aplikacji. Możliwość personifikacji tożsamości aplikacji może stanowić podniesienie uprawnień do tego, co użytkownik może zrobić za pośrednictwem swoich przypisań ról. Należy pamiętać, że przypisanie użytkownika do roli administratora aplikacji daje mu możliwość personifikacji tożsamości aplikacji.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/applications/create | Tworzenie wszystkich typów aplikacji |
> | microsoft.directory/applications/delete | Usuwanie wszystkich typów aplikacji |
> | microsoft.directory/applications/appRoles/update | Aktualizowanie właściwości appRoles we wszystkich typach aplikacji |
> | microsoft.directory/applications/audience/update | Aktualizowanie właściwości odbiorców dla aplikacji |
> | microsoft.directory/applications/authentication/update | Aktualizowanie uwierzytelniania we wszystkich typach aplikacji |
> | microsoft.directory/applications/basic/update | Aktualizowanie podstawowych właściwości aplikacji |
> | microsoft.directory/applications/credentials/update | Aktualizowanie poświadczeń aplikacji |
> | microsoft.directory/applications/owners/update | Aktualizowanie właścicieli aplikacji |
> | microsoft.directory/applications/permissions/update | Aktualizowanie ujawnionych uprawnień i wymaganych uprawnień we wszystkich typach aplikacji |
> | microsoft.directory/applications/policies/update | Aktualizowanie zasad aplikacji |
> | microsoft.directory/applications/verification/update | Aktualizowanie właściwości applicationsverification |
> | microsoft.directory/applications/synchronization/standard/read | Odczyt ustawień aprowizowania skojarzonych z obiektem aplikacji |
> | microsoft.directory/applicationTemplates/instantiate | Tworzenia wystąpienia aplikacji galerii na podstawie szablonów aplikacji |
> | microsoft.directory/auditLogs/allProperties/read | Odczytywanie wszystkich właściwości dzienników inspekcji, w tym właściwości uprzywilejowanych |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Tworzenie i usuwanie uprawnień uwierzytelniania OAuth 2.0 oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/applicationPolicies/create | Tworzenie zasad aplikacji |
> | microsoft.directory/applicationPolicies/delete | Usuwanie zasad aplikacji |
> | microsoft.directory/applicationPolicies/standard/read | Odczytywanie standardowych właściwości zasad aplikacji |
> | microsoft.directory/applicationPolicies/owners/read | Odczytywanie właścicieli zasad aplikacji |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | Odczytywanie zasad aplikacji zastosowanych do listy obiektów |
> | microsoft.directory/applicationPolicies/basic/update | Aktualizowanie standardowych właściwości zasad aplikacji |
> | microsoft.directory/applicationPolicies/owners/update | Aktualizowanie właściwości właściciela zasad aplikacji |
> | microsoft.directory/provisioningLogs/allProperties/read | Odczytywanie wszystkich właściwości dzienników aprowizowania |
> | microsoft.directory/servicePrincipals/create | Tworzenie jednostek usługi |
> | microsoft.directory/servicePrincipals/delete | Usuwanie jednostki usługi |
> | microsoft.directory/servicePrincipals/disable | Wyłączanie jednostki usługi |
> | microsoft.directory/servicePrincipals/enable | Włączanie jednostek usługi |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Zarządzanie poświadczeniami logowania pojedynczego hasła dla jednostki usługi |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Zarządzanie wpisami tajnymi i poświadczeniami aprowiowania aplikacji |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Uruchamianie, ponowne uruchamianie i wstrzymywanie zadań synchronizacji aprowizacji aplikacji |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Tworzenie zadań synchronizacji i schematu aprowizacji aplikacji i zarządzanie nimi |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Odczytywanie poświadczeń logowania pojedynczego hasła dla jednostki usługi |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-application-admin | Przyznaj zgodę na uprawnienia aplikacji i uprawnienia delegowane w imieniu dowolnego użytkownika lub wszystkich użytkowników, z wyjątkiem uprawnień aplikacji dla programu Microsoft Graph i programu Azure AD Graph |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizowanie przypisań ról jednostki usługi |
> | microsoft.directory/servicePrincipals/audience/update | Aktualizowanie właściwości odbiorców w podmiotach zabezpieczeń usługi |
> | microsoft.directory/servicePrincipals/authentication/update | Aktualizowanie właściwości uwierzytelniania dla jednostki usługi |
> | microsoft.directory/servicePrincipals/basic/update | Aktualizowanie podstawowych właściwości jednostki usługi |
> | microsoft.directory/servicePrincipals/credentials/update | Aktualizowanie poświadczeń jednostki usługi |
> | microsoft.directory/servicePrincipals/owners/update | Aktualizowanie właścicieli jednostki usługi |
> | microsoft.directory/servicePrincipals/permissions/update | Aktualizowanie uprawnień jednostki usługi |
> | microsoft.directory/servicePrincipals/policies/update | Aktualizowanie zasad jednostki usługi |
> | microsoft.directory/servicePrincipals/tag/update | Aktualizowanie właściwości tagu dla jednostki usługi |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Odczyt ustawień aprowowania skojarzonych z jednostką usługi |
> | microsoft.directory/signInReports/allProperties/read | Odczytywanie wszystkich właściwości raportów logowania, w tym właściwości uprzywilejowanych |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="cloud-device-administrator"></a>Administrator urządzenia w chmurze

Użytkownicy w tej roli mogą włączać, wyłączać i usuwać urządzenia w usłudze Azure AD oraz odczytywać Windows 10 funkcji BitLocker (jeśli są obecne) w Azure Portal. Rola nie udziela uprawnień do zarządzania innymi właściwościami na urządzeniu.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Odczytywanie wszystkich właściwości dzienników inspekcji, w tym właściwości uprzywilejowanych |
> | microsoft.directory/bitlockerKeys/key/read | Odczytywanie metadanych i klucza funkcji BitLocker na urządzeniach |
> | microsoft.directory/devices/delete | Usuwanie urządzeń z usługi Azure AD |
> | microsoft.directory/devices/disable | Wyłączanie urządzeń w usłudze Azure AD |
> | microsoft.directory/devices/enable | Włączanie urządzeń w usłudze Azure AD |
> | microsoft.directory/devices/extensionAttributes/update | Aktualizowanie wszystkich wartości właściwości devices.extensionAttributes |
> | microsoft.directory/deviceManagementPolicies/standard/read | Odczytywanie standardowych właściwości zasad aplikacji do zarządzania urządzeniami |
> | microsoft.directory/deviceManagementPolicies/basic/update | Aktualizowanie podstawowych właściwości zasad aplikacji do zarządzania urządzeniami |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Odczytywanie standardowych właściwości zasad rejestracji urządzeń |
> | microsoft.directory/deviceRegistrationPolicy/basic/update | Aktualizowanie podstawowych właściwości zasad rejestracji urządzeń |
> | microsoft.directory/signInReports/allProperties/read | Odczytywanie wszystkich właściwości raportów logowania, w tym właściwości uprzywilejowanych |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |

## <a name="compliance-administrator"></a>Administrator zgodności

Użytkownicy z tą rolą mają uprawnienia do zarządzania funkcjami związanymi ze zgodnością w centrum zgodności usług Centrum zgodności platformy Microsoft 365, centrum administracyjne platformy Microsoft 365, Azure i Office 365 Security & Compliance Center. Przypisani mogą również zarządzać wszystkimi funkcjami w centrum administracyjnym programu Exchange i centrum administracyjnym usługi Teams & Skype dla firm oraz tworzyć bilety pomocy technicznej dla platformy Azure i Microsoft 365. Więcej informacji można znaleźć na stronie About Microsoft 365 admin roles (Informacje [o rolach administratora).](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

W | Można to zrobić
----- | ----------
[Centrum zgodności platformy Microsoft 365](https://protection.office.com) | Ochrona danych organizacji i zarządzanie nimi w różnych usługach Microsoft 365 danych<br>Zarządzanie alertami zgodności
[Menedżer zgodności](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Śledzenie, przypisywanie i weryfikowanie działań dotyczących zgodności z przepisami w organizacji
[Centrum zgodności zabezpieczeń usługi Office 365 & Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Zarządzanie zarządzaniem danymi<br>Przeprowadzanie badania prawnego i danych<br>Zarządzanie żądaniem podmiotu danych<br><br>Ta rola ma takie same uprawnienia jak grupa [ról administratora](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) zgodności w usłudze Office 365 Security & Compliance Center z kontrolą dostępu opartą na rolach.
[Intune](/intune/role-based-access-control) | Wyświetl wszystko inspekcji usługi Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Ma uprawnienia tylko do odczytu i może zarządzać alertami<br>Może tworzyć i modyfikować zasady dotyczące plików oraz zezwalać na akcje nadzoru nad plikami<br>Może wyświetlać wszystkie wbudowane raporty w obszarze Zarządzanie danymi

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.directory/entitlementManagement/allProperties/read | Odczytywanie wszystkich właściwości w zarządzaniu uprawnieniami w usłudze Azure AD |
> | microsoft.office365.complianceManager/allEntities/allTasks | Zarządzanie wszystkimi aspektami Menedżera zgodności usługi Office 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="compliance-data-administrator"></a>Administrator danych zgodności

Użytkownicy z tą rolą mają uprawnienia do śledzenia danych na Centrum zgodności platformy Microsoft 365, centrum administracyjne platformy Microsoft 365 i Azure. Użytkownicy mogą również śledzić dane zgodności w centrum administracyjnym programu Exchange, menedżerze zgodności i aplikacji Teams & centrum administracyjnym usługi Skype dla firm i tworzyć bilety pomocy technicznej dla platformy Azure i Microsoft 365. [Ta dokumentacja zawiera](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) szczegółowe informacje na temat różnic między administratorem zgodności a administratorem danych zgodności.

W | Można to zrobić
----- | ----------
[Centrum zgodności platformy Microsoft 365](https://protection.office.com) | Monitorowanie zasad związanych ze zgodnością w usługach Microsoft 365 internetowych<br>Zarządzanie alertami zgodności
[Menedżer zgodności](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Śledzenie, przypisywanie i weryfikowanie działań dotyczących zgodności z przepisami w organizacji
[Centrum zgodności zabezpieczeń usługi Office 365 & Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Zarządzanie zarządzaniem danymi<br>Przeprowadzanie badania prawnego i danych<br>Zarządzanie żądaniem podmiotu danych<br><br>Ta rola ma takie same uprawnienia jak grupa [RoleGroup](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) administratora danych zgodności w usłudze Office 365 Security & Compliance Center kontroli dostępu opartej na rolach.
[Intune](/intune/role-based-access-control) | Wyświetl wszystko danych inspekcji usługi Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Ma uprawnienia tylko do odczytu i może zarządzać alertami<br>Może tworzyć i modyfikować zasady dotyczące plików oraz zezwalać na akcje nadzoru nad plikami<br>Może wyświetlać wszystkie wbudowane raporty w obszarze Zarządzanie danymi

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w Microsoft Cloud App Security |
> | microsoft.azure.informationProtection/allEntities/allTasks | Zarządzanie wszystkimi aspektami Azure Information Protection |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.complianceManager/allEntities/allTasks | Zarządzanie wszystkimi aspektami Menedżera zgodności usługi Office 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="conditional-access-administrator"></a>Administrator dostępu warunkowego

Użytkownicy z tą rolą mają możliwość zarządzania Azure Active Directory dostępem warunkowym.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/conditionalAccessPolicies/create | Tworzenie zasad dostępu warunkowego |
> | microsoft.directory/conditionalAccessPolicies/delete | Usuwanie zasad dostępu warunkowego |
> | microsoft.directory/conditionalAccessPolicies/standard/read | Odczyt dostępu warunkowego dla zasad |
> | microsoft.directory/conditionalAccessPolicies/owners/read | Odczytywanie właścicieli zasad dostępu warunkowego |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Odczytywanie właściwości "zastosowane do" dla zasad dostępu warunkowego |
> | microsoft.directory/conditionalAccessPolicies/basic/update | Aktualizowanie podstawowych właściwości zasad dostępu warunkowego |
> | microsoft.directory/conditionalAccessPolicies/owners/update | Aktualizowanie właścicieli zasad dostępu warunkowego |
> | microsoft.directory/conditionalAccessPolicies/tenantDefault/update | Aktualizowanie domyślnej dzierżawy dla zasad dostępu warunkowego |
> | microsoft.directory/crossTenantAccessPolicies/create | Tworzenie zasad dostępu między dzierżawami |
> | microsoft.directory/crossTenantAccessPolicies/delete | Usuwanie zasad dostępu między dzierżawami |
> | microsoft.directory/crossTenantAccessPolicies/standard/read | Odczytywanie podstawowych właściwości zasad dostępu między dzierżawami |
> | microsoft.directory/crossTenantAccessPolicies/owners/read | Odczytywanie właścicieli zasad dostępu między dzierżawami |
> | microsoft.directory/crossTenantAccessPolicies/policyAppliedTo/read | Odczytywanie właściwości policyAppliedTo zasad dostępu między dzierżawami |
> | microsoft.directory/crossTenantAccessPolicies/basic/update | Aktualizowanie podstawowych właściwości zasad dostępu między dzierżawami |
> | microsoft.directory/crossTenantAccessPolicies/owners/update | Aktualizowanie właścicieli zasad dostępu między dzierżawami |
> | microsoft.directory/crossTenantAccessPolicies/tenantDefault/update | Aktualizowanie domyślnej dzierżawy dla zasad dostępu między dzierżawami |

## <a name="customer-lockbox-access-approver"></a>Osoba zatwierdzająca dostęp do skrytki klienta

Zarządza [Skrytka klienta żądaniami](/office365/admin/manage/customer-lockbox-requests) w organizacji. Otrzymują oni powiadomienia e-mail dotyczące Skrytka klienta żądań oraz mogą zatwierdzać i odrzucać żądania od centrum administracyjne platformy Microsoft 365. Mogą również włączyć lub Skrytka klienta funkcji. Tylko administratorzy globalni mogą resetować hasła osób przypisanych do tej roli.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.office365.lockbox/allEntities/allTasks | Zarządzanie wszystkimi aspektami Skrytka klienta |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="desktop-analytics-administrator"></a>Desktop Analytics administratora

Użytkownicy w tej roli mogą zarządzać Desktop Analytics usługą. Obejmuje to możliwość wyświetlania spisu zasobów, tworzenia planów wdrażania oraz wyświetlania stanu wdrożenia i kondycji.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | Zarządzanie wszystkimi aspektami Desktop Analytics |

## <a name="directory-readers"></a>Czytniki katalogów

Użytkownicy w tej roli mogą odczytywać podstawowe informacje o katalogu. Ta rola powinna być używana w przypadku:

* Udzielanie określonego zestawu użytkowników-gości dostępu do odczytu zamiast udzielania go wszystkim użytkownikom-gościom.
* Udzielanie określonego zestawu użytkowników niebędących administratorami do usługi Azure Portal gdy ustawienie "Ogranicz dostęp do portalu usługi Azure AD tylko do administratorów" ma wartość "Tak".
* Udzielanie jednostkom usługi dostępu do katalogu, w którym Directory.Read.All nie jest opcją.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/administrativeUnits/standard/read | Odczytywanie podstawowych właściwości jednostek administracyjnych |
> | microsoft.directory/administrativeUnits/members/read | Odczytywanie członków jednostek administracyjnych |
> | microsoft.directory/applications/standard/read | Odczytywanie standardowych właściwości aplikacji |
> | microsoft.directory/applications/owners/read | Odczytywanie właścicieli aplikacji |
> | microsoft.directory/applications/policies/read | Odczytywanie zasad aplikacji |
> | microsoft.directory/contacts/standard/read | Odczytywanie podstawowych właściwości kontaktów w usłudze Azure AD |
> | microsoft.directory/contacts/memberOf/read | Odczytywanie członkostwa w grupie dla wszystkich kontaktów w usłudze Azure AD |
> | microsoft.directory/contracts/standard/read | Odczytywanie podstawowych właściwości kontraktów partnerów |
> | microsoft.directory/devices/standard/read | Odczytywanie podstawowych właściwości na urządzeniach |
> | microsoft.directory/devices/memberOf/read | Odczytywanie członkosstwa urządzeń |
> | microsoft.directory/devices/registeredOwners/read | Odczytywanie zarejestrowanych właścicieli urządzeń |
> | microsoft.directory/devices/registeredUsers/read | Odczytywanie zarejestrowanych użytkowników urządzeń |
> | microsoft.directory/directoryRoles/standard/read | Aktualizowanie podstawowych właściwości w rolach usługi Azure AD |
> | microsoft.directory/directoryRoles/eligibleMembers/read | Odczytywanie kwalifikujących się członków ról usługi Azure AD |
> | microsoft.directory/directoryRoles/members/read | Odczytywanie wszystkich członków ról usługi Azure AD |
> | microsoft.directory/domains/standard/read | Odczytywanie podstawowych właściwości domen |
> | microsoft.directory/groups/standard/read | Odczytywanie podstawowych właściwości grup |
> | microsoft.directory/groups/appRoleAssignments/read | Odczytywanie przypisań ról aplikacji grup |
> | microsoft.directory/groups/memberOf/read | Odczytywanie grup, których grupa jest członkiem w usłudze Azure AD |
> | microsoft.directory/groups/members/read | Odczytywanie członków grup |
> | microsoft.directory/groups/owners/read | Odczytywanie właścicieli grup |
> | microsoft.directory/groups/settings/read | Odczyt ustawień grup |
> | microsoft.directory/groupSettings/standard/read | Odczytywanie podstawowych właściwości ustawień grupy |
> | microsoft.directory/groupSettingTemplates/standard/read | Odczytywanie podstawowych właściwości szablonów ustawień grupy |
> | microsoft.directory/oAuth2PermissionGrants/standard/read | Odczytywanie podstawowych właściwości przy udzielanych uprawnieniach protokołu OAuth 2.0 |
> | microsoft.directory/organization/standard/read | Odczytywanie podstawowych właściwości w organizacji |
> | microsoft.directory/organization/trustedCAsForPasswordlessAuth/read | Odczytywanie zaufanych urzędów certyfikacji w celu uwierzytelniania bez hasła |
> | microsoft.directory/applicationPolicies/standard/read | Odczytywanie standardowych właściwości zasad aplikacji |
> | microsoft.directory/roleAssignments/standard/read | Odczytywanie podstawowych właściwości przypisań ról |
> | microsoft.directory/roleDefinitions/standard/read | Odczytywanie podstawowych właściwości definicji ról |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Odczytywanie przypisań ról jednostki usługi |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | Odczytywanie przypisań ról przypisanych do jednostki usługi |
> | microsoft.directory/servicePrincipals/standard/read | Odczytywanie podstawowych właściwości jednostki usługi |
> | microsoft.directory/servicePrincipals/memberOf/read | Odczytywanie członkosstwa w grupach dla jednostki usługi |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | Odczytywanie delegowanych uprawnień udzielanych dla jednostki usługi |
> | microsoft.directory/servicePrincipals/owners/read | Odczytywanie właścicieli jednostki usługi |
> | microsoft.directory/servicePrincipals/ownedObjects/read | Odczytywanie posiadanych obiektów jednostki usługi |
> | microsoft.directory/servicePrincipals/policies/read | Odczytywanie zasad jednostki usługi |
> | microsoft.directory/subscribedSkus/standard/read | Odczytywanie podstawowych właściwości subskrypcji |
> | microsoft.directory/users/standard/read | Odczytywanie podstawowych właściwości użytkowników |
> | microsoft.directory/users/appRoleAssignments/read | Odczytywanie przypisań ról aplikacji użytkowników |
> | microsoft.directory/users/directReports/read | Odczytywanie raportów bezpośrednich dla użytkowników |
> | microsoft.directory/users/manager/read | Read manager of users (Menedżer odczytu użytkowników) |
> | microsoft.directory/users/memberOf/read | Odczytywanie członkosstwa użytkowników w grupach |
> | microsoft.directory/users/oAuth2PermissionGrants/read | Odczytywanie delegowanych uprawnień udzielanych użytkownikom |
> | microsoft.directory/users/ownedDevices/read | Odczytywanie posiadanych urządzeń użytkowników |
> | microsoft.directory/users/ownedObjects/read | Odczytywanie posiadanych obiektów użytkowników |
> | microsoft.directory/users/registeredDevices/read | Odczytywanie zarejestrowanych urządzeń użytkowników |

## <a name="directory-synchronization-accounts"></a>Konta synchronizacji katalogów

Nie używaj. Ta rola jest automatycznie przypisywana do usługi Azure AD Connect i nie jest przeznaczona ani obsługiwana do żadnego innego użytku.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/applications/create | Tworzenie wszystkich typów aplikacji |
> | microsoft.directory/applications/delete | Usuwanie wszystkich typów aplikacji |
> | microsoft.directory/applications/appRoles/update | Aktualizowanie właściwości appRoles we wszystkich typach aplikacji |
> | microsoft.directory/applications/audience/update | Aktualizowanie właściwości odbiorców dla aplikacji |
> | microsoft.directory/applications/authentication/update | Aktualizowanie uwierzytelniania we wszystkich typach aplikacji |
> | microsoft.directory/applications/basic/update | Aktualizowanie podstawowych właściwości aplikacji |
> | microsoft.directory/applications/credentials/update | Aktualizowanie poświadczeń aplikacji |
> | microsoft.directory/applications/owners/update | Aktualizowanie właścicieli aplikacji |
> | microsoft.directory/applications/permissions/update | Aktualizowanie ujawnionych uprawnień i wymaganych uprawnień do wszystkich typów aplikacji |
> | microsoft.directory/applications/policies/update | Aktualizowanie zasad aplikacji |
> | microsoft.directory/organization/dirSync/update | Aktualizowanie właściwości synchronizacji katalogu organizacji |
> | microsoft.directory/policies/create | Tworzenie zasad w usłudze Azure AD |
> | microsoft.directory/policies/delete | Usuwanie zasad w usłudze Azure AD |
> | microsoft.directory/policies/standard/read | Odczytywanie podstawowych właściwości zasad |
> | microsoft.directory/policies/owners/read | Odczytywanie właścicieli zasad |
> | microsoft.directory/policies/policyAppliedTo/read | Odczytywanie właściwości policies.policyAppliedTo |
> | microsoft.directory/policies/basic/update | Aktualizowanie podstawowych właściwości zasad |
> | microsoft.directory/policies/owners/update | Aktualizowanie właścicieli zasad |
> | microsoft.directory/policies/tenantDefault/update | Aktualizowanie domyślnych zasad organizacji |
> | microsoft.directory/servicePrincipals/create | Tworzenie jednostek usługi |
> | microsoft.directory/servicePrincipals/delete | Usuwanie jednostki usługi |
> | microsoft.directory/servicePrincipals/enable | Włączanie jednostek usługi |
> | microsoft.directory/servicePrincipals/disable | Wyłączanie jednostki usługi |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Zarządzanie poświadczeniami logowania pojedynczego hasła dla jednostki usługi |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Odczytywanie poświadczeń logowania pojedynczego hasła dla jednostki usługi |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Odczytywanie przypisań ról jednostki usługi |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | Odczytywanie przypisań ról przypisanych do jednostki usługi |
> | microsoft.directory/servicePrincipals/standard/read | Odczytywanie podstawowych właściwości jednostki usługi |
> | microsoft.directory/servicePrincipals/memberOf/read | Odczytywanie członkosstwa w grupach dla jednostki usługi |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | Odczytywanie delegowanych uprawnień udzielanych dla jednostki usługi |
> | microsoft.directory/servicePrincipals/owners/read | Odczytywanie właścicieli jednostki usługi |
> | microsoft.directory/servicePrincipals/ownedObjects/read | Odczytywanie posiadanych obiektów jednostki usługi |
> | microsoft.directory/servicePrincipals/policies/read | Odczytywanie zasad jednostki usługi |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizowanie przypisań ról jednostki usługi |
> | microsoft.directory/servicePrincipals/audience/update | Aktualizowanie właściwości odbiorców w podmiotach zabezpieczeń usługi |
> | microsoft.directory/servicePrincipals/authentication/update | Aktualizowanie właściwości uwierzytelniania dla jednostki usługi |
> | microsoft.directory/servicePrincipals/basic/update | Aktualizowanie podstawowych właściwości jednostki usługi |
> | microsoft.directory/servicePrincipals/credentials/update | Aktualizowanie poświadczeń jednostki usługi |
> | microsoft.directory/servicePrincipals/owners/update | Aktualizowanie właścicieli jednostki usługi |
> | microsoft.directory/servicePrincipals/permissions/update | Aktualizowanie uprawnień jednostki usługi |
> | microsoft.directory/servicePrincipals/policies/update | Aktualizowanie zasad jednostki usługi |
> | microsoft.directory/servicePrincipals/tag/update | Aktualizowanie właściwości tagu dla jednostki usługi |

## <a name="directory-writers"></a>Twórcy katalogów

Użytkownicy w tej roli mogą odczytywać i aktualizować podstawowe informacje o użytkownikach, grupach i jednostkach usługi. Przypisz tę rolę tylko do aplikacji, które nie obsługują struktury [wyrażania zgody.](../develop/quickstart-register-app.md) Nie należy przypisywać go do żadnych użytkowników.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/groups/assignLicense | Przypisywanie licencji produktów do grup w celu licencjonowania opartego na grupach |
> | microsoft.directory/groups/create | Tworzenie grup z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/reprocessLicenseAssignment | Ponowne przetwarzanie przypisań licencji dla licencjonowania opartego na grupach |
> | microsoft.directory/groups/basic/update | Aktualizowanie podstawowych właściwości grup z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/classification/update | Aktualizowanie właściwości klasyfikacji grup, z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/dynamicMembershipRule/update | Aktualizowanie reguły członkostwa dynamicznego grup, z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/groupType/update | Aktualizowanie właściwości groupType grupy |
> | microsoft.directory/groups/members/update | Aktualizowanie członków grup z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/onPremWriteBack/update | Aktualizowanie Azure Active Directory, które mają być zapisywane z powrotem w środowisku lokalnym przy użyciu Azure AD Connect |
> | microsoft.directory/groups/owners/update | Aktualizowanie właścicieli grup z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/settings/update | Aktualizowanie ustawień grup |
> | microsoft.directory/groups/visibility/update | Aktualizowanie właściwości widoczności grup |
> | microsoft.directory/groupSettings/create | Tworzenie ustawień grupy |
> | microsoft.directory/groupSettings/delete | Usuwanie ustawień grupy |
> | microsoft.directory/groupSettings/basic/update | Aktualizowanie podstawowych właściwości ustawień grupy |
> | microsoft.directory/oAuth2PermissionGrants/create | Tworzenie uprawnień uwierzytelniania OAuth 2.0 |
> | microsoft.directory/oAuth2PermissionGrants/basic/update | Aktualizowanie uprawnień uwierzytelniania OAuth 2.0 |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Zarządzanie wpisami tajnymi i poświadczeniami aprowiowania aplikacji |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Uruchamianie, ponowne uruchamianie i wstrzymywanie zadań synchronizacji aprowizacji aplikacji |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Tworzenie zadań synchronizacji i schematu aprowizacji aplikacji i zarządzanie nimi |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Udzielanie jednostki usługi bezpośredniego dostępu do danych grupy |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizowanie przypisań ról jednostki usługi |
> | microsoft.directory/users/assignLicense | Zarządzanie licencjami użytkowników |
> | microsoft.directory/users/create | Dodawanie użytkowników |
> | microsoft.directory/users/disable | Wyłączanie użytkowników |
> | microsoft.directory/users/enable | Włączanie użytkowników |
> | microsoft.directory/users/invalidateAllRefreshTokens | Wymuszanie wylogowania przez unieważnienie tokenów odświeżania użytkownika |
> | microsoft.directory/users/reprocessLicenseAssignment | Ponowne przetwarzanie przypisań licencji dla użytkowników |
> | microsoft.directory/users/basic/update | Aktualizowanie podstawowych właściwości użytkowników |
> | microsoft.directory/users/manager/update | Menedżer aktualizacji dla użytkowników |
> | microsoft.directory/users/userPrincipalName/update | Aktualizowanie głównej nazwy użytkownika użytkowników |

## <a name="domain-name-administrator"></a>Administrator nazwy domeny

Użytkownicy z tą rolą mogą zarządzać nazwami domen (odczytywać, dodawać, weryfikować, aktualizować i usuwać). Mogą również odczytywać informacje o użytkownikach, grupach i aplikacjach, ponieważ te obiekty mają zależności domeny. W środowiskach lokalnych użytkownicy z tą rolą mogą konfigurować nazwy domen na potrzeby federacji, aby skojarzone z nimi użytkownicy były zawsze uwierzytelniani lokalnie. Ci użytkownicy mogą następnie logować się do usług opartych na usłudze Azure AD przy użyciu haseł lokalnych za pośrednictwem logowania pojedynczego. Ustawienia federacji muszą być synchronizowane za pośrednictwem Azure AD Connect, aby użytkownicy mieli również uprawnienia do zarządzania Azure AD Connect.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/domains/allProperties/allTasks | Tworzenie i usuwanie domen oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |

## <a name="dynamics-365-administrator"></a>Dynamics 365 Administrator

Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Dynamics 365 Online, gdy usługa jest obecna, a także możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji zawiera temat Use the service admin role to manage your Azure AD organization (Używanie roli [administratora usługi do zarządzania organizacją usługi Azure AD).](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)

> [!NOTE]
> W interfejsie API Microsoft Graph Azure AD PowerShell ta rola jest identyfikowana jako "Administrator usługi Dynamics 365". Jest to "Administrator usługi Dynamics 365" w Azure Portal [.](https://portal.azure.com)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.dynamics365/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Dynamics 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="exchange-administrator"></a>Exchange Administrator

Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Exchange Online, gdy usługa jest obecna. Ma również możliwość tworzenia wszystkich grup Microsoft 365, zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji można znaleźć [w te Microsoft 365 roli administratora](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)programu .

> [!NOTE]
> W interfejsie MICROSOFT GRAPH API i programie PowerShell usługi Azure AD ta rola jest identyfikowana jako "Administrator usługi programu Exchange". Jest to "Administrator programu Exchange" w Azure Portal [.](https://portal.azure.com) Jest to "Administrator usługi Exchange Online" w centrum [administracyjnym programu Exchange.](https://go.microsoft.com/fwlink/p/?LinkID=529144)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/groups/hiddenMembers/read | Odczytywanie ukrytych członków grupy |
> | microsoft.directory/groups.unified/create | Tworzenie Microsoft 365 grup z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/groups.unified/delete | Usuwanie Microsoft 365 grupy z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/groups.unified/restore | Przywracanie Microsoft 365 grupy |
> | microsoft.directory/groups.unified/basic/update | Aktualizowanie podstawowych właściwości Microsoft 365 grup z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups.unified/members/update | Aktualizowanie członków Microsoft 365 grupy z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/groups.unified/owners/update | Aktualizowanie właścicieli Microsoft 365 grupy z wyłączeniem grup przypisywanych do ról |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.exchange/allEntities/basic/allTasks | Zarządzanie wszystkimi aspektami usługi Exchange Online |
> | microsoft.office365.network/performance/allProperties/read | Odczytywanie wszystkich właściwości wydajności sieci w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Odczytywanie raportów użycia usługi Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="exchange-recipient-administrator"></a>Administrator adresata programu Exchange

Użytkownicy z tą rolą mają dostęp do odczytu do adresatów i dostęp do zapisu do atrybutów tych adresatów w u usługi Exchange Online. Więcej informacji można znaleźć [na stronie Odbiorcy programu Exchange.](/exchange/recipients/recipients)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.office365.exchange/allRecipients/allProperties/allTasks | Tworzenie i usuwanie wszystkich adresatów oraz odczytywanie i aktualizowanie wszystkich właściwości adresatów w u usługi Exchange Online |
> | microsoft.office365.exchange/messageTracking/allProperties/allTasks | Zarządzanie wszystkimi zadaniami śledzenia komunikatów w u usługi Exchange Online |
> | microsoft.office365.exchange/migration/allProperties/allTasks | Zarządzanie wszystkimi zadaniami związanymi z migracją adresatów w u usługi Exchange Online |

## <a name="external-id-user-flow-administrator"></a>Administrator przepływu użytkownika zewnętrznego identyfikatora

Użytkownicy z tą rolą mogą tworzyć przepływy użytkowników (nazywane również "wbudowanymi" zasadami) i zarządzać nimi w Azure Portal. Ci użytkownicy mogą dostosowywać zawartość HTML/CSS/JavaScript, zmieniać wymagania usługi MFA, wybierać oświadczenia w tokenie, zarządzać łącznikami interfejsu API i konfigurować ustawienia sesji dla wszystkich przepływów użytkowników w organizacji usługi Azure AD. Z drugiej strony ta rola nie obejmuje możliwości przeglądania danych użytkowników ani zmieniania atrybutów uwzględnionych w schemacie organizacji. Zmiany zasad Identity Experience Framework (nazywane również zasadami niestandardowymi) również znajdują się poza zakresem tej roli.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/b2cUserFlow/allProperties/allTasks | Odczytywanie i konfigurowanie atrybutów użytkownika w Azure Active Directory B2C |

## <a name="external-id-user-flow-attribute-administrator"></a>Administrator atrybutów przepływu użytkownika zewnętrznego identyfikatora

Użytkownicy z tą rolą dodają lub usuwają atrybuty niestandardowe dostępne dla wszystkich przepływów użytkownika w organizacji usługi Azure AD. W związku z tym użytkownicy z tą rolą mogą zmieniać lub dodawać nowe elementy do schematu użytkownika końcowego i wpływać na zachowanie wszystkich przepływów użytkowników oraz pośrednio powodować zmiany danych, które mogą być zadawane użytkownikom końcowi i ostatecznie wysyłane jako oświadczenia do aplikacji. Ta rola nie może edytować przepływów użytkownika.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/b2cUserAttribute/allProperties/allTasks | Odczytywanie i konfigurowanie zasad niestandardowych w Azure Active Directory B2C |

## <a name="external-identity-provider-administrator"></a>Administrator zewnętrznego dostawcy tożsamości

Ten administrator zarządza federacją między organizacjami usługi Azure AD i zewnętrznymi dostawcami tożsamości. Dzięki tej roli użytkownicy mogą dodawać nowych dostawców tożsamości i konfigurować wszystkie dostępne ustawienia (np. ścieżkę uwierzytelniania, identyfikator usługi, przypisane kontenery kluczy). Ten użytkownik może umożliwić organizacji usługi Azure AD ufanie uwierzytelnianiu od zewnętrznych dostawców tożsamości. Wynikowy wpływ na środowisko użytkownika końcowego zależy od typu organizacji:

* Organizacje usługi Azure AD dla pracowników i partnerów: Dodanie federacji (np. z usługą Gmail) natychmiast wpłynie na wszystkie zaproszenia gości, które nie zostały jeszcze zrealizowane. Zobacz Adding Google as an identity provider for B2B guest users (Dodawanie firmy [Google jako dostawcy tożsamości dla użytkowników-gości B2B).](../external-identities/google-federation.md)
* Azure Active Directory B2C organizacji: dodanie federacji (na przykład z usługą Facebook lub z inną organizacją usługi Azure AD) nie ma natychmiastowego wpływu na przepływy użytkowników końcowych, dopóki dostawca tożsamości nie zostanie dodany jako opcja w przepływie użytkownika (nazywanym również zasadami wbudowanymi). Zobacz [konfigurowanie konto Microsoft jako dostawcy tożsamości,](../../active-directory-b2c/identity-provider-microsoft-account.md) aby uzyskać przykład. Aby zmienić przepływy użytkownika, wymagana jest ograniczona rola "Administrator przepływu użytkowników B2C".

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/identityProviders/allProperties/allTasks | Odczytywanie i konfigurowanie dostawców tożsamości w Azure Active Directory B2C |

## <a name="global-administrator"></a>Administrator globalny

Użytkownicy z tą rolą mają dostęp do wszystkich funkcji administracyjnych w usłudze Azure Active Directory, a także usług korzystających z tożsamości usługi Azure Active Directory, takich jak Centrum zabezpieczeń platformy Microsoft 365, Centrum zgodności platformy Microsoft 365, Exchange Online, SharePoint Online i Skype dla firm Online. Ponadto administratorzy globalni mogą [podnieść swój dostęp, aby](../../role-based-access-control/elevate-access-global-admin.md) zarządzać wszystkimi subskrypcjami i grupami zarządzania platformy Azure. Dzięki temu administratorzy globalni mogą uzyskać pełny dostęp do wszystkich zasobów platformy Azure przy użyciu odpowiedniej dzierżawy usługi Azure AD. Osoba, która zasejestruje się w organizacji usługi Azure AD, staje się administratorem globalnym. W firmie może być więcej niż jeden administrator globalny. Administratorzy globalni mogą resetować hasło dla dowolnego użytkownika i wszystkich innych administratorów.

> [!NOTE]
> Najlepszym rozwiązaniem jest przypisanie roli administratora globalnego do mniej niż pięciu osób w organizacji. Aby uzyskać więcej informacji, zobacz [Najlepsze rozwiązania dotyczące ról usługi Azure AD.](best-practices.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/accessReviews/allProperties/allTasks | Tworzenie i usuwanie przeglądów dostępu oraz odczytywanie i aktualizowanie wszystkich właściwości przeglądów dostępu w usłudze Azure AD |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | Tworzenie jednostek administracyjnych (w tym członków) i zarządzanie nimi |
> | microsoft.directory/applications/allProperties/allTasks | Tworzenie i usuwanie aplikacji oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/applications/synchronization/standard/read | Odczyt ustawień aprowizowania skojarzonych z obiektem aplikacji |
> | microsoft.directory/applicationTemplates/instantiate | Tworzenia wystąpienia aplikacji z galerii na podstawie szablonów aplikacji |
> | microsoft.directory/appRoleAssignments/allProperties/allTasks | Tworzenie i usuwanie obiektów appRoleAssignments oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/auditLogs/allProperties/read | Odczytywanie wszystkich właściwości dzienników inspekcji, w tym właściwości uprzywilejowanych |
> | microsoft.directory/authorizationPolicy/allProperties/allTasks | Zarządzanie wszystkimi aspektami zasad autoryzacji |
> | microsoft.directory/bitlockerKeys/key/read | Odczytywanie metadanych i klucza funkcji BitLocker na urządzeniach |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w Microsoft Cloud App Security |
> | microsoft.directory/connectors/create | Tworzenie łączników serwera proxy aplikacji |
> | microsoft.directory/connectors/allProperties/read | Odczytywanie wszystkich właściwości łączników serwera proxy aplikacji |
> | microsoft.directory/connectorGroups/create | Tworzenie grup łączników serwera proxy aplikacji |
> | microsoft.directory/connectorGroups/delete | Usuwanie grup łączników serwera proxy aplikacji |
> | microsoft.directory/connectorGroups/allProperties/read | Odczytywanie wszystkich właściwości grup łączników serwera proxy aplikacji |
> | microsoft.directory/connectorGroups/allProperties/update | Aktualizowanie wszystkich właściwości grup łączników serwera proxy aplikacji |
> | microsoft.directory/contacts/allProperties/allTasks | Tworzenie i usuwanie kontaktów oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/contracts/allProperties/allTasks | Tworzenie i usuwanie kontraktów partnerów oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/devices/allProperties/allTasks | Tworzenie i usuwanie urządzeń oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/deviceManagementPolicies/standard/read | Odczytywanie standardowych właściwości zasad aplikacji do zarządzania urządzeniami |
> | microsoft.directory/deviceManagementPolicies/basic/update | Aktualizowanie podstawowych właściwości zasad aplikacji do zarządzania urządzeniami |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Odczytywanie standardowych właściwości zasad rejestracji urządzeń |
> | microsoft.directory/deviceRegistrationPolicy/basic/update | Aktualizowanie podstawowych właściwości zasad rejestracji urządzeń |
> | microsoft.directory/directoryRoles/allProperties/allTasks | Tworzenie i usuwanie ról katalogu oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/directoryRoleTemplates/allProperties/allTasks | Tworzenie i usuwanie szablonów ról usługi Azure AD oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/domains/allProperties/allTasks | Tworzenie i usuwanie domen oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | Tworzenie i usuwanie zasobów oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure AD entitlement management |
> | microsoft.directory/groups/allProperties/allTasks | Tworzenie i usuwanie grup oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/groupsAssignableToRoles/create | Tworzenie grup z możliwością przypisania do roli |
> | microsoft.directory/groupsAssignableToRoles/delete | Usuwanie grup przypisywanych do ról |
> | microsoft.directory/groupsAssignableToRoles/restore | Przywracanie grup przypisywanych do ról |
> | microsoft.directory/groupsAssignableToRoles/allProperties/update | Aktualizowanie grup przypisywanych do ról |
> | microsoft.directory/groupSettings/allProperties/allTasks | Tworzenie i usuwanie ustawień grupy oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/groupSettingTemplates/allProperties/allTasks | Tworzenie i usuwanie szablonów ustawień grupy oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/identityProtection/allProperties/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w Azure AD Identity Protection |
> | microsoft.directory/loginOrganizationBranding/allProperties/allTasks | Tworzenie i usuwanie właściwości loginTenantBranding oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Tworzenie i usuwanie uprawnień uwierzytelniania OAuth 2.0 oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/organization/allProperties/allTasks | Tworzenie i usuwanie organizacji oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/policies/allProperties/allTasks | Tworzenie i usuwanie zasad oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/conditionalAccessPolicies/allProperties/allTasks | Zarządzanie wszystkimi właściwościami zasad dostępu warunkowego |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Odczytywanie wszystkich zasobów w Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Odczytywanie wszystkich właściwości dzienników aprowizowania |
> | microsoft.directory/roleAssignments/allProperties/allTasks | Tworzenie i usuwanie przypisań ról oraz odczytywanie i aktualizowanie wszystkich właściwości przypisania roli |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | Tworzenie i usuwanie definicji ról oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Tworzenie i usuwanie właściwości scopedRoleMemberships oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/serviceAction/activateService | Może wykonać akcję "aktywuj usługę" dla usługi |
> | microsoft.directory/serviceAction/disableDirectoryFeature | Może wykonać akcję usługi "Wyłącz funkcję katalogu" |
> | microsoft.directory/serviceAction/enableDirectoryFeature | Może wykonać akcję usługi "Włącz funkcję katalogu" |
> | microsoft.directory/serviceAction/getAvailableExtentionProperties | Może wykonać akcję usługi getAvailableExtentionProperties |
> | microsoft.directory/servicePrincipals/allProperties/allTasks | Tworzenie i usuwanie jednostki usługi oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-company-admin | Udzielanie zgody na wszelkie uprawnienia do dowolnej aplikacji |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Udzielanie jednostki usługi bezpośredniego dostępu do danych grupy |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Odczyt ustawień aprowizowania skojarzonych z jednostką usługi |
> | microsoft.directory/signInReports/allProperties/read | Odczytywanie wszystkich właściwości raportów logowania, w tym właściwości uprzywilejowanych |
> | microsoft.directory/subscribedSkus/allProperties/allTasks | Kupowanie subskrypcji i zarządzanie nimi oraz usuwanie subskrypcji |
> | microsoft.directory/users/allProperties/allTasks | Tworzenie i usuwanie użytkowników oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/permissionGrantPolicies/create | Tworzenie zasad udzielania uprawnień |
> | microsoft.directory/permissionGrantPolicies/delete | Usuwanie zasad udzielania uprawnień |
> | microsoft.directory/permissionGrantPolicies/standard/read | Odczytywanie standardowych właściwości zasad udzielania uprawnień |
> | microsoft.directory/permissionGrantPolicies/basic/update | Aktualizowanie podstawowych właściwości zasad udzielania uprawnień |
> | microsoft.directory/servicePrincipalCreationPolicies/create | Tworzenie zasad tworzenia jednostki usługi |
> | microsoft.directory/servicePrincipalCreationPolicies/delete | Usuwanie zasad tworzenia jednostki usługi |
> | microsoft.directory/servicePrincipalCreationPolicies/standard/read | Odczytywanie standardowych właściwości zasad tworzenia jednostki usługi |
> | microsoft.directory/servicePrincipalCreationPolicies/basic/update | Aktualizowanie podstawowych właściwości zasad tworzenia jednostki usługi |
> | microsoft.azure.advancedThreatProtection/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Azure Advanced Threat Protection |
> | microsoft.azure.informationProtection/allEntities/allTasks | Zarządzanie wszystkimi aspektami Azure Information Protection |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.commerce.billing/allEntities/allTasks | Zarządzanie wszystkimi aspektami rozliczeń usługi Office 365 |
> | microsoft.dynamics365/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Dynamics 365 |
> | microsoft.flow/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Microsoft Power Automate |
> | microsoft.intune/allEntities/allTasks | Zarządzanie wszystkimi aspektami Microsoft Intune |
> | microsoft.office365.complianceManager/allEntities/allTasks | Zarządzanie wszystkimi aspektami Menedżera zgodności usługi Office 365 |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | Zarządzanie wszystkimi aspektami Desktop Analytics |
> | microsoft.office365.exchange/allEntities/basic/allTasks | Zarządzanie wszystkimi aspektami usługi Exchange Online |
> | microsoft.office365.lockbox/allEntities/allTasks | Zarządzanie wszystkimi aspektami Skrytka klienta |
> | microsoft.office365.messageCenter/messages/read | Odczytywanie komunikatów Centrum wiadomości w centrum administracyjne platformy Microsoft 365, z wyjątkiem komunikatów zabezpieczeń |
> | microsoft.office365.messageCenter/securityMessages/read | Odczytywanie komunikatów zabezpieczeń Centrum wiadomości w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.network/performance/allProperties/read | Odczytywanie wszystkich właściwości wydajności sieci w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.protectionCenter/allEntities/allProperties/allTasks | Zarządzanie wszystkimi aspektami centrów zabezpieczeń i zgodności |
> | microsoft.office365.search/content/manage | Tworzenie i usuwanie zawartości oraz odczytywanie i aktualizowanie wszystkich właściwości w uwitrynie Microsoft Search |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w Centrum Microsoft 365 zabezpieczeń i zgodności |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.sharePoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w programie SharePoint |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Skype dla firm Online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Odczytywanie raportów użycia usługi Office 365 |
> | microsoft.office365.userCommunication/allEntities/allTasks | Odczytywanie i aktualizowanie widoczności nowych komunikatów |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |
> | microsoft.powerApps/allEntities/allTasks | Zarządzanie wszystkimi aspektami Power Apps |
> | microsoft.powerApps.powerBI/allEntities/allTasks | Zarządzanie wszystkimi aspektami Power BI |
> | microsoft.windows.defenderAdvancedThreatProtection/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Microsoft Defender dla punktu końcowego |

## <a name="global-reader"></a>Czytelnik globalny

Użytkownicy w tej roli mogą odczytywać ustawienia i informacje administracyjne Microsoft 365 usługach, ale nie mogą podjąć akcji zarządzania. Czytelnik globalny jest odpowiednikiem administratora globalnego tylko do odczytu. Przypisz czytelnika globalnego zamiast administratora globalnego do planowania, inspekcji lub badań. Użyj czytelnika globalnego w połączeniu z innymi ograniczonymi rolami administratora, takich jak Administrator programu Exchange, aby ułatwić pracę bez przypisywania roli administratora globalnego. Czytelnik globalny współpracuje z centrum administracyjne platformy Microsoft 365, centrum administracyjnym programu Exchange, centrum administracyjnym programu SharePoint, centrum administracyjnym usługi Teams, Centrum zabezpieczeń, Centrum zgodności, centrum administracyjnym usługi Azure AD Zarządzanie urządzeniami centrum administracyjnym.

> [!NOTE]
> Rola czytelnika globalnego ma teraz kilka ograniczeń —
>
>- [Centrum administracyjne usługi OneDrive](https://admin.onedrive.com/) — centrum administracyjne usługi OneDrive nie obsługuje roli Czytelnik globalny
>- [Centrum administracyjne M365 —](https://admin.microsoft.com/Adminportal/Home#/homepage) czytelnik globalny nie może odczytywać zintegrowanych aplikacji. Karta Zintegrowane aplikacje nie znajduje się w **obszarze** **Ustawienia** w lewym okienku Centrum administracyjnego M365.
>- [Centrum zgodności & zabezpieczeń](https://sip.protection.office.com/homepage) pakietu Office — czytelnik globalny nie może odczytywać dzienników inspekcji SCC, przeszukiwać zawartości ani zobaczyć bezpiecznego wyniku.
>- [Centrum administracyjne usługi Teams](https://admin.teams.microsoft.com) — czytelnik globalny nie może odczytywać informacji o cyklu życia aplikacji **Teams,** raportach analizy **&,** zarządzaniu urządzeniami ip **przez** telefon i **katalogu aplikacji.**
>- [Privileged Access Management (PAM)](/office365/securitycompliance/privileged-access-management-overview) nie obsługuje roli czytelnika globalnego.
>- [Azure Information Protection](/azure/information-protection/what-is-information-protection) — czytnik globalny jest [](/azure/information-protection/reports-aip) obsługiwany tylko w przypadku centralnego raportowania, a Twoja organizacja usługi Azure AD nie znajduje się na ujednoliconej [platformie etykietowania.](/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)
>
> Te funkcje są obecnie w trakcie opracowywania.
>

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/applications/applicationProxy/read | Odczytywanie wszystkich właściwości serwera proxy aplikacji |
> | microsoft.directory/applications/synchronization/standard/read | Odczyt ustawień aprowizowania skojarzonych z obiektem aplikacji |
> | microsoft.directory/auditLogs/allProperties/read | Odczytywanie wszystkich właściwości dzienników inspekcji, w tym właściwości uprzywilejowanych |
> | microsoft.directory/bitlockerKeys/key/read | Odczytywanie metadanych i klucza funkcji BitLocker na urządzeniach |
> | microsoft.directory/connectors/allProperties/read | Odczytywanie wszystkich właściwości łączników serwera proxy aplikacji |
> | microsoft.directory/connectorGroups/allProperties/read | Odczytywanie wszystkich właściwości grup łączników serwera proxy aplikacji |
> | microsoft.directory/entitlementManagement/allProperties/read | Odczytywanie wszystkich właściwości w usłudze Azure AD entitlement management |
> | microsoft.directory/deviceManagementPolicies/standard/read | Odczytywanie standardowych właściwości zasad aplikacji do zarządzania urządzeniami |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Odczytywanie standardowych właściwości zasad rejestracji urządzeń |
> | microsoft.directory/groups/hiddenMembers/read | Odczytywanie ukrytych członków grupy |
> | microsoft.directory/policies/standard/read | Odczytywanie podstawowych właściwości zasad |
> | microsoft.directory/policies/owners/read | Odczytywanie właścicieli zasad |
> | microsoft.directory/policies/policyAppliedTo/read | Odczytywanie właściwości policies.policyAppliedTo |
> | microsoft.directory/conditionalAccessPolicies/standard/read | Odczyt dostępu warunkowego dla zasad |
> | microsoft.directory/conditionalAccessPolicies/owners/read | Odczytywanie właścicieli zasad dostępu warunkowego |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Odczytywanie właściwości "zastosowane do" dla zasad dostępu warunkowego |
> | microsoft.directory/provisioningLogs/allProperties/read | Odczytywanie wszystkich właściwości dzienników aprowizowania |
> | microsoft.directory/servicePrincipals/authentication/read | Odczytywanie właściwości uwierzytelniania dla jednostki usługi |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Odczyt ustawień aprowizowania skojarzonych z jednostką usługi |
> | microsoft.directory/signInReports/allProperties/read | Odczytywanie wszystkich właściwości raportów logowania, w tym właściwości uprzywilejowanych |
> | microsoft.directory/users/strongAuthentication/read | Odczytywanie właściwości silnego uwierzytelniania dla użytkowników |
> | microsoft.commerce.billing/allEntities/read | Odczytywanie wszystkich zasobów rozliczeń usługi Office 365 |
> | microsoft.office365.exchange/allEntities/standard/read | Odczytywanie wszystkich zasobów usługi Exchange Online |
> | microsoft.office365.messageCenter/messages/read | Odczytywanie komunikatów Centrum wiadomości w centrum administracyjne platformy Microsoft 365, z wyłączeniem komunikatów zabezpieczeń |
> | microsoft.office365.messageCenter/securityMessages/read | Odczytywanie komunikatów zabezpieczeń Centrum wiadomości w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.network/performance/allProperties/read | Odczytaj wszystkie właściwości wydajności sieci w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.protectionCenter/allEntities/allProperties/read | Odczytywanie wszystkich właściwości w centrach zabezpieczeń i zgodności |
> | microsoft.office365.securityComplianceCenter/allEntities/read | Odczytywanie standardowych właściwości Microsoft 365 Centrum zabezpieczeń i zgodności |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Odczytywanie raportów użycia usługi Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="groups-administrator"></a>Administrator grup

Użytkownicy w tej roli mogą tworzyć grupy i zarządzać nimi oraz ich ustawieniami, na przykład zasadami nazewnictwa i wygasania. Ważne jest, aby zrozumieć, że przypisanie użytkownika do tej roli daje mu możliwość zarządzania wszystkimi grupami w organizacji w różnych obciążeniach, takich jak Teams, SharePoint, Yammer, oprócz programu Outlook. Ponadto użytkownik będzie mógł zarządzać różnymi ustawieniami grup w różnych portalach administracyjnym, takich jak Centrum administracyjne firmy Microsoft, Azure Portal, a także ustawieniami specyficznymi dla obciążenia, np. zespołami i centrami administracyjnym programu SharePoint.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/groups/assignLicense | Przypisywanie licencji produktów do grup w celu licencjonowania opartego na grupach |
> | microsoft.directory/groups/create | Tworzenie grup z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/delete | Usuwanie grup z wyłączeniem grupy przypisanej do ról |
> | microsoft.directory/groups/hiddenMembers/read | Odczytywanie ukrytych członków grupy |
> | microsoft.directory/groups/reprocessLicenseAssignment | Ponowne przetwarzanie przypisań licencji dla licencjonowania opartego na grupach |
> | microsoft.directory/groups/restore | Przywracanie usuniętych grup |
> | microsoft.directory/groups/basic/update | Aktualizowanie podstawowych właściwości grup z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/classification/update | Aktualizowanie właściwości klasyfikacji grup, z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/dynamicMembershipRule/update | Aktualizowanie reguły członkostwa dynamicznego grup, z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/groupType/update | Aktualizowanie właściwości groupType grupy |
> | microsoft.directory/groups/members/update | Aktualizowanie członków grup z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/onPremWriteBack/update | Aktualizowanie Azure Active Directory, które mają być zapisywane z powrotem w środowisku lokalnym przy użyciu Azure AD Connect |
> | microsoft.directory/groups/owners/update | Aktualizowanie właścicieli grup z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/settings/update | Aktualizowanie ustawień grup |
> | microsoft.directory/groups/visibility/update | Aktualizowanie właściwości widoczności grup |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Udzielanie jednostki usługi bezpośredniego dostępu do danych grupy |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="guest-inviter"></a>Zapraszanie gości

Użytkownicy w tej roli mogą zarządzać Azure Active Directory użytkownika-gościa  B2B, gdy ustawienie Członkowie mogą zapraszać użytkowników ma wartość Nie. Więcej informacji na temat współpracy B2B można znaleźć na stronie About Azure AD B2B collaboration (Informacje o współpracy [B2B w usłudze Azure AD).](../external-identities/what-is-b2b.md) Nie obejmuje żadnych innych uprawnień.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/users/inviteGuest | Zapraszanie gości |
> | microsoft.directory/users/standard/read | Odczytywanie podstawowych właściwości użytkowników |
> | microsoft.directory/users/appRoleAssignments/read | Odczytywanie przypisań ról aplikacji użytkowników |
> | microsoft.directory/users/directReports/read | Odczytywanie raportów bezpośrednich dla użytkowników |
> | microsoft.directory/users/manager/read | Odczytywanie menedżera użytkowników |
> | microsoft.directory/users/memberOf/read | Odczytywanie członkosstwa użytkowników w grupach |
> | microsoft.directory/users/oAuth2PermissionGrants/read | Odczytywanie delegowanych uprawnień udzielanych użytkownikom |
> | microsoft.directory/users/ownedDevices/read | Odczytywanie posiadanych urządzeń użytkowników |
> | microsoft.directory/users/ownedObjects/read | Odczytywanie posiadanych obiektów użytkowników |
> | microsoft.directory/users/registeredDevices/read | Odczytywanie zarejestrowanych urządzeń użytkowników |

## <a name="helpdesk-administrator"></a>Administrator pomocy technicznej

Użytkownicy z tą rolą mogą zmieniać hasła, unieważniać tokeny odświeżania, zarządzać żądaniami obsługi i monitorować kondycję usługi. Unieważnienie tokenu odświeżania wymusza na użytkowniku zalogowanie się ponownie. To, czy administrator pomocy technicznej może zresetować hasło użytkownika i unieważnić tokeny odświeżania, zależy od przypisanej roli użytkownika. Aby uzyskać listę ról, dla których administrator pomocy technicznej może resetować hasła i unieważniać tokeny odświeżania, zobacz [Uprawnienia do resetowania haseł.](#password-reset-permissions)

> [!IMPORTANT]
> Użytkownicy z tą rolą mogą zmieniać hasła osób, które mogą mieć dostęp do poufnych lub prywatnych informacji albo do konfiguracji krytycznej wewnątrz i na zewnątrz Azure Active Directory. Zmiana hasła użytkownika może oznaczać możliwość założenia tożsamości i uprawnień tego użytkownika. Na przykład:
>
>- Właściciele rejestracji aplikacji i aplikacji dla przedsiębiorstw, którzy mogą zarządzać poświadczeniami posiadanych aplikacji. Te aplikacje mogą mieć uprzywilejowane uprawnienia w usłudze Azure AD i w innym miejscu nie są przyznawane administratorom pomocy technicznej. Za pomocą tej ścieżki administrator pomocy technicznej może przyjąć tożsamość właściciela aplikacji, a następnie dalej przyjąć tożsamość aplikacji uprzywilejowanej, aktualizując poświadczenia aplikacji.
>- Właściciele subskrypcji platformy Azure, którzy mogą mieć dostęp do informacji poufnych lub prywatnych albo do konfiguracji o krytycznym znaczeniu na platformie Azure.
>- Grupa zabezpieczeń i Microsoft 365 grupy zabezpieczeń, którzy mogą zarządzać członkostwem w grupie. Grupy te mogą udzielać dostępu do informacji poufnych lub prywatnych albo do konfiguracji krytycznej w usłudze Azure AD i w innych miejscach.
>- Administratorzy w innych usługach poza usługą Azure AD, takich jak Exchange Online, Centrum zabezpieczeń i zgodności pakietu Office oraz systemy zasobów ludzkich.
>- Osoby inne niż administratorzy, np. kadry kierownicze, pracownicy prawni i pracownicy ds. zasobów ludzkich, którzy mogą mieć dostęp do informacji poufnych lub prywatnych.

Delegowanie uprawnień administracyjnych dla podzbiorów użytkowników i stosowanie zasad do podzbioru użytkowników jest możliwe za pomocą [jednostek administracyjnych.](administrative-units.md)

Ta rola była wcześniej nazywana "administratorem haseł" w [Azure Portal](https://portal.azure.com/). Nazwa "Administrator pomocy technicznej" w usłudze Azure AD jest teraz odpowiada jej nazwie w programie Azure AD PowerShell i interfejsie API Microsoft Graph API.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/bitlockerKeys/key/read | Odczytywanie metadanych i klucza funkcji BitLocker na urządzeniach |
> | microsoft.directory/users/invalidateAllRefreshTokens | Wymuszanie wylogowania przez unieważnienie tokenów odświeżania użytkownika |
> | microsoft.directory/users/password/update | Resetowanie haseł dla wszystkich użytkowników |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="hybrid-identity-administrator"></a>Administrator tożsamości hybrydowej

Użytkownicy w tej roli mogą tworzyć i wdrażać konfigurację aprowizowania oraz zarządzać nimi z usługi AD do usługi Azure AD przy użyciu aprowowania w chmurze, a także zarządzać ustawieniami Azure AD Connect federacyjnymi. Użytkownicy mogą również rozwiązywać problemy i monitorować dzienniki przy użyciu tej roli.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/applications/create | Tworzenie wszystkich typów aplikacji |
> | microsoft.directory/applications/delete | Usuwanie wszystkich typów aplikacji |
> | microsoft.directory/applications/appRoles/update | Aktualizowanie właściwości appRoles we wszystkich typach aplikacji |
> | microsoft.directory/applications/audience/update | Aktualizowanie właściwości odbiorców dla aplikacji |
> | microsoft.directory/applications/authentication/update | Aktualizowanie uwierzytelniania we wszystkich typach aplikacji |
> | microsoft.directory/applications/basic/update | Aktualizowanie podstawowych właściwości aplikacji |
> | microsoft.directory/applications/credentials/update | Aktualizowanie poświadczeń aplikacji |
> | microsoft.directory/applications/owners/update | Aktualizowanie właścicieli aplikacji |
> | microsoft.directory/applications/permissions/update | Aktualizowanie ujawnionych uprawnień i wymaganych uprawnień do wszystkich typów aplikacji |
> | microsoft.directory/applications/policies/update | Aktualizowanie zasad aplikacji |
> | microsoft.directory/applications/synchronization/standard/read | Odczyt ustawień aprowizowania skojarzonych z obiektem aplikacji |
> | microsoft.directory/applicationTemplates/instantiate | Tworzenia wystąpienia aplikacji galerii na podstawie szablonów aplikacji |
> | microsoft.directory/auditLogs/allProperties/read | Odczytywanie wszystkich właściwości dzienników inspekcji, w tym właściwości uprzywilejowanych |
> | microsoft.directory/cloudProvisioning/allProperties/allTasks | Odczytywanie i konfigurowanie wszystkich właściwości usługi Azure AD Cloud Provisioning. |
> | microsoft.directory/domains/allProperties/read | Odczytywanie wszystkich właściwości domen |
> | microsoft.directory/domains/federation/update | Aktualizowanie właściwości federacji domen |
> | microsoft.directory/organization/dirSync/update | Aktualizowanie właściwości synchronizacji katalogu organizacji |
> | microsoft.directory/provisioningLogs/allProperties/read | Odczytywanie wszystkich właściwości dzienników aprowizowania |
> | microsoft.directory/servicePrincipals/create | Tworzenie jednostek usługi |
> | microsoft.directory/servicePrincipals/delete | Usuwanie jednostki usługi |
> | microsoft.directory/servicePrincipals/disable | Wyłączanie jednostki usługi |
> | microsoft.directory/servicePrincipals/enable | Włączanie jednostek usługi |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Zarządzanie wpisami tajnymi i poświadczeniami aprowiowania aplikacji |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Uruchamianie, ponowne uruchamianie i wstrzymywanie zadań synchronizacji aprowizacji aplikacji |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Tworzenie zadań synchronizacji i schematu aprowizacji aplikacji i zarządzanie nimi |
> | microsoft.directory/servicePrincipals/audience/update | Aktualizowanie właściwości odbiorców w podmiotach zabezpieczeń usługi |
> | microsoft.directory/servicePrincipals/authentication/update | Aktualizowanie właściwości uwierzytelniania dla jednostki usługi |
> | microsoft.directory/servicePrincipals/basic/update | Aktualizowanie podstawowych właściwości jednostki usługi |
> | microsoft.directory/servicePrincipals/credentials/update | Aktualizowanie poświadczeń jednostki usługi |
> | microsoft.directory/servicePrincipals/owners/update | Aktualizowanie właścicieli jednostki usługi |
> | microsoft.directory/servicePrincipals/permissions/update | Aktualizowanie uprawnień jednostki usługi |
> | microsoft.directory/servicePrincipals/policies/update | Aktualizowanie zasad jednostki usługi |
> | microsoft.directory/servicePrincipals/tag/update | Aktualizowanie właściwości tagu dla jednostki usługi |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Odczyt ustawień aprowizowania skojarzonych z jednostką usługi |
> | microsoft.directory/signInReports/allProperties/read | Odczytywanie wszystkich właściwości raportów logowania, w tym właściwości uprzywilejowanych |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.messageCenter/messages/read | Odczytywanie komunikatów Centrum wiadomości w centrum administracyjne platformy Microsoft 365, z wyjątkiem komunikatów zabezpieczeń |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="insights-administrator"></a>Administrator szczegółowych informacji

Użytkownicy w tej roli mogą uzyskać dostęp do pełnego zestawu funkcji administracyjnych w aplikacji [M365 Insights.](https://go.microsoft.com/fwlink/?linkid=2129521) Ta rola ma możliwość odczytywania informacji o katalogu, monitorowania kondycji usługi, biletów pomocy technicznej plików i uzyskiwania dostępu do aspektów ustawień administratora usługi Insights.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.insights/allEntities/allTasks | Zarządzanie wszystkimi aspektami aplikacji Insights |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="insights-business-leader"></a>Insights Business Leader

Użytkownicy w tej roli mogą uzyskać dostęp do zestawu pulpitów nawigacyjnych i szczegółowych informacji za pośrednictwem aplikacji [M365 Insights.](https://go.microsoft.com/fwlink/?linkid=2129521) Obejmuje to pełny dostęp do wszystkich pulpitów nawigacyjnych oraz prezentowanych szczegółowych informacji i funkcji eksploracji danych. Użytkownicy w tej roli nie mają dostępu do ustawień konfiguracji produktu, za co odpowiada rola administratora usługi Insights.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.insights/reports/read | Wyświetlanie raportów i pulpitu nawigacyjnego w aplikacji Szczegółowych informacji |
> | microsoft.insights/programs/update | Wdrażanie programów i zarządzanie nimi w aplikacji Insights |

## <a name="intune-administrator"></a>Intune Administrator

Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Intune Online, gdy usługa jest obecna. Ponadto ta rola obejmuje możliwość zarządzania użytkownikami i urządzeniami w celu skojarzenia zasad, a także tworzenia grup i zarządzania nimi. Więcej informacji można znaleźć [na stronie Kontrola administracji opartej na rolach (RBAC) przy użyciu Microsoft Intune](/intune/role-based-access-control).

Ta rola może tworzyć wszystkie grupy zabezpieczeń i zarządzać nimi. Jednak administrator usługi Intune nie ma uprawnień administratora do grup pakietu Office. Oznacza to, że administrator nie może zaktualizować właścicieli lub członkossstwa wszystkich grup pakietu Office w organizacji. Może jednak zarządzać grupę pakietu Office, którą tworzy, która jest częścią jego uprawnień użytkownika końcowego. W związku z tym każda grupę pakietu Office (nie grupę zabezpieczeń), która tworzy, należy wliczyć do limitu przydziału 250.

> [!NOTE]
> W interfejsie API Microsoft Graph Azure AD PowerShell ta rola jest identyfikowana jako "Administrator usługi Intune". Jest to "Administrator usługi Intune" w [Azure Portal](https://portal.azure.com).

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/bitlockerKeys/key/read | Odczytywanie metadanych i klucza funkcji BitLocker na urządzeniach |
> | microsoft.directory/contacts/create | Tworzenie kontaktów |
> | microsoft.directory/contacts/delete | Usuwanie kontaktów |
> | microsoft.directory/contacts/basic/update | Aktualizowanie podstawowych właściwości kontaktów |
> | microsoft.directory/devices/create | Tworzenie urządzeń (rejestrowanie w usłudze Azure AD) |
> | microsoft.directory/devices/delete | Usuwanie urządzeń z usługi Azure AD |
> | microsoft.directory/devices/disable | Wyłączanie urządzeń w usłudze Azure AD |
> | microsoft.directory/devices/enable | Włączanie urządzeń w usłudze Azure AD |
> | microsoft.directory/devices/basic/update | Aktualizowanie podstawowych właściwości na urządzeniach |
> | microsoft.directory/devices/extensionAttributes/update | Aktualizowanie wszystkich wartości właściwości devices.extensionAttributes |
> | microsoft.directory/devices/registeredOwners/update | Aktualizowanie zarejestrowanych właścicieli urządzeń |
> | microsoft.directory/devices/registeredUsers/update | Aktualizowanie zarejestrowanych użytkowników urządzeń |
> | microsoft.directory/deviceManagementPolicies/standard/read | Odczytywanie standardowych właściwości zasad aplikacji do zarządzania urządzeniami |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Odczytywanie standardowych właściwości zasad rejestracji urządzeń |
> | microsoft.directory/groups/hiddenMembers/read | Odczytywanie ukrytych członków grupy |
> | microsoft.directory/groups.security/create | Tworzenie grup zabezpieczeń z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/groups.security/delete | Usuwanie grup zabezpieczeń z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/groups.security/basic/update | Aktualizowanie podstawowych właściwości grup zabezpieczeń z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/groups.security/classification/update | Aktualizowanie właściwości klasyfikacji grup zabezpieczeń z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/groups.security/dynamicMembershipRule/update | Aktualizacja właściwości dynamicMembershipRule grup zabezpieczeń z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/groups.security/members/update | Aktualizowanie członków grup zabezpieczeń z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/groups.security/owners/update | Aktualizowanie właścicieli grup zabezpieczeń z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/groups.security/visibility/update | Aktualizowanie właściwości widoczności grup zabezpieczeń z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/users/basic/update | Aktualizowanie podstawowych właściwości użytkowników |
> | microsoft.directory/users/manager/update | Menedżer aktualizacji dla użytkowników |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.intune/allEntities/allTasks | Zarządzanie wszystkimi aspektami Microsoft Intune |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="kaizala-administrator"></a>Kaizala Administrator

Użytkownicy z tą rolą mają globalne uprawnienia do zarządzania ustawieniami w usłudze Microsoft Kaizala, gdy usługa jest obecna, a także możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Ponadto użytkownik może uzyskać dostęp do raportów dotyczących & dotyczących użycia usługi Kaizala przez członków organizacji i raportów biznesowych generowanych przy użyciu akcji usługi Kaizala.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="knowledge-administrator"></a>Administrator wiedzy

Użytkownicy pełniący tę rolę mają pełny dostęp do wszystkich ustawień wiedzy, uczenia i inteligentnych funkcji w centrum administracyjne platformy Microsoft 365. Mają ogólną wiedzę na temat pakietu produktów, szczegółów licencjonowania i mają odpowiedzialność za kontrolowanie dostępu. Administrator wiedzy może tworzyć zawartość, na przykład tematy, akronimy i zasoby szkoleniowe, oraz zarządzać nimi. Ponadto użytkownicy ci mogą tworzyć centra zawartości, monitorować kondycję usługi i tworzyć żądania obsługi.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/groups.security/create | Tworzenie grup zabezpieczeń z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/groups.security/createAsOwner | Tworzenie grup zabezpieczeń z wykluczeniem grup przypisywanych do ról, a twórca jest dodawany jako pierwszy właściciel |
> | microsoft.directory/groups.security/delete | Usuwanie grup zabezpieczeń z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/groups.security/basic/update | Aktualizowanie podstawowych właściwości grup zabezpieczeń z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/groups.security/members/update | Aktualizowanie członków grup zabezpieczeń z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/groups.security/owners/update | Aktualizowanie właścicieli grup zabezpieczeń z wykluczeniem grup przypisywanych do ról |
> | microsoft.office365.knowledge/contentUnderstanding/allProperties/allTasks | Odczytywanie i aktualizowanie wszystkich właściwości zrozumienia zawartości w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.knowledge/knowledgeNetwork/allProperties/allTasks | Odczytywanie i aktualizowanie wszystkich właściwości sieci wiedzy w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.protectionCenter/sensitivityLabels/allProperties/read | Odczytywanie wszystkich właściwości etykiet czułości w centrach zabezpieczeń i zgodności |
> | microsoft.office365.sharePoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w programie SharePoint |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="license-administrator"></a>Administrator licencji

Użytkownicy w tej roli mogą dodawać, usuwać i aktualizować przypisania licencji użytkowników, grup (przy użyciu licencjonowania opartego na grupach) oraz zarządzać lokalizacją użycia użytkowników. Rola nie daje możliwości zakupu subskrypcji ani zarządzania nimi, tworzenia grup ani zarządzania nimi ani tworzenia użytkowników poza lokalizacją użycia. Ta rola nie ma dostępu do wyświetlania i tworzenia biletów pomocy technicznej ani zarządzania nimi.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/groups/assignLicense | Przypisywanie licencji produktów do grup w celu licencjonowania opartego na grupach |
> | microsoft.directory/groups/reprocessLicenseAssignment | Ponowne przetwarzanie przypisań licencji dla licencjonowania opartego na grupach |
> | microsoft.directory/users/assignLicense | Zarządzanie licencjami użytkowników |
> | microsoft.directory/users/reprocessLicenseAssignment | Ponowne przetwarzanie przypisań licencji dla użytkowników |
> | microsoft.directory/users/usageLocation/update | Aktualizowanie lokalizacji użycia użytkowników |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="message-center-privacy-reader"></a>Centrum wiadomości czytelnika prywatności

Użytkownicy w tej roli mogą monitorować wszystkie powiadomienia w Centrum wiadomości, w tym komunikaty dotyczące prywatności danych. Centrum wiadomości osób, które chcą uzyskać powiadomienia e-mail dotyczące prywatności danych, mogą anulować subskrypcję przy użyciu Centrum wiadomości preferencji. Tylko administrator globalny i czytelnik Centrum wiadomości prywatności mogą odczytywać komunikaty dotyczące prywatności danych. Ponadto ta rola zawiera możliwość wyświetlania grup, domen i subskrypcji. Ta rola nie ma uprawnień do wyświetlania i tworzenia żądań obsługi ani zarządzania nimi.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Odczytywanie komunikatów Centrum wiadomości w centrum administracyjne platformy Microsoft 365, z wyjątkiem komunikatów zabezpieczeń |
> | microsoft.office365.messageCenter/securityMessages/read | Odczytywanie komunikatów zabezpieczeń Centrum wiadomości w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="message-center-reader"></a>Centrum wiadomości czytelnika

Użytkownicy w tej roli mogą monitorować [](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) powiadomienia i aktualizacje porad dotyczących kondycji w Centrum wiadomości dla swojej organizacji w zakresie skonfigurowanych usług, takich jak Exchange, Intune i Microsoft Teams. Centrum wiadomości czytelnicy otrzymują cotygodniowe skróty wiadomości e-mail z wpisami i aktualizacjami oraz mogą udostępniać wpisy w centrum wiadomości w Microsoft 365. W usłudze Azure AD użytkownicy przypisani do tej roli będą mieć dostęp tylko do odczytu w usługach Azure AD, takich jak użytkownicy i grupy. Ta rola nie ma dostępu do wyświetlania i tworzenia biletów pomocy technicznej ani zarządzania nimi.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Odczytywanie komunikatów Centrum wiadomości w centrum administracyjne platformy Microsoft 365, z wyjątkiem komunikatów zabezpieczeń |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="modern-commerce-user"></a>Nowoczesny użytkownik handlowy

Nie używaj. Ta rola jest automatycznie przypisywana z usługi Commerce i nie jest przeznaczona ani obsługiwana do żadnego innego użycia. Zobacz szczegóły poniżej.

Rola Nowoczesnych użytkowników handlowych daje określonym użytkownikom uprawnienia dostępu do aplikacji centrum administracyjne platformy Microsoft 365 i wyświetlanie pozycji nawigacji po lewej stronie dla pozycji **Strona** **główna,** Rozliczenia i **Pomoc techniczna.** Zawartość dostępna w tych obszarach [](../../cost-management-billing/manage/understand-mca-roles.md) jest kontrolowana przez role specyficzne dla handlu przypisane do użytkowników w celu zarządzania produktami, które kupili dla siebie lub Twojej organizacji. Może to obejmować zadania, takie jak płacenie rachunku lub dostęp do kont rozliczeniowych i profilów rozliczeniowych.

Użytkownicy z rolą Nowoczesnego użytkownika handlowego zwykle mają uprawnienia administracyjne w innych systemach zakupów firmy Microsoft, ale nie mają ról administratora globalnego ani ról administrator rozliczeń używanych do uzyskiwania dostępu do centrum administracyjnego.

**Kiedy jest przypisana rola nowoczesnego użytkownika handlowego?**

* **Zakup samoobsługowy w usłudze centrum administracyjne platformy Microsoft 365** — zakup samoobsługowy daje użytkownikom możliwość wypróbowania nowych produktów przez zakup lub zarejestrowanie się dla nich samodzielnie. Te produkty są zarządzane w centrum administracyjnym. Użytkownicy, którzy dokonają zakupu samoobsługowego, mają przypisaną rolę w systemie handlowym oraz rolę Nowoczesnego użytkownika handlowego, która umożliwia zarządzanie zakupami w centrum administracyjnym. Administratorzy mogą blokować zakupy samoobsługowe (na Power BI, Power Apps, Power Automate) za pośrednictwem [programu PowerShell.](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell) Aby uzyskać więcej informacji, zobacz [Zakup samoobsługowy — często zadawane pytania](/microsoft-365/commerce/subscriptions/self-service-purchase-faq).
* Zakupy na platformie handlowej firmy **Microsoft** — podobnie jak w przypadku zakupu samoobsługowego, gdy użytkownik kupuje produkt lub usługę w usłudze Microsoft AppSource lub Azure Marketplace, jest przypisywana rola Nowoczesnego użytkownika handlowego, jeśli nie ma roli administratora globalnego lub administratora rozliczeń. W niektórych przypadkach użytkownicy mogą mieć zablokowaną opcję dokonywania tych zakupów. Aby uzyskać więcej informacji, zobacz [Platforma handlowa firmy Microsoft.](../../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase)
* **Propozycje firmy Microsoft** — propozycja jest formalną ofertą firmy Microsoft dla Organizacji w zakresie zakupu produktów i usług firmy Microsoft. Gdy osoba akceptująca propozycję nie ma roli administratora globalnego ani administratora rozliczeń w usłudze Azure AD, ma przypisaną rolę specyficzną dla handlu w celu ukończenia propozycji oraz rolę nowoczesnego użytkownika handlowego w celu uzyskania dostępu do centrum administracyjnego. Podczas uzyskiwania dostępu do centrum administracyjnego mogą korzystać tylko z funkcji autoryzowanych przez ich rolę specyficzną dla handlu.
* **Role specyficzne dla handlu —** niektórzy użytkownicy mają przypisane role specyficzne dla handlu. Jeśli użytkownik nie jest administratorem globalnym ani administratorem rozliczeń, otrzyma rolę Nowoczesnego użytkownika handlowego, aby uzyskać dostęp do centrum administracyjnego.

Jeśli rola Nowoczesnego użytkownika handlowego nie jest przypisana do użytkownika, utraci dostęp do centrum administracyjne platformy Microsoft 365. Osoby zarządzające produktami, zarówno dla siebie, jak i dla organizacji, nie będą mogły nimi zarządzać. Może to obejmować przypisywanie licencji, zmienianie metod płatności, płacenie rachunku lub inne zadania związane z zarządzaniem subskrypcjami.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.commerce.billing/partners/read | Odczytywanie właściwości partnera Microsoft 365 Billing |
> | microsoft.commerce.volumeLicenseServiceCenter/allEntities/allTasks | Zarządzanie wszystkimi aspektami centrum usługi licencjonowania zbiorowego |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="network-administrator"></a>Administrator sieci

Użytkownicy w tej roli mogą przeglądać zalecenia firmy Microsoft dotyczące architektury obwodowej sieci, które są oparte na telemetrii sieci z lokalizacji użytkowników. Wydajność sieci dla Microsoft 365 opiera się na dokładnej architekturze obwodowej sieci klienta przedsiębiorstwa, która zazwyczaj jest specyficzna dla lokalizacji użytkownika. Ta rola umożliwia edytowanie odnalezionych lokalizacji użytkowników i konfigurowanie parametrów sieciowych dla tych lokalizacji w celu ułatwienia ulepszonych pomiarów telemetrii i rekomendacji dotyczących projektowania

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.office365.network/locations/allProperties/allTasks | Zarządzanie wszystkimi aspektami lokalizacji sieciowych |
> | microsoft.office365.network/performance/allProperties/read | Odczytywanie wszystkich właściwości wydajności sieci w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="office-apps-administrator"></a>Administrator aplikacji pakietu Office

Użytkownicy w tej roli mogą zarządzać Microsoft 365 chmury aplikacji. Obejmuje to zarządzanie zasadami chmury, samoobsługowe zarządzanie pobieraniem oraz możliwość wyświetlania raportu związanego z aplikacjami pakietu Office. Ta rola dodatkowo daje możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi w głównym centrum administracyjnym. Użytkownicy przypisani do tej roli mogą również zarządzać komunikacją nowych funkcji w aplikacjach pakietu Office.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.messageCenter/messages/read | Odczytywanie komunikatów Centrum wiadomości w centrum administracyjne platformy Microsoft 365, z wyłączeniem komunikatów zabezpieczeń |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.userCommunication/allEntities/allTasks | Odczytywanie i aktualizowanie widoczności nowych komunikatów |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="partner-tier1-support"></a>Pomoc techniczna dla partnerów w warstwie 1

Nie używaj. Ta rola została wycofana i zostanie usunięta z usługi Azure AD w przyszłości. Ta rola jest przeznaczona do użytku przez niewielką liczbę partnerów handlowych firmy Microsoft i nie jest przeznaczona do użytku ogólnego.

> [!IMPORTANT]
> Ta rola może resetować hasła i unieważniać tokeny odświeżania tylko dla użytkowników niebędących administratorami. Tej roli nie należy używać, ponieważ jest przestarzała i nie będzie już zwracana w interfejsie API.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/applications/appRoles/update | Aktualizowanie właściwości appRoles we wszystkich typach aplikacji |
> | microsoft.directory/applications/audience/update | Aktualizowanie właściwości odbiorców dla aplikacji |
> | microsoft.directory/applications/authentication/update | Aktualizowanie uwierzytelniania we wszystkich typach aplikacji |
> | microsoft.directory/applications/basic/update | Aktualizowanie podstawowych właściwości aplikacji |
> | microsoft.directory/applications/credentials/update | Aktualizowanie poświadczeń aplikacji |
> | microsoft.directory/applications/owners/update | Aktualizowanie właścicieli aplikacji |
> | microsoft.directory/applications/permissions/update | Aktualizowanie ujawnionych uprawnień i wymaganych uprawnień we wszystkich typach aplikacji |
> | microsoft.directory/applications/policies/update | Aktualizowanie zasad aplikacji |
> | microsoft.directory/contacts/create | Tworzenie kontaktów |
> | microsoft.directory/contacts/delete | Usuwanie kontaktów |
> | microsoft.directory/contacts/basic/update | Aktualizowanie podstawowych właściwości kontaktów |
> | microsoft.directory/groups/create | Tworzenie grup z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/delete | Usuwanie grup z wyłączeniem grupy przypisanej do roli |
> | microsoft.directory/groups/restore | Przywracanie usuniętych grup |
> | microsoft.directory/groups/members/update | Aktualizowanie członków grup z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/owners/update | Aktualizowanie właścicieli grup z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Tworzenie i usuwanie uprawnień uwierzytelniania OAuth 2.0 oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizowanie przypisań ról jednostki usługi |
> | microsoft.directory/users/assignLicense | Zarządzanie licencjami użytkowników |
> | microsoft.directory/users/create | Dodawanie użytkowników |
> | microsoft.directory/users/delete | Usuwanie użytkowników |
> | microsoft.directory/users/disable | Wyłączanie użytkowników |
> | microsoft.directory/users/enable | Włączanie użytkowników |
> | microsoft.directory/users/invalidateAllRefreshTokens | Wymuszanie wylogowania przez unieważnienie tokenów odświeżania użytkownika |
> | microsoft.directory/users/restore | Przywracanie usuniętych użytkowników |
> | microsoft.directory/users/basic/update | Aktualizowanie podstawowych właściwości użytkowników |
> | microsoft.directory/users/manager/update | Menedżer aktualizacji dla użytkowników |
> | microsoft.directory/users/password/update | Resetowanie haseł dla wszystkich użytkowników |
> | microsoft.directory/users/userPrincipalName/update | Aktualizowanie głównej nazwy użytkownika użytkowników |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="partner-tier2-support"></a>Pomoc techniczna dla partnerów w warstwie 2

Nie używaj. Ta rola została wycofana i zostanie usunięta z usługi Azure AD w przyszłości. Ta rola jest przeznaczona do użytku przez niewielką liczbę partnerów handlowych firmy Microsoft i nie jest przeznaczona do użytku ogólnego.

> [!IMPORTANT]
> Ta rola może resetować hasła i unieważniać tokeny odświeżania dla wszystkich użytkowników niebędących administratorami i administratorami (w tym administratorów globalnych). Tej roli nie należy używać, ponieważ jest przestarzała i nie będzie już zwracana w interfejsie API.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/applications/appRoles/update | Aktualizowanie właściwości appRoles we wszystkich typach aplikacji |
> | microsoft.directory/applications/audience/update | Aktualizowanie właściwości odbiorców dla aplikacji |
> | microsoft.directory/applications/authentication/update | Aktualizowanie uwierzytelniania we wszystkich typach aplikacji |
> | microsoft.directory/applications/basic/update | Aktualizowanie podstawowych właściwości aplikacji |
> | microsoft.directory/applications/credentials/update | Aktualizowanie poświadczeń aplikacji |
> | microsoft.directory/applications/owners/update | Aktualizowanie właścicieli aplikacji |
> | microsoft.directory/applications/permissions/update | Aktualizowanie ujawnionych uprawnień i wymaganych uprawnień we wszystkich typach aplikacji |
> | microsoft.directory/applications/policies/update | Aktualizowanie zasad aplikacji |
> | microsoft.directory/contacts/create | Tworzenie kontaktów |
> | microsoft.directory/contacts/delete | Usuwanie kontaktów |
> | microsoft.directory/contacts/basic/update | Aktualizowanie podstawowych właściwości kontaktów |
> | microsoft.directory/domains/allProperties/allTasks | Tworzenie i usuwanie domen oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/groups/create | Tworzenie grup z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/delete | Usuwanie grup z wyłączeniem grupy przypisanej do ról |
> | microsoft.directory/groups/restore | Przywracanie usuniętych grup |
> | microsoft.directory/groups/members/update | Aktualizowanie członków grup z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/owners/update | Aktualizowanie właścicieli grup z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Tworzenie i usuwanie uprawnień uwierzytelniania OAuth 2.0 oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/organization/basic/update | Aktualizowanie podstawowych właściwości w organizacji |
> | microsoft.directory/roleAssignments/allProperties/allTasks | Tworzenie i usuwanie przypisań ról oraz odczytywanie i aktualizowanie wszystkich właściwości przypisania roli |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | Tworzenie i usuwanie definicji ról oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Tworzenie i usuwanie właściwości scopedRoleMemberships oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizowanie przypisań ról jednostki usługi |
> | microsoft.directory/subscribedSkus/standard/read | Odczytywanie podstawowych właściwości subskrypcji |
> | microsoft.directory/users/assignLicense | Zarządzanie licencjami użytkowników |
> | microsoft.directory/users/create | Dodawanie użytkowników |
> | microsoft.directory/users/delete | Usuwanie użytkowników |
> | microsoft.directory/users/disable | Wyłączanie użytkowników |
> | microsoft.directory/users/enable | Włączanie użytkowników |
> | microsoft.directory/users/invalidateAllRefreshTokens | Wymuszanie wylogowania przez unieważnienie tokenów odświeżania użytkownika |
> | microsoft.directory/users/restore | Przywracanie usuniętych użytkowników |
> | microsoft.directory/users/basic/update | Aktualizowanie podstawowych właściwości użytkowników |
> | microsoft.directory/users/manager/update | Menedżer aktualizacji dla użytkowników |
> | microsoft.directory/users/password/update | Resetowanie haseł dla wszystkich użytkowników |
> | microsoft.directory/users/userPrincipalName/update | Aktualizowanie głównej nazwy użytkownika użytkowników |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="password-administrator"></a>Administrator haseł

Użytkownicy z tą rolą mają ograniczoną możliwość zarządzania hasłami. Ta rola nie daje możliwości zarządzania żądaniami obsługi ani monitorowania kondycji usługi. To, czy administrator haseł może zresetować hasło użytkownika, zależy od przypisanej roli użytkownika. Aby uzyskać listę ról, dla których administrator haseł może resetować hasła, zobacz [Uprawnienia do resetowania haseł.](#password-reset-permissions)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/users/password/update | Resetowanie haseł dla wszystkich użytkowników |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="power-bi-administrator"></a>Power BI administratora

Użytkownicy z tą rolą mają globalne uprawnienia w usłudze Microsoft Power BI, gdy usługa jest obecna, a także możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji można [znaleźć w te Power BI roli administratora systemu](/power-bi/service-admin-role).

> [!NOTE]
> W interfejsie API Microsoft Graph Azure AD PowerShell ta rola jest identyfikowana jako "administrator Power BI Service". Jest to "Power BI administrator" w Azure Portal [.](https://portal.azure.com)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |
> | microsoft.powerApps.powerBI/allEntities/allTasks | Zarządzanie wszystkimi aspektami Power BI |

## <a name="power-platform-administrator"></a>Power Platform administratorem

Użytkownicy w tej roli mogą tworzyć wszystkie aspekty środowisk, usługi PowerApps, przepływów i zasad ochrony przed utratą danych oraz zarządzać nimi. Ponadto użytkownicy z tą rolą mają możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.dynamics365/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Dynamics 365 |
> | microsoft.flow/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Microsoft Power Automate |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |
> | microsoft.powerApps/allEntities/allTasks | Zarządzanie wszystkimi aspektami Power Apps |

## <a name="printer-administrator"></a>Administrator drukarki

Użytkownicy w tej roli mogą rejestrować drukarki i zarządzać wszystkimi aspektami wszystkich konfiguracji drukarek w rozwiązaniu Microsoft Drukowanie uniwersalne, w tym ustawieniami łącznika Drukowanie uniwersalne. Mogą wyrazić zgodę na wszystkie delegowane żądania uprawnień drukowania. Administratorzy drukarek mają również dostęp do raportów drukowania.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.azure.print/allEntities/allProperties/allTasks | Tworzenie i usuwanie drukarek i łączników oraz odczytywanie i aktualizowanie wszystkich właściwości w uwitrynie Microsoft Print |

## <a name="printer-technician"></a>Technik drukarek

Użytkownicy z tą rolą mogą rejestrować drukarki i zarządzać stanem drukarek w rozwiązaniu microsoft Drukowanie uniwersalne microsoft. Mogą również odczytywać wszystkie informacje o łączniku. Kluczowym zadaniem, które nie może wykonać technik drukarek, jest ustawienie uprawnień użytkownika do drukarek i udostępniania drukarek.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.azure.print/connectors/allProperties/read | Odczytywanie wszystkich właściwości łączników w uwitrynie Microsoft Print |
> | microsoft.azure.print/printers/allProperties/read | Odczytywanie wszystkich właściwości drukarek w witrynie Microsoft Print |
> | microsoft.azure.print/printers/register | Rejestrowanie drukarek w uwitrynie Microsoft Print |
> | microsoft.azure.print/printers/unregister | Wyrejestruj drukarki w układzie Microsoft Print |
> | microsoft.azure.print/printers/basic/update | Aktualizowanie podstawowych właściwości drukarek w witrynie Microsoft Print |

## <a name="privileged-authentication-administrator"></a>Administrator uwierzytelniania uprzywilejowanego

Użytkownicy z tą rolą mogą ustawić lub zresetować dowolną metodę uwierzytelniania (w tym hasła) dla dowolnego użytkownika, w tym administratorów globalnych. Administratorzy uwierzytelniania uprzywilejowanego mogą wymusić ponowne zarejestrowanie się użytkowników przy użyciu istniejących poświadczeń innych niż hasła (takich jak MFA lub FIDO) i odwołać "pamiętaj uwierzytelnianie wieloskładnikowe na urządzeniu", monitując o uwierzytelnianie wieloskładnikowe podczas następnego logowania wszystkich użytkowników.

Rola [administrator uwierzytelniania](#authentication-administrator) ma uprawnienia do wymuszania ponownej rejestracji i uwierzytelniania wieloskładnikowego dla standardowych użytkowników i użytkowników z niektórymi rolami administratora.

Rola [administratora zasad uwierzytelniania](#authentication-policy-administrator) ma uprawnienia do ustawienia zasad metody uwierzytelniania dzierżawy, które określają, które metody mogą być rejestrowane i stosowane przez poszczególnych użytkowników.

| Rola | Zarządzanie metodami uwierzytelniania użytkowników | Zarządzanie uwierzytelniania wieloskładnikowego na użytkownika | Zarządzanie ustawieniami usługi MFA | Zarządzanie zasadami metody uwierzytelniania | Zarządzanie zasadami ochrony haseł |
| ---- | ---- | ---- | ---- | ---- | ---- |
| administrator uwierzytelniania | Tak dla niektórych użytkowników (patrz powyżej) | Tak dla niektórych użytkowników (patrz powyżej) | Nie | Nie | Nie |
| Administrator uwierzytelniania uprzywilejowanego| Tak dla wszystkich użytkowników | Tak dla wszystkich użytkowników | Nie | Nie | Nie |
| Administrator zasad uwierzytelniania | Nie | Nie | Tak | Tak | Tak |

> [!IMPORTANT]
> Użytkownicy z tą rolą mogą zmieniać poświadczenia dla osób, które mogą mieć dostęp do informacji poufnych lub prywatnych albo do konfiguracji krytycznej wewnątrz i na zewnątrz Azure Active Directory. Zmiana poświadczeń użytkownika może oznaczać możliwość założenia tożsamości i uprawnień tego użytkownika. Na przykład:
>
>* Właściciele rejestracji aplikacji i aplikacji dla przedsiębiorstw, którzy mogą zarządzać poświadczeniami posiadanych aplikacji. Te aplikacje mogą mieć uprzywilejowane uprawnienia w usłudze Azure AD i w innym miejscu nie są przyznawane administratorom uwierzytelniania. Za pomocą tej ścieżki administrator uwierzytelniania może przyjąć tożsamość właściciela aplikacji, a następnie dodatkowo założyć tożsamość aplikacji uprzywilejowanej, aktualizując poświadczenia aplikacji.
>* Właściciele subskrypcji platformy Azure, którzy mogą mieć dostęp do poufnych lub prywatnych informacji albo do konfiguracji o krytycznym znaczeniu na platformie Azure.
>* Grupa zabezpieczeń i Microsoft 365 grupy zabezpieczeń, którzy mogą zarządzać członkostwem w grupie. Grupy te mogą udzielać dostępu do informacji poufnych lub prywatnych albo do konfiguracji krytycznej w usłudze Azure AD i w innych miejscach.
>* Administratorzy w innych usługach poza usługą Azure AD, takich jak Exchange Online, Centrum zabezpieczeń i zgodności pakietu Office oraz systemy zasobów ludzkich.
>* Osoby inne niż administratorzy, na przykład pracownicy kadry kierowniczej, prawni i pracownicy, którzy mogą mieć dostęp do poufnych lub prywatnych informacji.


> [!IMPORTANT]
> Ta rola nie jest obecnie w stanie zarządzać uwierzytelniania wieloskładnikowego dla uwierzytelniania wieloskładnikowego w starszej wersji portalu zarządzania usługą MFA. Te same funkcje można wykonać za pomocą polecenia [Set-MsolUser](/powershell/module/msonline/set-msoluser) modułu Azure AD Powershell.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/users/invalidateAllRefreshTokens | Wymuszanie wylogowania przez unieważnienie tokenów odświeżania użytkownika |
> | microsoft.directory/users/strongAuthentication/update | Aktualizowanie właściwości silnego uwierzytelniania dla użytkowników |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="privileged-role-administrator"></a>Administrator ról uprzywilejowanych

Użytkownicy z tą rolą mogą zarządzać przypisaniami ról w Azure Active Directory, a także w ramach Azure AD Privileged Identity Management. Mogą tworzyć grupy, które można przypisać do ról usługi Azure AD, i zarządzać nimi. Ponadto ta rola umożliwia zarządzanie wszystkimi aspektami jednostek Privileged Identity Management i administracyjnych.

> [!IMPORTANT]
> Ta rola daje możliwość zarządzania przypisaniami dla wszystkich ról usługi Azure AD, w tym roli administratora globalnego. Ta rola nie obejmuje żadnych innych uprzywilejowanych możliwości w usłudze Azure AD, takich jak tworzenie lub aktualizowanie użytkowników. Jednak użytkownicy przypisani do tej roli mogą przyznać sobie lub innym dodatkowe uprawnienia, przypisując dodatkowe role.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | Tworzenie jednostek administracyjnych (w tym członków) i zarządzanie nimi |
> | microsoft.directory/appRoleAssignments/allProperties/allTasks | Tworzenie i usuwanie obiektów appRoleAssignments oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/authorizationPolicy/allProperties/allTasks | Zarządzanie wszystkimi aspektami zasad autoryzacji |
> | microsoft.directory/directoryRoles/allProperties/allTasks | Tworzenie i usuwanie ról katalogu oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/groupsAssignableToRoles/create | Tworzenie grup z możliwością przypisania do roli |
> | microsoft.directory/groupsAssignableToRoles/delete | Usuwanie grup przypisywanych do ról |
> | microsoft.directory/groupsAssignableToRoles/restore | Przywracanie grup przypisywanych do ról |
> | microsoft.directory/groupsAssignableToRoles/allProperties/update | Aktualizowanie grup przypisywanych do ról |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Tworzenie i usuwanie uprawnień uwierzytelniania OAuth 2.0 oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/privilegedIdentityManagement/allProperties/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w Privileged Identity Management |
> | microsoft.directory/roleAssignments/allProperties/allTasks | Tworzenie i usuwanie przypisań ról oraz odczytywanie i aktualizowanie wszystkich właściwości przypisania roli |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | Tworzenie i usuwanie definicji ról oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Tworzenie i usuwanie właściwości scopedRoleMemberships oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizowanie przypisań ról jednostki usługi |
> | microsoft.directory/servicePrincipals/permissions/update | Aktualizowanie uprawnień jednostki usługi |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-company-admin | Wyrażanie zgody na wszelkie uprawnienia do dowolnej aplikacji |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="reports-reader"></a>Czytelnik raportów

Użytkownicy z tą rolą mogą wyświetlać dane raportowania użycia i pulpit nawigacyjny raportów w centrum administracyjne platformy Microsoft 365 i pakiet kontekstowy wdrożenia w Power BI. Ponadto rola zapewnia dostęp do raportów logowania i działań w usłudze Azure AD oraz danych zwracanych przez interfejs API Microsoft Graph raportowania. Użytkownik przypisany do roli Czytelnik raportów może uzyskać dostęp tylko do odpowiednich metryk użycia i wdrożenia. Nie mają oni żadnych uprawnień administratora do konfigurowania ustawień ani uzyskiwania dostępu do centrów administracyjnych specyficznych dla produktu, takich jak program Exchange. Ta rola nie ma dostępu do wyświetlania i tworzenia biletów pomocy technicznej oraz zarządzania nimi.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Odczytywanie wszystkich właściwości dzienników inspekcji, w tym właściwości uprzywilejowanych |
> | microsoft.directory/provisioningLogs/allProperties/read | Odczytywanie wszystkich właściwości dzienników aprowizowania |
> | microsoft.directory/signInReports/allProperties/read | Odczytywanie wszystkich właściwości raportów logowania, w tym właściwości uprzywilejowanych |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.office365.network/performance/allProperties/read | Odczytaj wszystkie właściwości wydajności sieci w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Odczytywanie raportów użycia usługi Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="search-administrator"></a>Administrator wyszukiwania

Użytkownicy w tej roli mają pełny dostęp do wszystkich funkcji zarządzania usługą Microsoft Search w centrum administracyjne platformy Microsoft 365. Ponadto ci użytkownicy mogą wyświetlać centrum wiadomości, monitorować kondycję usługi i tworzyć żądania obsługi.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Odczytywanie komunikatów Centrum wiadomości w centrum administracyjne platformy Microsoft 365, z wyjątkiem komunikatów zabezpieczeń |
> | microsoft.office365.search/content/manage | Tworzenie i usuwanie zawartości oraz odczytywanie i aktualizowanie wszystkich właściwości w uwitrynie Microsoft Search |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="search-editor"></a>Edytor wyszukiwania

Użytkownicy w tej roli mogą tworzyć i usuwać zawartość usługi Microsoft Search oraz zarządzać centrum administracyjne platformy Microsoft 365, w tym zakładkami,&jako i lokalizacjami.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Odczytywanie komunikatów Centrum wiadomości w centrum administracyjne platformy Microsoft 365, z wyjątkiem komunikatów zabezpieczeń |
> | microsoft.office365.search/content/manage | Tworzenie i usuwanie zawartości oraz odczytywanie i aktualizowanie wszystkich właściwości w uwitrynie Microsoft Search |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="security-administrator"></a>Administrator zabezpieczeń

Użytkownicy z tą rolą mają uprawnienia do zarządzania funkcjami związanymi z zabezpieczeniami w centrum zgodności usług Centrum zabezpieczeń platformy Microsoft 365, Azure Active Directory Identity Protection, Azure Active Directory Authentication, Azure Information Protection i Office 365 Security & Compliance Center. Więcej informacji na temat uprawnień usługi Office 365 można znaleźć w tesłudze: Uprawnienia w Centrum [& zabezpieczeń.](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)

W | Można to zrobić
--- | ---
[Centrum zabezpieczeń platformy Microsoft 365](https://protection.office.com) | Monitorowanie zasad związanych z zabezpieczeniami w usługach Microsoft 365 internetowych<br>Zarządzanie zagrożeniami bezpieczeństwa i alertami<br>Wyświetlanie raportów
Centrum ochrony tożsamości | Wszystkie uprawnienia roli Czytelnik zabezpieczeń<br>Ponadto możliwość wykonywania wszystkich operacji centrum ochrony tożsamości z wyjątkiem resetowania haseł
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Wszystkie uprawnienia roli Czytelnik zabezpieczeń<br>**Nie można** zarządzać przypisaniami ról lub ustawieniami usługi Azure AD
[Centrum zgodności zabezpieczeń usługi Office 365 & Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Zarządzanie zasadami zabezpieczeń<br>Wyświetlanie i badanie zagrożeń bezpieczeństwa oraz reagowanie na nie<br>Wyświetlanie raportów
Azure Advanced Threat Protection | Monitorowanie podejrzanych działań zabezpieczeń i reagowanie na nie
Windows Defender ATP i EDR | Przypisywanie ról<br>Zarządzanie grupami maszyn<br>Konfigurowanie wykrywania zagrożeń punktu końcowego i zautomatyzowanego korygowania<br>Wyświetlanie i badanie alertów oraz reagowanie na nie
[Intune](/intune/role-based-access-control) | Widok informacji o użytkownikach, urządzeniach, rejestracji, konfiguracji i aplikacji<br>Nie można wprowadzić zmian w usłudze Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Dodawanie administratorów, dodawanie zasad i ustawień, przekazywanie dzienników i wykonywanie akcji ładu
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | Może wyświetlać zasady zabezpieczeń, wyświetlać stany zabezpieczeń, edytować zasady zabezpieczeń, wyświetlać alerty i zalecenia, odrzucać alerty i zalecenia
[Microsoft 365 kondycji usługi](/office365/enterprise/view-service-health) | Wyświetlanie kondycji usług Microsoft 365 danych
[Inteligentna blokada](../authentication/howto-password-smart-lockout.md) | Zdefiniuj próg i czas trwania blokad w przypadku nieudanych zdarzeń logowania.
[Ochrona hasłem](../authentication/concept-password-ban-bad.md) | Konfigurowanie niestandardowej listy zakazanych haseł lub ochrony haseł w środowisku lokalnym.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/applications/policies/update | Aktualizowanie zasad aplikacji |
> | microsoft.directory/auditLogs/allProperties/read | Odczytywanie wszystkich właściwości dzienników inspekcji, w tym właściwości uprzywilejowanych |
> | microsoft.directory/bitlockerKeys/key/read | Odczytywanie metadanych i klucza funkcji BitLocker na urządzeniach |
> | microsoft.directory/entitlementManagement/allProperties/read | Odczytywanie wszystkich właściwości w zarządzaniu uprawnieniami w usłudze Azure AD |
> | microsoft.directory/identityProtection/allProperties/read | Odczytywanie wszystkich zasobów w Azure AD Identity Protection |
> | microsoft.directory/identityProtection/allProperties/update | Zaktualizuj wszystkie zasoby w Azure AD Identity Protection |
> | microsoft.directory/policies/create | Tworzenie zasad w usłudze Azure AD |
> | microsoft.directory/policies/delete | Usuwanie zasad w usłudze Azure AD |
> | microsoft.directory/policies/basic/update | Aktualizowanie podstawowych właściwości zasad |
> | microsoft.directory/policies/owners/update | Aktualizowanie właścicieli zasad |
> | microsoft.directory/policies/tenantDefault/update | Aktualizowanie domyślnych zasad organizacji |
> | microsoft.directory/conditionalAccessPolicies/create | Tworzenie zasad dostępu warunkowego |
> | microsoft.directory/conditionalAccessPolicies/delete | Usuwanie zasad dostępu warunkowego |
> | microsoft.directory/conditionalAccessPolicies/standard/read | Odczyt dostępu warunkowego dla zasad |
> | microsoft.directory/conditionalAccessPolicies/owners/read | Odczytywanie właścicieli zasad dostępu warunkowego |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Odczytywanie właściwości "zastosowane do" dla zasad dostępu warunkowego |
> | microsoft.directory/conditionalAccessPolicies/basic/update | Aktualizowanie podstawowych właściwości zasad dostępu warunkowego |
> | microsoft.directory/conditionalAccessPolicies/owners/update | Aktualizowanie właścicieli zasad dostępu warunkowego |
> | microsoft.directory/conditionalAccessPolicies/tenantDefault/update | Aktualizowanie domyślnej dzierżawy dla zasad dostępu warunkowego |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Odczytywanie wszystkich zasobów w Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Odczytywanie wszystkich właściwości dzienników aprowizowania |
> | microsoft.directory/servicePrincipals/policies/update | Aktualizowanie zasad jednostki usługi |
> | microsoft.directory/signInReports/allProperties/read | Odczytywanie wszystkich właściwości raportów logowania, w tym właściwości uprzywilejowanych |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.protectionCenter/allEntities/standard/read | Odczytywanie standardowych właściwości wszystkich zasobów w centrach zabezpieczeń i zgodności |
> | microsoft.office365.protectionCenter/allEntities/basic/update | Aktualizowanie podstawowych właściwości wszystkich zasobów w centrach zabezpieczeń i zgodności |
> | microsoft.office365.protectionCenter/attackSimulator/ładunek/allProperties/allTasks | Tworzenie ładunków ataków i zarządzanie nimi w symulatorze ataków |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Przeczytaj raporty odpowiedzi symulacji ataku i powiązanego trenowania |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | Tworzenie szablonów symulacji ataków i zarządzanie nimi w symulatorze ataków |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="security-operator"></a>Operator zabezpieczeń

Użytkownicy z tą rolą mogą zarządzać alertami i mieć globalny dostęp tylko do odczytu do funkcji związanych z zabezpieczeniami, w tym wszystkie informacje w centrum zgodności usług Centrum zabezpieczeń platformy Microsoft 365, Azure Active Directory, Identity Protection, Privileged Identity Management i Office 365 Security &. Więcej informacji na temat uprawnień usługi Office 365 można znaleźć w [tesłudze: Permissions (Uprawnienia) w Centrum zgodności & Security ( Security & Compliance Center).](/office365/securitycompliance/permissions-in-the-security-and-compliance-center)

W | Można to zrobić
--- | ---
[Centrum zabezpieczeń platformy Microsoft 365](https://protection.office.com) | Wszystkie uprawnienia roli Czytelnik zabezpieczeń<br>Wyświetlanie i badanie alertów dotyczących zagrożeń bezpieczeństwa oraz reagowanie na nie
Usługa Azure AD Identity Protection | Wszystkie uprawnienia roli Czytelnik zabezpieczeń<br>Ponadto możliwość wykonywania wszystkich operacji centrum usługi Identity Protection z wyjątkiem resetowania haseł i konfigurowania wiadomości e-mail z alertami.
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Wszystkie uprawnienia roli Czytelnik zabezpieczeń
[Centrum zgodności zabezpieczeń usługi Office 365 & Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Wszystkie uprawnienia roli Czytelnik zabezpieczeń<br>Wyświetlanie i badanie alertów zabezpieczeń oraz reagowanie na nie
Windows Defender ATP i EDR | Wszystkie uprawnienia roli Czytelnik zabezpieczeń<br>Wyświetlanie i badanie alertów zabezpieczeń oraz reagowanie na nie
[Intune](/intune/role-based-access-control) | Wszystkie uprawnienia roli Czytelnik zabezpieczeń
[Cloud App Security](/cloud-app-security/manage-admins) | Wszystkie uprawnienia roli Czytelnik zabezpieczeń
[Microsoft 365 kondycji usługi](/office365/enterprise/view-service-health) | Wyświetlanie kondycji usług Microsoft 365 danych

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Odczytywanie wszystkich właściwości dzienników inspekcji, w tym właściwości uprzywilejowanych |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w Microsoft Cloud App Security |
> | microsoft.directory/identityProtection/allProperties/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w Azure AD Identity Protection |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Odczytywanie wszystkich zasobów w Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Odczytywanie wszystkich właściwości dzienników aprowizowania |
> | microsoft.directory/signInReports/allProperties/read | Odczytywanie wszystkich właściwości raportów logowania, w tym właściwości uprzywilejowanych |
> | microsoft.azure.advancedThreatProtection/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Azure Advanced Threat Protection |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.intune/allEntities/read | Odczytywanie wszystkich zasobów w Microsoft Intune |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w centrum Microsoft 365 Security and Compliance Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.windows.defenderAdvancedThreatProtection/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Microsoft Defender dla punktu końcowego |

## <a name="security-reader"></a>Czytelnik zabezpieczeń

Użytkownicy z tą rolą mają globalny dostęp tylko do odczytu do funkcji związanych z zabezpieczeniami, w tym wszystkich informacji w usługach Centrum zabezpieczeń platformy Microsoft 365, Azure Active Directory, Identity Protection, Privileged Identity Management, a także możliwość odczytu raportów logowania i dzienników inspekcji usługi Azure Active Directory oraz w Centrum zgodności usługi Office 365 Security &. Więcej informacji na temat uprawnień usługi Office 365 można znaleźć w [tesłudze: Permissions (Uprawnienia) w Centrum zgodności & Security ( Security & Compliance Center).](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)

W | Można to zrobić
--- | ---
[Centrum zabezpieczeń platformy Microsoft 365](https://protection.office.com) | Wyświetlanie zasad związanych z zabezpieczeniami w Microsoft 365 usług<br>Wyświetlanie zagrożeń bezpieczeństwa i alertów<br>Wyświetlanie raportów
Centrum ochrony tożsamości | Odczytywanie wszystkich raportów zabezpieczeń i informacji o ustawieniach funkcji zabezpieczeń<br><ul><li>Ochrona przed spamem<li>Szyfrowanie<li>Ochrona przed utratą danych<li>Ochrona przed złośliwym oprogramowaniem<li>Zaawansowana ochrona przed zagrożeniami<li>Ochrona przed wyłudzeniem informacji<li>Reguły przepływu poczty e-mail
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Ma dostęp tylko do odczytu do wszystkich informacji w usłudze Azure AD Privileged Identity Management: zasady i raporty dotyczące przypisań ról i przeglądów zabezpieczeń usługi Azure AD.<br>**Nie** można zarejestrować się Azure AD Privileged Identity Management ani wprowadzać w nim żadnych zmian. W portalu Privileged Identity Management lub za pomocą programu PowerShell osoba z tą rolą może aktywować dodatkowe role (na przykład Administrator globalny lub Administrator ról uprzywilejowanych), jeśli użytkownik jest do nich uprawniony.
[Centrum zgodności zabezpieczeń usługi Office 365 & Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Wyświetlanie zasad zabezpieczeń<br>Wyświetlanie i badanie zagrożeń bezpieczeństwa<br>Wyświetlanie raportów
Windows Defender ATP i EDR | Wyświetlanie i badanie alertów. Po włączeniu kontroli dostępu opartej na rolach w usłudze Windows Defender ATP użytkownicy z uprawnieniami tylko do odczytu, takimi jak rola usługi Azure AD czytelnik zabezpieczeń, utracą dostęp, dopóki nie zostaną przypisani do Windows Defender ATP.
[Intune](/intune/role-based-access-control) | wyświetla informacje o użytkownikach, urządzeniach, rejestracji, konfiguracji i aplikacji. Nie może wprowadzać zmian w usłudze Intune.
[Cloud App Security](/cloud-app-security/manage-admins) | Ma uprawnienia tylko do odczytu i może zarządzać alertami
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | Może wyświetlać zalecenia i alerty, wyświetlać zasady zabezpieczeń, wyświetlać stany zabezpieczeń, ale nie może wprowadzać zmian
[Microsoft 365 kondycji usługi](/office365/enterprise/view-service-health) | Wyświetlanie kondycji usług Microsoft 365 danych

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Odczytywanie wszystkich właściwości dzienników inspekcji, w tym właściwości uprzywilejowanych |
> | microsoft.directory/bitlockerKeys/key/read | Odczytywanie metadanych i klucza funkcji BitLocker na urządzeniach |
> | microsoft.directory/entitlementManagement/allProperties/read | Odczytywanie wszystkich właściwości w usłudze Azure AD entitlement management |
> | microsoft.directory/identityProtection/allProperties/read | Odczytywanie wszystkich zasobów w Azure AD Identity Protection |
> | microsoft.directory/policies/standard/read | Odczytywanie podstawowych właściwości zasad |
> | microsoft.directory/policies/owners/read | Odczytywanie właścicieli zasad |
> | microsoft.directory/policies/policyAppliedTo/read | Odczytywanie właściwości policies.policyAppliedTo |
> | microsoft.directory/conditionalAccessPolicies/standard/read | Odczyt dostępu warunkowego dla zasad |
> | microsoft.directory/conditionalAccessPolicies/owners/read | Odczytywanie właścicieli zasad dostępu warunkowego |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Odczytywanie właściwości "zastosowane do" dla zasad dostępu warunkowego |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Odczytywanie wszystkich zasobów w Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Odczytywanie wszystkich właściwości dzienników aprowizowania |
> | microsoft.directory/signInReports/allProperties/read | Odczytywanie wszystkich właściwości raportów logowania, w tym właściwości uprzywilejowanych |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.office365.protectionCenter/allEntities/standard/read | Odczytywanie standardowych właściwości wszystkich zasobów w centrach zabezpieczeń i zgodności |
> | microsoft.office365.protectionCenter/attackSimulator/ładunek/allProperties/read | Odczytywanie wszystkich właściwości ładunków ataków w symulatorze ataków |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Przeczytaj raporty odpowiedzi symulacji ataku i powiązanego szkolenia |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/read | Odczytywanie wszystkich właściwości szablonów symulacji ataku w symulatorze ataków |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="service-support-administrator"></a>Administrator pomocy technicznej usługi

Użytkownicy z tą rolą mogą otwierać żądania pomocy technicznej dotyczące platformy Microsoft dla platformy Azure i usług Microsoft 365 oraz otwierać pulpit nawigacyjny usługi i centrum wiadomości w Azure Portal [i](https://portal.azure.com) [centrum administracyjne platformy Microsoft 365.](https://admin.microsoft.com) Więcej informacji można znaleźć [na stronie About admin roles (Informacje o rolach administratorów).](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

> [!NOTE]
> Wcześniej ta rola nosiła nazwę "Administrator usługi" w Azure Portal [i](https://portal.azure.com) [centrum administracyjne platformy Microsoft 365](https://admin.microsoft.com). Zmieniliśmy jej nazwę na "Administrator pomocy technicznej usługi", aby dopasować ją do nazwy exsiting w usługach Microsoft Graph API, Azure AD interfejs Graph API i Azure AD PowerShell.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.network/performance/allProperties/read | Odczytaj wszystkie właściwości wydajności sieci w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="sharepoint-administrator"></a>SharePoint Administrator

Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft SharePoint Online, gdy usługa jest obecna, a także możliwość tworzenia wszystkich grup Microsoft 365, zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji można znaleźć [na stronie About admin roles (Informacje o rolach administratorów).](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

> [!NOTE]
> W interfejsie API Microsoft Graph Azure AD PowerShell ta rola jest identyfikowana jako "Administrator usługi SharePoint". Jest to "Administrator programu SharePoint" w [Azure Portal](https://portal.azure.com).

> [!NOTE]
> Ta rola przyznaje również uprawnienia o zakresie do interfejsu API usługi Microsoft Graph dla usługi Microsoft Intune, umożliwiając zarządzanie i konfigurację zasad związanych z zasobami programu SharePoint i usługi OneDrive.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/groups.unified/create | Tworzenie Microsoft 365 grup z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/groups.unified/delete | Usuwanie Microsoft 365 z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/groups.unified/restore | Przywracanie Microsoft 365 zasobów |
> | microsoft.directory/groups.unified/basic/update | Aktualizowanie podstawowych właściwości Microsoft 365 grup z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/groups.unified/members/update | Aktualizowanie członków Microsoft 365 grupy z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/groups.unified/owners/update | Aktualizowanie właścicieli grup Microsoft 365 z wykluczeniem grup przypisywanych do ról |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.network/performance/allProperties/read | Odczytywanie wszystkich właściwości wydajności sieci w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.sharePoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w programie SharePoint |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Odczytywanie raportów użycia usługi Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="skype-for-business-administrator"></a>Administrator programu Skype dla firm

Użytkownicy z tą rolą mają uprawnienia globalne w programie Microsoft Skype dla firm, gdy usługa jest obecna, a także zarządzają atrybutami użytkownika specyficznym dla programu Skype w usłudze Azure Active Directory. Ponadto ta rola daje możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi oraz uzyskiwania dostępu do aplikacji Teams i Skype dla firm Admin Center. Konto musi być również licencjonowane dla usługi Teams lub nie może uruchamiać poleceń cmdlet programu PowerShell dla usługi Teams. Więcej informacji można znaleźć [na stronie About the Skype for Business admin role](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) and Teams licensing information (Informacje o roli administratora programu Skype dla firm i aplikacji Teams) na stronie [licencjonowania](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing) dodatków Skype dla firm i Microsoft Teams

> [!NOTE]
> W interfejsie API Microsoft Graph azure AD PowerShell ta rola jest identyfikowana jako "Administrator usługi Lync". Jest to "Administrator programu Skype dla firm" w [Azure Portal](https://portal.azure.com/).

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Skype dla firm Online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Odczytywanie raportów użycia usługi Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="teams-administrator"></a>Teams Administrator

Użytkownicy w tej roli mogą zarządzać wszystkimi aspektami obciążenia aplikacji Microsoft Teams za pośrednictwem centrum administracyjnego usługi Microsoft Teams & skype dla firm i odpowiednich modułów programu PowerShell. Obejmuje to między innymi wszystkie narzędzia do zarządzania związane z telefonią, wiadomościami, spotkaniami i samymi zespołami. Ta rola dodatkowo daje możliwość tworzenia wszystkich grup Microsoft 365, zarządzania biletami pomocy technicznej i monitorowania kondycji usługi.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/groups/hiddenMembers/read | Odczytywanie ukrytych członków grupy |
> | microsoft.directory/groups.unified/create | Tworzenie Microsoft 365 grup z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/groups.unified/delete | Usuwanie Microsoft 365 grupy z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/groups.unified/restore | Przywracanie Microsoft 365 grupy |
> | microsoft.directory/groups.unified/basic/update | Aktualizowanie podstawowych właściwości Microsoft 365 grup z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups.unified/members/update | Aktualizowanie członków Microsoft 365 grupy z wykluczeniem grup przypisywanych do ról |
> | microsoft.directory/groups.unified/owners/update | Aktualizowanie właścicieli Microsoft 365 grupy z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Udzielanie jednostki usługi bezpośredniego dostępu do danych grupy |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.network/performance/allProperties/read | Odczytaj wszystkie właściwości wydajności sieci w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Skype dla firm Online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Odczytywanie raportów użycia usługi Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |
> | microsoft.teams/allEntities/allProperties/allTasks | Zarządzanie wszystkimi zasobami w u użytkownikach Teams |

## <a name="teams-communications-administrator"></a>Administrator komunikacji usługi Teams

Użytkownicy w tej roli mogą zarządzać aspektami obciążenia aplikacji Microsoft Teams związanymi z telefonią & telefonii. Obejmuje to narzędzia do zarządzania przypisywaniem numerów telefonów, zasady połączeń głosowych i spotkań oraz pełny dostęp do zestawu narzędzi do analizy połączeń.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Skype dla firm Online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Odczytywanie raportów użycia usługi Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |
> | microsoft.teams/callQuality/allProperties/read | Odczytywanie wszystkich danych na pulpicie nawigacyjnym jakości wywołań (CQD) |
> | microsoft.teams/meetings/allProperties/allTasks | Zarządzanie spotkaniami, w tym zasadami spotkań, konfiguracjami i mostkami konferencji |
> | microsoft.teams/voice/allProperties/allTasks | Zarządzanie głosem, w tym zasadami połączeń oraz spisem numerów telefonów i przypisywaniem numerów telefonów |

## <a name="teams-communications-support-engineer"></a>Inżynier pomocy technicznej ds. komunikacji usługi Teams

Użytkownicy w tej roli mogą rozwiązywać problemy z komunikacją w usłudze Microsoft Teams & Skype dla firm przy użyciu narzędzi do rozwiązywania problemów z wywołaniami użytkownika w centrum administracyjnym usługi Microsoft Teams & Skype dla firm. Użytkownicy w tej roli mogą wyświetlać pełne informacje o rekordzie połączeń dla wszystkich zaangażowanych uczestników. Ta rola nie ma dostępu do wyświetlania i tworzenia biletów pomocy technicznej oraz zarządzania nimi.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Skype dla firm Online |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |
> | microsoft.teams/callQuality/allProperties/read | Odczytywanie wszystkich danych na pulpicie nawigacyjnym jakości wywołań (CQD) |

## <a name="teams-communications-support-specialist"></a>Specjalista pomocy technicznej ds. komunikacji usługi Teams

Użytkownicy w tej roli mogą rozwiązywać problemy z komunikacją w usłudze Microsoft Teams & Skype dla firm przy użyciu narzędzi do rozwiązywania problemów z wywołaniami użytkowników w usłudze Microsoft Teams & centrum administracyjnym usługi Skype dla firm. Użytkownicy w tej roli mogą tylko wyświetlać szczegóły użytkownika w wywołaniu dla określonego użytkownika, który odszukali. Ta rola nie ma dostępu do wyświetlania i tworzenia biletów pomocy technicznej ani zarządzania nimi.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Skype dla firm Online |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |
> | microsoft.teams/callQuality/standard/read | Odczytywanie podstawowych danych na pulpicie nawigacyjnym jakości wywołań (CQD) |

## <a name="teams-devices-administrator"></a>Administrator urządzeń usługi Teams

Użytkownicy z tą rolą mogą zarządzać [urządzeniami certyfikowanymi przez program Teams](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) z centrum administracyjnego usługi Teams. Ta rola umożliwia wyświetlanie wszystkich urządzeń na pierwszy rzut oka z możliwością wyszukiwania i filtrowania urządzeń. Użytkownik może sprawdzić szczegóły każdego urządzenia, w tym zalogowane konto, marki i model urządzenia. Użytkownik może zmienić ustawienia na urządzeniu i zaktualizować wersje oprogramowania. Ta rola nie udziela uprawnień do sprawdzania aktywności aplikacji Teams i wywołania jakości urządzenia.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |
> | microsoft.teams/devices/standard/read | Zarządzanie wszystkimi aspektami urządzeń certyfikowanych przez usługę Teams, w tym zasadami konfiguracji |

## <a name="usage-summary-reports-reader"></a>Czytelnik raportów podsumowania użycia

Użytkownicy z tą rolą mogą uzyskać dostęp do zagregowanych danych na poziomie dzierżawy i skojarzonych szczegółowych informacji w Centrum administracyjnym usługi Microsoft 365 na temat użycia i oceny produktywności, ale nie mogą uzyskać dostępu do żadnych szczegółów ani szczegółowych informacji na poziomie użytkownika. W Microsoft 365 administracyjnym dla tych dwóch raportów rozróżniamy zagregowane dane na poziomie dzierżawy i szczegóły na poziomie użytkownika. Ta rola zapewnia dodatkową warstwę ochrony danych umożliwiających identyfikację poszczególnych użytkowników, o którą prosili zarówno klienci, jak i zespoły prawne.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.office365.network/performance/allProperties/read | Odczytaj wszystkie właściwości wydajności sieci w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.usageReports/allEntities/standard/read | Odczytywanie zagregowanych raportów użycia usługi Office 365 na poziomie dzierżawy |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="user-administrator"></a>Administrator użytkowników

Użytkownicy z tą rolą mogą tworzyć użytkowników i zarządzać wszystkimi aspektami użytkowników z pewnymi ograniczeniami (zobacz tabelę) oraz mogą aktualizować zasady wygasania haseł. Ponadto użytkownicy z tą rolą mogą tworzyć wszystkie grupy i zarządzać nimi. Ta rola obejmuje również możliwość tworzenia widoków użytkowników i zarządzania nimi, zarządzania biletami pomocy technicznej oraz monitorowania kondycji usługi. Administratorzy użytkowników nie mają uprawnień do zarządzania niektórymi właściwościami użytkowników w większości ról administratorów. Użytkownik z tą rolą nie ma uprawnień do zarządzania uwierzytelniania wieloskładnikowego. Role, które są wyjątkami od tego ograniczenia, zostały wymienione w poniższej tabeli.

| Uprawnienia administratora użytkowników | Uwagi |
| --- | --- |
| Tworzenie użytkowników i grup<br/>Tworzenie widoków użytkowników i zarządzanie nimi<br/>Zarządzanie biletami pomocy technicznej pakietu Office<br/>Aktualizowanie zasad wygasania haseł |  |
| Zarządzanie licencjami<br/>Zarządzanie wszystkimi właściwościami użytkownika z wyjątkiem głównej nazwy użytkownika | Dotyczy wszystkich użytkowników, w tym wszystkich administratorów |
| Usuwanie i przywracanie<br/>Wyłącz i włącz<br/>Zarządzanie wszystkimi właściwościami użytkownika, w tym główną nazwą użytkownika<br/>Aktualizowanie kluczy urządzeń (FIDO) | Dotyczy użytkowników, którzy nie są administratorami lub pełnią dowolną z następujących ról:<ul><li>Administrator pomocy technicznej</li><li>Użytkownik bez roli</li><li>Administrator użytkowników</li></ul> |
| Unieważnij tokeny odświeżania<br/>Resetowanie hasła | Aby uzyskać listę ról, dla których administrator użytkowników może resetować hasła i unieważniać tokeny odświeżania, zobacz [Uprawnienia do resetowania haseł.](#password-reset-permissions) |

> [!IMPORTANT]
> Użytkownicy z tą rolą mogą zmieniać hasła osób, które mogą mieć dostęp do poufnych lub prywatnych informacji albo do konfiguracji krytycznej wewnątrz i na zewnątrz Azure Active Directory. Zmiana hasła użytkownika może oznaczać możliwość założenia tożsamości i uprawnień tego użytkownika. Na przykład:
>
>- Właściciele rejestracji aplikacji i aplikacji dla przedsiębiorstw, którzy mogą zarządzać poświadczeniami posiadanych aplikacji. Te aplikacje mogą mieć uprawnienia uprzywilejowane w usłudze Azure AD i w innym miejscu nie są przyznawane administratorom użytkowników. Za pomocą tej ścieżki administrator użytkowników może przyjąć tożsamość właściciela aplikacji, a następnie dalej przyjąć tożsamość aplikacji uprzywilejowanej, aktualizując poświadczenia aplikacji.
>- Właściciele subskrypcji platformy Azure, którzy mogą mieć dostęp do poufnych lub prywatnych informacji albo do konfiguracji o krytycznym znaczeniu na platformie Azure.
>- Grupa zabezpieczeń i Microsoft 365 grupy zabezpieczeń, którzy mogą zarządzać członkostwem w grupie. Grupy te mogą udzielać dostępu do informacji poufnych lub prywatnych albo do konfiguracji krytycznej w usłudze Azure AD i w innych miejscach.
>- Administratorzy w innych usługach poza usługą Azure AD, takich jak Exchange Online, Centrum zabezpieczeń i zgodności pakietu Office oraz systemy zasobów ludzkich.
>- Osoby inne niż administratorzy, np. kadry kierownicze, pracownicy prawni i pracownicy ds. zasobów ludzkich, którzy mogą mieć dostęp do informacji poufnych lub prywatnych.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | microsoft.directory/appRoleAssignments/create | Tworzenie przypisań ról aplikacji |
> | microsoft.directory/appRoleAssignments/delete | Usuwanie przypisań ról aplikacji |
> | microsoft.directory/appRoleAssignments/basic/update | Aktualizowanie podstawowych właściwości przypisań ról aplikacji |
> | microsoft.directory/contacts/create | Tworzenie kontaktów |
> | microsoft.directory/contacts/delete | Usuwanie kontaktów |
> | microsoft.directory/contacts/basic/update | Aktualizowanie podstawowych właściwości kontaktów |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | Tworzenie i usuwanie zasobów oraz odczytywanie i aktualizowanie wszystkich właściwości w zarządzaniu uprawnieniami w usłudze Azure AD |
> | microsoft.directory/groups/assignLicense | Przypisywanie licencji produktów do grup w celu licencjonowania opartego na grupach |
> | microsoft.directory/groups/create | Tworzenie grup z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/delete | Usuwanie grup z wyłączeniem grupy przypisanej do ról |
> | microsoft.directory/groups/hiddenMembers/read | Odczytywanie ukrytych członków grupy |
> | microsoft.directory/groups/reprocessLicenseAssignment | Ponowne przetwarzanie przypisań licencji dla licencjonowania opartego na grupach |
> | microsoft.directory/groups/restore | Przywracanie usuniętych grup |
> | microsoft.directory/groups/basic/update | Aktualizowanie podstawowych właściwości grup z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/classification/update | Aktualizowanie właściwości klasyfikacji grup, z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/dynamicMembershipRule/update | Aktualizowanie reguły członkostwa dynamicznego grup z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/groupType/update | Aktualizowanie właściwości groupType grupy |
> | microsoft.directory/groups/members/update | Aktualizowanie członków grup z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/onPremWriteBack/update | Aktualizowanie Azure Active Directory, które mają być zapisywane z powrotem w środowisku lokalnym przy użyciu Azure AD Connect |
> | microsoft.directory/groups/owners/update | Aktualizowanie właścicieli grup z wyłączeniem grup przypisywanych do ról |
> | microsoft.directory/groups/settings/update | Aktualizowanie ustawień grup |
> | microsoft.directory/groups/visibility/update | Aktualizowanie właściwości widoczności grup |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Tworzenie i usuwanie uprawnień uwierzytelniania OAuth 2.0 oraz odczytywanie i aktualizowanie wszystkich właściwości |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizowanie przypisań ról jednostki usługi |
> | microsoft.directory/users/assignLicense | Zarządzanie licencjami użytkowników |
> | microsoft.directory/users/create | Dodawanie użytkowników |
> | microsoft.directory/users/delete | Usuwanie użytkowników |
> | microsoft.directory/users/disable | Wyłączanie użytkowników |
> | microsoft.directory/users/enable | Włączanie użytkowników |
> | microsoft.directory/users/inviteGuest | Zapraszanie gości |
> | microsoft.directory/users/invalidateAllRefreshTokens | Wymuszanie wylogowania przez unieważnienie tokenów odświeżania użytkownika |
> | microsoft.directory/users/reprocessLicenseAssignment | Ponowne przetwarzanie przypisań licencji dla użytkowników |
> | microsoft.directory/users/restore | Przywracanie usuniętych użytkowników |
> | microsoft.directory/users/basic/update | Aktualizowanie podstawowych właściwości użytkowników |
> | microsoft.directory/users/manager/update | Menedżer aktualizacji dla użytkowników |
> | microsoft.directory/users/password/update | Resetowanie haseł dla wszystkich użytkowników |
> | microsoft.directory/users/userPrincipalName/update | Aktualizowanie głównej nazwy użytkownika użytkowników |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie Service Health w centrum administracyjne platformy Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie żądań obsługi Microsoft 365 zarządzanie nimi |
> | microsoft.office365.webPortal/allEntities/standard/read | Odczytywanie podstawowych właściwości wszystkich zasobów w centrum administracyjne platformy Microsoft 365 |

## <a name="how-to-understand-role-permissions"></a>Jak zrozumieć uprawnienia roli

Schemat uprawnień jest luźno zgodny z formatem REST Microsoft Graph:

`<namespace>/<entity>/<propertySet>/<action>`

Na przykład:

`microsoft.directory/applications/credentials/update`

| Permission, element | Opis |
| --- | --- |
| namespace | Produkt lub usługa, która uwidacznia zadanie i jest dołączana do `microsoft` . Na przykład wszystkie zadania w usłudze Azure AD używają przestrzeni `microsoft.directory` nazw . |
| jednostka | Funkcja logiczna lub składnik ujawniony przez usługę w Microsoft Graph. Na przykład usługa Azure AD uwidacznia użytkowników i grupy, program OneNote uwidacznia notatki, a program Exchange uwidacznia skrzynki pocztowe i kalendarze. Istnieje specjalne słowo `allEntities` kluczowe do określania wszystkich jednostek w przestrzeni nazw. Jest to często używane w rolach, które przyznają dostęp do całego produktu. |
| propertySet | Określone właściwości lub aspekty jednostki, dla której udzielany jest dostęp. Na przykład program daje możliwość odczytu adresu URL odpowiedzi, adresu URL wylogowania i niejawnej właściwości przepływu w obiekcie aplikacji `microsoft.directory/applications/authentication/read` w usłudze Azure AD.<ul><li>`allProperties` Wyznacza wszystkie właściwości jednostki, w tym właściwości uprzywilejowane.</li><li>`standard` określa typowe właściwości, ale wyklucza właściwości uprzywilejowane powiązane z `read` akcją. Na przykład program umożliwia odczytywanie standardowych właściwości, takich jak publiczny numer telefonu i adres e-mail, ale nie prywatny pomocniczy numer telefonu lub adres e-mail używany do uwierzytelniania `microsoft.directory/user/standard/read` wieloskładnikowego.</li><li>`basic` określa typowe właściwości, ale wyklucza uprzywilejowane właściwości powiązane z `update` akcją. Zestaw właściwości, które można odczytać, może różnić się od tego, co można zaktualizować. Dlatego istnieją słowa kluczowe `standard` i , które to `basic` odzwierciedlają.</li></ul> |
| action | Udzielana operacja, najczęściej tworzyć, odczytywać, aktualizować lub usuwać (CRUD). Istnieje specjalne słowo kluczowe do określania wszystkich powyższych `allTasks` możliwości (tworzenie, odczytywanie, aktualizowanie i usuwanie). |

## <a name="deprecated-roles"></a>Przestarzałe role

Nie należy używać następujących ról. Zostały one wycofane i w przyszłości zostaną usunięte z usługi Azure AD.

* Administrator licencji adhoc
* Dołączanie urządzenia
* Menedżerowie urządzeń
* Użytkownicy urządzeń
* Wyślij wiadomość e-mail do twórcy zweryfikowanego użytkownika
* Administrator skrzynki pocztowej
* Dołączanie urządzenia w miejscu pracy

## <a name="roles-not-shown-in-the-portal"></a>Role, które nie są wyświetlane w portalu

Nie każda rola zwrócona przez program PowerShell lub program MS interfejs Graph API jest widoczna w Azure Portal. W poniższej tabeli przedstawiono organizację tych różnic.

Nazwa interfejsu API | Azure Portal nazwy użytkownika | Uwagi
-------- | ------------------- | -------------
Dołączanie urządzenia | Przestarzałe | [Dokumentacja przestarzałych ról](#deprecated-roles)
Menedżerowie urządzeń | Przestarzałe | [Dokumentacja przestarzałych ról](#deprecated-roles)
Użytkownicy urządzeń | Przestarzałe | [Dokumentacja przestarzałych ról](#deprecated-roles)
Konta synchronizacji katalogów | Nie jest wyświetlany, ponieważ nie należy go używać | [Dokumentacja kont synchronizacji katalogów](#directory-synchronization-accounts)
Użytkownik-gość | Nie pokazano, ponieważ nie można jej używać | NA
Pomoc techniczna dla partnerów w warstwie 1 | Nie jest wyświetlany, ponieważ nie należy go używać | [Dokumentacja pomocy technicznej dla partnerów w warstwie 1](#partner-tier1-support)
Pomoc techniczna dla partnerów w warstwie 2 | Nie jest wyświetlany, ponieważ nie należy go używać | [Dokumentacja pomocy technicznej warstwy 2 partnera](#partner-tier2-support)
Ograniczony użytkownik-gość | Nie jest wyświetlany, ponieważ nie można go używać | NA
Użytkownik | Nie jest wyświetlany, ponieważ nie można go używać | NA
Dołączanie urządzenia w miejscu pracy | Przestarzałe | [Dokumentacja przestarzałych ról](#deprecated-roles)

## <a name="password-reset-permissions"></a>Uprawnienia resetowania hasła

Nagłówki kolumn reprezentują role, które mogą resetować hasła. Wiersze tabeli zawierają role, dla których można zresetować swoje hasło.

Hasło można zresetować | Administrator haseł | Administrator pomocy technicznej | Administrator uwierzytelniania | Administrator użytkownika | Administrator uwierzytelniania uprzywilejowanego | Administrator globalny
------ | ------ | ------ | ------ | ------ | ------ | ------
Administrator uwierzytelniania | &nbsp; | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Czytniki katalogów | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Administrator globalny | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:\*
Administrator grup | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Zapraszanie gości | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Administrator pomocy technicznej | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Centrum wiadomości czytelnika | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Administrator haseł | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Administrator uwierzytelniania uprzywilejowanego | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Administrator ról uprzywilejowanych | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Czytelnik raportów | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Użytkownik (bez roli administratora) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Administrator użytkowników | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Czytelnik raportów podsumowania użycia | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:

\* Administrator globalny nie może usunąć własnego przypisania administratora globalnego. Ma to na celu zapobieganie sytuacji, w której organizacja ma 0 administratorów globalnych.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról usługi Azure AD do grup](groups-assign-role.md)
- [Omówienie różnych ról](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Przypisywanie użytkownika jako administratora subskrypcji platformy Azure](../../role-based-access-control/role-assignments-portal-subscription-admin.md)
