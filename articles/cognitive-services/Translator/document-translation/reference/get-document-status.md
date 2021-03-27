---
title: Metoda pobierania dokumentu dla tłumaczenia dokumentu
titleSuffix: Azure Cognitive Services
description: Metoda Get Document status zwraca stan określonego dokumentu.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 79bc3d076c1a7e164cab9c3231b29be84370e04a
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613150"
---
# <a name="document-translation-get-document-status"></a>Tłumaczenie dokumentu: pobieranie stanu dokumentu

Metoda Get Document status zwraca stan określonego dokumentu. Metoda zwraca stan tłumaczenia określonego dokumentu na podstawie identyfikatora żądania i identyfikatora dokumentu.

## <a name="request-url"></a>Adres URL żądania

Wyślij `GET` żądanie do:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents/{documentId}
```

Dowiedz się, jak znaleźć swoją [niestandardową nazwę domeny](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Wszystkie żądania interfejsu API do usługi tłumaczenia dokumentów wymagają niestandardowego punktu końcowego domeny**.
> * Nie można użyć punktu końcowego znalezionego na stronie klucze zasobów Azure Portal _i punktu_ końcowego ani globalnego punktu końcowego usługi Translator — `api.cognitive.microsofttranslator.com` w celu żądania protokołu HTTP do tłumaczenia dokumentów.

## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przesłane na ciągu zapytania są następujące:

|Parametr zapytania|Wymagane|Opis|
|--- |--- |--- |
|Element DocumentID|Prawda|Identyfikator dokumentu.|
|identyfikator|Prawda|Identyfikator wsadu.|
## <a name="request-headers"></a>Nagłówki żądań

Nagłówki żądań to:

|Nagłówki|Opis|
|--- |--- |
|Ocp-Apim-Subscription-Key|Wymagany nagłówek żądania|

## <a name="response-status-codes"></a>Kody stanu odpowiedzi

Oto możliwe kody stanu HTTP zwracane przez żądanie.

|Kod stanu|Opis|
|--- |--- |
|200|OK. Pomyślne żądanie i zostało zaakceptowane przez usługę. Są zwracane szczegóły operacji. HeadersRetry-After: integerETag: ciąg|
|401|Próby. Sprawdź swoje poświadczenia.|
|404|Nie znaleziono. Nie znaleziono zasobu.|
|500|Wewnętrzny błąd serwera.|
|Inne kody stanu|<ul><li>Zbyt wiele żądań</li><li>Serwer tymczasowy niedostępny</li></ul>|

## <a name="get-document-status-response"></a>Pobierz odpowiedź na stan dokumentu

### <a name="successful-get-document-status-response"></a>Pomyślne Pobieranie odpowiedzi o stanie dokumentu

|Nazwa|Typ|Opis|
|--- |--- |--- |
|path|ciąg|Lokalizacja dokumentu lub folderu.|
|createdDateTimeUtc|ciąg|Data i godzina utworzenia operacji.|
|lastActionDateTimeUtc|ciąg|Data i godzina aktualizacji stanu operacji.|
|status|Ciąg|Lista możliwych stanów dla zadania lub dokumentu: <ul><li>Anulowane</li><li>Anulowanie</li><li>Niepowodzenie</li><li>NotStarted</li><li>Uruchomienie</li><li>Powodzenie</li><li>ValidationFailed</li></ul>|
|na wartość|ciąg|Dwuliterowy kod języka w języku. Zapoznaj się z listą języków.|
|progress|liczba|Postęp tłumaczenia, jeśli jest dostępny|
|identyfikator|ciąg|Identyfikator dokumentu.|
|characterCharged|liczba całkowita|Znaki naliczone przez interfejs API.|

### <a name="error-response"></a>Odpowiedź na błąd

|Nazwa|Typ|Opis|
|--- |--- |--- |
|kod|ciąg|Wyliczenia zawierające kody błędów wysokiego poziomu. Możliwe wartości:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Brak autoryzacji</li></ul>|
|message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|
|innerError|InnerErrorV2|Nowy format błędu wewnętrznego, który jest zgodny ze wskazówkami dotyczącymi interfejsu API Cognitive Services. Zawiera wymagane właściwości ErrorCode, komunikat i opcjonalne właściwości target, szczegóły (para klucz wartość), błąd wewnętrzny (może być zagnieżdżony).|
|innerError. Code|ciąg|Pobiera ciąg błędu kodu.|
|innerError. Message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|

## <a name="examples"></a>Przykłady

### <a name="example-successful-response"></a>Przykład pomyślnej odpowiedzi
Poniższy obiekt JSON jest przykładem pomyślnej odpowiedzi.

```JSON
{
  "path": "https://myblob.blob.core.windows.net/destinationContainer/fr/mydoc.txt",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Running",
  "to": "fr",
  "progress": 0.1,
  "id": "273622bd-835c-4946-9798-fd8f19f6bbf2",
  "characterCharged": 0
}
```

### <a name="example-error-response"></a>Przykładowa odpowiedź na błąd

Poniższy obiekt JSON jest przykładem odpowiedzi na błąd. Schemat dla innych kodów błędów jest taki sam.

Kod stanu: 401

```JSON
{
  "error": {
    "code": "Unauthorized",
    "message": "User is not authorized",
    "target": "Document",
    "innerError": {
      "code": "Unauthorized",
      "message": "Operation is not authorized"
    }
  }
}
```

## <a name="next-steps"></a>Następne kroki

Skorzystaj z naszego przewodnika Szybki Start, aby dowiedzieć się więcej o korzystaniu z tłumaczenia dokumentów i biblioteki klienckiej.

> [!div class="nextstepaction"]
> [Wprowadzenie do tłumaczenia dokumentu](../get-started-with-document-translation.md)
