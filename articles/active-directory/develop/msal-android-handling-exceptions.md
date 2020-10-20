---
title: Błędy i wyjątki (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak obsługiwać błędy i wyjątki, dostęp warunkowy i wyzwania dotyczące oświadczeń w aplikacjach MSAL systemu Android.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 08/07/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: f40c2bb0f529f9e0683c67bea884443458707f4f
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92206598"
---
# <a name="handle-exceptions-and-errors-in-msal-for-android"></a>Obsługa wyjątków i błędów w MSAL dla systemu Android

Wyjątki w bibliotece uwierzytelniania firmy Microsoft (MSAL) są przeznaczone do ułatwienia deweloperom aplikacji rozwiązywania problemów z ich aplikacjami. Komunikaty o wyjątkach nie są zlokalizowane.

Podczas przetwarzania wyjątków i błędów można użyć samego typu wyjątku i kodu błędu, aby rozróżnić wyjątki.  Aby uzyskać listę kodów błędów, zobacz [kody błędów uwierzytelniania i autoryzacji](reference-aadsts-error-codes.md).

Podczas logowania mogą wystąpić błędy dotyczące zaleceń, dostęp warunkowy (MFA, zarządzanie urządzeniami, ograniczenia lokalizacji), wystawianie tokenów i wykup oraz właściwości użytkownika.


|Error — Klasa | Ciąg przyczyny/błędu| Jak obsłużyć |
|-----------|------------|----------------|
|`MsalUiRequiredException`| <ul><li>`INVALID_GRANT`: Token odświeżania użyty do zrealizowania tokenu dostępu jest nieprawidłowy, wygasł lub odwołany. Przyczyną tego wyjątku może być zmiana hasła. </li><li>`NO_TOKENS_FOUND`: Token dostępu nie istnieje i nie można znaleźć tokenu odświeżania do zrealizowania tokenu dostępu.</li> <li>Wymagany krok<ul><li>Uwierzytelnianie wieloskładnikowe</li><li>Brakujące oświadczenia</li></ul></li><li>Zablokowany przez dostęp warunkowy (na przykład instalacja [brokera uwierzytelniania](./msal-android-single-sign-on.md) jest wymagana)</li><li>`NO_ACCOUNT_FOUND`: W pamięci podręcznej nie ma dostępnego konta na potrzeby uwierzytelniania dyskretnego.</li></ul> |Wywołaj `acquireToken()` , aby monitować użytkownika o podanie nazwy użytkownika i hasła oraz prawdopodobnie wyrazić zgodę i przeprowadzić uwierzytelnianie wieloskładnikowe.|
|`MsalDeclinedScopeException`|<ul><li>`DECLINED_SCOPE`: Użytkownik lub serwer nie zaakceptował wszystkich zakresów. Serwer może odrzucać zakres, jeśli żądany zakres nie jest obsługiwany, nie został rozpoznany lub nie jest obsługiwany dla danego konta. </li></ul>| Deweloper powinien zdecydować, czy kontynuować uwierzytelnianie z przyznanymi zakresami, czy zakończyć proces uwierzytelniania. Opcja ponownego przesłania żądania pobrania tokenu tylko dla przyznanych zakresów i podaj wskazówki, dla których uprawnienia zostały przyznane przez przekazywanie `silentParametersForGrantedScopes` i wywoływanie `acquireTokenSilent` . |
|`MsalServiceException`|<ul><li>`INVALID_REQUEST`: To żądanie nie ma wymaganego parametru, zawiera nieprawidłowy parametr, zawiera parametr więcej niż jeden raz lub jest nieprawidłowo sformułowany. </li><li>`SERVICE_NOT_AVAILABLE`: Reprezentuje kody błędów 500/503/506 ze względu na to, że usługa jest wyłączona. </li><li>`UNAUTHORIZED_REQUEST`: Klient nie ma autoryzacji do żądania kodu autoryzacji.</li><li>`ACCESS_DENIED`: Właściciel zasobu lub serwer autoryzacji odrzucił żądanie.</li><li>`INVALID_INSTANCE`: `AuthorityMetadata` Walidacja nie powiodła się</li><li>`UNKNOWN_ERROR`: Żądanie do serwera nie powiodło się, ale nie wystąpił błąd i `error_description` zostały zwrócone z usługi.</li><ul>| Ta klasa wyjątku reprezentuje błędy podczas komunikacji z usługą, może pochodzić z punktów końcowych autoryzacji lub tokenu. MSAL odczytuje błąd i error_description z odpowiedzi serwera. Zazwyczaj te błędy są rozwiązywane przez naprawianie konfiguracji aplikacji w kodzie lub w portalu rejestracji aplikacji. Sporadyczne awarie usługi mogą wyzwolić to ostrzeżenie, co może być zminimalizowane przez oczekiwanie na odzyskanie usługi.  |
|`MsalClientException`|<ul><li> `MULTIPLE_MATCHING_TOKENS_DETECTED`: Znaleziono wiele wpisów pamięci podręcznej, a zestaw SDK nie może zidentyfikować poprawnego dostępu lub tokenu odświeżania z pamięci podręcznej. Ten wyjątek zwykle wskazuje na usterkę w zestawie SDK do przechowywania tokenów lub że Urząd nie został podany w żądaniu dyskretnym i znaleziono wiele zgodnych tokenów. </li><li>`DEVICE_NETWORK_NOT_AVAILABLE`: Żadna z aktywnych sieci nie jest dostępna na urządzeniu. </li><li>`JSON_PARSE_FAILURE`: Zestaw SDK nie może przeanalizować formatu JSON.</li><li>`IO_ERROR`: `IOException` Wystąpił błąd urządzenia lub sieci. </li><li>`MALFORMED_URL`: Adres URL jest źle sformułowany. Prawdopodobnie przyczyną jest konstruowanie żądania uwierzytelniania, urzędu lub identyfikatora URI przekierowania. </li><li>`UNSUPPORTED_ENCODING`: Kodowanie nie jest obsługiwane przez urządzenie. </li><li>`NO_SUCH_ALGORITHM`: Algorytm używany do generowania wezwania [PKCE](https://tools.ietf.org/html/rfc7636) nie jest obsługiwany. </li><li>`INVALID_JWT`: `JWT` zwrócony przez serwer jest nieprawidłowy lub jest pusty lub źle sformułowany. </li><li>`STATE_MISMATCH`: Stan z odpowiedzi autoryzacji jest niezgodny z stanem w żądaniu autoryzacji. W przypadku żądań autoryzacji zestaw SDK sprawdzi stan zwrócony z przekierowania i ten, który został wysłany w żądaniu. </li><li>`UNSUPPORTED_URL`: Nieobsługiwany adres URL, nie można przeprowadzić walidacji urzędu usług AD FS. </li><li> `AUTHORITY_VALIDATION_NOT_SUPPORTED`: Urząd nie jest obsługiwany na potrzeby weryfikacji urzędu. Zestaw SDK obsługuje urzędy B2C, ale nie obsługuje weryfikacji urzędu B2C. Obsługiwane są tylko dobrze znane hosty. </li><li>`CHROME_NOT_INSTALLED`: Przeglądarka Chrome nie jest zainstalowana na urządzeniu. Zestaw SDK używa niestandardowej karty Chrome dla żądań autoryzacji, jeśli jest dostępny, i powróci do przeglądarki Chrome. </li><li>`USER_MISMATCH`: Użytkownik podany w żądaniu tokenu uzyskiwania nie jest zgodny z użytkownikiem zwróconym z serwera.</li></ul>|Ta klasa wyjątku reprezentuje błędy ogólne, które są lokalne dla biblioteki. Te wyjątki mogą być obsługiwane przez poprawienie żądania.|
|`MsalUserCancelException`|<ul><li>`USER_CANCELED`: Użytkownik zainicjował przepływ interaktywny i przed odebraniem tokenów anulował żądanie. </li></ul>||
|`MsalArgumentException`|<ul><li>`ILLEGAL_ARGUMENT_ERROR_CODE`</li><li>`AUTHORITY_REQUIRED_FOR_SILENT`: Należy określić urząd dla `acquireTokenSilent` .</li></ul>|Te błędy mogą być rozwiązywane przez argumenty poprawiające dewelopera i zapewnić działanie dla uwierzytelniania interaktywnego, wywołania zwrotnego, zakresy i konto z prawidłowym IDENTYFIKATORem.|


## <a name="catching-errors"></a>Przechwytywanie błędów

Poniższy fragment kodu przedstawia przykład przechwytywania błędów w przypadku wywołań dyskretnych `acquireToken` .

```java
/**
 * Callback used in for silent acquireToken calls.
 */
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");

            /* Successfully got a token, use it to call a protected resource - MSGraph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    };
}
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [błędach rejestrowania](./msal-logging.md?tabs=android)