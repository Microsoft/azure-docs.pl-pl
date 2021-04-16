---
title: Ograniczanie uprawnień dostępu użytkowników-gości — Azure Active Directory | Microsoft Docs
description: Ogranicz uprawnienia dostępu użytkowników-gości przy użyciu Azure Portal, programu PowerShell lub Microsoft Graph w Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 01/14/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: df4cb32720d80dd23289be7e760c9934e9a8db8a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501505"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>Ograniczanie uprawnień dostępu gościa (wersja zapoznawcza) w Azure Active Directory

Azure Active Directory (Azure AD) umożliwia ograniczenie tego, co zewnętrzni użytkownicy-goście mogą zobaczyć w swojej organizacji w usłudze Azure AD. Użytkownicy-goście są domyślnie ustawiani na ograniczony poziom uprawnień w usłudze Azure AD, natomiast domyślnym ustawieniem dla użytkowników-członków jest pełny zestaw domyślnych uprawnień użytkownika. Jest to wersja zapoznawcza nowego poziomu uprawnień użytkownika-gościa w ustawieniach współpracy zewnętrznej organizacji usługi Azure AD w celu uzyskania jeszcze bardziej ograniczonego dostępu, dzięki czemu dostępne są teraz:

Poziom uprawnień         | Poziom dostępu | Wartość
----------------         | ------------ | -----
Tak samo jak użytkownicy członkowski     | Goście mają taki sam dostęp do zasobów usługi Azure AD jak użytkownicy członkowski | a0b1b346-4d3e-4e8b-98f8-753987be4970
Ograniczony dostęp (ustawienie domyślne) | Goście mogą zobaczyć członkostwo we wszystkich nieu ukrytych grupach | 10dae51f-b6af-4016-8d66-8c2a99b929b3
**Ograniczony dostęp (nowy)**  | **Goście nie widzą członkostwa w żadnych grupach** | **2af84b1e-32c8-42b7-82bc-daa82404023b**

Jeśli dostęp gości jest ograniczony, goście mogą wyświetlać tylko własny profil użytkownika. Uprawnienia do wyświetlania innych użytkowników nie są dozwolone, nawet jeśli gość wyszukuje według głównej nazwy użytkownika lub objectId. Ograniczony dostęp ogranicza również możliwość zobaczenia członkostwa użytkowników-gości w grupach, w których się znajduje. Aby uzyskać więcej informacji o ogólnych domyślnych uprawnieniach użytkownika, w tym o uprawnieniach użytkowników-gości, zobacz What are the default user permissions in Azure Active Directory? (Jakie są domyślne uprawnienia użytkownika w [programie Azure Active Directory?).](../fundamentals/users-default-permissions.md)

## <a name="permissions-and-licenses"></a>Uprawnienia i licencje

Aby skonfigurować ustawienia współpracy zewnętrznej, musisz mieć rolę administratora globalnego. Nie ma żadnych dodatkowych wymagań dotyczących licencjonowania w celu ograniczenia dostępu gościa.

## <a name="update-in-the-azure-portal"></a>Aktualizacja w Azure Portal

W istniejących kontrolkach interfejsu użytkownika Azure Portal zmiany uprawnień użytkownika-gościa.

