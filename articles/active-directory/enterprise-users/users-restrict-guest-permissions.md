---
title: Ograniczanie uprawnień dostępu gościa-Azure Active Directory | Microsoft Docs
description: Ogranicz uprawnienia dostępu gościa za pomocą Azure Portal, programu PowerShell lub Microsoft Graph w Azure Active Directory
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
ms.openlocfilehash: bf2d0d3335468147575eb53a99940866baa18375
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222525"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>Ogranicz uprawnienia dostępu gościa (wersja zapoznawcza) w Azure Active Directory

Usługa Azure Active Directory (Azure AD) umożliwia ograniczenie możliwości wyświetlania użytkowników zewnętrznych w organizacji w usłudze Azure AD. Użytkownicy-Goście są domyślnie ustawiani na ograniczony poziom uprawnień w usłudze Azure AD, podczas gdy domyślnie dla użytkowników należących do członków jest pełny zestaw domyślnych uprawnień użytkownika. Jest to wersja zapoznawcza nowego poziomu uprawnień użytkownika-gościa w ustawieniach współpracy zewnętrznej w organizacji usługi Azure AD w celu uzyskania jeszcze ograniczonego dostępu, dzięki czemu dostępne są teraz:

Poziom uprawnień         | Poziom dostępu
----------------         | ------------
Taki sam jak użytkownicy będący członkami     | Goście mają taki sam dostęp do zasobów usługi Azure AD jako użytkownicy będący członkami
Ograniczony dostęp (wartość domyślna) | Goście mogą zobaczyć członkostwo wszystkich nieukrytych grup
**Ograniczony dostęp (NOWOŚĆ)**  | **Goście nie mogą zobaczyć członkostwa w żadnej grupie**

Jeśli dostęp gościa jest ograniczony, Goście mogą wyświetlać tylko własny profil użytkownika. Uprawnienie do wyświetlania innych użytkowników nie jest dozwolone, nawet jeśli gość przeszukuje według głównej nazwy użytkownika lub identyfikatora obiektu objectId. Ograniczony dostęp uniemożliwia użytkownikom-Gościom Wyświetlanie członkostwa w grupach, do których się znajdują. Aby uzyskać więcej informacji o ogólnych uprawnieniach użytkownika, w tym uprawnienia użytkownika-gościa, zobacz [co to są domyślne uprawnienia użytkownika w Azure Active Directory?](../fundamentals/users-default-permissions.md).

## <a name="permissions-and-licenses"></a>Uprawnienia i licencje

Aby skonfigurować ustawienia współpracy zewnętrznej, musisz mieć rolę administratora globalnego. Nie ma dodatkowych wymagań dotyczących licencjonowania, aby ograniczyć dostęp gościa.

## <a name="update-in-the-azure-portal"></a>Aktualizacja w Azure Portal

