---
title: Obsługa błędów i wyjątków w bibliotece MSAL.js
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak obsługiwać błędy i wyjątki, wyzwania dotyczące oświadczeń dostępu warunkowego i ponawianie prób w aplikacjach MSAL.js.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, hahamil
ms.custom: aaddev
ms.openlocfilehash: 20d276aba2ee3260911748cbee0a16020270059a
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761343"
---
# <a name="handle-errors-and-exceptions-in-msaljs"></a>Obsługa błędów i wyjątków w bibliotece MSAL.js

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msaljs"></a>Obsługa błędów w MSAL.js

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

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

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


[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Następne kroki

Rozważ włączenie [rejestrowania w MSAL.js](msal-logging-js.md) , aby ułatwić diagnozowanie i Debugowanie problemów.
