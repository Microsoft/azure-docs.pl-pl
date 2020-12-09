---
title: Błędy i wyjątki (MSAL)
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak obsługiwać błędy i wyjątki, dostęp warunkowy i wyzwania dotyczące oświadczeń w aplikacjach MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5b4ed1e21c33a952b639009b619db4f497f2cfbf
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860069"
---
# <a name="handle-msal-exceptions-and-errors"></a>Obsługa wyjątków i błędów MSAL

Ten artykuł zawiera omówienie różnych typów błędów i zaleceń dotyczących obsługi typowych błędów logowania.

## <a name="msal-error-handling-basics"></a>Podstawowe informacje dotyczące obsługi błędów MSAL

Wyjątki w bibliotece uwierzytelniania firmy Microsoft (MSAL) są przeznaczone dla deweloperów aplikacji do rozwiązywania problemów — nie do wyświetlania użytkownikom końcowym. Komunikaty o wyjątkach nie są zlokalizowane.

Podczas przetwarzania wyjątków i błędów można użyć samego typu wyjątku i kodu błędu, aby rozróżnić wyjątki.  Aby uzyskać listę kodów błędów, zobacz [kody błędów uwierzytelniania i autoryzacji](reference-aadsts-error-codes.md).

Podczas logowania mogą wystąpić błędy dotyczące zaleceń, dostęp warunkowy (MFA, zarządzanie urządzeniami, ograniczenia lokalizacji), wystawianie tokenów i wykup oraz właściwości użytkownika.

Zapoznaj się z następującą sekcją zgodną z używanym językiem, aby uzyskać więcej informacji na temat obsługi błędów aplikacji.

## <a name="net"></a>[.NET](#tab/dotnet)

Podczas przetwarzania wyjątków platformy .NET można użyć samego typu wyjątku i `ErrorCode` elementu członkowskiego do rozróżnienia między wyjątkami. `ErrorCode` wartości są stałymi typu [MsalError](/dotnet/api/microsoft.identity.client.msalerror).

Możesz również obejrzeć pola elementów [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception)i [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception).

Jeśli zgłoszono [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) , spróbuj użyć [kodów błędów uwierzytelniania i autoryzacji](reference-aadsts-error-codes.md) , aby sprawdzić, czy kod jest tam widoczny.

### <a name="common-net-exceptions"></a>Typowe wyjątki platformy .NET

Poniżej przedstawiono typowe wyjątki, które mogą zostać zgłoszone i niektóre możliwe środki zaradcze:  

