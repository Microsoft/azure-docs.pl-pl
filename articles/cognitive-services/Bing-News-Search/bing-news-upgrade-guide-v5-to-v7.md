---
title: Uaktualnij interfejs API wyszukiwania wiadomości Bing V5 do wersji 7
titleSuffix: Azure Cognitive Services
description: Identyfikuje części aplikacji wyszukiwanie wiadomości Bing, które należy zaktualizować, aby użyć wersji 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: 564af32b724c8b4883cd27d01813e246e5fa4901
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100205"
---
# <a name="news-search-api-upgrade-guide"></a>Przewodnik uaktualniania wyszukiwanie wiadomości API

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Ten przewodnik uaktualniania identyfikuje zmiany między wersjami 5 i 7 interfejs API wyszukiwania wiadomości Bing. Skorzystaj z tego przewodnika, aby ułatwić identyfikację części aplikacji, które należy zaktualizować, aby użyć wersji 7.

## <a name="breaking-changes"></a>Zmiany powodujące niezgodność

### <a name="endpoints"></a>Punkty końcowe

- Numer wersji punktu końcowego został zmieniony z 5 na wersji 7. Na przykład `https://api.cognitive.microsoft.com/bing/v7.0/news/search`.

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

### <a name="object-changes"></a>Zmiany obiektów

- Dodano `contractualRules` pole do obiektu [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) . `contractualRules`Pole zawiera listę reguł, które należy wykonać (na przykład przypisanie artykułu). Należy zastosować przypisanie podane w `contractualRules` zamiast przy użyciu `provider` . Artykuł zawiera `contractualRules` informacje tylko wtedy, gdy odpowiedź [interfejsu API wyszukiwanie w sieci Web](../bing-web-search/search-the-web.md) zawiera odpowiedź na wiadomości.

## <a name="non-breaking-changes"></a>Niekrytyczne zmiany

### <a name="query-parameters"></a>Parametry zapytania

- Dodano produkty jako możliwe wartości, dla których można ustawić parametr zapytania [Category](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) . Zobacz [kategorie według rynków](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).

- Dodano parametr zapytania [SortBy —](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortby) , który zwraca tematy trendów posortowane według daty z ostatnim pierwszym.

- Dodano parametr [od](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#since) zapytania, który zwraca tematy trendów, które zostały odnalezione przez usługę Bing w lub po określonej sygnaturze czasowej epoki z systemem UNIX.

### <a name="object-changes"></a>Zmiany obiektów

- Dodano `mentions` pole do obiektu [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) . `mentions`Pole zawiera listę jednostek (osób lub miejsc), które zostały znalezione w artykule.

- Dodano `video` pole do obiektu [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) . `video`Pole zawiera film wideo dotyczący artykułu z wiadomościami. Film wideo to element \<iframe\> , który można osadzić lub miniaturę ruchu.

- Dodano `sort` pole do obiektu [News](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) . W tym `sort` polu jest wyświetlana kolejność sortowania artykułów. Na przykład artykuły są sortowane według istotności (wartość domyślna) lub daty.

- Dodano obiekt [SortValue](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortvalue) , który definiuje porządek sortowania. `isSelected`Pole wskazuje, czy odpowiedź była używana w kolejności sortowania. W przypadku **wartości true** odpowiedź była używana w kolejności sortowania. Jeśli `isSelected` ma **wartość false** , możesz użyć adresu URL w `url` polu, aby zażądać innej kolejności sortowania.
