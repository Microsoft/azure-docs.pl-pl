---
title: Zasady samoobsługowego resetowania haseł — Azure Active Directory
description: Informacje na temat różnych opcji zasad samoobsługowego resetowania hasła Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 990d8ef275982b6d70c51819e47b33f543345023
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91531279"
---
# <a name="password-policies-and-account-restrictions-in-azure-active-directory"></a>Zasady haseł i ograniczenia kont w Azure Active Directory

W Azure Active Directory (Azure AD) istnieją zasady haseł, które definiują ustawienia, takie jak złożoność hasła, długość lub wiek. Istnieje również zasada, która definiuje akceptowalne znaki i długość dla nazw użytkowników.

Gdy funkcja samoobsługowego resetowania hasła (SSPR) jest używana do zmiany lub resetowania hasła w usłudze Azure AD, sprawdzane są zasady haseł. Jeśli hasło nie spełnia wymagań zasad, użytkownik zostanie poproszony o ponowienie próby. Administratorzy platformy Azure mają pewne ograniczenia dotyczące używania SSPR, które różnią się od zwykłych kont użytkowników.

W tym artykule opisano ustawienia zasad haseł i wymagania dotyczące złożoności skojarzone z kontami użytkowników w dzierżawie usługi Azure AD oraz sposób korzystania z programu PowerShell w celu sprawdzania lub ustawiania ustawień wygasania haseł.

## <a name="username-policies"></a><a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Zasady nazw użytkowników

Każde konto, które loguje się do usługi Azure AD, musi mieć unikatową wartość atrybutu nazwy głównej użytkownika (UPN) skojarzoną z danym kontem. W środowiskach hybrydowych ze środowiskiem lokalnym Active Directory Domain Services (AD DS) synchronizowanym z usługą Azure AD przy użyciu Azure AD Connect domyślnie nazwa UPN usługi Azure AD jest ustawiona na nazwę UPN Premium.

Poniższa tabela zawiera opis zasad nazw, które są stosowane do lokalnych kont AD DS, które są synchronizowane z usługą Azure AD, oraz dla kont użytkowników tylko w chmurze utworzonych bezpośrednio w usłudze Azure AD:

| Właściwość | Wymagania UserPrincipalName |
| --- | --- |
| Dozwolone znaki |<ul> <li>A – Z</li> <li>a – z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Znaki nie są dozwolone |<ul> <li>Dowolny " \@ \" znak, który nie oddziela nazwy użytkownika z domeny.</li> <li>Nie może zawierać znaku kropki "." bezpośrednio poprzedzającego " \@ \" symbol</li></ul> |
| Ograniczenia długości |<ul> <li>Łączna długość nie może przekraczać 113 znaków</li><li>Przed " \@ symbolem" może znajdować się maksymalnie 64 znaków \"</li><li>Po "symbolu" może znajdować się maksymalnie 48 znaków \@ \"</li></ul> |

## <a name="azure-ad-password-policies"></a><a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Zasady haseł usługi Azure AD

Zasady dotyczące haseł są stosowane do wszystkich kont użytkowników, które są tworzone i zarządzane bezpośrednio w usłudze Azure AD. Nie można zmodyfikować tych zasad haseł, chociaż można [skonfigurować niestandardowe hasła zabronione dla ochrony hasłem usługi Azure AD](tutorial-configure-custom-password-protection.md).

Zasady haseł nie mają zastosowania do kont użytkowników synchronizowanych z lokalnego środowiska AD DS przy użyciu Azure AD Connect, o ile nie zostanie włączona EnforceCloudPasswordPolicyForPasswordSyncedUsers.

Zdefiniowane są następujące opcje zasad haseł:

