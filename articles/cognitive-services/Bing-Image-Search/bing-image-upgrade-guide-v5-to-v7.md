---
title: Uaktualnianie z wersji interfejs API wyszukiwania obrazów Bing V5 do wersji 7
titleSuffix: Azure Cognitive Services
description: Ten przewodnik uaktualniania zawiera informacje o zmianach między wersjami 5 i 7 interfejs API wyszukiwania obrazów Bing. Skorzystaj z tego przewodnika, aby ułatwić identyfikację części aplikacji, które należy zaktualizować, aby użyć wersji 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: d10db37934bb0d6571eb0191d5f1be47dae000ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96342179"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Podręcznik uaktualnienia interfejs API wyszukiwania obrazów Bing wersji 7

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Enterprise Agreement, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Ten przewodnik uaktualniania identyfikuje zmiany między wersjami 5 i 7 interfejs API wyszukiwania obrazów Bing. Skorzystaj z tego przewodnika, aby ułatwić identyfikację części aplikacji, które należy zaktualizować, aby użyć wersji 7.

## <a name="breaking-changes"></a>Fundamentalne zmiany

### <a name="endpoints"></a>Punkty końcowe

- Numer wersji punktu końcowego został zmieniony z 5 na wersji 7. Na przykład https: \/ /API.Cognitive.Microsoft.com/Bing/ \* \* v 7.0 * */images/Search.

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

- Nazwa `modulesRequested` parametru zapytania została zmieniona na [moduły](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).  

- Zmieniono nazwy adnotacji na Tagi. Zobacz [](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) Tags Query Parameter.  

- Zmieniono listę obsługiwanych rynków wartości filtru ShoppingSources na en-US. Zobacz [ImageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Zmiany w usłudze Image Insights

- Zmieniono nazwę `annotations` pola elementu [ImagesInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) na `imageTags` .  

- Zmieniono nazwę `AnnotationModule` obiektu na [ImageTagsModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetagsmodule).  

- Zmieniono nazwę `Annotation` obiektu na [tag](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#tag)i usunięto `confidence` pole.  

- Zmieniono nazwę `insightsSourcesSummary` pola obiektu [obrazu](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) na `insightsMetadata` .  

- Zmieniono nazwę `InsightsSourcesSummary` obiektu na [InsightsMetadata](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightsmetadata).  

- Dodano `https://api.cognitive.microsoft.com/bing/v7.0/images/details` punkt końcowy. Użyj tego punktu końcowego, aby zażądać szczegółowych informacji o obrazie zamiast punktu końcowego/images/Search. Zobacz [szczegółowe informacje o obrazie](./image-insights.md).

- Następujące parametry zapytania są teraz prawidłowe tylko w `/images/details` punkcie końcowym.  

    -   [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)  
    -   [Moduły](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  
    -   [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)  
    -   [CAB](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  
    -   [cal](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
    -   [car](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
    -   [Cat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
    -   [CT](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#ct)  

- Zmieniono nazwę `ImageInsightsResponse` obiektu na [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights).  

- Zmieniono typy danych następujących pól w obiekcie [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights) .  

    -   Zmieniono typ `relatedCollections` pola z `ImageGallery[]` na [RelatedCollectionsModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedcollectionsmodule).  

    -   Zmieniono typ `pagesIncluding` pola z `Image[]` na [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Zmieniono typ `relatedSearches` pola z `Query[]` na [RelatedSearchesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedsearchesmodule).  

    -   Zmieniono typ `recipes` pola z `Recipe[]` na [RecipesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recipesmodule).  

    -   Zmieniono typ `visuallySimilarImages` pola z `Image[]` na [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Zmieniono typ `visuallySimilarProducts` pola z `ProductSummaryImage[]` na [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Usunął `ProductSummaryImage` obiekt i przeniósł pola powiązane z produktem do obiektu [obrazu](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) . `Image`Obiekt zawiera pola powiązane z produktem tylko wtedy, gdy obraz jest dołączony jako część produktów podobnych do wizualizacji w odpowiedzi na szczegółowe informacje o obrazie.  

    -   Zmieniono typ `recognizedEntityGroups` pola z `RecognizedEntityGroup[]` na [RecognizedEntitiesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recognizedentitiesmodule).  

-   Zmieniono nazwę `categoryClassification` pola elementu [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) na `annotations` i zmieniono jego typ na `AnnotationsModule` .  

### <a name="images-answer"></a>Odpowiedź na obrazy

-   Usunięto pola displayShoppingSourcesBadges i displayRecipeSourcesBadges z [obrazów](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images).  

-   Zmieniono nazwę `nextOffsetAddCount` pola [obrazów](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) na `nextOffset` . Sposób korzystania z przesunięcia również został zmieniony. Wcześniej należy ustawić parametr zapytania [przesunięcia](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) na `nextOffsetAddCount` wartość i poprzednią wartość przesunięcia oraz liczbę obrazów w wyniku. Teraz ustawiasz `offset` `nextOffset` wartość.  


## <a name="non-breaking-changes"></a>Niekrytyczne zmiany

### <a name="query-parameters"></a>Parametry zapytania

- Dodano przezroczysty jako możliwą wartość filtru [ImageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) . Filtr przezroczysty zwraca tylko obrazy z przezroczystym tłem.

- Dodano dowolną wartość filtru [licencji](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license) . Dowolny filtr zwraca tylko obrazy, które są objęte licencją.

- Dodano parametry zapytania [MaxFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) i [minFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) . Użyj tych filtrów, aby zwrócić obrazy w zakresie rozmiarów plików.  

- Dodano parametry zapytania [maxHeight](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxheight), [minHeight](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minheight), [MaxWidth](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxwidth), [minWidth](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minwidth) . Użyj tych filtrów, aby zwrócić obrazy w zakresie wysokości i szerokości.  

### <a name="object-changes"></a>Zmiany obiektów

- Dodano `description` pola i `lastUpdated` do obiektu [oferty](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offer) .  

- Dodano `name` pole do obiektu [ImageGallery](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagegallery) .  

- Dodano `similarTerms` do obiektu [images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) . To pole zawiera listę warunków, które są podobne w znaczeniu dla ciągu zapytania użytkownika.