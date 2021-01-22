---
title: Rejestracja i logowanie za pomocą zasad niestandardowych
titleSuffix: Azure AD B2C
description: Wysyłać hasła jednorazowe (OTP) w wiadomościach tekstowych do telefonów użytkowników aplikacji przy użyciu zasad niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2600ea3488c643bcf215b058425de42cd439dcff
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660271"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c"></a>Skonfiguruj konto i zaloguj się przy użyciu zasad niestandardowych w Azure AD B2C

Rejestracja i logowanie za pomocą telefonu w Azure Active Directory B2C (Azure AD B2C) umożliwia użytkownikom rejestrowanie się i logowanie do aplikacji przy użyciu hasła jednorazowego (OTP) wysyłanego w wiadomości tekstowej do telefonu. Hasła jednorazowe mogą pomóc zminimalizować ryzyko naruszenia lub naruszania haseł przez użytkowników.

Wykonaj kroki opisane w tym artykule, aby użyć zasad niestandardowych, aby umożliwić klientom rejestrowanie się i logowanie się do aplikacji przy użyciu hasła jednorazowego wysyłanego do telefonu.

## <a name="pricing"></a>Cennik

Hasła jednorazowe są wysyłane do użytkowników za pomocą wiadomości SMS i mogą być naliczone za każdy wysłany komunikat. Aby uzyskać informacje o cenach, zapoznaj się z sekcją **oddzielne opłaty** w [cenniku Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="user-experience-for-phone-sign-up-and-sign-in"></a>Środowisko użytkownika do rejestracji i logowania za telefon

Przy rejestrowaniu i logowaniu użytkownik może zarejestrować się w aplikacji przy użyciu numeru telefonu jako identyfikatora podstawowego. Środowisko użytkownika końcowego podczas rejestrowania i logowania zostało opisane poniżej.

> [!NOTE]
> Zdecydowanie sugerujemy, aby uwzględnić informacje o zgodzie w rejestrowaniu i logowaniu, podobnie jak w przypadku przykładowego tekstu poniżej. Ten przykładowy tekst jest przeznaczony wyłącznie do celów informacyjnych. Zapoznaj się z podręcznikiem dotyczącym monitorowania kodu w [witrynie sieci Web CTIA](https://www.ctia.org/programs) i zapoznaj się z własnymi ekspertami z prawami lub zgodnościami, aby uzyskać wskazówki dotyczące ostatecznej konfiguracji tekstu i funkcji w celu spełnienia własnych potrzeb dotyczących zgodności:
>
> *Podając swój numer telefonu, wyrażasz zgodę na otrzymanie jednorazowego kodu dostępu wysyłanego przez wiadomość tekstową, aby ułatwić zalogowanie się do programu *&lt; INSERT &gt; : Nazwa aplikacji*. Mogą obowiązywać standardowe stawki za komunikaty i dane.*
>
> *&lt;Wstawianie: link do zasad zachowania poufności informacji&gt;*<br/>*&lt;Wstawianie: link do warunków użytkowania usługi&gt;*

Aby dodać własne informacje o zgodzie, Dostosuj Poniższy przykład. Uwzględnij ją w `LocalizedResources` usłudze dla ContentDefinition używanej przez poproszony element z kontrolką wyświetlania (plik *Phone_Email_Base.xml* na stronie [rejestracja i logowanie][starter-pack-phone]za pomocą telefonu):

```xml
<LocalizedResources Id="phoneSignUp.en">        
    <LocalizedStrings>
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_msg_intro">By providing your phone number, you consent to receiving a one-time passcode sent by text message to help you sign into {insert your application name}. Standard message and data rates may apply.</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_text">Privacy Statement</LocalizedString>                
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_url">{insert your privacy statement URL}</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_text">Terms and Conditions</LocalizedString>             
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_url">{insert your terms and conditions URL}</LocalizedString>          
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please verify your country code and phone number</LocalizedString>        
    </LocalizedStrings>      
</LocalizedResources>
   ```

### <a name="phone-sign-up-experience"></a>Środowisko rejestracji na telefonie

Jeśli użytkownik nie ma jeszcze konta dla aplikacji, może go utworzyć, wybierając link **zarejestruj się teraz** . Zostanie wyświetlona strona rejestracji, w której użytkownik wybiera swój **kraj**, wprowadzi numer telefonu i wybierze pozycję **Wyślij kod**.

![Użytkownik uruchamia logowanie do telefonu](media/phone-authentication/phone-signup-start.png)

Jednorazowy kod weryfikacyjny jest wysyłany do numeru telefonu użytkownika. Użytkownik wprowadzi **kod weryfikacyjny** na stronie rejestracji, a następnie wybierze pozycję **Weryfikuj kod**. (Jeśli użytkownik nie był w stanie pobrać kodu, może wybrać pozycję **Wyślij nowy kod**).

![Użytkownik weryfikuje kod podczas rejestracji w telefonie](media/phone-authentication/phone-signup-verify-code.png)

Użytkownik wprowadza wszelkie inne informacje wymagane na stronie rejestracji. Na przykład **Nazwa wyświetlana** **, imię i** **nazwisko** (kraj i numer telefonu pozostają wypełnione). Jeśli użytkownik chce użyć innego numeru telefonu, może wybrać pozycję **Zmień numer** , aby ponownie uruchomić konto. Po zakończeniu użytkownik wybiera pozycję **Kontynuuj**.

![Użytkownik udostępnia dodatkowe informacje](media/phone-authentication/phone-signup-additional-info.png)

Następnie użytkownik zostanie poproszony o podanie wiadomości e-mail dotyczącej odzyskiwania. Użytkownik wprowadza swój adres e-mail, a następnie wybiera pozycję **Wyślij kod weryfikacyjny**. Kod jest wysyłany do skrzynki odbiorczej poczty e-mail użytkownika, którą można pobrać i wprowadzić w polu **kod weryfikacyjny** . Następnie użytkownik wybiera polecenie **Weryfikuj kod**. 

Po zweryfikowaniu kodu użytkownik wybiera pozycję **Utwórz** , aby utworzyć konto. Lub jeśli użytkownik chce użyć innego adresu e-mail, może wybrać pozycję **Zmień wiadomość e-mail**.

![Użytkownik tworzy konto](media/phone-authentication/email-verification.png)

### <a name="phone-sign-in-experience"></a>Środowisko logowania przy użyciu telefonu

Jeśli użytkownik ma konto z numerem telefonu w postaci identyfikatora, użytkownik wprowadza numer telefonu i wybiera pozycję **Kontynuuj**. Potwierdzają one kraj i numer telefonu, wybierając pozycję **Kontynuuj**, a jednorazowy kod weryfikacyjny jest wysyłany do telefonu. Użytkownik wprowadzi kod weryfikacyjny i wybierze opcję **Kontynuuj** , aby się zalogować.

![Środowisko użytkownika logowania do telefonu](media/phone-authentication/phone-signin-screens.png)

## <a name="deleting-a-user-account"></a>Usuwanie konta użytkownika

W niektórych przypadkach może być konieczne usunięcie użytkownika i skojarzonych z nim danych z katalogu Azure AD B2C. Aby uzyskać szczegółowe informacje na temat sposobu usuwania konta użytkownika za pomocą Azure Portal, zapoznaj się z [tymi instrukcjami](/microsoft-365/compliance/gdpr-dsr-azure#step-5-delete). 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]



## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem uwierzytelniania OTP wymagane są następujące zasoby.

* [Dzierżawa Azure AD B2C](tutorial-create-tenant.md)
* [Aplikacja sieci Web zarejestrowana](tutorial-register-applications.md) w dzierżawie
* [Zasady niestandardowe](custom-policy-get-started.md) przekazane do dzierżawy

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Pobierz konto startowe & logowania za pomocą telefonu

Zacznij od aktualizacji niestandardowych plików zasad rejestracji i logowania na telefonie, aby współpracowały z dzierżawą Azure AD B2C.

1. Znajdź [pliki zasad niestandardowych rejestracji i logowania na telefonie][starter-pack-phone] w lokalnym klonie repozytorium pakietu początkowego lub Pobierz je bezpośrednio. Pliki zasad XML znajdują się w następującym katalogu:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. W każdym pliku Zastąp ciąg `yourtenant` nazwą dzierżawy Azure AD B2C. Na przykład jeśli nazwa dzierżawy usługi B2C jest *contosob2c*, wszystkie wystąpienia `yourtenant.onmicrosoft.com` stają się dostępne `contosob2c.onmicrosoft.com` .

1. Wykonaj kroki opisane w sekcji [Dodawanie identyfikatorów aplikacji do zasad niestandardowych](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) [w temacie Rozpoczynanie pracy z zasadami niestandardowymi w Azure Active Directory B2C](custom-policy-get-started.md). W takim przypadku należy zaktualizować `/phone-number-passwordless/` **`Phone_Email_Base.xml`** za pomocą **identyfikatorów aplikacji (klienta)** dwóch aplikacji zarejestrowanych podczas kończenia wymagań wstępnych, *IdentityExperienceFramework* i *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>Przekazywanie plików zasad

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do dzierżawy Azure AD B2C.
1. W obszarze **zasady** wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz pozycję **Przekaż zasady niestandardowe**.
1. Przekaż pliki zasad w następującej kolejności:
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone.xml*
    1. *SignUpOrSignInWithPhoneOrEmail.xml*
    1. *ProfileEditPhoneOnly.xml*
    1. *ProfileEditPhoneEmail.xml*
    1. *ChangePhoneNumber.xml*
    1. *PasswordResetEmail.xml*

Podczas przekazywania każdego pliku, platforma Azure dodaje prefiks `B2C_1A_` .

## <a name="test-the-custom-policy"></a>Testowanie zasad niestandardowych

1. W obszarze **zasady niestandardowe** wybierz pozycję **B2C_1A_SignUpOrSignInWithPhone**.
1. W obszarze **Wybierz aplikację** wybierz aplikację *webapp1* , która została zarejestrowana przy wypełnianiu wymagań wstępnych.
1. Dla **opcji wybierz adres URL odpowiedzi** wybierz opcję `https://jwt.ms` .
1. Wybierz pozycję **Uruchom teraz** i zarejestruj się przy użyciu adresu e-mail lub numeru telefonu.
1. Wybierz pozycję **Uruchom teraz** ponownie i zaloguj się przy użyciu tego samego konta, aby upewnić się, że konfiguracja jest poprawna.

## <a name="get-user-account-by-phone-number"></a>Pobierz konto użytkownika według numeru telefonu

Użytkownik, który zarejestruje się przy użyciu numeru telefonu, bez adresu e-mail odzyskiwania jest rejestrowany w katalogu Azure AD B2C i numerem telefonu jako nazwy logowania. Aby zmienić numer telefonu, dział pomocy technicznej lub zespół pomocy technicznej musi najpierw znaleźć swoje konto, a następnie zaktualizować numer telefonu.

Możesz znaleźć użytkownika według numeru telefonu (nazwy logowania), korzystając z [Microsoft Graph](microsoft-graph-operations.md):

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Przykład:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>Następne kroki

Możesz znaleźć pakiet startowy zasad dotyczących rejestracji i logowania na telefonie (oraz inne pakiety startowe) w witrynie GitHub: [Azure-Samples/Active-Directory-B2C-Custom-Policy-starterpack/scenariusze/telefon-number-Password][starter-pack-phone] pliki zasad pakietu początkowego używają profilów technicznych usługi uwierzytelniania wieloskładnikowego i przekształceń oświadczeń numeru telefonu:
* [Zdefiniuj profil techniczny usługi Azure AD Multi-Factor Authentication](multi-factor-auth-technical-profile.md)
* [Definiowanie przekształceń oświadczeń numeru telefonu](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