| Właściwość | Wymagania |
| --- | --- |
| Dozwolone znaki |<ul><li>A – Z</li><li>a – z</li><li>0 – 9</li> <li>@ # $% ^ & *-_! + = [] {} &#124; \: ",. ? / \` ~ " ( ) ;</li> <li>puste miejsce</li></ul> |
| Znaki nie są dozwolone | Znaki Unicode. |
| Ograniczenia dotyczące haseł |<ul><li>Co najmniej 8 znaków i maksymalnie 256 znaków.</li><li>Wymaga trzech z czterech następujących elementów:<ul><li>Małe litery.</li><li>Wielkie litery.</li><li>Liczby (0-9).</li><li>Symbole (zobacz poprzednie ograniczenia dotyczące haseł).</li></ul></li></ul> |
| Czas wygaśnięcia hasła (maksymalny wiek hasła) |<ul><li>Wartość domyślna: **90** dni.</li><li>Wartość można skonfigurować za pomocą `Set-MsolPasswordPolicy` polecenia cmdlet z modułu Azure Active Directory programu Windows PowerShell.</li></ul> |
| Powiadomienie o wygaśnięciu hasła (gdy użytkownicy są powiadamiani o wygaśnięciu hasła) |<ul><li>Wartość domyślna: **14** dni (przed wygaśnięciem hasła).</li><li>Wartość można skonfigurować przy użyciu `Set-MsolPasswordPolicy` polecenia cmdlet.</li></ul> |
| Wygaśnięcie hasła (niech hasła nigdy nie wygasa) |<ul><li>Wartość domyślna: **false** (wskazuje, że hasło ma datę wygaśnięcia).</li><li>Wartość można skonfigurować dla poszczególnych kont użytkowników przy użyciu `Set-MsolUser` polecenia cmdlet.</li></ul> |
| Historia zmian haseł | *Nie* można ponownie użyć ostatniego hasła, gdy użytkownik zmieni hasło. |
| Historia resetowania hasła | Ostatniego hasła *można* użyć ponownie, gdy użytkownik resetuje zapomniane hasło. |
| Blokada konta | Po 10 nieudanych próbach logowania z nieprawidłowym hasłem użytkownik jest blokowany przez jedną minutę. Więcej nieprawidłowych prób logowania Zablokuj użytkownika w celu zwiększenia czasu trwania. [Inteligentna blokada](howto-password-smart-lockout.md) śledzi ostatnie trzy niewłaściwe skróty haseł, aby uniknąć zwiększenia licznika blokady dla tego samego hasła. Jeśli ktoś wprowadzi to samo złe hasło wielokrotnie, to zachowanie nie spowoduje zablokowania konta. |

## <a name="administrator-reset-policy-differences"></a>Administrator reset policy differences (Różnice zasad resetowania administratora)

Domyślnie konta administratorów mają włączoną funkcję samoobsługowego resetowania hasła i wymuszane są *silne domyślne zasady* resetowania hasła. Te zasady mogą być inne niż te, które zostały zdefiniowane dla użytkowników i nie można zmienić tych zasad. Należy zawsze testować funkcję resetowania hasła jako użytkownik bez przypisanych ról administratora platformy Azure.

W przypadku zasad dwubramowych Administratorzy nie mogą korzystać z pytań zabezpieczających.

Zasady dwóch bram wymagają dwóch danych uwierzytelniania, takich jak adres e-mail, aplikacja uwierzytelniania lub numer telefonu. Zasady dwóch bram są stosowane w następujących okolicznościach:

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

Można wyłączyć korzystanie z SSPR dla kont administratorów za pomocą polecenia cmdlet [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) programu PowerShell. `-SelfServePasswordResetEnabled $False`Parametr wyłącza SSPR dla administratorów.

### <a name="exceptions"></a>Wyjątki

Zasady pojedynczej bramy wymagają jednej części danych uwierzytelniania, takich jak adres e-mail lub numer telefonu. Zasady pojedynczej bramy są stosowane w następujących okolicznościach:

* Jest to w ciągu pierwszych 30 dni subskrypcji wersji próbnej; oraz
* Domena niestandardowa nie została skonfigurowana dla dzierżawy usługi Azure AD, więc jest używana wartość domyślna **. onmicrosoft.com*. Domyślna domena **. onmicrosoft.com* nie jest zalecana do użycia w środowisku produkcyjnym; lub
* Azure AD Connect nie synchronizuje tożsamości

## <a name="password-expiration-policies"></a><a name="set-password-expiration-policies-in-azure-ad"></a>Zasady wygasania haseł

*Administrator globalny* lub *administrator użytkownika* może użyć [moduł Microsoft Azure AD dla Windows PowerShell](/powershell/module/Azuread/?view=azureadps-2.0) , aby ustawić hasła użytkowników, które nie wygasną.

Można również użyć poleceń cmdlet programu PowerShell, aby usunąć konfigurację nigdy nie wygasa lub zobaczyć, które hasła użytkowników są ustawione na nigdy nie wygasają.

Te wskazówki dotyczą innych dostawców, takich jak usługa Intune i Microsoft 365, które również polegają na usłudze Azure AD na potrzeby tożsamości i usług katalogowych. Wygaśnięcie hasła jest jedyną częścią zasad, które można zmienić.

