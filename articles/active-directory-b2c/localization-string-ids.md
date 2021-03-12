---
title: Identyfikatory ciągów lokalizacji — Azure Active Directory B2C | Microsoft Docs
description: Określ identyfikatory dla definicji zawartości z IDENTYFIKATORem API. signuporsignin w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/10/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b60b447d8302b89813ca462c3220603ef926eb26
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102631187"
---
# <a name="localization-string-ids"></a>Identyfikatory ciągów lokalizacji

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **Lokalizacja** umożliwia obsługę wielu ustawień regionalnych lub języków w zasadach dla podróży użytkownika. Ten artykuł zawiera listę identyfikatorów lokalizacji, których można użyć w ramach zasad. Aby zapoznać się z lokalizacją interfejsu użytkownika, zobacz [Lokalizacja](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Elementy strony do rejestracji lub logowania

Następujące identyfikatory są używane dla definicji zawartości z IDENTYFIKATORem `api.signuporsignin` i z [własnym profilem technicznym](self-asserted-technical-profile.md).

| ID (Identyfikator) | Wartość domyślna | Wersja układu strony |
| -- | ------------- | ------ |
| **forgotpassword_link** | Forgot your password? | `All` |
| **createaccount_intro** | Nie masz konta? | `All` |
| **button_signin** | Zaloguj | `All` |
| **social_intro** | Zaloguj się przy użyciu konta społecznościowego | `All` |
| **remember_me** |Nie wylogowuj mnie. | `All` |
| **unknown_error** | Mamy problemy z zalogowaniem się. Spróbuj ponownie później. | `All` |
| **divider_title** | LUB | `All` |
| **local_intro_email** | Zaloguj się przy użyciu istniejącego konta | `< 2.0.0` |
| **logonIdentifier_email** | Adres e-mail | `< 2.0.0` |
| **requiredField_email** | Wprowadź swój adres e-mail | `< 2.0.0` |
| **invalid_email** | Wprowadź prawidłowy adres e-mail | `< 2.0.0` |
| **email_pattern** | ^ [a-za-Z0-9.! # $% & "" \* +/=? ^ \_ \` { \| } ~-] + @ [a-za-Z0-9-] + (?: \\ . [ a-za-Z0-9-] +) \* $ |`< 2.0.0` |
| **local_intro_username** | Zaloguj się przy użyciu nazwy użytkownika | `< 2.0.0` |
| **logonIdentifier_username** | Nazwa użytkownika | `< 2.0.0` |
| **requiredField_username** | Wprowadź swoją nazwę użytkownika | `< 2.0.0` |
| **hasło** | Hasło | `< 2.0.0` |
| **requiredField_password** | Wprowadź hasło | `< 2.0.0` |
| **createaccount_link** | Utwórz konto teraz | `< 2.0.0` |
| **cancel_message** | Użytkownik zapomniał hasła | `< 2.0.0` |
| **invalid_password** | Wprowadzone hasło ma nieoczekiwany format. | `< 2.0.0` |
| **createaccount_one_link** | Utwórz konto teraz | `>= 2.0.0` |
| **createaccount_two_links** | Zarejestruj się w usłudze {0} lub {1} | `>= 2.0.0` |
| **createaccount_three_links** | Zarejestruj się w usłudze {0} , {1} lub {2} | `>= 2.0.0` |
| **local_intro_generic** | Zaloguj się przy użyciu swojego {0} | `>= 2.1.0` |
| **requiredField_generic** | Wprowadź swój {0} | `>= 2.1.0` |
| **invalid_generic** | Wprowadź prawidłową {0} | `>= 2.1.1` |
| **Nagłówek** | Zaloguj | `>= 2.1.1` |


> [!NOTE]
> * Symbole zastępcze, takie jak {0} , są wypełniane automatycznie przy użyciu `DisplayName` wartości `ClaimType` . 
> * Aby dowiedzieć się, jak lokalizować `ClaimType` , zobacz [przykład rejestracji lub logowania](#signupsigninexample).

Poniższy przykład pokazuje użycie niektórych elementów interfejsu użytkownika na stronie rejestracji lub logowania:

:::image type="content" source="./media/localization-string-ids/localization-susi-2.png" alt-text="Zrzut ekranu przedstawiający stronę rejestracji lub logowania U góry elementów.":::

### <a name="sign-up-or-sign-in-identity-providers"></a>Dostawcy tożsamości rejestracji lub logowania

Identyfikator dostawców tożsamości jest konfigurowany w elemencie  **ClaimsExchange** podróży użytkownika. Aby zlokalizować tytuł dostawcy tożsamości, **ElementType** ma wartość `ClaimsProvider` , a **STRINGID** jest ustawiony na identyfikator `ClaimsExchange` .

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Poniższy przykład lokalizuje dostawcę tożsamości w serwisie Facebook do języka arabskiego:

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

### <a name="sign-up-or-sign-in-error-messages"></a>Komunikaty o błędach rejestracji lub logowania

| ID (Identyfikator) | Wartość domyślna |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | Hasło jest niepoprawne. |
| **UserMessageIfPasswordExpired**| Twoje hasło wygasło.|
| **UserMessageIfClaimsPrincipalDoesNotExist** | Nie możemy znaleźć Twojego konta. |
| **UserMessageIfOldPasswordUsed** | Wygląda na to, że użyto starego hasła. |
| **DefaultMessage** | Nieprawidłowa nazwa użytkownika lub hasło. |
| **UserMessageIfUserAccountDisabled** | Twoje konto zostało zablokowane. Skontaktuj się z pomocą techniczną, aby ją odblokować, a następnie spróbuj ponownie. |
| **UserMessageIfUserAccountLocked** | Twoje konto jest tymczasowo zablokowane, aby zapobiec nieautoryzowanemu użyciu. Spróbuj ponownie później. |
| **AADRequestsThrottled** | W tej chwili jest zbyt wiele żądań. Poczekaj chwilę i spróbuj ponownie. |

<a name="signupsigninexample"></a>
### <a name="sign-up-or-sign-in-example"></a>Przykład rejestracji lub logowania

```xml
<LocalizedResources Id="api.signuporsignin.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="heading">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="social_intro">Sign in with your social account</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_intro_generic">Sign in with your {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_password">Please enter your password</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_generic">Please enter your {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_generic">Please enter a valid {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_one_link">Sign up now</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_two_links">Sign up with {0} or {1}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_three_links">Sign up with {0}, {1}, or {2}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">Forgot your password?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_signin">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="divider_title">OR</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="unknown_error">We are having trouble signing you in. Please try again later.</LocalizedString>
    <!-- Uncomment the remember_me only if the keep me signed in is activated. 
    <LocalizedString ElementType="UxElement" StringId="remember_me">Keep me signed in</LocalizedString> -->
    <LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfPasswordExpired">Your password has expired.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">Looks like you used an old password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">Invalid username or password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Elementy interfejsu użytkownika dotyczące rejestracji i samopotwierdzonej strony

Poniżej przedstawiono identyfikatory dla definicji zawartości z IDENTYFIKATORem `api.localaccountsignup` lub dowolną definicją zawartości, która rozpoczyna się od `api.selfasserted` , takich jak `api.selfasserted.profileupdate` i i z `api.localaccountpasswordreset` [własnym profilem technicznym](self-asserted-technical-profile.md).

| ID (Identyfikator) | Wartość domyślna |
| -- | ------------- |
| **ver_sent** | Kod weryfikacyjny został wysłany do: |
| **ver_but_default** | Domyślne |
| **cancel_message** | Użytkownik anulował wprowadzanie niepotwierdzonych informacji |
| **preloader_alt** | Czekaj |
| **ver_but_send** | Wyślij kod weryfikacyjny |
| **alert_yes** | Tak |
| **error_fieldIncorrect** | Co najmniej jedno pole jest wypełniane nieprawidłowo. Sprawdź swoje wpisy i spróbuj ponownie. |
| **czteroletniego** | Year (Rok) |
| **verifying_blurb** | Zaczekaj na przetworzenie informacji. |
| **button_cancel** | Anuluj |
| **ver_fail_no_retry** | Wykonano zbyt wiele nieudanych prób. Spróbuj ponownie później. |
| **bieżącym** | Month (Miesiąc) |
| **ver_success_msg** | Zweryfikowano adres E-mail. Teraz można kontynuować. |
| **months** | Styczeń, luty, Marzec, Kwiecień, maj, czerwiec, Lipiec, sierpień, wrzesień, październik, listopad, grudzień |
| **ver_fail_server** | Wystąpił problem podczas weryfikowania Twojego adresu e-mail. Wprowadź prawidłowy adres e-mail i spróbuj ponownie. |
| **error_requiredFieldMissing** | Brak wymaganego pola. Wypełnij wszystkie wymagane pola i spróbuj ponownie. |
| **initial_intro** | Podaj następujące informacje. |
| **ver_but_resend** | Wyślij nowy kod |
| **button_continue** | Utwórz |
| **error_passwordEntryMismatch** | Pola wprowadzania hasła nie są zgodne. Wprowadź to samo hasło w obu polach i spróbuj ponownie. |
| **ver_incorrect_format** | Niepoprawny format. |
| **ver_but_edit** | Zmień adres e-mail |
| **ver_but_verify** | Zweryfikuj kod |
| **alert_no** | Nie |
| **ver_info_msg** | Kod weryfikacyjny został wysłany do Twojej skrzynki odbiorczej. Skopiuj ją do poniższego pola wejściowego. |
| **dzień** | Dzień |
| **ver_fail_throttled** | Zbyt wiele żądań do zweryfikowania tego adresu e-mail. Zaczekaj chwilę, a następnie spróbuj ponownie. |
| **helplink_text** | Co to jest? |
| **ver_fail_retry** | Ten kod jest nieprawidłowy. Spróbuj ponownie. |
| **alert_title** | Anuluj wprowadzanie szczegółów |
| **required_field** | Te informacje są wymagane. |
| **alert_message** | Czy na pewno chcesz anulować wprowadzanie szczegółów? |
| **ver_intro_msg** | Konieczna jest weryfikacja. Kliknij przycisk Wyślij. |
| **ver_input** | Kod weryfikacyjny |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>Komunikaty o błędach dotyczące rejestracji i niepotwierdzonych stron

| ID (Identyfikator) | Wartość domyślna |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Użytkownik o określonym IDENTYFIKATORze już istnieje. Wybierz inną. |
| **UserMessageIfClaimNotVerified** | Nie zweryfikowano zgłoszenia: {0} |
| **UserMessageIfIncorrectPattern** | Nieprawidłowy wzorzec dla: {0} |
| **UserMessageIfMissingRequiredElement** | Brak wymaganego elementu: {0} |
| **UserMessageIfValidationError** | Błąd weryfikacji przez: {0} |
| **UserMessageIfInvalidInput** | {0} ma nieprawidłowe dane wejściowe. |
| **Brak ograniczenia** | W tej chwili jest zbyt wiele żądań. Poczekaj chwilę i spróbuj ponownie. |

W poniższym przykładzie pokazano użycie niektórych elementów interfejsu użytkownika na stronie rejestracji:

![Strona rejestracji z nazwami elementów interfejsu użytkownika z etykietą](./media/localization-string-ids/localization-sign-up.png)

Poniższy przykład pokazuje użycie niektórych elementów interfejsu użytkownika na stronie rejestracji, po kliknięciu przycisku Wyślij kod weryfikacyjny przez użytkownika:

![Strona rejestracji — elementy środowiska użytkownika weryfikacji wiadomości e-mail](./media/localization-string-ids/localization-email-verification.png)

## <a name="sign-up-and-self-asserted-pages-example"></a>Przykład rejestracji i stron z własnym potwierdzeniem

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Email address that can be used to contact you.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">Enter new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">Confirm New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">Confirm new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">Display Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">Your display name.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">Surname</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">Your surname (also known as family name or last name).</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">Given Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">Your given name (also known as first name).</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">A required field is missing. Please fill out all required fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="helplink_text">What is this?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please provide the following details.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="preloader_alt">Please wait</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="required_field">This information is required.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_edit">Change e-mail</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_resend">Send new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_send">Send verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_verify">Verify code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">That code is incorrect. Please try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_server">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_info_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_input">Verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_success_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">Claim not verified: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">A user with the specified ID already exists. Please choose a different one.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">Incorrect pattern for: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">{0} has invalid input.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">Missing required element: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">Error in validation by: {0}</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="phone-factor-authentication-page-user-interface-elements"></a>Elementy interfejsu użytkownika na stronie uwierzytelnianie w ramach wskaźnika telefonu

Poniżej znajdują się identyfikatory definicji zawartości z IDENTYFIKATORem `api.phonefactor` i [profilem technicznym dla fabryki telefonu](phone-factor-technical-profile.md).

| ID (Identyfikator) | Wartość domyślna |
| -- | ------------- |
| **button_verify** | Zadzwoń do mnie |
| **country_code_label** | Kod kraju |
| **cancel_message** | Użytkownik anulował uwierzytelnianie wieloskładnikowe |
| **text_button_send_second_code** | Wyślij nowy kod |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Mamy dla Ciebie następujący numer rekordu. Możemy wysłać kod za pośrednictwem wiadomości SMS lub telefonu w celu uwierzytelnienia użytkownika. |
| **intro_mixed_p** | W rekordach są dostępne następujące numery. Wybierz numer telefonujący lub Wyślij kod za pośrednictwem wiadomości SMS w celu uwierzytelnienia użytkownika. |
| **button_verify_code** | Weryfikuj kod |
| **requiredField_code** | Wprowadź otrzymany kod weryfikacyjny |
| **invalid_code** | Wprowadź otrzymany 6-cyfrowy kod |
| **button_cancel** | Anuluj |
| **local_number_input_placeholder_text** | Numer telefonu |
| **button_retry** | Ponów próbę |
| **alternative_text** | Nie mam mojego telefonu |
| **intro_phone_p** | W rekordach są dostępne następujące numery. Wybierz numer telefonu, z którym możemy się uwierzytelnić. |
| **intro_phone** | Mamy dla Ciebie następujący numer rekordu. Będziemy telefonować. |
| **enter_code_text_intro** | Wprowadź kod weryfikacyjny poniżej lub  |
| **intro_entry_phone** | Wprowadź numer poniżej, aby można było uwierzytelnić się za pomocą telefonu. |
| **intro_entry_sms** | Wprowadź numer poniżej, aby można było wysłać kod za pośrednictwem wiadomości SMS w celu uwierzytelnienia użytkownika. |
| **button_send_code** | Wyślij kod |
| **invalid_number** | Wprowadź prawidłowy numer telefonu |
| **intro_sms** | Mamy dla Ciebie następujący numer rekordu. Wyślemy kod za pośrednictwem wiadomości SMS w celu uwierzytelnienia użytkownika. |
| **intro_entry_mixed** | Wprowadź numer poniżej, aby można było wysłać kod za pośrednictwem wiadomości SMS lub telefonu w celu uwierzytelnienia użytkownika. |
| **number_pattern** | ^\\+ (?: [0-9] [ \\ x20-]?) {6,14} [0-9] $ |
| **intro_sms_p** |W rekordach są dostępne następujące numery. Wybierz numer, który możemy wysłać kod za pośrednictwem wiadomości SMS w celu uwierzytelnienia użytkownika. |
| **requiredField_countryCode** | Wybierz swój kod kraju |
| **requiredField_number** | Wprowadź numer telefonu |
| **country_code_input_placeholder_text** |Kraj lub region |
| **number_label** | Numer telefonu |
| **error_tryagain** | Podany numer telefonu jest zajęty lub niedostępny. Sprawdź numer i spróbuj ponownie. |
| **error_incorrect_code** | Wprowadzony kod weryfikacyjny nie jest zgodny z naszymi rekordami. Spróbuj ponownie lub zażądaj nowego kodu. |
| **countryList** | Zobacz [listę krajów](#phone-factor-authentication-page-example). |
| **error_448** | Podany numer telefonu jest nieosiągalny. |
| **error_449** | Użytkownik przekroczył liczbę ponownych prób. |
| **verification_code_input_placeholder_text** | Kod weryfikacyjny |

W poniższym przykładzie pokazano użycie niektórych elementów interfejsu użytkownika na stronie rejestracji usługi MFA:

![Elementy środowiska użytkownika rejestracji uwierzytelniania w usłudze Factor Authentication](./media/localization-string-ids/localization-mfa1.png)

Poniższy przykład pokazuje użycie niektórych elementów interfejsu użytkownika na stronie walidacji usługi MFA:

![Elementy środowiska użytkownika weryfikacji uwierzytelniania w składniku telefonu](./media/localization-string-ids/localization-mfa2.png)

## <a name="phone-factor-authentication-page-example"></a>Przykład strony z uwierzytelnianiem przez składnik telefonu

```xml
<LocalizedResources Id="api.phonefactor.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="button_verify">Call Me</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_label">Country Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="cancel_message">The user has canceled multi-factor authentication</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="text_button_send_second_code">Send a new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="code_pattern">\d{6}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed">We have the following number on record for you. We can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed_p">We have the following numbers on record for you. Choose a number that we can phone or send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_code">Please enter the verification code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_code">Please enter the 6-digit code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_number_input_placeholder_text">Phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_retry">Retry</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="alternative_text">I don't have my phone</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone_p">We have the following numbers on record for you. Choose a number that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone">We have the following number on record for you. We will phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="enter_code_text_intro">Enter your verification code below, or</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_phone">Enter a number below that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_sms">Enter a number below that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_number">Please enter a valid phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms">We have the following number on record for you. We will send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_mixed">Enter a number below that we can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_pattern">^\+(?:[0-9][\x20-]?){6,14}[0-9]$</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms_p">We have the following numbers on record for you. Choose a number that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_countryCode">Please select your country code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_number">Please enter your phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_input_placeholder_text">Country or region</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_label">Phone Number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_tryagain">The phone number you provided is busy or unavailable. Please check the number and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_incorrect_code">The verification code you have entered does not match our records. Please try again, or request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="countryList">{"DEFAULT":"Country/Region","AF":"Afghanistan","AX":"Åland Islands","AL":"Albania","DZ":"Algeria","AS":"American Samoa","AD":"Andorra","AO":"Angola","AI":"Anguilla","AQ":"Antarctica","AG":"Antigua and Barbuda","AR":"Argentina","AM":"Armenia","AW":"Aruba","AU":"Australia","AT":"Austria","AZ":"Azerbaijan","BS":"Bahamas","BH":"Bahrain","BD":"Bangladesh","BB":"Barbados","BY":"Belarus","BE":"Belgium","BZ":"Belize","BJ":"Benin","BM":"Bermuda","BT":"Bhutan","BO":"Bolivia","BQ":"Bonaire","BA":"Bosnia and Herzegovina","BW":"Botswana","BV":"Bouvet Island","BR":"Brazil","IO":"British Indian Ocean Territory","VG":"British Virgin Islands","BN":"Brunei","BG":"Bulgaria","BF":"Burkina Faso","BI":"Burundi","CV":"Cabo Verde","KH":"Cambodia","CM":"Cameroon","CA":"Canada","KY":"Cayman Islands","CF":"Central African Republic","TD":"Chad","CL":"Chile","CN":"China","CX":"Christmas Island","CC":"Cocos (Keeling) Islands","CO":"Colombia","KM":"Comoros","CG":"Congo","CD":"Congo (DRC)","CK":"Cook Islands","CR":"Costa Rica","CI":"Côte d'Ivoire","HR":"Croatia","CU":"Cuba","CW":"Curaçao","CY":"Cyprus","CZ":"Czech Republic","DK":"Denmark","DJ":"Djibouti","DM":"Dominica","DO":"Dominican Republic","EC":"Ecuador","EG":"Egypt","SV":"El Salvador","GQ":"Equatorial Guinea","ER":"Eritrea","EE":"Estonia","ET":"Ethiopia","FK":"Falkland Islands","FO":"Faroe Islands","FJ":"Fiji","FI":"Finland","FR":"France","GF":"French Guiana","PF":"French Polynesia","TF":"French Southern Territories","GA":"Gabon","GM":"Gambia","GE":"Georgia","DE":"Germany","GH":"Ghana","GI":"Gibraltar","GR":"Greece","GL":"Greenland","GD":"Grenada","GP":"Guadeloupe","GU":"Guam","GT":"Guatemala","GG":"Guernsey","GN":"Guinea","GW":"Guinea-Bissau","GY":"Guyana","HT":"Haiti","HM":"Heard Island and McDonald Islands","HN":"Honduras","HK":"Hong Kong SAR","HU":"Hungary","IS":"Iceland","IN":"India","ID":"Indonesia","IR":"Iran","IQ":"Iraq","IE":"Ireland","IM":"Isle of Man","IL":"Israel","IT":"Italy","JM":"Jamaica","JP":"Japan","JE":"Jersey","JO":"Jordan","KZ":"Kazakhstan","KE":"Kenya","KI":"Kiribati","KR":"Korea","KW":"Kuwait","KG":"Kyrgyzstan","LA":"Laos","LV":"Latvia","LB":"Lebanon","LS":"Lesotho","LR":"Liberia","LY":"Libya","LI":"Liechtenstein","LT":"Lithuania","LU":"Luxembourg","MO":"Macao SAR","MK":"North Macedonia","MG":"Madagascar","MW":"Malawi","MY":"Malaysia","MV":"Maldives","ML":"Mali","MT":"Malta","MH":"Marshall Islands","MQ":"Martinique","MR":"Mauritania","MU":"Mauritius","YT":"Mayotte","MX":"Mexico","FM":"Micronesia","MD":"Moldova","MC":"Monaco","MN":"Mongolia","ME":"Montenegro","MS":"Montserrat","MA":"Morocco","MZ":"Mozambique","MM":"Myanmar","NA":"Namibia","NR":"Nauru","NP":"Nepal","NL":"Netherlands","NC":"New Caledonia","NZ":"New Zealand","NI":"Nicaragua","NE":"Niger","NG":"Nigeria","NU":"Niue","NF":"Norfolk Island","KP":"North Korea","MP":"Northern Mariana Islands","NO":"Norway","OM":"Oman","PK":"Pakistan","PW":"Palau","PS":"Palestinian Authority","PA":"Panama","PG":"Papua New Guinea","PY":"Paraguay","PE":"Peru","PH":"Philippines","PN":"Pitcairn Islands","PL":"Poland","PT":"Portugal","PR":"Puerto Rico","QA":"Qatar","RE":"Réunion","RO":"Romania","RU":"Russia","RW":"Rwanda","BL":"Saint Barthélemy","KN":"Saint Kitts and Nevis","LC":"Saint Lucia","MF":"Saint Martin","PM":"Saint Pierre and Miquelon","VC":"Saint Vincent and the Grenadines","WS":"Samoa","SM":"San Marino","ST":"São Tomé and Príncipe","SA":"Saudi Arabia","SN":"Senegal","RS":"Serbia","SC":"Seychelles","SL":"Sierra Leone","SG":"Singapore","SX":"Sint Maarten","SK":"Slovakia","SI":"Slovenia","SB":"Solomon Islands","SO":"Somalia","ZA":"South Africa","GS":"South Georgia and South Sandwich Islands","SS":"South Sudan","ES":"Spain","LK":"Sri Lanka","SH":"St Helena, Ascension, Tristan da Cunha","SD":"Sudan","SR":"Suriname","SJ":"Svalbard","SZ":"Swaziland","SE":"Sweden","CH":"Switzerland","SY":"Syria","TW":"Taiwan","TJ":"Tajikistan","TZ":"Tanzania","TH":"Thailand","TL":"Timor-Leste","TG":"Togo","TK":"Tokelau","TO":"Tonga","TT":"Trinidad and Tobago","TN":"Tunisia","TR":"Turkey","TM":"Turkmenistan","TC":"Turks and Caicos Islands","TV":"Tuvalu","UM":"U.S. Outlying Islands","VI":"U.S. Virgin Islands","UG":"Uganda","UA":"Ukraine","AE":"United Arab Emirates","GB":"United Kingdom","US":"United States","UY":"Uruguay","UZ":"Uzbekistan","VU":"Vanuatu","VA":"Vatican City","VE":"Venezuela","VN":"Vietnam","WF":"Wallis and Futuna","YE":"Yemen","ZM":"Zambia","ZW":"Zimbabwe"}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_448">The phone number you provided is unreachable.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_449">User has exceeded the number of retry attempts.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_code_input_placeholder_text">Verification code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="strongAuthenticationPhoneNumber" StringId="DisplayName">Phone Number</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>

```

## <a name="verification-display-control-user-interface-elements"></a>Elementy interfejsu użytkownika kontrolki wyświetlania weryfikacji

Poniżej przedstawiono identyfikatory [kontrolki wyświetlania weryfikacyjnej](display-control-verification.md) z [układem strony w wersji](page-layout.md) 2.1.0 lub nowszej.

| ID (Identyfikator) | Wartość domyślna |
| -- | ------------- |
|intro_msg <sup>*</sup>| Konieczna jest weryfikacja. Kliknij przycisk Wyślij.|
|success_send_code_msg | Kod weryfikacyjny został wysłany. Skopiuj ją do poniższego pola wejściowego.|
|failure_send_code_msg | Wystąpił problem podczas weryfikowania Twojego adresu e-mail. Wprowadź prawidłowy adres e-mail i spróbuj ponownie.|
|success_verify_code_msg | Zweryfikowano adres E-mail. Teraz można kontynuować.|
|failure_verify_code_msg | Wystąpił problem podczas weryfikowania Twojego adresu e-mail. Spróbuj ponownie.|
|but_send_code | Wyślij kod weryfikacyjny|
|but_verify_code | Zweryfikuj kod|
|but_send_new_code | Wyślij nowy kod|
|but_change_claims | Zmień adres e-mail|

Uwaga: `intro_msg` element jest ukryty i nie jest wyświetlany na stronie z własnym poproszonym. Aby je wyświetlić, użyj [Customiztion HTML](customize-ui-with-html.md) z kaskadowe arkusze stylów. Na przykład:
    
```css
.verificationInfoText div{display: block!important}
```

### <a name="verification-display-control-example"></a>Przykład kontrolki wyświetlania weryfikacji

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
   <!-- Display control UI elements-->
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_send_code_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_send_code_msg">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_verify_code_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_verify_code_msg">We are having trouble verifying your email address. Please try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_new_code">Send new code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_change_claims">Change e-mail</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="verification-display-control-user-interface-elements-deprecated"></a>Elementy interfejsu użytkownika kontrolki wyświetlania weryfikacji (przestarzałe)

Poniżej przedstawiono identyfikatory [kontrolki wyświetlania weryfikacyjnej](display-control-verification.md) z [wersją układu strony](page-layout.md) 2.0.0.

| ID (Identyfikator) | Wartość domyślna |
| -- | ------------- |
|verification_control_but_change_claims |Zmiana |
|verification_control_fail_send_code |Nie można wysłać kodu, spróbuj ponownie później. |
|verification_control_fail_verify_code |Nie można zweryfikować kodu, spróbuj ponownie później. |
|verification_control_but_send_code |Wyślij kod |
|verification_control_but_send_new_code |Wyślij nowy kod |
|verification_control_but_verify_code |Weryfikuj kod |
|verification_control_code_sent| Kod weryfikacyjny został wysłany. Skopiuj ją do poniższego pola wejściowego. |

### <a name="verification-display-control-example-deprecated"></a>Przykład kontrolki wyświetlania weryfikacji (przestarzałe)

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_change_claims">Change</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_send_code">Failed to send the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_verify_code">Failed to verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_new_code">Send New Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_code_sent">Verification code has been sent. Please copy it to the input box below.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="restful-service-error-messages"></a>Komunikaty o błędach usługi RESTful

Poniżej przedstawiono identyfikatory komunikatów o błędach [profilu technicznego usługi RESTful](restful-technical-profile.md) :

| ID (Identyfikator) | Wartość domyślna |
| -- | ------------- |
|DefaultUserMessageIfRequestFailed | Nie można nawiązać połączenia z punktem końcowym usługi RESTful. Adres URL usługi RESTful: {0} |
|UserMessageIfCircuitOpen | {0} Adres URL usługi RESTful: {1} |
|UserMessageIfDnsResolutionFailed | Nie można rozpoznać nazwy hosta punktu końcowego usługi RESTful. Adres URL usługi RESTful: {0} |
|UserMessageIfRequestTimeout | Nie można nawiązać połączenia z punktem końcowym usługi RESTful w ramach limitu czasu w {0} sekundach. Adres URL usługi RESTful: {1} |


### <a name="restful-service-example"></a>Przykład usługi RESTful Service

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultUserMessageIfRequestFailed">Failed to establish connection to restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCircuitOpen">Unable to connect to the restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfDnsResolutionFailed">Failed to resolve the hostname of the restful service endpoint.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfRequestTimeout">Failed to establish connection to restful service end point within timeout limit.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-mfa-error-messages"></a>Komunikaty o błędach usługi Azure AD MFA

Poniżej przedstawiono identyfikatory dla komunikatu o błędzie [profilu technicznego usługi Azure AD MFA](multi-factor-auth-technical-profile.md) :

| ID (Identyfikator) | Wartość domyślna |
| -- | ------------- |
|UserMessageIfCouldntSendSms | Nie można wysłać wiadomości SMS na telefon, spróbuj użyć innego numeru telefonu. |
|UserMessageIfInvalidFormat | Twój numer telefonu ma nieprawidłowy format, popraw go i spróbuj ponownie.|
|UserMessageIfMaxAllowedCodeRetryReached | Nieprawidłowy kod został wprowadzony zbyt wiele razy, spróbuj ponownie później.|
|UserMessageIfServerError | Nie można użyć usługi MFA, spróbuj ponownie później.|
|UserMessageIfThrottled | Twoje żądanie zostało ograniczone, spróbuj ponownie później.|
|UserMessageIfWrongCodeEntered|Wprowadzono nieprawidłowy kod, spróbuj ponownie.|

### <a name="azure-ad-mfa-example"></a>Przykład usługi Azure AD MFA

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCouldntSendSms">Cannot Send SMS to the phone, please try another phone number.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidFormat">Your phone number is not in a valid format, please correct it and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxAllowedCodeRetryReached">Wrong code entered too many times, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfServerError">Cannot use MFA service, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">Your request has been throttled, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfWrongCodeEntered">Wrong code entered, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-sspr"></a>Samoobsługowe resetowanie hasła w usłudze Azure AD

Poniżej przedstawiono identyfikatory komunikatów o błędach [profilu technicznego usługi Azure AD SSPR](aad-sspr-technical-profile.md) :

| ID (Identyfikator) | Wartość domyślna |
| -- | ------------- |
|UserMessageIfChallengeExpired | Kod wygasł.|
|UserMessageIfInternalError | Usługa poczty e-mail napotkała błąd wewnętrzny, spróbuj ponownie później.|
|UserMessageIfThrottled | Wysłano zbyt wiele żądań, spróbuj ponownie później.|
|UserMessageIfVerificationFailedNoRetry | Przekroczono maksymalną liczbę prób weryfikacji.|
|UserMessageIfVerificationFailedRetryAllowed | Weryfikacja nie powiodła się, spróbuj ponownie.|


### <a name="azure-ad-sspr-example"></a>Przykład usługi Azure AD SSPR

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInternalError">We are having trouble verifying your email address. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfChallengeExpired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedNoRetry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="one-time-password-error-messages"></a>Komunikaty o błędach z hasłem jednorazowym

Poniżej przedstawiono identyfikatory komunikatów o błędach [profilu technicznego hasła jednorazowego](one-time-password-technical-profile.md)

| ID (Identyfikator) | Wartość domyślna |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |Weryfikacja o jednym czasie z podaną hasłem przekroczyła maksymalną liczbę prób |
|UserMessageIfSessionDoesNotExist |Sesja weryfikacji hasła jednorazowego wygasła |
|UserMessageIfSessionConflict |Konflikt sesji weryfikacji hasła jednorazowego |
|UserMessageIfInvalidCode |Hasło jednorazowe podane do weryfikacji jest nieprawidłowe |
|UserMessageIfVerificationFailedRetryAllowed |Ten kod jest nieprawidłowy. Spróbuj ponownie. | 

### <a name="one-time-password-example"></a>Przykład hasła jednorazowego

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="claims-transformations-error-messages"></a>Komunikaty o błędach przekształceń oświadczeń

Poniżej przedstawiono identyfikatory komunikatów o błędach przekształceń oświadczeń:

| ID (Identyfikator) | Przekształcanie oświadczeń | Wartość domyślna |
| -- | ------------- |------------- |
|UserMessageIfClaimsTransformationBooleanValueIsNotEqual |[AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) | Porównanie wartości wystąpienia logicznego nie powiodło się dla typu zgłoszenia "oświadczenie inputclaim".| 
|DateTimeGreaterThan |[AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan) | Porównywanie wartości dla roszczeń nie powiodło się: podany lewy operand jest większy niż prawy operand.|
|UserMessageIfClaimsTransformationStringsAreNotEqual |[AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal) | Porównywanie wartości w ramach roszczeń nie powiodło się przy użyciu StringComparison "OrdinalIgnoreCase".|

### <a name="claims-transformations-example"></a>Przykład przekształceń oświadczeń

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Your email address hasn't been verified.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DateTimeGreaterThan">Expiration date must be greater that the current date.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationStringsAreNotEqual">The email entry fields do not match. Please enter the same email address in both fields and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="next-steps"></a>Następne kroki

Przykłady lokalizacji można znaleźć w następujących artykułach:

- [Dostosowywanie języka za pomocą zasad niestandardowych w Azure Active Directory B2C](language-customization.md)
- [Dostosowywanie języka za pomocą przepływów użytkowników w Azure Active Directory B2C](language-customization.md)