| Wyjątek | Kod błędu | Ograniczanie ryzyka|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS65001: użytkownik lub administrator nie wyraził zgody na korzystanie z aplikacji o IDENTYFIKATORze "{appId}" o nazwie "{nazwa_aplikacji}". Wyślij interaktywne żądanie autoryzacji dla tego użytkownika i zasobu.| Musisz najpierw uzyskać zgodę użytkownika. Jeśli nie korzystasz z platformy .NET Core (bez interfejsu użytkownika sieci Web), wywołaj (tylko raz) `AcquireTokeninteractive` . Jeśli korzystasz z platformy .NET Core lub nie chcesz go wykonać `AcquireTokenInteractive` , użytkownik może przejść do adresu URL, aby wyrazić zgodę: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read` . Aby wywołać `AcquireTokenInteractive` : `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS50079: użytkownik musi korzystać z [uwierzytelniania wieloskładnikowego (MFA)](../authentication/concept-mfa-howitworks.md).| Nie ma żadnych środków zaradczych. Jeśli skonfigurowano usługę MFA dla dzierżawy, a Azure Active Directory (AAD) zdecyduje się ją wymusić, należy wrócić do interaktywnego przepływu, takiego jak `AcquireTokenInteractive` lub `AcquireTokenByDeviceCode` .|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) |AADSTS90010: typ grantu nie jest obsługiwany przez punkty końcowe */typowe* lub */consumers* . Użyj */Organizations* lub punktu końcowego określonego dla dzierżawy. Użyto */typowe*.| Zgodnie z opisem w komunikacie z usługi Azure AD urząd musi mieć dzierżawę lub inny */Organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) | AADSTS70002: treść żądania musi zawierać następujący parametr: `client_secret or client_assertion` .| Ten wyjątek może być zgłaszany, jeśli aplikacja nie została zarejestrowana jako publiczna aplikacja kliencka w usłudze Azure AD. W Azure Portal Edytuj manifest dla aplikacji i ustaw wartość `allowPublicClient` `true` . |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)| `unknown_user Message`: Nie można zidentyfikować zalogowanego użytkownika| Biblioteka nie mogła wykonać zapytania dotyczącego bieżącego zalogowanego użytkownika systemu Windows lub ten użytkownik nie jest przyłączony do usługi AD lub AAD (przyłączone do miejsca pracy użytkownicy nie są obsługiwane). Środki zaradcze 1: w systemie platformy UWP Sprawdź, czy aplikacja ma następujące możliwości: uwierzytelnianie przedsiębiorstwa, sieci prywatne (klient i serwer), informacje o koncie użytkownika. Środki zaradcze 2: Zaimplementuj własną logikę, aby pobrać nazwę użytkownika (na przykład john@contoso.com ), a następnie użyj `AcquireTokenByIntegratedWindowsAuth` formularza, który przyjmuje nazwę użytkownika.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)|integrated_windows_auth_not_supported_managed_user| Ta metoda opiera się na protokole udostępnianym przez Active Directory (AD). Jeśli użytkownik został utworzony w Azure Active Directory bez kopii zapasowej usługi AD ("zarządzany"), ta metoda zakończy się niepowodzeniem. Użytkownicy utworzeni w usłudze AD i korzystający z usługi AAD ("federacyjne") mogą korzystać z tej nieinteraktywnej metody uwierzytelniania. Środki zaradcze: Użyj uwierzytelniania interakcyjnego.|

### `MsalUiRequiredException`

Jeden ze wspólnych kodów stanu zwróconych z MSAL.NET, gdy jest wywoływany `AcquireTokenSilent()` `MsalError.InvalidGrantError` . Ten kod stanu oznacza, że aplikacja powinna ponownie wywoływać bibliotekę uwierzytelniania, ale w trybie interaktywnym (AcquireTokenInteractive lub AcquireTokenByDeviceCodeFlow dla publicznych aplikacji klienckich, a także w aplikacjach sieci Web). Wynika to z faktu, że przed wystawieniem tokenu uwierzytelniania wymagane jest dodatkowe interakcje użytkownika.

Większość czasu `AcquireTokenSilent` , gdy nie powiedzie się, jest to spowodowane tym, że pamięć podręczna tokenów nie ma tokenów pasujących do Twojego żądania. Tokeny dostępu wygasają za 1 godzinę i `AcquireTokenSilent` próbują pobrać nowy plik na podstawie tokenu odświeżania (w przypadku OAuth2 warunków jest to przepływ "Odśwież token"). Ten przepływ może również zakończyć się niepowodzeniem z różnych powodów, na przykład jeśli administrator dzierżawy skonfiguruje bardziej rygorystyczne zasady logowania. 

