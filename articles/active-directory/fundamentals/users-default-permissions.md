---
title: Domyślne uprawnienia użytkownika — Azure Active Directory | Microsoft Docs
description: Dowiedz się więcej o różnych uprawnieniach użytkownika dostępnych w Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18, contperf-fy21q1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85c588b82d2b6f4a4dce0ce41effc3b0336df3f4
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567319"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Jakie są domyślne uprawnienia użytkownika w Azure Active Directory?
W usłudze Azure Active Directory (Azure AD) wszystkim użytkownikom jest udzielany zestaw uprawnień domyślnych. Dostęp użytkownika składa się z typu użytkownika, jego przypisań [ról](active-directory-users-assign-role-azure-portal.md)i własności poszczególnych obiektów. W tym artykule opisano te uprawnienia domyślne oraz zawarto porównanie uprawnień domyślnych członka i użytkownika gościa. Domyślne uprawnienia użytkownika można zmienić tylko w ustawieniach użytkownika w usłudze Azure AD.

## <a name="member-and-guest-users"></a>Użytkownicy będący członkami i gośćmi
Zestaw otrzymanych uprawnień domyślnych zależy od tego, czy użytkownik jest natywnym członkiem dzierżawy (użytkownikiem członkowskim), czy też jest przejmyty z innego katalogu jako gość współpracy B2B (użytkownik-gość). Aby [uzyskać więcej informacji na temat dodawania użytkowników-gości,](../external-identities/what-is-b2b.md) zobacz Co to jest współpraca B2B w usłudze Azure AD?
* Użytkownicy będący członkami mogą rejestrować aplikacje, zmieniać swoje zdjęcie profilowe, numer telefonu komórkowego oraz hasło. Mogą też zapraszać gości B2B. Dodatkowo użytkownicy mogą odczytywać wszystkie informacje o katalogach (z kilkoma wyjątkami). 
* Użytkownicy-goście mają ograniczone uprawnienia do katalogu. Mogą oni zarządzać własnym profilem, zmieniać własne hasło i pobierać pewne informacje o innych użytkownikach, grupach i aplikacjach, ale nie mogą odczytywać wszystkich informacji katalogu. Na przykład użytkownicy-goście nie mogą wyliczać listy wszystkich użytkowników, grup i innych obiektów katalogu. Gości można dodawać do ról administratora, dzięki czemu uzyskają oni pełne uprawnienia do odczytu i zapisu zawarte w roli. Goście mogą również zapraszać innych gości.

## <a name="compare-member-and-guest-default-permissions"></a>Porównanie uprawnień domyślnych członka i gościa