> [!NOTE]
> Tylko hasła kont użytkowników, które nie są zsynchronizowane za poorednictwem Azure AD Connect, można skonfigurować tak, aby nie wygasać. Aby uzyskać więcej informacji na temat synchronizacji katalogów, zobacz [łączenie usługi AD z usługą Azure AD](../hybrid/whatis-hybrid-identity.md).

### <a name="set-or-check-the-password-policies-by-using-powershell"></a>Set or check the password policies by using PowerShell (Ustawianie i sprawdzanie zasad haseł za pomocą programu PowerShell)

Aby rozpocząć, [Pobierz i zainstaluj moduł Azure AD PowerShell](/powershell/module/Azuread/?view=azureadps-2.0) , a następnie [Połącz go z dzierżawą usługi Azure AD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples).

Po zainstalowaniu modułu wykonaj następujące kroki, aby wykonać każde zadanie zgodnie z wymaganiami.

### <a name="check-the-expiration-policy-for-a-password"></a>Sprawdź zasady wygasania hasła

1. Otwórz wiersz polecenia programu PowerShell i [Nawiąż połączenie z dzierżawą usługi Azure AD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples) przy użyciu konta *administratora globalnego* lub *administratora użytkowników* .
1. Uruchom jedno z następujących poleceń dla pojedynczego użytkownika lub dla wszystkich użytkowników:

   * Aby sprawdzić, czy hasło jednego użytkownika jest ustawione na nigdy nie wygasa, uruchom następujące polecenie cmdlet. Zastąp ciąg `<user ID>` identyfikatorem użytkownika, który chcesz sprawdzić, na przykład *driley \@ contoso.onmicrosoft.com*:

       ```powershell
       Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

   * Aby wyświetlić ustawienie **hasło nigdy nie wygasa** dla wszystkich użytkowników, uruchom następujące polecenie cmdlet:

       ```powershell
       Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

### <a name="set-a-password-to-expire"></a>Ustaw hasło na wygasające

1. Otwórz wiersz polecenia programu PowerShell i [Nawiąż połączenie z dzierżawą usługi Azure AD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples) przy użyciu konta *administratora globalnego* lub *administratora użytkowników* .
1. Uruchom jedno z następujących poleceń dla pojedynczego użytkownika lub dla wszystkich użytkowników:

   * Aby ustawić hasło jednego użytkownika w taki sposób, aby hasło wygasło, uruchom następujące polecenie cmdlet. Zastąp `<user ID>` identyfikatorem użytkownika, który chcesz sprawdzić, na przykład *driley \@ contoso.onmicrosoft.com*

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
       ```

   * Aby ustawić hasła wszystkich użytkowników w organizacji w taki sposób, aby wygaśnie, użyj następującego polecenia cmdlet:

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
       ```

### <a name="set-a-password-to-never-expire"></a>Ustaw hasło nigdy nie wygasa

1. Otwórz wiersz polecenia programu PowerShell i [Nawiąż połączenie z dzierżawą usługi Azure AD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples) przy użyciu konta *administratora globalnego* lub *administratora użytkowników* .
1. Uruchom jedno z następujących poleceń dla pojedynczego użytkownika lub dla wszystkich użytkowników:

   * Aby ustawić hasło jednego użytkownika tak, aby nigdy nie wygasło, uruchom następujące polecenie cmdlet. Zastąp `<user ID>` identyfikatorem użytkownika, który chcesz sprawdzić, na przykład *driley \@ contoso.onmicrosoft.com*

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
       ```

   * Aby ustawić hasła wszystkich użytkowników w organizacji, aby nigdy nie wygasnąć, uruchom następujące polecenie cmdlet:

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
       ```

   > [!WARNING]
   > Hasła są ustawione na wartość `-PasswordPolicies DisablePasswordExpiration` okres ważności na podstawie `pwdLastSet` atrybutu. W oparciu o `pwdLastSet` atrybut, jeśli zmienisz datę wygaśnięcia na `-PasswordPolicies None` , wszystkie hasła, które mają `pwdLastSet` starszą niż 90 dni, wymagają od użytkownika zmiany ich przy następnym logowaniu. Ta zmiana może mieć wpływ na dużą liczbę użytkowników.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z usługą SSPR, zobacz [Samouczek: umożliwienie użytkownikom odblokowania konta lub resetowania haseł przy użyciu Azure Active Directory samoobsługowego resetowania hasła](tutorial-enable-sspr.md).

Jeśli masz problemy z SSPR, zobacz Rozwiązywanie problemów z funkcją samoobsługowego [resetowania hasła](active-directory-passwords-troubleshoot.md)
