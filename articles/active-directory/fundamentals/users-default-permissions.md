---
title: Domyślne uprawnienia użytkownika — Azure Active Directory | Microsoft Docs
description: Poznaj różne uprawnienia użytkowników dostępne w Azure Active Directory.
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
ms.openlocfilehash: eb01a3e0fc5bc11a4d3de62b16aafb7dd308e34a
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724275"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Jakie są domyślne uprawnienia użytkownika w Azure Active Directory?
W usłudze Azure Active Directory (Azure AD) wszystkim użytkownikom jest udzielany zestaw uprawnień domyślnych. Dostęp użytkownika składa się z typu użytkownika, ich [przypisań ról](active-directory-users-assign-role-azure-portal.md)i ich własności do poszczególnych obiektów. W tym artykule opisano te uprawnienia domyślne oraz zawarto porównanie uprawnień domyślnych członka i użytkownika gościa. Domyślne uprawnienia użytkownika można zmienić tylko w ustawieniach użytkownika w usłudze Azure AD.

## <a name="member-and-guest-users"></a>Użytkownicy będący członkami i gośćmi
Zestaw odebranych uprawnień domyślnych zależy od tego, czy użytkownik jest natywnym członkiem dzierżawy (użytkownik), czy też użytkownik jest przełączany z innego katalogu jako gość współpracy B2B (gość). Zobacz artykuł [co to jest współpraca B2B w usłudze Azure AD?](../external-identities/what-is-b2b.md) Aby uzyskać więcej informacji na temat dodawania użytkowników-Gości.
* Użytkownicy będący członkami mogą rejestrować aplikacje, zmieniać swoje zdjęcie profilowe, numer telefonu komórkowego oraz hasło. Mogą też zapraszać gości B2B. Dodatkowo użytkownicy mogą odczytywać wszystkie informacje o katalogach (z kilkoma wyjątkami). 
* Użytkownicy-Goście mają ograniczone uprawnienia do katalogu. Mogą oni zarządzać własnym profilem, zmieniać własne hasło i pobierać pewne informacje dotyczące innych użytkowników, grup i aplikacji, ale nie mogą odczytywać wszystkich informacji o katalogu. Na przykład użytkownicy-Goście nie mogą wyliczyć listy wszystkich użytkowników, grup i innych obiektów katalogu. Gości można dodawać do ról administratora, dzięki czemu uzyskają oni pełne uprawnienia do odczytu i zapisu zawarte w roli. Goście mogą również zapraszać innych Gości.

## <a name="compare-member-and-guest-default-permissions"></a>Porównanie uprawnień domyślnych członka i gościa

