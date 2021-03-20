---
title: Obsługa błędów i wyjątków na platformie MSAL.NET
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak obsługiwać błędy i wyjątki, wyzwania dotyczące oświadczeń dostępu warunkowego i ponawianie prób w MSAL.NET.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 565acd745ba5d7fdec71f306d3851e599838f7d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584048"
---
# <a name="handle-errors-and-exceptions-in-msalnet"></a>Obsługa błędów i wyjątków na platformie MSAL.NET

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msalnet"></a>Obsługa błędów w MSAL.NET

Podczas przetwarzania wyjątków platformy .NET można użyć samego typu wyjątku i `ErrorCode` elementu członkowskiego do rozróżnienia między wyjątkami. `ErrorCode` wartości są stałymi typu [MsalError](/dotnet/api/microsoft.identity.client.msalerror).

Możesz również obejrzeć pola elementów [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception)i [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception).

Jeśli zgłoszono [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) , spróbuj użyć [kodów błędów uwierzytelniania i autoryzacji](reference-aadsts-error-codes.md) , aby sprawdzić, czy kod jest tam widoczny.

### <a name="common-net-exceptions"></a>Typowe wyjątki platformy .NET

Poniżej przedstawiono typowe wyjątki, które mogą zostać zgłoszone i niektóre możliwe środki zaradcze:  

| Wyjątek | Kod błędu | Ograniczanie ryzyka|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS65001: użytkownik lub administrator nie wyraził zgody na korzystanie z aplikacji o IDENTYFIKATORze "{appId}" o nazwie "{nazwa_aplikacji}". Wyślij interaktywne żądanie autoryzacji dla tego użytkownika i zasobu.| Najpierw pobierz zgodę użytkownika. Jeśli nie korzystasz z platformy .NET Core (bez interfejsu użytkownika sieci Web), wywołaj (tylko raz) `AcquireTokeninteractive` . Jeśli korzystasz z platformy .NET Core lub nie chcesz go wykonać `AcquireTokenInteractive` , użytkownik może przejść do adresu URL, aby wyrazić zgodę: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read` . Aby wywołać `AcquireTokenInteractive` : `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS50079: użytkownik musi korzystać z [uwierzytelniania wieloskładnikowego (MFA)](../authentication/concept-mfa-howitworks.md).| Nie ma żadnych środków zaradczych. Jeśli skonfigurowano usługę MFA dla dzierżawy, a Azure Active Directory (AAD) zdecyduje się ją wymusić, Wróć do interaktywnego przepływu, takiego jak `AcquireTokenInteractive` lub `AcquireTokenByDeviceCode` .|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) |AADSTS90010: typ grantu nie jest obsługiwany przez punkty końcowe */typowe* lub */consumers* . Użyj */Organizations* lub punktu końcowego określonego dla dzierżawy. Użyto */typowe*.| Zgodnie z opisem w komunikacie z usługi Azure AD urząd musi mieć dzierżawę lub inny */Organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) | AADSTS70002: treść żądania musi zawierać następujący parametr: `client_secret or client_assertion` .| Ten wyjątek może być zgłaszany, jeśli aplikacja nie została zarejestrowana jako publiczna aplikacja kliencka w usłudze Azure AD. W Azure Portal Edytuj manifest dla aplikacji i ustaw wartość `allowPublicClient` `true` . |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)| `unknown_user Message`: Nie można zidentyfikować zalogowanego użytkownika| Biblioteka nie mogła wykonać zapytania dotyczącego bieżącego zalogowanego użytkownika systemu Windows lub ten użytkownik nie ma usługi AD lub usługi Azure AD (przyłączone do miejsca pracy nie są obsługiwane). Środki zaradcze 1: w systemie platformy UWP Sprawdź, czy aplikacja ma następujące możliwości: uwierzytelnianie przedsiębiorstwa, sieci prywatne (klient i serwer), informacje o koncie użytkownika. Środki zaradcze 2: Zaimplementuj własną logikę, aby pobrać nazwę użytkownika (na przykład john@contoso.com ), a następnie użyj `AcquireTokenByIntegratedWindowsAuth` formularza, który przyjmuje nazwę użytkownika.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)|integrated_windows_auth_not_supported_managed_user| Ta metoda opiera się na protokole udostępnianym przez Active Directory (AD). Jeśli użytkownik został utworzony w usłudze Azure AD bez konieczności tworzenia kopii zapasowej ("zarządzany"), ta metoda zakończy się niepowodzeniem. Użytkownicy utworzeni w usłudze AD i podłączeni przez usługę Azure AD ("federacyjny") mogą korzystać z tej nieinteraktywnej metody uwierzytelniania. Środki zaradcze: Użyj uwierzytelniania interakcyjnego.|

