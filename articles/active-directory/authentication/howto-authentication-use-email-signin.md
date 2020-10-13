---
title: Zaloguj się przy użyciu wiadomości e-mail jako alternatywny identyfikator logowania dla Azure Active Directory
description: Dowiedz się, jak skonfigurować i umożliwić użytkownikom logowanie się do Azure Active Directory przy użyciu adresu e-mail jako alternatywnego identyfikatora logowania (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calui
ms.openlocfilehash: c822aaebb2451d709f6afcdeba959f39c4d491cb
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964540"
---
# <a name="sign-in-to-azure-active-directory-using-email-as-an-alternate-login-id-preview"></a>Zaloguj się, aby Azure Active Directory przy użyciu poczty e-mail jako alternatywnego identyfikatora logowania (wersja zapoznawcza)

Wiele organizacji chce pozwolić użytkownikom na logowanie się do Azure Active Directory (Azure AD) przy użyciu tych samych poświadczeń, co w lokalnym środowisku katalogu. W tym podejściu, nazywanego uwierzytelnianiem hybrydowym, użytkownicy muszą pamiętać tylko jeden zestaw poświadczeń.

Niektóre organizacje nie zostały przeniesione do uwierzytelniania hybrydowego z następujących powodów:

* Domyślnie główna nazwa użytkownika (UPN) usługi Azure AD jest ustawiona na tę samą nazwę UPN co katalog lokalny.
* Zmiana nazwy UPN usługi Azure AD tworzy niezrównaną zgodność między środowiskami Premium i Azure AD, które mogą spowodować problemy z niektórymi aplikacjami i usługami.
* Ze względu na przyczyny biznesowe lub dotyczące zgodności organizacja nie chce używać lokalnej nazwy UPN do logowania się do usługi Azure AD.

Aby ułatwić przechodzenie do uwierzytelniania hybrydowego, można teraz skonfigurować usługę Azure AD w taki sposób, aby umożliwić użytkownikom logowanie się przy użyciu wiadomości e-mail w zweryfikowanej domenie jako alternatywny identyfikator logowania. Jeśli na przykład *firma Contoso* została Poprzednia zalogowaniem się do firmy *Fabrikam*, a nie będziesz w stanie zalogować się przy użyciu starszej `balas@contoso.com` nazwy UPN, można teraz użyć poczty e-mail jako alternatywnego identyfikatora logowania. Aby uzyskać dostęp do aplikacji lub usług, użytkownicy będą logować się do usługi Azure AD przy użyciu przypisanych im adresów e-mail, takich jak `balas@fabrikam.com` .

W tym artykule opisano sposób włączania i używania poczty e-mail jako alternatywnego identyfikatora logowania. Ta funkcja jest dostępna w wersji Azure AD — wersja Bezpłatna lub nowszej.

> [!NOTE]
> Logowanie do usługi Azure AD za pomocą poczty e-mail jako alternatywny identyfikator logowania jest publiczną funkcją w wersji zapoznawczej Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Przegląd podejścia do logowania za pomocą usługi Azure AD

Aby zalogować się do usługi Azure AD, użytkownicy wprowadzają nazwę, która jednoznacznie identyfikuje swoje konto. Historycznie, jako nazwę logowania można użyć nazwy UPN usługi Azure AD.

W przypadku organizacji, w których lokalna nazwa UPN jest preferowanym adresem e-mail użytkownika, to podejście było doskonałe. Organizacje te ustawili nazwę UPN usługi Azure AD na dokładnie taką samą wartość jak lokalna nazwa UPN, a użytkownicy będą mieć spójne środowisko logowania.

Jednak w niektórych organizacjach lokalna nazwa UPN nie jest używana jako nazwy logowania. W środowiskach lokalnych Skonfiguruj AD DS lokalny, aby zezwolić na logowanie przy użyciu alternatywnego identyfikatora logowania. Ustawienie nazwy UPN usługi Azure AD na taką samą wartość jak lokalna nazwa UPN nie jest opcją, ponieważ usługa Azure AD będzie wymagała od użytkowników zalogowania się przy użyciu tej wartości.

Typowym obejściem tego problemu jest ustawienie nazwy UPN usługi Azure AD na adres e-mail, za pomocą którego użytkownik oczekuje na zalogowanie. Takie podejście działa, chociaż skutkuje różnymi nazwami UPN między lokalną usługą AD i usługą Azure AD, a ta konfiguracja nie jest zgodna ze wszystkimi obciążeniami Microsoft 365.

Innym rozwiązaniem jest zsynchronizowanie usługi Azure AD i lokalnych nazw UPN z tą samą wartością, a następnie skonfigurowanie usługi Azure AD, aby umożliwić użytkownikom logowanie się do usługi Azure AD za pomocą zweryfikowanej wiadomości e-mail. Aby zapewnić tę możliwość, należy zdefiniować co najmniej jeden adres e-mail w atrybucie *proxyAddresses* użytkownika w katalogu lokalnym. *ProxyAddresses* są następnie synchronizowane z usługą Azure AD automatycznie przy użyciu Azure AD Connect.

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

Zaloguj się do usługi Azure AD za pomocą poczty e-mail, ponieważ alternatywny identyfikator logowania jest dostępny w wersji Azure AD — wersja Bezpłatna lub nowszej.

W bieżącym stanie wersji zapoznawczej następujące ograniczenia są stosowane, gdy użytkownik loguje się przy użyciu adresu e-mail bez nazwy UPN jako alternatywnego identyfikatora logowania:

* Użytkownicy mogą zobaczyć swoją nazwę UPN, nawet jeśli zalogowano się za pomocą wiadomości e-mail bez nazwy UPN. Może być widoczne następujące przykładowe zachowanie:
    * Użytkownik jest monitowany o zalogowanie się przy użyciu nazwy UPN po skierowaniu do logowania do usługi Azure AD za pomocą usługi `login_hint=<non-UPN email>` .
    * Gdy użytkownik zaloguje się przy użyciu wiadomości e-mail bez nazwy UPN i wprowadzi nieprawidłowe hasło, Strona *"wprowadź hasło"* zmieni się, aby wyświetlić nazwę UPN.
    * W niektórych witrynach i aplikacjach firmy Microsoft, takich jak [https://portal.azure.com](https://portal.azure.com) i Microsoft Office, formant **Menedżera kont** zwykle wyświetlany w prawym górnym rogu może wyświetlać nazwę UPN użytkownika zamiast wiadomości e-mail bez nazwy UPN użytej do zalogowania się.

* Niektóre przepływy nie są obecnie zgodne z wiadomościami e-mail bez nazwy UPN, na przykład:
    * Ochrona tożsamości nie jest obecnie *zgodna z* alternatywnymi identyfikatorami logowania w wiadomości e-mail z wykryciem zagrożeń. To wykrywanie ryzyka używa nazwy UPN w celu dopasowania do nieujawnionych poświadczeń. Aby uzyskać więcej informacji, zobacz [Azure AD Identity Protection wykrywania ryzyka i korygowania][identity-protection].
    * Zaproszenia B2B wysyłane do alternatywnej poczty e-mail identyfikatora logowania nie są w pełni obsługiwane. Po zaakceptowaniu zaproszenia wysłanego do wiadomości e-mail jako alternatywnego identyfikatora logowania logowanie za pomocą alternatywnej wiadomości e-mail może nie być możliwe w przypadku użytkownika w punkcie końcowym dzierżawcy.

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>Synchronizuj adresy e-mail logowania z usługą Azure AD

Tradycyjne Active Directory Domain Services (AD DS) lub Active Directory Federation Services (AD FS) uwierzytelnianie odbywa się bezpośrednio w sieci i jest obsługiwane przez infrastrukturę AD DS. W przypadku uwierzytelniania hybrydowego użytkownicy mogą rejestrować się bezpośrednio w usłudze Azure AD.

Aby obsłużyć to podejście do uwierzytelniania hybrydowego, zsynchronizuj lokalne środowisko AD DS z usługą Azure AD przy użyciu [Azure AD Connect][azure-ad-connect] i skonfiguruj je tak, aby korzystało z funkcji synchronizacji skrótów haseł (PHS) lub uwierzytelniania Pass-Through (PTA).

W obu opcjach konfiguracji użytkownik przesyła swoją nazwę użytkownika i hasło do usługi Azure AD, która sprawdza poprawność poświadczeń i wystawia bilet. Gdy użytkownicy logują się do usługi Azure AD, eliminuje konieczność, aby Twoja organizacja mogła hostować infrastrukturę AD FS i zarządzać nią.

![Diagram tożsamości hybrydowej usługi Azure AD z synchronizacją skrótów haseł](media/howto-authentication-use-email-signin/hybrid-password-hash-sync.png)

![Diagram tożsamości hybrydowej usługi Azure AD z uwierzytelnianiem przekazującym](media/howto-authentication-use-email-signin/hybrid-pass-through-authentication.png)

Jeden z atrybutów użytkownika, które są automatycznie synchronizowane przez Azure AD Connect, to *proxyAddresses*. Jeśli użytkownicy mają adres e-mail zdefiniowany w środowisku Premium AD DS w ramach atrybutu *proxyAddresses* , zostanie automatycznie zsynchronizowany z usługą Azure AD. Ten adres e-mail może być następnie używany bezpośrednio w procesie logowania usługi Azure AD jako alternatywny identyfikator logowania.

> [!IMPORTANT]
> Tylko wiadomości e-mail w zweryfikowanych domenach dla dzierżawy są synchronizowane z usługą Azure AD. Każda dzierżawa usługi Azure AD ma jedną lub więcej zweryfikowanych domen, dla których sprawdzono własność i które są w unikatowy sposób powiązane z dzierżawcą.
>
> Aby uzyskać więcej informacji, zobacz [Dodawanie i weryfikowanie niestandardowej nazwy domeny w usłudze Azure AD][verify-domain].

Aby uzyskać więcej informacji, zobacz [Wybieranie odpowiedniej metody uwierzytelniania dla rozwiązania do tworzenia tożsamości hybrydowej usługi Azure AD][hybrid-auth-methods].

## <a name="enable-user-sign-in-with-an-email-address"></a>Włącz logowanie użytkownika przy użyciu adresu e-mail

Gdy użytkownicy z zastosowanym atrybutem *proxyAddresses* są synchronizowane z usługą Azure AD przy użyciu Azure AD Connect, należy włączyć tę funkcję, aby użytkownicy mogli logować się za pomocą poczty e-mail jako alternatywnego identyfikatora logowania dla dzierżawy. Ta funkcja instruuje serwery logowania usługi Azure AD, aby nie tylko sprawdzać nazwę logowania względem wartości nazw UPN, ale także względem wartości *proxyAddresses* dla adresu e-mail.

W trakcie korzystania z wersji zapoznawczej można obecnie włączyć tylko logowanie za pomocą poczty e-mail jako alternatywną funkcję identyfikatora logowania przy użyciu programu PowerShell. Musisz mieć uprawnienia *administratora dzierżawy* , aby wykonać następujące czynności:

1. Otwórz sesję programu PowerShell jako administrator, a następnie zainstaluj moduł *AzureADPreview* przy użyciu polecenia cmdlet [Install-module][Install-Module] :

    ```powershell
    Install-Module AzureADPreview
    ```

    Jeśli zostanie wyświetlony monit, wybierz pozycję **Y** , aby zainstalować pakiet NuGet lub zainstalować go z niezaufanego repozytorium.

1. Zaloguj się do dzierżawy usługi Azure AD jako *Administrator dzierżawy* przy użyciu polecenia cmdlet [Connect-AzureAD][Connect-AzureAD] :

    ```powershell
    Connect-AzureAD
    ```

    Polecenie zwraca informacje o Twoim koncie, środowisku i IDENTYFIKATORze dzierżawy.

1. Sprawdź, czy zasady *HomeRealmDiscoveryPolicy* już istnieją w dzierżawie przy użyciu polecenia cmdlet [Get-AzureADPolicy][Get-AzureADPolicy] w następujący sposób:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

1. Jeśli obecnie nie ma skonfigurowanych zasad, polecenie zwróci wartość Nothing. Jeśli jest zwracana zasada, Pomiń ten krok i przejdź do następnego kroku, aby zaktualizować istniejące zasady.

    Aby dodać zasady *HomeRealmDiscoveryPolicy* do dzierżawy, należy użyć polecenia cmdlet [New-AzureADPolicy][New-AzureADPolicy] i ustawić atrybut *AlternateIdLogin* na *"Enabled": true* , jak pokazano w następującym przykładzie:

    ```powershell
    New-AzureADPolicy -Definition @('{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    Po pomyślnym utworzeniu zasad polecenie zwróci identyfikator zasad, jak pokazano w następujących przykładowych danych wyjściowych:

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. Jeśli już skonfigurowano zasady, sprawdź, czy atrybut *AlternateIdLogin*   jest włączony, jak pokazano w następujących przykładowych danych wyjściowych zasad:

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    Jeśli zasady istnieją, ale atrybut *AlternateIdLogin* , który nie jest obecny lub jest włączony, lub jeśli istnieją inne atrybuty dla zasad, które chcesz zachować, zaktualizuj istniejące zasady za pomocą polecenia cmdlet [Set-AzureADPolicy][Set-AzureADPolicy] .

    > [!IMPORTANT]
    > Podczas aktualizowania zasad upewnij się, że dołączasz wszelkie stare ustawienia i nowy atrybut *AlternateIdLogin* .

    Poniższy przykład dodaje atrybut *AlternateIdLogin* i zachowuje atrybut *AllowCloudPasswordValidation* , który mógł już zostać ustawiony:

    ```powershell
    Set-AzureADPolicy -id b581c39c-8fe3-4bb5-b53d-ea3de05abb4b `
        -Definition @('{"HomeRealmDiscoveryPolicy" :{"AllowCloudPasswordValidation":true,"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    Upewnij się, że zaktualizowane zasady pokazują zmiany i że atrybut *AlternateIdLogin* jest teraz włączony:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

Po zastosowaniu zasad może upłynąć do czasu, aż będzie możliwe zalogowanie się i umożliwienie użytkownikom logowania się przy użyciu alternatywnego identyfikatora logowania.

## <a name="test-user-sign-in-with-email"></a>Testowanie logowania użytkownika przy użyciu poczty e-mail

Aby sprawdzić, czy użytkownicy mogą logować się za pomocą poczty e-mail, przejdź do [https://myprofile.microsoft.com][my-profile] konta użytkownika i zaloguj się na nim przy użyciu adresu e-mail, na przykład `balas@fabrikam.com` , nie nazwy UPN, na przykład `balas@contoso.com` . Środowisko logowania powinno wyglądać i funkcjonować tak samo jak w przypadku zdarzenia logowania opartego na nazwach UPN.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli użytkownicy mają problemy ze zdarzeniami logowania przy użyciu adresu e-mail, należy zapoznać się z następującymi krokami rozwiązywania problemów:

1. Upewnij się, że konto użytkownika ma ustawiony adres e-mail dla atrybutu *proxyAddresses* w środowisku Premium AD DS.
1. Sprawdź, czy Azure AD Connect jest skonfigurowany i czy pomyślnie synchronizuje konta użytkowników ze środowiska Premium AD DS w usłudze Azure AD.
1. Upewnij się, że zasady usługi Azure AD *HomeRealmDiscoveryPolicy* mają atrybut *AlternateIdLogin* ustawiony na *wartość "Enabled": true*:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tożsamości hybrydowej, takiej jak aplikacja usługi Azure AD proxy lub Azure AD Domain Services, zobacz [tożsamość hybrydową usługi Azure AD w celu uzyskania dostępu do obciążeń Premium i zarządzania nimi][hybrid-overview].

Aby uzyskać więcej informacji o operacjach związanych z tożsamościami hybrydowymi, zobacz Jak działa synchronizacja [skrótów haseł][phs-overview] lub synchronizacja [przekazująca][pta-overview] .

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md
[identity-protection]: ../identity-protection/overview-identity-protection.md#risk-detection-and-remediation

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[my-profile]: https://myprofile.microsoft.com