**Warstwowy** | **Uprawnienia członka** | **Domyślne uprawnienia użytkownika-gościa** | **Ograniczone uprawnienia użytkownika-gościa (wersja zapoznawcza)**
------------ | --------- | ---------- | ----------
Użytkownicy i kontakty | <ul><li>Wyliczanie listy wszystkich użytkowników i kontaktów<li>Odczytywanie wszystkich publicznych właściwości użytkowników i kontaktów</li><li>Zapraszanie gości<li>Zmiana własnego hasła<li>Zarządzanie własnym numerem telefonu komórkowego<li>Zarządzanie własnym zdjęciem<li>Unieważnianie własnych tokenów odświeżania</li></ul> | <ul><li>Odczytywanie własnych właściwości<li>Odczytaj wyświetlaną nazwę, adres e-mail, nazwę logowania, Zdjęcie, główną nazwę użytkownika i właściwości typu użytkownika innych użytkowników i kontaktów<li>Zmiana własnego hasła<li>Wyszukaj innego użytkownika według identyfikatora ObjectId (jeśli jest to dozwolone)<li>Menedżer odczytu i bezpośredni raport informacje dla innych użytkowników</li></ul> | <ul><li>Odczytywanie własnych właściwości<li>Zmiana własnego hasła</li></ul>
Grupy | <ul><li>Tworzenie grup zabezpieczeń<li>Tworzenie grup Microsoft 365<li>Wyliczanie listy wszystkich grup<li>Odczytywanie wszystkich właściwości grup<li>Odczytywanie nieukrytych członkostw w grupach<li>Odczytaj ukryte członkostwa w grupach Microsoft 365 dla grupy sprzężonej<li>Zarządzanie właściwościami, własnością i członkostwem w grupach, do których należy użytkownik<li>Dodawanie gości do posiadanych grup<li>Zarządzanie dynamicznymi ustawieniami członkostwa<li>Usuwanie posiadanych grup<li>Przywróć należące do Microsoft 365 grupy</li></ul> | <ul><li>Odczytaj właściwości nieukrytych grup, w tym członkostwo i prawa własności (nawet grupy Niesprzężone)<li>Odczytaj ukryte członkostwa w grupach Microsoft 365 dla grup sprzężonych<li>Wyszukaj grupy według nazwy wyświetlanej lub identyfikatora obiektu (jeśli jest to dozwolone)</li></ul> | <ul><li>Odczytaj identyfikator obiektu dla grup sprzężonych<li>Odczytuj członkostwo i własność przyłączonych grup w niektórych aplikacjach Microsoft 365 (jeśli są dozwolone)</li></ul>
Aplikacje | <ul><li>Rejestrowanie (tworzenie) nowej aplikacji<li>Wyliczanie listy wszystkich aplikacji<li>Odczytywanie właściwości zarejestrowanych aplikacji i aplikacji dla przedsiębiorstw<li>Zarządzanie właściwościami, przydziałami i poświadczeniami posiadanych aplikacji<li>Tworzenie i usuwanie hasła aplikacji dla użytkownika<li>Usuwanie posiadanych aplikacji<li>Przywracanie posiadanych aplikacji</li></ul> | <ul><li>Odczytywanie właściwości zarejestrowanych aplikacji i aplikacji dla przedsiębiorstw</li></ul> | <ul><li>Odczytywanie właściwości zarejestrowanych aplikacji i aplikacji dla przedsiębiorstw
Urządzenia</li></ul> | <ul><li>Wyliczenie listy wszystkich urządzeń<li>Odczytywanie wszystkich właściwości urządzenia<li>Zarządzanie wszystkimi właściwościami posiadanych urządzeń</li></ul> | Brak uprawnień | Brak uprawnień
Katalog | <ul><li>Odczytywanie wszystkich informacji o firmie<li>Odczytywanie wszystkich domen<li>Odczytywanie wszystkich kontraktów partnera</li></ul> | <ul><li>Odczytaj nazwę wyświetlaną firmy<li>Odczytywanie wszystkich domen</li></ul> | <ul><li>Odczytaj nazwę wyświetlaną firmy<li>Odczytywanie wszystkich domen</li></ul>
Role i zakresy | <ul><li>Odczytywanie wszystkich ról administracyjnych i członkostw<li>Odczytywanie wszystkich właściwości i członkostw jednostek administracyjnych</li></ul> | Brak uprawnień | Brak uprawnień
Subskrypcje | <ul><li>Odczytywanie wszystkich subskrypcji<li>Włączanie członka planu usługi</li></ul> | Brak uprawnień | Brak uprawnień
Zasady | <ul><li>Odczytywanie wszystkich właściwości zasad<li>Zarządzanie wszystkimi właściwościami posiadanych zasad</li></ul> | Brak uprawnień | Brak uprawnień

## <a name="restrict-member-users-default-permissions"></a>Ogranicz domyślne uprawnienia użytkowników członkowskich 

Uprawnienia domyślne dla użytkowników należących do członków mogą być ograniczone w następujący sposób:

