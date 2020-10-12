---
title: Odwoływanie dostępu użytkownika w nagłej sytuacji Azure Active Directory | Microsoft Docs
description: Jak odwołać wszystkie prawa dostępu dla użytkownika w Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.topic: how-to
author: curtand
ms.author: curtand
manager: daveba
ms.reviewer: krbain
ms.date: 07/15/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8d1b6d912cadb073251d72ee327f4b19950ab1b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90055189"
---
# <a name="revoke-user-access-in-azure-active-directory"></a>Odwołaj dostęp użytkownika w Azure Active Directory

Wśród scenariuszy, które mogą wymagać od administratora odpowiedzenia wszystkich dostępu dla użytkownika, zalicza się do nich naruszone konta, zakończenie pracownika i inne zagrożenia. W zależności od stopnia złożoności środowiska Administratorzy mogą wykonać kilka czynności, aby upewnić się, że dostęp został odwołany. W niektórych scenariuszach może istnieć okres między inicjacją odwołania dostępu a faktycznym odwołaniem dostępu.

Aby zmniejszyć ryzyko, należy zrozumieć, jak działają tokeny. Istnieje wiele rodzajów tokenów, które znajdują się w jednym z wzorców wymienionych w poniższych sekcjach.

## <a name="access-tokens-and-refresh-tokens"></a>Tokeny dostępu i odświeżanie tokenów

Tokeny dostępu i tokeny odświeżania są często używane w przypadku aplikacji klienckich, a także używane w aplikacjach opartych na przeglądarce, takich jak aplikacje jednostronicowe.

- Gdy użytkownicy uwierzytelniają się w usłudze Azure AD, zasady autoryzacji są oceniane w celu ustalenia, czy użytkownik może uzyskać dostęp do określonego zasobu.  

- W przypadku autoryzacji usługa Azure AD wystawia token dostępu i token odświeżania dla zasobu.  

- Tokeny dostępu wystawione przez usługę Azure AD są domyślnie ostatnie przez 1 godzinę. Jeśli protokół uwierzytelniania zezwala, aplikacja może w trybie dyskretnym ponownie uwierzytelnić użytkownika przez przekazanie tokenu odświeżania do usługi Azure AD po wygaśnięciu tokenu dostępu.

Usługa Azure AD następnie ponownie oceni swoje zasady autoryzacji. Jeśli użytkownik nadal jest autoryzowany, usługa Azure AD wystawia nowy token dostępu i Odśwież token.

Tokeny dostępu mogą stanowić zagrożenie bezpieczeństwa, jeśli dostęp musi zostać odwołany w czasie krótszym niż okres istnienia tokenu, który zwykle trwa około godziny. Z tego powodu firma Microsoft aktywnie pracuje nad przeprowadzeniem [ciągłej oceny dostępu](../conditional-access/concept-continuous-access-evaluation.md) do aplikacji Microsoft 365, co pomaga zapewnić unieważnienie tokenów dostępu w czasie niemal rzeczywistym.  

## <a name="session-tokens-cookies"></a>Tokeny sesji (pliki cookie)

Większość aplikacji opartych na przeglądarce korzysta z tokenów sesji zamiast tokenów dostępu i odświeżania.  

- Gdy użytkownik otwiera przeglądarkę i uwierzytelnia się w aplikacji za pośrednictwem usługi Azure AD, użytkownik otrzymuje dwa tokeny sesji. Jeden z usługi Azure AD i drugi z aplikacji.  

- Gdy aplikacja wystawia swój własny token sesji, dostęp do aplikacji podlega sesji aplikacji. W tym momencie użytkownik ma wpływ tylko na zasady autoryzacji, których dotyczy aplikacja.

- Zasady autoryzacji usługi Azure AD są ponownie oceniane tak często, jak aplikacja wysyła użytkownika z powrotem do usługi Azure AD. Ponowne obliczanie zwykle odbywa się w trybie dyskretnym, chociaż częstotliwość zależy od konfiguracji aplikacji. Istnieje możliwość, że aplikacja nigdy nie wyśle użytkownika z powrotem do usługi Azure AD, o ile token sesji jest prawidłowy.

- Aby token sesji został odwołany, aplikacja musi odwołać dostęp na podstawie własnych zasad autoryzacji. Usługa Azure AD nie może bezpośrednio odwołać tokenu sesji wystawionego przez aplikację.  

## <a name="revoke-access-for-a-user-in-the-hybrid-environment"></a>Odwołaj dostęp dla użytkownika w środowisku hybrydowym

