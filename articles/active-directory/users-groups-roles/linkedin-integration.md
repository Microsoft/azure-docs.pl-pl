---
title: Zgoda administratora dla połączeń konta LinkedIn — Azure AD | Microsoft Docs
description: Wyjaśniono, jak włączyć lub wyłączyć połączenia konta integracji z usługą LinkedIn w aplikacjach firmy Microsoft w Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d27cd30d6543d967cca2648597854a06f8ea4eb7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90053977"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Integracja połączeń konta LinkedIn w Azure Active Directory

Możesz zezwolić użytkownikom w organizacji na dostęp do swoich połączeń LinkedIn w niektórych aplikacjach firmy Microsoft. Żadne dane nie są udostępniane, dopóki użytkownicy nie wyrazią zgody na połączenie swoich kont. Swoją organizację można zintegrować w [centrum administracyjnym](https://aad.portal.azure.com)usługi Azure Active Directory (Azure AD).

> [!IMPORTANT]
> Ustawienie połączeń konta LinkedIn jest obecnie wdrażane w organizacjach usługi Azure AD. Gdy jest on wdrażany w organizacji, jest domyślnie włączony.
>
> Wyjątki:
>
> * To ustawienie nie jest dostępne dla klientów korzystających z Microsoft Cloud dla instytucji rządowych Stanów Zjednoczonych, Microsoft Cloud Niemczech ani platformy Azure i Microsoft 365 obsługiwanych przez firmę 21Vianet w Chinach.
> * Ustawienie jest domyślnie wyłączone dla organizacji usługi Azure AD, które są obsługiwane w Niemczech. Należy pamiętać, że to ustawienie nie jest dostępne dla klientów korzystających Microsoft Cloud Niemiec.
> * Ustawienie jest domyślnie wyłączone dla organizacji, które są obsługiwane we Francji.
>
> Po włączeniu połączeń konta LinkedIn dla Twojej organizacji połączenia z kontem działają po zalogowaniu się użytkowników do aplikacji uzyskujących dostęp do danych firmy w ich imieniu. Aby uzyskać informacje na temat ustawienia zgody użytkownika, zobacz [Jak usunąć dostęp użytkownika do aplikacji](../manage-apps/methods-for-removing-user-access.md).

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Włącz połączenia konta LinkedIn w Azure Portal

Połączenia konta LinkedIn można włączyć tylko dla użytkowników, którzy mają mieć dostęp, z całej organizacji tylko do wybranych użytkowników w organizacji.

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com/) przy użyciu konta, które jest administratorem globalnym dla organizacji usługi Azure AD.
1. Wybierz pozycję **Użytkownicy**.
1. Na stronie **Użytkownicy** wybierz pozycję **Ustawienia użytkownika**.
1. W obszarze **połączenia konta serwisu LinkedIn**użytkownicy mogą łączyć swoje konta w celu uzyskiwania dostępu do połączeń LinkedIn w niektórych aplikacjach firmy Microsoft. Żadne dane nie są udostępniane, dopóki użytkownicy nie wyrazią zgody na połączenie swoich kont.

    * Wybierz opcję **tak** , aby włączyć usługę dla wszystkich użytkowników w organizacji
    * Wybierz **wybraną grupę** , aby włączyć usługę tylko dla grupy wybranych użytkowników w organizacji
    * Wybierz pozycję **nie** , aby wycofać zgodę ze wszystkich użytkowników w organizacji

    ![Integracja połączeń konta LinkedIn w organizacji](./media/linkedin-integration/linkedin-integration.png)

1. Gdy skończysz, wybierz pozycję **Zapisz** , aby zapisać ustawienia.

> [!Important]
> Integracja serwisu LinkedIn nie jest w pełni włączona dla użytkowników, dopóki nie wyrazi zgody na połączenie ich kont. Po włączeniu połączeń kont dla użytkowników nie są udostępniane żadne dane.

### <a name="assign-selected-users-with-a-group"></a>Przypisywanie wybranych użytkowników do grupy

