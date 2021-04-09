---
title: Wyłączanie logowania użytkownika dla aplikacji dla przedsiębiorstw w usłudze Azure AD
description: Jak wyłączyć aplikację dla przedsiębiorstw, dzięki czemu żaden użytkownik nie może zalogować się do niej w Azure Active Directory
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
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
ms.openlocfilehash: 783de636d4520f48f624f3380f811f1f18366330
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259240"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Wyłącz logowania użytkowników dla aplikacji dla przedsiębiorstw w Azure Active Directory

Można łatwo wyłączyć aplikację przedsiębiorstwa, aby uniemożliwić użytkownikom zalogowanie się do niej w usłudze Azure Active Directory (Azure AD). Potrzebujesz odpowiednich uprawnień do zarządzania aplikacją dla przedsiębiorstw. Ponadto musisz być administratorem globalnym katalogu.

## <a name="how-do-i-disable-user-sign-ins"></a>Jak mogę wyłączyć logowania użytkowników?

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
1. Wybierz pozycję **wszystkie usługi**, wpisz **Azure Active Directory** w polu tekstowym, a następnie wybierz klawisz **Enter**.
1. W okienku **Azure Active Directory**  -   **_DirectoryName_*_ (czyli okienku usługi Azure AD dla katalogu, którym zarządzasz) wybierz pozycję _ aplikacje dla* przedsiębiorstw**.
1. W okienku **aplikacje dla przedsiębiorstw — wszystkie aplikacje** zostanie wyświetlona lista aplikacji, którymi można zarządzać. Wybierz aplikację.
1. W okienku ***nazwa_aplikacji** _ (czyli okienku z nazwą wybranej aplikacji w tytule) wybierz _ * właściwości * *.
1. W okienku ***nazwa_aplikacji** _-_ *Właściwości** wybierz pozycję **nie** , aby **włączyć Logowanie użytkowników?**.
1. Wybierz polecenie **Zapisz** .

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Wyłączenie aplikacji nieznajdującej się na liście przy użyciu programu Azure AD PowerShell

Jeśli znasz identyfikator aplikacji, która nie jest wyświetlana na liście aplikacji dla przedsiębiorstw (na przykład dlatego, że aplikacja została usunięta lub jednostka usługi nie została jeszcze utworzona z powodu wstępnej autoryzacji przez firmę Microsoft), możesz ręcznie utworzyć nazwę główną usługi dla aplikacji, a następnie wyłączyć ją przy użyciu [polecenia cmdlet programu AzureAD PowerShell](/powershell/module/azuread/New-AzureADServicePrincipal).

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
* [Usuwanie przypisania użytkownika lub grupy z aplikacji dla przedsiębiorstw](./assign-user-or-group-access-portal.md)
* [Zmiana nazwy lub logo aplikacji dla przedsiębiorstw](./add-application-portal-configure.md)
