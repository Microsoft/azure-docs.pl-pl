---
title: Uaktualnij interfejs API wyszukiwania wideo Bing V5 do wersji 7
titleSuffix: Azure Cognitive Services
description: Identyfikuje części aplikacji wyszukiwanie wideo Bing, które należy zaktualizować, aby użyć wersji 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: e47691c82d43a35a7235eb5c3fd3cf7bca081aee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96351916"
---
# <a name="video-search-api-upgrade-guide"></a>Przewodnik uaktualniania wyszukiwanie wideo API

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Enterprise Agreement, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Ten przewodnik uaktualniania identyfikuje zmiany między wersjami 5 i 7 interfejs API wyszukiwania wideo Bing. Skorzystaj z tego przewodnika, aby ułatwić identyfikację części aplikacji, które należy zaktualizować, aby użyć wersji 7.

## <a name="breaking-changes"></a>Fundamentalne zmiany

### <a name="endpoints"></a>Punkty końcowe

- Numer wersji punktu końcowego został zmieniony z 5 na wersji 7. Na przykład `https://api.cognitive.microsoft.com/bing/v7.0/videos/search`.

### <a name="error-response-objects-and-error-codes"></a>Obiekty odpowiedzi błędów i kody błędów

- Wszystkie żądania zakończone niepowodzeniem powinny teraz zawierać `ErrorResponse` obiekt w treści odpowiedzi.

- Dodano następujące pola do `Error` obiektu.  
  - `subCode`&mdash;Dzieli kod błędu do zasobników dyskretnych, o ile to możliwe
  - `moreDetails`&mdash;Dodatkowe informacje na temat błędu opisanego w `message` polu
   

- Zamieniono kody błędów v5 z następującymi możliwymi `code` wartościami i `subCode` .

|Kod|Podkod|Opis
|-|-|-
|Błąd servererror|UnexpectedError<br/>ResourceError<br/>Nie zaimplementowano|Bing zwraca błąd servererror w każdym wystąpieniu kodu podrzędnego. Odpowiedź zawiera te błędy, jeśli kod stanu HTTP to 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Zablokowane|Bing zwraca InvalidRequest, gdy jakakolwiek część żądania jest nieprawidłowa. Na przykład brakuje wymaganego parametru lub wartość parametru jest nieprawidłowa.<br/><br/>Jeśli błąd to ParameterMissing lub ParameterInvalidValue, kod stanu HTTP to 400.<br/><br/>Jeśli błąd to HttpNotAllowed, kod stanu HTTP 410.
|RateLimitExceeded||Bing zwraca RateLimitExceeded za każdym razem, gdy przekroczą limit przydziału zapytań na sekundę (zapytań) lub zapytania miesięcznie (QPM).<br/><br/>Bing zwraca kod stanu HTTP 429 w przypadku przekroczenia zapytań i 403 w przypadku przekroczenia QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing zwraca InvalidAuthorization, gdy Bing nie może uwierzytelnić obiektu wywołującego. Na przykład `Ocp-Apim-Subscription-Key` brakuje nagłówka lub klucz subskrypcji jest nieprawidłowy.<br/><br/>Nadmiarowość występuje, jeśli określono więcej niż jedną metodę uwierzytelniania.<br/><br/>Jeśli błąd to InvalidAuthorization, kod stanu HTTP to 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Usługa Bing zwraca InsufficientAuthorization, gdy obiekt wywołujący nie ma uprawnień dostępu do zasobu. Taka sytuacja może wystąpić, jeśli klucz subskrypcji został wyłączony lub wygasł. <br/><br/>Jeśli błąd to InsufficientAuthorization, kod stanu HTTP to 403.

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

### <a name="query-parameters"></a>Parametry zapytania

- Nazwa `modulesRequested` parametru zapytania została zmieniona na [moduły](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#modulesrequested).  

### <a name="object-changes"></a>Zmiany obiektów

- Zmieniono nazwę `nextOffsetAddCount` pola [wideo](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) na `nextOffset` . Sposób korzystania z przesunięcia również został zmieniony. Wcześniej należy ustawić parametr zapytania [przesunięcia](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset) na `nextOffset` wartość i poprzednią wartość przesunięcia oraz liczbę filmów wideo w wyniku. Teraz możesz po prostu ustawić `offset` parametr zapytania na `nextOffset` wartość.  
  
- Zmieniono typ danych `relatedVideos` pola z `Video[]` na [VideosModule](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videosmodule) (zobacz [VideoDetails](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videodetails)).