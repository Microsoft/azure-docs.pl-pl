---
title: Obsługa błędów i wyjątków w MSAL4J
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak obsługiwać błędy i wyjątki, wyzwania dotyczące oświadczeń dostępu warunkowego i ponawianie prób w aplikacjach MSAL4J.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/27/2020
ms.author: marsma
ms.reviewer: saeeda, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8563804a736c37acc9a96eb4a186933507f34200
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98760702"
---
# <a name="handle-errors-and-exceptions-in-msal-for-java"></a>Obsługa błędów i wyjątków w bibliotece MSAL dla języka Java

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-java"></a>Obsługa błędów w programie MSAL for Java

W programie MSAL for Java istnieją trzy typy wyjątków: `MsalClientException` , i, `MsalServiceException` `MsalInteractionRequiredException` które dziedziczą z `MsalException` .

- `MsalClientException` jest zgłaszany w przypadku wystąpienia błędu, który jest lokalny dla biblioteki lub urządzenia.
- `MsalServiceException` jest zgłaszany, gdy usługa bezpiecznego tokenu (STS) zwróci odpowiedź na błąd lub Wystąpił inny błąd sieciowy.
- `MsalInteractionRequiredException` jest zgłaszany, gdy do pomyślnego uwierzytelnienia wymagane jest współdziałanie z interfejsem użytkownika.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` przedstawia nagłówki HTTP zwracane w żądaniach do usługi STS. Uzyskaj dostęp do nich za pośrednictwem `MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

Jeden z typowych kodów stanu zwróconych z MSAL for Java podczas `AcquireTokenSilently()` wywoływania `InvalidGrantError` . Oznacza to, że przed wystawieniem tokenu uwierzytelniania wymagane jest dodatkowe interakcje użytkownika. Aplikacja powinna ponownie wywołać bibliotekę uwierzytelniania, ale w trybie interaktywnym, wysyłając `AuthorizationCodeParameters` lub `DeviceCodeParameters` do publicznych aplikacji klienckich.

Większość czasu `AcquireTokenSilently` , gdy nie powiedzie się, jest to spowodowane tym, że pamięć podręczna tokenów nie ma tokenu pasującego do żądania. Tokeny dostępu wygasają w ciągu godziny i `AcquireTokenSilently` spróbują uzyskać nowy plik na podstawie tokenu odświeżania. W OAuth2ch jest to przepływ tokenu odświeżania. Ten przepływ może również zakończyć się niepowodzeniem z różnych powodów, takich jak Administrator dzierżawy konfiguruje bardziej rygorystyczne zasady logowania.

Niektóre warunki wynikające z tego błędu są łatwe do rozwiązania użytkownikom. Na przykład może być konieczne zaakceptowanie warunków użytkowania lub żądanie nie może zostać spełnione z bieżącą konfiguracją, ponieważ maszyna musi nawiązać połączenie z określoną siecią firmową.

MSAL uwidacznia `reason` pole, którego można użyć, aby zapewnić lepsze środowisko użytkownika. Na przykład `reason` pole może prowadzić do poinformowania użytkownika o wygaśnięciu hasła lub zapewnieniu zgody na korzystanie z niektórych zasobów. Obsługiwane wartości są częścią  `InteractionRequiredExceptionReason` wyliczenia:

| Przyczyna | Znaczenie | Zalecana obsługa |
|---------|-----------|-----------------------------|
| `BasicAction` | Warunek może zostać rozpoznany przez interakcję użytkownika podczas przepływu uwierzytelniania interaktywnego. | Wywoływanie `acquireToken` przy użyciu parametrów interaktywnych. |
| `AdditionalAction` | Warunek można rozwiązać przez dodatkowe interakcje z systemem poza przepływem uwierzytelniania interaktywnego. | Wywołaj `acquireToken` Parametry interaktywne, aby wyświetlić komunikat objaśniający akcję zaradczą do wykonania. Aplikacja wywołująca może zdecydować się na ukrycie przepływów, które wymagają dodatkowej akcji, jeśli użytkownik nie będzie mógł wykonać akcji naprawczej. |
| `MessageOnly` | Nie można teraz rozwiązać warunku. Uruchom interaktywny przepływ uwierzytelniania, aby wyświetlić komunikat objaśniający warunek. | Wywołaj `acquireToken` z parametrami interaktywnym, aby wyświetlić komunikat objaśniający warunek. `acquireToken` zwróci błąd, `UserCanceled` gdy użytkownik odczyta komunikat i zamknie okno. Aplikacja może zdecydować się na ukrycie przepływów, które powodują, że użytkownik prawdopodobnie nie będzie mógł skorzystać z wiadomości. |
| `ConsentRequired`| Brak zgody użytkownika lub został on odwołany. |Wywołaj `acquireToken` Parametry interaktywne, aby użytkownik mógł wyrazić zgodę. |
| `UserPasswordExpired` | Hasło użytkownika wygasło. | Wywołaj `acquireToken` z parametrem Interactive, aby użytkownik mógł zresetować swoje hasło. |
| `None` |  Dostępne są również dalsze szczegóły. Warunek może zostać rozpoznany przez interakcję użytkownika podczas przepływu uwierzytelniania interaktywnego. | Wywoływanie `acquireToken` przy użyciu parametrów interaktywnych. |

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

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Następne kroki

Rozważ włączenie [rejestrowania w programie MSAL for Java](msal-logging-java.md) , aby ułatwić diagnozowanie i Debugowanie problemów.