**Warstwowy** | **Uprawnienia członka** | **Domyślne uprawnienia użytkownika-gościa** | **Ograniczone uprawnienia użytkownika-gościa (wersja zapoznawcza)**
------------ | --------- | ---------- | ----------
Użytkownicy i kontakty | <ul><li>Wyliczanie listy wszystkich użytkowników i kontaktów<li>Odczytywanie wszystkich publicznych właściwości użytkowników i kontaktów</li><li>Zapraszanie gości<li>Zmiana własnego hasła<li>Zarządzanie własnym numerem telefonu komórkowego<li>Zarządzanie własnym zdjęciem<li>Unieważnianie własnych tokenów odświeżania</li></ul> | <ul><li>Odczytywanie własnych właściwości<li>Odczytywanie nazwy wyświetlanej, adresu e-mail, nazwy logowania, zdjęcia, głównej nazwy użytkownika i właściwości typu użytkownika innych użytkowników i kontaktów<li>Zmiana własnego hasła<li>Wyszukaj innego użytkownika według właściwości ObjectId (jeśli jest dozwolona)<li>Odczytywanie informacji o menedżerze i raportach bezpośrednich innych użytkowników</li></ul> | <ul><li>Odczytywanie własnych właściwości<li>Zmiana własnego hasła</li><li>Zarządzanie własnym numerem telefonu komórkowego</li></ul>
Grupy | <ul><li>Tworzenie grup zabezpieczeń<li>Tworzenie Microsoft 365 zasobów<li>Wyliczanie listy wszystkich grup<li>Odczytywanie wszystkich właściwości grup<li>Odczytywanie nieukrytych członkostw w grupach<li>Odczytywanie ukrytych Microsoft 365 grupy dla grupy przyłączone<li>Zarządzanie właściwościami, własnością i członkostwem w grupach, których właścicielem jest użytkownik<li>Dodawanie gości do posiadanych grup<li>Zarządzanie dynamicznymi ustawieniami członkostwa<li>Usuwanie posiadanych grup<li>Przywracanie posiadanych Microsoft 365 zasobów</li></ul> | <ul><li>Odczytywanie właściwości nieukońowanych grup, w tym członkostwa i własności (nawet niesłączeniu grup)<li>Odczytywanie ukrytych Microsoft 365 członkostwa w grupach dla grup przyłączone<li>Wyszukiwanie grup według nazwy wyświetlanej lub obiektu ObjectId (jeśli jest dozwolone)</li></ul> | <ul><li>Odczytywanie identyfikatora obiektu dla grup przyłączone<li>Odczytywanie członkostwa i własności grup przyłączone w niektórych Microsoft 365 (jeśli jest to dozwolone)</li></ul>
Aplikacje | <ul><li>Rejestrowanie (tworzenie) nowej aplikacji<li>Wyliczanie listy wszystkich aplikacji<li>Odczytywanie właściwości zarejestrowanych aplikacji i aplikacji dla przedsiębiorstw<li>Zarządzanie właściwościami, przydziałami i poświadczeniami posiadanych aplikacji<li>Tworzenie i usuwanie hasła aplikacji dla użytkownika<li>Usuwanie posiadanych aplikacji<li>Przywracanie posiadanych aplikacji</li></ul> | <ul><li>Odczytywanie właściwości zarejestrowanych aplikacji i aplikacji dla przedsiębiorstw</li></ul> | <ul><li>Odczytywanie właściwości zarejestrowanych aplikacji i aplikacji dla przedsiębiorstw
Urządzenia</li></ul> | <ul><li>Wyliczanie listy wszystkich urządzeń<li>Odczytywanie wszystkich właściwości urządzenia<li>Zarządzanie wszystkimi właściwościami posiadanych urządzeń</li></ul> | Brak uprawnień | Brak uprawnień
Katalog | <ul><li>Odczytywanie wszystkich informacji o firmie<li>Odczytywanie wszystkich domen<li>Odczytywanie wszystkich kontraktów partnera</li></ul> | <ul><li>Odczytywanie nazwy wyświetlanej firmy<li>Odczytywanie wszystkich domen</li></ul> | <ul><li>Odczytywanie nazwy wyświetlanej firmy<li>Odczytywanie wszystkich domen</li></ul>
Role i zakresy | <ul><li>Odczytywanie wszystkich ról administracyjnych i członkostw<li>Odczytywanie wszystkich właściwości i członkostw jednostek administracyjnych</li></ul> | Brak uprawnień | Brak uprawnień
Subskrypcje | <ul><li>Odczytywanie wszystkich subskrypcji<li>Włączanie członka planu usługi</li></ul> | Brak uprawnień | Brak uprawnień
Zasady | <ul><li>Odczytywanie wszystkich właściwości zasad<li>Zarządzanie wszystkimi właściwościami posiadanych zasad</li></ul> | Brak uprawnień | Brak uprawnień

## <a name="restrict-member-users-default-permissions"></a>Ograniczanie uprawnień domyślnych użytkowników członkowskich 

Uprawnienia domyślne dla użytkowników członkowskich można ograniczyć w następujący sposób:

