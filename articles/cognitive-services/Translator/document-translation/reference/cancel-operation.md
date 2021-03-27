---
title: Metoda operacji anulowania tłumaczenia dokumentu
titleSuffix: Azure Cognitive Services
description: Metoda Cancel Operations anuluje aktualnie przetwarzane lub kolejkowane operacje.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 39730f118dd93a972f238f85ef890f4dc54ca91a
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613162"
---
# <a name="document-translation-cancel-operations"></a>Tłumaczenie dokumentu: operacje anulowania

Anuluj aktualnie przetwarzane lub kolejkowane operacje. Operacja nie zostanie anulowana, jeśli została już zakończona lub nie powiodła się lub została anulowana. Zostanie zwrócone Nieprawidłowe żądanie. Wszystkie dokumenty, które ukończyły tłumaczenie nie zostaną anulowane i zostaną naliczone opłaty. Wszystkie dokumenty oczekujące zostaną anulowane, jeśli jest to możliwe.

## <a name="request-url"></a>Adres URL żądania

Wyślij `DELETE` żądanie do:
```DELETE HTTP
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
```

Dowiedz się, jak znaleźć swoją [niestandardową nazwę domeny](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Wszystkie żądania interfejsu API do usługi tłumaczenia dokumentów wymagają niestandardowego punktu końcowego domeny**.
> * Nie można użyć punktu końcowego znalezionego na stronie klucze zasobów Azure Portal _i punktu_ końcowego ani globalnego punktu końcowego usługi Translator — `api.cognitive.microsofttranslator.com` w celu żądania protokołu HTTP do tłumaczenia dokumentów.

## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przesłane na ciągu zapytania są następujące:

|Parametr zapytania|Wymagane|Opis|
|-----|-----|-----|
|identyfikator|Prawda|Identyfikator operacji.|

## <a name="request-headers"></a>Nagłówki żądań

Nagłówki żądań to:

|Nagłówki|Opis|
|-----|-----|
|Ocp-Apim-Subscription-Key|Wymagany nagłówek żądania|

## <a name="response-status-codes"></a>Kody stanu odpowiedzi

Oto możliwe kody stanu HTTP zwracane przez żądanie.

| Kod stanu| Opis|
|-----|-----|
|200|OK. Żądanie anulowania zostało przesłane|
|401|Próby. Sprawdź swoje poświadczenia.|
|404|Nie znaleziono. Nie znaleziono zasobu. 
|500|Wewnętrzny błąd serwera.
|Inne kody stanu|<ul><li>Zbyt wiele żądań</li><li>Serwer tymczasowy niedostępny</li></ul>|

## <a name="cancel-operations-response"></a>Anuluj odpowiedź na operacje

### <a name="successful-response"></a>Pomyślna odpowiedź

Poniższe informacje są zwracane w odpowiedzi na pomyślne.

|Nazwa|Typ|Opis|
|--- |--- |--- |
|identyfikator|ciąg|Identyfikator operacji.|
|createdDateTimeUtc|ciąg|Data i godzina utworzenia operacji.|
|lastActionDateTimeUtc|ciąg|Data i godzina aktualizacji stanu operacji.|
|status|Ciąg|Lista możliwych stanów dla zadania lub dokumentu: <ul><li>Anulowane</li><li>Anulowanie</li><li>Niepowodzenie</li><li>NotStarted</li><li>Uruchomienie</li><li>Powodzenie</li><li>ValidationFailed</li></ul>|
|Podsumowanie|StatusSummary|Podsumowanie zawierające szczegóły wymienione poniżej.|
|summary. Total|liczba całkowita|Łączna liczba dokumentów.|
|Podsumowanie. nie powiodło się|liczba całkowita|Liczba dokumentów zakończonych niepowodzeniem.|
|summary. Success|liczba całkowita|Liczba pomyślnie przetłumaczonych dokumentów.|
|Podsumowanie. w toku|liczba całkowita|Liczba dokumentów w toku.|
|summary. notYetStarted|liczba całkowita|Liczba dokumentów, które nie zostały jeszcze uruchomione.|
|Podsumowanie. anulowane|liczba całkowita|Liczba anulowanych.|
|summary. totalCharacterCharged|liczba całkowita|Łączna liczba znaków naliczanych przez interfejs API.|

### <a name="error-response"></a>Odpowiedź na błąd

|Nazwa|Typ|Opis|
|--- |--- |--- |
|kod|ciąg|Wyliczenia zawierające kody błędów wysokiego poziomu. Możliwe wartości:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Brak autoryzacji</li></ul>|
|message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|
|obiektów|ciąg|Pobiera Źródło błędu. Na przykład może to być "Documents" lub "ID dokumentu" dla nieprawidłowego dokumentu.|
|innerError|InnerErrorV2|Nowy format błędu wewnętrznego, który jest zgodny ze wskazówkami dotyczącymi interfejsu API Cognitive Services. Zawiera wymagane właściwości ErrorCode, komunikat i opcjonalne właściwości target, szczegóły (para klucz wartość), błąd wewnętrzny (może być zagnieżdżony).|
|innerError. Code|ciąg|Pobiera ciąg błędu kodu.|
|fabryk. Faktyczny. Message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|

## <a name="examples"></a>Przykłady

### <a name="example-successful-response"></a>Przykład pomyślnej odpowiedzi

Poniższy obiekt JSON jest przykładem pomyślnej odpowiedzi.

Kod stanu: 200

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

Kod stanu: 500

```JSON
{
  "error": {
    "code": "InternalServerError",
    "message": "Internal Server Error",
    "target": "Operation",
    "innerError": {
      "code": "InternalServerError",
      "message": "Unexpected internal server error has occurred"
    }
  }
}
```

## <a name="next-steps"></a>Następne kroki

Skorzystaj z naszego przewodnika Szybki Start, aby dowiedzieć się więcej o korzystaniu z tłumaczenia dokumentów i biblioteki klienckiej.

> [!div class="nextstepaction"]
> [Wprowadzenie do tłumaczenia dokumentu](../get-started-with-document-translation.md)