Wprowadziliśmy zmiany w istniejących kontrolkach Azure Portal dla uprawnień użytkowników-Gości.

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu uprawnień administratora globalnego.
1. Na stronie Przegląd **Azure Active Directory** organizacji wybierz pozycję **Ustawienia użytkownika**.
1. W obszarze **użytkownicy zewnętrzni** wybierz pozycję **Zarządzaj ustawieniami współpracy zewnętrznej**.
1. Na stronie **Ustawienia współpracy zewnętrznej** wybierz opcję **dostęp użytkownika-Gość jest ograniczony do właściwości i członkostw własnych opcji obiektów katalogu** .

    ![Strona ustawień współpracy zewnętrznej usługi Azure AD](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. Wybierz pozycję **Zapisz**. Wprowadzenie zmian może potrwać do 15 minut dla użytkowników-Gości.

## <a name="update-with-the-microsoft-graph-api"></a>Aktualizowanie za pomocą interfejsu API Microsoft Graph

Dodaliśmy nowy interfejs API Microsoft Graph, aby skonfigurować uprawnienia gościa w organizacji usługi Azure AD. Następujące wywołania interfejsu API mogą zostać wykonane w celu przypisania dowolnego poziomu uprawnień. Wartość guestUserRoleId używana tutaj to zilustrowanie najbardziej ograniczonego ustawienia użytkownika gościa. Aby uzyskać więcej informacji na temat używania Microsoft Graph do ustawiania uprawnień gościa, zobacz [authorizationPolicy typu zasobu](/graph/api/resources/authorizationpolicy).

### <a name="configuring-for-the-first-time"></a>Konfigurowanie po raz pierwszy

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

Odpowiedź powinna być pomyślna 204.

### <a name="updating-the-existing-value"></a>Aktualizowanie istniejącej wartości

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

Odpowiedź powinna być pomyślna 204.

### <a name="view-the-current-value"></a>Wyświetl bieżącą wartość

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

## <a name="update-with-powershell-cmdlets"></a>Aktualizowanie za pomocą poleceń cmdlet programu PowerShell

Dzięki tej funkcji dodaliśmy możliwość skonfigurowania ograniczonych uprawnień za pośrednictwem poleceń cmdlet programu PowerShell w wersji 2. Polecenia cmdlet programu PowerShell Get i Set zostały opublikowane w wersji 2.0.2.85.

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

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Set — polecenie: Set-AzureADMSAuthorizationPolicy

Przykład:

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> W razie żądania należy wprowadzić authorizationPolicy jako identyfikator.

## <a name="supported-microsoft-365-services"></a>Obsługiwane Microsoft 365 Services

### <a name="supported-services"></a>Obsługiwane usługi

Obsługiwane przez nas oznacza, że środowisko jest zgodnie z oczekiwaniami; jest to takie samo, jak w przypadku bieżącego środowiska gościa.

- Teams
- Outlook (OWA)
- SharePoint
- Planista w zespołach
- Aplikacja sieci Web planisty

### <a name="services-currently-not-supported"></a>Usługi, które nie są obecnie obsługiwane

Usługa bez bieżącej obsługi może mieć problemy ze zgodnością z nowym ustawieniem ograniczenia gościa.

- Formularze
- Aplikacja mobilna planisty
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

Pytanie | Odpowiedź
-------- | ------
Gdzie są naliczane te uprawnienia? | Te uprawnienia na poziomie katalogu są wymuszane w ramach usług Azure AD i portali, w tym Microsoft Graph, PowerShell V2, Azure Portal i my Apps. Na Microsoft 365 usług korzystających z Microsoft 365 grup na potrzeby scenariuszy współpracy dotyczy to również programów Outlook, Microsoft Teams i SharePoint.
Jak ograniczone uprawnienia mają wpływ na to, które grupy mogą zobaczyć Goście? | Niezależnie od domyślnego lub ograniczonego uprawnienia gościa Goście nie mogą wyliczyć listy grup lub użytkowników. Goście mogą widzieć grupy, do których należą zarówno w Azure Portal, jak i w portalu Moje aplikacje, w zależności od uprawnień:<li>**Uprawnienia domyślne**: aby znaleźć grupy, do których należą, w Azure Portal, gość musi wyszukać swój identyfikator obiektu na liście **Wszyscy użytkownicy** , a następnie wybrać pozycję **grupy**. W tym miejscu można zobaczyć listę grup, do których należą, w tym wszystkie szczegóły grupy, takie jak imię i nazwisko, adres e-mail itd. W portalu My Apps można zobaczyć listę grup, których są członkami, oraz grup, do których należą.</li><li>**Ograniczone uprawnienia gościa**: w Azure Portal nadal mogą znaleźć listę grup, do których należą, wyszukując identyfikator obiektu na liście wszyscy użytkownicy, a następnie wybierając pozycję grupy. Mogą widzieć tylko bardzo ograniczone szczegóły dotyczące grupy, w szczególności identyfikator obiektu. Według projektu nazwa i kolumny wiadomości E-mail są puste i typ grupy nie został rozpoznany. W portalu Moje aplikacje nie są w stanie uzyskać dostępu do listy grup, których są członkami, lub grup, do których należą.</li><br>Aby uzyskać bardziej szczegółowe porównanie uprawnień katalogu, które pochodzą z interfejs API programu Graph, zobacz [domyślne uprawnienia użytkownika](../fundamentals/users-default-permissions.md#member-and-guest-users).
Które części portalu Moje aplikacje mają wpływ na tę funkcję? | Funkcje grup w portalu My Apps będą honorować te nowe uprawnienia. Obejmuje to wszystkie ścieżki umożliwiające wyświetlenie listy grup i członkostwa w grupach w moich aplikacjach. Nie wprowadzono żadnych zmian w dostępności kafelka grupy. Dostępność kafelka grupy jest nadal kontrolowana przez istniejące ustawienie grupy w Azure Portal.
Czy te uprawnienia zastępują ustawienia gościa programu SharePoint lub programu Microsoft Teams? | Nie. Te istniejące ustawienia nadal kontrolują środowisko i dostęp do tych aplikacji. Jeśli na przykład widzisz problemy w programie SharePoint, sprawdź ustawienia udostępniania zewnętrznego.
Jakie są znane problemy ze zgodnością w programie Planner i Yammer? | <li>Z uprawnieniami ustawionymi na wartość "Restricted" Goście zalogowani do aplikacji mobilnej planisty nie będą mogli uzyskać dostępu do planów ani żadnych zadań.<li>Gdy uprawnienia mają ustawioną wartość "ograniczone", Goście zalogowani do usługi Yammer nie będą mogli opuścić grupy.
Czy moje istniejące uprawnienia gościa zostaną zmienione w mojej dzierżawie? | W bieżących ustawieniach nie wprowadzono żadnych zmian. Zachowamy zgodność z poprzednimi wersjami z istniejącymi ustawieniami. Użytkownik zdecyduje się na wprowadzenie zmian.
Czy te uprawnienia będą domyślnie ustawione? | Nie. Istniejące domyślne uprawnienia pozostają bez zmian. Opcjonalnie można ustawić uprawnienia bardziej restrykcyjne.
Czy istnieją jakieś wymagania licencyjne dotyczące tej funkcji? | Nie. nie ma żadnych nowych wymagań dotyczących licencjonowania w przypadku tej funkcji.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o istniejących uprawnieniach gościa w usłudze Azure AD, zobacz [co to są domyślne uprawnienia użytkownika w Azure Active Directory?](../fundamentals/users-default-permissions.md)
- Aby wyświetlić Microsoft Graph metod interfejsu API ograniczenia dostępu gościa, zobacz [authorizationPolicy typu zasobu](/graph/api/resources/authorizationpolicy)
- Aby odwołać dostęp dla użytkownika, zobacz [odwoływanie dostępu użytkowników w usłudze Azure AD](users-revoke-access.md)