Interakcja ma na celu przeprowadzenie akcji przez użytkownika. Niektóre z tych warunków są łatwe do rozpoznania (na przykład zaakceptowanie warunków użytkowania za pomocą jednego kliknięcia), a niektóre z nich nie mogą zostać rozwiązane z bieżącą konfiguracją (na przykład w przypadku, gdy komputer musi nawiązać połączenie z określoną siecią firmową). Niektóre pomocne w konfigurowaniu użytkownika — uwierzytelnianie wieloskładnikowe lub instalowanie Microsoft Authenticator na urządzeniu.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` Wyliczenie klasyfikacji

MSAL uwidacznia `Classification` pole, które można odczytać, aby zapewnić lepszy interfejs użytkownika, na przykład informujący użytkownika, że jego hasło wygasło lub aby wyrazić zgodę na użycie niektórych zasobów. Obsługiwane wartości są częścią `UiRequiredExceptionClassification` wyliczenia:

| Klasyfikacja    | Znaczenie           | Zalecana obsługa |
|-------------------|-------------------|----------------------|
| BasicAction | Warunek może zostać rozpoznany przez interakcję użytkownika podczas przepływu uwierzytelniania interaktywnego. | Call AcquireTokenInteractively (). |
| AdditionalAction | Warunek można rozwiązać przez dodatkowe interakcje z systemem, poza przepływem uwierzytelniania interaktywnego. | Wywołaj AcquireTokenInteractively (), aby wyświetlić komunikat objaśniający akcję zaradczą. Aplikacja wywołująca może zdecydować się na ukrycie przepływów, które wymagają additional_action, jeśli użytkownik prawdopodobnie nie ukończy akcji zaradczej. |
| MessageOnly      | Nie można teraz rozwiązać warunku. Uruchomienie przepływu uwierzytelniania interaktywnego spowoduje wyświetlenie komunikatu opisującego warunek. | Wywołaj AcquireTokenInteractively (), aby wyświetlić komunikat objaśniający warunek. AcquireTokenInteractively () zwróci błąd UserCanceled, gdy użytkownik odczyta komunikat i zamknie okno. Aplikacja wywołująca może zdecydować się na ukrycie przepływów, co powoduje message_only, jeśli użytkownik prawdopodobnie nie skorzysta z tego komunikatu.|
| ConsentRequired  | Brak zgody użytkownika lub został on odwołany. | Wywołaj AcquireTokenInteractively () dla użytkownika, aby wyrazić zgodę. |
| UserPasswordExpired | Hasło użytkownika wygasło. | Wywołaj AcquireTokenInteractively (), aby użytkownik mógł zresetować swoje hasło. |
| PromptNeverFailed| Uwierzytelnianie interaktywne zostało wywołane z monitem o podanie parametru = nigdy, wymuszając MSAL na podstawie plików cookie przeglądarki i nie wyświetlaj przeglądarki. To nie powiodło się. | Wywołaj AcquireTokenInteractively () bez monitowania. Brak |
| AcquireTokenSilentFailed | Zestaw MSAL SDK nie ma wystarczających informacji do pobrania tokenu z pamięci podręcznej. Może to być spowodowane brakiem tokenów w pamięci podręcznej lub nie znaleziono konta. Komunikat o błędzie zawiera więcej szczegółów.  | Call AcquireTokenInteractively (). |
| Brak    | Nie podano dalszych szczegółów. Warunek może zostać rozpoznany przez interakcję użytkownika podczas przepływu uwierzytelniania interaktywnego. | Call AcquireTokenInteractively (). |

## <a name="net-code-example"></a>Przykład kodu platformy .NET

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```

## <a name="javascript"></a>[JavaScript](#tab/javascript)

MSAL.js zapewnia obiekty błędów, które są abstrakcyjne i klasyfikuje różne typy typowych błędów. Zapewnia także interfejs umożliwiający dostęp do szczegółowych informacji o błędach, takich jak komunikaty o błędach, aby odpowiednio je obsługiwać.

### <a name="error-object"></a>Obiekt Error

```javascript
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```

Rozszerzając klasę błędu, masz dostęp do następujących właściwości:
- `AuthError.message`: Analogicznie jak `errorMessage` .
- `AuthError.stack`: Ślad stosu dla zgłoszonych błędów.

### <a name="error-types"></a>Typy błędów

Dostępne są następujące typy błędów:

- `AuthError`: Klasa błędu podstawowego dla biblioteki MSAL.js, używana również w przypadku nieoczekiwanych błędów.

- `ClientAuthError`: Error — Klasa, która oznacza problem z uwierzytelnianiem klienta. Większość błędów, które pochodzą z biblioteki, zostanie ClientAuthErrors. Te błędy powodują wywołanie metody logowania, gdy logowanie jest już w toku, użytkownik anuluje logowanie i tak dalej.

- `ClientConfigurationError`: Klasa Error, rozszerzenie `ClientAuthError` zgłoszone przed żądaniami, gdy określone parametry konfiguracji użytkownika są źle sformułowane lub nie istnieją.

- `ServerError`: Klasa Error reprezentuje ciągi błędów wysyłane przez serwer uwierzytelniania. Mogą to być błędy, takie jak nieprawidłowe formaty lub parametry żądania albo inne błędy, które uniemożliwiają uwierzytelnienie lub autoryzowanie użytkownika przez serwer.

