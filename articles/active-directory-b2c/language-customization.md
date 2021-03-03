---
title: Dostosowanie języka w Azure Active Directory B2C
description: Dowiedz się więcej o dostosowywaniu środowiska w przepływach użytkownika w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 418f0797343a64728c4e48084b09bd0e426cec62
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101686414"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Dostosowanie języka w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Dostosowanie języka w programie Azure Active Directory B2C (Azure AD B2C) umożliwia przepływom użytkownika dostosowywać się do potrzeb klientów. Firma Microsoft udostępnia tłumaczenia dla [języków 36](#supported-languages), ale można również udostępniać własne tłumaczenia w dowolnym języku. Nawet jeśli środowisko użytkownika jest dostarczane tylko dla jednego języka, można dostosować dowolny tekst na stronach.

## <a name="how-language-customization-works"></a>Jak działa dostosowanie języka

Za pomocą dostosowywania języka można wybrać Języki, w których przepływ użytkownika jest dostępny. Po włączeniu funkcji można podać parametr ciągu zapytania, `ui_locales` z poziomu aplikacji. Gdy wywołasz się do Azure AD B2C, Strona zostanie przetłumaczona na wskazane ustawienia regionalne. Ten typ konfiguracji zapewnia pełną kontrolę nad językami w przepływie użytkownika i ignoruje ustawienia języka w przeglądarce klienta.

Użytkownik może nie potrzebować tego poziomu kontroli nad językami, które widzi klient. Jeśli nie podano `ui_locales` parametru, środowisko klienta jest podyktowane ustawieniami przeglądarki. Można nadal kontrolować Języki, do których przepływ użytkownika jest tłumaczony przez dodanie go jako obsługiwanego języka. Jeśli w przeglądarce klienta ustawiono język, który nie ma być obsługiwany, w zamian zostanie wyświetlony język wybrany jako domyślny w obsługiwanych kulturach.

* **interfejs użytkownika — określony język**: po włączeniu dostosowywania języka przepływ użytkownika zostanie przetłumaczony na język, który jest określony w tym miejscu.
* **Język żądany w przeglądarce**: Jeśli żaden `ui_locales` parametr nie został określony, przepływ użytkownika zostanie przetłumaczony na język żądany w przeglądarce, *Jeśli jest obsługiwany*.
* **Język domyślny dla zasad**: Jeśli w przeglądarce nie określono języka lub określono taki, który nie jest obsługiwany, przepływ użytkownika zostanie przetłumaczony na domyślny język przepływu użytkownika.

