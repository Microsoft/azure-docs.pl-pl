---
title: Zarządzanie uprawnieniami użytkowników i administratorów — Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak przeglądać uprawnienia aplikacji w usłudze Azure AD i zarządzać nimi. Na przykład odwołaj wszystkie uprawnienia przyznane aplikacji.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: iangithinji
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcd137030e4e1f3e88f47ec5ba78b3bde08fe068
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373983"
---
# <a name="take-action-on-overprivileged-or-suspicious-applications-in-azure-active-directory"></a>Podjąć działania w przypadku aplikacji o nadanych lub podejrzanych dostępach w Azure Active Directory

Dowiedz się, jak przeglądać uprawnienia aplikacji i zarządzać nimi. Ten artykuł zawiera różne akcje, które można podjąć w celu zabezpieczenia aplikacji zgodnie ze scenariuszem. Te akcje dotyczą wszystkich aplikacji, które zostały dodane do dzierżawy usługi Azure Active Directory (Azure AD) za pośrednictwem zgody użytkownika lub administratora.

Aby uzyskać więcej informacji na temat wyrażania zgody na aplikacje, [zobacz Azure Active Directory platformę wyrażania zgody.](../develop/consent-framework.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać poniższe czynności, musisz zalogować się jako administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.

Aby ograniczyć dostęp do aplikacji, należy wymagać przypisania użytkownika, a następnie przypisać użytkowników lub grupy do aplikacji.  Aby uzyskać więcej informacji, [zobacz Metody przypisywania użytkowników i grup](./assign-user-or-group-access-portal.md).

Dostęp do portalu usługi Azure AD umożliwia uzyskanie kontekstowych skryptów programu PowerShell do wykonywania akcji.
 
1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.
2. Wybierz **Azure Active Directory**  >  **dla przedsiębiorstw.**
3. Wybierz aplikację, do której chcesz ograniczyć dostęp.
4. Wybierz pozycję **Uprawnienia**. Na pasku poleceń wybierz pozycję **Przejrzyj uprawnienia.**

![Zrzut ekranu przedstawiający okno przeglądu uprawnień.](./media/manage-application-permissions/review-permissions.png)


## <a name="control-access-to-an-application"></a>Kontrola dostępu do aplikacji

Zalecamy ograniczenie dostępu do aplikacji przez włączenie **ustawienia Przypisanie** użytkownika.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.
2. Wybierz **Azure Active Directory**  >  **dla przedsiębiorstw.**
3. Wybierz aplikację, do której chcesz ograniczyć dostęp.
4. Wybierz **pozycję Właściwości,** a następnie ustaw wymagane **wymaganie użytkownika na** wartość **Tak.**
5. Wybierz **pozycję Użytkownicy i grupy,** a następnie usuń niechcianych użytkowników przypisanych do aplikacji.
6. Przypisz użytkowników lub grupy do aplikacji.

Opcjonalnie możesz usunąć wszystkich użytkowników przypisanych do aplikacji przy użyciu programu PowerShell.

## <a name="revoke-all-permissions-for-an-application"></a>Odwoływanie wszystkich uprawnień dla aplikacji

Użycie skryptu programu PowerShell odwołuje wszystkie uprawnienia przyznane tej aplikacji.

> [!NOTE]
> Odwołanie bieżącego przyznanego uprawnienia nie zatrzyma użytkownikom ponownego wyrażania zgody na aplikację. Jeśli chcesz zablokować użytkownikom dostęp do wyrażania zgody, przeczytaj konfigurowanie sposobu wyrażania przez [użytkowników zgody na aplikacje.](configure-user-consent.md)

Opcjonalnie możesz wyłączyć aplikację, aby uniemożliwić użytkownikom dostęp do aplikacji i uniemożliwić aplikacji dostęp do danych.

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) jako administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.
2. Wybierz **Azure Active Directory**  >  **dla przedsiębiorstw.**
3. Wybierz aplikację, do której chcesz ograniczyć dostęp.
4. Wybierz **pozycję Właściwości,** a następnie ustaw opcję Włączone dla użytkowników na **potrzeby logowania?** na **wartość Nie.**

## <a name="investigate-a-suspicious-application"></a>Badanie podejrzanej aplikacji

Zalecamy ograniczenie dostępu do aplikacji przez włączenie ustawienia **Przypisywanie** użytkownika. Następnie przejrzyj uprawnienia przyznane aplikacji przez użytkowników i administratorów.

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) jako administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.
3. Wybierz **Azure Active Directory**  >  **dla przedsiębiorstw.**
5. Wybierz aplikację, do której chcesz ograniczyć dostęp.
6. Wybierz **pozycję Właściwości,** a następnie ustaw wymagane **wymaganie użytkownika na** wartość **Tak.**
7. Wybierz **pozycję Uprawnienia** i przejrzyj uprawnienia administratora i użytkownika, na które wyraził zgodę.

Opcjonalnie za pomocą programu PowerShell można:

- Usuń wszystkich przypisanych użytkowników, aby nie logować się do aplikacji.
- Unieważnij tokeny odświeżania dla użytkowników, którzy mają dostęp do aplikacji.
- Odwołaj wszystkie uprawnienia dla aplikacji.

Możesz też wyłączyć aplikację, aby zablokować dostęp użytkowników i zatrzymać dostęp aplikacji do danych.


## <a name="disable-a-malicious-application"></a>Wyłączanie złośliwej aplikacji 

Zalecamy wyłączenie aplikacji, aby zablokować dostęp użytkowników i uniemożliwić aplikacji dostęp do danych. Jeśli zamiast tego usuniesz aplikację, użytkownicy będą mieć możliwość ponownego wyrażenia zgody na aplikację i udzielenia dostępu do danych.

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) jako administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.
2. Wybierz **Azure Active Directory**  >  **dla przedsiębiorstw.**
3. Wybierz aplikację, do której chcesz ograniczyć dostęp.
4. Wybierz **pozycję Właściwości**, a następnie skopiuj identyfikator obiektu.

### <a name="powershell-commands"></a>Polecenia programu PowerShell


Pobierz identyfikator obiektu jednostki usługi.

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) jako administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.
2. Wybierz **Azure Active Directory**  >  **dla przedsiębiorstw.**
3. Wybierz aplikację, do której chcesz ograniczyć dostęp.
4. Wybierz **pozycję Właściwości**, a następnie skopiuj identyfikator obiektu.

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

Odwołaj uprawnienia przyznane aplikacji.

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
Unieważnij tokeny odświeżania.
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
- [Zarządzanie zgodą na aplikacje i ocenianie żądania zgody](manage-consent-requests.md)
- [Konfigurowanie zgody użytkownika](configure-user-consent.md)
- [Konfigurowanie przepływu pracy zgody administratora](configure-admin-consent-workflow.md)