Uprawnienie | Wyjaśnienie ustawienia
---------- | ------------
Użytkownicy mogą rejestrować aplikacje | Ustawienie tej opcji na nie uniemożliwia użytkownikom tworzenie rejestracji aplikacji. Następnie można przyznać tę możliwość określonym osobom, dodając je do roli dewelopera aplikacji.
Zezwalaj użytkownikom na łączenie konta służbowego z usługą LinkedIn | Ustawienie tej opcji na nie uniemożliwia użytkownikom łączenie konta służbowego z kontem usługi LinkedIn. Aby uzyskać więcej informacji, zobacz [Udostępnianie danych połączeń konta usługi LinkedIn i wyrażanie zgody.](../enterprise-users/linkedin-user-consent.md)
Możliwość tworzenia grup zabezpieczeń | Ustawienie tej opcji na wartość Nie uniemożliwia użytkownikom tworzenie grup zabezpieczeń. Administratorzy globalni i administratorzy użytkowników mogą nadal tworzyć grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../enterprise-users/groups-settings-cmdlets.md).
Możliwość tworzenia grup Microsoft 365 grupy | Ustawienie tej opcji na nie uniemożliwia użytkownikom tworzenie Microsoft 365 grup. Ustawienie tej opcji na wartość Niektóre umożliwia wybranemu zestawowi użytkowników tworzenie Microsoft 365 grupy. Administratorzy globalni i administratorzy użytkowników nadal będą mogli tworzyć Microsoft 365 grupy. Aby uzyskać więcej informacji, zobacz [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../enterprise-users/groups-settings-cmdlets.md).
Ogranicz dostęp do portalu administracyjnego usługi Azure AD | Ustawienie tej opcji na nie umożliwia użytkownikom niebędącym administratorami korzystanie z portalu administracyjnego usługi Azure AD w celu odczytywania zasobów usługi Azure AD i zarządzania nimi. Tak ogranicza dostęp wszystkich użytkowników niebędących administratorami do żadnych danych usługi Azure AD w portalu witrynie administracyjnej.<p>**Uwaga:** to ustawienie nie ogranicza dostępu do danych usługi Azure AD przy użyciu programu PowerShell lub innych klientów, takich jak Visual Studio.W przypadku ustawienia wartości Tak, aby przyznać określoneowi użytkownikowi bez uprawnień administratora możliwość korzystania z portalu administracyjnego usługi Azure AD i przypisywania wszelkich ról administracyjnych, takich jak rola Czytelnicy katalogów.<p>Ta rola umożliwia odczytywanie podstawowych informacji o katalogu, które są domyślnie użytkownikami członkowskimi (goście i jednostki usługi nie).
Możliwość odczytywania innych użytkowników | To ustawienie jest dostępne tylko w programie PowerShell. Ustawienie tej flagi na $false uniemożliwia wszystkim użytkownikom niebędącym administratorami odczytywanie informacji o użytkowniku z katalogu. Ta flaga nie uniemożliwia odczytywania informacji o użytkowniku w innych usługi firmy Microsoft takich jak Exchange Online. To ustawienie jest przeznaczone dla szczególnych okoliczności i ustawienie tej flagi na $false nie jest zalecane.

## <a name="restrict-guest-users-default-permissions"></a>Ograniczanie uprawnień domyślnych użytkowników-gości

Uprawnienia domyślne dla użytkowników-gości można ograniczyć w następujący sposób:

>[!NOTE]
>Ustawienie ograniczeń dostępu użytkowników gości zastąpiło ustawienie **Uprawnienia użytkowników-gości są ograniczone.** Aby uzyskać wskazówki dotyczące korzystania z tej funkcji, zobacz [Ograniczanie uprawnień dostępu gościa (wersja zapoznawcza) w Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).