### `MsalUiRequiredException`

Jeden ze wspólnych kodów stanu zwróconych z MSAL.NET, gdy jest wywoływany `AcquireTokenSilent()` `MsalError.InvalidGrantError` . Ten kod stanu oznacza, że aplikacja powinna ponownie wywoływać bibliotekę uwierzytelniania, ale w trybie interaktywnym (AcquireTokenInteractive lub AcquireTokenByDeviceCodeFlow dla publicznych aplikacji klienckich należy mieć wyzwanie w aplikacjach sieci Web). Wynika to z faktu, że przed wystawieniem tokenu uwierzytelniania wymagane jest dodatkowe interakcje użytkownika.

Większość czasu `AcquireTokenSilent` , gdy nie powiedzie się, jest to spowodowane tym, że pamięć podręczna tokenów nie ma tokenów pasujących do Twojego żądania. Tokeny dostępu wygasają za 1 godzinę i `AcquireTokenSilent` próbują pobrać nowy plik na podstawie tokenu odświeżania (w przypadku OAuth2 warunków jest to przepływ "Odśwież token"). Ten przepływ może również zakończyć się niepowodzeniem z różnych powodów, na przykład jeśli administrator dzierżawy skonfiguruje bardziej rygorystyczne zasady logowania. 

Interakcja ma na celu przeprowadzenie akcji przez użytkownika. Niektóre z tych warunków są łatwe do rozpoznania (na przykład zaakceptowanie warunków użytkowania za pomocą jednego kliknięcia), a niektóre z nich nie mogą zostać rozwiązane z bieżącą konfiguracją (na przykład w przypadku, gdy komputer musi nawiązać połączenie z określoną siecią firmową). Niektóre pomocne w konfigurowaniu użytkownika — uwierzytelnianie wieloskładnikowe lub instalowanie Microsoft Authenticator na urządzeniu.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` Wyliczenie klasyfikacji

MSAL uwidacznia `Classification` pole, które można przeczytać, aby zapewnić lepsze środowisko użytkownika. Na przykład, aby poinformować użytkownika o wygaśnięciu jego hasła lub zapewnieniu zgody na korzystanie z niektórych zasobów. Obsługiwane wartości są częścią `UiRequiredExceptionClassification` wyliczenia:

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
[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

Gdy wywoływany jest interfejs API wymagający dostępu warunkowego z MSAL.NET, aplikacja będzie musiała obsługiwać wyjątki wezwania. Zostanie ona wyświetlona jako [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) , gdzie Właściwość [oświadczenia](/dotnet/api/microsoft.identity.client.msalserviceexception.claims) nie będzie pusta.

Aby obsłużyć wyzwanie żądania, należy użyć `.WithClaim()` metody `PublicClientApplicationBuilder` klasy.

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

### <a name="http-error-codes-500-600"></a>Kody błędów HTTP 500-600

MSAL.NET implementuje prosty mechanizm ponawiania prób w przypadku błędów z kodami błędów HTTP 500-600.

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
    // . . .
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```

## <a name="next-steps"></a>Następne kroki

Rozważ włączenie [rejestrowania w usłudze MSAL.NET](msal-logging-dotnet.md) , aby ułatwić diagnozowanie i Debugowanie problemów.
