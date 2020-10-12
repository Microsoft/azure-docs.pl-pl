---
title: Obsługa błędów aplikacji klienta ADAL — najlepsze rozwiązania | Azure
description: Zapewnia wskazówki dotyczące obsługi błędów i najlepsze rozwiązania dla aplikacji klienckich ADAL.
services: active-directory
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: how-to
ms.workload: identity
ms.date: 02/27/2017
ROBOTS: NOINDEX
ms.openlocfilehash: ad5595f7eebc8feca2f00a6f95e10c547ded9529
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85383738"
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Błąd obsługi najlepszych rozwiązań dla klientów biblioteki uwierzytelniania Azure Active Directory (ADAL)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Ten artykuł zawiera wskazówki dotyczące typu błędów, które deweloperzy mogą napotkać, w przypadku używania biblioteki ADAL do uwierzytelniania użytkowników. W przypadku korzystania z biblioteki ADAL istnieje kilka przypadków, w których deweloper może potrzebować krok po kroku i obsłużyć błędy. Właściwa obsługa błędów zapewnia doskonałe środowisko użytkownika końcowego i ogranicza liczbę użytkowników końcowych, którzy muszą się zalogować.

W tym artykule omówiono określone przypadki dla każdej platformy obsługiwanej przez bibliotekę ADAL oraz sposób, w jaki aplikacja może prawidłowo obsługiwać poszczególne przypadki. Wskazówki dotyczące błędów są podzielone na dwie szersze kategorie na podstawie wzorców pozyskiwania tokenów udostępnianych przez interfejsy API biblioteki ADAL:

- **AcquireTokenSilent**: klient próbuje uzyskać token dyskretnie (bez interfejsu użytkownika) i może się nie powieść, jeśli biblioteka ADAL nie powiedzie się. 
- **AcquireToken**: Klient może próbować uzyskać ciche pobieranie, ale może również wykonywać interaktywne żądania, które wymagają logowania.

