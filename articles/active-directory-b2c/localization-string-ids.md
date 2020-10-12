---
title: Identyfikatory ciągów lokalizacji — Azure Active Directory B2C | Microsoft Docs
description: Określ identyfikatory dla definicji zawartości z IDENTYFIKATORem API. signuporsignin w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 611d676f5f588ff32f981692456160e269642a43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87428118"
---
# <a name="localization-string-ids"></a>Identyfikatory ciągów lokalizacji

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **Lokalizacja** umożliwia obsługę wielu ustawień regionalnych lub języków w zasadach dla podróży użytkownika. Ten artykuł zawiera listę identyfikatorów lokalizacji, których można użyć w ramach zasad. Aby zapoznać się z lokalizacją interfejsu użytkownika, zobacz [Lokalizacja](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Elementy strony do rejestracji lub logowania

Następujące identyfikatory są używane dla definicji zawartości o IDENTYFIKATORze `api.signuporsignin` .

| ID (Identyfikator) | Wartość domyślna |
| -- | ------------- |
| **local_intro_email** | Zaloguj się przy użyciu istniejącego konta |
| **logonIdentifier_email** | Adres e-mail |
| **requiredField_email** | Wprowadź swój adres e-mail |
| **invalid_email** | Wprowadź prawidłowy adres e-mail |
| **email_pattern** | ^ [a-za-Z0-9.! # $% & ""*+/=? ^ _ \` { \| } ~-] + @ [a-za-Z0-9-] + (?: \\ . [ a-za-Z0-9-] +)*$ |
| **local_intro_username** | Zaloguj się przy użyciu nazwy użytkownika |
| **logonIdentifier_username** | Nazwa użytkownika |
| **requiredField_username** | Wprowadź swoją nazwę użytkownika |
| **hasło** | Hasło |
| **requiredField_password** | Wprowadź hasło |
| **invalid_password** | Wprowadzone hasło ma nieoczekiwany format. |
| **forgotpassword_link** | Forgot your password? |
| **createaccount_intro** | Nie masz konta? |
| **createaccount_link** | Utwórz konto teraz |
| **divider_title** | LUB |
| **cancel_message** | Użytkownik zapomniał hasła |
| **button_signin** | Zaloguj |
| **social_intro** | Zaloguj się przy użyciu konta społecznościowego |
  **remember_me** |Nie wylogowuj mnie|
| **unknown_error** | Mamy problemy z zalogowaniem się. Spróbuj ponownie później. |

Poniższy przykład pokazuje użycie niektórych elementów interfejsu użytkownika na stronie rejestracji lub logowania:

![Elementy środowiska użytkownika dotyczące rejestracji lub logowania](./media/localization-string-ids/localization-susi.png)

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
| **UserMessageIfClaimsPrincipalDoesNotExist** | Nie możemy znaleźć Twojego konta. |
| **UserMessageIfOldPasswordUsed** | Wygląda na to, że użyto starego hasła. |
| **DefaultMessage** | Nieprawidłowa nazwa użytkownika lub hasło. |
| **UserMessageIfUserAccountDisabled** | Twoje konto zostało zablokowane. Skontaktuj się z pomocą techniczną, aby ją odblokować, a następnie spróbuj ponownie. |
| **UserMessageIfUserAccountLocked** | Twoje konto jest tymczasowo zablokowane, aby zapobiec nieautoryzowanemu użyciu. Spróbuj ponownie później. |
| **AADRequestsThrottled** | W tej chwili jest zbyt wiele żądań. Poczekaj chwilę i spróbuj ponownie. |

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Elementy interfejsu użytkownika dotyczące rejestracji i samopotwierdzonej strony

Poniżej przedstawiono identyfikatory dla definicji zawartości z IDENTYFIKATORem `api.localaccountsignup` lub dowolną definicją zawartości, która rozpoczyna się od `api.selfasserted` , takich jak `api.selfasserted.profileupdate` i `api.localaccountpasswordreset` .

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


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Elementy interfejsu użytkownika na stronie uwierzytelnianie w ramach wskaźnika telefonu

Poniżej przedstawiono identyfikatory dla definicji zawartości o IDENTYFIKATORze `api.phonefactor` .

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
| **button_retry** | Ponawianie próby |
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
| **countryList** | Zobacz [listę krajów](#countries-list). |
| **error_448** | Podany numer telefonu jest nieosiągalny. |
| **error_449** | Użytkownik przekroczył liczbę ponownych prób. |
| **verification_code_input_placeholder_text** | Kod weryfikacyjny |

W poniższym przykładzie pokazano użycie niektórych elementów interfejsu użytkownika na stronie rejestracji usługi MFA:

![Strona rejestracji — elementy środowiska użytkownika weryfikacji wiadomości e-mail](./media/localization-string-ids/localization-mfa1.png)

Poniższy przykład pokazuje użycie niektórych elementów interfejsu użytkownika na stronie walidacji usługi MFA:

![Strona rejestracji — elementy środowiska użytkownika weryfikacji wiadomości e-mail](./media/localization-string-ids/localization-mfa2.png)

## <a name="verification-display-control-user-interface-elements"></a>Elementy interfejsu użytkownika kontrolki wyświetlania weryfikacji

Poniżej przedstawiono identyfikatory [kontrolki wyświetlania weryfikacji](display-control-verification.md)

| ID (Identyfikator) | Wartość domyślna |
| -- | ------------- |
|verification_control_but_change_claims |Zmiana |
|verification_control_fail_send_code |Nie można wysłać kodu, spróbuj ponownie później. |
|verification_control_fail_verify_code |Nie można zweryfikować kodu, spróbuj ponownie później. |
|verification_control_but_send_code |Wyślij kod |
|verification_control_but_send_new_code |Wyślij nowy kod |
|verification_control_but_verify_code |Weryfikuj kod |
|verification_control_code_sent| Kod weryfikacyjny został wysłany. Skopiuj ją do poniższego pola wejściowego. |

### <a name="example"></a>Przykład

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


### <a name="example"></a>Przykład

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

## <a name="azure-mfa-error-messages"></a>Komunikaty o błędach usługi Azure MFA

Poniżej przedstawiono identyfikatory komunikatów o błędach [profilu technicznego usługi Azure MFA](multi-factor-auth-technical-profile.md) :

| ID (Identyfikator) | Wartość domyślna |
| -- | ------------- |
|UserMessageIfCouldntSendSms | Nie można wysłać wiadomości SMS na telefon, spróbuj użyć innego numeru telefonu. |
|UserMessageIfInvalidFormat | Twój numer telefonu ma nieprawidłowy format, popraw go i spróbuj ponownie.|
|UserMessageIfMaxAllowedCodeRetryReached | Nieprawidłowy kod został wprowadzony zbyt wiele razy, spróbuj ponownie później.|
|UserMessageIfServerError | Nie można użyć usługi MFA, spróbuj ponownie później.|
|UserMessageIfThrottled | Twoje żądanie zostało ograniczone, spróbuj ponownie później.|
|UserMessageIfWrongCodeEntered|Wprowadzono nieprawidłowy kod, spróbuj ponownie.|

### <a name="example"></a>Przykład

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


### <a name="example"></a>Przykład

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

### <a name="example"></a>Przykład

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</LocalizedString>
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

### <a name="example"></a>Przykład

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Your email address hasn't been verified.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DateTimeGreaterThan">Expiration date must be greater that the current date.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationStringsAreNotEqual">The email entry fields do not match. Please enter the same email address in both fields and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="countries-list"></a>Lista krajów

Poniżej przedstawiono wartości countryList używane przez uwierzytelnianie wieloskładnikowe.

```JSON
{"DEFAULT":"Country/Region","AF":"Afghanistan","AX":"Åland Islands","AL":"Albania","DZ":"Algeria","AS":"American Samoa","AD":"Andorra","AO":"Angola","AI":"Anguilla","AQ":"Antarctica","AG":"Antigua and Barbuda","AR":"Argentina","AM":"Armenia","AW":"Aruba","AU":"Australia","AT":"Austria","AZ":"Azerbaijan","BS":"Bahamas","BH":"Bahrain","BD":"Bangladesh","BB":"Barbados","BY":"Belarus","BE":"Belgium","BZ":"Belize","BJ":"Benin","BM":"Bermuda","BT":"Bhutan","BO":"Bolivia","BQ":"Bonaire","BA":"Bosnia and Herzegovina","BW":"Botswana","BV":"Bouvet Island","BR":"Brazil","IO":"British Indian Ocean Territory","VG":"British Virgin Islands","BN":"Brunei","BG":"Bulgaria","BF":"Burkina Faso","BI":"Burundi","CV":"Cabo Verde","KH":"Cambodia","CM":"Cameroon","CA":"Canada","KY":"Cayman Islands","CF":"Central African Republic","TD":"Chad","CL":"Chile","CN":"China","CX":"Christmas Island","CC":"Cocos (Keeling) Islands","CO":"Colombia","KM":"Comoros","CG":"Congo","CD":"Congo (DRC)","CK":"Cook Islands","CR":"Costa Rica","CI":"Côte d'Ivoire","HR":"Croatia","CU":"Cuba","CW":"Curaçao","CY":"Cyprus","CZ":"Czech Republic","DK":"Denmark","DJ":"Djibouti","DM":"Dominica","DO":"Dominican Republic","EC":"Ecuador","EG":"Egypt","SV":"El Salvador","GQ":"Equatorial Guinea","ER":"Eritrea","EE":"Estonia","ET":"Ethiopia","FK":"Falkland Islands","FO":"Faroe Islands","FJ":"Fiji","FI":"Finland","FR":"France","GF":"French Guiana","PF":"French Polynesia","TF":"French Southern Territories","GA":"Gabon","GM":"Gambia","GE":"Georgia","DE":"Germany","GH":"Ghana","GI":"Gibraltar","GR":"Greece","GL":"Greenland","GD":"Grenada","GP":"Guadeloupe","GU":"Guam","GT":"Guatemala","GG":"Guernsey","GN":"Guinea","GW":"Guinea-Bissau","GY":"Guyana","HT":"Haiti","HM":"Heard Island and McDonald Islands","HN":"Honduras","HK":"Hong Kong SAR","HU":"Hungary","IS":"Iceland","IN":"India","ID":"Indonesia","IR":"Iran","IQ":"Iraq","IE":"Ireland","IM":"Isle of Man","IL":"Israel","IT":"Italy","JM":"Jamaica","JP":"Japan","JE":"Jersey","JO":"Jordan","KZ":"Kazakhstan","KE":"Kenya","KI":"Kiribati","KR":"Korea","KW":"Kuwait","KG":"Kyrgyzstan","LA":"Laos","LV":"Latvia","LB":"Lebanon","LS":"Lesotho","LR":"Liberia","LY":"Libya","LI":"Liechtenstein","LT":"Lithuania","LU":"Luxembourg","MO":"Macao SAR","MK":"North Macedonia","MG":"Madagascar","MW":"Malawi","MY":"Malaysia","MV":"Maldives","ML":"Mali","MT":"Malta","MH":"Marshall Islands","MQ":"Martinique","MR":"Mauritania","MU":"Mauritius","YT":"Mayotte","MX":"Mexico","FM":"Micronesia","MD":"Moldova","MC":"Monaco","MN":"Mongolia","ME":"Montenegro","MS":"Montserrat","MA":"Morocco","MZ":"Mozambique","MM":"Myanmar","NA":"Namibia","NR":"Nauru","NP":"Nepal","NL":"Netherlands","NC":"New Caledonia","NZ":"New Zealand","NI":"Nicaragua","NE":"Niger","NG":"Nigeria","NU":"Niue","NF":"Norfolk Island","KP":"North Korea","MP":"Northern Mariana Islands","NO":"Norway","OM":"Oman","PK":"Pakistan","PW":"Palau","PS":"Palestinian Authority","PA":"Panama","PG":"Papua New Guinea","PY":"Paraguay","PE":"Peru","PH":"Philippines","PN":"Pitcairn Islands","PL":"Poland","PT":"Portugal","PR":"Puerto Rico","QA":"Qatar","RE":"Réunion","RO":"Romania","RU":"Russia","RW":"Rwanda","BL":"Saint Barthélemy","KN":"Saint Kitts and Nevis","LC":"Saint Lucia","MF":"Saint Martin","PM":"Saint Pierre and Miquelon","VC":"Saint Vincent and the Grenadines","WS":"Samoa","SM":"San Marino","ST":"São Tomé and Príncipe","SA":"Saudi Arabia","SN":"Senegal","RS":"Serbia","SC":"Seychelles","SL":"Sierra Leone","SG":"Singapore","SX":"Sint Maarten","SK":"Slovakia","SI":"Slovenia","SB":"Solomon Islands","SO":"Somalia","ZA":"South Africa","GS":"South Georgia and South Sandwich Islands","SS":"South Sudan","ES":"Spain","LK":"Sri Lanka","SH":"St Helena, Ascension, Tristan da Cunha","SD":"Sudan","SR":"Suriname","SJ":"Svalbard","SZ":"Swaziland","SE":"Sweden","CH":"Switzerland","SY":"Syria","TW":"Taiwan","TJ":"Tajikistan","TZ":"Tanzania","TH":"Thailand","TL":"Timor-Leste","TG":"Togo","TK":"Tokelau","TO":"Tonga","TT":"Trinidad and Tobago","TN":"Tunisia","TR":"Turkey","TM":"Turkmenistan","TC":"Turks and Caicos Islands","TV":"Tuvalu","UM":"U.S. Outlying Islands","VI":"U.S. Virgin Islands","UG":"Uganda","UA":"Ukraine","AE":"United Arab Emirates","GB":"United Kingdom","US":"United States","UY":"Uruguay","UZ":"Uzbekistan","VU":"Vanuatu","VA":"Vatican City","VE":"Venezuela","VN":"Vietnam","WF":"Wallis and Futuna","YE":"Yemen","ZM":"Zambia","ZW":"Zimbabwe"}
```

## <a name="next-steps"></a>Następne kroki

Przykłady lokalizacji można znaleźć w następujących artykułach:

- [Dostosowywanie języka za pomocą zasad niestandardowych w Azure Active Directory B2C](custom-policy-localization.md)
- [Dostosowywanie języka za pomocą przepływów użytkowników w Azure Active Directory B2C](user-flow-language-customization.md)