---
title: Uzyskiwanie obsługiwanej metody źródeł magazynu
titleSuffix: Azure Cognitive Services
description: Metoda get supported storage sources zwraca listę obsługiwanych źródeł magazynu.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 97212a0da47a25330def26a5bff2b39ff840a23d
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836192"
---
# <a name="get-supported-storage-sources"></a>Uzyskiwanie obsługiwanych źródeł magazynu

Metoda Get supported storage sources zwraca listę źródeł/opcji magazynu obsługiwanych przez usługę tłumaczenia dokumentów.

## <a name="request-url"></a>Adres URL żądania

Wyślij `GET` żądanie do:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/storagesources
```

Dowiedz się, jak znaleźć [niestandardową nazwę domeny.](../get-started-with-document-translation.md#find-your-custom-domain-name)

> [!IMPORTANT]
>
> * **Wszystkie żądania interfejsu API do usługi tłumaczenia dokumentów wymagają niestandardowego punktu końcowego domeny**.
> * Nie można użyć punktu końcowego znalezionego na  stronie kluczy Azure Portal punktu końcowego ani globalnego punktu końcowego translatora — do przesyłania żądań HTTP do `api.cognitive.microsofttranslator.com` tłumaczenia dokumentów.

## <a name="request-headers"></a>Nagłówki żądań

Nagłówki żądań to:

|Nagłówki|Opis|
|--- |--- |
|Ocp-Apim-Subscription-Key|Wymagany nagłówek żądania|

## <a name="response-status-codes"></a>Kody stanu odpowiedzi

Poniżej przedstawiono możliwe kody stanu HTTP zwracane przez żądanie.

|Kod stanu|Opis|
|--- |--- |
|200|OK. Żądanie pomyślne i zwraca listę źródeł magazynu.|
|500|Wewnętrzny błąd serwera.|
|Inne kody stanu|<ul><li>Zbyt wiele żądań</li><li>Serwer jest tymczasowo niedostępny</li></ul>|

## <a name="get-supported-storage-sources-response"></a>Uzyskiwanie odpowiedzi na obsługiwane źródła magazynu

### <a name="successful-get-supported-storage-sources-response"></a>Pomyślna odpowiedź na pytanie o obsługiwane źródła magazynu
Typ podstawowy zwracanych list w interfejsie API Get supported storage sources (Uzyskiwanie obsługiwanych źródeł magazynu).

|Nazwa|Typ|Opis|
|--- |--- |--- |
|wartość|ciąg []|Lista obiektów.|


### <a name="error-response"></a>Odpowiedź z błędem

|Nazwa|Typ|Opis|
|--- |--- |--- |
|kod|ciąg|Wylinia zawierające kody błędów wysokiego poziomu. Możliwe wartości:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Brak autoryzacji</li></ul>|
|message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|
|innerError|InnerErrorV2|Nowy format błędu wewnętrznego, który jest zgodny z Cognitive Services API. Zawiera wymagane właściwości ErrorCode, komunikat i opcjonalny element docelowy właściwości, szczegóły (para wartości klucza), błąd wewnętrzny (może być zagnieżdżony).|
|innerError.code|ciąg|Pobiera ciąg błędu kodu.|
|innerError.message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|

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

### <a name="example-error-response"></a>Przykładowa odpowiedź z błędem
Poniżej przedstawiono przykład odpowiedzi z błędem. Schemat innych kodów błędów jest taki sam.

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

Postępuj zgodnie z naszymi przewodnikami Szybki start, aby dowiedzieć się więcej na temat korzystania z tłumaczenia dokumentów i biblioteki klienta.

> [!div class="nextstepaction"]
> [Wprowadzenie do tłumaczenia dokumentów](../get-started-with-document-translation.md)