> [!NOTE]
> Jeśli używasz niestandardowych atrybutów użytkownika, musisz podać własne tłumaczenia. Aby uzyskać więcej informacji, zobacz [Dostosowywanie ciągów](#customize-your-strings).

::: zone pivot="b2c-custom-policy"

Lokalizacja wymaga trzech kroków: 

1. Skonfiguruj jawną listę obsługiwanych języków
1. Podawanie ciągów i kolekcji specyficznych dla języka
1. Edytuj [definicję zawartości](contentdefinitions.md) strony. 

::: zone-end 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="support-requested-languages-for-ui_locales"></a>Obsługa żądanych języków dla ui_locales

Zasady, które zostały utworzone przed ogólnym udostępnieniem dostosowania języka, muszą najpierw włączyć tę funkcję. Zasady i przepływy użytkowników utworzone po włączeniu dostosowywania języka są domyślnie włączone.

Po włączeniu dostosowywania języka w przepływie użytkownika można kontrolować język przepływu użytkownika, dodając `ui_locales` parametr.

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
1. Kliknij przepływ użytkownika, który chcesz włączyć dla tłumaczeń.
1. Wybierz **Języki**.
1. Wybierz pozycję **Włącz Dostosowywanie języka**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Wybierz Języki, w których przepływ użytkownika jest włączony

Zezwól na tłumaczenie zestawu języków dla przepływu użytkownika na żądanie w przeglądarce bez `ui_locales` parametru.

1. Upewnij się, że przepływ użytkownika ma włączone dostosowanie języka z poprzednich instrukcji.
1. Na stronie **Języki** dla przepływu użytkownika wybierz język, który ma być obsługiwany.
1. W okienku właściwości Zmień wartość **włączone** na **tak**.
1. Wybierz pozycję **Zapisz** w górnej części okienka właściwości.

>[!NOTE]
>Jeśli `ui_locales` parametr nie zostanie podany, Strona zostanie przetłumaczona na język przeglądarki klienta tylko wtedy, gdy jest włączona.
>

## <a name="customize-your-strings"></a>Dostosowywanie ciągów

Dostosowanie języka umożliwia dostosowanie dowolnego ciągu w przepływie użytkownika.

1. Upewnij się, że przepływ użytkownika ma włączone dostosowanie języka z poprzednich instrukcji.
1. Na stronie **Języki** dla przepływu użytkownika wybierz język, który chcesz dostosować.
1. W obszarze **pliki zasobów na poziomie strony** wybierz stronę, którą chcesz edytować.
1. Wybierz pozycję **Pobierz domyślne** (lub **Pobierz zastąpienia** , jeśli wcześniej edytowano ten język).

Te kroki zapewniają plik JSON, którego można użyć do rozpoczęcia edytowania ciągów.

### <a name="change-any-string-on-the-page"></a>Zmień dowolny ciąg na stronie

1. Otwórz plik JSON pobrany z poprzednich instrukcji w edytorze JSON.
1. Znajdź element, który chcesz zmienić. Możesz znaleźć `StringId` ciąg, którego szukasz, lub poszukać `Value` atrybutu, który ma zostać zmieniony.
1. Zaktualizuj `Value` atrybut o dane, które mają być wyświetlane.
1. Dla każdego ciągu, który chcesz zmienić, przejdź `Override` do `true` .
1. Zapisz plik i przekaż zmiany. (Formant przekazywania można znaleźć w tym samym miejscu, w którym został pobrany plik JSON).

> [!IMPORTANT]
> Jeśli musisz przesłonić ciąg, upewnij się, że wartość jest ustawiona `Override` na `true` . Jeśli wartość nie zostanie zmieniona, wpis zostanie zignorowany.

### <a name="change-extension-attributes"></a>Zmień atrybuty rozszerzenia

Jeśli chcesz zmienić ciąg dla niestandardowego atrybutu użytkownika lub chcesz dodać go do pliku JSON, jest w następującym formacie:

```json
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
  ]
}
```

Zamień `<ExtensionAttribute>` na nazwę niestandardowego atrybutu użytkownika.

Zamień na `<ExtensionAttributeValue>` Nowy ciąg, który ma być wyświetlany.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Podaj listę wartości za pomocą LocalizedCollections

Jeśli chcesz podać listę wartości odpowiedzi, musisz utworzyć `LocalizedCollections` atrybut. `LocalizedCollections` jest tablicą `Name` par i `Value` . Kolejność dla elementów będzie kolejność, w jakiej są wyświetlane. Aby dodać `LocalizedCollections` , użyj następującego formatu:

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
        {
          "Name":"<Response1>",
          "Value":"<Value1>"
        },
        {
          "Name":"<Response2>",
          "Value":"<Value2>"
        }
      ]
    }
  ]
}
```

* `ElementId` jest atrybutem użytkownika, do którego ten `LocalizedCollections` atrybut jest odpowiedzią.
* `Name` jest wartością pokazywaną użytkownikowi.
* `Value` to co jest zwracane w ramach żądania, gdy ta opcja jest zaznaczona.

### <a name="upload-your-changes"></a>Przekaż zmiany

1. Po wprowadzeniu zmian w pliku JSON Wróć do swojej dzierżawy B2C.
1. Wybierz pozycję **przepływy użytkownika** i kliknij przepływ użytkownika, który chcesz włączyć dla tłumaczeń.
1. Wybierz **Języki**.
1. Wybierz język, w którym chcesz przeprowadzić tłumaczenie.
1. Wybierz stronę, na której chcesz przekazać tłumaczenia.
1. Wybierz ikonę folderu i wybierz plik JSON do przekazania.

Zmiany są zapisywane automatycznie w przepływie użytkownika.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Dostosowywanie interfejsu użytkownika strony przy użyciu dostosowywania języka

Istnieją dwa sposoby lokalizowania [zawartości HTML](customize-ui-with-html.md). Jednym ze sposobów jest włączenie [dostosowywania języka](language-customization.md). Włączenie tej funkcji pozwala Azure AD B2C do przesyłania dalej parametru Connect OpenID Connect `ui-locales` do punktu końcowego. Na serwerze zawartości można użyć tego parametru, aby zapewnić dostosowane strony HTML, które są specyficzne dla języka.

Alternatywnie można ściągnąć zawartość z różnych miejsc na podstawie ustawień regionalnych, które są używane. W punkcie końcowym z obsługą mechanizmu CORS można skonfigurować strukturę folderów, aby hostować zawartość dla określonych języków. Jeśli używasz wartości wieloznacznej, nastąpi wywołanie odpowiedniej metody `{Culture:RFC5646}` . Załóżmy na przykład, że jest to identyfikator URI strony niestandardowej:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Możesz załadować stronę w `fr` . Gdy Strona pobiera zawartość HTML i CSS, pobiera z:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Dodawanie języków niestandardowych

Możesz również dodać Języki, dla których firma Microsoft obecnie nie udostępnia tłumaczeń. Musisz podać tłumaczenia dla wszystkich ciągów w przepływie użytkownika. Kody języka i ustawień regionalnych są ograniczone do tych w standardzie ISO 639-1. Format kodu ustawień regionalnych powinien mieć wartość "ISO_639-1_code"-"CountryCode", na przykład `en-GB` . Aby uzyskać więcej informacji na temat formatów identyfikatorów ustawień regionalnych, zapoznaj się z tematem https://docs.microsoft.com/openspecs/office_standards/ms-oe376/6c085406-a698-4e12-9d4d-c3b0ee3dbc4a

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
2. Kliknij przepływ użytkownika, w którym chcesz dodać Języki niestandardowe, a następnie kliknij pozycję **Języki**.
3. Wybierz pozycję **Dodaj język niestandardowy** w górnej części strony.
4. W otwartym okienku kontekstu Zidentyfikuj język, w którym są udostępniane tłumaczenia, wprowadzając prawidłowy kod ustawień regionalnych.
5. Dla każdej strony można pobrać zestaw zastąpień dla języka angielskiego i rozpocząć korzystanie z tłumaczeń.
6. Po zakończeniu pracy z plikami JSON można przekazać je dla każdej strony.
7. Wybierz pozycję **Włącz**, a przepływ użytkownika może teraz pokazać ten język dla użytkowników.
8. Zapisz język.

>[!IMPORTANT]
>Przed zapisaniem należy włączyć Języki niestandardowe lub przekazywać do nich zastąpienia.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="set-up-the-list-of-supported-languages"></a>Skonfiguruj listę obsługiwanych języków

Otwórz plik rozszerzeń zasad. Na przykład <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Wyszukaj element [BuildingBlocks](buildingblocks.md) . Jeśli element nie istnieje, Dodaj go.
1. Dodaj `Localization` element z obsługiwanymi językami: angielski (domyślny) i hiszpański.  


```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="provide-language-specific-labels"></a>Podaj etykiety specyficzne dla języka

