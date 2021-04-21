---
title: Uzyskiwanie stanu tłumaczeń
titleSuffix: Azure Cognitive Services
description: Metoda stanu get translations zwraca listę przesłanych żądań wsadowych oraz stan każdego żądania.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: a67544dc4f654a692338c76099daa19934a9ea45
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836210"
---
# <a name="get-translations-status"></a>Uzyskiwanie stanu tłumaczeń

Metoda Get translations status zwraca listę przesłanych żądań wsadowych oraz stan każdego żądania. Ta lista zawiera tylko żądania wsadowe przesłane przez użytkownika (na podstawie subskrypcji). Stan każdego żądania jest sortowany według identyfikatora.

Jeśli liczba żądań przekracza limit stronicowania, jest używane stronicowanie po stronie serwera. Odpowiedzi z podziałem na strony wskazują wynik częściowy i zawierają token kontynuacji w odpowiedzi. Brak tokenu kontynuacji oznacza, że żadne dodatkowe strony nie są dostępne.

$top i $skip można użyć do określenia liczby wyników do zwrócenia oraz przesunięcia dla kolekcji.

Serwer honoruje wartości określone przez klienta. Klienci muszą być jednak przygotowani do obsługi odpowiedzi, które zawierają inny rozmiar strony lub token kontynuacji.

Gdy są $top i $skip, serwer powinien najpierw zastosować $skip, a $top do kolekcji. 

> [!NOTE]
> Jeśli serwer nie może honorować $top i/lub $skip, serwer musi zwrócić klientowi komunikat o błędzie, zamiast po prostu zignorować opcje zapytania. Zmniejsza to ryzyko, że klient będzie przyjmował założenia dotyczące zwracanych danych.

## <a name="request-url"></a>Adres URL żądania

Wyślij `GET` żądanie do:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
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
|$skip|Fałsz|Pomiń $skip wpisów w kolekcji. Po po $top i $skip są dostarczane $skip najpierw stosowane.|
|$top|Fałsz|Weź $top wpisów w kolekcji. Po po $top i $skip są dostarczane $skip najpierw stosowane.|

## <a name="request-headers"></a>Nagłówki żądań

Nagłówki żądań to:

|Nagłówki|Opis|
|--- |--- |
|Ocp-Apim-Subscription-Key|Wymagany nagłówek żądania|

## <a name="response-status-codes"></a>Kody stanu odpowiedzi

Poniżej przedstawiono możliwe kody stanu HTTP zwracane przez żądanie.

|Kod stanu|Opis|
|--- |--- |
|200|OK. Żądanie pomyślne i zwraca stan wszystkich operacji. HeadersRetry-After: integerETag: string|
|400|Złe żądanie. Nieprawidłowe żądanie. Sprawdź parametry wejściowe.|
|401|Nieautoryzowanych. Sprawdź swoje poświadczenia.|
|500|Wewnętrzny błąd serwera.|
|Inne kody stanu|<ul><li>Zbyt wiele żądań</li><li>Serwer jest tymczasowo niedostępny</li></ul>|

## <a name="get-translations-status-response"></a>Uzyskiwanie odpowiedzi stanu tłumaczeń

### <a name="successful-get-translations-status-response"></a>Odpowiedź na pytanie o stan pomyślnego uzyskania tłumaczeń

Po pomyślnym zwróceniu odpowiedzi są zwracane następujące informacje.

|Nazwa|Typ|Opis|
|--- |--- |--- |
|identyfikator|ciąg|Identyfikator operacji.|
|createdDateTimeUtc|ciąg|Data i godzina utworzenia operacji.|
|lastActionDateTimeUtc|ciąg|Data i godzina aktualizacji stanu operacji.|
|status|Ciąg|Lista możliwych stanów zadania lub dokumentu: <ul><li>Anulowane</li><li>Anulowanie</li><li>Niepowodzenie</li><li>NotStarted</li><li>Uruchomienie</li><li>Powodzenie</li><li>ValidationFailed</li></ul>|
|Podsumowanie|StatusSummary[]|Podsumowanie zawierające szczegóły wymienione poniżej.|
|summary.total|liczba całkowita|Liczba wszystkich dokumentów.|
|summary.failed|liczba całkowita|Liczba dokumentów nie powiodła się.|
|summary.success|liczba całkowita|Liczba pomyślnie przetłumaczonych dokumentów.|
|summary.inProgress|liczba całkowita|Liczba dokumentów w toku.|
|summary.notYetStarted|liczba całkowita|Liczba dokumentów, których przetwarzanie nie zostało jeszcze rozpoczęte.|
|summary.cancelled|liczba całkowita|Liczba anulowanych dokumentów.|
|summary.totalCharacterCharged|liczba całkowita|Łączna liczba znaków, dla których naliczane są opłaty.|

### <a name="error-response"></a>Odpowiedź z błędem

|Nazwa|Typ|Opis|
|--- |--- |--- |
|kod|ciąg|Wylinia zawierające kody błędów wysokiego poziomu. Możliwe wartości:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Brak autoryzacji</li></ul>|
|message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|
|Docelowego|ciąg|Pobiera źródło błędu. Na przykład w przypadku nieprawidłowego dokumentu będzie to "dokumenty" lub "identyfikator dokumentu".|
|innerError|InnerErrorV2|Nowy format błędu wewnętrznego, który jest zgodny z Cognitive Services API. Zawiera wymagane właściwości ErrorCode, docelowy komunikat i opcjonalne właściwości, szczegóły (para wartości klucza), błąd wewnętrzny (może być zagnieżdżony).|
|innerError.code|ciąg|Pobiera ciąg błędu kodu.|
|innerError.message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|

## <a name="examples"></a>Przykłady

### <a name="example-successful-response"></a>Przykład pomyślnej odpowiedzi

Poniżej przedstawiono przykład pomyślnej odpowiedzi.

```JSON
{
  "value": [
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
  ]
}
```

### <a name="example-error-response"></a>Przykładowa odpowiedź o błędzie

Poniżej przedstawiono przykład odpowiedzi z błędem. Schemat innych kodów błędów jest taki sam.

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
