---
title: Uaktualnij sprawdzanie pisowni Bing API V5 do wersji 7
titleSuffix: Azure Cognitive Services
description: Identyfikuje części aplikacji sprawdzanie pisowni Bing, które należy zaktualizować, aby użyć wersji 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: 642aa23bc74f1f3f44deb6e489e6f7aafe7ff237
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91254129"
---
# <a name="spell-check-api-upgrade-guide"></a>Podręcznik uaktualnienia interfejs API sprawdzania pisowni

Ten przewodnik uaktualniania identyfikuje zmiany między wersjami 5 i 7 interfejsu API sprawdzanie pisowni Bing. Skorzystaj z tego przewodnika, aby ułatwić identyfikację części aplikacji, które należy zaktualizować, aby użyć wersji 7.

## <a name="breaking-changes"></a>Zmiany powodujące niezgodność

### <a name="endpoints"></a>Punkty końcowe

- Numer wersji punktu końcowego został zmieniony z 5 na wersji 7. Na przykład `https://api.cognitive.microsoft.com/bing/v7.0/spellcheck`.

### <a name="error-response-objects-and-error-codes"></a>Obiekty odpowiedzi błędów i kody błędów

- Wszystkie żądania zakończone niepowodzeniem powinny teraz zawierać `ErrorResponse` obiekt w treści odpowiedzi.

- Dodano następujące pola do `Error` obiektu.  
  - `subCode`&mdash;Dzieli kod błędu do zasobników dyskretnych, o ile to możliwe
  - `moreDetails`&mdash;Dodatkowe informacje na temat błędu opisanego w `message` polu
   

- Zamieniono kody błędów v5 z następującymi możliwymi `code` wartościami i `subCode` .  
  
|Kod|Podkod|Opis
|-|-|-
|Błąd servererror|UnexpectedError<br/>ResourceError<br/>Nie zaimplementowano|Bing zwraca błąd servererror za każdym razem, gdy wystąpi którykolwiek z warunków podkodu. Odpowiedź zawiera te błędy, jeśli kod stanu HTTP to 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Zablokowane|Bing zwraca InvalidRequest, gdy jakakolwiek część żądania nie jest prawidłowa. Na przykład brakuje wymaganego parametru lub wartość parametru jest nieprawidłowa.<br/><br/>Jeśli błąd to ParameterMissing lub ParameterInvalidValue, kod stanu HTTP to 400.<br/><br/>Jeśli błąd to HttpNotAllowed, kod stanu HTTP 410.
|RateLimitExceeded||Bing zwraca RateLimitExceeded za każdym razem, gdy przekroczą limit przydziału zapytań na sekundę (zapytań) lub zapytania miesięcznie (QPM).<br/><br/>Bing zwraca kod stanu HTTP 429 w przypadku przekroczenia zapytań i 403 w przypadku przekroczenia QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing zwraca InvalidAuthorization, gdy Bing nie może uwierzytelnić obiektu wywołującego. Na przykład `Ocp-Apim-Subscription-Key` brakuje nagłówka lub klucz subskrypcji jest nieprawidłowy.<br/><br/>Nadmiarowość występuje, jeśli określono więcej niż jedną metodę uwierzytelniania.<br/><br/>Jeśli błąd to InvalidAuthorization, kod stanu HTTP to 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing zwraca InsufficientAuthorization, gdy obiekt wywołujący nie ma uprawnień dostępu do zasobu. Taka sytuacja może wystąpić, jeśli klucz subskrypcji został wyłączony lub wygasł. <br/><br/>Jeśli błąd to InsufficientAuthorization, kod stanu HTTP to 403.

- Poniżej przedstawiono mapowanie poprzednich kodów błędów do nowych kodów. Jeśli pobrano zależność od kodów błędów w programie V5, zaktualizuj kod odpowiednio.  
  
|Kod w wersji 5|Kod w wersji 7. podkod
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Disabled|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|Błąd servererror. UnexpectedError
DataSourceErrors|Błąd servererror. ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
Nie zaimplementowano|Błąd servererror. nie zaimplementowano
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Zablokowane|InvalidRequest. zablokowane

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wymagania dotyczące użycia i wyświetlania](./UseAndDisplayRequirements.md)
