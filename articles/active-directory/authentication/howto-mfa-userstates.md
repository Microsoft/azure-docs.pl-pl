---
title: Włącz Multi-Factor Authentication dla poszczególnych użytkowników — Azure Active Directory
description: Dowiedz się, jak włączyć usługę Azure Multi-Factor Authentication dla poszczególnych użytkowników, zmieniając stan użytkownika
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c51863cd95deff5e460707b004206f6fde54cac4
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84337754"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Włączanie usługi Azure Multi-Factor Authentication dla poszczególnych użytkowników w celu zabezpieczenia zdarzeń logowania

Istnieją dwa sposoby zabezpieczania zdarzeń logowania użytkowników przez wymaganie uwierzytelniania wieloskładnikowego w usłudze Azure AD. Pierwszy i preferowany opcja to skonfigurowanie zasad dostępu warunkowego, które wymagają uwierzytelniania wieloskładnikowego w określonych warunkach. Drugą opcją jest włączenie każdego użytkownika dla Multi-Factor Authentication platformy Azure. Gdy użytkownicy są włączani indywidualnie, przeprowadzają uwierzytelnianie wieloskładnikowe przy każdym logowaniu (z pewnymi wyjątkami, takimi jak logowanie przy użyciu zaufanych adresów IP lub włączenie funkcji _zapamiętanych urządzeń_ ).