- `InteractionRequiredAuthError`: Klasa Error, rozciąga `ServerError` się do reprezentowania błędów serwera, które wymagają wywołania interaktywnego. Ten błąd jest zgłaszany przez `acquireTokenSilent` , jeśli użytkownik musi współdziałać z serwerem w celu zapewnienia poświadczeń lub zgody na uwierzytelnianie/autoryzację. Kody błędów obejmują `"interaction_required"` , `"login_required"` , i `"consent_required"` .

Aby obsłużyć błędy w przepływach uwierzytelniania przy użyciu metod redirect ( `loginRedirect` , `acquireTokenRedirect` ), należy zarejestrować wywołanie zwrotne, które jest wywoływane z sukcesem lub niepowodzeniem po przekierowaniu przy użyciu `handleRedirectCallback()` metody w następujący sposób:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Metody dla podskakujących okienek ( `loginPopup` , `acquireTokenPopup` ) zwracają niesie obietnice zwiększenia, więc można użyć wzorca Promise (. then i. catch), aby je obsłużyć w sposób pokazany:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Błędy wymagające interakcji

Zwracany jest błąd podczas próby użycia nieinteraktywnej metody uzyskiwania tokenu, takiego jak `acquireTokenSilent` , ale MSAL nie może wykonać go w trybie dyskretnym.

Możliwe przyczyny są następujące:

- musisz się zalogować
- Musisz wyrazić zgodę
- musisz przejść przez środowisko uwierzytelniania wieloskładnikowego.

Korygowanie polega na wywołaniu metody interaktywnej, takiej jak `acquireTokenPopup` lub `acquireTokenRedirect` :

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

## <a name="python"></a>[Python](#tab/python)

W programie MSAL for Python większość błędów jest przekazywanych jako wartość zwrotna z wywołania interfejsu API. Ten błąd jest reprezentowany jako słownik zawierający odpowiedź JSON z platformy tożsamości firmy Microsoft.