[LocalizedResources](localization.md#localizedresources) `Localization` elementu zawiera listę zlokalizowanych ciągów. Element zlokalizowane zasoby ma identyfikator, który służy do jednoznacznego identyfikowania zlokalizowanych zasobów. Ta identyfikator jest używana w dalszej części elementu [definicji zawartości](contentdefinitions.md) .

Można skonfigurować zlokalizowane elementy zasobów dla definicji zawartości i dowolny język, który ma być obsługiwany. Aby dostosować ujednolicone strony rejestracji lub logowania w języku angielskim i hiszpańskim, należy dodać następujące `LocalizedResources` elementy po zamknięciu `</SupportedLanguages>` elementu.

> [!NOTE]
> W poniższym przykładzie dodaliśmy symbol funta `#` na początku każdego wiersza, dzięki czemu można łatwo znaleźć zlokalizowane etykiety na ekranie.

```xml
<!--Local account sign-up or sign-in page English-->
<Localization Enabled="true">
  ...
  <LocalizedResources Id="api.signuporsignin.en">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Email Address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Please enter your email</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Username</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Sign up now</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Please enter your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#Don't have an account?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#Forgot your password?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#OR</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#The user has forgotten their password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Sign in</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Sign in with your social account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Please enter your password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#The password you entered is not in the expected format.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Sign in with your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Sign in with your existing account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Please enter a valid email address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#We are having trouble signing you in. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Your password is incorrect.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#We can't seem to find your account.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Looks like you used an old password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#Invalid username or password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up or sign-in page Spanish-->
  <LocalizedResources Id="api.signuporsignin.es">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Este campo es obligatorio</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Registrarse ahora</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Escriba su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#¿No tiene una cuenta?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#¿Olvidó su contraseña?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#O</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#El usuario ha olvidado su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Iniciar sesión</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Iniciar sesión con su cuenta de redes sociales</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Escriba su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#La contraseña que ha escrito no está en el formato esperado.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Iniciar sesión con su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Iniciar sesión con su cuenta existente</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Escriba una dirección de correo electrónico válida</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#Tenemos problemas para iniciar su sesión. Vuelva a intentarlo más tarde.  </LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Su contraseña es incorrecta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#Parece que no podemos encontrar su cuenta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Parece que ha usado una contraseña antigua.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#El nombre de usuario o la contraseña no son válidos.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Se bloqueó su cuenta. Póngase en contacto con la persona responsable de soporte técnico para desbloquearla y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Su cuenta se bloqueó temporalmente para impedir un uso no autorizado. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page English-->
  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Email Address</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Email address that can be used to contact you.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Please enter a valid email address.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Enter new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirm New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirm new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Display Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Your display name.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Surname</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Your surname (also known as family name or last name).  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Given Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Your given name (also known as first name).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Create</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#A required field is missing. Please fill out all required fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#What is this?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Please provide the following details.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Please wait</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#This information is required.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Change e-mail</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Send new code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Send verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Verify code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#That code is expired. Please request a new code.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#You've made too many incorrect attempts. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#That code is incorrect. Please try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#Verification is necessary. Please click Send button.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#E-mail address verified. You can now continue.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Claim not verified: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#A user with the specified ID already exists. Please choose a different one.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Incorrect pattern for: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} has invalid input.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Missing required element: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error in validation by: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page Spanish-->
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Dirección de correo electrónico</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Introduzca una dirección de correo electrónico válida.  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#Nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Escriba la contraseña nueva</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#De 8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Nombre para mostrar</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Su nombre para mostrar.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Apellido</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Su apellido.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Nombre</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Su nombre (también conocido como nombre de pila).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Crear</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#Hay uno o varios campos rellenados de forma incorrecta. Compruebe las entradas y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#Los campos de entrada de contraseña no coinciden. Escriba la misma contraseña en ambos campos y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#Falta un campo obligatorio. Rellene todos los campos necesarios y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#¿Qué es esto?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Proporcione los siguientes detalles.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Espere</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#Esta información es obligatoria.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Cambiar correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Enviar nuevo código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Enviar código de comprobación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Comprobar código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#El código ha expirado. Solicite otro nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#Ha realizado demasiados intentos incorrectos. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#Ese código es incorrecto. Inténtelo de nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#Tenemos problemas para comprobar la dirección de correo electrónico. Escriba una dirección de correo electrónico válida y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#Ha habido demasiadas solicitudes para comprobar esta dirección de correo electrónico. Espere un poco y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Se ha enviado el código de verificación a su Bandeja de entrada. Cópielo en el siguiente cuadro de entrada.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Código de verificación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#La comprobación es obligatoria. Haga clic en el botón Enviar.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#Dirección de correo electrónico comprobada. Puede continuar.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Reclamación no comprobada: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Patrón incorrecto para: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} tiene una entrada no válida.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Falta un elemento obligatorio: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error en la validación de: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

## <a name="edit-the-content-definition-with-the-localization"></a>Edytowanie definicji zawartości z lokalizacją

Wklej całą zawartość elementu ContentDefinitions, który został skopiowany jako element podrzędny elementu BuildingBlocks.

W poniższym przykładzie w języku angielskim (EN) i hiszpańskim (ES) ciągi niestandardowe są dodawane do strony rejestracji lub logowania oraz na stronie rejestracji konta lokalnego. **LocalizedResourcesReferenceId** dla każdego **LocalizedResourcesReferenceu** jest taka sama jak ich ustawienia regionalne, ale można użyć dowolnego ciągu jako identyfikatora. Dla każdej kombinacji języka i strony należy wskazać odpowiednie **LocalizedResources** wcześniej utworzone.

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.signuporsignin">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>

  <ContentDefinition Id="api.localaccountsignup">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>
</ContentDefinitions>
```

##  <a name="upload-and-test-your-updated-custom-policy"></a>Przekazywanie i testowanie zaktualizowanych zasad niestandardowych

### <a name="upload-the-custom-policy"></a>Przekazywanie zasad niestandardowych

1. Zapisz plik rozszerzeń.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wyszukaj i wybierz **Azure AD B2C**.
1. W obszarze **zasady** wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz pozycję **Przekaż zasady niestandardowe**.
1. Przekaż wcześniej zmieniony plik rozszerzeń.

### <a name="test-the-custom-policy-by-using-run-now"></a>Testowanie zasad niestandardowych za pomocą polecenia **Uruchom teraz**

1. Wybierz przekazane zasady, a następnie wybierz pozycję **Uruchom teraz**.
1. Powinno być możliwe wyświetlenie zlokalizowanej strony rejestracji lub logowania.
1. Kliknij link do rejestracji, aby wyświetlić zlokalizowaną stronę rejestracji w usłudze.
1. Zmień język domyślny przeglądarki na hiszpański. Można też dodać parametr ciągu zapytania `ui_locales` do żądania autoryzacji. Na przykład: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize&client_id=0239a9cc-309c-4d41-12f1-31299feb2e82&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&ui_locales=es
```

::: zone-end


## <a name="additional-information"></a>Dodatkowe informacje

::: zone pivot="b2c-user-flow"

### <a name="page-ui-customization-labels-as-overrides"></a>Etykiety dostosowywania interfejsu użytkownika strony jako zastąpienia

Po włączeniu dostosowywania języka poprzednie zmiany w etykietach korzystających z dostosowywania interfejsu użytkownika są utrwalane w pliku JSON dla języka angielskiego (pl). Można nadal zmieniać etykiety i inne ciągi przez przekazywanie zasobów języka w celu dostosowania języka.

::: zone-end

### <a name="up-to-date-translations"></a>Aktualne tłumaczenia

Firma Microsoft dokłada starań, aby zapewnić najbardziej aktualne tłumaczenia do użycia. Firma Microsoft stale ulepsza tłumaczenia i zapewnia ich zgodność. Firma Microsoft zidentyfikuje usterki i zmiany w globalnej terminologii oraz wprowadzi aktualizacje, które bezproblemowo pracują w przepływie użytkownika.

### <a name="support-for-right-to-left-languages"></a>Obsługa języków pisanych od prawej do lewej

Firma Microsoft obecnie nie obsługuje języków pisanych od prawej do lewej. Można to zrobić za pomocą niestandardowych ustawień regionalnych i CSS, aby zmienić sposób wyświetlania ciągów. Jeśli potrzebujesz tej funkcji, zagłosuj na opinię na temat [platformy Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Tłumaczenia dostawcy tożsamości społecznościowej

Firma Microsoft udostępnia `ui_locales` parametr OIDC na potrzeby logowania do sieci społecznościowych. Jednak niektórzy dostawcy tożsamości społecznościowych, w tym Facebook i Google, nie honorli.

### <a name="browser-behavior"></a>Zachowanie przeglądarki

Programy Chrome i Firefox żądają swojego języka zestawu. Jeśli jest to obsługiwany język, jest on wyświetlany przed ustawieniem domyślnym. Przeglądarka Microsoft Edge obecnie nie żąda języka i przechodzi bezpośrednio do języka domyślnego.

## <a name="supported-languages"></a>Obsługiwane języki

Azure AD B2C obejmuje obsługę następujących języków. Języki przepływu użytkownika są udostępniane przez Azure AD B2C. Języki powiadomień usługi uwierzytelniania wieloskładnikowego (MFA) są udostępniane przez [usługę Azure AD MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Język              | Kod języka | Przepływy użytkowników         | Powiadomienia usługi MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabski                | ty            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Bułgarski             | BG            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Języku                | mld USD            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) |
| Kataloński               | urzędu certyfikacji            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Czeski                 | Rejestr            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Duński                | da            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Niemiecki                | de            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Grecki                 | Colon            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Angielski               | en            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Hiszpański               | es            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Estoński              | staw            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Baskijski                | Europejska            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Fiński               | fi            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Francuski                | fr            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Galicyjski              | GL            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Gudżarati              | gu            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) |
| Hebrajski                | Przewodniczący            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Hindi                 | Cześć            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Chorwacki              | godz.            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Węgierski             | Węgry            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Indonezyjski            | identyfikator            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Włoski               | it            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| japoński              | ja            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Kazachski                | kk            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Kannada               | kN            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) |
| Koreański                | Ko            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Litewski            | lt            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Łotewski               | LV            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Malayalam             | ml            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) |
| Marathi               | Pan            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) |
| Malajski                 | Arial            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Norweski (Bokmal)      | sesja            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) |
| Niderlandzki                 | nl            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Norweski             | nie            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Pendżabski               | biur            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) |
| Polski                | zysków            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Portugalski (Brazylia)   | pt-br         | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Portugalski (Portugalia) | pt-pt         | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Rumuński              | ro            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Rosyjski               | ru            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Słowacki                | SK            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Słoweński             | SL            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Serbski – cyrylica    | Wirtualizacja SR-cryl-CS    | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Serbski – łaciński       | Wirtualizacja sr-latn-cs    | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Szwedzki               | sv            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Tamilski                 | Ta            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) |
| Telugu                | Usuń            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) |
| Tajlandzki                  | th            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Turecki               | zdawczy            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Ukraiński             | Południowe Zjednoczone Królestwo            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Wietnamski            | VI            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Chiński – uproszczony  | zh-Hans       | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Chiński – tradycyjny | zh-Hant       | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |


## <a name="next-steps"></a>Następne kroki

::: zone pivot="b2c-user-flow"

Więcej informacji o sposobach dostosowywania interfejsu użytkownika aplikacji można znaleźć w temacie [Dostosowywanie interfejsu użytkownika aplikacji w Azure Active Directory B2C](customize-ui-with-html.md).

::: zone-end 

::: zone pivot="b2c-custom-policy"

- Dowiedz się więcej o elemencie [lokalizacyjnym](localization.md) w IEF Reference.
- Zobacz listę [identyfikatorów ciągów lokalizacji](localization-string-ids.md) dostępnych w Azure AD B2C.

::: zone-end 
