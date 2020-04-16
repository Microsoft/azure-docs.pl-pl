---
title: Zasady samoobsługowego resetowania haseł — usługa Azure Active Directory
description: Dowiedz się więcej o różnych opcjach samoobsługowego resetowania haseł usługi Azure Active Directory
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
ms.openlocfilehash: 5f5987bee82dc22d3742cb5d87040930e5d2c52d
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393030"
---
# <a name="self-service-password-reset-policies-and-restrictions-in-azure-active-directory"></a>Zasady i ograniczenia samoobsługowego resetowania haseł w usłudze Azure Active Directory

W tym artykule opisano zasady haseł i wymagania dotyczące złożoności skojarzone z kontami użytkowników w dzierżawie usługi Azure Active Directory (Azure AD).

## <a name="administrator-reset-policy-differences"></a>Administrator reset policy differences (Różnice zasad resetowania administratora)

**Firma Microsoft wymusza silne domyślne zasady resetowania haseł *dwubramkowych* dla dowolnej roli administratora platformy Azure**. Ta zasada może się różnić od tej zdefiniowanej dla użytkowników i nie można zmienić tej zasady. Należy zawsze przetestować funkcję resetowania hasła jako użytkownika bez przypisanych ról administratora platformy Azure.

W przypadku zasad dwóch **bramek administratorzy nie mają możliwości używania pytań zabezpieczających.**

Zasady dwubramkowe wymagają dwóch elementów danych uwierzytelniania, takich jak *adres e-mail,* *aplikacja uwierzytelniająca*lub *numer telefonu.* Zasady dwubramkowe mają zastosowanie w następujących okolicznościach:

* Dotyczy to wszystkich następujących ról administratora platformy Azure:
  * Administrator działu pomocy technicznej
  * Administrator pomocy technicznej usługi
  * Administrator rozliczeń
  * Pomoc techniczna poziomu 1 partnera
  * Obsługa partnerów Tier2
  * Administrator programu Exchange
  * Administrator programu Skype dla firm
  * Administrator użytkownika
  * Autorzy katalogów
  * Administrator globalny lub administrator firmy
  * Administrator programu SharePoint
  * Administrator zgodności
  * Administrator aplikacji
  * Administrator zabezpieczeń
  * Administrator ról uprzywilejowanych
  * Administrator usługi Intune
  * Administrator usługi proxy aplikacji
  * Administrator dynamics 365
  * Administrator usługi Power BI
  * Administrator uwierzytelniania
  * Administrator uwierzytelniania uprzywilejowanego

* Jeśli w wersji próbnej upłynęło 30 dni; Lub
* Domena niestandardowa została skonfigurowana dla dzierżawy usługi Azure AD, takiej jak *contoso.com;* Lub
* Usługa Azure AD Connect synchronizuje tożsamości z katalogu lokalnego

### <a name="exceptions"></a>Wyjątki

Zasady jednej bramki wymagają jednego fragmentu danych uwierzytelniania, takiego jak adres e-mail lub numer telefonu. Zasady jednej bramy mają zastosowanie w następujących okolicznościach:

* To w ciągu pierwszych 30 dni od subskrypcji próbnej; Lub
* Domena niestandardowa nie została skonfigurowana dla dzierżawy usługi Azure AD, więc używa domyślnej **onmicrosoft.com*. Domyślna*domena *onmicrosoft.com* nie jest zalecana do użytku produkcyjnego; I
* Usługa Azure AD Connect nie synchronizuje tożsamości

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Zasady UserPrincipalName mające zastosowanie do wszystkich kont użytkowników

Każde konto użytkownika, które musi zalogować się do usługi Azure AD, musi mieć unikatową nazwę główną użytkownika (UPN) wartość atrybutu skojarzoną z ich kontem. W poniższej tabeli przedstawiono zasady stosowane zarówno do lokalnych kont użytkowników Usług Domenowych Active Directory, które są synchronizowane z chmurą, jak i do kont użytkowników tylko w chmurze:

