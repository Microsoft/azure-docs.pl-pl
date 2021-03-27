---
title: Operacje pobierania tłumaczenia dokumentu
titleSuffix: Azure Cognitive Services
description: Metoda Get Operations zwraca listę przesłanych żądań wsadowych i stan dla każdego żądania.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: c42f3081a831c267c7bc605267b99e2a916ea3d8
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613093"
---
# <a name="document-translation-get-operations"></a>Tłumaczenie dokumentu: pobieranie operacji

Metoda Get Operations zwraca listę przesłanych żądań wsadowych i stan dla każdego żądania. Ta lista zawiera tylko żądania wsadowe przesłane przez użytkownika (w oparciu o subskrypcję). Stan każdego żądania jest posortowany według identyfikatora.

Jeśli liczba żądań przekracza nasz limit stronicowania, używane jest stronicowanie po stronie serwera. Odpowiedzi z podziałem na strony wskazują częściowy wynik i w odpowiedzi zawierają token kontynuacji. Brak tokenu kontynuacji oznacza, że żadne dodatkowe strony nie są dostępne.

parametry zapytania $top i $skip mogą służyć do określania liczby wyników do zwrócenia i przesunięcia dla kolekcji.

Serwer uznaje wartości określone przez klienta. Jednak klienci muszą być przygotowani do obsługi odpowiedzi zawierających inny rozmiar strony lub zawierają token kontynuacji.

Po dołączeniu obu $top i $skip serwer powinien najpierw zastosować $skip a następnie $top na kolekcji. 

> [!NOTE]
> Jeśli serwer nie może honorować $top i/lub $skip, serwer musi zwrócić błąd do klienta informującego o tym, zamiast pomijać opcje zapytania. Zmniejsza to ryzyko wprowadzenia przez klienta założeń dotyczących zwracanych danych.

## <a name="request-url"></a>Adres URL żądania

Wyślij `GET` żądanie do:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
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
|$skip|Fałsz|Pomiń wpisy $skip w kolekcji. Po podaniu obu $top i $skip należy najpierw zastosować $skip.|
|$top|Fałsz|Wypełnij $top wpisów w kolekcji. Po podaniu obu $top i $skip należy najpierw zastosować $skip.|

## <a name="request-headers"></a>Nagłówki żądań

Nagłówki żądań to:

|Nagłówki|Opis|
|--- |--- |
|Ocp-Apim-Subscription-Key|Wymagany nagłówek żądania|

## <a name="response-status-codes"></a>Kody stanu odpowiedzi

Oto możliwe kody stanu HTTP zwracane przez żądanie.

|Kod stanu|Opis|
|--- |--- |
|200|OK. Pomyślne żądanie i zwraca stan wszystkich operacji. HeadersRetry-After: integerETag: ciąg|
|400|Nieprawidłowe żądanie. Nieprawidłowe żądanie. Sprawdź parametry wejściowe.|
|401|Próby. Sprawdź swoje poświadczenia.|
|500|Wewnętrzny błąd serwera.|
|Inne kody stanu|<ul><li>Zbyt wiele żądań</li><li>Serwer tymczasowy niedostępny</li></ul>|

## <a name="get-operations-response"></a>Pobierz odpowiedź na operacje

### <a name="successful-get-operations-response"></a>Pomyślne Pobieranie odpowiedzi operacji

Poniższe informacje są zwracane w odpowiedzi na pomyślne.

|Nazwa|Typ|Opis|
|--- |--- |--- |
|identyfikator|ciąg|Identyfikator operacji.|
|createdDateTimeUtc|ciąg|Data i godzina utworzenia operacji.|
|lastActionDateTimeUtc|ciąg|Data i godzina aktualizacji stanu operacji.|
|status|Ciąg|Lista możliwych stanów dla zadania lub dokumentu: <ul><li>Anulowane</li><li>Anulowanie</li><li>Niepowodzenie</li><li>NotStarted</li><li>Uruchomienie</li><li>Powodzenie</li><li>ValidationFailed</li></ul>|
|Podsumowanie|StatusSummary[]|Podsumowanie zawierające szczegóły wymienione poniżej.|
|summary. Total|liczba całkowita|Łączna liczba dokumentów.|
|Podsumowanie. nie powiodło się|liczba całkowita|Liczba dokumentów zakończonych niepowodzeniem.|
|summary. Success|liczba całkowita|Liczba pomyślnie przetłumaczonych dokumentów.|
|Podsumowanie. w toku|liczba całkowita|Liczba dokumentów w toku.|
|summary. notYetStarted|liczba całkowita|Liczba dokumentów, które nie zostały jeszcze uruchomione.|
|Podsumowanie. anulowane|liczba całkowita|Liczba anulowanych dokumentów.|
|summary. totalCharacterCharged|liczba całkowita|Łączna liczba znaków naliczanych.|

###<a name="error-response"></a>Odpowiedź na błąd

|Nazwa|Typ|Opis|
|--- |--- |--- |
|kod|ciąg|Wyliczenia zawierające kody błędów wysokiego poziomu. Możliwe wartości:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Brak autoryzacji</li></ul>|
|message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|
|obiektów|ciąg|Pobiera Źródło błędu. Na przykład może to być "Documents" lub "ID dokumentu" w przypadku nieprawidłowego dokumentu.|
|innerError|InnerErrorV2|Nowy format błędu wewnętrznego, który jest zgodny ze wskazówkami dotyczącymi interfejsu API Cognitive Services. Zawiera wymagane właściwości ErrorCode, komunikat i opcjonalne właściwości target, szczegóły (para klucz wartość), błąd wewnętrzny (może to być zagnieżdżone).|
|innerError. Code|ciąg|Pobiera ciąg błędu kodu.|
|innerError. Message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|

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

### <a name="example-error-response"></a>Przykładowa odpowiedź na błąd

Poniżej przedstawiono przykład odpowiedzi na błąd. Schemat dla innych kodów błędów jest taki sam.

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
