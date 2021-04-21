---
title: Uzyskiwanie stanu tłumaczenia
titleSuffix: Azure Cognitive Services
description: Metoda get translation status zwraca stan żądania tłumaczenia dokumentu.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 8b129974396e420948737c9bdf47a5707decab6b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836211"
---
# <a name="get-translation-status"></a>Uzyskiwanie stanu tłumaczenia

Metoda Get translation status zwraca stan żądania tłumaczenia dokumentu. Stan obejmuje ogólny stan żądania i stan dokumentów, które są tłumaczone w ramach tego żądania.

## <a name="request-url"></a>Adres URL żądania

Wyślij `GET` żądanie do:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
```

Dowiedz się, jak znaleźć [niestandardową nazwę domeny.](../get-started-with-document-translation.md#find-your-custom-domain-name)

> [!IMPORTANT]
>
> * **Wszystkie żądania interfejsu API do usługi tłumaczenia dokumentów wymagają niestandardowego punktu końcowego domeny**.
> * Nie można użyć punktu końcowego znalezionego na  stronie kluczy Azure Portal punktu końcowego ani globalnego punktu końcowego translatora — do przesyłania żądań HTTP do `api.cognitive.microsofttranslator.com` tłumaczenia dokumentów.


## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przekazane w ciągu zapytania to:

|Parametr zapytania|Wymagane|Opis|
|--- |--- |--- |
|identyfikator|Prawda|Identyfikator operacji.|

## <a name="request-headers"></a>Nagłówki żądań

Nagłówki żądań to:

|Nagłówki|Opis|
|--- |--- |
|Ocp-Apim-Subscription-Key|Wymagany nagłówek żądania|

## <a name="response-status-codes"></a>Kody stanu odpowiedzi

Poniżej przedstawiono możliwe kody stanu HTTP zwracane przez żądanie.

|Kod stanu|Opis|
|--- |--- |
|200|OK. Żądanie powiodło się i zwraca stan operacji tłumaczenia wsadowego. HeadersRetry-After: integerETag: string|
|401|Nieautoryzowanych. Sprawdź swoje poświadczenia.|
|404|Zasób nie został znaleziony.|
|500|Wewnętrzny błąd serwera.|
|Inne kody stanu|<ul><li>Zbyt wiele żądań</li><li>Serwer jest tymczasowo niedostępny</li></ul>|

## <a name="get-translation-status-response"></a>Uzyskiwanie odpowiedzi stanu tłumaczenia

### <a name="successful-get-translation-status-response"></a>Pomyślna odpowiedź na pytanie o stan tłumaczenia

Po pomyślnym zwróceniu odpowiedzi są zwracane następujące informacje.

|Nazwa|Typ|Opis|
|--- |--- |--- |
|identyfikator|ciąg|Identyfikator operacji.|
|createdDateTimeUtc|ciąg|Data i godzina utworzenia operacji.|
|lastActionDateTimeUtc|ciąg|Data i godzina aktualizacji stanu operacji.|
|status|Ciąg|Lista możliwych stanów zadania lub dokumentu: <ul><li>Anulowane</li><li>Anulowanie</li><li>Niepowodzenie</li><li>NotStarted</li><li>Uruchomienie</li><li>Powodzenie</li><li>Błąd walidacji</li></ul>|
|Podsumowanie|StatusSummary|Podsumowanie zawierające szczegóły wymienione poniżej.|
|summary.total|liczba całkowita|Łączna liczba.|
|summary.failed|liczba całkowita|Liczba nieudanych awarii.|
|summary.success|liczba całkowita|Liczba pomyślnych.|
|summary.inProgress|liczba całkowita|Liczba w toku.|
|summary.notYetStarted|liczba całkowita|Liczba jeszcze nie uruchomionych.|
|summary.cancelled|liczba całkowita|Liczba anulowanych.|
|summary.totalCharacterCharged|liczba całkowita|Łączna liczba znaków naliczanych przez interfejs API.|

###<a name="error-response"></a>Odpowiedź o błędzie

|Nazwa|Typ|Opis|
|--- |--- |--- |
|kod|ciąg|Wylinia zawierające kody błędów wysokiego poziomu. Możliwe wartości:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Brak autoryzacji</li></ul>|
|message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|
|Docelowego|ciąg|Pobiera źródło błędu. Na przykład w przypadku nieprawidłowego dokumentu będzie to "dokumenty" lub "identyfikator dokumentu".|
|innerError|InnerErrorV2|Nowy format błędu wewnętrznego, który jest zgodny z Cognitive Services API. Zawiera on wymagane właściwości ErrorCode, komunikat i opcjonalny element docelowy właściwości, szczegóły (para wartości klucza), błąd wewnętrzny (może być zagnieżdżony).|
|innerError.code|ciąg|Pobiera ciąg błędu kodu.|
|innerError.message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|

## <a name="examples"></a>Przykłady

### <a name="example-successful-response"></a>Przykład pomyślnej odpowiedzi

Następujący obiekt JSON jest przykładem pomyślnej odpowiedzi.

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

Postępuj zgodnie z naszymi przewodnikami Szybki start, aby dowiedzieć się więcej na temat korzystania z tłumaczenia dokumentów i biblioteki klienta.

> [!div class="nextstepaction"]
> [Wprowadzenie do tłumaczenia dokumentów](../get-started-with-document-translation.md)