Zamieniono opcję "selected", która określa listę użytkowników z opcją wyboru grupy użytkowników, aby umożliwić połączenie serwisu LinkedIn i kont Microsoft dla pojedynczej grupy zamiast wielu pojedynczych użytkowników. Jeśli nie masz włączonych połączeń konta LinkedIn dla wybranych użytkowników, nie musisz nic robić. Jeśli wcześniej włączono połączenia konta usługi LinkedIn dla wybranych użytkowników indywidualnych, należy:

1. Pobierz bieżącą listę poszczególnych użytkowników
1. Przenieś obecnie włączonych poszczególnych użytkowników do grupy
1. Użyj grupy z poprzedniej jako wybranej grupy w ustawieniu połączenia konta LinkedIn w centrum administracyjnym usługi Azure AD.

> [!NOTE]
> Nawet jeśli nie przeniesiesz obecnie wybranych użytkowników do grupy, nadal będzie można zobaczyć informacje o serwisie LinkedIn w aplikacjach firmy Microsoft.

### <a name="move-currently-selected-users-to-a-group"></a>Przenieś aktualnie wybranych użytkowników do grupy

1. Utwórz plik CSV dla użytkowników wybranych dla połączeń kont usługi LinkedIn.
1. Zaloguj się do Microsoft 365 przy użyciu konta administratora.
1. Uruchom program PowerShell.
1. Zainstaluj moduł usługi Azure AD, uruchamiając `Install-Module AzureAD`
1. Uruchom poniższy skrypt:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Aby użyć grupy z kroku dwa jako wybranej grupy w ustawieniach połączeń konta LinkedIn w centrum administracyjnym usługi Azure AD, zobacz [Włączanie połączeń konta LinkedIn w Azure Portal](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Użyj zasady grupy, aby włączyć połączenia konta LinkedIn

1. Pobierz [pliki szablonów administracyjnych pakietu Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Wyodrębnij pliki **ADMX** i skopiuj je do magazynu centralnego.
1. Otwórz przystawkę Zarządzanie zasadami grupy.
1. Utwórz obiekt zasady grupy przy użyciu następującego ustawienia: **Konfiguracja użytkownika**  >  **Szablony administracyjne**  >  **Microsoft Office 2016**  >  **różne**  >  **Pokaż funkcje serwisu LinkedIn w aplikacjach pakietu Office**.
1. Wybierz opcję **włączone** lub **wyłączone**.
  
   State | Efekt
   ------ | ------
   **Włączone** | Opcja **Pokaż funkcje serwisu LinkedIn w aplikacjach pakietu Office** w programie Office 2016 jest włączona. Użytkownicy w organizacji mogą korzystać z funkcji serwisu LinkedIn w aplikacjach pakietu Office 2016.
   **Wyłączone** | Ustawienie **Pokaż funkcje serwisu LinkedIn w aplikacjach pakietu Office** w pakiecie Office 2016 jest wyłączone, a użytkownicy końcowi nie mogą zmieniać tego ustawienia. Użytkownicy w organizacji nie mogą korzystać z funkcji LinkedIn w swoich aplikacjach pakietu Office 2016.

Te zasady grupy mają wpływ tylko na aplikacje pakietu Office 2016 dla komputera lokalnego. Jeśli użytkownicy wyłączają serwis LinkedIn w swoich aplikacjach pakietu Office 2016, nadal będą mogli wyświetlać funkcje serwisu LinkedIn w Microsoft 365.

## <a name="next-steps"></a>Następne kroki

* [Wyrażanie zgody użytkownika i udostępnianie danych w serwisie LinkedIn](linkedin-user-consent.md)

* [Informacje i funkcje serwisu LinkedIn w aplikacjach firmy Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centrum pomocy w serwisie LinkedIn](https://www.linkedin.com/help/linkedin)

* [Wyświetl bieżące ustawienie integracji serwisu LinkedIn w Azure Portal](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)