Uprawnienie | Wyjaśnienie ustawienia
---------- | ------------
Ograniczenia dostępu gości (wersja zapoznawcza) | Ustawienie tej opcji na **użytkownicy-goście mają taki sam dostęp** jak członkowie domyślnie udziela wszystkich uprawnień użytkowników-członków użytkownikom-gościom.<p>Ustawienie tej opcji na **dostęp użytkowników-gości** jest domyślnie ograniczone do właściwości i członkostwa w ich własnych obiektach katalogu ogranicza dostęp gościa tylko do własnego profilu użytkownika. Dostęp do innych użytkowników nie jest już dozwolony nawet w przypadku wyszukiwania według głównej nazwy użytkownika, obiektu ObjectId lub nazwy wyświetlanej. Dostęp do informacji o grupach, w tym członkostwa w grupach, również nie jest już dozwolony.<p>**Uwaga:** to ustawienie nie uniemożliwia dostępu do grup przyłączone w niektórych usługach Microsoft 365 takich jak Microsoft Teams. Aby dowiedzieć się [więcej, zobacz Dostęp gościa](/MicrosoftTeams/guest-access) w aplikacji Microsoft Teams.<p>Użytkownicy-goście nadal mogą być dodani do ról administratorów niezależnie od tych ustawień uprawnień.
Goście mogą zapraszać gości | Ustawienie tej opcji na tak umożliwia gościom zapraszanie innych gości. Aby dowiedzieć się więcej, zobacz [Delegate invitations for B2B collaboration](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) (Delegowanie zaproszeń na współpracę B2B).
Członkowie mogą zapraszać gości | Ustawienie tej opcji na tak umożliwia członkom katalogu niebędącym administratorami zapraszanie gości. Aby dowiedzieć się więcej, zobacz [Delegate invitations for B2B collaboration](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) (Delegowanie zaproszeń na współpracę B2B).
Administratorzy i użytkownicy o roli zapraszającego gości mogą zapraszać | Ustawienie tej opcji na tak umożliwia administratorom i użytkownikom z roli "Zapraszanie gości" zapraszanie gości. Po ustawieniu wartości Tak użytkownicy w osoba zapraszająca gości nadal będą mogli zapraszać gości, niezależnie od ustawienia Członkowie mogą zapraszać gości. Aby dowiedzieć się więcej, zobacz [Delegate invitations for B2B collaboration](../external-identities/delegate-invitations.md#assign-the-guest-inviter-role-to-a-user) (Delegowanie zaproszeń na współpracę B2B).

## <a name="object-ownership"></a>Własność obiektu

### <a name="application-registration-owner-permissions"></a>Uprawnienia właściciela przy rejestracji aplikacji
Gdy użytkownik rejestruje aplikację, jest on automatycznie dodawany jako właściciel aplikacji. Jako właściciel użytkownik może zarządzać metadanymi aplikacji, takimi jak nazwa i uprawnienia żądane przez aplikację. Właściciele mogą również zarządzać konfiguracją aplikacji specyficzną dla dzierżawy. Może to być na przykład konfiguracja logowania jednokrotnego i przypisania użytkownika. Właściciel może również dodawać i usuwać innych właścicieli. W przeciwieństwie do administratorów globalnych właściciele mogą zarządzać tylko posiadanymi przez siebie aplikacjami.

### <a name="enterprise-application-owner-permissions"></a>Uprawnienia właściciela aplikacji przedsiębiorstwa
Gdy użytkownik dodaje nową aplikację przedsiębiorstwa, jest automatycznie dodawany jako właściciel. Jako właściciel może zarządzać konfiguracją aplikacji specyficzną dla dzierżawy, taką jak konfiguracja logowania jednokrotnego, aprowizowanie i przypisania użytkowników. Właściciel może również dodawać i usuwać innych właścicieli. W przeciwieństwie do administratorów globalnych właściciele mogą zarządzać tylko posiadaną przez siebie aplikacją.

### <a name="group-owner-permissions"></a>Uprawnienia właściciela grupy
Gdy użytkownik tworzy grupę, jest automatycznie dodawany jako właściciel tej grupy. Jako właściciel może zarządzać właściwościami grupy, takimi jak nazwa, a także zarządzać członkostwem w grupie. Właściciel może również dodawać i usuwać innych właścicieli. W przeciwieństwie do administratorów globalnych i administratorów użytkowników właściciele mogą zarządzać tylko posiadanych przez nich grupami. Aby dowiedzieć się, jak przypisać właściciela grupy, zobacz [Managing owners for a group](active-directory-accessmanagement-managing-group-owners.md) (Zarządzanie właścicielami grupy).

### <a name="ownership-permissions"></a>Uprawnienia własności
W poniższych tabelach opisano określone uprawnienia w Azure Active Directory członkowskie mają nad posiadanych obiektów. Użytkownik ma te uprawnienia tylko do obiektów, których jest właścicielem.

#### <a name="owned-application-registrations"></a>Rejestracje posiadanych aplikacji
Użytkownicy mogą wykonywać następujące akcje w przypadku rejestracji posiadanych aplikacji.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/applications/audience/update | Zaktualizuj właściwość applications.audience w Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Zaktualizuj właściwość applications.authentication w Azure Active Directory. |
| microsoft.directory/applications/basic/update | Aktualizowanie podstawowych właściwości aplikacji w Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Zaktualizuj właściwość applications.credentials w Azure Active Directory. |
| microsoft.directory/applications/delete | Usuń aplikacje w Azure Active Directory. |
| microsoft.directory/applications/owners/update | Zaktualizuj właściwość applications.owners w Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Zaktualizuj właściwość applications.permissions w Azure Active Directory. |
| microsoft.directory/applications/policies/update | Zaktualizuj właściwość applications.policies w Azure Active Directory. |
| microsoft.directory/applications/restore | Przywracanie aplikacji w Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Posiadane aplikacje dla przedsiębiorstw
Użytkownicy mogą wykonywać następujące akcje na posiadanych aplikacjach przedsiębiorstwa. Aplikacja przedsiębiorstwa składa się z jednostki usługi, co najmniej jednej zasady aplikacji, a czasami obiektu aplikacji w tej samej dzierżawie co jednostka usługi.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w dziennikach auditLogs Azure Active Directory. |
| microsoft.directory/policies/basic/update | Aktualizowanie podstawowych właściwości zasad w Azure Active Directory. |
| microsoft.directory/policies/delete | Usuń zasady w Azure Active Directory. |
| microsoft.directory/policies/owners/update | Zaktualizuj właściwość policies.owners w Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Zaktualizuj właściwość servicePrincipals.appRoleAssignedTo w Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Zaktualizuj właściwość users.appRoleAssignments w Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Zaktualizuj właściwość servicePrincipals.audience w Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Zaktualizuj właściwość servicePrincipals.authentication w Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Zaktualizuj podstawowe właściwości właściwości servicePrincipals w Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Zaktualizuj właściwość servicePrincipals.credentials w Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Usuń servicePrincipals w Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Zaktualizuj właściwość servicePrincipals.owners w Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Zaktualizuj właściwość servicePrincipals.permissions w pliku Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Zaktualizuj właściwość servicePrincipals.policies w Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) na stronie signInReports w Azure Active Directory. |

