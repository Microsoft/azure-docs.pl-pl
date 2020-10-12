---
title: Jednokrotne uwierzytelnianie kodu dostępu dla użytkowników-Gości B2B — Azure AD
description: Jak używać jednorazowego kodu dostępu wiadomości E-mail do uwierzytelniania użytkowników Gości B2B bez konieczności konto Microsoft.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 714e4484c71b995bee186a2d94dc45c7ff82c50d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87909206"
---
# <a name="email-one-time-passcode-authentication-preview"></a>Wyślij wiadomość e-mail do jednorazowego uwierzytelniania kodu dostępu (wersja zapoznawcza)

> [!NOTE]
> Jednorazowy kod dostępu wiadomości e-mail jest publiczną funkcją w wersji zapoznawczej Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

W tym artykule opisano, jak włączyć jednokrotne uwierzytelnianie kodu dostępu w wiadomości E-mail dla użytkowników-Gości B2B. Funkcja jednorazowego kodu dostępu wiadomości E-mail uwierzytelnia użytkowników gościa B2B, gdy nie mogą być uwierzytelniane za pośrednictwem innych takich metod jak Azure AD, konto Microsoft (MSA) lub Google Federation. W przypadku uwierzytelniania za pomocą jednorazowego kodu dostępu nie ma potrzeby tworzenia konto Microsoft. Gdy użytkownik-Gość zrealizuje zaproszenie lub uzyskuje dostęp do zasobu udostępnionego, może zażądać kodu tymczasowego, który jest wysyłany na adres e-mail. Następnie wprowadzają ten kod, aby kontynuować logowanie.

