---
title: Anulowanie metody tłumaczenia
titleSuffix: Azure Cognitive Services
description: Metoda anulowania tłumaczenia anuluje obecnie przetwarzanie lub operację w kolejce.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: e3b7da30f54b9d9468b46a2cd0972a3397e5cdce
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865111"
---
# <a name="cancel-translation"></a>Anulowanie tłumaczenia

Anulowanie operacji przetwarzania lub operacji w kolejce. Operacja nie zostanie anulowana, jeśli została już ukończona, nie powiodła się lub została anulowana. Zostanie zwrócone złe żądanie. Wszystkie dokumenty, które zostały ukończone, nie zostaną anulowane i zostaną naliczone opłaty. Jeśli to możliwe, wszystkie oczekujące dokumenty zostaną anulowane.

## <a name="request-url"></a>Adres URL żądania

Wyślij `DELETE` żądanie do:

```DELETE HTTP
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
```

Dowiedz się, jak znaleźć [niestandardową nazwę domeny.](../get-started-with-document-translation.md#find-your-custom-domain-name)

> [!IMPORTANT]
>
> * **Wszystkie żądania interfejsu API do usługi tłumaczenia dokumentów wymagają niestandardowego punktu końcowego domeny**.
> * Nie można użyć punktu końcowego znalezionego na  stronie kluczy Azure Portal punktu końcowego ani globalnego punktu końcowego translatora — do przesyłania żądań HTTP do `api.cognitive.microsofttranslator.com` tłumaczenia dokumentów.

## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przekazane w ciągu zapytania to:

|Parametr zapytania|Wymagane|Opis|
|-----|-----|-----|
|identyfikator|Prawda|Identyfikator operacji.|

## <a name="request-headers"></a>Nagłówki żądań

Nagłówki żądań to:

|Nagłówki|Opis|
|-----|-----|
|Ocp-Apim-Subscription-Key|Wymagany nagłówek żądania|

## <a name="response-status-codes"></a>Kody stanu odpowiedzi

Poniżej przedstawiono możliwe kody stanu HTTP zwracane przez żądanie.

| Kod stanu| Opis|
|-----|-----|
|200|OK. Żądanie anulowania zostało przesłane|
|401|Nieautoryzowanych. Sprawdź swoje poświadczenia.|
|404|Nie znaleziono. Zasób nie został znaleziony. 
|500|Wewnętrzny błąd serwera.
|Inne kody stanu|<ul><li>Zbyt wiele żądań</li><li>Serwer jest tymczasowo niedostępny</li></ul>|

## <a name="cancel-translation-response"></a>Anulowanie odpowiedzi tłumaczenia

### <a name="successful-response"></a>Pomyślna odpowiedź

Po pomyślnym zwróceniu odpowiedzi są zwracane następujące informacje.

|Nazwa|Typ|Opis|
|--- |--- |--- |
|identyfikator|ciąg|Identyfikator operacji.|
|createdDateTimeUtc|ciąg|Data i godzina utworzenia operacji.|
|lastActionDateTimeUtc|ciąg|Data i godzina aktualizacji stanu operacji.|
|status|Ciąg|Lista możliwych stanów zadania lub dokumentu: <ul><li>Anulowane</li><li>Anulowanie</li><li>Niepowodzenie</li><li>NotStarted</li><li>Uruchomienie</li><li>Powodzenie</li><li>Błąd walidacji</li></ul>|
|Podsumowanie|StatusSummary|Podsumowanie zawierające szczegóły wymienione poniżej.|
|summary.total|liczba całkowita|Liczba wszystkich dokumentów.|
|summary.failed|liczba całkowita|Liczba dokumentów nie powiodła się.|
|summary.success|liczba całkowita|Liczba pomyślnie przetłumaczonych dokumentów.|
|summary.inProgress|liczba całkowita|Liczba dokumentów w toku.|
|summary.notYetStarted|liczba całkowita|Liczba dokumentów, których przetwarzanie nie zostało jeszcze rozpoczęte.|
|summary.cancelled|liczba całkowita|Liczba anulowanych.|
|summary.totalCharacterCharged|liczba całkowita|Łączna liczba znaków naliczanych przez interfejs API.|

### <a name="error-response"></a>Odpowiedź o błędzie

|Nazwa|Typ|Opis|
|--- |--- |--- |
|kod|ciąg|Wylinia zawierające kody błędów wysokiego poziomu. Możliwe wartości:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Brak autoryzacji</li></ul>|
|message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|
|Docelowego|ciąg|Pobiera źródło błędu. Na przykład w przypadku nieprawidłowego dokumentu będzie to "dokumenty" lub "identyfikator dokumentu".|
|innerError|InnerErrorV2|Nowy format błędu wewnętrznego, który jest zgodny z Cognitive Services API. Zawiera on wymagane właściwości ErrorCode, komunikat i opcjonalny element docelowy właściwości, szczegóły (para wartości klucza), błąd wewnętrzny (może być zagnieżdżony).|
|innerError.code|ciąg|Pobiera ciąg błędu kodu.|
|Wewnętrzny. Eroor.message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|

## <a name="examples"></a>Przykłady

### <a name="example-successful-response"></a>Przykład pomyślnej odpowiedzi

Następujący obiekt JSON jest przykładem pomyślnej odpowiedzi.

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

### <a name="example-error-response"></a>Przykładowa odpowiedź z błędem

Następujący obiekt JSON jest przykładem odpowiedzi o błędzie. Schemat innych kodów błędów jest taki sam.

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

Postępuj zgodnie z naszymi przewodnikami Szybki start, aby dowiedzieć się więcej na temat korzystania z tłumaczenia dokumentów i biblioteki klienta.

> [!div class="nextstepaction"]
> [Wprowadzenie do tłumaczenia dokumentów](../get-started-with-document-translation.md)
