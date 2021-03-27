---
title: Metoda tłumaczenia dokumentu Get Document Storage
titleSuffix: Azure Cognitive Services
description: Metoda Get Document Storage Source zwraca listę obsługiwanych źródeł magazynu.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: d6d8b1d08bbef1c37cbf0583022428037808580d
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613149"
---
# <a name="document-translation-get-document-storage-source"></a>Tłumaczenie dokumentu: Pobierz Źródło magazynu dokumentów

Metoda Get Document Storage Source zwraca listę źródeł/opcji magazynu obsługiwanych przez usługę tłumaczenia dokumentów.

## <a name="request-url"></a>Adres URL żądania

Wyślij `GET` żądanie do:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/storagesources
```

Dowiedz się, jak znaleźć swoją [niestandardową nazwę domeny](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Wszystkie żądania interfejsu API do usługi tłumaczenia dokumentów wymagają niestandardowego punktu końcowego domeny**.
> * Nie można użyć punktu końcowego znalezionego na stronie klucze zasobów Azure Portal _i punktu_ końcowego ani globalnego punktu końcowego usługi Translator — `api.cognitive.microsofttranslator.com` w celu żądania protokołu HTTP do tłumaczenia dokumentów.

## <a name="request-headers"></a>Nagłówki żądań

Nagłówki żądań to:

|Nagłówki|Opis|
|--- |--- |
|Ocp-Apim-Subscription-Key|Wymagany nagłówek żądania|

## <a name="response-status-codes"></a>Kody stanu odpowiedzi

Oto możliwe kody stanu HTTP zwracane przez żądanie.

|Kod stanu|Opis|
|--- |--- |
|200|OK. Pomyślne żądanie i zwraca listę źródeł magazynu.|
|500|Wewnętrzny błąd serwera.|
|Inne kody stanu|<ul><li>Zbyt wiele żądań</li><li>Serwer tymczasowy niedostępny</li></ul>|

## <a name="get-document-storage-source-response"></a>Pobierz odpowiedź źródłową magazynu dokumentów

### <a name="successful-get-document-storage-source-response"></a>Pomyślnie pobrano odpowiedź źródłową magazynu dokumentów
Typ podstawowy dla zwracanej listy w interfejsie API pobierania dokumentów źródłowych magazynu.

|Nazwa|Typ|Opis|
|--- |--- |--- |
|wartość|ciąg []|Lista obiektów.|


### <a name="error-response"></a>Odpowiedź na błąd

|Nazwa|Typ|Opis|
|--- |--- |--- |
|kod|ciąg|Wyliczenia zawierające kody błędów wysokiego poziomu. Możliwe wartości:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Brak autoryzacji</li></ul>|
|message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|
|innerError|InnerErrorV2|Nowy format błędu wewnętrznego, który jest zgodny ze wskazówkami dotyczącymi interfejsu API Cognitive Services. Zawiera wymagane właściwości ErrorCode, komunikat i opcjonalne właściwości target, szczegóły (para klucz wartość), błąd wewnętrzny (może to być zagnieżdżone).|
|innerError. Code|ciąg|Pobiera ciąg błędu kodu.|
|innerError. Message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|

## <a name="examples"></a>Przykłady

### <a name="example-successful-response"></a>Przykład pomyślnej odpowiedzi

Poniżej przedstawiono przykład pomyślnej odpowiedzi.

```JSON
{
  "value": [
    "AzureBlob"
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