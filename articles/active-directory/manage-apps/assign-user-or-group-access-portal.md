---
title: Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw w usłudze Azure AD
description: Jak wybrać aplikację dla przedsiębiorstw, aby przypisać do niej użytkownika lub grupę w Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: kenwith
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eeab9cbb68567b6af3b1381b8ec5e67e82461ec
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763741"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw w usłudze Azure Active Directory

W tym artykule opisano sposób przypisywania użytkowników lub grup do aplikacji dla przedsiębiorstw w usłudze Azure Active Directory (Azure AD), z poziomu Azure Portal lub przy użyciu programu PowerShell. Po przypisaniu użytkownika do aplikacji, aplikacja pojawia się w [panelu dostępu moje aplikacje](https://myapps.microsoft.com/) użytkownika w celu ułatwienia dostępu. Jeśli aplikacja uwidacznia role, można także przypisać konkretną rolę do użytkownika.

Aby uzyskać większą kontrolę, niektóre typy aplikacji korporacyjnych można skonfigurować tak, aby [wymagały przypisania użytkownika](#configure-an-application-to-require-user-assignment). 

Aby [przypisać użytkownika lub grupę do aplikacji dla przedsiębiorstw](#assign-users-or-groups-to-an-app-via-the-azure-portal), należy zalogować się jako Administrator globalny, administrator aplikacji, administrator aplikacji w chmurze lub przypisany właściciel aplikacji przedsiębiorstwa.

> [!NOTE]
> Przypisanie oparte na grupach wymaga Azure Active Directory — wersja Premium wersji P1 lub P2. Przypisanie oparte na grupach jest obsługiwane tylko dla grup zabezpieczeń. Członkostwa w grupach zagnieżdżonych i grupy Office 365 nie są obecnie obsługiwane. Aby uzyskać więcej informacji o wymaganiach dotyczących licencjonowania funkcji omówionych w tym artykule, zobacz [stronę z cennikiem Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory). 

## <a name="configure-an-application-to-require-user-assignment"></a>Konfigurowanie aplikacji w celu wymagania przypisania użytkownika

Korzystając z następujących typów aplikacji, można wymagać, aby użytkownicy przypisani do aplikacji mogli uzyskać do niej dostęp:

- Aplikacje skonfigurowane do federacyjnego logowania jednokrotnego (SSO) przy użyciu uwierzytelniania opartego na protokole SAML
- Aplikacje serwera proxy aplikacji korzystające z Azure Active Directory wstępnego uwierzytelniania
- Aplikacje utworzone na platformie aplikacji usługi Azure AD, która korzysta z uwierzytelniania OAuth 2,0/OpenID Connect, gdy użytkownik lub administrator wyraził zgodę na tę aplikację.

Gdy przypisanie użytkownika jest wymagane, tylko użytkownicy jawnie przypisani do aplikacji (za pośrednictwem bezpośredniego przypisania użytkownika lub w oparciu o członkostwo w grupach) będą mogli się zalogować. Użytkownicy mogą uzyskiwać dostęp do aplikacji na stronie Moje aplikacje lub za pomocą linku bezpośredniego. 

Jeśli przypisanie *nie jest wymagane*, ponieważ ta opcja została ustawiona na wartość **nie** lub ponieważ aplikacja korzysta z innego trybu logowania jednokrotnego, każdy użytkownik będzie mógł uzyskać dostęp do aplikacji, jeśli ma bezpośredni link do aplikacji lub **adres URL dostępu użytkownika** na stronie **Właściwości** aplikacji. 

To ustawienie nie ma wpływu na to, czy aplikacja jest wyświetlana w panelu dostępu moje aplikacje. Aplikacje są wyświetlane w panelach dostępu moje aplikacje użytkowników po przypisaniu użytkownika lub grupy do aplikacji. W przypadku programu w tle zobacz [Zarządzanie dostępem do aplikacji](what-is-access-management.md).


Aby wymagać przypisania użytkownika do aplikacji:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta administratora lub jako właściciela aplikacji.

2. Wybierz pozycję **Azure Active Directory**. W menu nawigacji po lewej stronie wybierz pozycję **aplikacje dla przedsiębiorstw**.

3. Wybierz aplikację z listy. Jeśli nie widzisz aplikacji, zacznij pisać jej nazwę w polu wyszukiwania. Lub Użyj kontrolek filtr, aby wybrać typ aplikacji, stan lub widoczność, a następnie wybierz pozycję **Zastosuj**.

4. W menu nawigacji po lewej stronie wybierz pozycję **Właściwości**.

5. Upewnij się, że **przypisanie użytkownika jest wymagane?** przełącznik jest ustawiony na **wartość tak**.

   > [!NOTE]
   > Jeśli **wymagane jest przypisanie użytkownika?** przełącznik nie jest dostępny, można użyć programu PowerShell, aby ustawić właściwość appRoleAssignmentRequired w jednostce usługi.

6. Wybierz przycisk **Zapisz** w górnej części ekranu.

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>Przypisywanie użytkowników lub grup do aplikacji za pośrednictwem Azure Portal

1. Zaloguj się do [Azure Portal](https://portal.azure.com) za pomocą konta administratora globalnego, administratora aplikacji lub administratora aplikacji w chmurze lub jako przypisanego właściciela aplikacji przedsiębiorstwa.
2. Wybierz pozycję **Azure Active Directory**. W menu nawigacji po lewej stronie wybierz pozycję **aplikacje dla przedsiębiorstw**.
3. Wybierz aplikację z listy. Jeśli nie widzisz aplikacji, zacznij pisać jej nazwę w polu wyszukiwania. Lub Użyj kontrolek filtr, aby wybrać typ aplikacji, stan lub widoczność, a następnie wybierz pozycję **Zastosuj**.
4. W menu nawigacji po lewej stronie wybierz pozycję **Użytkownicy i grupy**.
   > [!NOTE]
   > Jeśli chcesz przypisać użytkowników do aplikacji firmy Microsoft, takich jak aplikacje pakietu Office 365, niektóre z tych aplikacji używają programu PowerShell. 
5. Wybierz przycisk **Dodaj użytkownika** .
6. W okienku **Dodaj przypisanie** wybierz pozycję **Użytkownicy i grupy**.
7. Wybierz użytkownika lub grupę, którą chcesz przypisać do aplikacji, lub zacznij wpisywać nazwę użytkownika lub grupy w polu wyszukiwania. Możesz wybrać wielu użytkowników i grupy, a wybrane opcje będą widoczne w obszarze **wybrane elementy**.
8. Po zakończeniu kliknij przycisk **Wybierz**.

   ![Przypisywanie użytkownika lub grupy do aplikacji](./media/assign-user-or-group-access-portal/assign-users.png)

9. W okienku **Użytkownicy i grupy** wybierz co najmniej jednego użytkownika lub grupę z listy, a następnie wybierz przycisk **Wybierz** w dolnej części okienka.
10. Jeśli aplikacja obsługuje tę funkcję, można przypisać rolę do użytkownika lub grupy. W okienku **Dodaj przypisanie** wybierz **pozycję Wybierz rolę**. Następnie w okienku **Wybierz rolę** wybierz rolę, która ma zostać zastosowana do wybranych użytkowników lub grup, a następnie wybierz pozycję **OK** w dolnej części okienka. 

    > [!NOTE]
    > Jeśli aplikacja nie obsługuje wyboru roli, zostanie przypisana domyślna rola dostępu. W takim przypadku aplikacja zarządza poziomem dostępu dostępnym dla użytkowników.

2. W okienku **Dodaj przypisanie** wybierz przycisk **Przypisz** w dolnej części okienka.

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>Przypisywanie użytkowników lub grup do aplikacji za pomocą programu PowerShell

1. Otwórz wiersz polecenia programu Windows PowerShell z podwyższonym poziomem uprawnień.

   > [!NOTE]
   > Należy zainstalować moduł AzureAD (Użyj polecenia `Install-Module -Name AzureAD` ). Jeśli zostanie wyświetlony monit o zainstalowanie modułu NuGet lub nowego modułu Azure Active Directory v2 PowerShell, wpisz Y i naciśnij klawisz ENTER.

1. Uruchom `Connect-AzureAD` i zaloguj się przy użyciu konta administratora globalnego.
1. Użyj następującego skryptu, aby przypisać użytkownika i rolę do aplikacji:

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

Więcej informacji o sposobie przypisywania użytkownika do roli aplikacji znajduje się w dokumentacji dotyczącej usługi [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0).

Aby przypisać grupę do aplikacji dla przedsiębiorstw, należy zamienić element `Get-AzureADUser` with `Get-AzureADGroup` i zastąpić `New-AzureADUserAppRoleAssignment` go `New-AzureADGroupAppRoleAssignment` .

Więcej informacji o sposobie przypisywania grupy do roli aplikacji znajduje się w dokumentacji dotyczącej usługi [New-AzureADGroupAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureadgroupapproleassignment?view=azureadps-2.0).

### <a name="example"></a>Przykład

Ten przykład przypisuje użytkownikowi Britta Simon do aplikacji [analizy w miejscu pracy firmy Microsoft](https://products.office.com/business/workplace-analytics) przy użyciu programu PowerShell.

1. W programie PowerShell Przypisz odpowiednie wartości do zmiennych $username, $app _name i $app _role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. W tym przykładzie nie wiemy, co to jest dokładna nazwa roli aplikacji, którą chcemy przypisać do Britta Simon. Uruchom następujące polecenia, aby uzyskać użytkownika ($user) i nazwę główną usługi ($sp) przy użyciu nazwy UPN użytkownika i nazw wyświetlanych jednostki usługi.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. Uruchom polecenie, `$sp.AppRoles` Aby wyświetlić role dostępne dla aplikacji do analizy w miejscu pracy. W tym przykładzie chcemy przypisać rolę analityka (ograniczony dostęp) Britta Simon.

   ![Pokazuje role dostępne dla użytkownika przy użyciu roli analiza w miejscu pracy](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Przypisz nazwę roli do `$app_role_name` zmiennej.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. Uruchom następujące polecenie, aby przypisać użytkownika do roli aplikacji:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="related-articles"></a>Pokrewne artykuły:

- [Dowiedz się więcej o dostępie użytkowników końcowych do aplikacji](end-user-experiences.md)
- [Planowanie wdrożenia panelu dostępu usługi Azure AD](access-panel-deployment-plan.md)
- [Zarządzanie dostępem do aplikacji](what-is-access-management.md)
 
## <a name="next-steps"></a>Następne kroki

- [Zobacz wszystkie moje grupy](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Usuwanie przypisania użytkownika lub grupy z aplikacji dla przedsiębiorstw](remove-user-or-group-access-portal.md)
- [Wyłączanie logowania użytkownika dla aplikacji dla przedsiębiorstw](disable-user-sign-in-portal.md)
- [Zmiana nazwy lub logo aplikacji dla przedsiębiorstw](change-name-or-logo-portal.md)
