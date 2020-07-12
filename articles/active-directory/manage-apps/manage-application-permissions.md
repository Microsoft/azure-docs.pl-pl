---
title: Zarządzanie uprawnieniami użytkowników i administratorów — Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak przeglądać uprawnienia dla aplikacji w usłudze Azure AD i zarządzać nimi. Na przykład, jeśli chcesz odwołać wszystkie uprawnienia udzielone aplikacji.
services: active-directory
author: mimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00d878c7b2f78d037e89235f3bb30c02fd11a7ae
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277648"
---
# <a name="take-action-on-overpriviledged-or-suspicious-application-in-azure-active-directory"></a>Podejmowanie działań na overpriviledged lub podejrzanych aplikacjach w Azure Active Directory

Dowiedz się, jak przeglądać uprawnienia aplikacji i zarządzać nimi. W zależności od scenariusza ten artykuł zawiera różne akcje, które można wykonać w celu zabezpieczenia aplikacji. Dotyczy to wszystkich aplikacji, które zostały dodane do dzierżawy usługi Azure Active Directory (Azure AD) za pośrednictwem zgody użytkownika lub administratora.

Aby uzyskać więcej informacji na temat wyrażania zgody na aplikacje, zobacz [Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Wymagania wstępne

Możliwość wykonania poniższych czynności wymaga zalogowania się jako Administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.

Aby ograniczyć dostęp do aplikacji, należy wymagać przypisania użytkownika, a następnie przypisania użytkowników lub grup do aplikacji.  Aby uzyskać więcej informacji, zobacz [metody przypisywania użytkowników i grup](methods-for-assigning-users-and-groups.md).

Możesz uzyskać dostęp do portalu usługi Azure AD, aby uzyskać kontekstowe skrypty programu PowerShell do wykonywania akcji.
 
1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.
2. Wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**.
3. Wybierz aplikację, do której chcesz ograniczyć dostęp.
4. Wybierz pozycję **uprawnienia**. Na pasku poleceń wybierz pozycję **Przejrzyj uprawnienia**.

![Przejrzyj uprawnienia](./media/manage-application-permissions/review-permissions.png)

## <a name="i-want-to-control-access-to-an-application"></a>Chcę kontrolować dostęp do aplikacji

Zalecamy ograniczenie dostępu do tej aplikacji przez włączenie ustawienia przypisywania użytkowników.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.
2. Wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**.
3. Wybierz aplikację, do której chcesz ograniczyć dostęp.
4. Wybierz pozycję **Właściwości** , a następnie ustaw dla ustawienia wymagane dla użytkownika wartość tak.
5. Wybierz pozycję **Użytkownicy i grupy** , a następnie usuń niechcianych użytkowników przypisanych do aplikacji.
6. Przypisz użytkowników lub grupy do aplikacji.

Opcjonalnie można usunąć wszystkich użytkowników przypisanych do aplikacji przy użyciu programu PowerShell.

## <a name="i-want-to-revoke-all-permissions-for-an-application"></a>Chcę odwołać wszystkie uprawnienia dla aplikacji

Przy użyciu programu PowerShell są odwoływane wszystkie uprawnienia udzielone tej aplikacji.

> [!NOTE]
> Odwoływanie bieżącego uprawnienia nie uniemożliwi użytkownikom reconseing aplikacji. Jeśli chcesz zablokować użytkownikom możliwość wyrażania zgody na aplikację, Przeczytaj [temat Konfigurowanie sposobu, w jaki użytkownicy końcowi wyrażają zgodę na aplikacje](configure-user-consent.md).

Opcjonalnie można wyłączyć aplikację, aby uniemożliwić użytkownikom dostęp do aplikacji, a aplikacja uzyskuje dostęp do danych.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.
2. Wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**.
3. Wybierz aplikację, do której chcesz ograniczyć dostęp.
4. Wybierz pozycję **Właściwości** , a następnie ustaw opcję Włącz, aby użytkownicy mogli się zalogować? na nie.

## <a name="application-is-suspicious-and-i-want-to-investigate"></a>Aplikacja jest podejrzana i chcę zbadać

Zalecamy ograniczenie dostępu do tej aplikacji przez włączenie ustawienia przypisywania użytkowników i przejrzenie uprawnień przypisanych do aplikacji przez użytkowników i administratorów.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.
3. Wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**.
5. Wybierz aplikację, do której chcesz ograniczyć dostęp.
6. Wybierz pozycję **Właściwości** , a następnie ustaw dla ustawienia wymagane dla użytkownika wartość tak.
7. Wybierz pozycję **uprawnienia** i przejrzyj uprawnienia wysłane przez administratora i użytkownika.

Opcjonalne, można:

- Za pomocą programu PowerShell Usuń wszystkich użytkowników przypisanych, aby uniemożliwić im zalogowanie się do aplikacji.
- Za pomocą programu PowerShell Unieważnij tokeny odświeżania dla użytkowników, którzy mają dostęp do aplikacji.
- Przy użyciu programu PowerShell Odwołaj wszystkie uprawnienia dla tej aplikacji
- Wyłącz aplikację, aby blokować użytkownikom dostęp do danych i je zatrzymywać.


## <a name="application-is-malicious-and-im-compromised"></a>Aplikacja jest złośliwa i naruszona

Zalecamy wyłączenie aplikacji w celu zablokowania użytkownikom dostępu do aplikacji i uzyskiwania przez nich dostępu do danych. Jeśli usuniesz aplikację zamiast tego, użytkownicy końcowi będą mogli wyrazić zgodę na aplikację i udzielić dostępu do danych.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.
2. Wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**.
3. Wybierz aplikację, do której chcesz ograniczyć dostęp.
4. Wybierz pozycję **Właściwości** , a następnie skopiuj identyfikator obiektu.

### <a name="powershell-commands"></a>Polecenia programu PowerShell


Pobierz identyfikator obiektu nazwy głównej usługi

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.
2. Wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**.
3. Wybierz aplikację, do której chcesz ograniczyć dostęp.
4. Wybierz pozycję **Właściwości** , a następnie skopiuj identyfikator obiektu.

```powershell
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $sp.ObjectId
```
Usuń wszystkich użytkowników przypisanych do aplikacji.
 ```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get Azure AD App role assignments using objectId of the Service Principal
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

    # Remove all users and groups assigned to the application
    $assignments | ForEach-Object {
        if ($_.PrincipalType -eq "User") {
            Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        } elseif ($_.PrincipalType -eq "Group") {
            Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        }
    }
 ```

Odwołaj uprawnienia przyznane aplikacji

```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get all delegated permissions for the service principal
    $spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

    # Remove all delegated permissions
    $spOAuth2PermissionsGrants | ForEach-Object {
        Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
    }

    # Get all application permissions for the service principal
    $spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

    # Remove all delegated permissions
    $spApplicationPermissions | ForEach-Object {
        Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
    }
```
Unieważnianie tokenów odświeżania
```powershell
        Connect-AzureAD

        # Get Service Principal using objectId
        $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

        # Get Azure AD App role assignments using objectID of the Service Principal
        $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

        # Revoke refresh token for all users assigned to the application
        $assignments | ForEach-Object {
            Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
        }
```
## <a name="next-steps"></a>Następne kroki
- [Zarządzanie zgodą na aplikacje i ocenę wniosku o zgodę](manage-consent-requests.md)
- [Konfigurowanie zgody użytkownika](configure-user-consent.md)
- [Konfigurowanie przepływu pracy zgody administratora](configure-admin-consent-workflow.md)
