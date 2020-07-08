---
title: Usuwanie przypisań użytkowników lub grup z aplikacji w usłudze Azure AD
description: Jak usunąć przypisanie dostępu użytkownika lub grupy z aplikacji przedsiębiorstwa w Azure Active Directory
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/12/2019
ms.author: kenwith
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a09b3307403cb78da3bd22f3cc85bd75d7ebd858
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763061"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Usuwanie przypisania użytkownika lub grupy z aplikacji dla przedsiębiorstw w Azure Active Directory

Można łatwo usunąć użytkownika lub grupę z przypisanego dostępu do jednej z aplikacji w przedsiębiorstwie w Azure Active Directory (Azure AD). Potrzebujesz odpowiednich uprawnień do zarządzania aplikacją dla przedsiębiorstw. Ponadto musisz być administratorem globalnym katalogu.

> [!NOTE]
> W przypadku aplikacji firmy Microsoft (takich jak aplikacje pakietu Office 365) Użyj programu PowerShell, aby usunąć użytkowników do aplikacji dla przedsiębiorstw.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Jak mogę usunąć przypisanie użytkownika lub grupy do aplikacji przedsiębiorstwa w Azure Portal?

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
1. Wybierz pozycję **wszystkie usługi**, wpisz **Azure Active Directory** w polu tekstowym, a następnie wybierz klawisz **Enter**.
1. Na stronie **Azure Active Directory- *DirectoryName* ** (czyli stronie usługi Azure AD dla katalogu, którym zarządzasz) wybierz pozycję **aplikacje dla przedsiębiorstw**.
1. Na stronie **aplikacje dla przedsiębiorstw — wszystkie aplikacje** zostanie wyświetlona lista aplikacji, którymi można zarządzać. Wybierz aplikację.
1. ***Na stronie Przegląd aplikacji*** (czyli stronie z nazwą wybranej aplikacji w tytule) wybierz pozycję **Użytkownicy & grupy**.
1. ***Na stronie*** nazwa **użytkownika & przypisanie grupy** wybierz jednego z kilku użytkowników lub grup, a następnie wybierz polecenie **Usuń** . Potwierdź swoją decyzję w wierszu polecenia.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Jak mogę usunąć przypisanie użytkownika lub grupy do aplikacji przedsiębiorstwa przy użyciu programu PowerShell?

1. Otwórz wiersz polecenia programu Windows PowerShell z podwyższonym poziomem uprawnień.

   > [!NOTE]
   > Należy zainstalować moduł AzureAD (Użyj polecenia `Install-Module -Name AzureAD` ). Jeśli zostanie wyświetlony monit o zainstalowanie modułu NuGet lub nowego modułu Azure Active Directory v2 PowerShell, wpisz Y i naciśnij klawisz ENTER.

1. Uruchom `Connect-AzureAD` i zaloguj się przy użyciu konta administratora globalnego.
1. Użyj następującego skryptu, aby usunąć użytkownika i rolę z aplikacji:

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

## <a name="next-steps"></a>Następne kroki

- [Zobacz wszystkie moje grupy](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](assign-user-or-group-access-portal.md)
- [Wyłączanie logowania użytkownika dla aplikacji dla przedsiębiorstw](disable-user-sign-in-portal.md)
- [Zmiana nazwy lub logo aplikacji dla przedsiębiorstw](change-name-or-logo-portal.md)