W przypadku środowiska hybrydowego z Active Directorym lokalnym synchronizowanym z Azure Active Directory firma Microsoft zaleca administratorów IT wykonanie następujących czynności.  

### <a name="on-premises-active-directory-environment"></a>Lokalne środowisko Active Directory

Jako administrator w Active Directory połączyć się z siecią lokalną, Otwórz program PowerShell i wykonaj następujące czynności:

1. Wyłącz użytkownika w Active Directory. Zapoznaj się z tematem [disable-ADAccount](/powershell/module/addsadministration/disable-adaccount?view=win10-ps).

    ```PowerShell
    Disable-ADAccount -Identity johndoe  
    ```

1. Zresetuj hasło użytkownika dwa razy w Active Directory. Zapoznaj się z [ustawieniem Set-ADAccountPassword](/powershell/module/addsadministration/set-adaccountpassword?view=win10-ps).

    > [!NOTE]
    > Powodem, że zmiana hasła użytkownika jest dwa razy, ma na celu ograniczenie ryzyka związanego z przekazywaniem skrótu, szczególnie w przypadku opóźnień w lokalnej replikacji haseł. Jeśli możesz bezpiecznie założyć, że to konto nie zostało naruszone, możesz zresetować hasło tylko raz.

    > [!IMPORTANT] 
    > W poniższych poleceniach cmdlet nie należy używać przykładowych haseł. Należy koniecznie zmienić hasła na ciąg losowy.

    ```PowerShell
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd1" -Force)
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd2" -Force)
    ```

### <a name="azure-active-directory-environment"></a>Środowisko Azure Active Directory

Jako administrator w Azure Active Directory Otwórz program PowerShell, uruchom ``Connect-AzureAD`` polecenie i wykonaj następujące czynności:

1. Wyłącz użytkownika w usłudze Azure AD. Zapoznaj się z [ustawieniem Set-AzureADUser](/powershell/module/azuread/Set-AzureADUser?view=azureadps-2.0).

    ```PowerShell
    Set-AzureADUser -ObjectId johndoe@contoso.com -AccountEnabled $false
    ```
1. Odwołaj tokeny odświeżania usługi Azure AD użytkownika. Odwołaj się do [odwołania do AzureADUserAllRefreshToken](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0).

    ```PowerShell
    Revoke-AzureADUserAllRefreshToken -ObjectId johndoe@contoso.com
    ```

1. Wyłącz urządzenia użytkownika. Zapoznaj się z tematem [Get-AzureADUserRegisteredDevice](/powershell/module/azuread/get-azureaduserregistereddevice?view=azureadps-2.0).

    ```PowerShell
    Get-AzureADUserRegisteredDevice -ObjectId johndoe@contoso.com | Set-AzureADDevice -AccountEnabled $false
    ```

## <a name="optional-steps"></a>Kroki opcjonalne

- [Czyszczenie danych firmowych z aplikacji zarządzanych przez usługę Intune](/mem/intune/apps/apps-selective-wipe).

- [Wyczyść ustawienia fabryczne urządzeń należących do firmy](/mem/intune/remote-actions/devices-wipe).

> [!NOTE]
> Nie można odzyskać danych z urządzenia po wyczyszczeniu.

## <a name="when-access-is-revoked"></a>Po odwołaniu dostępu

Po wykonaniu powyższych czynności Administratorzy nie mogą uzyskać nowych tokenów dla żadnej aplikacji powiązanej z Azure Active Directory. Czas upływający między odwołaniem a użytkownikiem, który utraci dostęp, zależy od tego, w jaki sposób aplikacja udziela dostępu:

- W przypadku **aplikacji korzystających z tokenów dostępu**użytkownik utraci dostęp po wygaśnięciu tokenu dostępu.

- W przypadku aplikacji korzystających z **tokenów sesji**istniejące sesje kończą się zaraz po wygaśnięciu tokenu. Jeśli wyłączony stan użytkownika zostanie zsynchronizowany z aplikacją, aplikacja może automatycznie odwołać istniejące sesje użytkownika, jeśli jest ono skonfigurowane.  Czas trwania zależy od częstotliwości synchronizacji między aplikacją a usługą Azure AD.

## <a name="next-steps"></a>Następne kroki

- [Zasady bezpiecznego dostępu dla administratorów usługi Azure AD](directory-admin-roles-secure.md)
- [Dodawanie lub aktualizowanie informacji o profilu użytkownika](../fundamentals/active-directory-users-profile-azure-portal.md)