Ta funkcja jest obecnie dostępna w wersji zapoznawczej (zobacz [rezygnację z wersji zapoznawczej](#opting-in-to-the-preview) poniżej). W wersji zapoznawczej ta funkcja będzie domyślnie włączona dla wszystkich dzierżawców.

> [!NOTE]
> Jednorazowy kod dostępu użytkownicy muszą logować się przy użyciu linku zawierającego kontekst dzierżawy (na przykład `https://myapps.microsoft.com/?tenantid=<tenant id>` lub `https://portal.azure.com/<tenant id>` w przypadku zweryfikowanej domeny `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com` ). Bezpośrednie linki do aplikacji i zasobów działają również tak długo, jak w przypadku kontekstu dzierżawy. Użytkownicy-Goście nie mogą obecnie zalogować się za pomocą punktów końcowych, które nie mają kontekstu dzierżawy. Na przykład przy użyciu `https://myapps.microsoft.com` , `https://portal.azure.com` , lub wspólny punkt końcowy zespołów spowoduje wystąpienie błędu. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Środowisko użytkownika dla użytkowników-Gości jednorazowego kodu dostępu
W przypadku uwierzytelniania za pomocą jednorazowego kodu dostępu użytkownik-Gość może skorzystać z zaproszenia przez kliknięcie linku bezpośredniego lub wysłanie wiadomości e-mail z zaproszeniem. W obu przypadkach komunikat w przeglądarce wskazuje, że kod zostanie wysłany na adres e-mail użytkownika-gościa. Użytkownik-Gość wybierze opcję **Wyślij kod**:
 
   ![Zrzut ekranu przedstawiający przycisk Wyślij kod](media/one-time-passcode/otp-send-code.png)
 
Kod dostępu jest wysyłany na adres e-mail użytkownika. Użytkownik pobiera kod dostępu z wiadomości e-mail i wprowadza go w oknie przeglądarki:
 
   ![Zrzut ekranu przedstawiający stronę wprowadzanie kodu](media/one-time-passcode/otp-enter-code.png)
 
Użytkownik-Gość jest teraz uwierzytelniany i może zobaczyć zasób udostępniony lub kontynuować logowanie. 

> [!NOTE]
> Jednorazowe kody dostępu są ważne przez 30 minut. Po 30 minutach określony jednorazowy kod dostępu nie jest już ważny, a użytkownik musi zażądać nowego. Sesje użytkowników wygasają po upływie 24 godzin. Po upływie tego czasu użytkownik-gość otrzyma nowy kod dostępu podczas uzyskiwania dostępu do zasobu. Wygaśnięcie sesji zapewnia dodatkowe zabezpieczenia, zwłaszcza gdy użytkownik-Gość opuszcza swoją firmę lub nie potrzebuje już dostępu.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Kiedy użytkownik-Gość otrzymuje jednorazowy kod dostępu?

Gdy użytkownik-Gość zrealizuje zaproszenie lub używa linku do zasobu, który został Ci udostępniony, otrzyma jednorazowy kod dostępu, jeśli:
- Nie mają konta usługi Azure AD 
- Nie mają konto Microsoft 
- Zapraszana dzierżawa nie została skonfigurowana dla użytkowników usługi Google Federation for @gmail.com i @googlemail.com 

W momencie zaproszenia nie ma wskazania, że użytkownik, którego zapraszasz, będzie używać uwierzytelniania jednorazowego kodu dostępu. Jednak po zalogowaniu się użytkownika-gościa uwierzytelnianie jednorazowego kodu dostępu będzie metodą rezerwową, jeśli nie można użyć innych metod uwierzytelniania. 

Użytkowników-Gości, którzy uwierzytelniają się za pomocą jednorazowych kodów dostępu w Azure Portal, można wyświetlić, przechodząc do **Azure Active Directory**  >  **użytkowników**.

![Zrzut ekranu przedstawiający użytkownika jednorazowego kodu dostępu z wartością źródłową OTP](media/one-time-passcode/otp-users.png)

> [!NOTE]
> Gdy użytkownik zrealizuje jednorazowy kod dostępu, a następnie uzyska konto MSA, konta usługi Azure AD lub inne konto federacyjne, będzie nadal uwierzytelniane przy użyciu jednorazowego kodu dostępu. Jeśli chcesz zaktualizować metodę uwierzytelniania, możesz usunąć konto użytkownika-gościa i je zaprosić.

### <a name="example"></a>Przykład
Użytkownik alexdoe@gmail.com -gość jest zapraszany do firmy Fabrikam, która nie ma skonfigurowanej usługi Google Federation. Alex nie ma konto Microsoft. Otrzymają one jednorazowy kod dostępu do uwierzytelniania.

## <a name="opting-in-to-the-preview"></a>Rezygnacja z wersji zapoznawczej 
Wykonanie akcji zgody może potrwać kilka minut. Po tym czasie tylko nowo zaproszeni użytkownicy, którzy spełniają powyższe warunki, będą korzystać z uwierzytelniania jednorazowego kodu dostępu. Użytkownicy-Goście, którzy wcześniej korzystali z zaproszenia, będą nadal używać tej samej metody uwierzytelniania.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>Aby zrezygnować z korzystania z portalu usługi Azure AD
1.  Zaloguj się do [Azure Portal](https://portal.azure.com/) jako Administrator globalny usługi Azure AD.
2.  W okienku nawigacji wybierz pozycję **Azure Active Directory**.
3.  Wybierz pozycję **tożsamości zewnętrzne**  >  **Ustawienia współpracy zewnętrznej**.
5.  W obszarze **Włączanie poczty e-mail One-Time kod dostępu dla Gości (wersja zapoznawcza)** wybierz pozycję **tak**.
 
### <a name="to-opt-in-using-powershell"></a>Aby wyrazić zgodę na korzystanie z programu PowerShell

Najpierw należy zainstalować najnowszą wersję programu Azure AD PowerShell dla modułu Graph (AzureADPreview). Następnie określ, czy zasady B2B już istnieją, i uruchom odpowiednie polecenia.

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>Wymaganie wstępne: Zainstaluj najnowszy moduł AzureADPreview
Najpierw sprawdź, które moduły zostały zainstalowane. Otwórz program PowerShell jako użytkownik z podwyższonym poziomem uprawnień (Uruchom jako administrator) i uruchom następujące polecenie:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

Jeśli moduł AzureADPreview nie wyświetla żadnego komunikatu informującego o tym, że istnieje nowsza wersja, wszystko jest gotowe. W przeciwnym razie — w zależności od rezultatu — wykonaj jedną z następujących czynności:

- Jeśli nie są zwracane żadne wyniki, uruchom następujące polecenie, aby zainstalować moduł AzureADPreview:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Jeśli w wynikach jest wyświetlany tylko moduł AzureAD, uruchom następujące polecenia, aby zainstalować moduł AzureADPreview: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Jeśli w wynikach jest wyświetlany tylko moduł AzureADPreview, ale pojawia się komunikat informujący o tym, że istnieje nowsza wersja, uruchom następujące polecenia, aby zaktualizować moduł: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

Może zostać wyświetlony monit z informacją, że instalujesz moduł z niezaufanego repozytorium. Dzieje się tak w sytuacji, gdy repozytorium PSGallery nie zostało ustawione wcześniej jako zaufane. Naciśnij klawisz **Y**, aby zainstalować moduł.

#### <a name="check-for-existing-policies-and-opt-in"></a>Sprawdzanie istniejących zasad i wybór

Następnie sprawdź, czy już istnieje B2BManagementPolicy, uruchamiając następujące polecenie:

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- Jeśli dane wyjściowe mają wartość false, zasady nie istnieją. Utwórz nowy B2BManagementPolicy i Zasłuchaj wersji zapoznawczej, uruchamiając następujące czynności:

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- Jeśli dane wyjściowe mają wartość true, zasady B2BManagementPolicy obecnie istnieją. Aby zaktualizować zasady i zalogować się do wersji zapoznawczej, uruchom następujące polecenie:
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>Rezygnacja z wersji zapoznawczej po rezygnacji z
Wykonanie akcji rezygnacji może potrwać kilka minut. Jeśli wyłączysz Podgląd, wszyscy użytkownicy-Goście, którzy wykonali jednorazowy kod dostępu, nie będą mogli się zalogować. Można usunąć użytkownika-gościa i ponownie zaprosić użytkownika, aby umożliwić im ponowne zalogowanie się przy użyciu innej metody uwierzytelniania.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>Aby wyłączyć podgląd przy użyciu portalu usługi Azure AD
1.  Zaloguj się do [Azure Portal](https://portal.azure.com/) jako Administrator globalny usługi Azure AD.
2.  W okienku nawigacji wybierz pozycję **Azure Active Directory**.
3.  Wybierz pozycję **tożsamości zewnętrzne**  >  **Ustawienia współpracy zewnętrznej**.
5.  W obszarze **Włączanie poczty e-mail One-Time kod dostępu dla Gości (wersja zapoznawcza)** wybierz pozycję **nie**.

### <a name="to-turn-off-the-preview-using-powershell"></a>Aby wyłączyć podgląd przy użyciu programu PowerShell
Zainstaluj najnowszą wersję modułu AzureADPreview, jeśli nie masz jej już (patrz [warunek wstępny: Zainstaluj najnowszy moduł AzureADPreview](#prerequisite-install-the-latest-azureadpreview-module) powyżej). Następnie sprawdź, czy zasady jednorazowego podglądu kodu dostępu są obecnie dostępne, wykonując następujące czynności:

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

Jeśli dane wyjściowe mają wartość true, zrezygnuj z wersji zapoznawczej, uruchamiając następujące polecenie:

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