1. Zaloguj się do centrum [administracyjnego usługi Azure AD,](https://aad.portal.azure.com) administrator globalny uprawnieniami.
1. Na stronie **przeglądu Azure Active Directory** organizacji wybierz pozycję **Ustawienia użytkownika.**
1. W **obszarze Użytkownicy zewnętrzni** wybierz pozycję **Zarządzaj ustawieniami współpracy zewnętrznej.**
1. Na stronie **Ustawienia współpracy zewnętrznej** wybierz opcję Dostęp użytkowników-gości jest ograniczony do właściwości i członkosstwa własnych obiektów **katalogu.**

    ![Strona ustawień współpracy zewnętrznej usługi Azure AD](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. Wybierz pozycję **Zapisz**. Zmiany mogą zostać wprowadzone w przypadku użytkowników-gości po maksymalnie 15 minutach.

## <a name="update-with-the-microsoft-graph-api"></a>Aktualizowanie za pomocą interfejsu API Microsoft Graph api

Dodaliśmy nowy interfejs API Microsoft Graph do konfigurowania uprawnień gościa w organizacji usługi Azure AD. W celu przypisania dowolnego poziomu uprawnień można użyć następujących wywołań interfejsu API. Wartość guestUserRoleId używana w tym miejscu jest zilustrowana najbardziej ograniczonym ustawieniem użytkownika-gościa. Aby uzyskać więcej informacji na temat używania Microsoft Graph do ustawienia uprawnień gościa, zobacz typ zasobu [authorizationPolicy.](/graph/api/resources/authorizationpolicy)

### <a name="configuring-for-the-first-time"></a>Konfigurowanie po raz pierwszy

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

Odpowiedzią powinna być Powodzenie 204.

### <a name="updating-the-existing-value"></a>Aktualizowanie istniejącej wartości

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

Odpowiedzią powinna być Powodzenie 204.

### <a name="view-the-current-value"></a>Wyświetlanie bieżącej wartości

````PowerShell
GET https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy
````

Przykładowa odpowiedź:

````PowerShell
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/authorizationPolicy/$entity",
    "id": "authorizationPolicy",
    "displayName": "Authorization Policy",
    "description": "Used to manage authorization related settings across the company.",
    "enabledPreviewFeatures": [],
    "guestUserRoleId": "10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "permissionGrantPolicyIdsAssignedToDefaultUserRole": [
        "user-default-legacy"
    ]
}
````

## <a name="update-with-powershell-cmdlets"></a>Aktualizacja przy użyciu poleceń cmdlet programu PowerShell

Dzięki tej funkcji dodaliśmy możliwość konfigurowania ograniczonych uprawnień za pomocą poleceń cmdlet programu PowerShell w wersji 2. Polecenia cmdlet get i set programu PowerShell zostały opublikowane w wersji 2.0.2.85.

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>Pobierz polecenie: Get-AzureADMSAuthorizationPolicy

Przykład:

````PowerShell
PS C:\WINDOWS\system32> Get-AzureADMSAuthorizationPolicy

Id                                                : authorizationPolicy
OdataType                                         :
Description                                       : Used to manage authorization related settings across the company.
DisplayName                                       : Authorization Policy
EnabledPreviewFeatures                            : {}
GuestUserRoleId                                   : 10dae51f-b6af-4016-8d66-8c2a99b929b3
PermissionGrantPolicyIdsAssignedToDefaultUserRole : {user-default-legacy}
````

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Ustaw polecenie: Set-AzureADMSAuthorizationPolicy

Przykład:

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> Na żądanie należy wprowadzić wartość authorizationPolicy jako identyfikator.

## <a name="supported-microsoft-365-services"></a>Obsługiwane Microsoft 365 usługi

### <a name="supported-services"></a>Obsługiwane usługi

Obsługiwane przez nas oznacza, że środowisko jest zgodnie z oczekiwaniami; w szczególności, że jest ona taka sama jak bieżące środowisko gościa.

- Teams
- Outlook (OWA)
- SharePoint
- Planista w aplikacji Teams
- Aplikacja internetowa aplikacji Planner

### <a name="services-currently-not-supported"></a>Usługi obecnie nie są obsługiwane

Usługa bez bieżącej pomocy technicznej może mieć problemy ze zgodnością z nowym ustawieniem ograniczeń gościa.

- Formularze
- Aplikacja mobilna Planner
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

