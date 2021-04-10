---
title: Stan operacji pobierania tłumaczenia dokumentu
titleSuffix: Azure Cognitive Services
description: Metoda pobieranie stanu operacji zwraca stan żądania tłumaczenia dokumentu.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: e8aa9b93dc089cc05dd5157a7ac84cceb5f6fcd5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613084"
---
# <a name="document-translation-get-operation-status"></a>Tłumaczenie dokumentu: pobieranie stanu operacji

Metoda Pobierz dokumenty operacji zwraca stan żądania tłumaczenia dokumentu. Stan obejmuje ogólny stan żądania oraz stan dokumentów, które są tłumaczone w ramach tego żądania.

## <a name="request-url"></a>Adres URL żądania

Wyślij `GET` żądanie do:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
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
|identyfikator|Prawda|Identyfikator operacji.|

## <a name="request-headers"></a>Nagłówki żądań

Nagłówki żądań to:

|Nagłówki|Opis|
|--- |--- |
|Ocp-Apim-Subscription-Key|Wymagany nagłówek żądania|

## <a name="response-status-codes"></a>Kody stanu odpowiedzi

Oto możliwe kody stanu HTTP zwracane przez żądanie.

|Kod stanu|Opis|
|--- |--- |
|200|OK. Pomyślne żądanie i zwraca stan operacji tłumaczenia wsadowego. HeadersRetry-After: integerETag: ciąg|
|401|Próby. Sprawdź swoje poświadczenia.|
|404|Nie znaleziono zasobu.|
|500|Wewnętrzny błąd serwera.|
|Inne kody stanu|<ul><li>Zbyt wiele żądań</li><li>Serwer tymczasowy niedostępny</li></ul>|

## <a name="get-operation-status-response"></a>Pobierz odpowiedź stanu operacji

### <a name="successful-get-operation-status-response"></a>Odpowiedź dotycząca stanu operacji pobrania powiodła się

Poniższe informacje są zwracane w odpowiedzi na pomyślne.

|Nazwa|Typ|Opis|
|--- |--- |--- |
|identyfikator|ciąg|Identyfikator operacji.|
|createdDateTimeUtc|ciąg|Data i godzina utworzenia operacji.|
|lastActionDateTimeUtc|ciąg|Data i godzina aktualizacji stanu operacji.|
|status|Ciąg|Lista możliwych stanów dla zadania lub dokumentu: <ul><li>Anulowane</li><li>Anulowanie</li><li>Niepowodzenie</li><li>NotStarted</li><li>Uruchomienie</li><li>Powodzenie</li><li>ValidationFailed</li></ul>|
|Podsumowanie|StatusSummary|Podsumowanie zawierające szczegóły wymienione poniżej.|
|summary. Total|liczba całkowita|Łączna liczba.|
|Podsumowanie. nie powiodło się|liczba całkowita|Liczba zakończonych niepowodzeniem.|
|summary. Success|liczba całkowita|Liczba zakończonych powodzeniem.|
|Podsumowanie. w toku|liczba całkowita|Liczba w toku.|
|summary. notYetStarted|liczba całkowita|Liczba nie została jeszcze uruchomiona.|
|Podsumowanie. anulowane|liczba całkowita|Liczba anulowanych.|
|summary. totalCharacterCharged|liczba całkowita|Łączna liczba znaków naliczanych przez interfejs API.|

###<a name="error-response"></a>Odpowiedź na błąd

|Nazwa|Typ|Opis|
|--- |--- |--- |
|kod|ciąg|Wyliczenia zawierające kody błędów wysokiego poziomu. Możliwe wartości:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Brak autoryzacji</li></ul>|
|message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|
|obiektów|ciąg|Pobiera Źródło błędu. Na przykład może to być "Documents" lub "ID dokumentu" dla nieprawidłowego dokumentu.|
|innerError|InnerErrorV2|Nowy format błędu wewnętrznego, który jest zgodny ze wskazówkami dotyczącymi interfejsu API Cognitive Services. Zawiera wymagane właściwości ErrorCode, komunikat i opcjonalne właściwości target, szczegóły (para klucz wartość), błąd wewnętrzny (może być zagnieżdżony).|
|innerError. Code|ciąg|Pobiera ciąg błędu kodu.|
|innerError. Message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|

## <a name="examples"></a>Przykłady

### <a name="example-successful-response"></a>Przykład pomyślnej odpowiedzi

Poniższy obiekt JSON jest przykładem pomyślnej odpowiedzi.

```JSON
{
  "id": "727bf148-f327-47a0-9481-abae6362f11e",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Succeeded",
  "summary": {
    "total": 10,
    "failed": 1,
    "success": 9,
    "inProgress": 0,
    "notYetStarted": 0,
    "cancelled": 0,
    "totalCharacterCharged": 0
  }
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