Uprawnienie | Wyjaśnienie ustawienia
---------- | ------------
Użytkownicy mogą rejestrować aplikację | Ustawienie tej opcji na wartość nie uniemożliwia użytkownikom tworzenie rejestracji aplikacji. Możliwość można następnie udzielić z powrotem do określonych osób przez dodanie ich do roli Deweloper aplikacji.
Zezwalaj użytkownikom na łączenie konta służbowego z usługą LinkedIn | Ustawienie tej opcji na wartość nie uniemożliwia użytkownikom łączenie konta służbowego z kontem usługi LinkedIn. Aby uzyskać więcej informacji, zobacz [konta usługi LinkedIn — udostępnianie i wyrażanie danych](../enterprise-users/linkedin-user-consent.md).
Możliwość tworzenia grup zabezpieczeń | Ustawienie tej opcji na wartość Nie uniemożliwia użytkownikom tworzenie grup zabezpieczeń. Administratorzy globalni i Administratorzy użytkowników nadal mogą tworzyć grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../enterprise-users/groups-settings-cmdlets.md).
Możliwość tworzenia grup Microsoft 365 | Ustawienie tej opcji na wartość nie uniemożliwia użytkownikom tworzenie grup Microsoft 365. Ustawienie tej opcji na kilka umożliwia wybranie zestawu użytkowników w celu utworzenia grup Microsoft 365. Administratorzy globalni i Administratorzy użytkowników nadal będą mogli tworzyć grupy Microsoft 365. Aby uzyskać więcej informacji, zobacz [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../enterprise-users/groups-settings-cmdlets.md).
Ogranicz dostęp do portalu administracyjnego usługi Azure AD | Ustawienie tej opcji na nie umożliwia innym firmom korzystanie z portalu administracyjnego usługi Azure AD w celu odczytywania zasobów usługi Azure AD i zarządzania nimi. Wartość tak ogranicza dostęp wszystkich innych niż administratorów do danych usługi Azure AD w portalu administracyjnym.<p>**Uwaga**: to ustawienie nie ogranicza dostępu do danych usługi Azure AD przy użyciu programu PowerShell lub innych klientów, takich jak Visual Studio. w przypadku ustawienia wartości tak w celu przyznania konkretnemu użytkownikowi niebędącemu administratorem możliwości korzystania z portalu administracyjnego usługi Azure AD Przypisz dowolną rolę administracyjną, taką jak rola czytelnicy katalogów.<p>Ta rola umożliwia odczytywanie informacji o katalogu podstawowym, które są domyślnie dostępne dla użytkowników (Goście i nazwy główne usługi).
Możliwość odczytywania innych użytkowników | To ustawienie jest dostępne tylko w programie PowerShell. Ustawienie tej flagi na $false uniemożliwia wszystkim innym niż administratorzy odczytywanie informacji o użytkownikach z katalogu. Ta flaga nie uniemożliwia odczytywania informacji o użytkowniku w innych usługach firmy Microsoft, takich jak Exchange Online. To ustawienie jest przeznaczone dla szczególnych okoliczności i ustawienie tej flagi na $false nie jest zalecane.

## <a name="restrict-guest-users-default-permissions"></a>Ogranicz domyślne uprawnienia gościa

Uprawnienia domyślne dla użytkowników-Gości mogą być ograniczone w następujący sposób:

>[!NOTE]
>Ustawienie ograniczenia dostępu użytkownika gościa zastąpiło ustawienie **ograniczone uprawnienia użytkowników-Gości** . Aby uzyskać wskazówki dotyczące korzystania z tej funkcji, zobacz [ograniczanie uprawnień dostępu gościa (wersja zapoznawcza) w Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).