| Właściwość | Wymagania dotyczące userPrincipalName |
| --- | --- |
| Dozwolone znaki |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Znaki niedozwolone |<ul> <li>Dowolny\@ \" znak ", który nie oddziela nazwy użytkownika od domeny.</li> <li>Nie może zawierać znaku kropki "."\@ \" bezpośrednio poprzedzającego symbol "</li></ul> |
| Wiązania długości |<ul> <li>Całkowita długość nie może przekraczać 113 znaków</li><li>Przed symbolem " może znajdować się\@ \" maksymalnie 64 znaki.</li><li>Po symbolu " może znajdować się\@ \" maksymalnie 48 znaków.</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Password policies that only apply to cloud user accounts (Zasady haseł dotyczące tylko kont użytkowników w chmurze)

W poniższej tabeli opisano ustawienia zasad haseł stosowane do kont użytkowników, które są tworzone i zarządzane w usłudze Azure AD:

| Właściwość | Wymagania |
| --- | --- |
| Dozwolone znaki |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = ] { } &#124; \ : ' , . ? / \`~ " ( ) ;</li> <li>puste miejsce</li></ul> |
| Znaki niedozwolone | Znaki Unicode. |
| Ograniczenia haseł |<ul><li>Co najmniej 8 znaków i maksymalnie 256 znaków.</li><li>Wymaga trzech z czterech z następujących czynności:<ul><li>Małe litery.</li><li>Wielkie litery.</li><li>Liczby (0-9).</li><li>Symbole (patrz poprzednie ograniczenia haseł).</li></ul></li></ul> |
| Czas wygaśnięcia hasła (maksymalny wiek hasła) |<ul><li>Wartość domyślna: **90** dni.</li><li>Wartość jest konfigurowalna `Set-MsolPasswordPolicy` przy użyciu polecenia cmdlet z modułu usługi Azure Active Directory dla programu Windows PowerShell.</li></ul> |
| Powiadomienie o wygaśnięciu hasła (gdy użytkownicy są powiadamiani o wygaśnięciu hasła) |<ul><li>Wartość domyślna: **14** dni (przed wygaśnięciem hasła).</li><li>Wartość można konfigurować `Set-MsolPasswordPolicy` za pomocą polecenia cmdlet.</li></ul> |
| Wygaśnięcie hasła (niech hasła nigdy nie wygasną) |<ul><li>Wartość domyślna: **false** (wskazuje, że hasło ma datę wygaśnięcia).</li><li>Wartość można skonfigurować dla kont poszczególnych `Set-MsolUser` użytkowników przy użyciu polecenia cmdlet.</li></ul> |
| Historia zmiany hasła | Ostatniego hasła *nie można* użyć ponownie, gdy użytkownik zmieni hasło. |
| Historia resetowania hasła | Ostatnie hasło *może* być użyte ponownie, gdy użytkownik zresetuje zapomniane hasło. |
| Blokada konta | Po 10 nieudanych próbach logowania przy niewłaściwym haśle użytkownik jest zablokowany na minutę. Dalsze niepoprawne próby logowania zablokować użytkownika na dłuższy czas. [Inteligentna blokada](howto-password-smart-lockout.md) śledzi trzy ostatnie złe skróty haseł, aby uniknąć zwiększania licznika blokady dla tego samego hasła. Jeśli ktoś wprowadzi to samo złe hasło wiele razy, to zachowanie nie spowoduje, że konto zostanie zablokowane. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Ustawianie zasad wygasania haseł w usłudze Azure AD

*Administrator globalny* lub *administrator użytkownika* usługi w chmurze firmy Microsoft może użyć *modułu Microsoft Azure AD module dla programu Windows PowerShell,* aby ustawić hasła użytkowników, aby nie wygasały. Polecenia cmdlet programu Windows PowerShell można również użyć, aby usunąć konfigurację nigdy nie wygasa lub zobaczyć, które hasła użytkowników nigdy nie wygasną.

