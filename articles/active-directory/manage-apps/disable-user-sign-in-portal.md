---
title: Wyłączanie logowania użytkowników dla aplikacji przedsiębiorstwa w usłudze Azure AD
description: Jak wyłączyć aplikację dla przedsiębiorstw, aby żaden użytkownik nie mógł się do niego zalogować w Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/12/2019
ms.author: iangithinji
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9af580ef8b3691a5f188ac15069dda00a5f5802
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374254"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Wyłączanie logowania użytkowników dla aplikacji przedsiębiorstwa w Azure Active Directory

Aplikację przedsiębiorstwa można łatwo wyłączyć, aby żaden użytkownik nie może się do niego zalogować w usłudze Azure Active Directory (Azure AD). Do zarządzania aplikacją przedsiębiorstwa potrzebne są odpowiednie uprawnienia. Musisz być administratorem globalnym katalogu.

## <a name="how-do-i-disable-user-sign-ins"></a>Jak mogę wyłączyć logowania użytkowników?

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
1. Wybierz **pozycję Wszystkie usługi**, **Azure Active Directory** w polu tekstowym, a następnie naciśnij klawisz **.**
1. W **okienku Azure Active Directory**  -   **_directoryname_*_ (okienko usługi Azure AD*** dla katalogu, którym zarządzasz), wybierz pozycję _ Aplikacje dla przedsiębiorstw.
1. W **okienku Aplikacje dla przedsiębiorstw** — Wszystkie aplikacje zostanie wyświetlona lista aplikacji, które można zarządzać. Wybierz aplikację.
1. W okienku ***nazwa_aplikacji**(czyli okienku z nazwą wybranej aplikacji w tytule) wybierz pozycję _*Właściwości**.
1. W okienku ***nazwa aplikacji** _ - _ *Właściwości** wybierz opcję **Nie** dla opcji Włączone dla użytkowników **do logowania?**.
1. Wybierz polecenie **Zapisz.**

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Wyłączanie aplikacji nieznajdej się na liście przy użyciu programu PowerShell usługi Azure AD

Jeśli znasz element AppId aplikacji, która nie jest wyświetlana na liście aplikacji dla przedsiębiorstw (na przykład ponieważ aplikacja została usunięta lub jednostka usługi nie została jeszcze utworzona z powodu wstępnej autoryzacji aplikacji przez firmę Microsoft), możesz ręcznie utworzyć jednostkę usługi dla aplikacji, a następnie wyłączyć ją za pomocą polecenia [cmdlet programu PowerShell usługi AzureAD.](/powershell/module/azuread/New-AzureADServicePrincipal)

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

* [Wyświetlanie wszystkich moich grup](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](assign-user-or-group-access-portal.md)
* [Usuwanie przypisania użytkownika lub grupy z aplikacji przedsiębiorstwa](./assign-user-or-group-access-portal.md)
* [Zmienianie nazwy lub logo aplikacji przedsiębiorstwa](./add-application-portal-configure.md)
