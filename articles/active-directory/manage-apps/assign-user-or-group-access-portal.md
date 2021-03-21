---
title: Zarządzanie przypisaniem użytkowników dla aplikacji w usłudze Azure Active Directory
description: Informacje o przypisywaniu i cofaniu przypisywania użytkowników i grup w aplikacji przy użyciu Azure Active Directory do zarządzania tożsamościami.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: kenwith
ms.reviewer: luleon
ms.openlocfilehash: 50dcde478b708cd53d8229d70a9ddf4b1ff271be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259738"
---
# <a name="manage-user-assignment-for-an-app-in-azure-active-directory"></a>Zarządzanie przypisaniem użytkowników dla aplikacji w usłudze Azure Active Directory

W tym artykule opisano sposób przypisywania użytkowników i grup do aplikacji dla przedsiębiorstw w usłudze Azure Active Directory (Azure AD), z poziomu Azure Portal lub przy użyciu programu PowerShell. Po przypisaniu użytkownika do aplikacji, aplikacja pojawia się w [moich aplikacjach](https://myapps.microsoft.com/) użytkownika w celu ułatwienia dostępu. Jeśli aplikacja uwidacznia role, można także przypisać konkretną rolę do użytkownika.

Aby uzyskać większą kontrolę, niektóre typy aplikacji korporacyjnych można skonfigurować tak, aby [wymagały przypisania użytkownika](#configure-an-application-to-require-user-assignment). 

> [!IMPORTANT]
> Po przypisaniu grupy do aplikacji tylko użytkownicy w grupie będą mieli dostęp. Przypisanie nie jest kaskadowe do grup zagnieżdżonych.

> [!NOTE]
> Przypisanie oparte na grupach wymaga Azure Active Directory — wersja Premium wersji P1 lub P2. Przypisanie oparte na grupach jest obsługiwane tylko dla grup zabezpieczeń. Członkostwa w grupach zagnieżdżonych i grup Microsoft 365 nie są obecnie obsługiwane. Aby uzyskać więcej informacji o wymaganiach dotyczących licencjonowania funkcji omówionych w tym artykule, zobacz [stronę z cennikiem Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory). 

## <a name="configure-an-application-to-require-user-assignment"></a>Konfigurowanie aplikacji w celu wymagania przypisania użytkownika

Korzystając z następujących typów aplikacji, można wymagać, aby użytkownicy przypisani do aplikacji mogli uzyskać do niej dostęp:

- Aplikacje skonfigurowane do federacyjnego logowania jednokrotnego (SSO) przy użyciu uwierzytelniania opartego na protokole SAML
- Aplikacje serwera proxy aplikacji korzystające z Azure Active Directory wstępnego uwierzytelniania
- Aplikacje utworzone na platformie aplikacji usługi Azure AD, która korzysta z uwierzytelniania OAuth 2,0/OpenID Connect, gdy użytkownik lub administrator wyraził zgodę na tę aplikację.

Gdy przypisanie użytkownika jest wymagane, tylko użytkownicy jawnie przypisani do aplikacji (za pośrednictwem bezpośredniego przypisania użytkownika lub w oparciu o członkostwo w grupach) będą mogli się zalogować. Użytkownicy mogą uzyskiwać dostęp do aplikacji na stronie Moje aplikacje lub za pomocą linku bezpośredniego. 

Jeśli przypisanie *nie jest wymagane*, ponieważ ta opcja została ustawiona na wartość **nie** lub ponieważ aplikacja korzysta z innego trybu logowania jednokrotnego, każdy użytkownik będzie mógł uzyskać dostęp do aplikacji, jeśli ma bezpośredni link do aplikacji lub **adres URL dostępu użytkownika** na stronie **Właściwości** aplikacji. 

To ustawienie nie ma wpływu na to, czy aplikacja jest wyświetlana w aplikacjach. Aplikacje są wyświetlane w panelach dostępu moje aplikacje użytkowników po przypisaniu użytkownika lub grupy do aplikacji. W przypadku programu w tle zobacz [Zarządzanie dostępem do aplikacji](what-is-access-management.md).

Aby wymagać przypisania użytkownika do aplikacji:
1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta administratora lub jako właściciela aplikacji.
2. Wybierz pozycję **Azure Active Directory**. W menu nawigacji po lewej stronie wybierz pozycję **aplikacje dla przedsiębiorstw**.
3. Wybierz aplikację z listy. Jeśli nie widzisz aplikacji, zacznij pisać jej nazwę w polu wyszukiwania. Lub Użyj kontrolek filtr, aby wybrać typ aplikacji, stan lub widoczność, a następnie wybierz pozycję **Zastosuj**.
4. W menu nawigacji po lewej stronie wybierz pozycję **Właściwości**.
5. Upewnij się, że **przypisanie użytkownika jest wymagane?** przełącznik jest ustawiony na **wartość tak**.
   > [!NOTE]
   > Jeśli **wymagane jest przypisanie użytkownika?** przełącznik nie jest dostępny, można użyć programu PowerShell, aby ustawić właściwość appRoleAssignmentRequired w jednostce usługi.
6. Wybierz przycisk **Zapisz** w górnej części ekranu.

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-azure-portal"></a>Przypisywanie i cofanie przypisania użytkowników i grup w przypadku aplikacji przy użyciu Azure Portal
Aby dowiedzieć się, jak przypisywać lub cofać przypisanie, użytkownika lub grupę przy użyciu Azure Portal, zobacz [serie szybkiego startu w zarządzaniu aplikacjami](add-application-portal-assign-users.md).

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-graph-api"></a>Przypisywanie i cofanie przypisania użytkowników i grup w przypadku aplikacji przy użyciu interfejs API programu Graph
Za pomocą interfejs API programu Graph można przypisywać i cofać przypisanie użytkowników oraz grup dla aplikacji. Aby dowiedzieć się więcej, zobacz [przypisania ról aplikacji](/graph/api/resources/approleassignment).

## <a name="assign-users-and-groups-to-an-app-using-powershell"></a>Przypisywanie użytkowników i grup do aplikacji przy użyciu programu PowerShell
1. Otwórz wiersz polecenia programu Windows PowerShell z podwyższonym poziomem uprawnień.
   > [!NOTE]
   > Należy zainstalować moduł AzureAD (Użyj polecenia `Install-Module -Name AzureAD` ). Jeśli zostanie wyświetlony monit o zainstalowanie modułu NuGet lub nowego modułu Azure Active Directory v2 PowerShell, wpisz Y i naciśnij klawisz ENTER.
2. Uruchom `Connect-AzureAD` i zaloguj się przy użyciu konta administratora globalnego.
3. Użyj następującego skryptu, aby przypisać użytkownika i rolę do aplikacji:

    ```powershell
    # Assign the values to the variables
    $username = "<Your user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```
Więcej informacji o sposobie przypisywania użytkownika do roli aplikacji znajduje się w dokumentacji dotyczącej usługi [New-AzureADUserAppRoleAssignment](/powershell/module/azuread/new-azureaduserapproleassignment).

Aby przypisać grupę do aplikacji dla przedsiębiorstw, należy zamienić element `Get-AzureADUser` with `Get-AzureADGroup` i zastąpić `New-AzureADUserAppRoleAssignment` go `New-AzureADGroupAppRoleAssignment` .

Więcej informacji o sposobie przypisywania grupy do roli aplikacji znajduje się w dokumentacji dotyczącej usługi [New-AzureADGroupAppRoleAssignment](/powershell/module/azuread/new-azureadgroupapproleassignment).

### <a name="example"></a>Przykład

Ten przykład przypisuje użytkownikowi Britta Simon do aplikacji [analizy w miejscu pracy firmy Microsoft](https://products.office.com/business/workplace-analytics) przy użyciu programu PowerShell.

1. W programie PowerShell Przypisz odpowiednie wartości do zmiennych $username, $app _name i $app _role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```
2. W tym przykładzie nie wiemy, co to jest dokładna nazwa roli aplikacji, którą chcemy przypisać do Britta Simon. Uruchom następujące polecenia, aby uzyskać użytkownika ($user) i nazwę główną usługi ($sp) przy użyciu nazwy UPN użytkownika i nazw wyświetlanych jednostki usługi.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
3. Uruchom polecenie, `$sp.AppRoles` Aby wyświetlić role dostępne dla aplikacji do analizy w miejscu pracy. W tym przykładzie chcemy przypisać rolę analityka (ograniczony dostęp) Britta Simon.
   ![Pokazuje role dostępne dla użytkownika przy użyciu roli analiza w miejscu pracy](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)
4. Przypisz nazwę roli do `$app_role_name` zmiennej.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```
5. Uruchom następujące polecenie, aby przypisać użytkownika do roli aplikacji:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="unassign-users-and-groups-from-an-app-using-powershell"></a>Cofanie przypisania użytkowników i grup z aplikacji przy użyciu programu PowerShell

1. Otwórz wiersz polecenia programu Windows PowerShell z podwyższonym poziomem uprawnień.
   > [!NOTE]
   > Należy zainstalować moduł AzureAD (Użyj polecenia `Install-Module -Name AzureAD` ). Jeśli zostanie wyświetlony monit o zainstalowanie modułu NuGet lub nowego modułu Azure Active Directory v2 PowerShell, wpisz Y i naciśnij klawisz ENTER.
2. Uruchom `Connect-AzureAD` i zaloguj się przy użyciu konta administratora globalnego.
3. Użyj następującego skryptu, aby usunąć użytkownika i rolę z aplikacji:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ```


## <a name="related-articles"></a>Pokrewne artykuły:

- [Dowiedz się więcej o dostępie użytkowników końcowych do aplikacji](end-user-experiences.md)
- [Planowanie wdrożenia usługi Azure AD my Apps](my-apps-deployment-plan.md)
- [Zarządzanie dostępem do aplikacji](what-is-access-management.md)
 
## <a name="next-steps"></a>Następne kroki

- [Zobacz wszystkie moje grupy](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Usuwanie przypisania użytkownika lub grupy z aplikacji dla przedsiębiorstw]()
- [Wyłączanie logowania użytkownika dla aplikacji dla przedsiębiorstw](disable-user-sign-in-portal.md)
- [Zmiana nazwy lub logo aplikacji dla przedsiębiorstw](./add-application-portal-configure.md)