Uprawnienie | Wyjaśnienie ustawienia
---------- | ------------
Ograniczenia dostępu użytkowników Gości (wersja zapoznawcza) | Ustawienie tej opcji dla **użytkowników-Gości ma taki sam dostęp, jak członkowie** domyślnie przyznają wszystkim uprawnienia użytkownikom-Gościom.<p>Ustawienie tej opcji na **dostęp gościa jest ograniczone do właściwości i członkostwa własnych obiektów katalogu** ogranicza dostęp gościa tylko do własnego profilu użytkownika. Dostęp do innych użytkowników nie jest już dozwolony nawet podczas wyszukiwania według nazwy głównej użytkownika, identyfikatora obiektu lub nazwy wyświetlanej. Dostęp do informacji o grupach, w tym członkostwa w grupach, również nie jest już dozwolony.<p>**Uwaga**: to ustawienie nie zapobiega dostępowi do przyłączonych grup w niektórych usługach Microsoft 365, takich jak Microsoft Teams. Aby dowiedzieć się więcej, zobacz [dostęp gościa w usłudze Microsoft Teams](/MicrosoftTeams/guest-access) .<p>Użytkowników-Gości można nadal dodawać do ról administratora, niezależnie od tego, jakie są te ustawienia uprawnień.
Goście mogą zapraszać gości | Ustawienie tej opcji na wartość tak umożliwia Gościom Zapraszanie innych Gości. Aby dowiedzieć się więcej, zobacz [delegowanie zaproszeń do współpracy B2B](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) .
Członkowie mogą zapraszać gości | Ustawienie tej opcji na tak umożliwia członkom katalogu niebędącym administratorami zapraszanie Gości. Aby dowiedzieć się więcej, zobacz [delegowanie zaproszeń do współpracy B2B](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) .
Administratorzy i użytkownicy o roli zapraszającego gości mogą zapraszać | Ustawienie tej opcji na wartość tak umożliwia administratorom i użytkownikom w roli "zapraszanie gościa" zapraszanie Gości. W przypadku ustawienia opcji tak użytkownicy w roli zapraszania gościa nadal będą mogli zapraszać Gości, niezależnie od elementów członkowskich, które mogą zapraszać ustawienia. Aby dowiedzieć się więcej, zobacz [delegowanie zaproszeń do współpracy B2B](../external-identities/delegate-invitations.md#assign-the-guest-inviter-role-to-a-user) .

## <a name="object-ownership"></a>Własność obiektu

### <a name="application-registration-owner-permissions"></a>Uprawnienia właściciela przy rejestracji aplikacji
Gdy użytkownik rejestruje aplikację, jest on automatycznie dodawany jako właściciel aplikacji. Jako właściciel użytkownik może zarządzać metadanymi aplikacji, takimi jak nazwa i uprawnienia żądane przez aplikację. Właściciele mogą również zarządzać konfiguracją aplikacji specyficzną dla dzierżawy. Może to być na przykład konfiguracja logowania jednokrotnego i przypisania użytkownika. Właściciel może również dodawać i usuwać innych właścicieli. W przeciwieństwie do administratorów globalnych właściciele mogą zarządzać tylko posiadanymi przez siebie aplikacjami.

### <a name="enterprise-application-owner-permissions"></a>Uprawnienia właściciela aplikacji dla przedsiębiorstw
Gdy użytkownik doda nową aplikację dla przedsiębiorstw, zostanie ona automatycznie dodana jako właściciel. Jako właściciel mogą zarządzać konfiguracją aplikacji specyficzną dla dzierżawców, taką jak konfiguracja logowania jednokrotnego, Inicjowanie obsługi administracyjnej i przypisania użytkowników. Właściciel może również dodawać i usuwać innych właścicieli. W przeciwieństwie do administratorów globalnych właściciele mogą zarządzać tylko aplikacjami, których są właścicielami.

### <a name="group-owner-permissions"></a>Uprawnienia właściciela grupy
Gdy użytkownik tworzy grupę, jest automatycznie dodawany jako właściciel tej grupy. Jako właściciel mogą zarządzać właściwościami grupy, takimi jak nazwa, a także do zarządzania członkostwem w grupie. Właściciel może również dodawać i usuwać innych właścicieli. W przeciwieństwie do administratorów globalnych i administratorów użytkowników, właściciele mogą zarządzać tylko tymi grupami. Aby dowiedzieć się, jak przypisać właściciela grupy, zobacz [Managing owners for a group](active-directory-accessmanagement-managing-group-owners.md) (Zarządzanie właścicielami grupy).

### <a name="ownership-permissions"></a>Uprawnienia własności
W poniższych tabelach opisano określone uprawnienia w Azure Active Directory użytkownicy członkowie mają do nich obiekty. Użytkownik ma tylko te uprawnienia do obiektów, które są właścicielami.

#### <a name="owned-application-registrations"></a>Rejestracje posiadanych aplikacji
Użytkownicy mogą wykonywać następujące działania dotyczące rejestracji aplikacji należących do użytkownika.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. katalog/aplikacje/odbiorcy/aktualizacja | Aktualizowanie właściwości Applications. odbiorców w Azure Active Directory. |
| Microsoft. katalog/aplikacje/uwierzytelnianie/aktualizacja | Aktualizowanie właściwości Applications. Authentication w Azure Active Directory. |
| Microsoft. Directory/Applications/Basic/Update | Zaktualizuj podstawowe właściwości aplikacji w Azure Active Directory. |
| Microsoft. Directory/Applications/Credentials/Update | Zaktualizuj Właściwość Applications. Credentials w Azure Active Directory. |
| Microsoft. katalog/aplikacje/usuwanie | Usuń aplikacje w Azure Active Directory. |
| Microsoft. Directory/Applications/Owners/Update | Aktualizowanie właściwości Applications. Owners w Azure Active Directory. |
| Microsoft. Directory/Applications/Permissions/Update | Aktualizowanie właściwości Applications. Permissions w Azure Active Directory. |
| Microsoft. Directory/Applications/policies/Update | Zaktualizuj Właściwość Applications. policies w Azure Active Directory. |
| Microsoft. katalog/aplikacje/przywracanie | Przywróć aplikacje w Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Posiadane aplikacje dla przedsiębiorstw
Użytkownicy mogą wykonywać następujące działania w aplikacjach należących do przedsiębiorstwa. Aplikacja firmowa składa się z nazwy głównej usługi, co najmniej jednej zasady aplikacji, a czasami obiektu aplikacji w tej samej dzierżawie co nazwa główna usługi.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie auditLogs w Azure Active Directory. |
| Microsoft. katalog/zasady/podstawowa/aktualizacja | Zaktualizuj podstawowe właściwości zasad w Azure Active Directory. |
| Microsoft. Directory/zasady/usuwanie | Usuń zasady w Azure Active Directory. |
| Microsoft. katalog/zasady/właściciele/aktualizacja | Aktualizowanie właściwości policies. Owners w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignedTo/Update | Zaktualizuj Właściwość serviceprincipals. appRoleAssignedTo w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignments/Update | Zaktualizuj Właściwość Users. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/odbiorcy/aktualizacja | Aktualizacja właściwości serviceprincipals. odbiorca w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Authentication/Update | Aktualizacja właściwości serviceprincipals. Authentication w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Basic/Update | Zaktualizuj podstawowe właściwości obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Credentials/Update | Aktualizacja właściwości serviceprincipals. Credentials w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Delete | Usuwanie obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Owners/Update | Aktualizacja właściwości serviceprincipals. Owners w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Permissions/Update | Aktualizacja właściwości serviceprincipals. Permissions w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/policies/Update | Aktualizacja właściwości serviceprincipals. policies w Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie signInReports w Azure Active Directory. |

#### <a name="owned-devices"></a>Urządzenia należące
Użytkownicy mogą wykonywać następujące działania na urządzeniach należących do firmy.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Devices/bitLockerRecoveryKeys/Read | Odczytaj Właściwość Devices. bitLockerRecoveryKeys w Azure Active Directory. |
| Microsoft. Directory/Devices/Disable | Wyłącz urządzenia w Azure Active Directory. |

#### <a name="owned-groups"></a>Należące do Ciebie grupy
Użytkownicy mogą wykonywać następujące działania w grupach będących własnością.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Groups/appRoleAssignments/Update | Zaktualizuj Właściwość groups. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Groups/Basic/Update | Zaktualizuj podstawowe właściwości dla grup w Azure Active Directory. |
| Microsoft. Directory/Groups/Delete | Usuń grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/dynamicMembershipRule/Update | Zaktualizuj Właściwość groups. dynamicMembershipRule w Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Update | Aktualizowanie właściwości groups. Members w Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Update | Aktualizacja właściwości groups. Owners w Azure Active Directory. |
| Microsoft. Directory/Groups/Restore | Przywróć grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/Settings/Update | Aktualizowanie właściwości groups. Settings w Azure Active Directory. |

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat ustawienia ograniczeń dostępu użytkowników Gości, zobacz [ograniczanie uprawnień dostępu gościa (wersja zapoznawcza) w Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).
* Aby dowiedzieć się więcej o sposobie przypisywania ról administratora usługi Azure AD, zobacz [Przypisywanie użytkownika do ról administratorów w Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Aby dowiedzieć się więcej o kontrolowaniu dostępu do zasobów na platformie Microsoft Azure, zobacz [Understanding resource access in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md) (Opis dostępu do zasobów na platformie Azure).
* Aby uzyskać więcej informacji dotyczących relacji między usługą Azure Active Directory i subskrypcją platformy Azure, zobacz [Jak subskrypcje platformy Azure są kojarzone z usługą Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).
* [Zarządzanie użytkownikami](add-users-azure-active-directory.md)