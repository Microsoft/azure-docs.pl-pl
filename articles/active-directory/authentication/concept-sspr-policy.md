---
title: Zasady samoobsługowego resetowania haseł — Azure Active Directory
description: Informacje na temat różnych opcji zasad samoobsługowego resetowania hasła Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8b6d08dd2073de80ac0f7fd08f510d9cda80545
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143238"
---
# <a name="self-service-password-reset-policies-and-restrictions-in-azure-active-directory"></a>Zasady i ograniczenia dotyczące samoobsługowego resetowania hasła w Azure Active Directory

W tym artykule opisano zasady haseł i wymagania dotyczące złożoności skojarzone z kontami użytkowników w dzierżawie usługi Azure Active Directory (Azure AD).

## <a name="administrator-reset-policy-differences"></a>Administrator reset policy differences (Różnice zasad resetowania administratora)

**Firma Microsoft wymusza silne domyślne zasady *dwubramowego* resetowania hasła dla dowolnej roli administratora platformy Azure**. Te zasady mogą być inne niż te, które zostały zdefiniowane dla użytkowników i nie można zmienić tych zasad. Należy zawsze testować funkcję resetowania hasła jako użytkownik bez przypisanych ról administratora platformy Azure.

W przypadku zasad dwubramowych **administratorzy nie mogą korzystać z pytań zabezpieczających**.

Zasady dwóch bram wymagają dwóch danych uwierzytelniania, takich jak *adres e-mail*, *aplikacja uwierzytelniania*lub *numer telefonu*. Zasady dwóch bram są stosowane w następujących okolicznościach:

* Dotyczy to wszystkich następujących ról administratora platformy Azure:
  * Administrator pomocy technicznej
  * Administrator pomocy technicznej usługi
  * Administrator rozliczeń
  * Obsługa pomoc partnera
  * Obsługa SVR partnera
  * Administrator programu Exchange
  * Administrator programu Skype dla firm
  * Administrator użytkowników
  * Autorzy katalogów
  * Administrator globalny lub administrator firmy
  * Administrator programu SharePoint
  * Administrator zgodności
  * Administrator aplikacji
  * Administrator zabezpieczeń
  * Administrator ról uprzywilejowanych
  * Administrator usługi Intune
  * Administrator usługi serwera proxy aplikacji
  * Administrator systemu Dynamics 365
  * Administrator usługi Power BI
  * Administrator uwierzytelniania
  * Administrator uprzywilejowanego uwierzytelniania

* W przypadku upływu 30 dni od subskrypcji próbnej oraz
* Skonfigurowano domenę niestandardową dla dzierżawy usługi Azure AD, np. *contoso.com*; oraz
* Azure AD Connect synchronizuje tożsamości z katalogu lokalnego

### <a name="exceptions"></a>Wyjątki

Zasady pojedynczej bramy wymagają jednej części danych uwierzytelniania, takich jak adres e-mail lub numer telefonu. Zasady pojedynczej bramy są stosowane w następujących okolicznościach:

* Jest to w ciągu pierwszych 30 dni subskrypcji wersji próbnej; oraz
* Domena niestandardowa nie została skonfigurowana dla dzierżawy usługi Azure AD, więc jest używana wartość domyślna **. onmicrosoft.com*. Domyślna domena **. onmicrosoft.com* nie jest zalecana do użycia w środowisku produkcyjnym; lub
* Azure AD Connect nie synchronizuje tożsamości

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Zasady UserPrincipalName mające zastosowanie do wszystkich kont użytkowników

Każde konto użytkownika, które musi zalogować się do usługi Azure AD, musi mieć unikatową wartość atrybutu nazwy głównej użytkownika (UPN) skojarzoną z danym kontem. Poniższa tabela zawiera opis zasad dotyczących zarówno Active Directory Domain Services lokalnych kont użytkowników, które są synchronizowane z chmurą, jak i konta użytkowników tylko w chmurze:

