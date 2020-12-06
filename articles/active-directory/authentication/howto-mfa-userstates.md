---
title: Włącz Multi-Factor Authentication dla poszczególnych użytkowników — Azure Active Directory
description: Dowiedz się, jak włączyć Multi-Factor Authentication usługi Azure AD dla poszczególnych użytkowników, zmieniając stan użytkownika
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 749829f641119273813d3c8ca826daf8b4dc4d11
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742667"
---
# <a name="enable-per-user-azure-ad-multi-factor-authentication-to-secure-sign-in-events"></a>Włącz usługę Azure AD Multi-Factor Authentication dla poszczególnych użytkowników, aby zabezpieczyć zdarzenia logowania

Aby zabezpieczyć zdarzenia logowania użytkowników w usłudze Azure AD, można wymagać uwierzytelniania wieloskładnikowego (MFA). Zalecanym podejściem do ochrony użytkowników jest włączenie Multi-Factor Authentication usługi Azure AD przy użyciu zasad dostępu warunkowego. Dostęp warunkowy jest funkcją Azure AD — wersja Premium P1 lub P2, która umożliwia stosowanie reguł, które wymagają uwierzytelniania MFA w określonych scenariuszach. Aby rozpocząć korzystanie z dostępu warunkowego, zobacz [Samouczek: Zabezpieczanie zdarzeń logowania użytkowników przy użyciu usługi Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

W przypadku bezpłatnych dzierżawców usługi Azure AD bez dostępu warunkowego można [użyć domyślnych ustawień zabezpieczeń, aby chronić użytkowników](../fundamentals/concept-fundamentals-security-defaults.md). Użytkownicy są monitowani o uwierzytelnianie MFA zgodnie z potrzebami, ale nie można definiować własnych reguł, aby kontrolować zachowanie.

W razie potrzeby można włączyć każde konto dla Multi-Factor Authentication usługi Azure AD dla poszczególnych użytkowników. Gdy użytkownicy są włączani indywidualnie, przeprowadzają uwierzytelnianie wieloskładnikowe przy każdym logowaniu (z pewnymi wyjątkami, takimi jak logowanie przy użyciu zaufanych adresów IP lub włączenie opcji _Zapamiętaj usługę MFA na zaufanych urządzeniach_ ).

Zmiana stanu użytkowników nie jest zalecana, chyba że licencje usługi Azure AD nie obejmują dostępu warunkowego i nie chcesz używać domyślnych ustawień zabezpieczeń. Aby uzyskać więcej informacji na temat różnych sposobów włączania uwierzytelniania wieloskładnikowego, zobacz [funkcje i licencje usługi Azure AD Multi-Factor Authentication](concept-mfa-licensing.md).

> [!IMPORTANT]
>
> W tym artykule szczegółowo opisano sposób wyświetlania i zmieniania stanu Multi-Factor Authentication usługi Azure AD dla poszczególnych użytkowników. Jeśli używasz dostępu warunkowego lub domyślnych ustawień zabezpieczeń, nie przeglądasz ani nie włączysz kont użytkowników, wykonując następujące kroki.
>
> Włączenie Multi-Factor Authentication usługi Azure AD za pomocą zasad dostępu warunkowego nie powoduje zmiany stanu użytkownika. Nie należy alarmować, jeśli użytkownicy są wyłączeni. Dostęp warunkowy nie zmienia stanu.
>
> **Nie włączaj ani nie Wymuszaj Multi-Factor Authentication usługi Azure AD dla poszczególnych użytkowników, jeśli używasz zasad dostępu warunkowego.**

## <a name="azure-ad-multi-factor-authentication-user-states"></a>Stany użytkowników Multi-Factor Authentication usługi Azure AD

Stan użytkownika wskazuje, czy administrator zarejestrował je w Multi-Factor Authentication usługi Azure AD dla poszczególnych użytkowników. Konta użytkowników w usłudze Azure AD Multi-Factor Authentication mają trzy różne stany:

| Stan | Opis | Uwzględnione starsze uwierzytelnianie | Uwzględnione aplikacje przeglądarki | Zmodyfikowane nowoczesne uwierzytelnianie |
|:---:| --- |:---:|:--:|:--:|
| Disabled | Domyślny stan użytkownika nie jest rejestrowany w Multi-Factor Authentication usługi Azure AD dla poszczególnych użytkowników. | Nie | Nie | Nie |
| Enabled (Włączony) | Użytkownik jest rejestrowany w usłudze Azure AD Multi-Factor Authentication, ale nadal może używać swojego hasła do starszego uwierzytelniania. Jeśli użytkownik nie zarejestrował jeszcze metod uwierzytelniania MFA, otrzyma monit o zarejestrowanie przy następnym logowaniu przy użyciu nowoczesnego uwierzytelniania (na przykład za pośrednictwem przeglądarki sieci Web). | Nie. Starsze uwierzytelnianie jest nadal wykonywane do momentu ukończenia procesu rejestracji. | Tak. Po wygaśnięciu sesji wymagana jest rejestracja w usłudze Azure AD Multi-Factor Authentication.| Tak. Po wygaśnięciu tokenu dostępu wymagana jest rejestracja w usłudze Azure AD Multi-Factor Authentication. |
| Enforced (Wymuszony) | Użytkownik jest zarejestrowany dla poszczególnych użytkowników w usłudze Azure AD Multi-Factor Authentication. Jeśli użytkownik nie zarejestrował jeszcze metod uwierzytelniania, otrzyma monit o zarejestrowanie przy następnym logowaniu przy użyciu nowoczesnego uwierzytelniania (na przykład za pośrednictwem przeglądarki sieci Web). Użytkownicy, którzy ukończyli rejestrację w stanie *włączonym* , są automatycznie przenoszona do stanu *wymuszonego* . | Tak. Aplikacje wymagają haseł aplikacji. | Tak. Usługa Azure AD Multi-Factor Authentication jest wymagana podczas logowania. | Tak. Usługa Azure AD Multi-Factor Authentication jest wymagana podczas logowania. |

Wszyscy użytkownicy zaczynają *wyłączać*. Po zarejestrowaniu użytkowników w usłudze Azure AD Multi-Factor Authentication ich stan zmieni się na *włączone*. Gdy użytkownicy będą mogli się zalogować i zakończyć proces rejestracji, ich stan zmieni się na *wymuszone*. Administratorzy mogą przenosić użytkowników między Stanami *, w tym* z *wymuszone* lub *wyłączone*.

> [!NOTE]
> Jeśli uwierzytelnianie wieloskładnikowe dla użytkowników zostanie ponownie włączone dla użytkownika, a użytkownik nie zostanie ponownie zarejestrowany, jego stan MFA nie przechodzi z *włączonego* do *WYMUSZONego* w interfejsie użytkownika zarządzania usługą MFA. Administrator musi przenieść użytkownika bezpośrednio do *wymuszenia*.

## <a name="view-the-status-for-a-user"></a>Wyświetlanie stanu użytkownika

Aby wyświetlić Stany użytkowników i zarządzać nimi, wykonaj następujące kroki, aby uzyskać dostęp do strony Azure Portal:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator.
1. Wyszukaj i wybierz pozycję *Azure Active Directory*, a następnie wybierz pozycję **Użytkownicy**  >  **Wszyscy użytkownicy**.
1. Wybierz pozycję **Multi-Factor Authentication**. Może być konieczne przewinięcie w prawo, aby wyświetlić tę opcję menu. Wybierz Poniższy przykładowy zrzut ekranu, aby wyświetlić okno pełne Azure Portal i lokalizację menu: [ ![ Wybierz Multi-Factor Authentication z okna użytkowników w usłudze Azure AD.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Zostanie otwarta nowa strona, która wyświetla stan użytkownika, jak pokazano w poniższym przykładzie.
   ![Zrzut ekranu pokazujący przykładowe informacje o stanie użytkownika dotyczące usługi Azure AD Multi-Factor Authentication](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Zmiana stanu użytkownika

Aby zmienić stan Multi-Factor Authentication usługi Azure AD dla użytkownika, wykonaj następujące czynności:

1. Wykonaj poprzednie kroki, aby [wyświetlić stan użytkownika](#view-the-status-for-a-user) , który zostanie wyświetlony na stronie **Użytkownicy** usługi Azure AD Multi-Factor Authentication.
1. Znajdź użytkownika, który ma zostać włączony dla użytkownika usługi Azure AD Multi-Factor Authentication. Być może trzeba będzie zmienić widok u góry, aby **Użytkownicy**.
   ![Wybierz użytkownika, którego stan chcesz zmienić z karty użytkownicy](./media/howto-mfa-userstates/enable1.png)
1. Zaznacz pole wyboru obok nazw użytkowników, dla których chcesz zmienić stan.
1. Po prawej stronie w obszarze **szybkie kroki** wybierz pozycję **Włącz** lub **Wyłącz**. W poniższym przykładzie użytkownik *Jan Kowalski* ma pole wyboru obok nazwy i jest włączone do użycia: ![ Włącz wybranego użytkownika, klikając pozycję Włącz w menu szybkie kroki](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Włączono* użytkowników, którzy zostali zarejestrowani *w usłudze Azure* AD Multi-Factor Authentication automatycznie. Nie zmieniaj ręcznie stanu użytkownika na *wymuszone* , chyba że użytkownik jest już zarejestrowany lub jeśli jest to możliwe, aby użytkownik mógł obsłużyć przerwy w połączeniach ze starszymi protokołami uwierzytelniania.

1. Potwierdź wybór w otwartym oknie podręcznym.

Po włączeniu użytkowników Powiadom ich pocztą e-mail. Poinformuj użytkowników, że zostanie wyświetlony monit o zarejestrowanie się przy następnym logowaniu. Ponadto, jeśli w organizacji są używane aplikacje nie korzystające z przeglądarki, które nie obsługują nowoczesnego uwierzytelniania, muszą one tworzyć hasła aplikacji. Aby uzyskać więcej informacji, zobacz [Podręcznik użytkownika końcowego usługi Azure AD Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user-first-time.md) .

## <a name="change-state-using-powershell"></a>Zmień stan przy użyciu programu PowerShell

Aby zmienić stan użytkownika przy użyciu [programu Azure AD PowerShell](/powershell/azure/), należy zmienić `$st.State` parametr dla konta użytkownika. Istnieją trzy możliwe stany konta użytkownika:

* *Włączono*
* *Enforced (Wymuszony)*
* *Disabled*  

Ogólnie rzecz biorąc nie przenoś użytkowników bezpośrednio do stanu *wymuszonego* , chyba że są one już zarejestrowane dla usługi MFA. W takim przypadku starsze aplikacje uwierzytelniania przestaną działać, ponieważ użytkownik nie przeszedł Multi-Factor Authentication rejestracji w usłudze Azure AD i uzyskał [hasło aplikacji](howto-mfa-app-passwords.md). W niektórych przypadkach takie zachowanie może być odpowiednie, ale ma wpływ na środowisko użytkownika do momentu zarejestrowania użytkownika.

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

## <a name="convert-users-from-per-user-mfa-to-conditional-access"></a>Konwertowanie użytkowników z usługi MFA na użytkownika na dostęp warunkowy

Poniższy program PowerShell może pomóc w konwersji do Multi-Factor Authentication usługi Azure AD opartej na dostęp warunkowy.

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
> Jeśli uwierzytelnianie wieloskładnikowe zostanie ponownie włączone dla użytkownika, a użytkownik nie zostanie ponownie zarejestrowany, jego stan MFA nie przechodzi z *włączonego* do *wymuszonego* w interfejsie użytkownika zarządzania uwierzytelnianiem usługi MFA. W takim przypadku administrator musi przenieść użytkownika bezpośrednio do *wymuszenia*.

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować ustawienia Multi-Factor Authentication usługi Azure AD, zobacz  [Konfigurowanie ustawień Multi-Factor Authentication usługi Azure AD](howto-mfa-mfasettings.md).

Aby zarządzać ustawieniami użytkownika w usłudze Azure AD Multi-Factor Authentication, zobacz [Zarządzanie ustawieniami użytkownika w usłudze Azure ad Multi-Factor Authentication](howto-mfa-userdevicesettings.md).

Aby zrozumieć, dlaczego użytkownik otrzymał monit lub nie był monitowany o przeprowadzenie uwierzytelniania MFA, zobacz [raporty usługi Azure AD Multi-Factor Authentication](howto-mfa-reporting.md).
