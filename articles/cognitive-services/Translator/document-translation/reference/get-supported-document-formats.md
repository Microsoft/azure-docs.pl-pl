---
title: Uzyskiwanie obsługiwanej metody formatów dokumentów
titleSuffix: Azure Cognitive Services
description: Metoda get supported document formats zwraca listę obsługiwanych formatów dokumentów.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 09e8f82b6a4ff26f0ae53090e7c330f72d3b9583
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836198"
---
# <a name="get-supported-document-formats"></a>Uzyskiwanie obsługiwanych formatów dokumentów

Metoda Pobierz obsługiwane formaty dokumentów zwraca listę formatów dokumentów obsługiwanych przez usługę tłumaczenia dokumentów. Lista zawiera wspólne rozszerzenie pliku i typ zawartości w przypadku korzystania z interfejsu API przekazywania.

## <a name="request-url"></a>Adres URL żądania

Wyślij `GET` żądanie do:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/documents/formats
```

Dowiedz się, jak znaleźć [niestandardową nazwę domeny.](../get-started-with-document-translation.md#find-your-custom-domain-name)

> [!IMPORTANT]
>
> * **Wszystkie żądania interfejsu API do usługi tłumaczenia dokumentów wymagają niestandardowego punktu końcowego domeny**.
> * Nie można użyć punktu końcowego znalezionego na  stronie kluczy Azure Portal punktu końcowego ani globalnego punktu końcowego translatora — do przesyłania żądań HTTP do `api.cognitive.microsofttranslator.com` tłumaczenia dokumentów.

## <a name="request-headers"></a>Nagłówki żądań

Nagłówki żądań to:

|Nagłówki|Opis|
|-----|-----|
|Ocp-Apim-Subscription-Key|Wymagany nagłówek żądania|

## <a name="response-status-codes"></a>Kody stanu odpowiedzi

Poniżej przedstawiono możliwe kody stanu HTTP zwracane przez żądanie.

|Kod stanu|Opis|
|-----|-----|
|200|OK. Zwraca listę obsługiwanych formatów plików dokumentów.|
|500|Wewnętrzny błąd serwera.|
|Inne kody stanu|<ul><li>Zbyt wiele żądań</li><li>Serwer jest tymczasowo niedostępny</li></ul>|

## <a name="file-format-response"></a>Odpowiedź w formacie pliku

### <a name="successful-fileformatlistresult-response"></a>Pomyślna odpowiedź fileFormatListResult

Po pomyślnym zwróceniu odpowiedzi są zwracane następujące informacje.

|Nazwa|Typ|Opis|
|--- |--- |--- |
|wartość|FileFormat []|FileFormat[] zawiera szczegółowe informacje wymienione poniżej.|
|value.format|string[]|Obsługiwane typy zawartości dla tego formatu.|
|value.fileExtensions|string[]|Obsługiwane rozszerzenie pliku dla tego formatu.|
|value.contentTypes|string[]|Nazwa formatu.|
|value.versions|String[]|Obsługiwana wersja.|

### <a name="error-response"></a>Odpowiedź z błędem

|Nazwa|Typ|Opis|
|--- |--- |--- |
 |kod|ciąg|Wylinia zawierające kody błędów wysokiego poziomu. Możliwe wartości:<ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Brak autoryzacji</li></ul>|
|message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|
|innerError|InnerErrorV2|Nowy format błędu wewnętrznego, który jest zgodny z Cognitive Services API. Zawiera wymagane właściwości ErrorCode, docelowy komunikat i opcjonalne właściwości, szczegóły (para wartości klucza), błąd wewnętrzny (może być zagnieżdżony).|
|innerError.code|ciąg|Pobiera ciąg błędu kodu.|
|innerError.message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|

## <a name="examples"></a>Przykłady

### <a name="example-successful-response"></a>Przykład pomyślnej odpowiedzi
Poniżej przedstawiono przykład pomyślnej odpowiedzi.

Kod stanu: 200

```JSON
{
  "value": [
    {
      "format": "PlainText",
      "fileExtensions": [
        ".txt"
      ],
      "contentTypes": [
        "text/plain"
      ],
      "versions": []
    },
    {
      "format": "PortableDocumentFormat",
      "fileExtensions": [
        ".pdf"
      ],
      "contentTypes": [
        "application/pdf"
      ],
      "versions": []
    },
    {
      "format": "OpenXmlPresentation",
      "fileExtensions": [
        ".pptx"
      ],
      "contentTypes": [
        "application/vnd.openxmlformats-officedocument.presentationml.presentation"
      ],
      "versions": []
    },
    {
      "format": "OpenXmlSpreadsheet",
      "fileExtensions": [
        ".xlsx"
      ],
      "contentTypes": [
        "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
      ],
      "versions": []
    },
    {
      "format": "OutlookMailMessage",
      "fileExtensions": [
        ".msg"
      ],
      "contentTypes": [
        "application/vnd.ms-outlook"
      ],
      "versions": []
    },
    {
      "format": "HtmlFile",
      "fileExtensions": [
        ".html"
      ],
      "contentTypes": [
        "text/html"
      ],
      "versions": []
    },
    {
      "format": "OpenXmlWord",
      "fileExtensions": [
        ".docx"
      ],
      "contentTypes": [
        "application/vnd.openxmlformats-officedocument.wordprocessingml.document"
      ],
      "versions": []
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