#### <a name="owned-devices"></a>Posiadane urządzenia
Użytkownicy mogą wykonywać następujące akcje na posiadanych urządzeniach.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Odczytaj właściwość devices.bitLockerRecoveryKeys w Azure Active Directory. |
| microsoft.directory/devices/disable | Wyłącz urządzenia w Azure Active Directory. |

#### <a name="owned-groups"></a>Posiadane grupy
Użytkownicy mogą wykonywać następujące akcje na posiadanych grupach.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Zaktualizuj właściwość groups.appRoleAssignments w Azure Active Directory. |
| microsoft.directory/groups/basic/update | Aktualizowanie podstawowych właściwości grup w Azure Active Directory. |
| microsoft.directory/groups/delete | Usuń grupy w Azure Active Directory. |
| microsoft.directory/groups/dynamicMembershipRule/update | Zaktualizuj właściwość groups.dynamicMembershipRule w Azure Active Directory. |
| microsoft.directory/groups/members/update | Zaktualizuj właściwość groups.members w Azure Active Directory. |
| microsoft.directory/groups/owners/update | Zaktualizuj właściwość groups.owners w Azure Active Directory. |
| microsoft.directory/groups/restore | Przywracanie grup w Azure Active Directory. |
| microsoft.directory/groups/settings/update | Zaktualizuj właściwość groups.settings w Azure Active Directory. |

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat ustawienia ograniczeń dostępu użytkowników gości, zobacz [Ograniczanie uprawnień dostępu gościa (wersja zapoznawcza) w Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).
* Aby dowiedzieć się więcej na temat przypisywania ról administratora usługi Azure AD, zobacz Przypisywanie użytkownika do ról administratora w [usłudze Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Aby dowiedzieć się więcej o kontrolowaniu dostępu do zasobów na platformie Microsoft Azure, zobacz [Understanding resource access in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md) (Opis dostępu do zasobów na platformie Azure).
* Aby uzyskać więcej informacji dotyczących relacji między usługą Azure Active Directory i subskrypcją platformy Azure, zobacz [Jak subskrypcje platformy Azure są kojarzone z usługą Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).
* [Zarządzanie użytkownikami](add-users-azure-active-directory.md)