| Właściwość | Wymagania UserPrincipalName |
| --- | --- |
| Dozwolone znaki |<ul> <li>A – Z</li> <li>a – z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Znaki nie są dozwolone |<ul> <li>Dowolny "\@ \" znak, który nie oddziela nazwy użytkownika z domeny.</li> <li>Nie może zawierać znaku kropki "." bezpośrednio poprzedzającego\@ \" "symbol</li></ul> |
| Ograniczenia długości |<ul> <li>Łączna długość nie może przekraczać 113 znaków</li><li>Przed "symbolem"\@ \" może znajdować się maksymalnie 64 znaków</li><li>Po "symbolu"\@ \" może znajdować się maksymalnie 48 znaków</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Password policies that only apply to cloud user accounts (Zasady haseł dotyczące tylko kont użytkowników w chmurze)

W poniższej tabeli opisano ustawienia zasad haseł stosowane do kont użytkowników, które są tworzone i zarządzane w usłudze Azure AD:

| Właściwość | Wymagania |
| --- | --- |
| Dozwolone znaki |<ul><li>A – Z</li><li>a – z</li><li>0 – 9</li> <li>@ # $% ^ & *-_! + = [] {} &#124; \: ",. ? / \`~ " ( ) ;</li> <li>puste miejsce</li></ul> |
| Znaki nie są dozwolone | Znaki Unicode. |
| Ograniczenia dotyczące haseł |<ul><li>Co najmniej 8 znaków i maksymalnie 256 znaków.</li><li>Wymaga trzech z czterech następujących elementów:<ul><li>Małe litery.</li><li>Wielkie litery.</li><li>Liczby (0-9).</li><li>Symbole (zobacz poprzednie ograniczenia dotyczące haseł).</li></ul></li></ul> |
| Czas wygaśnięcia hasła (maksymalny wiek hasła) |<ul><li>Wartość domyślna: **90** dni.</li><li>Wartość można skonfigurować za pomocą `Set-MsolPasswordPolicy` polecenia cmdlet z modułu Azure Active Directory programu Windows PowerShell.</li></ul> |
| Powiadomienie o wygaśnięciu hasła (gdy użytkownicy są powiadamiani o wygaśnięciu hasła) |<ul><li>Wartość domyślna: **14** dni (przed wygaśnięciem hasła).</li><li>Wartość można skonfigurować przy użyciu `Set-MsolPasswordPolicy` polecenia cmdlet.</li></ul> |
| Wygaśnięcie hasła (niech hasła nigdy nie wygasa) |<ul><li>Wartość domyślna: **false** (wskazuje, że hasło ma datę wygaśnięcia).</li><li>Wartość można skonfigurować dla poszczególnych kont użytkowników przy użyciu `Set-MsolUser` polecenia cmdlet.</li></ul> |
| Historia zmian haseł | *Nie* można ponownie użyć ostatniego hasła, gdy użytkownik zmieni hasło. |
| Historia resetowania hasła | Ostatniego hasła *można* użyć ponownie, gdy użytkownik resetuje zapomniane hasło. |
| Blokada konta | Po 10 nieudanych próbach logowania z nieprawidłowym hasłem użytkownik jest blokowany przez jedną minutę. Więcej nieprawidłowych prób logowania Zablokuj użytkownika w celu zwiększenia czasu trwania. [Inteligentna blokada](howto-password-smart-lockout.md) śledzi ostatnie trzy niewłaściwe skróty haseł, aby uniknąć zwiększenia licznika blokady dla tego samego hasła. Jeśli ktoś wprowadzi to samo złe hasło wielokrotnie, to zachowanie nie spowoduje zablokowania konta. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Ustawianie zasad wygasania haseł w usłudze Azure AD

*Administrator globalny* lub *administrator użytkownika* usługi firmy Microsoft w chmurze może użyć *moduł Microsoft Azure AD dla Windows PowerShell* , aby ustawić hasła użytkowników, które nie wygasną. Można również użyć poleceń cmdlet programu Windows PowerShell, aby usunąć konfigurację nigdy nie wygasa lub zobaczyć, które hasła użytkowników są ustawione na nigdy nie wygasają.

