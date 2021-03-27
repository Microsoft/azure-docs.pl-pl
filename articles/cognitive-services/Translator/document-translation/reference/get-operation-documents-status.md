---
title: Stan dokumentów operacji pobierania tłumaczenia dokumentu
titleSuffix: Azure Cognitive Services
description: Metoda Pobierz dokumenty operacji zwraca stan wszystkich dokumentów w żądaniu tłumaczenia dokumentu wsadowego.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: c6cf33f32abb473badbb11bf58bd52ffba53d75c
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613103"
---
# <a name="document-translation-get-operation-documents-status"></a>Tłumaczenie dokumentu: pobieranie stanu dokumentów operacji

Metoda Pobierz dokumenty operacji zwraca stan wszystkich dokumentów w żądaniu tłumaczenia dokumentu wsadowego.

Dokumenty zawarte w odpowiedzi są sortowane według identyfikatora dokumentu w kolejności malejącej. Jeśli liczba dokumentów w odpowiedzi przekracza nasz limit stronicowania, używane jest stronicowanie po stronie serwera. Odpowiedzi z podziałem na strony wskazują częściowy wynik i w odpowiedzi zawierają token kontynuacji. Brak tokenu kontynuacji oznacza, że żadne dodatkowe strony nie są dostępne.

parametry zapytania $top i $skip mogą służyć do określania liczby wyników do zwrócenia i przesunięcia dla kolekcji. Serwer uznaje wartości określone przez klienta. Jednak klienci muszą być przygotowani do obsługi odpowiedzi zawierających inny rozmiar strony lub zawierają token kontynuacji.

Po dołączeniu obu $top i $skip serwer powinien najpierw zastosować $skip a następnie $top na kolekcji.

> [!NOTE]
> Jeśli serwer nie może honorować $top i/lub $skip, serwer musi zwrócić błąd do klienta informującego o tym, zamiast pomijać opcje zapytania. Zmniejsza to ryzyko wprowadzenia przez klienta założeń dotyczących zwracanych danych.

## <a name="request-url"></a>Adres URL żądania

Wyślij `GET` żądanie do:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents
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
|200|OK. Pomyślne żądanie i zwraca stan dokumentów. HeadersRetry-After: integerETag: ciąg|
|400|Nieprawidłowe żądanie. Sprawdź parametry wejściowe.|
|401|Próby. Sprawdź swoje poświadczenia.|
|404|Nie znaleziono zasobu.|
|500|Wewnętrzny błąd serwera.|
|Inne kody stanu|<ul><li>Zbyt wiele żądań</li><li>Serwer tymczasowy niedostępny</li></ul>|


## <a name="get-operation-documents-status-response"></a>Pobierz odpowiedź stanu dokumentów operacji

### <a name="successful-get-operation-documents-status-response"></a>Pomyślne pobieranie stanu dokumentów operacji — odpowiedź

Poniższe informacje są zwracane w odpowiedzi na pomyślne.

|Nazwa|Typ|Opis|
|--- |--- |--- |
|@nextLink|ciąg|Adres URL następnej strony. Wartość null, jeśli nie ma więcej dostępnych stron.|
|wartość|DocumentStatusDetail []|Szczegółowy stan poszczególnych dokumentów wymienionych poniżej.|
|Value. Path|ciąg|Lokalizacja dokumentu lub folderu.|
|Value. createdDateTimeUtc|ciąg|Data i godzina utworzenia operacji.|
|Value. lastActionDateTimeUt|ciąg|Data i godzina aktualizacji stanu operacji.|
|wartość. status|status|Lista możliwych stanów dla zadania lub dokumentu.<ul><li>Anulowane</li><li>Anulowanie</li><li>Niepowodzenie</li><li>NotStarted</li><li>Uruchomienie</li><li>Powodzenie</li><li>ValidationFailed</li></ul>|
|value.to|ciąg|Do języka.|
|wartość. postęp|ciąg|Postęp tłumaczenia, jeśli jest dostępny.|
|value.id|ciąg|Identyfikator dokumentu.|
|Value. characterCharged|liczba całkowita|Znaki naliczone przez interfejs API.|

### <a name="error-response"></a>Odpowiedź na błąd

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
      "path": "https://myblob.blob.core.windows.net/destinationContainer/fr/mydoc.txt",
      "createdDateTimeUtc": "2020-03-26T00:00:00Z",
      "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
      "status": "Running",
      "to": "fr",
      "progress": 0.1,
      "id": "273622bd-835c-4946-9798-fd8f19f6bbf2",
      "characterCharged": 0
    }
  ],
  "@nextLink": "https://westus.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55/documents?$top=5&$skip=15"
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
