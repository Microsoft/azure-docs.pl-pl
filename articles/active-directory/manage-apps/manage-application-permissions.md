---
title: Zarządzanie uprawnieniami użytkowników i administratorów — Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak przeglądać uprawnienia dla aplikacji w usłudze Azure AD i zarządzać nimi. Na przykład Odwołaj wszystkie uprawnienia udzielone aplikacji.
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbd2bc7b9ccc9efe0c6fdf8de02665160667cb8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256936"
---
# <a name="take-action-on-overprivileged-or-suspicious-applications-in-azure-active-directory"></a>Podejmowanie akcji w przypadku aplikacji z ograniczeniami lub podejrzanymi uprawnieniami w Azure Active Directory

Dowiedz się, jak przeglądać uprawnienia aplikacji i zarządzać nimi. Ten artykuł zawiera różne akcje, które można wykonać w celu zabezpieczenia aplikacji zgodnie z scenariuszem. Te akcje dotyczą wszystkich aplikacji, które zostały dodane do dzierżawy usługi Azure Active Directory (Azure AD) za pośrednictwem zgody użytkownika lub administratora.

Aby uzyskać więcej informacji na temat wyrażania zgody na aplikacje, zobacz [Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać następujące akcje, należy zalogować się jako Administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.

Aby ograniczyć dostęp do aplikacji, należy wymagać przypisania użytkownika, a następnie przypisania użytkowników lub grup do aplikacji.  Aby uzyskać więcej informacji, zobacz [metody przypisywania użytkowników i grup](./assign-user-or-group-access-portal.md).

Możesz uzyskać dostęp do portalu usługi Azure AD, aby uzyskać kontekstowe skrypty programu PowerShell do wykonywania akcji.
 
1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.
2. Wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**.
3. Wybierz aplikację, do której chcesz ograniczyć dostęp.
4. Wybierz pozycję **Uprawnienia**. Na pasku poleceń wybierz pozycję **Przejrzyj uprawnienia**.

![Zrzut ekranu przedstawiający okno Przeglądanie uprawnień.](./media/manage-application-permissions/review-permissions.png)


## <a name="control-access-to-an-application"></a>Kontrola dostępu do aplikacji

Zalecamy, aby ograniczyć dostęp do aplikacji przez włączenie ustawienia **przypisania użytkownika** .

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.
2. Wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**.
3. Wybierz aplikację, do której chcesz ograniczyć dostęp.
4. Wybierz pozycję **Właściwości**, a następnie ustaw opcję **wymagane przez użytkownika** na **wartość tak**.
5. Wybierz pozycję **Użytkownicy i grupy**, a następnie usuń niechcianych użytkowników przypisanych do aplikacji.
6. Przypisywanie użytkowników lub grup do aplikacji.

Opcjonalnie można usunąć wszystkich użytkowników przypisanych do aplikacji przy użyciu programu PowerShell.

## <a name="revoke-all-permissions-for-an-application"></a>Odwołaj wszystkie uprawnienia dla aplikacji

Przy użyciu skryptu programu PowerShell odwołuje się wszystkie uprawnienia udzielone tej aplikacji.

> [!NOTE]
> Cofnięcie aktualnie udzielonego uprawnienia nie uniemożliwi użytkownikom ponownego przesłania do aplikacji. Aby uniemożliwić użytkownikom wyrażanie zgody, Przeczytaj [temat Konfigurowanie sposobu, w jaki użytkownicy zgadzają się na aplikacje](configure-user-consent.md).

Opcjonalnie można wyłączyć aplikację, aby uniemożliwić użytkownikom uzyskiwanie dostępu do aplikacji i uniemożliwić dostęp aplikacji do danych.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.
2. Wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**.
3. Wybierz aplikację, do której chcesz ograniczyć dostęp.
4. Wybierz pozycję **Właściwości**, a następnie ustaw opcję Włącz, aby **użytkownicy mogli się logować?** do **nie**.

## <a name="investigate-a-suspicious-application"></a>Zbadaj podejrzaną aplikację

Zalecamy, aby ograniczyć dostęp do aplikacji przez włączenie ustawienia **przypisania użytkownika** . Następnie zapoznaj się z uprawnieniami przyznanymi przez użytkowników i administratorów do aplikacji.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.
3. Wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**.
5. Wybierz aplikację, do której chcesz ograniczyć dostęp.
6. Wybierz pozycję **Właściwości**, a następnie ustaw opcję **wymagane przez użytkownika** na **wartość tak**.
7. Wybierz pozycję **uprawnienia** i sprawdź uprawnienia wysłane przez administratora i użytkownika.

Opcjonalnie za pomocą programu PowerShell można:

- Usuń wszystkich przypisanych użytkowników, aby uniemożliwić im logowanie do aplikacji.
- Unieważnij tokeny odświeżania dla użytkowników, którzy mają dostęp do aplikacji.
- Odwołaj wszystkie uprawnienia dla aplikacji.

Można też wyłączyć aplikację, aby blokować dostęp użytkowników i zatrzymywać dostęp aplikacji do danych.


## <a name="disable-a-malicious-application"></a>Wyłącz złośliwą aplikację 

Zalecamy wyłączenie aplikacji, aby zablokować dostęp użytkowników i uniemożliwić aplikacji dostęp do danych. Jeśli aplikacja zostanie usunięta, użytkownicy będą mogli ponownie wyrazić zgodę na aplikację i udzielić dostępu do danych.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.
2. Wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**.
3. Wybierz aplikację, do której chcesz ograniczyć dostęp.
4. Wybierz pozycję **Właściwości**, a następnie skopiuj identyfikator obiektu.

### <a name="powershell-commands"></a>Polecenia programu PowerShell


Pobierz identyfikator obiektu nazwy głównej usługi.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.
2. Wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**.
3. Wybierz aplikację, do której chcesz ograniczyć dostęp.
4. Wybierz pozycję **Właściwości**, a następnie skopiuj identyfikator obiektu.

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
Unieważnianie tokenów odświeżania.
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