Te wskazówki odnoszą się do innych dostawców, takich jak usługa Intune i pakiet Office 365, które również polegają na usłudze Azure AD na potrzeby tożsamości i usług katalogowych. Wygaśnięcie hasła jest jedyną częścią zasad, które można zmienić.

> [!NOTE]
> Tylko hasła kont użytkowników, które nie są synchronizowane za poorednictwem synchronizacji katalogów, można skonfigurować tak, aby nie wygaśnie. Aby uzyskać więcej informacji na temat synchronizacji katalogów, zobacz [łączenie usługi AD z usługą Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Set or check the password policies by using PowerShell (Ustawianie i sprawdzanie zasad haseł za pomocą programu PowerShell)

Aby rozpocząć, [Pobierz i zainstaluj moduł Azure AD PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0) , a następnie [Połącz go z dzierżawą usługi Azure AD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples). Po zainstalowaniu modułu wykonaj następujące kroki, aby skonfigurować każde pole.

### <a name="check-the-expiration-policy-for-a-password"></a>Sprawdź zasady wygasania hasła

1. Nawiązywanie połączenia z programem Windows PowerShell przy użyciu poświadczeń administratora użytkownika lub administratora firmy.
1. Uruchom jedno z następujących poleceń:

   * Aby sprawdzić, czy hasło jednego użytkownika jest ustawione na nigdy nie wygasa, uruchom następujące polecenie cmdlet przy użyciu nazwy UPN (na przykład *\@contoso.ONMICROSOFT.com*) lub identyfikatora użytkownika, który chcesz sprawdzić:

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * Aby wyświetlić ustawienie **hasło nigdy nie wygasa** dla wszystkich użytkowników, uruchom następujące polecenie cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>Ustaw hasło na wygasające

1. Nawiązywanie połączenia z programem Windows PowerShell przy użyciu poświadczeń administratora użytkownika lub administratora firmy.
1. Wykonaj jedno z następujących poleceń:

   * Aby ustawić hasło jednego użytkownika w taki sposób, aby hasło wygasło, uruchom następujące polecenie cmdlet przy użyciu nazwy UPN lub identyfikatora użytkownika użytkownika:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * Aby ustawić hasła wszystkich użytkowników w organizacji w taki sposób, aby wygaśnie, użyj następującego polecenia cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>Ustaw hasło nigdy nie wygasa

1. Nawiązywanie połączenia z programem Windows PowerShell przy użyciu poświadczeń administratora użytkownika lub administratora firmy.
1. Wykonaj jedno z następujących poleceń:

   * Aby ustawić hasło jednego użytkownika tak, aby nigdy nie wygasło, uruchom następujące polecenie cmdlet przy użyciu nazwy UPN lub identyfikatora użytkownika użytkownika:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * Aby ustawić hasła wszystkich użytkowników w organizacji, aby nigdy nie wygasnąć, uruchom następujące polecenie cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > Hasła są ustawione `-PasswordPolicies DisablePasswordExpiration` na wartość okres ważności na `pwdLastSet` podstawie atrybutu. W oparciu o `pwdLastSet` atrybut, jeśli zmienisz datę wygaśnięcia na `-PasswordPolicies None`, wszystkie hasła, które mają `pwdLastSet` starszą niż 90 dni, wymagają od użytkownika zmiany ich przy następnym logowaniu. Ta zmiana może mieć wpływ na dużą liczbę użytkowników.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z usługą SSPR, zobacz [Samouczek: umożliwienie użytkownikom odblokowania konta lub resetowania haseł przy użyciu Azure Active Directory samoobsługowego resetowania hasła](tutorial-enable-sspr.md).

Jeśli masz problemy z SSPR, zobacz Rozwiązywanie problemów z funkcją samoobsługowego [resetowania hasła](active-directory-passwords-troubleshoot.md)
