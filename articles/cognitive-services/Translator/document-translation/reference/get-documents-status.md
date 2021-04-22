---
title: Uzyskiwanie stanu dokumentów
titleSuffix: Azure Cognitive Services
description: Metoda get documents status zwraca stan wszystkich dokumentów w żądaniu tłumaczenia dokumentu wsadowego.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: 8476c4891cef9d9055b16c7ac574e569ecf5b3f2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864868"
---
# <a name="get-documents-status"></a>Uzyskiwanie stanu dokumentów

Metoda get documents status zwraca stan wszystkich dokumentów w żądaniu tłumaczenia dokumentu wsadowego.

Dokumenty zawarte w odpowiedzi są sortowane według identyfikatora dokumentu w kolejności malejącej. Jeśli liczba dokumentów w odpowiedzi przekroczy limit stronicowania, zostanie użyte stronicowanie po stronie serwera. Odpowiedzi z podziałem na strony wskazują wynik częściowy i zawierają w odpowiedzi token kontynuacji. Brak tokenu kontynuacji oznacza, że żadne dodatkowe strony nie są dostępne.

$top i $skip można użyć do określenia liczby wyników do zwrócenia oraz przesunięcia dla kolekcji. Serwer honoruje wartości określone przez klienta. Klienci muszą być jednak przygotowani do obsługi odpowiedzi, które zawierają inny rozmiar strony lub token kontynuacji.

Jeśli są $top i $skip, serwer powinien najpierw zastosować $skip, a $top do kolekcji.

> [!NOTE]
> Jeśli serwer nie może honorować $top i/lub $skip, serwer musi zwrócić komunikat o błędzie do klienta, który o nim informuje, zamiast po prostu zignorować opcje zapytania. Zmniejsza to ryzyko, że klient będzie przyjmował założenia dotyczące zwracanych danych.

## <a name="request-url"></a>Adres URL żądania

Wyślij `GET` żądanie do:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents
```

Dowiedz się, jak znaleźć [niestandardową nazwę domeny.](../get-started-with-document-translation.md#find-your-custom-domain-name)

> [!IMPORTANT]
>
> * **Wszystkie żądania interfejsu API do usługi tłumaczenia dokumentów wymagają niestandardowego punktu końcowego domeny**.
> * Nie można użyć punktu końcowego znalezionego na  stronie kluczy i punktu końcowego Azure Portal ani globalnego punktu końcowego usługi Translator — do przesyłania żądań HTTP do tłumaczenia `api.cognitive.microsofttranslator.com` dokumentów.

## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przekazane w ciągu zapytania to:

|Parametr zapytania|Wymagane|Opis|
|--- |--- |--- |
|identyfikator|Prawda|Identyfikator operacji.|
|$skip|Fałsz|Pomiń $skip wpisów w kolekcji. Po po $top i $skip zostaną podane $skip zostaną zastosowane jako pierwsze.|
|$top|Fałsz|Weź $top wpisów w kolekcji. Po po $top i $skip zostaną podane $skip zostaną zastosowane jako pierwsze.|

## <a name="request-headers"></a>Nagłówki żądań

Nagłówki żądań to:

|Nagłówki|Opis|
|--- |--- |
|Ocp-Apim-Subscription-Key|Wymagany nagłówek żądania|

## <a name="response-status-codes"></a>Kody stanu odpowiedzi

Poniżej przedstawiono możliwe kody stanu HTTP zwracane przez żądanie.

|Kod stanu|Opis|
|--- |--- |
|200|OK. Żądanie powiodło się i zwraca stan dokumentów. HeadersRetry-After: integerETag: string|
|400|Nieprawidłowe żądanie. Sprawdź parametry wejściowe.|
|401|Nieautoryzowanych. Sprawdź swoje poświadczenia.|
|404|Zasób nie został znaleziony.|
|500|Wewnętrzny błąd serwera.|
|Inne kody stanu|<ul><li>Zbyt wiele żądań</li><li>Serwer jest tymczasowo niedostępny</li></ul>|


## <a name="get-documents-status-response"></a>Uzyskiwanie odpowiedzi stanu dokumentów

### <a name="successful-get-documents-status-response"></a>Pomyślna odpowiedź na pytanie o stan pobierz dokumenty

Po pomyślnym zwróceniu odpowiedzi są zwracane następujące informacje.

|Nazwa|Typ|Opis|
|--- |--- |--- |
|@nextLink|ciąg|Adres URL następnej strony. Wartość null, jeśli nie ma więcej dostępnych stron.|
|wartość|DocumentStatusDetail []|Szczegółowy stan poszczególnych dokumentów wymienionych poniżej.|
|value.path|ciąg|Lokalizacja dokumentu lub folderu.|
|value.createdDateTimeUtc|ciąg|Data i godzina utworzenia operacji.|
|value.lastActionDateTimeUt|ciąg|Data i godzina aktualizacji stanu operacji.|
|value.status|status|Lista możliwych stanów zadania lub dokumentu.<ul><li>Anulowane</li><li>Anulowanie</li><li>Niepowodzenie</li><li>NotStarted</li><li>Uruchomienie</li><li>Powodzenie</li><li>Błąd walidacji</li></ul>|
|value.to|ciąg|Do języka.|
|value.progress|ciąg|Postęp tłumaczenia, jeśli jest dostępny.|
|value.id|ciąg|Identyfikator dokumentu.|
|value.characterCharged|liczba całkowita|Znaki naliczane przez interfejs API.|

### <a name="error-response"></a>Odpowiedź o błędzie

|Nazwa|Typ|Opis|
|--- |--- |--- |
|kod|ciąg|Wylinia zawierające kody błędów wysokiego poziomu. Możliwe wartości:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Brak autoryzacji</li></ul>|
|message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|
|Docelowego|ciąg|Pobiera źródło błędu. Na przykład w przypadku nieprawidłowego dokumentu będzie to "dokumenty" lub "identyfikator dokumentu".|
|innerError|InnerErrorV2|Nowy format błędu wewnętrznego, który jest zgodny z Cognitive Services API. Zawiera wymagane właściwości ErrorCode, komunikat i opcjonalny element docelowy właściwości, szczegóły (para wartości klucza), błąd wewnętrzny (może być zagnieżdżony).|
|innerError.code|ciąg|Pobiera ciąg błędu kodu.|
|innerError.message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|

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

### <a name="example-error-response"></a>Przykładowa odpowiedź z błędem

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