* Pomyślna odpowiedź zawiera `"access_token"` klucz. Format odpowiedzi jest definiowany przez protokół OAuth2. Aby uzyskać więcej informacji, zobacz [5,1 Pomyślne odpowiedzi](https://tools.ietf.org/html/rfc6749#section-5.1)
* Odpowiedź na błąd zawiera `"error"` i zwykle `"error_description"` . Format odpowiedzi jest definiowany przez protokół OAuth2. Aby uzyskać więcej informacji, zobacz [5,2 odpowiedzi na błędy](https://tools.ietf.org/html/rfc6749#section-5.2)

Po zwróceniu błędu `"error_description"` klucz zawiera komunikat z możliwością odczytu przez człowieka, który z kolei zwykle zawiera kod błędu platformy tożsamości firmy Microsoft. Aby uzyskać szczegółowe informacje o różnych kodach błędów, zobacz [kody błędów uwierzytelniania i autoryzacji](./reference-aadsts-error-codes.md).

W programie MSAL for Python wyjątki są rzadko, ponieważ większość błędów jest obsługiwana przez zwrócenie wartości błędu. `ValueError`Wyjątek jest zgłaszany tylko wtedy, gdy występuje problem z próbą użycia biblioteki — na przykład wtedy, gdy parametry interfejsu API są źle sformułowane.

## <a name="java"></a>[Java](#tab/java)

W programie MSAL for Java istnieją trzy typy wyjątków: `MsalClientException` , i, `MsalServiceException` `MsalInteractionRequiredException` które dziedziczą z `MsalException` .

- `MsalClientException` jest zgłaszany w przypadku wystąpienia błędu, który jest lokalny dla biblioteki lub urządzenia.
- `MsalServiceException` jest zgłaszany, gdy usługa bezpiecznego tokenu (STS) zwróci odpowiedź na błąd lub Wystąpił inny błąd sieciowy.
- `MsalInteractionRequiredException` jest zgłaszany, gdy do pomyślnego uwierzytelnienia wymagane jest współdziałanie z interfejsem użytkownika.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` przedstawia nagłówki HTTP zwracane w żądaniach do usługi STS. Uzyskaj dostęp do nich za pośrednictwem `MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

Jeden z typowych kodów stanu zwróconych z MSAL for Java podczas `AcquireTokenSilently()` wywoływania `InvalidGrantError` . Oznacza to, że przed wystawieniem tokenu uwierzytelniania wymagane jest dodatkowe interakcje użytkownika. Aplikacja powinna ponownie wywołać bibliotekę uwierzytelniania, ale w trybie interaktywnym, wysyłając `AuthorizationCodeParameters` lub `DeviceCodeParameters` do publicznych aplikacji klienckich.

Większość czasu `AcquireTokenSilently` , gdy nie powiedzie się, jest to spowodowane tym, że pamięć podręczna tokenów nie ma tokenu pasującego do żądania. Tokeny dostępu wygasają w ciągu godziny i `AcquireTokenSilently` spróbują uzyskać nowy plik na podstawie tokenu odświeżania. W OAuth2ch jest to przepływ tokenu odświeżania. Ten przepływ może również zakończyć się niepowodzeniem z różnych powodów, takich jak Administrator dzierżawy konfiguruje bardziej rygorystyczne zasady logowania.

Niektóre warunki wynikające z tego błędu są łatwe do rozwiązania użytkownikom. Na przykład może być konieczne zaakceptowanie warunków użytkowania. Lub prawdopodobnie żądanie nie może zostać spełnione z bieżącą konfiguracją, ponieważ maszyna musi nawiązać połączenie z określoną siecią firmową.

MSAL uwidacznia `reason` pole, którego można użyć, aby zapewnić lepsze środowisko użytkownika. Na przykład `reason` pole może prowadzić do poinformowania użytkownika o wygaśnięciu hasła lub zapewnieniu zgody na korzystanie z niektórych zasobów. Obsługiwane wartości są częścią  `InteractionRequiredExceptionReason` wyliczenia:

| Przyczyna | Znaczenie | Zalecana obsługa |
|---------|-----------|-----------------------------|
| `BasicAction` | Warunek może zostać rozpoznany przez interakcję użytkownika w trakcie przepływu uwierzytelniania interaktywnego | Wywołanie `acquireToken` z parametrami interaktywnym |
| `AdditionalAction` | Warunek można rozwiązać przez dodatkowe interakcje z systemem poza przepływem uwierzytelniania interaktywnego. | Wywołaj `acquireToken` Parametry interaktywne, aby wyświetlić komunikat objaśniający akcję zaradczą do wykonania. Aplikacja wywołująca może zdecydować się na ukrycie przepływów, które wymagają dodatkowej akcji, jeśli użytkownik nie będzie mógł wykonać akcji naprawczej. |
| `MessageOnly` | Nie można teraz rozwiązać warunku. Uruchom interaktywny przepływ uwierzytelniania, aby wyświetlić komunikat objaśniający warunek. | Wywołaj `acquireToken` z parametrami interaktywnym, aby wyświetlić komunikat objaśniający warunek. `acquireToken` zwróci błąd, `UserCanceled` gdy użytkownik odczyta komunikat i zamknie okno. Aplikacja może zdecydować się na ukrycie przepływów, które powodują, że użytkownik prawdopodobnie nie będzie mógł skorzystać z wiadomości. |
| `ConsentRequired`| Brak zgody użytkownika lub został on odwołany. |Wywołaj `acquireToken` Parametry interaktywne, aby użytkownik mógł wyrazić zgodę. |
| `UserPasswordExpired` | Hasło użytkownika wygasło. | Wywołanie `acquireToken` z parametrem interaktywnym, aby użytkownik mógł zresetować swoje hasło |
| `None` |  Dostępne są również dalsze szczegóły. Warunek może zostać rozpoznany przez interakcję użytkownika podczas przepływu uwierzytelniania interaktywnego. | Wywołanie `acquireToken` z parametrami interaktywnym |

### <a name="code-example"></a>Przykład kodu

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

## <a name="iosmacos"></a>[iOS/macOS](#tab/iosmacos)

Pełna lista MSAL dla błędów systemu iOS i macOS jest wymieniona w [wyliczeniu MSALError](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Wszystkie błędy wygenerowane przez MSAL są zwracane z `MSALErrorDomain` domeną.

W przypadku błędów systemowych MSAL zwraca oryginalny `NSError` z interfejsu API systemu. Na przykład jeśli pozyskiwanie tokenu nie powiedzie się z powodu braku łączności sieciowej, MSAL zwraca błąd w `NSURLErrorDomain` domenie i `NSURLErrorNotConnectedToInternet` kodzie.

Zalecamy, aby obsłużyć co najmniej dwa następujące błędy MSAL po stronie klienta:

- `MSALErrorInteractionRequired`: Użytkownik musi wykonać żądanie interaktywne. Istnieje wiele warunków, które mogą prowadzić do tego błędu, takich jak wygasła sesja uwierzytelniania lub konieczność dodatkowych wymagań dotyczących uwierzytelniania. Wywołaj interfejs API pozyskiwania tokenów MSAL Interactive do odzyskania. 

- `MSALErrorServerDeclinedScopes`: Niektóre lub wszystkie zakresy zostały odrzucone. Zdecyduj, czy chcesz kontynuować z przyznanymi zakresami, czy zatrzymać proces logowania.

> [!NOTE]
> `MSALInternalError`Wyliczenie powinno być używane tylko do celów referencyjnych i debugowania. Nie należy próbować automatycznie obsłużyć tych błędów w czasie wykonywania. Jeśli w aplikacji wystąpią jakieś błędy, które znajdują się w obszarze, warto wyświetlić ogólny komunikat z informacjami o tym `MSALInternalError` , co się stało.

Na przykład `MSALInternalErrorBrokerResponseNotReceived` oznacza, że użytkownik nie wykonał uwierzytelniania i został ręcznie zwrócony do aplikacji. W takim przypadku aplikacja powinna wyświetlić ogólny komunikat o błędzie z informacją o tym, że uwierzytelnianie nie zostało ukończone i zasugeruj, że próbują ponownie przeprowadzić uwierzytelnianie.

Następujący przykładowy kod języka C przedstawia najlepsze rozwiązania dotyczące obsługi niektórych typowych warunków błędu:

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

## <a name="conditional-access-and-claims-challenges"></a>Wyzwania dotyczące dostępu warunkowego i oświadczeń

W przypadku odzyskania tokenów w trybie dyskretnym aplikacja może otrzymywać błędy w przypadku, gdy interfejs API, do którego próbujesz uzyskać dostęp, wymaga [żądania oświadczeń dostępu warunkowego](../azuread-dev/conditional-access-dev-guide.md) , takiego jak zasady MFA.

Wzorzec obsługi tego błędu polega na interaktywnej pozyskaniu tokenu przy użyciu MSAL. Interaktywny pozyskiwanie tokenu wyświetla użytkownika i daje im możliwość spełnienia wymaganych zasad dostępu warunkowego.

W niektórych przypadkach podczas wywoływania interfejsu API wymagającego dostępu warunkowego można odebrać wyzwanie oświadczeń w błędzie z interfejsu API. Na przykład jeśli zasady dostępu warunkowego mają mieć urządzenie zarządzane (Intune), błąd będzie wyglądać podobnie do [AADSTS53000: urządzenie musi być zarządzane, aby można było uzyskać dostęp do tego zasobu](reference-aadsts-error-codes.md) lub czegoś podobnego. W takim przypadku można przekazać oświadczenia w wywołaniu tokenu pozyskiwania, aby użytkownik był monitowany o spełnienie odpowiednich zasad.

### <a name="net"></a>.NET

Gdy wywoływany jest interfejs API wymagający dostępu warunkowego z MSAL.NET, aplikacja będzie musiała obsługiwać wyjątki wezwania. Zostanie ona wyświetlona jako [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) , gdzie Właściwość [oświadczenia](/dotnet/api/microsoft.identity.client.msalserviceexception.claims) nie będzie pusta.

Aby obsłużyć wyzwanie żądania, należy użyć `.WithClaim()` metody `PublicClientApplicationBuilder` klasy.

### <a name="javascript"></a>JavaScript

W przypadku korzystania z tokenów dyskretnie (przy użyciu programu `acquireTokenSilent` ) przy użyciu MSAL.js aplikacja może otrzymywać błędy, gdy do interfejsu API, do którego próbujesz uzyskać dostęp, jest wymagane [żądanie oświadczeń dostępu warunkowego](../azuread-dev/conditional-access-dev-guide.md) , takie jak zasady MFA.

Wzorzec do obsługi tego błędu polega na tym, że wywołanie interaktywne w celu uzyskania tokenu w MSAL.js, takich jak `acquireTokenPopup` lub `acquireTokenRedirect` , jak w poniższym przykładzie:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // call API
}).catch(function(error) {
    if (error instanceof InteractionRequiredAuthError) {
    
        // extract, if exists, claims from error message
        if (error.ErrorMessage.claims) {
            accessTokenRequest.claimsRequest = JSON.stringify(error.ErrorMessage.claims);
        }
        
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // call API
        }).catch(function(error) {
            console.log(error);
        });
    }
});
```

Interaktywny uzyskiwanie tokenu wyświetla użytkownika i daje im możliwość spełnienia wymaganych zasad dostępu warunkowego.

Gdy wywołujesz interfejs API wymagający dostępu warunkowego, możesz odebrać wyzwanie dotyczące oświadczeń w błędzie z interfejsu API. W takim przypadku można przekazać oświadczenia zwrócone w błędzie do `claimsRequest` pola `AuthenticationParameters.ts` klasy w celu spełnienia odpowiednich zasad. 

Zobacz [żądanie dodatkowych oświadczeń](active-directory-optional-claims.md) , aby uzyskać więcej szczegółów.

### <a name="msal-for-ios-and-macos"></a>Biblioteka MSAL dla systemów iOS i macOS

MSAL dla systemów iOS i macOS umożliwia żądanie określonych oświadczeń w scenariuszach pozyskiwania tokenów interaktywnych i dyskretnych.

Aby zażądać oświadczeń niestandardowych, określ `claimsRequest` w `MSALSilentTokenParameters` lub `MSALInteractiveTokenParameters` .

Aby uzyskać więcej informacji [, zobacz żądanie oświadczeń niestandardowych przy użyciu MSAL dla systemów iOS i macOS](request-custom-claims.md) .

## <a name="retrying-after-errors-and-exceptions"></a>Ponawianie próby po wystąpieniu błędów i wyjątkach

Podczas wywoływania MSAL należy wdrożyć własne zasady ponawiania. MSAL nawiązuje wywołania protokołu HTTP do usługi AAD i mogą wystąpić Sporadyczne awarie, na przykład można wyłączyć sieć lub serwer jest przeciążony.  

### <a name="http-error-codes-500-600"></a>Kody błędów HTTP 500-600

MSAL.NET implementuje prosty mechanizm ponawiania prób w przypadku błędów z kodami błędów HTTP 500-600.

### <a name="http-429"></a>HTTP 429

Gdy serwer tokenów usług jest przeciążony za dużo żądań, zwraca błąd HTTP 429 z wskazówką dotyczącą tego, jak długo można spróbować ponownie w `Retry-After` polu odpowiedzi.

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) powierzchnie `System.Net.Http.Headers.HttpResponseHeaders` jako właściwości `namedHeaders` . Możesz użyć dodatkowych informacji z kodu błędu, aby zwiększyć niezawodność aplikacji. W opisanym przypadku można użyć `RetryAfterproperty` (typu `RetryConditionHeaderValue` ) i obliczeń, kiedy należy ponowić próbę.

Oto przykład dla aplikacji demona przy użyciu przepływu poświadczeń klienta. Można dostosować ten element do dowolnej metody uzyskiwania tokenu.

```csharp

bool retry = false;
do
{
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account).ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (serviceException.ErrorCode == "temporarily_unavailable")
         {
             RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
             if (retryAfter.Delta.HasValue)
             {
                 delay = retryAfter.Delta;
             }
             else if (retryAfter.Date.HasValue)
             {
                 delay = retryAfter.Date.Value.Offset;
             }
         }
    }
    …
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```
