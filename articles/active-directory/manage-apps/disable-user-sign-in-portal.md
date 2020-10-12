---
title: Wyłączanie logowania użytkownika dla aplikacji dla przedsiębiorstw w usłudze Azure AD
description: Jak wyłączyć aplikację dla przedsiębiorstw, dzięki czemu żaden użytkownik nie może zalogować się do niej w Azure Active Directory
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
ms.openlocfilehash: 4c6fc9a4716f1427257b3bbf18e5fa653567e141
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84763384"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Wyłącz logowania użytkowników dla aplikacji dla przedsiębiorstw w Azure Active Directory

Można łatwo wyłączyć aplikację przedsiębiorstwa, aby uniemożliwić użytkownikom zalogowanie się do niej w usłudze Azure Active Directory (Azure AD). Potrzebujesz odpowiednich uprawnień do zarządzania aplikacją dla przedsiębiorstw. Ponadto musisz być administratorem globalnym katalogu.

## <a name="how-do-i-disable-user-sign-ins"></a>Jak mogę wyłączyć logowania użytkowników?

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
1. Wybierz pozycję **wszystkie usługi**, wpisz **Azure Active Directory** w polu tekstowym, a następnie wybierz klawisz **Enter**.
1. W okienku **Azure Active Directory**  -   ***DirectoryName*** (czyli okienku usługi Azure AD dla katalogu, którym zarządzasz) wybierz pozycję **aplikacje dla przedsiębiorstw**.
1. W okienku **aplikacje dla przedsiębiorstw — wszystkie aplikacje** zostanie wyświetlona lista aplikacji, którymi można zarządzać. Wybierz aplikację.
1. W okienku ***nazwa_aplikacji*** (czyli okienku z nazwą wybranej aplikacji w tytule) wybierz pozycję **Właściwości**.
1. W okienku ***appname***  -  **Właściwości** programu nazwa_aplikacji wybierz pozycję **nie** , aby **umożliwić użytkownikom logowanie się?**.
1. Wybierz polecenie **Zapisz** .

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Wyłączenie aplikacji nieznajdującej się na liście przy użyciu programu Azure AD PowerShell

Jeśli znasz identyfikator aplikacji, która nie jest wyświetlana na liście aplikacji dla przedsiębiorstw (na przykład dlatego, że aplikacja została usunięta lub jednostka usługi nie została jeszcze utworzona z powodu wstępnej autoryzacji przez firmę Microsoft), możesz ręcznie utworzyć nazwę główną usługi dla aplikacji, a następnie wyłączyć ją przy użyciu [polecenia cmdlet programu AzureAD PowerShell](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0).

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>Następne kroki

* [Zobacz wszystkie moje grupy](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](assign-user-or-group-access-portal.md)
* [Usuwanie przypisania użytkownika lub grupy z aplikacji dla przedsiębiorstw](remove-user-or-group-access-portal.md)
* [Zmiana nazwy lub logo aplikacji dla przedsiębiorstw](change-name-or-logo-portal.md)
