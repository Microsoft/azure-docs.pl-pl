---
title: Ukrywanie aplikacji dla przedsiębiorstw w interfejsie użytkownika w usłudze Azure AD
description: Jak ukryć aplikację dla przedsiębiorstw w doświadczeniach użytkownika w panelach dostępu Azure Active Directory lub Microsoft 365 uruchamiania.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: iangithinji
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: a90f3e3aeb1d68c6c6e6eeea29c04ff7880dccd3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374203"
---
# <a name="hide-enterprise-applications-from-end-users-in-azure-active-directory"></a>Ukrywanie aplikacji dla przedsiębiorstw przed użytkownikami Azure Active Directory

Instrukcje dotyczące ukrywania aplikacji w panelu MyApps użytkownika końcowego lub Microsoft 365 uruchamiania. Gdy aplikacja jest ukryta, użytkownicy nadal mają uprawnienia do aplikacji. 

## <a name="prerequisites"></a>Wymagania wstępne

administrator aplikacji są wymagane do ukrycia aplikacji na panelu MyApps i Microsoft 365 uruchamiania.

administrator globalny są wymagane do ukrycia wszystkich Microsoft 365 aplikacji.


## <a name="hide-an-application-from-the-end-user"></a>Ukrywanie aplikacji przed użytkownikiem końcowy
Aby ukryć aplikację w panelu MyApps i Microsoft 365 uruchamiania aplikacji, należy wykonać poniższe kroki.

1.  Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator globalny katalogu.
2.  Wybierz pozycję **Azure Active Directory**.
3.  Wybierz **pozycję Aplikacje dla przedsiębiorstw.** Zostanie **otwarty blok Aplikacje dla przedsiębiorstw —** Wszystkie aplikacje.
4.  W **obszarze Typ aplikacji** wybierz pozycję Aplikacje dla **przedsiębiorstw,** jeśli nie została jeszcze wybrana.
5.  Wyszukaj aplikację, którą chcesz ukryć, a następnie kliknij aplikację.  Zostanie otwarte omówienie aplikacji.
6.  Kliknij pozycję **Właściwości**. 
7.  W przypadku **pytania Widoczne dla użytkowników?** kliknij pozycję **Nie.**
8.  Kliknij pozycję **Zapisz**.

> [!NOTE]
> Te instrukcje dotyczą tylko aplikacji dla przedsiębiorstw.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Ukrywanie aplikacji przy użyciu programu PowerShell usługi Azure AD

Aby ukryć aplikację w panelu MyApps, możesz ręcznie dodać tag HideApp do jednostki usługi dla aplikacji. Uruchom następujące polecenia [programu PowerShell usługi AzureAD,](/powershell/module/azuread/#service_principals) aby ustawić właściwość Widoczne dla **użytkowników?** aplikacji na **wartość Nie.** 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-myapps-panel"></a>Ukrywanie Microsoft 365 aplikacji w panelu MyApps

Aby ukryć wszystkie aplikacje Microsoft 365 w panelu MyApps, należy wykonać poniższe kroki. Aplikacje są nadal widoczne w portalu usługi Office 365.

1.  Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator globalny katalogu.
2.  Wybierz pozycję **Azure Active Directory**.
3.  Wybierz pozycję **Użytkownicy**.
4.  Wybierz pozycję **Ustawienia użytkownika**.
5.  W **obszarze Aplikacje dla przedsiębiorstw** kliknij pozycję Zarządzaj uruchamianiem i wyświetlaniem aplikacji przez użytkowników **końcowych.**
6.  W **przypadku ustawienia Użytkownicy widzą tylko aplikacje usługi Office 365 w portalu usługi Office 365** kliknij przycisk **Tak.**
7.  Kliknij pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki
* [Wyświetlanie wszystkich moich grup](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](assign-user-or-group-access-portal.md)
* [Usuwanie przypisania użytkownika lub grupy z aplikacji przedsiębiorstwa](./assign-user-or-group-access-portal.md)
* [Zmienianie nazwy lub logo aplikacji przedsiębiorstwa](./add-application-portal-configure.md)
