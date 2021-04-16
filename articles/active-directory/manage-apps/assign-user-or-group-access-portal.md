---
title: Zarządzanie przypisaniem użytkowników dla aplikacji w usłudze Azure Active Directory
description: Dowiedz się, jak przypisywać i nieprzypisać użytkowników i grupy dla aplikacji przy użyciu Azure Active Directory zarządzania tożsamościami.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: iangithinji
ms.reviewer: luleon
ms.openlocfilehash: 097bf55cc7e5372749240c19afb09ba374d437af
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377976"
---
# <a name="manage-user-assignment-for-an-app-in-azure-active-directory"></a>Zarządzanie przypisaniem użytkowników dla aplikacji w usłudze Azure Active Directory

W tym artykule przedstawiono sposób przypisywania użytkowników i grup do aplikacji przedsiębiorstwa w usłudze Azure Active Directory (Azure AD) z poziomu witryny Azure Portal lub przy użyciu programu PowerShell. Po przypisaniu użytkownika do aplikacji aplikacja zostanie wyświetlona w jego [Moje aplikacje](https://myapps.microsoft.com/) w celu uzyskania do niego łatwego dostępu. Jeśli aplikacja uwidacznia role, możesz także przypisać określoną rolę do użytkownika.

Aby uzyskać większą kontrolę, niektóre typy aplikacji dla przedsiębiorstw można skonfigurować tak, aby [wymagały przypisania użytkownika.](#configure-an-application-to-require-user-assignment) 

> [!IMPORTANT]
> Po przypisaniu grupy do aplikacji dostęp będą mieli tylko użytkownicy w tej grupie. Przypisanie nie powoduje kaskady dla grup zagnieżdżonych.

> [!NOTE]
> Przypisanie oparte na grupach wymaga Azure Active Directory — wersja Premium wersji P1 lub P2. Przypisanie oparte na grupach jest obsługiwane tylko w przypadku grup zabezpieczeń. Członkostwo w grupach zagnieżdżonych Microsoft 365 grupy nie są obecnie obsługiwane. Aby uzyskać więcej wymagań dotyczących licencjonowania funkcji omówiony w tym artykule, zobacz stronę [Azure Active Directory cennika.](https://azure.microsoft.com/pricing/details/active-directory) 

## <a name="configure-an-application-to-require-user-assignment"></a>Konfigurowanie aplikacji w celu wymagania przypisania użytkownika

W przypadku następujących typów aplikacji można wymagać przypisania użytkowników do aplikacji przed uzyskaniem do nich dostępu:

- Aplikacje skonfigurowane do federowego logowania jednokrotnego (SSO) z uwierzytelnianiem opartym na saml
- serwer proxy aplikacji aplikacji, które używają Azure Active Directory wstępnego uwierzytelniania
- Aplikacje oparte na platformie aplikacji usługi Azure AD, które używają uwierzytelniania OAuth 2.0/OpenID Connect, gdy użytkownik lub administrator wyraził na to zgodę.

Jeśli wymagane jest przypisanie użytkownika, tylko ci użytkownicy, którzy zostaną jawnie przypisani do aplikacji (za pośrednictwem bezpośredniego przypisania użytkownika lub na podstawie członkostwa w grupie), będą mogli się zalogować. Mogą oni uzyskać dostęp do aplikacji na Moje aplikacje lub za pomocą linku bezpośredniego. 

Jeśli przypisanie nie jest wymagane *,* ponieważ  ta opcja została ustawiona na Nie lub ponieważ aplikacja używa innego trybu logowania jednokrotnego, każdy użytkownik będzie mógł uzyskać dostęp do aplikacji, jeśli ma bezpośredni link do aplikacji lub adres **URL** dostępu użytkowników na stronie **Właściwości** aplikacji. 

To ustawienie nie ma wpływu na to, czy aplikacja jest wyświetlana na Moje aplikacje. Aplikacje są wyświetlane Moje aplikacje panelach dostępu użytkowników po przypisaniu użytkownika lub grupy do aplikacji. Aby uzyskać ogólne informacje, zobacz [Zarządzanie dostępem do aplikacji.](what-is-access-management.md)

Aby wymagać przypisania użytkownika dla aplikacji:
1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta administratora lub jako właściciel aplikacji.
2. Wybierz pozycję **Azure Active Directory**. W menu nawigacji po lewej stronie wybierz pozycję **Aplikacje dla przedsiębiorstw.**
3. Wybierz aplikację z listy. Jeśli nie widzisz aplikacji, zacznij wpisywać jej nazwę w polu wyszukiwania. Możesz też użyć kontrolek filtru, aby wybrać typ, stan lub widoczność aplikacji, a następnie wybrać pozycję **Zastosuj.**
4. W menu nawigacji po lewej stronie wybierz pozycję **Właściwości.**
5. Upewnij się, **że przełącznik Przypisanie użytkownika jest wymagany?** ma ustawioną wartość **Tak.**
   > [!NOTE]
   > Jeśli przełącznik Przypisanie użytkownika jest **wymagany?,** możesz użyć programu PowerShell, aby ustawić właściwość appRoleAssignmentRequired dla jednostki usługi.
6. Wybierz **przycisk Zapisz** w górnej części ekranu.

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-azure-portal"></a>Przypisywanie lub co najmniej przypisywanie użytkowników i grup dla aplikacji przy użyciu Azure Portal
Aby dowiedzieć się, jak przypisać lub co najmniej przypisać użytkownika lub grupę przy użyciu usługi Azure Portal, zobacz serię Szybki start na temat [zarządzania aplikacją.](add-application-portal-assign-users.md)

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-graph-api"></a>Przypisywanie lub co najmniej przypisywanie użytkowników i grup dla aplikacji przy użyciu interfejs Graph API
Za pomocą tego interfejs Graph API przypisać lub co najmniej przypisać użytkowników i grupy dla aplikacji. Aby dowiedzieć się więcej, zobacz [Przypisania ról aplikacji.](/graph/api/resources/approleassignment)

## <a name="assign-users-and-groups-to-an-app-using-powershell"></a>Przypisywanie użytkowników i grup do aplikacji przy użyciu programu PowerShell
1. Otwórz wiersz polecenia Windows PowerShell podwyższonym poziomem uprawnień.
   > [!NOTE]
   > Musisz zainstalować moduł AzureAD (użyj polecenia `Install-Module -Name AzureAD` ). Jeśli zostanie wyświetlony monit o zainstalowanie modułu NuGet lub nowego modułu programu PowerShell Azure Active Directory V2, wpisz Y i naciśnij klawisz ENTER.
2. Uruchom `Connect-AzureAD` i zaloguj się przy użyciu konta użytkownika administratora globalnego.
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
Aby uzyskać więcej informacji na temat przypisywania użytkownika do roli aplikacji, zobacz dokumentację dla [new-AzureADUserAppRoleAssignment](/powershell/module/azuread/new-azureaduserapproleassignment).

Aby przypisać grupę do aplikacji przedsiębiorstwa, należy zastąpić element `Get-AzureADUser` elementem i `Get-AzureADGroup` zastąpić element `New-AzureADUserAppRoleAssignment` elementem `New-AzureADGroupAppRoleAssignment` .

Aby uzyskać więcej informacji na temat przypisywania grupy do roli aplikacji, zobacz dokumentację dla [new-AzureADGroupAppRoleAssignment](/powershell/module/azuread/new-azureadgroupapproleassignment).

### <a name="example"></a>Przykład

W tym przykładzie użytkownik Britta Simon jest przypisywany do aplikacji [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) przy użyciu programu PowerShell.

1. W programie PowerShell przypisz odpowiednie wartości do zmiennych $username, $app_name i $app_role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```
2. W tym przykładzie nie wiemy, jaka jest dokładna nazwa roli aplikacji, którą chcemy przypisać do użytkownika Britta Simon. Uruchom następujące polecenia, aby pobrać użytkownika ($user) i jednostkę usługi ($sp) przy użyciu nazwy UPN użytkownika i nazwy wyświetlanej jednostki usługi.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
3. Uruchom polecenie , `$sp.AppRoles` aby wyświetlić role dostępne dla aplikacji Workplace Analytics. W tym przykładzie chcemy przypisać britta Simon rolę analityka (ograniczony dostęp).
   ![Przedstawia role dostępne dla użytkownika przy użyciu roli analizy miejsca pracy](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)
4. Przypisz nazwę roli do `$app_role_name` zmiennej .

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

## <a name="unassign-users-and-groups-from-an-app-using-powershell"></a>Cokoń przypisanie użytkowników i grup z aplikacji przy użyciu programu PowerShell

1. Otwórz wiersz polecenia Windows PowerShell podwyższonym poziomem uprawnień.
   > [!NOTE]
   > Musisz zainstalować moduł AzureAD (użyj polecenia `Install-Module -Name AzureAD` ). Jeśli zostanie wyświetlony monit o zainstalowanie modułu NuGet lub nowego modułu Azure Active Directory PowerShell w wersji 2, wpisz Y i naciśnij klawisz ENTER.
2. Uruchom `Connect-AzureAD` i zaloguj się przy użyciu konta użytkownika administratora globalnego.
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
- [Planowanie wdrożenia usługi Azure AD Moje aplikacje wdrożenia](my-apps-deployment-plan.md)
- [Zarządzanie dostępem do aplikacji](what-is-access-management.md)
 
## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie wszystkich moich grup](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Usuwanie przypisania użytkownika lub grupy z aplikacji przedsiębiorstwa]()
- [Wyłączanie logowania użytkowników dla aplikacji przedsiębiorstwa](disable-user-sign-in-portal.md)
- [Zmienianie nazwy lub logo aplikacji przedsiębiorstwa](./add-application-portal-configure.md)
