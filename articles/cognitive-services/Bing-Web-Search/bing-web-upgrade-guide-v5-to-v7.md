---
title: Uaktualnianie z interfejsu API V5 do wersji 7 — interfejs API wyszukiwania w sieci Web Bing
titleSuffix: Azure Cognitive Services
description: Ustal, które części aplikacji wymagają aktualizacji, aby używać wyszukiwanie w sieci Web Bing interfejsów API wersji 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: 7ee8d05a542c6906d4ebe70f7e2a461752c6e3f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85609456"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Uaktualnianie z wersji interfejs API wyszukiwania w sieci Web Bing V5 do wersji 7

Ten przewodnik uaktualniania identyfikuje zmiany między wersjami 5 i 7 interfejs API wyszukiwania w sieci Web Bing. Skorzystaj z tego przewodnika, aby ułatwić identyfikację części aplikacji, które należy zaktualizować, aby użyć wersji 7.

## <a name="breaking-changes"></a>Zmiany powodujące niezgodność

### <a name="endpoints"></a>Punkty końcowe

- Numer wersji punktu końcowego zmienił się z 5 na wersji 7. Na przykład https: \/ \/ API.Cognitive.Microsoft.com/Bing/**v 7.0**/Search.

### <a name="error-response-objects-and-error-codes"></a>Obiekty odpowiedzi błędów i kody błędów

- Wszystkie żądania zakończone niepowodzeniem powinny teraz zawierać `ErrorResponse` obiekt w treści odpowiedzi.

- Dodano następujące pola do `Error` obiektu.  
  - `subCode`&mdash;Dzieli kod błędu do zasobników dyskretnych, o ile to możliwe
  - `moreDetails`&mdash;Dodatkowe informacje na temat błędu opisanego w `message` polu


- Zamieniono kody błędów v5 z następującymi możliwymi `code` wartościami i `subCode` .

|Kod|Podkod|Opis
|-|-|-
|Błąd servererror|UnexpectedError<br/>ResourceError<br/>Nie zaimplementowano|Bing zwraca błąd servererror za każdym razem, gdy wystąpi którykolwiek z warunków podkodu. Odpowiedź będzie zawierać te błędy, jeśli kod stanu HTTP to 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Zablokowane|Bing zwraca InvalidRequest, gdy jakakolwiek część żądania jest nieprawidłowa. Na przykład brakuje wymaganego parametru lub wartość parametru jest nieprawidłowa.<br/><br/>Jeśli błąd to ParameterMissing lub ParameterInvalidValue, kod stanu HTTP to 400.<br/><br/>Jeśli błąd to HttpNotAllowed, kod stanu HTTP 410.
|RateLimitExceeded||Bing zwraca RateLimitExceeded za każdym razem, gdy przekroczą limit przydziału zapytań na sekundę (zapytań) lub zapytania miesięcznie (QPM).<br/><br/>Bing zwraca kod stanu HTTP 429 w przypadku przekroczenia zapytań i 403 w przypadku przekroczenia QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing zwraca InvalidAuthorization, gdy Bing nie może uwierzytelnić obiektu wywołującego. Na przykład `Ocp-Apim-Subscription-Key` brakuje nagłówka lub klucz subskrypcji jest nieprawidłowy.<br/><br/>Nadmiarowość występuje, jeśli określono więcej niż jedną metodę uwierzytelniania.<br/><br/>Jeśli błąd to InvalidAuthorization, kod stanu HTTP to 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Usługa Bing zwraca InsufficientAuthorization, gdy obiekt wywołujący nie ma uprawnień dostępu do zasobu. Ten błąd może wystąpić, jeśli klucz subskrypcji został wyłączony lub wygasł. <br/><br/>Jeśli błąd to InsufficientAuthorization, kod stanu HTTP to 403.

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


## <a name="non-breaking-changes"></a>Niekrytyczne zmiany  

### <a name="headers"></a>Nagłówki

- Dodano opcjonalny nagłówek żądania [pragma](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#pragma) . Usługa Bing domyślnie zwraca zawartość buforowaną, jeśli jest dostępna. Aby zapobiec zwracaniu zawartości buforowanej przez usługę Bing, dla nagłówka Pragma ustaw wartość no-cache (na przykład Pragma: no-cache).

### <a name="query-parameters"></a>Parametry zapytania

- Dodano parametr zapytania [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) . Użyj tego parametru, aby określić liczbę odpowiedzi, które ma uwzględnić odpowiedź. Odpowiedzi są wybierane na podstawie klasyfikacji. Na przykład, jeśli ustawisz ten parametr na trzy (3), odpowiedź zawiera trzy pierwsze odpowiedzi z rangą.  

- Dodano parametr [podwyższanie poziomu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) zapytania. Użyj tego parametru wraz z `answerCount` , aby jawnie uwzględnić jeden lub więcej typów odpowiedzi niezależnie od ich klasyfikacji. Na przykład w celu promowania wideo i obrazów w odpowiedzi należy ustawić opcję Podwyższ poziom do *filmów wideo*. Lista odpowiedzi, które chcesz podwyższyć, nie jest uwzględniana w `answerCount` limicie. Na przykład jeśli wartość `answerCount` jest równa 2 i `promote` jest ustawiona na *wideo, obrazy*, odpowiedź może zawierać strony sieci Web, wiadomości, wideo i obrazy.

### <a name="object-changes"></a>Zmiany obiektów

- Dodano `someResultsRemoved` pole do obiektu [webodpowiedź](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) . Pole zawiera wartość logiczną wskazującą, czy odpowiedź wyklucza niektóre wyniki z odpowiedzi w sieci Web.  