> [!NOTE]
> Zalecanym podejściem jest włączenie Multi-Factor Authentication platformy Azure korzystającej z zasad dostępu warunkowego. Zmiana stanu użytkowników nie jest już zalecana, chyba że licencje nie obejmują dostępu warunkowego, ponieważ wymaga ona od użytkowników wykonywania uwierzytelniania MFA przy każdym logowaniu. Aby rozpocząć korzystanie z dostępu warunkowego, zobacz [Samouczek: Zabezpieczanie zdarzeń logowania użytkowników przy użyciu usługi Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> W przypadku bezpłatnych dzierżawców usługi Azure AD bez dostępu warunkowego można [użyć domyślnych ustawień zabezpieczeń, aby chronić użytkowników](../fundamentals/concept-fundamentals-security-defaults.md).

## <a name="azure-multi-factor-authentication-user-states"></a>Stany użytkownika Multi-Factor Authentication platformy Azure

Konta użytkowników na platformie Azure Multi-Factor Authentication mają trzy różne stany:

> [!IMPORTANT]
> Włączenie Multi-Factor Authentication platformy Azure za pomocą zasad dostępu warunkowego nie powoduje zmiany stanu użytkownika. Nie należy alarmować, jeśli użytkownicy są wyłączeni. Dostęp warunkowy nie zmienia stanu.
>
> **Nie należy włączać ani wymuszać użytkowników, jeśli są używane zasady dostępu warunkowego.**

| Stan | Opis | Uwzględnione aplikacje nie korzystające z przeglądarki | Uwzględnione aplikacje przeglądarki | Zmodyfikowane nowoczesne uwierzytelnianie |
|:---:| --- |:---:|:--:|:--:|
| Disabled (Wyłączony) | Domyślny stan nowego użytkownika, który nie jest zarejestrowany w usłudze Azure Multi-Factor Authentication. | Nie | Nie | Nie |
| Enabled (Włączony) | Użytkownik został zarejestrowany w usłudze Azure Multi-Factor Authentication Otrzymują monit o zarejestrowanie się przy następnym logowaniu. | Nie.  Nadal działają do momentu zakończenia procesu rejestracji. | Tak. Po wygaśnięciu sesji wymagana jest rejestracja w usłudze Azure Multi-Factor Authentication.| Tak. Po wygaśnięciu tokenu dostępu wymagana jest rejestracja w usłudze Azure Multi-Factor Authentication. |
| Enforced (Wymuszony) | Użytkownik został zarejestrowany i ukończył proces rejestracji w usłudze Azure Multi-Factor Authentication. | Tak. Aplikacje wymagają haseł aplikacji. | Tak. Usługa Azure Multi-Factor Authentication jest wymagana podczas logowania. | Tak. Usługa Azure Multi-Factor Authentication jest wymagana podczas logowania. |

Stan użytkownika wskazuje, czy administrator zarejestrował je na platformie Azure Multi-Factor Authentication i czy zakończył proces rejestracji.

Wszyscy użytkownicy zaczynają *wyłączać*. Po zarejestrowaniu użytkowników w usłudze Azure Multi-Factor Authentication ich stan zmieni się na *włączone*. Gdy użytkownicy będą mogli się zalogować i zakończyć proces rejestracji, ich stan zmieni się na *wymuszone*.

> [!NOTE]
> Jeśli usługa MFA jest ponownie włączona w obiekcie użytkownika, który ma już szczegóły rejestracji, na przykład telefon lub poczta e-mail, Administratorzy muszą ponownie zarejestrować usługę MFA za pośrednictwem Azure Portal lub PowerShell. Jeśli użytkownik nie zostanie ponownie zarejestrowany, jego stan MFA nie przechodzi z *włączonego* do *wymuszanego* w interfejsie użytkownika zarządzania uwierzytelnianiem usługi MFA.

## <a name="view-the-status-for-a-user"></a>Wyświetlanie stanu użytkownika

Wykonaj następujące kroki, aby uzyskać dostęp do strony Azure Portal, na której można wyświetlać Stany użytkowników i zarządzać nimi:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator.
1. Wyszukaj i wybierz pozycję *Azure Active Directory*, a następnie wybierz pozycję **Użytkownicy**  >  **Wszyscy użytkownicy**.
1. Wybierz **Multi-Factor Authentication**. Może być konieczne przewinięcie w prawo, aby wyświetlić tę opcję menu. Wybierz Poniższy przykładowy zrzut ekranu, aby zobaczyć pełne okno Azure Portal i lokalizację menu:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Wybierz Multi-Factor Authentication z okna użytkowników w usłudze Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Zostanie otwarta nowa strona, która wyświetla stan użytkownika, jak pokazano w poniższym przykładzie.
   ![Zrzut ekranu pokazujący przykładowe informacje o stanie użytkownika dotyczące usługi Azure Multi-Factor Authentication](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Zmiana stanu użytkownika

Aby zmienić stan Multi-Factor Authentication platformy Azure dla użytkownika, wykonaj następujące czynności:

1. Wykonaj powyższe kroki, aby przejść do strony **Użytkownicy** usługi Azure Multi-Factor Authentication.
1. Znajdź użytkownika, który ma zostać włączony dla Multi-Factor Authentication platformy Azure. Być może trzeba będzie zmienić widok u góry, aby **Użytkownicy**.
   ![Wybierz użytkownika, którego stan chcesz zmienić z karty użytkownicy](./media/howto-mfa-userstates/enable1.png)
1. Zaznacz pole wyboru obok nazw użytkowników, dla których chcesz zmienić stan.
1. Po prawej stronie w obszarze **szybkie kroki**wybierz pozycję **Włącz** lub **Wyłącz**. W poniższym przykładzie użytkownik *Jan Kowalski* ma pole wyboru obok nazwy i jest włączone do użycia: ![ Włącz wybranego użytkownika, klikając pozycję Włącz w menu szybkie kroki](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Włączone* użytkownicy są automatycznie przełączane w celu *wymuszenia* rejestracji w usłudze Azure Multi-Factor Authentication. Nie zmieniaj ręcznie stanu użytkownika na *wymuszone*.

1. Potwierdź wybór w otwartym oknie podręcznym.

Po włączeniu użytkowników Powiadom ich pocztą e-mail. Poinformuj użytkowników, że zostanie wyświetlony monit o zarejestrowanie się przy następnym logowaniu. Ponadto, jeśli w organizacji są używane aplikacje nie korzystające z przeglądarki, które nie obsługują nowoczesnego uwierzytelniania, muszą one tworzyć hasła aplikacji. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem użytkownika końcowego Multi-Factor Authentication platformy Azure](../user-help/multi-factor-authentication-end-user.md) , aby ułatwić Ci rozpoczęcie pracy.

## <a name="change-state-using-powershell"></a>Zmień stan przy użyciu programu PowerShell

Aby zmienić stan użytkownika przy użyciu [programu Azure AD PowerShell](/powershell/azure/overview), należy zmienić `$st.State` parametr dla konta użytkownika. Istnieją trzy możliwe stany konta użytkownika:

* *Włączone*
* *Enforced (Wymuszony)*
* *Wyłączone*  

Nie przenoś użytkowników bezpośrednio do stanu *wymuszonego* . W takim przypadku aplikacje niekorzystające z przeglądarki przestaną działać, ponieważ użytkownik nie przeszedł za pośrednictwem usługi Azure Multi-Factor Authentication Registration i uzyskał [hasło aplikacji](howto-mfa-mfasettings.md#app-passwords).

Aby rozpocząć, zainstaluj moduł *MSOnline* za pomocą polecenia [Install-module](/powershell/module/powershellget/install-module) w następujący sposób:

```PowerShell
Install-Module MSOnline
```

Następnie połącz się za pomocą polecenia [Connect-MsolService](/powershell/module/msonline/connect-msolservice):

```PowerShell
Connect-MsolService
```

Poniższy przykładowy skrypt programu PowerShell umożliwia uwierzytelnianie wieloskładnikowe dla pojedynczego użytkownika o nazwie *bsimon@contoso.com* :

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

Korzystanie z programu PowerShell jest dobrym rozwiązaniem w przypadku konieczności zbiorczego włączania użytkowników. Poniższy skrypt przechodzi przez listę użytkowników i włącza uwierzytelnianie wieloskładnikowe na swoich kontach. Zdefiniuj konta użytkowników ustawione w pierwszym wierszu w `$users` następujący sposób:

   ```PowerShell
   # Define your list of users to update state in bulk
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"

   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

Aby wyłączyć uwierzytelnianie wieloskładnikowe, Poniższy przykład pobiera użytkownika z poleceniem [Get-MsolUser](/powershell/module/msonline/get-msoluser), a następnie usuwa wszystkie *StrongAuthenticationRequirements* ustawione dla określonego użytkownika przy użyciu polecenia [Set-MsolUser](/powershell/module/msonline/set-msoluser):

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

Można również bezpośrednio wyłączyć uwierzytelnianie wieloskładnikowe dla użytkownika przy użyciu polecenia [Set-MsolUser](/powershell/module/msonline/set-msoluser) w następujący sposób:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Konwertowanie użytkowników z usługi MFA na użytkownika na podstawie dostępu warunkowego

Poniższe środowisko programu PowerShell może pomóc w konwersji do usługi Azure Multi-Factor Authentication opartej na dostęp warunkowy.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Niedawno zmieniono zachowanie i ten skrypt programu PowerShell. Wcześniej skrypt zapisany z metod MFA, wyłączył uwierzytelnianie MFA i przywrócił metody. Nie jest to już konieczne, ponieważ domyślne zachowanie funkcji Disable nie czyści metod.
>
> Jeśli usługa MFA jest ponownie włączona w obiekcie użytkownika, który ma już szczegóły rejestracji, na przykład telefon lub poczta e-mail, Administratorzy muszą ponownie zarejestrować usługę MFA za pośrednictwem Azure Portal lub PowerShell. Jeśli użytkownik nie zostanie ponownie zarejestrowany, jego stan MFA nie przechodzi z *włączonego* do *wymuszanego* w interfejsie użytkownika zarządzania uwierzytelnianiem usługi MFA.

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować ustawienia usługi Azure Multi-Factor Authentication, takie jak Zaufane adresy IP, niestandardowe wiadomości głosowe i alerty oszustwa, zobacz [Konfigurowanie ustawień usługi azure Multi-Factor Authentication](howto-mfa-mfasettings.md). Aby zarządzać ustawieniami użytkownika dla usługi Azure Multi-Factor Authentication, zobacz [Zarządzanie ustawieniami użytkownika przy użyciu usługi azure Multi-Factor Authentication](howto-mfa-userdevicesettings.md).

Aby zrozumieć, dlaczego użytkownik otrzymał monit lub nie był monitowany o przeprowadzenie uwierzytelniania MFA, zobacz [Azure Multi-Factor Authentication Reports](howto-mfa-reporting.md).