Pytanie | Odpowiedź
-------- | ------
Gdzie mają zastosowanie te uprawnienia? | Te uprawnienia na poziomie katalogu są wymuszane w usługach i portalach usługi Azure AD, w tym w Microsoft Graph, powershell v2, Azure Portal i Moje aplikacje portal. Microsoft 365 usługi wykorzystujące Microsoft 365 do scenariuszy współpracy, w szczególności outlook, Microsoft Teams i SharePoint.
Jak ograniczone uprawnienia wpływają na grupy, które mogą zobaczyć goście? | Niezależnie od domyślnych lub ograniczonych uprawnień gościa goście nie mogą wyliczać listy grup ani użytkowników. Goście mogą zobaczyć grupy, do których są członkami zarówno w Azure Portal, jak i w Moje aplikacje w zależności od uprawnień:<li>**Uprawnienia domyślne:** aby znaleźć grupy, których członkowie znajdują się w grupie Azure Portal, gość musi wyszukać swój identyfikator obiektu na liście **Wszyscy** użytkownicy, a następnie wybrać pozycję **Grupy.** W tym miejscu mogą wyświetlić listę grup, których są członkami, w tym wszystkie szczegóły grupy, w tym nazwę, adres e-mail i tak dalej. W Moje aplikacje portalu mogą oni wyświetlić listę grup, których są właścicielami, oraz grupy, których są członkami.</li><li>**Ograniczone uprawnienia gościa:** w Azure Portal nadal mogą znaleźć listę grup, których są członkami, wyszukując identyfikator obiektu na liście Wszyscy użytkownicy, a następnie wybierz pozycję Grupy. Mogą oni zobaczyć tylko bardzo ograniczone szczegóły dotyczące grupy, zwłaszcza identyfikator obiektu. Domyślnie kolumny Nazwa i Adres e-mail są puste, a typ grupy jest nierozpoznany. W portalu Moje aplikacje nie mogą uzyskać dostępu do listy grup, których są właścicielami, lub grup, których są członkami.</li><br>Aby uzyskać bardziej szczegółowe porównanie uprawnień katalogu, które pochodzą z interfejs Graph API, zobacz [Domyślne uprawnienia użytkownika.](../fundamentals/users-default-permissions.md#member-and-guest-users)
Na które części portalu Moje aplikacje wpłynie ta funkcja? | Funkcje grup w portalu Moje aplikacje będą honorować te nowe uprawnienia. Obejmuje to wszystkie ścieżki do wyświetlania listy grup i członkostwa w grupach w Moje aplikacje. Nie wnoszono żadnych zmian w dostępności kafelka grupy. Dostępność kafelka grupy jest nadal kontrolowana przez istniejące ustawienie grupy w Azure Portal.
Czy te uprawnienia zastępują ustawienia gościa programu SharePoint lub aplikacji Microsoft Teams? | Nie. Te istniejące ustawienia nadal kontrolują środowisko i dostęp w tych aplikacjach. Jeśli na przykład widzisz problemy w programie SharePoint, sprawdź ustawienia udostępniania zewnętrznego.
Jakie są znane problemy ze zgodnością w programie Planner i usłudze Yammer? | <li>Jeśli uprawnienia są ustawione na "ograniczone", goście zalogowani do aplikacji mobilnej Planner nie będą mogli uzyskać dostępu do planów ani żadnych zadań.<li>Jeśli uprawnienia są ustawione na "ograniczone", goście zalogowani do usługi Yammer nie będą mogli opuścić grupy.
Czy moje istniejące uprawnienia gościa zostaną zmienione w mojej dzierżawie? | W bieżących ustawieniach nie zostały wprowadzone żadne zmiany. Utrzymujemy zgodność z poprzednimi wersjami z istniejącymi ustawieniami. Ty decydujesz, kiedy chcesz wprowadzić zmiany.
Czy te uprawnienia zostaną ustawione domyślnie? | Nie. Istniejące uprawnienia domyślne pozostają niezmienione. Opcjonalnie możesz ustawić uprawnienia jako bardziej restrykcyjne.
Czy istnieją jakiekolwiek wymagania licencyjne dotyczące tej funkcji? | Nie, ta funkcja nie ma nowych wymagań dotyczących licencjonowania.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat istniejących uprawnień gościa w usłudze Azure AD, zobacz Jakie są domyślne uprawnienia użytkownika w [usłudze Azure Active Directory?](../fundamentals/users-default-permissions.md)
- Aby wyświetlić metody Microsoft Graph API ograniczania dostępu gościa, zobacz authorizationPolicy resource type (Typ [zasobu authorizationPolicy)](/graph/api/resources/authorizationpolicy)
- Aby odwołać cały dostęp użytkownika, zobacz [Odwoływanie dostępu użytkownika w usłudze Azure AD](users-revoke-access.md)