> [!TIP]
> Dobrym pomysłem jest zarejestrowanie wszystkich błędów i wyjątków podczas korzystania z biblioteki ADAL i usługi Azure AD. Dzienniki nie tylko ułatwiają zrozumienie ogólnej kondycji aplikacji, ale są również ważne podczas debugowania. Mimo że aplikacja może zostać odzyskana po pewnych błędach, może to mieć szersze problemy z projektowaniem, które wymagają zmiany kodu w celu rozwiązania. 
> 
> Podczas wdrażania warunków błędu objętych tym dokumentem należy rejestrować kod błędu i opis z przyczyn omówionych wcześniej. Przykłady kodu rejestrowania można znaleźć w dokumentacji dotyczącej [błędów i rejestrowania](#error-and-logging-reference) . 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

AcquireTokenSilent próbuje uzyskać token z gwarancją, że użytkownik końcowy nie widzi interfejsu użytkownika. Istnieje kilka przypadków, w których uzyskanie dyskretne może zakończyć się niepowodzeniem i musi być obsługiwane za pomocą żądań interaktywnych lub przez domyślną procedurę obsługi. Firma Microsoft szczegółowe się z informacjami o tym, kiedy i jak należy korzystać z każdego przypadku w poniższych sekcjach.

Istnieje zestaw błędów generowanych przez system operacyjny, co może wymagać obsługi błędów specyficznych dla aplikacji. Aby uzyskać więcej informacji, zobacz sekcję "błędy systemu operacyjnego" w [dokumentacji dotyczącej błędów i rejestrowania](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Scenariusze aplikacji

- [Natywne aplikacje klienckie](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#native-client) (iOS, Android, Desktop .NET lub Xamarin)
- Aplikacje [klienckie sieci Web](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#web-client) wywołujące [zasób](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#resource-server) (.NET)

### <a name="error-cases-and-actionable-steps"></a>Przypadki błędów i kroki z możliwością wykonania akcji

Zasadniczo istnieją dwa przypadki błędów AcquireTokenSilent:

| Sprawa | Opis |
|------|-------------|
| **Przypadek 1**. błąd jest rozpoznawany przy użyciu interakcyjnego logowania | W przypadku błędów spowodowanych brakiem prawidłowych tokenów wymagane jest żądanie interaktywne. W każdym przypadku wyszukiwanie w pamięci podręcznej i nieprawidłowy/wygasły token odświeżania wymagają wywołania AcquireToken do rozwiązania.<br><br>W takich przypadkach użytkownik końcowy musi zostać poproszony o zalogowanie się. Aplikacja może natychmiast wykonać żądanie interaktywne, po interakcji z użytkownikiem końcowym (np. naciśnięciem przycisku logowania) lub nowszym. Wybór zależy od żądanego zachowania aplikacji.<br><br>Zapoznaj się z kodem w poniższej sekcji w tym konkretnym przypadku i błędami, które je Diagnozuj.|
| **Przypadek 2**: błąd nie jest rozpoznawany przy użyciu interakcyjnego logowania | W przypadku błędów sieci i przejściowych/tymczasowych lub innych awarii wykonywanie interakcyjnego żądania AcquireToken nie rozwiąże problemu. Niezbędne interakcyjne komunikaty logowania mogą również frustrować użytkowników końcowych. Biblioteka ADAL automatycznie próbuje wykonać pojedynczej próby dla większości błędów w przypadku błędów AcquireTokenSilent.<br><br>Aplikacja kliencka może również próbować ponowić próbę w pewnym momencie, ale w przypadku i w zależności od zachowania aplikacji oraz odpowiedniego środowiska użytkownika końcowego. Na przykład aplikacja może wykonać AcquireTokenSilentą ponowną próbę po kilku minutach lub w odpowiedzi na pewną akcję użytkownika końcowego. Natychmiastowe ponowienie próby spowoduje ograniczenie ograniczenia aplikacji i nie powinno być podejmowane próby.<br><br>Kolejna próba nie powiedzie się z powodu tego samego błędu nie oznacza, że klient powinien wykonać interaktywne żądanie przy użyciu AcquireToken, ponieważ nie rozwiąże błędu.<br><br>Zapoznaj się z kodem w poniższej sekcji w tym konkretnym przypadku i błędami, które je Diagnozuj. |

### <a name="net"></a>.NET

Poniższe wskazówki zawierają przykłady obsługi błędów w połączeniu z metodami ADAL: 

- acquireTokenSilentAsync(...)
- acquireTokenSilentSync(...) 
- [przestarzałe] acquireTokenSilent (...)
- [przestarzałe] acquireTokenByRefreshToken (...) 

Kod zostanie wdrożony w następujący sposób:

```csharp
try{
    AcquireTokenSilentAsync(…);
} 

catch (AdalSilentTokenAcquisitionException e) {
    // Exception: AdalSilentTokenAcquisitionException
    // Caused when there are no tokens in the cache or a required refresh failed. 

    // Action: Case 1, resolvable with an interactive request. 
} 

catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
} 
    
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET. 
    // e.ErrorCode contains the error code. 

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
    // Example Error: network_not_available, default case.
}
```

### <a name="android"></a>Android

Poniższe wskazówki zawierają przykłady obsługi błędów w połączeniu z metodami ADAL: 

- acquireTokenSilentSync(...)
- acquireTokenSilentAsync(...)
- [przestarzałe] acquireTokenSilent (...)

Kod zostanie wdrożony w następujący sposób:

```java
// *Inside callback*
public void onError(Exception e) {

    if (e instanceof AuthenticationException) {
        // Exception: AdalException
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode().

        // Errors: ADALError.ERROR_SILENT_REQUEST,
        // ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED,
        // ADALError.INVALID_TOKEN_CACHE_ITEM
        // Description: Request failed due to no tokens in
        // cache or failed a required refresh. 

        // Action: Case 1, resolvable with an interactive request. 

        // Action: Case 2, not resolvable with an interactive request.
        // Attempt retry after a timed interval or user action.
        // Example Errors: default case,
        // DEVICE_CONNECTION_IS_NOT_AVAILABLE, 
        // BROKER_AUTHENTICATOR_ERROR_GETAUTHTOKEN,
    }
}
```

### <a name="ios"></a>iOS

Poniższe wskazówki zawierają przykłady obsługi błędów w połączeniu z metodami ADAL: 

- acquireTokenSilentWithResource(...)

Kod zostanie wdrożony w następujący sposób:

```objc
[context acquireTokenSilentWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL Objective-C.
            // Error Code: result.error.code

            // Errors: AD_ERROR_SERVER_REFRESH_TOKEN_REJECTED, AD_ERROR_CACHE_NO_REFRESH_TOKEN
            // Description: No tokens in cache or failed a required token refresh failed. 
            // Action: Case 1, resolvable with an interactive request. 

            // Error: AD_ERROR_CACHE_MULTIPLE_USERS
            // Description: There was ambiguity in the silent request resulting in multiple cache items.
            // Action: Special Case, application should perform another silent request and specify the user using ADUserIdentifier. 
            // Can be caused in cases of a multi-user application. 

            // Action: Case 2, not resolvable with an interactive request.
            // Attempt retry after some time or user action.
            // Example Errors: default case,
            // AD_ERROR_CACHE_BAD_FORMAT
        }
    }
}]
```

## <a name="acquiretoken"></a>AcquireToken

AcquireToken to domyślna metoda ADAL używana do uzyskiwania tokenów. W przypadkach, gdy tożsamość użytkownika jest wymagana, AcquireToken próbuje najpierw uzyskać token dyskretny, a następnie w razie potrzeby wyświetla interfejs użytkownika (chyba że PromptBehavior. nigdy nie jest przesyłany). W przypadkach, gdy wymagana jest tożsamość aplikacji, AcquireToken próbuje uzyskać token, ale nie wyświetla interfejsu użytkownika, ponieważ nie ma żadnych użytkowników końcowych. 

Podczas obsługi błędów AcquireToken obsługa błędów jest zależna od platformy i scenariusza, w którym aplikacja próbuje osiągnąć. 

System operacyjny może również generować zestaw błędów, które wymagają obsługi błędów zależnych od konkretnej aplikacji. Aby uzyskać więcej informacji, zobacz "błędy systemu operacyjnego" w [dokumentacji dotyczącej błędów i rejestrowania](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Scenariusze aplikacji

- Natywne aplikacje klienckie (iOS, Android, Desktop .NET lub Xamarin)
- Aplikacje sieci Web wywołujące interfejs API zasobów (.NET)
- Aplikacje jednostronicowe (JavaScript)
- Aplikacje między usługami (.NET, Java)
  - Wszystkie scenariusze, w tym w imieniu
  - W imieniu określonych scenariuszy

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Przypadki błędów i kroki umożliwiające wykonanie akcji: natywne aplikacje klienckie

Jeśli tworzysz natywną aplikację kliencką, istnieje kilka przypadków obsługi błędów, które należy wziąć pod uwagę, które odnoszą się do problemów z siecią, błędów przejściowych i innych błędów specyficznych dla platformy. W większości przypadków aplikacja nie powinna wykonywać bezpośrednich ponownych prób, ale należy zaczekać na interakcję użytkownika końcowego, która monituje o logowanie. 

Istnieje kilka specjalnych przypadków, w których jedna ponowna próba może rozwiązać problem. Na przykład gdy użytkownik musi włączyć dane na urządzeniu lub ukończyć pobieranie brokera usługi Azure AD po awarii początkowej. 

W przypadku awarii aplikacja może przedstawić interfejs użytkownika, aby umożliwić użytkownikowi końcowemu przeprowadzenie pewnej interakcji, która poprosi o ponowienie próby. Na przykład jeśli urządzenie nie powiodło się z powodu błędu w trybie offline, przycisk "Spróbuj ponownie się zalogować" monituje o ponowienie próby AcquireToken, a nie natychmiast ponowienie błędu. 

Obsługa błędów w natywnych aplikacjach może być definiowana przez dwa sytuacje:

|  |  |
|------|-------------|
| **Przypadek 1**:<br>Błąd niepowtarzający operacji (większość przypadków) | 1. nie próbuj natychmiast próbować. Zaprezentowanie interfejsu użytkownika końcowego na podstawie określonego błędu, który wywołuje ponowną próbę (na przykład "Spróbuj zalogować się ponownie" lub "Pobierz aplikację brokera usługi Azure AD"). |
| **Przypadek 2**:<br>Błąd powtarzania | 1. wykonaj jedną ponowną próbę, ponieważ użytkownik końcowy mógł wprowadzić stan, który spowoduje sukces.<br><br>2. Jeśli próba nie powiedzie się, zaprezentowanie interfejsu użytkownika końcowego na podstawie określonego błędu, który wywołuje ponowną próbę ("Spróbuj ponownie się zalogować", "Pobierz aplikację brokera usługi Azure AD" itp.). |

> [!IMPORTANT]
> Jeśli konto użytkownika zostanie przesłane do biblioteki ADAL w wywołaniu dyskretnym i zakończy się niepowodzeniem, kolejne żądanie interaktywne umożliwi użytkownikowi końcowemu zalogowanie się przy użyciu innego konta. Po pomyślnym AcquireTokenniu przy użyciu konta użytkownika aplikacja musi sprawdzić, czy zalogowany użytkownik jest zgodny z lokalnym obiektem użytkownika aplikacji. Niezgodność nie generuje wyjątku (z wyjątkiem w celu C), ale należy ją uwzględnić w przypadkach, gdy użytkownik jest znany lokalnie przed żądaniami uwierzytelniania (np. wywołaniem dyskretnym).
>

#### <a name="net"></a>.NET

Poniższe wskazówki zawierają przykłady obsługi błędów w połączeniu ze wszystkimi niecichymi AcquireToken (...) Metody ADAL, *z wyjątkiem*: 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- AcquireTokenAsync (..., ClientCredential,...)
- AcquireTokenAsync(..., ClientAssertion, ...)
- AcquireTokenAsync (..., UserAssertion,...)   

Kod zostanie wdrożony w następujący sposób:

```csharp
try {
    AcquireTokenAsync(…);
} 
    
catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.
    
    // Design time consideration: Certain errors may be caused at development and exposed through this exception. 
    // Looking inside the description will give more guidance on resolving the specific issue. 

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

    } 

catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // e.ErrorCode contains the error code

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

> [!NOTE]
> Biblioteka ADAL .NET ma dodatkowe uwagi, ponieważ obsługuje PromptBehavior. Never, która ma zachowanie takie jak AcquireTokenSilent.
>

Poniższe wskazówki zawierają przykłady obsługi błędów w połączeniu z metodami ADAL: 

- acquireToken (..., PromptBehavior. Never)

Kod zostanie wdrożony w następujący sposób:

```csharp
    try {acquireToken(…, PromptBehavior.Never);
    } 

catch(AdalServiceException e) {
    // Exception: AdalServiceException represents 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

} catch (AdalException e) {
    // Error Code: e.ErrorCode == "user_interaction_required"
    // Description: user_interaction_required indicates the silent request failed 
    // in a way that's resolvable with an interactive request.
    // Action: Resolvable with an interactive request. 

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

#### <a name="android"></a>Android

Poniższe wskazówki zawierają przykłady obsługi błędów w połączeniu ze wszystkimi niecichymi AcquireToken (...) Metody ADAL. 

Kod zostanie wdrożony w następujący sposób:

```java
AcquireTokenAsync(…);

// *Inside callback*
public void onError(Exception e) {
    if (e instanceof AuthenticationException) {
        // Exception: AdalException 
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode();

        // Error: ADALError.BROKER_APP_INSTALLATION_STARTED
        // Description: Broker app not installed, user will be prompted to download the app. 

        // Action: Case 2, Retriable Error 
        // Perform a single retry. If that fails, only try again after user action. 

        // Action: Case 1, Non-Retriable 
        // Do not perform an immediate retry. Only retry after user action. 
        // Example Errors: default case, DEVICE_CONNECTION_IS_NOT_AVAILABLE
    }
}
```

#### <a name="ios"></a>iOS

Poniższe wskazówki zawierają przykłady obsługi błędów w połączeniu ze wszystkimi niecichymi AcquireToken (...) Metody ADAL. 

Kod zostanie wdrożony w następujący sposób:

```objc
[context acquireTokenWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL ObjC.
            // Error Code: result.error.code 

            // Error: AD_ERROR_SERVER_WRONG_USER
            // Description: App passed a user into ADAL and the end user signed in with a different account. 
            // Action: Case 1, Non-retriable (as is) and up to the application on how to handle this case. 
            // It can attempt a new request without specifying the user, or use UI to clarify the user account to sign in. 

            // Action: Case 1, Non-Retriable 
            // Do not perform an immediate retry. Only retry after user action. 
            // Example Errors: default case
        }
    }
}]
```

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Przypadki błędów i kroki z możliwością działania: aplikacje sieci Web wywołujące interfejs API zasobów (.NET)

W przypadku kompilowania aplikacji sieci Web platformy .NET, która wywołuje metodę pobiera token przy użyciu kodu autoryzacji dla zasobu, jedynym wymaganym kodem jest domyślna procedura obsługi dla ogólnego przypadku. 

Poniższe wskazówki zawierają przykłady obsługi błędów w połączeniu z metodami ADAL: 

- AcquireTokenByAuthorizationCodeAsync(...)

Kod zostanie wdrożony w następujący sposób:

```csharp
try {
    AcquireTokenByAuthorizationCodeAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action or wait until much later. 
    // Example Errors: default case
}
```

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Przypadki błędów i kroki z możliwością podejmowania działań: aplikacje jednostronicowe (adal.js)

Jeśli tworzysz aplikację jednostronicową przy użyciu adal.js z AcquireToken, kod obsługi błędu jest podobny do tego w przypadku typowego wywołania dyskretnego. W adal.js AcquireToken nigdy nie pokazuje interfejsu użytkownika. 

Niepowodzenie AcquireToken ma następujące sytuacje:

|  |  |
|------|-------------|
| **Przypadek 1**:<br>Rozpoznawalnie przy użyciu żądania interaktywnego | 1. Jeśli logowanie () nie powiedzie się, nie wykonuj natychmiastowej próby. Ponów próbę, gdy akcja użytkownika zostanie ponowiona.|
| **Przypadek 2**:<br>Nierozpoznawalne w przypadku żądania interaktywnego. Błąd jest ponawiany. | 1. wykonaj jedną ponowną próbę, ponieważ w poprawce użytkownika końcowego wprowadzono stan, którego wynikiem jest sukces.<br><br>2. Jeśli próba nie powiedzie się, zaprezentowanie użytkownikowi końcowemu akcji na podstawie konkretnego błędu, który może wywołać ponawianie próby ("Spróbuj zalogować się ponownie"). |
| **Przypadek 3**:<br>Nierozpoznawalne w przypadku żądania interaktywnego. Nie ponowienie próby. | 1. nie próbuj natychmiast próbować. Przedstaw użytkownikowi końcowemu akcję na podstawie konkretnego błędu, który może wywołać ponowienie próby ("Spróbuj zalogować się ponownie"). |

Kod zostanie wdrożony w następujący sposób:

```javascript
AuthContext.acquireToken(…, function(error, errorDesc, token) {
    if (error || errorDesc) {
        // Represents any token acquisition failure that occurred. 
        // Error Code: error.indexOf("<ERROR_STRING>")

        // Errors: if (error.indexOf("interaction_required"))
        //         if (error.indexOf("login required"))
        // Description: ADAL wasn't able to silently acquire a token because of expire or fresh session. 
        // Action: Case 1, Resolvable with an interactive login() request. 

        // Error: if (error.indexOf("Token Renewal Failed")) 
        // Description: Timeout when refreshing the token.
        // Action: Case 2, Not resolvable interactively, error is retriable.
        // Perform a single retry. Only try again after user action.

        // Action: Case 3, Not resolvable interactively, error is not retriable. 
        // Do not perform an immediate retry. Only retry after user action.
        // Example Errors: default case
    }
}
```

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Przypadki błędów i kroki z możliwością wykonywania akcji: aplikacje Service-to-Service (tylko platforma .NET)

W przypadku kompilowania aplikacji typu "usługa do usługi" używającej AcquireToken istnieje kilka kluczowych błędów, jakie musi obsłużyć kod. Jedyną możliwością wystąpienia błędu jest zwrócenie błędu z powrotem do aplikacji wywołującej (w przypadku wystąpienia) lub zastosowanie strategii ponawiania prób. 

#### <a name="all-scenarios"></a>Wszystkie scenariusze

Dla *wszystkich* scenariuszy aplikacji między usługami, w tym w imieniu:

- Nie należy próbować natychmiastowej próby. Biblioteka ADAL próbuje pojedynczą ponowną próbę w przypadku niektórych nieudanych żądań. 
- Kontynuuj ponawianie próby, gdy akcja użytkownika lub aplikacji zostanie pożądana. Na przykład aplikacja demona, która działa w pewnym interwale, powinna czekać do następnego interwału do ponowienia próby.

Poniższe wskazówki zawierają przykłady obsługi błędów w połączeniu z metodami ADAL: 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- AcquireTokenAsync (..., ClientCredential,...)
- AcquireTokenAsync(...,ClientAssertion, ...)
- AcquireTokenAsync(...,UserAssertion, ...)

Kod zostanie wdrożony w następujący sposób:

```csharp
try {
    AcquireTokenAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Errors: default case
}  
```

#### <a name="on-behalf-of-scenarios"></a>Scenariusze w imieniu

Scenariusze aplikacji typu "usługa do obsługi" *w imieniu* usługi.

Poniższe wskazówki zawierają przykłady obsługi błędów w połączeniu z metodami ADAL: 

- AcquireTokenAsync(..., UserAssertion, ...)

Kod zostanie wdrożony w następujący sposób:

```csharp
try {
AcquireTokenAsync(…);
} 

catch (AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Error: On-Behalf-Of Error Handler
    if (e.ErrorCode == "interaction_required") {
        // Description: The client needs to perform some action due to a config from admin. 
        // Action: Capture `claims` parameter inside ex.InnerException.InnerException.
        // Generate HTTP error 403 with claims, throw back HTTP error to client.
        // Wait for client to retry. 
    }
} 
        
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Error: default case
}
```

Utworzyliśmy [kompletny przykład](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) demonstrujący ten scenariusz.

## <a name="error-and-logging-reference"></a>Informacje o błędach i rejestrowaniu

### <a name="logging-personal-identifiable-information--organizational-identifiable-information"></a>Rejestrowanie informacji osobistych & informacji o organizacji 
Domyślnie rejestrowanie biblioteki ADAL nie przechwytuje ani nie rejestruje żadnych informacji osobistych ani informacji o organizacji. Biblioteka umożliwia deweloperom aplikacji włączenie tego elementu przy użyciu metody ustawiającej w klasie rejestratora. Dzięki rejestrowaniu informacji osobistych lub informacji o organizacji, aplikacja jest odpowiedzialna za bezpieczne obsługiwanie bardzo poufnych danych i spełnianie wymagań prawnych.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>Błędy biblioteki ADAL

W celu zbadania określonych błędów ADAL kod źródłowy w [repozytorium Azure-ActiveDirectory-Library for dotnet](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) jest najlepszym odwołaniem do błędu.

#### <a name="guidance-for-error-logging-code"></a>Wskazówki dotyczące kodu rejestrowania błędów

Rejestrowanie w programie .NET ADAL zmiany w zależności od platformy, na której pracujesz. Zapoznaj się z [rejestrowaniem wiki](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net) , aby uzyskać kod dotyczący włączania rejestrowania.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>Błędy biblioteki ADAL

W celu zbadania określonych błędów ADAL kod źródłowy w [repozytorium Azure-ActiveDirectory-Library-for-Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) jest najlepszym odwołaniem do błędu.

#### <a name="operating-system-errors"></a>Błędy systemu operacyjnego

Błędy systemu operacyjnego Android są ujawniane za pomocą protokołu AuthenticationException w bibliotece ADAL, są identyfikowane jako "SERVER_INVALID_REQUEST" i mogą być bardziej szczegółowe w opisach błędów. 

Aby zapoznać się z pełną listą typowych błędów i czynności, które należy podjąć w przypadku napotkania aplikacji lub użytkowników końcowych, zobacz witrynę [typu wiki systemu Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki)w systemie ADAL. 

#### <a name="guidance-for-error-logging-code"></a>Wskazówki dotyczące kodu rejestrowania błędów

```java
// 1. Configure Logger
Logger.getInstance().setExternalLogger(new ILogger() {    
    @Override   
    public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) { 
    // …
    // You can write this to logfile depending on level or errorcode. 
    writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);    
    }
}

// 2. Set the log level
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);

// By default, the `Logger` does not capture any PII or OII

// PII or OII will be logged
Logger.getInstance().setEnablePII(true);

// To STOP logging PII or OII, use the following setter
Logger.getInstance().setEnablePII(false);


// 3. Send logs to logcat.
adb logcat > "C:\logmsg\logfile.txt";
```

### <a name="ios"></a>iOS

#### <a name="adal-library-errors"></a>Błędy biblioteki ADAL

Aby poznać konkretne błędy biblioteki ADAL, najlepszym odwołaniem do błędu jest kod źródłowy w [repozytorium Azure-ActiveDirectory-Library-for-objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) .

#### <a name="operating-system-errors"></a>Błędy systemu operacyjnego

Błędy systemu iOS mogą wystąpić podczas logowania, gdy użytkownicy korzystają z widoków sieci Web i charakteru uwierzytelniania. Może to być spowodowane warunkami, takimi jak błędy protokołu TLS, limity czasu lub błędy sieci:

- W przypadku udostępniania uprawnień logowania nie są trwałe, a pamięć podręczna jest pusta. Można rozwiązać, dodając następujący wiersz kodu do łańcucha kluczy: `[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- W przypadku zestawu NsUrlDomain błędów akcja zmienia się w zależności od logiki aplikacji. Zobacz [dokumentację referencyjną NSURLErrorDomain](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) dla określonych wystąpień, które mogą być obsługiwane.
- Zapoznaj się z tematem [typowe problemy dotyczące biblioteki ADAL obj](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) .

#### <a name="guidance-for-error-logging-code"></a>Wskazówki dotyczące kodu rejestrowania błędów

```objc
// 1. Enable NSLogging
[ADLogger setNSLogging:YES];

// 2. Set the log level (if you want verbose)
[ADLogger setLevel:ADAL_LOG_LEVEL_VERBOSE];

// 3. Set up a callback block to simply print out
[ADLogger setLogCallBack:^(ADAL_LOG_LEVEL logLevel, NSString *message, NSString *additionalInformation, NSInteger errorCode, NSDictionary *userInfo) {
     NSString* log = [NSString stringWithFormat:@"%@ %@", message, additionalInformation];    
     NSLog(@"%@", log);
}];
```

### <a name="guidance-for-error-logging-code---javascript"></a>Wskazówki dotyczące rejestrowania błędów — JavaScript 

```javascript
0: Error1: Warning2: Info3: Verbose
window.Logging = {
    level: 3,
    log: function (message) {
        console.log(message);
    }
};
```

## <a name="related-content"></a>Zawartość pokrewna

* [Biblioteki uwierzytelniania usługi Azure AD][AAD-Auth-Libraries]
* [Scenariusze uwierzytelniania usługi Azure AD][AAD-Auth-Scenarios]
* [Integrowanie aplikacji z Azure Active Directory][AAD-Integrating-Apps]

Skorzystaj z poniższej sekcji komentarzy, aby przekazać Opinie i pomóc nam udoskonalić naszą zawartość.

[![Wyświetla przycisk "Zaloguj się przy użyciu konta Microsoft"][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->

[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Integrating-Apps]:../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