Te wskazówki dotyczą innych dostawców, takich jak Intune i Office 365, które również opierają się na usłudze Azure AD dla usług tożsamości i katalogów. Wygasanie hasła jest jedyną częścią zasad, które można zmienić.

> [!NOTE]
> Tylko hasła dla kont użytkowników, które nie są synchronizowane za pomocą synchronizacji katalogów, można skonfigurować tak, aby nie wygasały. Aby uzyskać więcej informacji na temat synchronizacji katalogów, zobacz [Łączenie usługi AD z usługą Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Set or check the password policies by using PowerShell (Ustawianie i sprawdzanie zasad haseł za pomocą programu PowerShell)

Aby rozpocząć, [pobierz i zainstaluj moduł programu Azure AD PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Po zainstalowaniu modułu należy wykonać następujące czynności, aby skonfigurować każde pole.

### <a name="check-the-expiration-policy-for-a-password"></a>Sprawdź zasady wygasania hasła

1. Połącz się z programem Windows PowerShell przy użyciu poświadczeń administratora użytkownika lub administratora firmy.
1. Uruchom jedno z następujących poleceń:

   * Aby sprawdzić, czy hasło pojedynczego użytkownika nigdy nie wygaśnie, uruchom następujące polecenie cmdlet przy użyciu nazwy UPN (na przykład *\@aprilr contoso.onmicrosoft.com)* lub identyfikatora użytkownika użytkownika, który chcesz sprawdzić:

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * Aby wyświetlić ustawienie **Hasło nigdy nie wygasa** dla wszystkich użytkowników, uruchom następujące polecenie cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>Ustawianie hasła, które ma wygasnąć

1. Połącz się z programem Windows PowerShell przy użyciu poświadczeń administratora użytkownika lub administratora firmy.
1. Wykonaj jedno z następujących poleceń:

   * Aby ustawić hasło jednego użytkownika tak, aby hasło wygasło, uruchom następujące polecenie cmdlet przy użyciu nazwy UPN lub identyfikatora użytkownika:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * Aby ustawić hasła wszystkich użytkowników w organizacji tak, aby wygasły, użyj następującego polecenia cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>Ustawianie hasła, które nigdy nie wygasa

1. Połącz się z programem Windows PowerShell przy użyciu poświadczeń administratora użytkownika lub administratora firmy.
1. Wykonaj jedno z następujących poleceń:

   * Aby ustawić, że hasło jednego użytkownika nigdy nie wygaśnie, uruchom następujące polecenie cmdlet przy użyciu nazwy UPN lub identyfikatora użytkownika użytkownika:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * Aby ustawić, że hasła wszystkich użytkowników w organizacji nigdy nie wygasną, uruchom następujące polecenie cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > Hasła ustawione `-PasswordPolicies DisablePasswordExpiration` na wiek na `pwdLastSet` podstawie atrybutu. Na podstawie `pwdLastSet` atrybutu, jeśli zmienisz `-PasswordPolicies None`wygaśnięcie na , `pwdLastSet` wszystkie hasła, które mają starsze niż 90 dni wymagają od użytkownika, aby zmienić je przy następnym loguje. Ta zmiana może mieć wpływ na dużą liczbę użytkowników.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z samoobsługowego resetowania haseł, zobacz [Samouczek: Umożliwianie użytkownikom odblokowywania konta lub resetowania haseł przy użyciu samoobsługowego resetowania hasła usługi Azure Active Directory](tutorial-enable-sspr.md).

Jeśli ty lub użytkownicy mają problemy z samoobsługowym łazem samoobsługowym, zobacz Rozwiązywanie problemów z [samoobsługowym resetowaniem hasła](active-directory-passwords-troubleshoot.md)
